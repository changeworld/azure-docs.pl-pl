---
title: Funkcje języka definicji przepływu pracy — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o funkcji tworzenia aplikacji logiki z języka definicji przepływu pracy
services: logic-apps
author: ecfan
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/25/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 8240ddf8a93c6589f89f3ad680c1c99c594742c9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="workflow-definition-language-functions-reference-for-azure-logic-apps"></a>Odwołanie do funkcji języka definicji przepływu pracy dla usługi Azure Logic Apps

W tym artykule opisano funkcje, których można użyć podczas tworzenia przepływów pracy z [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Aby dowiedzieć się więcej na temat funkcji w definicji aplikacji logiki, zobacz [język definicji przepływu pracy dla usługi Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md#functions). 

> [!NOTE]
> W składni definicji parametru znak zapytania (?), która pojawia się po parametr oznacza, że parametr jest opcjonalny. Na przykład, zobacz [getFutureTime()](#getFutureTime).

<a name="action"></a>

## <a name="action"></a>action

Zwraca *bieżącego* akcji danych wyjściowych w czasie wykonywania lub wartości z innych pary nazwa wartość JSON, które można przypisać do wyrażenia. Domyślnie ta funkcja odwołuje się do obiektu całą operację, ale Opcjonalnie można określić właściwości, której wartość ma. Zobacz też [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Można użyć `action()` funkcja tylko w tych miejscach: 

* `unsubscribe` Właściwość dla akcji elementu webhook, aby dostęp do wyniku z oryginalnego `subscribe` żądania
* `trackedProperties` Właściwość dla akcji
* `do-until` Pętli warunku akcji

```
action()
action().outputs.body.<property> 
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Właściwość*> | Nie | Ciąg | Nazwa właściwości obiektu akcji którego wartość ma: **nazwa**, **startTime**, **endTime**, **dane wejściowe**,  **generuje**, **stan**, **kod**, **trackingId**, i **clientTrackingId**. W portalu Azure te właściwości można znaleźć, sprawdzając szczegóły określonych wykonywania w historii. Aby uzyskać więcej informacji, zobacz [interfejsu API REST - akcji uruchamiania przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | -----| ----------- | 
| <*wynik akcji*> | Ciąg | Dane wyjściowe z bieżącej akcji lub właściwość | 
|||| 

<a name="actionBody"></a>

## <a name="actionbody"></a>actionBody

Akcja zwrotu `body` danych wyjściowych w czasie wykonywania. Skrócona forma `actions('<actionName>').outputs.body`. Zobacz [body()](#body) i [actions()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Nazwa akcji*> | Yes | Ciąg | Nazwa akcji `body` wyjściowy, który ma | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | -----| ----------- | 
| <*wynik akcji treści*> | Ciąg | `body` Dane wyjściowe z określoną akcję | 
|||| 

*Przykład*

W tym przykładzie pobiera `body` dane wyjściowe z akcji Twitter `Get user`: 

```
actionBody('Get_user')
```

I zwraca wynik tego:

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

## <a name="actionoutputs"></a>actionOutputs

Zwraca wynik akcji w czasie wykonywania. Skrócona forma `actions('<actionName>').outputs`. Zobacz [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Nazwa akcji*> | Yes | Ciąg | Nazwa akcji danych wyjściowych, które mają | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | -----| ----------- | 
| <*Dane wyjściowe*> | Ciąg | Dane wyjściowe z określoną akcję | 
|||| 

*Przykład*

W tym przykładzie pobiera dane wyjściowe z akcji Twitter `Get user`: 

```
actionOutputs('Get_user')
```

I zwraca wynik tego:

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

## <a name="actions"></a>Akcje

Zwraca wynik akcji w czasie wykonywania lub wartości z innych pary nazwa wartość JSON, które można przypisać do wyrażenia. Domyślnie funkcja odwołuje się do obiektu całą operację, ale Opcjonalnie można określić właściwości którego żądaną wartość. Skrócona wersje można znaleźć [actionBody()](#actionBody), [actionOutputs()](#actionOutputs), i [body()](#body). Dla bieżącej akcji, zobacz [action()](#action).

> [!NOTE] 
> Wcześniej, można użyć `actions()` funkcji lub `conditions` element podczas określania, czy akcja był uruchamiany na podstawie na dane wyjściowe z inną akcję. Jednak aby jawnie zadeklarować zależności między działaniami, należy używać działań zależnych `runAfter` właściwości. Aby dowiedzieć się więcej o `runAfter` właściwości, zobacz [wychwycić i obsłużyć awarie razem z właściwością runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property> 
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Nazwa akcji*> | Yes | Ciąg | Nazwa dla obiektu akcji których dane wyjściowe mają  | 
| <*Właściwość*> | Nie | Ciąg | Nazwa właściwości obiektu akcji którego wartość ma: **nazwa**, **startTime**, **endTime**, **dane wejściowe**,  **generuje**, **stan**, **kod**, **trackingId**, i **clientTrackingId**. W portalu Azure te właściwości można znaleźć, sprawdzając szczegóły określonych wykonywania w historii. Aby uzyskać więcej informacji, zobacz [interfejsu API REST - akcji uruchamiania przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | -----| ----------- | 
| <*wynik akcji*> | Ciąg | Dane wyjściowe do określonej akcji lub właściwości | 
|||| 

*Przykład*

W tym przykładzie pobiera `status` wartość właściwości z akcji Twitter `Get user` w czasie wykonywania: 

```
actions('Get_user').outputs.body.status 
```

I zwraca wynik tego: `"Succeeded"`

<a name="add"></a>

## <a name="add"></a>dodaj

Zwraca wynik z dodanie dwóch liczb.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*summand_1*>, <*summand_2*> | Yes | Liczba całkowita, Float, lub mieszane | Numery do dodania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | -----| ----------- | 
| <*Suma wyników*> | Liczba całkowita lub Float | Dodawanie określonej liczby wynik | 
|||| 

*Przykład*

W tym przykładzie dodaje określonej liczby:

```
add(1, 1.5)
```

I zwraca wynik tego: `2.5`

<a name="addDays"></a>

## <a name="adddays"></a>addDays

Dodaj liczbę dni do sygnatury czasowej.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*dni korzystania z*> | Yes | Liczba całkowita | Dodatnie lub ujemne liczbę dni do dodania | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Znacznik czasu oraz określonej liczby dni  | 
|||| 

*Przykład 1*

W tym przykładzie dodaje 10 dni do określonej sygnatury czasowej:

```
addDays('2018-03-15T13:00:00Z', 10)
```

I zwraca wynik tego: `"2018-03-25T00:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć dni od określonej sygnatury czasowej:

```
addDays('2018-03-15T00:00:00Z', -5)
```

I zwraca wynik tego: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

## <a name="addhours"></a>addHours

Dodaj liczbę godzin do sygnatury czasowej.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*Godziny*> | Yes | Liczba całkowita | Dodatnie lub ujemne liczbę godzin, aby dodać | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Znacznik czasu oraz wybranej liczby godzin  | 
|||| 

*Przykład 1*

W tym przykładzie dodaje 10 godzin do określonej sygnatury czasowej:

```
addHours('2018-03-15T00:00:00Z', 10)
```

I zwraca wynik tego: `"2018-03-15T10:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć godzin od określonej sygnatury czasowej:

```
addHours('2018-03-15T15:00:00Z', -5)
```

I zwraca wynik tego: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

## <a name="addminutes"></a>addMinutes

Dodaj liczbę minut do sygnatury czasowej.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*minut*> | Yes | Liczba całkowita | Dodatnie lub ujemne liczbę minut, aby dodać | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Znacznik czasu oraz określoną liczbę minut | 
|||| 

*Przykład 1*

W tym przykładzie dodaje 10 minut do określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

I zwraca wynik tego: `"2018-03-15T00:20:00.0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć minut od określonej sygnatury czasowej:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

I zwraca wynik tego: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

## <a name="addproperty"></a>addProperty

Dodaj właściwość i jej wartość lub pary nazwa wartość do obiektu JSON, a następnie wróć zaktualizowany obiekt. Jeśli ten obiekt już istnieje w czasie wykonywania, funkcja zwraca błąd.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | Yes | Obiekt | Obiekt JSON, w której chcesz dodać właściwość | 
| <*Właściwość*> | Yes | Ciąg | Nazwa właściwości do dodania | 
| <*Wartość*> | Yes | Dowolne | Wartość właściwości |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano obiekt*> | Obiekt | Zaktualizowany obiekt JSON z określonej właściwości | 
|||| 

*Przykład*

W tym przykładzie dodaje `accountNumber` właściwości `customerProfile` obiektu, który jest konwertowana na format JSON z [JSON()](#json) funkcji. Funkcja przypisuje wartość, która jest generowana przez [guid()](#guid) funkcji i zwraca obiekt zaktualizowane:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

## <a name="addseconds"></a>addSeconds

Dodaj liczba sekund do sygnatury czasowej.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*Sekund*> | Yes | Liczba całkowita | Dodatnie lub ujemne liczbę sekund, aby dodać | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Znacznik czasu oraz określonej liczby sekund  | 
|||| 

*Przykład 1*

W tym przykładzie powoduje dodanie 10 sekund do określonej sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

I zwraca wynik tego: `"2018-03-15T00:00:10.0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięciu sekund do określonej sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

I zwraca wynik tego: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

## <a name="addtotime"></a>addToTime

Dodaj liczbę jednostek czasu do sygnatury czasowej. Zobacz też [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*Interwał*> | Yes | Liczba całkowita | Liczba jednostek określony czas do dodania | 
| <*timeUnit*> | Yes | Ciąg | Jednostka czasu do użycia z *interwał*: "Drugi", "Minute", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Year" | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Znacznik czasu oraz określoną liczbę jednostek czasu  | 
|||| 

*Przykład 1*

W tym przykładzie dodaje jeden dzień do określonej sygnatury czasowej:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day') 
```

I zwraca wynik tego: `"2018-01-02T00:00:00:0000000Z"`

*Przykład 2*

W tym przykładzie dodaje jeden dzień do określonej sygnatury czasowej:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

I zwraca wynik w formacie opcjonalne "D": `"Tuesday, January 2, 2018"`

<a name="and"></a>

## <a name="and"></a>i

Sprawdź, czy wszystkie wyrażenia mają wartość true. Zwraca wartość true, gdy spełnione są wszystkie wyrażenia lub zwróci wartość false, gdy co najmniej jedno wyrażenie ma wartość false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*wyrażenie1*>, <*wyrażenie2*>,... | Yes | Wartość logiczna | Wyrażenia, aby sprawdzić | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | -----| ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Zwraca wartość true, gdy spełnione są wszystkie wyrażenia. Zwraca false, gdy co najmniej jedno wyrażenie ma wartość false. | 
|||| 

*Przykład 1*

Te przykłady Sprawdź, czy określonej wartości logiczne to: true wszystkie:

```
and(true, true)
and(false, true)
and(false, false)
```

I zwraca wyniki:

* Pierwszym przykładzie: oba wyrażenia są spełnione, więc zwraca `true`. 
* Drugi przykład: jedno wyrażenie ma wartość false, więc zwraca `false`.
* Przykład trzeci: oba wyrażenia są false, więc zwraca `false`.

*Przykład 2*

Te przykłady Sprawdź, czy określonego wyrażenia są warunki:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

I zwraca wyniki:

* Pierwszym przykładzie: oba wyrażenia są spełnione, więc zwraca `true`. 
* Drugi przykład: jedno wyrażenie ma wartość false, więc zwraca `false`.
* Przykład trzeci: oba wyrażenia są false, więc zwraca `false`.

<a name="array"></a>

## <a name="array"></a>tablica

Zwraca tablicę z jednej z określonych danych wejściowych. Dla wielu danych wejściowych, zobacz [createArray()](#createArray). 

```
array('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg do utworzenia tablicy | 
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

I zwraca wynik tego: `["hello"]`

<a name="base64"></a>

## <a name="base64"></a>base64

Zwraca ciąg wersji algorytmem Base64.

```
base64('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg wejściowy | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*ciąg base64*> | Ciąg | Wersja kodowany w formacie base64 dla ciągu wejściowego | 
|||| 

*Przykład*

W tym przykładzie konwertuje ciąg "hello" na ciąg kodowany w formacie base64:

```
base64('hello')
```

I zwraca wynik tego: `"aGVsbG8="`

<a name="base64ToBinary"></a>

## <a name="base64tobinary"></a>base64ToBinary

Zwróć binarnej wersji dla ciągu zakodowanego algorytmem base64.

```
base64ToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg kodowany w formacie base64 do przekonwertowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dane binarne dla base64 ciągu*> | Ciąg | Binarna wersja dla ciągu zakodowanego algorytmem base64 | 
|||| 

*Przykład*

W tym przykładzie konwertuje "aGVsbG8 =" Ciąg zakodowany w formacie base64 ciąg binarny:

```
base64ToBinary('aGVsbG8=')
```

I zwraca wynik tego: 

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

## <a name="base64tostring"></a>base64ToString

Zwraca ciąg wersji ciąg kodowany w formacie base64, efektywnie dekodowania ciąg base64. Aby użyć tej funkcji zamiast [decodeBase64()](#decodeBase64). Mimo że obie funkcje działają tak samo, `base64ToString()` jest preferowana.

```
base64ToString('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg kodowany w formacie base64, zdekodować | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Dekodowany-— ciąg base64*> | Ciąg | Ciąg wersji ciąg kodowany w formacie base64 | 
|||| 

*Przykład*

W tym przykładzie konwertuje "aGVsbG8 =" ciąg kodowany w formacie base64, aby po prostu określonym ciągiem:

```
base64ToString('aGVsbG8=')
```

I zwraca wynik tego: `"hello"`

<a name="binary"></a>

## <a name="binary"></a>Binarne 

Zwróć binarna wersja ciągu.

```
binary('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg do przekonwertowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dane binarne dla danych wejściowych wartość*> | Ciąg | Binarna wersja dla określonego ciągu | 
|||| 

*Przykład*

W tym przykładzie konwertuje ciąg "hello" na ciąg binarny:

```
binary('hello')
```

I zwraca wynik tego: 

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

## <a name="body"></a>treść

Akcja zwrotu `body` danych wyjściowych w czasie wykonywania. Skrócona forma `actions('<actionName>').outputs.body`. Zobacz [actionBody()](#actionBody) i [actions()](#actions).

```
body('<actionName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Nazwa akcji*> | Yes | Ciąg | Nazwa akcji `body` wyjściowy, który ma | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | -----| ----------- | 
| <*wynik akcji treści*> | Ciąg | `body` Dane wyjściowe z określoną akcję | 
|||| 

*Przykład*

W tym przykładzie pobiera `body` dane wyjściowe z `Get user` Twitter akcji: 

```
body('Get_user')
```

I zwraca wynik tego: 

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

## <a name="bool"></a>wartość logiczna

Zwracany Boolean wersji dla wartości.

```
bool(<value>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Dowolne | Wartość do przekonwertowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Wartość logiczna wersji określoną wartość | 
|||| 

*Przykład*

Te przykłady przekonwertować określonej wartości na wartości logiczne: 

```
bool(1)
bool(0)
```

I zwraca wyniki: 

* Pierwszym przykładzie: `true` 
* Drugi przykład: `false`

<a name="coalesce"></a>

## <a name="coalesce"></a>połączenie

Zwraca pierwszą wartość inną niż null z co najmniej jeden parametr. Puste ciągi, puste tablice i puste obiekty nie są wartości null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*object_1*>, <*object_2*>,... | Yes | Można mieszać typów | Jeden lub więcej elementów do sprawdzenia wartości null | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*pierwszy z systemem innym niż null — element*> | Dowolne | Pierwszy element lub wartość, która nie jest zerowa. Jeśli wszystkie parametry mają wartość null, funkcja zwraca wartość null. | 
|||| 

*Przykład*

Poniższe przykłady zwracanie pierwsza wartość inną niż null z określonymi wartościami lub wartość null, wszystkie wartości są równe null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

I zwraca wyniki: 

* Pierwszym przykładzie: `true` 
* Drugi przykład: `"hello"`
* Przykładem jest: `null`

<a name="concat"></a>

## <a name="concat"></a>concat

Połącz co najmniej dwa ciągi i zwraca połączony ciąg. 

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst1*>, <*Tekst2*>,... | Yes | Ciąg | Co najmniej dwa ciągi do łączenia | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*text1text2...*> | Ciąg | Ciąg utworzony z połączonych ciągów wejściowych | 
|||| 

*Przykład*

W tym przykładzie połączenie ciągów "Hello" i "World":

```
concat('Hello', 'World')
```

I zwraca wynik tego: `"HelloWorld"`

<a name="contains"></a>

## <a name="contains"></a>zawiera

Sprawdź, czy kolekcja zawiera konkretny element. Zwróć wartość true, gdy element zostanie znaleziony, lub zwróci wartość false, gdy nie można odnaleźć. Ta funkcja jest rozróżniana wielkość liter.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

W szczególności ta funkcja działa dla tych typów kolekcji: 

* A *ciąg* można znaleźć *podciągu*
* *Tablicy* można znaleźć *wartość*
* A *słownika* można znaleźć *klucza*

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekcja*> | Yes | Ciąg, tablicy lub słownik | Aby sprawdzić kolekcji | 
| <*Wartość*> | Yes | Ciąg, tablicy lub słownika, odpowiednio | Element, aby znaleźć | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Zwrócić wartość true, gdy element zostanie znaleziony. Zwraca false Jeśli nie można odnaleźć. |
|||| 

*Przykład 1*

W tym przykładzie sprawdza ciąg "hello world" dla "world" podciąg i zwraca wartość true:

```
contains('hello world', 'world')
```

*Przykład 2*

W tym przykładzie sprawdza ciąg "hello world" dla "universe" podciąg i zwraca wartość false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

## <a name="convertfromutc"></a>convertFromUtc

Konwertuj sygnaturę czasową z uniwersalny czas koordynowany (UTC) zgodnie ze strefą czasową docelowej.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*destinationTimeZone*> | Yes | Ciąg | Nazwa strefy czasowej docelowej. Aby uzyskać więcej informacji, zobacz [strefy czasowej ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*przekonwertować sygnatury czasowej*> | Ciąg | Sygnatura czasowa konwertowane na strefę czasową docelowego | 
|||| 

*Przykład 1*

W tym przykładzie Konwertuje sygnatury czasowej określona strefa czasowa: 

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

I zwraca wynik tego: `"2018-01-01T00:00:00.0000000"`

*Przykład 2*

W tym przykładzie konwertuje określona strefa czasowa i format sygnatury czasowej:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

I zwraca wynik tego: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

## <a name="converttimezone"></a>convertTimeZone

Przekonwertować sygnatury czasowej ze źródłową strefą czasową zgodnie ze strefą czasową docelowej.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*sourceTimeZone*> | Yes | Ciąg | Nazwa strefy czasowej źródła. Aby uzyskać więcej informacji, zobacz [strefy czasowej ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*destinationTimeZone*> | Yes | Ciąg | Nazwa strefy czasowej docelowej. Aby uzyskać więcej informacji, zobacz [strefy czasowej ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*przekonwertować sygnatury czasowej*> | Ciąg | Sygnatura czasowa konwertowane na strefę czasową docelowego | 
|||| 

*Przykład 1*

W tym przykładzie konwertuje źródłową strefą czasową zgodnie ze strefą czasową docelowych: 

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

I zwraca wynik tego: `"2018-01-01T00:00:00.0000000"`

*Przykład 2*

W tym przykładzie konwertuje strefę czasową określona strefa czasowa i formatu:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

I zwraca wynik tego: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

## <a name="converttoutc"></a>convertToUtc

Konwertuj sygnaturę czasową ze źródłową strefą czasową uniwersalny czas koordynowany (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*sourceTimeZone*> | Yes | Ciąg | Nazwa strefy czasowej źródła. Aby uzyskać więcej informacji, zobacz [strefy czasowej ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*przekonwertować sygnatury czasowej*> | Ciąg | Sygnatura czasowa konwertowana na czas UTC | 
|||| 

*Przykład 1*

W tym przykładzie konwertuje sygnaturę czasową UTC: 

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

I zwraca wynik tego: `"2018-01-01T08:00:00.0000000Z"`

*Przykład 2*

W tym przykładzie konwertuje sygnaturę czasową UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

I zwraca wynik tego: `"Monday, January 1, 2018"`

<a name="createArray"></a>

## <a name="createarray"></a>createArray

Zwraca tablicę z wielu danych wejściowych. Dla pojedynczego wejściowych tablic, zobacz [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Obiekt1*>, <*obiekt2*>,... | Yes | Występuje, ale nie mieszane | Co najmniej dwa elementy do utworzenia tablicy | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| [<*Obiekt1*>, <*obiekt2*>,...] | Tablica | Tablica utworzone na podstawie wszystkich elementów wejściowych | 
|||| 

*Przykład*

W tym przykładzie tworzy tablicę na podstawie tych danych wejściowych:

```
createArray('h', 'e', 'l', 'l', 'o')
```

I zwraca wynik tego: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

## <a name="datauri"></a>dataUri

Zwróć danych uniform resource identifier (URI) ciągu. 

```
dataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg do przekonwertowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Identyfikator uri danych*> | Ciąg | Identyfikator URI danych dla ciągu wejściowego | 
|||| 

*Przykład*

W tym przykładzie tworzy identyfikator URI danych dla ciągu "hello":

```
dataUri('hello') 
```

I zwraca wynik tego: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

## <a name="datauritobinary"></a>dataUriToBinary

Zwróć binarnej wersji dla identyfikatora URI danych (URI). Aby użyć tej funkcji zamiast [decodeDataUri()](#decodeDataUri). Mimo że obie funkcje działają tak samo, `decodeDataUri()` jest preferowana.

```
dataUriToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Identyfikator URI do przekonwertowania danych | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dane binarne dla danych uri*> | Ciąg | Binarna wersja dla identyfikatora URI danych | 
|||| 

*Przykład*

W tym przykładzie jest tworzony binarnej wersji danych identyfikatora URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca wynik tego: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

## <a name="datauritostring"></a>dataUriToString

Zwraca ciąg wersji danych Identyfikator uniform resource identifier (URI).

```
dataUriToString('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Identyfikator URI do przekonwertowania danych | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*ciąg dla danych uri*> | Ciąg | Wersja ciągu dla identyfikatora URI danych | 
|||| 

*Przykład*

W tym przykładzie tworzy ciąg identyfikatora URI danych:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca wynik tego: `"hello"`

<a name="dayOfMonth"></a>

## <a name="dayofmonth"></a>DayOfMonth

Zwróć dzień miesiąca z sygnatury czasowej. 

```
dayOfMonth('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dzień miesiąca*> | Liczba całkowita | Dzień miesiąca z określonej sygnatury czasowej | 
|||| 

*Przykład*

W tym przykładzie zwraca numer dnia miesiąca z sygnatura czasowa:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

I zwraca wynik tego: `15`

<a name="dayOfWeek"></a>

## <a name="dayofweek"></a>DayOfWeek

Zwraca dzień tygodnia z sygnatury czasowej.  

```
dayOfWeek('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dzień tygodnia*> | Liczba całkowita | Dzień tygodnia z określonej sygnatury czasowej, których niedziela wynosi 0, od poniedziałku jest 1 i tak dalej | 
|||| 

*Przykład*

W tym przykładzie zwraca numer dnia tygodnia z sygnatura czasowa:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

I zwraca wynik tego: `3`

<a name="dayOfYear"></a>

## <a name="dayofyear"></a>Dzieńroku

Zwróć dzień roku z sygnatury czasowej. 

```
dayOfYear('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dzień roku*> | Liczba całkowita | Dzień roku z określonej sygnatury czasowej | 
|||| 

*Przykład*

W tym przykładzie zwraca liczbę dni w roku od sygnatura czasowa:

```
dayOfYear('2018-03-15T13:27:36Z')
```

I zwraca wynik tego: `74`

<a name="decodeBase64"></a>

## <a name="decodebase64"></a>decodeBase64

Zwraca ciąg wersji ciąg kodowany w formacie base64, efektywnie dekodowania ciąg base64. Należy rozważyć użycie [base64ToString()](#base64ToString) zamiast `decodeBase64()`. Mimo że obie funkcje działają tak samo, `base64ToString()` jest preferowana.

```
decodeBase64('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg kodowany w formacie base64, zdekodować | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Dekodowany-— ciąg base64*> | Ciąg | Ciąg wersji ciąg kodowany w formacie base64 | 
|||| 

*Przykład*

W tym przykładzie tworzy ciąg na ciąg kodowany w formacie base64:

```
decodeBase64('aGVsbG8=')
```

I zwraca wynik tego: `"hello"`

<a name="decodeDataUri"></a>

## <a name="decodedatauri"></a>decodeDataUri

Zwróć binarnej wersji dla identyfikatora URI danych (URI). Należy rozważyć użycie [dataUriToBinary()](#dataUriToBinary), a nie `decodeDataUri()`. Mimo że obie funkcje działają tak samo, `dataUriToBinary()` jest preferowana.

```
decodeDataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Dane ciągu identyfikatora URI w celu zdekodowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dane binarne dla danych uri*> | Ciąg | Binarna wersja danych ciągu identyfikatora URI. | 
|||| 

*Przykład*

W tym przykładzie zwraca binarnej wersji danych identyfikatora URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca wynik tego: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

## <a name="decodeuricomponent"></a>decodeuricomponent —

Zwracany ciąg czy zastępuje Usuń znaki dekodowane wersje. 

```
decodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg z znaki specjalne w celu zdekodowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dekodowany uri*> | Ciąg | Zaktualizowano ciągu z znaki dekodowane specjalne | 
|||| 

*Przykład*

W tym przykładzie zastępuje znaki specjalne w tym ciągu dekodowane wersji:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

I zwraca wynik tego: `"https://contoso.com"`

<a name="div"></a>

## <a name="div"></a>div

Zwracają wynik liczbą całkowitą z dzielenia liczby dwa. Aby uzyskać wynik pozostałą, zobacz [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*dzielna*> | Yes | Liczba całkowita lub Float | Numer do dzielenia przez *dzielnik.* | 
| <*dzielnik.*> | Yes | Liczba całkowita lub Float | Liczba, która dzieli *dzielna*, ale nie może wynosić 0 | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*iloraz wyniku*> | Liczba całkowita | Całkowitą wyniku dzielenia numer pierwszej przez druga liczba | 
|||| 

*Przykład*

Oba Przykłady dzielenia numer pierwszej przez druga liczba:

```
div(10, 5)
div(11, 5)
```

I zwraca wynik tego: `2`

<a name="encodeUriComponent"></a>

## <a name="encodeuricomponent"></a>encodeuricomponent —

Zwraca wersję uniform resource identifier (URI) zakodowany ciągu przez zamianę znaków niezabezpieczony adres URL znaki specjalne. Należy rozważyć użycie [uriComponent()](#uriComponent), a nie `encodeUriComponent()`. Mimo że obie funkcje działają tak samo, `uriComponent()` jest preferowana.

```
encodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg do przekonwertowania na format kodowany w formacie identyfikatora URI | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*zakodowany identyfikator uri*> | Ciąg | Ciąg kodowany w formacie identyfikatora URI z znaki specjalne | 
|||| 

*Przykład*

W tym przykładzie tworzy wersję kodowany w formacie identyfikatora URI dla tych parametrów:

```
encodeUriComponent('https://contoso.com')
```

I zwraca wynik tego: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

## <a name="empty"></a>pusty

Sprawdź, czy kolekcja jest pusta. Zwraca wartość true, gdy kolekcja jest pusta lub zwróci wartość false, gdy nie jest pusty.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekcja*> | Yes | Ciąg, tablicy lub w obiekcie | Aby sprawdzić kolekcji | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Zwraca wartość true, gdy kolekcja jest pusta. Zwraca wartość false, gdy nie jest pusty. | 
|||| 

*Przykład* 

Te przykłady Sprawdź, czy określony kolekcje są puste:

```
empty('')
empty('abc')
```

I zwraca wyniki: 

* Pierwszym przykładzie: przekazuje ciąg pusty, funkcja zwraca `true`. 
* Drugi przykład: przekazuje ciąg "abc", dlatego funkcja zwraca `false`. 

<a name="endswith"></a>

## <a name="endswith"></a>endsWith

Sprawdź, czy ciąg kończy się wyrazem określonych podciąg. Zwraca wartość true, gdy zostanie znaleziony podciąg lub zwróci wartość false, gdy nie można odnaleźć. Ta funkcja nie jest rozróżniana wielkość liter.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg do sprawdzenia | 
| <*Tekstprzeszukiwany*> | Yes | Ciąg | Końcowy podciąg do znalezienia | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ  | Wartość logiczna | Gdy zostanie znaleziony podciąg końcowy zwrócić wartość true. Zwraca false Jeśli nie można odnaleźć. | 
|||| 

*Przykład 1* 

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się wyrazem "world" ciąg:

```
endsWith('hello world', 'world')
```

I zwraca wynik tego: `true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się ciągiem "universe":

```
endsWith('hello world', 'universe')
```

I zwraca wynik tego: `false`

<a name="equals"></a>

## <a name="equals"></a>równa się

Sprawdź, czy zarówno wartości, wyrażenia lub obiekty są równoważne. Gdy zarówno są równoważne lub zwróci wartość false, gdy nie są one równoważne zwrócić wartość true.

```
equals('<object1>', '<object2>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Obiekt1*>, <*obiekt2*> | Yes | Różne | Wartości, wyrażenia lub obiektów do porównania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Zwrócić wartość true, jeśli oba typy są równoważne. Zwraca wartość false, gdy nie jest równorzędny. | 
|||| 

*Przykład*

Poniższe przykłady Sprawdź, czy określone dane wejściowe są równoważne. 

```
equals(true, 1)
equals('abc', 'abcd')
```

I zwraca wyniki: 

* Pierwszym przykładzie: obie wartości są równoważne, dlatego funkcja zwraca `true`.
* Drugi exmaple: obie wartości nie są równoważne, dlatego funkcja zwraca `false`.

<a name="first"></a>

## <a name="first"></a>pierwszy

Zwraca pierwszy element z ciągiem lub tablicą.

```
first('<collection>')
first([<collection>])
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekcja*> | Yes | Ciąg lub tablica | Kolekcja gdzie można znaleźć pierwszego elementu |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*pierwszej kolekcji elementów*> | Dowolne | Pierwszy element w kolekcji | 
|||| 

*Przykład*

Poniższe przykłady znaleźć pierwszego elementu w tych kolekcjach:

```
first('hello')
first([0, 1, 2])
```

I zwrócone następujące wyniki: 

* Pierwszym przykładzie: `"h"`
* Drugi exmaple: `0`

<a name="float"></a>

## <a name="float"></a>Float

Konwertuj ciąg wersji dla liczba zmiennoprzecinkowa na rzeczywista liczba zmiennoprzecinkowa. Ta funkcja służy tylko wtedy, gdy przekazywanie niestandardowych parametrów do aplikacji, takich jak aplikacji logiki.

```
float('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg, który ma prawidłową liczbę zmiennoprzecinkowe do przekonwertowania |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartości typu float*> | Float | Liczba zmiennoprzecinkowa dla określonego ciągu | 
|||| 

*Przykład*

W tym przykładzie jest tworzony ciąg wersji dla tego liczba zmiennoprzecinkowa:

```
float('10.333')
```

I zwraca wynik tego: `10.333`

<a name="formatDateTime"></a>

## <a name="formatdatetime"></a>formatDateTime

Zwraca sygnaturę czasową w określonym formacie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*ponownie sformatowany sygnatury czasowej*> | Ciąg | Zaktualizowano sygnatury czasowej w określonym formacie | 
|||| 

*Przykład*

W tym przykładzie konwertuje określony format sygnatury czasowej:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

I zwraca wynik tego: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

## <a name="formdatamultivalues"></a>formDataMultiValues

Zwraca tablicę z wartościami zgodnych z nazwą klucza w akcji *dane formularza* lub *postać zakodowanych* danych wyjściowych. 

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Nazwa akcji*> | Yes | Ciąg | Akcję, której wyjście ma wartość klucza | 
| <*Klucz*> | Yes | Ciąg | Nazwa klucza, którego wartość ma | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| [<*tablicy z klucza wartości*>] | Tablica | Tablica nie zawierająca wszystkie wartości, które zgodny z określonym kluczem | 
|||| 

*Przykład* 

W tym przykładzie tworzy tablicę z wartości klucza "Temat" w określonej akcji danych formularza lub wynik zakodowany w postaci:  

```
formDataMultiValues('Send_an_email', 'Subject')
```

I zwraca tekst tematu w tablicy, na przykład: `["Hello world"]`

<a name="formDataValue"></a>

## <a name="formdatavalue"></a>formDataValue

Zwraca pojedynczą wartość, który odpowiada nazwie klucza w akcji *dane formularza* lub *postać zakodowanych* danych wyjściowych. Jeśli funkcja znajdzie więcej niż jedno dopasowanie, funkcja zwraca błąd.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Nazwa akcji*> | Yes | Ciąg | Akcję, której wyjście ma wartość klucza | 
| <*Klucz*> | Yes | Ciąg | Nazwa klucza, którego wartość ma |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*klucz wartość*> | Ciąg | Wartość w określonym kluczu  | 
|||| 

*Przykład* 

W tym przykładzie tworzy ciąg z wartości klucza "Temat" w określonej akcji danych formularza lub wynik zakodowany w postaci:  

```
formDataValue('Send_an_email', 'Subject')
```

I zwraca tekst tematu jako ciąg, na przykład: `"Hello world"`

<a name="getFutureTime"></a>

## <a name="getfuturetime"></a>getFutureTime

Zwraca bieżącą sygnaturę czasową plus jednostki określonego czasu.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Interwał*> | Yes | Liczba całkowita | Liczba jednostek określonego czasu do odjęcia | 
| <*timeUnit*> | Yes | Ciąg | Jednostka czasu do użycia z *interwał*: "Drugi", "Minute", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Year" | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Bieżąca sygnatura czasowa plus określoną liczbę jednostek czasu | 
|||| 

*Przykład 1*

Załóżmy, że bieżąca sygnatura czasowa jest "2018-03-01T00:00:00.0000000Z". W tym przykładzie dodano pięć dni na ten znacznik:

```
getFutureTime(5, 'Day')
```

I zwraca wynik tego: `"2018-03-06T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżąca sygnatura czasowa jest "2018-03-01T00:00:00.0000000Z". W tym przykładzie dodaje pięć dni i konwertuje wynik do formatu "D":

```
getFutureTime(5, 'Day', 'D')
```

I zwraca wynik tego: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

## <a name="getpasttime"></a>getPastTime

Zwraca bieżącą sygnaturę czasową minus jednostki określonego czasu.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Interwał*> | Yes | Liczba całkowita | Liczba jednostek określonego czasu do odjęcia | 
| <*timeUnit*> | Yes | Ciąg | Jednostka czasu do użycia z *interwał*: "Drugi", "Minute", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Year" | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Bieżąca sygnatura czasowa pomniejszonej o określoną liczbę jednostek czasu | 
|||| 

*Przykład 1*

Załóżmy, że bieżąca sygnatura czasowa jest "2018-02-01T00:00:00.0000000Z". W tym przykładzie odejmuje pięć dni od tego znacznika czasu:

```
getPastTime(5, 'Day')
```

I zwraca wynik tego: `"2018-01-27T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżąca sygnatura czasowa jest "2018-02-01T00:00:00.0000000Z". W tym przykładzie odejmuje pięć dni i konwertuje wynik do formatu "D":

```
getPastTime(5, 'Day', 'D')
```

I zwraca wynik tego: `"Saturday, January 27, 2018"`

<a name="greater"></a>

## <a name="greater"></a>większa

Sprawdź, czy pierwsza wartość jest większa od drugiej wartości. Zwróć wartość true, jeśli pierwsza wartość jest więcej lub zwróci wartość false, gdy mniej.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Liczba całkowita, Float lub ciąg | Pierwsza wartość do sprawdzenia, czy jest większa niż wartość drugiej | 
| <*Wykonanie funkcji CompareTo*> | Yes | Liczba całkowita, Float lub ciąg, odpowiednio | Wartość porównania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Pierwsza wartość jest większa od drugiej wartości, zwraca wartość true. Pierwsza wartość jest równa lub mniejsza niż wartość drugiej, zwróci wartość false. | 
|||| 

*Przykład*

Poniższe przykłady Sprawdź, czy pierwsza wartość jest większa od drugiej wartości:

```
greater(10, 5)
greater('apple', 'banana')
```

I zwrócone następujące wyniki: 

* Pierwszym przykładzie: `true`
* Drugi przykład: `false`

<a name="greaterOrEquals"></a>

## <a name="greaterorequals"></a>greaterOrEquals

Sprawdź, czy pierwsza wartość jest większa niż lub równa drugiej wartości.
Zwróć wartość true, jeśli pierwsza wartość jest mniejsza lub zwróci wartość false, jeśli pierwsza wartość jest mniejsza.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Liczba całkowita, Float lub ciąg | Pierwsza wartość do sprawdzenia, czy większa lub równa wartości drugiego | 
| <*Wykonanie funkcji CompareTo*> | Yes | Liczba całkowita, Float lub ciąg, odpowiednio | Wartość porównania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Pierwsza wartość jest większa niż lub równa drugiej wartości, zwraca wartość true. Zwróci wartość false, jeśli pierwsza wartość jest mniejsza niż wartość drugiej. | 
|||| 

*Przykład*

Poniższe przykłady Sprawdź, czy pierwsza wartość jest mniejsza niż wartość drugiej:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

I zwrócone następujące wyniki: 

* Pierwszym przykładzie: `true`
* Drugi przykład: `false`

<a name="guid"></a>

## <a name="guid"></a>Identyfikator GUID

Generuj Unikatowy identyfikator globalny (GUID) jako ciąg, na przykład "c2ecc88d-88c8-4096-912c-d6f2e2b138ce": 

```
guid()
```

Ponadto można określić inny format identyfikatora GUID innego niż domyślny format "D", który jest 32 cyfry szesnastkowe rozdzielone myślnikami.

```
guid('<format>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Format*> | Nie | Ciąg | Pojedynczy [specyfikatorze formatu](https://msdn.microsoft.com/library/97af8hh4) zwracane identyfikatora GUID. Domyślnie ma format "D", ale można użyć "N", "D", "B", "P" lub "X". | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Wartość identyfikatora GUID*> | Ciąg | Losowo generowany identyfikator GUID | 
|||| 

*Przykład* 

W tym przykładzie generuje ten sam identyfikator GUID, ale jako 32 cyfry szesnastkowe rozdzielone myślnikami, a w nawiasach: 

```
guid('P')
```

I zwraca wynik tego: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

## <a name="if"></a>if

Sprawdź, czy wyrażenie jest równa true lub false. Na podstawie wyniku, zwrócić określoną wartość.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*wyrażenie*> | Yes | Wartość logiczna | Wyrażenie do sprawdzenia | 
| <*Wartość_dla_prawdy*> | Yes | Dowolne | Wartość zwracana, gdy wyrażenie jest prawdziwe | 
| <*Wartość_dla_fałszu*> | Yes | Dowolne | Wartość zwracana, gdy wyrażenie jest fałszywe | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*określony return-value*> | Dowolne | Określona wartość, która zwraca zależności od tego, czy wyrażenie jest wartość PRAWDA lub FAŁSZ | 
|||| 

*Przykład* 

W tym przykładzie zwraca `"yes"` ponieważ określone wyrażenie zwraca wartość true. W przeciwnym razie zwraca przykładzie `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

## <a name="indexof"></a>indexOf

Zwraca pozycję początkową lub wartość indeksu dla podciąg. Ta funkcja nie jest rozróżniana wielkość liter, a indeksy rozpoczynać się od cyfry 0. 

```
indexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg zawierający podciąg, aby znaleźć | 
| <*Tekstprzeszukiwany*> | Yes | Ciąg | Podciąg do znalezienia | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość indeksu*>| Liczba całkowita | Pozycja lub indeks wartości początkowej wskazany podciąg. <p>W przypadku ciągu nie zostanie znaleziony, zwraca liczbę -1. </br>Jeśli ciąg jest pusty, zwraca numer 0. | 
|||| 

*Przykład* 

W tym przykładzie wyszukiwanie początkowa wartość indeksu podciągu "world" w ciągu "hello world":

```
indexOf('hello world', 'world')
```

I zwraca wynik tego: `6`

<a name="int"></a>

## <a name="int"></a>int

Zwraca ciąg wersji liczby całkowitej.

```
int('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg do przekonwertowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wynik liczba całkowita*> | Liczba całkowita | Wersja całkowitą dla określonego ciągu | 
|||| 

*Przykład* 

W tym przykładzie jest tworzony wersji całkowitą dla ciągu "10":

```
int('10')
```

I zwraca wynik tego: `10`

<a name="item"></a>

## <a name="item"></a>Element

Użyta wewnątrz identycznych akcji w tablicy, zwraca bieżący element w tablicy podczas akcji bieżącej iteracji. Można także uzyskać wartości z właściwości tego elementu. 

```
item()
```

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Bieżąca tablicy element*> | Dowolne | Bieżący element w tablicy dla danej akcji bieżącej iteracji | 
|||| 

*Przykład* 

W tym przykładzie pobiera `body` element z bieżącego komunikatu dla działania "Send_an_email" w bieżącej iteracji pętli dla każdego:

```
item().body
```

<a name="items"></a>

## <a name="items"></a>pozycje

Zwraca bieżący element z każdym cyklu w pętli dla każdego. Funkcja ta wewnątrz dla każdej pętli.

```
items('<loopName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Yes | Ciąg | Nazwa dla każdej pętli for | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Element*> | Dowolne | Element z bieżącego cyklu w określonej dla każdej pętli | 
|||| 

*Przykład* 

W tym przykładzie pobiera bieżący element z określonym dla każdej pętli:

```
items('myForEachLoopName')
```

<a name="json"></a>

## <a name="json"></a>JSON

Zwraca wartość typu JavaScript Object Notation (JSON) lub obiekt string lub XML.

```
json('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg lub XML | Konwertuj, typ string lub XML | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Wyniku JSON*> | Typ macierzysty JSON lub obiektu | Wartość typu macierzystego JSON lub obiektu dla określonego ciągu lub XML. Jeśli ten ciąg ma wartość null, funkcja zwraca pusty obiekt. | 
|||| 

*Przykład 1* 

W tym przykładzie konwertuje ciąg na wartość JSON:

```
json('[1, 2, 3]')
```

I zwraca wynik tego: `[1, 2, 3]`

*Przykład 2*

W tym przykładzie konwertuje tego ciągu JSON: 

```
json('{"fullName": "Sophia Owen"}')
```

I zwraca wynik tego:

```
{
  "fullName": "Sophia Owen"
}
```

*Przykład 3*

W tym przykładzie konwertuje plik XML do formatu JSON: 

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

I zwraca wynik tego:

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

## <a name="intersection"></a>część wspólną

Zwraca kolekcję, która ma *tylko* wspólne elementy w określonej kolekcji. Pojawią się w wyniku, element musi występować we wszystkich zbiorach przekazane do tej funkcji. Jeśli jeden lub więcej elementów mają taką samą nazwę, ostatni element o takiej nazwie zostanie wyświetlony w wyniku.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>,... | Yes | Tablica lub obiekt, ale nie oba na raz | Kolekcje, z którym chcesz *tylko* wspólne elementy | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wspólne elementy*> | Tablica lub obiekt, odpowiednio | Kolekcja, która ma tylko wspólne elementy w określonej kolekcji | 
|||| 

*Przykład* 

W tym przykładzie umożliwia znalezienie wspólne elementy w tych tablicach:  

```
intersection([1, 2, 3], [101, 2, 1, 10], [6, 8, 1, 2])
```

I zwraca tablicę z *tylko* te elementy: `[1, 2]`

<a name="join"></a>

## <a name="join"></a>join

Zwracany ciąg, który zawiera wszystkie elementy z tablicy i ma każdego znaku rozdzielone *ogranicznik*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekcja*> | Yes | Tablica | Tablica, która ma elementów do dołączenia |  
| <*Ogranicznik*> | Yes | Ciąg | Separator między każdego znaku w ciągu wynikowym | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*char1*><*ogranicznik*><*char2*><*ogranicznik*>... | Ciąg | Wynikowy ciąg utworzony z wszystkich elementów w określonej tablicy |
|||| 

*Przykład* 

W tym przykładzie tworzy ciąg z wszystkich elementów w tej tablicy znakiem określony jako ogranicznik:

```
join([a, b, c], '.')
```

I zwraca wynik tego: `"a.b.c"`

<a name="last"></a>

## <a name="last"></a>ostatni

Zwraca ostatni element z kolekcji.

```
last('<collection>')
last([<collection>])
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekcja*> | Yes | Ciąg lub tablica | Kolekcja gdzie można znaleźć ostatniego elementu | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*ostatnie kolekcji elementów*> | Ciąg lub tablica, odpowiednio | Ostatni element w kolekcji | 
|||| 

*Przykład* 

Poniższe przykłady odnaleźć ostatniego elementu w tych kolekcjach:

```
last('abcd')
last([0, 1, 2, 3])
```

I zwraca wyniki: 

* Pierwszym przykładzie: `"d"`
* Drugi przykład: `3`

<a name="lastindexof"></a>

## <a name="lastindexof"></a>lastIndexOf

Zwraca wartości pozycji lub indeks końcowej dla podciąg. Ta funkcja nie jest rozróżniana wielkość liter, a indeksy rozpoczynać się od cyfry 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg zawierający podciąg, aby znaleźć | 
| <*Tekstprzeszukiwany*> | Yes | Ciąg | Podciąg do znalezienia | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość końcowa indeksu*> | Liczba całkowita | Końcowy pozycji lub indeks wartość wskazany podciąg. <p>W przypadku ciągu nie zostanie znaleziony, zwraca liczbę -1. </br>Jeśli ciąg jest pusty, zwraca numer 0. | 
|||| 

*Przykład* 

W tym przykładzie wyszukiwanie końcową wartość indeksu podciągu "world" w ciągu "hello world":

```
lastIndexOf('hello world', 'world')
```

I zwraca wynik tego: `10`

<a name="length"></a>

## <a name="length"></a>długość

Zwraca liczbę elementów w kolekcji.

```
length('<collection>')
length([<collection>])
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekcja*> | Yes | Ciąg lub tablica | Kolekcja elementów do liczby | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*długość lub liczby*> | Liczba całkowita | Liczba elementów w kolekcji | 
|||| 

*Przykład*

Te przykłady liczbę elementów w tych kolekcjach: 

```
length('abcd')
length([0, 1, 2, 3])
```

I zwraca wynik tego: `4`

<a name="less"></a>

## <a name="less"></a>mniej

Sprawdź, czy pierwsza wartość jest mniejsza niż wartość drugiej.
Zwróć wartość true, jeśli pierwsza wartość jest mniejsza lub zwróci wartość false, jeśli pierwsza wartość jest więcej.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Liczba całkowita, Float lub ciąg | Pierwsza wartość do sprawdzenia, czy poniżej drugiej wartości | 
| <*Wykonanie funkcji CompareTo*> | Yes | Liczba całkowita, Float lub ciąg, odpowiednio | Element porównania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Zwraca wartość PRAWDA, jeśli pierwsza wartość jest mniejsza od drugiej wartości. Pierwsza wartość jest równa lub większa od drugiej wartości, zwraca wartość false. | 
|||| 

*Przykład*

Te przykłady Sprawdź, czy pierwsza wartość jest mniejsza niż wartość drugiej.

```
less(5, 10)
less('banana', 'apple')
```

I zwrócone następujące wyniki: 

* Pierwszym przykładzie: `true`
* Drugi przykład: `false`

<a name="lessOrEquals"></a>

## <a name="lessorequals"></a>lessOrEquals

Sprawdź, czy pierwsza wartość jest mniejsza niż lub równa drugiej wartości.
Zwróć wartość true, jeśli pierwsza wartość jest mniejsza lub równa lub zwróci wartość false, jeśli pierwsza wartość jest więcej.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Liczba całkowita, Float lub ciąg | Pierwsza wartość, aby sprawdzić, czy mniej niż lub równy drugiej wartości | 
| <*Wykonanie funkcji CompareTo*> | Yes | Liczba całkowita, Float lub ciąg, odpowiednio | Element porównania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ  | Wartość logiczna | Pierwsza wartość jest mniejsza lub równa wartości drugiego, zwrócić wartość true. Pierwsza wartość jest większa niż wartość drugiej, zwróci wartość false. |  
|||| 

*Przykład*

Poniższe przykłady Sprawdź, czy pierwsza wartość jest mniejsza lub równa od drugiej wartości.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

I zwrócone następujące wyniki: 

* Pierwszym przykładzie: `true`
* Drugi przykład: `false`

<a name="listCallbackUrl"></a>

## <a name="listcallbackurl"></a>listCallbackUrl

Zwróć "wywołania zwrotnego adresu URL" wywołuje wyzwalacz lub akcji. Ta funkcja działa tylko w przypadku wyzwalacze i akcje dla **HttpWebhook** i **ApiConnectionWebhook** typy łącznika, ale nie **ręcznego**,  **Cykl**, **HTTP**, i **APIConnection** typów. 

```
listCallbackUrl()
```

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*adres URL wywołania zwrotnego*> | Ciąg | Adres URL wywołania zwrotnego dla wyzwalacza lub akcji |  
|||| 

*Przykład*

Ten przykład przedstawia przykładowy adres URL wywołania zwrotnego, że ta funkcja może zwrócić:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

## <a name="max"></a>maks.

Zwraca największą wartość z listy lub tablicy z numerami, która ma charakter obu końców. 

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Liczba1*>, <*liczba2*>,... | Yes | Liczba całkowita i/lub liczb zmiennoprzecinkowych | Zbiór liczby, z którego ma zostać najwyższą wartość | 
| [<*Liczba1*>, <*liczba2*>,...] | Yes | Array — liczba całkowita i/lub liczb zmiennoprzecinkowych | Tablica liczby, z którego ma zostać najwyższą wartość | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość maksymalna*> | Liczba całkowita lub Float | Największa wartość w określonej tablicy lub zbioru liczb | 
|||| 

*Przykład* 

Te przykłady uzyskać najwyższą wartość z liczb i tablicy:

```
max(1, 2, 3)
max([1, 2, 3])
```

I zwraca wynik tego: `3`

<a name="min"></a>

## <a name="min"></a>min.

Zwraca najmniejszą wartość z zestawu liczb lub tablicy.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Liczba1*>, <*liczba2*>,... | Yes | Liczba całkowita i/lub liczb zmiennoprzecinkowych | Zbiór liczb, z którego mają być najniższy wartość | 
| [<*Liczba1*>, <*liczba2*>,...] | Yes | Array — liczba całkowita i/lub liczb zmiennoprzecinkowych | Tablica liczb, z którego mają być najniższy wartość | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość minimalna*> | Liczba całkowita lub Float | Najniższa wartość w określonym zestawie, liczby lub określonej tablicy | 
|||| 

*Przykład* 

Te przykłady uzyskać najmniejszą wartość w zestawie cyfr i tablicy:

```
min(1, 2, 3)
min([1, 2, 3])
```

I zwraca wynik tego: `1`

<a name="mod"></a>

## <a name="mod"></a>mod

Zwraca resztę z dzielenia liczby dwa. Aby uzyskać wynik liczba całkowita, zobacz [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*dzielna*> | Yes | Liczba całkowita lub Float | Numer do dzielenia przez *dzielnik.* | 
| <*dzielnik.*> | Yes | Liczba całkowita lub Float | Liczba, która dzieli *dzielna*, ale nie może wynosić 0. | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*modulo wyników*> | Liczba całkowita lub Float | Resztę z dzielenia numer pierwszej przez druga liczba | 
|||| 

*Przykład* 

W tym przykładzie dzieli numer pierwszej przez druga liczba:

```
mod(3, 2)
```

I zwraca wynik tego: `1`

<a name="mul"></a>

## <a name="mul"></a>mul

Zwraca produktu z pomnożenie dwóch liczb.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*multiplicand1*> | Yes | Liczba całkowita lub Float | Liczbę Aby pomnożyć przez *multiplicand2* | 
| <*multiplicand2*> | Yes | Liczba całkowita lub Float | Liczba który wielokrotności *multiplicand1* | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wynik produktu*> | Liczba całkowita lub Float | Produktu z pomnożenie numer pierwszej przez druga liczba | 
|||| 

*Przykład* 

Poniższe przykłady wielu pierwsza liczba przez druga liczba:

```
mul(1, 2)
mul(1.5, 2)
```

I zwrócone następujące wyniki:

* Pierwszym przykładzie: `2`
* Drugi przykład `3`

<a name="multipartBody"></a>

## <a name="multipartbody"></a>multipartBody

Zwróć wynik akcji, który ma wiele części treści dla określonej części.

```
multipartBody('<actionName>', <index>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Nazwa akcji*> | Yes | Ciąg | Nazwa akcji, która ma produkt wyjściowy wieloczęściowego | 
| <*Indeks*> | Yes | Liczba całkowita | Wartość indeksu dla części, która ma | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Treści*> | Ciąg | Określona część treści | 
|||| 

<a name="not"></a>

## <a name="not"></a>nie

Sprawdź, czy wyrażenie jest fałszywe. Zwróć wartość true, jeśli wyrażenie ma wartość false lub zwróci wartość false, gdy ma wartość true.

```
not(<expression>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*wyrażenie*> | Yes | Wartość logiczna | Wyrażenie do sprawdzenia | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Zwrócić wartość true, jeśli wyrażenie ma wartość false. Wyrażenie jest prawdziwe, zwróci wartość false. |  
|||| 

*Przykład 1*

Te przykłady Sprawdź, czy określonego wyrażenia mają wartość false: 

```
not(false)
not(true)
```

I zwrócone następujące wyniki:

* Pierwszym przykładzie: wyrażenie jest false, dlatego funkcja zwraca `true`.
* Drugi przykład: wyrażenie ma wartość true, dlatego funkcja zwraca `false`.

*Przykład 2*

Te przykłady Sprawdź, czy określonego wyrażenia mają wartość false: 

```
not(equals(1, 2))
not(equals(1, 1))
```

I zwrócone następujące wyniki:

* Pierwszym przykładzie: wyrażenie jest false, dlatego funkcja zwraca `true`.
* Drugi przykład: wyrażenie ma wartość true, dlatego funkcja zwraca `false`.

<a name="or"></a>

## <a name="or"></a>lub

Sprawdź, czy co najmniej jedno wyrażenie ma wartość true. Zwraca wartość true, jeśli co najmniej jedno wyrażenie ma wartość true lub zwróci wartość false, gdy są wszystkie wartość false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*wyrażenie1*>, <*wyrażenie2*>,... | Yes | Wartość logiczna | Wyrażenia, aby sprawdzić | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ | Wartość logiczna | Zwrócić wartość true, jeśli co najmniej jedno wyrażenie ma wartość true. Wszystkie wyrażenia są false, zwróci wartość false. |  
|||| 

*Przykład 1*

Te przykłady Sprawdź, czy co najmniej jedno wyrażenie jest prawdziwe:

```
or(true, false)
or(false, false)
```

I zwrócone następujące wyniki:

* Pierwszym przykładzie: co najmniej jedno wyrażenie ma wartość true, dlatego funkcja zwraca `true`.
* Drugi przykład: oba wyrażenia są ma wartość FAŁSZ, dlatego funkcja zwraca `false`.

*Przykład 2*

Te przykłady Sprawdź, czy co najmniej jedno wyrażenie jest prawdziwe:

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

I zwrócone następujące wyniki:

* Pierwszym przykładzie: co najmniej jedno wyrażenie ma wartość true, dlatego funkcja zwraca `true`.
* Drugi przykład: oba wyrażenia są ma wartość FAŁSZ, dlatego funkcja zwraca `false`.

<a name="parameters"></a>

## <a name="parameters"></a>parameters

Zwraca wartość dla parametru, który jest opisany w definicji aplikacji logiki. 

```
parameters('<parameterName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Nazwa parametru*> | Yes | Ciąg | Nazwa parametru, którego wartość ma | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość parametru*> | Dowolne | Wartość parametru określonego | 
|||| 

*Przykład* 

Załóżmy, że ta wartość JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

W tym przykładzie pobiera wartość określonego parametru:

```
parameters('fullName')
```

I zwraca wynik tego: `"Sophia Owen"`

<a name="rand"></a>

## <a name="rand"></a>RAND

Zwraca losową liczbę całkowitą z określonego zakresu, która ma charakter tylko po zakończeniu początkowej.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość MinValue*> | Yes | Liczba całkowita | Najmniejszej liczby całkowitej z zakresu | 
| <*MaxValue*> | Yes | Liczba całkowita | Liczba całkowita, która wykonuje największą liczbę całkowitą w zakresie funkcji mogą zwrócić | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*losowe wyników*> | Liczba całkowita | Losowych liczb całkowitych zwrócony z określonego zakresu |  
|||| 

*Przykład*

W tym przykładzie pobiera losową liczbę całkowitą z zakresu określonego, z wyłączeniem wartość maksymalna: 

```
rand(1, 5)
```

I zwraca jedną z tych numerów w wyniku: `1`, `2`, `3`, lub `4` 

<a name="range"></a>

## <a name="range"></a>Zakres

Zwraca tablicę liczba całkowita, która rozpoczyna się od określonej wartości całkowitej.

```
range(<startIndex>, <count>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość startIndex*> | Yes | Liczba całkowita | Wartość całkowita uruchamiany jako pierwszy element tablicy | 
| <*Liczba*> | Yes | Liczba całkowita | Liczby całkowite w tablicy | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| [<*wynik zakresu*>] | Tablica | Z liczby całkowite począwszy od określonego indeksu tablicy |  
|||| 

*Przykład*

W tym przykładzie tworzy tablicę liczba całkowita, która rozpoczyna się od określonego indeksu i o określonej liczbie liczb całkowitych:

```
range(1, 4)
```

I zwraca wynik tego: `[1, 2, 3, 4]`

<a name="replace"></a>

## <a name="replace"></a>Zamień

Zastąp podciągu określonego ciągu i zwraca ciąg wyniku. Ta funkcja jest rozróżniana wielkość liter.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg zawierający podciąg, aby zastąpić | 
| <*oldText*> | Yes | Ciąg | Aby zastąpić podciąg | 
| <*newText*> | Yes | Ciąg | Ciąg zastępczy | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*zaktualizowany tekst*> | Ciąg | Ciąg zaktualizowane po zastąpieniu podciąg <p>Jeśli nie zostanie znaleziony podciąg, zwróć oryginalny ciąg. | 
|||| 

*Przykład* 

W tym przykładzie znalezieniu "starego" substring "stary ciąg" i zastępuje "starego" za pomocą instrukcji "new": 

```
replace('the old string', 'old', 'new')
```

I zwraca wynik tego: `"the new string"`

<a name="removeProperty"></a>

## <a name="removeproperty"></a>removeProperty

Usuń właściwość z obiektu i zwraca zaktualizowany obiekt.

```
removeProperty(<object>, '<property>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | Yes | Obiekt | Obiekt JSON, z której chcesz usunąć właściwość | 
| <*Właściwość*> | Yes | Ciąg | Nazwa właściwości do usunięcia | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano obiekt*> | Obiekt | Zaktualizowany obiekt JSON bez określonej właściwości | 
|||| 

*Przykład*

W tym przykładzie powoduje usunięcie `"accountLocation"` właściwość z `"customerProfile"` obiektu, który jest konwertowana na format JSON z [JSON()](#json) funkcji i zwraca obiekt zaktualizowane:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

## <a name="setproperty"></a>Metoda setProperty

Ustawianie wartości właściwości obiektu i zwraca zaktualizowany obiekt. Aby dodać nową właściwość, należy użyć tej funkcji lub [addProperty()](#addProperty) funkcji.

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | Yes | Obiekt | Obiekt JSON, którego chcesz ustawić właściwość | 
| <*Właściwość*> | Yes | Ciąg | Nazwa dla istniejących lub nowych właściwości do ustawienia | 
| <*Wartość*> | Yes | Dowolne | Wartość do ustawienia dla określonej właściwości |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano obiekt*> | Obiekt | Zaktualizowany obiekt JSON których należy ustawić właściwość | 
|||| 

*Przykład*

W tym przykładzie `"accountNumber"` właściwość `"customerProfile"` obiektu, który jest konwertowana na format JSON z [JSON()](#json) funkcji. Funkcja przypisuje wartość wygenerowaną przez [guid()](#guid) funkcji i zwraca zaktualizowany obiekt JSON:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

## <a name="skip"></a>Pomiń

Usuwanie elementów z na początku kolekcji, a następnie wróć *wszystkie inne* elementów.

```
skip([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekcja*> | Yes | Tablica | Kolekcja elementów, których chcesz usunąć | 
| <*Liczba*> | Yes | Liczba całkowita | Dodatnia liczba całkowita liczba elementów do usunięcia z przodu | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| [<*kolekcji zaktualizowane*>] | Tablica | Zaktualizowano kolekcji po usunięciu określonych elementów | 
|||| 

*Przykład*

W tym przykładzie usuwa jeden element, liczba 0, z przodu określonej tablicy: 

```
skip([0, 1, 2, 3], 1)
```

I zwraca tej tablicy pozostałe elementy: `[1,2,3]`

<a name="split"></a>

## <a name="split"></a>split

Rozdzielone tablicę, która ma wszystkich znaków z ciągu i ma każdy znak powrotu *ogranicznik*.

```
split('<text>', '<separator>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg, który zawiera znaki podziału |  
| <*Separator*> | Yes | Ciąg | Separator między każdego znaku w wynikowej tablicy | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| [<*char1*><*separatora*><*char2*><*separatora*>...] | Tablica | Tablica wynikowa utworzone na podstawie wszystkich elementów w określonym ciągu |
|||| 

*Przykład* 

W tym przykładzie tworzy tablicę określonego ciągu, oddzielając każdy znak przecinka jako ogranicznik:

```
split('abc', ',')
```

I zwraca wynik tego: `[a, b, c]`

<a name="startOfDay"></a>

## <a name="startofday"></a>startOfDay

Zwróć początek dnia dla sygnatury czasowej. 

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Określonej sygnatury czasowej, ale począwszy od zera.-godzinnym oznaczyć do dnia | 
|||| 

*Przykład* 

W tym przykładzie znajduje początek dnia dla sygnatura czasowa:

```
startOfDay('2018-03-15T13:30:30Z')
```

I zwraca wynik tego: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

## <a name="startofhour"></a>startOfHour

Zwróć początek godziny dla sygnatury czasowej. 

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Określonej sygnatury czasowej, ale począwszy od zera.-minutowy oznaczyć godzinę | 
|||| 

*Przykład* 

W tym przykładzie znajduje początek godziny dla sygnatura czasowa:

```
startOfHour('2018-03-15T13:30:30Z')
```

I zwraca wynik tego: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

## <a name="startofmonth"></a>startOfMonth

Zwraca początek miesiąca dla sygnatury czasowej. 

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Określonej sygnatury czasowej, ale uruchomienie na pierwszy dzień miesiąca o godzinie zero znaku | 
|||| 

*Przykład* 

W tym przykładzie zwraca początek miesiąca dla sygnatura czasowa:

```
startOfMonth('2018-03-15T13:30:30Z')
```

I zwraca wynik tego: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

## <a name="startswith"></a>startsWith

Sprawdź, czy ciąg rozpoczyna się od określonego podciąg. Zwraca wartość true, gdy zostanie znaleziony podciąg lub zwróci wartość false, gdy nie można odnaleźć. Ta funkcja nie jest rozróżniana wielkość liter.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg do sprawdzenia | 
| <*Tekstprzeszukiwany*> | Yes | Ciąg | Początkowy ciąg można znaleźć | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| wartość PRAWDA lub FAŁSZ  | Wartość logiczna | Gdy zostanie znaleziony podciąg początkowy zwrócić wartość true. Zwraca false Jeśli nie można odnaleźć. | 
|||| 

*Przykład 1* 

W tym przykładzie sprawdza, czy ciąg "hello world" rozpoczyna się od podciągu "hello":

```
startsWith('hello world', 'hello')
```

I zwraca wynik tego: `true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" rozpoczyna się od podciągu "pozdrowienia":

```
startsWith('hello world', 'greetings')
```

I zwraca wynik tego: `false`

<a name="string"></a>

## <a name="string"></a>ciąg

Zwraca ciąg wersji wartość.

```
string(<value>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Dowolne | Wartość do przekonwertowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość ciągu*> | Ciąg | Wersja ciągu określoną wartość | 
|||| 

*Przykład 1* 

W tym przykładzie jest tworzony dla tego numeru wersji ciąg:

```
string(10)
```

I zwraca wynik tego: `"10"`

*Przykład 2*

W tym przykładzie tworzy ciąg do określonego obiektu JSON i używa ukośnika odwrotnego (\\) jako znaku ucieczki znaku cudzysłowu (").

```
string( { "name": "Sophie Owen" } )
```

I zwraca wynik tego: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

## <a name="sub"></a>Sub

Zwraca wynik z odjęcie druga liczba od pierwszej liczby.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*minuend*> | Yes | Liczba całkowita lub Float | Liczba, od której do odjęcia *subtrahend* | 
| <*subtrahend*> | Yes | Liczba całkowita lub Float | Numer, którą chcesz odjąć od *minuend* | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wynik*> | Liczba całkowita lub Float | Wynik odjęcie druga liczba od pierwszej liczby | 
|||| 

*Przykład* 

W tym przykładzie odejmuje druga liczba od pierwszej liczby:

```
sub(10.3, .3)
```

I zwraca wynik tego: `10`

<a name="substring"></a>

## <a name="substring"></a>substring

Zwracanie znaków z ciągu, zaczynając od określonej pozycji lub indeksu. Początek wartości indeksu o numerze 0. 

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg znaków, których chcesz | 
| <*Wartość startIndex*> | Yes | Liczba całkowita | Liczba dodatnia, dla pozycji początkowej lub wartość indeksu | 
| <*długość*> | Yes | Liczba całkowita | Dodatnią liczbę znaków, które mają w podciągu | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wynik podciągu*> | Ciąg | Substring z określoną liczbę znaków, zaczynając od pozycji indeksu określonego w ciągu ze źródłem | 
|||| 

*Przykład* 

W tym przykładzie jest tworzony podciąg pięć znaków z określonym ciągiem znaków, zaczynając od wartości indeksu 6:

```
substring('hello world', 6, 5)
```

I zwraca wynik tego: `"world"`

<a name="subtractFromTime"></a>

## <a name="subtractfromtime"></a>subtractFromTime

Odjęcie liczby jednostek czasu z sygnatury czasowej. Zobacz też [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg, który zawiera znacznik czasu | 
| <*Interwał*> | Yes | Liczba całkowita | Liczba jednostek określonego czasu do odjęcia | 
| <*timeUnit*> | Yes | Ciąg | Jednostka czasu do użycia z *interwał*: "Drugi", "Minute", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Year" | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Zaktualizowano sygnatury czasowej*> | Ciąg | Sygnatura czasowa pomniejszonej o określoną liczbę jednostek czasu | 
|||| 

*Przykład 1*

W tym przykładzie odejmuje jeden dzień z sygnatura czasowa:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day') 
```

I zwraca wynik tego: `"2018-01-01T00:00:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje jeden dzień z sygnatura czasowa:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D') 
```

I zwraca wynik w opcjonalne formacie "D": `"Monday, January, 1, 2018"`

<a name="take"></a>

## <a name="take"></a>podejmij

Zwraca elementy z przodu kolekcji. 

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekcja*> | Yes | Ciąg lub tablica | Kolekcja elementów, których chcesz | 
| <*Liczba*> | Yes | Liczba całkowita | Dodatnia liczba całkowita liczba elementów, które mają z przodu | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*podzbiór*> lub [<*podzestawu*>] | Ciąg lub tablica, odpowiednio | Ciąg lub tablica, która ma określoną liczbę elementów pobranych z przodu oryginalnej kolekcji | 
|||| 

*Przykład*

Te przykłady Pobierz określoną liczbę elementów z przodu tych kolekcji:

```
take('abcde`, 3)
take([0, 1, 2, 3, 4], 3)
```

I zwrócone następujące wyniki:

* Pierwszym przykładzie: `"abc"`
* Drugi przykład: `[0, 1, 2]`

<a name="ticks"></a>

## <a name="ticks"></a>znaczniki osi

Zwraca `ticks` wartości właściwości dla określonej sygnatury czasowej. A *znaczników* interwał 100-nanosekundowych.

```
ticks('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Znacznik czasu*> | Yes | Ciąg | Ciąg dla sygnatury czasowej | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Liczba znaczników*> | Liczba całkowita | Liczbę taktów od określonej sygnatury czasowej | 
|||| 

<a name="toLower"></a>

## <a name="tolower"></a>toLower

Zwraca ciąg w formacie małe litery. Jeśli znak w ciągu nie ma małych liter, ten znak pozostaje niezmieniona zwracany ciąg.

```
toLower('<text>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg do zwrócenia w formacie małe litery | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*małe tekstu*> | Ciąg | Oryginalny ciąg w formacie małe litery | 
|||| 

*Przykład* 

W tym przykładzie konwertuje ciąg na małe litery: 

```
toLower('Hello World')
```

I zwraca wynik tego: `"hello world"`

<a name="toUpper"></a>

## <a name="toupper"></a>toUpper

Zwraca ciąg w formacie wielkie litery. Jeśli znak w ciągu nie ma wielkimi literami, ten znak pozostaje niezmieniona zwracany ciąg.

```
toUpper('<text>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg do zwrócenia w formacie wielkie litery | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*tekst na wielkie litery*> | Ciąg | Oryginalny ciąg w formacie wielkie litery | 
|||| 

*Przykład* 

W tym przykładzie konwertuje ciąg na wielkie litery:

```
toUpper('Hello World')
```

I zwraca wynik tego: `"HELLO WORLD"`

<a name="trigger"></a>

## <a name="trigger"></a>Wyzwalacz

Zwraca dane wyjściowe tego wyzwalacza w czasie wykonywania lub wartości z innych pary nazwa wartość JSON, które można przypisać do wyrażenia. 

* Wewnątrz elementu trigger wejść funkcja zwraca dane wyjściowe z poprzednie wykonanie. 

* Wewnątrz spełnienie warunku wyzwalacza funkcja zwraca dane wyjściowe z bieżącego wykonywania. 

Domyślnie funkcja odwołuje się do obiektu całego wyzwalacza, ale Opcjonalnie można określić właściwości którego żądaną wartość. Ponadto ta funkcja posiada dostępne wersje skrótu, zobacz [triggerOutputs()](#triggerOutputs) i [triggerBody()](#triggerBody). 

```
trigger()
```

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dane wyjściowe wyzwalacza*> | Ciąg | Dane wyjściowe z wyzwalacza w czasie wykonywania | 
|||| 

<a name="triggerBody"></a>

## <a name="triggerbody"></a>triggerBody

Zwraca wyzwalacza `body` danych wyjściowych w czasie wykonywania. Skrócona forma `trigger().outputs.body`. Zobacz [trigger()](#trigger). 

```
triggerBody()
```

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wyzwalacz treści wyjścia*> | Ciąg | `body` Dane wyjściowe z wyzwalacza | 
|||| 

<a name="triggerFormDataMultiValues"></a>

## <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Zwraca tablicę z wartościami, które odpowiada nazwie klucza w wyzwalacza *dane formularza* lub *postać zakodowanych* danych wyjściowych. 

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Klucz*> | Yes | Ciąg | Nazwa klucza, którego wartość ma | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| [<*tablicy z klucza wartości*>] | Tablica | Tablica nie zawierająca wszystkie wartości, które zgodny z określonym kluczem | 
|||| 

*Przykład* 

W tym przykładzie tworzy tablicę z wartości klucza "feedUrl" w danych formularza wyzwalacz RSS lub wynik zakodowany w postaci: 

```
triggerFormDataMultiValues('feedUrl')
```

I zwraca tej tablicy jako wynik przykładzie: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

## <a name="triggerformdatavalue"></a>triggerFormDataValue

Zwraca ciąg z pojedynczą wartość, który odpowiada nazwie klucza w wyzwalacza *dane formularza* lub *postać zakodowanych* danych wyjściowych. Jeśli funkcja znajdzie więcej niż jedno dopasowanie, funkcja zwraca błąd.

```
triggerFormDataValue('<key>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Klucz*> | Yes | Ciąg | Nazwa klucza, którego wartość ma |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*klucz wartość*> | Ciąg | Wartość w określonym kluczu | 
|||| 

*Przykład* 

W tym przykładzie tworzy ciąg z wartości klucza "feedUrl" w danych formularza wyzwalacz RSS lub wynik zakodowany w postaci:

```
triggerFormDataValue('feedUrl')
```

I zwraca tego ciągu jako wynik przykładzie: `"http://feeds.reuters.com/reuters/topNews"` 

<a name="triggerMultipartBody"></a>

Zwróć treści dla określonej części w danych wyjściowych wyzwalacz, który ma wiele części. 

```
triggerMultipartBody(<index>)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Indeks*> | Yes | Liczba całkowita | Wartość indeksu dla części, która ma |
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Treści*> | Ciąg | Treść określona część wieloczęściowych danych wyjściowych tego wyzwalacza | 
|||| 

<a name="triggerOutputs"></a>

## <a name="triggeroutputs"></a>triggerOutputs

Zwraca dane wyjściowe tego wyzwalacza w czasie wykonywania lub wartości z innych pary nazwa wartość JSON. Skrócona forma `trigger().outputs`. Zobacz [trigger()](#trigger). 

```
triggerOutputs()
```

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dane wyjściowe wyzwalacza*> | Ciąg | Dane wyjściowe z wyzwalacza w czasie wykonywania  | 
|||| 

<a name="trim"></a>

## <a name="trim"></a>TRIM

Usuń spacje wiodące i końcowe z ciągu i zwraca ciąg zaktualizowane.

```
trim('<text>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Tekst*> | Yes | Ciąg | Ciąg, który ma odstępy wiodące i końcowe do usunięcia | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*updatedText*> | Ciąg | Zaktualizowana wersja dla oryginalnego ciągu bez spacji wiodących lub końcowych | 
|||| 

*Przykład* 

W tym przykładzie Usuwa odstępy wiodące i końcowe z ciągu "Hello World":  

```
trim(' Hello World  ')
```

I zwraca wynik tego: `"Hello World"`

<a name="union"></a>

## <a name="union"></a>Unii

Zwraca kolekcję, która ma *wszystkie* elementy z określonymi kolekcjami. Pojawią się w wyniku, element może występować w żadnej kolekcji przekazane do tej funkcji. Jeśli jeden lub więcej elementów mają taką samą nazwę, ostatni element o takiej nazwie zostanie wyświetlony w wyniku. 

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>,...  | Yes | Tablica lub obiekt, ale nie oba na raz | Kolekcje, z którym chcesz *wszystkie* elementy | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*updatedCollection*> | Tablica lub obiekt, odpowiednio | Kolekcja wszystkich elementów z określonymi kolekcjami — bez duplikatów | 
|||| 

*Przykład* 

W tym przykładzie pobiera *wszystkie* elementy z tych kolekcji: 

```
union([1, 2, 3], [1, 2, 10, 101])
```

I zwraca wynik tego: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

## <a name="uricomponent"></a>uriComponent

Zwraca wersję uniform resource identifier (URI) zakodowany ciągu przez zamianę znaków niezabezpieczony adres URL znaki specjalne. Aby użyć tej funkcji zamiast [encodeUriComponent()](#encodeUriComponent). Mimo że obie funkcje działają tak samo, `uriComponent()` jest preferowana.

```
uriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg do przekonwertowania na format kodowany w formacie identyfikatora URI | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*zakodowany identyfikator uri*> | Ciąg | Ciąg kodowany w formacie identyfikatora URI z znaki specjalne | 
|||| 

*Przykład*

W tym przykładzie tworzy wersję kodowany w formacie identyfikatora URI dla tych parametrów:

```
uriComponent('https://contoso.com')
```

I zwraca wynik tego: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

## <a name="uricomponenttobinary"></a>uriComponentToBinary

Zwróć binarna wersja składnika uniform resource identifier (URI).

```
uriComponentToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg kodowany w formacie identyfikatora URI do przekonwertowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dane binarne dla — zakodowane uri*> | Ciąg | Binarna wersja na ciąg kodowany w formacie identyfikatora URI. Zawartość binarna jest zakodowany w formacie base64 i reprezentowany przez `$content`. | 
|||| 

*Przykład*

W tym przykładzie jest tworzony binarnej wersji dla tego ciąg kodowany w formacie identyfikatora URI: 

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

I zwraca wynik tego: 

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

## <a name="uricomponenttostring"></a>uriComponentToString

Zwraca ciąg, efektywnie dekodowania ciąg kodowany w formacie identyfikatora URI zakodowana ciąg wersji identyfikator uniform resource identifier (URI).

```
uriComponentToString('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg kodowany w formacie identyfikatora URI do zdekodowania | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*dekodowany uri*> | Ciąg | Wersja dekodowane ciąg kodowany w formacie identyfikatora URI | 
|||| 

*Przykład*

W tym przykładzie jest tworzony Dekodowany ciąg wersji ten ciąg kodowany w formacie identyfikatora URI: 

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

I zwraca wynik tego: `"https://contoso.com"` 

<a name="uriHost"></a>

## <a name="urihost"></a>uriHost

Zwraca `host` wartość Identyfikator uniform resource identifier (URI).

```
uriHost('<uri>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Identyfikator URI*> | Yes | Ciąg | Identyfikator URI którego `host` ma wartość | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość hosta*> | Ciąg | `host` Wartość dla określonego identyfikatora URI. | 
|||| 

*Przykład*

Znajduje się w tym przykładzie `host` wartość dla tego identyfikatora URI: 

```
uriHost('https://www.localhost.com:8080')
```

I zwraca wynik tego: `"www.localhost.com"`

<a name="uriPath"></a>

## <a name="uripath"></a>uriPath

Zwraca `path` wartość Identyfikator uniform resource identifier (URI). 

```
uriPath('<uri>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Identyfikator URI*> | Yes | Ciąg | Identyfikator URI którego `path` ma wartość | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość ścieżki*> | Ciąg | `path` Wartość dla określonego identyfikatora URI. Jeśli `path` nie ma wartości, zwraca znak "/". | 
|||| 

*Przykład*

Znajduje się w tym przykładzie `path` wartość dla tego identyfikatora URI: 

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca wynik tego: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

## <a name="uripathandquery"></a>uriPathAndQuery

Zwraca `path` i `query` wartości Identyfikator uniform resource identifier (URI).

```
uriPathAndQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Identyfikator URI*> | Yes | Ciąg | Identyfikator URI których `path` i `query` wartości | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość ścieżki — zapytania*> | Ciąg | `path` i `query` wartości dla określonego identyfikatora URI. Jeśli `path` nie określić wartości, zwraca znak "/". | 
|||| 

*Przykład*

Znajduje się w tym przykładzie `path` i `query` wartości dla tego identyfikatora URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca wynik tego: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

## <a name="uriport"></a>uriPort

Zwraca `port` wartość Identyfikator uniform resource identifier (URI).

```
uriPort('<uri>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Identyfikator URI*> | Yes | Ciąg | Identyfikator URI którego `port` ma wartość | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość portu*> | Liczba całkowita | `port` Wartość dla określonego identyfikatora URI. Jeśli `port` nie Określ wartość, aby uzyskać domyślnego portu dla protokołu. | 
|||| 

*Przykład*

W tym przykładzie zwraca `port` wartość dla tego identyfikatora URI:

```
uriPort('http://www.localhost:8080')
```

I zwraca wynik tego: `8080`

<a name="uriQuery"></a>

## <a name="uriquery"></a>Właściwość uriQuery

Zwraca `query` wartość Identyfikator uniform resource identifier (URI).

```
uriQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Identyfikator URI*> | Yes | Ciąg | Identyfikator URI którego `query` ma wartość | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość zapytania*> | Ciąg | `query` Wartość dla określonego identyfikatora URI. | 
|||| 

*Przykład*

W tym przykładzie zwraca `query` wartość dla tego identyfikatora URI: 

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca wynik tego: `"?date=today"`

<a name="uriScheme"></a>

## <a name="urischeme"></a>UriScheme

Zwraca `scheme` wartość Identyfikator uniform resource identifier (URI).

```
uriScheme('<uri>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Identyfikator URI*> | Yes | Ciąg | Identyfikator URI którego `scheme` ma wartość | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość schemat*> | Ciąg | `scheme` Wartość dla określonego identyfikatora URI. | 
|||| 

*Przykład*

W tym przykładzie zwraca `scheme` wartość dla tego identyfikatora URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca wynik tego: `"http"`

<a name="utcNow"></a>

## <a name="utcnow"></a>utcNow

Zwraca bieżącą sygnaturę czasową. 

```
utcNow('<format>')
```

Opcjonalnie można określić inny format z <*format*> parametru. 


| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Format*> | Nie | Ciąg | Albo [specyfikator formatu pojedynczego](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-ddT:mm:ss:fffffffK), która spełnia [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*sygnatury czasowej bieżącego*> | Ciąg | Bieżąca data i godzina | 
|||| 

*Przykład 1*

Załóżmy, że obecnie wynosi 15 kwietnia 2018 1:00:00 PM. W tym przykładzie pobiera bieżącą sygnaturę czasową: 

```
utcNow()
```

I zwraca wynik tego: `"2018-04-15T13:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że obecnie wynosi 15 kwietnia 2018 1:00:00 PM. W tym przykładzie pobiera bieżącą sygnaturę czasową formacie opcjonalne "D":

```
utcNow('D')
```

I zwraca wynik tego: `"Sunday, April 15, 2018"`

<a name="variables"></a>

## <a name="variables"></a>zmienne

Zwraca wartość określonej zmiennej. 

```
variables('<variableName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*nazwa_zmiennej*> | Yes | Ciąg | Nazwa zmiennej, którego wartość ma | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*wartość zmiennej*> | Dowolne | Wartość dla określonej zmiennej | 
|||| 

*Przykład*

Załóżmy, że bieżąca wartość dla zmiennej "numItems" wynosi 20. W tym przykładzie pobiera wartość całkowitą dla tej zmiennej:

```
variables('numItems')
```

I zwraca wynik tego: `20`

<a name="workflow"></a>

## <a name="workflow"></a>przepływ pracy

Zwróć wszystkie szczegółowe informacje o sam przepływ pracy w czasie wykonywania. 

```
workflow().<property>
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Właściwość*> | Nie | Ciąg | Nazwa dla właściwości przepływu pracy, którego wartość ma <p>Obiekt przepływ pracy ma następujące właściwości: **nazwa**, **typu**, **identyfikator**, **lokalizacji**, i **Uruchom**. **Uruchom** wartość właściwości jest również obiektu, który ma następujące właściwości: **nazwa**, **typu**, i **identyfikator**. | 
||||| 

*Przykład*

W tym przykładzie zwraca nazwę dla bieżące uruchomienie przepływu pracy:

```
workflow().run.name
```

<a name="xml"></a>

## <a name="xml"></a>xml

Zwraca ciąg, który zawiera obiekt JSON w wersji XML. 

```
xml('<value>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*Wartość*> | Yes | Ciąg | Ciąg z obiektu JSON do przekonwertowania <p>Obiekt JSON musi mieć właściwość tylko jeden katalog główny. <br>Należy używać znaku ukośnika odwrotnego (\\) jako znak ucieczki dla podwójny cudzysłów ("). | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Wersja pliku XML*> | Obiekt | Zakodowany XML określony ciąg lub obiekt JSON | 
|||| 

*Przykład 1*

W tym przykładzie jest tworzony w wersji XML dla tych parametrów, który zawiera obiekt JSON: 

`xml( '{ \"name\": \"Sophia Owen\" }' )`

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

W tym przykładzie tworzy XML dla ciąg, który zawiera ten obiekt JSON:

`xml( '{ \"person\": { \"name\": \"Sophia Owen\", \"city\": \"Seattle\" } }' )`

I zwraca wynik XML: 

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

## <a name="xpath"></a>wyrażenie XPath

Sprawdź, czy XML węzły lub wartości zgodne wyrażenie XPath (XML Path Language) i zwracają pasującego węzłów lub wartości. Wyrażenie XPath, lub po prostu "XPath", pozwala przejść strukturę dokumentu XML, dzięki czemu można wybrać węzły lub wartości obliczeń w zawartości XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*XML*> | Yes | Dowolne | Ciąg XML, aby wyszukać węzłów lub wartości, które odpowiadają wartość wyrażenia XPath | 
| <*wyrażenie XPath*> | Yes | Dowolne | Wyrażenie XPath używanych do znalezienia dopasowania węzłów XML lub wartości | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*węzeł XML*> | XML | Węzeł XML, gdy tylko jeden węzeł odpowiada określonemu wyrażeniu XPath | 
| <*Wartość*> | Dowolne | Wartość z węzła XML, gdy tylko jedną wartość odpowiada określonemu wyrażeniu XPath | 
| [<*xml node1*>, <*xml node2*>,...] </br>— lub — </br>[<*wartość1*>, <*wartość2*>,...] | Tablica | Tablica z węzłów XML lub wartości, które odpowiadają na określone wyrażenie XPath | 
|||| 

*Przykład 1*

W tym przykładzie znajduje węzły, które odpowiadają `<name></name>` węzła do określonych argumentów i zwraca tablicę z tych wartości węzła: 

`xpath(xml(parameters('items')), '/produce/item/name')`

Oto argumenty:

* Ciąg "elementy", który zawiera plik XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  W przykładzie użyto [parameters()](#parameters) funkcji można pobrać ciągu XML w argumencie "elementy", ale również należy przekonwertować ciągu na XML format przy użyciu [xml()](#xml) funkcji. 

* To wyrażenie XPath, który jest przekazywany jako ciąg:

  `"/produce/item/name"`

W tym miejscu jest tablica wynikowa węzłów, które odpowiadają `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Przykład 2*

Po, na przykład 1, w tym przykładzie znajduje węzły, które odpowiadają `<count></count>` węzła i dodaje te wartości węzła z `sum()` funkcji:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

I zwraca wynik tego: `30`

*Przykład 3*

Na przykład oba wyrażenia znaleźć węzły, które odpowiadają `<location></location>` węzła do określonych argumentów, które obejmują XML z przestrzenią nazw. Wyrażenia użyć znaku ukośnika odwrotnego (\\) jako znak ucieczki dla podwójny cudzysłów (").

* *Wyrażenie 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Wyrażenia 2* 

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

Oto argumenty:

* Plik XML, w tym przestrzeń nazw dokumentu XML, `xmlns="http://contoso.com"`: 

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Albo wyrażenia XPath w tym miejscu:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]`

W tym miejscu jest węzeł wynik, który odpowiada `<location></location` węzła:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Przykład 4*

Po, na przykład 3, w tym przykładzie znajduje wartość w `<location></location>` węzła: 

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

I zwraca wynik tego: `"Paris"`

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md)
