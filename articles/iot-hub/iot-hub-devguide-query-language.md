---
title: Zrozumienie języka zapytań usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — opis usługi IoT Hub podobnego do SQL zapytania język używany do pobierania informacji o urządzeniu/modułu bliźniaczych reprezentacji i zadań z usługi IoT hub.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: rezas
ms.openlocfilehash: e5387f1e44a55b0a30f8620b49d237ac1e1ec2b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442141"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń i modułów, zadań i routingu wiadomości

IoT Hub udostępnia zaawansowane podobnego do SQL języka można pobrać informacji dotyczących [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md) i [zadania](iot-hub-devguide-jobs.md), i [routing komunikatów](iot-hub-devguide-messages-d2c.md). Ten artykuł przedstawia:

* Wprowadzenie do najważniejszych funkcji języka zapytań usługi IoT Hub, a
* Szczegółowy opis języka. Aby uzyskać szczegółowe informacje dotyczące języka zapytania do rozsyłania wiadomości, zobacz [zapytania w routingu komunikatów](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Zapytania dotyczące bliźniaczych reprezentacji urządzeń i modułu

[Bliźniacze reprezentacje urządzeń](iot-hub-devguide-device-twins.md) i bliźniaczych reprezentacjach modułów mogą zawierać dowolne obiekty JSON jako tagów i właściwości. Usługa IoT Hub umożliwia zapytań bliźniaczych reprezentacji urządzeń i bliźniaczych reprezentacjach modułów jako pojedynczy dokument JSON zawierający wszystkie informacje bliźniaczej reprezentacji.

Załóżmy na przykład, że bliźniaczych reprezentacji urządzeń usługi IoT hub mają następującą strukturę (bliźniaczą reprezentację modułu mogą być podobne tylko przy użyciu dodatkowych moduleId):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Zapytania dotyczące bliźniaczych reprezentacji urządzeń

Usługa IoT Hub udostępnia bliźniaczych reprezentacji urządzeń jako kolekcji dokumentów o nazwie **urządzeń**. Na przykład następujące zapytanie pobiera cały zestaw bliźniaczych reprezentacji urządzeń:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Usługa Azure IoT SDKs](iot-hub-devguide-sdks.md) Obsługuj stronicowanie dużych wyników.

Usługa IoT Hub umożliwia pobieranie bliźniaczych reprezentacji urządzeń filtrowanie przy użyciu dowolnego warunków. Na przykład do otrzymywania urządzenia bliźniaczych reprezentacji gdzie **location.region** ustawiony jest tag **USA** Użyj następującego zapytania:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Operatory logiczne i porównań arytmetycznej są również obsługiwane. Na przykład aby pobrać bliźniaczych reprezentacji urządzeń znajdujących się w Stanach Zjednoczonych i skonfigurowane do wysyłania telemetrii za mniej niż minutę, użyj następującego zapytania:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Dla wygody, jest również możliwość użycia tablicowych o **w** i **nW** operatorów (nie w). Na przykład można pobrać bliźniaczych reprezentacji urządzeń podlegających łączności przewodowej lub Wi-Fi, użyj następującego zapytania:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Często jest niezbędne do identyfikowania wszystkich bliźniaków urządzeń, które zawierają określoną właściwość. Usługa IoT Hub obsługuje funkcję `is_defined()` do tego celu. Na przykład aby bliźniaczych reprezentacji urządzeń pobierania, które definiują `connectivity` właściwości użyj następującego zapytania:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Zapoznaj się [klauzuli WHERE](iot-hub-devguide-query-language.md#where-clause) sekcji pełną dokumentację funkcji filtrowania.

Grupowanie i agregacje są również obsługiwane. Na przykład aby znaleźć liczby urządzeń w każdy stan konfiguracji telemetrii, użyj następującego zapytania:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

To grupowanie zapytanie zwróci wynik podobny do poniższego przykładu:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

W tym przykładzie trzech urządzeń. raportowane Konfiguracja zakończyła się pomyślnie, nadal są dwa stosowania konfiguracji i jedną zgłosił błąd.

Kwerend projekcji umożliwiają deweloperom wróć do właściwości, które ich interesują. Na przykład można pobrać ostatniego działania wszystkich odłączone urządzenia, użyj następującego zapytania:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Zapytania dotyczące bliźniaczych reprezentacji modułu

Zapytań o bliźniaczych reprezentacjach modułów jest podobne do zapytań o bliźniaczych reprezentacji urządzeń, ale przy użyciu innej kolekcji/przestrzeni nazw, czyli zamiast "z urządzenia" można tworzyć zapytania device.modules:

```sql
SELECT * FROM devices.modules
```

Firma Microsoft nie zezwalaj na sprzężenie między urządzeniami i devices.modules kolekcji. Jeśli chcesz bliźniaczych reprezentacjach modułów zapytań między urządzeniami, możesz zrobić go na podstawie tagów. To zapytanie będzie zwracać wszystkie bliźniaczych reprezentacjach modułów dla wszystkich urządzeń ze stanem skanowania:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

To zapytanie będzie zwracać wszystkie bliźniaczych reprezentacjach modułów ze stanem skanowania, ale tylko na określony podzbiór urządzeń:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>Przykład w języku C#

Funkcjonalność zapytań jest uwidaczniany przez [zestawu SDK usługi C#](iot-hub-devguide-sdks.md) w **RegistryManager** klasy.

Oto przykład prostego zapytania:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

**Zapytania** o rozmiarze strony (maksymalnie 100) jest tworzone wystąpienie obiektu. Następnie wiele stron są pobierane przez wywołanie metody **GetNextAsTwinAsync** metody wiele razy.

Obiekt zapytania udostępnia wiele **dalej** wartości w zależności od opcji deserializacji wymagane przez zapytanie. Na przykład obiekty zadanie lub bliźniaczych reprezentacji urządzeń lub zwykły JSON, korzystając z projekcji.

### <a name="nodejs-example"></a>Przykład platformy node.js

Funkcjonalność zapytań jest uwidaczniany przez [zestawu SDK usługi Azure IoT dla środowiska Node.js](iot-hub-devguide-sdks.md) w **rejestru** obiektu.

Oto przykład prostego zapytania:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

**Zapytania** o rozmiarze strony (maksymalnie 100) jest tworzone wystąpienie obiektu. Następnie wiele stron są pobierane przez wywołanie metody **nextAsTwin** metoda wiele razy.

Obiekt zapytania udostępnia wiele **dalej** wartości w zależności od opcji deserializacji wymagane przez zapytanie. Na przykład obiekty zadanie lub bliźniaczych reprezentacji urządzeń lub zwykły JSON, korzystając z projekcji.

### <a name="limitations"></a>Ograniczenia

> [!IMPORTANT]
> Wyniki zapytania może mieć tylko kilka minut opóźnienia w odniesieniu do najnowszych wartości bliźniaczych reprezentacji urządzeń. Jeśli podczas badania indywidualnych bliźniacze reprezentacje urządzeń za pomocą Identyfikatora, należy użyć pobierania bliźniaczej reprezentacji urządzenia z interfejsu API. Ten interfejs API zawsze zawiera najnowsze wartości i ma wyższy limity ograniczania przepływności.

Obecnie porównania są obsługiwane tylko między typami pierwotnymi (nie obiektów), na przykład `... WHERE properties.desired.config = properties.reported.config` jest obsługiwana tylko wtedy, gdy te właściwości mają wartości pierwotnych.

## <a name="get-started-with-jobs-queries"></a>Wprowadzenie do zapytań zadania

[Zadania](iot-hub-devguide-jobs.md) umożliwiają wykonywanie operacji na zestawach urządzeń. Każdy bliźniaczej reprezentacji urządzenia zawiera informacje o wszystkich zadań, które jest częścią kolekcji o nazwie **zadań**.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Obecnie ta kolekcja jest odpytywalny jako **devices.jobs** języka zapytań usługi IoT Hub.

> [!IMPORTANT]
> Obecnie właściwości zadania nigdy nie jest zwracana podczas wykonywania zapytań dotyczących bliźniaczych reprezentacji urządzeń. Oznacza to, że te zapytania, które zawierają "z urządzenia". Właściwości zadania może zostać oceniony jedynie bezpośrednio z zapytania przy użyciu `FROM devices.jobs`.
>
>

Na przykład aby uzyskać wszystkie zadania (ostatnie i zaplanowane), które wpływają na jednym urządzeniu, można użyć następującego zapytania:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Należy pamiętać o tym, jak to zapytanie zawiera stan specyficznych dla urządzenia (i ewentualnie odpowiedzi metody bezpośredniej) każde zadanie zwracane.

Użytkownik może również filtrować za pomocą dowolnego warunków logicznych na temat wszystkich właściwości obiektu w **devices.jobs** kolekcji.

Na przykład aby pobrać wszystkie ukończone urządzenia bliźniaczej reprezentacji zadania aktualizacji, które zostały utworzone od września 2016 roku dla określonego urządzenia, użyj następującego zapytania:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Możesz również pobrać wyniki poszczególnych urządzeń pojedynczego zadania.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Ograniczenia

Obecnie zapytanie na **devices.jobs** nie obsługują:

* Projekcji, dlatego tylko `SELECT *` jest możliwe.
* Warunki, które odwołują się do bliźniaczej reprezentacji urządzenia, oprócz właściwości zadania (zobacz poprzednią sekcję).
* Wykonuje agregacje, takie jak liczba, avg, Grupuj według.

## <a name="basics-of-an-iot-hub-query"></a>Podstawowe informacje dotyczące zapytań usługi IoT Hub

Składa się każdego zapytania usługi IoT Hub, wybierz opcję i z klauzul, w której opcjonalne oraz klauzule GROUP BY. Każdego zapytania jest uruchamiane na kolekcji dokumentów JSON, na przykład bliźniaczych reprezentacji urządzeń. Klauzula FROM wskazuje kolekcji dokumentów, należy powtórzyć w (**urządzeń** lub **devices.jobs**). Następnie jest stosowany filtr w klauzuli WHERE. Za pomocą agregacji są pogrupowane wyniki tego kroku określone w klauzuli GROUP BY. Dla każdej grupy jest generowany wiersz jak określono w klauzuli SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM — klauzula

**z < from_specification >** klauzuli może przyjmować tylko dwie wartości: **Z urządzeń** do zapytań bliźniaczych reprezentacji urządzeń lub **z devices.jobs** do szczegółów poszczególnych urządzeń zadania kwerendy.


## <a name="where-clause"></a>Klauzula WHERE
**Gdzie < filter_condition >** klauzula jest opcjonalne. Określa ona, że co najmniej jeden warunek, że za pomocą pliku JSON dokumenty w kolekcji FROM musi spełniać być dołączane jako część wyniku. Dowolny dokument JSON musi być określone warunki "true", mają zostać uwzględnione w wynikach.

Dozwolone warunki są opisane w sekcji [wyrażeń i warunków](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>Klauzula SELECT

**Wybierz < select_list >** jest obowiązkowy i określa, jakie wartości są pobierane z zapytania. Określa wartości JSON, które będą używane do generowania nowych obiektów JSON.
Dla każdego elementu filtrowane (i opcjonalnie pogrupowanych) podzbiorem kolekcji od fazy projekcji generuje nowy obiekt JSON. Ten obiekt jest konstruowany przy użyciu wartości określone w klauzuli SELECT.

Poniżej przedstawiono gramatyki w klauzuli SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** odwołuje się do żadnej właściwości dokumentu JSON w kolekcji FROM. Kilka przykładów klauzule SELECT można znaleźć w Rozpoczynanie pracy z usługą sekcja zapytań bliźniaczych reprezentacji urządzeń.

Obecnie wybór klauzule różni się od **wybierz*** są obsługiwane tylko w agregacji zapytań dotyczących bliźniaczych reprezentacji urządzeń.

## <a name="group-by-clause"></a>Klauzula GROUP BY
**GROUP BY < group_specification >** klauzula jest opcjonalny krok, który jest wykonywany po określony w klauzuli WHERE i przed projekcji określonej we właściwości w oknie Wybierz filtr. Grup dokumentów na podstawie wartości atrybutu. Te grupy są używane do generowania wartości zagregowane, jak to określono w klauzuli SELECT.

Przykładem zapytania za pomocą GROUP BY jest:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Posiadanie składnia GROUP BY jest następująca:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** odwołuje się do żadnej właściwości dokumentu JSON w kolekcji FROM.

Obecnie w klauzuli GROUP BY jest obsługiwana tylko podczas wykonywania zapytań dotyczących bliźniaczych reprezentacji urządzeń.

> [!IMPORTANT]
> Termin `group` aktualnie jest traktowane jako specjalne — słowo kluczowe w zapytaniach. W przypadku używasz `group` jako nazwę właściwości, należy wziąć pod uwagę ujęta w podwójne nawiasy, aby uniknąć błędów, np. `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`.
>
>

## <a name="expressions-and-conditions"></a>Wyrażeń i warunków
Na wysokim poziomie *wyrażenie*:

* Daje w wyniku wystąpienia typu JSON (np. atrybut typu wartość logiczna, liczba, ciąg, tablicy lub obiektu).
* Jest definicją manipulowanie danymi pochodzące z dokumentu JSON urządzenia i stałe, za pomocą wbudowanych operatorów i funkcji.

*Warunki* są wyrażeniami, które dają wartość logiczną. Dowolna inna niż atrybut typu wartość logiczna stała **true** jest traktowany jako **false**. Ta reguła zawiera **null**, **niezdefiniowane**, dowolnego wystąpienia obiektu lub tablicy i dowolny ciąg, wartość logiczna **false**.

Składnia wyrażeń jest następująca:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Aby zrozumieć, co oznacza każdy symbol w składni wyrażenia, zapoznaj się z poniższą tabelą:

| Symbol | Definicja |
| --- | --- |
| attribute_name | Dokument JSON w dowolnej właściwości **FROM** kolekcji. |
| binary_operator | Wszelkie operatora binarnego na liście [operatory](#operators) sekcji. |
| nazwa_funkcji| Dowolnej funkcji, na liście [funkcje](#functions) sekcji. |
| decimal_literal |Liczba zmiennoprzecinkowa wyrażona w notacji dziesiętnej. |
| hexadecimal_literal |Liczba wyrażona przez ciąg "0 x" następuje ciąg cyfr szesnastkowych. |
| string_literaL |Literały ciągów są reprezentowane przez Sekwencja zero lub więcej znaków Unicode lub sekwencji unikowych ciągów znaków Unicode. Literały ciągów są ujęte w apostrofy lub podwójnych cudzysłowów. Dozwolone sekwencje ucieczki: `\'`, `\"`, `\\`, `\uXXXX` dla znaków Unicode, zdefiniowane przez 4 cyfr szesnastkowych. |

### <a name="operators"></a>Operatory
Obsługiwane są następujące operatory:

| Rodzina | Operatory |
| --- | --- |
| Arytmetyczny |+, -, *, /, % |
| Logiczny |AND, OR, NOT |
| Porównanie |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funkcje
Podczas wykonywania zapytań dotyczących bliźniaczych reprezentacji i zadań, które obsługuje tylko funkcja jest następująca:

| Funkcja | Opis |
| -------- | ----------- |
| IS_DEFINED(Property) | Zwraca wartość Boolean wskazującą, jeśli właściwość zostanie przypisana wartość (w tym `null`). |

Obsługiwane są następujące funkcje matematyczne w warunkach trasy:

| Funkcja | Opis |
| -------- | ----------- |
| ABS(x) | Zwraca wartość bezwzględną (dodatnią) podanego wyrażenia liczbowego. |
| EXP(x) | Zwraca wartość określonego wyrażenia liczbowego (e ^ x). |
| Power(x,y) | Zwraca wartość z określonego wyrażenia do wskazanej potęgi (x ^ y).|
| SQUARE(x) | Zwraca kwadrat określoną wartość liczbową. |
| CEILING(x) | Zwraca najmniejszą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub większą. |
| FLOOR(x) | Zwraca największą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub mniejszą. |
| SIGN(x) | Zwraca wynik dodatni (+ 1), wartość zero (0) lub minus (-1) z określonego wyrażenia liczbowego.|
| SQRT(x) | Zwraca pierwiastek kwadratowy z określoną wartość liczbową. |

W warunkach trasy następujące sprawdzania typu i rzutowania funkcje są obsługiwane:

| Funkcja | Opis |
| -------- | ----------- |
| AS_NUMBER | Konwertuje ciąg wejściowy na liczbę. `noop` Jeśli wartość wejściowa jest numerem; `Undefined` Jeśli ciąg nie reprezentuje liczbę.|
| IS_ARRAY | Zwraca wartość logiczną wskazującą, czy Tablica typu z określonego wyrażenia. |
| IS_BOOL | Zwraca wartość logiczną wskazującą, jeśli typ określonego wyrażenie jest wartością logiczną. |
| IS_DEFINED | Zwraca wartość logiczną wskazującą, do właściwości przypisano wartość. |
| IS_NULL | Zwraca wartość logiczną wskazującą, jeśli typ określonego wyrażenie ma wartość null. |
| IS_NUMBER | Zwraca wartość logiczną wskazującą, jeżeli typ podanego wyrażenia jest liczbą. |
| IS_OBJECT | Zwraca wartość logiczną wskazującą, czy obiekt JSON typu z określonego wyrażenia. |
| IS_PRIMITIVE | Zwraca wartość logiczną wskazującą, czy podstawowy typu z określonego wyrażenia (string, Boolean, numeryczne, lub `null`). |
| IS_STRING | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia ciągu. |

Obsługiwane są następujące funkcje ciągów w warunkach trasy:

| Funkcja | Opis |
| -------- | ----------- |
| CONCAT (x, y,...) | Zwraca ciąg, który jest wynikiem połączenia co najmniej dwóch wartości ciągu. |
| LENGTH(x) | Zwraca liczbę znaków z określonego wyrażenia ciągu.|
| LOWER(x) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery. |
| UPPER(x) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych małych liter na wielkie litery. |
| SUBSTRING (string, start [, długość]) | Zwraca część wyrażenia ciągu, zaczynając od pozycji liczony od zera określony znak i kontynuuje do określonej długości lub do końca ciągu. |
| INDEX_OF (string, fragment) | Zwraca pozycję początkową pierwszego wystąpienia drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli ciąg nie zostanie znaleziony.|
| STARTS_WITH (x, y) | Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia rozpoczyna się od drugiego. |
| ENDS_WITH (x, y) | Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia kończy się na drugi. |
| CONTAINS(x,y) | Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie. |

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wykonywać zapytania w aplikacjach przy użyciu [Azure IoT SDKs](iot-hub-devguide-sdks.md).
