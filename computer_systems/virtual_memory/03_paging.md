# Page Translation

Page translation은 virtual page number를 physical page number로 변환시키는 과정.

* Virtual address = \[virtual page number(vpn), page offset\]
* Physical address = \[physical page number(ppn), page offset\]

예를 들어, m-bit virtual address, n bit physical address, k-bit page offset일 때,

* page size: $2^k$
* virtual page: $2^{(m-k)}$개
* physical page: $2^{(n-k)}$개
* vpn = va / pg_sz
* offset = va % pg_sz
* ppn = page_table\[vpn\]
* pa = ppn * pg_sz + offset = page_table\[va/pg_sz\]*pg_sz + va%pg_sz

## Page Table

Segment table같은 virtual page와 physical frame의 mapping table.
각 process마다 page table이 존재한다.

### Page Table Location

Page table은 physical memory에 저장되며,
page table base register(PTBR)이 현재 process의 page table의 base를 가리킨다.
Page table은 per-process object이므로 각 base address는 PCB에 저장되며,
context switch시 PTBR의 값을 변경한다.

### Page Protection

Page Table은 mapping 이외에도 protection 등 additional metadata를 가진다.

* Present bit(x86: PTE_P): page가 가리키는 physical memory에 present한가 (mapping이 유효한가)
* Read/Write bits(x86: PTE_W): Read/Write accessibility
* User bit(x86: PTE_U): User mode accessibility

## Fill on Demand

Physical frame을 처음부터 할당하지 않고, 필요할 때 할당한다.
예를 들어, 프로그램을 실행할 때 binary를 memory에 모두 올리지 않고,
최초 실행할 때 page fault를 발생시키고 해당 page를 memory에 올린다.

더 최적화할 경우, 조만간 사용할 것으로 예상되는 page를 asynchronous하게 할당할 수 있다.

## Paging Issue

### Page Table Size Issue

Page size가 크면 page table의 크기는 줄어들지만 (# of pages = VM size / page size),
(internal) fragmentation이 증가하고 finer-grained control이 불가하다.

반면 page size가 작으면 page table의 크기가 증가해서 resource overhead가 증가한다.
예를 들어, 32bit address space에서 page size가 4KB, page table entry가 4byte일 때,
process별로 (4GB / 4KB) * 4byte = 4MB의 page table을 가진다.
뿐만 아니라 page table도 결국 physical page에 저장되는데 page table size가 page size보다 크면 여러 page를 사용하게 되어 문제가 발생한다.

### Translation Issue

각 memory access가 page table 때문에 실제로는 두 번씩 access가 발생한다.
