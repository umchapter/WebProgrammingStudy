# 알고리즘을 통해 구현한 웹 기반 이미지 처리 서비스 
# 프로젝트 소개
> OpenCV를 사용하지 않고 이미지 처리 알고리즘 분석을 통해 Javascript로 웹 이미지 처리 서비스를 구현하는 것을 목표로 하였다.
> 편의를 위해 RAW 이미지 파일(Grey Scale, 정방형 이미지)로 유형을 제한하였다.

# 시연
## https://umchapter.github.io/WebProgrammingStudy/

# 기능  <br>
## 1.화소점 처리
> 화소점 처리는 원 화소의 값이나 위치를 바탕으로 단일 화소 값을 변경하는 기술이다.

### 1. 밝기 조절
> $$Output \, Image \, Pixel = Input \, Image \, Pixel \pm \alpha$$

### 2. 색상 반전
> $$Output \, Image \, Pixel = 255 - Input \, Image \, Pixel$$

### 3. 흑백 처리
> $$Output \, Image \, Pixel = 255 - Input \, Image \, Pixel$$

### 4. 히스토그램 탐색
> 일정 간격으로 구별된 데이터의 발생빈도를 막대그래프 형태로 나열한 것을 히스토그램이라 한다. 히스토그램을 통해 이미지의 특성을 쉽게 파악할 수 있으며, 이를 활용하여 이미지의 픽셀 값의 분포를 원하는 방향으로 조정할 수 있다.

#### 4-1 히스토그램 스트레칭
> $$ Output \, Image \, Pixel = \frac {Input \, Image \, Pixel - Low}{High - Low} \times 255 $$
> 여기서 $High$와 $Low$는 각각 $Input \, Image$에서 가장 높은 값(밝기)과 낮은 값이다. 다시 말해 히스토그램 스트레칭은 픽셀에 대해 일종의 Min-Max Scaling을 수행한다. 이를 통해 분포를 고르게(스트레칭) 만들어 명암 대비가 높아진 뚜렷한 이미지를 얻을 수 있다.

#### 4-2. 엔드-인 탐색(End-In Search)
> ```math
> Output \, Image \, Pixel = \begin{Bmatrix} 0& Input \, Image \, Pixel < Low\\ \\ \frac {Input \, Image \, Pixel - Low}{High - Low} & Low \leq Input \, Image \, Pixel \lt High \\ \\ 255 & High \leq Input \, Image \, Pixel \end{Bmatrix}
> ```
> 엔드-인 탐색은 히스토그램 스트레칭을 수행할 이미지에 대해 일정 범위 밖의 밝기 값을 제거함으로써 히스토그램의 분포를 좀 더 균일하게 만드는 처리 기법이다. 즉 히스토그램 스트레칭에서는 이미지 내에서 결정된 High(Max)와 Low(Min)값을 사용하는 데 반해, 엔드-인 탐색에서는 사용자가 임의의 High와 Low의 임계값을 설정한다.

#### 4-3. 히스토그램 평활화(Equalization)
> 히스토그램 평활화는 히스토그램의 누적도수를 정규화하여 얻은 값을 통해 밝기 분포를 재분배하여 명암 대비를 최대화 하는 처리 기법이다.

##### - 단계별 수행절차
1. 픽셀의 명암값 $i$의 빈도수 히스토그램 생성 &rarr; $hist[i] \quad (0 \leq i \lt 256)$
2. 각 명암값 $i$에서 $0 \sim i$까지의 누적도수 계산 &rarr; $Sum[i]=\sum_{j=0}^i hist[j]$
3. 2단계에서 구한 누적도수를 정규화 &rarr; $n[i]=Sum[i] \times \frac{1}{N} \times I_{max}$ $(I_{max}$ 는 이미지에서 존재할 수 있는 가장 큰 밝기 값 &rarr; $255)$

## 2. 화소 영역 처리
> 화소 영역 처리란 해당 입력 화소 뿐만 아니라 그 주위의 화소값도 함께 고려하는 공간 영역 연산을 말한다. 이 점에서 화소의 원값이나 위치를 바탕으로 화소 값을 변경하는 화소점 처리와 구별된다.
> * 컨볼루션 연산 \
화소 영역 처리에서는 일정한 영역에 대하여 다음과 같은 합성곱 연산을 수행한다.
>$$\sum_{m=(x-k)}^{x+k} \sum_{(n=y-k)}^{y+k}(I[m,n] \times M[m,n])$$
>( $k:$마스크의 크기, $ __I__:$컨볼루션의 대상이 되는 이미지의 영역, $ __M__:$마스크 행렬 )
### 1. 엠보싱(Embossing)
> 엠보싱은 입력 영상을 양각 형태로 보이게 하는 이미지 처리 기술이다.
> * 엠보싱 마스크
> ```math
> \begin{pmatrix} -1 & 0 & 0 \\ 0 & 0 & 0 \\ 0 & 0 & 1 \end{pmatrix} \; or \; \begin{pmatrix} -1 & -1 & 0 \\ -1 & 0 & 1 \\ 0 & 1 & 1 \end{pmatrix}
> ```
>엠보싱 마스크는 두 가지 종류로 구별된다. 왼쪽과 같이 주대각 성분을 기반으로 하는 것과, 오른쪽과 같이 반대각선을 기준으로 하는 것이다. 이 프로젝트에는 효과가 뚜렷한 후자를 사용하였다.

### 3. 샤프닝(Sharpening)
> 샤프닝이란 주변 픽셀과의 차이를 극대화 시켜 경계 부분의 명암비를 증가시키는 처리 기법을 말한다.
> * 샤프닝 마스크
> ```math
> \begin{pmatrix} -1 & -1 & -1 \\ -1 & 9 & -1 \\ -1 & -1 & -1 \end{pmatrix} \; or \; \begin{pmatrix} -1 & -1 & -1 & -1 & -1 \\ -1 & -1 & -1 & -1 & -1 \\ -1 & -1 & 25 & -1 & -1 \\ -1 & -1 & -1 & -1 & -1 \\ -1 & -1 & -1 & -1 & -1  \end{pmatrix} \; or \; \cdots
> ```
> 이와 같이 정방형의 행렬에서 가운데 값을 제외한 나머지가 -1인 마스크를 사용한다.

### 4. 블러링(Blurring)
> 블러링이란 주변 픽셀과의 평균값을 이용하여 이미지를 매끄럽게(smoothing) 하는 처리 기법이다.
> * 블러링 마스크  
블러링 마스크는 마스크 내부의 픽셀들의 평균을 구한다. 평균법은 산술평균, 가중평균 등 다양하다. 이번 프로젝트에서는 산술평균과 가우스 분포를 이용한 가중평균, 그리고 양방향 필터를 구현하였다.
#### 4-1. 산술평균 블러링
> 산술평균 블러링 마스크는 마스크의 총 합이 1이고, 마스크 사이즈의 제곱의 역수로 채워져 있다. 예시는 다음과 같다. 
> ``` math
> \begin{pmatrix} \frac{1}{9} & \frac{1}{9} & \frac{1}{9} \\ \\ \frac{1}{9} & \frac{1}{9} & \frac{1}{9} \\ \\ \frac{1}{9} & \frac{1}{9} & \frac{1}{9} \end{pmatrix}
> ```

#### 4-2. 가우시안 블러링
> 가우시안 블러링 마스크는 이차원 정규분포에 따라 거리별로 가중치를 다르게 계산하는 가중평균 마스크이다. 산술평균 마스크에 비해 노이즈에 강하다는 장점이 있다.
> ```math
> \begin{align} & G_{xy} = \frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}} \\
> & \omega=\frac{1}{\sum G_{xy}} \end{align}
> ```
> $x$와 $y$가 마스크의 좌표임에 주의하라. 여기서 $\sigma$를 통해 표준편차를 조정하여 가중치를 변화시킬 수 있다. $\omega$는 정규화를 위한 가중치로, 이미지가 지나치게 어두워지는 것을 방지한다.

#### 4-3. 양방향 필터 블러링(Bilateral Filter -)
> 양방향 필터 블러링은 엣지가 소실되는 블러링의 단점을 극복한 블러링 기법이다. 이미지가 매끄럽게 변하면서도 초점이 흐려지듯 뭉개진 것처럼 보이지 않는 효과가 있다.
> $$BF[I]_p = \frac{1}{W_p}\sum_{q\in S}G_{\sigma_s}(\parallel p-q \parallel)G_{\sigma_r}(|I_p-I_q|)I_q$$
> 축약하면 가우시안 블러를 적용하고, 다시 가우스 분포를 활용하여 주변 픽셀과의 차이를 가중평균하는 것으로 설명할 수 있다. $\sigma_r$이 커질수록 이미지는 매끄러워지고 수채화처럼 변하게 된다.

### 5. 경계선 검출(Edge Detection)
> 경계선 검출은 이미지에서 픽셀 값이 급격하게 변하는 경계선을 검출하는 이미지 처리 기법이다.
> 본래 복잡하지만 상대적으로 우월한 Canny Edge Detection 기법을 구현하고 싶었으나, 시간 및 실력의 부족으로 완성하지 못했다. 이에 Sobel 마스크를 통해 경계선 검출기를 구현하였다.
> * Sobel Mask
> $$\nabla f = \left[\frac{\partial f}{\partial x},\frac{\partial f}{\partial y} \right]$$
> 위 그래디언트를 활용하여 마스크의 크기변화에 따른 가중치를 구현할 수 있다.

## 3. 기하학적 변환
> 기하학적 변환은 이미지를 구성하는 화소의 공간적 위치를 재배치하는 과정을 의미한다.

### 1. 상하/좌우 반전
> * 이미지 상하 반전 기능
> $\text{이미지의 좌상단을 (0,0)으로 하고, 우하단을 (MaxRow, MaxCol)라 할 때, 임의의 픽셀의 좌표} (i,j) \text{에 대해,}$ 
> $$\text{Output Image}(i,j) = \text{Input Image(MaxRow}-i,\; j)$$
>  * 이미지 좌우 반전 기능
> $$\text{Output Image}(i,j) = \text{Input Image}(i, \; \text{MaxCol}-j)$$

### 2. 이미지의 축소
> 이미지의 축소는 출력 이미지의 크기에 맞춰 원본 이미지에서 적절한 분포로 픽셀을 뽑아내는 과정을 거친다. 이 과정에서 픽셀의 손실을 얼마나 수용할지, 그리고 주변 픽셀값을 얼만큼 반영할지에 따라 알고리즘이 결정된다.  
> 우선 계단현상(aliasing)을 줄이기 위해 평균 블러링을 적용하고, 동시에 양방향 필터를 적용하여 이미지가 흐려지는 현상을 방지하였다. 그러나 계단현상이 일부 다시 나타나는 부작용이 있었다.

### 3. 이미지의 확대
> 이미지의 확대는 적절한 분포로 원본 이미지의 픽셀을 출력 이미지에 배분한 뒤 빈 공간을 메우는 과정을 거친다. 이 과정에서 빈 공간을 메우는 방법에 따라 알고리즘이 결정된다.  
> 이번 프로젝트에서는 양선형 보간법(Bilateral Interpolate)을 적용하였다. 상기의 이미지를 통해 알 수 있듯이, 픽셀 간 거리에 따른 가중 평균을 활용하여 새로운 픽셀의 값을 찾는다. 이번 프로젝트에서는 우선 모든 행에 대해 보간법을 먼저 수행하고, 이후 열에 대해 보간법을 수행하는 방식으로 구현하였다.

### 4. 회전
> 회전 알고리즘은 아래와 같이 표현할 수 있다.
> ```math
> \begin{bmatrix} x_{new} \\ y_{new} \end{bmatrix} = \begin{bmatrix} \cos\theta & -\sin\theta \\ \sin\theta & \cos\theta \end{bmatrix} \begin{bmatrix} x_{old} \\ y_{old} \end{bmatrix}
>```

> 그러나 단순히 위와 같이 계산하여 구현할 경우, 이미지에 빈 구멍(hole)이 생기는 문제와 이미지가 잘리는 문제 등이 발생한다. 따라서 몇 가지 보완을 수행해야 한다.
> * 회전 알고리즘의 보완
> ```math
> \begin{bmatrix} x_{old} \\ y_{old} \end{bmatrix} = \begin{bmatrix} \cos\theta & \sin\theta \\ -\sin\theta & \cos\theta \end{bmatrix} \begin{bmatrix} x_{new} -C_x_{new} \\ y_{old} - C_y_{new} \end{bmatrix} + \begin{bmatrix} C_x_{old} \\ C_y_{old} \end{bmatrix}
>```

> 위와 같이 역방향 사상을 고려하여 hole 문제를 해결한다. 다만 상기 $x,\;y$의 범위 문제에 유의하여야 한다.  
> 또한 회전에 따른 출력 이미지의 크기 변화를 아래와 같이 반영한다.
$$ H' = |H\cdot\cos\theta| + |W\cdot\sin\theta| $$
$$ W' = |H\cdot\sin\theta| + |W\cdot\cos\theta| $$

