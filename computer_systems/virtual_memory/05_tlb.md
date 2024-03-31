# Translation Lookup Buffer

Address translation도 page table을 보기 위해 memory access가 필요하니,
caching으로 performance upgrade를 노릴 수 있다.
TLB는 address translation에 대한 cache이다.

## TLB Reach

TLB Reach = TLB Size $\times$ Page Size, which is ideally equal to working set.
만약 TLB reach가 working set보다 작다면 miss rate이 급증할 것이다.

그런데, video같은 큰 data를 memory에 올린다면 working set이 훨씬 클 것이 자명.
이럴 경우 TLB entry가 page가 아닌 superpage를 mapping하게 하여 TLB reach를 키울 수 있다.

## Tagged TLB

Context switch가 일어나면 TLB는 쓸모가 없어진다.
그래서 context switch가 일어나면 TLB를 invalid로 mark하자니 향후 재사용 가능성이 존재해서 애매하다.
따라서 각 TLB entry가 PID를 tag로 저장하게 해서, PID가 일치할 때만 TLB hit.

## TLB Shootdown

Page fault 등으로 page table entry가 변경되었을 때 TLB는 더 이상 유효하지 않기에 날려줘야 한다.
한편 multicore에서는 모든 Core의 TLB를 날려줘야 하기 때문에 synchronous하게 처리할 수 없다.
따라서 interrupt를 발생시키고 모든 core의 TLB를 날리는 과정을 거쳐 return한다.
이를 TLB shootdown이라고 한다.

## Virtually Addressed Cache

조금 더 optimization을 한다면, L1 cache를 virtually addressed cache로 만들면 cache hit이면 TLB lookup 없이 바로 data를 쓸 수 있다.
이와 동시에 cache miss를 가정하고 병렬적으로 TLB access한다.
