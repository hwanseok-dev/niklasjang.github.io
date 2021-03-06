---
title: "[PS][완전탐색][N자리 K진수] Chapter 0"
excerpt: "N자리 K진수 개념 이해하기"
date: 2020-03-22
categories:
  - PS
tags:
  - ps 
  - back-tracking
toc : true
toc_label: "Table of contents"
toc_icon: "list"  # corresponding Font Awesome icon name (without fa prefix)
toc_sticky: true
---

완전탐색의 첫 번째 알고리즘 백트래킹입니다. 백트래킹의 첫 번째 유형인 N자리 K진수에 대해서 학습합니다.
- - -

## 백트래킹 : 개념

백트래킹은 완전탐색의 일부입니다. 정답이 될 수 있는 모든 가능성을 살펴보는데, 정답이 될 수 없는 경우가 나오면 더이상 진행하지 않습니다. 이때 처음으로 되돌아가지 않고 한 step만 뒤로가서 다시 모든 경우를 살펴보는 방법을 의미합니다.  

## N자리 K진수 : 개념

길이가 N인 배열에 대해, 각 칸을 0~k-1로 채우는 모든 경우를 순회하는 경우를 의미합니다.  

Ex) 3자리  4진수
```
000
001
002
003
010
011
012
013
...
230
231
232
233
330
331
332
333
```

각 자리수에 대해서 0부터 k-1까지의 숫자라 순환되고 변경되는 자리수는 일의 자리 -> 십의 자리 -> 백의 자리 순서대로 진행됩니다. 코드를 작성할 때에는 재귀형태로 구현할 것이기 때문에 "백의 자리 0부터 k-1까지 순회한다. 이때 백의 자리가 저장될 때마다 십의 자리를 0부터 k-1까지 순회한다. 이때 십의 자리가 저장될 때마다 일의 자리를 0부터 k-1까지 순회한다."고 할 수 있습니다.  

## N자리 K진수 : 코드  

이를 코드로 구현하면 아래와 같습니다.    

```cpp
/*기본적인 N자리 K진수*/
#include <iostream>

using namespace std;

int n = 0, k = 0; //N:배열의 길이 , K: 진수
int arr[100]; //숫자가 저장될 배열

/*depth번째 index의 배열을 0부터 k-1까지 채운다. 0부터 n-1자리까지 모두 채워졌으면 출력한다.*/
void recur(int depth) {
  //0부터 n-1자리까지 모두 채워진 경우
  if (depth == n) {
    for (int j = 0; j < n; j++) {
      cout << arr[j];
    }
    cout << " ";
    return;
  }
  //각 칸을 0부터 k-1까지 저장한다. 
  for (i = 0; i < k; i++) {
    arr[depth] = i;
    recur(depth + 1);
  }
}

int main(void) {
  //빠른 표준입력
  cin.tie(NULL);
  ios::sync_with_stdio("false");
  cin >> n >> k;
  //배열의 0번쨰 index부터 채우기 시작해야한다.
  recur(0);
  return 0;
}
```

위 N자리 K진수의 기본 코드는 통으로 외우는 것이 좋습니다. if 조건문을 사용해서 recur()이 끝나야하는 조건을 설정해줍니다. recur()을 호출하는 부분이 for문에 있기 때문에 if 조건문이 참이 되어 return;이 호출되었을 때 for문이 다시 실행됩니다.  

앞서 언급한 3자리 4진수의 경우  

```
000
001
002
003
010
011
012
013
```

003 다음으로 010이 출력되는 과정을 생각해보겠습니다. 003을 출력한 것은 00이후에 2번 index에 0,1,2,3을 순서대로 채워넣었기 때문입니다. 003이 출력되고 return;이 호출되어 recur()이 종료합니다. 다음으로는 십의 자리에 1을 채우고 일의 자리에 0,1,2,3을 순서대로 채워넣습니다. 01까지 채우고 2번 index에 0을 넣을 때 이미 003에서의 3이 들어있을 것입니다. 이를 0,1,2,3 순서대로 덮어쓰면서 010,011,012,013이 출력됩니다.  

## N자리 K진수 : 문제

N자리 K진수를 연습하는 대표적인 문제로는 [N과M](https://www.acmicpc.net/workbook/view/2052)이 있습니다. 1~12 중에서 1~8까지의 문제가 N자리 K진수를 연습하는 부분입니다. 설명과 이해의 편의를 위해서 3 - 1 - 2 - 4 - 7 - 5 - 6 - 8의 순서로 문제를 풀이하겠습니다.  

### N과 M (3)

3번 문제는 가장 기본적인 N자리 K진수입니다. 문제의 조건을 정리하면 아래와 같습니다. 

1. N자리 K진수
1. 같은 수를 여러 번 골라도 된다.
1. 중복되는 수열은 한 번만 출력한다.
1. 각 수열은 공백으로 구분해서 출력한다.
1. 사전 순으로 증가하는 순서로 출력한다. 

3번 문제의 코드는 [여기](https://gist.github.com/niklasjang/7a05fb4ff5be248ebd145bd8f5c4bc90)에 있습니다.  

### N과 M (1)

1번 문제는 3번 문제와 한 가지 조건만 달라집니다. 

1. N자리 K진수
1. ~~같은 수를 여러 번 골라도 된다.~~ -> 같은 수를 여러 번 고르면 안된다. 
1. 중복되는 수열은 한 번만 출력한다.
1. 각 수열은 공백으로 구분해서 출력한다.
1. 사전 순으로 증가하는 순서로 출력한다. 

같은 수를 여러 번 고르지 않게 하기 위해서는 bool 타입의 visited 배열을 사용하는 것이 가장 편리합니다.  

1번 문제의 코드는 [여기](https://gist.github.com/niklasjang/fdf53e26b64594cca15b878370916ee9)에 있습니다.  

if(!visited[i])를 사용하는 것은 조건문을 통해서 실행되면 안되는 경우를 먼저 처리하기 위해서입니다. 그리고 recur(depth+1)를 호출한 뒤에 visited[i] = false;로 바꿔주는 부분을 주의해서 기억합니다.  

### N과 M (2)

2번 문제는 3번 문제와 한 가지 조건만 달라집니다. 

1. N자리 K진수
1. ~~같은 수를 여러 번 골라도 된다.~~ -> 고른 수열은 오름차순이어야 한다. 
1. 중복되는 수열은 한 번만 출력한다.
1. 각 수열은 공백으로 구분해서 출력한다.
1. 사전 순으로 증가하는 순서로 출력한다. 

2번 문제의 코드는 [여기](https://gist.github.com/niklasjang/50013629c03ea86cbd070e135f2a379a)에 있습니다.  

위 방법은 1번째 이상의 index에 대해서 조건문을 통해서 문제의 조건을 만족하는 방법입니다. 이전 인덱스보다 큰 값만 배열에 저장될 수 있도록 하는 것입니다.  

2번 문제에서 recur()의 param을 2개로 늘려서 푸는 방법은 [여기](https://gist.github.com/niklasjang/601c51561f4fe1753767c5ee422bd321)에 있습니다.  

위 방법은 start를 인자로 넘겨서 각 자리의 숫자라 start~k-1까지 저장될 수 있도록 합니다. 이 문제를 풀 때는 먼저 설명한 방법이 직관적일 수 있으나 문제 풀이의 방법 넓히는 것에 목적이 있습니다.  

### N과 M (4)

2번 문제는 3번 문제와 한 가지 조건만 달라집니다. 

1. N자리 K진수
1. ~~같은 수를 여러 번 골라도 된다.~~ -> 고른 수열은 비내림차순이어야 한다. 길이가 K인 수열 A가 A1 ≤ A2 ≤ ... ≤ AK-1 ≤ AK를 만족하면, 비내림차순이라고 한다.
1. 중복되는 수열은 한 번만 출력한다.
1. 각 수열은 공백으로 구분해서 출력한다.
1. 사전 순으로 증가하는 순서로 출력한다. 

이 방법은 2번 문제의 코드에서 한 가지만 변형하면 풀 수 있습니다. ~~recur(depth+1, i+1);~~ -> recur(depth+1, i);  

이 문제의 코드는 [여기](https://gist.github.com/niklasjang/2e1b4c3e66d4828f4f2e2af7a336098e)에 있습니다.  

### N과 M (7)

7번 문제는 3번 문제와 한 가지 조건만 달라집니다. 

1. N자리 K진수
1. 같은 수를 여러 번 골라도 된다. + 단, K진수가 아닌 주어지는 K개의 수만 사용할 수 있다. 
1. 중복되는 수열은 한 번만 출력한다.
1. 각 수열은 공백으로 구분해서 출력한다.
1. 사전 순으로 증가하는 순서로 출력한다. 

입력으로 주어지는 값들을 배열에 저장하고 sort()를 사용해서 오름차순으로 정렬한 뒤 사용하면 됩니다.  

이 문제의 코드는 [여기](https://gist.github.com/niklasjang/5bcc97fe2a1ba1b41dd1f14c2e4ba1ba)에 있습니다. 

### N과 M (5)
### N과 M (6)
### N과 M (8)

5,6,8번 문제도 7번 문제와 같이 앞서 풀이한 문제를 변형하여 N자리 K진수의 방법으로 해결할 수 있습니다. 


