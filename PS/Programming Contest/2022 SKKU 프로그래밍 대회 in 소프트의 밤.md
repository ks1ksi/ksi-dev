![[KakaoTalk_Photo_2022-11-02-00-46-54.png]]

2022년 10월 27일에 진행한 SKKU 프로그래밍 대회.
하나정도는 더 풀어볼만 했는데 막판에 집중력을 잃고 4솔..
그래도 올해 초에 참가했던 대회보다는 한 문제 더 풀었다.
내년에는 5솔 이상 하고 10등 안에 들 수 있지 않을까?

## A 안녕 클레오파트라 세상에서 제일가는 포테이토칩
[문제 링크](https://www.acmicpc.net/problem/25904)

내야 하는 목소리 $X$를 1씩 늘리면서 확인해보면 된다. $N$번 사람까지 도달한 경우 1번 사람부터 다시 진행할 수 있도록 인덱스를 바꿔준다.

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    int n, x;
    cin >> n >> x;
    vector<int> v(n);
    for (auto& num : v) {
        cin >> num;
    }

    int cur = 0;
    while (1) {
        if (v[cur] < x) {
            cout << cur+1 << '\n';
            break;
        }
        cur++;
        x++;
        if (cur == n) cur = 0;
    }

    return 0;
}
```

## B 장인은 도구를 탓하지 않는다
[문제 링크](https://www.acmicpc.net/problem/25905)

망치 9개를 사용하면 되고, 곱셈 연산이므로 순서에 관계없이 강화 확률이 가장 낮은 망치를 제외한 나머지 망치 9개를 선택하면 된다. 

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;
using ld = long double;

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    cout << fixed << setprecision(7);

    vector<ld> v(10);
    for (auto& x : v) {
        cin >> x;
    }
    sort(v.begin(), v.end());

    ld ans = 1e9;
    for (int i = 1; i < 10; i++) {
        ans = ans * v[i] / i;
    }

    cout << ans << '\n';

    return 0;
}
```

## C 수렵의 시간이다!
[문제 링크](https://www.acmicpc.net/problem/25906)

현재 가진 돈으로 착용할 수 있는 모든 방어구의 조합을 시도한다. 이 때 ***방어구를 착용하지 못하는 경우***도 고려해야 한다. 입력받은 방어구에 아무런 효과도 없고 가격도 0인 더미 방어구 한개씩을 추가해주면 편하게 구현할 수 있다. 
만약 강화까지 가능하다면, 현재 착용한 방어구에 대해 전부 시도해본다. 스킬 레벨 증가량을 올릴 스킬과 내릴 스킬도 전부 시도해본다.
실수할 여지가 굉장히 많은 문제다.

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

int t, k;
vector<vector<int>> skills(5);
vector<vector<pair<int, vector<int>>>> items(3);

int calculate(int money, pair<int, vector<int>> &item1, pair<int, vector<int>> &item2, pair<int, vector<int>> &item3) {
    int tc = item1.first + item2.first + item3.first;
    if (tc > money) return -1;

    int ret = 0;
    vector<int>lv(5);
    for (int i = 0; i < 5; i++) {
        lv[i] = item1.second[i] + item2.second[i] + item3.second[i];
        ret += skills[i][min((int)skills[i].size()-1, lv[i])];
    }

    if (money - k >= tc) {
        for (int i = 0; i < 5; i++) { // down
            if (!lv[i]) continue;
            lv[i]--;
            for (int j = 0; j < 5; j++) { // up
                if (i == j) continue;
                lv[j]++;
                int tmp = 0;
                for (int k = 0; k < 5; k++) {
                    tmp += skills[k][min((int)skills[k].size()-1, lv[k])];
                }
                ret = max(ret, tmp);
                lv[j]--;
            }
            lv[i]++;
        }
    }

    return ret;
}

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    cin >> t >> k;

    for (auto& v : skills) {
        int sz;
        cin >> sz;
        v.push_back(0);
        for (int i = 0; i < sz; i++) {
            int x;
            cin >> x;
            v.push_back(x);
        }
    }
    
    for (auto& v : items) {
        int sz;
        cin >> sz;
        v.resize(sz+1);

        v[0].first = 0;
        for (int i = 0; i < 5; i++) {
            v[0].second.push_back(0);
        }

        for (int i = 1; i <= sz; i++) {
            cin >> v[i].first;
        }

        for (int i = 1; i <= sz; i++) {
            for (int j = 0; j < 5; j++) {
                int x;
                cin >> x;
                v[i].second.push_back(x);
            }
        }
    }

    int ans = 0;

    for (int i = 0; i < items[0].size(); i++) {
        for (int j = 0; j < items[1].size(); j++) {
            for (int k = 0; k < items[2].size(); k++) {
                ans = max(ans, calculate(t, items[0][i], items[1][j], items[2][k]));
            }
        }
    }

    cout << ans << '\n';

    return 0;
}
```

## D 양과 늑대
[문제 링크](https://www.acmicpc.net/problem/25907)

첫 번째 날에는 항상 양이 도착하고, 마지막 날에는 항상 늑대의 수가 양의 수보다 많고, 하루에 늑대 혹은 양이 한 마리씩 도착하기 때문에 1번째 날부터 $N$번째 날중 적어도 하루 이상 늑대와 양의 수가 같아진다. 
- $lo <= hi$이고,  $mid = (lo+hi)/2$라 하자. 
- $mid$번째 날에 양과 늑대의 수가 같다면 $mid$번째 날이 정답이다.
- $mid$번쨰 날에 양이 더 많다면, $mid+1$번째 날과 $hi$번째 날 사이에 정답이 존재한다.
- $mid$번째 날에 늑대가 더 많다면 $lo$번째 날과 $mid-1$번째 날 사이에 정답이 존재한다.
인터랙티브 문제라 출력 이후 버퍼를 `flush` 해야 한다.

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    int n;
    cin >> n;

    int lo = 1, hi = n, cnt;
    while (lo <= hi) {
        int mid = (lo + hi) / 2;
        cout << "? " << mid << endl;
        cin >> cnt;

        if (cnt*2 > mid) lo = mid + 1;
        else if (cnt*2 < mid) hi = mid - 1;
        else {
            cout << "! " << mid << endl;
            break;
        }
    }
    
    return 0;
}
```

나머지 문제도 추후 업데이트 예정.