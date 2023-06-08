# Proof Mode

Coq의 나머지 절반인 Proof Mode에 대해서 살펴보자.

사실 우리는 이미 proof의 본질이 무엇인지 알고 있다.
Coq에서 Proposition의 증명이란 해당 proposition의 원소가 존재함을 보이는 것,
즉 원소를 제시하는 것과 같다.
증명에 대한 이 정의는 확장 가능하여 임의의 type을 증명할 수 있게 해준다.
(nat, bool 등은 증명 가능하다)

그리고 Definition 문서에서도 언급했지만 `Theorem`, `Goal` 등 Assertion command는
모두 `Definition` command의 special case이고, `Definition` command with term으로 나타낼 수 있다.

```coq
(* Definition not_false_is_true: not False := {goal}. *)
Theorem not_false_is_true: not False.
  intros x. (* fun x: False => {goal} *)
  exact x. (* x *)
Qed.
```

Proof mode에서 tactic을 이용해서 증명을 풀어나가는데,
즉 모든 tactic은 `Definition` sentence에서 제시하는 term의 일부분에 대응한다는 뜻이다.

그럼에도 불구하고 proof mode를 사용하는 이유는 `Definition`으로 term을 한 번에 제시하는 방법은 매우 직관적이지 않고 어렵다.
프로그래밍으로 치면 모든 코드를 메인 함수에 우겨 넣는 것과 마찬가지이다.

우리는 그래서 보통 자주 반복되는 코드를 따로 함수 또는 메소드로 만든다.
이에 대응하는 것이 tactic이다.
특히, 자주 반복되는 패턴들에 대해서 유용한 tactic들이 많이 있어서 proof mode가 더욱 편리하다.

하지만 어쨌든 근본적으로 Proof mode가 Definition sentence와 같다는 사실을 계속 상기하면서 문서를 풀어나갈 것이다.

## Goals and Proof State

`Theorem`, `Lemma`, `Goal` 등으로 proposition을 assert하면 proof mode로 들어간다.
이 때 증명하고자 하는 하나 이상의 proposition들을 goal이라고 부른다.

Goal들은 `Definition` command의 term에서 채워야 하는 부분의 type에 해당한다.

예를 들어,

```coq
Definition this_is_true : True.
```

`this_is_true`는 `True` type의 element인데, 아직 term이 주어지지 않았다.
따라서 Proof mode로 들어가서, `True`라는 goal을 증명(`True` type element를 제시)해야 한다.

증명 과정에서 goal은 계속 조금씩 바뀌고 여러 개의 subgoal로 나뉘기도 한다.
Unproven goal들의 집합을 proof state라고 한다.

증명이 성공하면 global environment에 True type의 constant this_is_true가 추가된다.

## Bullets

여러 개의 subgoal이 있을 때, bullet (`*`, `+`, `-`) 또는 curly bracket을 이용해서
subgoal에 focus / unfocus할 수 있다.
curly bracket은 열면 focus, 닫으면 unfocus라서 structure가 명확한데,
bullet은 unfocus하는 방법이 같은 level의 bullet으로 다음 subgoal로 넘어가는 방법밖에 없다.

## Exiting Proof Mode

증명이 끝나면 proof mode를 벗어나야 한다.
이는 `Definition` command에서 마지막에 `.`를 찍는 것과 같다.

Proof mode를 정상적으로 exit하는 경우 (proof에 성공해서),
증명이 끝나고 나면 coq kernel이 proof term이 `well-typed`인지 확인한 후,
해당 term의 type이 statement와 일치하는지 확인한다.
이렇게 검증이 끝나면 해당 proof에 대응하는 term은 (주어진 이름이 있는 경우) name에 binding되어
global environment에 추가된다.

### Qed

Proof mode를 정상적으로 exit하는 세 command 중 하나.
특징은 Opaque constant로서 추가된다는 점.
Opaque constant는 non-unfoldable해서 `Compute`같은 방법으로 unfold가 안된다.

### Save

Qed와 비슷하나, identifier를 반드시 줘야 하고, 이 identifier가 기존의 identifier를 override한다. `Goal` command랑 쓰면 좋다.

### Defined

Qed와 비슷하나, opaque constant가 아닌 transparent constant다.
따라서 unfoldable하다.
또, Save와 비슷하나, identifier를 주는 것이 선택사항이다.

### Admitted

지금 하는 증명을 포기하는데, 아몰랑 axiom으로 global environment에 추가해버린다.

### Abort

지금 하는 증명을 모두 폐기한다.
