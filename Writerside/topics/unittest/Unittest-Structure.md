# 03. 단위 테스트 구조

## AAA 패턴

- 준비(Arrange) - 실행(Act) - 검증(Assert) 패턴
- 준비(Arrange)
    - 테스트 대상 시스템(SUT : System Under Test) 과 해당 의종성을 원하는 상태로 구성
- 실행(Act)
    - 테스트 대상 Method 호출하고, 준비된 의존성을 전달하며, 출력 값을 캡쳐한다.
- 검증(Assert)
    - 결과를 검증한다.
- Given-When-Then 패턴
    - AAA 패턴과 동일하지만, AAA 패턴은 TDD에 GWT 패턴은 BDD에 사용되는 용어이다.
    - 행동 중심으로 시나리오 기반으로 테스트 코드를 작성하기 유용하다.

## 고려사항

1. 하나의 테스트 구문에 여러 개의 AAA 패턴이 들어가지 않도록 한다.
2. 테스트 구문에 IF 문을 피하도록 한다.
3. 테스트 준비 구절은 가급적 줄인다.
    - 테스트 클래스 내 비공개 Method, Factory Class 를 활용하여 캡슐화
    - 캡슐화 한 Class 는 다른 테스트에서 활용 가능하다.
4. 테스트 검증은 하나의 동작을 검증하도록 한다.
    - 단일 동작 단위에 대한 여러 결과를 검증 할 수 이다.
5. 종료 단계
    - 테스트에서 사용된 리소스를 정리하는 단계.
    - 테스트로 작성된 파일을 지우거나, 임시 객체를 지우면서 Side-Effect 를 예방한다.
    - 공통 코드로 작성가능하기 때문에 AAA 패턴에서는 제외된다.

## Test Fixture (테스트 픽스처)

- 테스트 실행 대상 객체로 정규 의존성을 가진다.
- 테스트 실행 전에 고정상태로 유지하기 때문에 동일한 결과를 생성한다.

## 테스트 픽스쳐 재사용

- Case 1
    - 테스트 생성자에서 공통 테스트 Fixture 를 초기화 한다.
    - 즉, 테스트 실행시 테스트 클래스 내의 생성자에서 픽스처를 초기화 하고 공유한다.
    - 장점
        - 각 테스트에서 생성자를 일일히 작성할 필요 없이 초기 생성자에서 생성한 픽스처를 사용하여 테스트를 수행한다.
    - 단점
        - 테스트 간 결합도가 높아진다.
        - 테스트 가독성이 떨어진다.
    - Example
        - 공통 텍스트 픽스쳐는 공유 상태로 두어선 안된다.
        - 아래와 같은 코드에서 _store 의 값이 임의로 변경되면 다른 테스트가 실패할 수 있다.

      ```Python
      class CustomerTests(unittest.TestCase):
          # 공통 테스트 픽스쳐
          _store = new Store()
          _store.add(Item, 10)
          _customer = new Customer()
          
          def test_1(self):
              pass
      ```

        - 위의 코드를 아래와 같이 변경한다. ( Python Unittest Style )
            - Python Unittest Style 에서는 SetUp 함수에서 테스트 픽스쳐를 선언하면
            - 각 테스트마다 Fixture 를 호출하여 초기화 한다.

      ```Python
      class CustomerTests(unittest.TestCase):
          
          def setUp(self):
              # 공통 테스트 픽스쳐
              self._store = new Store()
              self._store.add(Item, 10)
              self._customer = new Customer()
          
          def test_1(self):
              pass
      ```

- Case 2.
    - 비공개 팩토리 메서드(Private Factory Method)
      > 객체를 생성할 때 어떤 클래스의 인스턴스를 만들 지 서브 클래스에서 결정
    - 테스트 클래스에서 픽스처를 초기화 하는 것이 아닌, 각 테스트 실행 시기에 팩토리 메서드로 구현된 픽스처를 초기화 한다.
    - 다만, DB Connection, Resource 초기화 등 테스트 전반적으로 사용하는 코드의 경우 Base Class 로 초기화 한다.
    - Example.
      ```Python
      class DatabaseConnection:
          _db_conn = new Database

          def InitDBConn(self):
            return self._db_conn

      class CustomerTests(unittest.TestCase, DatabaseConnection):
          db_conn = DatabaseConnection.InitDBConn()

          def test_1(self):
            self.db_conn.query()
      ```

## 단위 테스트 명명 규칙

- 엄격한 명명 정책을 따르지 않지만, 어떤 테스트인지 알 수 있도록 한다.
- 단어를 밑줄로 표시한다.
- ex.) [테스트 대상 메서드]_[시나리오]_[예상결과]

> - XUnit Style 에서는 test_ 로 시작하는 함수 혹은 파일명이 자동으로 테스트 대상으로 자동으로 선택된다.
> - 내가 선호하는 테스트 명은 시나리오 이름을 폴더 명 혹은 Class 명으로, 함수명은 test_[테스트 대상 Method]_[예상결과] 으로 선언한다.

## 매개변수화된 테스트

- 테스트케이스 작성시 매개변수마다 다른 결과를 도출하는 경우가 발생한다.
- 각 매개변수 마다 테스트 케이스를 만드는 것 보다 모으는 방법이 필요하다.
- [Parameterized Unit Testing in Python](https://medium.com/@samarthgvasist/parameterized-unit-testing-in-python-9be82fa7e17f)

```Python
@parameterized.expand([
        (None, None),
        ('2022', 'Kannada'),
        ('2021', 'Hindi'),
        ('2011', 'English')
    ])
def test_invalid_input(self, year, language):
        service = MovieService(self.movie_store)
        with self.assertRaises(Exception):
            service.movie_service_handler(year, language)
```

## 검증문

- 테스트 결과를 검증하는 방법 with. Python
    1. unittest
       [unittest.TestCase.assert](https://docs.python.org/ko/3/library/unittest.html#unittest.TestCase.assertEqual)
    2. pytest
       [pytest assert](https://docs.pytest.org/en/7.4.x/how-to/assert.html#assert)

```Python 
def test_function():
    assert f() == 4
```