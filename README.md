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
> $$Output \, Image \, Pixel = \frac {Input \, Image \, Pixel - Low}{High - Low} \times 255$$
> 여기서 $High$와 $Low$는 각각 $Input \, Image$에서 가장 높은 값(밝기)과 낮은 값이다. 다시 말해 히스토그램 스트레칭은 픽셀에 대해 일종의 Min-Max Scaling을 수행한다. 이를 통해 분포를 고르게(스트레칭) 만들어 명암 대비가 높아진 뚜렷한 이미지를 얻을 수 있다.

#### 4-2. 엔드-인 탐색(End-In Search)
> $$Output \, Image \, Pixel = \begin{Bmatrix} 0& Input \, Image \, Pixel < Low\\ \\ \frac {Input \, Image \, Pixel - Low}{High - Low} & Low \leq Input \, Image \, Pixel \lt High \\ \\ 255 & High \leq Input \, Image \, Pixel \end{Bmatrix} $$
> 엔드-인 탐색은 히스토그램 스트레칭을 수행할 이미지에 대해 일정 범위 밖의 밝기 값을 제거함으로써 히스토그램의 분포를 좀 더 균일하게 만드는 처리 기법이다. 즉 히스토그램 스트레칭에서는 이미지 내에서 결정된 High(Max)와 Low(Min)값을 사용하는 데 반해, 엔드-인 탐색에서는 사용자가 임의의 High와 Low의 임계값을 설정한다.

#### 4-3. 히스토그램 평활화(Equalization)
> 히스토그램 평활화는 히스토그램의 누적도수를 정규화하여 얻은 값을 통해 밝기 분포를 재분배하여 명암 대비를 최대화 하는 처리 기법이다.

##### - 단계별 수행절차
1. 픽셀의 명암값 $i$의 빈도수 히스토그램 생성 $\rarr hist[i] \quad (0 \leq i \lt 256)$
2. 각 명암값 $i$에서 $0 \sim i$까지의 누적도수 계산 $\rarr Sum[i] = \sum_{j=0}^{i}{hist[j]} $
3. 2단계에서 구한 누적도수를 정규화 $\rarr n[i]=Sum[i] \times \frac{1}{N} \times I_{max}$ $(I_{max}$ 는 이미지에서 존재할 수 있는 가장 큰 밝기 값 $\rarr 255)$

## 2. 화소 영역 처리
> 화소 영역 처리란 해당 입력 화소 뿐만 아니라 그 주위의 화소값도 함께 고려하는 공간 영역 연산을 말한다. 이 점에서 화소의 원값이나 위치를 바탕으로 화소 값을 변경하는 화소점 처리와 구별된다.