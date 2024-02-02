# Example: Formal Logic

모든 프로그래밍 언어는 syntax(형태)와 semantics(의미)를 가진다.
그리고 둘 다 귀납적으로 정의된다.

형식 논리를 통해 살펴보자. 형식 논리는 논리학을 기호화한 것으로 프로그래밍 언어이다.
아래는 Propositional Logic의 Syntax와 Semantics 일부이다.

## Syntax

$
f \rightarrow \, T \, \vert \, F \newline
\quad \vert \quad \neg f \newline
\quad \vert \quad f \, \Rightarrow \, f \newline
\cdots
$

## Semantics (Inference Rule)

- $\frac{}{\Gamma \vdash T}$
- $\frac{}{\Gamma \vdash f} (f \in \Gamma)$
- $\frac{\Gamma \vdash F}{\Gamma \vdash f}$
- $\frac{\Gamma \vdash \neg \neg f}{\Gamma \vdash f}$
- $\cdots$

### Soundness

Inference Rule이 안전(sound)하려면 $^\forall f, \phi \vdash f \Rightarrow f \rm{\space is \space true}$, 즉, 추론 규칙에 의해 도출된 명제는 모두 참이어야 한다.

## Completeness

Inference Rule이 완전(complete)하려면 $^\forall f, f \rm \space is \space true \it \Rightarrow \phi \vdash f$, 즉, 모든 참인 명제는 추론 규칙에 의해 도출되어야 한다.
