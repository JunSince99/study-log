- [분할정복](#분할정복)
  - [2.1 이분검색](#21-이분검색)
  - [2.2 합병정렬](#22-합병정렬)
  - [2.3 분할정복 설계방법](#23-분할정복-설계방법)
  - [2.4 빠른정렬(분할교환정렬)](#24-빠른정렬분할교환정렬)
  - [2.5 쉬트라센의 행렬곱셈 알고리즘](#25-쉬트라센의-행렬곱셈-알고리즘)
  - [2.6 큰 정수 계산법](#26-큰-정수-계산법)
    - [2.6.1 큰 정수 표현: 덧셈과 기타 1차시간 연산](#261-큰-정수-표현-덧셈과-기타-1차시간-연산)
    - [2.6.2 큰 정수 곱셈](#262-큰-정수-곱셈)
  - [2.7 분할정복법을 사용할 수 없는 경우](#27-분할정복법을-사용할-수-없는-경우)
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
Quicksort는 합병정렬과 비슷하지만 배열을 분할하는 방식이 다르다.  
pivot 원소를 설정하여 pivot보다 작은 원소는 왼쪽으로, 크거나 같은 원소는 오른쪽으로 분할한다.  
피벗을 고르는 법도 효율성에 큰 영향을 끼치지만 편의상 배열의 첫번째 원소를 피벗으로 정하는 알고리즘을 짜보자.
~~~cpp
void quicksort (index low, index high)
{
    index pivotpoint;

    if (high > low) {
        partition (low, high, pivotpoint);
        quicksort (low, pivotpoint - 1);
        quicksort (pivotpoint + 1, high);
    }
}

void partition (index low, index high, index& pivotpoint) {
    index i, j;
    keytype pivotitem;

    pivotitem = S[low];
    j = low;
    for (i = low + 1; i < = high; i++) {
        if (S[i] < pivotitem) {
            j++;
            S[i]와 S[j] 교체;
        }
    }
    pivotpoint = j;
    S[low]와 S[pivotpoint] 교체
}
~~~
quicksort의 최악 시간복잡도는 $O(n^2)$이고 평균 시간복잡도는 $nlogn$이다.
## 2.5 쉬트라센의 행렬곱셈 알고리즘
쉬트라센의 행렬곱셈 방법은 다음과 같다.

$$
\begin{aligned}
m_1 &= (a_{11} + a_{22})(b_{11} + b_{22}) \\
m_2 &= (a_{21} + a_{22})b_{11} \\
m_3 &= a_{11}(b_{12} - b_{22}) \\
m_4 &= a_{22}(b_{21} - b_{11}) \\
m_5 &= (a_{11} + a_{12})b_{22} \\
m_6 &= (a_{21} - a_{11})(b_{11} + b_{12}) \\
m_7 &= (a_{12} - a_{22})(b_{21} + b_{22})
\end{aligned}
$$
$$
C = \begin{bmatrix}
m_1 + m_4 - m_5 + m_7 & m_3 + m_5 \\
m_2 + m_4 & m_1 + m_3 - m_2 + m_6
\end{bmatrix}
$$

이 방법은 4개의 부분행렬로 나눌 수 있는 큰 행렬에도 똑같이 적용할 수 있어서 분할정복이 가능하게 해준다.
기존의 행렬곱셈 알고리즘이 $O(n^3)$의 시간복잡도를 가지고있는데 반해 쉬트라센 알고리즘은 $O(n^{2.81})$의 시간복잡도를 가지고 있다.

행렬곱셈 알고리즘은 최소한 2차시간은 되어야함이 증명돼있지만 2차시간 알고리즘은 아직 개발되지 않았고 2차시간 알고리즘의 개발이 불가능하다는 것도 증명되지 않아 가능한지 알 수 없다.
## 2.6 큰 정수 계산법
정수를 표현하는 저장공간의 용량을 초과하는 정수 연산을 해야한다면, 소프트웨어로 정수를 표현하고 처리해야 한다. 분할정복으로 이 문제를 해결해보자.
### 2.6.1 큰 정수 표현: 덧셈과 기타 1차시간 연산
큰 정수를 표현하는 직관적인 방법은 숫자(digit) 배열을 사용하는 것이다. 예를들어 정수 153,127은 S[] = {7, 2, 1, 3, 5, 1}로 표현할 수 있다. bool 변수 하나를 추가로 사용해 음수 양수를 표현할 수도 있다.
~~~cpp
// 큰 정수 배열을 덧셈하는 함수
void largesum(A[], B[]) {
    배열 C[];
    int a;
    index i=1, j=1, k=1;
    while (i<=A[]의 크기 && j<=B[]의 크기) {
        C[k] = (A[i] + B[j] + a) / 10; // 더하고 올림수 a에.
        a = (A[i] + B[j] + a) % 10;
        i++;j++;k++;
    }
    남은 친구들 C[]에.
}
~~~
### 2.6.2 큰 정수 곱셈
큰 수의 곱셈을 배열로 직관적으로 구현하면 2차시간, 즉 $O(n^2)$의 시간복잡도를 가진다.  
분할정복을 사용하면 2차시간보다 빠른 알고리즘을 만들 수 있다.

$567,832 = 567 \times 10^3 + 832$\
$9,423,723 = 9423 \times 10^3 + 723$

이렇게 곱셈하기 충분히 쉬운 수까지 분할하여 계산하면 더 빠르게 연산할 수 있다.  
수식으로 표현하면  정수 u와 정수 v를 곱하는 경우.  

$u = x \times 10^m + y$\
$v = w \times 10^m + z$

$uv = xw \times 10^{2m} + (xz + wy) \times 10^m + yz$  
위과 같이 네번의 곱셈과 1차시간 연산으로 구할 수 있다. $xy, xz, wy, yz$는 재귀를 통해 다시 분할 하거나 임계점보다 작은 정수라면 단순 곱셈으로 처리한다.
~~~cpp
large_integer prod (large_integer u, large_integer v) {
    large integer x, y, w, z
    int n, m

    n = max(u의 자리수, v의 자리수);
    if (u == 0 || v == 0)
        return 0;
    else if ( n <= threshold) // 임계점보다 작을 시
        return u X v; // 단순 곱셈
    else {
        m = 내림(n/2);
        x = u divide 10^m, y = u % 10^m;
        w = v divide 10^m, z = v % 10^m;
        return prod(x, w) X 10^2m + (prod(x, z) + prod(w, y)) X 10^m + prod(y, z);
    }
}
~~~
하지만 이 알고리즘은 여전히 $O(n^2)$의 시간복잡도를 가지고 있다. 자리수가 반 정도인 정수로 네번의 곱셈을 수행해야하는데, 이 곱셈 횟수를 줄인다면 2차시간보다 좋은 알고리즘을 얻을 수 있다.  
함수 prod는 재귀로 prod를 네번 호출하여
$$
\begin{aligned}
xw, xz+yw, yz
\end{aligned}
$$
위와 같은 값을 얻는다. 이 대신
$$
\begin{aligned}
(x+y)(w+z) = xw+(xz+yw)+yz
\end{aligned}
$$
라는 점에서 착안하여
$$
\begin{aligned}
(x+y)(w+z),\ xw,\ yz
\end{aligned}
$$
위 처럼 세번만 곱셈해도 (재귀호출 해도)
$$
\begin{aligned}
&xw, yz \\
&(x+y)(w+z) - xw - yz = (xz+yw)
\end{aligned}
$$
이렇게 필요한 값을 모두 얻을 수 있다는 것을 확인할 수 있다.
~~~cpp
large_integer prod2 (large_integer u, large_integer v)
{
    large_integer x, y, w, z, r, p, q;
    int n, m;

    n = max(u의 자리수, v의 자리수);
    if (u == 0 || v == 0)
        return 0;
    else if (n <= threshold)
        return u X v; // 단순 곱셈
    else {
        m = 내림(n/2);
        x = u divide 10^m, y = u % 10^m;
        w = v divide 10^m, z = v % 10^m;
        r = prod2 (x + y, w + z);
        p = prod2 (x, w);
        q = prod2 (y, z);
        return p X 10^2m + (r-p-q) X 10^m + q;
    }
}
~~~
이 경우엔 $O(n^{log_23}) \approx O(n^{1.58})$의 시간복잡도를 같는다.
## 2.7 분할정복법을 사용할 수 없는 경우
가능하다면, 다음 두 경우에는 분할정복법을 피해야 한다.
~~~
1. 크기 n인 사례가 n에 가까운 크기의 두 개 이상의 사례로 분할된다.
2. 크기 n인 사례가 n/x 크기의 거의 n개 사례로 분할된다.
~~~