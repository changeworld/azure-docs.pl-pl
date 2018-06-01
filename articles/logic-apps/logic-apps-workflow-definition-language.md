---
title: Schemat języka definicji przepływu pracy — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zapisać definicji niestandardowego przepływu pracy dla usługi Azure Logic Apps język definicji przepływu pracy
services: logic-apps
author: ecfan
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: efbfffec10b665ebab230375e774e476199c4ad5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886813"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>Definicji przepływu pracy aplikacji logiki ze schematem język definicji przepływu pracy

Po utworzeniu przepływu pracy aplikacji logiki z [Azure Logic Apps](../logic-apps/logic-apps-overview.md), rzeczywiste logiki uruchomionym aplikacji logiki zawiera opis podstawowej definicji do przepływu pracy. Ten opis następuje strukturą, która ma zdefiniowany i sprawdzone przez schemat język definicji przepływu pracy, który używa [JavaScript Object Notation (JSON)](https://www.json.org/) format. 
  
## <a name="workflow-definition-structure"></a>Struktura definicji przepływu pracy

Definicji przepływu pracy ma przynajmniej jeden wyzwalacz, który tworzy aplikację logiki, a także co najmniej jednej akcji, które uruchamia aplikację logiki. 

Oto ogólny struktura definicji przepływu pracy:  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| Element | Wymagane | Opis | 
|---------|----------|-------------| 
| definicja | Yes | Element początkowy definicji przepływu pracy | 
| $schema | Tylko wtedy, gdy zewnętrznie odwołujące się do definicji przepływu pracy | Lokalizacja pliku schematu JSON, który zawiera opis wersji języka definicji przepływu pracy, który można znaleźć tutaj: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | Nie | Numer wersji definicji przepływu pracy, czyli "1.0.0.0" domyślnie. Do identyfikacji i Potwierdź prawidłowe definicji podczas wdrażania przepływu pracy, należy określić wartość do użycia. | 
| parameters | Nie | Definicje dla jednego lub więcej parametrów, które przekazują dane do przepływu pracy <p><p>Parametry maksymalna: 50 | 
| Wyzwalacze | Nie | Definicje dla jednego lub więcej wyzwalaczy, które wystąpienia przepływu pracy. Można zdefiniować więcej niż jeden wyzwalacz, ale tylko przy użyciu języka definicji przepływu pracy, nie będzie poprzez projektanta aplikacji logiki. <p><p>Wyzwalacze maksymalna: 10 | 
| Akcje | Nie | Definicje dla co najmniej jednej akcji do wykonania w czasie wykonywania przepływu pracy <p><p>Osiągnięto maksymalną liczbę akcji: 250 | 
| wyjścia | Nie | Definicje danych wyjściowych, które zwracają z przepływu pracy Uruchom <p><p>Wyniki maksymalna: 10 |  
|||| 

## <a name="parameters"></a>Parametry

W `parameters` sekcji, zdefiniuj wszystkie parametry przepływu pracy, używane przez aplikację logiki w wdrożenia dla akceptowanie danych wejściowych. Zarówno deklaracji parametrów i wartości parametrów są wymagane na wdrożenie. Przed użyciem tych parametrów w innych częściach przepływu pracy, upewnij się, że zadeklarować wszystkie parametry w tych sekcjach. 

Poniżej przedstawiono ogólną strukturę dla definicji parametru:  

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": { 
      "key": { 
        "name": "<key-value>"
      } 
    }
  }
},
```

| Element | Wymagane | Typ | Opis |  
|---------|----------|------|-------------|  
| type | Yes | int, float, string, securestring, bool, tablicę, obiekt JSON, secureobject <p><p>**Uwaga**: wszystkie hasła, klucze i klucze tajne, użyj `securestring` i `secureobject` typów, ponieważ `GET` operacji nie zwraca tych typów. | Typ parametru |
| Wartość domyślna | Nie | Identyczny `type` | Wartość domyślna parametru, jeśli wartość nie zostanie określona, gdy tworzy wystąpienie przepływu pracy | 
| allowedValues | Nie | Identyczny `type` | Tablica z wartościami, które może zaakceptować parametru |  
| metadane | Nie | Obiekt JSON | Inne szczegóły parametrów, na przykład nazwę lub czytelny opis aplikacji logiki lub używane przez program Visual Studio lub innych narzędzi danych czasu projektowania |  
||||

## <a name="triggers-and-actions"></a>Wyzwalacze i akcje  

W definicji przepływu pracy `triggers` i `actions` sekcje definiują wywołania, które mają miejsce podczas wykonywania do przepływu pracy. Informacje o składni i więcej informacji na temat tych sekcji, zobacz [przepływu pracy wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Dane wyjściowe 

W `outputs` sekcji, zdefiniuj danych przepływu pracy mogą zwracać po zakończeniu uruchamiania. Na przykład aby śledzić stan określonego lub wartość z każdym uruchomieniu, należy określić zwracanych danych wyjściowych przepływu pracy. 

> [!NOTE]
> Gdy odpowiada na przychodzące żądania z interfejsu API REST usługi, nie używaj `outputs`. Zamiast tego należy użyć `Response` typ akcji. Aby uzyskać więcej informacji, zobacz [przepływu pracy wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md).

Poniżej przedstawiono ogólną strukturę dla definicji danych wyjściowych: 

```json
"outputs": {
  "<key-name>": {  
    "type": "<key-type>",  
    "value": "<key-value>"  
  }
} 
```

| Element | Wymagane | Typ | Opis | 
|---------|----------|------|-------------| 
| <*Nazwa klucza*> | Yes | Ciąg | Wartość zwracana nazwę klucza dla danych wyjściowych |  
| type | Yes | int, float, string, securestring, bool, tablicę, obiekt JSON | Typ dla wartości zwracanej w danych wyjściowych | 
| wartość | Yes | Identyczny `type` | Wartość zwracana danych wyjściowych |  
||||| 

Aby uzyskać dane wyjściowe z przepływu pracy, Przejrzyj historię wykonywania aplikacji logiki i szczegółowe informacje w portalu Azure lub użyj [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows). Można również przekazać dane wyjściowe z systemami zewnętrznymi, na przykład usługi Power BI, dzięki czemu można tworzyć pulpity nawigacyjne. 

<a name="expressions"></a>

## <a name="expressions"></a>Wyrażenia

Z formatu JSON może mieć wartości literałów, które istnieją w czasie projektowania, na przykład:

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

Może także zawierać wartości, które nie znajdują się do czasu wykonywania. Do reprezentowania tych wartości, można użyć *wyrażenia*, które są oceniane w czasie wykonywania. Wyrażenie jest sekwencją, która może zawierać jeden lub więcej [funkcje](#functions), [operatory](#operators), zmienne, jawne wartości lub stałymi. W definicji przepływu pracy, można użyć wyrażenia dowolne miejsce w wartości ciągu JSON, prefiksu wyrażenia znaku (@). Podczas obliczania wyrażenia, który reprezentuje wartość JSON, treść wyrażenia jest wyodrębniany przez usunięcie @ znaków i zawsze powoduje inną wartość JSON. 

Na przykład w uprzednio zdefiniowanej `customerName` właściwości, można pobrać wartości właściwości, za pomocą [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) funkcji w wyrażeniu i przypisać tę wartość do `accountName` właściwości:

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

*Ciąg interpolacji* również pozwala używać wielu wyrażeń wewnątrz ciągów, które są kodowane przez @ znaków i nawiasy klamrowe ({}). Oto składnia:

```json
@{ "<expression1>", "<expression2>" }
```

Wynik jest zawsze typu string, co ta możliwość podobny do `concat()` funkcji, na przykład: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Jeśli masz literałem, który rozpoczyna się znakiem @, prefiks @-znak z inną @-znak jako znak ucieczki: @@

Poniższe przykłady pokazują, jak są analizowane wyrażeń:

| Wartość JSON | Wynik |
|------------|--------| 
| "Sophia Owen" | Zwraca następujące znaki: "Sophia Owen" |
| "array [1]" | Zwraca następujące znaki: "array [1]" |
| "\@\@" | Zwraca te znaki jako ciąg o jeden znak: " \@" |   
| " \@" | Zwraca jako ciąg znaków dwóch następujących znaków: " \@" |
|||

Te przykłady Załóżmy, że zdefiniujesz "myBirthMonth" równa się "Stycznia" i "myAge" równa liczbie 42:  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

Poniższe przykłady pokazują, jak są analizowane następujących wyrażeń:

| Wyrażenie JSON | Wynik |
|-----------------|--------| 
| "@parameters(myBirthMonth)" | Zwraca ciąg: "Stycznia" |  
| "@{parameters('myBirthMonth')}" | Zwraca ciąg: "Stycznia" |  
| "@parameters(myAge)" | Zwraca to liczba: 42 |  
| "@{parameters('myAge')}" | Zwraca to liczba jako ciąg: "42" |  
| "Mój wiek jest @{parameters('myAge')}" | Zwraca ciąg: "Mój wiek jest 42" |  
| "@concat("Mój wiek jest", string(parameters('myAge')))" | Zwraca ciąg: "Mój wiek jest 42" |  
| "Mój wiek jest @@ {parameters('myAge')}" | Ten ciąg, który zawiera wyrażenie zwraca: "Mój wiek jest @{parameters('myAge')}" | 
||| 

Podczas pracy wizualnie w Projektancie aplikacji logiki, na przykład można utworzyć wyrażenia za pośrednictwem Konstruktora wyrażeń: 

![Projektant aplikacji logiki > Konstruktor wyrażeń](./media/logic-apps-workflow-definition-language/expression-builder.png)

Gdy wszystko będzie gotowe, znajduje się wyrażenie dla odpowiadających im właściwości w definicji przepływu pracy, na przykład, `searchQuery` właściwości w tym miejscu:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
       "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operatory

W [wyrażenia](#expressions) i [funkcje](#functions), Operatorzy wykonywania określonych zadań, takich jak odwołanie do właściwości lub wartości w tablicy. 

| Operator | Zadanie | 
|----------|------|
| ' | Aby użyć literału ciągu jako dane wejściowe lub wyrażeń i funkcji, zawijać ciąg tylko w pojedynczy cudzysłów, na przykład `'<myString>'`. Nie używaj podwójnego cudzysłowu (""), które powodują konflikt z formatowaniem JSON wokół całego wyrażenia. Na przykład: <p>**Tak**: length('Hello') </br>**Nie**: length("Hello") <p>Jeśli tablice lub liczb, nie trzeba zawijania znaków interpunkcyjnych. Na przykład: <p>**Tak**: długość ([1, 2, 3]) </br>**Nie**: długość ("[1, 2, 3]") | 
| [] | Aby odwołać się do wartości w określonej pozycji (indeks) w tablicy, użyj nawiasy kwadratowe. Na przykład, aby uzyskać drugiego elementu w tablicy: <p>`myArray[1]` | 
| . | Aby odwołać się do właściwości w obiekcie użycia kropki. Na przykład, aby pobrać `name` właściwość `customer` obiekt JSON: <p>`"@parameters('customer').name"` | 
| ? | Aby odwołać właściwości w obiekcie bez błędów czasu wykonywania o wartości null, należy użyć operatora znaku zapytania. Na przykład do obsługi null wyników z wyzwalaczy, można użyć tego wyrażenia: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Funkcje

Niektóre wyrażenia pobrać wartości z akcji środowiska uruchomieniowego, które jeszcze nie istnieje podczas uruchamiania aplikacji logiki. Aby odwołać i pracować z tych wartości w wyrażeniach, można użyć [ *funkcje*](../logic-apps/workflow-definition-language-functions-reference.md). Na przykład możesz użyć matematyczne funkcji do obliczeń, takich jak [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) funkcji, która zwraca sumę liczb całkowitych lub elementów przestawnych. Aby uzyskać szczegółowe informacje dotyczące każdej funkcji, zobacz [artykułu alfabetyczny spis](../logic-apps/workflow-definition-language-functions-reference.md).
Lub kontynuować zapoznawanie funkcji i ich ogólnego przeznaczenia.

Poniżej przedstawiono kilka przykład zadań, które można wykonywać za pomocą funkcji: 

| Zadanie | Składnia funkcji | Wynik | 
| ---- | --------------- | -------------- | 
| Zwraca ciąg w formacie małe litery. | toLower ("<*tekst*>") <p>Na przykład: toLower('Hello') | tekst "hello" | 
| Zwraca unikatowy identyfikator globalny (GUID). | GUID() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

W tym przykładzie pokazano, jak można uzyskać wartości z `customerName` parametru i przypisz, że wartość do `accountName` właściwości przy użyciu [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) funkcja w wyrażeniu:

```json
"accountName": "@parameters('customerName')"
```

Oto kilka innych metod ogólnych użyć funkcji w wyrażeniach:

| Zadanie | Funkcja składni w wyrażeniu | 
| ---- | -------------------------------- | 
| Wykonaj pracę z elementem przez przekazanie do funkcji tego elementu. | "@<*functionName*> (<*elementu*>)" | 
| 1. Pobierz *parameterName*przez wartość przy użyciu zagnieżdżone `parameters()` funkcji. </br>2. Wykonaj pracę z wynikiem przekazując tę wartość do *functionName*. | "@<*functionName*> (parametry (" <*parameterName*>')) " | 
| 1. Pobierz wynik z funkcji zagnieżdżonej wewnętrzny *functionName*. </br>2. Przekazać wynik do funkcji zewnętrznej *functionName2*. | "@<*functionName2*> (<*functionName*> (<*elementu*>))" | 
| 1. Uzyskanie wyniku z *functionName*. </br>2. Biorąc pod uwagę, że wynik jest obiektu z właściwością *propertyName*, pobrać wartości tej właściwości. | "@<*functionName*>(<*item*>). <*propertyName*>" | 
||| 

Na przykład `concat()` funkcja może zająć dwie lub więcej wartości ciągu jako parametry. Ta funkcja scala te ciągi w jeden ciąg. Użytkownik może przekazać w literałach ciągu, na przykład "Sophia" i "Owen", aby uzyskać ciąg Scalonej "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Alternatywnie można pobrać wartości ciągu z parametrów. W tym przykładzie użyto `parameters()` funkcji w każdym `concat()` parametru i `firstName` i `lastName` parametrów. Przekazuj wynikowy ciągi do `concat()` funkcjonować tak, aby pobrać połączony ciąg, na przykład "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

W obu przypadkach zarówno przykłady Przypisz wynik do `customerName` właściwości. 

Aby uzyskać szczegółowe informacje dotyczące każdej funkcji, zobacz [artykułu alfabetyczny spis](../logic-apps/workflow-definition-language-functions-reference.md).
Lub kontynuować zapoznawanie funkcje oparte na ich ogólnego przeznaczenia.

<a name="string-functions"></a>

### <a name="string-functions"></a>Funkcje ciągów

Aby pracować z ciągami, można używać tych funkcji ciąg, a także niektóre [kolekcji funkcji](#collection-functions). Funkcje ciągów działa tylko na ciągach. 

| String — funkcja | Zadanie | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Połącz co najmniej dwa ciągi i zwraca połączony ciąg. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Sprawdź, czy ciąg kończy się wyrazem wskazany podciąg. | 
| [Identyfikator GUID](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Wygeneruj Unikatowy identyfikator globalny (GUID) jako ciąg. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Zwraca pozycję początkową podciąg. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Zwraca pozycję końcową dla podciąg. | 
| [Zamień](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Zastąp podciągu określonego ciągu i zwraca ciąg zaktualizowane. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Zwraca tablicę, która ma wszystkich znaków z ciągu i oddziela każdego znaku znakiem określonego ogranicznika. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Sprawdź, czy ciąg rozpoczyna się od określonego podciąg. | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Zwracanie znaków z ciągu, zaczynając od określonej pozycji. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Zwraca ciąg w formacie małe litery. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Zwraca ciąg w formacie wielkie litery. | 
| [TRIM](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Usuń spacje wiodące i końcowe z ciągu i zwraca ciąg zaktualizowane. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Kolekcja funkcji

Aby pracować z kolekcji, zazwyczaj tablic ciągów i czasami słowników, możesz użyć tych funkcji kolekcji. 

| Funkcja kolekcji | Zadanie | 
| ------------------- | ---- | 
| [zawiera](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Sprawdź, czy kolekcja zawiera konkretny element. |
| [pusty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Sprawdź, czy kolekcja jest pusta. | 
| [pierwszy](../logic-apps/workflow-definition-language-functions-reference.md#first) | Zwraca pierwszy element z kolekcji. | 
| [część wspólną](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Zwraca kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Zwraca ciąg, który ma *wszystkie* elementy z tablicy, oddzielonych określony znak. | 
| [ostatni](../logic-apps/workflow-definition-language-functions-reference.md#last) | Zwraca ostatni element z kolekcji. | 
| [długość](../logic-apps/workflow-definition-language-functions-reference.md#length) | Zwraca liczbę elementów w string lub array. | 
| [Pomiń](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Usuwanie elementów z na początku kolekcji, a następnie wróć *wszystkie inne* elementów. | 
| [podejmij](../logic-apps/workflow-definition-language-functions-reference.md#take) | Zwraca elementy z przodu kolekcji. | 
| [Unii](../logic-apps/workflow-definition-language-functions-reference.md#union) | Zwraca kolekcję, która ma *wszystkie* elementy z określonymi kolekcjami. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Porównanie funkcji

Do pracy z warunkami, porównać wartości i wyniki wyrażenia lub oceny różnego rodzaju logiki, możesz użyć tych funkcji porównania. Aby uzyskać pełną dokumentację dotyczące każdej funkcji, zobacz [artykułu alfabetyczny spis](../logic-apps/workflow-definition-language-functions-reference.md).

| Porównanie funkcji | Zadanie | 
| ------------------- | ---- | 
| [I](../logic-apps/workflow-definition-language-functions-reference.md#and) | Sprawdź, czy wszystkie wyrażenia mają wartość true. | 
| [równa się](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Sprawdź, czy obie wartości są równoważne. | 
| [większa](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Sprawdź, czy pierwsza wartość jest większa od drugiej wartości. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Sprawdź, czy pierwsza wartość jest większa niż lub równa drugiej wartości. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Sprawdź, czy wyrażenie jest równa true lub false. Na podstawie wyniku, zwrócić określoną wartość. | 
| [mniej](../logic-apps/workflow-definition-language-functions-reference.md#less) | Sprawdź, czy pierwsza wartość jest mniejsza niż wartość drugiej. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Sprawdź, czy pierwsza wartość jest mniejsza niż lub równa drugiej wartości. | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Sprawdź, czy wyrażenie jest fałszywe. | 
| [lub](../logic-apps/workflow-definition-language-functions-reference.md#or) | Sprawdź, czy co najmniej jedno wyrażenie ma wartość true. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Funkcje konwersji

Aby zmienić typ lub format wartości, możesz użyć tych funkcji konwersji. Na przykład można zmienić wartości z wartością logiczną na liczbę całkowitą. Aby dowiedzieć się, jak aplikacje logiki obsługuje typy zawartości podczas konwersji, zobacz [obsługi typów zawartości](../logic-apps/logic-apps-content-type.md). Aby uzyskać pełną dokumentację dotyczące każdej funkcji, zobacz [artykułu alfabetyczny spis](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcja konwersji | Zadanie | 
| ------------------- | ---- | 
| [Tablica](../logic-apps/workflow-definition-language-functions-reference.md#array) | Zwraca tablicę z jednej z określonych danych wejściowych. Dla wielu danych wejściowych, zobacz [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [Base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Zwraca ciąg wersji algorytmem Base64. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Zwróć binarnej wersji dla ciągu zakodowanego algorytmem base64. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Zwraca ciąg wersji ciąg kodowany w formacie base64. | 
| [Binarne](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Zwróć binarnej wersji dla wartości wejściowej. | 
| [wartość logiczna](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Zwracany Boolean wersji wartości wejściowej. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Zwraca tablicę z wielu danych wejściowych. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Zwraca identyfikator URI danych wartości wejściowej. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Zwróć binarnej wersji danych identyfikatora URI. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Zwraca ciąg wersji danych identyfikatora URI. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Zwraca ciąg wersji ciąg kodowany w formacie base64. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Zwróć binarnej wersji danych identyfikatora URI. | 
| [decodeuricomponent —](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Zwracany ciąg czy zastępuje Usuń znaki dekodowane wersje. | 
| [encodeuricomponent —](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Zwraca ciąg, który zastępuje znaki niezabezpieczony adres URL znaki specjalne. | 
| [Float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Zwraca zmiennoprzecinkową punktu liczba wartości wejściowych. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Zwraca ciąg wersji liczby całkowitej. | 
| [JSON](../logic-apps/workflow-definition-language-functions-reference.md#json) | Zwraca wartość typu JavaScript Object Notation (JSON) lub obiekt string lub XML. | 
| [Ciąg](../logic-apps/workflow-definition-language-functions-reference.md#string) | Zwraca ciąg wersji wartości wejściowej. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Zwróć wersja kodowany w formacie identyfikatora URI dla wartości wejściowej przez zamianę znaków niezabezpieczony adres URL znaki specjalne. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Zwróć binarnej wersji na ciąg kodowany w formacie identyfikatora URI. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Zwraca ciąg wersji ciąg kodowany w formacie identyfikatora URI. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Zwraca ciąg w wersji XML. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Funkcje matematyczne

Aby pracować z liczbami całkowitymi i elementów przestawnych, można użyć tych funkcje matematyczne. Aby uzyskać pełną dokumentację dotyczące każdej funkcji, zobacz [artykułu alfabetyczny spis](../logic-apps/workflow-definition-language-functions-reference.md).

| Math — funkcja | Zadanie | 
| ------------- | ---- | 
| [Dodaj](../logic-apps/workflow-definition-language-functions-reference.md#add) | Zwraca wynik z dodanie dwóch liczb. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Zwraca wynik dzielenia dwóch liczb. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Zwraca największą wartość ze zbioru liczb lub tablicy. | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Zwraca najmniejszą wartość z zestawu liczb lub tablicy. | 
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Zwraca resztę z dzielenia liczby dwa. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Zwraca produktu z pomnożenie dwóch liczb. | 
| [RAND](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Zwraca losową liczbę całkowitą z określonego zakresu. | 
| [Zakres](../logic-apps/workflow-definition-language-functions-reference.md#range) | Zwraca tablicę liczba całkowita, która rozpoczyna się od określonej wartości całkowitej. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Zwraca wynik z odjęcie druga liczba od pierwszej liczby. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Funkcje daty i godziny

Aby pracować z dat i godzin, można użyć tych funkcji daty i godziny.
Aby uzyskać pełną dokumentację dotyczące każdej funkcji, zobacz [artykułu alfabetyczny spis](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcja daty lub godziny | Zadanie | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Dodaj liczbę dni do sygnatury czasowej. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Dodaj liczbę godzin do sygnatury czasowej. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Dodaj liczbę minut do sygnatury czasowej. | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Dodaj liczba sekund do sygnatury czasowej. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Dodaj liczbę jednostek czasu do sygnatury czasowej. Zobacz też [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Konwertuj sygnaturę czasową z uniwersalny czas koordynowany (UTC) zgodnie ze strefą czasową docelowej. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Przekonwertować sygnatury czasowej ze źródłową strefą czasową zgodnie ze strefą czasową docelowej. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konwertuj sygnaturę czasową ze źródłową strefą czasową uniwersalny czas koordynowany (UTC). | 
| [DayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Zwróć dzień składnik miesiąca z sygnatury czasowej. | 
| [DayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Zwróć dzień tygodnia składnika z sygnatury czasowej. | 
| [Dzieńroku](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Zwróć dzień składnik roku z sygnatury czasowej. | 
| [FormatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Zwraca daty z sygnatury czasowej. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Zwraca bieżącą sygnaturę czasową plus jednostki określonego czasu. Zobacz też [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Zwraca bieżącą sygnaturę czasową minus jednostki określonego czasu. Zobacz też [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Zwróć początek dnia dla sygnatury czasowej. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Zwróć początek godziny dla sygnatury czasowej. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Zwraca początek miesiąca dla sygnatury czasowej. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odjęcie liczby jednostek czasu z sygnatury czasowej. Zobacz też [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [znaczniki osi](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Zwraca `ticks` wartości właściwości dla określonej sygnatury czasowej. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Zwraca bieżącą sygnaturę czasową jako ciąg. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Funkcje przepływu pracy

Te funkcje przepływu pracy można:

* Uzyskiwanie szczegółowych informacji o wystąpienia przepływu pracy w czasie wykonywania. 
* Współpraca z dane wejściowe, używane do tworzenia wystąpienia aplikacji logiki.
* Odwołują się dane wyjściowe z wyzwalacze i akcje.

Na przykład można odwoływać się dane wyjściowe z jedną akcję i użyć tych danych w późniejszym akcji. Aby uzyskać pełną dokumentację dotyczące każdej funkcji, zobacz [artykułu alfabetyczny spis](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcja przepływ pracy | Zadanie | 
| ----------------- | ---- | 
| [Akcja](../logic-apps/workflow-definition-language-functions-reference.md#action) | Zwraca dane wyjściowe bieżącej akcji w czasie wykonywania lub wartości z innych pary nazwa wartość JSON. Zobacz też [akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Akcja zwrotu `body` danych wyjściowych w czasie wykonywania. Zobacz też [treści](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Zwraca wynik akcji w czasie wykonywania. Zobacz [akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [Akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Zwraca wynik akcji w czasie wykonywania lub wartości z innych pary nazwa wartość JSON. Zobacz też [akcji](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [body](#body) | Akcja zwrotu `body` danych wyjściowych w czasie wykonywania. Zobacz też [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Utworzyć tablicy o wartości, które odpowiadają nazwy klucza w *dane formularza* lub *postać zakodowanych* wyniki akcji. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Zwraca pojedynczą wartość, który odpowiada nazwie klucza w akcji *dane formularza* lub *wynik zakodowany w postaci*. | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gdy wewnątrz identycznych działania dotyczące tablicy, zwraca bieżący element w tablicy podczas akcji bieżącej iteracji. | 
| [Elementy](../logic-apps/workflow-definition-language-functions-reference.md#items) | Gdy wewnątrz dla każdego lub czy do pętli, zwracany bieżący element z określonym pętli.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Zwróć "wywołania zwrotnego adresu URL" wywołuje wyzwalacz lub akcji. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Zwróć wynik akcji, który ma wiele części treści dla określonej części. | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Zwraca wartość dla parametru, który jest opisany w definicji aplikacji logiki. | 
| [Wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Zwraca dane wyjściowe tego wyzwalacza, w czasie wykonywania, lub z innych pary nazwa wartość JSON. Zobacz też [triggerOutputs](#triggerOutputs) i [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Zwraca wyzwalacza `body` danych wyjściowych w czasie wykonywania. Zobacz [wyzwalacza](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Zwraca pojedynczą wartość dopasowania nazwy klucza w *dane formularza* lub *postać zakodowanych* wyzwolenia danych wyjściowych. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Zwróć dla określonej części treści wieloczęściowej dane wyjściowe tego wyzwalacza. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Utworzenie tablicy, których wartości odpowiada nazwie klucza w *dane formularza* lub *postać zakodowanych* wyzwolenia danych wyjściowych. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Zwraca dane wyjściowe tego wyzwalacza w czasie wykonywania lub wartości z innych pary nazwa wartość JSON. Zobacz [wyzwalacza](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [zmienne](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Zwraca wartość określonej zmiennej. | 
| [przepływ pracy](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Zwróć wszystkie szczegółowe informacje o sam przepływ pracy w czasie wykonywania. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>Funkcje analizy identyfikatora URI

Identyfikatory uniform resource identifier (URI) i uzyskać różne wartości właściwości dla tych identyfikatorów URI, można użyć tych analizy funkcji identyfikatora URI. Aby uzyskać pełną dokumentację dotyczące każdej funkcji, zobacz [artykułu alfabetyczny spis](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcja analizy identyfikatora URI | Zadanie | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Zwraca `host` wartość Identyfikator uniform resource identifier (URI). | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Zwraca `path` wartość Identyfikator uniform resource identifier (URI). | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Zwraca `path` i `query` wartości Identyfikator uniform resource identifier (URI). | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Zwraca `port` wartość Identyfikator uniform resource identifier (URI). | 
| [Właściwość uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Zwraca `query` wartość Identyfikator uniform resource identifier (URI). | 
| [UriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Zwraca `scheme` wartość Identyfikator uniform resource identifier (URI). | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>Funkcje JSON i XML

Do pracy z obiektami JSON i XML węzłów, można użyć funkcji manipulowania. Aby uzyskać pełną dokumentację dotyczące każdej funkcji, zobacz [artykułu alfabetyczny spis](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcję manipulowania | Zadanie | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Dodaj właściwość i jej wartość lub pary nazwa wartość do obiektu JSON, a następnie wróć zaktualizowany obiekt. | 
| [połączenie](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Zwraca pierwszą wartość inną niż null z co najmniej jeden parametr. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Usuń właściwość z obiektu JSON i zwracać zaktualizowany obiekt. | 
| [Metoda setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ustaw wartość właściwości obiektu JSON i zwróć zaktualizowany obiekt. | 
| [wyrażenie XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Sprawdź, czy XML węzły lub wartości zgodne wyrażenie XPath (XML Path Language) i zwracają pasującego węzłów lub wartości. | 
||| 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [akcje język definicji przepływu pracy i wyzwalaczy](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Dowiedz się więcej o programowe tworzenie i zarządzanie nimi logiki aplikacji za pomocą [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows)
