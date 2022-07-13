# [Trang chủ](https://ppap-1264589.github.io/interesting-solution)

## Ý nghĩa

Một thuật toán rất khỏe để phân tích một số ra thừa số nguyên tố.

## Cấu trúc dữ liệu và giải thuật: 

-	[Cơ sở thuật toán Pollard Rho](https://cp-algorithms.com/algebra/factorization.html)

-	[Thuật toán Pollard Rho – Tiếng Anh](https://home.cs.colorado.edu/~srirams/courses/csci2824-spr14/pollardsRho.html)

-	[Ứng dụng của “Birthday Paradox” và thuật toán “Rùa và Thỏ” trong bài toán phân tích thừa số nguyên tố](http://www.giaithuatlaptrinh.com/?p=393)

-	[Cách cài đặt biến thể của thuật toán Pollard (thuật toán Brent), được trình bày ở chương 6, hàm rho, blog của Spyofgame](https://codeforces.com/blog/entry/78435)

-	[So sánh về các thuật toán phân tích thừa số nguyên tố](http://www.connellybarnes.com/documents/factoring.pdf)

## Code: [POLLARD.cpp](https://ideone.com/3Dh1ks)

```c++
#include <bits/stdc++.h>
#define up(i,a,b) for (int i = (int)a; i <= (int)b; i++)
#define u64 unsigned long long
#define u128 __uint128_t
#define ep emplace_back
#define all(x) x.begin(), x.end()
using namespace std;

const vector<u64> Milbase = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37};

mt19937_64 RNG(chrono::high_resolution_clock::now().time_since_epoch().count());
u64 UID(u64 l, u64 r){
    uniform_int_distribution<mt19937_64::result_type> random_number(l, r);
    return random_number(RNG);
}

u64 fpow(u64 base, u64 d, u64 MOD){
    u64 res = 1;
    for (; d; d >>= 1, base = (u128)base*base % MOD){
        if (d & 1) res = (u128)res*base % MOD;
    }
    return res;
}

bool divisible(u64 base, u64 cnt2, u64 d, u64 x){
    u64 cur = fpow(base, d, x);
    if (cur == 1 || cur == x-1) return true;
    up(i,1,cnt2-1){
        cur = (u128)cur*cur % x;
        if (cur == x-1) return true;
    }
    return false;
}

bool MillerRabin(u64 x){
    if (x < 2) return false;

    u64 d = x-1;
    int cnt2 = 0;
    while ((d & 1) == 0){
        ++cnt2;
        d >>= 1;
    }

    for (auto base : Milbase){
        if (x == base) return true;
        if (x % base == 0) return false;
        if (!divisible(base, cnt2, d, x)) return false;
    }
    return true;
}

vector<u64> quickfact(u64 n){
    vector<u64> res;
    for (int i = 2; i*i <= n; i++){
        while (n % i == 0){
            res.ep(i);
            n /= i;
        }
    }
    if (n > 1) res.ep(n);
    return res;
}

const int C = 2;
u64 f(u64 x, u64 MOD){
    return ((u128)x*x + C) % MOD;
}

u64 ABS(u64 x, u64 y){
    if (x > y) return x-y;
    return y-x;
}

u64 Brent(u64 n){
    u64 x, y, k, res;
    x = y = UID(2, n-1);
    k = 1;
    while (true){
        up(i,1,k){
            x = f(x, n);
            res = __gcd(ABS(x, y), n);
            if (res > 1) return res;
        }
        y = x;
        k <<= 1;
    }
    assert(false);
}

vector<u64> Pollard(u64 x){
    if (MillerRabin(x)) return {x};
    if (x <= 1e4) return quickfact(x);

    vector<u64> res;

    u64 p = 0;
    p = Brent(x);
    vector<u64> L = Pollard(p);
    vector<u64> R = Pollard(x/p);
    res.insert(res.end(), all(L));
    res.insert(res.end(), all(R));
    return res;
}

void factorize(u64 x){
    vector<u64> factors = Pollard(x);
    map<u64, int> M;

    for (auto val : factors) ++M[val];
    for (auto it : M){
        cout << it.first << "^" << it.second << " ";
    }
    cout << "\n";
}

signed main(){
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    #define Task "A"
    if (fopen(Task".inp", "r")){
        freopen(Task".inp", "r", stdin);
        freopen(Task".out", "w", stdout);
    }

    u64 x;
    while (cin >> x) factorize(x);
}
```

## Chú ý

Cũng giống thuật MillerRabin, không phải trình chấm nào cũng chấp nhận kiểu số __uint128_t và hàm sinh mt19937, ví dụ như các kì thi HSG của Việt Nam

Thuật toán này có thể ăn được một vài bài ICPC một cách khá láo :) 
