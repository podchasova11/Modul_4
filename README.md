# Modul_4
<details>
  <summary>Параметризация</summary>
  
## Параметризация:
  
  Это до безумия крутая фича pytest, которая позволяет написать один тест, а в итоге выполнить 2,3,4,10 тестов, запуская один и тот же тест, но с разными данными.

1. Для параметризации теста с одним параметром используется специальный декоратор
- `@pytest.mark.parametrize(”имя_параметра”, [значение_1, значение_2])` - это декоратор параметризации, который принимает в себя `имя параметра` (как хотите называйте) и список его `значений`для каждого теста.
  
`Синтаксис:`
  
  
  ```
  
  @pytest.mark.parametrize("имя_параметра", [значение_1, значение_2])
def test_numbers(имя_параметра): # Имя параметра прокидывается в качестве аргумента тестового метода
    print(имя_параметра) # Все как в функциях, просто используем прокинутый аргумент
  
  
  ```
  2. Для параметризации теста с несколькими параметрами используется тот же декоратор, но синтаксис немного меняется
  
  ```
  @pytest.mark.parametrize(
	"имя_параметра_1, имя_параметра_2", [ # Параметры
	  (значение_параметра_1, значение_параметра_2), # Первый тест
	  (значение_параметра_1, значение_параметра_2), # Второй тест
	  (значение_параметра_1, значение_параметра_2)  # Третий тест
	]
)
def test_print_people(имя_параметра_1, имя_параметра_2):
    print(имя_параметра_1, имя_параметра_2)
  
  ```
  
  Когда у нас несколько параметров, то значения помещаются внутрь кортежа, где каждый элемент кортежа соответствует значение каждого параметра. Можно просто представить себе табличку)
  
  |        | имя_параметра_1               | имя_параметра_2 |
| ------------- |:------------------:| -----:|
| Тест 1     | значение_параметра_1    | значение_параметра_2|
| Тест 2     | значение_параметра_1 |   значение_параметра_2 |
| Тест 3  | значение_параметра_1        |    значение_параметра_2 |
  
  
Markdown | не такой | красивый
--- | --- | ---
*Но выводится* | `так же` | **клево**
1 | 2 | 3
  
  ## Разбор примеров
  
  ## `Один` параметр
  
  Aбстрактный пример:
  В данном примере мы прописываем параметризацию в одну строчку без кортежа, но с большим количеством значений, это будет нечитабельно. Поэтому предлагаю использовать перенос строк (будет похоже на табличку):
  
  ```
  @pytest.mark.parametrize(
	"имя_параметра", [
	   значение_1, # Значение параметра для первого теста
	   значение_2, 
	   значение_3, 
	   значение_4,
	   значение_5,
	   значение_6
	]
)
def test_numbers(имя_параметра):
    print(имя_параметра)
  
  ```
  
  Реальный пример:
  
  ```
  @pytest.mark.parametrize(
	"number",
	[ 
	    "One", # Тест 1
	    "Two", # Тест 2
	    "Three", # Тест 3
	    "Four", # Тест 4
	    "Five", # Тест 5
	] 
)
def test_print_numbers(number):
    print(number)
  
  ```
  
  При запуске такого теста, мы увидим, что у нас запустилось 5 тестов, и в каждом отображается значение нашего параметра, а также наш принт. Мы видим, что он тоже меняется, так как через него мы выводим значение параметра.
  
  `Пример 1:` Перепишем наши тесты с прошлого урока в один.
  
  ```
  import pytest

from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager


class TestPages: # Название тестового класса

    def setup(self):
        self.service = Service(ChromeDriverManager().install())
        self.driver = webdriver.Chrome(service=self.service)
	   
    def test_open_login_page(self):
        self.driver.get("https://demoqa.com/login")
        assert self.driver.current_url == "https://demoqa.com/login", "Ошибка"
		
    def test_open_books_page(self):
        self.driver.get("https://demoqa.com/books")
        assert self.driver.current_url == "https://demoqa.com/books", "Ошибка"
		
    def test_open_profile_page(self):
        self.driver.get("https://demoqa.com/profile")
        assert self.driver.current_url == "https://demoqa.com/profile", "Ошибка"

    def teardown(self):
        self.driver.close()
  
  ```
  
  Мы видим, что в наших тестах меняются ссылки для открытия и проверки, соответственно, мы без проблем можем написать один параметризированный тест, в котором обозначим следующее:

- Тест назовем просто `test_open_page()`
- `имя_параметра` у нас будет `domain`
- в качестве значений будем прокидывать наши ссылки

## Шаг 1 - пропишем декоратор параметризации:
  
  ```
  @pytest.mark.parametrize(
	"domain", 
	[
	  "https://demoqa.com/login", # domain для первого теста
	  "https://demoqa.com/books", # domain для второго теста
	  "https://demoqa.com/profile" # domain для третьего теста
	]
)
  
  ```
  ## Шаг 2 - Прокинем имя параметра в тест
  
  ```
  import pytest

from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

class TestPages:

    def setup(self):
        self.service = Service(ChromeDriverManager().install())
        self.driver = webdriver.Chrome(service=self.service)

    @pytest.mark.parametrize(
        "domain",
        [
            "https://demoqa.com/login", # URL для первого теста
            "https://demoqa.com/books", # URL для второго теста
            "https://demoqa.com/profile" # URL для третьего теста
        ]
    )
    def test_open_page(self, domain): # Прокидываем URL в тест
        self.driver.get(domain)
        assert self.driver.current_url == domain, "Ошибка"

    def teardown(self):
        self.driver.quit()
  
  ```
  Готово, как итог у нас очень сильно сократился код и у нас все так же 3 теста.
  
  
