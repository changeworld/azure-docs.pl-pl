---
title: Informacje dotyczące funkcji w języku definicji przepływu pracy — Azure Logic Apps i Microsoft Flow
description: Przewodnik referencyjny dotyczący funkcji w wyrażeniach utworzonych za pomocą języka definicji przepływu pracy dla Azure Logic Apps i Microsoft Flow
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 08/15/2018
ms.openlocfilehash: 8ad7d7497a0fbe6fcf20ad5b50ef4e6cac9ed51f
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868902"
---
# <a name="functions-reference-for-workflow-definition-language-in-azure-logic-apps-and-microsoft-flow"></a>Dokumentacja funkcji dla języka definicji przepływu pracy w Azure Logic Apps i Microsoft Flow

W przypadku definicji przepływu pracy w [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Microsoft Flow](https://docs.microsoft.com/flow/getting-started)niektóre [wyrażenia](../logic-apps/logic-apps-workflow-definition-language.md#expressions) pobierają wartości z akcji środowiska uruchomieniowego, które mogą jeszcze nie istnieć podczas uruchamiania przepływu pracy. Aby odwołać się do tych wartości lub przetworzyć wartości w tych wyrażeniach, można użyć *funkcji* udostępnianych przez [Język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Ta strona referencyjna dotyczy zarówno Azure Logic Apps, jak i Microsoft Flow, ale pojawia się w dokumentacji Azure Logic Apps. Mimo że ta strona odwołuje się głównie do usługi Logic Apps, te funkcje działają zarówno dla przepływów, jak i aplikacji logiki. Aby uzyskać więcej informacji o funkcjach i wyrażeniach w Microsoft Flow, zobacz [use Expressions in Conditions](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Na przykład można obliczyć wartości przy użyciu funkcji matematycznych, takich jak [Funkcja Add ()](../logic-apps/workflow-definition-language-functions-reference.md#add), jeśli chcesz, aby suma była sumą z liczb całkowitych lub zmiennoprzecinkowych. Oto kilka innych przykładowych zadań, które można wykonywać za pomocą funkcji:

| Zadanie | Składnia funkcji | Wynik |
| ---- | --------------- | ------ |
| Zwraca ciąg w formacie małymi literami. | toLower ("< >*tekstu*") <p>Na przykład: toLower ("Hello") | Hello |
| Zwraca unikatowy identyfikator globalny (GUID). | Identyfikator GUID () |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Aby znaleźć funkcje [na podstawie ich ogólnego przeznaczenia](#ordered-by-purpose), zapoznaj się z poniższymi tabelami. Lub, aby uzyskać szczegółowe informacje na temat każdej funkcji, zobacz [alfabetyczną listę](#alphabetical-list).

> [!NOTE]
> W składni definicji parametrów, znak zapytania (?), który pojawia się po parametrze oznacza, że parametr jest opcjonalny.
> Na przykład zobacz [getFutureTime ()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funkcje w wyrażeniach

Aby pokazać, jak używać funkcji w wyrażeniu, ten przykład pokazuje, jak można uzyskać wartość z `customerName` parametru i przypisać tę wartość `accountName` do właściwości przy użyciu funkcji [Parameters ()](#parameters) w wyrażeniu:

```json
"accountName": "@parameters('customerName')"
```

Poniżej przedstawiono inne ogólne sposoby używania funkcji w wyrażeniach:

| Zadanie | Składnia funkcji w wyrażeniu |
| ---- | -------------------------------- |
| Wykonaj prace z elementem, przekazując ten element do funkcji. | "\@FunctionName > (< elementu >)"< |
| 1. Pobierz wartość *parametruname*przy użyciu funkcji zagnieżdżonej `parameters()` . </br>2. Wykonaj prace z wynikiem, przekazując tę wartość do *funkcji FunctionName*. | "\@FunctionName > (parametry (" < ParameterName > "))"< |
| 1. Pobierz wynik z zagnieżdżonej funkcji *wewnętrznej.* </br>2. Przekaż wynik do funkcji zewnętrznej *functionName2*. | "\@functionName2 > (< FunctionName > (< elementu >))"< |
| 1. Pobierz wynik z *funkcji FunctionName*. </br>2. Uwzględniając, że wynik jest obiektem z właściwością *PropertyName*, Pobierz wartość tej właściwości. | "\@FunctionName > (< elementu >). < PropertyName >"< |
|||

Na przykład `concat()` funkcja może przyjmować dwie lub więcej wartości ciągu jako parametry. Ta funkcja łączy te ciągi w jeden ciąg.
Można przekazać literały ciągu, na przykład "Sophia" i "Owen", aby uzyskać ciąg połączony "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Lub można uzyskać wartości ciągu z parametrów. Ten przykład używa `parameters()` funkcji w każdym `concat()` parametrze i `firstName` parametrów i `lastName` . Następnie można przekazać wyniki do `concat()` funkcji, aby uzyskać ciąg połączony, na przykład "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

W obu przypadkach oba przykłady przypisują wynik do `customerName` właściwości.

Poniżej przedstawiono dostępne funkcje uporządkowane według ich ogólnego przeznaczenia lub można przeglądać funkcje w oparciu o [kolejność alfabetyczną](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funkcje ciągów

Aby korzystać z ciągów, można użyć tych funkcji ciągów, a także niektórych [funkcji kolekcji](#collection-functions).
Funkcje ciągów działają tylko na ciągach.

| Funkcja String | Zadanie |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Połącz dwa lub więcej ciągów i zwróć połączony ciąg. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Sprawdź, czy ciąg jest kończący się określonym podciągiem. |
| [ident](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generuj unikatowy identyfikator globalny (GUID) jako ciąg. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Zwróć pozycję początkową dla podciągu. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Zwróć pozycję początkową dla ostatniego wystąpienia podciągu. |
| [stępować](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Zamień podciąg na określony ciąg i zwróć zaktualizowany ciąg. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Zwraca tablicę zawierającą podciągi, rozdzieloną przecinkami, od większego ciągu na podstawie określonego znaku ogranicznika w oryginalnym ciągu. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Sprawdź, czy ciąg rozpoczyna się od określonego podciągu. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Zwraca znaki z ciągu, zaczynając od określonej pozycji. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Zwraca ciąg w formacie małymi literami. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Zwraca ciąg w formacie wielką literą. |
| [Trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Usuń spacje wiodące i końcowe z ciągu, a następnie Zwróć zaktualizowany ciąg. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funkcje kolekcji

Do pracy z kolekcjami, ogólnie tablicami, ciągami i czasami słownikami można używać tych funkcji kolekcji.

| Funkcja kolekcji | Zadanie |
| ------------------- | ---- |
| [wyświetlana](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Sprawdź, czy kolekcja zawiera określony element. |
| [ciągiem](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Sprawdź, czy kolekcja jest pusta. |
| [pierwszego](../logic-apps/workflow-definition-language-functions-reference.md#first) | Zwróć pierwszy element z kolekcji. |
| [część wspólną](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Zwróć kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gdy wewnątrz powtarzającej się akcji nad tablicą, zwraca bieżący element w tablicy podczas bieżącej iteracji akcji. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Zwraca ciąg, który zawiera *wszystkie* elementy z tablicy, oddzielone określonym znakiem. |
| [ostatniego](../logic-apps/workflow-definition-language-functions-reference.md#last) | Zwróć ostatni element z kolekcji. |
| [Długość](../logic-apps/workflow-definition-language-functions-reference.md#length) | Zwraca liczbę elementów w ciągu lub tablicy. |
| [Skocz](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy. |
| [czasochłonn](../logic-apps/workflow-definition-language-functions-reference.md#take) | Zwróć elementy z przodu kolekcji. |
| [Unii](../logic-apps/workflow-definition-language-functions-reference.md#union) | Zwraca kolekcję zawierającą *wszystkie* elementy z określonych kolekcji. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logiczne funkcje porównania

Aby działać z warunkami, porównywać wartości i wyniki wyrażeń lub oceniać różne rodzaje logiki, można użyć tych logicznych funkcji porównywania.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Logiczna funkcja porównywania | Zadanie |
| --------------------------- | ---- |
| [lub](../logic-apps/workflow-definition-language-functions-reference.md#and) | Sprawdź, czy wszystkie wyrażenia mają wartość PRAWDA. |
| [ubiegł](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Sprawdź, czy obie wartości są równoważne. |
| [mniejszą](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Sprawdź, czy pierwsza wartość jest większa od drugiej wartości. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Sprawdź, czy wyrażenie ma wartość true lub false. W oparciu o wynik Zwraca określoną wartość. |
| [wcześniejsz](../logic-apps/workflow-definition-language-functions-reference.md#less) | Sprawdź, czy pierwsza wartość jest mniejsza od drugiej wartości. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości. |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Sprawdź, czy wyrażenie ma wartość false. |
| [lub](../logic-apps/workflow-definition-language-functions-reference.md#or) | Sprawdź, czy co najmniej jedno wyrażenie ma wartość true. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funkcje konwersji

Aby zmienić typ lub format wartości, można użyć tych funkcji konwersji.
Na przykład można zmienić wartość z wartości logicznej na liczbę całkowitą.
Aby uzyskać więcej informacji na temat sposobu, w jaki Logic Apps obsługuje typy zawartości podczas konwersji, zobacz [Obsługa typów zawartości](../logic-apps/logic-apps-content-type.md).
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja konwersji | Zadanie |
| ------------------- | ---- |
| [macierzy](../logic-apps/workflow-definition-language-functions-reference.md#array) | Zwraca tablicę z pojedynczego określonego danych wejściowych. W przypadku wielu danych wejściowych [Zobacz sekcję](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Zwróć wersję z kodowaniem Base64 dla ciągu. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Zwróć wersję binarną dla ciągu zakodowanego algorytmem Base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64. |
| [binarny](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Zwróć wersję binarną dla wartości wejściowej. |
| [logiczna](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Zwróć wersję logiczną dla wartości wejściowej. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Zwróć tablicę z wielu danych wejściowych. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Zwróć identyfikator URI danych dla wartości wejściowej. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Zwróć wersję binarną dla identyfikatora URI danych. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Zwraca wersję ciągu dla identyfikatora URI danych. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Zwróć wersję binarną dla identyfikatora URI danych. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Zwraca ciąg, który zastępuje znaki ucieczki z zdekodowanymi wersjami. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Zwraca ciąg, który zastępuje znaki w postaci niebezpiecznej adresów URL. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Zwróć liczbę zmiennoprzecinkową dla wartości wejściowej. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Zwróć wersję całkowitą dla ciągu. |
| [kodu](../logic-apps/workflow-definition-language-functions-reference.md#json) | Zwróć wartość typu JavaScript Object Notation (JSON) lub obiekt dla ciągu lub XML. |
| [ciąg](../logic-apps/workflow-definition-language-functions-reference.md#string) | Zwraca wersję ciągu dla wartości wejściowej. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Zwróć wersję z kodowaniem URI dla wartości wejściowej przez zastępowanie znaków w adresie URL bez znaku ucieczki. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Zwróć wersję binarną dla ciągu zakodowanego za pomocą identyfikatora URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Zwraca wersję ciągu dla ciągu zakodowanego przy użyciu identyfikatora URI. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Zwraca wersję XML dla ciągu. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Funkcje matematyczne

Aby korzystać z liczb całkowitych i zmiennoprzecinkowych, można użyć tych funkcji matematycznych.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja matematyczna | Zadanie |
| ------------- | ---- |
| [dodana](../logic-apps/workflow-definition-language-functions-reference.md#add) | Zwróć wynik dodawania dwóch liczb. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Zwróć wynik dzielenia dwóch liczb. |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Zwraca najwyższą wartość z zestawu liczb lub tablicy. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Zwróć najniższą wartość z zestawu liczb lub tablicy. |
| [Funkcja](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Zwróć resztę z dzielenia dwóch liczb. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Zwróć produkt z mnożenia dwóch liczb. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Zwraca losową liczbę całkowitą z podanego zakresu. |
| [zakresu](../logic-apps/workflow-definition-language-functions-reference.md#range) | Zwróć tablicę liczb całkowitych, która zaczyna się od określonej liczby całkowitej. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Zwraca wynik odejmowania drugiej liczby od pierwszej liczby. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funkcje daty i godziny

Aby działać z datami i godzinami, można użyć tych funkcji daty i godziny.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja daty lub godziny | Zadanie |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Dodaj liczbę dni do sygnatury czasowej. |
| [addgodz.](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Dodaj liczbę godzin do sygnatury czasowej. |
| [addminut](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Dodaj liczbę minut do sygnatury czasowej. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Dodaj liczbę sekund do sygnatury czasowej. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Dodaj liczbę jednostek czasu do sygnatury czasowej. Zobacz również [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Przekształć sygnaturę czasową od uniwersalnego czasu koordynowanego (UTC) na docelową strefę czasową. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Przekonwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalne czas uniwersalny (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Zwróć dzień składnika miesiąca z sygnatury czasowej. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Zwróć dzień składnika tygodnia z sygnatury czasowej. |
| [dzieńroku](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Zwróć dzień składnika roku z sygnatury czasowej. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Zwróć datę z sygnatury czasowej. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Zwróć bieżącą sygnaturę czasową powiększoną o określoną liczbę jednostek czasu. Zobacz również [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Zwróć bieżącą sygnaturę czasową minus określoną liczbę jednostek czasu. Zobacz również [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Zwróć początek dnia dla sygnatury czasowej. |
| [Początek godziny](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Zwróć początek godziny dla sygnatury czasowej. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Zwróć początek miesiąca dla sygnatury czasowej. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odejmij wiele jednostek czasu od sygnatury czasowej. Zobacz również [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [taktów](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Zwraca wartość `ticks` właściwości dla określonego sygnatury czasowej. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Zwróć bieżącą sygnaturę czasową jako ciąg. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funkcje przepływu pracy

Te funkcje przepływu pracy mogą pomóc:

* Pobierz szczegóły dotyczące wystąpienia przepływu pracy w czasie wykonywania.
* Pracuj z danymi wejściowymi używanymi do tworzenia wystąpień aplikacji logiki lub przepływów.
* Odwołuje się do danych wyjściowych z wyzwalaczy i akcji.

Na przykład można odwoływać się do danych wyjściowych z jednej akcji i używać ich w późniejszej akcji.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja przepływu pracy | Zadanie |
| ----------------- | ---- |
| [transakcji](../logic-apps/workflow-definition-language-functions-reference.md#action) | Zwraca dane wyjściowe bieżącej akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz także [Akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Zwraca `body` dane wyjściowe akcji w czasie wykonywania. Zobacz także [treść](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Zwraca dane wyjściowe akcji w czasie wykonywania. Zobacz [Akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [wykonane](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Zwraca dane wyjściowe akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz również [Akcja](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [body](#body) | Zwraca `body` dane wyjściowe akcji w czasie wykonywania. Zobacz również [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Utwórz tablicę z wartościami, które pasują do nazwy klucza w danych wyjściowych akcji *form-Data* lub *form-Encoded* . |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Zwróć pojedynczą wartość, która pasuje do nazwy klucza w danych wyjściowych *formularza* akcji lub *formularza*. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gdy wewnątrz powtarzającej się akcji nad tablicą, zwraca bieżący element w tablicy podczas bieżącej iteracji akcji. |
| [produktów](../logic-apps/workflow-definition-language-functions-reference.md#items) | Gdy wewnątrz pętli Foreach lub do until zwraca bieżący element z określonej pętli.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | W pętli until należy zwrócić wartość indeksu bieżącej iteracji. Ta funkcja może być używana wewnątrz zagnieżdżonych pętli do. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Zwróć "adres URL wywołania zwrotnego", który wywołuje wyzwalacz lub akcję. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Zwróć treść określonej części w danych wyjściowych akcji z wieloma częściami. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Zwraca wartość parametru, który jest opisany w definicji przepływu pracy. |
| [uruchamiać](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub z innych par nazw i wartości JSON. Zobacz również [triggerOutputs](#triggerOutputs) i [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Zwraca `body` dane wyjściowe wyzwalacza w czasie wykonywania. Zobacz [wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Zwróć pojedynczą wartość odpowiadającą nazwie klucza w danych wyjściowych wyzwalacza *form-Data* lub *form* . |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Zwróć treść konkretnej części w wyjściu wyzwalacza. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Utwórz tablicę, której wartości pasują do nazwy klucza w danych wyjściowych wyzwalacza *form-Data* lub *form-Encoded* . |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz [wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [modyfikacj](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Zwraca wartość dla określonej zmiennej. |
| [utworzonego](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Zwróć wszystkie szczegóły dotyczące przepływu pracy w czasie wykonywania. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funkcje analizy identyfikatorów URI

Aby korzystać z identyfikatorów URI (Uniform Resource Identifier) i uzyskać różne wartości właściwości dla tych identyfikatorów URI, można użyć tych funkcji analizy URI.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja analizy identyfikatora URI | Zadanie |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | `host` Zwróć wartość identyfikatora URI (Uniform Resource Identifier). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | `path` Zwróć wartość identyfikatora URI (Uniform Resource Identifier). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Zwróć wartości `query` i dla identyfikatora URI (Uniform Resource Identifier). `path` |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | `port` Zwróć wartość identyfikatora URI (Uniform Resource Identifier). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | `query` Zwróć wartość identyfikatora URI (Uniform Resource Identifier). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | `scheme` Zwróć wartość identyfikatora URI (Uniform Resource Identifier). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funkcje manipulowania: KOD XML & JSON

Aby współdziałać z obiektami JSON i węzłami XML, można użyć tych funkcji manipulowania.
Aby uzyskać pełne informacje o każdej z tych funkcji, zobacz [alfabetyczną listę](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja manipulowania | Zadanie |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Dodaj właściwość i jej wartość lub parę nazwa-wartość do obiektu JSON i zwróć zaktualizowany obiekt. |
| [łączonych](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Zwróć pierwszą wartość inną niż null z co najmniej jednego parametru. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Usunięcie właściwości z obiektu JSON i zwrócenie zaktualizowanego obiektu. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ustaw wartość właściwości obiektu JSON i zwróć zaktualizowany obiekt. |
| [Lokalizacja](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Sprawdź XML dla węzłów lub wartości, które pasują do wyrażenia XPath (język ścieżki XML) i zwracają pasujące węzły lub wartości. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Wszystkie funkcje — Alfabetyczna lista

Ta sekcja zawiera listę wszystkich dostępnych funkcji w kolejności alfabetycznej.

<a name="action"></a>

### <a name="action"></a>Akcja

Zwraca dane wyjściowe *bieżącej* akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON, które można przypisać do wyrażenia.
Domyślnie ta funkcja odwołuje się do całego obiektu Action, ale można opcjonalnie określić właściwość, której wartość chcesz.
Zobacz także [Akcje ()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

`action()` Funkcji można używać tylko w tych miejscach:

* Właściwość akcji elementu webhook, aby można było uzyskać dostęp do wyniku z oryginalnego `subscribe`żądania `unsubscribe`
* `trackedProperties` Właściwość akcji
* Warunek `do-until` pętli dla akcji

```
action()
action().outputs.body.<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Nie | String | Nazwa właściwości obiektu akcji, której wartość ma być: **name**, **StartTime**, **Endtime**, Inputs,  **Output,** **status**, **Code**, **trackingId**i **clientTrackingId**. W Azure Portal można znaleźć te właściwości, przeglądając szczegóły konkretnej historii uruchamiania. Aby uzyskać więcej informacji, zobacz [działania interfejsu API REST — przepływ pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | -----| ----------- |
| <*Akcja — dane wyjściowe*> | String | Dane wyjściowe z bieżącej akcji lub właściwości |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Zwraca `body` dane wyjściowe akcji w czasie wykonywania.
Skrót dla `actions('<actionName>').outputs.body`.
Zobacz [treść ()](#body) i [Akcje ()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | String | Nazwa żądanego `body` danych wyjściowych akcji |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | -----| ----------- |
| <*Akcja — treść wyjściowa*> | Ciąg | `body` Dane wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera `body` dane wyjściowe z akcji `Get user`usługi Twitter:

```
actionBody('Get_user')
```

I zwraca ten wynik:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Zwraca dane wyjściowe akcji w czasie wykonywania.
Skrót dla `actions('<actionName>').outputs`.
Zobacz [Akcje ()](#actions).

```
actionOutputs('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | String | Nazwa żądanego danych wyjściowych akcji |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | -----| ----------- |
| <*rozdzielczości*> | Ciąg | Dane wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera dane wyjściowe z akcji `Get user`usługi Twitter:

```
actionOutputs('Get_user')
```

I zwraca ten wynik:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>wykonane

Zwraca dane wyjściowe akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON, które można przypisać do wyrażenia. Domyślnie funkcja odwołuje się do całego obiektu akcji, ale można opcjonalnie określić właściwość, której wartość chcesz.
W przypadku wersji skróconych zobacz [actionBody ()](#actionBody), [actionOutputs ()](#actionOutputs)i [Body ()](#body).
Aby uzyskać bieżącą akcję, zobacz [Akcja ()](#action).

> [!NOTE]
> Wcześniej można było użyć `actions()` funkcji `conditions` lub elementu przy określaniu, że akcja została uruchomiona na podstawie danych wyjściowych z innej akcji. Jednak aby zadeklarować jawnie zależności między akcjami, musisz teraz użyć `runAfter` właściwości zależnej akcji.
> Aby dowiedzieć się więcej `runAfter` na temat właściwości, zobacz [awarie catch i Handle z właściwością runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Yes | Ciąg | Nazwa obiektu akcji, dla którego chcesz uzyskać dane wyjściowe.  |
| <*wartość*> | Nie | Ciąg | Nazwa właściwości obiektu akcji, której wartość ma być: **name**, **StartTime**, **Endtime**, Inputs,  **Output,** **status**, **Code**, **trackingId**i **clientTrackingId**. W Azure Portal można znaleźć te właściwości, przeglądając szczegóły konkretnej historii uruchamiania. Aby uzyskać więcej informacji, zobacz [działania interfejsu API REST — przepływ pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | -----| ----------- |
| <*Akcja — dane wyjściowe*> | String | Dane wyjściowe z określonej akcji lub właściwości |
||||

*Przykład*

Ten przykład pobiera `status` wartość właściwości z akcji `Get user` Twitter w czasie wykonywania:

```
actions('Get_user').outputs.body.status
```

I zwraca ten wynik:`"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Zwróć wynik dodawania dwóch liczb.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Yes | Liczba całkowita, zmiennoprzecinkowa lub mieszana | Liczby do dodania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | -----| ----------- |
| <*wynik — suma*> | Liczba całkowita lub zmiennoprzecinkowa | Wynik dodawania określonych liczb |
||||

*Przykład*

Ten przykład dodaje określone liczby:

```
add(1, 1.5)
```

I zwraca ten wynik:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>Liczba dni

Dodaj liczbę dni do sygnatury czasowej.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | String | Ciąg zawierający sygnaturę czasową. |
| <*dni*> | Yes | Liczba całkowita | Dodatnia lub ujemna liczba dni do dodania |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Ciąg | Sygnatura czasowa powiększona o określoną liczbę dni  |
||||

*Przykład 1*

Ten przykład dodaje 10 dni do określonej sygnatury czasowej:

```
addDays('2018-03-15T13:00:00Z', 10)
```

I zwraca ten wynik:`"2018-03-25T00:00:0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć dni od określonej sygnatury czasowej:

```
addDays('2018-03-15T00:00:00Z', -5)
```

I zwraca ten wynik:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addgodz.

Dodaj liczbę godzin do sygnatury czasowej.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*liczb*> | Yes | Liczba całkowita | Dodatnia lub ujemna liczba godzin do dodania |
| <*Formatowanie*> | Nie | String | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Ciąg | Sygnatura czasowa powiększona o określoną liczbę godzin  |
||||

*Przykład 1*

Ten przykład dodaje 10 godzin do określonej sygnatury czasowej:

```
addHours('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik:`"2018-03-15T10:00:0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć godzin od określonej sygnatury czasowej:

```
addHours('2018-03-15T15:00:00Z', -5)
```

I zwraca ten wynik:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addminut

Dodaj liczbę minut do sygnatury czasowej.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | String | Ciąg zawierający sygnaturę czasową. |
| <*minut*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba minut do dodania |
| <*Formatowanie*> | Nie | String | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Sygnatura czasowa powiększona o określoną liczbę minut |
||||

*Przykład 1*

Ten przykład dodaje 10 minut do określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

I zwraca ten wynik:`"2018-03-15T00:20:00.0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć minut od określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

I zwraca ten wynik:`"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Dodaj właściwość i jej wartość lub parę nazwa-wartość do obiektu JSON i zwróć zaktualizowany obiekt. Jeśli obiekt już istnieje w czasie wykonywania, funkcja zgłasza błąd.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object*> | Tak | Object | Obiekt JSON, w którym chcesz dodać właściwość |
| <*wartość*> | Yes | Ciąg | Nazwa właściwości do dodania |
| <*wartościami*> | Yes | Any | Wartość właściwości |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Zaktualizowany obiekt JSON z określoną właściwością. |
||||

*Przykład*

Ten przykład dodaje `accountNumber` Właściwość `customerProfile` do obiektu, który jest konwertowany na notację JSON przy użyciu funkcji [JSON ()](#json) .
Funkcja przypisuje wartość wygenerowaną przez funkcję [GUID ()](#guid) i zwraca zaktualizowany obiekt:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>AddSeconds

Dodaj liczbę sekund do sygnatury czasowej.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg zawierający sygnaturę czasową. |
| <*s*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba sekund do dodania |
| <*Formatowanie*> | Nie | String | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Sygnatura czasowa powiększona o określoną liczbę sekund.  |
||||

*Przykład 1*

Ten przykład dodaje 10 sekund do określonej sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik:`"2018-03-15T00:00:10.0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć sekund do określonej sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

I zwraca ten wynik:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Dodaj liczbę jednostek czasu do sygnatury czasowej.
Zobacz również [getFutureTime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg zawierający sygnaturę czasową. |
| <*dat*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*timeUnit*> | Tak | String | Jednostka czasu do użycia z interwałem : "Sekunda", "minuta", "godzina", "dzień", "tydzień", "miesiąc", "Year" |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Sygnatura czasowa powiększona o określoną liczbę jednostek czasu  |
||||

*Przykład 1*

Ten przykład dodaje jeden dzień do określonego znacznika czasu:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik:`"2018-01-02T00:00:00.0000000Z"`

*Przykład 2*

Ten przykład dodaje jeden dzień do określonego znacznika czasu:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

I zwraca wynik przy użyciu opcjonalnego formatu "D":`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>i

Sprawdź, czy wszystkie wyrażenia mają wartość PRAWDA.
Zwraca wartość true, jeśli wszystkie wyrażenia są prawdziwe, lub zwraca wartość false, jeśli co najmniej jedno wyrażenie ma wartość false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*>,... | Tak | Boolean | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | -----| ----------- |
| prawda lub FAŁSZ | Boolean | Zwraca wartość true, jeśli wszystkie wyrażenia mają wartość true. Zwraca wartość false, jeśli co najmniej jedno wyrażenie ma wartość false. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy określone wartości logiczne są spełnione:

```
and(true, true)
and(false, true)
and(false, false)
```

I zwraca te wyniki:

* Pierwszy przykład: Oba wyrażenia są prawdziwe, dlatego zwraca `true`.
* Drugi przykład: Jedno wyrażenie ma wartość false, więc `false`zwraca wartość.
* Trzeci przykład: Oba wyrażenia mają wartość false, więc `false`zwraca wartość.

*Przykład 2*

Te przykłady sprawdzają, czy określone wyrażenia są prawdziwe:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

I zwraca te wyniki:

* Pierwszy przykład: Oba wyrażenia są prawdziwe, dlatego zwraca `true`.
* Drugi przykład: Jedno wyrażenie ma wartość false, więc `false`zwraca wartość.
* Trzeci przykład: Oba wyrażenia mają wartość false, więc `false`zwraca wartość.

<a name="array"></a>

### <a name="array"></a>array

Zwraca tablicę z pojedynczego określonego danych wejściowych.
W przypadku wielu danych wejściowych zobacz polecenie "XmlArray" [()](#createArray).

```
array('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Yes | String | Ciąg do tworzenia tablicy |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| [<*wartość*>] | Array | Tablica zawierająca pojedyncze określone dane wejściowe |
||||

*Przykład*

Ten przykład tworzy tablicę z ciągu "Hello":

```
array('hello')
```

I zwraca ten wynik:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Zwróć wersję z kodowaniem Base64 dla ciągu.

```
base64('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | String | Ciąg wejściowy |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*ciąg Base64*> | String | Wersja zakodowana algorytmem Base64 dla ciągu wejściowego |
||||

*Przykład*

Ten przykład konwertuje ciąg "Hello" na ciąg szyfrowany algorytmem Base64:

```
base64('hello')
```

I zwraca ten wynik:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Zwróć wersję binarną dla ciągu zakodowanego algorytmem Base64.

```
base64ToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | String | Ciąg zakodowany w formacie base64 do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*Binary-for-Base64-String*> | String | Wersja binarna dla ciągu zakodowanego algorytmem Base64 |
||||

*Przykład*

Ten przykład konwertuje ciąg "aGVsbG8 =" zakodowany algorytmem Base64 na ciąg binarny:

```
base64ToBinary('aGVsbG8=')
```

I zwraca ten wynik:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64, efektywnie dekodowanie ciągu Base64.
Użyj tej funkcji zamiast [decodeBase64 ()](#decodeBase64).
Chociaż obie funkcje działają w ten sam sposób `base64ToString()` , jest preferowane.

```
base64ToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Zakodowany w formacie base64 ciąg do zdekodowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*zdekodowane-Base64-String*> | String | Wersja ciągu dla ciągu zakodowanego algorytmem Base64 |
||||

*Przykład*

Ten przykład konwertuje ciąg "aGVsbG8 =" zakodowany algorytmem Base64 na tylko ciąg:

```
base64ToString('aGVsbG8=')
```

I zwraca ten wynik:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Zwróć wersję binarną ciągu.

```
binary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | String | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*binarne — wartość wejściowa*> | Ciąg | Wersja binarna określonego ciągu |
||||

*Przykład*

Ten przykład konwertuje ciąg "Hello" na ciąg binarny:

```
binary('hello')
```

I zwraca ten wynik:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>jednostce

Zwraca `body` dane wyjściowe akcji w czasie wykonywania.
Skrót dla `actions('<actionName>').outputs.body`.
Zobacz [actionBody ()](#actionBody) i [Akcje ()](#actions).

```
body('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Yes | Ciąg | Nazwa żądanego `body` danych wyjściowych akcji |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | -----| ----------- |
| <*Akcja — treść wyjściowa*> | String | `body` Dane wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera `body` dane wyjściowe `Get user` z akcji usługi Twitter:

```
body('Get_user')
```

I zwraca ten wynik:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

Zwróć wersję logiczną dla wartości.

```
bool(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Any | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Boolean | Wersja logiczna dla określonej wartości |
||||

*Przykład*

Te przykłady umożliwiają konwersję określonych wartości na wartości logiczne:

```
bool(1)
bool(0)
```

I zwraca te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>łączonych

Zwróć pierwszą wartość inną niż null z co najmniej jednego parametru.
Puste ciągi, puste tablice i puste obiekty nie mają wartości null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Yes | Any, można mieszać typy | Co najmniej jeden element do sprawdzenia dla wartości null |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*element pierwszy-inny niż null*> | Any | Pierwszy element lub wartość, która nie jest równa null. Jeśli wszystkie parametry mają wartość null, ta funkcja zwraca wartość null. |
||||

*Przykład*

Te przykłady zwracają pierwszą wartość o wartości innej niż null z określonych wartości lub wartości null, jeśli wszystkie wartości mają wartość null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

I zwraca te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`"hello"`
* Trzeci przykład:`null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Połącz dwa lub więcej ciągów i zwróć połączony ciąg.

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*tekst1*>, <*Tekst2*>,... | Tak | Ciąg | Co najmniej dwa ciągi do połączenia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*text1text2...* > | Ciąg | Ciąg utworzony na podstawie połączonych ciągów wejściowych |
||||

*Przykład*

Ten przykład łączy ciągi "Hello" i "World":

```
concat('Hello', 'World')
```

I zwraca ten wynik:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>zawiera

Sprawdź, czy kolekcja zawiera określony element.
Zwraca wartość true, jeśli element zostanie znaleziony lub zwraca wartość false, jeśli nie znaleziono.
Ta funkcja uwzględnia wielkość liter.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

W przypadku tej funkcji działa ona na następujących typach kolekcji:

* *Ciąg* , aby znaleźć *podciąg*
* *Tablica* , aby znaleźć *wartość*
* *Słownik* służący do znajdowania *klucza*

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Yes | Ciąg, tablica lub słownik | Kolekcja do sprawdzenia |
| <*wartościami*> | Tak | Odpowiednio ciąg, tablica lub słownik | Element do znalezienia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Boolean | Zwraca wartość true, jeśli element zostanie znaleziony. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza ciąg "Hello World" dla podciągu "World" i zwraca wartość true:

```
contains('hello world', 'world')
```

*Przykład 2*

Ten przykład sprawdza ciąg "Hello World" dla podciągu "Universe" i zwraca wartość false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Przekształć sygnaturę czasową od uniwersalnego czasu koordynowanego (UTC) na docelową strefę czasową.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*destinationTimeZone*> | Tak | String | Nazwa docelowej strefy czasowej. Aby uzyskać więcej informacji, zobacz [identyfikatory stref czasowych](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*Formatowanie*> | Nie | String | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Sygnatura czasowa konwertowana na docelową strefę czasową |
||||

*Przykład 1*

Ten przykład konwertuje sygnaturę czasową na określoną strefę czasową:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

I zwraca ten wynik:`"2018-01-01T00:00:00.0000000"`

*Przykład 2*

Ten przykład konwertuje sygnaturę czasową na określoną strefę czasową i format:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Przekonwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg zawierający sygnaturę czasową. |
| <*sourceTimeZone*> | Tak | String | Nazwa źródłowej strefy czasowej. Aby uzyskać więcej informacji, zobacz [identyfikatory stref czasowych](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*destinationTimeZone*> | Tak | String | Nazwa docelowej strefy czasowej. Aby uzyskać więcej informacji, zobacz [identyfikatory stref czasowych](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Sygnatura czasowa konwertowana na docelową strefę czasową |
||||

*Przykład 1*

Ten przykład konwertuje źródłową strefę czasową na docelową strefę czasową:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

I zwraca ten wynik:`"2018-01-01T00:00:00.0000000"`

*Przykład 2*

Ten przykład konwertuje strefę czasową na określoną strefę czasową i format:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalne czas uniwersalny (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg zawierający sygnaturę czasową. |
| <*sourceTimeZone*> | Tak | Ciąg | Nazwa źródłowej strefy czasowej. Aby uzyskać więcej informacji, zobacz [identyfikatory stref czasowych](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*Formatowanie*> | Nie | String | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Sygnatura czasowa konwertowana na UTC |
||||

*Przykład 1*

Ten przykład konwertuje sygnaturę czasową na czas UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

I zwraca ten wynik:`"2018-01-01T08:00:00.0000000Z"`

*Przykład 2*

Ten przykład konwertuje sygnaturę czasową na czas UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Zwróć tablicę z wielu danych wejściowych.
Dla pojedynczych tablic wejściowych zobacz [Array ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*obiekt1*>, <*Obiekt2*>,... | Yes | Wszystkie, ale nie mieszane | Co najmniej dwa elementy, aby utworzyć tablicę |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| [<*obiekt1*>, <*Obiekt2*>,...] | Array | Tablica utworzona na podstawie wszystkich elementów wejściowych |
||||

*Przykład*

Ten przykład tworzy tablicę z następujących danych wejściowych:

```
createArray('h', 'e', 'l', 'l', 'o')
```

I zwraca ten wynik:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Zwróć identyfikator URI (Uniform Resource Identifier) danych dla ciągu.

```
dataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Yes | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | Identyfikator URI danych dla ciągu wejściowego |
||||

*Przykład*

W tym przykładzie tworzony jest identyfikator URI danych dla ciągu "Hello":

```
dataUri('hello')
```

I zwraca ten wynik:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Zwróć wersję binarną dla identyfikatora URI (Uniform Resource Identifier) danych.
Użyj tej funkcji zamiast [decodeDataUri ()](#decodeDataUri).
Chociaż obie funkcje działają w ten sam sposób `dataUriBinary()` , jest preferowane.

```
dataUriToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Yes | String | Identyfikator URI danych do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*Binary-for-Data-URI*> | String | Wersja binarna identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie tworzona jest wersja binarna dla tego identyfikatora URI danych:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Zwraca wersję ciągu dla identyfikatora URI (Uniform Resource Identifier) danych.

```
dataUriToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | String | Identyfikator URI danych do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Ciąg | Wersja ciągu dla identyfikatora URI danych |
||||

*Przykład*

Ten przykład tworzy ciąg dla tego identyfikatora URI danych:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Zwróć dzień miesiąca z sygnatury czasowej.

```
dayOfMonth('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*dzień miesiąca*> | Liczba całkowita | Dzień miesiąca od określonej sygnatury czasowej |
||||

*Przykład*

Ten przykład zwraca liczbę dla dnia miesiąca z tej sygnatury czasowej:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

I zwraca ten wynik:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Zwróć dzień tygodnia z sygnatury czasowej.

```
dayOfWeek('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg zawierający sygnaturę czasową. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*dzień tygodnia*> | Liczba całkowita | Dzień tygodnia od określonej sygnatury czasowej, gdzie Niedziela ma wartość 0, poniedziałek wynosi 1 itd. |
||||

*Przykład*

Ten przykład zwraca liczbę dni tygodnia od tej sygnatury czasowej:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

I zwraca ten wynik:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dzieńroku

Zwróć dzień roku z sygnatury czasowej.

```
dayOfYear('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasową. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*dzień roku*> | Liczba całkowita | Dzień roku od określonej sygnatury czasowej |
||||

*Przykład*

Ten przykład zwraca numer dnia roku z tej sygnatury czasowej:

```
dayOfYear('2018-03-15T13:27:36Z')
```

I zwraca ten wynik:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Zwraca wersję ciągu dla ciągu zakodowanego algorytmem Base64, efektywnie dekodowanie ciągu Base64.
Rozważ użycie [base64ToString ()](#base64ToString) zamiast `decodeBase64()`.
Chociaż obie funkcje działają w ten sam sposób `base64ToString()` , jest preferowane.

```
decodeBase64('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Zakodowany w formacie base64 ciąg do zdekodowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*zdekodowane-Base64-String*> | Ciąg | Wersja ciągu dla ciągu zakodowanego algorytmem Base64 |
||||

*Przykład*

Ten przykład tworzy ciąg dla ciągu zakodowanego algorytmem Base64:

```
decodeBase64('aGVsbG8=')
```

I zwraca ten wynik:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Zwróć wersję binarną dla identyfikatora URI (Uniform Resource Identifier) danych.
Rozważ użycie [dataUriToBinary ()](#dataUriToBinary), a nie `decodeDataUri()`.
Chociaż obie funkcje działają w ten sam sposób `dataUriToBinary()` , jest preferowane.

```
decodeDataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | String | Ciąg identyfikatora URI danych do zdekodowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*Binary-for-Data-URI*> | String | Wersja binarna ciągu identyfikatora URI danych |
||||

*Przykład*

Ten przykład zwraca wersję binarną dla tego identyfikatora URI danych:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Zwraca ciąg, który zastępuje znaki ucieczki z zdekodowanymi wersjami.

```
decodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | String | Ciąg znaków ucieczki do zdekodowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | Zaktualizowany ciąg z zdekodowanymi znakami ucieczki |
||||

*Przykład*

Ten przykład zastępuje znaki ucieczki w tym ciągu z zdekodowanymi wersjami:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Zwraca liczbę całkowitą z wyniku dzielenia dwóch liczb.
Aby uzyskać resztę, zobacz [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*płacone*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik* |
| <*divisor*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba dzieląca *dzielną*, ale nie może być równa 0. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Liczba całkowita | Wynik z wartości całkowitej dzielącej pierwszą liczbę przez drugą liczbę |
||||

*Przykład*

Oba przykłady dzielą pierwszą liczbę o drugą liczbę:

```
div(10, 5)
div(11, 5)
```

I zwróć ten wynik:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeURIComponent —

Zwróć jednolity identyfikator zasobów (URI) dla ciągu przez zastępowanie znaków w adresie URL bez znaku ucieczki.
Rozważ użycie [uriComponent ()](#uriComponent), a nie `encodeUriComponent()`.
Chociaż obie funkcje działają w ten sam sposób `uriComponent()` , jest preferowane.

```
encodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Yes | Ciąg | Ciąg do przekonwertowania na format zakodowany przy użyciu identyfikatora URI |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | Ciąg znaków w formacie URI z znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzona jest wersja zakodowana przy użyciu identyfikatora URI dla tego ciągu:

```
encodeUriComponent('https://contoso.com')
```

I zwraca ten wynik:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>ciągiem

Sprawdź, czy kolekcja jest pusta.
Zwraca wartość true, jeśli kolekcja jest pusta lub zwraca wartość false, jeśli nie jest pusta.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg, tablica lub obiekt | Kolekcja do sprawdzenia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Boolean | Zwraca wartość true, jeśli kolekcja jest pusta. Zwraca wartość false, jeśli nie jest pusta. |
||||

*Przykład*

Te przykłady sprawdzają, czy określone kolekcje są puste:

```
empty('')
empty('abc')
```

I zwraca te wyniki:

* Pierwszy przykład: Przekazuje pusty ciąg, aby funkcja zwracała wartość `true`.
* Drugi przykład: Przekazuje ciąg "ABC", aby funkcja zwracała wartość `false`.

<a name="endswith"></a>

### <a name="endswith"></a>EndsWith

Sprawdź, czy ciąg jest kończący się określonym podciągiem.
Zwraca wartość true, jeśli znaleziono podciąg lub zwraca wartość false, jeśli nie znaleziono.
Ta funkcja nie uwzględnia wielkości liter.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg do sprawdzenia |
| <*searchText*> | Tak | Ciąg | Końcowy podciąg do znalezienia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ  | Boolean | Zwraca wartość true, gdy zostanie znaleziony końcowy podciąg. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza, czy ciąg "Hello World" jest zakończony ciągiem "World":

```
endsWith('hello world', 'world')
```

I zwraca ten wynik:`true`

*Przykład 2*

Ten przykład sprawdza, czy ciąg "Hello World" jest zakończony ciągiem "Universe":

```
endsWith('hello world', 'universe')
```

I zwraca ten wynik:`false`

<a name="equals"></a>

### <a name="equals"></a>równa się

Sprawdź, czy obie wartości, wyrażenia lub obiekty są równoważne.
Zwraca wartość true, jeśli oba są równoważne, lub zwraca wartość false, jeśli nie są równoważne.

```
equals('<object1>', '<object2>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*obiekt1*>, <*Obiekt2*> | Tak | Poszczególne | Wartości, wyrażenia lub obiekty do porównania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Boolean | Zwraca wartość true, jeśli oba są równoważne. Zwraca wartość false, jeśli nie jest równoważne. |
||||

*Przykład*

Te przykłady sprawdzają, czy określone dane wejściowe są równoważne.

```
equals(true, 1)
equals('abc', 'abcd')
```

I zwraca te wyniki:

* Pierwszy przykład: Obie wartości są równoważne, więc funkcja zwraca wartość `true`.
* Drugi przykład: Obie wartości nie są równoważne, więc funkcja zwraca `false`wartość.

<a name="first"></a>

### <a name="first"></a>pierwszego

Zwróć pierwszy element z ciągu lub tablicy.

```
first('<collection>')
first([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg lub tablica | Kolekcja, w której ma zostać znaleziony pierwszy element. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Any | Pierwszy element w kolekcji |
||||

*Przykład*

W tych przykładach znaleziono pierwszy element w tych kolekcjach:

```
first('hello')
first(createArray(0, 1, 2))
```

I zwróć następujące wyniki:

* Pierwszy przykład:`"h"`
* Drugi przykład:`0`

<a name="float"></a>

### <a name="float"></a>float

Konwertuj wersję ciągu dla liczby zmiennoprzecinkowej na rzeczywistą liczbę zmiennoprzecinkową.
Tej funkcji można użyć tylko w przypadku przekazywania parametrów niestandardowych do aplikacji, na przykład aplikacji logiki lub przepływu.

```
float('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg | Ciąg, który ma prawidłową liczbę zmiennoprzecinkową do przekonwertowania. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | Liczba zmiennoprzecinkowa dla podanego ciągu. |
||||

*Przykład*

W tym przykładzie tworzona jest wersja ciągu dla tej liczby zmiennoprzecinkowej:

```
float('10.333')
```

I zwraca ten wynik:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Zwróć sygnaturę czasową w określonym formacie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*Formatowanie*> | Nie | String | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | Zaktualizowana sygnatura czasowa w określonym formacie |
||||

*Przykład*

Ten przykład konwertuje sygnaturę czasową do określonego formatu:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

I zwraca ten wynik:`"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Zwraca tablicę z wartościami, które pasują do nazwy klucza w danych wyjściowych *formularza* akcji lub *formularza* .

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | Ciąg | Akcja, której dane wyjściowe mają żądaną wartość klucza |
| <*głównych*> | Yes | String | Nazwa klucza, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| [<*Array-with-Key-values*>] | Array | Tablica ze wszystkimi wartościami, które pasują do określonego klucza |
||||

*Przykład*

W tym przykładzie tworzona jest tablica z wartości klucza "subject" w danych wyjściowych formularza z danymi lub formularza:

```
formDataMultiValues('Send_an_email', 'Subject')
```

I zwraca tekst podmiotu w tablicy, na przykład:`["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Zwróć pojedynczą wartość, która pasuje do nazwy klucza w danych wyjściowych *formularza* akcji lub *formularza* .
Jeśli funkcja odnajdzie więcej niż jedno dopasowanie, funkcja zgłasza błąd.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | Ciąg | Akcja, której dane wyjściowe mają żądaną wartość klucza |
| <*głównych*> | Tak | String | Nazwa klucza, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*key-value*> | Ciąg | Wartość w określonym kluczu  |
||||

*Przykład*

W tym przykładzie tworzony jest ciąg z wartości klucza "subject" w danych wyjściowych formularza określonej przez daną akcję lub formularza:

```
formDataValue('Send_an_email', 'Subject')
```

I zwraca tekst podmiotu jako ciąg, na przykład:`"Hello world"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Zwróć bieżącą sygnaturę czasową powiększoną o określoną liczbę jednostek czasu.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*dat*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do odjęcia |
| <*timeUnit*> | Tak | String | Jednostka czasu do użycia z interwałem : "Sekunda", "minuta", "godzina", "dzień", "tydzień", "miesiąc", "Year" |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Bieżąca sygnatura czasowa powiększona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-03-01T00:00:00.0000000 Z".
Ten przykład dodaje pięć dni do tej sygnatury czasowej:

```
getFutureTime(5, 'Day')
```

I zwraca ten wynik:`"2018-03-06T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-03-01T00:00:00.0000000 Z".
Ten przykład dodaje pięć dni i konwertuje wynik na format "D":

```
getFutureTime(5, 'Day', 'D')
```

I zwraca ten wynik:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Zwróć bieżącą sygnaturę czasową minus określoną liczbę jednostek czasu.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*dat*> | Yes | Liczba całkowita | Liczba określonych jednostek czasu do odjęcia |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z interwałem : "Sekunda", "minuta", "godzina", "dzień", "tydzień", "miesiąc", "Year" |
| <*Formatowanie*> | Nie | String | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Bieżąca sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-02-01T00:00:00.0000000 Z".
Ten przykład odejmuje pięć dni od tej sygnatury czasowej:

```
getPastTime(5, 'Day')
```

I zwraca ten wynik:`"2018-01-27T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżąca sygnatura czasowa to "2018 r-02-01T00:00:00.0000000 Z".
Ten przykład odejmuje pięć dni i konwertuje wynik na format "D":

```
getPastTime(5, 'Day', 'D')
```

I zwraca ten wynik:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>mniejszą

Sprawdź, czy pierwsza wartość jest większa od drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest większa lub zwraca wartość false, jeśli jest mniejsza.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Yes | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy większa niż druga wartość |
| <*compareTo*> | Yes | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Wartość porównania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Boolean | Zwraca wartość true, jeśli pierwsza wartość jest większa niż druga wartość. Zwraca wartość false, gdy pierwsza wartość jest równa lub mniejsza od drugiej wartości. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest większa niż druga wartość:

```
greater(10, 5)
greater('apple', 'banana')
```

I zwróć następujące wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest większa lub równa lub zwraca wartość false, gdy pierwsza wartość jest mniejsza.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy większa lub równa drugiej wartości |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Wartość porównania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Boolean | Zwraca wartość true, jeśli pierwsza wartość jest większa lub równa drugiej wartości. Zwraca wartość false, jeśli pierwsza wartość jest mniejsza od drugiej wartości. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest większa lub równa drugiej wartości:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

I zwróć następujące wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="guid"></a>

### <a name="guid"></a>ident

Generuj unikatowy identyfikator globalny (GUID) jako ciąg, na przykład "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Ponadto można określić inny format dla identyfikatora GUID innego niż format domyślny, "D", który jest 32 cyfr oddzielonych łącznikiem.

```
guid('<format>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formatowanie*> | Nie | String | Pojedynczy [specyfikator formatu](https://msdn.microsoft.com/library/97af8hh4) dla ZWRÓCONEGO identyfikatora GUID. Domyślnie formatem jest "D", ale można użyć "N", "D", "B", "P" lub "X". |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*GUID-value*> | String | Losowo wygenerowany identyfikator GUID |
||||

*Przykład*

Ten przykład generuje ten sam identyfikator GUID, ale jako 32 cyfr, rozdzielone łącznikami i ujęte w nawiasy:

```
guid('P')
```

I zwraca ten wynik:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Sprawdź, czy wyrażenie ma wartość true lub false.
W oparciu o wynik Zwraca określoną wartość.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenia*> | Tak | Boolean | Wyrażenie do sprawdzenia |
| <*valueIfTrue*> | Tak | Any | Wartość, która ma zostać zwrócona, gdy wyrażenie ma wartość true. |
| <*valueIfFalse*> | Tak | Any | Wartość, która ma zostać zwrócona, gdy wyrażenie ma wartość false. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*określona — zwraca wartość*> | Any | Określona wartość, która zwraca w zależności od tego, czy wyrażenie ma wartość Prawda czy fałsz. |
||||

*Przykład*

Ten przykład zwraca `"yes"` , ponieważ określone wyrażenie zwraca wartość true.
W przeciwnym razie przykład zwraca `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Zwraca pozycję początkową lub wartość indeksu podciągu.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
indexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Yes | Ciąg | Ciąg, który zawiera podciąg do znalezienia |
| <*searchText*> | Tak | String | Podciąg do znalezienia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*index-value*>| Liczba całkowita | Pozycja początkowa lub wartość indeksu dla podanego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, Zwróć liczbę-1. |
||||

*Przykład*

W tym przykładzie znaleziono początkową wartość indeksu dla podciągu "World" w ciągu "Hello World":

```
indexOf('hello world', 'world')
```

I zwraca ten wynik:`6`

<a name="int"></a>

### <a name="int"></a>int

Zwróć wersję całkowitą dla ciągu.

```
int('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | String | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*Liczba całkowita-wynik*> | Liczba całkowita | Wersja całkowita dla podanego ciągu |
||||

*Przykład*

Ten przykład tworzy wersję całkowitą dla ciągu "10":

```
int('10')
```

I zwraca ten wynik:`10`

<a name="item"></a>

### <a name="item"></a>elementów

W przypadku użycia wewnątrz powtarzającej się akcji nad tablicą zwraca bieżący element w tablicy podczas bieżącej iteracji akcji.
Możesz również pobrać wartości z właściwości tego elementu.

```
item()
```

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Any | Bieżący element w tablicy dla bieżącej iteracji akcji |
||||

*Przykład*

Ten przykład pobiera `body` element z bieżącej wiadomości dla akcji "Send_an_email" wewnątrz bieżącej iteracji pętli for-each:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

Zwraca bieżący element z każdego cyklu w pętli for-each.
Użyj tej funkcji wewnątrz pętli for-each.

```
items('<loopName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Tak | String | Nazwa pętli for-each |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*elementów*> | Any | Element z bieżącego cyklu w określonej pętli for-each |
||||

*Przykład*

Ten przykład pobiera bieżący element z określonej pętli for-each:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Zwraca wartość indeksu bieżącej iteracji w pętli until. Ta funkcja może być używana wewnątrz zagnieżdżonych pętli do. 

```
iterationIndexes('<loopName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Yes | String | Nazwa pętli until | 
||||| 

| Wartość zwracana | Type | Opis | 
| ------------ | ---- | ----------- | 
| <*indeks*> | Liczba całkowita | Wartość indeksu bieżącej iteracji w pętli określonej do momentu | 
|||| 

*Przykład* 

Ten przykład tworzy zmienną licznika i zwiększa tę zmienną o jeden podczas każdej iteracji w pętli until do momentu osiągnięcia pięciu wartości licznika. W przykładzie tworzony jest również zmienna, która śledzi bieżący indeks dla każdej iteracji. W pętli until podczas każdej iteracji, przykład zwiększa licznik, a następnie przypisuje wartość licznika do bieżącej wartości indeksu, a następnie zwiększa licznik. W dowolnym momencie można określić bieżącą liczbę iteracji, pobierając bieżącą wartość indeksu.

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5),
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>kodu

Zwróć wartość typu JavaScript Object Notation (JSON) lub obiekt dla ciągu lub XML.

```
json('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Ciąg lub XML | Ciąg lub XML do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*Wynik JSON*> | Natywny typ lub obiekt JSON | Wartość lub obiekt natywnego typu JSON dla określonego ciągu lub XML. Jeśli ciąg ma wartość null, funkcja zwraca pusty obiekt. |
||||

*Przykład 1*

Ten przykład konwertuje ten ciąg na wartość JSON:

```
json('[1, 2, 3]')
```

I zwraca ten wynik:`[1, 2, 3]`

*Przykład 2*

Ten przykład konwertuje ten ciąg na format JSON:

```
json('{"fullName": "Sophia Owen"}')
```

I zwraca ten wynik:

```
{
  "fullName": "Sophia Owen"
}
```

*Przykład 3*

Ten przykład konwertuje ten kod XML na format JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

I zwraca ten wynik:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>część wspólną

Zwróć kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji.
Aby pojawił się w wyniku, element musi znajdować się we wszystkich kolekcjach przekazaniu do tej funkcji.
Jeśli co najmniej jeden element ma taką samą nazwę, w wyniku zostanie wyświetlony ostatni element o tej nazwie.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcji collection1*>, <*Collection2*>,... | Yes | Tablica lub obiekt, ale nie oba | Kolekcje, z których mają być *tylko* wspólne elementy |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*elementy wspólne*> | Odpowiednio tablica lub obiekt | Kolekcja, która ma tylko wspólne elementy w określonej kolekcji |
||||

*Przykład*

Ten przykład umożliwia znalezienie typowych elementów w następujących tablicach:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

I zwraca tablicę zawierającą *tylko* te elementy:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Zwraca ciąg, który zawiera wszystkie elementy z tablicy i zawiera każdy znak oddzielony ogranicznikiem.

```
join([<collection>], '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Array | Tablica zawierająca elementy do przyłączenia. |
| <*delimiter*> | Tak | String | Separator, który pojawia się między poszczególnymi znakami w ciągu wynikiem |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | String | Powstały ciąg utworzony na podstawie wszystkich elementów w określonej tablicy. |
||||

*Przykład*

Ten przykład tworzy ciąg ze wszystkich elementów w tej tablicy z określonym znakiem jako ogranicznikiem:

```
join(createArray('a', 'b', 'c'), '.')
```

I zwraca ten wynik:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>ostatniego

Zwróć ostatni element z kolekcji.

```
last('<collection>')
last([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg lub tablica | Kolekcja, w której ma zostać znaleziony ostatni element |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Odpowiednio ciąg lub tablicę | Ostatni element w kolekcji |
||||

*Przykład*

Te przykłady znajdują ostatni element w tych kolekcjach:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

I zwraca te wyniki:

* Pierwszy przykład:`"d"`
* Drugi przykład:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Zwraca pozycję początkową lub wartość indeksu dla ostatniego wystąpienia podciągu.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | Ciąg | Ciąg, który zawiera podciąg do znalezienia |
| <*searchText*> | Yes | String | Podciąg do znalezienia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Liczba całkowita | Pozycja początkowa lub wartość indeksu dla ostatniego wystąpienia określonego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, Zwróć liczbę-1. |
||||

*Przykład*

W tym przykładzie znaleziono początkową wartość indeksu dla ostatniego wystąpienia podciągu "World" w ciągu "Hello World":

```
lastIndexOf('hello world', 'world')
```

I zwraca ten wynik:`6`

<a name="length"></a>

### <a name="length"></a>length

Zwraca liczbę elementów w kolekcji.

```
length('<collection>')
length([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg lub tablica | Kolekcja zawierająca elementy do zliczenia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*Długość-lub-liczba*> | Liczba całkowita | Liczba elementów w kolekcji |
||||

*Przykład*

Te przykłady zliczają liczbę elementów w tych kolekcjach:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

I zwróć ten wynik:`4`

<a name="less"></a>

### <a name="less"></a>wcześniejsz

Sprawdź, czy pierwsza wartość jest mniejsza od drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Yes | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy mniejsza od drugiej wartości |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Element porównania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Boolean | Zwraca wartość true, jeśli pierwsza wartość jest mniejsza od drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest równa lub większa od drugiej wartości. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest mniejsza od drugiej wartości.

```
less(5, 10)
less('banana', 'apple')
```

I zwróć następujące wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub równa lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Yes | Liczba całkowita, zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy mniejsza lub równa drugiej wartości |
| <*compareTo*> | Tak | Odpowiednio liczby całkowite, zmiennoprzecinkowe lub ciąg | Element porównania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ  | Boolean | Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub równa drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest większa niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

I zwróć następujące wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Zwróć "adres URL wywołania zwrotnego", który wywołuje wyzwalacz lub akcję.
Ta funkcja działa tylko z wyzwalaczami i akcjami dla typów łączników **HttpWebhook** i **ApiConnectionWebhook** , ale nie z typami **ręcznymi**, **cyklem**, **http**i **APIConnection** .

```
listCallbackUrl()
```

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*callback-URL*> | Ciąg | Adres URL wywołania zwrotnego dla wyzwalacza lub akcji |
||||

*Przykład*

Ten przykład przedstawia przykładowy adres URL wywołania zwrotnego, który może zwrócić ta funkcja:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>Maksymalny

Zwraca najwyższą wartość z listy lub tablicy z liczbami, które są włącznie na obu końcach.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*liczba1*>, <*liczba2*>,... | Tak | Liczba całkowita, zmiennoprzecinkowa lub oba | Zbiór liczb, z których ma być najwyższa wartość |
| [<*liczba1*>, <*liczba2*>,...] | Tak | Array-Integer, float lub Both | Tablica liczb, z której ma być najwyższa wartość |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*max-value*> | Liczba całkowita lub zmiennoprzecinkowa | Najwyższa wartość w określonej tablicy lub zestawie liczb |
||||

*Przykład*

Te przykłady uzyskują najwyższą wartość z zestawu liczb i tablicy:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

I zwróć ten wynik:`3`

<a name="min"></a>

### <a name="min"></a>min.

Zwróć najniższą wartość z zestawu liczb lub tablicy.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*liczba1*>, <*liczba2*>,... | Yes | Liczba całkowita, zmiennoprzecinkowa lub oba | Zbiór liczb, z których ma być najmniejsza wartość. |
| [<*liczba1*>, <*liczba2*>,...] | Tak | Array-Integer, float lub Both | Tablica liczb, z której ma być najmniejsza wartość. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*wartość minimalna*> | Liczba całkowita lub zmiennoprzecinkowa | Najniższa wartość w określonym zestawie liczb lub określonej tablicy. |
||||

*Przykład*

Te przykłady uzyskują najniższą wartość w zestawie liczb i tablicy:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

I zwróć ten wynik:`1`

<a name="mod"></a>

### <a name="mod"></a>Funkcja

Zwróć resztę z dzielenia dwóch liczb.
Aby uzyskać wynik całkowity, zobacz [DIV ()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*płacone*> | Yes | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik* |
| <*divisor*> | Yes | Liczba całkowita lub zmiennoprzecinkowa | Liczba dzieląca *dzielną*, ale nie może być równa 0. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Liczba całkowita lub zmiennoprzecinkowa | Reszta z dzielenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

Ten przykład dzieli pierwszą liczbę przez drugą liczbę:

```
mod(3, 2)
```

I zwróć ten wynik:`1`

<a name="mul"></a>

### <a name="mul"></a>mul

Zwróć produkt z mnożenia dwóch liczb.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do pomnożenia przez *multiplicand2* |
| <*multiplicand2*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która *multiplicand1* wielokrotność |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*produkt — wynik*> | Liczba całkowita lub zmiennoprzecinkowa | Produkt nie pomnożyć pierwszej liczby przez drugą liczbę |
||||

*Przykład*

Te przykłady wielokrotności pierwszej liczby przez drugą liczbę:

```
mul(1, 2)
mul(1.5, 2)
```

I zwróć następujące wyniki:

* Pierwszy przykład:`2`
* Drugi przykład`3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Zwróć treść określonej części w danych wyjściowych akcji z wieloma częściami.

```
multipartBody('<actionName>', <index>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Yes | String | Nazwa akcji, która zawiera dane wyjściowe z wieloma częściami. |
| <*indeks*> | Tak | Liczba całkowita | Wartość indeksu dla potrzebnej części |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*body*> | Ciąg | Treść określonej części |
||||

<a name="not"></a>

### <a name="not"></a>niemożliwe

Sprawdź, czy wyrażenie ma wartość false.
Zwraca wartość true, jeśli wyrażenie ma wartość false, lub zwraca wartość false, gdy wartość jest równa true.

```json
not(<expression>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenia*> | Tak | Boolean | Wyrażenie do sprawdzenia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Boolean | Zwraca wartość true, jeśli wyrażenie ma wartość false. Zwraca wartość false, jeśli wyrażenie ma wartość true. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy określone wyrażenia mają wartość false:

```json
not(false)
not(true)
```

I zwróć następujące wyniki:

* Pierwszy przykład: Wyrażenie ma wartość false, więc funkcja zwraca `true`wartość.
* Drugi przykład: Wyrażenie ma wartość true, więc funkcja zwraca wartość `false`.

*Przykład 2*

Te przykłady sprawdzają, czy określone wyrażenia mają wartość false:

```json
not(equals(1, 2))
not(equals(1, 1))
```

I zwróć następujące wyniki:

* Pierwszy przykład: Wyrażenie ma wartość false, więc funkcja zwraca `true`wartość.
* Drugi przykład: Wyrażenie ma wartość true, więc funkcja zwraca wartość `false`.

<a name="or"></a>

### <a name="or"></a>lub

Sprawdź, czy co najmniej jedno wyrażenie ma wartość true.
Zwraca wartość true, jeśli co najmniej jedno wyrażenie ma wartość true lub zwraca wartość false, jeśli wszystkie mają wartość false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*>,... | Yes | Boolean | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ | Boolean | Zwraca wartość true, jeśli co najmniej jedno wyrażenie ma wartość true. Zwraca wartość false, jeśli wszystkie wyrażenia mają wartość false. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(true, false)
or(false, false)
```

I zwróć następujące wyniki:

* Pierwszy przykład: Co najmniej jedno wyrażenie ma wartość true, więc funkcja zwraca `true`wartość.
* Drugi przykład: Oba wyrażenia mają wartość false, więc funkcja zwraca `false`wartość.

*Przykład 2*

Te przykłady sprawdzają, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

I zwróć następujące wyniki:

* Pierwszy przykład: Co najmniej jedno wyrażenie ma wartość true, więc funkcja zwraca `true`wartość.
* Drugi przykład: Oba wyrażenia mają wartość false, więc funkcja zwraca `false`wartość.

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Zwraca wartość parametru, który jest opisany w definicji przepływu pracy.

```
parameters('<parameterName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Tak | Ciąg | Nazwa parametru, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Any | Wartość dla określonego parametru |
||||

*Przykład*

Załóżmy, że masz tę wartość JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Ten przykład pobiera wartość dla określonego parametru:

```
parameters('fullName')
```

I zwraca ten wynik:`"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>Rand

Zwraca losową liczbę całkowitą z podanego zakresu, która jest dopuszczająca tylko do końca początkowego.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Tak | Liczba całkowita | Najmniejsza liczba całkowita z zakresu |
| <*maxValue*> | Tak | Liczba całkowita | Liczba całkowita, która następuje po największej liczbie całkowitej z zakresu, który może zwracać funkcja |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*random-result*> | Liczba całkowita | Losowa liczba całkowita zwrócona z określonego zakresu |
||||

*Przykład*

Ten przykład pobiera losową liczbę całkowitą z podanego zakresu, z wyłączeniem wartości maksymalnej:

```
rand(1, 5)
```

I zwraca jedną z tych liczb jako wynik: `1`, `2`, `3`lub`4`

<a name="range"></a>

### <a name="range"></a>zakresu

Zwróć tablicę liczb całkowitych, która zaczyna się od określonej liczby całkowitej.

```
range(<startIndex>, <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Tak | Liczba całkowita | Wartość całkowita, która uruchamia tablicę jako pierwszy element |
| <*liczbą*> | Tak | Liczba całkowita | Liczba liczb całkowitych w tablicy |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| [<*zakres — wynik*>] | Array | Tablica z liczbami całkowitymi rozpoczynającymi się od określonego indeksu |
||||

*Przykład*

Ten przykład tworzy tablicę liczb całkowitych, która zaczyna się od określonego indeksu i ma określoną liczbę liczb całkowitych:

```
range(1, 4)
```

I zwraca ten wynik:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>stępować

Zamień podciąg na określony ciąg i zwróć ciąg wynikowy. Ta funkcja uwzględnia wielkość liter.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Tak | String | Ciąg zawierający podciąg, który ma zostać zamieniony. |
| <*oldText*> | Yes | String | Podciąg, który ma zostać zamieniony. |
| <*newText*> | Tak | String | Ciąg zamienny |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-text*> | Ciąg | Zaktualizowany ciąg po zamianie podciągu <p>Jeśli podciąg nie zostanie znaleziony, zwróć oryginalny ciąg. |
||||

*Przykład*

Ten przykład wyszukuje "stary" podciąg w "starym ciągu" i zamienia "stary" z "New":

```
replace('the old string', 'old', 'new')
```

I zwraca ten wynik:`"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Usunięcie właściwości z obiektu i zwrócenie zaktualizowanego obiektu.

```
removeProperty(<object>, '<property>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object*> | Yes | Object | Obiekt JSON, z którego chcesz usunąć Właściwość |
| <*wartość*> | Yes | Ciąg | Nazwa właściwości do usunięcia. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Zaktualizowany obiekt JSON bez określonej właściwości |
||||

*Przykład*

Ten przykład usuwa `"accountLocation"` Właściwość `"customerProfile"` z obiektu, który jest konwertowany na notację JSON przy użyciu funkcji [JSON ()](#json) i zwraca zaktualizowany obiekt:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Ustaw wartość właściwości obiektu i zwróć zaktualizowany obiekt.
Aby dodać nową właściwość, można użyć tej funkcji lub funkcji AddProperty [()](#addProperty) .

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object*> | Tak | Object | Obiekt JSON, którego właściwość ma zostać ustawiona. |
| <*wartość*> | Yes | String | Nazwa istniejącej lub nowej właściwości do ustawienia |
| <*wartościami*> | Tak | Any | Wartość do ustawienia dla określonej właściwości |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Zaktualizowany obiekt JSON, którego właściwość została ustawiona |
||||

*Przykład*

Ten przykład ustawia `"accountNumber"` Właściwość `"customerProfile"` obiektu, który jest konwertowany na notację JSON przy użyciu funkcji [JSON ()](#json) .
Funkcja przypisuje wartość wygenerowaną przez funkcję [GUID ()](#guid) i zwraca zaktualizowany obiekt JSON:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

### <a name="skip"></a>Skocz

Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy.

```
skip([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Array | Kolekcja, której elementy chcesz usunąć. |
| <*liczbą*> | Yes | Liczba całkowita | Dodatnia liczba całkowita liczby elementów do usunięcia na przedniej |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| [<*Zaktualizowano — zbieranie*>] | Array | Zaktualizowana kolekcja po usunięciu określonych elementów |
||||

*Przykład*

Ten przykład usuwa jeden element, liczbę 0 z przodu określonej tablicy:

```
skip(createArray(0, 1, 2, 3), 1)
```

I zwraca tę tablicę z pozostałymi elementami:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Zwraca tablicę zawierającą podciągi, rozdzieloną przecinkami, na podstawie określonego znaku ogranicznika w oryginalnym ciągu.

```
split('<text>', '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Yes | String | Ciąg do rozdzielenia na podciągi na podstawie określonego ogranicznika w oryginalnym ciągu |
| <*delimiter*> | Tak | String | Znak w oryginalnym ciągu, który ma być używany jako ogranicznik |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| [<*podciąg1*>, <*subciąg2*>,...] | Array | Tablica zawierająca podciągi z oryginalnego ciągu, rozdzielona przecinkami |
||||

*Przykład*

Ten przykład tworzy tablicę z podciągami z określonego ciągu na podstawie określonego znaku jako ogranicznika:

```
split('a_b_c', '_')
```

I zwraca tę tablicę jako wynik:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Zwróć początek dnia dla sygnatury czasowej.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg zawierający sygnaturę czasową. |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Określona sygnatura czasowa, ale rozpoczynająca się od znaku zero godziny na dzień |
||||

*Przykład*

Ten przykład umożliwia znalezienie początku dnia dla tej sygnatury czasowej:

```
startOfDay('2018-03-15T13:30:30Z')
```

I zwraca ten wynik:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>Początek godziny

Zwróć początek godziny dla sygnatury czasowej.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg zawierający sygnaturę czasową. |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Określona sygnatura czasowa, ale rozpoczynająca się od znaku 0 minuty dla godziny |
||||

*Przykład*

Ten przykład umożliwia znalezienie początku godziny dla tej sygnatury czasowej:

```
startOfHour('2018-03-15T13:30:30Z')
```

I zwraca ten wynik:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Zwróć początek miesiąca dla sygnatury czasowej.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg zawierający sygnaturę czasową. |
| <*Formatowanie*> | Nie | String | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Ciąg | Określona sygnatura czasowa, ale rozpoczyna się pierwszego dnia miesiąca w znaku 0-godzinnym |
||||

*Przykład*

Ten przykład zwraca początek miesiąca dla tej sygnatury czasowej:

```
startOfMonth('2018-03-15T13:30:30Z')
```

I zwraca ten wynik:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Sprawdź, czy ciąg rozpoczyna się od określonego podciągu.
Zwraca wartość true, jeśli znaleziono podciąg lub zwraca wartość false, jeśli nie znaleziono.
Ta funkcja nie uwzględnia wielkości liter.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Yes | String | Ciąg do sprawdzenia |
| <*searchText*> | Yes | Ciąg | Ciąg początkowy do znalezienia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| prawda lub FAŁSZ  | Boolean | Zwraca wartość true, gdy zostanie znaleziony początkowy podciąg. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza, czy ciąg "Hello World" rozpoczyna się od ciągu "Hello":

```
startsWith('hello world', 'hello')
```

I zwraca ten wynik:`true`

*Przykład 2*

Ten przykład sprawdza, czy ciąg "Hello World" rozpoczyna się od ciągu "Greetings":

```
startsWith('hello world', 'greetings')
```

I zwraca ten wynik:`false`

<a name="string"></a>

### <a name="string"></a>ciąg

Zwraca wersję ciągu dla wartości.

```
string(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | Any | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*string-value*> | Ciąg | Wersja ciągu dla określonej wartości. |
||||

*Przykład 1*

W tym przykładzie zostanie utworzona wersja ciągu dla tej liczby:

```
string(10)
```

I zwraca ten wynik:`"10"`

*Przykład 2*

Ten przykład tworzy ciąg dla określonego obiektu JSON i używa znaku ukośnika odwrotnego (\\) jako znaku ucieczki dla podwójnego znaku cudzysłowu (").

```
string( { "name": "Sophie Owen" } )
```

I zwraca ten wynik:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Zwraca wynik odejmowania drugiej liczby od pierwszej liczby.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*odjemna*> | Yes | Liczba całkowita lub zmiennoprzecinkowa | Liczba, z której ma zostać odjęta *odjemnik* |
| <*odjemnik*> | Yes | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która ma zostać odjęta od *odjemna* |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*wynika*> | Liczba całkowita lub zmiennoprzecinkowa | Wynik odejmowania drugiej liczby od pierwszej liczby |
||||

*Przykład*

Ten przykład odejmuje drugą liczbę od pierwszej liczby:

```
sub(10.3, .3)
```

I zwraca ten wynik:`10`

<a name="substring"></a>

### <a name="substring"></a>podciąg

Zwraca znaki z ciągu, zaczynając od określonej pozycji lub indeksu.
Wartości indeksu zaczynają się od liczby 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Yes | Ciąg | Ciąg, którego znaki mają być potrzebne. |
| <*startIndex*> | Tak | Liczba całkowita | Liczba dodatnia równa lub większa od 0, która ma być używana jako pozycja początkowa lub wartość indeksu. |
| <*Długość*> | Yes | Liczba całkowita | Dodatnia liczba znaków, które mają być używane w podciągu. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*substring-result*> | Ciąg | Podciąg z określoną liczbą znaków, zaczynając od określonej pozycji indeksu w ciągu źródłowym. |
||||

*Przykład*

Ten przykład tworzy znak podciągu pięć znaków z określonego ciągu, rozpoczynając od wartości indeksu 6:

```
substring('hello world', 6, 5)
```

I zwraca ten wynik:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Odejmij wiele jednostek czasu od sygnatury czasowej.
Zobacz również [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | Ciąg | Ciąg zawierający sygnaturę czasową. |
| <*dat*> | Yes | Liczba całkowita | Liczba określonych jednostek czasu do odjęcia |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z interwałem : "Sekunda", "minuta", "godzina", "dzień", "tydzień", "miesiąc", "Year" |
| <*Formatowanie*> | Nie | String | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Ciąg | Sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Ten przykład odejmuje jeden dzień od tej sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik:`"2018-01-01T00:00:00:0000000Z"`

*Przykład 2*

Ten przykład odejmuje jeden dzień od tej sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

I zwraca ten wynik przy użyciu opcjonalnego formatu "D":`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>czasochłonn

Zwróć elementy z przodu kolekcji.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*zbiera*> | Tak | Ciąg lub tablica | Kolekcja, której elementy chcesz |
| <*liczbą*> | Tak | Liczba całkowita | Dodatnia liczba całkowita liczby elementów, które mają być od przodu |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*podzbiór*> lub [*podzbiór*< >] | Odpowiednio ciąg lub tablicę | Ciąg lub tablica, która ma określoną liczbę elementów pobranych z przodu oryginalnej kolekcji |
||||

*Przykład*

Te przykłady pobierają określoną liczbę elementów z przodu tych kolekcji:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

I zwróć następujące wyniki:

* Pierwszy przykład:`"abc"`
* Drugi przykład:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>taktów

Zwraca wartość `ticks` właściwości dla określonego sygnatury czasowej.
*Cykl* jest interwałem 100-nanosekund.

```
ticks('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg dla sygnatury czasowej |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Liczba całkowita | Liczba taktów od określonej sygnatury czasowej |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Zwraca ciąg w formacie małymi literami. Jeśli znak w ciągu nie ma małych wersji, ten znak pozostaje niezmieniony w zwracanym ciągu.

```
toLower('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Yes | String | Ciąg do zwrócenia w formacie małymi literami |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*małe litery tekstu*> | Ciąg | Oryginalny ciąg w formacie małymi literami |
||||

*Przykład*

Ten przykład konwertuje ten ciąg na małe litery:

```
toLower('Hello World')
```

I zwraca ten wynik:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Zwraca ciąg w formacie wielką literą. Jeśli znak w ciągu nie ma Wielkiej wersji, ten znak pozostaje niezmieniony w zwracanym ciągu.

```
toUpper('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Yes | String | Ciąg do zwrócenia w formacie wielką literą |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*wielkie litery — tekst*> | String | Oryginalny ciąg w formacie wielką literą |
||||

*Przykład*

Ten przykład konwertuje ten ciąg na wielkie litery:

```
toUpper('Hello World')
```

I zwraca ten wynik:`"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Uruchamiać

Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazw i wartości JSON, które można przypisać do wyrażenia.

* W danych wejściowych wyzwalacza ta funkcja zwraca dane wyjściowe z poprzedniego wykonania.

* W warunku wyzwalacza ta funkcja zwraca dane wyjściowe z bieżącego wykonania.

Domyślnie funkcja odwołuje się do całego obiektu wyzwalacza, ale można opcjonalnie określić właściwość, której wartość chcesz.
Ponadto ta funkcja ma dostępne wersje skrócone, zobacz [triggerOutputs ()](#triggerOutputs) i [triggerBody ()](#triggerBody).

```
trigger()
```

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*wyzwalacz-wyjście*> | Ciąg | Dane wyjściowe wyzwalacza w czasie wykonywania |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Zwraca `body` dane wyjściowe wyzwalacza w czasie wykonywania.
Skrót dla `trigger().outputs.body`.
Zobacz [wyzwalacz ()](#trigger).

```
triggerBody()
```

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*wyzwalacz-treść — dane wyjściowe*> | String | `body` Dane wyjściowe wyzwalacza |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Zwraca tablicę z wartościami, które pasują do nazwy klucza w danych *wyjściowych* *formularza* wyzwalacza lub formularza.

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*głównych*> | Tak | Ciąg | Nazwa klucza, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| [<*Array-with-Key-values*>] | Array | Tablica ze wszystkimi wartościami, które pasują do określonego klucza |
||||

*Przykład*

Ten przykład tworzy tablicę z wartości klucza "feedUrl" w danych wyjściowych formularza wyzwalacza RSS lub formularza:

```
triggerFormDataMultiValues('feedUrl')
```

I zwraca tę tablicę jako przykładowy wynik:`["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Zwraca ciąg z pojedynczą wartością, która pasuje do nazwy klucza w danych wyjściowych *formularza* wyzwalacza lub  formularza.
Jeśli funkcja odnajdzie więcej niż jedno dopasowanie, funkcja zgłasza błąd.

```
triggerFormDataValue('<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*głównych*> | Yes | String | Nazwa klucza, którego wartość chcesz wykonać. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*key-value*> | String | Wartość w określonym kluczu |
||||

*Przykład*

Ten przykład tworzy ciąg z wartości klucza "feedUrl" w danych wyjściowych formularza wyzwalacza RSS lub formularza:

```
triggerFormDataValue('feedUrl')
```

I zwraca ten ciąg jako przykładowy wynik:`"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Zwróć treść określonej części w danych wyjściowych wyzwalacza, który ma wiele części.

```
triggerMultipartBody(<index>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*indeks*> | Tak | Liczba całkowita | Wartość indeksu dla potrzebnej części |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*body*> | Ciąg | Treść określonej części w wyzwalaczu z wieloczęściowym wynikiem |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazw i wartości JSON.
Skrót dla `trigger().outputs`.
Zobacz [wyzwalacz ()](#trigger).

```
triggerOutputs()
```

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*wyzwalacz-wyjście*> | String | Dane wyjściowe wyzwalacza w czasie wykonywania  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trim

Usuń spacje wiodące i końcowe z ciągu, a następnie Zwróć zaktualizowany ciąg.

```
trim('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Opis*> | Yes | Ciąg | Ciąg, który zawiera spacje wiodące i końcowe do usunięcia |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updatedText*> | Ciąg | Zaktualizowana wersja oryginalnego ciągu bez spacji wiodących lub końcowych |
||||

*Przykład*

Ten przykład usuwa spacje wiodące i końcowe z ciągu "Hello world":

```
trim(' Hello World  ')
```

I zwraca ten wynik:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>Unii

Zwraca kolekcję zawierającą *wszystkie* elementy z określonych kolekcji.
Aby pojawił się w wyniku, element może pojawić się w dowolnej kolekcji przekazaną do tej funkcji. Jeśli co najmniej jeden element ma taką samą nazwę, w wyniku zostanie wyświetlony ostatni element o tej nazwie.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcji collection1*>, <*Collection2*>,...  | Tak | Tablica lub obiekt, ale nie oba | Kolekcje, z których mają być *wszystkie* elementy |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Odpowiednio tablica lub obiekt | Kolekcja zawierająca wszystkie elementy z określonych kolekcji — brak duplikatów |
||||

*Przykład*

Ten przykład pobiera *wszystkie* elementy z tych kolekcji:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

I zwraca ten wynik:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Zwróć jednolity identyfikator zasobów (URI) dla ciągu przez zastępowanie znaków w adresie URL bez znaku ucieczki.
Użyj tej funkcji zamiast [encodeURIComponent — ()](#encodeUriComponent).
Chociaż obie funkcje działają w ten sam sposób `uriComponent()` , jest preferowane.

```
uriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Yes | String | Ciąg do przekonwertowania na format zakodowany przy użyciu identyfikatora URI |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Ciąg | Ciąg znaków w formacie URI z znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzona jest wersja zakodowana przy użyciu identyfikatora URI dla tego ciągu:

```
uriComponent('https://contoso.com')
```

I zwraca ten wynik:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Zwróć wersję binarną dla składnika Uniform Resource Identifier (URI).

```
uriComponentToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | String | Ciąg zakodowany przy użyciu identyfikatora URI do przekonwertowania |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*binarny dla kodowanego identyfikatora URI*> | String | Wersja binarna dla ciągu zakodowanego przy użyciu identyfikatora URI. Zawartość binarna jest zakodowana algorytmem Base64 i `$content`reprezentowana przez. |
||||

*Przykład*

W tym przykładzie tworzona jest wersja binarna dla tego ciągu zakodowanego przy użyciu identyfikatora URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Zwróć wersję ciągu dla zakodowanego ciągu identyfikatora URI (Uniform Resource Identifier) w celu efektywnego dekodowania ciągu zakodowanego przy użyciu identyfikatora URI.

```
uriComponentToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Yes | String | Ciąg zakodowany przy użyciu identyfikatora URI, który ma zostać zdekodowany |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | Zdekodowana wersja dla ciągu zakodowanego przy użyciu identyfikatora URI |
||||

*Przykład*

Ten przykład tworzy zdekodowaną wersję ciągu dla tego ciągu kodowanego URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:`"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

`host` Zwróć wartość identyfikatora URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*adresu*> | Tak | String | Identyfikator URI, `host` którego wartość chcesz |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*host-value*> | String | `host` Wartość dla określonego identyfikatora URI |
||||

*Przykład*

Ten przykład umożliwia znalezienie `host` wartości dla tego identyfikatora URI:

```
uriHost('https://www.localhost.com:8080')
```

I zwraca ten wynik:`"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

`path` Zwróć wartość identyfikatora URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*adresu*> | Tak | String | Identyfikator URI, `path` którego wartość chcesz |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*ścieżka — wartość*> | String | `path` Wartość dla określonego identyfikatora URI. Jeśli `path` nie ma wartości, zwróć znak "/". |
||||

*Przykład*

Ten przykład umożliwia znalezienie `path` wartości dla tego identyfikatora URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik:`"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Zwróć wartości `query` i dla identyfikatora URI (Uniform Resource Identifier). `path`

```
uriPathAndQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*adresu*> | Yes | String | Identyfikator URI, `path` którego `query` chcesz użyć, i wartości |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*Path-Query-Value*> | String | Wartości `path` i`query` dla określonego identyfikatora URI. Jeśli `path` wartość nie zostanie określona, zwraca znak "/". |
||||

*Przykład*

Ten przykład umożliwia znalezienie `path` wartości `query` i dla tego identyfikatora URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik:`"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

`port` Zwróć wartość identyfikatora URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*adresu*> | Tak | Ciąg | Identyfikator URI, `port` którego wartość chcesz |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*port-value*> | Liczba całkowita | `port` Wartość dla określonego identyfikatora URI. Jeśli `port` wartość nie zostanie określona, zwraca domyślny port dla protokołu. |
||||

*Przykład*

Ten przykład zwraca `port` wartość dla tego identyfikatora URI:

```
uriPort('http://www.localhost:8080')
```

I zwraca ten wynik:`8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

`query` Zwróć wartość identyfikatora URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*adresu*> | Yes | String | Identyfikator URI, `query` którego wartość chcesz |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*query-value*> | String | `query` Wartość dla określonego identyfikatora URI |
||||

*Przykład*

Ten przykład zwraca `query` wartość dla tego identyfikatora URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik:`"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

`scheme` Zwróć wartość identyfikatora URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*adresu*> | Tak | Ciąg | Identyfikator URI, `scheme` którego wartość chcesz |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*scheme-value*> | String | `scheme` Wartość dla określonego identyfikatora URI |
||||

*Przykład*

Ten przykład zwraca `scheme` wartość dla tego identyfikatora URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik:`"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Zwróć bieżącą sygnaturę czasową.

```
utcNow('<format>')
```

Opcjonalnie można określić inny format z parametrem > <*Format*.


| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formatowanie*> | Nie | Ciąg | [Pojedynczy specyfikator formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatowania niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej to ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDTgg: mm: SS: fffffffK), który jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | Ciąg | Bieżąca data i godzina |
||||

*Przykład 1*

Załóżmy, że dzisiaj to 15 kwietnia 2018 o 1:00:00 PM.
Ten przykład pobiera bieżącą sygnaturę czasową:

```
utcNow()
```

I zwraca ten wynik:`"2018-04-15T13:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że dzisiaj to 15 kwietnia 2018 o 1:00:00 PM.
Ten przykład pobiera bieżącą sygnaturę czasową przy użyciu opcjonalnego formatu "D":

```
utcNow('D')
```

I zwraca ten wynik:`"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Modyfikacj

Zwraca wartość dla określonej zmiennej.

```
variables('<variableName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Tak | Ciąg | Nazwa zmiennej, której wartość chcesz wykonać. |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*Zmienna-wartość*> | Any | Wartość dla określonej zmiennej |
||||

*Przykład*

Załóżmy, że bieżąca wartość dla zmiennej "numItems" wynosi 20.
Ten przykład pobiera wartość całkowitą dla tej zmiennej:

```
variables('numItems')
```

I zwraca ten wynik:`20`

<a name="workflow"></a>

### <a name="workflow"></a>przepływ pracy

Zwróć wszystkie szczegóły dotyczące przepływu pracy w czasie wykonywania.

```
workflow().<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartość*> | Nie | String | Nazwa właściwości przepływu pracy, której wartość ma być <p>Obiekt przepływu pracy ma następujące właściwości: **name**, **Type**, **ID**, **Location**i **Run**. Wartość właściwości **Run** jest również obiektem, który ma następujące właściwości: **name**, **Type**i **ID**. |
|||||

*Przykład*

Ten przykład zwraca nazwę bieżącego przebiegu przepływu pracy:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Zwróć wersję XML dla ciągu, który zawiera obiekt JSON.

```
xml('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wartościami*> | Tak | String | Ciąg z obiektem JSON do przekonwertowania <p>Obiekt JSON musi mieć tylko jedną właściwość root, która nie może być tablicą. <br>Użyj znaku ukośnika odwrotnego\\() jako znaku ucieczki dla podwójnego cudzysłowu ("). |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*Wersja XML*> | Object | Zakodowany kod XML dla określonego ciągu lub obiektu JSON. |
||||

*Przykład 1*

W tym przykładzie jest tworzona wersja XML dla tego ciągu, który zawiera obiekt JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

I zwraca ten wynik XML:

```xml
<name>Sophia Owen</name>
```

*Przykład 2*

Załóżmy, że masz ten obiekt JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Ten przykład tworzy kod XML dla ciągu, który zawiera ten obiekt JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

I zwraca ten wynik XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Lokalizacja

Sprawdź XML dla węzłów lub wartości, które pasują do wyrażenia XPath (język ścieżki XML) i zwracają pasujące węzły lub wartości. Wyrażenie XPath lub "XPath" ułatwia nawigowanie po strukturze dokumentu XML, dzięki czemu można wybrać węzły lub wartości obliczeniowe w zawartości XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Tak | Any | Ciąg XML służący do wyszukiwania węzłów lub wartości, które pasują do wartości wyrażenia XPath. |
| <*Lokalizacja*> | Tak | Any | Wyrażenie XPath używane do znajdowania pasujących węzłów lub wartości XML |
|||||

| Wartość zwracana | Type | Opis |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Węzeł XML, gdy tylko jeden węzeł pasuje do określonego wyrażenia XPath |
| <*wartościami*> | Any | Wartość z węzła XML, gdy tylko jedna wartość jest zgodna z określonym wyrażeniem XPath |
| [<*XML-węzeł1*>, <*xml-Węzeł2*>,...] </br>—lub— </br>[<*wartość1*>, <*wartość2*>,...] | Array | Tablica z węzłami lub wartościami XML, które pasują do określonego wyrażenia XPath |
||||

*Przykład 1*

Ten przykład umożliwia znalezienie węzłów, które `<name></name>` pasują do węzła w określonych argumentach, i zwraca tablicę z tymi wartościami węzła:

`xpath(xml(parameters('items')), '/produce/item/name')`

Oto argumenty:

* Ciąg "Items", który zawiera ten kod XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  W przykładzie użyto funkcji [Parameters ()](#parameters) , aby uzyskać ciąg XML z argumentu "Items", ale musi także przekonwertować ciąg na format XML przy użyciu funkcji [XML ()](#xml) .

* To wyrażenie XPath, które jest przesyłane jako ciąg:

  `"/produce/item/name"`

Oto tablica wyników z węzłami, które pasują `<name></name`do:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Przykład 2*

Jak pokazano na przykładzie 1, ten przykład odnajduje węzły, `<count></count>` które pasują do węzła i dodaje te `sum()` wartości węzła przy użyciu funkcji:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

I zwraca ten wynik:`30`

*Przykład 3*

W tym przykładzie oba wyrażenia znajdą węzły, które pasują do `<location></location>` węzła, w określonych argumentach, które zawierają XML z przestrzenią nazw. Wyrażenia używają znaku ukośnika odwrotnego (\\) jako znaku ucieczki dla podwójnego cudzysłowu (").

* *Wyrażenie 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Wyrażenie 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Oto argumenty:

* Ten plik XML, który zawiera przestrzeń nazw dokumentu XML `xmlns="http://contoso.com"`,:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Wyrażenie XPath tutaj:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Oto węzeł wynikowy zgodny z `<location></location>` węzłem:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Przykład 4*

Poniższy przykład dotyczy znalezienia wartości w `<location></location>` węźle:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

I zwraca ten wynik:`"Paris"`

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [języku definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md)
