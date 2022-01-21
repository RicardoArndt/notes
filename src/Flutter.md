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
