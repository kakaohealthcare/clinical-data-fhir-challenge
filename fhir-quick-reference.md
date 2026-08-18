# FHIR R4 빠른 참조

과제 풀이 시간을 아끼기 위한 요약본입니다. **아래 리소스가 모두 이번 과제에 필요한 것은 아닙니다** — 어떤 리소스를 쓸지는 여러분이 판단하세요. 정확한 정의는 공식 문서(<https://hl7.org/fhir/R4/resourcelist.html>)가 기준입니다.

표기: Card. `1..1` = 필수, `0..1`/`0..*` = 선택 (`0..*`는 JSON에서 배열).

## 공통 문법

- 모든 리소스는 `"resourceType"`과 `"id"`를 가진 JSON 객체입니다.
- **Reference** (다른 리소스 참조): `{ "reference": "리소스타입/리소스id" }`
- **code**: 문자열 하나. 허용 값 집합이 스펙에 고정돼 있습니다. 예: `"finished"`
- **Coding**: `{ "system": "코드체계 URI", "code": "...", "display": "..." }` — `text` 필드 없음
- **CodeableConcept**: `{ "coding": [Coding, ...], "text": "..." }` — coding 없이 `text`만도 유효
- **dateTime**: ISO 8601. 날짜만(`"2024-01-01"`)도 유효하며, 시각을 쓰면 `T` 구분자와 시간대가 필요합니다 (`"2024-01-01T09:10:00+09:00"`)
- **Period**: `{ "start": dateTime, "end": dateTime }`
- **값이 없는 필드는 키를 통째로 생략**합니다. `null`, `""`, `[]`는 유효하지 않습니다.

## Patient — 의료 서비스를 받는 개인

| 필드 | 타입 | Card. | 비고 |
| --- | --- | --- | --- |
| `identifier` | Identifier | 0..\* | `{ "system": URI, "value": "..." }` — 업무 식별자(등록번호 등) |
| `name` | HumanName | 0..\* | |
| `gender` | code | 0..1 | `male` \| `female` \| `other` \| `unknown` |
| `birthDate` | date | 0..1 | `YYYY-MM-DD` |
| `telecom` | ContactPoint | 0..\* | 연락처 |
| `address` | Address | 0..\* | |

## Encounter — 환자와 의료 제공자 간의 상호작용(외래·응급·입원 등)

| 필드 | 타입 | Card. | 비고 |
| --- | --- | --- | --- |
| `status` | code | **1..1** | `planned` \| `arrived` \| `in-progress` \| `finished` \| `cancelled` \| `unknown` 등 |
| `class` | Coding | **1..1** | v3-ActCode: `AMB`(외래) \| `EMER`(응급) \| `IMP`(입원) \| `HH`(가정) \| `VR`(원격) 등, system: `http://terminology.hl7.org/CodeSystem/v3-ActCode` |
| `type` | CodeableConcept | 0..\* | 내원의 구체적 유형 |
| `serviceType` | CodeableConcept | 0..1 | 제공된 진료 서비스의 종류 |
| `subject` | Reference | 0..1 | 대상 환자 |
| `period` | Period | 0..1 | 시작·종료 시각 |
| `reasonCode` | CodeableConcept | 0..\* | 내원 사유 |
| `serviceProvider` | Reference(Organization) | 0..1 | 진료 제공 조직 |

## Appointment — 진료 예약(미래 시점의 만남 계획)

| 필드 | 타입 | Card. | 비고 |
| --- | --- | --- | --- |
| `status` | code | **1..1** | `proposed` \| `booked` \| `arrived` \| `fulfilled` \| `cancelled` 등 |
| `start` / `end` | instant | 0..1 | 예약 시각 (instant는 시간대 필수) |
| `participant` | BackboneElement | **1..\*** | 참여자 목록 (필수) |
| `serviceType` | CodeableConcept | 0..\* | |

## Medication — 약물의 정의(제품/성분 정보)

| 필드 | 타입 | Card. | 비고 |
| --- | --- | --- | --- |
| `code` | CodeableConcept | 0..1 | 약물을 식별하는 코드·표시명 |
| `status` | code | 0..1 | `active` \| `inactive` \| `entered-in-error` |
| `form` | CodeableConcept | 0..1 | 제형 |
| `ingredient` | BackboneElement | 0..\* | 성분 |

## MedicationRequest — 약물의 처방·오더(공급·투여 지시)

| 필드 | 타입 | Card. | 비고 |
| --- | --- | --- | --- |
| `status` | code | **1..1** | `active` \| `on-hold` \| `cancelled` \| `completed` \| `stopped` \| `draft` \| `unknown` 등 |
| `intent` | code | **1..1** | `proposal` \| `plan` \| `order` \| `original-order` 등 |
| `medication[x]` | CodeableConcept 또는 Reference(Medication) | **1..1** | 둘 중 하나만: `medicationCodeableConcept` 또는 `medicationReference` |
| `subject` | Reference | **1..1** | 대상 환자 |
| `encounter` | Reference(Encounter) | 0..1 | 연관된 내원 |
| `authoredOn` | dateTime | 0..1 | 처방 작성 시각 |
| `requester` | Reference | 0..1 | 처방자 |
| `dosageInstruction` | Dosage | 0..\* | 용법 |
| `dispenseRequest.validityPeriod` | Period | 0..1 | 처방 유효 기간 |

## MedicationAdministration — 약물의 실제 투여 기록

| 필드 | 타입 | Card. | 비고 |
| --- | --- | --- | --- |
| `status` | code | **1..1** | `in-progress` \| `completed` \| `not-done` 등 |
| `medication[x]` | CodeableConcept 또는 Reference | **1..1** | |
| `subject` | Reference | **1..1** | |
| `effective[x]` | dateTime 또는 Period | **1..1** | 실제 투여 시각·기간 (필수) |
| `context` | Reference(Encounter) | 0..1 | |

## MedicationDispense — 약물의 조제·불출 기록

| 필드 | 타입 | Card. | 비고 |
| --- | --- | --- | --- |
| `status` | code | **1..1** | `preparation` \| `in-progress` \| `completed` 등 |
| `medication[x]` | CodeableConcept 또는 Reference | **1..1** | |
| `subject` | Reference | 0..1 | |
| `quantity` | Quantity | 0..1 | 조제량 |
| `whenHandedOver` | dateTime | 0..1 | 전달 시각 |

## MedicationStatement — 약물 복용 사실에 대한 진술(환자·보호자 보고 등)

| 필드 | 타입 | Card. | 비고 |
| --- | --- | --- | --- |
| `status` | code | **1..1** | `active` \| `completed` \| `unknown` 등 |
| `medication[x]` | CodeableConcept 또는 Reference | **1..1** | |
| `subject` | Reference | **1..1** | |
| `effective[x]` | dateTime 또는 Period | 0..1 | 복용 시기 |
| `informationSource` | Reference | 0..1 | 진술 출처 |
