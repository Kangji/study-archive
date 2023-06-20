# Systolic Array (SA)

Accelartor에서 input matrix를 저장하고 있는 buffer로부터 모든 PE로 data를 전송하면 낭비.
따라서 SA에서는 한 PE가 사용한 input value를 인접한 PE로 전달 (input 재사용)

input을 재사용하기 때문에 row마다, column마다 1 cycle씩 delay가 있음 (latency)
output을 저장하는 output buffer는 column 단위로 달려있기 때문에 vector inner product가 끝난 후 column 방향으로 결과를 flush해줘야 한다.

## Pipelining

SA를 이용해서 tile multiplication을 진행할 때 같은 output tile에 대한 multiplication은 같은 output에 accumulate되므로 flush할 필요가 없다 (Pilelining).

그러나 다음 output tile로 넘어갈 때는 output buffer로 flush를 해줘야 한다 (m cycle).

Initial latency는 최초 1회만 존재 => 나머지 output tile들은 flush와 동시에 initialize

따라서 output tile이 K개고 m by n, n by k matrix multiplication이라면,
총 cycle = $m + K \times (n + m)$

## Memory Bandwidth

사실 SA에서 보통 memory bandwith가 bottleneck인 경우가 많다.
(Weight matrix가 memory에 있음)

### Reason 1

MLP나 RNN에서 Weight은 Matrix-Vector 연산이라 딱 한 번만 쓰이기 때문에 재활용이 불가능하다.
따라서 batch size를 늘리면 해결 가능.
이 때 activation matrix size가 커져서 (m by n에서 m이 커짐) tiling 필요.

### Reason 2

Memory bandwidth가 보통 부족하다.
Matrix-Matrix multiplication에서 weight을 재활용하면 해결 가능.
이를 위한 methodology가 weight stationary SA.

## Weight Stationary SA

여태까지 본 SA는 output stationary.

반면 weight stationary는 연산 순서가 (tile 단위로) column부터라서 weight matrix는 딱 한 번만 읽어온다.
하나의 weight tile로 여러 tile multiplication을 진행한다.

따라서 matrix가 클 수록 하나의 weight tile로 수행하는 tile multiplication 수가 늘어나서,
DRAM latency를 CPU latency에 숨길 수 있다.

다만 column of tile이 모두 partial sum을 기억해야 하기 때문에 PE마다 partial sum buffer가 많이 필요하다.
