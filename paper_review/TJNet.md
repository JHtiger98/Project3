# TJNet
### Automatic Radiographic Bone Age Assessment Using Deep Joint Learning with Attention Modules
- [Link](https://www.semanticscholar.org/paper/Automatic-Radiographic-Bone-Age-Assessment-Using-Tang-Wu/6342afdefe3ecb2af3706240a6b57108d2534705)
- [PDF](https://pdfs.semanticscholar.org/6342/afdefe3ecb2af3706240a6b57108d2534705.pdf)
- Reference: Tang, W., Wu, G., & Shen, G. (2020). Automatic Radiographic Bone Age Assessment Using Deep Joint Learning with Attention Modules.

----------
## Introduction

GP기법은 엑스레이 유사성을 시각적으로 검사하여 표준 아틀라스와 개인 판단에 의존한다. 따라서, 때때로 두 방사선 전문의가 동일한 방사선 사진에 대해 다른 예측을 할 가능성이 높다. 좀 더 정확하고 객관적인 평가를 위해, 태너 화이트하우스(TW) 기법은 방사선 사진의 더 구체적인 영역 사이의 평가를 필요로 하며 체계적인 채점을 위한 세부적인 특징을 추출하였다.

- **TW기법의 ROI**

![image](https://user-images.githubusercontent.com/121841464/229710297-f65b8c1f-e8ab-48ef-b71f-2b3cc0cdea79.png)

지난 10년동안 머신 러닝은 의료 데이터를 이해하고 해석하는 데 혁신적인 진전을 이루었다. 특히, 다양한 심층 신경망은 의사들이 많은 질병을 진단하는 데 도움을 줄 수 있는 잠재력을 입증하였다. 본 논문에서는 방사선 사진 기반 골 연령 예측을 위한 다중 스케일 잔차 신경망 (TJ-Net)을 제안하여 연령 그룹 분류에 중요한 특징을 강조하기 위한 주의 메커니즘을 소개한다. 기존의 연구에서 골 연령 평가를 위한 특징들로 의미 있는 성별 분류를 제공할 수 있다고 했다. 이 연구에서는, 기존의 연구 결과를 성별과 골 연령 학습 프레임워크로 확장한다. 그 결과로 학습된 기능이 기존의 아틀라스 기반 평가와 일치하며 다른 데이터 세트로 변환이 가능함을 확인할 수 있다.

----------
## Proposed Method
### TJ-Net의 구조
여기서 TJ-Net이라고 불리는 골 연령 분류 모델은 여러 기능의 블록으로 이루어져 있다. 아래 그림에서 TJ-Net의 두 갈래 구조를 잘 보여주고 있다. 이 모델은 input image를 77개의 클래스로 나눈 골 연령(3개월 단위)과 성별 레이블(성별 분류기)로 매핑한다. **블록1** ~ **블록4**는 Convolutional/Pooling으로 구성되며 input의 중요한 특징들을 추출한다. **블록5**는 성별 분류를 끝내기 위해 multi-scale 피처를 high-level의 dense 층으로 녹아 들게 한다. 연령 분류기인 **블록6**는 이미지 특징과 성별 정보를 연령 카테고리에 매칭 시킨다. 최종 output은 softmax 함수를 거쳐 나오고, **블록5**와 **블록6**는 완전연결계층이 있다.
> CBAM (Convolutional Block Attention Module)과 IncRes (Inception ResNet) 모듈을 TJ-Net에 도입하여 어텐션 매커니즘, 잔차 학습, 다중 스케일 특징을 활용하여 골 연령 관련 특징 추출 능력을 강화한다.

<img src="https://user-images.githubusercontent.com/121841464/229719177-6320cf9e-2783-41ba-ba8d-40d8f67fa175.png" width="700">

### CBAM (Convolutional Block Attention Module)
앞부분의 Convolutional block에서 탐지된 채널과 공간 특징 중, Convolution-filtered 특징은 골 연령을 구별하는데 있어서 중요하다. CBAM의 도입은 특징들을 채널과 공간 차원의 전체적인 목표와의 관련성을 더 강화하기 위함이다. TJ-Net에 있는 3개의 CBAM은 각각 2가지의 순차적으로 쌓인 요소로 구성된다: **Channel Attention Module, Spatial Attention Module**

<img src="https://user-images.githubusercontent.com/121841464/229735774-6509a19f-452e-48bc-8f10-14ee5f49bf54.png" width="700">

1. Input Features가 Average Pooling, Maximum Pooling을 병렬로 통과한다.
2. 2개의 완전연결계층이 Sigmoid 활성화 함수를 통해 Channel attention scale vector를 생성한다.
3. 스케일 별로 가중된 원본은 새로운 tensor를 생성한다.
4. 새롭게 생성된 tensor는 이전의 과정과 같은 병렬 구조를 통과한다.
5. Sigmoid 활성화 함수가 Spatial attention scale matrix를 출력한다.
6. Output feature map은 scale matrix와 input features의 곱이다.

### Inception Residual Module
TJ-Net에는 살짝 다른 구조를 가진 2개의 Inception Residual Module이 있다. **lncRes1**은 다양한 Convolutional kernel 크기를 가진 여러 branches로 구성 되어있다. Multi-scale branches는 1x1 과 3x3 Convolutional kernel을 특별한 방법으로 조합하였다. **lncRes2**는 각각 1x1, 3x3, 1x3, 3x1 사이즈의 Inception branches를 가지고 있다. **lncRes1**과 **lncRes2** 모듈은 각각은 4개의 Multi-scale branches를 가지고 있으며, 1x1, 3x3, 3x1, 5x5, 1x7, 7x1의 조합이다. 아래의 그림은 **IncRes1**의 구조이다.

<img src="https://user-images.githubusercontent.com/121841464/229744052-cce72276-355e-4b1f-9d50-bd1bc3f77d81.png" width="700">

----------
## Experiments
### Datasets & Preprocessing
본 실험은 TJ-Net 모델을 평가하기 위해 **RSNA**와 **Tongji** 데이터셋을 사용하였다. **RSNA** 데이터셋은 12611장의 학습 데이터, 1425장의 검증 데이터, 200장의 테스트 데이터로 구성되어 있다. 하지만 대회가 종료됨에 따라 **RSNA**의 테스트 데이터셋은 더 이상 사용할 수 없었기에, 학습 데이터셋을 0.8 : 0.8 : 0.1 비율로 나누어 학습, 검증, 테스트 데이터로 사용하였다. **Tongji** 데이터셋은 Huazhong 과학기술 대학교의 교습병원인 **Tongji** 병원의 엑스레이 이미지를 사용하였다. 또한, 원본 엑스레이 사진 속 손의 방향을 일관되게 만들기 위해 엑스레이 이미지들을 회전시켰다. 품질이 좋지 않은 엑스레이 사진은 버리고 1385개의 엑스레이 이미지(남자: 768, 여자: 617)를 사용하였다. 너무 작은 이미지는 필요한 세부 사항을 생략할 것이고, 고해상도 이미지는 연구실의 제한된 컴퓨팅 자원의 학습 과정을 지연시킬 수 있다. 이에 따라, 본 실험은 **RSNA**와 **Tongji** 데이터셋을 픽셀 크기로 통일하였다.

### Implementation
- TJ-Net 개발 환경 및 설정
  - Python 3.6
  - Tensorflow 1.7
  - NVIDIA 2080Ti GPU
  - ADAM optimizer
  - Batch size: 16

### Results & Analysis
우선 RSNA 데이터로 TJ-Net을 훈련시키고 생성된 파라미터들을 Tongji훈련 세트로 Fine-tuning 하였다. 그리고 TJ-Net 모델의 비교를 위해, 성별 입력 모듈과 분류를 위한 dense 층이 있으며 다른 Backbone(e.g. VGG16, ResNet50, InceptionV3,4)을 사용하는 모델을 개발하였다. TJ-Net 모델이 0.41, 0.42년의 MAD, RMSE 값으로 두 값 모두 최상의 결과를 보여주었다. 또한, TJ-Net의 1-3 동결된 블록이 0.36년, 0.551의 MAD, RMSE 값으로 최상의 결과를 보여주었다. 이 결과는, TJ-Net 모델이 방사선 골 연령 예측에 중요한 특징을 포착할 수 있다는 것을 증명하였다.
- RSNA 데이터셋으로 테스트한 모델 성능 비교표
<img src="https://user-images.githubusercontent.com/121841464/229838042-b136a6df-d673-42a1-9ed2-1b326ccd3153.png" width="300">

- 다양한 동결 단계를 사용한 전이 학습의 비교표
<img src="https://user-images.githubusercontent.com/121841464/229990134-a58cd734-f8c2-4bce-a63f-18116a63b33c.png" width="500">

##
- **블록4**의 활성화맵 (골연령: 15세, 성별: 남자), **왼쪽: CBAM 없이 학습, 오른쪽: CBAM 학습**
<img src="https://user-images.githubusercontent.com/121841464/229992972-51976803-f53a-43af-a822-b1a1396036a2.png" width="500">
훈련된 모델에 대한 CBAM의 영향을 시각화하기 위해 블록4에서 학습한 활성화된 특징들의 히트맵을 그린 결과, CBAM 활성화맵의 주요 포인트들이 TW기법의 ROI와 거의 일치한 것을 확인 할 수 있다.

##
- 예측 오류 분포의 비교, **왼쪽: 블록5의 성별 정보 공동 학습, 오른쪽: 공동 학습 없이 학습**
<img src="https://user-images.githubusercontent.com/121841464/229994659-00422240-b816-48c2-a9da-4253d7707906.png" width="700">
TJ-Net에 공동 학습(Joint learning)을 추가하면 대부분의 연령에 대해 더 정확한 예측이 가능하다.

##
- **블록6**의 성별 정보 가중치 매트릭스 비교 (검은색=0), **위: 공동 학습 없이 학습, 아래: 공동 학습**
<img src="https://user-images.githubusercontent.com/121841464/229997832-7b1c972a-b894-4b64-af4c-4fc4359a7b8c.png" width="700">
공동 학습(Joint learning)이 성별 가중치 매트릭스를 더 특별하게 만들어, 골 연령에 대한 성별 input의 패턴에 대한 영향을 더 강화한다는 것을 알 수 있다.

----------
## Conclusion
이 논문은, 자동 방사선 골 연령 평가를 위해 특별히 설계된 딥러닝 신경망인 일명 **TJ-Net**을 제안했다. 어텐션 모듈은 전문가가 탐색한 초점과 유사한 특징을 찾는데 도움을 주고, 공동 성별/연령 학습이 성별 레이블에 대한 연령 조건 예측을 향상시켰다. 결과적으로, TJ-Net은 RSNA, Tongji 데이터셋에서 0.41년, 0.36년의 MAD를 기록하며, 다른 모델의 최첨단 방법보다도 더 좋은 성능을 기록하였다.
