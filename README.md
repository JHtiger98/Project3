## Project3: Prediction of Bone Age &amp; Height Growth through Hand X-ray images
![image](https://user-images.githubusercontent.com/121841464/228569149-cd5c524e-c23b-43ae-86c8-40578ea4f557.png)

## 참조한 논문 리뷰
* 라벨링 기법: [TW3](https://github.com/JHtiger98/Project3/blob/main/paper_review/TW3.md)
* 객체 탐지 모델: [YOLO](https://github.com/JHtiger98/Project3/blob/main/paper_review/YOLO.md)
* 골 연령 예측 모델: [TJNet](https://github.com/JHtiger98/Project3/blob/main/paper_review/TJNet.md)

----------
##  개요
### 데이터
 - X-ray 검사 이미지(골 연령 판독 검사 기준)
   - 손목을 포함한 왼손 X-ray
   - 만 2세~17세
   - 남자 665명/ 여자 572명
- 기본 신체 기록, 나이, 성별 데이터
- 전문의 2명의 골연령 판독 데이터
- 2017 소아 청소년 성장도표 (출처: 질병관리청)

### 제작 환경 및 모듈
> <img src="https://user-images.githubusercontent.com/115753833/228258560-2890c929-baa9-430f-8a7f-469528e453a4.png" width='200'>

### 프로젝트 진행 순서
<img src="https://user-images.githubusercontent.com/115753833/228264806-68f26327-9a10-48a0-affe-b869a86b6a55.png" width='700'>

----------
## 이미지 전처리
### X-ray에서 손 영역만 추출
<img src="https://user-images.githubusercontent.com/115753833/228265668-164f3dc7-a27a-47c2-a86f-d8d5f2be7da1.png" width="49%"><img src="https://user-images.githubusercontent.com/115753833/228265895-704d1f23-1ab0-4069-92cf-eeaa9a12d96a.png" width="49%">

### 손목 회전: 정확도 높이기
<img src="https://user-images.githubusercontent.com/115753833/228266456-6a6537c1-f61f-4929-bb57-cd9b0d3bc43e.png" width="49%">

### 모폴로지 연산을 통한 뼈 도출: TOPHAT 사용
- **모폴로지 연산이란?**
  - 팽창과 수축의 형태학적 변환을 결합 하여 사용
  - 연산에 도움: 연산 시 형태가 변형되기도 하므로 두 방법을 결합할 경우 오류가 적음
<img src="https://user-images.githubusercontent.com/115753833/228267505-8a591782-6b1a-400f-a557-25f6d1a798cd.png" width="300">

> - **모폴로지 연산에 TOPHAT옵션 사용 이유** 
>    - 형태의 변형은 줄임
>    - 밝기가 크게 튀는 부분만을 강조
>    - 뼈 도출이 가장 잘됨
> <img src="https://user-images.githubusercontent.com/115753833/228268282-4cfd632a-717a-47c8-bb0c-1bd1bd72bc86.png" width="700">

### 마스크 생성 및 뼈 도출 (최종 이미지)
<img src="https://user-images.githubusercontent.com/115753833/228269708-18906552-2d3c-4bd1-b626-f9933e5ae19e.png" width="60%">
<img src="https://user-images.githubusercontent.com/115753833/228270121-1b37d704-4993-4d33-82e0-4fe52ff81dc0.png" width="60%">

----------
## 이미지 라벨링
### TW3 기법을 참고하여 라벨 선정
<img src="https://user-images.githubusercontent.com/115753833/228273470-50e2a73e-4f7d-4349-b120-78fc8ae5113f.png" width="700">

### Roboflow를 활용한 관절 Annotation
<img src="https://user-images.githubusercontent.com/115753833/228274409-42822a34-f77e-4823-a25a-4316dfb86a12.png" width="700">

### YOLOv5를 활용한 관절 객체 탐지
<img src="https://user-images.githubusercontent.com/121841464/228577492-766a95c5-205d-4aec-9afb-8c82ff30c422.png" width="700">
  
### YOLOv5 Fine Tuning
<img src="https://user-images.githubusercontent.com/115753833/228275594-794ba07d-4a7b-4431-a8a7-c3e2c7b0b9e9.png" width="700">

### Tensorboard로 epochs에 따른 mAP, Precision, Recall 값 시각화
<img src="https://user-images.githubusercontent.com/115753833/228276697-5444914b-0f30-469b-9a07-11af5d601db1.png" width="700">

### 라벨링 결과
<img src="https://user-images.githubusercontent.com/115753833/228277304-463272eb-ca02-4f87-9c5a-ce68b11ef4ed.png" width="700">

----------
## 골 연령 예측
### 데이터 준비
<img src="https://user-images.githubusercontent.com/115753833/228277900-aaf1c8fc-f56b-4b88-883e-2977eb69adf3.png" width="700">

### 논문을 참조한 모델 구축: TJ-Net
- **각 관절(라벨) 이미지에 대한 모델 구조**
<img src="https://user-images.githubusercontent.com/115753833/228279612-83c0527e-156b-4b73-9546-7948876ce226.png" width="700">

- **원본 이미지와 성별 정보가 더해진 모델 전체 구조**
<img src="https://user-images.githubusercontent.com/115753833/228278952-763b622b-4b66-431f-91e5-419ce8ef4747.png" width="700">

### TJNet Fine Tuning
<img src="https://user-images.githubusercontent.com/115753833/228281257-88ff7ed7-fdbd-477a-bdc9-3f209973a89c.png" width="700">

> - 검증 지표
>   - MSE: 손실 값으로 평가, 오차의 민감도가 높아 회귀 예측 시 중요한 평가 지표로 사용
>   - MAE: 오차의 범위를 직관적으로 알기 쉽지만 오차의 민감도가 낮아 보조적인 평가 지표로 사용

### 최적모델 학습 결과
<img src="https://user-images.githubusercontent.com/115753833/228281671-3b7770d7-67eb-4494-b969-83701e148986.png" width="700">

----------
## 키 성장 예측
### 키 성장 공식
<img src="https://user-images.githubusercontent.com/115753833/228281899-d44ec0b0-00c2-42d3-9512-d1fe993726bc.png" width="700">

### 예측한 골연령, 성별, 검사시 나이 데이터 -> 키 성장 공식에 대입
<img src="https://user-images.githubusercontent.com/115753833/228282259-0e6a1011-2b3b-4e09-ab01-21b8264a5bd4.png" width="700">

### 히스토그램으로 시각화 하기
<img src="https://user-images.githubusercontent.com/121841464/228573651-49429358-744f-48e0-9c29-6062a5af531c.png" width="700">

----------
## 결론
- 기대효과: 
  - GUI를 통해 전문의나 환자가 쉽게 이용 가능하도록 함
  - 딥러닝 학습을 통한 시간 절약
  - 예측 단계 축소로 인한 비용 절약
- 한계점:
  - 현재 모델로 전문의보다 오차범위가 1개월 정도 더 크게 나오므로 정확도 개선 필요
  - 최종 키 예측 시 외부적 변수를 고려하기 어려움
  - 현재 표본의 최종 키 데이터가 없어 최종 키 정확도 판별 불가능
