# Dart/Flutter

## Variables

### Implicitly type:
```dart
    var value = 1;
```

### Explicitly type:
```dart
    int value = 1;
    String value = “2”;
```

### Late keyword is used to initialize a variable late:
```dart
    late List<int> numbers;

    if (true) {
        numbers = List<int>();
    }
```

### Final keyword is used to initialize a var not changed during method execution
```dart
    final var number = 1;

    //doesn`t work
    number = 1;
```

### Numbers

#### Has two types of numbers

- int
- double

Boths are subclasses of num
