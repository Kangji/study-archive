# Memory

CPU, I/O Device들은 Interconnect를 통해 DRAM에 access한다.

## Step 1: Address Translation (IOMMU)

I/O Device들의 virtual address space를 system memory의 physical address로 translate해주는 unit이다.

Virtual Address의 장점은 (1) Isolation (2) Illusion.

## Step 2: AXI Protocol (Interconnect)

우리가 살펴볼 AMD AXI Protocol의 interconnect는 bus 형태로,
hardware component들이 연결되어 서로 통신한다.

Master-Slave 구조:

* Master: issue request (i.e., CPU)
* Interconnect: proxy => master에겐 slave, slave에겐 master
* Slave: serve request (i.e., memory controller)

### Split Transaction

Request-Response를 각각 비동기적으로 처리해서 효율을 높인다.

이를 위해 AXI의 master-slave component 사이에는 5개의 독립적인 channel이 존재한다.

* (Write) Address (M->S), Data (M->S), Response (S->M)
* (Read) Address (M->S), Data (S->M)

각 channel의 main payload port는 다음과 같다

* (Write) `AWADDR[n-1:0]`, `WDATA[n-1:0]`, `BRESP`
* (Read) `ARADDR[n-1:0]`, `RDATA[n-1:0]`

각 channel은 valid, ready 두 개의 handshake signal wire가 있다.
둘 다 1일 때만 clock rising edge에 payload transfer가 일어난다.

* `AWVALID`, `WVALID`, `BVALID`, `ARVALID`, `RVALID`: send ready (sender -> receiver)
* `AWREADY`, `WREADY`, `BREADY`, `ARREADY`, `RREADY`: receive ready (receiver -> sender)

### Data Transfer in Bus

* **burst** (group of data elements)가 bus를 타고 target block으로 이동
* Read/Write address channel로 address & burst metadata(type, length, size) 전송
  * `AWLEN[3:0]`, `ARLEN[3:0]`: burst length (0 ~ 15)
    * length = single burst에 포함된 data element 수 = transfer cycle 수
  * `AWSIZE[2:0]`, `ARSIZE[2:0]`: burst size (exponential => 2^0 ~ 2^7)
    * size = 한 cycle에 transfer되는 byte 수
  * `AWBURST[1:0]`, `ARBURST[1:0]`: burst type (FIXED, INCR, WRAP)
    * Master가 provide하는 address는 첫 data element의 address 뿐
    * 나머지 element의 address는 burst type을 보고 slave가 계산해야 함

### Flow Control

* Stop-and-Wait: Sender가 Req + Data를 보내고 Ack을 받을 때까지 기다림 => 최소 2 cycle
* Single Credit-based: Ack (= READY) is a credit
  * READY = 1 => sender가 보낸 Req + Data를 같은 cycle에 receiver가 수신한 것을 보장
  * READY가 0이라면 receiver가 수신했다는 보장이 없음 => 다음 cycle에 retry
* Handshaking: For each cycle,
  * Sender: 보낼 정보가 있으면 보낼 준비를 하고 valid = 1
  * Receiver: 정보를 받을 수 있으면 받을 준비를 하고 ready = 1
  * data transfer iff valid, ready 모두 1

### Multiple Outstanding Requests

* Outstanding request: requested, but not received response yet
* Ability to issue multiple requests before receiving responses for previous requests
  * Overlap the service of multiple request on the bus
* For example, non-blocking cache allows data cache to supply hits during a miss
  * OOO Execution
  * hit under multiple miss (or miss under miss): cache에서 한 번에 여러 개의 outstanding miss가 존재할 수 있다

### Connectivity Issue

모든 component가 communicate 가능한 것은 아니다

### Arbitration Scheme

Scheduling issue => Round Robin(RR), Least Recently Granted(LRG), ...

## Step 3: DRAM Access

### Reducing Cache Miss Penalty

* Non-blocking cache의 miss penalty 자체를 줄일 수도 있다.
  * bank parallelism: 서로 다른 bank에서 병렬적으로 read
  * row buffer hits: data가 같은 row에 있는 경우 row access 없이 row buffer를 재활용 가능 (row buffer hits)
* 이를 위해 memory access scheduling이 필요함

### DRAM Architecture

* 3 dimension: bank, row, column

### DRAM Operations

* Row Access (ACT) : Data를 읽거나 쓸 bank와 row를 row buffer로 fetch
  * 이 때 기존 row가 destroy됨
* Column Access (RD/WR) : Row buffer에서 data에 해당하는 column만 선택
  * 하나의 row에 여러 data가 들어있을 수 있음
* Precharge: destroy된 row를 복구

### SDR / DDR

* DDR : rising edge와 falling edge 모두 이용해서 bandwith 2배

### Refresh

* DRAM의 각 row는 주기적으로 refresh (activation + precharge)해줘야 data loss가 안생김
  * auto-refresh command
* Refresh하는 동안 DRAM bank는 사용 불가
* Reducing Refresh Overhead
  * Per-bank Refresh ⇒ overlap refresh and memory access
