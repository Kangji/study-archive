# Queries

Django가 query를 사용하는 방법은 두 가지가 있다.
* Model API를 활용하여 instance를 DB에 저장하거나 삭제하여 sync를 맞추는 방법
* Model 클래스의 `Manager`를 이용하여 query를 생성하고 실행하는 방법

Model API는 다음과 같다.
* Insert-or-update: `instance.save()` => Instance와 DB의 sync를 맞춘다.
    * instance가 DB에 없으면 insert, 있으면 update
    * 이 과정에서 default 등 설정된 값은 instance에도 반영
    * Intermediary Model이 없는 many-to-many relation은 `instance.related_fields.add(*another)`로 relation들을 추가할 수 있다.
* Delete: `instance.delete()` => Instance를 DB에서 삭제

## Manager & QuerySet

모든 Model class는 기본적으로 자기 자신에 대한 manager를 `objects` attribute로 가지고 있다.
`Manager`는 Django model에 대한 DB query operation interface를 제공한다.

Manager는 각종 method를 통해 직접 DB에 접근하거나, `QuerySet` object를 생성한다.
QuerySet은 Select 쿼리의 "실행"이며 iterable이자 subscriptable이다.
또한 QuerySet은 lazy하며 element에 access할 때 query를 실제로 실행시킨다.

QuerySet이 한 번 실행되면 그 결과를 캐싱하는데,
만약 subscription으로 특정 row에만 접근했다면 caching하지 않는다.
즉, 최초로 queryset으로 iterator를 생성할 때 caching을 하게 된다.
따라서 subscription으로만 계속 row를 읽는다면 매 번 DB에 접근하게 된다.

QuerySet은 query인 동시에 query builder이다.
그리고 Manager 또한 그렇다. 왜냐하면 모든 Manager는 initial QuerySet이 내장되어 있기 때문이다.
따라서 사실 QuerySet을 생성하는 Manager method는 internal QuerySet에 대해 호출된다.

각 model마다 가지고 있는 `objects` manager의 initial query는 대응하는 table의 모든 row를 가져오는 쿼리이다.
한편, 앞서 살펴보았던 relationship에서 one-to-many(`ForeignKey`의 reverse accessor),
혹은 many-to-many(`ManyToManyField`와 reverse accessor)는 모두 manager이다.
이들은 `objects`와는 달리 object와 관련된 condition이 붙은 initial query를 가지고 있다.
예를 들어, `row.related_items`의 initial query는 `SELECT * FROM ref_table WHERE ref_table.table_id = ?`이다.

다음은 QuerySet(or Manager)의 기본적인 builder pattern method들이다.

* `all() -> QuerySet`: initial query(Manager), 혹은 no-op(QuerySet)
* `filter(*conditions) -> QuerySet`: `WHERE` clause
* `exclude(*conditions) -> QuerySet`: `WHERE NOT` clause
* `order_by(*fields) -> QuerySet`: `ORDER BY` clause
* `distinct() -> QuerySet`: `DISTINCT`

## Non-QuerySet Methods

Manager와 QuerySet은 select 외에도 insert, update, delete query도 지원하지만, 이들은 lazy하지 않다.
따라서 이들은 QuerySet을 return하지 않고 즉시 DB query를 호출한다.
Insert는 `create(**fields)`, update는 `update(**fields)`, delete는 `delete()`이다.
Update와 delete의 `WHERE` 절은 `filter` 등으로 처리해야 한다.
한편, queryset을 이용하지 않는 select도 있는데, `get(condition)`, `first()` 등이다.

## Aggregation

* `annotate(**kwargs)`: Add columns or expressions, i.e., aggregation functions to select columns
* `alias(**kwargs)`: Annotate과 동일하나, 추가된 column/expression은 다른 queryset method에서 사용될 뿐 결과에는 존재하지 않는다.
* `values(*fields)`: QuerySet이 각 row를 model이 아닌 dictionary로 return하며, 명시된 field들이 각 dictionary의 key가 된다.
* `aggregate(**kwargs)`: Annotate과 비슷하지만 lazy하지 않으며, 명시된 aggregation function들의 결과를 dictionary로 return한다.

`annotate`, `alias`는 각 row가 각 group이 되고, `aggregate`는 전체 row들이 하나의 group이 된다.
`values`를 잘 활용하면 원하는 대로 `GROUP BY`를 할 수 있는데, `values`는 `GROUP BY fields`를 암시한다.

## Condition

Condition은 `field__lookuptype=value` 형식으로 주어져야 한다.
[`lookuptype`](https://docs.djangoproject.com/en/5.0/ref/models/querysets/#field-lookups)은 `lte`, `lt`, `gte`, `gt`, `exact` 등등이고, `exact`가 default다.

## Join

Join을 활용할 때는 `table1__table2__field__lookuptype=value` 형식으로 주어져야 한다.
다만 Join이 Left outer join인 점은 유의하자.

## `F` Object

Condition이 다른 field를 reference하기 위해서 `django.db.models.F` object를 만들어주어야 한다.

```py
Entry.objects.filter(number_of_comments__gt=F("number_of_pingbacks"))
```

## `Q` Object

Condition들을 wrapping하는 `django.db.models.Q` object를 사용하면 더 미세한 control이 가능하다.
예를 들면 `Q` 객체끼리 `|` 하면 `OR` statement가 된다.

## Nested Filter

Filter(혹은 exclude)를 여러 번 호출하는 것과 여러 condition을 한 번에 넘겨주는 것은 다르다.

예를 들어,

```py
# select distinct dept.*
# from dept
#   left outer join student
#   on student.dept_id = dept.id
# where student.name = 'alice' and student.sex = 'M'
Department.objects.filter(student__name='alice', student__sex=Sex.M)
# select distinct D.* from (
#   select dept.*
#   from dept
#       left outer join student
#       on student.dept_id = dept.id
#   where student.name = 'alice') as D
#   left outer join student
#   on student.dept_id = dept.id
# where student.sex = 'M'
Department.objects.filter(student__name='alice').filter(student__sex=Sex.M)
```

첫 번째 쿼리는 이름이 alice인 남학생이 있는 department를 return하지만,
두 번째 쿼리는 이름이 alice인 학생과 남학생이 모두 있는 department를 return한다.

## Ordering

Ordering과 관련하여 몇몇 주의사항이 있다.
* `Lower` 등 DB function을 활용하여 ordering 기준을 복잡하게 설정할 수 있다.
* Related table의 column을 명시하고 싶다면 condition에서처럼 `__`(double underscore)를 사용한다.
* `-`를 field 이름 앞에 붙이면 descending order를 뜻한다. 반면 DB function은 `asc()`, `desc()` 등의 method를 사용한다.
* `order_by`는 이전 `order_by`를 overwrite하기 때문에 여러 column에 대한 ordering을 원한다면 한 번에 줘야 한다.

## Set Operation

* `union(*qs, all=False) -> QuerySet`: `UNION`/`UNION ALL` operation
* `intersection(*qs) -> QuerySet`: `INTERSECT` operation
* `difference(*qs) -> QuerySet`: `EXCEPT` operation

Set Operation이 return하는 queryset의 model은 method를 호출한 model을 따라간다.

## N+1 Problem

Relationship이 lazy하기 때문에 다른 ORM들과 마찬가지로 N+1 문제 등에 직면하게 된다.
Django에서는 manager/queryset에 related option을 주는 method를 호출하여 eager loading을 강제할 수 있다.
이 경우 foreign key가 descriptor가 아닌 실제 object가 된다.

```py
# Lazy Loading (N+1 Queries)
Dog.objects.filter(id__gt=65540)
# Select Related = JOIN Query (Single Query)
Dog.objects.select_related('owner').filter(id__gt=65540)
# Prefetch Related = Select-In Query (Two Queries)
Dog.objects.prefetch_related('owner').filter(id__gt=65540)
```

Select related는 JOIN을 이용한 single query이므로 many-to-one에 적합하고,
Prefetch related는 SELECT-IN을 이용한 double query이므로 one-to-many나 many-to-many에 적합하다.

## Asynchronous Queries

QuerySet은 lazy하기 때문에 asynchronously iterable하다.
반면 lazy하지 않은 API들은 각각 asynchronous version을 제공한다.
예를 들면, `delete`는 `adelete`, `get`은 `aget`, `save`는 `asave`.

## Transaction

모든 View 함수가 begin transaction을 default로 설정하고 싶으면,
`settings.py`에 `DATABASE`에서 해당 DB의 `ATOMIC_REQUESTS`를 True로 설정하면 된다.
기본은 False.

개별 view를 control하고 싶으면 `django.db.transaction` 모듈의 `atomic` 함수를 이용하자.
`atomic`은 decorator(`@atomic`)이자 context manager(`with atomic():`)이다.

만약 `ATOMIC_REQUESTS`가 True인데 non-atomic을 하고 싶으면 `non_atomic_requests` decorator를 사용하자.

## QuerySet Reference

그 외에도 다양한 API를 제공하니,
[reference](https://docs.djangoproject.com/en/5.0/ref/models/querysets/)를 참고하며 개발하도록 하자.
