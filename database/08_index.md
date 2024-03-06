# Indexing

To speed up access of data

## Index file

* Each entry consists of (search key + pointer)
* Much smaller than original file
* **Search key**: attribute(s) to look up the record

## Index Evaluation Metric

* Access Types Supported
  * Point Query: specific value for search key
  * Range Query: search key value falling in a specified range
* Time
  * Access Time
  * Insertion Time
  * Deletion Time
* Space Overhead

## Ordered Index

Search keys are stored in sorted order.
In this document we don't cover hash index.

* **Primary Index / Clustering Index**
  * File에서 search key order가 sequential order와 같음
  * Index Sequential File: primary index에 따라 정렬된 file
* **Secondary Index / Non-Clustering Index**
  * File에서 search key order가 file의 sequential order와 다름

## Dense Index

모든 search key value에 대한 index record를 포함하는 index

## Sparse Index

몇몇 search key value에 대한 index record만 포함하는 index

* Record가 search key value에 대해 정렬되어 있어야 함 => primary index
* dense index보다 느림

## Multilevel Index

* Index가 너무 커서 block에 다 안들어가면 multi level로 쪼갬
* Each non-leaf index is a sparse index over child nodes

## B+ Tree Index

Performance depends on height of index tree => need balance

### Node Structure

N slots for pointer, N-1 slots for search key values.

* Root Node
  * At least 2 children
  * Root가 leaf이면 0 ~ n-1 values (0 ~ n-1 pointers)
* Leaf Node
  * Search key value $\lceil\frac{n-1}{2}\rceil$ 개에서 n-1개
  * Pointer $P_i$가 search key value $K_i$에 해당하는 pointer
  * Pointer $P_n$은 next leaf node
* Non-leaf Node
  * Search key value $\lceil\frac{n}{2}\rceil-1$ 개에서 n-1개
  * Pointer는 $\lceil\frac{n}{2}\rceil$ 개에서 n개 (value보다 1개 많음)
  * $P_i$가 가리키는 subtree의 모든 search key value는 $K_{i-1} \le v \lt K_i$

### Observation

* 최대 높이 = $\lceil log_{\lceil\frac{n}{2}\rceil}(K)\rceil$
* 보통 node 1개 = block 1개 => block끼리 physically 인접해있을 필요 없음

### B+ Tree Search

* Tree 높이만큼의 index block + (1+) data block scan해야 함 (range query)

### B+ Tree Insert

1. Insert할 leaf node 찾기
2. Search key가 없으면 node에 search key 추가
3. 만약 leaf node가 full이라면 n개의 value를 split해야 함
    1. 기존 node에 $\lceil\frac{n}{2}\rceil$ 개의 value-pointer pair를 남김
    2. 새 node에 나머지를 넣음
    3. 새 node의 가장 작은 search key value를 parent로 올림
4. Parent도 꽉찼으면 n개의 value를 split해야 함
    1. 기존 node에 $\lceil\frac{n-1}{2}\rceil$ 개의 value를 남김
    2. 당연히 그 다음 value는 위로 올라가야 함 (value만)
    3. 나머지 value와 pointer를 새 node에 넣음
5. split 필요 없을 때까지, 혹은 root가 split될 때까지 반복
6. root가 split될 때는 새 root도 만들어줘야 함

### B+ Tree Delete

1. Delete할 leaf node 찾기
2. 만약 leaf node에 key가 너무 적으면 merge and redistribute
    1. 일단 merge sibling (sibling 선택 기준 없음)
        1. 만약 merge했을 때 key가 n개 이상이면 split (redistribute)
        2. 홀수개면 왼쪽에 하나 더
        3. 오른쪽 node의 가장 작은 search key value를 parent에서 update, 끝
    2. parent에서도 두 sibling을 가르던 search key value를 delete해야 함
3. 만약 parent도 key가 너무 적으면 merge and redistribute
4. non-leaf node들은 parent의 search key value까지 포함해서 merge and redistribute.
    1. 일단 merge => L + 1 + R개의 value, (L + 1) + (R + 1)개의 pointer
        1. 만약 merge했을 때 key가 n개 이상이면 split (redistribute)
        2. 짝수개면 왼쪽에 하나 더
        3. 오른쪽 node의 가장 작은 search key value를 parent에서 update, 끝
    2. parent에서도 두 sibling을 가르던 search key value를 delete해야 함
5. merge and redistribute 필요 없을 때까지, 혹은 root가 사라질 때까지 반복

## Discussion

### 14-1. Index

Q. Indices speed query processing, but it is usually a bad idea to create indices on every (combination of) attribute. Explain why.

A. Too much space overhead, search는 빠를지언정 manipulation은 현저히 느려진다. 매번 모든 index를 update해야 하므로.

### 14-2. Dense / Sparse Index

Q. When is it preferable to use a dense index rather than a sparse index?

A. Several cases are:

1. secondary index는 반드시 dense
2. Search Peformance vs. Space
3. Dense는 찾고자 하는 attribute가 search key에 포함될 때 data block에 접근하지 않아도 됨

### 14-3. Primary Index

Q. Is it possible in general to have two clustering indices on the same relation for different search keys?

A. Impossible. Primary index는 반드시 file의 정렬 기준을 search key로 사용해야 함.

### 14-4. B+ Tree, Half Full

Q. Define the meaning of “at least half full” of a B+-tree node.

A. leaf node: half of (n-1), non-leaf node: half of n.

### 14-6. B+ Tree, Height

Consider a tree index of degree 100 on a primary key ID of a table student with 100,000 records.

Q. How many block accesses should we expect for an equality search on ID?

A. Tree 최대 높이 = 3 => 4번 (3 + access data block)

* leaf node 최대 2000개 ( 100000 / ceil((100 - 1) / 2) )
* level 2 node 최대 40개 ( 2000 / ceil(100 / 2) )
* level 3 node 최대 1개 (40 / ceil(100 / 2)) => root

### 14-7. B+ Tree, Height

Consider a (secondary) B+-tree index of degree 100 on the name attribute of student with 100,000 records.

Q. How many block accesses should we expect for an equality search on name?

A. Tree 최대 높이 3 => 3 + n번 (3 + access data block n번)

* candidate key가 아님 => 중복이 있을 수 있다 => 해당하는 record 여러 개일 수 있다
* secondary index => record가 여러 파일에 흩어져 있을 수 있다 => record마다 block access

### 14-10. B+ Tree, Time Complexity

Q. What is the complexity of a B+-tree update? (Insertion and deletion)

A. $O(log_{N}(K))$. node split, merge, redistribute는 O(1)

### 14-11. B+ Tree, Fun

Q. What would the occupancy of each leaf node of a B+-tree be, if index entries are inserted in sorted order? For example, 1, 2, 3, ..., 100, 101, ...

A. $\lceil\frac{n}{2}\rceil$
