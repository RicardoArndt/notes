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
- Widget test
    - Kind of tests made on Flutter widgets. They check if a widget is present in the widget tree or how many time does it apperar.
- Integration test
    - Kind of tests in which different parts of the code ate combined and tested together as a group.

### Unit

For the pure test without test a widget we can use a framework called "test" by adding the dev dependency, ex:
```yaml
dev_dependencies:
    test: version_here
```

**Pattern to create files for unit test**

- name_*test.dart* in folder /test/unit

**Command to run unit test**
- *flutter test test/name_test.dart* 
    - options
        - *--coverage <dir>*

**The test always involved by function *void main() {}***

**Composite structure of file test**

- *void test() {}* 
    - This function used to describe the function which it will be tested

- *void expect(a, b)*

- *void group()*
    - Generally, a class has more than a single method to be tested. Using the group() function.

- *void allOf()* 
    - Match a multiple cases. Ex:

```dart
expect('A short string', allOf([
    contains('rt'),
    startsWith('A '),
    endsWith('.')
]))
```

**Handling errors**

```dart
expect(() => Function(), throwsA(isA<ExceptionClass>()));
```

When is common exception you can use the shortcut, equivalent:
```dart
expect(() => { ... }, throwsException);
```

**Testing asynchronous code**

```dart
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

```dart
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
```dart
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
```dart
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

```dart
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

```dart
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

```dart
void main() {
    group('CounterBloc', () {
        blocTest(...);

        blocTest(...);
        
        blocTest(...);
    });
}
```

### Widget

Testing Flutter widgets requires the same process you've seen in unit testing with the addition of some new techniques brought by the built-in *flutter_test* package.

Widget tests are meant to check whether one or more widgets have been properly put in the widget tree.

```dart
void main() {
    testWidgets('Testing if Myself has name and age', (WidgetTester tester) async {
        await tester.pumpWidget(MySelf('Ricardo', '25'));

        final CommonFinders name = find.text('Ricardo');
        final CommonFinders age = find.text('25');

        expect(name, findsWigets);
        expect(age, findsOneWidget);
    });
}
```

**Asserts**
- findsNothing:
    - asserts that the finder has found no widgets in the tree;
- findsOneWidget:
    - asserts that the finder has found exactly one widget in the tree;
- findsWidgets:
    - asserts that the finder has found one or more widgets in the tree;
- findsNWiget:
    - asserts that the finder has found exactly N widget in the tree; 
    - where N is a value defined by you.

**Finders**
- find.text(...):
    - you'll often have the need to look for the presence of a Text widget and this method does exactly that;
- find.byWidget(...):
    - it's useful when you want to look for a specific widget in the tree making sure it appears on the screen a certain number of time.
    - **Example**:

```dart
final target = Icon(Icons.error);

await tester.pumpWidget(
    Center(
        child: Padding(
            padding: const EdgeInsets.all(10.0),
            child: target,
        )
    )
);

final finder = find.byWidget(target);

expect(finder, findsOneWidget);
```

- find.byType(...): 
    - looks for widgets of a particular type. It can be used in the following way:

```dart
expect(find.byType(IconButton), findsWigets);
```

Suppose you had to test a StatefulWidget which has a series of animations inside. Other than making sure certain widgets are appearing to the UI, you could also want to test its performances:

```dart
await tester.pumpWidget(MyWidget());
await tester.pump(Duration(milliseconds: 100));
```

**Testing blocs and providers**
It might happen that a particular part of the widget tree you want to test depends on one or more providers of any kind. There's nothing special to do when it comes to testing as just need to normally wrap the widget in the provider you need.

```dart
await tester.pumpWidget(
    Provider<Something>(
        child: MyWidget(),
    ),
);
```

Multiple providers
```dart
await tester.pumpWidget(
    MultiProvider(
        providers: [...],
        child: MyWidget(),
    ),
);
```

*Mock bloc*

```dart
class MockCounterBloc extends MockBloc<int> implements CounterBloc {}

void main() {
    final counterBloc = MockCounterBloc();

    whenListen(counterBloc, Stream.fromIterable([0, 1, 2]));

    await tester.pumpWidget(
        BlocProvider<CounterBloc>.value(
            value: counterBloc,
            child: MyWidget(),
        )
    );

    expect(counterBloc.state, equals(2));
}
```

Basically before using pumpWidget() you need to create a "fake bloc"using a mock, similarly to what you'd do with a fake HTTP client. In this way there;s the possibility to send a series of states we decide just by using whenListen(). It's also possible making sure that states are emitted in a certain order:

```dart
// Making sure the state is correct
expect(counterBloc.state, equals(2));

// Making sure the stream emits certain values
await expectLater(counterBloc, emitsInOrder(<int>[1, 2]));
```

The first case just makes sure that the latest state equals 2 while the second one ensures that the entire "emission flow" is correct.

### Integration

Package flutter_driver

This kind of test gathers together both the UI and the business logic so that you can test the app as a whole. With integration tests you're able to verify how the visual components and the code behave together; the app runs on a device (or simulator) and it's told to do certain things automatically, such as pressing on buttons.

For integration test you can create new folder named test_driver located at the root of your project (same level as lib folder).

Create file at test_driver folder named app.dart

```dart
void main() {
    enableFlutterDriverExtension();

    runApp(CounterApp());
}
```

Implements the test, file app_test.dart

```dart
void main() {
    group('Counter App test', () {
        final counterText = find.byValueKey('counter');
        final incrementButton = find.byValueKey('increment');
        final decrementButton = find.byValueKey('decrement');

        late final FlutterDriver driver;

        // Is called before any test starts
        setUpAll(() async {
            driver = await FlutterDriver.connect();
        });

        // Is called before any test ends
        tearDownAll(() async {
            driver.close();
        });

        test('Counter increment', () async {
            await driver.tap(incrementButton);

            var readText = await driver.getText(counterText);

            expect(readText, "1");
        });

        test('Counter decrement', () async {
            await driver.tap(decrementButton);

            var readText = await driver.getText(counterText);

            expect(readText, "0");
        });

        //Example enters a text in input field
        test('Enters a text', () {
            final textField = find.byValueKey('itsName');

            await driver.setTextEntryEmulation(enabled: true);
            await driver.tap(textField);
            await driver.enterText('Flutter is...');
            await driver.enterText('Awesome!');
        });
    });
}
```

**Utils methods**

- enterText:
    - Enters the given text in an input field like if the user were tapping on the keyboard;
- getText:
    - Reads the text from a Text widget;
- scroll:
    - The driver simulates a finger scrolling on a list. It's possible to also set the direction and the duration.
- screenshot:
    - Takes a screenshot of the page and stores it in a PNG file.

**Command**

```bash
flutter driver --target=test_driver/app.dart
```

## StatefullWidget vs StatelessWidget

- StatelessWidget:
    - Use this kind of widget when you need to create a piece of UI that is not going to change over the time. It's a standalone block that doesn't depend on external events or sources, it just relies on its constructor and the internal data.
- StatefullWidget:
    - Use this kind of widget when you need to create a piece of UI that is going to change over the time. In this case the UI is going to dynamically change due to external events such as the received of an HTTP request or the callback triggered by a button tap.

## StatefullWidget

```dart
class Counter extends StatefulWidget {
    // Dont forget const constructor
    const Counter();

    @override
    _CounterState createState() => _CounterState();
}

// Undescore names is private member
class _CounterState extends State<Counter> {
    int _counter = 0;

    @override
    Widget build(BuildContext context) {
        return Column(
            ...
        );
    }
```

- Counter is the widget (what's inserted on the tree) while _CounterState is the state. 
- The state survives to rebuilds but its **build()** method doesn't.

**Props**

- If you used the constructor of a StatefulWidget to set some data, the associated State<T> class can get a reference to them by simply using the widget getter. Again, try to use const as much as possible.

```dart
class WidgetDemo extends StatefulWidget {
    final int id;
    const WidgetDemo(this.id);

    @override
    _WidgetDemoState createState() => _WidgetDemoState();
}

class _WidgetDemoState extends State<WidgetDemo> {
    @override
    Widget build(BuildContext context) {
        return Text('The given id is ${widget.id}');
    }
}
```

## Forms

Creating form text field with padding, example:

```dart
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

## Firebase

**Installation**

- Install packages pub

```yaml
firebase_core: "1.11.0"
cloud_firestore: "3.1.6"
```

- Create firebase account
- Configure android/build.gradle
    - Add 
        ```gradle
        dependencies {
            ...
            classpath 'com.google.gms:google-services:4.3.10'
        }
        ```
- Configure android/app/build.gradle
    - Add 
        ```gradle
        apply plugin: 'com.google.gms.google-services'
        ```
- Add file google-services.json to android/app folder
- If error minSdkVersion
    - Change android/app/build.gradle
        - minSdkVersion localProperties.getProperty('flutter.minSdkVersion').toInteger()
    - Add local.properties
        - flutter.minSdkVersion=19

**Using Firestore**

```dart
import "package:cloud_firestore/cloud_firestore.dart";

class TodoDAO {
    Future<List<Todo>> getTodos() async {
        QuerySnapshot snapshot = await Firestore.instance.collection("todos").getDocuments();

        AllTodos todos = AllTodos.fromSnapshot(snapshot);

        return todos;
    }
}
```

*Snapshot*
- This is the data you wanted in the moment that you asked for it.

*QuerySnapshot*
- Is a class that represents some data from the database at any given moment.

*Instance*
- Is a static getter on the firestore package that represents the database.

*Collectioon*
- Is a method that retrieves a collection from your database.
- "todos" is the path, and "todos/$id" is specific todo.

## Dependency Injection

*Simple package*: injector.dart

## Publishing the app

**Android**
- Create developer account on google $25 (once)
- Adding a launcher icon
- Create an upload keystore
- Reference the keystore from the app
- Configure signing in gradle
- Run *flutter build appbundle*
- Send apk to the console mode https://play.google.com/
- Create version for internal test (Adding emails to list)

For more details visit the page: https://docs.flutter.dev/deployment/android

