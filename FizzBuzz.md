Lets take a look at the FizzBuzz problem while exploring some of the tradeoffs involved in developing applications.
We are going to look at a few different implementations of FizzBuzz, each has been designed with different purpose. We will also explore the effect of each implementation.

## Simple Fizz Buzz
```csharp
for (int i = 0; i < 100; i++) {
    if (0 == i % 3 || 0 == i % 5) {
        Console.WriteLine("FizzBuzz");
    }
    else if (0 == i % 3) {
        Console.WriteLine("Fizz");
    }
    else if (0 == i % 5) {
        Console.WriteLine("Buzz");
    }
    else {
        Console.WriteLine(i);
    }
}
```

## Reduce Duplicate Code
```csharp
const string FIZZ = "Fizz";
const string BUZZ = "Buzz";

public static void FizzBuzz() {
    for (int i = 0; i < 100; i++) {
        bool isFizz = (0 == i % 3);
        bool isBuzz = (0 == i % 5);
        string result = string.Empty;

        result += isFizz ? FIZZ : string.Empty;
        result += isBuzz ? BUZZ : string.Empty;
        result += !isFizz && !isBuzz ? i.ToString() : string.Empty;

        Console.WriteLine(result);
    }
}
```

## Parameterized
```csharp
public static void FizzBuzz(IEnumerable<int> range, IEnumerable<Tuple<int, string>> triggers) {
    foreach (int i in range) {
        string result = string.Empty;
        foreach (var trigger in triggers) {
            result += (0 == i % trigger.Item1) ? trigger.Item2 : string.Empty;
        }
        result = string.IsNullOrEmpty(result) ? i.ToString() : result;
        Console.WriteLine(result);
    }
}

FizzBuzz(
    Enumerable.Range(1, 100), 
    new Tuple<int, string>[] {
        Tuple.Create<int, string>(3, "Fizz"),
        Tuple.Create<int, string>(5, "Buzz")
    }
);
```

## Predicates
```csharp
public static void FizzBuzz(IEnumerable<int> range, IEnumerable<Tuple<Predicate<int>, string>> triggers) {
    foreach (int i in range) {
        string result = string.Empty;
        foreach (var trigger in triggers) {
            result += trigger.Item1(i) ? trigger.Item2 : string.Empty;
        }
        result = string.IsNullOrEmpty(result) ? i.ToString() : result;
        Console.WriteLine(result);
    }
}

FizzBuzz(
    Enumerable.Range(1, 100),
    new Tuple<Predicate<int>, string>[] {
        Tuple.Create<Predicate<int>, string>(i => 0 == i%3, "Fizz"),
        Tuple.Create<Predicate<int>, string>(i => 0 == i%5, "Buzz")
    }
);
```

## Lazy Evaluation
```csharp
public static IEnumerable<string> FizzBuzz(IEnumerable<int> range, IEnumerable<Tuple<Predicate<int>, string>> triggers) {
    return range.Select(i => {
        var parts = triggers.Select(t => t.Item1(i) ? t.Item2 : string.Empty);
        var text = string.Join(string.Empty, parts);
        return string.IsNullOrEmpty(text) ? i.ToString() : text;
    });
}

var result = FizzBuzz(
    Enumerable.Range(1, 100),
    new Tuple<Predicate<int>, string>[] {
        Tuple.Create<Predicate<int>, string>(i => 0 == i%3, "Fizz"),
        Tuple.Create<Predicate<int>, string>(i => 0 == i%5, "Buzz")
    }
);
foreach(var line in result) { Console.WriteLine(line); }
```
