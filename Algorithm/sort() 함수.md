# 기본사용법

sort() 함수는 C++의 algorithm헤더에 포함되어 있습니다.

사용 방법은 아래와 같습니다.

```c
#include <iostream>
#include <algorithm>

using namespace std;

int main(void) {
    int a[10] = {9, 3, 5, 4, 1, 10, 8, 6, 7, 2};
    
		// 오름차순 정렬 
    sort(a, a + 10); // ( 정렬 대상, 사이즈)

    for (int i = 0; i < 10; i++) {
        cout << a[i] << ' ';
    }

    return 0;
}
```

# 응용하기 ( compare함수 )

아래의 코드와 같이 compare()를 만들어 세번째 인자값으로 넣게 되면, 
해당 함수의 반환값에 맞게 정렬이 동작합니다.
만약에 compare()의 부등호만 반대로 바꾼다면 내림차순으로 정렬할 수 있습니다.

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

bool compare(int a, int b) {
    return a < b; // 오름차순 정렬 기준
}

int main(void) {
    int a[10] = {9, 3, 5, 4, 1, 10, 8, 6, 7, 2};
    sort(a, a + 10, compare); // compare 함수 기준으로 정렬
    for(int i = 0; i < 10; i++) {
        cout << a[i] << ' ';
    }
}
```

# Vector와 Pair에서의 Sort 함수

`Vector`와 `Pair`는 `배열`과 `클래스`를 대체할 수 있는 문법입니다.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main(void) {
    vector<pair<int, string>> v;
    v.push_back(pair<int, string>(90, "박한울"));
    v.push_back(pair<int, string>(85, "이태일"));
    v.push_back(pair<int, string>(82, "나동빈"));
    v.push_back(pair<int, string>(98, "강종구"));
    v.push_back(pair<int, string>(79, "이상욱"));

    sort(v.begin(), v.end()); // int 기준 오름차순 정렬

    for(int i = 0; i < v.size(); i++) {
        cout << v[i].second << ' ';
    }

    return 0;
}
```

## 예제

- 학생을 나타낼 수 있는 정보가 이름, 성적, 생년월일일 때 학생을 성적 순서대로 나열하고자 한다.
다만 성적이 동일한 경우 나이가 더 어린 학생이 우선순위가 높다

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

bool compare(pair<string, pair<int, int>> a, pair<string, pair<int, int>> b) {
    if(a.second.first == b.second.first) {
        return a.second.second > b.second.second;
    } else {
        return a.second.first > b.second.first;
    }
}

int main(void) {
    vector<pair<string, pair<int, int>>> v;

    v.push_back(pair<string, pair<int, int>>("나동빈", make_pair(90, 19961222)));
    v.push_back(pair<string, pair<int, int>>("이태일", make_pair(97, 19930518)));
    v.push_back(pair<string, pair<int, int>>("박한울", make_pair(95, 19930203)));
    v.push_back(pair<string, pair<int, int>>("이상욱", make_pair(90, 19921207)));
    v.push_back(pair<string, pair<int, int>>("강종구", make_pair(88, 19900302)));

    sort(v.begin(), v.end(), compare);

    for(int i = 0; i < v.size(); i++) {
        cout << v[i].first << ' ';
    }

    return 0;
}

```

# Reference

[https://m.blog.naver.com/ndb796/221227975229?recommendTrackingCode=2](https://m.blog.naver.com/ndb796/221227975229?recommendTrackingCode=2)
