# Домашнее задание к занятию 4. «Тестирование. Unit-тесты»

## Правила выполнения домашней работы

* Домашнее задание выполняется на базе проекта, над которым вы работали в предыдущем модуле. 
* В поле для сдачи домашней работы прикрепите ссылку на Pull Request проекта в репозитории на Git.
* Задачи со звёздочкой (*) решать не обязательно.
* Любые вопросы по решению задач задавайте в чате учебной группы.

## Задача 1
1. Реализуйте на базе проекта **Navigation** unit-тесты для логики в FeedViewModel и LoginViewModel.
2. Создайте отдельный класс в тестовом таргете для каждой из модели с именами _FeedViewModelTests_ и _LoginViewModelTests_.
3. В каждом классе должно быть:
- либо по **одному** тесту на **каждый** публичный метод у соответствующей ViewModel, если таких методов несколько;
- либо **3** теста, проверяющих разные варианты работы метода, если такой метод **единственный**.
4. В приложении должно быть минимум три теста, все тесты должны успешно проходить.

Помните: если у вас не получается написать тест на каждую строчку и проверить абсолютно всё — это нормально. Попробуйте придумать обходной путь, который проверит хотя бы основную часть работы.

##### Пример:
```swift
func updateState(viewInput: ViewInput, text: String) {
        switch viewInput {
        case .checkButtonDidTap:
            state = .checking
            FeedModel().check(word: text) { result in
                switch result {
                case .success(true):
                    self.state = .checked(result: true)
                case .success(false):
                    ()
                case .failure(.invalidText):
                    print("Invalid text")
                    self.state = .error(error: .invalidText)
                case .failure(.emptyText):
                    print("Empty text")
                    self.state = .error(error: .emptyText)
                }
            }
        case .pushButtonDidTap:
            ()
        }
    }
```

Для этого метода набор unit-тестов должен быть составлен так, чтобы каждый из них проверял одно из возможных конечных состояний _self.state_. В сумме с помощью тестов должны быть проверены все состояния.
Состояние _self.state_ изменится после того, как выполнится _FeedModel().check(word: text) { ... }_, над которым у вас нет контроля. Поэтому для этого метода нужно отказаться от использования _FeedModel()_ напрямую и закрыть её протоколом, например _FeedModelProtocol_. Это позволит нам в тестах передать фейковый класс:

``` swift
class FeedModelMock: FeedModelProtocol {
    var fakeResult:  Result<Bool, Error>!
    func check(word: String, completion: (Result<Bool, Error>) -> Void) {
        completion(fakeResult)
    }
}
```
Теперь в unit-тесте мы можем настроить проперти _fakeResult_ и влиять на переменную _self.state_:

fakeResult = .success(true) -> XCTAssertEqual(viewModel.state, .checked(result: true)).

## Задача 2* (со звёздочкой)

Напишите unit-тесты для класса _NetworkService_. Помните, что ваши тесты должны быть отвязаны от настоящих запросов в сеть. Поэтому в этом классе нужно отказаться от использования _URLSession.dataTask()_  напрямую и вынести обращение к ней и получение от неё **response/data/error** в отдельный класс, закрытый протоколом. В тестах соответствующий этому протоколу фейковый класс должен возвращать разные виды результата: положительный/отрицательный/пустой, а вы должны проверить, как _NetworkService_ их обрабатывает.



