# 🏢 LG 그룹 공식 홈페이지 클론 프로젝트 (Web Service Platform)

> 라즈베리파이 온프레미스 서버 구축부터 프론트엔드 커스텀, SEO 및 웹 접근성 최적화까지 아우르는 **13주 풀스택 웹 개발 여정**입니다.

[![Website](https://img.shields.io/badge/Website-lg.kimgyutae.com-A50034?style=for-the-badge&logo=google-chrome&logoColor=white)](https://lg.kimgyutae.com/)
[![WordPress](https://img.shields.io/badge/WordPress-7.0-21759b?style=for-the-badge&logo=wordpress&logoColor=white)](https://wordpress.org/)
[![Raspberry Pi](https://img.shields.io/badge/Raspberry%20Pi-5-A22846?style=for-the-badge&logo=raspberry-pi&logoColor=white)](https://www.raspberrypi.com/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-Tunnel-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://www.cloudflare.com/)

---

## 📌 프로젝트 개요

클라우드 호스팅(AWS, 가비아 등)에 의존하지 않고, 물리적 서버(Raspberry Pi 5) 하드웨어 조립부터 네트워크 세팅, 보안 인프라, CMS 구축, 프론트엔드 디자인, 최종 배포까지 **웹 서비스가 구동되는 A to Z를 직접 경험**하기 위해 기획된 프로젝트입니다.

- **목표:** LG 그룹 공식 홈페이지(lg.co.kr)의 정보 구조와 LG Careers(careers.lg.com)의 UI/UX를 벤치마킹하여 실무 수준의 기업형 웹사이트 구축
- **개발 기간:** 2026학년도 1학기 (약 16주 중 13주 핵심 개발)
- **참여 인원:** 김규태, 황주은

---

## 🛠 기술 스택 (Tech Stack)

### Infrastructure & Network
- **H/W:** Raspberry Pi 5 (8GB RAM, NVMe SSD)
- **OS:** Raspberry Pi OS (64-bit Bookworm)
- **Network/Security:** Cloudflare (DNS, CDN, SSL, DDoS), Cloudflare Tunnel (Argo Tunnel), UFW, fail2ban

### Backend & Server
- **Web Server Panel:** CloudPanel
- **Stack:** Nginx, PHP 8.x, MariaDB
- **CMS:** WordPress 7.0

### Frontend & UI
- **Theme/Builder:** Blocksy v2.1.40, Elementor v4.0.7
- **Languages:** HTML5, CSS3, JavaScript (Vanilla)
- **Fonts/Icons:** Noto Sans KR, FontAwesome, SVG Icons

### Plugins (WP)
- ACF (Advanced Custom Fields), CPT UI, AIOS (All-In-One Security), WP-Optimize, UpdraftPlus 등

---

## 🏗 시스템 아키텍처 (System Architecture)

가정용 네트워크의 한계(동적 IP, 포트 제한)를 극복하기 위해 **Cloudflare Tunnel**을 도입하여 안전하고 빠르고 안정적인 아키텍처를 구현했습니다.

```mermaid
graph LR
    User[사용자 브라우저] -->|HTTPS| CF[Cloudflare Edge<br>DNS / CDN / WAF]
    CF -->|Cloudflare Tunnel<br>보안 연결| PI[Raspberry Pi 5<br>On-Premise Server]
    
    subgraph PI[Raspberry Pi 5 (CloudPanel)]
        Nginx[Nginx Web Server] --> PHP[PHP-FPM 8.x]
        PHP --> WP[WordPress Core]
        WP <--> DB[(MariaDB)]
    end
```

---

## 🚀 주요 구현 기능

### 1. 프론트엔드 (UI/UX & Interactions)
- **커스텀 메가 메뉴:** CSS `:has()` 선택자를 활용하여 JS 없이 부드럽게 확장되는 전역 네비게이션 구현.
- **REST API 동적 게시판:** WordPress REST API(`/wp-json/wp/v2/posts`)를 비동기 Fetch하여 메인 페이지 뉴스 카드 그리드 렌더링.
- **도트 네비게이션 (Full-page Scroll):** 휠 이벤트 감지 및 부드러운 스크롤 이동을 지원하는 커스텀 바닐라 JS 작성 (배경색에 따른 자동 색상 반전 포함).
- **반응형 웹 디자인:** Desktop, Tablet, Mobile 모든 환경에 최적화된 Grid/Flexbox 레이아웃.

### 2. 백엔드 (CMS Data Modeling)
- **커스텀 포스트 타입(CPT):** 기본 Post 외에 `business_report`, `audit_report`, `ir_information` 구축.
- **커스텀 필드(ACF):** 첨부파일, 발행 연도, 문서 유형 등 전문 데이터를 다루기 위한 메타 데이터 필드 매핑.
- **보안 강화:** AIOS 플러그인을 통한 기본 로그인 URL 변경(`/open_the_door`), 무차별 대입 공격(Brute Force) 방어.

### 3. 품질 최적화 (SEO & a11y)
- **Technical SEO:** 고유 `<title>`, Canonical URL, Schema.org 마크업, XML 사이트맵 분리 생성, Speculation Rules 기반 Prefetch 적용.
- **웹 접근성 (WCAG 2.1 AA):** 스킵 네비게이션, 시맨틱 태그, ARIA 랜드마크 및 속성(`aria-expanded`, `inert` 등), 키보드 네비게이션 및 색상 대비 최적화.

---

## 🔥 트러블슈팅 경험

### 1. Cloudflare Error 1033 (Argo Tunnel Error)
- **문제:** 간헐적으로 터널이 끊기며 사이트 접속이 차단됨. 데몬 크래시 및 라우터의 동적 IP 변경이 겹친 현상.
- **해결:** `cloudflared` systemd 서비스에 `Restart=always` 및 `WatchdogSec` 추가. 라우터 레벨에서 라즈베리파이 MAC 주소 기반 고정 IP 할당 후 터널 라우팅 재구성.
- **결과:** 서버 가동률 95% 미만에서 99.9%로 극적 향상.

### 2. 헤더 스크롤 CSS Specificity 충돌
- **문제:** 홈페이지의 투명 헤더 효과와 Blocksy 테마의 스티키(Sticky) 헤더, 커스텀 메가 메뉴 배경이 서로 우선순위 경합을 벌여 스크롤 시 화면이 깜빡임.
- **해결:** CSS 스코핑 기법을 활용하여 홈페이지 전용 클래스(`.page-id-838`)로 투명 배경을 격리. 메가 메뉴의 배경 트랜지션을 분리 적용.
- **결과:** 0.4초의 부드러운 `ease` 트랜지션으로 이질감 없는 헤더 UI 완성.

---

## 📅 13주 차 커리큘럼 로드맵

- **Phase 1. 인프라 구축 (1~4주):** 라즈베리파이 조립, OS 설치, 고정 IP/UFW 설정, Cloudflare 터널 연결.
- **Phase 2. 환경 구성 및 디자인 (5~8주):** CloudPanel/WP 설치, 테마 벤치마킹, IA 설계, 메가 메뉴 구현, CPT 데이터 구축.
- **Phase 3. 커스텀 개발 및 트러블슈팅 (9~10주):** JS 동적 게시판, 도트 네비게이션, CSS 충돌 및 Error 1033 집중 해결.
- **Phase 4. 최적화 (11~12주):** 웹 표준 SEO 적용, 스크린 리더 호환 ARIA 속성 및 웹 접근성 개선.
- **Phase 5. 배포 및 문서화 (13주):** 크로스 브라우징/Lighthouse 성능 테스트, 기획서 및 명세서 최종 산출물 정리.

---

## 📂 프로젝트 산출물 문서
프로젝트의 세부적인 기획 및 설계 과정은 다음 문서들에서 확인할 수 있습니다.
*(저장소 내 문서 폴더 등 경로가 있다면 추후 링크 업데이트 요망)*
1. 웹 서비스 기획서 (WBS 포함)
2. 요구사항 명세서 (SRS)
3. 기능 명세서 (124개 기능)
4. 종합 발표 기획서 (PPT 스토리보드)

---

## 📜 License
This project is for educational purposes.
ⓒ 2026 LG Corp. Designed by 김규태 & 황주은.
