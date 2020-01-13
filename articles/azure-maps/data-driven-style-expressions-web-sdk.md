---
title: Wyrażenia stylów oparte na danych w zestawie SDK sieci Web Azure Maps | Mapy Microsoft Azure
description: Ten artykuł zawiera informacje na temat używania wyrażeń stylów opartych na danych w Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 8372012734d937da99c32d2d18fed91ae52c7444
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911780"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Wyrażenia stylów oparte na danych (zestaw SDK sieci Web)

Wyrażenia umożliwiają stosowanie logiki biznesowej do opcji stylu, które obserwują właściwości zdefiniowane w poszczególnych kształtach w źródle danych. Wyrażeń można także używać do filtrowania danych w źródle danych lub w warstwie. Wyrażenia mogą składać się z logiki warunkowej, takich jak if-Statements, i mogą być również używane do manipulowania danymi za pomocą polecenia. Operatory String, Logical i matematyczne. 

Style oparte na danych mogą zmniejszyć ilość kodu wymaganą do zaimplementowania logiki biznesowej wokół stylów. W przypadku użycia z warstwami wyrażenia są oceniane w czasie renderowania w osobnym wątku, który zapewnia lepszą wydajność w porównaniu do oceny logiki biznesowej w wątku interfejsu użytkownika.

Poniższy klip wideo zawiera omówienie stylów opartych na danych w Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Wyrażenia są reprezentowane jako tablice JSON. Pierwszy element wyrażenia w tablicy jest ciągiem, który określa nazwę operatora wyrażenia. Na przykład "+" lub "Case". Następne elementy (jeśli istnieją) są argumentami wyrażenia. Każdy argument jest wartością literału (ciągiem, liczbą, wartością logiczną lub `null`) lub inną tablicą wyrażeń. Poniższe pseudokodzie definiuje podstawową strukturę wyrażenia. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Zestaw SDK sieci Web Azure Maps obsługuje wiele typów, które mogą być używane samodzielnie lub w połączeniu z innymi wyrażeniami.

| Typ wyrażeń | Opis |
|---------------------|-------------|
| [Wyrażenie agregujące](#aggregate-expression) | Wyrażenie definiujące obliczenia, które jest przetwarzane przez zestaw danych i może być używane z opcją `clusterProperties` `DataSource`. |
| [Wyrażenia logiczne](#boolean-expressions) | Wyrażenia logiczne zawierają zestaw wyrażeń operatorów logicznych do oceniania porównania wartości logicznych. |
| [Wyrażenia koloru](#color-expressions) | Wyrażenia kolorów ułatwiają tworzenie wartości kolorów i manipulowanie nimi. |
| [Wyrażenia warunkowe](#conditional-expressions) | Wyrażenia warunkowe udostępniają operacje logiki, które są podobne do instrukcji if-Statement. |
| [Wyrażenia danych](#data-expressions) | Zapewnia dostęp do danych właściwości w funkcji. |
| [Wyrażenia interpolowane i krokowe](#interpolate-and-step-expressions) | Wyrażenia interpolacji i kroku mogą służyć do obliczania wartości w interpolowanej krzywej lub funkcji kroku. |
| [Wyrażenia specyficzne dla warstwy](#layer-specific-expressions) | Specjalne wyrażenia mające zastosowanie tylko do jednej warstwy. |
| [Wyrażenia matematyczne](#math-expressions) | Udostępnia operatory matematyczne do wykonywania obliczeń opartych na danych w ramach struktury wyrażeń. |
| [Wyrażenia operatora ciągu](#string-operator-expressions) | Wyrażenia operatora ciągu wykonują operacje konwersji na ciągach, takich jak łączenie i konwertowanie wielkości liter. |
| [Wyrażenia typu](#type-expressions) | Wyrażenia typu dostarczają narzędzia do testowania i konwertowania różnych typów danych, takich jak ciągi, liczby i wartości logiczne. |
| [Wyrażenia powiązań zmiennych](#variable-binding-expressions) | Wyrażenia powiązań zmiennych umożliwiają przechowywanie wyników obliczeń w zmiennej i odwoływanie się w innym miejscu wyrażenia bez konieczności ponownego obliczania przechowywanej wartości. |
| [Wyrażenie powiększenia](#zoom-expression) | Pobiera bieżący poziom powiększenia mapy w czasie renderowania. |

We wszystkich przykładach w tym dokumencie zostanie użyta następująca funkcja, aby przedstawić różne sposoby używania różnych typów wyrażeń. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
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
| `['geometry-type']` | string | Pobiera typ geometrii funkcji: punkt, MultiPoint, LineString, MultiLineString, Wielokąt, MultiPolygon. |
| `['get', string]` | wartość | Pobiera wartość właściwości z właściwości bieżącej funkcji. Zwraca wartość null, jeśli brakuje żądanej właściwości. |
| `['get', string, object]` | wartość | Pobiera wartość właściwości z właściwości podanego obiektu. Zwraca wartość null, jeśli brakuje żądanej właściwości. |
| `['has', string]` | wartość logiczna | Określa, czy właściwości funkcji mają określoną właściwość. |
| `['has', string, object]` | wartość logiczna | Określa, czy właściwości obiektu mają określoną właściwość. |
| `['id']` | wartość | Pobiera identyfikator funkcji, jeśli ma. |
| `['length', string | array]` | numer | Pobiera długość ciągu lub tablicy. |

**Przykłady**

Do właściwości funkcji można uzyskać dostęp bezpośrednio w wyrażeniu przy użyciu wyrażenia `get`. Poniższy przykład używa wartości "zoneColor" funkcji, aby określić Właściwość Color warstwy bąbelkowej. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Powyższy przykład będzie działał prawidłowo, jeśli wszystkie funkcje punktu mają właściwość `zoneColor`, ale w przeciwnym razie kolor będzie prawdopodobnie przywrócony do "czerni". Aby zmodyfikować kolor rezerwowy, wyrażenie `case` może być używane w połączeniu z wyrażeniem `has`, aby sprawdzić, czy właściwość istnieje i czy nie zwróci koloru rezerwowego.

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

Warstwy bąbelków i symboli domyślnie renderują współrzędne wszystkich kształtów w źródle danych. Można to zrobić, aby wyróżnić wierzchołki wielokątów lub linii. Opcja `filter` warstwy może służyć do ograniczenia typu geometrii funkcji renderowanych przy użyciu wyrażenia `['geometry-type']` w wyrażeniu logicznym. Poniższy przykład ogranicza warstwę bąbelków, tak aby były renderowane tylko funkcje `Point`.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

W poniższym przykładzie zostanie wyrenderowana zarówno funkcja `Point`, jak i `MultiPoint`. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Podobnie konspekt wielokątów będzie renderowany w warstwach liniowych. Aby wyłączyć to zachowanie w warstwie liniowej, Dodaj filtr, który zezwala tylko na funkcje `LineString` i `MultiLineString`.  

## <a name="math-expressions"></a>Wyrażenia matematyczne

Wyrażenia matematyczne zapewniają operatory matematyczne do wykonywania obliczeń opartych na danych w ramach struktury wyrażeń.

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['+', number, number, …]` | numer | Oblicza sumę podanych liczb. |
| `['-', number]` | numer | Odejmuje 0 według podanej liczby. |
| `['-', number, number]` | numer | Odejmuje pierwsze numery według drugiej liczby. |
| `['*', number, number, …]` | numer | Mnoży określone liczby razem. |
| `['/', number, number]` | numer | Dzieli pierwszą liczbę przez drugą liczbę. |
| `['%', number, number]` | numer | Oblicza resztę podczas dzielenia pierwszej liczby przez drugą liczbę. |
| `['^', number, number]` | numer | Oblicza wartość pierwszej wartości podniesioną do potęgi drugiej liczby. |
| `['abs', number]` | numer | Oblicza wartość bezwzględną podanej liczby. |
| `['acos', number]` | numer | Oblicza arcus cosinus podanej liczby. |
| `['asin', number]` | numer | Oblicza arcus sinus dla podanej liczby. |
| `['atan', number]` | numer | Oblicza arcus tangens podanej liczby. |
| `['ceil', number]` | numer | Zaokrągla liczbę w górę do najbliższej liczby całkowitej. |
| `['cos', number]` | numer | Oblicza cos o podanej liczbie. |
| `['e']` | numer | Zwraca stałą matematyczną `e`. |
| `['floor', number]` | numer | Zaokrągla liczbę w dół do poprzedniej wartości całkowitej. |
| `['ln', number]` | numer | Oblicza logarytm naturalny podanej liczby. |
| `['ln2']` | numer | Zwraca stałą matematyczną `ln(2)`. |
| `['log10', number]` | numer | Oblicza logarytm dziesiętny dla podanej liczby. |
| `['log2', number]` | numer | Oblicza logarytm dziesiętny dla podanej liczby. |
| `['max', number, number, …]` | numer | Oblicza maksymalną liczbę w określonym zestawie liczb. |
| `['min', number, number, …]` | numer | Oblicza minimalną liczbę w określonym zestawie liczb. |
| `['pi']` | numer | Zwraca stałą matematyczną `PI`. |
| `['round', number]` | numer | Zaokrągla liczbę do najbliższej liczby całkowitej. Połowy wartości są zaokrąglane w kierunku od zera. Na przykład `['round', -1.5]` oblicza wartość-2. |
| `['sin', number]` | numer | Oblicza sinus dla podanej liczby. |
| `['sqrt', number]` | numer | Oblicza pierwiastek kwadratowy z podanej liczby. |
| `['tan', number]` | numer | Oblicza tangens podanej liczby. |

## <a name="aggregate-expression"></a>Wyrażenie agregujące

Wyrażenie agregujące definiuje obliczenia przetwarzane w zestawie danych i może być używane z `clusterProperties` opcją `DataSource`. Wynik tych wyrażeń musi być liczbą lub wartością logiczną. 

Wyrażenie agregujące przyjmuje trzy wartości: wartość operatora i wartość początkowa oraz wyrażenie służące do pobierania właściwości z poszczególnych funkcji w danych w celu zastosowania operacji agregowania. To wyrażenie ma następujący format:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: funkcja wyrażenia, która zostanie następnie zastosowana do wszystkich wartości obliczanych przez `mapExpression` dla każdego punktu w klastrze. Obsługiwane operatory; 
    - Dla liczb: `+`, `*`, `max``min`
    - W przypadku wartości logicznych: `all`, `any`
- initialValue: wartość początkowa, w której agregowana jest pierwsza wartość obliczeniowa.
- mapExpression: wyrażenie, które jest stosowane do każdego punktu w zestawie danych.

**Przykłady**

Jeśli wszystkie funkcje w zestawie danych mają właściwość `revenue`, która jest liczbą. Łączny przychód wszystkich punktów w klastrze utworzonym na podstawie zestawu danych można obliczyć przy użyciu następującego wyrażenia agregującego: `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Wyrażenia logiczne

Wyrażenia logiczne zawierają zestaw wyrażeń operatorów logicznych do oceniania porównania wartości logicznych.

Porównując wartości, porównanie jest ściśle wpisane. Wartości różnych typów są zawsze uznawane za nierówne. Przypadki, w których typy muszą być różne w czasie analizy, są uważane za nieprawidłowe i spowodują błąd analizy. 

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['! ', boolean]` | wartość logiczna | Negacja logiczna. Zwraca `true`, jeśli dane wejściowe są `false`, i `false` Jeśli dane wejściowe są `true`. |
| `['!= ', value, value]` | wartość logiczna | Zwraca `true`, jeśli wartości wejściowe nie są równe, `false` w przeciwnym razie. |
| `['<', value, value]` | wartość logiczna | Zwraca `true`, jeśli pierwsze dane wejściowe są ściśle mniejsze od drugiej, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['<=', value, value]` | wartość logiczna | Zwraca `true`, jeśli pierwsze dane wejściowe są mniejsze lub równe sekundzie, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['==', value, value]` | wartość logiczna | Zwraca `true`, jeśli wartości wejściowe są równe, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['>', value, value]` | wartość logiczna | Zwraca `true`, jeśli pierwsze dane wejściowe są ściśle większe niż sekundy, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['>=' value, value]` | wartość logiczna | Zwraca `true`, jeśli pierwsze dane wejściowe są większe lub równe drugiej, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['all', boolean, boolean, …]` | wartość logiczna | Zwraca `true`, jeśli wszystkie dane wejściowe są `true`, `false` w przeciwnym razie. |
| `['any', boolean, boolean, …]` | wartość logiczna | Zwraca `true`, jeśli którykolwiek z danych wejściowych jest `true`, `false` w przeciwnym razie. |

## <a name="conditional-expressions"></a>Wyrażenia warunkowe

Wyrażenia warunkowe udostępniają operacje logiki, które są podobne do instrukcji if-Statement.

Następujące wyrażenia wykonują warunkowe operacje logiki na danych wejściowych. Na przykład wyrażenie `case` zawiera logikę "if/then/else", podczas gdy wyrażenie `match` jest podobne do "Switch-Statement". 

### <a name="case-expression"></a>Wyrażenie CASE

Wyrażenie `case` jest typem wyrażenia warunkowego, które zawiera instrukcję if-Statement, taką jak Logic (if/then/else). Tego typu wyrażenie jest wykonywane przez listę warunków logicznych i zwraca wartość wyjściową pierwszego warunku logicznego, który ma wartość true.

Poniższy pseudokodzie definiuje strukturę wyrażenia `case`. 

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

Poniższy przykład przechodzi przez inne warunki logiczne do momentu znalezienia jednej, która oblicza `true`, a następnie zwraca tę skojarzoną wartość. Jeśli żaden warunek logiczny nie zostanie oceniony jako `true`, zostanie zwrócona wartość rezerwowa. 

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

Wyrażenie `match` jest typem wyrażenia warunkowego, które zawiera instrukcję Switch, taką jak Logic. Wejście może być dowolnym wyrażeniem, takim jak `['get', 'entityType']`, które zwraca ciąg lub liczbę. Każda etykieta musi być pojedynczą wartością literału lub tablicą wartości literałów, których wartości muszą być wszystkie ciągami lub liczbami. Dane wejściowe są zgodne, Jeśli dowolne wartości w tablicy pasują do siebie. Każda etykieta musi być unikatowa. Jeśli typ danych wejściowych nie jest zgodny z typem etykiet, wynik będzie wartością rezerwową.

Poniższy pseudokodzie definiuje strukturę wyrażenia `match`. 

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

Poniższy przykład sprawdza, czy właściwość `entityType` funkcji Point w warstwie bąbelkowej wyszukuje dopasowanie. Jeśli znajdzie dopasowanie, zostanie zwrócona określona wartość lub zwróci wartość rezerwową.

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

W poniższym przykładzie zastosowano tablicę, aby wyświetlić zestaw etykiet, które powinny zwracać tę samą wartość. Jest to znacznie bardziej wydajne niż wyświetlanie każdej etykiety osobno. W takim przypadku, jeśli właściwość `entityType` jest "restauracji" lub "grocery_store", zostanie zwrócony kolor "Red".

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

W poniższym przykładzie używane jest wyrażenie Match do wykonania filtru typu "in Array" lub "Array Contains", w tym przypadku filtrowanie danych mających wartość identyfikatora, który znajduje się na liście dozwolonych identyfikatorów. W przypadku używania wyrażeń z filtrami wynik musi być wartością logiczną.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Wyrażenie łączenia

Wyrażenie `coalesce` jest wykonywane przez zestaw wyrażeń do momentu uzyskania pierwszej wartości innej niż null i zwrócenie tej wartości. 

Poniższy pseudokodzie definiuje strukturę wyrażenia `coalesce`. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Przykład**

Poniższy przykład używa wyrażenia `coalesce`, aby ustawić opcję `textField` warstwy symboli. Jeśli w funkcji nie ma właściwości `title` lub została ona ustawiona na `null`, w wyrażeniu zostanie wyszukana Właściwość `subtitle`, jeśli jej brak lub `null`, spowoduje to powrót do pustego ciągu. 

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

Poniższy przykład używa wyrażenia `coalesce`, aby pobrać pierwszą dostępną ikonę obrazu dostępną na ikonie Sprite mapy z listy określonych nazw obrazów.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Wyrażenia typu

Wyrażenia typu dostarczają narzędzia do testowania i konwertowania różnych typów danych, takich jak ciągi, liczby i wartości logiczne.

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | Array \| — obiekt | Zwraca tablicę literałową lub wartość obiektu. Użyj tego wyrażenia, aby zapobiec ocenie tablicy lub obiektu jako wyrażenia. Jest to konieczne, gdy tablica lub obiekt musi zostać zwrócony przez wyrażenie. |
| `['image', string]` | string | Sprawdza, czy określony identyfikator obrazu jest ładowany do Sprite obrazu mapy. Jeśli jest, zwracany jest identyfikator, w przeciwnym razie zwracana jest wartość null. |
| `['to-boolean', value]` | wartość logiczna | Konwertuje wartość wejściową do wartości logicznej. Wynik jest `false`, gdy dane wejściowe są pustym ciągiem, `0`, `false`, `null`lub `NaN`. w przeciwnym razie `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Konwertuje wartość wejściową na kolor. Jeśli podano wiele wartości, każda z nich jest szacowana w kolejności do momentu uzyskania pierwszej pomyślnej konwersji. Jeśli żadne dane wejściowe nie mogą być konwertowane, wyrażenie jest błędem. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | numer | Konwertuje wartość wejściową na liczbę, jeśli jest to możliwe. Jeśli dane wejściowe są `null` lub `false`, wynik wynosi 0. Jeśli dane wejściowe są `true`, wynik wynosi 1. Jeśli dane wejściowe są ciągami, są konwertowane na liczbę przy użyciu funkcji [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) String specyfikacji języka ECMAScript. Jeśli podano wiele wartości, każda z nich jest szacowana w kolejności do momentu uzyskania pierwszej pomyślnej konwersji. Jeśli żadne dane wejściowe nie mogą być konwertowane, wyrażenie jest błędem. |
| `['to-string', value]` | string | Konwertuje wartość wejściową na ciąg. Jeśli dane wejściowe są `null`, wynik jest `""`. Jeśli dane wejściowe są wartością logiczną, wynik jest `"true"` lub `"false"`. Jeśli dane wejściowe są liczbami, są konwertowane na ciąg przy użyciu funkcji [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number specyfikacji języka ECMAScript. Jeśli dane wejściowe są kolorem, są konwertowane na ciąg koloru RGBA CSS `"rgba(r,g,b,a)"`. W przeciwnym razie dane wejściowe są konwertowane na ciąg przy użyciu funkcji [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) specyfikacji języka ECMAScript. |
| `['typeof', value]` | string | Zwraca ciąg opisujący typ danej wartości. |

> [!TIP]
> Jeśli komunikat o błędzie podobny do `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` pojawia się w konsoli przeglądarki, oznacza to, że w kodzie znajduje się wyrażenie, które zawiera tablicę, która nie ma ciągu dla swojej pierwszej wartości. Jeśli chcesz, aby wyrażenie zwracało tablicę, zawiń tablicę za pomocą wyrażenia `literal`. Poniższy przykład ustawia ikonę `offset` opcji warstwy symboli, która musi być tablicą zawierającą dwie liczby, przy użyciu wyrażenia `match` do wyboru między dwiema wartościami przesunięcia na podstawie wartości właściwości `entityType` funkcji punktu.
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
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Wyrażenia koloru

Wyrażenia kolorów ułatwiają tworzenie wartości kolorów i manipulowanie nimi.

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Tworzy wartość koloru dla składników *czerwony*, *zielony*i *niebieski* , które muszą namieścić się w zakresie między `0` i `255`, i składnika alfa `1`. Jeśli dowolny składnik znajduje się poza zakresem, wyrażenie jest błędem. |
| `['rgba', number, number, number, number]` | color | Tworzy wartość koloru na podstawie *czerwonych*, *zielonych*, *niebieskich* składników, które muszą należeć do zakresu `0` i `255`, i składnika alfa w zakresie `0` i `1`. Jeśli dowolny składnik znajduje się poza zakresem, wyrażenie jest błędem. |
| `['to-rgba']` | \[Number, Number, Number, Number\] | Zwraca tablicę z czterema elementami zawierającą składniki *czerwony*, *zielony*, *niebieski*i *alfa* koloru wejścia w tej kolejności. |

**Przykład**

Poniższy przykład tworzy i RGB wartość koloru, która ma *czerwoną* wartość `255`, oraz wartości *zielony* i *niebieski* , które są obliczane przez pomnożenie `2.5` przez wartość właściwości `temperature`. Po zmianie temperatury kolor zmieni się na różne odcienie *czerwieni*.

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

## <a name="string-operator-expressions"></a>Wyrażenia operatora ciągu

Wyrażenia operatora ciągu wykonują operacje konwersji na ciągach, takich jak łączenie i konwertowanie wielkości liter. 

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Łączy wiele ciągów ze sobą. Każda wartość musi być ciągiem. Użyj wyrażenia typu `to-string`, aby przekonwertować inne typy wartości na ciąg w razie potrzeby. |
| `['downcase', string]` | string | Konwertuje określony ciąg na małe litery. |
| `['upcase', string]` | string | Konwertuje określony ciąg na wielkie litery. |

**Przykład**

Poniższy przykład konwertuje Właściwość `temperature` funkcji Point na ciąg, a następnie łączy "°F" na końcu.

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

Powyższe wyrażenie renderuje kod PIN na mapie z tekstem "64 °F" na początku, jak pokazano na poniższej ilustracji.

<center>

przykład ![wyrażenia operatora ciągu](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Wyrażenia interpolowane i krokowe

Wyrażenia interpolacji i kroku mogą służyć do obliczania wartości w interpolowanej krzywej lub funkcji kroku. Wyrażenia te przyjmują wyrażenie, które zwraca wartość liczbową jako dane wejściowe, na przykład `['get',  'temperature']`. Wartość wejściowa jest oceniana pod kątem par wartości wejściowych i wyjściowych o nazwie "Stop", aby określić wartość, która najlepiej pasuje do interpolowanej krzywej lub funkcji Step. Wartości wejściowe dla każdego zatrzymania muszą być liczbą i być uporządkowane rosnąco. Wartości wyjściowe muszą być liczbami, tablicami liczb lub kolorem.

### <a name="interpolate-expression"></a>Wyrażenie interpolacji

Wyrażenia `interpolate` można użyć do obliczenia ciągłego, gładkiego zestawu wartości poprzez interpolację między wartościami zatrzymania. Wyrażenie `interpolate` zwracające wartości koloru generuje kolor gradientu, w którym są wybierane wartości wyniku.

Istnieją trzy typy metod interpolacji, których można użyć w wyrażeniu `interpolate`:
 
* `['linear']` — interpoluje liniowo między parą przerwań.
* `['exponential', base]` — interpolowane wykładniczo między zatrzymania. Wartość `base` określa szybkość, z jaką wzrasta dane wyjściowe. Wyższe wartości sprawiają, że dane wyjściowe zwiększają się w kierunku górnego końca zakresu. Wartość `base` bliska 1 generuje dane wyjściowe, które zwiększają się liniowo.
* `['cubic-bezier', x1, y1, x2, y2]` — interpoluje przy użyciu [zakrzywionej krzywej Beziera](https://developer.mozilla.org/docs/Web/CSS/timing-function) zdefiniowanej przez podaną punkty kontrolne.

Oto przykład sposobu, w jaki wyglądają te różne typy interpolacji. 

| Liniowe  | Wykładnicze | Metria sześcienne |
|---------|-------------|--------------|
| ![Wykres interpolacji liniowej](media/how-to-expressions/linear-interpolation.png) | ![Wykres interpolacji wykładniczej](media/how-to-expressions/exponential-interpolation.png) | ![Wykres interpolacji "sześciennych"](media/how-to-expressions/bezier-curve-interpolation.png) |

Poniższy pseudokodzie definiuje strukturę wyrażenia `interpolate`. 

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

Poniższy przykład używa wyrażenia `linear interpolate`, aby ustawić właściwość `color` warstwy bąbelkowej na podstawie właściwości `temperature` funkcji Point. Jeśli `temperature` wartość jest mniejsza niż 60, zostaną zwrócone "niebieska", jeśli między 60 i mniej niż 70 zostanie zwrócone żółte, jeśli między 70 i mniej niż 80 zostanie zwrócone "pomarańczowy", jeśli zostanie wyświetlona wartość 80 lub większa, zostanie zwrócony czerwony znak "Red".

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

Na poniższej ilustracji przedstawiono, jak kolory są wybierane dla powyższego wyrażenia.
 
<center>

![przykładowe wyrażenie interpolacji](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Wyrażenie kroku

Wyrażenia `step` mogą służyć do obliczania dyskretnych, ocenianych wartości wyniku przez obliczenie [funkcji stałej rozkład elementowy](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) zdefiniowanej przez zatrzymywanie. 

Poniższy pseudokodzie definiuje strukturę wyrażenia `step`. 

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

Wyrażenia kroków zwracają wartość wyjściową elementu Stop tuż przed wartością wejściową lub pierwszą wartość wejściową, jeśli dane wejściowe są mniejsze niż pierwsze zatrzymanie. 

**Przykład**

Poniższy przykład używa wyrażenia `step`, aby ustawić właściwość `color` warstwy bąbelkowej na podstawie właściwości `temperature` funkcji Point. Jeśli `temperature` wartość jest mniejsza niż 60, zostaną zwrócone "niebieska", jeśli między 60 i mniej niż 70 zostanie zwrócone "żółty", jeśli między 70 i mniej niż 80 zostanie zwrócone "pomarańczowy", jeśli zostanie wyświetlona wartość 80 lub większa, zostanie zwrócony czerwony znak "Red".

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

Na poniższej ilustracji przedstawiono, jak kolory są wybierane dla powyższego wyrażenia.
 
<center>

przykład ![wyrażenia kroku](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Wyrażenia specyficzne dla warstwy

Specjalne wyrażenia mające zastosowanie tylko do określonych warstw.

### <a name="heat-map-density-expression"></a>Wyrażenie gęstości mapy cieplnej

Wyrażenie gęstości mapy cieplnej Pobiera wartość gęstości mapy cieplnej dla każdego piksela w warstwie mapy cieplnej i jest definiowana jako `['heatmap-density']`. Ta wartość jest liczbą między `0` i `1` i jest używana w połączeniu z wyrażeniem `interpolation` lub `step` do definiowania gradientu koloru używanego do kolorowania mapy cieplnej. Tego wyrażenia można używać tylko w [opcji Color](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) warstwy mapy cieplnej.

> [!TIP]
> Kolor na indeksie 0 w wyrażeniu interpolacji lub domyślnym kolorem kroku, definiuje kolor obszaru, w którym nie ma danych i może służyć do definiowania koloru tła. Wiele woli ustawić tę wartość na przezroczystą lub częściowo przezroczystą czerń. 

**Przykład**

W tym przykładzie zastosowano wyrażenie interpolacji liniowej w celu utworzenia gładkiego gradientu koloru na potrzeby renderowania mapy cieplnej. 

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

Oprócz używania gładkiego gradientu do kolorowania mapy cieplnej, kolory można określić w ramach zestawu zakresów przy użyciu wyrażenia `step`. Użycie wyrażenia `step` na potrzeby kolorowania mapy cieplnej powoduje oddzielenie gęstości na wizualizację w taki sposób, aby była podobna do mapy stylu konturu lub wykresu radarowego.  

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

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [dodawania do warstwy mapy cieplnej](map-add-heat-map-layer.md) .

### <a name="line-progress-expression"></a>Wyrażenie postępu wiersza

Wyrażenie postępu wiersza pobiera postęp wzdłuż linii gradientu w warstwie liniowej i jest zdefiniowany jako `['line-progress']`. Ta wartość jest liczbą z zakresu od 0 do 1 i jest używana w połączeniu z wyrażeniem `interpolation` lub `step`. Tego wyrażenia można używać tylko z [opcją strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) warstwy liniowej. 

> [!NOTE]
> Opcja `strokeGradient` warstwy liniowej wymaga, aby opcja `lineMetrics` źródła danych była ustawiona na `true`.

**Przykład**

Poniższy przykład używa wyrażenia `['line-progress']`, aby zastosować gradient koloru do obrysu wiersza.

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

[Zobacz przykład na żywo](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Wyrażenie formatu pola tekstowego

Wyrażenie formatu pola tekstowego może być używane z opcją `textField` właściwości `textOptions` warstwy symboli, aby zapewnić formatowanie tekstu mieszanego. To wyrażenie umożliwia określenie zestawu ciągów wejściowych i opcji formatowania. Dla każdego ciągu wejściowego w tym wyrażeniu można określić następujące opcje.

 * `'font-scale'` — określa współczynnik skalowania dla rozmiaru czcionki. Jeśli ta wartość jest określona, spowoduje to zastąpienie właściwości `size` `textOptions` dla danego ciągu.
 * `'text-font'` — określa jedną lub więcej rodzin czcionek, które powinny być używane dla tego ciągu. Jeśli ta wartość jest określona, spowoduje to zastąpienie właściwości `font` `textOptions` dla danego ciągu.
 * `'text-color'` — określa kolor, który ma zostać zastosowany do tekstu podczas renderowania. 

Poniższy pseudokodzie definiuje strukturę wyrażenia formatu pola tekstowego. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Przykład**

Poniższy przykład formatuje pole tekstowe przez dodanie pogrubionej czcionki i skalowanie w górę rozmiaru czcionki właściwości `title` funkcji. W tym przykładzie dodano również właściwość `subtitle` funkcji w wierszu wiersza z rozmiarem czcionki skalowanej w dół i kolorem czerwonym.

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
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Ta warstwa będzie renderować funkcję Point, jak pokazano na poniższej ilustracji:
 
<center>

![obraz funkcji punktu ze sformatowanym polem tekstowym](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Wyrażenie formatu liczbowego

Wyrażenia `number-format` można używać tylko z opcją `textField` warstwy symboli. To wyrażenie konwertuje podaną liczbę na sformatowany ciąg. To wyrażenie otacza funkcję [Number. ToLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) języka JavaScript i obsługuje następujący zestaw opcji.

 * `locale` — należy określić tę opcję, aby przekonwertować liczby na ciągi w sposób, który jest wyrównany do określonego języka. Przekaż [tag języka BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) do tej opcji.
 * `currency` — aby przekonwertować liczbę na ciąg reprezentujący walutę. Możliwe wartości to [ISO 4217 kody walutowe](https://en.wikipedia.org/wiki/ISO_4217), takie jak "USD" dla DOLARa USA, "EUR" dla euro lub "CNY" dla CHIŃSKIego RMBu.
 * `'min-fraction-digits'` — określa minimalną liczbę miejsc dziesiętnych do uwzględnienia w wersji ciągu liczby.
 * `'max-fraction-digits'` — określa maksymalną liczbę miejsc dziesiętnych do uwzględnienia w wersji ciągu liczby.

Poniższy pseudokodzie definiuje strukturę wyrażenia formatu pola tekstowego. 

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

Poniższy przykład używa wyrażenia `number-format`, aby zmodyfikować sposób, w jaki Właściwość `revenue` funkcji Point jest renderowana w `textField` opcji warstwy symboli, która jest wyświetlana jako wartość dolara USA.

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

Ta warstwa będzie renderować funkcję Point, jak pokazano na poniższej ilustracji:

<center>

przykład ![wyrażenia formatu liczbowego](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Wyrażenie powiększenia

Wyrażenie `zoom` służy do pobierania bieżącego poziomu powiększenia mapy w czasie renderowania i jest definiowana jako `['zoom']`. To wyrażenie zwraca liczbę z przedziału minimalnego i maksymalnego poziomu powiększenia mapy. Użycie tego wyrażenia umożliwia dynamiczne modyfikowanie stylów, gdy poziom powiększenia mapy zostanie zmieniony. Wyrażenia `zoom` można używać tylko z wyrażeniami `interpolate` i `step`.

**Przykład**

Domyślnie promienie punktów danych renderowane w warstwie mapy cieplnej mają stały promień pikseli dla wszystkich poziomów powiększenia. Ponieważ mapa jest powiększona, zagregowane dane są sumowane i wygląda inaczej niż warstwa mapy cieplnej. Wyrażenie `zoom` może służyć do skalowania promienia dla każdego poziomu powiększenia, tak że każdy punkt danych obejmuje ten sam obszar fizyczny mapy. Spowoduje to, że warstwa mapy cieplnej będzie wyglądać bardziej statyczna i spójna. Każdy poziom powiększenia mapy ma dwa piksele w pionie i poziomie jako poprzedni poziom powiększenia. Skalowanie promienia w taki sposób, że podwaja z każdym poziomem powiększenia, spowoduje utworzenie mapy cieplnej, która będzie wyglądać spójnie na wszystkich poziomach powiększenia. Można to osiągnąć przy użyciu wyrażenia `zoom` z wyrażeniem `base 2 exponential interpolation`, jak pokazano poniżej. 

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

## <a name="variable-binding-expressions"></a>Wyrażenia powiązań zmiennych

Wyrażenia powiązań zmiennych przechowują wyniki obliczeń w zmiennej, dzięki czemu można odwoływać się w innym miejscu w wyrażeniu wielokrotnie bez konieczności ponownego obliczania. Jest to przydatna Optymalizacja dla wyrażeń obejmujących wiele obliczeń

| Wyrażenie | Zwracany typ | Opis |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"let",<br/>&nbsp;&nbsp;&nbsp;&nbsp;Name1: ciąg,<br/>&nbsp;&nbsp;&nbsp;&nbsp;wartość1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;NAME2: ciąg,<br/>&nbsp;&nbsp;&nbsp;&nbsp;wartość2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;...<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Przechowuje co najmniej jedną wartość jako zmienne do użycia przez wyrażenie `var` w wyrażeniu podrzędnym zwracającym wynik. |
| `['var', name: string]` | ile | Odwołuje się do zmiennej, która została utworzona przy użyciu wyrażenia `let`. |

**Przykład**

W tym przykładzie używane jest wyrażenie, które oblicza przychód względem współczynnika temperatury, a następnie używa wyrażenia `case`, aby oszacować różne operacje logiczne dla tej wartości. Wyrażenie `let` służy do przechowywania przychodów względem współczynnika temperatury, tak aby tylko raz była obliczana tylko raz, a wyrażenie `var` odwołuje się do tej zmiennej tak często, jak to konieczne, bez konieczności ponownego obliczania.

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

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu, które implementują wyrażenia:

> [!div class="nextstepaction"] 
> [Dodaj warstwę symboli](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę wielokątów](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Dodaj warstwę mapy cieplnej](map-add-heat-map-layer.md)

Dowiedz się więcej o opcjach warstwy, które obsługują wyrażenia:

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
