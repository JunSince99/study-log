# 분할정복
분할정복은 문제의 입력사례를 두 개 이상의 작은 입력사례로 계속 분할하여 답을 바로 얻을 수 있을 정도로 작은 문제로 만드는 기법이다.  
분할정복은 햐향식(top-down) 문제풀이 방식으로 상위 문제의 해답은 하위 문제의 해답을 가지고 구한다. 이는 기본적으로 재귀함수가 작동하는 원리이다. 먼저 알고리즘을 재귀로 작성하고 이후에 재귀 알고리즘은 더 효율적인 반복 알고리즘으로 수정할 수도 있다.
## 2.1 이분검색
Chapter1에서 봤던 이분검색 알고리즘도 분할정복 방식이다.  
먼저 재귀로 작성한 이분검색 알고리즘을 보면
~~~cpp
index location (index low, index high)
{
    index mid;
    if (low > high)
        return 0;
    else {
        mid = 내림((low + high) / 2);
        if(x == S[mid])
            return mid
        else if (x < S[mid])
            return location (low, mid-1);
        else
            return location (mid + 1, high);
    }
}
~~~
재귀 이분검색 알고리즘은 꼬리 재귀(tail-recursion)이다.  
꼬리 재귀란 호출 이후에 더 이상 연산이 없는, 재귀 호출이 함수의 마지막 동작인 재귀 함수이다.
이러한 꼬리 재귀 함수는 1장에서 구현했던 것처럼 반복 알고리즘으로 변환하기 수월하다.
## 2.2 합병정렬
쌍방 합병은 정렬된 두 개의 배열을 하나의 정렬된 배열로 합치는 것이다. 이 합병을 통하여 임의의 배열을 정렬할 수 있다. 예를 들어 원소가 16개인 배열을 계속 반으로 나눠 한 개의 원소를 가진 배열로 나눈다. 한 개의 원소를 가진 배열은 반드시 정렬돼있는 상태라고 볼 수 있으므로 나눠진 모든 배열을 다시 쌍방 합병하면 정렬된 배열 하나를 얻을 수 있을 것이다.
~~~cpp
void mergesort (배열의 길이 n, 배열 S[])
{
    int h = n/2;
    mergesort(배열 U의 길이 h, U[] = S[1~h]);
    mergesort(배열 V의 길이 m, V[] = S[h+1~n]);

    merge(h, m, U[], V[], S[]);
}

void merge(int h, int m, U[], V[], S[])
{
    index i, j, k;

    i=1; j=1; k=1;
    while (i <= j && j <= m) {
        if (U[i] < V[j]) {
            S[k] = U[i];
            i++;
        } else {
            S[k] = V[j];
            j++;
        }
        k++;
    }
    if (U[]나 V[]에 남은 부분이 있다면)
    비교 후에 남은 부분 S[k~h+m]에 추가하기
}
~~~
합병정렬 알고리즘은 정렬 과정에서 2n개 정도의 배열이 추가적으로 만들어진다.  
이것을 개선하여 n개의 원소를 가진 배열 하나만 필요한 알고리즘을 만들 수도 있다.
~~~cpp
void mergesort2 (index low, index high)
{
    index mid;

    if (low < high) {
        mid = 내림((low + high) / 2);
        mergesort2(low, mid);
        mergesort2(mid+1, high);

        merge2(low, mid, high);
    }
}

void merge2 (index low, index mid, index high)
{
    index i, j, k;
    U[low ~ high]; // 새로 만든 n개의 원소를 가진 배열(합병에 필요한 지역 배열)
    i = low; j = mid + 1; k = low;

    while (i <= mid && j <= high) {
        if (S[i] < S[j]) {
            U[k] = S[i];
            i++;
        }
        else {
            U[k] = S[j];
            j++;
        }
        k++;
    }
    if (i > mid) // i가 mid보다 크면 j~high가 남아있는 것이므로
        U[k ~ high] = S[j ~ high]; // j~high를 U[]에 추가
    else
        U[k ~ high] = S[i ~ mid];// 반대의 경우
    S[low ~ high] = U[low ~ high]; // 정렬된 U를 S로 대체
}
~~~
## 2.3 분할정복 설계방법
분할정복 설계 절차
~~~
1. 문제의 입력사례를 하나 이상의 작은 입력사례로 분할한다.
2. 작은 입력사례들을 각각 정복한다(푼다). 작은 입력사례가 충분히 작지 않으면 재귀 호출한다.
3. 필요하다면, 작은 입력사례의 해답을 통합하여 원래 입력사례의 해답을 구한다.
~~~
3번에서 필요하다면을 붙인 이유는 이분검색과 같은 알고리즘처럼 해답을 통합할 필요가 없는 경우도 있기 때문이다.
## 2.4 빠른정렬(분할교환정렬)
## 2.5 쉬트라센의 행렬곱셈 알고리즘
## 2.6 큰 정수 계산법
### 2.6.1 큰 정수 표현: 덧셈과 기타 1차시간 연산
### 2.6.2 큰 정수 곱셈
## 2.7 임계값의 결정
## 2.8 분할정복법을 사용할 수 없는 경우