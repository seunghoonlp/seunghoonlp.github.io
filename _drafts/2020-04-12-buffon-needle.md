---
layout: post
title: "뷔퐁의 바늘 문제 (Buffon's Needle Problem)"
---

**뷔퐁의 바늘 문제(Buffon's Needle Problem)** 는 18세기 프랑스의 수학자 뷔퐁(Georges-Louis Leclerc, Comte de Buffon)이 제시한 문제이며, 내용은 다음과 같다:

> 평행한 나무 판자로 이루어진 마루바닥에 바늘을 떨어뜨릴 때, 바늘이 두 나무 판자에 걸칠 확률은 얼마인가?

결론부터 말하자면 정답은 $$\frac{2}{\pi}\frac{l}{t}$$이다. 이때, $$l$$은 바늘의 길이, $$t$$는 나무 판자의 너비이다.

여기서 눈여겨 볼 점은 바로 $$\pi$$이다. $$\pi$$와는 도무지 인연이 없어보이는 문제에서 어떻게 이런 값이 나올 수 있을까?

이 문제는 기하적 확률을 사용한 최초의 문제로 일컬어지며, 약간의 기하학과 정적분으로 쉽게 해결할 수 있다. 실제로 구과정 수학의 정석 실력편에는 이 문제가 연습문제로 실리기도 했다. 

### 풀이
앞서 언급되었듯, 바늘의 길이를 $$l$$, 나무 판자의 너비를 $$t$$라고 하자. 문제를 최대한 단순하게 만들어본다면, 결국 바늘을 던지는 행위는 임의의 점 $$(x, y)$$를 잡고, 각 $$\theta$$ 방향으로 길이 $$l$$만큼 뻗어나가는 선분을 그리는 행위라고 볼 수 있다. 또한 $$\theta$$의 범위는 $$0 \le \theta \le \pi$$로 보아도 무방하다. 왜냐하면 $$\theta > \pi$$인 선분은 평행이동 시키면 조금 아랫쪽에서 $$2\pi - \theta$$ 방향으로 그은 선분과 동일하기 때문이다.

그림으로 문제의 상황을 나타내본다면 다음과 같다.

![뷔퐁의 바늘 문제 모식도](/assets/images/buffon_needle/buffon.jpg)

바늘이 두 나무 판자에 걸치는 것은 어떻게 판별할 수 있을까? $$y + l\sin\theta \ge t$$이면 바늘이 두 나무 판자에 걸친다고 볼 수 있다.

그렇다면 확률을 계산해보자. 확률에 영향을 미치는 변수는 $$y$$와 $$\theta$$이다. 위에서 구한 $$y + l\sin\theta \ge t$$을 이항하면 $$l\sin \theta \ge t - y$$이고, $$t-y$$를 새로운 변수 $$y'$$로 두자. 여기서 $$y'$$의 범위는 $$0 \le y' \le t$$이며, $$\theta$$의 범위는 $$0 \le \theta \le \pi$$이다. 

$$y'$$, $$\theta$$를 두 축으로 하는 좌표평면을 표본공간으로 두면, 표본공간의 전체 넓이는 $$t\pi$$이며, 사건에 해당하는 영역은 $$l\sin\theta \ge y'$$이므로 $$y' = l\sin\theta$$의 그래프와 $$\theta$$축이 이루는 도형에 해당된다.

그래프로 현재 상황을 나타내보자.

![뷔퐁의 바늘 문제 그래프](/assets/images/buffon_needle/buffon_graph.jpg)

따라서 확률은 (사건의 넓이) / (표본공간의 넓이)로 계산할 수 있다. 

$$
p = \frac{\int_0^\pi l\sin\theta\,  d\theta}{t\pi} = \frac{l\left[-\cos \theta \right]_0^\pi}{t\pi} = \frac{2l}{t\pi}
$$

앞서 설명했던 결과와 일치한다! 

### C++과 Python을 이용한 시뮬레이션
Python과 C++로 짜보았는데, 어째서인지 C++ 코드가 Python보다 느리다. 아마 C++에서 지원하는 실수 난수 생성기에서 느려진 것 같은데 좀 더 빠른 방법이 없는 지 알아봐야할 듯...

Python 코드에선 `matplotlib`로 그래프도 그려보았다. 시행 횟수가 증가할 수록 추정치가 $$\pi$$값에 수렴하는 모습을 볼 수 있다.

![뷔퐁의 바늘 문제 시뮬레이션](/assets/images/buffon_needle/buffon_result.png)

#### Python
```python
import math
import random
import matplotlib.pyplot as plt

print("Enter the value: Number of the needle | Length of the needle | Width of the lines")
needle_n, needle_l, line_w = input().split()

needle_n = int(needle_n)
needle_l = float(needle_l)
line_w = float(line_w)

intsec = 0
pltdata = []

for i in range(1, needle_n + 1):
    t_y = random.uniform(0.0, line_w)
    t_theta = random.uniform(0.0, math.pi / 2.0)
    if (t_y + (needle_l * math.sin(t_theta)) >= line_w):
        intsec += 1
    pltdata.append((i / intsec) * 2.0 * needle_l / line_w if intsec else 0)
    
plt.plot(range(1, needle_n + 1), pltdata, color = 'g')
plt.xlabel('Attempt')
plt.ylabel('Estimated Value')

plt.axhline(math.pi, color = 'r', label = 'pi')
plt.legend()
plt.show()
```

#### C++
```cpp
#define _USE_MATH_DEFINES
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

random_device rd;

double f_rand(double src, double dst)
{
    mt19937 mt(rd());
    uniform_real_distribution<double> dis(src, dst);
    return dis(mt);
}

double buffon_pi(ll needle_n, double needle_l, double line_w)
{
    ll intsec = 0LL;
    for (ll i = 0LL; i < needle_n; ++i) {
        double t_y = f_rand(0.0, line_w);
        double t_theta = f_rand(0.0, M_PI_2);

        if (t_y + (needle_l * sin(t_theta)) >= line_w) intsec++;
    }

    double res = ((double)needle_n / intsec) * 2.0 * needle_l / line_w;

    return intsec ? res : 0;
}

int main()
{
    ll needle_n;
    double needle_l, line_w;

    cout << "Enter the value: Number of the needle | Length of the needle | Width of the lines" << endl;
    cin >> needle_n >> needle_l >> line_w;
    cout << setprecision(16) << buffon_pi(needle_n, needle_l, line_w) << endl;

    return 0;    
}
```