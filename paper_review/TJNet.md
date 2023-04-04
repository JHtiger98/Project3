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

지난 10년동안 머신 러닝은 의료 데이터를 이해하고 해석하는 데 혁신적인 진전을 이루었다. 특히, 다양한 심층 신경망은 의사들이 많은 질병을 진단하는 데 도움을 줄 수 있는 잠재력을 입증하였다. 본 논문에서는 방사선 사진 기반 골 연령 예측을 위한 다중 스케일 잔차 신경망 (TJ--Net)을 제안하여 연령 그룹 분류에 중요한 특징을 강조하기 위한 주의 메커니즘을 소개한다. 기존의 연구에서 골 연령 평가를 위한 특징들로 의미 있는 성별 분류를 제공할 수 있다고 했다. 이 연구에서는, 기존의 연구 결과를 성별과 골 연령 학습 프레임워크로 확장한다. 그 결과로 학습된 기능이 기존의 아틀라스 기반 평가와 일치하며 다른 데이터 세트로 변환이 가능함을 확인할 수 있다.

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

