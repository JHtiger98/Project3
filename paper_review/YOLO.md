# YOLO
### You Only Look Once: Unified, Real-Time Object Detection
- [Link](https://arxiv.org/abs/1506.02640)
- [PDF](https://arxiv.org/pdf/1506.02640.pdf)
- Reference: Joseph Redmon, Santosh Divvala, Ross Girshick, Ali Farhadi. (2016). Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR). (pp. 779-788).

----------
## Unified Detection
![image](https://user-images.githubusercontent.com/121841464/228446819-1e4a7c7d-982e-4ec5-822e-719874d09f2a.png)

1. 입력 이미지를 S X S Grid로 나눠 준다.

2. 각각의 Grid cell은 B(Bounding box)와 그에 대한 Confidence score를 갖는다. Confidence score는 Bounding box가 Object를 포함한다는 예측을 얼마나 확신하는지, 그리고 그에 대한 예측이 얼마나 정확할지를 반영한다. 
> Confidence Score = Pr(Object) * IOU{pred}^{truth}

3. 각각의 Bounding box는 x, y , w, h와 confidence로 구성된다.

4. 각각의 Grid cell은 C(Conditional class probability)를 갖는다.  논문에서는 Yolo의 평가를 위해 PASCAL VOC을 사용하여 class 수는 20개이다.
> Conditional Class Probability = Pr(Class{i} | Object)

5. Confidence class probability와 각 박스의 Confidence score을 곱하면 이 점수는 그 class가 그 박스안에 나타날 가능성과 박스가 물체에 얼마나 적합한가를 모두 포함한다.
> Class Specific Confidence Score = Conditional Class Probability * Confidence Score <br>
> = Pr(Class{i} | Object) * Pr(Object) * IOU{pred}^{truth} <br>
> = Pr(Class{i}) * IOU{pred}^{truth}

- S * S * (B * 5 + C) tensor로 encode 된다
  - S X S : Grid Cell 개수
  - B : Bounding box 개수
  - 5: 각 Bounding box에 대한 5개의 변수 (x, y, w, h, confidence)
  - C: Class 개수
> S = 7, B = 2, C = 20 인 PASCAL VOC를 평가를 위해 사용했고 이 경우에는 7 * 7 * 30 final prediction tensor 크기를 갖는다.

----------
## Network Design
![image](https://user-images.githubusercontent.com/121841464/228455420-404a129c-90d3-41d3-bbcd-ea968fdfe820.png)

이 모델은 Convolutional 신경망을 구현한다. 초반의 Convolutional layers는 이미지의 특성을 추출하고 Fully connected layers는 output의 확률과 좌표를 예측한다.
이미지 분류를 위해 구조는 GoogLeNet에서 영감을 받았다. YOLO Network는 24개의 Convolutional layers와 2개의 Fully connected layers로 이루어져 있다.
Inception module을 사용하는 GoogLeNet과 달리 YOLO는 1X1 Reduction layers를 사용하고 3X3 Convolutional layers가 뒤따라온다.
빠른 물체 감지를 위해 더 빠른 버전의 YOLO를 학습하고, 이 Fast YOLO는 더 적은 수의 Convolutional layers를 사용한다 (24 -> 9).
Fast YOLO는 사이즈를 제외하고는 학습과 테스트 Parameters는 YOLO와 모두 동일하다.

----------
## Experiments
![image](https://user-images.githubusercontent.com/121841464/228464651-f1b1c4aa-541b-4a01-816d-5eefa2d02517.png)

이것은 PASCAL VOC를 이용한 detector들의 mAP(정확도)와 FPS(속도)를 비교 해 놓은 표이다.
Fast YOLO가 가장 빠른 FPS를 기록했고 다른 Real-time detector 보다 정확도도 2배 이상 높게 기록 되었다.
YOLO는 Fast YOLO보다 110FPS 느리었지만 10mAP 더 정확했으며 속도 역시 Real-time detector로써는 손색없을 정도이다.

![image](https://user-images.githubusercontent.com/121841464/228468096-789dfeeb-324d-40bb-8bea-998c2b4d7397.png)

- 오류 분석
  - Correct: correct class and IOU > .5
  - Localization: correct class, .1 < IOU < .5 
  - Similar: class is similar, IOU > .1
> YOLO의 Localization error는 다른 error들의 합보다도 큽니다. Fast R-CNN은 YOLO보다 더 적은 Localization error를 범하지만 Background error는 3배가 넘는다. 

![image](https://user-images.githubusercontent.com/121841464/228474117-d99a8112-d3cf-4bf4-90e5-d0331d5f01b5.png)

YOLO와 R-CNN VOC 2007에서 높은 AP를 보여줬다. 하지만 Picasso와 People-Art 데이터셋에 적용을 했을 때, YOLO는 유사한 AP를 보여줬지만 R-CNN은 AP가 많이 떨어지는 모습을 보였다. 그 이유는, YOLO는 DPM과 마찬가지로 객체의 사이즈와 모양은 물론이고 그 객체와의 관계와 객체가 보통 어디에 나타나는지를 같이 모델링 하기 때문이다. 예술 작품과 현실 이미지는 pixel level은 매우 다르지만, 객체의 크기와 모양이 비슷하므로 YOLO는 여전히 좋은 예측을 할 수 있다.
