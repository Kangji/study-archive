# Data Storage Structure

## File Organization

* DB = Collection of files
* File = Sequence of records
  * block 단위로 disk에 저장됨
  * 하나의 file당 한 table의 record or 여러 table의 record
* Record = Sequence of fields
  * Fixed or variable length record

## Variable Length Record

* attribute 순서대로
* variable length attribute는 fixed length metadata를 먼저 저장
  * metadata = offset + length of actual data
* fixed length attribute는 그대로 저장
* 마지막에, 그리고 variable length data 앞에 null bitmap: null인지 아닌지

## Slotted Page Structure

* Page header
  * \# of records
  * end of free space
  * location(ptr), size of each block
* 각 record에 대한 pointer 제공
* Record는 뒤에서부터 채워서 fragmentation 방지

## Storage Access

CPU(DBMS)는 main memory하고만 communicate.
따라서 읽고 쓰기 전에 disk block을 main memory에 올려야 한다. 이 영역을 **buffer**라고 한다.

### Buffer Manager

Buffer를 효율적으로 관리하는 DBMS의 subsystem. MMU처럼 Block replacement를 수행.

## Discussion

### 13-3. Buffer

Q. Suppose transaction T1 requests write(X) on a database item X, and the DBMS subsequently responds with a success message. Does this mean that X is updated in the disk? Explain.

A. No. It is written on the block in the buffer.
