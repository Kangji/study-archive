# Physical Storage System

## Metrics

* Data Access Speed
* Cost per Unit of Data
* Reliability => data loss on system crash / physical failure
* Persistency => volatile/non-volatile

## Storage Hierarchy

* primary storage : fastest, volatile (Cache, Memory)
* secondary storage : non-volatile, on-line (Disk)
* tertiary storage: non-volatile, off-line (CDRom, Tape)

Online : under control of CPU

## Magnetic Disk

* read-write head
* circular track = set of sectors
* platter = set of circular tracks => spins continuously => Random Access
* cylinder = ith track of all platters

## Performance Measurement

### Access Time

* from when a read or write request is issued to when data transfer begins
* Seek Time + Rotational Latency

* Seek Time
  * time to reposition the arm over the correct track.
  * random => avg = 1/2 worst
* Rotational Latency
  * time for the sector to come under the head
  * rotation => avg = 1/2 of hz

### Data Transfer Rate

The rate at which data can be retrieved from or stored to the disk.

### Mean Time To Failure (MTTF)

The average time the disk is expected to run continuously without any failure.

## Optimization

* block 단위 data transfer
  * larger block => less overhead, but more waste space
* disk-arm scheduling
  * elevator algorithm: arm을 한쪽 방향으로 움직이면서 그 방향 request 다 처리
* file organization
  * 한 파일의 데이터를 같은/인접한 block에 저장 => seek time 감소
* nonvolatile write buffer
  * caching과 같은 원리
* log disk

## RAID System

Multiple disks shown as a single disk of **high speed & reliability**

## Reliability via Redundancy

### Mirroring

* Duplicate every disk => MTTF 비약적으로 상승 (둘 다 고장나야 해서)
* Write both, Read one

### Parity

* Mirroring은 reliable한 만큼 too much redundancy
* M개의 block을 (M+1)개의 disk에 저장 => 1개 disk failure까지는 복구 가능

## Performance via Parallelism

* Stripe data across multiple disk => Transfer rate 대략 N배
* Bit-level => worse seek time
* Block-level => block 단위로 striping, 여러 block access시 빠름

## RAID Level

* level 0: block striping + no redundancy
* level 1: block striping + mirroring
  * best write performance
* level 5: block striping + block interleaved distributed parity
  * lower storage overhead but higher write overhead

## Discussion

### 12-1. Online Storage

Q. Why are the following storage mediums not widely used for on-line storage?

* Magnetic tape
* Optical storage

A. Magnetic tape: no random access, Optical storage: write once, read many

### 12-2. Flash Memory

Q. Given the characteristics of flash memory and the mechanics of data access in disk-based databases, suggest ways in which flash memory can be used to enhance database performance.

A. Write buffer

### 12-3. Parity

Q. Consider the data and parity-block arrangement on four disks:

![Parity Block Arrangement](assets/003.png)

The $B_i$s represent data blocks; the $P_i$s represent parity blocks.
Parity block $P_i$is the parity block for data blocks $B_{4i-3}$ to $B_{4i}.
What, if any, problem might this arrangement present?

A. 4개의 block과 parity block까지 총 5개의 block 중 두 개 이상이 fail하면 복구가 안된다.
따라서 이 5개의 block은 서로 다른 disk에 저장되어 있어야 한다.

### 12-4. Scrubbing

Q. Data scrubbing is an error correction technique that uses a background task to periodically inspect the storage for errors, then corrects detected errors using redundant data in the form of different checksums or copies of data. Why do you think data scrubbing is used widely in most commercial RAID systems?

A. On-line correction은 user experience에 좋지 않고, correction 너무 안하면 error가 누적되기 때문에 "가끔씩" 주기적으로 해주는 것이 좋다. Correction은 전체 disk scan을 해야 해서 비싸므로 background task로 돌린다.
