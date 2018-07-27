---
title: Odwołanie do schematu dla język definicji przepływów pracy — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zapisz definicje niestandardowego przepływu pracy dla usługi Azure Logic Apps za pomocą języka definicji przepływu pracy
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 04/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 9268ca3db6c99c4e660690e25a2331a1fa1cdf96
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263678"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Odwołanie do schematu dla język definicji przepływów pracy w usłudze Azure Logic Apps

Podczas tworzenia przepływu pracy aplikacji logiki za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md), podstawową definicję Twój przepływ pracy w tym artykule opisano rzeczywiste logikę, która jest uruchamiana dla aplikacji logiki. Ten opis jest zgodna to struktura, która została zdefiniowana i zweryfikowane przez schemat języka definicji przepływu pracy, który używa [JavaScript Object Notation (JSON)](https://www.json.org/). 
  
## <a name="workflow-definition-structure"></a>Struktura definicji przepływu pracy

Definicja przepływu pracy ma co najmniej jeden wyzwalacz, który tworzy wystąpienie aplikacji logiki, a także co najmniej jednej akcji, których Twoja aplikacja logiki działa. 

Poniżej przedstawiono ogólną strukturę dla definicji przepływu pracy:  
  
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
| definicja | Yes | Element początkowy dla swojej definicji przepływu pracy | 
| $schema | Tylko wtedy, gdy zewnętrznie odwołujące się do definicji przepływu pracy | Lokalizacja pliku schematu JSON, który opisuje wersję język definicji przepływów pracy, który można znaleźć tutaj: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentversion — | Nie | Numer wersji dla definicji przepływu pracy, czyli "1.0.0.0" domyślnie. Aby ułatwić identyfikowanie i Potwierdź poprawną definicję, wdrażając przepływu pracy, należy określić wartość do użycia. | 
| parameters | Nie | Definicje dla jednego lub więcej parametrów, które przekazują dane do Twojego przepływu pracy <p><p>Parametry maksymalna: 50 | 
| Wyzwalacze | Nie | Definicje dla co najmniej jeden wyzwalacze, które wystąpienia przepływu pracy. Można zdefiniować więcej niż jeden wyzwalacz, ale tylko za pomocą języka definicji przepływu pracy, nie wizualnie za pomocą projektanta aplikacji logiki. <p><p>Wyzwalacze maksymalna: 10 | 
| Akcje | Nie | Definicje dla co najmniej jedną akcję do wykonania w czasie wykonywania przepływu pracy <p><p>Maksymalną liczbę akcji: 250 | 
| wyjścia | Nie | Definicje dla danych wyjściowych, które zwracają z przebiegu przepływu pracy <p><p>Dane wyjściowe maksymalna: 10 |  
|||| 

## <a name="parameters"></a>Parametry

W `parameters` sekcji, określ wszystkie parametry przepływu pracy używanych przez aplikację logiki we wdrożeniu do akceptowania dane wejściowe. Deklaracji parametru i wartości parametrów są wymagane podczas wdrażania. Przed za pomocą tych parametrów w innych częściach przepływu pracy, upewnij się, że zadeklarować wszystkie parametry w tych sekcjach. 

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
| type | Yes | int, float, string, securestring, bool, tablicę, obiekt JSON, secureobject <p><p>**Uwaga**: w przypadku wszystkich haseł, kluczy i wpisów tajnych, użyj `securestring` i `secureobject` typów, ponieważ `GET` operacji nie zwraca tych typów. | Typ parametru |
| defaultValue | Nie | Takie same jak `type` | Domyślna wartość parametru, jeśli wartość nie zostanie określona, gdy tworzy wystąpienie przepływu pracy | 
| allowedValues | Nie | Takie same jak `type` | Tablica wartości akceptujące parametr |  
| metadane | Nie | Obiekt JSON | Inne szczegóły parametrów, na przykład nazwę lub czytelny opis dla swojej aplikacji logiki lub danych czasu projektowania używanych przez program Visual Studio lub innych narzędzi |  
||||

## <a name="triggers-and-actions"></a>Wyzwalacze i akcje  

W definicji przepływu pracy `triggers` i `actions` sekcje definiują wywołania, które występują podczas wykonywania Twój przepływ pracy. Informacje o składni i dowiedzieć się więcej o tych sekcji, zobacz [wyzwalaczy przepływu pracy i działań](../logic-apps/logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Dane wyjściowe 

W `outputs` sekcji, definiują dane, które może zwracać przepływu pracy, po zakończeniu uruchamiania. Na przykład aby śledzić stan określonego lub wartości z poszczególnymi uruchomieniami, określić czy dane wyjściowe przepływu pracy zwraca dane. 

> [!NOTE]
> Podczas odpowiadania na żądania przychodzące z interfejsu API REST usługi, nie używaj `outputs`. Zamiast tego należy użyć `Response` typ akcji. Aby uzyskać więcej informacji, zobacz [wyzwalaczy przepływu pracy i działań](../logic-apps/logic-apps-workflow-actions-triggers.md).

Poniżej przedstawiono ogólną strukturę definicji danych wyjściowych dla: 

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
| <*Nazwa klucza*> | Yes | Ciąg | Nazwa klucza dla produktu wyjściowego zwracają wartość |  
| type | Yes | int, float, string, securestring, bool, tablicę, obiekt JSON | Typ dla wartości zwracanej w danych wyjściowych | 
| wartość | Yes | Takie same jak `type` | Wartość zwracana w danych wyjściowych |  
||||| 

Aby uzyskać dane wyjściowe z przebiegu przepływu pracy, Przejrzyj historię uruchomień aplikacji logiki i szczegółowe informacje w witrynie Azure portal lub [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows). Można również przekazać dane wyjściowe z systemami zewnętrznymi, na przykład usługi Power BI, dzięki czemu można tworzyć pulpity nawigacyjne. 

<a name="expressions"></a>

## <a name="expressions"></a>Wyrażenia

Za pomocą formatu JSON może mieć wartości literału, które istnieją w czasie projektowania, na przykład:

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

Również może mieć wartości, które nie istnieją do czasu wykonywania. Do reprezentowania tych wartości, można użyć *wyrażenia*, które są oceniane w czasie wykonywania. Wyrażenie jest sekwencji, który może zawierać jeden lub więcej [funkcje](#functions), [operatory](#operators), zmiennych, jawne wartości lub stałe. W definicji przepływu pracy, można użyć wyrażenia dowolnym miejscu w wartości ciągu JSON przez dodanie przedrostka wyrażenia ze znakiem w (\@). Podczas obliczania wyrażenia, która reprezentuje wartość JSON, treści wyrażenia jest wyodrębniany, usuwając \@ znaków i zawsze skutkuje inną wartość JSON. 

Na przykład uprzednio zdefiniowany `customerName` właściwości, można pobrać wartości właściwości, za pomocą [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) działać w wyrażeniach, a następnie przypisać tę wartość do `accountName` właściwości:

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

*Interpolacja ciągów* również pozwala używać wielu wyrażeń wewnątrz ciągów, które zostaną opakowane przy \@ znaków i nawiasy klamrowe ({}). Poniżej przedstawiono składnię:

```json
@{ "<expression1>", "<expression2>" }
```

Wynik to zawsze ciąg wprowadzania tej możliwości podobne do `concat()` funkcji, na przykład: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Jeśli masz ciąg literału, który rozpoczyna się od \@ znak, prefiks \@ znak z inną \@ znak jako znak ucieczki: \@\@

Poniższe przykłady pokazują, jak są obliczane wyrażenia:

| Wartość JSON | Wynik |
|------------|--------| 
| "Sophia Owen" | Zwraca następujące znaki: "Sophia Owen" |
| "tablica [1]" | Zwraca następujące znaki: "array [1]" |
| "\@\@" | Zwraca te znaki jako ciąg jednego znaku: "\@" |   
| " \@" | Zwracają te znaki jako ciąg dwóch znaków: " \@" |
|||

W poniższych przykładach Załóżmy, że należy zdefiniować "myBirthMonth" równa się "Stycznia" i "myAge" równą liczbie 42:  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

Poniższe przykłady pokazują, jak są obliczane następujących wyrażeń:

| Wyrażenie JSON | Wynik |
|-----------------|--------| 
| "\@parameters('myBirthMonth')" | Zwraca następujący ciąg: "Stycznia" |  
| "\@{parameters('myBirthMonth')}" | Zwraca następujący ciąg: "Stycznia" |  
| "\@parameters('myAge')" | Zwraca to numer: 42 |  
| "\@{parameters('myAge')}" | Zwraca ten numer jako ciąg znaków: "42" |  
| "Mój wiek to \@{parameters('myAge')}" | Zwraca następujący ciąg: "Mój wiek to 42" |  
| "\@concat ("Mój wiek to", string(parameters('myAge')))" | Zwraca następujący ciąg: "Mój wiek to 42" |  
| "Mój wiek to \@ \@{parameters('myAge')}" | Ten ciąg, który zawiera wyrażenie zwraca: "Mój wiek to \@{parameters('myAge')}" | 
||| 

Podczas pracy wizualnie w Projektancie aplikacji logiki, możesz utworzyć wyrażenia przez Konstruktor wyrażeń na przykład: 

![Projektant aplikacji logiki > Konstruktor wyrażeń](./media/logic-apps-workflow-definition-language/expression-builder.png)

Gdy wszystko będzie gotowe, znajduje się wyrażenie dla odpowiednich właściwości w definicji przepływu pracy, na przykład, `searchQuery` właściwości w tym miejscu:

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

W [wyrażeń](#expressions) i [funkcje](#functions), operatory wykonywania określonych zadań, takich jak odwołanie do właściwości lub wartość w tablicy. 

| Operator | Zadanie | 
|----------|------|
| ' | Aby użyć literału ciągu jako dane wejściowe lub w wyrażeniach i funkcje, opakowywanie ciąg tylko pojedynczy cudzysłów, na przykład `'<myString>'`. Nie należy używać podwójnego cudzysłowu (""), które powodują konflikt z formatowaniem JSON wokół całe wyrażenie. Na przykład: <p>**Tak**: length('Hello') </br>**Nie**: length("Hello") <p>Jeśli przekazujesz, tablic lub liczby, nie trzeba zawijanie znaków interpunkcyjnych. Na przykład: <p>**Tak**: długość ([1, 2, 3]) </br>**Nie**: długość ("[1, 2, 3]") | 
| [] | Aby odwołać się do wartości w określonym położeniu (indeks) w tablicy, Użyj nawiasów kwadratowych. Na przykład, aby uzyskać drugi element w tablicy: <p>`myArray[1]` | 
| . | Aby odwoływać się do właściwości w obiekcie, użyj operatora kropki. Na przykład, aby uzyskać `name` właściwość `customer` obiekt JSON: <p>`"@parameters('customer').name"` | 
| ? | Odwoływanie się do wartości null właściwości w obiekcie bez błędów środowiska uruchomieniowego, użyj operatora znaku zapytania. Na przykład do obsługi o wartości null dane wyjściowe z wyzwalaczem, możesz użyć tego wyrażenia: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Funkcje

Niektóre wyrażenia, Uzyskaj ich wartości z akcji środowiska uruchomieniowego, które jeszcze nie istnieje podczas uruchamiania aplikacji logiki. Aby odwołać się i pracować z tych wartości w wyrażeniach, można użyć [ *funkcje*](../logic-apps/workflow-definition-language-functions-reference.md). Na przykład, można użyć funkcje matematyczne do obliczeń, takich jak [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) funkcji, która zwraca sumę liczb całkowitych lub wartości zmiennoprzecinkowe. Aby uzyskać szczegółowe informacje dotyczące każdej funkcji, zobacz [artykułu alfabetyczny](../logic-apps/workflow-definition-language-functions-reference.md).
Można też uzyskać więcej informacji o funkcji i ich ogólnego przeznaczenia.

Poniżej przedstawiono kilka zadań przykładu, które można wykonywać za pomocą funkcji: 

| Zadanie | Składnia funkcji | Wynik | 
| ---- | --------------- | ------ | 
| Zwraca ciąg w formacie małe litery. | toLower ('<*tekstu*> ") <p>Na przykład: toLower('Hello') | "hello" | 
| Zwraca unikatowy identyfikator globalny (GUID). | GUID() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

Ten przykład przedstawia, jak można uzyskać wartości z `customerName` parametru i przypisanie tej wartości do `accountName` właściwości przy użyciu [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) funkcji w wyrażeniu:

```json
"accountName": "@parameters('customerName')"
```

Oto kilka innych sposobów ogólne używane funkcje w wyrażeniach:

| Zadanie | Składnia funkcji w wyrażeniu | 
| ---- | -------------------------------- | 
| Wykonaj pracy za pomocą elementu przez przekazanie tego elementu do funkcji. | "\@<*functionName*> (<*elementu*>)" | 
| 1. Pobierz *parameterName*firmy wartości za pomocą zagnieżdżonego `parameters()` funkcji. </br>2. Wykonywać pracę z wynikiem, przekazując wartość tego do *functionName*. | "\@<*functionName*> (parametry (" <*parameterName*> "))" | 
| 1. Uzyskiwanie wyników zagnieżdżonych funkcji wewnętrznej *functionName*. </br>2. Przekazać wynik do funkcji zewnętrznej *functionName2*. | "\@<*functionName2*> (<*functionName*> (<*elementu*>))" | 
| 1. Pobierz wynik z *functionName*. </br>2. Biorąc pod uwagę, że wynik jest obiekt z właściwością *propertyName*, wartość tej właściwości. | "\@<*functionName*>(<*item*>). <*propertyName*>" | 
||| 

Na przykład `concat()` funkcji może potrwać co najmniej dwóch wartości ciągu jako parametry. Ta funkcja łączy te ciągi w jeden ciąg. Możesz albo przekazać Literały ciągu, na przykład "Sophia" i "Owen", aby uzyskać ciąg połączone "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Alternatywnie można uzyskać wartości parametrów z parametrów. W tym przykładzie użyto `parameters()` funkcji w każdym `concat()` parametru i `firstName` i `lastName` parametrów. Następnie przekaż wynikowy ciągi, które `concat()` funkcji tak, aby uzyskać ciąg połączone, przykładowo "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

W obu przypadkach oba przykłady Przypisz wynik do `customerName` właściwości. 

Aby uzyskać szczegółowe informacje dotyczące każdej funkcji, zobacz [artykułu alfabetyczny](../logic-apps/workflow-definition-language-functions-reference.md).
Można też uzyskać więcej informacji o funkcji, w oparciu o ich ogólnego przeznaczenia.

<a name="string-functions"></a>

### <a name="string-functions"></a>Funkcje ciągów

Aby pracować z ciągów, można użyć tych funkcji ciąg, a także niektóre [kolekcji funkcji](#collection-functions). Funkcje ciągów działa tylko na ciągi. 

| String — funkcja | Zadanie | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Łączenie dwóch lub więcej ciągów, a następnie zwraca połączony ciąg. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Sprawdź, czy ciąg kończy się podanym podciągiem. | 
| [Identyfikator GUID](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generuj Unikatowy identyfikator globalny (GUID) jako ciąg. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Zwraca pozycję początkową podciąg. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Zwróć pozycji końcowej dla podciąg. | 
| [Zastąp](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Zamień podciąg określony ciąg i zwraca ciąg zaktualizowane. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Zwraca tablicę, która ma wszystkich znaków z ciągu i oddziela każdego znaku znakiem określonego ogranicznika. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Sprawdź, czy ciąg zaczyna się od określonego podciąg. | 
| [podciąg](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Zwraca znaki ciągu, zaczynając od określonej pozycji. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Zwraca ciąg w formacie małe litery. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Zwraca ciąg w formacie wielkie litery. | 
| [TRIM](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Usuń spacje wiodące i końcowe z ciągu i zwraca ciąg zaktualizowane. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Kolekcja funkcji

Aby pracować z kolekcji, zazwyczaj tablice, ciągi i czasami słowników, można użyć tych funkcji w kolekcji. 

| Kolekcja funkcji | Zadanie | 
| ------------------- | ---- | 
| [zawiera](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Sprawdź, czy kolekcja zawiera określony element. |
| [pusty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Sprawdź, czy kolekcja jest pusta. | 
| [pierwszy](../logic-apps/workflow-definition-language-functions-reference.md#first) | Zwraca pierwszy element z kolekcji. | 
| [Część wspólna](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Zwraca kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Zwraca ciąg, który ma *wszystkie* elementy z tablicy, oddzielone od określonego znaku. | 
| [ostatni](../logic-apps/workflow-definition-language-functions-reference.md#last) | Zwraca ostatni element z kolekcji. | 
| [Długość](../logic-apps/workflow-definition-language-functions-reference.md#length) | Zwraca liczbę elementów w tablicy lub ciągu. | 
| [Pomiń](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Usuń elementy z przodu kolekcji i zwracają *znajdują się wszystkie inne* elementów. | 
| [Wypełnij](../logic-apps/workflow-definition-language-functions-reference.md#take) | Zwraca elementy z przodu kolekcji. | 
| [Unia](../logic-apps/workflow-definition-language-functions-reference.md#union) | Zwraca kolekcję, która ma *wszystkie* elementy z określonymi kolekcjami. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Funkcje porównania

Do pracy z warunkami, Porównaj wartości i wyniki wyrażenia lub obliczyć różnego rodzaju logiki, można użyć tych funkcji porównywania. Aby uzyskać pełną dokumentację o każdej z nich, zobacz [artykułu alfabetyczny](../logic-apps/workflow-definition-language-functions-reference.md).

| Porównanie funkcji | Zadanie | 
| ------------------- | ---- | 
| [i](../logic-apps/workflow-definition-language-functions-reference.md#and) | Sprawdź, czy wszystkie wyrażenia mają wartość true. | 
| [równa się](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Sprawdź, czy obie wartości są równoważne. | 
| [większa](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Sprawdź, czy pierwsza wartość jest większa niż wartość drugiego. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Sprawdź, czy pierwsza wartość jest większa lub równa wartości drugiego. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Sprawdź, czy wyrażenie jest prawdziwe lub fałszywe. Na podstawie wyniku, zwrócić określoną wartość. | 
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Sprawdź, czy pierwsza wartość jest mniejsza od drugiej wartości. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Sprawdź, czy pierwsza wartość jest mniejsza niż lub równa wartości drugiego. | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Sprawdź, czy wyrażenie jest fałszywe. | 
| [lub](../logic-apps/workflow-definition-language-functions-reference.md#or) | Sprawdź, czy co najmniej jedno wyrażenie ma wartość true. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Funkcje konwersji

Aby zmienić typ lub format wartości, można użyć tych funkcji konwersji. Na przykład można zmienić wartości z wartością logiczną na liczbę całkowitą. Aby dowiedzieć się, jak Logic Apps obsługuje typy zawartości podczas konwersji, zobacz [Obsługa typów zawartości](../logic-apps/logic-apps-content-type.md). Aby uzyskać pełną dokumentację o każdej z nich, zobacz [artykułu alfabetyczny](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcja konwersji | Zadanie | 
| ------------------- | ---- | 
| [Tablica](../logic-apps/workflow-definition-language-functions-reference.md#array) | Zwraca tablicę z jednej z określonych danych wejściowych. Dla wielu danych wejściowych, zobacz [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [Base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Zwraca wersję algorytmem Base64 dla ciągu. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Zwraca binarną wersję ciągu zakodowanego algorytmem base64. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Zwraca ciąg wersji ciąg kodowany w formacie base64. | 
| [dane binarne](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Zwraca binarną wersję dla wartości wejściowej. | 
| [wartość logiczna](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Zwróć logiczna wersja dla wartości wejściowej. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Zwraca tablicę z wielu danych wejściowych. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Zwraca identyfikator URI danych wartości wejściowej. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Zwraca binarną wersję dla identyfikatora URI danych. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Zwraca wersję ciągu dla identyfikatora URI danych. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Zwraca ciąg wersji ciąg kodowany w formacie base64. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Zwraca binarną wersję dla identyfikatora URI danych. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Zwróć ciąg, zastępuje znak ucieczki znaków zdekodowanego wersje. | 
| [encodeuricomponent —](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Zwraca ciąg, który zastępuje adres URL niebezpiecznych znaków ze znakami ucieczki. | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Zwraca zmiennoprzecinkową punktu numer dla wartości wejściowej. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Zwróć wersja liczby całkowitej, ciągu. | 
| [JSON](../logic-apps/workflow-definition-language-functions-reference.md#json) | Zwraca wartość typu JavaScript Object Notation (JSON) lub obiekt ciągu lub kodu XML. | 
| [ciąg](../logic-apps/workflow-definition-language-functions-reference.md#string) | Zwraca wersję ciągu dla wartości wejściowej. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Zwraca wersję zakodowany jako identyfikator URI dla wartości wejściowej, zastępując znaki niebezpieczne adresu URL znaki ucieczki. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Zwraca binarną wersję na ciąg kodowany w formacie identyfikatora URI. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Zwraca ciąg wersji ciąg zakodowany jako identyfikator URI. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Zwraca ciąg w wersji XML. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Funkcje matematyczne

Aby pracować z liczbami całkowitymi i wartości zmiennoprzecinkowe, można użyć tych funkcji matematycznych. Aby uzyskać pełną dokumentację o każdej z nich, zobacz [artykułu alfabetyczny](../logic-apps/workflow-definition-language-functions-reference.md).

| Math — funkcja | Zadanie | 
| ------------- | ---- | 
| [Dodaj](../logic-apps/workflow-definition-language-functions-reference.md#add) | Zwróć wynik dodawania dwóch liczb. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Zwraca wynik dzielenia dwóch liczb. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Zwraca najwyższą wartość z zestawu numerów lub tablicy. | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Zwraca najmniejszą wartość z zestawu numerów lub tablicy. | 
| [dzielenie modulo](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Zwraca resztę z dzielenia dwóch liczb. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Zwraca iloczyn mnożenia dwóch liczb. | 
| [RAND](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Zwraca losową liczbę całkowitą z określonego zakresu. | 
| [Zakres](../logic-apps/workflow-definition-language-functions-reference.md#range) | Zwraca tablicę liczb całkowitych, który rozpoczyna się od określonej wartości całkowitej. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Zwraca wynik odejmowania drugą liczbę od pierwszej liczby. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Funkcje daty i godziny

Aby pracować z datami i godzinami, można użyć tych funkcji daty i godziny.
Aby uzyskać pełną dokumentację o każdej z nich, zobacz [artykułu alfabetyczny](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcja daty lub godziny | Zadanie | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Dodaj liczbę dni do sygnatury czasowej. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Dodaj liczbę godzin do sygnatury czasowej. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Dodaj liczbę minut do sygnatury czasowej. | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Dodaj liczbę sekund do sygnatury czasowej. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Dodaj liczbę jednostek czasu do sygnatury czasowej. Zobacz też [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Docelową strefę czasową, należy przekonwertować sygnatury czasowej względem uniwersalnego czasu koordynowanego (UTC). | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konwertowanie sygnatury czasowej ze źródłowej strefy czasowej uniwersalny czas koordynowany (UTC). | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Zwraca składnik dnia miesiąca z sygnatury czasowej. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Zwraca składnik dnia tygodnia z sygnatury czasowej. | 
| [Dzieńroku](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Zwraca składnik dnia roku z sygnatury czasowej. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Zwracanie dnia z sygnatury czasowej. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Zwraca bieżącą sygnaturę czasową, a także jednostek w określonym czasie. Zobacz też [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Zwraca bieżącą sygnaturę czasową minus jednostek w określonym czasie. Zobacz też [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Zwróć początek dnia sygnaturę czasową. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Zwróć początek godziny dla sygnatury czasowej. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Zwróć początek miesiąca dla sygnatury czasowej. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odjęcie liczby jednostek czasu z sygnatury czasowej. Zobacz też [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [znaczniki](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Zwróć `ticks` wartości właściwości dla określonej sygnatury czasowej. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Zwraca bieżącą sygnaturę czasową jako ciąg. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Funkcje przepływów pracy

Te funkcje przepływów pracy mogą pomóc:

* Uzyskaj szczegółowe informacje dotyczące wystąpienia przepływu pracy w czasie wykonywania. 
* Praca z danych wejściowych używana podczas tworzenia wystąpienia aplikacji logiki.
* Przywołują dane wyjściowe z wyzwalaczy i akcji.

Można na przykład przywołują dane wyjściowe z jedną akcję i użyć tych danych w późniejszym akcji. Aby uzyskać pełną dokumentację o każdej z nich, zobacz [artykułu alfabetyczny](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcja przepływu pracy | Zadanie | 
| ----------------- | ---- | 
| [Akcja](../logic-apps/workflow-definition-language-functions-reference.md#action) | Zwraca wynik bieżącej akcji, w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz też [akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Akcja zwrotu `body` dane wyjściowe w czasie wykonywania. Zobacz też [treści](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Zwraca wynik akcji w czasie wykonywania. Zobacz [akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [Akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Zwraca wynik akcji, w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz też [akcji](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [body](#body) | Akcja zwrotu `body` dane wyjściowe w czasie wykonywania. Zobacz też [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Utwórz tablicę przy użyciu wartości, które odpowiadają nazwę klucza w *dane formularza* lub *zakodowany w postaci* danych wyjściowych akcji. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Zwraca jedną wartość, która pasuje do nazwy klucza w celu wykonania akcji *dane formularza* lub *wynik zakodowany w postaci*. | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gdy wewnątrz powtarzającej się akcji, za pośrednictwem tablicy, zwraca bieżący element w tablicy podczas iteracji bieżącej akcji. | 
| [Elementy](../logic-apps/workflow-definition-language-functions-reference.md#items) | Gdy komputer znajduje się wewnątrz for-each lub wykonaj aż do pętli, zwracany bieżący element określonej pętli.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Zwróć "adres URL wywołania zwrotnego" wywołuje wyzwalacz lub akcję. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Zwraca treść dla konkretnego fragmentu w danych wyjściowych akcji, która ma wiele części. | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Zwraca wartość parametru, który jest opisany w definicji aplikacji logiki. | 
| [Wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Zwraca wynik tego wyzwalacza, w czasie wykonywania, lub z innych par nazw i wartości JSON. Zobacz też [triggerOutputs](#triggerOutputs) i [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Wróć do tego wyzwalacza `body` dane wyjściowe w czasie wykonywania. Zobacz [wyzwalacza](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Zwraca pojedynczą wartość odpowiadającą nazwę klucza w *dane formularza* lub *zakodowany w postaci* wyzwalacza danych wyjściowych. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Zwraca treść dla określonej części w wieloczęściowych danych wyjściowych wyzwalacza. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Utwórz tablicę, których wartości pasują nazwę klucza w *dane formularza* lub *zakodowany w postaci* wyzwalacza danych wyjściowych. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Zwracanie danych wyjściowych wyzwalacza, w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz [wyzwalacza](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [Zmienne](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Zwraca wartość określonej zmiennej. | 
| [Przepływ pracy](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Zwraca wszystkie szczegóły samego przepływu pracy w czasie wykonywania. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>Funkcje analizy identyfikatora URI

Aby pracować z uniform resource identifier (URI) i uzyskać różne wartości właściwości te identyfikatory URI, można użyć te funkcje analizy identyfikatora URI. Aby uzyskać pełną dokumentację o każdej z nich, zobacz [artykułu alfabetyczny](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcja analizy identyfikatora URI | Zadanie | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Zwróć `host` wartość dla identyfikatora uniform resource identifier (URI). | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Zwróć `path` wartość dla identyfikatora uniform resource identifier (URI). | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Zwróć `path` i `query` wartości dla identyfikatora uniform resource identifier (URI). | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Zwróć `port` wartość dla identyfikatora uniform resource identifier (URI). | 
| [Właściwość uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Zwróć `query` wartość dla identyfikatora uniform resource identifier (URI). | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Zwróć `scheme` wartość dla identyfikatora uniform resource identifier (URI). | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>Funkcje JSON i XML

Aby pracować z obiektami JSON i XML węzłów, można użyć tych funkcji manipulowania. Aby uzyskać pełną dokumentację o każdej z nich, zobacz [artykułu alfabetyczny](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkcję manipulowania | Zadanie | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Dodaj właściwość i jego wartość lub pary nazwa wartość w obiekcie JSON i zwraca zaktualizowany obiekt. | 
| [COALESCE](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Zwraca pierwszą wartość inną niż null z co najmniej jeden parametr. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Usuwanie właściwości z obiektu JSON i zwróć zaktualizowany obiekt. | 
| [Metoda setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ustaw wartość właściwości w obiekcie JSON i zwraca zaktualizowany obiekt. | 
| [wyrażenie XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Sprawdź XML węzłów lub wartości, które odpowiadają wyrażenie XPath (XML Path Language), a następnie zwracają pasujące węzły lub wartości. | 
||| 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [język definicji przepływów pracy akcji i wyzwalaczy](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Dowiedz się więcej o programowe tworzenie i zarządzanie aplikacjami logiki za pomocą [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows)
