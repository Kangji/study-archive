# Matrix Multiplication (MM)

AI에서 흔히 말하는 hardware 가속기는 결국 행렬 곱셈을 빨리 할 수 있도록 도와주는 장치이다.
소프트웨어와 다르게 하드웨어는 더 많은 최적화 방법이 존재하는데,
대표적인 예시가 병렬화이다.

## Vector-Vector Multiplication

행렬 곱셈을 이해하기 위해서 먼저 벡터 곱샘부터 이해해야 한다.
행렬 곱셈은 결국 벡터 곱셈들로 이루어져 있기 때문이다.

### Multiplier Accumulator (MAC)

$out \leftarrow out + a \times b$

MAC에서 각 cycle에 일어나는 일이다. CPU의 ALU에서는 한 cycle로 불가능한 연산이다.

### Processing Element (PE)

따라서 MAC을 이용하면 n cycle만에 길이 n인 벡터곱을 수행할 수 있다.
다만 MAC만 가지고는 안되고, 당연하지만 control이 필요하다.
언제 시작하고 총 몇 cycle이 걸리는지 알아야 하기 때문이다.
PE는 MAC + Counter + control이다.

## Matrix-Matrix Multiplication

$C = A \times B$에서 $C_{i,j}$는 $A_{i,*} \times B_{*,j}$ 이므로 $C$의 각 element는 벡터곱으로 도출된다. 따라서 m by k개의 PE가 있으면 n cycle만에 m by n, n by k matrix multiplication을 parallel하게 수행할 수 있다.

Hardware accelerator는 이처럼 여러 개의 PE로 구성된다.

## Tensor Core

Tensor core는 Matrix multiplication을 vector vector outer product의 합으로 봐서 더 병렬화를 진행한 case이다.

$C = A \times B$에서 $C = \Sigma_{i = 1}^{n}A_{*,i} \times B_{i,*}$로도 볼 수 있다.
따라서 n개의 accelarot가 있으면 matrix multiplication을 1 cycle만에 처리할 수 있다.
각 accelerator는 vector vector outer product를 수행하고, adder tree를 이용해서 결과를 합친다.

## Tile Multiplication

MM에서 보통 문제가 되는 것은 행렬의 크기가 너무 크다는 것이다.
따라서 이 경우 input, output matrix를 accelartor 단위로 여러 개의 tile로 쪼개서 계산한다.

## Application: Convolutional Neural Network (CNN)

### 2D Convolution

* Dot product of 2D image & filter
  * $H_1$ by $H_2$ input image (or feature map)
  * $R_1$ by $R_2$ filter (or window)
  * $E_1$ by $E_2$ output channel (or feature map)
* One dot product is done by one neuron, thus $E_1 \times E_2$ neurons required
  * Each neuron performs $R_1 \times R_2$-length vector dot product
  * Total \# multiplications : $R_1 \times R_2 \times E_1 \times E_2$

### 3D Convolution

* Dot product of 3D image & filter
  * $H_1$ by $H_2$ by $C$ input image (or feature map)
  * $M$개의 $R_1$ by $R_2$ by $C$ filter (or window)
    * \# parameters : $R_1 \times R_2 \times C \times M$
  * $M$개의 $E_1$ by $E_2$ output channel (or feature map)
* One dot product is done by one neuron, thus $M \times E_1 \times E_2$ neurons required
  * Each neuron performs $R_1 \times R_2 \times C$-length vector dot product
  * Total \# multiplications : $R_1 \times R_2 \times C \times M \times E_1 \times E_2$

### Convolution Lowering

* M개의 filter => M개의 $R_1 \times R_2 \times C$-length vector
=> $M$ by $R_1 \times R_2 \times C$ matrix
* $E_1$ by $E_2$ output channel
=> $E_1 \times E_2$개의 $R_1$ by $R_2$ by $C$ input image
=> $R_1 \times R_2 \times C$-length vector
=> $R_1 \times R_2 \times C$ by $E_1 \times E_2$ matrix
* Result of matrix multiplication = $M$ by $E_1 \times E_2$
=> $M$ by $E_1$ by $E_2$ output channel

## Magnitude-based Pruning

* 0에 가까운 weight들을 prune
* Reduce parameter size

### Sparse Weight Matrix

* Compressed Sparse Row(CSR) or Column(CSC) ⇒ 0을 쳐내기
* value[i]: i번째 nonzero element (order by (row, column))
* column index[i]: i번째 nonzero element가 몇 번째 column에서 온 건지
* row pointer[i]: 몇번째 nonzero element부터가 원래 matrix의 ith row의 element

### Weight Clustering

* Weight을 n개의 bin으로 quantize 후 weight 대신 bin # 저장
* matrix element의 datasize significantly reduce

## Column-wise Pruning

* Weight matrix에서 zero column은 skip
* reduce # cycle ⇒ increase performance
* train ⇒ repeat (특정 threshold 이하 column prune ⇒ fine tune) until quality is maintained

## NVIDIA Zero Skipping

* 2:4 sparsity : at most 2 nonzero out of 4 elements
* Compress ⇒ half size weight matrix + half size nonzero indice matrix (small data size)

## Sparse Tensor Core

* nonzero indice matrix를 보고 activation matrix에서도 대응하는 element skip ⇒ 연산량 절반
