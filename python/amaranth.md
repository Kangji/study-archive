# Amaranth

The Amaranth hardware description language is a Python library for register transfer level modeling of synchronous logic.

## Signal

- either reg or wire
- Create with shape
- `m.d.comb` or `m.d.sync` with eq instead of assign, `<=`, `=`

## Array

- Array of Signal `Array([Signal(n, name = f'signal_{i}') for i in range(3)])`

## Memory

- (parameter) `width`: word width
- (parameter) `depth`: memory depth (depth = 2 ** addr_width)

### ReadPort

- (parameter) `domain`: default `sync` (1 cycle delay), `comb` (async)
- (parameter) `transparency`: default `True` ⇒ 이번 cycle에 해당 addr에 write된다면 그걸 read
- `m.submodules.rdport = rdport = self.mem.read_port()`
- (input) `addr`, `en`
- (output) `data`

### WritePort

- (parameter) `domain`: `sync` (1 cycle delay)
- `m.submodules.wrport = wrport = self.mem.write_port()`
- (input) `addr`, `en`, `data`

## FIFO (SyncFIFOBuffered)

- (parameter) `width`: word width
- (parameter) `depth`: FIFO depth
- (input) `w_data`, `w_en`, `r_en`
- (output) `r_data`, `r_rdy`, `r_level`, `w_rdy`, `w_level`
  - `rdy`: FIFO에 read/write data가 준비되었는지 ⇒ 준비 안됐을 때 enable되면 nop
  - `level`: FIFO에 있는 word 수 (read = write)
  - fwft = false ⇒ empty FIFO에 write할 때 r_rdy, r_data는 1 cycle 늦게 업데이트됨

## FSM

- `switch case`처럼 사용
  - switch: `with m.FSM("fsm_name")` block
  - case: `with m.State(”state_name”)` block
- (synchronous) state transition: `m.next = "next_state"`
