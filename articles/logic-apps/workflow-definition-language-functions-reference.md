---
title: Dokumentacja dotycząca funkcji język definicji przepływów pracy — Azure Logic Apps i Microsoft Flow
description: Przewodnik dla funkcji w wyrażeniach utworzone za pomocą języka definicji przepływu pracy dla usługi Azure Logic Apps i Microsoft Flow
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 08/15/2018
ms.openlocfilehash: f5e2af7a7118eaa95e43049b3594ffd584aad4cc
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203074"
---
# <a name="functions-reference-for-workflow-definition-language-in-azure-logic-apps-and-microsoft-flow"></a>Dokumentacja funkcji programu język definicji przepływów pracy w usłudze Azure Logic Apps i Microsoft Flow

Dla definicji przepływu pracy w [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Microsoft Flow](https://docs.microsoft.com/flow/getting-started), niektóre [wyrażeń](../logic-apps/logic-apps-workflow-definition-language.md#expressions) uzyskać ich wartości z akcji środowiska uruchomieniowego, które jeszcze nie istnieje podczas przepływu pracy zostanie uruchomiony. Aby odwołać te wartości lub przetwarzać wartości w tych wyrażeniach, można użyć *funkcje* dostarczone przez [język definicji przepływów pracy](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Ta strona odwołania dotyczy zarówno usługi Azure Logic Apps i Microsoft Flow, ale pojawia się w dokumentacji usługi Azure Logic Apps. Mimo że ta strona odnosi się to do aplikacji logiki, funkcje te działają zarówno dla przepływów i aplikacji logiki. Aby uzyskać więcej informacji na temat funkcji i wyrażenia w Microsoft Flow, zobacz [korzystanie z wyrażeń w warunkach](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Na przykład, można obliczyć wartości przy użyciu funkcji matematycznych, takich jak [funkcja add()](../logic-apps/workflow-definition-language-functions-reference.md#add), jeśli chcesz sumę liczb całkowitych lub wartości zmiennoprzecinkowe. Oto kilka innych przykład zadania, które można wykonywać za pomocą funkcji:

| Zadanie | Składnia funkcji | Wynik |
| ---- | --------------- | ------ |
| Zwraca ciąg w formacie małe litery. | toLower ('<*tekstu*> ") <p>Na przykład: toLower('Hello') | "hello" |
| Zwraca unikatowy identyfikator globalny (GUID). | GUID() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Znajdują się funkcje [na podstawie ich ogólnego celu](#ordered-by-purpose), przejrzyj następujące tabele. Lub, aby uzyskać szczegółowe informacje dotyczące każdej funkcji, zobacz [Alfabetyczna lista](#alphabetical-list).

> [!NOTE]
> W składni dla definicji parametru znak zapytania (?), który pojawia się, gdy parametr oznacza, że parametr jest opcjonalny.
> Na przykład zobacz [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funkcje w wyrażeniach

Aby pokazać, jak używać funkcji w wyrażeniu, w tym przykładzie przedstawiono, jak można uzyskać wartości z `customerName` parametru i przypisanie tej wartości do `accountName` właściwości przy użyciu [parameters()](#parameters) funkcji w wyrażeniu:

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

Na przykład `concat()` funkcji może potrwać co najmniej dwóch wartości ciągu jako parametry. Ta funkcja łączy te ciągi w jeden ciąg.
Możesz albo przekazać Literały ciągu, na przykład "Sophia" i "Owen", aby uzyskać ciąg połączone "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Alternatywnie można uzyskać wartości parametrów z parametrów. W tym przykładzie użyto `parameters()` funkcji w każdym `concat()` parametru i `firstName` i `lastName` parametrów. Następnie przekaż wynikowy ciągi, które `concat()` funkcji tak, aby uzyskać ciąg połączone, przykładowo "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

W obu przypadkach oba przykłady Przypisz wynik do `customerName` właściwości.

Oto dostępne funkcje uporządkowane według ich ogólnego przeznaczenia lub Przeglądaj, funkcje, w zależności od [kolejności alfabetycznej](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funkcje ciągów

Aby pracować z ciągów, można użyć tych funkcji ciąg, a także niektóre [kolekcji funkcji](#collection-functions).
Funkcje ciągów działa tylko na ciągi.

| String — funkcja | Zadanie |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Łączenie dwóch lub więcej ciągów, a następnie zwraca połączony ciąg. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Sprawdź, czy ciąg kończy się podanym podciągiem. |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generuj Unikatowy identyfikator globalny (GUID) jako ciąg. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Zwraca pozycję początkową podciąg. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Zwraca pozycję początkową ostatnie wystąpienie podciągu. |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Zamień podciąg określony ciąg i zwraca ciąg zaktualizowane. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Zwraca tablicę zawierającą podciągów, oddzielone przecinkami, z dłuższym ciągu oparte na znak określonego ogranicznika w oryginalnym ciągu. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Sprawdź, czy ciąg zaczyna się od określonego podciąg. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Zwraca znaki ciągu, zaczynając od określonej pozycji. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Zwraca ciąg w formacie małe litery. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Zwraca ciąg w formacie wielkie litery. |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Usuń spacje wiodące i końcowe z ciągu i zwraca ciąg zaktualizowane. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Kolekcja funkcji

Aby pracować z kolekcji, zazwyczaj tablice, ciągi i czasami słowników, można użyć tych funkcji w kolekcji.

| Kolekcja funkcji | Zadanie |
| ------------------- | ---- |
| [zawiera](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Sprawdź, czy kolekcja zawiera określony element. |
| [pusty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Sprawdź, czy kolekcja jest pusta. |
| [pierwszy](../logic-apps/workflow-definition-language-functions-reference.md#first) | Zwraca pierwszy element z kolekcji. |
| [Część wspólna](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Zwraca kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gdy wewnątrz powtarzającej się akcji, za pośrednictwem tablicy, zwraca bieżący element w tablicy podczas iteracji bieżącej akcji. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Zwraca ciąg, który ma *wszystkie* elementy z tablicy, oddzielone od określonego znaku. |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Zwraca ostatni element z kolekcji. |
| [Długość](../logic-apps/workflow-definition-language-functions-reference.md#length) | Zwraca liczbę elementów w tablicy lub ciągu. |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Usuń elementy z przodu kolekcji i zwracają *znajdują się wszystkie inne* elementów. |
| [Wypełnij](../logic-apps/workflow-definition-language-functions-reference.md#take) | Zwraca elementy z przodu kolekcji. |
| [Unia](../logic-apps/workflow-definition-language-functions-reference.md#union) | Zwraca kolekcję, która ma *wszystkie* elementy z określonymi kolekcjami. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funkcje logiczne porównania

Do pracy z warunkami, Porównaj wartości i wyniki wyrażenia lub obliczyć różnego rodzaju logiki, można użyć tych funkcji logicznej porównania.
Aby uzyskać pełną dokumentację o każdej z nich, zobacz [Alfabetyczna lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja porównywania logiczne | Zadanie |
| --------------------------- | ---- |
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

## <a name="conversion-functions"></a>Funkcje konwersji

Aby zmienić typ lub format wartości, można użyć tych funkcji konwersji.
Na przykład można zmienić wartości z wartością logiczną na liczbę całkowitą.
Aby uzyskać więcej informacji na temat aplikacji logiki obsługi typów zawartości podczas konwersji, zobacz [Obsługa typów zawartości](../logic-apps/logic-apps-content-type.md).
Aby uzyskać pełną dokumentację o każdej z nich, zobacz [Alfabetyczna lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja konwersji | Zadanie |
| ------------------- | ---- |
| [Tablica](../logic-apps/workflow-definition-language-functions-reference.md#array) | Zwraca tablicę z jednej z określonych danych wejściowych. Dla wielu danych wejściowych, zobacz [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Zwraca wersję algorytmem Base64 dla ciągu. |
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
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Zwraca ciąg, który zastępuje adres URL niebezpiecznych znaków ze znakami ucieczki. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Zwraca zmiennoprzecinkową punktu numer dla wartości wejściowej. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Zwróć wersja liczby całkowitej, ciągu. |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Zwraca wartość typu JavaScript Object Notation (JSON) lub obiekt ciągu lub kodu XML. |
| [ciąg](../logic-apps/workflow-definition-language-functions-reference.md#string) | Zwraca wersję ciągu dla wartości wejściowej. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Zwraca wersję zakodowany jako identyfikator URI dla wartości wejściowej, zastępując znaki niebezpieczne adresu URL znaki ucieczki. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Zwraca binarną wersję na ciąg kodowany w formacie identyfikatora URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Zwraca ciąg wersji ciąg zakodowany jako identyfikator URI. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Zwraca ciąg w wersji XML. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Funkcje matematyczne

Aby pracować z liczbami całkowitymi i wartości zmiennoprzecinkowe, można użyć tych funkcji matematycznych.
Aby uzyskać pełną dokumentację o każdej z nich, zobacz [Alfabetyczna lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Math — funkcja | Zadanie |
| ------------- | ---- |
| [Dodaj](../logic-apps/workflow-definition-language-functions-reference.md#add) | Zwróć wynik dodawania dwóch liczb. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Zwraca wynik dzielenia dwóch liczb. |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Zwraca najwyższą wartość z zestawu numerów lub tablicy. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Zwraca najmniejszą wartość z zestawu numerów lub tablicy. |
| [dzielenie modulo](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Zwraca resztę z dzielenia dwóch liczb. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Zwraca iloczyn mnożenia dwóch liczb. |
| [RAND](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Zwraca losową liczbę całkowitą z określonego zakresu. |
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Zwraca tablicę liczb całkowitych, który rozpoczyna się od określonej wartości całkowitej. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Zwraca wynik odejmowania drugą liczbę od pierwszej liczby. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funkcje daty i godziny

Aby pracować z datami i godzinami, można użyć tych funkcji daty i godziny.
Aby uzyskać pełną dokumentację o każdej z nich, zobacz [Alfabetyczna lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

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

## <a name="workflow-functions"></a>Funkcje przepływów pracy

Te funkcje przepływów pracy mogą pomóc:

* Uzyskaj szczegółowe informacje dotyczące wystąpienia przepływu pracy w czasie wykonywania.
* Praca z danych wejściowych używana podczas tworzenia wystąpienia aplikacji logiki lub przepływy.
* Przywołują dane wyjściowe z wyzwalaczy i akcji.

Można na przykład przywołują dane wyjściowe z jedną akcję i użyć tych danych w późniejszym akcji.
Aby uzyskać pełną dokumentację o każdej z nich, zobacz [Alfabetyczna lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

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
| [Elementy](../logic-apps/workflow-definition-language-functions-reference.md#items) | Gdy komputer znajduje się wewnątrz pętli Foreach lub pętlą Until Zwróć bieżący element określonej pętli.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Gdy wewnątrz pętli Until, zwróć wartość indeksu dla bieżącej iteracji. Można użyć tę funkcję wewnątrz zagnieżdżonej aż do pętli. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Zwróć "adres URL wywołania zwrotnego" wywołuje wyzwalacz lub akcję. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Zwraca treść dla konkretnego fragmentu w danych wyjściowych akcji, która ma wiele części. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Zwraca wartość parametru, który jest opisany w definicji przepływu pracy. |
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Zwraca wynik tego wyzwalacza, w czasie wykonywania, lub z innych par nazw i wartości JSON. Zobacz też [triggerOutputs](#triggerOutputs) i [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Wróć do tego wyzwalacza `body` dane wyjściowe w czasie wykonywania. Zobacz [wyzwalacza](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Zwraca pojedynczą wartość odpowiadającą nazwę klucza w *dane formularza* lub *zakodowany w postaci* wyzwalacza danych wyjściowych. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Zwraca treść dla określonej części w wieloczęściowych danych wyjściowych wyzwalacza. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Utwórz tablicę, których wartości pasują nazwę klucza w *dane formularza* lub *zakodowany w postaci* wyzwalacza danych wyjściowych. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Zwracanie danych wyjściowych wyzwalacza, w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz [wyzwalacza](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Zmienne](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Zwraca wartość określonej zmiennej. |
| [Przepływ pracy](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Zwraca wszystkie szczegóły samego przepływu pracy w czasie wykonywania. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funkcje analizy identyfikatora URI

Aby pracować z uniform resource identifier (URI) i uzyskać różne wartości właściwości te identyfikatory URI, można użyć te funkcje analizy identyfikatora URI.
Aby uzyskać pełną dokumentację o każdej z nich, zobacz [Alfabetyczna lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja analizy identyfikatora URI | Zadanie |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Zwróć `host` wartość dla identyfikatora uniform resource identifier (URI). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Zwróć `path` wartość dla identyfikatora uniform resource identifier (URI). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Zwróć `path` i `query` wartości dla identyfikatora uniform resource identifier (URI). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Zwróć `port` wartość dla identyfikatora uniform resource identifier (URI). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Zwróć `query` wartość dla identyfikatora uniform resource identifier (URI). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Zwróć `scheme` wartość dla identyfikatora uniform resource identifier (URI). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funkcje manipulowania: JSON I XML

Aby pracować z obiektami JSON i XML węzłów, można użyć tych funkcji manipulowania.
Aby uzyskać pełną dokumentację o każdej z nich, zobacz [Alfabetyczna lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcję manipulowania | Zadanie |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Dodaj właściwość i jego wartość lub pary nazwa wartość w obiekcie JSON i zwraca zaktualizowany obiekt. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Zwraca pierwszą wartość inną niż null z co najmniej jeden parametr. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Usuwanie właściwości z obiektu JSON i zwróć zaktualizowany obiekt. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ustaw wartość właściwości w obiekcie JSON i zwraca zaktualizowany obiekt. |
| [wyrażenie XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Sprawdź XML węzłów lub wartości, które odpowiadają wyrażenie XPath (XML Path Language), a następnie zwracają pasujące węzły lub wartości. |
|||

<a name="alphabetical-list"></a>
<a name="action"></a>

### <a name="action"></a>action

Zwróć *bieżącego* danych wyjściowych akcji środowiska uruchomieniowego lub wartości z innych par nazwa i wartość JSON, które można przypisać do wyrażenia.
Domyślnie ta funkcja odwołuje się do obiektu całą operację, ale Opcjonalnie można określić właściwości żądanymi wartościami.
Zobacz też [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Możesz użyć `action()` funkcji tylko w następujących miejscach:

* `unsubscribe` Właściwości dla akcji elementu webhook, tak aby dostęp do wyników z oryginalnym `subscribe` żądania
* `trackedProperties` Właściwość na akcję
* `do-until` Pętli warunku dla akcji

```
action()
action().outputs.body.<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*property*> | Nie | String | Nazwa właściwości obiektu akcji żądanymi wartościami: **nazwa**, **startTime**, **endTime**, **dane wejściowe**,  **dane wyjściowe**, **stan**, **kodu**, **trackingId**, i **clientTrackingId**. W witrynie Azure portal te właściwości można znaleźć, sprawdzając szczegóły określonego historii uruchamiania w. Aby uzyskać więcej informacji, zobacz [interfejs API REST — akcji uruchamiania przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*wynik akcji*> | String | Dane wyjściowe z bieżącej akcji lub właściwością |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Akcja zwrotu `body` dane wyjściowe w czasie wykonywania.
Skrócona forma funkcji `actions('<actionName>').outputs.body`.
Zobacz [body()](#body) i [actions()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | String | Nazwa akcji `body` danych wyjściowych, który ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*wynik akcji — treść*> | String | `body` Dane wyjściowe z określonej akcji |
||||

*Przykład*

W tym przykładzie pobiera `body` dane wyjściowe z akcji Twitter `Get user`:

```
actionBody('Get_user')
```

I zwraca wynik:

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

Zwraca wynik akcji w czasie wykonywania.
Skrócona forma funkcji `actions('<actionName>').outputs`.
Zobacz [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | String | Nazwa akcji wyjściowym, które mają |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*output*> | String | Dane wyjściowe z określonej akcji |
||||

*Przykład*

W tym przykładzie pobiera dane wyjściowe z akcji Twitter `Get user`:

```
actionOutputs('Get_user')
```

I zwraca wynik:

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

## <a name="all-functions---alphabaetical-list"></a>Wszystkie funkcje - alphabaetical list

<a name="actions"></a>

### <a name="actions"></a>Akcje

Zwraca wynik akcji, w czasie wykonywania lub wartości z innych par nazwa i wartość JSON, które można przypisać do wyrażenia. Domyślnie funkcja odwołuje się do obiektu całą operację, ale Opcjonalnie można określić właściwości którego wartość, która ma.
Skrót wersji, zobacz [actionBody()](#actionBody), [actionOutputs()](#actionOutputs), i [body()](#body).
Dla bieżącej akcji, zobacz [action()](#action).

> [!NOTE]
> Wcześniej, można użyć `actions()` funkcji lub `conditions` elementu podczas określania, czy akcję Przeprowadziliśmy oparte na danych wyjściowych z innej akcji. Jednak, aby zadeklarować jawnie zależności między akcjami, należy teraz użyć Akcja zależnych `runAfter` właściwości.
> Aby dowiedzieć się więcej na temat `runAfter` właściwości, zobacz [przechwytywać i obsługiwać błędy z właściwością runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | String | Nazwa dla obiektu akcji którego dane wyjściowe mają  |
| <*property*> | Nie | String | Nazwa właściwości obiektu akcji żądanymi wartościami: **nazwa**, **startTime**, **endTime**, **dane wejściowe**,  **dane wyjściowe**, **stan**, **kodu**, **trackingId**, i **clientTrackingId**. W witrynie Azure portal te właściwości można znaleźć, sprawdzając szczegóły określonego historii uruchamiania w. Aby uzyskać więcej informacji, zobacz [interfejs API REST — akcji uruchamiania przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*wynik akcji*> | String | Dane wyjściowe z określonej akcji lub właściwością |
||||

*Przykład*

W tym przykładzie pobiera `status` wartości właściwości z akcji Twitter `Get user` w czasie wykonywania:

```
actions('Get_user').outputs.body.status
```

I zwraca wynik: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Zwróć wynik dodawania dwóch liczb.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Tak | Liczba całkowita, Float, lub małe i wielkie | Numery do dodania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*Suma wyników*> | Liczba całkowita lub zmiennoprzecinkowa | Wynik dodawania podanych liczb |
||||

*Przykład*

Ten przykład dodaje podanych liczb:

```
add(1, 1.5)
```

I zwraca wynik: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Dodaj liczbę dni do sygnatury czasowej.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | String | Ciąg, który zawiera znacznik czasu |
| <*days*> | Yes | Integer | Dodatnia lub ujemna liczba dni do dodania |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Sygnatura czasowa, a także określoną liczbę dni  |
||||

*Przykład 1*

Ten przykład dodaje 10 dni do określonej sygnatury czasowej:

```
addDays('2018-03-15T13:00:00Z', 10)
```

I zwraca wynik: `"2018-03-25T00:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć dni od określonej sygnatury czasowej:

```
addDays('2018-03-15T00:00:00Z', -5)
```

I zwraca wynik: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Dodaj liczbę godzin do sygnatury czasowej.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
| <*godz.* > | Tak | Integer | Dodatnia lub ujemna liczba godzin do dodania |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Sygnatura czasowa, a także określoną liczbę godzin  |
||||

*Przykład 1*

Ten przykład dodaje 10 godzin do określonej sygnatury czasowej:

```
addHours('2018-03-15T00:00:00Z', 10)
```

I zwraca wynik: `"2018-03-15T10:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć godzin od określonej sygnatury czasowej:

```
addHours('2018-03-15T15:00:00Z', -5)
```

I zwraca wynik: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Dodaj liczbę minut do sygnatury czasowej.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | String | Ciąg, który zawiera znacznik czasu |
| <*minuty*> | Tak | Integer | Dodatnia lub ujemna liczba minut, aby dodać |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Sygnatura czasowa, a także określoną liczbę minut |
||||

*Przykład 1*

Ten przykład dodaje 10 minut do określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

I zwraca wynik: `"2018-03-15T00:20:00.0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje za pięć minut od określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

I zwraca wynik: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Dodaj właściwość i jego wartość lub pary nazwa wartość w obiekcie JSON i zwraca zaktualizowany obiekt. Jeśli ten obiekt już istnieje w czasie wykonywania, funkcja zgłasza błąd.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object*> | Tak | Object | Obiekt JSON, w której chcesz dodać właściwość |
| <*property*> | Yes | String | Nazwa właściwości do dodania |
| <*value*> | Yes | Dowolne | Wartość właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Zaktualizowano obiekt JSON z określoną właściwością |
||||

*Przykład*

Ten przykład dodaje `accountNumber` właściwości `customerProfile` obiektu, który jest konwertowana na format JSON za pomocą [JSON()](#json) funkcji.
Funkcja przypisuje wartość, która jest generowana przez [guid()](#guid) funkcji i zwraca obiekt zaktualizowane:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Dodaj liczbę sekund do sygnatury czasowej.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
| <*seconds*> | Tak | Integer | Dodatnia lub ujemna liczba sekund do dodania |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Sygnatura czasowa, a także określoną liczbę sekund  |
||||

*Przykład 1*

Ten przykład dodaje 10 sekund do określonej sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

I zwraca wynik: `"2018-03-15T00:00:10.0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć sekund, co określona sygnatura czasowa:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

I zwraca wynik: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Dodaj liczbę jednostek czasu do sygnatury czasowej.
Zobacz też [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | String | Ciąg, który zawiera znacznik czasu |
| <*interval*> | Tak | Integer | Liczba jednostek w określonym czasie można dodać |
| <*timeUnit*> | Yes | String | Jednostka czasu za pomocą *interwał*: "Drugi", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Sygnatura czasowa, a także określoną liczbę jednostek czasu  |
||||

*Przykład 1*

Ten przykład dodaje jeden dzień do określonej sygnatury czasowej:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

I zwraca wynik: `"2018-01-02T00:00:00.0000000Z"`

*Przykład 2*

Ten przykład dodaje jeden dzień do określonej sygnatury czasowej:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

I zwraca wynik za pomocą opcjonalnych formatu "D": `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>i

Sprawdź, czy wszystkie wyrażenia mają wartość true.
Zwraca wartość true, gdy spełnione są wszystkie wyrażenia lub zwróci wartość false, gdy co najmniej jedno wyrażenie ma wartość false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*>,... | Yes | Boolean | Wyrażenia, aby sprawdzić |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Zwraca wartość true, gdy spełnione są wszystkie wyrażenia. Co najmniej jedno wyrażenie ma wartość false, zwróci wartość false. |
||||

*Przykład 1*

Te przykłady Sprawdź, czy określone wartości logiczne są wszystkie true:

```
and(true, true)
and(false, true)
and(false, false)
```

I zwraca następujące wyniki:

* Pierwszy przykład: Oba wyrażenia są spełnione, więc zwraca `true`.
* Drugi przykład: Jedno wyrażenie ma wartość false, dlatego zwraca `false`.
* Trzeci przykład: Oba wyrażenia są fałszywe, dlatego zwraca `false`.

*Przykład 2*

Te przykłady Sprawdź, czy określonych wyrażeń są wszystkie true:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

I zwraca następujące wyniki:

* Pierwszy przykład: Oba wyrażenia są spełnione, więc zwraca `true`.
* Drugi przykład: Jedno wyrażenie ma wartość false, dlatego zwraca `false`.
* Trzeci przykład: Oba wyrażenia są fałszywe, dlatego zwraca `false`.

<a name="array"></a>

### <a name="array"></a>tablica

Zwraca tablicę z jednej z określonych danych wejściowych.
Dla wielu danych wejściowych, zobacz [createArray()](#createArray).

```
array('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Yes | String | Ciąg do tworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*wartość*>] | Tablica | Tablica zawierająca pojedynczy określone dane wejściowe |
||||

*Przykład*

W tym przykładzie tworzy tablicę z ciągu "hello":

```
array('hello')
```

I zwraca wynik: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Zwraca wersję algorytmem Base64 dla ciągu.

```
base64('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg wejściowy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciąg base64*> | String | Wersja algorytmem Base64 dla ciągu wejściowego |
||||

*Przykład*

Ten przykład konwertuje ciąg "hello" na ciąg kodowany w formacie base64:

```
base64('hello')
```

I zwraca wynik: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Zwraca binarną wersję ciągu zakodowanego algorytmem base64.

```
base64ToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg kodowany w formacie base64 do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dane binarne dla base64 ciągu*> | String | Binarna wersja dla ciągu zakodowanego algorytmem base64 |
||||

*Przykład*

Ten przykład konwertuje "aGVsbG8 =" Ciąg zakodowany w formacie base64 ciąg binarny:

```
base64ToBinary('aGVsbG8=')
```

I zwraca wynik:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Zwraca ciąg wersji ciągu zakodowanego algorytmem base64, efektywnie dekodowanie ciągu base64.
Aby użyć tej funkcji zamiast [decodeBase64()](#decodeBase64).
Mimo że obie funkcje działają tak samo, `base64ToString()` jest preferowana.

```
base64ToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg kodowany w formacie base64 do odkodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | Wersja ciągu dla ciągu zakodowanego algorytmem base64 |
||||

*Przykład*

Ten przykład konwertuje "aGVsbG8 =" Ciąg zakodowany w formacie base64 po prostu określonym ciągiem:

```
base64ToString('aGVsbG8=')
```

I zwraca wynik: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Zwraca binarną wersję ciągu.

```
binary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Yes | String | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | String | Binarna wersja dla określonego ciągu |
||||

*Przykład*

Ten przykład konwertuje ciąg "hello" ciąg binarny:

```
binary('hello')
```

I zwraca wynik:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>Treść

Akcja zwrotu `body` dane wyjściowe w czasie wykonywania.
Skrócona forma funkcji `actions('<actionName>').outputs.body`.
Zobacz [actionBody()](#actionBody) i [actions()](#actions).

```
body('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | String | Nazwa akcji `body` danych wyjściowych, który ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*wynik akcji — treść*> | String | `body` Dane wyjściowe z określonej akcji |
||||

*Przykład*

W tym przykładzie pobiera `body` dane wyjściowe z `Get user` akcji w usłudze Twitter:

```
body('Get_user')
```

I zwraca wynik:

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

Zwróć logiczna wersji dla wartości.

```
bool(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Wartość logiczna wersji dla określonej wartości |
||||

*Przykład*

Te przykłady konwertowania wartości określone wartości logiczne:

```
bool(1)
bool(0)
```

I zwraca następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>COALESCE

Zwraca pierwszą wartość inną niż null z co najmniej jeden parametr.
Puste ciągi, pusta tablice i puste obiekty nie mają wartość null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Tak | Można łączyć typów | Co najmniej jeden element, aby sprawdzić, czy wartość null |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*pierwszy bez-o wartości null — element*> | Dowolne | Pierwszy element lub wartość, która nie ma wartości null. Jeśli wszystkie parametry mają wartość null, funkcja zwraca wartość null. |
||||

*Przykład*

Te przykłady zwraca pierwszą wartość inną niż null z określonymi wartościami, lub wartość null w przypadku wszystkich wartości null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

I zwraca następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `"hello"`
* Trzeci przykład: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Łączenie dwóch lub więcej ciągów, a następnie zwraca połączony ciąg.

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*TEXT1*>, <*text2*>,... | Tak | String | Co najmniej dwa ciągi do łączenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*text1text2...* > | String | Ciąg utworzony na podstawie połączonych ciągów wejściowych |
||||

*Przykład*

W tym przykładzie łączy ciągi "Hello" i "World":

```
concat('Hello', 'World')
```

I zwraca wynik: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>zawiera

Sprawdź, czy kolekcja zawiera określony element.
Zwraca wartość true, gdy element zostanie znaleziony, lub zwróci wartość false, gdy nie można odnaleźć.
Ta funkcja jest uwzględniana wielkość liter.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Ściślej mówiąc ta funkcja działa na tych typach kolekcji:

* A *ciąg* można znaleźć *podciąg*
* *Tablicy* można znaleźć *wartość*
* A *słownika* można znaleźć *klucza*

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcja*> | Tak | Ciąg, tablicy lub słownika | Kolekcja do sprawdzenia |
| <*value*> | Yes | Ciąg, tablicy lub słownika, odpowiednio | Element, aby znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Zwraca wartość true, gdy element zostanie znaleziony. Zwraca wartość false podczas nie można odnaleźć. |
||||

*Przykład 1*

W tym przykładzie, sprawdza, czy ciąg "hello world", aby uzyskać podciągu "world" i zwraca wartość true:

```
contains('hello world', 'world')
```

*Przykład 2*

W tym przykładzie, sprawdza, czy ciąg "hello world" do podciągu "wszechświat" i zwraca wartość false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Docelową strefę czasową, należy przekonwertować sygnatury czasowej względem uniwersalnego czasu koordynowanego (UTC).

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | String | Ciąg, który zawiera znacznik czasu |
| <*destinationTimeZone*> | Yes | String | Nazwa docelowa strefa czasowa. Aby uzyskać więcej informacji, zobacz [identyfikatorów strefy czasowej](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Sygnatury czasowej na docelową strefę czasową |
||||

*Przykład 1*

Ten przykład konwertuje jest sygnatura czasowa określona strefa czasowa:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

I zwraca wynik: `"2018-01-01T00:00:00.0000000"`

*Przykład 2*

Ten przykład konwertuje sygnatury czasowej do określonej strefy czasowej i formatu:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

I zwraca wynik: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | String | Ciąg, który zawiera znacznik czasu |
| <*sourceTimeZone*> | Tak | String | Nazwa źródłowa strefa czasowa. Aby uzyskać więcej informacji, zobacz [identyfikatorów strefy czasowej](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*destinationTimeZone*> | Tak | String | Nazwa docelowa strefa czasowa. Aby uzyskać więcej informacji, zobacz [identyfikatorów strefy czasowej](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Sygnatury czasowej na docelową strefę czasową |
||||

*Przykład 1*

Ten przykład konwertuje źródłowej strefy czasowej na docelową strefę czasową:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

I zwraca wynik: `"2018-01-01T00:00:00.0000000"`

*Przykład 2*

Ten przykład konwertuje strefie czasowej określonej strefy czasowej i formatu:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

I zwraca wynik: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konwertowanie sygnatury czasowej ze źródłowej strefy czasowej uniwersalny czas koordynowany (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
| <*sourceTimeZone*> | Tak | String | Nazwa źródłowa strefa czasowa. Aby uzyskać więcej informacji, zobacz [identyfikatorów strefy czasowej](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Sygnatura czasowa konwertowana na czas UTC |
||||

*Przykład 1*

Ten przykład konwertuje sygnaturę czasową UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

I zwraca wynik: `"2018-01-01T08:00:00.0000000Z"`

*Przykład 2*

Ten przykład konwertuje sygnaturę czasową UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

I zwraca wynik: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Zwraca tablicę z wielu danych wejściowych.
Dla pojedynczego tablic danych wejściowych, zobacz [konstruktora()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiekt1*>, <*obiekt2*>,... | Tak | Występuje, ale nie mieszane | Co najmniej dwa elementy przeznaczone do tworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*Obiekt1*>, <*obiekt2*>,...] | Tablica | Tablica utworzone na podstawie elementów wejściowych |
||||

*Przykład*

W tym przykładzie tworzy tablicę z tych danych wejściowych:

```
createArray('h', 'e', 'l', 'l', 'o')
```

I zwraca wynik: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Zwróć dane identyfikatora uniform resource identifier (URI) dla ciągu.

```
dataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | Ciąg wejściowy identyfikator URI danych |
||||

*Przykład*

W tym przykładzie tworzy ciąg "hello" identyfikatora URI danych:

```
dataUri('hello')
```

I zwraca wynik: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Zwraca binarną wersję dla danych identyfikatora uniform resource identifier (URI).
Aby użyć tej funkcji zamiast [decodeDataUri()](#decodeDataUri).
Mimo że obie funkcje działają tak samo, `dataUriBinary()` jest preferowana.

```
dataUriToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Yes | String | Identyfikator URI do przekonwertowania danych |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | Binarna wersja dla identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie tworzy binarna wersja dla tego identyfikatora URI danych:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Zwraca wersję ciągu dla identyfikatora uniform resource identifier (URI) danych.

```
dataUriToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Identyfikator URI do przekonwertowania danych |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | Wersja ciągu identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie tworzy ciąg identyfikatora URI danych:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca wynik: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Zwracanie dnia miesiąca z sygnatury czasowej.

```
dayOfMonth('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień miesiąca*> | Integer | Dzień miesiąca z określonej sygnatury czasowej |
||||

*Przykład*

W tym przykładzie zwraca numer w ciągu dnia, miesiąca, z tą sygnaturą czasową:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

I zwraca wynik: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Zwróć dzień tygodnia z sygnatury czasowej.

```
dayOfWeek('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Integer | Dzień tygodnia z określonej sygnatury czasowej, gdzie niedziela to 0, poniedziałek to 1 i tak dalej |
||||

*Przykład*

W tym przykładzie zwraca numer dnia tygodnia z tą sygnaturą czasową:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

I zwraca wynik: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>Dzieńroku

Zwróć dzień roku z sygnatury czasowej.

```
dayOfYear('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Yes | String | Ciąg, który zawiera znacznik czasu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Integer | Dzień roku od określonej sygnatury czasowej |
||||

*Przykład*

W tym przykładzie zwraca liczbę dni w roku, z tą sygnaturą czasową:

```
dayOfYear('2018-03-15T13:27:36Z')
```

I zwraca wynik: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Zwraca ciąg wersji ciągu zakodowanego algorytmem base64, efektywnie dekodowanie ciągu base64.
Należy rozważyć użycie [base64ToString()](#base64ToString) zamiast `decodeBase64()`.
Mimo że obie funkcje działają tak samo, `base64ToString()` jest preferowana.

```
decodeBase64('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg kodowany w formacie base64 do odkodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | Wersja ciągu dla ciągu zakodowanego algorytmem base64 |
||||

*Przykład*

W tym przykładzie tworzy ciąg dla ciągu zakodowanego algorytmem base64:

```
decodeBase64('aGVsbG8=')
```

I zwraca wynik: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Zwraca binarną wersję dla danych identyfikatora uniform resource identifier (URI).
Należy rozważyć użycie [dataUriToBinary()](#dataUriToBinary), zamiast `decodeDataUri()`.
Mimo że obie funkcje działają tak samo, `dataUriToBinary()` jest preferowana.

```
decodeDataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Dane ciągu identyfikatora URI do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | Binarna wersja dla ciągu identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie zwraca binarna wersja dla tego identyfikatora URI danych:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Zwróć ciąg, zastępuje znak ucieczki znaków zdekodowanego wersje.

```
decodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg ze znakami ucieczki do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | Zaktualizowano ciąg znaków zdekodowanego ucieczki |
||||

*Przykład*

W tym przykładzie zastępuje znaki ucieczki, w tym ciągu zdekodowany wersjami:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

I zwraca wynik: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Zwracanie wyniku liczbę całkowitą z dzielenia dwóch liczb.
Aby uzyskać wynik pozostała, zobacz [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Yes | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik.* |
| <*divisor*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która dzieli *dzielna*, ale nie może wynosić 0 |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer | Liczba całkowita wynik dzielenia pierwszej liczby przy drugą liczbę |
||||

*Przykład*

Oba przykłady Podziel pierwszy numer przy drugą liczbę:

```
div(10, 5)
div(11, 5)
```

I zwracają wynik: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Zwraca wersję identyfikator (URI) zakodowane uniform resource identifier ciągu, zastępując znaki niebezpieczne adresu URL znaki ucieczki.
Należy rozważyć użycie [uriComponent()](#uriComponent), zamiast `encodeUriComponent()`.
Mimo że obie funkcje działają tak samo, `uriComponent()` jest preferowana.

```
encodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg do przekonwertowania na format zakodowany jako identyfikator URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | Ciąg kodowany w formacie identyfikatora URI, znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzy wersję zakodowany jako identyfikator URI tego ciągu:

```
encodeUriComponent('https://contoso.com')
```

I zwraca wynik: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>pusty

Sprawdź, czy kolekcja jest pusta.
Zwraca wartość true, jeśli kolekcja jest pusta lub zwróci wartość false, gdy nie jest pusty.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcja*> | Yes | Ciąg, tablicy lub obiektu | Kolekcja do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Zwraca wartość true, gdy kolekcja jest pusta. Zwraca wartość false, gdy nie jest pusty. |
||||

*Przykład*

Te przykłady Sprawdź, czy określone kolekcje są puste:

```
empty('')
empty('abc')
```

I zwraca następujące wyniki:

* Pierwszy przykład: Przekazuje ciąg pusty, funkcja zwraca `true`.
* Drugi przykład: Przekazuje ciąg "abc", więc funkcja zwraca `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Sprawdź, czy ciąg kończy się określonym podciąg.
Zwraca wartość true, gdy zostanie znaleziony podciąg lub zwróci wartość false, gdy nie można odnaleźć.
Ta funkcja nie jest rozróżniana wielkość liter.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Tak | String | Ciąg do sprawdzenia |
| <*searchText*> | Tak | String | Końcowy podciąg, który należy znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ  | Boolean | Zwróć wartość true, gdy zostanie znaleziony podciąg końcowej. Zwraca wartość false podczas nie można odnaleźć. |
||||

*Przykład 1*

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się ciągiem "world":

```
endsWith('hello world', 'world')
```

I zwraca wynik: `true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się ciągiem "wszechświat":

```
endsWith('hello world', 'universe')
```

I zwraca wynik: `false`

<a name="equals"></a>

### <a name="equals"></a>równa się

Sprawdź, czy zarówno wartości, wyrażenia lub obiekty są równoważne.
Zwraca wartość true, gdy oba są równoważne lub zwróci wartość false, gdy nie są równoważne.

```
equals('<object1>', '<object2>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiekt1*>, <*obiekt2*> | Tak | Różne | Wartości, wyrażenia lub obiekty do porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Zwróć wartość true, jeśli obie są równoważne. Zwraca wartość false, gdy nie jest równorzędny. |
||||

*Przykład*

Te przykłady Sprawdź, czy określony dane wejściowe są równoważne.

```
equals(true, 1)
equals('abc', 'abcd')
```

I zwraca następujące wyniki:

* Pierwszy przykład: Obie wartości są równoważne, więc funkcja zwraca `true`.
* Drugi przykład: Obie wartości nie są równoważne, więc funkcja zwraca `false`.

<a name="first"></a>

### <a name="first"></a>pierwszy

Zwraca pierwszy element z ciągu lub tablicy.

```
first('<collection>')
first([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcja*> | Yes | Tablica lub ciąg | Kolekcja gdzie można znaleźć pierwszego elementu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Dowolne | Pierwszy element w kolekcji |
||||

*Przykład*

Te przykłady znalezienia pierwszego elementu w tych kolekcjach:

```
first('hello')
first(createArray(0, 1, 2))
```

I zwrócić następujące wyniki:

* Pierwszy przykład: `"h"`
* Drugi przykład: `0`

<a name="float"></a>

### <a name="float"></a>float

Konwertuj wersję ciągu na liczbę zmiennoprzecinkową na rzeczywista liczba zmiennoprzecinkowa.
Ta funkcja służy tylko wtedy, gdy przekazywanie parametrów niestandardowych do aplikacji, na przykład aplikacji logiki lub przepływie.

```
float('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg, który zawiera prawidłową liczbę zmiennoprzecinkową do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | Liczba zmiennoprzecinkowa dla określonego ciągu |
||||

*Przykład*

W tym przykładzie tworzy wersję ciągu dla tego liczba zmiennoprzecinkowa:

```
float('10.333')
```

I zwraca wynik: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Zwraca sygnaturę czasową w określonym formacie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | Zaktualizowano sygnatury czasowej w określonym formacie |
||||

*Przykład*

Ten przykład konwertuje sygnaturę czasową na określony format:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

I zwraca wynik: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Zwraca tablicę wartości, które pasują do nazwy klucza w celu wykonania akcji *dane formularza* lub *zakodowany w postaci* danych wyjściowych.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Yes | String | Akcja, której dane wyjściowe z wartością klucza ma |
| <*key*> | Tak | String | Nazwa klucza, którego wartość ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*tablicy przy użyciu klucza wartości*>] | Tablica | Tablica nie zawierająca wszystkie wartości, które odpowiadają określony klucz |
||||

*Przykład*

W tym przykładzie tworzy tablicę z wartości klucza "Temat" w określonej akcji form-data lub wynik zakodowany w postaci:

```
formDataMultiValues('Send_an_email', 'Subject')
```

I zwraca tekst tematu w tablicy, na przykład: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Zwraca jedną wartość, która pasuje do nazwy klucza w celu wykonania akcji *dane formularza* lub *zakodowany w postaci* danych wyjściowych.
Jeśli funkcja znajdzie więcej niż jedno dopasowanie, funkcja zgłasza błąd.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | String | Akcja, której dane wyjściowe z wartością klucza ma |
| <*key*> | Tak | String | Nazwa klucza, którego wartość ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*key-value*> | String | Wartość w określonym kluczu  |
||||

*Przykład*

Ten przykład tworzy łańcuch z wartości klucza "Temat" w określonej akcji form-data lub wynik zakodowany w postaci:

```
formDataValue('Send_an_email', 'Subject')
```

I zwraca tekst tematu w postaci ciągu, na przykład: `"Hello world"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Zwraca bieżącą sygnaturę czasową, a także jednostek w określonym czasie.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Yes | Integer | Liczba jednostek określonego czasu do odjęcia |
| <*timeUnit*> | Tak | String | Jednostka czasu za pomocą *interwał*: "Drugi", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Bieżącą sygnaturę czasową, a także określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że jest bieżącą sygnaturę czasową "2018-03-01T00:00:00.0000000Z".
Ten przykład dodaje pięć dni do tego znacznik czasu:

```
getFutureTime(5, 'Day')
```

I zwraca wynik: `"2018-03-06T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że jest bieżącą sygnaturę czasową "2018-03-01T00:00:00.0000000Z".
Ten przykład dodaje pięć dni i konwertuje wynik do formatu "D":

```
getFutureTime(5, 'Day', 'D')
```

I zwraca wynik: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Zwraca bieżącą sygnaturę czasową minus jednostek w określonym czasie.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Tak | Integer | Liczba jednostek określonego czasu do odjęcia |
| <*timeUnit*> | Tak | String | Jednostka czasu za pomocą *interwał*: "Drugi", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Bieżącą sygnaturę czasową pomniejszone o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że jest bieżącą sygnaturę czasową "2018-02-01T00:00:00.0000000Z".
W tym przykładzie odejmuje pięć dni od tej sygnatury czasowej:

```
getPastTime(5, 'Day')
```

I zwraca wynik: `"2018-01-27T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że jest bieżącą sygnaturę czasową "2018-02-01T00:00:00.0000000Z".
W tym przykładzie odejmuje pięć dni i konwertuje wynik do formatu "D":

```
getPastTime(5, 'Day', 'D')
```

I zwraca wynik: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>większa

Sprawdź, czy pierwsza wartość jest większa niż wartość drugiego.
Zwraca wartość true, jeśli pierwsza wartość jest więcej lub zwróci wartość false, gdy mniej.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | Liczba całkowita, Float lub ciągu | Pierwsza wartość, aby sprawdzić, czy jest większa niż wartość drugiego |
| <*compareTo*> | Yes | Liczba całkowita, Float lub ciąg, odpowiednio | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Pierwsza wartość jest większa niż wartość drugiego, zwraca wartość true. Pierwsza wartość jest równa lub mniejsza niż wartość drugiego, zwróci wartość false. |
||||

*Przykład*

Te przykłady Sprawdź, czy pierwsza wartość jest większa niż wartość drugiego:

```
greater(10, 5)
greater('apple', 'banana')
```

I zwrócić następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Sprawdź, czy pierwsza wartość jest większa lub równa wartości drugiego.
Zwraca wartość true, jeśli pierwsza wartość jest większe lub równe lub zwróci wartość false, gdy pierwsza wartość jest mniejsza.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | Liczba całkowita, Float lub ciągu | Pierwsza wartość do sprawdzania, czy większa lub równa wartości drugiego |
| <*compareTo*> | Tak | Liczba całkowita, Float lub ciąg, odpowiednio | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Pierwsza wartość jest większa lub równa wartości drugiego, zwraca wartość true. Zwróci wartość false, gdy pierwsza wartość jest mniejsza od drugiej wartości. |
||||

*Przykład*

Te przykłady Sprawdź, czy pierwsza wartość jest mniejsza niż wartość drugiego:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

I zwrócić następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="guid"></a>

### <a name="guid"></a>Identyfikator GUID

Generuj Unikatowy identyfikator globalny (GUID) jako ciąg znaków, na przykład "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Ponadto można określić inny format identyfikatora GUID innego niż domyślny format "D", czyli 32 cyfry rozdzielone łącznikami.

```
guid('<format>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Format*> | Nie | String | Pojedynczy [specyfikatora formatu](https://msdn.microsoft.com/library/97af8hh4) zwracane identyfikatora GUID. Domyślny format to "D", ale można użyć "N", "D", "B", "P" lub "X". |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*GUID-value*> | String | Identyfikator GUID generowany losowo |
||||

*Przykład*

Ten przykład generuje ten sam identyfikator GUID, ale jako 32 cyfry rozdzielone łącznikami i ujęte w nawiasy:

```
guid('P')
```

I zwraca wynik: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Sprawdź, czy wyrażenie jest prawdziwe lub fałszywe.
Na podstawie wyniku, zwrócić określoną wartość.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Tak | Boolean | Wyrażenie do sprawdzenia |
| <*valueIfTrue*> | Tak | Dowolne | Wartość zwracana, gdy wyrażenie jest prawdziwe |
| <*valueIfFalse*> | Tak | Dowolne | Wartość zwracana, gdy wyrażenie jest fałszywe |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Dowolne | Określona wartość, która zwraca zależnie od tego, czy wyrażenie jest prawdziwe lub fałszywe |
||||

*Przykład*

W tym przykładzie zwraca `"yes"` ponieważ określone wyrażenie zwróci wartość true.
W przeciwnym razie zwraca przykład `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Zwraca pozycję początkową lub wartość indeksu podciąg.
Ta funkcja nie jest rozróżniana wielkość liter, a indeksy rozpoczynać się od cyfry 0.

```
indexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Tak | String | Ciąg zawierający podciąg, który należy znaleźć |
| <*searchText*> | Yes | String | Podciąg, który należy znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | Począwszy od pozycji lub indeks wartość określony podciąg. <p>Jeśli ciąg nie zostanie znaleziony, zwraca liczbę od -1. |
||||

*Przykład*

W tym przykładzie wyszukuje wartość początkowa indeksu dla podciągu "world" w ciągu "hello world":

```
indexOf('hello world', 'world')
```

I zwraca wynik: `6`

<a name="int"></a>

### <a name="int"></a>int

Zwróć wersja liczby całkowitej, ciągu.

```
int('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*integer-result*> | Integer | Wersja liczby całkowitej dla określonego ciągu |
||||

*Przykład*

W tym przykładzie tworzy wersja liczby całkowitej, ciągu "10":

```
int('10')
```

I zwraca wynik: `10`

<a name="item"></a>

### <a name="item"></a>Element

Gdy jest używana wewnątrz powtarzającej się akcji, za pośrednictwem tablicy, zwraca bieżący element w tablicy, podczas iteracji bieżącej akcji.
Możesz także uzyskać wartości z właściwości tego elementu.

```
item()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Dowolne | Bieżący element w tablicy dla bieżącej iteracji akcji |
||||

*Przykład*

W tym przykładzie pobiera `body` elementu z bieżącego komunikatu dla akcji "Send_an_email" wewnątrz pętli for-each bieżąca iteracja:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

Zwraca bieżący element z każdym cyklu w pętli for-each.
Tę funkcję wewnątrz pętli for-each.

```
items('<loopName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Tak | String | Nazwa pętli for-each |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*item*> | Dowolne | Element z bieżącego cyklu w określonej pętli for-each |
||||

*Przykład*

W tym przykładzie pobiera bieżący element z określonym pętli for-each:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Zwraca wartość indeksu dla bieżącej iteracji w pętli Until. Można użyć tę funkcję wewnątrz zagnieżdżonej aż do pętli. 

```
iterationIndexes('<loopName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Yes | String | Nazwa pętlą Until | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Indeks*> | Integer | Wartość indeksu dla bieżącej iteracji w określonym pętlą Until | 
|||| 

*Przykład* 

W tym przykładzie tworzy zmienną licznika i zwiększa tej zmiennej za pomocą jednej w każdej iteracji w pętli Until aż osiągnie wartość licznika, 5. Ten przykład tworzy również zmienną, która śledzi bieżący indeks dla każdej iteracji. W pętli Until, podczas każdej iteracji przykład zwiększa licznik i następnie przypisuje wartość licznika do bieżącej wartości indeksu i następnie zwiększa ten licznik. W dowolnym momencie można określić bieżącą liczbę iteracji, pobierając bieżącą wartość indeksu.

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

### <a name="json"></a>json

Zwraca wartość typu JavaScript Object Notation (JSON) lub obiekt ciągu lub kodu XML.

```
json('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Yes | Ciągu lub kodu XML | Ciąg lub XML do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Typ natywny JSON lub obiekt | Wartość typu natywnego JSON lub obiektu dla określonego ciągu lub kodu XML. Jeśli ten ciąg ma wartość null, funkcja zwraca pusty obiekt. |
||||

*Przykład 1*

W tym przykładzie ten ciąg jest konwertowany na wartość JSON:

```
json('[1, 2, 3]')
```

I zwraca wynik: `[1, 2, 3]`

*Przykład 2*

Ten przykład konwertuje te parametry do ciągu JSON:

```
json('{"fullName": "Sophia Owen"}')
```

I zwraca wynik:

```
{
  "fullName": "Sophia Owen"
}
```

*Przykład 3*

Ten przykład konwertuje plik XML do formatu JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

I zwraca wynik:

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

### <a name="intersection"></a>Część wspólna

Zwraca kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji.
Pojawią się w wyniku, element musi znajdować się we wszystkich kolekcjach przekazane do tej funkcji.
Jeśli jeden lub więcej elementów tej samej nazwie, ostatni element o tej nazwie pojawia się w wyniku.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*kolekcji 2*>,... | Tak | Tablicy lub obiektu, ale nie oba na raz | Kolekcje, z której mają *tylko* wspólne elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*common-items*> | Tablicy lub obiektu, odpowiednio | Kolekcja, która ma wspólne elementy w określonej kolekcji |
||||

*Przykład*

W tym przykładzie wyszukuje wspólne elementy między te macierze:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

I zwraca tablicę z *tylko* następujące elementy: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Zwracany ciąg, który ma wszystkie elementy z tablicy, a każdy znak rozdzielone *ogranicznik*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcja*> | Tak | Tablica | Tablica, która zawiera elementy do dołączenia do |
| <*delimiter*> | Yes | String | Separator, który pojawia się między każdego znaku w ciągu wynikowym |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | String | Wynikowy ciąg utworzony na podstawie wszystkich elementów w wybranej tablicy |
||||

*Przykład*

W tym przykładzie tworzy ciąg ze wszystkich elementów w tej tablicy od określonego znaku, jak ogranicznik:

```
join(createArray('a', 'b', 'c'), '.')
```

I zwraca wynik: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>ostatni

Zwraca ostatni element z kolekcji.

```
last('<collection>')
last([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcja*> | Yes | Tablica lub ciąg | Kolekcja gdzie można znaleźć ostatniego elementu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Ciąg lub tablicę, odpowiednio | Ostatni element w kolekcji |
||||

*Przykład*

Te przykłady znajdowanie ostatniego elementu w tych kolekcjach:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

I zwraca następujące wyniki:

* Pierwszy przykład: `"d"`
* Drugi przykład: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Zwraca pozycję początkową lub wartość indeksu ostatnie wystąpienie podciągu.
Ta funkcja nie jest rozróżniana wielkość liter, a indeksy rozpoczynać się od cyfry 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Tak | String | Ciąg zawierający podciąg, który należy znaleźć |
| <*searchText*> | Yes | String | Podciąg, który należy znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | Na pozycji lub indeks wartość początkową dla ostatniego wystąpienia określony podciąg. <p>Jeśli ciąg nie zostanie znaleziony, zwraca liczbę od -1. |
||||

*Przykład*

W tym przykładzie wyszukuje wartość początkową indeks ostatniego wystąpienia podciągu "world" w ciągu "hello world":

```
lastIndexOf('hello world', 'world')
```

I zwraca wynik: `6`

<a name="length"></a>

### <a name="length"></a>length

Zwraca liczbę elementów w kolekcji.

```
length('<collection>')
length([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcja*> | Tak | Tablica lub ciąg | Kolekcja elementów do zliczenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*długości lub liczby*> | Integer | Liczba elementów w kolekcji |
||||

*Przykład*

Te przykłady liczby elementów w tych kolekcjach:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

I zwracają wynik: `4`

<a name="less"></a>

### <a name="less"></a>less

Sprawdź, czy pierwsza wartość jest mniejsza od drugiej wartości.
Zwraca wartość true, jeśli pierwsza wartość jest mniejsza lub zwraca wartość false, jeśli pierwsza wartość jest większa.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | Liczba całkowita, Float lub ciągu | Pierwsza wartość, aby sprawdzić, czy mniejsza niż druga wartość |
| <*compareTo*> | Tak | Liczba całkowita, Float lub ciąg, odpowiednio | Element porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Zwraca wartość PRAWDA, jeśli pierwsza wartość jest mniejsza od drugiej wartości. Pierwsza wartość jest równa lub większa niż wartość drugiego, zwróci wartość false. |
||||

*Przykład*

Te przykłady Sprawdź, czy pierwsza wartość jest mniejsza od drugiej wartości.

```
less(5, 10)
less('banana', 'apple')
```

I zwrócić następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Sprawdź, czy pierwsza wartość jest mniejsza niż lub równa wartości drugiego.
Zwraca wartość true, jeśli pierwsza wartość jest mniejsza niż lub równe lub zwraca wartość false, jeśli pierwsza wartość jest więcej.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Yes | Liczba całkowita, Float lub ciągu | Pierwsza wartość, aby sprawdzić, czy mniejsza niż lub równa wartości drugiego |
| <*compareTo*> | Yes | Liczba całkowita, Float lub ciąg, odpowiednio | Element porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ  | Boolean | Pierwsza wartość jest mniejsza lub równa wartości drugiego, zwraca wartość true. Pierwsza wartość jest większa niż wartość drugiego, zwróci wartość false. |
||||

*Przykład*

Te przykłady Sprawdź, czy pierwsza wartość jest większa niż wartość drugiego.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

I zwrócić następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Zwróć "adres URL wywołania zwrotnego" wywołuje wyzwalacz lub akcję.
Ta funkcja działa tylko w przypadku wyzwalacze i akcje dla **HttpWebhook** i **ApiConnectionWebhook** typów łącznika, ale nie **ręczne**,  **Cykl**, **HTTP**, i **APIConnection** typów.

```
listCallbackUrl()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*callback-URL*> | String | Adres URL wywołania zwrotnego dla wyzwalacza lub akcji |
||||

*Przykład*

W tym przykładzie przedstawiono przykładowy adres URL wywołania zwrotnego, że ta funkcja może zwrócić:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>Maksymalna

Zwraca najwyższą wartość z listy lub tablicy z numerami, które się na obu końcach.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Liczba1*>, <*liczba2*>,... | Tak | Liczba całkowita i zmiennoprzecinkowa | Zbiór liczb, z którego mają najwyższą wartość |
| [<*Liczba1*>, <*liczba2*>,...] | Yes | Array — liczba całkowita i zmiennoprzecinkowa | Tablica liczb, z którego mają najwyższą wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*max-value*> | Liczba całkowita lub zmiennoprzecinkowa | Najwyższą wartość w określonej tablicy lub zbioru liczb |
||||

*Przykład*

Te przykłady uzyskać najwyższą wartość z zestawu kombinacja cyfr i tablicy:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

I zwracają wynik: `3`

<a name="min"></a>

### <a name="min"></a>min.

Zwraca najmniejszą wartość z zestawu numerów lub tablicy.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Liczba1*>, <*liczba2*>,... | Yes | Liczba całkowita i zmiennoprzecinkowa | Zbiór liczb, z którego ma najniższą wartość |
| [<*Liczba1*>, <*liczba2*>,...] | Tak | Array — liczba całkowita i zmiennoprzecinkowa | Tablica liczb, z którego ma najniższą wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*min-value*> | Liczba całkowita lub zmiennoprzecinkowa | Najniższa wartość w określonym zestawie numerów lub określonej tablicy |
||||

*Przykład*

Te przykłady Pobierz najmniejszą wartość w zestawie kombinacja cyfr i tablicy:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

I zwracają wynik: `1`

<a name="mod"></a>

### <a name="mod"></a>dzielenie modulo

Zwraca resztę z dzielenia dwóch liczb.
Aby uzyskać wyniku liczby całkowitej, zobacz [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Yes | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik.* |
| <*divisor*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która dzieli *dzielna*, ale nie może wynosić 0. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Liczba całkowita lub zmiennoprzecinkowa | Resztę z dzielenia pierwszej liczby przy drugą liczbę |
||||

*Przykład*

Ten przykład dzieli pierwsza liczba przy drugą liczbę:

```
mod(3, 2)
```

I zwracają wynik: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Zwraca iloczyn mnożenia dwóch liczb.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Yes | Liczba całkowita lub zmiennoprzecinkowa | Liczba, należy pomnożyć przez *multiplicand2* |
| <*multiplicand2*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, wielokrotności *multiplicand1* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*product-result*> | Liczba całkowita lub zmiennoprzecinkowa | Produkt mnożenia pierwsza liczba przy drugą liczbę |
||||

*Przykład*

Te przykłady wielu pierwsza liczba przy drugą liczbę:

```
mul(1, 2)
mul(1.5, 2)
```

I zwrócić następujące wyniki:

* Pierwszy przykład: `2`
* Drugi przykład `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Zwraca treść dla konkretnego fragmentu w danych wyjściowych akcji, która ma wiele części.

```
multipartBody('<actionName>', <index>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Yes | String | Nazwa akcji, która ma produkt wyjściowy z wieloma składnikami |
| <*Indeks*> | Yes | Integer | Wartość indeksu dla części, która ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*body*> | String | Treść dla określonej części |
||||

<a name="not"></a>

### <a name="not"></a>nie

Sprawdź, czy wyrażenie jest fałszywe.
Zwraca wartość true, jeśli wyrażenie ma wartość false lub zwróci wartość false, gdy ma wartość true.

```json
not(<expression>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Tak | Boolean | Wyrażenie do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Zwraca wartość true, gdy wyrażenie jest fałszywe. Wyrażenie jest prawdziwe, zwróci wartość false. |
||||

*Przykład 1*

Te przykłady Sprawdź, czy określonych wyrażeń mają wartość false:

```json
not(false)
not(true)
```

I zwrócić następujące wyniki:

* Pierwszy przykład: Wyrażenie ma wartość FAŁSZ, aby funkcja zwraca `true`.
* Drugi przykład: Wyrażenie ma wartość true, dzięki czemu funkcja zwraca `false`.

*Przykład 2*

Te przykłady Sprawdź, czy określonych wyrażeń mają wartość false:

```json
not(equals(1, 2))
not(equals(1, 1))
```

I zwrócić następujące wyniki:

* Pierwszy przykład: Wyrażenie ma wartość FAŁSZ, aby funkcja zwraca `true`.
* Drugi przykład: Wyrażenie ma wartość true, dzięki czemu funkcja zwraca `false`.

<a name="or"></a>

### <a name="or"></a>lub

Sprawdź, czy co najmniej jedno wyrażenie ma wartość true.
Zwraca wartość true, jeśli co najmniej jedno wyrażenie ma wartość true lub zwróci wartość false, gdy wszystkie mają wartość false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*>,... | Tak | Boolean | Wyrażenia, aby sprawdzić |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ | Boolean | Zwraca wartość true, gdy co najmniej jedno wyrażenie ma wartość true. Wszystkie wyrażenia mają wartość false, zwróci wartość false. |
||||

*Przykład 1*

Te przykłady Sprawdź, czy co najmniej jedno wyrażenie ma wartość true:

```json
or(true, false)
or(false, false)
```

I zwrócić następujące wyniki:

* Pierwszy przykład: Co najmniej jedno wyrażenie ma wartość true, dzięki czemu funkcja zwraca `true`.
* Drugi przykład: Oba wyrażenia są fałszywe, funkcja zwraca `false`.

*Przykład 2*

Te przykłady Sprawdź, czy co najmniej jedno wyrażenie ma wartość true:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

I zwrócić następujące wyniki:

* Pierwszy przykład: Co najmniej jedno wyrażenie ma wartość true, dzięki czemu funkcja zwraca `true`.
* Drugi przykład: Oba wyrażenia są fałszywe, funkcja zwraca `false`.

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Zwraca wartość parametru, który jest opisany w definicji przepływu pracy.

```
parameters('<parameterName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Tak | String | Nazwa parametru, którego wartość ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Dowolne | Wartość parametru określonego |
||||

*Przykład*

Załóżmy, że masz tę wartość JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

W tym przykładzie pobiera wartość określonego parametru:

```
parameters('fullName')
```

I zwraca wynik: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>RAND

Zwraca losową liczbę całkowitą z określonego zakresu (włączna) tylko na końcu wyjścia.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Tak | Integer | Najmniejsza liczba całkowita z zakresu |
| <*maxValue*> | Tak | Integer | Liczba całkowita, która następuje po największej liczbie całkowitej w zakresie, który może zwrócić — funkcja |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*random-result*> | Integer | Losową liczbę całkowitą, zwrócony z określonego zakresu |
||||

*Przykład*

W tym przykładzie pobiera losową wartość całkowitą z określonego zakresu, z wyłączeniem wartość maksymalna:

```
rand(1, 5)
```

I zwraca jedną z tych liczb w wyniku: `1`, `2`, `3`, lub `4`

<a name="range"></a>

### <a name="range"></a>Zakres

Zwraca tablicę liczb całkowitych, który rozpoczyna się od określonej wartości całkowitej.

```
range(<startIndex>, <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Tak | Integer | Wartość całkowitą, która rozpoczyna się tablicy jako pierwszy element |
| <*count*> | Tak | Integer | Liczb całkowitych w tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Tablica | Tablica o wartości całkowitych, zaczynając od określonego indeksu |
||||

*Przykład*

W tym przykładzie tworzy tablicę liczb całkowitych, który zaczyna się od określonego indeksu i ma określoną liczbę liczb całkowitych:

```
range(1, 4)
```

I zwraca wynik: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Zastąp

Zamień podciąg określony ciąg i zwraca ciąg wyniku. Ta funkcja jest uwzględniana wielkość liter.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Tak | String | Ciąg zawierający podciąg, który należy zastąpić |
| <*oldText*> | Yes | String | Podciąg w celu zastąpienia |
| <*newText*> | Yes | String | Ciąg zastępujący |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-text*> | String | Zaktualizowano parametry po zastąpieniu podciąg <p>Jeśli nie zostanie znaleziony podciąg, zwróć oryginalny ciąg. |
||||

*Przykład*

Ten przykład umożliwia znalezienie "starymi" substring "old string" i zastępuje "starymi" z "nowy":

```
replace('the old string', 'old', 'new')
```

I zwraca wynik: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Usuwanie właściwości obiektu i zwraca obiekt zaktualizowane.

```
removeProperty(<object>, '<property>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object*> | Tak | Object | Obiekt JSON, z której chcesz usunąć właściwość |
| <*property*> | Tak | String | Nazwa właściwości do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Zaktualizowano obiekt JSON bez określonej właściwości |
||||

*Przykład*

W tym przykładzie usuwa `"accountLocation"` właściwość `"customerProfile"` obiektu, który jest konwertowana na format JSON za pomocą [JSON()](#json) funkcji i zwraca obiekt zaktualizowane:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Ustawianie wartości właściwości obiektu i zwraca zaktualizowany obiekt.
Aby dodać nową właściwość, można użyć tej funkcji lub [addProperty()](#addProperty) funkcji.

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object*> | Tak | Object | Obiekt JSON, którego właściwości chcesz ustawić |
| <*property*> | Tak | String | Nazwa dla istniejących i nowych właściwości do ustawienia |
| <*value*> | Yes | Dowolne | Wartość do ustawienia dla określonej właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Zaktualizowano obiekt JSON, którego właściwością |
||||

*Przykład*

W tym przykładzie `"accountNumber"` właściwość `"customerProfile"` obiektu, który jest konwertowana na format JSON za pomocą [JSON()](#json) funkcji.
Funkcja przypisuje wartości generowane przez [guid()](#guid) funkcji i zwraca zaktualizowany obiekt JSON:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

### <a name="skip"></a>Pomiń

Usuń elementy z przodu kolekcji i zwracają *znajdują się wszystkie inne* elementów.

```
skip([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcja*> | Tak | Tablica | Kolekcja elementów, których chcesz usunąć |
| <*count*> | Tak | Integer | Dodatnia liczba całkowita liczba elementów do usunięcia z przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*kolekcji zaktualizowane*>] | Tablica | Zaktualizowano kolekcji po usunięciu określonych elementów |
||||

*Przykład*

W tym przykładzie usuwa jeden element, liczba 0, z przodu określonej tablicy:

```
skip(createArray(0, 1, 2, 3), 1)
```

I zwraca tej tablicy, pozostałe elementy: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Zwraca tablicę zawierającą podciągów, oddzielone przecinkami, w oparciu o znak określonego ogranicznika w oryginalnym ciągu.

```
split('<text>', '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Tak | String | Ciąg na podciągi na podstawie przy użyciu określonego ogranicznika w oryginalnym ciągu |
| <*delimiter*> | Tak | String | Znak w oryginalnym ciągu do użycia jako ogranicznika |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*substring1*>, <*substring2*>,...] | Tablica | Tablica zawierająca podciągów z oryginalnego ciągu, rozdzielone przecinkami |
||||

*Przykład*

W tym przykładzie tworzy tablicę z podciągów z określonego ciągu, w oparciu o określony znak, rozdzielając:

```
split('a_b_c', '_')
```

I zwraca tablicę, w tym w wyniku: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Zwróć początek dnia sygnaturę czasową.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Określonej sygnatury czasowej, ale począwszy od godziny zera znaku w ciągu dnia |
||||

*Przykład*

W tym przykładzie wyszukuje początek dnia tej sygnatury czasowej:

```
startOfDay('2018-03-15T13:30:30Z')
```

I zwraca wynik: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Zwróć początek godziny dla sygnatury czasowej.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Określona sygnatura czasowa, ale począwszy od zera minutowy oznaczyć za godzinę |
||||

*Przykład*

W tym przykładzie wyszukuje początek godziny dla tej sygnatury czasowej:

```
startOfHour('2018-03-15T13:30:30Z')
```

I zwraca wynik: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Zwróć początek miesiąca dla sygnatury czasowej.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Określona sygnatura czasowa, ale począwszy od pierwszego dnia miesiąca o znak zero godzinny |
||||

*Przykład*

W tym przykładzie zwraca początek miesiąca dla tej sygnatury czasowej:

```
startOfMonth('2018-03-15T13:30:30Z')
```

I zwraca wynik: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Sprawdź, czy ciąg zaczyna się od określonego podciąg.
Zwraca wartość true, gdy zostanie znaleziony podciąg lub zwróci wartość false, gdy nie można odnaleźć.
Ta funkcja nie jest rozróżniana wielkość liter.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Yes | String | Ciąg do sprawdzenia |
| <*searchText*> | Tak | String | Począwszy od ciąg do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| wartość PRAWDA lub FAŁSZ  | Boolean | Zwraca wartość true, gdy zostanie znaleziony podciąg począwszy od. Zwraca wartość false podczas nie można odnaleźć. |
||||

*Przykład 1*

W tym przykładzie sprawdza, czy ciąg "hello world" rozpoczyna się od podciągu "hello":

```
startsWith('hello world', 'hello')
```

I zwraca wynik: `true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" rozpoczyna się od podciągu "greetings":

```
startsWith('hello world', 'greetings')
```

I zwraca wynik: `false`

<a name="string"></a>

### <a name="string"></a>string

Zwraca wersję ciągu dla wartości.

```
string(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*string-value*> | String | Wersja ciągu dla określonej wartości |
||||

*Przykład 1*

W tym przykładzie tworzy wersję ciągu dla tego numeru:

```
string(10)
```

I zwraca wynik: `"10"`

*Przykład 2*

W tym przykładzie tworzy ciąg dla określonego obiektu JSON i używa znak ukośnika odwrotnego (\\) jako znak ucieczki dla znaku podwójnego cudzysłowu (").

```
string( { "name": "Sophie Owen" } )
```

I zwraca wynik: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Zwraca wynik odejmowania drugą liczbę od pierwszej liczby.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*odjemna*> | Yes | Liczba całkowita lub zmiennoprzecinkowa | Liczba, od której należy odjąć *odjemnik* |
| <*subtrahend*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, którą chcesz odjąć od *odjemna* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*result*> | Liczba całkowita lub zmiennoprzecinkowa | Wynik odejmowania drugą liczbę od pierwszej liczby |
||||

*Przykład*

W tym przykładzie odejmuje liczbę drugi z pierwszą liczbę:

```
sub(10.3, .3)
```

I zwraca wynik: `10`

<a name="substring"></a>

### <a name="substring"></a>podciąg

Zwraca znaki ciągu, zaczynając od określonej pozycji lub indeksu.
Początek wartości indeksu o numerze 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Yes | String | Ciąg znaków, którego chcesz |
| <*startIndex*> | Tak | Integer | Liczba dodatnia, równa lub większa niż 0, którego chcesz użyć jako wartości początkowej, pozycja lub indeks |
| <*Długość*> | Tak | Integer | Wartość dodatnią znaków, które mają wejść w podciąg |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*substring-result*> | String | Podciąg z określoną liczbę znaków, zaczynając od określonego indeksu pozycji w ciągu źródłowym |
||||

*Przykład*

W tym przykładzie tworzy podciąg pięć znaków od określonego ciągu znaków, zaczynając od wartości indeksu 6:

```
substring('hello world', 6, 5)
```

I zwraca wynik: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Odjęcie liczby jednostek czasu z sygnatury czasowej.
Zobacz też [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg, który zawiera znacznik czasu |
| <*interval*> | Tak | Integer | Liczba jednostek określonego czasu do odjęcia |
| <*timeUnit*> | Yes | String | Jednostka czasu za pomocą *interwał*: "Drugi", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Sygnatura czasowa pomniejszone o określoną liczbę jednostek czasu |
||||

*Przykład 1*

W tym przykładzie odejmuje jeden dzień z tą sygnaturą czasową:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

I zwraca wynik: `"2018-01-01T00:00:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje jeden dzień z tą sygnaturą czasową:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

I zwraca wynik przy użyciu formatu "D" opcjonalne: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>Wypełnij

Zwraca elementy z przodu kolekcji.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcja*> | Tak | Tablica lub ciąg | Kolekcja elementów, których chcesz |
| <*count*> | Tak | Integer | Dodatnia liczba całkowita, dla liczby elementów, które z przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*podzbiór*> lub [<*podzbioru*>] | Ciąg lub tablicę, odpowiednio | Ciąg lub tablicę, która ma określoną liczbę elementów z przodu oryginalnej kolekcji |
||||

*Przykład*

Te przykłady Pobierz określoną liczbę elementów na początku tych kolekcji:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

I zwrócić następujące wyniki:

* Pierwszy przykład: `"abc"`
* Drugi przykład: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>znaczniki

Zwróć `ticks` wartości właściwości dla określonej sygnatury czasowej.
A *znaczników* jest interwał 100-nanosekundowych.

```
ticks('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Tak | String | Ciąg dla sygnatury czasowej |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | Liczba impulsów od określonej sygnatury czasowej |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Zwraca ciąg w formacie małe litery. Jeśli znak w ciągu nie ma wersji małe litery, znaku pozostaje bez zmian w zwracanym ciągu.

```
toLower('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Yes | String | Ciąg do zwrócenia w formacie małe litery |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | String | Oryginalny ciąg w formacie małe litery |
||||

*Przykład*

Ten przykład konwertuje ten ciąg na małe litery:

```
toLower('Hello World')
```

I zwraca wynik: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Zwraca ciąg w formacie wielkie litery. Jeśli znak w ciągu nie ma wersji wielkie litery, znaku pozostaje bez zmian w zwracanym ciągu.

```
toUpper('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Tak | String | Ciąg do zwrócenia w formacie wielkie litery |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*tekst na wielkie litery*> | String | Oryginalny ciąg w formacie wielkie litery |
||||

*Przykład*

Ten przykład konwertuje ten ciąg na wielkie litery:

```
toUpper('Hello World')
```

I zwraca wynik: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Wyzwalacz

Zwracanie danych wyjściowych wyzwalacza, w czasie wykonywania lub wartości z innych par nazwa i wartość JSON, które można przypisać do wyrażenia.

* W danych wejściowych wyzwalacza ta funkcja zwraca dane wyjściowe z poprzedniego wykonania.

* Wewnątrz warunku wyzwalacza ta funkcja zwraca dane wyjściowe z bieżącego wykonywania.

Domyślnie funkcja odwołuje się do obiektu całego wyzwalacza, ale Opcjonalnie można określić właściwości którego wartość, która ma.
Ponadto ta funkcja została skrócona wersje dostępne, zobacz [triggerOutputs()](#triggerOutputs) i [triggerBody()](#triggerBody).

```
trigger()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dane wyjściowe wyzwalacza*> | String | Dane wyjściowe wyzwalacza w czasie wykonywania |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Wróć do tego wyzwalacza `body` dane wyjściowe w czasie wykonywania.
Skrócona forma funkcji `trigger().outputs.body`.
Zobacz [trigger()](#trigger).

```
triggerBody()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | String | `body` Dane wyjściowe z wyzwalacza |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Zwraca tablicę wartości, które pasują do nazwy klucza w wyzwalaczu *dane formularza* lub *zakodowany w postaci* danych wyjściowych.

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*key*> | Yes | String | Nazwa klucza, którego wartość ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*tablicy przy użyciu klucza wartości*>] | Tablica | Tablica nie zawierająca wszystkie wartości, które odpowiadają określony klucz |
||||

*Przykład*

W tym przykładzie tworzy tablicę z wartości klucza "feedUrl" w wyzwalacz RSS form-data lub wynik zakodowany w postaci:

```
triggerFormDataMultiValues('feedUrl')
```

I zwracającą wynik przykład tej tablicy: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Zwraca ciąg zawierający jedną wartość, która pasuje do nazwy klucza w wyzwalaczu *dane formularza* lub *zakodowany w postaci* danych wyjściowych.
Jeśli funkcja znajdzie więcej niż jedno dopasowanie, funkcja zgłasza błąd.

```
triggerFormDataValue('<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*key*> | Tak | String | Nazwa klucza, którego wartość ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*key-value*> | String | Wartość w określonym kluczu |
||||

*Przykład*

Ten przykład tworzy łańcuch z wartości klucza "feedUrl" w wyzwalacz RSS form-data lub wynik zakodowany w postaci:

```
triggerFormDataValue('feedUrl')
```

I zwraca ten ciąg jako wynik przykładu: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Zwraca treść dla konkretnego fragmentu w danych wyjściowych wyzwalacza, który ma wiele części.

```
triggerMultipartBody(<index>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Indeks*> | Tak | Integer | Wartość indeksu dla części, która ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*body*> | String | Treść dla określonej części w wieloczęściowych danych wyjściowych wyzwalacza |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Zwracanie danych wyjściowych wyzwalacza, w czasie wykonywania lub wartości z innych par nazw i wartości JSON.
Skrócona forma funkcji `trigger().outputs`.
Zobacz [trigger()](#trigger).

```
triggerOutputs()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dane wyjściowe wyzwalacza*> | String | Dane wyjściowe wyzwalacza w czasie wykonywania  |
||||

<a name="trim"></a>

### <a name="trim"></a>TRIM

Usuń spacje wiodące i końcowe z ciągu i zwraca ciąg zaktualizowane.

```
trim('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text*> | Tak | String | Ciąg, który ma początkowe i końcowe białe znaki do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | Zaktualizowaną wersję, oryginalnym ciągu bez spacji wiodących i końcowych |
||||

*Przykład*

W tym przykładzie usuwa wiodące i końcowe białe znaki z ciągu "Hello World":

```
trim(' Hello World  ')
```

I zwraca wynik: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Unia

Zwraca kolekcję, która ma *wszystkie* elementy z określonymi kolekcjami.
Pojawią się w wyniku, element może znajdować się w żadnej kolekcji przekazane do tej funkcji. Jeśli jeden lub więcej elementów tej samej nazwie, ostatni element o tej nazwie pojawia się w wyniku.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*kolekcji 2*>,...  | Yes | Tablicy lub obiektu, ale nie oba na raz | Kolekcje, z której mają *wszystkie* elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Tablicy lub obiektu, odpowiednio | Kolekcja ze wszystkich elementów z określonymi kolekcjami — bez duplikatów |
||||

*Przykład*

W tym przykładzie pobiera *wszystkie* elementy z tych kolekcji:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

I zwraca wynik: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Zwraca wersję identyfikator (URI) zakodowane uniform resource identifier ciągu, zastępując znaki niebezpieczne adresu URL znaki ucieczki.
Aby użyć tej funkcji zamiast [encodeUriComponent()](#encodeUriComponent).
Mimo że obie funkcje działają tak samo, `uriComponent()` jest preferowana.

```
uriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Yes | String | Ciąg do przekonwertowania na format zakodowany jako identyfikator URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | Ciąg kodowany w formacie identyfikatora URI, znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzy wersję zakodowany jako identyfikator URI tego ciągu:

```
uriComponent('https://contoso.com')
```

I zwraca wynik: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Zwraca binarną wersję składnika uniform resource identifier (URI).

```
uriComponentToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg zakodowany jako identyfikator URI do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | String | Binarna wersja ciąg kodowany w formacie identyfikatora URI. Zawartość binarna jest kodowany algorytmem base64 i reprezentowany przez `$content`. |
||||

*Przykład*

W tym przykładzie tworzy binarna wersja dla tego ciąg kodowany w formacie identyfikatora URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

I zwraca wynik:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Zwraca ciąg, efektywnie dekodowanie ciąg kodowany w formacie identyfikatora URI zakodowana wersję ciągu dla identyfikatora uniform resource identifier (URI).

```
uriComponentToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg zakodowany jako identyfikator URI do zdekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | Wersja Dekodowany ciąg kodowany w formacie identyfikatora URI |
||||

*Przykład*

W tym przykładzie tworzy wersja Dekodowany ciąg ten ciąg kodowany w formacie identyfikatora URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

I zwraca wynik: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Zwróć `host` wartość dla identyfikatora uniform resource identifier (URI).

```
uriHost('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Tak | String | Identyfikator URI którego `host` wartość, która ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*host-value*> | String | `host` Wartość dla określonego identyfikatora URI |
||||

*Przykład*

W tym przykładzie wyszukuje `host` wartość dla tego identyfikatora URI:

```
uriHost('https://www.localhost.com:8080')
```

I zwraca wynik: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Zwróć `path` wartość dla identyfikatora uniform resource identifier (URI).

```
uriPath('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Tak | String | Identyfikator URI którego `path` wartość, która ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*path-value*> | String | `path` Wartość dla określonego identyfikatora URI. Jeśli `path` nie ma wartości, zwraca znak "/". |
||||

*Przykład*

W tym przykładzie wyszukuje `path` wartość dla tego identyfikatora URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca wynik: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Zwróć `path` i `query` wartości dla identyfikatora uniform resource identifier (URI).

```
uriPathAndQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Yes | String | Identyfikator URI którego `path` i `query` wartości, które mają |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*path-query-value*> | String | `path` i `query` wartości dla określonego identyfikatora URI. Jeśli `path` nie Określ wartość, zwracają znaku "/". |
||||

*Przykład*

W tym przykładzie wyszukuje `path` i `query` wartości dla tego identyfikatora URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca wynik: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Zwróć `port` wartość dla identyfikatora uniform resource identifier (URI).

```
uriPort('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Yes | String | Identyfikator URI którego `port` wartość, która ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*port-value*> | Integer | `port` Wartość dla określonego identyfikatora URI. Jeśli `port` nie określenia wartości, zwraca domyślny port dla protokołu. |
||||

*Przykład*

W tym przykładzie zwraca `port` wartość dla tego identyfikatora URI:

```
uriPort('http://www.localhost:8080')
```

I zwraca wynik: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Zwróć `query` wartość dla identyfikatora uniform resource identifier (URI).

```
uriQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Tak | String | Identyfikator URI którego `query` wartość, która ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*query-value*> | String | `query` Wartość dla określonego identyfikatora URI |
||||

*Przykład*

W tym przykładzie zwraca `query` wartość dla tego identyfikatora URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca wynik: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Zwróć `scheme` wartość dla identyfikatora uniform resource identifier (URI).

```
uriScheme('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Tak | String | Identyfikator URI którego `scheme` wartość, która ma |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*scheme-value*> | String | `scheme` Wartość dla określonego identyfikatora URI |
||||

*Przykład*

W tym przykładzie zwraca `scheme` wartość dla tego identyfikatora URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca wynik: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Zwraca bieżącą sygnaturę czasową.

```
utcNow('<format>')
```

Opcjonalnie można określić innego formatu <*format*> parametr.


| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Format*> | Nie | String | Albo [pojedynczego specyfikatora formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowy wzorzec formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddTHH:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | String | Bieżącą datę i godzinę |
||||

*Przykład 1*

Załóżmy, że dziś jest 15 kwietnia 2018 r. o 13:00:00.
W tym przykładzie pobiera bieżącą sygnaturę czasową:

```
utcNow()
```

I zwraca wynik: `"2018-04-15T13:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że dziś jest 15 kwietnia 2018 r. o 13:00:00.
W tym przykładzie pobiera bieżącą sygnaturę czasową, używając formatu "D" opcjonalne:

```
utcNow('D')
```

I zwraca wynik: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Zmienne

Zwraca wartość określonej zmiennej.

```
variables('<variableName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Tak | String | Nazwa zmiennej, której wartość chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość zmiennej*> | Dowolne | Wartość określonej zmiennej |
||||

*Przykład*

Załóżmy, że bieżąca wartość dla zmiennej "numItems" wynosi 20.
W tym przykładzie pobiera wartość całkowitą dla tej zmiennej:

```
variables('numItems')
```

I zwraca wynik: `20`

<a name="workflow"></a>

### <a name="workflow"></a>przepływ pracy

Zwraca wszystkie szczegóły samego przepływu pracy w czasie wykonywania.

```
workflow().<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*property*> | Nie | String | Nazwa właściwości przepływu pracy, którego wartość ma <p>Obiekt przepływu pracy ma następujące właściwości: **nazwa**, **typu**, **identyfikator**, **lokalizacji**, i **Uruchom**. **Uruchom** wartość właściwości jest także obiekt, który ma następujące właściwości: **nazwa**, **typu**, i **identyfikator**. |
|||||

*Przykład*

W tym przykładzie zwraca nazwę Bieżący przebieg przepływu pracy:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Zwraca ciąg, który zawiera obiekt JSON w wersji XML.

```
xml('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*value*> | Tak | String | Ciąg z obiektu JSON do przekonwertowania <p>Obiekt JSON właściwość musi mieć tylko jeden katalog główny, który nie może być tablicą. <br>Należy użyć znaku ukośnika odwrotnego (\\) jako znak ucieczki dla podwójny cudzysłów ("). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | Zakodowany XML określony ciąg lub obiekt JSON |
||||

*Przykład 1*

W tym przykładzie tworzy wersję XML dla tego ciągu, który zawiera obiekt JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

I zwraca wynik XML:

```xml
<name>Sophia Owen</name>
```

*Przykład 2*

Załóżmy, że ten obiekt JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

W tym przykładzie tworzy XML na ciąg, który zawiera ten obiekt JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

I zwraca wynik XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>wyrażenie XPath

Sprawdź XML węzłów lub wartości, które odpowiadają wyrażenie XPath (XML Path Language), a następnie zwracają pasujące węzły lub wartości. Wyrażenie XPath, lub po prostu "XPath", pomaga umożliwia przejście strukturę dokumentu XML, można wybrać wartości obliczeniowe lub węzłów, które znajdują się w zawartości XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Yes | Dowolne | Ciąg XML, aby wyszukać węzłów lub wartości, które odpowiadają wartości wyrażenia XPath |
| <*xpath*> | Tak | Dowolne | Wyrażenie XPath, używana do znajdowania pasującego węzłów XML lub wartości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Węzeł XML, gdy tylko jeden węzeł odpowiada określonemu wyrażeniu XPath |
| <*value*> | Dowolne | Wartość z węzła XML, gdy tylko jedna wartość jest zgodna z określonego wyrażenia XPath |
| [<*Węzeł1 xml*>, <*Węzeł2 xml*>,...] </br>—lub— </br>[<*wartość1*>, <*wartość2*>,...] | Tablica | Tablicy o liczbie węzłów XML lub wartości, które pasują do określonego wyrażenia XPath |
||||

*Przykład 1*

W tym przykładzie wyszukuje węzły, które odpowiadają `<name></name>` węzła w określonych argumentów i zwraca tablicę z tych wartości węzła:

`xpath(xml(parameters('items')), '/produce/item/name')`

Oto argumenty:

* Ciąg "items", który zawiera plik XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  W przykładzie użyto [parameters()](#parameters) funkcji można pobrać ciągu XML z argumentem "items", ale także przekonwertować ciąg do formatu XML przy użyciu [xml()](#xml) funkcji.

* To wyrażenie XPath, który jest przekazywany jako ciąg:

  `"/produce/item/name"`

W tym miejscu jest tablica wynikowa z węzłami, które odpowiadają `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Przykład 2*

Czynności, na przykład 1, w tym przykładzie wyszukuje węzły, które odpowiadają `<count></count>` węzła i dodaje te wartości węzła przy użyciu `sum()` funkcji:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

I zwraca wynik: `30`

*Przykład 3*

W tym przykładzie oba wyrażenia Znajdź węzły, które odpowiadają `<location></location>` węzła, w określonych argumentów, które obejmują XML z przestrzenią nazw. Wyrażenia użyć znaku ukośnika odwrotnego (\\) jako znak ucieczki dla podwójny cudzysłów (").

* *Expression 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Expression 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Oto argumenty:

* Poniższy kod XML zawiera przestrzeń nazw dokumentu XML, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Albo wyrażenie XPath tutaj:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Oto węzeł wynik, który odpowiada `<location></location>` węzła:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Przykład 4*

Czynności, na przykład 3, w tym przykładzie wyszukuje wartość `<location></location>` węzła:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

I zwraca wynik: `"Paris"`

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [język definicji przepływów pracy](../logic-apps/logic-apps-workflow-definition-language.md)
