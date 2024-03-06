# Multilevel Cache

Main memory를 caching하듯이 cache를 caching하면 어떨까? 해서 나온 접근법이 multilevel cache이다.
Level에 따라 L1, L2, L3 Cache로 부른다.

Multilevel cache의 목표는 miss penalty를 줄이는 것.

## L1 Cache

일반적으로 L1 Cache는 CPU core 내 Register와 가장 가까운 위치에 있으며 Intel i7 core의 경우 instruction cache와 data cache를 분리시켜 놓은 것이 특징이다.

## L2 Cache

L2 Cache도 여전히 CPU core 내에 존재한다.

## L3 Cache

L3 Cache부터는 Core가 아닌 processor 상에 존재하며 여러 core들이 공유한다.
