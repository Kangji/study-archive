# Replacement Policy

N-way set associative cache에서 새로운 data block을 caching할 때 기존에 캐싱된 block 중 어느 것을 없앨 것인가? Random? FIFO?

Replacement Policy의 목표는 miss rate을 낮추는 것.

## FIFO

FIFO의 문제점: n-way set associative인데 n+1개의 data block을 repeated scan(A -> B -> C -> A -> B -> ...)하면 set이 꽉 찬 후 항상 cache miss가 난다.

## MIN: Best Solution

가장 오랫동안 사용하지 않<u>**을**</u> block을 replace하는 방법.
그러나 이론적인 policy일 뿐 실제 상황에서는 미래를 예측할 수 없고, computation cost가 크다.

## LRU: Approximation

LRU(Least Recently Used)는 가장 오랫동안 사용하지 않<u>**은**</u> block을 replace한다.

### Problem 1: Repeated Scan

FIFO와 마찬가지로 set size보다 block 수가 더 많은 경우의 repeated scan을 잘 handling하지 못한다.

### Solution 1: MRU

MRU(Most Recently Used)는 LRU와 반대 방식이며 repeated scan에 최적화되어 있다.

### Problem 2: Frequency

LRU는 frequency를 고려하지 않는다. 자주 접근하는 block을 미래에도 더 자주 접근할 가능성이 높다.

### Solution 2: LFU

LFU(Least Frequently Used)는 frequency를 tracking하여 빈도가 낮은 block을 replace.
하지만 MIN과 마찬가지로 computation cost가 크다.

### Solution 2-1: Approximated LFU

Frequency를 전부 tracking하지 않고, hot page와 cold page로만 나누어서 관리하는 방법.

* LRU-k: 각 block의 k-th recent access들을 비교하여 가장 오래된 block을 replace
* LRU-2Q: Am queue(hot)와 A1 queue(cold)로 나누어서 관리하며, 첫 access는 A1 queue로, A1 queue에 있는 block에 access할 경우 Am queue로 이동시킨다.
Am queue는 LRU, A1 queue는 FIFO를 따름.
