# clinical-data-fhir-challenge

임상 데이터 분석 및 HL7 FHIR 리소스 매핑 라이브 코딩 과제

이 저장소는 라이브 코딩 테스트에 사용하는 데이터와 시작 파일을 제공합니다. **문제 전문은 시험 당일 별도로 제공됩니다.**

## 구성

```text
├── data/
│   ├── patients.csv        # 환자 기본 정보
│   ├── visits.csv          # 내원·입원 기록
│   └── prescriptions.csv   # 처방·오더 기록
├── examples/
│   └── output_example.json # 문항 2 출력 형식 예시 (가상 값)
├── fhir-quick-reference.md # FHIR R4 주요 리소스·타입 요약 (시간 절약용)
└── NOTICE-MIMIC.md
```

## 데이터 설명

이 데이터는 MIMIC-III 임상 데이터베이스의 테이블 관계를 참고해 만든 **교육·평가용 fixture**이며, 실제 환자 데이터가 아닙니다.  
자세한 출처는 [NOTICE-MIMIC.md](NOTICE-MIMIC.md)를 참고하세요.

### `patients.csv`

| 컬럼 | 설명 |
| --- | --- |
| `patient_id` | 환자 식별자 |
| `gender` | 성별 (`M` 또는 `F`) |
| `birth_date` | 생년월일 (`YYYY-MM-DD`) |

### `visits.csv`

| 컬럼 | 설명 |
| --- | --- |
| `visit_id` | 내원 식별자 |
| `patient_id` | 내원한 환자 |
| `start_at` | 내원 시작 시각 (`YYYY-MM-DD HH:MM:SS`, 시간대는 한국 표준시 `+09:00`) |
| `end_at` | 내원 종료 시각 (`YYYY-MM-DD HH:MM:SS`, 시간대는 한국 표준시 `+09:00`) |
| `visit_type` | 내원 유형 — `OUTPATIENT`(외래), `EMERGENCY`(응급), `INPATIENT`(입원) |
| `department` | 진료과 (병원 내부 표시명) |

### `prescriptions.csv`

| 컬럼 | 설명 |
| --- | --- |
| `prescription_id` | 처방 행 식별자 |
| `patient_id` | 처방 대상 환자 |
| `visit_id` | 처방과 연결된 내원 |
| `start_date` | 처방 유효 시작일 (`YYYY-MM-DD`) |
| `end_date` | 처방 유효 종료일 (`YYYY-MM-DD`) |
| `drug_code` | 병원 내부(local formulary) 약물 코드 |
| `drug_name` | 약물 표시명 (일반명 또는 상품명, 규격 포함 가능) |
| `status` | 처방 상태 (병원 내부 구분값) |

처방 데이터는 처방·오더 기록이며, 실제 투약 완료 기록이 아닙니다.

## 준비 사항

- 이 저장소를 clone 하거나 ZIP으로 내려받은 뒤, 로컬에서 CSV를 읽고 결과 파일을 만들 수 있는 환경을 준비해 주세요.  
- 구현 언어는 자유입니다. 사용할 언어의 실행 환경을 미리 준비해 주세요.  
- **DB 서버는 제공되지 않습니다.** SQL을 사용하려면 SQLite 등 로컬에서 실행 가능한 환경을 직접 준비해 주세요.  
  CSV를 적재하는 방법은 자유이며, 적재 과정은 평가하지 않습니다.

## 참고

- FHIR R4 리소스 목록: https://hl7.org/fhir/R4/resourcelist.html
- FHIR R4 주요 리소스·타입 요약: [fhir-quick-reference.md](fhir-quick-reference.md)
