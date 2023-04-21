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

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
 // 처리 알고리즘
var value = parseInt(document.getElementById("brightness").value); // 웹페이지에서 사용자가 입력한 밝기 조절 값
for(var i=0; i<outH; i++) { // outH는 출력 이미지의 높이(행)
    for(var j=0; j<outW; j++) { // outW는 출력 이미지의 너비(열)
        if(inImage[i][j] + value < 0){ // inImage[i][j]는 입력 이미지의 해당 좌표의 픽셀값
            outImage[i][j] = 0; //outImagep[i][j]는 출력 이미지의 해당 좌표의 픽셀값
        } else if (inImage[i][j] + value > 255) {
            outImage[i][j] = 255;
        } else { // 위 부분은 밝기 값이 상한, 하한 값을 넘어가면 상한, 하한 값으로 바꿔주는 부분
            outImage[i][j] = inImage[i][j] + value; // 일반적인 경우
        }
    }
}
//
```

</div>
</details>

</br>

### 2. 색상 반전
> $$Output \, Image \, Pixel = 255 - Input \, Image \, Pixel$$

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
for(var i=0; i<outH; i++) { // outH는 출력 이미지의 높이(행)
    for(var j=0; j<outW; j++) { // outW는 출력 이미지의 너비(열)
        outImage[i][j] = 255 - inImage[i][j]; //outImagep[i][j]는 출력 이미지의 해당 좌표의 픽셀값, inImage[i][j]는 입력 이미지의 해당 좌표의 픽셀값
    }
}
//

```

</div>
</details>

</br>

### 3. 흑백 처리
> $$Output \, Image \, Pixel = \begin{pmatrix} 0 & Input \, Image \, Pixel \lt Threshold \\ 255 & Input \, Image \, Pixel \geq Threshold \end{pmatrix}$$

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

<details>
<summary>128기준 코드 보기</summary>
<div markdown="1">

```javascript
// 128 기준 알고리즘
for(var i=0; i<outH; i++) { // outH는 출력 이미지의 높이(행)
    for(var j=0; j<outW; j++) { // outW는 출력 이미지의 너비(열)
        if(inImage[i][j]>=128) { // inImage[i][j]는 입력 이미지의 해당 좌표의 픽셀값
            outImage[i][j] = 255; //outImagep[i][j]는 출력 이미지의 해당 좌표의 픽셀값
        } else {
            outImage[i][j] = 0;
        }
    }
}
//

```
</div>
</details>

</br>

<details>
<summary>중앙값 기준 코드 보기</summary>
<div markdown="1">

```javascript
var countArray = new Array(inH*inW); // 중앙값 뽑기 위해 일렬(입력 이미지의 행*너비 크기) 배열 생성

for(var i=0; i<outH; i++) { // outH는 출력 이미지의 높이(행)
    for(var j=0; j<outW; j++) { // outW는 출력 이미지의 너비(열)
        countArray[i*256 + j] = inImage[i][j]; // inImage[i][j]는 입력 이미지의 해당 좌표의 픽셀값
    }
}

countArray.sort(function(a, b) { // 정렬
    return a - b ;
});

// 처리 알고리즘
for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        if(inImage[i][j]>=countArray[Math.floor(inW*inH/2)]) { // 정렬 이후 Math.floor(inW*inH/2) 번째 값보다 큰가?
            outImage[i][j] = 255;
        } else {
            outImage[i][j] = 0;
        }
    }
}
//

```
</div>
</details>

</br>

<details>
<summary>평균값 기준 코드 보기</summary>
<div markdown="1">

```javascript
// 평균 값
var sum = 0;
for(var i=0; i<inH; i++) { // inH는 입력 이미지의 높이(행)
    for(var j=0; j<inW; j++) { // inW는 입력 이미지의 너비(열)
        sum += inImage[i][j]; // inImage[i][j]는 입력 이미지의 해당 좌표의 픽셀값
    }
}

// 처리 알고리즘
var mean = sum/(inH*inW);
for(var i=0; i<outH; i++) { // outH는 출력 이미지의 높이(행)
    for(var j=0; j<outW; j++) { // outW는 출력 이미지의 너비(열)
        if(inImage[i][j]>=mean) { // 입력 이미지의 픽셀값이 평균보다 큰지 판별
            outImage[i][j] = 255; //outImagep[i][j]는 출력 이미지의 해당 좌표의 픽셀값
        } else {
            outImage[i][j] = 0;
        }
    }
}
//

```
</div>
</details>

</br>

</div>
</details>

</br>

### 4. 히스토그램 탐색
> 일정 간격으로 구별된 데이터의 발생빈도를 막대그래프 형태로 나열한 것을 히스토그램이라 한다. 히스토그램을 통해 이미지의 특성을 쉽게 파악할 수 있으며, 이를 활용하여 이미지의 픽셀 값의 분포를 원하는 방향으로 조정할 수 있다.

#### 4-1 히스토그램 스트레칭
> ```math
>Output \, Image \, Pixel = \frac {Input \, Image \, Pixel - Low}{High - Low} \times 255
>```
> 여기서 $High$와 $Low$는 각각 $Input \, Image$에서 가장 높은 값(밝기)과 낮은 값이다. 다시 말해 히스토그램 스트레칭은 픽셀에 대해 일종의 Min-Max Scaling을 수행한다. 이를 통해 분포를 고르게(스트레칭) 만들어 명암 대비가 높아진 뚜렷한 이미지를 얻을 수 있다.

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
var low=inImage[0][0], high=inImage[0][0]; // 기준값을 좌상단 픽셀로 설정함
for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        if(inImage[i][j] < low) { // 입력 이미지 픽셀 중 가장 낮은 값 탐색
            low = inImage[i][j];
        }
        if(inImage[i][j] > high) { // 입력 이미지 픽셀 중 가장 높은 값 탐색
            high = inImage[i][j];
        }
    }
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        var out = ((inImage[i][j]-low)/(high-low) * 255.0) // Min-Max Scaling 진행 * 255 정규화
        outImage[i][j] = parseInt(out);
    }
}
//
```

</div>
</details>

</br>

#### 4-2. 엔드-인 탐색(End-In Search)
> ```math
> Output \, Image \, Pixel = \begin{Bmatrix} 0& Input \, Image \, Pixel < Low\\ \\ \frac {Input \, Image \, Pixel - Low}{High - Low} & Low \leq Input \, Image \, Pixel \lt High \\ \\ 255 & High \leq Input \, Image \, Pixel \end{Bmatrix}
> ```
> 엔드-인 탐색은 히스토그램 스트레칭을 수행할 이미지에 대해 일정 범위 밖의 밝기 값을 제거함으로써 히스토그램의 분포를 좀 더 균일하게 만드는 처리 기법이다. 즉 히스토그램 스트레칭에서는 이미지 내에서 결정된 High(Max)와 Low(Min)값을 사용하는 데 반해, 엔드-인 탐색에서는 사용자가 임의의 High와 Low의 임계값을 설정한다.

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
var low=inImage[0][0], high=inImage[0][0]; // 기준값을 좌상단 픽셀로 설정함
for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        if(inImage[i][j] < low) { // 입력 이미지 픽셀 중 가장 낮은 값 탐색
            low = inImage[i][j];
        }
        if(inImage[i][j] > high) { // 입력 이미지 픽셀 중 가장 높은 값 탐색
            high = inImage[i][j];
        }
    }
}

low += 50;  // 최저값과 최대값에 일정 값을 가감 및 차감하여 양 극단값 일부 날려줌
high -= 50;
for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        var out = ((inImage[i][j]-low)/(high-low) * 255.0) // Min-Max와 정규화
        if(out<0) { // 범위 벗어날 경우 보정해줌
            out = 0;
        } else if(out >=256 ) {
            out = 255;
        }
        outImage[i][j] = parseInt(out);
    }
}
//
```

</div>
</details>

</br>

#### 4-3. 히스토그램 평활화(Equalization)
> 히스토그램 평활화는 히스토그램의 누적도수를 정규화하여 얻은 값을 통해 밝기 분포를 재분배하여 명암 대비를 최대화 하는 처리 기법이다.

##### - 단계별 수행절차
>1. 픽셀의 명암값 $i$의 빈도수 히스토그램 생성 : $$hist[i] \quad (0 \leq i \lt 256)$$
>2. 각 명암값 $i$에서 $0 \sim i$까지의 누적도수 계산 : $$Sum[i]= \sum_{j=0}^{i} hist[j]$$
>3. 2단계에서 구한 누적도수를 정규화 : $$n[i]=Sum[i] \times \frac{1}{N} \times I_{max}$$ <p align=center>$(I_{max}$는 이미지에서 존재할 수 있는 가장 큰 밝기 값 &rarr;} $255)$<p/>

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
// 1단계 : 히스토그램 생성
var histo = new Array(256).fill(0); // 256 길이의 1차원 배열 생성
for(var i=0; i<inH; i++) { // 입력 이미지에서 픽셀의 밝기 값에 따라 histo 배열에 개수 기록
    for(var j=0; j<inW; j++) {
        histo[inImage[i][j]] += 1;
    }
}
// 2단계 : 누적 히스토그램 생성
var sumHisto = new Array(256).fill(0); // 256 길이의 1차원 배열 생성2
var sumValue = 0;
for(var i=0; i<inH; i++) { // histo 배열에 기록된 도수를 누적합 하여 sumHisto에 기록
        sumValue += histo[i];
        sumHisto[i] += sumValue;
}

// 3단계 : 정규화된 누적합 생성
// n[i] = sumHisto[i] * (1.0/(inH*inW) * 255.0)
var n = new Array(256).fill(0.0); // 256 길이의 1차원 배열 생성3
for(var i=0; i<256; i++) {
    n[i] = sumHisto[i] * (1.0/(inH*inW) * 255.0) // 정규화 누적합 계산
}

// 최종 : 정규화 된 누적합을 이용해서 픽셀값 변환
for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        outImage[i][j] = parseInt(n[inImage[i][j]]); // 입력 이미지의 특정 좌표의 밝기 값을 정규화 누적합의 값으로 대체
    }
}
//
```

</div>
</details>

</br>

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

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
// 마스크
var maskSize = parseInt(document.getElementById("maskSizeSelector").value); // 웹 페이지에서 사용자가 입력한 마스크 사이즈
var mask = new Array(maskSize);
var centerPoint = (maskSize-1)/2;

// 반대각선만 0인 마스크
for(var i=0; i<maskSize; i++) {
    mask[i] = new Array(maskSize).fill(0.0);
    for(var j=0; j<maskSize; j ++) {
        if(i+j<maskSize-1) { // 반대각선의 좌상단은 -1
            mask[i][j] = -1;
        } else if(i+j>maskSize-1) { // 반대각선의 우하단은 1
            mask[i][j] = 1;
        } else {
            continue;
        }
    }
}

var averageOfInImage = array2DAverage(inImage); // 입력 이미지의 픽셀값의 평균 구해주는 함수
/*
    // 2차원 배열 평균 함수
    function array2DAverage(array2D) {
        var sum = 0.0;
        for(array of array2D) {
            for(element of array) {
                sum += element;
            }
        }
        return sum / (array2D.length * array2D[0].length);
    }
*/

// 임시 입력 배열
var tempInImage = new Array(inH+maskSize-1);
for(var i=0; i<inH+maskSize-1; i++){
    tempInImage[i] = new Array(inW+maskSize-1).fill(averageOfInImage); // 입력 이미지에 패딩하고 평균으로 채움
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        tempInImage[i+centerPoint][j+centerPoint] = inImage[i][j]; // 임시 입력 이미지 중앙에 실제 입력 이미지 덧씌움
    }
}

// 임시 출력 배열(크기는 똑같지만 실수배열)
tempOutImage = new Array(outH);
for(var i=0; i<outH; i++) {
    tempOutImage[i] = new Array(outW);
}

// 컨볼루션 연산
for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        var S = 0.0;
        for(var m=0; m<maskSize; m++){
            for(var n=0; n<maskSize; n++) {
                S += tempInImage[i+m][j+n] * mask[m][n];
            }
        }
        tempOutImage[i][j] = S;
    }
}
// 후처리 -> mask의 합이 0이므로 0으로 수렴하여 어두워지는 것 후처리.
for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        outImage[i][j] = parseInt(tempOutImage[i][j] + averageOfInImage); // 입력 이미지의 평균 밝기 값을 더해주면서 보정
    }
}
```

</div>
</details>

</br>

### 3. 샤프닝(Sharpening)
> 샤프닝이란 주변 픽셀과의 차이를 극대화 시켜 경계 부분의 명암비를 증가시키는 처리 기법을 말한다.
> * 샤프닝 마스크
> ```math
> \begin{pmatrix} -1 & -1 & -1 \\ -1 & 9 & -1 \\ -1 & -1 & -1 \end{pmatrix} \; or \; \begin{pmatrix} -1 & -1 & -1 & -1 & -1 \\ -1 & -1 & -1 & -1 & -1 \\ -1 & -1 & 25 & -1 & -1 \\ -1 & -1 & -1 & -1 & -1 \\ -1 & -1 & -1 & -1 & -1  \end{pmatrix} \; or \; \cdots
> ```
> 이와 같이 정방형의 행렬에서 가운데 값을 제외한 나머지가 -1인 마스크를 사용한다.

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
// 샤프닝 마스크 (합 1)
// 마스크
var maskSize = parseInt(document.getElementById("maskSizeSelector").value); // 웹 페이지에서 사용자가 입력한 마스크 사이즈
var mask = new Array(maskSize);
var centerPoint = (maskSize-1)/2;

for(var i=0; i<maskSize; i++) {
    mask[i] = new Array(maskSize).fill(-1.0); // 샤프닝 마스크의 경우 한 가운뎃 값만 제외하고 나머지 -1
}
mask[centerPoint][centerPoint] = Math.pow(maskSize,2); // 한 가운뎃 값 지정

// 임시 입력 배열
var averageOfInImage = array2DAverage(inImage); // 입력 이미지 픽셀 값의 평균

var tempInImage = new Array(inH+maskSize-1);
for(var i=0; i<inH+maskSize-1; i++){
    tempInImage[i] = new Array(inW+maskSize-1).fill(averageOfInImage); // 평균 패딩
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        tempInImage[i+centerPoint][j+centerPoint] = inImage[i][j]; // 임시 출력 이미지 중앙에 실제 입력 이미지 덧씌움
    }
}

// 임시 출력 배열(크기는 똑같지만 실수배열)
tempOutImage = new Array(outH);
for(var i=0; i<outH; i++) {
    tempOutImage[i] = new Array(outW);
}

// 컨볼루션 연산
for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        var S = 0.0;
        for(var m=0; m<maskSize; m++){
            for(var n=0; n<maskSize; n++) {
                S += tempInImage[i+m][j+n] * mask[m][n];
            }
        }
        tempOutImage[i][j] = S;
    }
}

for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        outImage[i][j] = parseInt(tempOutImage[i][j]);
    }
}
//
```

</div>
</details>

</br>

### 4. 블러링(Blurring)
> 블러링이란 주변 픽셀과의 평균값을 이용하여 이미지를 매끄럽게(smoothing) 하는 처리 기법이다.
> * 블러링 마스크  
블러링 마스크는 마스크 내부의 픽셀들의 평균을 구한다. 평균법은 산술평균, 가중평균 등 다양하다. 이번 프로젝트에서는 산술평균과 가우스 분포를 이용한 가중평균, 그리고 양방향 필터를 구현하였다.
#### 4-1. 산술평균 블러링
> 산술평균 블러링 마스크는 마스크의 총 합이 1이고, 마스크 사이즈의 제곱의 역수로 채워져 있다. 예시는 다음과 같다. 
> ``` math
> \begin{pmatrix} \frac{1}{9} & \frac{1}{9} & \frac{1}{9} \\ \\ \frac{1}{9} & \frac{1}{9} & \frac{1}{9} \\ \\ \frac{1}{9} & \frac{1}{9} & \frac{1}{9} \end{pmatrix}
> ```

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
// 마스크
var maskSize = parseInt(document.getElementById("maskSizeSelector").value); // 웹 페이지에서 사용자가 입력한 마스크 사이즈
var centerPoint = (maskSize-1)/2;

var weight = Math.pow(maskSize, 2); // 마스크 사이즈를 통해 평균계산에 활용
var mask = new Array(maskSize);
for(var i=0;i<maskSize;i++) {
    mask[i] = new Array(maskSize).fill(1.0/weight);
}


// 임시 입력 배열(패딩 위한 여유 공간) -> 여기서는 average로 채워줌
// 임시 입력 배열
var tempInImage = new Array(inH+(maskSize-1));
for(var i=0; i<inH+(maskSize-1); i++){
    tempInImage[i] = new Array(inW+(maskSize-1)).fill(array2DAverage(inImage));
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        tempInImage[i+centerPoint][j+centerPoint] = inImage[i][j];
    }
}

// 컨볼루션 연산
for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        var S = 0.0;
        for(var m=0; m<maskSize; m++){
            for(var n=0; n<maskSize; n++) {
                S += tempInImage[i+m][j+n] * mask[m][n];
            }
        }
        outImage[i][j] = parseInt(S);
    }
}
//
```

</div>
</details>

</br>

#### 4-2. 가우시안 블러링
> 가우시안 블러링 마스크는 이차원 정규분포에 따라 거리별로 가중치를 다르게 계산하는 가중평균 마스크이다. 산술평균 마스크에 비해 노이즈에 강하다는 장점이 있다.
> ```math
> \begin{align} & G_{xy} = \frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}} \\
> & \omega=\frac{1}{\sum G_{xy}} \end{align}
> ```
> $x$와 $y$가 마스크의 좌표임에 주의하라. 여기서 $\sigma$를 통해 표준편차를 조정하여 가중치를 변화시킬 수 있다. $\omega$는 정규화를 위한 가중치로, 이미지가 지나치게 어두워지는 것을 방지한다.

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
var maskSize = parseInt(document.getElementById("maskSizeSelector").value); // 웹 페이지에서 사용자가 입력한 마스크 사이즈
var centerPoint = (maskSize-1)/2.0;

var sigmaGaussian = 3; // Gaussian filter의 표준편차
var mask = new Array(maskSize);
for(var i=0;i<maskSize;i++) {
    mask[i] = new Array(maskSize);
}

// 가우시안 필터 계산식
var weight = 0.0; // 정규화
for(var i=0;i<maskSize;i++) {
    for(var j=0;j<maskSize;j++) {
        mask[i][j] = (1.0/(2.0*Math.PI*Math.pow(sigmaGaussian, 2)))
                        *Math.exp(-(Math.pow(i-centerPoint, 2) + Math.pow(j-centerPoint, 2))/(2.0*Math.pow(sigmaGaussian, 2))) // 2차원 가우스 분포 계산식
        weight += mask[i][j];
    }
}

// 임시 입력 배열
var tempInImage = new Array(inH+(maskSize-1));
for(var i=0; i<inH+(maskSize-1); i++){
    tempInImage[i] = new Array(inW+(maskSize-1)).fill(array2DAverage(inImage)); // 입력 이미지의 평균 패딩
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        tempInImage[i+centerPoint][j+centerPoint] = inImage[i][j]; // 임시 입력 이미지 중앙에 실제 입력 이미지 덧씌움
    }
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        var S = 0.0;
        for(var m=0; m<maskSize; m++){
            for(var n=0; n<maskSize; n++) {
                S += (tempInImage[i+m][j+n] * mask[m][n] / weight); // 계산
            }
        }outImage[i][j] = parseInt(S);
    }  
}
//
```

</div>
</details>

</br>

#### 4-3. 양방향 필터 블러링(Bilateral Filter -)
> 양방향 필터 블러링은 엣지가 소실되는 블러링의 단점을 극복한 블러링 기법이다. 이미지가 매끄럽게 변하면서도 초점이 흐려지듯 뭉개진 것처럼 보이지 않는 효과가 있다.
> ```math
> BF[I]_p = \frac{1}{W_p}\sum_{q\in S}G_{\sigma_s}(\parallel p-q \parallel)G_{\sigma_r}(|I_p-I_q|)I_q
> ```  
> 축약하면 가우시안 블러를 적용하고, 다시 가우스 분포를 활용하여 주변 픽셀과의 차이를 가중평균하는 것으로 설명할 수 있다. $\sigma_r$이 커질수록 이미지는 매끄러워지고 수채화처럼 변하게 된다.

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
var maskSize = parseInt(document.getElementById("maskSizeSelector").value); // 웹 페이지에서 사용자가 입력한 마스크 사이즈
var centerPoint = (maskSize-1)/2.0;

var sigmaGPrime = 7; // -> Bilateral filter에서 적용할 G'의 표준편차
var sigmaGaussian = 3; // Gaussian filter의 표준편차
var mask = new Array(maskSize);
for(var i=0;i<maskSize;i++) {
    mask[i] = new Array(maskSize);
}

// 가우시안 필터 계산식
for(var i=0;i<maskSize;i++) {
    for(var j=0;j<maskSize;j++) {
        mask[i][j] = (1.0/(2.0*Math.PI*Math.pow(sigmaGaussian, 2)))
                        *Math.exp(-(Math.pow(i-centerPoint, 2) + Math.pow(j-centerPoint, 2))/(2.0*Math.pow(sigmaGaussian, 2))) // 2차원 가우스 분포 계산식
    }
}
// 임시 입력 배열
var tempInImage = new Array(inH+(maskSize-1));
for(var i=0; i<inH+(maskSize-1); i++){
    tempInImage[i] = new Array(inW+(maskSize-1)).fill(array2DAverage(inImage)); // 입력 이미지의 평균 패딩
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        tempInImage[i+centerPoint][j+centerPoint] = inImage[i][j]; // 임시 입력 이미지 중앙에 실제 입력 이미지 덧씌움
    }
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        var S = 0.0;
        var weight = 0.0; // -> 정규화
        for(var m=0; m<maskSize; m++){
            for(var n=0; n<maskSize; n++) {
                var gPrime = Math.exp(-Math.pow((tempInImage[i+m][j+n] - tempInImage[i + centerPoint][j + centerPoint]), 2) / (2.0 * Math.pow(sigmaGPrime, 2))); // 중앙값과 주변 픽셀의 차이에 대해 가우스 분포를 활용하여 계산
                weight += gPrime * mask[m][n];
                S += (tempInImage[i+m][j+n] * gPrime * mask[m][n]);
            }
        }outImage[i][j] = parseInt(S * (1.0 / weight));
    }  
}
//
```

</div>
</details>

</br>

### 5. 경계선 검출(Edge Detection)
> 경계선 검출은 이미지에서 픽셀 값이 급격하게 변하는 경계선을 검출하는 이미지 처리 기법이다.
> 본래 복잡하지만 상대적으로 우월한 Canny Edge Detection 기법을 구현하고 싶었으나, 시간 및 실력의 부족으로 완성하지 못했다. 이에 Sobel 마스크를 통해 경계선 검출기를 구현하였다.
> * Sobel Mask
> $$\nabla f = \left[\frac{\partial f}{\partial x},\frac{\partial f}{\partial y} \right]$$
> 위 그래디언트를 활용하여 마스크의 크기변화에 따른 가중치를 구현할 수 있다.

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 처리 알고리즘
// 마스크
var maskSize = parseInt(document.getElementById("maskSizeSelector").value); // 웹 페이지에서 사용자가 입력한 마스크 사이즈
var centerPoint = (maskSize-1)/2.0;

var mask = new Array(maskSize);
for(var i=0;i<maskSize;i++) {
    mask[i] = new Array(maskSize);
}

// sobel filter
var horizontal_filter = new Array(maskSize); // 편미분을 위해 횡방향으로의 변화만 추적
for(var i=0; i<maskSize; i++) {
    horizontal_filter[i] = new Array(maskSize);
    for(var j=0; j<maskSize; j++) {
        if(j == centerPoint) {
            horizontal_filter[i][j] = 0;
            continue;
        }
        var dist = Math.hypot(i-centerPoint, j-centerPoint); // 중심부로부터의 직선거리 계산
        horizontal_filter[i][j] = 1.0/Math.pow(dist, 2); // 단위 벡터 보정 * 기울기 계산 위한 변량 보정 -> 제곱의 역수 형태로 나타남
        if(j<centerPoint) {
            horizontal_filter[i][j] *= -1;
        }
    }
}

var vertical_filter = new Array(maskSize);
for(var i=0; i<maskSize; i++) {
    vertical_filter[i] = new Array(maskSize);
    for(var j=0; j<maskSize; j++) {
        if(i==centerPoint) {
            vertical_filter[i][j] = 0;
            continue;
        }
        var dist = Math.hypot(i-centerPoint, j-centerPoint); // horizontal filter 와 같은 논리
        vertical_filter[i][j] = 1.0/Math.pow(dist, 2);
        if(i<centerPoint) {
            vertical_filter[i][j] *= -1;
        }
    }
}

// 임시 입력 배열
var imageAverage = array2DAverage(inImage);
var tempInImage = new Array(inH+maskSize-1);
for(var i=0; i<inH+maskSize-1; i++){
    tempInImage[i] = new Array(inW+maskSize-1).fill(imageAverage); // 평균 패딩
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        tempInImage[i+centerPoint][j+centerPoint] = inImage[i][j];
    }
}


// sobel 필터 적용            
for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        var S = 0.0;
        for(var m=0; m<maskSize; m++){
            for(var n=0; n<maskSize; n++) {
                var horizonValue = tempInImage[i+m][j+n] * horizontal_filter[m][n];
                var verticalValue = tempInImage[i+m][j+n] * vertical_filter[m][n];
                S += horizonValue + verticalValue; // 합산하여 그래디언트 적용한 이미지 얻음
            }
        }
        outImage[i][j] = parseInt(S);
    }
}
```

</div>
</details>

</br>

## 3. 기하학적 변환
> 기하학적 변환은 이미지를 구성하는 화소의 공간적 위치를 재배치하는 과정을 의미한다.

### 1. 상하/좌우 반전
> * 이미지 상하 반전 기능
> $\text{이미지의 좌상단을 (0,0)으로 하고, 우하단을 (MaxRow, MaxCol)라 할 때, 임의의 픽셀의 좌표} (i,j) \text{에 대해,}$ 
> $$\text{Output Image}(i,j) = \text{Input Image(MaxRow}-i,\; j)$$
>  * 이미지 좌우 반전 기능
> $$\text{Output Image}(i,j) = \text{Input Image}(i, \; \text{MaxCol}-j)$$

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 상하 미러링 처리
for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        outImage[i][j] = inImage[outH -1 - i][j]; // length는 index + 1
    }
}
//

// 좌우 미러링 처리
for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        outImage[i][j] = inImage[i][outW - 1 - j]; // length는 index + 1
    }
}
//
```

</div>
</details>

</br>

### 2. 이미지의 축소
> 이미지의 축소는 출력 이미지의 크기에 맞춰 원본 이미지에서 적절한 분포로 픽셀을 뽑아내는 과정을 거친다. 이 과정에서 픽셀의 손실을 얼마나 수용할지, 그리고 주변 픽셀값을 얼만큼 반영할지에 따라 알고리즘이 결정된다.  
> 우선 계단현상(aliasing)을 줄이기 위해 평균 블러링을 적용하고, 동시에 양방향 필터를 적용하여 이미지가 흐려지는 현상을 방지하였다. 그러나 계단현상이 일부 다시 나타나는 부작용이 있었다.

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 축소 알고리즘
var maskSize = 3;

var mask = new Array(maskSize);
for(var i=0; i<maskSize; i++) {
    mask[i] = new Array(maskSize).fill(1.0);
}

var sigmaGPrime = 0.01; // -> Bilateral filter에서 적용할 G'의 표준편차

// 임시 입력 배열
var tempInImage = new Array(inH+(maskSize-1));
for(var i=0; i<inH+(maskSize-1); i++){
    tempInImage[i] = new Array(inW+(maskSize-1)).fill(array2DAverage(inImage)); // 평균 패딩
}

for(var i=0; i<inH; i++) {
    for(var j=0; j<inW; j++) {
        tempInImage[i+(maskSize-1)/2][j+(maskSize-1)/2] = inImage[i][j];
    }
}

for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        var newX = Math.round(i/value);
        var newY = Math.round(j/value);
        var S = 0.0;
        var weight = 0.0; // -> 정규화
        for(var m=0; m<maskSize; m++){
            for(var n=0; n<maskSize; n++) {
                var centerOfMask = (maskSize - 1)/2;
                // Bilateral Filter 적용하여 흐리지 않게 처리
                var gPrime = Math.exp(-Math.pow((tempInImage[newX+m][newY+n] - tempInImage[newX + centerOfMask][newY + centerOfMask]), 2) / (2.0 * Math.pow(sigmaGPrime, 2)));
                weight += gPrime * mask[m][n];
                S += (tempInImage[newX+m][newY+n] * gPrime * mask[m][n]);
            }
        }outImage[i][j] = parseInt(S * (1.0 / weight));
    }  
}
//
```

</div>
</details>

</br>

### 3. 이미지의 확대
> 이미지의 확대는 적절한 분포로 원본 이미지의 픽셀을 출력 이미지에 배분한 뒤 빈 공간을 메우는 과정을 거친다. 이 과정에서 빈 공간을 메우는 방법에 따라 알고리즘이 결정된다.  
> 이번 프로젝트에서는 양선형 보간법(Bilateral Interpolate)을 적용하였다. 상기의 이미지를 통해 알 수 있듯이, 픽셀 간 거리에 따른 가중 평균을 활용하여 새로운 픽셀의 값을 찾는다. 이번 프로젝트에서는 우선 모든 행에 대해 보간법을 먼저 수행하고, 이후 열에 대해 보간법을 수행하는 방식으로 구현하였다.

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 임시 출력 배열
var tempOutImage = new Array(outH);
for(var i=0; i<outH; i++){
    tempOutImage[i] = new Array(outW);
}

// 행방향 보간 함수
function interpolateRow(image, rowNum, startCol, endCol) {
    var totalLenght = endCol - startCol;
    for(var k=startCol+1; k<endCol; k++) {
        var alpha = (k - startCol)/totalLenght;
        image[rowNum][k] = (1-alpha)*image[rowNum][startCol] + alpha*image[rowNum][endCol];
    }
}

// 임시 출력 배열에 inImage 분배와 동시에 행방향 보간 수행
var distributeRatio = ((outH-1)/(inH-1)); // -> inH 값들 사이에 outH의 빈 공간 배분한다고 생각

for(var i=0; i<inH; i++) {
    var startCol = null; // 행마다 시작점 초기화
    var endCol = null;
    for(var j=0; j<inW; j++) {                           
        tempOutImage[Math.round(i*distributeRatio)][Math.round(j*distributeRatio)] = inImage[i][j];
        if(startCol == null) { // 시작점 초기화 된 상황에서는 새로 부여하고 다음 픽셀로.
            startCol = Math.round(j*distributeRatio);
            continue;
        } else {
            endCol = Math.round(j*distributeRatio); // 끝 열을 현재 열로 부여
            interpolateRow(tempOutImage, Math.round(i*distributeRatio), startCol, endCol)
            startCol = Math.round(j*distributeRatio); // 시작열을 다시 현재 열로 부여
            continue;
        }
    }
}           

// 열 방향 보간 수행
function interpolateCol(image, colNum, startRow, endRow) { // 특정 열의 보간 함수
    var totalLenght = endRow - startRow;
    for(var k=startRow+1; k<endRow; k++) {
        var beta = (k - startRow)/totalLenght;
        image[k][colNum] = (1-beta)*image[startRow][colNum] + beta*image[endRow][colNum];
    }
}

for(var j=0; j<outW; j++) { // 열방향 보간
    var startRow = null; // 열마다 시작점 초기화
    var endRow = null;
    for(var i=0; i<outH; i++) {      
        if(tempOutImage[i][j] == undefined) { // 빈 공간 스킵
            continue;
        }            
        if(startRow == null) { // 시작점 초기화 된 상황에서는 새로 부여하고 다음 픽셀로.
            startRow = i;
            continue;
        } else {
            endRow = i; // 끝 열을 현재 열로 부여
            interpolateCol(tempOutImage, j, startRow, endRow)
            startRow = i; // 시작열을 다시 현재 열로 부여
            continue;
        }
    }
}

for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {                        
        outImage[i][j] = Math.round(tempOutImage[i][j]);
    }
}
//
```

</div>
</details>

</br>

### 4. 회전
> 회전 알고리즘은 아래와 같이 표현할 수 있다.
> ```math
>\begin{bmatrix} x_{new} \\ y_{new} \end{bmatrix} = \begin{bmatrix} \cos\theta & -\sin\theta \\ \sin\theta & \cos\theta \end{bmatrix} \begin{bmatrix} x_{old} \\ y_{old} \end{bmatrix}
>```

> 그러나 단순히 위와 같이 계산하여 구현할 경우, 이미지에 빈 구멍(hole)이 생기는 문제와 이미지가 잘리는 문제 등이 발생한다. 따라서 몇 가지 보완을 수행해야 한다.
> * 회전 알고리즘의 보완
>```math
>{\begin{bmatrix} x_{old} \\ y_{old} \end{bmatrix}} = {\begin{bmatrix} \cos\theta & \sin\theta \\ -\sin\theta & \cos\theta \end{bmatrix}} {\begin{bmatrix} x_{new} -C_{x_{new}} \\ y_{new} - C_{y_{new}} \end{bmatrix}} + {\begin{bmatrix} C_{x_{old}} \\ C_{y_{old}} \end{bmatrix}}
>```

> 위와 같이 역방향 사상을 고려하여 hole 문제를 해결한다. 다만 상기 $x,y$의 범위 문제에 유의하여야 한다.  
> 또한 회전에 따른 출력 이미지의 크기 변화를 아래와 같이 반영한다.
$$H' = |H\cdot\cos\theta| + |W\cdot\sin\theta|$$
$$W' = |H\cdot\sin\theta| + |W\cdot\cos\theta|$$

<details>
<summary>소스 코드 보기</summary>
<div markdown="1">

```javascript
// 결과 배열 생성
var value = document.getElementById("rotation").value; // 웹 페이지에서 사용자가 입력한 각도
var theta = Math.PI/180 * value; // 라디안으로 변환
const sinTheta = Math.sin(theta); // 사인 계산
const cosTheta = Math.cos(theta); // 코사인 계산

rangeX = (Math.abs(inH*cosTheta) + Math.abs(inW*sinTheta)); // 회전에 따라 변화하는 이미지 사이즈 조정
rangeY = (Math.abs(inH*sinTheta) + Math.abs(inW*cosTheta));

outH = parseInt(rangeX);
outW = parseInt(rangeY);

cX = inH/2; // 원본 이미지의 중심점
cY = inW/2;

midX = outH/2; // 출력 이미지의 중심점
midY = outW/2;

outCanvas.height = outH;
outCanvas.width = outW;

outImage = new Array(outH);
for(var i=0; i<outH; i++) {
    outImage[i] = new Array(outW);
}            

// 회전 알고리즘
for(var i=0; i<outH; i++) {
    for(var j=0; j<outW; j++) {
        // hole 문제 해결 위해 역방향 사상으로 구성
        // outImage로 옮기고 싶은 이미지(=inImage의 회전된 이미지)의 회전축이 cX, cY 이므로 보정해줌.
        var oldX = parseInt(cosTheta*(i - midX) + sinTheta*(j - midY) + cX);
        var oldY = parseInt(-sinTheta*(i - midX) + cosTheta*(j - midY) + cY);
        if(((oldX>=0)&(oldX<inH))&((oldY>=0)&(oldY<inW))){ 
            outImage[i][j] = inImage[oldX][oldY];
        } else {
            outImage[i][j] = 255; // 역방향 사상에서 계산한 좌표가 원본 이미지의 범위에서 벗어나는 경우 처리
        }
    }
}
//
```

</div>
</details>

</br>
