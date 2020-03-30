---
title: Wyrażenia stylu oparte na danych w sdk azure maps web | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak używać wyrażeń stylu opartych na danych w sdk Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3f15033095b02dd35c2d8d7bda60ca184df64c9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475023"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Wyrażenia stylu oparte na danych (web SDK)

Wyrażenia umożliwiają zastosowanie logiki biznesowej do opcji stylizacji, które obserwują właściwości zdefiniowane w każdym kształcie w źródle danych. Wyrażenia mogą filtrować dane w źródle danych lub warstwie. Wyrażenia mogą składać się z logiki warunkowej, takich jak if-statements. I mogą być używane do manipulowania danymi przy użyciu: operatorów ciągów, operatorów logicznych i operatorów matematycznych.

Style oparte na danych zmniejszają ilość kodu potrzebnego do zaimplementowania logiki biznesowej wokół stylizacji. W przypadku użycia z warstwami wyrażenia są obliczane w czasie renderowania w osobnym wątku. Ta funkcja zapewnia zwiększoną wydajność w porównaniu do oceny logiki biznesowej w wątku interfejsu użytkownika.

Ten klip wideo zawiera omówienie stylów opartych na danych w zestawie Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Wyrażenia są reprezentowane jako tablice JSON. Pierwszym elementem wyrażenia w tablicy jest ciąg, który określa nazwę operatora wyrażenia. Na przykład "+" lub "case". Następne elementy (jeśli istnieją) są argumentami wyrażenia. Każdy argument jest wartością dosłowną (ciąg, liczba, wartość logiczna lub `null`) lub inną tablicą wyrażeń. Poniższy pseudokod definiuje podstawową strukturę wyrażenia. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Zestaw Azure Maps Web SDK obsługuje wiele typów wyrażeń. Wyrażenia mogą być używane samodzielnie lub w połączeniu z innymi wyrażeniami.

| Typ wyrażeń | Opis |
|---------------------|-------------|
| [Wyrażenie agregowane](#aggregate-expression) | Wyrażenie definiujące obliczenie, które jest przetwarzane przez zestaw danych `clusterProperties` i może `DataSource`być używane z opcją . |
| [Wyrażenia logiczne](#boolean-expressions) | Wyrażenia logiczne zapewniają zestaw wyrażeń operatorów logicznych do oceny porównań logicznych. |
| [Wyrażenia kolorów](#color-expressions) | Wyrażenia kolorów ułatwiają tworzenie i manipulowanie wartościami kolorów. |
| [Wyrażenia warunkowe](#conditional-expressions) | Wyrażenia warunkowe zapewniają operacje logiczne, które są podobne do instrukcji if. |
| [Wyrażenia danych](#data-expressions) | Zapewnia dostęp do danych właściwości w funkcji. |
| [Wyrażenia Interpolacji i kroków](#interpolate-and-step-expressions) | Wyrażenia interpolacji i kroków mogą być używane do obliczania wartości wzdłuż interpolowanej krzywej lub funkcji kroku. |
| [Wyrażenia specyficzne dla warstwy](#layer-specific-expressions) | Wyrażenia specjalne, które mają zastosowanie tylko do pojedynczej warstwy. |
| [Wyrażenia matematyczne](#math-expressions) | Zapewnia operatorów matematycznych do wykonywania obliczeń opartych na danych w ramach wyrażeń. |
| [Wyrażenia operatora ciągów](#string-operator-expressions) | Wyrażenia operatora ciągu wykonują operacje konwersji na ciągach, takich jak łączenie i konwertowanie sprawy. |
| [Wpisz wyrażenia](#type-expressions) | Wyrażenia typu zapewniają narzędzia do testowania i konwertowania różnych typów danych, takich jak ciągi, liczby i wartości logiczne. |
| [Wyrażenia wiązania zmiennej](#variable-binding-expressions) | Wyrażenia wiązania zmiennej przechowują wyniki obliczeń w zmiennej i odwołuje się w innym miejscu w wyrażeniu wiele razy bez konieczności ponownego obliczania przechowywanej wartości. |
| [Wyrażenie powiększenia](#zoom-expression) | Pobiera bieżący poziom powiększenia mapy w czasie renderowania. |

Wszystkie przykłady w tym dokumencie używają następującej funkcji, aby zademonstrować różne sposoby, w których można używać różnych typów wyrażeń. 

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
| `['geometry-type']` | ciąg | Pobiera typ geometrii operacji: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | value | Pobiera wartość właściwości z właściwości bieżącej operacji. Zwraca wartość null, jeśli brakuje żądanej właściwości. |
| `['get', string, object]` | value | Pobiera wartość właściwości z właściwości podanego obiektu. Zwraca wartość null, jeśli brakuje żądanej właściwości. |
| `['has', string]` | wartość logiczna | Określa, czy właściwości operacji mają określoną właściwość. |
| `['has', string, object]` | wartość logiczna | Określa, czy właściwości obiektu mają określoną właściwość. |
| `['id']` | value | Pobiera identyfikator funkcji, jeśli ma jeden. |
| `['length', string | array]` | numer | Pobiera długość ciągu lub tablicy. |
| `['in', boolean | string | number, array]` | wartość logiczna | Określa, czy element istnieje w tablicy |
| `['in', substring, string]` | wartość logiczna | Określa, czy podciąg istnieje w ciągu |

**Przykłady**

Właściwości funkcji są dostępne bezpośrednio w wyrażeniu `get` za pomocą wyrażenia. W tym przykładzie użyto wartości "zoneColor" operacji, aby określić właściwość koloru warstwy bąbelkowej. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Powyższy przykład będzie działać poprawnie, jeśli `zoneColor` wszystkie funkcje punktu mają właściwość. Jeśli tego nie zrobią, kolor prawdopodobnie powróci do "czerni". Aby zmodyfikować kolor rezerwowy, `case` należy użyć `has` wyrażenia w połączeniu z wyrażeniem, aby sprawdzić, czy właściwość istnieje. Jeśli właściwość nie istnieje, zwraca kolor rezerwowy.

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

Warstwy bąbelków i symboli domyślnie renderują współrzędne wszystkich kształtów w źródle danych. To zachowanie może wyróżniać wierzchołki wielokąta lub linii. Opcja `filter` warstwy może służyć do ograniczenia typu geometrii obiektów, które renderuje, przy użyciu `['geometry-type']` wyrażenia w wyrażeniu logicznym. Poniższy przykład ogranicza warstwę `Point` bąbelkową, dzięki czemu renderowane są tylko obiekty.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Poniższy przykład `Point` umożliwia `MultiPoint` renderowanie obu i funkcji. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Podobnie kontur wielokątów będzie renderowany w warstwach liniowych. Aby wyłączyć to zachowanie w warstwie liniowej, dodaj filtr, który zezwala `LineString` i `MultiLineString` oferuje tylko funkcje.  

## <a name="math-expressions"></a>Wyrażenia matematyczne

Wyrażenia matematyczne zapewniają operatorów matematycznych do wykonywania obliczeń opartych na danych w ramach wyrażeń.

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['+', number, number, …]` | numer | Oblicza sumę określonych liczb. |
| `['-', number]` | numer | Odejmuje 0 przez określoną liczbę. |
| `['-', number, number]` | numer | Odejmuje pierwsze liczby przez drugą liczbę. |
| `['*', number, number, …]` | numer | Mnoży określone liczby razem. |
| `['/', number, number]` | numer | Dzieli pierwszą liczbę przez drugą liczbę. |
| `['%', number, number]` | numer | Oblicza pozostałą część podczas dzielenia pierwszej liczby przez drugą liczbę. |
| `['^', number, number]` | numer | Oblicza wartość pierwszej wartości podniesionej do potęgi drugiej liczby. |
| `['abs', number]` | numer | Oblicza wartość bezwzględną określonej liczby. |
| `['acos', number]` | numer | Oblicza arckozynę określonej liczby. |
| `['asin', number]` | numer | Oblicza łuk o określonej liczbie. |
| `['atan', number]` | numer | Oblicza arctangent o określonej liczbie. |
| `['ceil', number]` | numer | Zaokrągla liczbę do następnej całej liczby całkowitej. |
| `['cos', number]` | numer | Oblicza cos określonej liczby. |
| `['e']` | numer | Zwraca stałą `e`matematyczną . |
| `['floor', number]` | numer | Zaokrągla liczbę w dół do poprzedniej całej liczby całkowitej. |
| `['ln', number]` | numer | Oblicza logarytm naturalny określonej liczby. |
| `['ln2']` | numer | Zwraca stałą `ln(2)`matematyczną . |
| `['log10', number]` | numer | Oblicza logarytm dziesięciu bazowych określonej liczby. |
| `['log2', number]` | numer | Oblicza logarytm base-two o określonej liczbie. |
| `['max', number, number, …]` | numer | Oblicza maksymalną liczbę w określonym zestawie liczb. |
| `['min', number, number, …]` | numer | Oblicza minimalną liczbę w określonym zestawie liczb. |
| `['pi']` | numer | Zwraca stałą `PI`matematyczną . |
| `['round', number]` | numer | Zaokrągla liczbę do najbliższej liczby całkowitej. Wartości w połowie drogi są zaokrąglane od zera. Na przykład `['round', -1.5]` ocenia do -2. |
| `['sin', number]` | numer | Oblicza sinus o określonej liczbie. |
| `['sqrt', number]` | numer | Oblicza pierwiastek kwadratowy o określonej liczbie. |
| `['tan', number]` | numer | Oblicza styczną określonej liczby. |

## <a name="aggregate-expression"></a>Wyrażenie agregowane

Wyrażenie agregujące definiuje obliczenia, które są przetwarzane przez zestaw danych `clusterProperties` i mogą `DataSource`być używane z opcją . Dane wyjściowe tych wyrażeń muszą być liczbą lub wartością logiczną. 

Wyrażenie agregacji przyjmuje w trzech wartościach: wartość operatora i wartość początkowa oraz wyrażenie do pobrania właściwości z każdej funkcji w danych, aby zastosować operację agregacji. To wyrażenie ma następujący format:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: Funkcja wyrażenia, która jest następnie stosowana względem `mapExpression` wszystkich wartości obliczonych przez dla każdego punktu w klastrze. Obsługiwane podmioty: 
    - Dla liczb: `+` `*`, `max`, ,`min`
    - Dla booleans: `all`,`any`
- initialValue: Wartość początkowa, w której pierwsza obliczona wartość jest agregowana.
- mapExpression: Wyrażenie, które jest stosowane względem każdego punktu w zestawie danych.

**Przykłady**

Jeśli wszystkie funkcje w `revenue` zestawie danych mają właściwość, która jest liczbą. Następnie można obliczyć całkowity przychód wszystkich punktów w klastrze, które są tworzone na podstawie zestawu danych. To obliczenie odbywa się przy użyciu następującego wyrażenia agregacji:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Wyrażenia logiczne

Wyrażenia logiczne zapewniają zestaw wyrażeń operatorów logicznych do oceny porównań logicznych.

Podczas porównywania wartości porównanie jest ściśle wpisywane. Wartości różnych typów są zawsze uważane za nierówne. Przypadki, w których typy są znane jako różne w czasie analizy są uważane za nieprawidłowe i spowoduje błąd analizy. 

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['! ', boolean]` | wartość logiczna | Logiczne negacja. `true` Zwraca, jeśli `false`dane `false` wejściowe są `true`, a jeśli dane wejściowe jest . |
| `['!= ', value, value]` | wartość logiczna | Zwraca, `true` jeśli wartości wejściowe `false` nie są równe, w przeciwnym razie. |
| `['<', value, value]` | wartość logiczna | Zwraca, `true` jeśli pierwsze dane wejściowe `false` jest ściśle mniejsza niż drugi, w przeciwnym razie. Argumenty muszą być zarówno ciągi lub obie liczby. |
| `['<=', value, value]` | wartość logiczna | Zwraca, `true` jeśli pierwsze dane wejściowe jest `false` mniejsza lub równa drugi, w przeciwnym razie. Argumenty muszą być zarówno ciągi lub obie liczby. |
| `['==', value, value]` | wartość logiczna | Zwraca, `true` jeśli wartości wejściowe są równe, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągi lub obie liczby. |
| `['>', value, value]` | wartość logiczna | Zwraca, `true` jeśli pierwsze dane wejściowe `false` jest ściśle większa niż drugi, w przeciwnym razie. Argumenty muszą być zarówno ciągi lub obie liczby. |
| `['>=' value, value]` | wartość logiczna | Zwraca, `true` jeśli pierwsze dane wejściowe jest `false` większa lub równa drugi, w przeciwnym razie. Argumenty muszą być zarówno ciągi lub obie liczby. |
| `['all', boolean, boolean, …]` | wartość logiczna | Zwraca, `true` jeśli wszystkie `true`dane `false` wejściowe są , w przeciwnym razie. |
| `['any', boolean, boolean, …]` | wartość logiczna | Zwraca, `true` jeśli którekolwiek `true`z `false` danych wejściowych jest , w przeciwnym razie. |

## <a name="conditional-expressions"></a>Wyrażenia warunkowe

Wyrażenia warunkowe zapewniają operacje logiczne, które są podobne do instrukcji if.

Następujące wyrażenia wykonują operacje logiki warunkowej na danych wejściowych. Na przykład `case` wyrażenie zawiera logikę "if/then/else", podczas gdy `match` wyrażenie jest jak "switch-statement". 

### <a name="case-expression"></a>Wyrażenie sprawy

Wyrażenie `case` jest typem wyrażenia warunkowego, które zawiera logikę "if/then/else". Ten typ wyrażenia przechodzi przez listę warunków logicznych. Zwraca wartość danych wyjściowych pierwszego warunku logicznego, aby ocenić true.

Poniższy pseudokod definiuje strukturę `case` wyrażenia. 

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

Poniższe przykładowe kroki przez różne warunki logiczne, `true`dopóki nie znajdzie taki, który ocenia , a następnie zwraca tę skojarzoną wartość. Jeśli nie ma wartości `true`warunku logicznego, zostanie zwrócona wartość rezerwowa. 

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

### <a name="match-expression"></a>Wyrażenie dopasowywki

Wyrażenie `match` jest typem wyrażenia warunkowego, które zapewnia switch-instrukcji, takich jak logika. Dane wejściowe mogą być `['get', 'entityType']` dowolnym wyrażeniem, takim jak zwraca ciąg lub liczba. Każda etykieta musi być pojedynczą wartością literału lub tablicą wartości literału, których wartościami muszą być wszystkie ciągi lub wszystkie liczby. Dane wejściowe są zgodne, jeśli którakolwiek z wartości w tablicy jest zgodna. Każda etykieta musi być unikatowa. Jeśli typ wejściowy nie pasuje do typu etykiet, wynikiem będzie wartość rezerwowa.

Poniższy pseudokod definiuje strukturę `match` wyrażenia. 

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

W poniższym przykładzie przyjrzy się `entityType` właściwości point obiektu w warstwie bąbelkowej wyszukuje dopasowanie. Jeśli znajdzie dopasowanie, ta określona wartość jest zwracana lub zwraca wartość rezerwową.

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

W poniższym przykładzie użyto tablicy do listy zestawu etykiet, które powinny zwracać tę samą wartość. Takie podejście jest znacznie bardziej wydajne niż wystawianie każdej etykiety indywidualnie. W takim przypadku, `entityType` jeśli obiekt jest "restauracja" lub "grocery_store", kolor "czerwony" zostanie zwrócony.

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

W poniższym przykładzie użyto wyrażenia dopasowania do wykonania filtru typu "in array" lub "array contains". W takim przypadku wyrażenie filtruje dane, które mają wartość identyfikatora, która znajduje się na liście dozwolonych identyfikatorów. Podczas używania wyrażeń z filtrami wynik musi być wartością logiczną.

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

Wyrażenie `coalesce` przechodzi przez zestaw wyrażeń, dopóki nie zostanie uzyskana pierwsza wartość niena null i zwraca tę wartość. 

Poniższy pseudokod definiuje strukturę `coalesce` wyrażenia. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Przykład**

W poniższym przykładzie `coalesce` użyto `textField` wyrażenia do ustawienia opcji warstwy symboli. Jeśli `title` właściwości brakuje funkcji lub ustawiona na `null`, wyrażenie spróbuje szukać `subtitle` właściwości, `null`jeśli jej brakuje lub , a następnie spadnie z powrotem do pustego ciągu. 

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

W poniższym przykładzie `coalesce` użyto wyrażenia do pobrania pierwszej dostępnej ikony obrazu dostępnej w ikonie mapy z listy określonych nazw obrazów.

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

## <a name="type-expressions"></a>Wpisz wyrażenia

Wyrażenia typu zapewniają narzędzia do testowania i konwertowania różnych typów danych, takich jak ciągi, liczby i wartości logiczne.

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | obiekt \| tablicowy | Zwraca dosłowną wartość tablicy lub obiektu. Użyj tego wyrażenia, aby zapobiec tablicy lub obiektu są oceniane jako wyrażenie. Jest to konieczne, gdy tablica lub obiekt musi zostać zwrócony przez wyrażenie. |
| `['image', string]` | ciąg | Sprawdza, czy określony identyfikator obrazu jest ładowany do ikonki obrazów map. Jeśli tak jest, identyfikator jest zwracany, w przeciwnym razie null jest zwracany. |
| `['to-boolean', value]` | wartość logiczna | Konwertuje wartość wejściową na wartość logiczną. Wynikiem `false` jest, gdy dane wejściowe `0` `false`są `null`pustym ciągiem, , , , lub `NaN`; w `true`przeciwnym razie jego . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Konwertuje wartość wejściową na kolor. Jeśli podano wiele wartości, każda z nich jest oceniana w kolejności do momentu uzyskania pierwszej pomyślnej konwersji. Jeśli żadne dane wejściowe nie mogą być konwertowane, wyrażenie jest błędem. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | numer | Konwertuje wartość wejściową na liczbę, jeśli to możliwe. Jeśli dane `null` wejściowe jest lub `false`, wynik wynosi 0. Jeśli dane `true`wejściowe są , wynik wynosi 1. Jeśli dane wejściowe są ciągiem, są konwertowane na liczbę przy użyciu funkcji ciągu [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) specyfikacji języka ECMAScript. Jeśli podano wiele wartości, każda z nich jest oceniana w kolejności do momentu uzyskania pierwszej pomyślnej konwersji. Jeśli żadne dane wejściowe nie mogą być konwertowane, wyrażenie jest błędem. |
| `['to-string', value]` | ciąg | Konwertuje wartość wejściową na ciąg. Jeśli dane `null`wejściowe są `""`, wynik jest . Jeśli dane wejściowe są wartością `"true"` `"false"`logiczną, wynikiem jest lub . Jeśli dane wejściowe są liczbą, są konwertowane na ciąg przy użyciu funkcji Numeru [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) specyfikacji języka ECMAScript. Jeśli dane wejściowe są kolorem, są konwertowane `"rgba(r,g,b,a)"`na ciąg kolorów CSS RGBA . W przeciwnym razie dane wejściowe są konwertowane na ciąg przy użyciu funkcji [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) specyfikacji języka ECMAScript. |
| `['typeof', value]` | ciąg | Zwraca ciąg opisujący typ danej wartości. |

> [!TIP]
> Jeśli komunikat o `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` błędzie podobny do pojawia się w konsoli przeglądarki, oznacza to, że istnieje wyrażenie gdzieś w kodzie, który ma tablicę, która nie ma ciągu dla jego pierwszej wartości. Jeśli wyrażenie ma zwrócić tablicę, zawiń `literal` tablicę wyrażeniem. W poniższym przykładzie ustawia opcję ikony `offset` warstwy symbolu, która musi być `match` tablicą zawierającą dwie liczby, przy `entityType` użyciu wyrażenia do wyboru między dwiema wartościami odsunięcia na podstawie wartości właściwości operacji punktu.
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

## <a name="color-expressions"></a>Wyrażenia kolorów

Wyrażenia kolorów ułatwiają tworzenie i manipulowanie wartościami kolorów.

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Tworzy wartość koloru z *komponentów czerwony,* *zielony* `0` i `255` *niebieski,* które muszą się wahać między i , i składnik alfa `1`. Jeśli którykolwiek składnik znajduje się poza zakresem, wyrażenie jest błędem. |
| `['rgba', number, number, number, number]` | color | Tworzy wartość koloru z *komponentów czerwony,* *zielony,* *niebieski,* które muszą mieć zakres między `0` i `255`, i składnik alfa w zakresie `0` i `1`. Jeśli którykolwiek składnik znajduje się poza zakresem, wyrażenie jest błędem. |
| `['to-rgba']` | \[numer, liczba, liczba, liczba\] | Zwraca czteroelementową tablicę zawierającą komponenty *czerwony,* *zielony,* *niebieski*i *alfa* w tej kolejności. |

**Przykład**

Poniższy przykład tworzy wartość koloru RGB, `255`która ma wartość *czerwony* , i `2.5` *zielony* i *niebieski* wartości, które są obliczane przez pomnożenie przez wartość `temperature` właściwości. Wraz ze zmianą temperatury kolor zmieni się na różne odcienie *czerwieni*.

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

## <a name="string-operator-expressions"></a>Wyrażenia operatora ciągów

Wyrażenia operatora ciągu wykonują operacje konwersji na ciągach, takich jak łączenie i konwertowanie sprawy. 

| Wyrażenie | Zwracany typ | Opis |
|------------|-------------|-------------|
| `['concat', string, string, …]` | ciąg | Łączy wiele ciągów razem. Każda wartość musi być ciągiem. Użyj `to-string` wyrażenia typu, aby w razie potrzeby przekonwertować inne typy wartości na ciąg. |
| `['downcase', string]` | ciąg | Konwertuje określony ciąg na małe litery. |
| `['upcase', string]` | ciąg | Konwertuje określony ciąg na wielkie litery. |

**Przykład**

Poniższy przykład konwertuje `temperature` właściwość funkcji punktu na ciąg, a następnie łączy "°F" na jej końcu.

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

Powyższe wyrażenie renderuje pinezkę na mapie z tekstem "64°F" nałożona na nią, jak pokazano na poniższej ilustracji.

<center>

![Przykład](media/how-to-expressions/string-operator-expression.png) wyrażenia operatora ciągu</center>

## <a name="interpolate-and-step-expressions"></a>Wyrażenia Interpolacji i kroków

Wyrażenia interpolacji i kroków mogą być używane do obliczania wartości wzdłuż interpolowanej krzywej lub funkcji kroku. Wyrażenia te przyjmują w wyrażeniu, które zwraca `['get',  'temperature']`wartość liczbową jako ich dane wejściowe, na przykład . Wartość wejściowa jest obliczana na podstawie par wartości wejściowych i wyjściowych w celu określenia wartości, która najlepiej pasuje do interpolowanej krzywej lub funkcji kroku. Wartości wyjściowe są nazywane "przystankami". Wartości wejściowe dla każdego przystanku musi być liczbą i być w porządku rosnącym. Wartości wyjściowe muszą być liczbą i tablicą liczb lub kolorem.

### <a name="interpolate-expression"></a>Interpolacja wyrażenia

Wyrażenie `interpolate` może służyć do obliczania ciągłego, gładkiego zestawu wartości przez interpolację między wartościami zatrzymania. Wyrażenie, `interpolate` które zwraca wartości kolorów, tworzy gradient kolorów, z którego są wybierane wartości wyników.

Istnieją trzy typy metod interpolacji, które `interpolate` mogą być używane w wyrażeniu:
 
* `['linear']`- Interpoluje liniowo między parą przystanków.
* `['exponential', base]`- Interpoluje wykładniczo między przystankami. Wartość `base` steruje szybkością, z jaką zwiększa się dane wyjściowe. Wyższe wartości sprawiają, że wydajność zwiększa się bardziej w kierunku górnej części zakresu. Wartość `base` bliska 1 daje dane wyjściowe, które zwiększa się bardziej liniowo.
* `['cubic-bezier', x1, y1, x2, y2]`- Interpoluje za pomocą [sześcienną krzywą Beziera](https://developer.mozilla.org/docs/Web/CSS/timing-function) zdefiniowaną przez dane punkty kontrolne.

Oto przykład tego, jak wyglądają te różne rodzaje interpolacji. 

| Liniowe  | Wykładnicze | Sześcienny Bezier |
|---------|-------------|--------------|
| ![Wykres interpolacji liniowej](media/how-to-expressions/linear-interpolation.png) | ![Wykres interpolacji wykładniczej](media/how-to-expressions/exponential-interpolation.png) | ![Wykres interpolacji Cubic Bezier](media/how-to-expressions/bezier-curve-interpolation.png) |

Poniższy pseudokod definiuje strukturę `interpolate` wyrażenia. 

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

W poniższym przykładzie `linear interpolate` użyto `color` wyrażenia do ustawienia właściwości `temperature` warstwy bąbelkowej na podstawie właściwości obiektu punktu. Jeśli `temperature` wartość jest mniejsza niż 60, zostanie zwrócona wartość "niebieski". Jeśli jest między 60 a mniej niż 70, żółty zostanie zwrócony. Jeśli jest od 70 do mniej niż 80, "pomarańczowy" zostanie zwrócony. Jeśli jest to 80 lub więcej, "czerwony" zostanie zwrócony.

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

Na poniższej ilustracji pokazano, jak kolory są wybierane dla powyższego wyrażenia.
 
<center>

![Przykład](media/how-to-expressions/interpolate-expression-example.png) wyrażenia interpolacji</center>

### <a name="step-expression"></a>Wyrażenie krokowe

Wyrażenie `step` może służyć do obliczania dyskretnych, stopniowanych wartości wyników przez ocenę [funkcji stałej fragmentarycznie zdefiniowanej](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) przez przystanki. 

Poniższy pseudokod definiuje strukturę `step` wyrażenia. 

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

Wyrażenia kroku zwracają wartość wyjściową zatrzymania tuż przed wartością wejściową lub pierwszą wartość wejściową, jeśli dane wejściowe są mniejsze niż pierwszy przystanek. 

**Przykład**

W poniższym przykładzie `step` użyto `color` wyrażenia do ustawienia właściwości `temperature` warstwy bąbelkowej na podstawie właściwości obiektu punktu. Jeśli `temperature` wartość jest mniejsza niż 60, zostanie zwrócona wartość "niebieski". Jeśli jest między 60 a mniej niż 70, "żółty" zostanie zwrócony. Jeśli jest od 70 do mniej niż 80, "pomarańczowy" zostanie zwrócony. Jeśli jest to 80 lub więcej, "czerwony" zostanie zwrócony.

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

Na poniższej ilustracji pokazano, jak kolory są wybierane dla powyższego wyrażenia.
 
<center>

![Przykład wyrażenia kroku](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Wyrażenia specyficzne dla warstwy

Wyrażenia specjalne, które mają zastosowanie tylko do określonych warstw.

### <a name="heat-map-density-expression"></a>Wyrażenie gęstości mapy cieplnej

Wyrażenie gęstości mapy cieplnej pobiera wartość gęstości mapy cieplnej dla każdego piksela `['heatmap-density']`w warstwie mapy cieplnej i jest zdefiniowane jako . Ta wartość jest `0` liczbą między i `1`. Jest on używany w `interpolation` połączeniu `step` z lub wyrażenie do definiowania gradientu kolorów używanego do kolorowania mapy cieplnej. Tego wyrażenia można używać tylko w [opcji koloru](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) warstwy mapy cieplnej.

> [!TIP]
> Kolor w indeksie 0, w wyrażeniu interpolacji lub domyślnym kolorze koloru kroku, definiuje kolor obszaru, w którym nie ma danych. Kolor w indeksie 0 może służyć do definiowania koloru tła. Wielu woli ustawić tę wartość na przezroczystą lub półprzezroczystą czerń.

**Przykład**

W tym przykładzie użyto wyrażenia interpolacji liniowej do utworzenia gładkiego gradientu kolorów do renderowania mapy cieplnej. 

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

Oprócz używania gradientu gładkiego do pokolorowania mapy cieplnej, kolory `step` można określić w obrębie zestawu zakresów za pomocą wyrażenia. Używanie `step` wyrażenia do kolorowania mapy cieplnej wizualnie dzieli gęstość na zakresy przypominające mapę stylu konturu lub radaru.  

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

Aby uzyskać więcej informacji, zobacz dodawanie dokumentacji [warstwy mapy cieplnej.](map-add-heat-map-layer.md)

### <a name="line-progress-expression"></a>Wyrażenie postępu wiersza

Wyrażenie postępu wiersza pobiera postęp wzdłuż linii gradientu w `['line-progress']`warstwie linii i jest zdefiniowane jako . Ta wartość jest liczbą z 0 i 1. Jest on używany w `interpolation` połączeniu `step` z lub wyrażenie. Tego wyrażenia można używać tylko z [opcją strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) warstwy liniowej. 

> [!NOTE]
> Opcja `strokeGradient` warstwy linii wymaga `lineMetrics` ustawienia opcji źródła danych `true`na .

**Przykład**

W tym przykładzie `['line-progress']` użyto wyrażenia do zastosowania gradientu kolorów do obrysu linii.

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

Wyrażenia formatu pola tekstowego `textField` mogą być używane `textOptions` z opcją właściwości warstwy symboli w celu zapewnienia formatowania tekstu mieszanego. To wyrażenie umożliwia określenie zestawu ciągów wejściowych i opcji formatowania. Dla każdego ciągu wejściowego w tym wyrażeniu można określić następujące opcje.

 * `'font-scale'`- Określa współczynnik skalowania rozmiaru czcionki. Jeśli zostanie określona, ta `size` wartość zastąpi `textOptions` właściwość dla poszczególnych ciągów.
 * `'text-font'`- Określa jedną lub więcej rodzin czcionek, które powinny być używane dla tego ciągu. Jeśli zostanie określona, ta `font` wartość zastąpi `textOptions` właściwość dla poszczególnych ciągów.
 * `'text-color'`- Określa kolor, który ma być stosowany do tekstu podczas renderowania. 

Poniższy pseudokod definiuje strukturę wyrażenia formatu pola tekstowego. 

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

Poniższy przykład formatuje pole tekstowe, dodając pogrubioną `title` czcionkę i zwiększając rozmiar czcionki właściwości obiektu. W tym przykładzie dodaje również `subtitle` właściwość operacji w nowym liczebności, ze skalowanym rozmiarem czcionki i kolorem czerwonym.

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

Ta warstwa spowoduje renderowanie funkcji punktu, jak pokazano na poniższej ilustracji:
 
<center>

![Obraz funkcji Punkt z sformatowanym polem](media/how-to-expressions/text-field-format-expression.png) tekstowym</center>

### <a name="number-format-expression"></a>Wyrażenie formatu liczb

Wyrażenia `number-format` można używać tylko `textField` z opcją warstwy symbolu. To wyrażenie konwertuje podany numer na sformatowany ciąg. To wyrażenie jest zawijane przez funkcję [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) języka JavaScript i obsługuje następujący zestaw opcji.

 * `locale`- Określ tę opcję konwersji liczb na ciągi w sposób, który jest wyrównany z określonym językiem. Przekaż [tag języka BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) do tej opcji.
 * `currency`- Aby przekonwertować liczbę na ciąg reprezentujący walutę. Możliwe wartości to [kody walut ISO 4217,](https://en.wikipedia.org/wiki/ISO_4217)takie jak "USD" dla dolara amerykańskiego, "EUR" dla euro lub "CNY" dla chińskiego RMB.
 * `'min-fraction-digits'`- Określa minimalną liczbę miejsc dziesiętnych do uwzględnienia w wersji ciągu liczby.
 * `'max-fraction-digits'`- Określa maksymalną liczbę miejsc dziesiętnych do uwzględnienia w wersji ciągu liczby.

Poniższy pseudokod definiuje strukturę wyrażenia formatu pola tekstowego. 

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

W poniższym przykładzie `number-format` użyto `revenue` wyrażenia do zmodyfikowania sposobu renderowania właściwości obiektu punktu w `textField` opcji warstwy symboli w taki sposób, aby była wyświetlana wartość w dolarach amerykańskich.

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

Ta warstwa spowoduje renderowanie funkcji punktu, jak pokazano na poniższej ilustracji:

<center>

![Przykład](media/how-to-expressions/number-format-expression.png) wyrażenia formatu liczb</center>

### <a name="image-expression"></a>Wyrażenie obrazu

Wyrażenia obrazu można używać `image` z `textField` opcjami i opcjami `fillPattern` warstwy symboli oraz z opcją warstwy wielokąta. To wyrażenie sprawdza, czy żądany obraz istnieje w stylu i zwróci `null`nazwę obrazu rozwiązał lub , w zależności od tego, czy obraz jest obecnie w stylu. Ten proces sprawdzania poprawności jest synchroniczne i wymaga obrazu zostały dodane do stylu przed żądaniem go w argumentu obrazu.

**Przykład**

W poniższym przykładzie `image` użyto wyrażenia, aby dodać ikonę w tekście w warstwie symboli. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Ta warstwa spowoduje renderowanie pola tekstowego w warstwie symboli, jak pokazano na poniższej ilustracji:

<center>

![Przykład](media/how-to-expressions/image-expression.png) wyrażenia obrazu</center>

## <a name="zoom-expression"></a>Wyrażenie powiększenia

Wyrażenie `zoom` jest używane do pobierania bieżącego poziomu powiększenia `['zoom']`mapy w czasie renderowania i jest zdefiniowane jako . To wyrażenie zwraca liczbę między minimalnym i maksymalnym zakresem powiększenia mapy. Interaktywne kontrolki map usługi Azure Maps dla sieci Web i systemu Android obsługują 25 poziomów powiększenia, ponumerowanych od 0 do 24. Użycie `zoom` wyrażenia umożliwia dynamiczną modyfikację stylów w miarę zmiany poziomu powiększenia mapy. Wyrażenie `zoom` może być używane `interpolate` `step` tylko z i wyrażenia.

**Przykład**

Domyślnie promienie punktów danych renderowanych w warstwie mapy cieplnej mają stały promień pikseli dla wszystkich poziomów powiększenia. W miarę powiększania mapy dane są agregowane, a warstwa mapy skupień wygląda inaczej. Wyrażenie `zoom` może służyć do skalowania promienia dla każdego poziomu powiększenia w taki sposób, aby każdy punkt danych obejmował ten sam obszar fizyczny mapy. Sprawi to, że warstwa mapy ciepła będzie wyglądać bardziej statycznie i spójnie. Każdy poziom powiększenia mapy ma dwa razy więcej pikseli w pionie i poziomie niż poprzedni poziom powiększenia. Skalowanie promienia, tak aby podwajał się z każdym poziomem powiększenia, spowoduje utworzenie mapy skupień, która wygląda spójnie na wszystkich poziomach powiększenia. Można to osiągnąć za `zoom` pomocą `base 2 exponential interpolation` wyrażenia z wyrażeniem, jak pokazano poniżej. 

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

Wyrażenia wiązania zmiennej przechowują wyniki obliczeń w zmiennej. Tak, że wyniki obliczeń można odwoływać się w innym miejscu w wyrażeniu wiele razy. Jest to przydatna optymalizacja dla wyrażeń, które obejmują wiele obliczeń.

| Wyrażenie | Zwracany typ | Opis |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"niech",<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: ciąg,<br/>&nbsp;&nbsp;&nbsp;&nbsp;wartość1: dowolna,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: ciąg,<br/>&nbsp;&nbsp;&nbsp;&nbsp;wartość2: dowolna,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression (wysmażenie dziecka)<br/>\] | | Przechowuje jedną lub więcej wartości jako `var` zmienne do użycia przez wyrażenie w wyrażeniu podrzędnym, który zwraca wynik. |
| `['var', name: string]` | Wszelki | Odwołuje się do zmiennej, `let` która została utworzona przy użyciu wyrażenia. |

**Przykład**

W tym przykładzie użyto wyrażenia, które oblicza przychód względem stosunku temperatury, a następnie używa `case` wyrażenia do oceny różnych operacji logicznych na tej wartości. Wyrażenie `let` jest używane do przechowywania przychodu względem stosunku temperatury, dzięki czemu musi być obliczany tylko raz. Wyrażenie `var` odwołuje się do tej zmiennej tak często, jak to konieczne, bez konieczności ponownego obliczania go.

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
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Dodawanie warstwy mapy cieplnej](map-add-heat-map-layer.md)

Dowiedz się więcej o opcjach warstwy obsługujących wyrażenia:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions (BubbleLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions (Opucjaje heatmaplayeroptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions (LineLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions (PolygonLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Opcje warstw symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
