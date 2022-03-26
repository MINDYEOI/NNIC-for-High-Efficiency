# Improvement of Image Coding Performance using Neural Network  

# 신경망 기술을 활용한 이미지 부호화 성능 개선

## Proposal

### Step 1

Image를 input 라고 하자. NNIC는 Minnen, Lee, Cheng이 제안한 Mean Scale Hyperprior 모델을 기본으로 약간 수정된 모델이다.

VCM의 경우, input을 feature로 받으므로 input $X$는 feature라고 하자. VCM에서의 블록도는 아래와 같다.

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0ngabfpq3j20rk02cglp.jpg" alt="image-20220326182405876" style="zoom:67%;" />

Feature Extract 과정을 통해 Image에서 Feature을 추출하고, 추출된 Feature $X$를 NNIC의 입력으로 넣는다. 압축 후 복원된 Feature $\hat{X}$을 Analysis Net에 넣어 Detection, Segmentation 등을 수행한다. 성능평가는 [MAP](https://lapina.tistory.com/98)을 통해 진행한다.

이미지를 입력으로 받는 NNIC의 경우, input $X$는 image라고 하자. 이미지를 입력으로 받는 NNIC의 블록도는 다음과 같다.

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0ngaqcbppj20cg02wt8l.jpg" alt="image-20220326183355042" style="zoom:67%;" />

Image $X$를 NNIC의 입력으로 넣는다. NNIC 과정을 통해 압축 후 복원된 이미지 $\hat{X}$를 얻는다. $X$와 $\hat{X}$를 통해 PSNR을 구한 후, PSNR을 통해 성능평가를 진행한다. (2022. 3. 현재 하고 있는 NNVC 실습이 이에 해당한다.)

**Step1을 통해 얻은 결과는 Anchor가 되어 이후 진행할 연구의 기초 비교대상이 된다.**

본 연구에서는 이미지를 입력으로 받는 NNIC를 연구하므로 이에만 초점을 맞춰서 제안 과정을 설명하고자 한다.

### Step 2 (essential)

Scaling을 중간 과정에 포함시킨다. 블록도는 다음과 같다.

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0ngsd1dfaj20q201y74b.jpg" alt="image-20220326193314830" style="zoom:67%;" />

Downscaling 과정을 통해 해상도를 낮추고, Super Resolution 또는 Bicubic Interpolation과 같은 기법을 통해 Upscaling을 하여 결과를 얻는다.

이 과정을 통해 R-D Curve를 확인해보면 아래와 같을 것으로 예상한다.

​	<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0nhijmh4rj20p80fwwfb.jpg" width=50% alt="image-20220326195828191" style="text-align: center;"/>

낮은 bpp에선 Anchor보다 PSNR 성능이 좋게 나오지만, 높은 bpp에선 Anchor보다 더 낮은 성능을 보일 것이다. 즉, 저해상도에선 더 좋은 성능을 보이지만 고해상도에선 더 낮은 성능이 예상된다.



### Step 3 (additional)

Step 2의 방식처럼 Scaling을 하게 되면 복잡도로 인해 하드웨어 처리에 불리해지고 bitrate에 따른 성능 변화가 생기므로 **블록을 기반으로** 처리하는 방안을 제안한다.

먼저 Image를 여러개의 2Nx2N 블록으로 나눈다.

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0npybc94sj20hk0b4aak.jpg" width=50% alt="image-20220327002957918" style="text-align: center;" />

Mode 1과 Mode 2, 두가지 모드로 실험을 진행한다.

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0nlz7r1f1j215k0jk0u5.jpg" alt="image-20220326223256273" style="zoom:80%;" />

먼저 **Mode 1**에서는 2Nx2N 블록을 NxN 블록 4개로 나누어 NxN 블록을 입력으로 받는 NNIC에 통과시켜 복원된 블록을 얻는다. 이를 다시 2Nx2N 블록으로 복원하여, 복원된 2Nx2N 블록(하늘색)과 오리지널 2Nx2N 블록(파란색)으로 성능을 측정한다.

**Mode 2**에서는 2Nx2N 블록을 Downsampling 하여 NxN 블록으로 만든다. 이 블록을 NxN 블록을 입력으로 받는 NNIC에 통과시켜 복원된 블록을 얻고, 이를 2Nx2N 블록으로 Upsampling 한다. 복원된 2Nx2N 블록(하늘색)과 오리지널 2Nx2N 블록(파란색)으로 성능을 측정한다.

각각 Mode에서 **입력이 이미지일 때와 블록일 때의 성능을 비교**하고, mode끼리의 성능 또한 비교한다.

인코더에선 모드 정보를 저장하고, 디코더에선 모드 정보를 받아서 복원한다.

예상되는 결과는 다음과 같다.

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0npn8udktj20p80fwmy2.jpg" width=50% alt="image-20220327003947376" style="text-align: center;" />



### Step 4 (Challenge)

Step3 단계에선 Down(Up)sampling을 bicubic interpolation 등 hand-craft method를 통해 진행한다. 이번 단계에서는 Sampling(Scaling)을 **NN based Down/Up Scaling**을 통해 진행한다. NN based Down/Up Scaling과 NNIC를 joint training 시켜서 더 좋은 성능을 도출해낼 수 있도록 한다.

예상되는 결과는 다음과 같다.

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1h0nq3tu6nlj20p80fw0tn.jpg" width=50% alt="image-20220327004629567" style="text-align: center;" />
