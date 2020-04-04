---
title: Opis języka zapytań usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — opis języka zapytań usługi IoT hub podobny do języka SQL, używany do pobierania informacji o bliźniaczych urządzeń/modułach i zadaniach z centrum IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.openlocfilehash: bcc53322ac6942b52853be561bc3441e23fbf53b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632926"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Język zapytań usługi IoT Hub dla urządzeń i bliźniaczych reprezentacji modułów, zadań i routingu komunikatów

Usługa IoT Hub udostępnia zaawansowany język podobny do języka SQL, który umożliwia pobieranie informacji dotyczących [bliźniąt bliźniąt urządzeń,](iot-hub-devguide-device-twins.md) [bliźniaczych modułów,](iot-hub-devguide-module-twins.md) [zadań](iot-hub-devguide-jobs.md)i [routingu wiadomości.](iot-hub-devguide-messages-d2c.md) Ten artykuł przedstawia:

* Wprowadzenie do głównych funkcji języka zapytań usługi IoT Hub oraz
* Szczegółowy opis języka. Aby uzyskać szczegółowe informacje na temat języka zapytań dotyczących routingu wiadomości, zobacz [kwerendy w routingu wiadomości](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Zapytania bliźniacze dwie urządzenia i modułu

[Bliźniacze reprezentacje urządzeń](iot-hub-devguide-device-twins.md) i [bliźniaczych reprezentacji modułów](iot-hub-devguide-module-twins.md) mogą zawierać dowolne obiekty JSON zarówno jako znaczniki, jak i właściwości. Usługa IoT Hub umożliwia wykonywanie zapytań o bliźniacze urządzenia i bliźniacze reprezentacje modułów jako pojedynczy dokument JSON zawierający wszystkie informacje bliźniaczej reprezentacji.

Załóżmy na przykład, że bliźniacze urządzenia ioT hub mają następującą strukturę (bliźniacze moduły będą podobne tylko z dodatkowym identyfikatorem moduleId):

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

### <a name="device-twin-queries"></a>Zapytania bliźniaczej reprezentacji urządzenia

Usługa IoT Hub udostępnia bliźniacze urządzenia jako kolekcję dokumentów o nazwie **urządzenia.** Na przykład następująca kwerenda pobiera cały zestaw bliźniacze urządzenia:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [ZestawY SDK usługi Azure IoT](iot-hub-devguide-sdks.md) obsługują stronicowanie dużych wyników.

Centrum IoT hub umożliwia pobieranie bliźniąt bliźniąt urządzeń filtrowania z dowolnego warunku. Na przykład, aby odbierać bliźniacze urządzenia, w których tag **location.region** jest ustawiony na **US,** użyj następującej kwerendy:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Obsługiwane są również operatory logiczne i porównania arytmetyczne. Na przykład, aby pobrać bliźniacze urządzenia znajdujące się w Stanach Zjednoczonych i skonfigurowane do wysyłania danych telemetrycznych mniej niż co minutę, należy użyć następującej kwerendy:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Dla wygody można również używać stałych tablicowych z operatorami **IN** i **NIN** (nie w). Na przykład, aby pobrać bliźniacze urządzenia, które zgłaszają WiFi lub łączność przewodową, użyj następującej kwerendy:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Często jest konieczne, aby zidentyfikować wszystkie bliźniacze urządzenia, które zawierają określoną właściwość. Centrum IoT obsługuje `is_defined()` tę funkcję w tym celu. Na przykład, aby pobrać bliźniacze urządzenia, które definiują `connectivity` właściwość, użyj następującej kwerendy:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Aby uzyskać pełne odniesienie do możliwości filtrowania, zapoznaj się z sekcją [klauzuli WHERE.](iot-hub-devguide-query-language.md#where-clause)

Grupowanie i agregacje są również obsługiwane. Na przykład, aby znaleźć liczbę urządzeń w każdym stanie konfiguracji telemetrii, należy użyć następującej kwerendy:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Ta kwerenda grupująca zwróci wynik podobny do następującego przykładu:

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

W tym przykładzie trzy urządzenia zgłosiły pomyślną konfigurację, dwa nadal stosują konfigurację, a jeden zgłosił błąd.

Zapytania rzutowania umożliwiają deweloperom zwracanie tylko właściwości, na których im zależy. Na przykład, aby pobrać ostatni czas działania wszystkich odłączonych urządzeń, użyj następującej kwerendy:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Zapytania bliźniaczej reprezentacji modułu

Wykonywanie zapytań dotyczących bliźniąt bliźniąt modułów jest podobne do wykonywania zapytań dotyczących bliźniąt urządzeń, ale przy użyciu innej kolekcji/obszaru nazw; zamiast z **urządzeń**, zapytania z **devices.modules:**

```sql
SELECT * FROM devices.modules
```

Nie zezwalamy na łączenie między urządzeniami i kolekcjami devices.modules. Jeśli chcesz zbadać bliźniacze modułu na różnych urządzeniach, należy to zrobić na podstawie tagów. Ta kwerenda zwróci wszystkie bliźniacze moduły na wszystkich urządzeniach o stanie skanowania:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Ta kwerenda zwróci wszystkie bliźniacze moduły ze stanem skanowania, ale tylko na określonym podzbiorze urządzeń:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>Przykład języka C#

Funkcja kwerendy jest widoczna przez [SDK usługi C#](iot-hub-devguide-sdks.md) w **registryManager** klasy.

Oto przykład prostej kwerendy:

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

Obiekt **kwerendy** jest tworzone z rozmiaru strony (do 100). Następnie wiele stron są pobierane przez wywołanie **GetNextAsTwinAsync** metody wiele razy.

Obiekt kwerendy udostępnia wiele **Next** wartości, w zależności od opcji deserializacji wymagane przez kwerendę. Na przykład bliźniaczej reprezentacji urządzenia lub obiektów zadań lub zwykły JSON podczas korzystania z projekcji.

### <a name="nodejs-example"></a>Przykład node.js

Funkcja kwerendy jest rozpoznawana przez [zestaw SDK usługi Azure IoT dla node.js](iot-hub-devguide-sdks.md) w obiekcie **rejestru.**

Oto przykład prostej kwerendy:

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

Obiekt **kwerendy** jest tworzone z rozmiaru strony (do 100). Następnie wiele stron są pobierane przez wywołanie **nextAsTwin** metody wiele razy.

Obiekt kwerendy udostępnia wiele **Next** wartości, w zależności od opcji deserializacji wymagane przez kwerendę. Na przykład bliźniaczej reprezentacji urządzenia lub obiektów zadań lub zwykły JSON podczas korzystania z projekcji.

### <a name="limitations"></a>Ograniczenia

> [!IMPORTANT]
> Wyniki kwerendy może mieć kilka minut opóźnienia w odniesieniu do najnowszych wartości w bliźniacze urządzenia. Jeśli kwerendy poszczególnych bliźniaczych reprezentacji urządzenia przez ID, należy użyć [pobierz bliźniaczej REST API](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin). Ten interfejs API zawsze zwraca najnowsze wartości i ma wyższe limity ograniczania przepustowości. Interfejs API REST można wydać bezpośrednio lub użyć równoważnej funkcji w jednym z [zestawów SDK usługi Usługi Usługi Usługi Azure IoT Hub.](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

Obecnie porównania są obsługiwane tylko między typami pierwotnymi (bez `... WHERE properties.desired.config = properties.reported.config` obiektów), na przykład jest obsługiwany tylko wtedy, gdy te właściwości mają wartości pierwotne.

## <a name="get-started-with-jobs-queries"></a>Wprowadzenie do zapytań o zadania

[Zadania](iot-hub-devguide-jobs.md) umożliwiają wykonywanie operacji na zestawach urządzeń. Każda bliźniacza bliźniaczka urządzenia zawiera informacje o zadaniach, których jest częścią kolekcji o nazwie **zadania**.

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

Obecnie ta kolekcja jest queryable jako **devices.jobs** w języku kwerendy Usługi IoT Hub.

> [!IMPORTANT]
> Obecnie właściwość zadań nigdy nie jest zwracana podczas wykonywania zapytań o bliźniacze urządzenia. Oznacza to, że kwerendy, które zawierają "Z urządzeń". Dostęp do właściwości zadań jest dostępny tylko `FROM devices.jobs`bezpośrednio za pomocą kwerend przy użyciu programu .
>
>

Na przykład, aby uzyskać wszystkie zadania (przeszłe i zaplanowane), które mają wpływ na jedno urządzenie, można użyć następującej kwerendy:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Należy zauważyć, jak ta kwerenda zapewnia stan specyficzne dla urządzenia (i ewentualnie bezpośredniej odpowiedzi metody) każdego zadania zwrócone.

Istnieje również możliwość filtrowania z dowolnych warunków logicznych na wszystkie właściwości obiektu w **kolekcji devices.jobs.**

Na przykład, aby pobrać wszystkie ukończone zadania aktualizacji bliźniaczej reprezentacji urządzenia, które zostały utworzone po wrześniu 2016 r. dla określonego urządzenia, należy użyć następującej kwerendy:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Można również pobrać wyniki na urządzenie pojedynczego zadania.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Ograniczenia

Obecnie zapytania dotyczące **devices.jobs** nie obsługują:

* Prognozy, dlatego `SELECT *` tylko możliwe.
* Warunki, które odnoszą się do bliźniaczej reprezentacji urządzenia oprócz właściwości zadania (zobacz poprzednią sekcję).
* Wykonywanie agregacji, takich jak count, avg, group by.

## <a name="basics-of-an-iot-hub-query"></a>Podstawy kwerendy Centrum IoT

Każde zapytanie Centrum IoT składa się z klauzul SELECT i FROM z opcjonalnymi klauzulami WHERE i GROUP BY. Każde zapytanie jest uruchamiane na kolekcji dokumentów JSON, na przykład bliźniaczych reprezentacji urządzeń. Klauzula FROM wskazuje kolekcję dokumentów, która ma być iterowana (**urządzenia**, **devices.modules**lub **devices.jobs**). Następnie stosuje się filtr w klauzuli WHERE. W za pomocą agregacji wyniki tego kroku są zgrupowane zgodnie z klauzulą GROUP BY. Dla każdej grupy wiersz jest generowany zgodnie z klauzulą SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Klauzula FROM

**From <from_specification>** klauzula może przyjąć tylko trzy wartości: Z **urządzeń** do bliźniaczki zapytania urządzenia, **FROM devices.modules** do bliźniaczki modułu kwerendy lub FROM **devices.jobs** do kwerendy zadania na urządzenie szczegóły.

## <a name="where-clause"></a>Klauzula WHERE

Klauzula **WHERE <filter_condition>** jest opcjonalna. Określa jeden lub więcej warunków, które dokumenty JSON w from kolekcji muszą spełniać, aby być uwzględnione jako część wyniku. Każdy dokument JSON musi ocenić określone warunki do "true", które mają być uwzględnione w wyniku.

Dozwolone warunki są opisane w sekcji [Wyrażenia i warunki](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>Klauzula SELECT

**>select_list <SELECT** jest obowiązkowa i określa, jakie wartości są pobierane z kwerendy. Określa wartości JSON, które mają być używane do generowania nowych obiektów JSON.
Dla każdego elementu filtrowanego (i opcjonalnie zgrupowanego) podzbioru kolekcji FROM faza projekcji generuje nowy obiekt JSON. Ten obiekt jest konstruowany z wartościami określonymi w klauzuli SELECT.

Poniżej znajduje się gramatyka select klauzuli:

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

**Attribute_name** odnosi się do dowolnej właściwości dokumentu JSON w from kolekcji. Niektóre przykłady select klauzul można znaleźć w wprowadzenie do zapytań bliźniaczej reprezentacji urządzenia.

Obecnie klauzule wyboru inne niż **SELECT*** są obsługiwane tylko w zapytaniach zbiorczych na bliźniacze urządzenia.

## <a name="group-by-clause"></a>KLAUZULA GROUP BY

**KLAUZULA GROUP BY <group_specification>** jest opcjonalnym krokiem, który jest wykonywany po filtrze określonym w klauzuli WHERE i przed projekcją określoną w SELECT. Grupuje dokumenty na podstawie wartości atrybutu. Grupy te są używane do generowania zagregowanych wartości, jak określono w klauzuli SELECT.

Przykładem kwerendy używającej grupy GROUP BY jest:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Formalną składnią grupy WEDŁUG jest:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** odnosi się do dowolnej właściwości dokumentu JSON w from kolekcji.

Obecnie klauzula GROUP BY jest obsługiwana tylko podczas wykonywania zapytań o bliźniacze urządzenia.

> [!IMPORTANT]
> Termin `group` ten jest obecnie traktowany jako specjalne słowo kluczowe w kwerendach. W przypadku użycia `group` jako nazwy właściwości należy rozważyć jej otoczenie podwójnymi nawiasami, `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`aby uniknąć błędów, np.
>

## <a name="expressions-and-conditions"></a>Wyrażenia i warunki

Na wysokim poziomie *wyrażenie:*

* Ocenia wystąpienie typu JSON (takie jak wartość logiczna, liczba, ciąg znaków, tablica lub obiekt).
* Jest definiowany przez manipulowanie danymi pochodzącymi z dokumentu JSON urządzenia i stałych przy użyciu wbudowanych operatorów i funkcji.

*Warunki* są wyrażenia, które oceniają do wartości logicznej. Każda stała inna niż wartość **logiczna true** jest uważana za **fałszywą.** Ta reguła zawiera **null,** **undefined**, dowolny obiekt lub wystąpienie tablicy, dowolny ciąg i **fałd logiczny**.

Składnia wyrażeń jest:

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

Aby zrozumieć, co oznacza każdy symbol w składni wyrażeń, zapoznaj się z następującą tabelą:

| Symbol | Definicja |
| --- | --- |
| Attribute_name | Wszelkie właściwości dokumentu JSON w **from** kolekcji. |
| binary_operator | Dowolny operator binarny wymieniony w sekcji [Operatorzy.](#operators) |
| function_name| Dowolna funkcja wymieniona w sekcji [Funkcje.](#functions) |
| decimal_literal |Pływak wyrażony w notacji dziesiętnej. |
| hexadecimal_literal |Liczba wyrażona przez ciąg "0x", po którym następuje ciąg cyfr szesnastkowych. |
| string_literal |Literały ciągów to ciągi Unicode reprezentowane przez sekwencję zero lub więcej znaków Unicode lub sekwencji ucieczki. Literały ciągów są ujęte w cudzysłowie pojedyncze lub cudzysłowy. Dozwolone znaki unicode `\"` `\\`zdefiniowane przez 4 cyfry szesnastkowe. `\uXXXX` `\'` |

### <a name="operators"></a>Operatory

Obsługiwane są następujące operatory:

| Family | Operatory |
| --- | --- |
| Arytmetyczny |+, -, *, /, % |
| Logiczny |AND, OR, NOT |
| Porównanie |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funkcje

Podczas wykonywania zapytań o bliźniacze i zadania jedyną obsługiwana funkcją jest:

| Funkcja | Opis |
| -------- | ----------- |
| IS_DEFINED(właściwość) | Zwraca wartość logiczną wskazującą, czy właściwość została `null`przypisana do wartości (w tym ). |

W warunkach tras obsługiwane są następujące funkcje matematyczne:

| Funkcja | Opis |
| -------- | ----------- |
| ABS(x) | Zwraca wartość bezwzględną (dodatnią) podanego wyrażenia liczbowego. |
| EXP(x) | Zwraca wartość wykładniczą określonego wyrażenia liczbowego (e^x). |
| MOC(x,y) | Zwraca wartość określonego wyrażenia do określonej mocy (x^y).|
| KWADRAT(x)    | Zwraca kwadrat określonej wartości liczbowej. |
| SUFIT(x) | Zwraca najmniejszą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub większą. |
| PIĘTRO(x) | Zwraca największą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub mniejszą. |
| ZNAK(x) | Zwraca znak dodatni (+1), zero (0) lub ujemny (-1) określonego wyrażenia liczbowego.|
| SQRT(x) | Zwraca pierwiastek kwadratowy określonej wartości liczbowej. |

W warunkach tras obsługiwane są następujące funkcje sprawdzania i odlewania typów:

| Funkcja | Opis |
| -------- | ----------- |
| AS_NUMBER | Konwertuje ciąg wejściowy na liczbę. `noop`jeśli dane wejściowe są liczbą; `Undefined` jeśli ciąg nie reprezentuje liczby.|
| IS_ARRAY | Zwraca wartość logiczną wskazującą, czy typem określonego wyrażenia jest tablica. |
| IS_BOOL | Zwraca wartość logiczną wskazującą, czy typem określonego wyrażenia jest wartość logiczna. |
| IS_DEFINED | Zwraca wartość logiczną wskazującą, do właściwości przypisano wartość. Jest to obsługiwane tylko wtedy, gdy wartość jest typem pierwotnym. Typy pierwotne obejmują ciąg, wartość logiczną, numeryczną lub `null`. DateTime, typy obiektów i tablice nie są obsługiwane. |
| IS_NULL | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia ma wartość null. |
| IS_NUMBER | Zwraca wartość logiczną wskazującą, czy typem określonego wyrażenia jest liczba. |
| IS_OBJECT | Zwraca wartość logiczną wskazującą, czy typem określonego wyrażenia jest obiekt JSON. |
| IS_PRIMITIVE | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest pierwotny (ciąg, wartość `null`logiczna, numeryczna lub ). |
| IS_STRING | Zwraca wartość logiczną wskazującą, czy typem określonego wyrażenia jest ciąg. |

W warunkach tras obsługiwane są następujące funkcje ciągu:

| Funkcja | Opis |
| -------- | ----------- |
| CONCAT(x, y, ...) | Zwraca ciąg, który jest wynikiem połączenia co najmniej dwóch wartości ciągu. |
| DŁUGOŚĆ(x) | Zwraca liczbę znaków określonego wyrażenia ciągu.|
| DOLNA(x) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery. |
| GÓRNA(x) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych małych liter na wielkie litery. |
| PODCIĄGNIE(ciąg, start [, długość]) | Zwraca część wyrażenia ciągu, zaczynając od określonego znaku zerowego i kontynuuje do określonej długości lub na końcu ciągu. |
| INDEX_OF(ciąg, fragment) | Zwraca pozycję początkową pierwszego wystąpienia drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli ciąg nie zostanie znaleziony.|
| STARTS_WITH(x, y) | Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu rozpoczyna się od drugiego. |
| ENDS_WITH(x, y) | Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu kończy się na drugim. |
| ZAWIERA(x,y) | Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wykonywać kwerendy w aplikacjach przy użyciu [zestawów SDK usługi Azure IoT.](iot-hub-devguide-sdks.md)
