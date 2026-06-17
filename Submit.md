# [우리FISA 6기] 클라우드 서비스 개발 과정 2팀 

## 1\. 프로젝트 개요
  * **주제** : 소상공인 성장 가능성 기반 대출 플랫폼
  * **프로젝트 기획 배경** : 국내 소상공인은 전체 중소기업의 90% 이상을 차지하는 핵심 경제 주체임에도 불구하고, 기존 대출 체계는 개인 신용 점수만을 기준으로 운영되어 실제 사업 운영 성과와 성장 가능성이 반영되지 않는 구조적 한계를 지닙니다. <br> 이러한 문제를 해결하기 위해 정부는 2026년 하반기부터 소상공인 특화 신용평가모형(SCB) 시행을 추진하고 있으며, SoFit은 이를 기반으로 사업자 CB등급과 실제 매출·성장 가능성 등 사업 운영 데이터를 종합적으로 반영한 성장 S등급 신용평가 체계를 도입하여, 기존 금융 시스템에서 소외되었던 소상공인에게 합리적인 대출 기회를 제공하는 맞춤형 금융 플랫폼을 구축하고자 합니다.
  * **기술 스택** : 
    * FE: React 19, TypeScript, Vite, React Query, Zustand, Tailwind CSS, Axios
    * BE: Java 21, Spring Boot, MySQL, Redis, Spring Security, Spring Batch
    * Infra: AWS, Docker, Jenkins, SonarQube, Prometheus, Grafana
    * AI: Python, LightGBM, SHAP, Fast API, Gemini LLM, Crontab, Pandas
   
        
## 2\. 아키텍쳐

### 2-1. 시스템 아키텍쳐
<img width="818" height="644" alt="스크린샷 2026-06-17 15 40 25" src="https://github.com/user-attachments/assets/b7e5c7e2-5f0b-4026-808e-53396743cc32" />


### 설명
사용자 프론트엔드는 CloudFront + S3로 서빙되고, API 요청은 WAF → ALB를 거쳐 두 AZ의 user_api EC2로 분산됩니다. <br>
사용자 세션은 ElastiCache(Primary/Replica)에 저장됩니다. <br>
온프레미스(OpenStack)에는 admin_front(Nginx + React), admin_back(Spring Boot), AI 서버(S등급 산출)가 각각 분리 운영됩니다.  <br>
DB는 MySQL InnoDB Cluster로 이중화하며 모든 서비스가 공유하고, 관리자 세션은 Redis에 저장됩니다. <br>
AWS와 온프레미스는 각 AZ에 배치한 WireGuard VPN으로 연결됩니다.  <br>
GitHub Push 시 Jenkins가 빌드·테스트(SonarQube 포함) 후 ECR → CodeDeploy로 자동 배포합니다. 

### 2-2. 소프트웨어 아키텍처
<img width="800" height="800" alt="image" src="https://github.com/user-attachments/assets/ef7c883a-a4df-48ff-8803-d01d337f1f43" />

### 설명
해당 아키텍처는 Presentation부터 Database까지 계층적으로 구성된 Layered 구조로, 각 레이어가 역할에 따라 분리되어 있습니다. <br> 
요청은 상위 레이어에서 하위 레이어로 순차적으로 전달되며, Controller–Service–Component–DBIO를 거쳐 데이터 처리 및 비즈니스 로직이 수행됩니다. <br>
또한 Utility와 Interface 영역을 통해 외부 시스템 연동 및 공통 기능을 분리하여, 확장성과 유지보수성을 고려한 구조로 설계되었습니다.

## 3\. 주요 기능 소개

### 3-1. 핵심 기술 구성
<img width="952" height="535" alt="image" src="https://github.com/user-attachments/assets/9c775c7f-961c-4a0d-8652-24c0b101fc7c" />


### 3-2. 통합 워크플로우 다이어그램
<img width="3064" height="2638" alt="비즈니스 프로세스 모델-비즈니스프로세스 모델 최종" src="https://github.com/user-attachments/assets/414362a7-dbfc-4b95-8fe8-1660ca631fe0" />



### 3-3. 세부 기능 소개

#### [기능 1] LightGBM 기반 성장 S등급 산출 및 SHAP + Gemini LLM 기반 성장 인사이트 생성

- **기능 설명**: 소상공인의 매출·거래·리뷰·상권 등 30개 피처를 입력으로 LightGBM 다중분류 모델이 성장 등급(S1~S10, S1이 최고)을 산출한다. 이후 SHAP(TreeExplainer / LightGBM `pred_contrib`)으로 "한 단계 위 등급"을 목표 클래스로 설정해 어떤 피처가 등급 상승에 기여(강점)하고 어떤 피처가 방해(개선 포인트)하는지 계산한 뒤, 그 결과를 Gemini LLM에 전달하여 소상공인이 이해할 수 있는 자연어 조언(`user_advice`)과 은행원이 심사에 참고할 수 있는 분석 텍스트(`admin_advice`)를 생성한다. 업력·경영주 경력처럼 사업자가 직접 바꿀 수 없는 피처는 개선 포인트에서 제외한다.

- **핵심 코드(스크립트)**:

```python
# LightGBM을 활용한 클래스 확률 예측 (shape: [1, 10] — S1~S10 각 확률)
probabilities = self._model.predict_proba(input_df)
predicted_index = int(np.argmax(probabilities[0]))
s_grade = SGrade.from_index(predicted_index)
```

```python
# SHAP 기반 강점/개선 포인트 추출 (목표 등급 기준)
shap_values = self._explainer.shap_values(input_df)
combined = shap_values[predicted_class][0]  # 한 단계 위 등급의 SHAP 값

positive_indices = np.where(combined > 0)[0]          # 강점 (양수)
negative_indices = np.where(combined < 0)[0]          # 개선 포인트 (음수)
```

```python
# Gemini LLM 자연어 조언 생성
genai.configure(api_key=settings.gemini_api_key)
self._model = genai.GenerativeModel(settings.gemini_model)
...
response = await self._model.generate_content_async(prompt)
advice = response.text.strip()
```

- **코드(스크립트) 링크**: [serving/predictor.py](https://github.com/woori-SoFit/SoFit/blob/9d4b17d94b6cb3c9c8d2f318cfc06820e032c460/ai/serving/predictor.py#L102), [serving/explainer.py](https://github.com/woori-SoFit/SoFit/blob/9d4b17d94b6cb3c9c8d2f318cfc06820e032c460/ai/serving/explainer.py#L104), [serving/advisor.py](https://github.com/woori-SoFit/SoFit/blob/9d4b17d94b6cb3c9c8d2f318cfc06820e032c460/ai/serving/advisor.py#L87)



#### [기능 3] Redis Pub/Sub + SSE를 활용한 실시간 알림 시스템
  - **기능 설명** : 대출 심사 완료·실행 등 주요 이벤트 발생 시, Redis Pub/Sub으로 메시지를 브로드캐스트하고 SSE(Server-Sent Events)를 통해 클라이언트에게 실시간 푸시 알림을 전달합니다. <br> 서버 이중화(스케일아웃) 환경에서도 모든 인스턴스가 Redis 채널을 구독하여 해당 유저의 SSE 연결을 보유한 인스턴스에서만 전송하는 구조로, 단일 장애 지점 없이 실시간 알림을 보장합니다.
  - **핵심 코드(스크립트)** :
 
  ```java
  // Redis Pub/Sub 수신 → SSE 전달 (RedisNotificationSubscriber.java)
  @Override
  public void onMessage(Message message, byte[] pattern) {
      NotificationPushRequest request = notificationSerializer.deserialize(message.getBody());
      sseEmitterManager.send(request.getUserId(), request);
  }

  // SSE emitter 등록 (SseEmitterManager.java)
  public SseEmitter subscribe(Long userId) {
      SseEmitter emitter = new SseEmitter(TIMEOUT);
      emitters.put(userId, emitter);
      emitter.onCompletion(() -> emitters.remove(userId, emitter));
      emitter.onTimeout(() -> emitters.remove(userId, emitter));
      // ...
      return emitter;
  }

  // SSE 전송 (SseEmitterManager.java)
  public void send(Long userId, NotificationPushRequest payload) {
      SseEmitter emitter = emitters.get(userId);
      if (emitter == null) return;
      emitter.send(SseEmitter.event().name("notification").data(payload));
  }
  ```
  - 코드 링크(스크립트 링크) : [notification/RedisNotificationSubscriber.java](https://github.com/woori-SoFit/SoFit/blob/9d4b17d94b6cb3c9c8d2f318cfc06820e032c460/backend/sofit-user/src/main/java/com/sofit/user/domain/notification/service/RedisNotificationSubscriber.java#L21),[notification/SseEmitterManager.java](https://github.com/woori-SoFit/SoFit/blob/9d4b17d94b6cb3c9c8d2f318cfc06820e032c460/backend/sofit-user/src/main/java/com/sofit/user/domain/notification/service/SseEmitterManager.java#L26)

#### [기능 4] Redis 세션 기반 로그인 보안
- **기능 설명**: 세션 인증의 보안성을 세 가지 축으로 강화한다.
① 만료 정책: 슬라이딩 10분(Redis TTL 자동 갱신, 유휴 시 만료) + 절대 12시간(SessionValidationFilter에서 loginTime 체크)의 이중 구조 <br>
② 브루트포스 방어: Redis에 IP별(5회/15분) · 계정별(10회/30분) 실패 카운터를 두고 TTL로 자동 잠금 해제한다. <br>
③ 동시 로그인 제어는 새 로그인 시 기존 세션을 만료시켜 단일 세션을 유지한다 <br>


- **핵심 코드(스크립트)**:

```java
// RedisSessionConfig.java — 슬라이딩 10분
@EnableRedisIndexedHttpSession(maxInactiveIntervalInSeconds = 600)
public class RedisSessionConfig { ... }
```

```java
// SessionValidationFilter.java — 절대 만료 12시간
if (loginTime != null && loginTime.plusHours(12).isBefore(LocalDateTime.now())) {
    session.invalidate();
}
```

```java
// LoginAttemptService.java — IP/계정별 잠금
public boolean isBlocked(String loginId, String ipAddress) {
    if (isIpBlocked(ipAddress)) return true;   // 5회 → 15분 잠금
    return isAccountBlocked(loginId);           // 10회 → 30분 잠금
}
```

```java
// SecurityConfig.java — 동시 로그인: 새 로그인 시 기존 세션 만료
.maximumSessions(1)
.maxSessionsPreventsLogin(false)
```

- **코드(스크립트) 링크**: 
[RedisSessionConfig.java](https://github.com/woori-SoFit/SoFit/blob/main/backend/sofit-user/src/main/java/com/sofit/user/global/config/RedisSessionConfig.java), [SessionValidationFilter.java](
https://github.com/woori-SoFit/SoFit/blob/main/backend/sofit-user/src/main/java/com/sofit/user/global/filter/SessionValidationFilter.java), [LoginAttemptService.java](https://github.com/woori-SoFit/SoFit/blob/main/backend/sofit-user/src/main/java/com/sofit/user/domain/auth/service/LoginAttemptService.java)
 
  
#### [기능 5] FastAPI + Crontab으로 이중 배치 서빙 구조

- **기능 설명**: S등급 산출을 두 가지 흐름으로 분리해 서빙한다.
① **건별 산출**은 Spring BE가 회원가입 등 이벤트 시점에 FastAPI `/api/s-grade/predict`를 동기 호출해 즉시 결과를 반환한다.
② **월별 전체 갱신**은 호스트의 crontab이 매월 1일 23:40에 `python -m batch.run_batch`를 실행하여 전체 회원의 등급을 한 번에 재산출하고 DB(`s_grade_report`, `s_grade_history`)에 적재한다.
또한 FastAPI에는 `/api/s-grade/batch` 트리거 API도 있어, 은행원이 관리자 페이지에서 수동으로 배치를 실행할 수 있다. 배치 실행 중 중복 트리거는 인메모리 플래그 + DB 상태 조회로 막고, 사용자별 처리 실패 시 최대 3회 재시도 후 `FAILED` 처리하며, 이전 배치가 비정상 종료되어 남은 `CALCULATING` 고아 건은 배치 시작 시 복구한다.

- **핵심 코드(스크립트)**:

```python
# batch/run_batch.py — crontab 진입점
# 스케줄링 (crontab 예시):
#   40 23 1 * * cd /home/ubuntu/SoFit-AI && python -m batch.run_batch
def main() -> None:
    ...
    asyncio.run(run_monthly_batch(execution_type=execution_type, triggered_by=triggered_by))
```

```python
# serving/main.py — FastAPI 수동/자동 배치 트리거 (이중화: HTTP API + crontab)
@app.post("/api/s-grade/batch", status_code=status.HTTP_202_ACCEPTED)
async def trigger_monthly_batch(triggered_by: int | None = Query(default=None)):
    async with _batch_lock:
        if _batch_running or await asyncio.to_thread(is_batch_running_in_db):
            raise HTTPException(status_code=409, detail="이미 배치가 실행 중입니다.")
        _batch_running = True
        asyncio.create_task(_run_batch_background(triggered_by))
    return BatchTriggerResponse(message="배치 실행이 시작되었습니다.")
```

```python
# batch/pipeline.py — 월별 배치 본체 (재시도 + 고아 건 복구)
recover_orphaned_calculating(conn)
...
while retry_count < MAX_RETRY_COUNT and not success:
    try:
        await process_single_user(model, row, s_grade_id, batch_execution_id, conn)
        success = True
    except Exception as e:
        retry_count += 1
        conn.rollback()
        if retry_count >= MAX_RETRY_COUNT:
            fail_grade_history(conn, s_grade_id)
```

- **코드(스크립트) 링크**: [batch/run_batch.py](https://github.com/woori-SoFit/SoFit/blob/9d4b17d94b6cb3c9c8d2f318cfc06820e032c460/ai/batch/run_batch.py#L35), [serving/main.py](https://github.com/woori-SoFit/SoFit/blob/9d4b17d94b6cb3c9c8d2f318cfc06820e032c460/ai/serving/main.py#L140),[batch/pipeline.py](https://github.com/woori-SoFit/SoFit/blob/9d4b17d94b6cb3c9c8d2f318cfc06820e032c460/ai/batch/pipeline.py#L622) 

