# Cache

A safe place for hiding or storing (copy of) things.
자주 사용하는(할 것으로 예상되는) object들(의 copy)을 가깝고 빠른 곳에 보관(캐싱)하여 효율을 올리는 기법.

실제로 메인 메모리는 system bus를 통해 CPU chip과 통신하지만 cache memory는 CPU chip에 내장되어 있어 접근 속도가 훨씬 빠르고 CPU의 자체 clock cycle을 따른다.

**Make Common Case Fast!** 캐시 개념은 메모리 뿐만 아니라 많은 곳에서 유용하게 사용된다.

### Example

* 자주 보는 책은 책상 또는 가방 등 접근성이 높은 곳에 보관한다.
그러나 책상에는 몇 권의 책만 올려놓을 수 있고 많은 책을 보관하기는 힘들다.
* 따라서 가끔 보는 졸업앨범 등은 서재 등에 보관하고 필요할 때만 꺼내서 본다.
접근성은 떨어지지만 접근 빈도 또한 낮아서 괜찮다.

### Hardware Cache Example

* (Physically/Virtually)-Addressed Memory Cache
* Translation Lookup Buffer

이번 글 및 다음 글(optimization)에서는 physically addressed memory cache만을 살펴볼 것이다.

## Terminology

* Block/Line: minimum data management unit
  * Block size: data capacity of a single block
* Hit: requested data found in upper level(cache)
  * Hit time: time to handle request when hit
  * Hit ratio: portion of hit over all requests
* Miss: requested data not found in cache
  * Miss penalty: additional time(access memory & update cache) compared to hit
  * Miss ratio: portion of miss over all requests

## Caching Mechanism

### Read Hit

그냥 캐싱된 데이터를 읽으면 끝.

### Read Miss

Read Miss 발생시 miss penalty 발생:

* memory에 접근해 block을 fetch
* fetched data를 캐싱

## Write Hit

Write operation을 수행 시 cache 뿐만 아니라 memory에도 write가 수행되어야 한다.
언제 memory write를 하는가?

* Write-through: cache와 memory를 모두 update
* Write-back: cache를 update한 후 dirty bit을 set.
나중에 dirty data가 다른 data로 replace될 때 memory에 write

## Write Miss: Allocate on Miss vs. Write Around

Write miss시 memory에 write하고 나서 이를 캐싱할 것인가?

* Allocate on Miss: memory write 후 caching
* Write Around: caching하지 않음

## Cache Location

어떤 데이터가 cache의 어디에 있는지 어떻게 알 수 있을까?

### Indexing

Cache는 데이터의 메모리 주소에 따라 데이터를 캐싱하는 위치를 결정한다.
캐시 용량이 더 작으므로, cache address = memory address % (# blocks * block size)
한편 # blocks, block size 모두 2의 지수이므로, 이는 결국 Memory address의 LSB n자리가 된다. (n = log(# blocks) + log(block size))

### Tag

그러나 여전히 해당 위치에 캐싱할 수 있는 메모리 주소는 여러 개가 있어서 캐싱된 데이터가 실제로 해당 메모리 주소의 데이터인지 확인해야 한다.
해당 위치에 캐싱되는 메모리 주소는 모두 LSB n자리가 같으므로, 나머지 MSB만 확인하면 된다.
이를 Tag라고 하며, 각 cache block마다 캐싱된 데이터와 함깨 tag를 저장한다.

예를 들어 16bit system에서 block size = 8 byte, # blocks = 256이라면 메모리 주소는 앞에서부터 tag 5bit, index 8bit, offset 3bit이다.

### Valid Bit

그럼에도 불구하고 여전히 저장된 data가 유효한지 확인해야 한다. 이를 valid bit로 나타낸다.
invalid block은 메인 메모리의 데이터가 변경되는 등의 이유로 발생한다.
이 경우 아무리 tag가 일치하더라도 cache miss이다.

Indexing, tag, valid bit를 종합하면 한 cache block의 물리적 크기는 1 + tag size + block size이다.

## Cache Performance

### Memory Stall Cycles

Memory stalls mainly originates from cache miss

$$\rm Memory \space Stall \space per \space Instruction = Miss \space Rate \times Miss \space Penalty$$

### Average Access Time (AMAT)

Average time to access data

$$\rm AMAT = Hit \space Time + Miss \space Rate \times Miss \space Penalty$$

To reduce AMAT:

* Reduce hit time
* Reduce miss rate
* Reduce miss penalty

## Associativity

한 cache entry(set)에 몇 개의 data block(=valid bit + tag + data)을 담을 수 있는가?
여기서 entry란 하나의 index(cache memory address)에 대응하는 영역을 말한다.

Associativity가 클수록 miss rate은 줄어들지만 일정 수준 이상에서부터는 그렇지 않다.
그 이유는? 한 set이 꽉 차지 않을 만큼 associativity가 크면 더 늘려도 차이가 없다.

### Direct Mapped Cache

앞서 살펴본 것. 한 entry에 한 block으로 cache index만으로 target cache block이 결정된다.

### Fully Associative Cache

Indexing이 전혀 없는 cache. 데이터가 어떤 cache block에도 저장될 수 있다.

### N-way Set Associative Cache

한 entry에 n개의 block이 있어 n개의 block을 모두 살펴봐야 함. Index는 set을 결정함.

## Page Coloring

만약 한 application이 참조하는 memory page가 indexing이 모두 한 set으로 몰린다면 해당 application performance는 심각하게 저하된다.
따라서 OS는 application에 memory page를 할당할 때 서로 다른 set으로 indexing되는 page들 위주로 골고루 할당해줘야 한다.
만약 set이 256개라면 OS는 256개의 color를 각 memory page에 할당한 후 color가 중복되지 않도록 할당.
