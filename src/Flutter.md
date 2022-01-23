# Flutter

## Structure

lib/ -> Source code of app.
- localizations/  -> Grouping localizations when app is for multiples languages
- routes/         -> Contains apps pages
- models/         -> Is for the "business logic"
  - daos/
  - repositories/
  - services/
  - etc...
- widgets/        -> Reusable UI Widgets
- main.dart
- routes.dart

## Testing Apps

### Types of testing flutter apps

- Unit test
- Widget test -> Kind of tests made on Flutter widgets. They check if a widget is present in the widget tree or how many time does it apperar.
- Integration test -> Kind of tests in which different parts of the code ate combined and tested together as a group.

### Unit

For the pure test without test a widget we can use a framework called "test" by adding the dev dependency, ex:
```yaml
dev_dependencies:
    test: version_here
```

**Pattern to create files for unit test**

- name_*test.dart* in folder /test/unit

**Command to run unit test**
- *flutter test test/name_test.dart* -> Command to run unit test to specific file.
    - options
        - *--coverage <dir>*

**The test always involved by function *void main() {}***

**Composite structure of file test**

- *void test() {}* -> This function used to describe the function which it will be tested

- *void expect(a, b)*

- *void group()* -> Generally, a class has more than a single method to be tested. Using the group() function.

- *void allOf()* -> Match a multiple cases. Ex:

```test
expect('A short string', allOf([
    contains('rt'),
    startsWith('A '),
    endsWith('.')
]))
```

**Handling errors**

```test
expect(() => Function(), throwsA(isA<ExceptionClass>()));
```

When is common exception you can use the shortcut, equivalent:
```test
expect(() => { ... }, throwsException);
```

**Testing asynchronous code**

```test
void main() {
    test('The unit test async', () async {
        final value = await Future<int>.value(25);

        expect(value, equals(25));
    });
}
```

**Mocking dependencies**

The official package to create mocks is named mockito.
```yaml
dev_dependencies:
    test: version_here
    mockito: version_here
```

```test
class HTTPMock extends Mock implements http.Client {}

void main() {
    test('Returns JSON in case of success', () async {
        final url = 'url';
        final mock = HTTPMock();
        final fakeJson = '[{}]';

        when(mock.get(url).whenComplete(() {
            return http.Response(fakeJson, 200);
        }));

        expect(
            await todo.getJson(mock),
            fakeJson
        );
    });
}
```

**Unit test blocs**
State management libraries require to be unit tested like any other logic implemented in the app and thus flutter_bloc is no exception. bloc_test is the package used to verify the bahavior of your blocs and cubits couldn't be easier.
Example:

It just expects an event of type increase or decrease and returns an integer.
```test
void main() {
    blocTest(
        'Emits [1] when increment is added',
        build: () => CounterBloc(),
        act: (bloc) => bloc.add(CounterEvent.increment),
        expect: [1],
    );
}
```

With act you can send events to the bloc being created in *build* and *expect()* is a list of expected emitted states. Of course there's the possibility to send multiple events and look for multiple results.
```test
void main() {
    blocTest(
        'Emits [1, 2, 1] when 2 increments and 1 decrement are added',
        build: () => CounterBloc(),
        act: (bloc) => bloc
            ..add(CounterEvent.increment)
            ..add(CounterEvent.increment)
            ..add(CounterEvent.decrement),
        expect: [1, 2, 1],
    );
}
```

Setting the optional skip parameters allows you to ignore a certain number of states. By default skip: 0 is set and the initial state is excluded from the expect list. Does your bloc await somewhere and thus you need to wait some time?

```test
void main() {
    blocTest(
        'Emits [1, 2, 1] when 2 increments and 1 decrement are added',
        build: () => CounterBloc(),
        wait: const Duration(seconds: 2),
        act: (bloc) => bloc
            ..add(CounterEvent.increment)
            ..add(CounterEvent.increment)
            ..add(CounterEvent.decrement),
        expect: [1, 2, 1],
    );
}
```
In this way, when act sends an event to the bloc, the emitted state is returned after the given time span (which is 2 seconds in the example). This is very useful when you need to wait, for example, when debouncing events.

```test
void main() {
    blocTest(
        'Description',
        build: () => CounterBloc(),
        wait: const Duration(seconds: 2),
        act: (bloc) => bloc.add(Something())
        expect: [0],
        errors: [
            isA<Exception>(),
        ]
    );
}
```
The errors parameter is used to catch exceptions, generally together with isA<T> to exactly match the type. Of course, bloc tests can be grouped like any other unit test.

```test
void main() {
    group('CounterBloc', () {
        blocTest(...);

        blocTest(...);
        
        blocTest(...);
    });
}
```

//TODO
### Widget

//TODO
### Integration

## Forms

To create new text form field use Padding first, ex

```flutter
Padding(
  padding: const EdgeInsets.symmetric(horizontal: 8, vertical: 16),
  child: TextFormField(
    decoration: const InputDecoration(
      border: OutlineInputBorder(),
      labelText: 'Description',
      hintText: 'Enter a description'
    ),
    validator: (value) {
      if (value == null || value.isEmpty) {
        return 'Please enter some description';
      }
      return null;
    },
  ),
)
```
