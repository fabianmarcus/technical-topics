# Go

Einarbeitung für ein mögliches Projekt.

## Erkenntnisse / Good to Know

- In Go wird nicht standardmäßig mit Konstanten gearbeitet. Es gibt zwar das `const`-Schlüsselwort, das wird aber nicht so häufig genutzt wie in anderen Sprachen. Stattdessen werden Variablen mit dem `var`-Schlüsselwort (oder in Short-Notation) deklariert und können zur Laufzeit geändert werden.
- Statt `null` (oder `undefined`) gibt es `nil`. `nil` kann für Pointer, Slices, Maps, Channels und Funktionen verwendet werden. Es ist der Standardwert für diese Typen, wenn sie nicht initialisiert wurden. Alle anderen Typen haben immer einen Standardwert, z.B. `0` für Zahlen, `false` für Booleans und `""` für Strings.
- Ein Objekt ist in Go ein Struct. Es gibt keine Klassen, Vererbung oder Interfaces wie in anderen Sprachen. Stattdessen werden Structs verwendet, um Daten zu gruppieren, und Interfaces, um Verhalten zu definieren.
- In Go wird über Groß- und Kleinschreibung entschieden, ob ein Feld oder eine Funktion eines Moduls exportiert ist oder nicht. Kleiner Anfangsbuchstabe im Namen bedeutet nicht exportiert, großer Anfangsbuchstabe bedeutet exportiert.
- In Go werden keine Einzelheiten eines Moduls importiert wie bei Typescript, sondern immer das ganze Modul bzw. Paket. Der Compiler regelt selbst, was benötigt wird und was nicht.
- Es sind Zuweisungen innerhalb der if- und for-Bedingungen möglich. Dadurch lässt sich eine Variable deklarieren und gleichzeitig prüfen, ob sie einen bestimmten Wert hat.
- Alles muss explizit importiert werden, auch Standardpakete wie `fmt` für Stringoperationen. Es gibt keine globalen Objekte wie `console` oder `Object`.
- JSON wird nicht so nativ unterstützt wie in Typescript.
- In Go können Mehrfachzuweisungen und Mehrfachrückgaben genutzt werden (siehe unten). Dadurch lassen sich mehrere Variablen gleichzeitig deklarieren und initialisieren, oder mehrere Werte aus einer Funktion zurückgeben.
- In Go wird regelmäßig mit Pointern gearbeitet. In Typescript ist es zwar auch möglich, "by reference" zu arbeiten, das kommt aber nur in Ausnahmefällen vor. In Go ist es die Regel.
- Go ist Multi-Threaded und bringt dadurch einige Problemstellungen mit sich, die in Single-Threaded-Sprachen wie Typescript nicht auftreten. Das fängt schon beim Zugriff auf eine einfache Map an.

## Syntax-Beispiele

### Zuweisungen

```go
package main

import "fmt"

type User struct {
    ID   int
    Name string
    Age  int
}

func double(x int) int {
    return x * 2
}

func main() {
    // 1) Grundtypen
    var s string = "Hallo"
    var i int = 42
    var f float64 = 3.14
    var b bool = true
    var r rune = 'A'
    var by byte = 255
    var c complex128 = 2 + 3i

    // 2) Kurzzuweisung (nur in Funktionen)
    x := 10
    y := "Go"

    // 3) Mehrfachzuweisung
    a, z := 1, 2
    a, z = z, a // Swap

    // 4) Konstanten
    const appName string = "go-test"
    const maxRetries = 3

    // 5) Struct-Zuweisung
    u1 := User{ID: 1, Name: "Mara", Age: 30}
    var u2 User
    u2.ID = 2
    u2.Name = "Timo"
    u2.Age = 25

    // 6) Array
    var arr1 [3]int = [3]int{1, 2, 3}
    arr2 := [...]string{"a", "b", "c"}

    // 7) Slice
    var sl1 []int = []int{10, 20, 30}
    sl2 := []string{"go", "ts"}
    sl2 = append(sl2, "rust")

    // 8) Map
    var m1 map[string]int = map[string]int{"alice": 1}
    m2 := map[string]string{"de": "Hallo", "en": "Hello"}
    m1["bob"] = 2
    m2["fr"] = "Bonjour"

    // 9) Pointer
    n := 99
    p := &n      // p zeigt auf n
    *p = 100     // Wert von n über Pointer ändern
    var pn *int  // nil-Pointer
    pn = &n

    // 10) Interface / any
    var v any
    v = "text"
    v = 123
    v = User{ID: 3, Name: "Lea", Age: 28}

    // 11) Funktion als Wert
    var fn func(int) int
    fn = double
    result := fn(21)

    // 12) Channel
    ch := make(chan int, 1)
    ch <- 7
    got := <-ch

    // 13) nil-fähige Typen
    var ps *string = nil
    var ss []int = nil
    var mm map[string]int = nil
    var chNil chan int = nil
    var fnNil func() = nil
    _ = ps
    _ = ss
    _ = mm
    _ = chNil
    _ = fnNil

    // 14) Zusammengesetzte Zuweisungsoperatoren
    counter := 10
    counter += 5
    counter -= 2
    counter *= 3
    counter /= 2
    counter %= 4

    fmt.Println(s, i, f, b, r, by, c, x, y, a, z, appName, maxRetries)
    fmt.Println(u1, u2, arr1, arr2, sl1, sl2, m1, m2, n, p, v, result, got, counter)
}
```

### If-Bedingung inkl. Zuweisung

```go
if err := decoder.Decode(body); err != nil {
    errorResponse := ResponseError{
        Code:    "invalid_request",
        Message: "Invalid JSON body",
    }
    res.WriteHeader(http.StatusBadRequest)
    encoder.Encode(errorResponse)
    return
}
```

### Mehrfachzuweisung und -rückgabe

```go
var shorts = map[string]string{}

func LookupCode(code string) (string, error) {
    if url, ok := shorts[code]; ok {
        return url, nil
    }
    return "", fmt.Errorf("code not found")
}
```

### Imports

Der Name des importierten Ordners bzw. das importierten Pakets muss eindeutig sein. Um zwei gleichnamige Pakete zu importieren, können Aliase für importierte Pakete vergeben werden:

```go
import (
    "fmt"
    mymath "math" // Alias für das Paket "math"
)
```
