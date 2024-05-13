# Unittest

Python unittest는 JUnit을 바탕으로 제작된 builtin testing framework이다.

## Basic Usage

```py
import unittest

class TestSomething(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        """
        This classmethod is executed only once,
        while `setUp` is executed for every test methods.

        When this method raises error,
        all tests and `tearDownClass` in this class will be skipped.
        """
    
    def setUp(self):
        """
        This method is executed before executing test methods.
        When this method raises error,
        the test and `tearDown` will be skipped.
        """
    
    def tearDown(self):
        """
        This method is executed after executing test methods,
        regardless of success.
        """

    @classmethod
    def tearDownClass(cls):
        """
        This classmethod is executed only once,
        while `tearDown` is executed for every test methods.
        """
    
    def test_something(self):
        self.assertEqual('foo'.upper(), 'Foo')
        self.assertTrue(2 == 2)
        self.assertFalse(2 == 3)
        with self.assertRaises(ValueError):
            int('h')
```

## Running Tests

```sh
python -m unittest [-v] test_module [...]               # Run tests in the module
python -m unittest path_to_test/test.py                 # Module as file
python -m unittest test_module.TestClass                # Run tests in the class
python -m unittest test_module.TestClass.test_method    # Run single test
```

## Test Management

Decorator를 이용하여 test의 동작을 control할 수 있다.

```py
class TestManagement(unittest.TestCase):
    @unittest.skip("reason")
    def test_deprecated(self): ...

    # or skipUnless
    @unittest.skipIf(somelib.__version__.split('.')[0] < '2', "Old dependency version")
    def test_somelib(self): ...

    @unittest.expectedFailure
    def test_must_fail(self):
        self.assertEqual(2, 3)
```

## Async Test

`TestCase` 대신 `IsolatedAsyncioTestCase`를 사용하여 coroutine을 test로 만들 수 있다.

```py
class AsyncTest(unittest.IsolatedAsyncioTestCase):
    async def asyncSetUp(): ...
    async def asyncTearDown(): ...
```

## Mocking Objects

`unittest.mock` 모듈의 `Mock`, `MagicMock`, `AsyncMock`, `@patch` 등을 사용할 수 있다.
`MagicMock`은 magic method(special method)의 mocking을 지원하고,
`AsyncMock`은 coroutione의 mocking을 지원한다.

```py
# Mock method.__class__ and identity
something.value = Mock(spec=Value)
assert isinstance(something.value, Value)
# Method always returns 3
something.method = Mock(return_value=3)
# Method always raises ValueError
something.method = Mock(side_effect=ValueError())
# Method is equal to f(x) = x+2
something.method = Mock(side_effect=lambda x: x + 2)
# Method behaves like generator
something.method = Mock(side_effect=[5, 4, 3, 2, 1])
```

`@patch`는 테스트 도중 특정 객체를 `MagicMock` object로 치환하고, 테스트가 끝나면 복원시킨다.
`@patch`는 치환한 `Mock` object를 기존 argument 뒤에 붙여주기 때문에 decorator 순서에 유의하자.
만약 클래스의 메소드를 치환하고자 한다면 `@patch.object`를 사용할 수 있다.

```py
def bar(self):
    return foo() * foo()

class Foo:
    def hello(self): ...

class Test(unittest.TestCase):

    @patch("foo")
    def test_bar(self, foo: MagicMock):
        foo.return_value = 5
        self.assertEqual(bar(), 25)
        self.assertEqual(foo.call_count, 2)
    
    @patch.object(Foo, "hello")
    def test_foo(self, hello: MagicMock): ...
```

### Mock Assertion

```py
mock.assert_called()            # Assert that mock object is called at least once
mock.assert_called_with(args)   # Assert that mock object is called with given args
mock.assert_called_once()       # Assert that mock object is called only once
mock.assert_called_once_with()  # Assert that mock object is called only once with given args
mock.assert_not_called()        # Assert that mock object is not called
async_mock.assert_awaited()     # Assert that mock object is awaited
```
