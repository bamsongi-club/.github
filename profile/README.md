# 밤송이클럽

**기능 구현에 머물지 않고 실패·동시성·운영 경계를 코드와 측정으로 검증하는 4인 백엔드 팀입니다.**

**프로젝트 기간:** 2026.07.21–2026.08.24

알밤메이트의 1~3차 MVP를 만들며 각자 맡은 기능의 설계·구현·테스트를 책임지고, 배포와 실측은 별도 상태로 검증했습니다.

[서비스 체험하기](https://bamsongiclub.cloud) · [프로젝트 저장소](https://github.com/bamsongi-club/albam-mate) · [로컬 실행·테스트](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/README.md#로컬-실행과-테스트) · [팀원별 문제 해결](#팀원별-문제-해결과-판단) · [부하 검증 환경](#측정에-사용한-부하-검증-환경) · [Second Brain Demo](https://github.com/bamsongi-club/bamsongi-brain-demo)

## 대표 프로젝트 · 알밤메이트

보드게임 모임은 게임·일정·장소·진행 방식·남은 자리를 각각 따로 확인해야 잡을 수 있습니다.
알밤메이트는 흩어진 정보를 한 화면에 모아 참가까지 이어줍니다.

<p align="center">
  <img
    src="./assets/albam-mate-product-overview.png"
    alt="공개용 합성 데이터로 재현한 알밤메이트 대표 모임 상세 화면"
    width="960"
  />
</p>

<p align="center"><sub>개인정보가 없는 공개용 합성 데이터로 재현한 화면입니다.</sub></p>

### 현재 상태

| 범위 | 상태 |
| --- | --- |
| 1·2차 MVP | 방 성립, 인증·검색·알림·채팅의 구현과 검증을 마치고 완료 시점의 범위와 제한사항을 아카이브했습니다. |
| 3차 MVP | AI 모임 도우미, 의미 기반 검색, 인기순 정렬, 채팅 목록·시스템 메시지, 운영 관측을 구현하고 자동 검증했습니다. 실시간 파티 매칭은 백엔드 구현과 부분 검증까지 마쳤습니다. |
| 배포·실측 | AI 기능군·의미 검색·실시간 매칭의 운영 배포와 최종 실측은 남아 있습니다. 운영 관측 일부는 고정 release의 임시 AWS 검증과 teardown까지 완료했습니다. |
| 공개 화면 | [bamsongiclub.cloud](https://bamsongiclub.cloud) · 2026-08-24 HTTPS `200` 응답 확인 |

### 기술 구성

| 구분 | 기술과 구조 |
| --- | --- |
| 구조 | 도메인 중심 모듈러 모놀리스 |
| 애플리케이션 | Java 21 · Spring Boot · Spring MVC · Spring Data JPA |
| 데이터·보안 | PostgreSQL · Flyway · Redis · Spring Security · Spring Session |
| 실시간·비동기 | WebSocket · Redis Pub/Sub · Transactional Outbox |
| AI·검색 | OpenAI · Cloudflare Workers AI · pgvector · `pg_trgm` |
| 검증·관측 | JUnit 5 · Testcontainers · k6 · Actuator · Micrometer OTLP |
| 부하 검증 환경 | [캠페인별 격리 AWS 스택](#측정에-사용한-부하-검증-환경) · App 2대 · PostgreSQL · Redis · 전용 k6 발생기 |
| 배포 경계 | 같은 SHA의 ARM64 이미지를 OIDC·SSM으로 전달하는 4 EC2 자동 배포 계약 · 무중단 상시 운영으로 판정하지 않음 |

[현재 제공 상태](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/README.md#현재-제공-상태) · [기능별 상태 정본](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/docs/p2/README.md#기능별-현재-상태) · [아키텍처](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/docs/ARCHITECTURE.md)

## 팀의 협업과 검증 기준

- 구현·검증·배포·측정을 같은 완료 상태로 묶지 않습니다.
- 성능 수치는 환경·조건·실패 구간과 함께 기록합니다.
- 회의에서 나온 의견은 코드·ADR·Issue와 대조한 뒤 사람이 결정합니다.
- 확인하지 못한 범위는 완료로 표현하지 않고 후속 검증으로 남깁니다.

## 팀

| 팀원 | 담당 영역 |
| --- | --- |
| 임지호 · [@vanilalatte03](https://github.com/vanilalatte03) | 인증·사용자 / 알림·Outbox / 운영 관측·대시보드 / CI·AI 협업 체계 |
| 한예진 · [@beyejin](https://github.com/beyejin) | 게임 카탈로그·검색 / 의미 기반 게임 검색 / 소셜 로그인·계정 연결 / 인기 게임 랭킹 |
| 양지원 · [@gone09-sketch](https://github.com/gone09-sketch) | ROOM 생명주기·상태 보정 / 대기열·동시성 검증 / 실시간 파티 매칭 |
| 전은기 · [@silverThunder09](https://github.com/silverThunder09) | 채팅·WebSocket / 방 참가 / AI 모임 도우미 / 자동 배포 파이프라인 |

프론트엔드 화면과 기능 간 통합 테스트는 네 명이 함께 진행합니다.

## 팀원별 문제 해결과 판단

### 임지호 · 측정 결과로 메시지 브로커 도입을 보류

PostgreSQL transactional outbox와 polling relay를 유지한 채 App 2대에서 수신자를 1명, 5명, 10명으로
늘리며 9회 측정했습니다. server-side p95는 4.210~4.968초였고 최종 backlog, 실패, retry는 모두
0건이었습니다. 현재 검증 범위에서는 Kafka나 RabbitMQ를 도입할 근거가 없다고 판단했습니다. 지속 혼합
부하는 App cgroup OOM으로 유효한 경계를 만들지 못했으므로 relay 포화점은 미측정으로 남겼습니다.
[알림 broker 판단서](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/docs/measurements/k6/jiho/notification-broker-decision-2026-08-11.md)

### 한예진 · 게임명 부분 검색에 GIN 적용

17만 건 카탈로그에서 같은 조건으로 `pg_trgm` GIN 인덱스 적용 전후를 비교했습니다. 검색 단독 시나리오의
p95는 3,034.1 ms에서 9.7 ms로 줄었지만, 여러 조회를 섞은 부하는 인덱스 적용 뒤에도 기준을 넘었습니다.
측정 결과에 따라 이름 부분 검색에 GIN을 도입하고 Flyway 마이그레이션과 PostgreSQL 회귀 테스트에
반영했습니다. 혼합 부하의 병목은 별도 과제로 남겼습니다.
[검색 인덱스 판단](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/docs/measurements/k6/yejin/keyword-search-index-decision-2026-08-11.md) · [Flyway 마이그레이션](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/src/main/resources/db/vendor-migration/postgresql/V26__add_game_name_trigram_index.sql)

### 양지원 · 다중 matcher의 중복 제안 방지

PostgreSQL `SELECT … FOR UPDATE SKIP LOCKED` 기반 claim과 같은 트랜잭션의 상태 전이로 하나의 대기 요청이
여러 제안에 들어가거나 일부 참가자만 전이되는 상황을 막았습니다. 독립 matcher 2개가 각 500회씩 경합한
measured round 3회에서 제안 500개와 참가자 전이 1,000개가 정확히 일치했고, 중복 점유와 부분 claim은
모두 0건이었습니다. candidate claim p95 최댓값은 36.4ms였으며 최종 수락·복구 성능은 별도 범위로 남겼습니다.
[측정 계약](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/docs/measurements/match-01-candidate-search-baseline-contract.md) · [측정 결과](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/docs/measurements/results/match-01/candidate-claim/candidate-claim-c017d2f52f6548dc85ab86fed0f0d668397a3fe3.json)

### 전은기 · 채팅 부하의 HTTP 500 제거

채팅 부하에서 HTTP 500이 700~760건 발생하고 활성 방 응답 p95가 3,119~5,105ms까지 증가했습니다. Redis
연결 팩토리를 세션 경로와 나머지 경로로 분리하고 남은 연결 실패는 500 대신 503으로 응답하도록 바꿨습니다.
수정 뒤 HTTP 500과 `RedisConnectionFailureException`은 0건, 활성 방 성공률은 100%, 응답 p95는 36ms가
됐습니다. 원인을 규명하지 못한 WebSocket 연결 실패는 별도 문제로 남겼습니다.
[수정 후 측정](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/docs/measurements/k6/eungi/chat-delivery-capacity-2026-08-12-after-607.md) · [반복 측정](https://github.com/bamsongi-club/albam-mate/blob/d11b6ad3a8f64a52ec332f1dab27599c266645b8/docs/measurements/k6/eungi/chat-delivery-capacity-2026-08-13-after-607-repeat.md)

## 측정에 사용한 부하 검증 환경

위 측정은 캠페인별로 격리한 임시 AWS 스택에서 실행했습니다. 그 스택의 Terraform·Ansible·CloudWatch 구성을
[Albam Mate Infrastructure Reference](https://github.com/bamsongi-club/albam-mate-infra-reference)로
공개했습니다. 운영 정본은 비공개 저장소에 남아 있고, 공개본은 2026-08-20 시점을 떼어 정제한 날짜 고정
스냅샷입니다.

| 구분 | 내용 |
| --- | --- |
| 구성 | App1 Nginx 단일 진입점 · Spring EC2 2대 · PostgreSQL · Redis · 분리된 VPC의 k6 발생기 |
| 인스턴스 | ARM64 Amazon Linux 2023 `t4g.micro` 4대 · CPU credit `standard` |
| 측정 조건 | ALB·NAT Gateway·Auto Scaling Group을 두지 않아 스케일 이벤트가 before/after를 흔들지 않습니다 |
| 수명 | `bootstrap → rebase → deploy → loadtest → destroy` · teardown 후 잔존 0건을 재조회로 확인합니다 |
| 자격증명 없는 검증 | `mock_provider`를 쓰는 Terraform 계약 테스트와 공개 경계 검사를 새 clone에서 그대로 실행할 수 있습니다 |

이 저장소도 완료 상태를 하나로 묶지 않습니다. 항목마다 `planned`·`implemented`·`verified`·`deployed`·
`measured`·`teardown`을 각각 적었습니다. 공개 스냅샷 자체의 확인 범위는 2026-08-20
`apply → deploy → 내부 health → destroy` 완주까지이고, `rebase`(이미지 빌드)와 `loadtest`까지 이어서
완주한 검증은 아닙니다.

[증거 상태](https://github.com/bamsongi-club/albam-mate-infra-reference/blob/main/docs/EVIDENCE_STATUS.md) · [설계 판단과 한계](https://github.com/bamsongi-club/albam-mate-infra-reference/blob/main/docs/DESIGN_DECISIONS.md) · [스냅샷 출처](https://github.com/bamsongi-club/albam-mate-infra-reference/blob/main/SOURCE_SNAPSHOT.md)

## 팀의 판단을 축적하는 Second Brain

### 회의 기록을 결정으로 바꾸는 과정

**회의 원문 보존 → 결정 후보 정리 → 프로젝트 정본 대조 → 사람 검수 → 공개 범위 분리**

회의록을 곧바로 결정으로 올리지 않습니다. Private Second Brain에는 원문과 결정 후보를 분리해 보존하고,
현재 계약은 프로젝트의 코드·ADR·Issue와 대조합니다. AI는 후보 탐색과 초안을 보조하지만, 사실 판정과
채택·보류·공개 여부는 사람이 정합니다.

<p align="center">
  <img
    src="./assets/bamsongi-brain-operating-graph-2026-08-18.png"
    alt="문서 제목과 내용을 표시하지 않은 실제 Private Brain 그래프"
    width="800"
  />
</p>

<p align="center"><sub>실제 팀 운영에 사용한 Private Second Brain의 그래프입니다. 2026-08-18 캡처 · 문서 제목·본문·내부 경로 미표시</sub></p>

Second Brain은 회의록 보관함에 머물지 않습니다. 대체된 결정과 아직 확인하지 못한 항목을 함께 남기고,
프로젝트 정본이 바뀌면 현재 상태를 다시 대조합니다. 공개할 때는 원문을 옮기지 않고 공개 GitHub 근거로
판정을 다시 구성합니다.

공개한 [Bamsongi Brain Demo](https://github.com/bamsongi-club/bamsongi-brain-demo)는 실제 자료를 복제하거나
익명화한 저장소가 아닙니다. 공개 GitHub 근거만으로 결정 상태, 구현·검증 단계와 사람 승인 게이트를
재현했습니다.

[데모 실행하기](https://github.com/bamsongi-club/bamsongi-brain-demo#바로-실행하기) · [공개 사례 전체 보기](https://github.com/bamsongi-club/bamsongi-brain-demo/blob/main/cases/README.md)
