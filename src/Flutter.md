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

Pattern to create files for unit test

name_**test.dart** in folder /test/unit

The test always involved by function **void main() {}**

Composite structure of file test

**void test() {}** -> This function used to describe the function which it will be tested
... will be continued

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
