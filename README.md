# Improvement of Image Coding Performance using Neural Network  

# 신경망 기술을 활용한 이미지 부호화 성능 개선

## Proposal

### Step 1

Image를 input 라고 하자.

VCM의 경우, input을 feature로 받으므로 input $X$는 feature라고 하자. VCM의 매커니즘은 아래와 같다.

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0ngabfpq3j20rk02cglp.jpg" alt="image-20220326182405876" style="zoom:67%;" />

Feature Extract 과정을 통해 Image에서 Feature을 추출하고, 추출된 Feature $X$를 NNIC의 입력으로 넣는다. 압축 후 복원된 Feature $\hat{X}$을 Analysis Net에 넣어 Detection, Segmentation 등을 수행한다. 성능평가는 [MAP](https://lapina.tistory.com/98)을 통해 진행한다.

NNIC의 경우, input을 image로 받으므로 input $X$는 image라고 하자. NNIC의 매커니즘은 다음과 같다.

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0ngaqcbppj20cg02wt8l.jpg" alt="image-20220326183355042" style="zoom:67%;" />

Image $X$를 NNIC의 입력으로 넣는다. NNIC 과정을 통해 압축 후 복원된 이미지 $\hat{X}$를 얻는다. $X$와 $\hat{X}$를 통해 PSNR을 구한 후, PSNR을 통해 성능평가를 진행한다. (2022. 3. 26 현재 하고 있는 NNVC 실습이 이에 해당한다.)

Step1을 통해 얻은 결과는 Anchor가 되어 이후 진행할 연구의 기초 비교대상이 된다.

