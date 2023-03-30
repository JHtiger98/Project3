# TW3
### TW3-Based Fully Automated Bone Age Assessment System Using Deep Neural Networks
- [Link](https://ieeexplore.ieee.org/document/8660640)
- [PDF](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=8660640)
- S. J. Son et al. (2019). "TW3-Based Fully Automated Bone Age Assessment System Using Deep Neural Networks" in IEEE Access vol 7. (pp. 33346-33358).

----------
## Abstract
![image](https://user-images.githubusercontent.com/121841464/228781311-7ebfa2d0-d44d-478c-b772-9fec63ec40c4.png)

골 연령 평가(BAA)는 딥러닝을 활용하기 좋은 객체 탐지와 분류가 사용되는 분야이다. 따라서 컨볼루션 신경망(CNN)은 BAA 자동화에 점점 더 많이 사용되고 있으며, 유망한 결과를 보여주고 있다. 본 논문에서는, 13개의 다른 epiphysis-metaphysis 성장을 선정하는 것을 시작으로 해당 골연령을 추정하는 것으로 끝나는 Tanner-Whitehouse 3 방법의 전체 과정을 자동화하기 위한 완전한 end-to-end BAA 시스템을 제안한다. 또한, 결과를 개선하기 위해 CNN과 기타 단계에 대한 구체적인 수정을 제안한다. 시스템의 훈련과 평가를 위해 3300개의 Annotated X-ray 이미지를 사용했으며, 실험 결과 13개의 영역에서 뼈 성숙도 수준에 대한 평균 상위 1순위 및 상위 2순위 예측 정확도는 각각 79.6% 및 97.2%로 나타났다. 연령 예측에서의 MAE와 MSE는 각각 0.46년과 0.62년이며, 실제 값과 1년 이내의 정확도가 97.6%로 나타났다. 이 시스템은 상용화된 GP(Greulich-Pyle) 기반 시스템보다 우수한 성능을 보여줬으며, 실제 임상 사용의 잠재력을 증명했다.

----------
## METHODLOGY
![image](https://user-images.githubusercontent.com/121841464/228777174-a270e7d2-4853-4d45-8e2c-a79c3dacfd82.png)

TW3 기반 BAA 시스템은 크게 3단계로 구성된다. 첫 번째 단계는 TW3 기법에 사용되는 실제 ROI를 포함한 손목, 엄지, 중지와 소지의 큰 영역을 추출하는 것입니다. 이러한 영역을 bROI(bounding ROIs)라고 한다.<br>
> 각 bROIs에 포함된 ROIs는 다음과 같다:
> - 손목의 bROI: Ulna, Radius;
> - 엄지의 bROI: 1st distal phalanx, 1st proximal phalanx, 1st metacarpal
> - 중지의 bROI: 3rd distal phalanx, 3rd middle phalanx, 3rd proximal phalanx, 3rd metacarpal
> - 소지의 bROI: 5th distal phalanx, 5th middle phalanx, 5th proximal phalanx, 5th metacarpal

두 번째 단계에서는 bROIs에서 13개의 실제 ROIs를 추출한다. 실제 ROI 추출에 딥러닝을 사용하는 반면, bROI 추출은 전통적인 이미지 프로세싱을 사용하기 때문에 제안된 ROI 추출 기술은 하이브리드라고 할 수 있다. 세 번째 단계에서는 ROls를 각각 골 성숙도에 따라 분류하고 점수로 변환한다. 마지막으로, 이러한 점수와 상관 행렬을 사용하여 뼈 연령을 예측한다.<br>
- **VGG-Net 모델을 이용한 TW3기반 BAA 시스템 구성**
<img src="https://user-images.githubusercontent.com/121841464/228790525-3c99fcff-c456-470c-81ab-f4d0a069b977.png" width="900">

- **골 성숙도에 따른 이미지 분류 예시**
<img src="https://user-images.githubusercontent.com/121841464/228793186-bc7a9e80-c92d-4182-a86b-c0ee3f189fc7.png" width="500">

