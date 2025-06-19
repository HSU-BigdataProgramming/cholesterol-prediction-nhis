# 건강검진 데이터를 활용한 콜레스테롤 수치 예측 및 건강지표 연관성 분석

## 프로젝트 개요
국민건강보험공단(NHIS)의 건강검진 데이터를 활용하여 콜레스테롤 수치 예측 및 비만, 당뇨병 등 다양한 질병 위험도를 평가하는 머신러닝 프로젝트입니다.

## 설치 라이브러리
Python 3.10 버전 권장

### 필수 라이브러리 설치
```bash
pip install -r requirements.txt
```

### 개별 라이브러리 버전
```bash
pip install ipykernel==6.29.5
pip install pandas==2.3.0
pip install matplotlib==3.10.3
pip install seaborn==0.13.2
pip install scikit-learn==1.7.0
pip install numpy==2.1.3
pip install xgboost==3.0.2
pip install tensorflow==2.19.0
```

## 파일 구조 및 설명

### 📁 데이터 파일

#### 원본 데이터
- `data/Health_2023.csv`: 원본 건강검진 데이터
- `data/health_2023_cleaned.csv`: 1차 전처리된 데이터 (결측치 제거)
- `data/health_2023_cleaned_final.csv`: 최종 전처리된 데이터 (이상치 처리 완료)

#### 파생 데이터
- `health_sample.csv`: 샘플 데이터
- `health_with_disease_flags.csv`: 질병 의심 플래그가 추가된 데이터

### 📊 분석 노트북

#### 1. `preprocessing.ipynb` - 데이터 전처리
**주요 작업:**
- 원본 데이터 로드 및 탐색
- 결손치 처리 (결손치 유무, 치아마모증유무, 제3대구치 컬럼 삭제)
- 총콜레스테롤 기준 결측값 삭제
- 이상치 시각화 및 처리
  - IQR 방식 대신 임상적 기준 적용
  - 각 컬럼별 정상 범위 설정
- 데이터 정렬 및 최종 저장

#### 2. `prediction_colesterol.ipynb` - 콜레스테롤 수치 예측 (회귀)
**주요 내용:**
- XGBoost Regressor를 사용한 콜레스테롤 수치 예측
- 예측 대상: 총콜레스테롤, 트리글리세라이드, HDL콜레스테롤, LDL콜레스테롤
- 입력 변수: 연령대, 성별, 신장, 체중, 허리둘레, 흡연상태, 음주여부, 혈압, 혈당
- BMI, 허리신장비 파생변수 추가

**성능 결과:**
- 총콜레스테롤: R² = 0.120
- 트리글리세라이드: R² = 0.179
- HDL콜레스테롤: R² = 0.215
- LDL콜레스테롤: R² = 0.101

#### 3. `prediction_colesterol_binary.ipynb` - 콜레스테롤 위험도 분류
**주요 내용:**
- 콜레스테롤 수치를 정상/위험 이진 분류
- 임상적 기준에 따른 위험도 분류

#### 4. `prediction_obesity.ipynb` - 비만 예측 모델
**주요 내용:**
- RandomForest Classifier를 사용한 비만 예측
- 정확도: 93.28%
- 교차검증 정확도: 93.29% (cv=10)

**특성 중요도:**
1. 허리둘레 (가장 중요)
2. 식전혈당(공복혈당)
3. 수축기혈압

#### 5. `prediction.ipynb` - 통합 예측 모델
**주요 내용:**
- 다양한 질병 위험도 종합 예측
- 질병별 의심 플래그 생성:
  - 당뇨병 의심: 29,399명
  - 고혈압 의심: 53,723명
  - 고지혈증 의심: 102,486명
  - 간질환 의심: 82,131명
  - 신장질환 의심: 54,796명
  - 심혈관질환 위험: 308,824명
  - 비만 의심: 211,267명

#### 7. `KMeans.ipynb` - 클러스터링 분석
**주요 내용:**
- K-means 클러스터링을 통한 건강상태 군집화
- 4개 클러스터 생성:
  - 클러스터 0: 건강 유지군 (당뇨 1.8%, 고혈압 1.4%)
  - 클러스터 1: 고도 비만·고혈당 경향군 (비만 72%)
  - 클러스터 2: 대사증후군 위험군 (비만 91.9%, 고지혈증 50.9%)
  - 클러스터 3: 고지혈증 중심군 (고지혈증 52%, 비만 77%)
- 지역별 클러스터 분포 분석

### 📈 시각화 결과

#### `plots/` 디렉토리
- `boxplot_*.png`: 각 변수별 상자그림 (이상치 탐지)
- `distribution_*.png`: 각 변수별 분포도
- 총 38개의 변수에 대한 시각화 파일

**주요 변수:**
- 혈압 관련: 수축기혈압, 이완기혈압
- 혈당 관련: 식전혈당(공복혈당)
- 지질 관련: 총콜레스테롤, HDL콜레스테롤, LDL콜레스테롤, 트리글리세라이드
- 간기능: 혈청지오티(AST), 혈청지피티(ALT), 감마지티피
- 신기능: 혈청크레아티닌, 요단백
- 신체계측: 신장, 체중, 허리둘레
- 기타: 혈색소, 시력, 청력, 치석 등

## 주요 분석 결과

### 1. 콜레스테롤 예측 모델의 한계
- 현재 데이터만으로는 콜레스테롤 수치 예측 정확도가 낮음 (R² < 0.22)
- 식습관, 유전적 요인, 운동 습관, 약물 복용 등 추가 정보 필요

### 2. 비만 예측 모델의 우수성
- 93% 이상의 높은 정확도
- 허리둘레가 가장 중요한 예측 변수
- 실제 임상에서 활용 가능한 수준

### 3. 질병 위험도 분석
- 심혈관질환 위험군이 전체의 약 50% 이상
- 지역별 건강상태 차이 확인
- 경기도, 서울이 고위험군 비중 높음

### 4. 클러스터링 인사이트
- 건강상태에 따른 4개 군집으로 분류
- 대사증후군 위험군 식별 가능
- 맞춤형 건강관리 전략 수립 가능

## 개선 방향

1. **데이터 보강**: 식습관, 운동 습관, 가족력 정보 추가
2. **피처 엔지니어링**: 더 많은 파생변수 생성
3. **모델 개선**: 딥러닝, 앙상블 기법 적용
4. **실시간 예측**: 웹 기반 예측 시스템 구축

## 참고 문헌

### 임상 기준 참고 자료
- 감마지티피: https://m.chamc.co.kr/media/magazine_detail.cha?idx=272
- 혈압: https://health.kdca.go.kr/healthinfo/biz/health/gnrlzHealthInfo/gnrlzHealthInfo/gnrlzHealthInfoView.do?cntnts_sn=5300
- 총콜레스테롤: https://medicine.yonsei.ac.kr/health/encyclopedia/treat_board.do?mode=view&articleNo=66862
- 트리글리세라이드: https://www.mfds.go.kr/brd/m_1060/down.do?brd_id=data0011&seq=14377&data_tp=A&file_seq=1
- 혈색소: https://health.kdca.go.kr/healthinfo/biz/health/gnrlzHealthInfo/gnrlzHealthInfo/gnrlzHealthInfoView.do?cntnts_sn=5298
- 간기능: https://m.blog.naver.com/naraemedical01/223801924741
- 크레아티닌: https://www.amc.seoul.kr/asan/healthinfo/management/managementDetail.do?managementId=104
- HDL콜레스테롤: https://www.amc.seoul.kr/asan/healthinfo/management/managementDetail.do?managementId=84
- LDL콜레스테롤: https://www.amc.seoul.kr/asan/healthinfo/management/managementDetail.do?managementId=83

## 라이센스
이 프로젝트는 MIT 라이센스 하에 배포됩니다.

