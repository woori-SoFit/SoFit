# SoFit

<img width="1920" height="1080" alt="우리FISA 6기 - SOFIT 최종발표" src="https://github.com/user-attachments/assets/2b8c5088-a355-41d7-9fb1-536e53581b5b" />


### 소상공인을 위한 **성장 신용 대출**! 더 많은 사장님에게 **맞춤 금융 기회**를 제공합니다.

> 2026 우리FIS 아카데미 최종 프로젝트 1등 수상작! 🥇

본 프로젝트는 정부의 소상공인 특화 신용평가모형(SCB) 시범사업 방향을 참고하여, 사업자 CB등급과 자체 성장 S등급을 결합한 대출 플랫폼을 구현했습니다.<br> 
소상공인의 사업성을 보다 공정하게 평가하고, 사용자가 대출 신청부터 심사, 실행, 사후관리까지 하나의 플랫폼에서 편리하게 이용할 수 있도록 지원합니다.

<br>

## 👥 팀원

<table>
  <tr>
    <th align="center">오색빛</th>
    <th align="center">남민영</th>
    <th align="center">고희연</th>
    <th align="center">김시온</th>
    <th align="center">이혜윤</th>
  </tr>
  <tr>
    <td align="center">
      <img width="180" alt="스크린샷 2026-06-22 오후 12 40 22" src="https://github.com/user-attachments/assets/3491bb64-ec58-47a0-9f7e-1342a150f2d6" />
    </td>
    <td align="center">
      <img width="180" alt="스크린샷 2026-06-22 오후 12 40 26" src="https://github.com/user-attachments/assets/d06523ed-8be2-4433-85a2-ba77684339a6" />
    </td>
    <td align="center">
      <img width="180" alt="스크린샷 2026-06-22 오후 12 40 32" src="https://github.com/user-attachments/assets/6244b688-0e6a-4bcb-8569-b764c24798c5" />
    </td>
    <td align="center">
      <img width="180" alt="스크린샷 2026-06-22 오후 12 40 38" src="https://github.com/user-attachments/assets/00dab343-f0b3-4bf5-bd92-8aca696df5c7" />
    </td>
    <td align="center">
      <img width="180" alt="스크린샷 2026-06-22 오후 12 40 44" src="https://github.com/user-attachments/assets/46d806c0-ca17-4c62-98a9-943466494791" />
    </td>
  </tr>
  <tr>
    <td align="center">
      <b>PM</b><br/>
      <a href="https://github.com/light11014">@light11014</a>
    </td>
    <td align="center">
      <b>TL & AI Leader</b><br/>
      <a href="https://github.com/Minyeong0724">@Minyeong0724</a>
    </td>
    <td align="center">
      <b>Backend Leader</b><br/>
      <a href="https://github.com/HeeYeon-Ko">@HeeYeon-Ko</a>
    </td>
    <td align="center">
      <b>Infra Leader</b><br/>
      <a href="https://github.com/noiskk">@noiskk</a>
    </td>
    <td align="center">
      <b>Frontend Leader</b><br/>
      <a href="https://github.com/hyeyoon23">@hyeyoon23</a>
    </td>
  </tr>
</table>


<br><br>

## 🗓️ WBS

<img width="1062" height="790" alt="image" src="https://github.com/user-attachments/assets/75a1eaa6-c74c-4d80-bbf5-ac0ebf423788" />

<br><br>

## 🛠️ 기술 스택 및 서비스 아키텍처

<img width="832" height="480" alt="우리FISA 6기 - SOFIT 최종발표 (3)" src="https://github.com/user-attachments/assets/7b32c757-7f47-4f1b-95a4-d41d19c3869d" />


<img width="832" height="652" alt="image" src="https://github.com/user-attachments/assets/2b4df34f-7ab7-4adf-b146-aeed95d0e270" />

<br><br>

## 🗄️ ERD

<img width="4016" height="2800" alt="sofit_erd_260529" src="https://github.com/user-attachments/assets/80b2c8e9-41f7-4e20-930e-bb7b911ba2f0" />

<br><br>

## 🤖 핵심 AI 성장평가 모델

- 사용자의 마이비즈 데이터를 기반으로 LightGBM 모델이 성장 S등급을 산출하고, SHAP 기반 XAI를 통해 주요 평가 요인을 분석합니다.
- Gemini LLM을 활용해 사용자와 은행원 각각에게 맞춤형 조언을 제공함으로써, 단순한 신용 평가를 넘어 설명 가능한 AI 기반 성장 평가를 지원합니다.

<img width="1920" height="1080" alt="우리FISA 6기 - SOFIT 최종발표 (7)" src="https://github.com/user-attachments/assets/1448467f-8ce0-4124-9cbb-4d355c1b78da" />

<br><br>

## 🚀 서비스 주요 기능

### 대출 서비스 — 사장님 맞춤 비대면 대출

- 회원가입부터 대출 실행까지 **단계별 멀티스텝**으로 쉽고 빠르게 신청을 완료합니다.
  - **본인인증** — 사업자 인증(KYC, 국세청 API Mock) + 금융인증서 연동 + PIN 전자서명
  - **상품 조회·계산** — 업종·금리·한도 필터 조회 + 원리금균등·원금균등·만기상환 사전 계산기
  - **신청·약정·실행** — 약관 동의 → 마이데이터 자동 연동 → 약정(전자서명) → 대출 실행
- 서류 직접 제출 없이, 사전 연동된 데이터로 비대면 신청이 가능합니다.

| 대출 신청 | 대출 진행 관리 | 약정 체결 및 대출 실행 | 실행 대출 관리 |
|------|------|------|------|
| <img width="334" height="723" alt="스크린샷 2026-06-15 15 13 01" src="https://github.com/user-attachments/assets/9c7c6976-c56e-4688-91bb-6999be634564" /> | <img width="334" height="723" alt="스크린샷 2026-06-15 15 26 27" src="https://github.com/user-attachments/assets/c11af4c8-7d08-4f55-b102-99e67991ae1e" /> | <img width="334" height="723" alt="localhost_5173_(iPhone 12 Pro) (20)" src="https://github.com/user-attachments/assets/3f2ee13a-f33d-4bae-ad1e-79ce0862d89d" /> |<img width="334" height="723" alt="localhost_5173_(iPhone 12 Pro) (25)" src="https://github.com/user-attachments/assets/1ee78e3d-0a27-4a66-abec-d7e11eb32ad1" />|

<br><br>

### 마이비즈데이터 — 우리 가게 데이터를 한눈에

- 사업장의 **매출·수익·고객·거래 데이터**를 월 단위로 분석해 대시보드로 제공합니다.
- 배달앱 매출·리뷰·평점, 입출금, 순이익, 부가세 신고 현황 등 흩어진 가게 데이터를 한 화면에 모았습니다.
- 이 데이터가 곧 AI 성장 등급 평가의 입력값이 되어, **비금융 데이터 기반 신용평가**를 가능하게 합니다.

| 매출 분석 | 손익 현황 | 고객/온라인 활동 | 업종/상권 분석 |
|------|------|------|------|
| <img width="334" height="723" alt="image" src="https://github.com/user-attachments/assets/90164677-840f-4550-af6a-b37c8e926f4f" /> | <img width="334" height="723" alt="image" src="https://github.com/user-attachments/assets/b464806b-afdd-4d1a-b178-7744b01b7574" /> | <img width="334" height="723" alt="image" src="https://github.com/user-attachments/assets/8e89c32b-33c5-4c96-8a8d-c7e214c89069" /> | <img width="334" height="723" alt="image" src="https://github.com/user-attachments/assets/4e3f1c7c-beb2-42ec-bda9-9127fae5fb0e" /> |

<br><br>

### 성장 등급 리포트 — AI 성장 등급 진단

- 대표자 개인 신용(CB)이 아닌 **사업의 성장 가능성**을 LightGBM 모델로 분석해 **S1~S10 등급**으로 환산합니다.
- **SHAP 기반 XAI 리포트** — 등급을 *왜* 받았는지 항목별 기여도와 개선 방향까지 설명합니다.

| 성장 등급 리포트 | 종합 성장 등급 | 강점/개선 영역 |
|------|------|------|
| <img width="230" height="490" alt="스크린샷 2026-06-17 12 12 57" src="https://github.com/user-attachments/assets/212ced66-b834-40a9-9b9a-92faabd225c1" /> | <img width="230" height="490" alt="sofit cloud_(iPhone 12 Pro) (2)" src="https://github.com/user-attachments/assets/e799b71d-cb2a-4c92-a4b2-4af90aac3798" /> | <img width="230" height="490" alt="sofit cloud_(iPhone 12 Pro) (3)" src="https://github.com/user-attachments/assets/3d14f55d-7bcc-408f-abf5-88a17a5f43c9" />|

<br><br>

### 대출 진행 알림 — 신청부터 실행까지 실시간
- 심사 단계 변경, 승인·거절 등 주요 이벤트를 **신청부터 실행까지 실시간으로 알림**합니다.
- 결과만 통보받던 기존 방식과 달리, 진행 상황을 단계별로 투명하게 확인할 수 있습니다.

| 알림 내역 | 
|------|
| <img width="230" height="490" alt="sofit cloud_(iPhone 12 Pro) (2) (1)" src="https://github.com/user-attachments/assets/39a29db3-4733-468a-bd07-2bf3e74c8274" />|

<br><br>

## 📦 산출물

### API 명세서

- 사용자, 은행원, AI 서버 간 주요 기능 흐름을 기준으로 총 64개의 API를 설계하고 문서화했습니다.

<img width="1920" height="1080" alt="우리FISA 6기 - SOFIT 최종발표 (4)" src="https://github.com/user-attachments/assets/1d12d30d-01e4-4cb2-b2cf-5ef434842107" />


### UI / 와이어프레임

- 서비스 기획 단계에서 와이어프레임을 먼저 설계하고, 이를 바탕으로 최종 UI 화면을 구체화했습니다.

<img width="1920" height="1080" alt="우리FISA 6기 - SOFIT 최종발표 (5)" src="https://github.com/user-attachments/assets/cdd7090f-2ce3-4cd9-8833-4711fd636639" />


