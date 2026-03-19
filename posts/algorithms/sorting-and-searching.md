---
title: Sorting & Searching Classic Problems
date: 2023-03-23 12:10:16
---
<center>

Sorting & Searching Problems

</center>

![](https://i.imgur.com/540iM1C.gif)

Before we begin tackling the problems, it's important for you to know that the process I'm using is not something that can be created quickly. It's the result of many years of hard work and dedication on my part. What sets my solutions apart from others is that I strive to explain things in simple terms and use examples and personal experiences to help you understand the problem. If you are a CodeForces player you can check my [profile](https://codeforces.com/profile/IronByte). So let's get started and enjoy the journey!



# I -  Concert Tickets
You can find out the problem [@CSES](https://cses.fi/problemset/task/1091).
This [problem](https://cses.fi/problemset/task/1091) involves the usage of a Data Structure called *multiset* from the Standard Template Library (STL) in *C++*.   

Before you check the solution, take some time to think about the [problem](https://cses.fi/problemset/task/1091) by yourself. The more you do this, the better you'll become at tackling problems. Make sure to verify the method used to solve the problem to ensure if it matches with yours or if it's another approach, in case you have already solved it.

### The Approach
The goal of the problem is to find the right price for each customer. Each customer has a maximum price they can pay and once they choose a price, this price turns to be not available anymore to other customers. Customers are served in the order they arrive. To solve this, we can use a *multiset* data structure that allows duplicate elements, and automatically sorts them using a binary search tree. This takes time *O(nlog(n))* to insert all tickets. Next, Let's assume a customer *X* has come to get a ticket and he has a specific price *P* that he can't go above it, we should search for the upper-bound ">" of this price *P* in the set of prices, let's call it *Target_Price* and this *Target_Price* should be the one under the *P* price. We can search for that *Target_Price* in a *O(log(n))* time complexity since the whole data structure involves the usage of a binary tree. Next, If that price exists in the set we should chose it and then delete it from the set in a *O(log(n))*. Otherwise we just print -1 means that we didn't find a price that satisfies the needs of the customer *X*. 

When we're done, the total time it takes for the algorithm to run is *Insert = O(nlog(n)) + Search = O(nlog(n)) + Delete = O(nlog(n))* means the time will be *O(nlog(n))*, which is efficient enough to handle values of "1 <= n,m <= 2*10^5". This means that the algorithm should work quickly without any issues of TLE (time limit exceeded).

Great let me give you a picture that will simulate the process that we will be doing. 
![](https://i.imgur.com/rEHBHFR.png)

### C++ Solution
```c++
#include <bits/stdc++.h>
 
/*###############
    Author => IronByte
    Follow me @ironbyte.me
###############*/
 
#pragma GCC target ("avx2")
#pragma GCC optimization ("O3")
#pragma GCC optimization ("unroll-loops")
#define eps 1e-9
#define MOD1 998244353
#define MOD2 1000000007
#define INV_2 499122177
#define INF 1000000000
#define PI 3.14159265358979323846
#define pb push_back
#define all(x) (x).begin(),(x).end()
using namespace std;
typedef long long int ll;


int main() {
    /* 
    + Turning off synchronization and untying the input/output streams,
    To speed up these operations and avoid unnecessary delays. 
    + Gaining some time especially for large input. 
    */
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
    
    /* Reading the number of tickets and the number of customers*/
    int n, m; 
	cin >> n >> m;

    /* 
    + Reading the price of each ticket.
    + inserting the price into a multiset.
    */

    multiset<int> tickets;
	for (int i = 0; i < n; i++) {
        int hi;
		cin >> hi; 
        tickets.insert(hi);
	}

    /* 
    + Iterating throught each of the prices of the customers. 
    + Searching for the upper bound for each price in the set of tickets.
    + If there is an upper bound we chose the one that is exactly under the upper bound.
    + We print the price if it exists and we delete that ticket from the set of tickets.
    + Otherwise, we print -1.
    */
	for (int i = 0; i < m; i++){
        int ti; 
		cin >> ti;
		auto it = tickets.upper_bound(ti);
		if (it == tickets.begin())
			cout << -1 << "\n";
		else {
			cout << *(--it) << "\n";
			tickets.erase(it);
		}
	}
    
    return 0;
}
```
In conclusion, by leveraging the power of *multiset*, we were able to reduce the time complexity of the problem from a naive *O(n^2)* solution to an optimized *O(nlog(n))* solution. This was achieved through the magic of *multiset* working behind the scenes.


# II - Restaurant Customers 
You can find out the problem [@CSES](https://cses.fi/problemset/task/1619).
This [problem](https://cses.fi/problemset/task/1619) is quite interesting since it involves using a technique called *Mapping* and an display that demonstrates the application of a Data Structure known as *map* from the Standard Template Library (STL) in the programming language of *C++*.

Before you check the solution, take some time to think about the [problem](https://cses.fi/problemset/task/1619) by yourself. The more you do this, the better you'll become at tackling problems. Make sure to verify the method used to solve the problem to ensure if it matches with yours or if it's another approach, in case you have already solved it.

### The Approach
To find the highest number of customers at a certain time, we used a technique called mapping. We used a data structure called "map" in C++, which sorts elements by their keys. We gave +1 to customers' arrival times and -1 to their departure times to track the number of customers over time.

The map structure will insert each value into the correct position using binary search, which takes *O(log(n))* time to insert one element into the right position, and to insert *n* elements in our case it will take *O(nlog(n))* time to insert all of them. After we finished mapping the arrival and departure times, we tracked down the highest number of customers or "Peak". We did this by generating two variables: one that increased with each +1 and another that kept track of the highest count = "Peak" observed. 

When we're done, the total time it takes for the algorithm to run is *O(nlog(n))*, which is efficient enough to handle values of "1<= n <= 2*10^5". This means that the algorithm should work quickly without any issues of TLE (time limit exceeded).

Adding a picture can let you understand more what i was talking about. 
![](https://i.imgur.com/tceUgbd.png)

### C++ Solution
```c++
#include <bits/stdc++.h>

/*###############
    Author => IronByte
    Follow me @ironbyte.me
###############*/

#pragma GCC target ("avx2")
#pragma GCC optimization ("O3")
#pragma GCC optimization ("unroll-loops")
#define eps 1e-9
#define MOD1 998244353
#define MOD2 1000000007
#define INV_2 499122177
#define INF 1000000000
#define PI 3.14159265358979323846
#define pb push_back
#define all(x) (x).begin(),(x).end()

using namespace std;
typedef long long int ll;


int main() {

    /*
    Turning off synchronization and untying the input/output streams,
    To speed up these operations and avoid unnecessary delays.
    ++ Gaining some time especially for large input.
    */
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);

    int n;
    cin >> n;

    /* Mapping Process */
    map<int, int> refi;
    for(int i = 0; i < n; i++) {
        int x, y;
        cin >> x >> y;
        refi[x] = 1;
        refi[y] = -1;
    }

    /* Tracking down the Peak */
    int ans = 0;
    int tmp = 0;  
    for(auto u : refi) {
        if (u.second == 1)  
            tmp++;
        else {
            ans = max(ans, tmp);
            tmp = 0;
        }
    }

    /* Answer */
    cout << ans << "\n";
    return 0;

}
```
In conclusion, by leveraging the power of *map*, we were able to reduce the time complexity of the problem to an optimized *O(nlog(n))* solution. This was achieved through the magic of *map* working behind the scenes and the mapping technique.

# III - Sum Of Two Values
You can find out the problem [@CSES](https://cses.fi/problemset/task/1640).
This [problem](https://cses.fi/problemset/task/1640) is quite interesting since it involves using a technique called *Two Pointers*.

Before you check the solution, take some time to think about the [problem](https://cses.fi/problemset/task/1640) by yourself. The more you do this, the better you'll become at tackling problems. Make sure to verify the method used to solve the problem to ensure if it matches with yours or if it's another approach, in case you have already solved it. One thing to mention about this problem, is that you can solve also using the *Mapping* technique, but i will put the light upon the *Two Pointers Technique* for this one. 


### The Approach
When encountering a problem, it's often uncomplicated and easy to grasp the objective at hand. Initially, one might attempt to resolve the issue by selecting one element and sequentially moving through the ones in front until reaching the target value, resulting in a time complexity of *O(N^2)*. However, we can optimize this to *O(N)* by utilizing the two pointers technique. To begin, we must sort the array and establish two pointers, one at the starting position (0) named *"left"* noted as *l*, and the other at the final position (n - 1) named *"right"* noted as *r*. These pointers will traverse the array until locating the target value. They are literally our heros. 

Here's the mechanisms of how this heros will move along the array:
* if (arr[l] + arr[r] == x) -> show the positions and quit. 
* if (arr[l] + arr[r] > x) -> decrement *r* by 1 (*r--*). 
* if (arr[l] + arr[r] < x) -> increment *l* by 1 (*l++*). 
* You will do the three previous mechanisms as long as the left and the right pointers didn't intersect means (l < r). 

It's important to note that once the array is sorted, the original positions of the elements are no longer preserved. Therefore, we need to associate each element with its respective index beforehand. By doing so, once the array is sorted, we can still access the original indices linked to their corresponding elements. Greddy right?

Come on take a look at this picture it will help you understand more the Approach: 
![](https://i.imgur.com/WvXDQ75.png)

### C++ Solution
```C++
#include <bits/stdc++.h>
 
/*###############
    Author => IronByte
    Follow me @ironbyte.me
###############*/
 
#pragma GCC target ("avx2")
#pragma GCC optimization ("O3")
#pragma GCC optimization ("unroll-loops")
#define eps 1e-9
#define MOD1 998244353
#define MOD2 1000000007
#define INV_2 499122177
#define INF 1000000000
#define PI 3.14159265358979323846
#define pb push_back
#define all(x) (x).begin(),(x).end()
using namespace std;
typedef long long int ll;


int main() {
    /*
    Turning off synchronization and untying the input/output streams,
    To speed up these operations and avoid unnecessary delays.
    ++ Gaining some time especially for large input.
    */
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);

    /* Reading the size of the array and the target*/
    int n, x;
	cin >> n >> x;

    /* Reading the value and mapping each value with the correct indice*/
    vector<pair<int, int>> values; 
    for(int i = 0 ; i < n; i++) {
        int val; 
        cin >> val; 
        pair<int, int> p;
        /* first will hold the value and second will hold the indice */
        p.first = val; 
        p.second = i + 1; 
        values.push_back(p); 
    }

    /* Sorting the array in term of value (not index) */
    sort(all(values)); 

    /* Initializing the pointers left and right */
    int l = 0, r = n - 1; 
    while(l < r) {
        /* The Mechanisms that i talked about !*/
        if (values.at(r).first + values.at(l).first == x) {
            cout << values.at(l).second << " " << values.at(r).second << "\n";
            return 0;
        } else {
            if (values.at(r).first + values.at(l).first > x)
                r--; 
            else 
                l++;
        }
    }
    
    cout << "IMPOSSIBLE\n"; 
    return 0;
}
```
In conclusion, by leveraging the power of the *Two Pointers* technique, we were able to reduce the time complexity of the problem from *O(n^2)* to an optimized *O(n)* solution. This was achieved through the magic of the *Two Pointers* technique. 

# IV - Sum Of Three Values
You can find out the problem [@CSES](https://cses.fi/problemset/task/1641).
This [problem](https://cses.fi/problemset/task/1641) is similar to the **Sum of Two Values** since it involves using a technique called *Two Pointers*.

Before you check the solution, take some time to think about the [problem](https://cses.fi/problemset/task/1641) by yourself. The more you do this, the better you'll become at tackling problems. Make sure to verify the method used to solve the problem to ensure if it matches with yours or if it's another approach, in case you have already solved it.

### The Approach
The strategy for solving this problem is similar to the previous one **Sum Of Two Values,** with the only difference being the addition of a third pointer that will indicate the position of the third value. By incorporating this third pointer, the time complexity can be reduced from *O(N^3)* (Naive solution) to *O(N^2)*. It's worth noting that problems of this nature are commonly presented in job interviews, despite their straightforward nature. 

You don't need a picture to simulate this one cuz if you already understand the **Sum Of Two Values** you will code this one on your own, meanwhile i will write the code for you. 

### C++ Solution
```c++
#include <bits/stdc++.h>
 
/*###############
    Author => IronByte
    Follow me @ironbyte.me
###############*/
 
#pragma GCC target ("avx2")
#pragma GCC optimization ("O3")
#pragma GCC optimization ("unroll-loops")
#define eps 1e-9
#define MOD1 998244353
#define MOD2 1000000007
#define INV_2 499122177
#define INF 1000000000
#define PI 3.14159265358979323846
#define pb push_back
#define all(x) (x).begin(),(x).end()
using namespace std;
typedef long long int ll;


int main() {
    /*
    Turning off synchronization and untying the input/output streams,
    To speed up these operations and avoid unnecessary delays.
    ++ Gaining some time especially for large input.
    */
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);

    /* Reading the size of the array and the target*/
    int n, x;
	cin >> n >> x;

    /* Reading the value and mapping each value with the correct indice*/
    vector<pair<int, int>> values; 
    for(int i = 0 ; i < n; i++) {
        int val; 
        cin >> val; 
        pair<int, int> p;
        /* first will hold the value and second will hold the indice */
        p.first = val; 
        p.second = i + 1; 
        values.push_back(p); 
    }

    /* Sorting the array in term of value (not index) */
    sort(all(values)); 

   
    for(int i = 0; i < n; i++) {
        /* Calculating each time the target !*/ 
        int target = x - values.at(i).first;
        /* Initializing the pointers left and right */
        int l = 0, r = n - 1; 
        while(l < r) {
            /* The Mechanisms that i talked about !*/
            if (values.at(r).first + values.at(l).first == target && i != l && i != r) {
                cout << values.at(i).second << " " << values.at(l).second << " " << values.at(r).second << "\n";
                return 0;
            } else {
                if (values.at(r).first + values.at(l).first > target)
                    r--; 
                else 
                    l++;
            }
        }
    }
    
    cout << "IMPOSSIBLE\n"; 
    return 0;
}
```

# V - Maximum Subarray Sum I 
You can find out the problem [@CSES](https://cses.fi/problemset/task/1643).
This [problem](https://cses.fi/problemset/task/1643) is quite interesting since it puts light upon an algorithm called *Kadane's Algorithm*.

Before you check the solution, take some time to think about the [problem](https://cses.fi/problemset/task/1643) by yourself. The more you do this, the better you'll become at tackling problems. Make sure to verify the method used to solve the problem to ensure if it matches with yours or if it's another approach, in case you have already solved it.

### The Approach
Initially, a simplistic approach to this problem involves fixing an element, traversing through each element and summing them up while keeping track of the maximum sum of a contiguous subarray. However, this would result in a time complexity of *O(N^2)*. Alternatively, we can implement Kadane's Algorithm, which involves using two variables. The first variable tracks the maximum sum, and the second variable is used to segment the array. Using this method, we can decrease the time complexity to *O(N)*. It's worth noting that the second variable can be imagined as a tool used to slice the array. 

Here's a picture as always that will let you understand more the algorithm: 
![](https://i.imgur.com/WpiMI0A.png)

### C++ Solution
```C++
#include <bits/stdc++.h>
 
/*###############
    Author => IronByte
    Follow me @ironbyte.me
###############*/
 
#pragma GCC target ("avx2")
#pragma GCC optimization ("O3")
#pragma GCC optimization ("unroll-loops")
#define eps 1e-9
#define MOD1 998244353
#define MOD2 1000000007
#define INV_2 499122177
#define INF 1000000000
#define PI 3.14159265358979323846
#define pb push_back
#define all(x) (x).begin(),(x).end()
using namespace std;
typedef long long int ll;

ll MAXLL(ll x , ll y) {
    if ( x >= y )
        return x ;
    else
        return y ;
}

int main() {
      /*
    Turning off synchronization and untying the input/output streams,
    To speed up these operations and avoid unnecessary delays.
    ++ Gaining some time especially for large input.
    */
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);

    /* Reading the size of the array */
    int n; 
    cin >> n; 

    /* Reading the array !*/
    int arr[n + 1]; 
    for(int i = 0; i < n; i++) 
        cin >> arr[i]; 
    
    /* initializing the array !*/
    ll best = INT_MIN;  
    ll tmp = 0;
    for(int i = 0; i < n; i++) {
        tmp = MAXLL(arr[i], tmp + arr[i]); 
        best = MAXLL(best, tmp); 
    }
    
    cout << best << "\n";
    return 0;
}
```
To conclude, Kadane's Algorithm is a highly effective approach that can outperform naive methods for solving similar problems. Its efficiency is almost like magic when compared to simplistic strategies.

# VI - Maximum Subarray Sum II
You can find out the problem [@CSES](https://cses.fi/problemset/task/1644).
This [problem](https://cses.fi/problemset/task/1644) is really interesting since it an inhanced version of the previous problem **Maximum Subarray Sum I** except the fact that we need to search for the maximum sum of a a contiguous subarray with length between a and b. It's not an easy problem but don't worry we got this little by little we will build a solution stick with me cuz i need your focus.  

Before you check the solution, take some time to think about the [problem](https://cses.fi/problemset/task/1644) by yourself. The more you do this, the better you'll become at tackling problems. Make sure to verify the method used to solve the problem to ensure if it matches with yours or if it's another approach, in case you have already solved it.

### The Approach
Perhaps a way to tackle this problem is to first create a simple approach and then focus on optimizing each component separately. For example, a basic approach to solve this problem could involve iterating through the entire array, fixing an element each time, and then iterating through all possible lengths between a and b. The sum of each part of the array between the fixed index i and i + a or i + b can then be calculated. then find the Maximum sum between all of them. 
Here take a look at this pseudo-code:
```c++
Best = -INF; 
/* Loop 1 */
for(int i = 0; i < n; i++) {
    /* Loop 2 */ 
    for(int len = a; len <= b; len++) {
        /* Loop 3 */ 
        sum = 0;
        for(int j = i; j < i + len; j++) {
            sum = sum + arr[j]; 
        }
        best = max(best, sum);
    }
}
```
It is evident that this Approach has a worst-case time complexity of *O(N^3)*, which is highly undesirable. Therefore, I will now begin optimizing the solution by eliminating certain loops. Interestingly, the third loop resembles a fundamental technique known as the prefix sum. By constructing a prefix sum, we can calculate the sum in O(1), as shown in the following example: sum = prefix[i + len] - prefix[i - 1].

anyway assmuing that the prefix sum array is builded the code will turn out like that: 
```c++
Best = -INF; 
/* Loop 1 */
for(int i = 0; i < n; i++) {
    /* Loop 2 */ 
    for(int len = a; len <= b; len++) {
        sum = prefix[i + len] - prefix[i - 1];
        best = max(best, sum);
    }
}
```
The current time complexity has been improved to *O(N^2)*, but there is still room for further optimization. We can achieve this by eliminating loop 2. Upon closer inspection, it becomes apparent that prefix[i-1] remains constant throughout loop 2. This suggests that we can remove it from the loop and simply subtract prefix[i-1] from the max sum. As a result, sum will be equal to prefix[i+len], effectively transforming the problem into one that involves maximizing prefix[i+len] over the range [i+a, i+b+1]. To accomplish this, we can use a set to track the maximum value in the given range each time we slide. By adding prefix[i+b+1] and removing prefix[i+a] at each iteration, we can obtain the final sum, which is equal to Max - prefix[i-1]. Finally, we can track down the maximum sum.

### C++ Solution:
```c++
#include <bits/stdc++.h>
 
/*###############
    Author => IronByte
    Follow me @ironbyte.me
###############*/
 
#pragma GCC target ("avx2")
#pragma GCC optimization ("O3")
#pragma GCC optimization ("unroll-loops")
#define eps 1e-9
#define MOD1 998244353
#define MOD2 1000000007
#define INV_2 499122177
#define INF 1000000000
#define PI 3.14159265358979323846
#define pb push_back
#define all(x) (x).begin(),(x).end()
using namespace std;
typedef long long int ll;

ll MAXLL(ll x , ll y) {
    if ( x >= y )
        return x ;
    else
        return y ;
}


int main() {
      /*
    Turning off synchronization and untying the input/output streams,
    To speed up these operations and avoid unnecessary delays.
    ++ Gaining some time especially for large input.
    */
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);

    /* Reading the size and the the a - b */
    int n, a, b; 
    cin >> n >> a >> b; 

    vector<ll> values(n + 1); 
    for(int i = 1; i <= n; i++)
        cin >> values[i]; 
    
    /* Building the prefix sum array */
    for(int i = 1; i <= n; i++)
        values[i] = values[i] + values[i - 1];
    /* Creating a set that will help us get ride of the loop 2*/
    set<pair<ll,int>> boundaries; 
    /* Initializing the set with the values from a -> b*/
    for(int i = a; i <= b; i++)
        boundaries.insert({values[i], i}); 
    
    ll best = -INF; 
    for(int i = 1; i <= n - a + 1; i++) {
        best = MAXLL(best, boundaries.rbegin()->first - values[i - 1]); 
        boundaries.erase({values[i + a - 1], i+a-1});
        if (i+b <= n) {
            boundaries.insert({values[i+b], i+b});
        }
    }
    cout << best << "\n";
    return 0;
}
```
In conclusion, this solution is a remarkable achievement as it transforms this problem from a time complexity of *O(N^3)* into one that is *O(N)*. It is like turning silver into gold, and it showcases the power of combining different techniques to solve complex problems. This is truly a magical feat.

I hope you loved this series of classic problems, if you had any questions don't hesitate to contact me either on linkedin or on discord @IronByte#0855. See ya in the next journey! 




