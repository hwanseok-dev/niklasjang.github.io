---
title: "히스토그램부터 모폴로지까지"
excerpt: "오일석 - Computer Vision Chapter 2."
date: 2019-06-03
categories:
  - Opencv
tags:
  - opencv

toc : true
toc_label: "Table of contents"
toc_icon: "list"  # corresponding Font Awesome icon name (without fa prefix)
toc_sticky: true
classes: wide  
---

# CGV-2019-오일석-02장 영상처리.pptx

## 히스토그램

### 히스토그램 계산

영상 f의 히스토그램은 명암값이 나타난 빈도수로, [0,L - 1] 사이의 명암값 각각이 영상에 몇 번 나타나는지 표시한다.

알고리즘 (2-1)  

식(2.1)  
식(2.2)  
예제 (2-1)  

### 히스토그램 용도

#### 영상의 특성 파악

- 어두운 영상인지, 밝은 영상인지 파악
- 이진화를 진행할 때 히스토그램이 어느 쪽으로 치우쳐있는지는 사용함. 두 개으 봉우리가 뚜렷하다면 이진화하기 쉽다.

#### 평활화를 통한 영상 품질 개선

히스토그램을 평활화하면 명암의 범위, 즉 `동적 범위`가 늘어나서 영상이 이전보다 선명해진다.  

식 (2.3)  

식 2.3의 식이 어떻게 사용되는지는 아래 예제 (2.2)의 표를 보면 알 수 있다.  

예제 2-2  

평활화를 진행한 히스토그램은 아래와 같은 모슴을 가진다.  

그림 2- 10

그림 2- 11을 보면 전체적으로 화질이 나빠진 것으로 느껴질 수도 있다. 새가 벌래는 먹는 모습은 뚜렷해졌지만 전체적으로 
사진의 느낌이 퇴화되었다고 느낄 수도 있다. 상황에 맞도록 평활화를 진행해야 한다. 

### 히스토그램의 역투영와 얼굴 검출

`역투영`은 물체의 모양은 무시하고 단순히 컬러 분포만으로 검출하는 방법으로, 히스토그램은 컬러 분포를 표현하는 방법을 사용된다.

그림 2-12  

예를 들어 히스토그램 역투영을 적용해 사람의 얼굴을 찾을 때는, 얼굴을 검출하기 위해서 비교 기준으로 모델의 얼굴의 히스토그램을 지정하고 이와 비슷한 히스토그램을 얼굴이라고 검출한다.
얼굴 검출에서 1차원만 사용하면 정확한 결과를 내기 어렵다. 특히 명암은 더욱 그러하다. 사람 얼굴 RGB를 HSI 공간으로 바꾼 후 I(integrity, 명암)은 무시하고 H(Hue, 색상)과 S(Saturation)채도를 사용하기로 하자. 

H와 S 두 개의 축에 대해서 각각 L단계로 나누면 $L_{2}$의 칸이 만들어진다. 축 하나를 표현하는데 1Byte를 사용하면 256*256칸의 너무 큰 공간을 사용한다. 또한 원래의 얼굴 영상이
100 * 80 과 같은 작은 크기의 이미지였다면 대부분의 칸은 0을 갖는 행렬이 될 것이다. 이는 너무 낭비가 심하므로 원래 히스토그램의 4*4 칸을 1 칸으로 축소하는 등의 `양자화`가 필요하다.  

어떤 입력 영상이 얼굴 영상인지 분별하는 알고리즘은 다음과 같다.

1. 입력 영상에서 특정 지점을 선택한다. 이 지점의 H와 S를 얻는다.
2. 모델의 히스토그램에서 (H,S) 좌표의 값을 확인한다. 이 값이 크다면 입력 영상이 모델의 영상과 비슷할 가능성이 높다.  

알고리즘 (2-3)  

모델 히스토그램과 입력 영상의 히스토그램의 비율을 계산한다. 모델의 히스토그램에 입력영상의 히스토그램의 역수를 곱하는 것으로 생각할 수 있다. 입력 영상의 얼굴 부분만 히스토그램의
값이 크다고 할 때, 모델 영상의 히스토그램에 입력 영상의 히스토그램의 역수를 곱하면 그 비율은 얼굴로 추정되는 부분에서 역수의 값을 곱한 것보다 더 큰 값을 갖게 될 것이다. (히스토그램의 값이 1보다 작은 0.01 ~0.08의 값을 갖는다.) 이러한 개념을 사용해서 입력 영상에서 얼굴 부분을 추출한 역투영 영상을 얻을 수 있다.  

## 이진 영상 : 명암 영상을 흑과 백만 가진 영상으로 변환

### 오츄 알고리즘 

- 이진화 했을 때 흑 그룹과 백 그룹 각각이 균일할수록 좋다는 원리에 착안하여 균일성이 클수록 `임계값 t`에게 높은 점수를 준다.
- `균일성`은 그룹의 분산으로 측정하는 데, **분산이 작을수록 균일성이 높다.** 히스토그램에서 두 개의 뾰족한 봉우리가 있으면 쉽고 좋은 결과를 얻을 수 있다고 볼 수 있다.
- 가능한 모든 임계값 t에 대해 점수를 계산한 후 가장 좋은 t를 최종 임계값으로 취한다. 

오츄 알고리즘에 대한 자세한 설명은 생략한다.  

## 연결요소

화소들이 어떻게 연결되어있는지 생각해볼 때, opencv에서는 가장 간단하게 사각형으로 정의한다.  하나의 화소는 이웃한 8개의 화소를 가진다. 이를 `8-연결성`이라고 부른다. 

연결요소는 이전에 정리했던 포스팅을 참고해서 이해합니다.

[flood-fill](https://niklasjang.github.io/boj/Flood-Fill/)  
[connected-components](https://niklasjang.github.io/boj/DFS-BFS/)  

## 영상 처리의 세 가지 기본 연산

영상 처리는 화소 입장에서 새로운 값을 부여받는 것을 의미합니다. 새로운 값을 취하는 방식에 따라서 3가지로 나뉩니다.

1. 점 연산 : 화소가 자신의 값만을 보고 새로운 값을 정한다.
2. 영역 연산 : 화소가 이웃에 있는 몇 개의 화소들을 보고 새로운 값을 정한다.
3. 기하 연산 : 일정한 기하하적 규칙에 따라 다른 곳에 있는 값을 취한다.  

### 점 연산

#### 선형 연산

식(2.11)  

위 식은 한 점의 화소값을 밝게/어룹게/반전하는 식을 나타냅니다. 이들은 모두 `선형 연산`에 속합니다.  

#### 비성현 연산

식(2.12)  

`감마 수정`이라고 부르는 비선형 연산은 [0,1] 사이의 값을 갖는 정규 영상입니다.  

그림(2.19)  

그림은 감마값에 따른 변환 함수의 모양과 연산을 적용한 결과를 보여줍니다. $/gamma$가 1보다 작으면 밝아지고, 1보다 크면 어두워지는 효과를 나타냅니다.
만약 $/gamma$가 1이라면 원본 영상을 나타냅니다.  

앞서 공부했던 이진화의 `계단 함수` 또는 `누적 히스토그램`도 점연산에 속하는 변환함수입니다.  

`디졸브`라는 효과도 있습니다. 두 영상을 겹치는 것으로 아래와 같은 식으로 효과를 나타냅니다.  

$ I = /alpha F + (1 - /alpha)B$ 단, $0<= /alpha <= 1$  

### 영역 연산  

그림(2.22)  

위 그림은 1차원에 관한 간단한 예제이지만, 매칭에 대한 물리적인 직관을 얻는데 아주 유용합니다. 이를 통해서 `상관(corelation)`과 `컨볼루션(convolution)`을 이해할 수 있을 것입니다. 


#### 상관과 컨볼루션

문제를 해결하기 위해서 $u$를 $f$의 위치 0,1,2,3,~ ,9에 각각 씌워 곱의 합을 계산하고, 그 결과를 새로운 영상 $g$라고 해보겠습니다. 그림(2-22)의 상관을 보면 index 6의 위치에서 최대 출력의 값 29를 갖습니다. 바로 window와 같은 값을 가지는 index가 6이기 때문입니다. 이처럼 **윈도우와 영상이 얼마나 비슷한지 측정해주는 연산을 `상관`이라고 합니다. `컨볼루션`은 `상관`과 비슷한데 윈도우를 적용하기 전에 뒤집는 것만 다릅니다.** 이렇게 상관과 컨볼루션을 계산한 결과를 window를 움직이면서 그때그때 적용하면 안되고 새로운 영상에 기록해둬야합니다.  

이제 1차원 윈도우르 2차원으로 확장해보겠습니다.  

그림(2.23)  

상관과 컨볼루션이 어떻게 다른지 보기 좋은 그림입니다. 상관의 출력 영상은 윈도우가 뒤집힌 형태이고 컨볼루션은 윈도우가 그대로 출력된 꼴이 됩니다.  

식(2.14)  

식에서 상관에서는 + 컨볼루션에서는 -가 붙은 것을 볼 수 있습니다. 이는 윈도우를 뒤집는 효과를 낼 수 있습니다. 매칭하여 물체를 검출하는 목적이 있으면 뒤집어서 맞추는 컨볼루션은 합리적이지 않습니다. 따라서 상관을 적용합니다. 반면 신호처리 분야는 연산의 특성과 동작을 분석하는 `임펄스 반응`이라는 성질을 사용합니다. 이 `임펄스 반응`은 상관에서는 나타나지 않고 컨볼루션에서만 나타납니다. 즉, 상황에 맞는 쓰임이 있습니다.  

이 책에서는 연산을 적용할 때 `상관`처럼 윈도우를 그대로 사용할 것이지만 연산을 부를 때는 `컨볼루션`이라고 하겠습니다. 많은 연구에서 이런 방식으로 사용하고 있기 때문입니다.  

다양한 컨볼루션의 효과는 아래와 같습니다.  

그림(2-24)  

#### 비선형 연산

커볼루션은 선형연산입니다. 상수를 곱하고 이들이 합을 구하는 단순한 연산이기 때문입니다.  

비선형 연산에는 메디안 필터가 있습니다.  

### 기하 연산

화소값을 갱신할 때 멀리 떨어져 있는 화소의 값도 영향을 주는 경우가 있습니다. 이 경우가 바로 `기하 연산`이 필요한 경우입니다. 

#### 동차 좌표와 동차 행렬

기하 변환은 `동차 좌표`와 `동차 행렬`로 표현합니다. 동차 좌표는 2차원 상의 점 $(x,y)$에 세 번째 값을 더해서 $(x,y,1)$로 변형시킨 형태입니다. 이 벡터를 원래 벡터와 구분하기 위해서 아래와 같이 표기합니다.  

식(2.15)  

동차 좌표는 세 요소에 같은 값을 곱하면 같은 좌표가 되는 특징을 가지고 있습니다. 예를 들어서 $(x,y,1)$와 $(ax,ay,a)$는 같은 동차 좌표입니다.  

동차 행렬은 아래의 표를 보고 이해합니다.  

표 (2-1) page 86  

예를 들어서 어떤 점을 y 방향으로 3, x 방향으로 2만큼 이동시키는 동차 행렬은 아래와 같습니다.  

식(2.16)  

예제(2.3)  
예제(2.4)  

### 영상에 기하 변환 적용

그림(2.27)  

기하 변환을 적용하면 결과 값이 double이 되는데 이를 정수로 변환하는 과정에서 위와 같은 문제가 발생할 수 있습니다. 이와 같이 인공적으로 발생하는 시각적으로 불만족스러운 현상을
모두 `에일리어싱aliasing`이라고 부릅니다. 이러한 현상을 해소하려는 과정을 `안티-에일리어싱anti-`라고 부릅니다.  안티-에일리러싱의 한 방법으로는 `양선형 보간법`이 있습니다.  

식(2.18)  
그림(2.30)  

## 모폴로지

모폴로지는 `이진 모폴로지`와 `명암 모폴로지`로 나뉜다.

### 이진 모폴로지

`모폴로지`는 `구조요소`를 사용하여 이진 영상에 있는 연결요소의 모양을 조작한다.  

그림 2-36  

위 그림은 다양항 구조요소를 보여줍니다. 모폴로지에서 구조 요소는 집합을 표현하는데, 값이 1인 요소만 집합에 속합니다. { (0, 0), (0,1), (-1,0), (0,-1), (1,0)} 이중에서(0,0)이 중앙값을 나타냅니다. 위 그림의 가장 오른쪽처럼 비대칭일 수도 있습니다.  

구조요소를 사용해서 4가지 연산을 진행할 수 있습니다.

1. 팽창
2. 침식
3. 열기
4. 닫기

식(2.22)  
식(2.23)  
식(2.24)  
식(2.25)  

그림(2-38)  

### 명암 모폴로지

명암 모폴로지는 구조요소의 값이 조금 달라진 형태입니다.  

그림(2-39)  

그림(2-40)  

위 그림에서 봉우리를 매우거나 깎는 역할을 수행해야 합니다.  

식(2.26)
식(2.27)
식(2.28)
식(2.29)
식(2.30)
식(2.31)










 

