---
title: Oparte na danych wyrażenia stylu w zestawie SDK sieci Web mapy platformy Azure | Dokumentacja firmy Microsoft
description: Jak używać wyrażeń styl opartych na danych w zestawie SDK sieci Web mapy platformy Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 0f63ed7d00f1ae3e30cdac76606559a4e9f49f04
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288099"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Styl opartych na danych wyrażeń (zestaw SDK sieci Web)

Wyrażenia umożliwia stosowanie logiki biznesowej do odnalezienia stylów, sprawdź właściwości zdefiniowane w każdy kształt w źródle danych. Wyrażenia można również filtrować dane w źródle danych lub warstwy. Wyrażenia może składać się z logikę warunkową, takich jak instrukcji if i może również służyć do manipulowania danymi. ciąg, operatory logiczne i matematyczne. 

Style opartych na danych może zmniejszyć ilość kodu potrzebne do zaimplementowania logikę biznesową wokół stylów. W przypadku użycia z warstwami, wyrażenia są obliczane w czasie renderowania w oddzielnym wątku, który zapewnia większą wydajność w porównaniu do oceniania logikę biznesową w wątku interfejsu użytkownika.

Poniższy klip wideo zawiera omówienie stylów opartych na danych w zestawie SDK sieci Web mapy usługi Azure.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Wyrażenia są reprezentowane jako tablice w formacie JSON. Pierwszy element wyrażenia w tablicy jest ciągiem, który określa nazwę operator wyrażenia. Na przykład "+" lub "case". Dalej elementy (jeśli istnieje) są argumentów do wyrażenia. Każdy argument jest albo wartość literału (ciąg, liczba, wartość logiczna, lub `null`), lub innej tablicy wyrażenia. Poniższym pseudokodzie definiuje podstawowa struktura wyrażenia. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Zestaw SDK usługi Azure Maps w sieci Web obsługuje wiele typów wyrażeń, które mogą być używane samodzielnie lub w połączeniu z innymi wyrażeniami.

| Typ wyrażenia | Opis |
|---------------------|-------------|
| [Wyrażenia logiczne](#boolean-expressions) | Wyrażenia logiczne zapewniają zestaw operatorów logicznych wyrażeń do oceny logiczna porównania. |
| [Wyrażenie koloru](#color-expressions) | Kolor wyrażenia ułatwienia tworzenia i manipulowania wartości kolorów. |
| [Wyrażenia warunkowe](#conditional-expressions) | Wyrażenia warunkowe zapewniają operacje logiki, które są podobne do instrukcji if. |
| [Wyrażenia danych](#data-expressions) | Zapewnia dostęp do danych właściwości w funkcji. |
| [Interpolacji i krok wyrażeń](#interpolate-and-step-expressions) | Interpolacji i wyrażeń kroku może służyć do obliczania wartości wzdłuż funkcję interpolowane krzywej lub krok. |
| [Wyrażenia określone warstwy](#layer-specific-expressions) | Specjalne wyrażeń, które dotyczą tylko jedną warstwę. |
| [Wyrażenia matematyczne](#math-expressions) | Zawiera operatory matematyczne do obliczeń sterowanych danymi w ramach wyrażenia. |
| [Wyrażenie operatora ciągu](#string-operator-expressions) | Wyrażenie operatora ciągu wykonują operacje konwersji na ciągi, takie jak łączenie i konwertowanie wielkości liter. |
| [Wyrażenia typu](#type-expressions) | Wyrażenia typu oferuje narzędzia do testowania i konwertowanie różnych typów danych, takich jak ciągi, liczby i wartości logiczne. |
| [Wyrażenia wiązania zmiennej](#variable-binding-expressions) | Wyrażenia wiązania zmiennej pozwalają wyników obliczeń jest przechowywana w zmiennej i odwoływać się gdzie indziej w wyrażeniu wiele razy bez konieczności ponownego obliczenia przechowywaną wartość. |
| [Wyrażenie powiększenia](#zoom-expression) | Pobiera bieżący poziom powiększenia mapy w czasie renderowania. |

Wszystkie przykłady w niniejszym dokumencie użyje następującą funkcję do przedstawiono różne sposoby, w tym, że można użyć różnych typów wyrażeń. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Wyrażenia danych

Wyrażenia danych zapewniają dostęp do danych właściwości w funkcji. 

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['at', number, array]` | obiekt | Pobiera element z tablicy. |
| `['geometry-type']` | string | Pobiera typ geometrii funkcji: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | wartość | Pobiera wartość właściwości przy użyciu właściwości bieżącej funkcji. Zwraca wartość null, jeśli brakuje wymaganej właściwości. |
| `['get', string, object]` | wartość | Pobiera wartość właściwości z właściwości podanych obiektu. Zwraca wartość null, jeśli brakuje wymaganej właściwości. |
| `['has', string]` | wartość logiczna | Określa, jeśli właściwości funkcji mają określoną właściwość. |
| `['has', string, object]` | wartość logiczna | Określa, jeśli właściwości obiektu mają określoną właściwość. |
| `['id']` | wartość | Pobiera identyfikator funkcji, jeśli taki istnieje. |
| `['length', string | array]` | numer | Pobiera długość ciągu lub tablicy. |

**Przykłady**

Właściwości funkcji można uzyskać bezpośrednio w wyrażeniu przy użyciu `get` wyrażenia. W poniższym przykładzie użyto wartości "zoneColor" funkcji określać właściwości kolorów bąbelków warstwy. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Powyższy przykład będzie działać prawidłowo, jeśli wszystkie funkcje punktu `zoneColor` właściwości, ale jeśli nie, kolor prawdopodobnie powróci do "black". Do modyfikowania rezerwowego koloru `case` wyrażenie mogą być używane w połączeniu z `has` wyrażenia, aby sprawdzić, jeśli właściwość istnieje i nie zwraca kolor zamiast tego.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Bąbelkowy i symbol warstwy współrzędne wszystkie kształty w źródle danych spowoduje, że domyślnie. Można to zrobić, aby wyróżnić wierzchołki wielokąta lub wiersza. `filter` Opcji warstwy może służyć do ograniczenia typów funkcji renderowania przy użyciu geometrii `['geometry-type']` wyrażenia w wyrażeniu logicznym. Poniższy przykład ogranicza warstwy bąbelek, aby tylko `Point` funkcje są renderowane.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Poniższy przykład umożliwia zarówno `Point` i `MultiPoint` funkcji do renderowania. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Podobnie konspektu wielokątów będą renderowane w warstwach wiersza. Aby wyłączyć to zachowanie w warstwie wiersza, Dodaj filtr, który dopuszcza tylko `LineString` i `MultiLineString` funkcji.  

## <a name="math-expressions"></a>Wyrażenia matematyczne

Wyrażenia matematyczne udostępniają operatory matematyczne do obliczeń sterowanych danymi w ramach wyrażenia.

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['+', number, number, …]` | numer | Oblicza sumę podanych liczb. |
| `['-', number]` | numer | Odejmuje 0 przez określoną liczbę. |
| `['-', number, number]` | numer | Odejmuje pierwszy numery przy drugą liczbę. |
| `['*', number, number, …]` | numer | Mnoży razem podanych liczb. |
| `['/', number, number]` | numer | Dzieli pierwsza liczba przy drugą liczbę. |
| `['%', number, number]` | numer | Oblicza resztę w przypadku dzielenia pierwsza liczba przy drugą liczbę. |
| `['^', number, number]` | numer | Oblicza wartość pierwszej wartości podniesioną do potęgi druga liczba. |
| `['abs', number]` | numer | Oblicza wartość bezwzględną określoną liczbę. |
| `['acos', number]` | numer | Oblicza arcus cosinus określoną liczbę. |
| `['asin', number]` | numer | Oblicza arcus sinus określoną liczbę. |
| `['atan', number]` | numer | Oblicza arcus tangens określoną liczbę. |
| `['ceil', number]` | numer | Zaokrągla liczbę w górę dalej całkowitą. |
| `['cos', number]` | numer | Oblicza cos logarytmu określonej liczby. |
| `['e']` | numer | Stałe matematyczne zwraca `e`. |
| `['floor', number]` | numer | Zaokrągla liczbę w dół do poprzedniej całkowitą. |
| `['ln', number]` | numer | Oblicza logarytm naturalny określoną liczbę. |
| `['ln2']` | numer | Stałe matematyczne zwraca `ln(2)`. |
| `['log10', number]` | numer | Oblicza base 10 logarytmu określonej liczby. |
| `['log2', number]` | numer | Oblicza logarytm dwóch base określoną liczbę. |
| `['max', number, number, …]` | numer | Oblicza maksymalną liczbę w określonym zestawie liczb. |
| `['min', number, number, …]` | numer | Oblicza minimalną w określonym zestawie liczb. |
| `['pi']` | numer | Stałe matematyczne zwraca `PI`. |
| `['round', number]` | numer | Zaokrągla liczbę do najbliższej liczby całkowitej. Wartości połowiczne są zaokrąglane dalej od zera. Na przykład `['round', -1.5]` daje w wyniku -2. |
| `['sin', number]` | numer | Oblicza sinus określoną liczbę. |
| `['sqrt', number]` | numer | Oblicza pierwiastek kwadratowy z podanej liczbie. |
| `['tan', number]` | numer | Oblicza tangens określoną liczbę. |
## <a name="boolean-expressions"></a>Wyrażenia logiczne

Wyrażenia logiczne zapewniają zestaw operatorów logicznych wyrażeń do oceny logiczna porównania.

Podczas porównywania wartości, wynikiem porównania jest ściśle wpisany. Wartości o różnych typach zawsze są uznawane za nierówne. Przypadki, gdzie typy są znane jako różne, w czasie analizy są uznawane za nieprawidłowe i generuje błąd analizy. 

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['! ', boolean]` | wartość logiczna | Negacja logiczna. Zwraca `true` Jeśli dane wejściowe są `false`, i `false` Jeśli dane wejściowe są `true`. |
| `['!= ', value, value]` | wartość logiczna | Zwraca `true` wartości wejściowe nie są równe, `false` inaczej. |
| `['<', value, value]` | wartość logiczna | Zwraca `true` Jeśli pierwszej danej wejściowej jest mniejsza niż druga Strona, `false` inaczej. Argumenty są wymagane zarówno ciągów lub obu numerów. |
| `['<=', value, value]` | wartość logiczna | Zwraca `true` Jeśli pierwszej danej wejściowej jest mniejsza niż druga Strona, `false` inaczej. Argumenty są wymagane zarówno ciągów lub obu numerów. |
| `['==', value, value]` | wartość logiczna | Zwraca `true` wartości wejściowe są równe, `false` inaczej. Argumenty są wymagane zarówno ciągów lub obu numerów. |
| `['>', value, value]` | wartość logiczna | Zwraca `true` Jeśli pierwszy element danych wejściowych jest większa od drugiej, `false` inaczej. Argumenty są wymagane zarówno ciągów lub obu numerów. |
| `['>=' value, value]` | wartość logiczna | Zwraca `true` Jeśli pierwszy element danych wejściowych jest większa niż lub równe drugiemu, `false` inaczej. Argumenty są wymagane zarówno ciągów lub obu numerów. |
| `['all', boolean, boolean, …]` | wartość logiczna | Zwraca `true` w przypadku wszystkich danych wejściowych `true`, `false` inaczej. |
| `['any', boolean, boolean, …]` | wartość logiczna | Zwraca `true` Jeśli dane wejściowe są `true`, `false` inaczej. |

## <a name="conditional-expressions"></a>Wyrażenia warunkowe

Wyrażenia warunkowe zapewniają operacje logiki, które są podobne do instrukcji if.

Następujących wyrażeń wykonują operacje warunkowe logiki na dane wejściowe. Na przykład `case` wyrażenie udostępnia logikę "if/then/else", podczas `match` wyrażenie jest takie jak "-instrukcji switch". 

### <a name="case-expression"></a>Wyrażenie CASE

A `case` wyrażenie jest typu wyrażenia warunkowego, która zapewnia instrukcji if, np. logikę (Jeśli/to/inaczej). Wyrażenie tego typu przechodzi przez listę warunków logicznych i zwraca wartość danych wyjściowych pierwszy warunek logiczny, który ma wartość true.

Poniższym pseudokodzie definiuje strukturę `case` wyrażenia. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Przykład**

Poniższy przykład przechodzi przez różnych warunków logicznych, dopóki nie zostanie znaleziony taki, który daje w wyniku `true`, a następnie zwraca, które skojarzone wartości. Jeśli nie warunek logiczny daje w wyniku `true`, zostanie zwrócona wartość rezerwowego. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Wyrażenie dopasowania

A `match` wyrażenie jest typu wyrażenia warunkowego, który zapewnia instrukcji switch, np. logikę. Dane wejściowe może być dowolne wyrażenie, takie jak `['get', 'entityType']` zwracającego ciągiem lub liczbą. Każda etykieta musi być pojedynczą wartość literału lub tablicę wartości literału, których wartości musi być wszystkie ciągi lub cyfry. Wartość wejściowa pasuje, jeśli dowolna z wartości w tablicy są zgodne. Każda etykieta musi być unikatowa. Jeśli typ danych wejściowych nie jest zgodny typ etykiet, wynik będzie wartością rezerwowego.

Poniższym pseudokodzie definiuje strukturę `match` wyrażenia. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Przykłady**

Poniższy przykład sprawdza `entityType` właściwości funkcji punktu w warstwie bąbelków wyszukuje dopasowania. Jeśli znajdzie dopasowanie, która jest zwracana wartość przeciwnym razie zwraca wartość rezerwowa.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

W poniższym przykładzie użyto tablicy, aby wyświetlić listę zestaw etykiet, które powinien zwrócić tę samą wartość. Jest to znacznie bardziej efektywne niż indywidualnie listy każdej etykiety. W takim przypadku `entityType` właściwości jest "restauracji" lub "grocery_store", "red" kolor zostaną zwrócone.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Wyrażenie łączące

A `coalesce` wyrażenie przechodzi przez zestaw wyrażeń, dopóki nie uzyskuje się pierwsza wartość inną niż null i zwraca tę wartość. 

Poniższym pseudokodzie definiuje strukturę ` coalesce` wyrażenia. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Przykład**

W poniższym przykładzie użyto `coalesce` wyrażenia, aby ustawić `textField` opcji warstwy symboli. Jeśli `title` brakuje właściwości funkcji lub równa `null`, wyrażenie będzie spróbuj szukasz `subtitle` właściwość, jeśli jego brak lub `null`, go następnie powróci do pustego ciągu. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>Wyrażenia typu

Wyrażenia typu oferuje narzędzia do testowania i konwertowanie różnych typów danych, takich jak ciągi, liczby i wartości logiczne.

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | Tablica \| obiektu | Zwraca wartość literału tablicy lub obiektu. Użyj tego wyrażenia, aby uniemożliwić oceniane jako wyrażenie tablicy lub obiektu. Jest to konieczne w przypadku, gdy tablicy lub obiektu musi być zwrócone przez wyrażenie. |
| `['to-boolean', value]` | wartość logiczna | Konwertuje wartości wejściowej na wartość logiczną. Wynik jest `false` gdy danych wejściowych jest ciągiem pustym `0`, `false`, `null`, lub `NaN`; w przeciwnym razie jego `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Konwertuje wartość wejściowa do koloru. Jeśli podano wiele wartości, każdy z nich jest kolejno Szacowana aż do uzyskania pierwszej konwersji pomyślnie. Jeśli można przekonwertować żadne dane wejściowe, wyrażenie jest błąd. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | numer | Konwertuje wartość wejściowa liczba, jeśli jest to możliwe. Jeśli dane wejściowe są `null` lub `false`, wynik jest równy 0. Jeśli dane wejściowe są `true`, wynik wyniesie 1. Jeśli dane wejściowe to ciąg, jest konwertowany na numer przy użyciu [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) ciąg funkcja specyfikacji języka ECMAScript. Jeśli podano wiele wartości, każdy z nich jest kolejno Szacowana aż do uzyskania pierwszej konwersji pomyślnie. Jeśli można przekonwertować żadne dane wejściowe, wyrażenie jest błąd. |
| `['to-string', value]` | string | Konwertuje wartość wejściowa na ciąg. Jeśli dane wejściowe są `null`, wynik jest `""`. Jeśli dane wejściowe jest wartością logiczną, wynik jest `"true"` lub `"false"`. Jeśli dane wejściowe jest liczbą, jest konwertowana na ciąg za pośrednictwem [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) liczba funkcji w specyfikacji języka ECMAScript. Jeśli dane wejściowe ma kolor, jest konwertowany na ciąg koloru CSS RGBA `"rgba(r,g,b,a)"`. W przeciwnym razie dane wejściowe są konwertowane na ciąg za pośrednictwem [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) funkcja specyfikacji języka ECMAScript. |
| `['typeof', value]` | string | Zwraca ciąg opisujący typ danej wartości. |

> [!TIP]
> Jeśli komunikat o błędzie podobny do `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` pojawi się w konsoli przeglądarki ją oznacza, że to wyrażenie gdzieś w kodzie, które zawiera tablicę, która nie ma parametrów dla swojej pierwszej wartości. Jeśli chcesz, aby wyrażenie, aby zwracało tablicę, opakowywanie tablicy przy użyciu `literal` wyrażenia. W poniższym przykładzie ustawiono ikonę `offset` opcji warstwę "symbol" musi być tablica zawierająca dwóch liczb, używając `match` wyrażenie wyboru między dwiema wartościami przesunięcia na podstawie wartości z `entityType` właściwości punktu Funkcja.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Wyrażenie koloru

Kolor wyrażenia ułatwienia tworzenia i manipulowania wartości kolorów.

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Tworzy wartość koloru z *czerwony*, *zielony*, i *niebieski* składników, które musi należeć do zakresu od `0` i `255`oraz jako składnik alfa z `1`. Jeśli dowolny składnik jest poza zakresem, wyrażenie jest błąd. |
| `['rgba', number, number, number, number]` | color | Tworzy wartość koloru z *czerwony*, *zielony*, *niebieski* składników, które musi należeć do zakresu od `0` i `255`oraz jako składnik alfa w zakresie `0` i `1`. Jeśli dowolny składnik jest poza zakresem, wyrażenie jest błąd. |
| `['to-rgba']` | \[Liczba, liczba, numer, numer\] | Zwraca tablicę czterech elementu zawierającą kolorów danych wejściowych *czerwony*, *zielony*, *niebieski*, i *alfa* składniki w tej kolejności. |

**Przykład**

Poniższy przykład tworzy i ma wartość koloru RGB *czerwony* wartość `255`, i *zielony* i *niebieski* wartości, które są obliczane przez pomnożenie `2.5` przez wartość `temperature` właściwości. Zmiana temperatury kolor zmieni się na różnych odcieni *czerwony*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Wyrażenie operatora ciągu

Wyrażenie operatora ciągu wykonują operacje konwersji na ciągi, takie jak łączenie i konwertowanie wielkości liter. 

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Łączy ze sobą wiele ciągów. Każda wartość musi być ciągiem. Użyj `to-string` wpisz wyrażenie można przekonwertować na ciąg inne typy wartości, jeśli to konieczne. |
| `['downcase', string]` | string | Konwertuje określony ciąg na małe litery. |
| `['upcase', string]` | string | Konwertuje określony ciąg na wielkie litery. |

**Przykład**

Poniższy przykład konwertuje `temperature` właściwości punktu są wyposażone w ciąg, a następnie łączy "° F" na końcu.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

Powyższym wyrażeniu powoduje wyświetlenie kodu pin na mapie z tekstem "64° F" nałożony na nim, jak pokazano na poniższej ilustracji.

<center>

![Przykładowe wyrażenie operatora ciągu](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Interpolacji i krok wyrażeń

Interpolacji i wyrażeń kroku może służyć do obliczania wartości wzdłuż funkcję interpolowane krzywej lub krok. Na wyrażenie zwracające wartość numeryczną jako danych wejściowych, na przykład wykonać te wyrażenia `['get',  'temperature']`. Wartość wejściowa zostaną ocenione względem par wartości wejściowe i wyjściowe o nazwie "zatrzymania", aby określić wartość, która najlepiej pasuje do funkcji interpolowane krzywej lub krok. Wartości wejściowe dla każdego stop musi być liczbą i znajdować się w kolejności rosnącej. Wartości danych wyjściowych musi być liczbą i tablicy liczb lub kolor.

### <a name="interpolate-expression"></a>Interpolacji wyrażenia

`interpolate` Wyrażenie może służyć do obliczania ciągłe, smooth zbiór wartości przez interpolowanie wartości. `interpolate` Wyrażenie zwracające wartości kolorów tworzy gradient kolorów, w wyniku których wartości są wybrane z.

Istnieją trzy typy metod interpolacji, które mogą być używane w `interpolate` wyrażenie:
 
* `['linear']` -Interpolacji liniowo pary zatrzymuje.
* `['exponential', base]` -Argumentu wykładniczo między dwoma punktami. `base` Wartość kontroluje szybkość, z jaką zwiększa się dane wyjściowe. Wyższe wartości należy zwiększyć bardziej ku najwyższych zakresu danych wyjściowych. A `base` wartość zbliżone do wartości 1 generuje dane wyjściowe, która zwiększa się bardziej liniowo.
* `['cubic-bezier', x1, y1, x2, y2]` -Interpolacji przy użyciu [krzywą Beziera trzeciego stopnia](https://developer.mozilla.org/docs/Web/CSS/timing-function) zdefiniowane przez punkty danej kontrolki.

Poniżej przedstawiono przykładowy wygląd różnego typu interpolations. 

| Liniowe  | Wykładnicze | Beziera trzeciego stopnia |
|---------|-------------|--------------|
| ![Wykres interpolacji liniowej](media/how-to-expressions/linear-interpolation.png) | ![Wykres interpolacji wykładnicza](media/how-to-expressions/exponential-interpolation.png) | ![Wykres Beziera trzeciego stopnia w interpolacji](media/how-to-expressions/bezier-curve-interpolation.png) |

Poniższym pseudokodzie definiuje strukturę `interpolate` wyrażenia. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Przykład**

W poniższym przykładzie użyto `linear interpolate` wyrażenia, aby ustawić `color` na podstawie właściwości warstwy bąbelków `temperature` właściwości funkcji punktów. Jeśli `temperature` jest mniejsza niż 60, "niebieski", zostaną zwrócone, jeśli między 60 i mniejszy niż 70, żółty zostaną zwrócone, jeśli od 70 do mniej niż 80, "pomarańczowy" zostaną zwrócone, jeśli zostanie zwrócony 80 lub większą, "red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Poniższa ilustracja pokazuje, jak kolory są wybierane w powyższym wyrażeniu.
 
<center>

![Przykładowe wyrażenie interpolacji](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Wyrażenie Step

A `step` wyrażenie może służyć do obliczania wartości dyskretnych, schodkowym wyników poprzez ocenę [funkcja rozkład elementowy stały](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) zdefiniowane przez zatrzymanie. 

Poniższym pseudokodzie definiuje strukturę `step` wyrażenia. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Wyrażenia Step zwróć wartość danych wyjściowych stop tuż przed wartości wejściowej lub pierwszej wartości wejściowej, jeśli dane wejściowe są mniejsze niż pierwszym krokiem. 

**Przykład**

W poniższym przykładzie użyto `step` wyrażenia, aby ustawić `color` na podstawie właściwości warstwy bąbelków `temperature` właściwości funkcji punktów. Jeśli `temperature` jest mniejsza niż 60, "niebieski", zostaną zwrócone, jeśli pomiędzy 60 i mniejszy niż 70 "żółty" zostaną zwrócone, jeśli od 70 do mniej niż 80, "pomarańczowy" zostaną zwrócone, jeśli zostanie zwrócony 80 lub większą, "red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Poniższa ilustracja pokazuje, jak kolory są wybierane w powyższym wyrażeniu.
 
<center>

![Przykładowe wyrażenie kroku](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Wyrażenia określone warstwy

Specjalne wyrażeń, które mają zastosowanie tylko do określonej warstwy.

### <a name="heat-map-density-expression"></a>Wyrażenie gęstość Mapa cieplna

Wyrażenie gęstość Mapa cieplna pobiera wartość gęstość Mapa cieplna każdego piksela na warstwie mapy cieplnej i jest zdefiniowany jako `['heatmap-density']`. Ta wartość jest liczbą z zakresu od `0` i `1` i jest używana w połączeniu z `interpolation` lub `step` gradient kolorów używany kolorować Mapa cieplna definiowanie w wyrażeniu. To wyrażenie można używać tylko w [opcja koloru](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) warstwy mapy cieplnej.

> [!TIP]
> Kolor pod indeksem 0 w wyrażeniu interpolacji lub domyślny kolor koloru krok określa kolor obszaru w przypadku, gdy nie ma żadnych danych i może służyć do definiowania kolor tła. Wiele chcesz ustawić tę wartość na przezroczyste lub częściowo przezroczysty czarny. 

**Przykład**

Tym przykładzie użyto wyrażenia interpolacji liniowej, aby utworzyć gradient kolorów do renderowania Mapa cieplna. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Oprócz używania smooth gradientu kolorować Mapa cieplna, kolory można określić w ramach zestawu zakresy przy użyciu `step` wyrażenia. Za pomocą `step` wyrażenie kolorowanie Mapa cieplna dzielony gęstość wizualnie do zakresów, bardziej podobny mapę Styl konturu lub radarowego.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Aby uzyskać więcej informacji, zobacz [Dodaj warstwę mapy cieplnej](map-add-heat-map-layer.md) dokumentacji.

### <a name="line-progress-expression"></a>Wyrażenie postępu linii

Wyrażenie postępu linii pobiera postęp wzdłuż linia gradientu w warstwie wiersza i jest zdefiniowany jako `['line-progress']`. Ta wartość jest liczbą z zakresu od 0 do 1 i jest używana w połączeniu z `interpolation` lub `step` wyrażenia. To wyrażenie można używać tylko z [opcji strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) warstwy wiersza. 

> [!NOTE]
> `strokeGradient` Opcja warstwę linii wymaga `lineMetrics` możliwość źródła danych można ustawić `true`.

**Przykład**

W poniższym przykładzie użyto `['line-progress']` wyrażenie, które dotyczą gradientu kolor pociągnięcia wiersza.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Zobacz przykład na żywo](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Wyrażenie formatu pola tekstowego

Wyrażenie formatu pola tekstowe mogą być używane z `textField` opcji warstwy symbol `textOptions` właściwości, aby zapewnić formatowania tekstu mieszanego. To wyrażenie umożliwia zestaw ciągów wejściowych i opcji formatowania, aby określić. Można określić następujące opcje dla każdego ciągu wejściowego, w tym wyrażeniu.

 * `'font-scale'` -Określa współczynnik skalowania rozmiar czcionki. Jeśli zostanie określony, ta wartość zastępuje `size` właściwość `textOptions` poszczególnych parametrów.
 * `'text-font'` -Określa rodziny czcionek, które powinny być używane dla tego ciągu. Jeśli zostanie określony, ta wartość zastępuje `font` właściwość `textOptions` poszczególnych parametrów.

Poniższym pseudokodzie definiuje strukturę wyrażenie format pola tekstowego. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**Przykład**

Poniższy przykład formatuje pola tekstowego przy dodawaniu pogrubioną czcionką i skalowanie w górę rozmiar czcionki `title` właściwości funkcji. Ten przykład dodaje również `subtitle` właściwość tę funkcję na nowym wierszem, za pomocą skalowany w dół rozmiar czcionki.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

Ta warstwa spowoduje, że funkcja punkt, jak pokazano na poniższej ilustracji:
 
<center>

![Obraz funkcji punktów z polem tekstu sformatowanego](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Wyrażenie formatu

`number-format` Wyrażenia można używać tylko z `textField` opcji warstwy symboli. To wyrażenie konwertuje podaną liczbę na ciąg formatowania. To wyrażenie opakowuje języka JavaScript [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) funkcji i obsługuje następujący zestaw opcji.

 * `locale` -Określ tę opcję, aby Konwertowanie liczb na ciągi w taki sposób, że zgodne z określonego języka. Przekaż [tagu języka BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) do tej opcji.
 * `currency` -Aby Konwertuj liczbę na ciąg reprezentujący walutę. Możliwe wartości to [kody waluty ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), takich jak "USD" dla USD, EUR "EUR" lub "CNY" dla Chińskiej RMB.
 * `'min-fraction-digits'` -Określa minimalną liczbę miejsc dziesiętnych, które mają zostać objęte wersję numer w ciągu.
 * `'max-fraction-digits'` -Określa maksymalną liczbę miejsc dziesiętnych, które mają zostać objęte wersję numer w ciągu.

Poniższym pseudokodzie definiuje strukturę wyrażenie format pola tekstowego. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Przykład**

W poniższym przykładzie użyto `number-format` wyrażenia, aby zmodyfikować sposób, w jaki `revenue` właściwości funkcji punktów jest renderowany w `textField` opcji symbolu warstwy tak, aby była wyświetlana wartość Dolar Amerykański.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Ta warstwa spowoduje, że funkcja punkt, jak pokazano na poniższej ilustracji:

<center>

![Liczba przykładowe wyrażenie formatu](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Wyrażenie powiększenia

A `zoom` wyrażenie służy do pobierania bieżący poziom powiększenia mapy w czasie renderowania i jest zdefiniowany jako `['zoom']`. To wyrażenie zwraca liczbę z zakresu poziomu minimalne i maksymalne powiększenie mapy. Użycie tego wyrażenia umożliwia style, które mają być modyfikowane dynamicznie, wraz ze zmianą poziom powiększenia mapy. `zoom` Wyrażenie może być używana tylko z `interpolate` i `step` wyrażenia.

**Przykład**

Domyślnie promienie punktów danych, renderowane przy użyciu warstwy mapy cieplnej mieć promień stały pikseli na wszystkich poziomach powiększenia. Jak mapy jest powiększony razem agregacji danych i warstwy mapy cieplnej wygląda inaczej. A `zoom` wyrażenia można skalować usługi radius dla każdego poziomu powiększenia w taki sposób, że każdy punkt danych obejmuje ten sam fizyczny obszar mapy. Dzięki temu warstwy mapy cieplnej Szukaj bardziej statycznych i spójne. Każdy poziom powiększenia mapy ma dwa razy wiele pikseli w pionie i w poziomie jako poprzedniego poziomu powiększenia. Skalowanie promień w taki sposób, że podwaja się przy użyciu każdego poziomu powiększenia utworzy mapę cieplną, która będzie spójny na wszystkich poziomach powiększenia. Można to zrobić za pomocą `zoom` wyrażenie `base 2 exponential interpolation` wyrażenia, jak pokazano poniżej. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Zobacz przykład na żywo](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Wyrażenia wiązania zmiennej

Wyrażenia wiązania zmiennej przechowywanie wyników obliczeń w zmiennej, tak, aby go można odwoływać się gdzie indziej w wyrażeniu wiele razy bez konieczności ponownego obliczenia go. Jest to przydatne optymalizacji dla wyrażeniami, które zawierają wiele obliczeń

| Wyrażenie | Zwracany typ | Opis |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"let"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa1: ciąg<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wartość1:,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa2: ciąg<br/>&nbsp;&nbsp;&nbsp;&nbsp;wartość2:,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Co najmniej jednej wartości są przechowywane jako zmienne do użytku przez `var` wyrażenia w wyrażeniu podrzędne, które zwraca wynik. |
| `['var', name: string]` | dowolne | Odwołuje się do zmiennej, która została utworzona za pomocą `let` wyrażenia. |

**Przykład**

W tym przykładzie użyto wyrażenia, który oblicza przychód względem współczynnik temperatury, a następnie używa `case` wyrażenie do oceny różnych operacji logicznych od tej wartości. `let` Wyrażenie jest używany do przechowywania przychód względem współczynnik temperatury, tak, aby tylko musi być obliczane raz i `var` wyrażenie odwołuje się do tej zmiennej tyle razy bez konieczności ponownego obliczenia go.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu, które implementują wyrażenia:

> [!div class="nextstepaction"] 
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Dodawanie kształtów](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Dodawanie warstwy mapy cieplnej](map-add-heat-map-layer.md)

Dowiedz się więcej na temat opcji warstwy, które obsługują wyrażeń:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
