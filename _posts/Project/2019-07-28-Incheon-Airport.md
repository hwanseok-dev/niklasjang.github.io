---
title: "[프로젝트] 인천공항 제 2 터미널 재배치 대안 평가"
excerpt: "2019.2.1~2019.2.28"
date: 2019-07-28
categories:
  - Project
tags:
  - Incheon-Airport
toc : true
toc_label: "Table of contents"
toc_icon: "list"  # corresponding Font Awesome icon name (without fa prefix)
toc_sticky: true
classes: wide  
---

## 프로젝트 설명

학교 교통 물류학부 김병종 교수님 밑에서 항공사 배치 대안 생성 과제에 참여하고 있습니다. 인천공항 터미널 확장 공사가 예정 중이어서 두 터미널 T1, T2에 어떤 항공사들을 배치하는 것이 효율적인지에 대해 여러가지 대안을 생성하고 평가하는 과제입니다.  

## 개인 역할  

이 과제에서 제가 맡은 부분은 아래와 같습니다.
​
1. 2018년 인천공항 취항항공사 목록을 먼저 받았습니다. 이 목록에는 각 항공사가 어떤 Alliance에 소속되어있는지 명시되어있습니다.
2. 2018년 인천공항을 이용했던 승객들의 간략한 데이터를 받았습니다. 개인을 식별할 수 있는 개인정보는 일체 들어있지 않습니다.
3. 저는 먼저 1.과2.의 자료를 종합하여 약 8천만명의 승객들이 어떤 항공사와 Alliance를 많이 이용했는지 전체적인 동향을 파악하였습니다. 
4. 그리고 3.에서 정리한 자료를 바탕으로 항공사와 Alliance별 전체 이용 승객 중에 한국인과 중국인의 비율을 구하였습니다. 
5. 마지막으로 이미 제시되어있는 대안에 모든 정보를 적용시켜 각 대안 별 특징을 구체화하였습니다. 이 특징들은 10년 뒤 인천공항을 이용할 것으로 예상되는 약 1억명의 항공수요와 이를 위한 사업모델을 분석하는데 도움을 주었습니다.

## 프로젝트 의의

사용한 언어는 MATLAB이고 프로젝트 수행 기간은 공식적으로는 한 달, 실제 수행 간은 일주일입니다. 

1. 처음 사용해보는 MATLAB으로 프로젝트를 수행했다는 점.
2. 처음으로 사회에 도움이 되는 프로그램을 작성했다는 점.
3. 처음으로 직접 작성한 프로그램으로 금전적인 소득을 올렸다는 점에서 오랫동안 기억에 남을 것 같습니다. 

## 프로젝트 Repo

프로젝트에서 사용했던 MATLAB 코드는 다음과 같습니다.

<https://github.com/niklasjang/Incheon_airport_relocation> 

730개의 엑셀 파일에 나누어진 2800만개의 출입국기록 데이터들을 정리하는 역할을 하는 코드들입니다.

~~(전체 파일 읽는데 시간 5시간 걸렸습니다.)~~

```txt
textToData : 터미널의 각 일별 데이터를 항공사별 내국인/중국인/기타외국인의 수로 추리는 코드
MergeData : textToData를 사용하는 과정에서 에러가 나서 분할된 결과를 하나로 합치는 코드 (new data = data1 +data2의 방식)
MergeTerminalData : T1을 정리한 자료와 T2를 정리한 자료를 MergeData를 사용해서 합칠 때, 결과를 확실하게 하기 위해 또 다른 방법으로 구현한 코드 (data1 += data2의 방식)
dataToResult : textToData의 자료를 Alliance별로 추리는 코드
```

## 프로젝트 결과

![result-1](/assets/images/projects/incheon-air-port/result-1.jpg)  

![result-2](/assets/images/projects/incheon-air-port/result-2.jpg)  

![result-3](/assets/images/projects/incheon-air-port/result-3.jpg)  


## 개발환경

1. Win10
2. MATLAB
3. EXCEL
