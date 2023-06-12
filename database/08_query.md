# Query Processing

## Query Processing Steps

1. Parsing(Parser) & Translation(Translator)
    * translate into IR, such as relational algebra
    * verify syntax
2. Optimization(Optimizer)
    * more than one way to evaluate query
    * choose optimal query plan
3. Evaluation(Execution Engine)
    * executes the query plan and return result

## Query Plan

* Evaluation Primitive: relational algebra expresion annotated with instructions on how to evaluate it
* Query plan: sequence of evaluation primitive to evaluate query
* More than one way to evaluate

## Query Optimization

* Equivalence: 임의의 relation instance에 대해여 두 query evaluation result가 같음
* cost-based optimization: estimate cost

## Query Cost

* \# of block transfer => $t_{\tau}$ = block transfer time
* \# of seeks => $t_s$ = block seek time (seek + rotational delay)

## Select Operation

* File Scan (a.k.a. sequential scan)
* Index Scan

### Linear Search

Full scan: $b_r\times t_\tau+1\times t_s$

Equality on Key: $\frac{b_r}{2}\times t_\tau+1\times t_s$
(찾는데 평균 파일의 절반 search)

* 한 file에 $b_r$개의 block
* 한 file의 block은 가까이 위치 => seek 1번만

### Primary Index, Equality on Key

$(h_i + 1) \times (t_\tau + t_s)$

* tree 높이 $h_i$ => index block $h_i$개 seek + transfer
* single record => data block 1개 seek + transfer

### Primary Index, Equality on NonKey

$h_i \times (t_\tau + t_s) + b \times t_\tau + 1\times t_s$

* index block은 동일
* multiple record => data block b개 transfer, 그러나 seek는 1번만

### Secondary Index, Equality on Key

primary index, equality on key와 동일

### Secondary Index, Equality on NonKey

$(h_i + n) \times (t_\tau + t_s)$

* index block은 동일
* record n개 => data block n개 각각 seek + transfer
* n은 보통 매우 큼 => expensive

### Primary Index, Comparison

* GT, GE => index로 first tuple 찾고 sequential scan
* LT, LE => index 없이 첫 tuple부터 scan

### Secondary Index, Comparison

Linear search is often better

## External Sort Merge

파일 size가 main memory보다 클 때 merge sort하는 방법.
Memory가 M개의 block이 fit한다고 하자.

1. file을 block M개씩 memory에 올려서 sort => 각각을 **run**이라고 부름
2. N개의 run을 최대 (M-1)개씩 나눠서 merge sort
    * M-1칸에 각 run에서 block 1개씩 올려서 (M-1) way merge
    * 1칸은 merge output 쓸 칸
    * Block 다 썼으면 해당 run에서 다음 block 올려서 사용
    * Output block 꽉찼으면 flush하고 이어서 write
    * 결과: M-1개 run이 1개의 run으로 합쳐짐
3. 2번을 총 $\lceil log_{M-1}(N)\rceil$ 번 반복해야 전체가 sort됨
    * 2번 과정을 **merge pass**라고 함

### Cost Analysis

* M = \# of blocks in buffer
* $b_r$ = \# of blocks in file
* run 개수 $N = \lceil\frac{b_r}{M}\rceil$
* merge path 수 $P = \lceil log_{M-1}N\rceil$
* Transfer
  * run generation, merge path마다 $b_r$ blocks read & write => $2b_r$ block transfers
  * final merge path에서 write transfer는 없음 (disk에 쓸 이유가 없어서)
  * total \# transfer = $b_r \times (2P + 1)$
* Seek
  * run generation에서는 run마다 1 read seek & 1 write seek => 2N seek
  * $b_b$ = 각 run마다 평균적으로 사용하는 buffer block의 수
  * 한 번의 seek에서 $b_b$개의 block을 read/write
  * 각 merge path마다 $2\lceil\frac{b_r}{b_b}\rceil$ 번의 seek
  * final merge path에서 write seek는 없음
  * total \# seek = 2N + $\lceil\frac{b_r}{b_b}\rceil(2P - 1)$

## Join Operation

여러 join algorithm이 있다.

Running Example:

* r has 5000 records in 100 blocks
* s has 10000 records in 400 blocks

### Nested Loop Join

모든 record pair에 대한 condition test를 진행 (for문이 record 단위)

Worst case: buffer size = 3 block (1 r, 1 s, 1 output)

1. r의 block을 1개 read => $b_r$ blocks 각각 1 transfer + 1 seek
2. r의 $n_r$ records 각각 s 전체를 read => 1 seek + $b_s$ blocks 각각 1 transfer

* \# Transfer : $b_r \times 1 + n_r \times b_s$
* \# Seek: $b_r \times 1 + n_r \times 1$

Best case: buffer size is enough => 그냥 두 file 다 읽으면 됨
=> \# Transfer : $b_r + b_s$, \# Seek : 2

### Block Nested Loop Join

for문 순서를 조금 변경 => block 단위가 먼저 outer loop로

Worst case: buffer size = 3 block (1 r, 1 s, 1 output)

1. r의 block 1개 read => $b_r$ blocks 각각 1 transfer + 1 seek
2. r의 block 각각 s 전체를 read => 1 seek + $b_s$ blocks 각각 1 transfer
3. 두 block 사이에서 가능한 모든 pair test

* \# Transfer : $b_r \times 1 + b_r \times b_s$
* \# Seek : $b_r \times 1 + b_r \times 1$

Buffer size = M blocks => (M-2 r, 1 s, 1 output)

* r의 block 한 번에 (M-2)개 read
* r read 횟수 $b_r$번이 아닌 N = $\lceil\frac{b_r}{M-2}\rceil$번
* r seek, s read 횟수 감소 ($b_r$ => N)
* \# Transfer : $b_r \times 1 + N \times b_s$
* \# Seek : $N \times 1 + N \times 1$

Best case: 동일

Improvements:

* equi-join on key: 만족하는 pair 찾으면 inner loop stop
* inner loop를 snake 형식으로 iterate => block transfer 횟수 약간 줄일 수 있음

### Hash Join

1. Join attribute를 hash해서 r과 s를 각각 n개의 partition으로 나눈다.
    * partition할 때 partition 1개 당 output buffer block 최소 1개 필요 ($b_b$)
2. partition 번호가 같은 것끼리 nested loop join
    1. 다른 hash function으로 in memory hash index를 구축
    2. $r_i$의 각 record마다 hash index로 match되는 $s_i$의 record 찾는다.

**Hash Table Overflow**: partition 크기가 buffer size보다 크면 문제됨.

* 해당 번호의 partition을 다른 hash function으로 또다시 partition하는 방법
* 이마저도 duplicate 많으면 소용 없음 => nested loop join으로 fallback

## Discussion

### 15-1. Equivalence

Q. Discuss the implications of changing the underlined expression to ‘any legal instances of the DB’.

A. Semantically equivalent: integrity constraint 때문에 결과가 같은 query도 존재
