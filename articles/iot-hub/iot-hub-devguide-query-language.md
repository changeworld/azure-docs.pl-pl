---
title: Zrozumienie język zapytań usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — opis Centrum IoT przypominającego SQL zapytania język używany do pobierania informacji o urządzeniu/modułu twins i zadania z Centrum IoT.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/26/2018
ms.author: elioda
ms.openlocfilehash: 27ddc41c463c00a061a396098f0ccfaa6cec80a1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Język zapytań Centrum IoT urządzenia i moduł twins, zadań i rozsyłania wiadomości

Centrum IoT zapewnia zaawansowane języka przypominającego SQL, aby pobrać informacje dotyczące [twins urządzenia] [ lnk-twins] i [zadania][lnk-jobs], i [rozsyłania wiadomości][lnk-devguide-messaging-routes]. W tym artykule przedstawiono:

* Wprowadzenie do najważniejszych funkcji języka kwerend Centrum IoT, i
* Szczegółowy opis języka.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Urządzenia i moduł dwie zapytań
[Urządzenie twins] [ lnk-twins] i twins moduł może zawierać dowolne obiekty JSON jako znaczniki i właściwości. Centrum IoT umożliwia twins urządzenia zapytania i twins modułu jako pojedynczego dokumentu JSON zawierający wszystkie informacje dwie.
Załóżmy na przykład, że Twoje twins urządzenia Centrum IoT ma następującą strukturę (dwie moduł będzie podobny tylko z dodatkowych moduleId):

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

### <a name="device-twin-queries"></a>Urządzenie dwie zapytań

Centrum IoT udostępnia twins urządzenia jako kolekcji dokumentów o nazwie **urządzeń**.
Dlatego poniższe zapytanie pobiera cały zestaw twins urządzenia:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Zestawy Azure SDK IoT] [ lnk-hub-sdks] obsługuje stronicowania duże wyniki.

Centrum IoT umożliwia pobieranie filtrowania z warunkami dowolnego twins urządzenia. Na przykład, aby otrzymać urządzenia twins where **location.region** ma ustawioną wartość tagu **USA** Użyj następującego zapytania:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Operatory logiczne i arytmetyczne porównania są obsługiwane również. Na przykład aby uzyskać urządzenia twins znajdujące się w Stanach Zjednoczonych i skonfigurowany, aby wysłać dane telemetryczne co mniej niż minutę, wykonaj następujące zapytanie:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Dla wygody istnieje również możliwość użycia tablicowych z **IN** i **nW** operatory (nie w). Na przykład aby pobrać twins urządzenia, które zgłosiły sieci Wi-Fi lub łączności przewodowej Użyj następującego zapytania:

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Często jest niezbędne w celu ustalenia wszystkich twins urządzenia zawierające określoną właściwość. Centrum IoT obsługuje funkcję `is_defined()` w tym celu. Na przykład, aby pobrać twins urządzenia, które definiują `connectivity` właściwości użyj następującego zapytania:

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

Zapoznaj się [klauzuli WHERE] [ lnk-query-where] sekcję, aby pełną dokumentację funkcji filtrowania.

Grupowanie i agregacje są również obsługiwane. Na przykład można znaleźć liczba urządzeń w każdej telemetrii stan konfiguracji, użyj następującego zapytania:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

To zapytanie grupowania zwróci wynik podobny do poniższego przykładu:

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

W tym przykładzie trzy urządzeń zgłoszonych Konfiguracja zakończyła się pomyślnie, nadal są dwa stosowania konfiguracji i jedną zgłosił błąd.

Kwerend projekcji umożliwiają deweloperom zwracać tylko właściwości, które ich interesują. Na przykład można pobrać ostatniego działania wszystkich rozłączona urządzeń, użyj następującej kwerendy:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Kwerendy dwie modułu

Wykonywanie zapytania na twins modułu jest podobny do zapytania na urządzeniu twins, ale za pomocą różnych kolekcji/przestrzeni nazw, tj. zamiast "z urządzenia" można wysyłać zapytania

```sql
SELECT * FROM devices.modules
```

Firma Microsoft nie zezwalaj na sprzężenie między urządzeniami i devices.modules kolekcji. Jeśli chcesz twins modułu zapytania na urządzeniach, to oparte na tagi zrobić. To zapytanie spowoduje zwrócenie wszystkich twins moduł dla wszystkich urządzeń ze stanem skanowania:

```sql
Select * from devices.modules where reported.properties.status = 'scanning'
```

To zapytanie spowoduje zwrócenie wszystkich twins modułu o stanie skanowania, ale tylko w określonym podzestawie urządzeń.

```sql
Select * from devices.modules where reported.properties.status = 'scanning' and deviceId IN ('device1', 'device2')  
```

### <a name="c-example"></a>Przykład C#
Funkcje zapytań jest udostępniany przez [C# usługi SDK] [ lnk-hub-sdks] w **RegistryManager** klasy.
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

**Zapytania** utworzeniu wystąpienia obiektu z rozmiarem strony (maksymalnie 100). Następnie wiele stron są pobierane przez wywołanie metody **GetNextAsTwinAsync** metody wiele razy.

Obiekt zapytania udostępnia wiele **dalej** wartości, w zależności od opcji deserializacji wymagane przez zapytanie. Na przykład dwie lub zadanie obiekty urządzeń lub zwykły JSON przy użyciu projekcji.

### <a name="nodejs-example"></a>Przykład node.js
Funkcje zapytań jest udostępniany przez [usługi Azure IoT SDK dla środowiska Node.js] [ lnk-hub-sdks] w **rejestru** obiektu.
Oto przykład prostego zapytania:

```nodejs
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

**Zapytania** utworzeniu wystąpienia obiektu z rozmiarem strony (maksymalnie 100). Następnie wiele stron są pobierane przez wywołanie metody **nextAsTwin** metody wiele razy.

Obiekt zapytania udostępnia wiele **dalej** wartości, w zależności od opcji deserializacji wymagane przez zapytanie. Na przykład dwie lub zadanie obiekty urządzeń lub zwykły JSON przy użyciu projekcji.

### <a name="limitations"></a>Ograniczenia

> [!IMPORTANT]
> Wyniki zapytania może mieć kilka minut opóźnienie względem ostatnie wartości twins urządzenia. Jeśli zapytanie twins poszczególne urządzenia przez identyfikator, za pomocą interfejsu API dwie pobrać urządzenia. Ten interfejs API zawsze zawiera najnowsze wartości i ma wyższy ograniczenie.

Obecnie porównania są obsługiwane tylko między typy pierwotne (nie obiektów), na przykład `... WHERE properties.desired.config = properties.reported.config` jest obsługiwana tylko w przypadku pierwotnych wartości tych właściwości.

## <a name="get-started-with-jobs-queries"></a>Wprowadzenie do kwerend zadania

[Zadania] [ lnk-jobs] umożliwiają wykonywanie operacji na zestawy urządzeń. Dwie każdego urządzenia zawiera informacje o wszystkich zadań, które jest częścią kolekcji o nazwie **zadania**.
Logicznie,

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

Ta kolekcja jest obecnie kolejność jako **devices.jobs** w Centrum IoT języka zapytań.

> [!IMPORTANT]
> Obecnie właściwości zadania nigdy nie jest zwracana podczas wykonywania zapytania twins urządzenia. Oznacza to, zapytania, które zawierają "z urządzeń". Właściwości zadania są dostępne tylko bezpośrednio przy użyciu zapytań `FROM devices.jobs`.
>
>

Na przykład aby Pobierz wszystkie zadania (ostatnich i zaplanowane), które mają wpływ na jednym urządzeniu, można użyć następującej kwerendy:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Należy zwrócić uwagę, jak to zapytanie informuje o stanie specyficzne dla urządzenia (oraz prawdopodobnie odpowiedzi metoda bezpośrednia) każde zadanie zwrócone.
Istnieje również możliwość filtrowania z dowolnego warunków logicznych na wszystkie właściwości obiektu w **devices.jobs** kolekcji.
Na przykład aby pobrać wszystkie urządzenia ukończone dwie aktualizacji zadania, które zostały utworzone po września 2016 dla określonego urządzenia, użyj następującego zapytania:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Można również pobierać wyników na urządzenie z jednym zadaniu.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Ograniczenia
Obecnie zapytanie na **devices.jobs** nie obsługują:

* Projekcji, w związku z tym tylko `SELECT *` jest możliwe.
* Warunki, które odwołują się do dwie urządzenia, oprócz właściwości zadania (zobacz poprzednią sekcję).
* Wykonywanie agregacji, takie jak liczba, avg, Grupuj według.

## <a name="device-to-cloud-message-routes-query-expressions"></a>Wyrażenia zapytań tras wiadomości urządzenia do chmury

Przy użyciu [trasy urządzenia do chmury][lnk-devguide-messaging-routes], można skonfigurować Centrum IoT wysłania wiadomości urządzenia do chmury do różnych punktów końcowych. Podczas wysyłania jest oparta na wyrażenia porównywany poszczególne wiadomości.

Trasa [warunku] [ lnk-query-expressions] używa tego samego języka zapytań Centrum IoT jako warunki w zapytaniach dwie i zadania. Warunki trasy są oceniane w nagłówkach wiadomości oraz i treść. Routingu wyrażeniu zapytania może obejmować tylko nagłówki komunikatów treść komunikatu lub oba. Centrum IoT zakłada określonego schematu w nagłówkach i treści wiadomości celu kierowania wiadomości. W poniższych sekcjach opisano, co jest wymagane dla Centrum IoT można przekierować poprawnie.

### <a name="routing-on-message-headers"></a>Routing w nagłówkach wiadomości

Centrum IoT przyjmuje następujące reprezentacja JSON w nagłówkach wiadomości do rozsyłania wiadomości:

```json
{
  "message": {
    "systemProperties": {
      "contentType": "application/json",
      "contentEncoding": "utf-8",
      "iothub-message-source": "deviceMessages",
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z"
    },
    "appProperties": {
      "processingPath": "<optional>",
      "verbose": "<optional>",
      "severity": "<optional>",
      "testDevice": "<optional>"
    },
    "body": "{\"Weather\":{\"Temperature\":50}}"
  }
}
```

Właściwości systemu wiadomości są poprzedzane prefiksem `'$'` symbolu.
Właściwości użytkownika są zawsze dostępne z jego nazwą. Jeśli nazwa właściwości użytkownika pokrywa się z właściwością systemu (takich jak `$contentType`), właściwości użytkownika są pobierane z `$contentType` wyrażenia.
Zawsze dostęp do właściwości systemu za pomocą nawiasów `{}`: na przykład można użyć wyrażenia `{$contentType}` do dostępu do właściwości systemu `contentType`. Nazwy właściwości w nawiasach kwadratowych zawsze pobierają odpowiadających im właściwości systemu.

Należy pamiętać, że nazwy właściwości bez uwzględniania wielkości liter.

> [!NOTE]
> Wszystkie właściwości wiadomości są ciągami. Właściwości systemu, zgodnie z opisem w [przewodnik dewelopera po][lnk-devguide-messaging-format], nie są obecnie dostępne do użycia w zapytaniach.
>

Na przykład, jeśli używasz `messageType` właściwość, należy kierować wszystkie dane telemetryczne jeden punkt końcowy, a wszystkie alerty do innego punktu końcowego. Można napisać poniższe wyrażenie można przekierować danych telemetrii:

```sql
messageType = 'telemetry'
```

I następującego wyrażenia do rozsyłania komunikatów alertu:

```sql
messageType = 'alert'
```

Wyrażenia logiczne i funkcje są również obsługiwane. Ta funkcja umożliwia rozróżnianie między poziom ważności, na przykład:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Zapoznaj się [wyrażenie i warunki] [ lnk-query-expressions] sekcję, aby pełną listę obsługiwanych operatory i funkcje.

### <a name="routing-on-message-bodies"></a>Routing w treści wiadomości

Centrum IoT można kierować tylko oparte na treść komunikatu zawartość, jeśli treść jest poprawnie sformułowany JSON zakodowane w formacie UTF-8, UTF-16 lub UTF-32. Ustaw typ zawartości wiadomości `application/json`. Ustaw zawartość, kodowanie obsługiwane kodowania UTF w nagłówkach wiadomości. Jeśli jeden z nagłówków nie zostanie określony, Centrum IoT nie próbuje ocenić żadnych wyrażenie zapytania dotyczące treści dla komunikatu. Jeśli wiadomość nie jest komunikat JSON lub jeśli wiadomość nie określa typu zawartości i kodowania zawartości, nadal służy rozsyłania wiadomości do rozsyłania wiadomości oparte na nagłówkach wiadomości.

Poniższy przykład pokazuje, jak utworzyć wiadomości o poprawnie sformułowany i zakodowanej treści JSON:

```csharp
string messageBody = @"{ 
                            ""Weather"":{ 
                                ""Temperature"":50, 
                                ""Time"":""2017-03-09T00:00:00.000Z"", 
                                ""PrevTemperatures"":[ 
                                    20, 
                                    30, 
                                    40 
                                ], 
                                ""IsEnabled"":true, 
                                ""Location"":{ 
                                    ""Street"":""One Microsoft Way"", 
                                    ""City"":""Redmond"", 
                                    ""State"":""WA"" 
                                }, 
                                ""HistoricalData"":[ 
                                    { 
                                    ""Month"":""Feb"", 
                                    ""Temperature"":40 
                                    }, 
                                    { 
                                    ""Month"":""Jan"", 
                                    ""Temperature"":30 
                                    } 
                                ] 
                            } 
                        }"; 
 
// Encode message body using UTF-8 
byte[] messageBytes = Encoding.UTF8.GetBytes(messageBody); 
 
using (var message = new Message(messageBytes)) 
{ 
    // Set message body type and content encoding. 
    message.ContentEncoding = "utf-8"; 
    message.ContentType = "application/json"; 
 
    // Add other custom application properties.  
    message.Properties["Status"] = "Active";    
 
    await deviceClient.SendEventAsync(message); 
}
```

Można użyć `$body` w wyrażeniu zapytania do rozsyłania wiadomości. Proste treści odwołania, typu odwołania tablicy treści lub wiele odwołań treści można użyć w wyrażeniu zapytania. Wyrażenie zapytania można także połączyć treści odwołanie z odwołaniem nagłówka wiadomości. Na przykład poniżej przedstawiono wszystkie wyrażenia prawidłową kwerendę:

```sql
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Podstawowe informacje o kwerendzie Centrum IoT
Każdej kwerendy Centrum IoT składa się wybierz i z klauzulami, w której opcjonalne i klauzul GROUP BY. Każdy zapytania jest uruchamiane na kolekcji dokumentów JSON, na przykład twins urządzenia. Klauzula FROM wskazuje kolekcji dokumentów, należy powtórzyć na (**urządzeń** lub **devices.jobs**). Następnie jest stosowany filtr w klauzuli WHERE. Z agregacji, wyniki tego kroku są grupowane jak określono w klauzuli GROUP BY. Dla każdej grupy jest generowany wiersz jak określono w klauzuli SELECT.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>klauzula FROM
**z < from_specification >** klauzuli może przyjmować tylko dwie wartości: **z urządzeń** do twins urządzenia zapytania, lub **z devices.jobs** do szczegółów na urządzenie zadania zapytania.

## <a name="where-clause"></a>Klauzula WHERE
**Gdzie < filter_condition >** klauzula jest opcjonalne. Określa się, że co najmniej jeden warunek, że JSON dokumentów w kolekcji FROM muszą spełniać być dołączane jako część wyniku. Każdy dokument JSON musi być określone warunki "true", aby było uwzględnione w wyniku.

Dozwolone warunki opisane w sekcji [wyrażeń i warunki][lnk-query-expressions].

## <a name="select-clause"></a>klauzula SELECT
**Wybierz < select_list >** jest wymagana i określa, jakie wartości są pobierane z zapytania. Określa wartości JSON ma być używany do generowania nowych obiektów JSON.
Dla każdego elementu filtrowane (i opcjonalnie grupowanych) podzestaw kolekcji FROM faza projekcji generuje nowy obiekt JSON. Ten obiekt jest tworzony przy użyciu wartości określone w klauzuli SELECT.

Gramatyka klauzuli SELECT jest następujący:

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

**Attribute_name** odwołuje się do żadnej właściwości w kolekcji z dokumentu JSON. Przykłady klauzule SELECT można znaleźć w [wprowadzenie do zapytań dwie urządzenia] [ lnk-query-getstarted] sekcji.

Obecnie wybór klauzule różni się od **wybierz*** są obsługiwane tylko w zapytaniach agregacji w twins urządzenia.

## <a name="group-by-clause"></a>klauzula GROUP BY
**GROUP BY < group_specification >** klauzuli to krok opcjonalny, który jest wykonywany po określony w klauzuli WHERE, a przed projekcji określonej w polu Wybierz filtr. Grup dokumentów na podstawie wartości atrybutu. Te grupy są używane do generowania wartości zagregowane, jak określono w klauzuli SELECT.

Przykładem zapytanie, używając GROUP BY jest:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Posiadanie składnia GROUP BY jest:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** odwołuje się do żadnej właściwości w kolekcji z dokumentu JSON.

Obecnie w klauzuli GROUP BY jest obsługiwana tylko podczas wykonywania zapytania twins urządzenia.

## <a name="expressions-and-conditions"></a>Wyrażenia i warunki
Na wysokim poziomie *wyrażenie*:

* Daje w wyniku wystąpienia typu JSON (na przykład logiczną, liczbą, string, tablicy lub obiektu).
* Jest zdefiniowana przez manipulację danymi pochodzących z dokumentu JSON urządzenia oraz stałe za pomocą wbudowanych operatorów i funkcji.

*Warunki* są wyrażenia, które zwrócą wartość logiczną. Wszystkie inne niż wartość logiczna stała **true** jest uznawany za **false**. Ta reguła zawiera **null**, **Niezdefiniowany**, wystąpienie obiektu ani tablicy, dowolnego ciągu i typu Boolean **false**.

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

Aby zrozumieć, co oznacza każdy symbol w składni wyrażeń, można skorzystać z poniższej tabeli:

| Symbol | Definicja |
| --- | --- |
| attribute_name | Dokument JSON w dowolnej właściwości **FROM** kolekcji. |
| binary_operator | Wszelkie operatora binarnego na liście [operatory](#operators) sekcji. |
| nazwa_funkcji| Dowolne funkcje wymienione w [funkcje](#functions) sekcji. |
| decimal_literal |Float, wyrażone w notacji dziesiętnej. |
| hexadecimal_literal |Liczba wyrażona w ciągu '0 x' następuje ciąg cyfr szesnastkowych. |
| literał |Literały ciągu są reprezentowane przez sekwencję zero lub więcej znaków Unicode lub sekwencji unikowych ciągów Unicode. Literały ciągu są ujęte w apostrofy lub podwójny cudzysłów. Dozwolone specjalne: `\'`, `\"`, `\\`, `\uXXXX` znaków Unicode, zdefiniowane przez 4 cyfr szesnastkowych. |

### <a name="operators"></a>Operatory
Obsługiwane są następujące operatory:

| Rodzina | Operatory |
| --- | --- |
| Operacje arytmetyczne |+, -, *, /, % |
| Logiczne |AND, OR, NOT |
| Porównanie |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funkcje
Podczas wykonywania zapytania twins i zadania, którego jedynym obsługiwanym funkcja jest:

| Funkcja | Opis |
| -------- | ----------- |
| IS_DEFINED(Property) | Zwraca wartość Boolean wskazującą, czy właściwość zostanie przypisana wartość (w tym `null`). |

W warunkach tras obsługiwane są następujące funkcje matematyczne:

| Funkcja | Opis |
| -------- | ----------- |
| ABS(x) | Zwraca wartość bezwzględną (dodatnia) z określonego wyrażenia liczbowego. |
| EXP(x) | Zwraca wartość określonego wyrażenia liczbowego wykładniczej (e ^ x). |
| Power(x,y) | Zwraca wartość określonego wyrażenia do podanej potęgi (x ^ y).|
| SQUARE(x) | Zwraca kwadrat określoną wartość liczbową. |
| CEILING(x) | Zwraca najmniejszą wartość całkowita większa lub równa określonej wyrażenia liczbowego. |
| FLOOR(x) | Zwraca największą liczbę całkowitą mniejszą niż określona wyrażenia liczbowego. |
| SIGN(x) | Zwraca plus (+ 1), 0 (zero) lub znakiem minus (-1) z określonego wyrażenia liczbowego.|
| SQRT(x) | Zwraca pierwiastek kwadratowy z określoną wartość liczbową. |

W warunkach trasy następujące Sprawdzanie typu i rzutowanie funkcje są obsługiwane:

| Funkcja | Opis |
| -------- | ----------- |
| AS_NUMBER | Konwertuje ciąg wejściowy liczbą. `noop` Jeśli dane wejściowe jest liczbą; `Undefined` czy ciąg reprezentuje liczbę.|
| IS_ARRAY — | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest typem tablicy. |
| IS_BOOL | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenie jest wartością logiczną. |
| IS_DEFINED | Zwraca wartość Boolean wskazującą, czy właściwość zostanie przypisana wartość. |
| IS_NULL | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest wartość null. |
| IS_NUMBER | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest liczbą. |
| IS_OBJECT | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest obiektem JSON. |
| IS_PRIMITIVE | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest właściwością pierwotną (string, Boolean, numeryczną, lub `null`). |
| IS_STRING | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest ciągiem. |

Obsługiwane są następujące funkcje ciągów w warunkach trasy:

| Funkcja | Opis |
| -------- | ----------- |
| CONCAT (x, y,...) | Zwraca ciąg, który jest wynikiem łączenie dwóch lub więcej wartości ciągu. |
| LENGTH(x) | Zwraca liczbę znaków z określonego wyrażenia ciągu.|
| LOWER(x) | Zwraca wyrażenie ciągu po konwersji danych wielką literę na małe litery. |
| UPPER(x) | Zwraca wyrażenie ciągu po konwersji danych małą literę na wielkie litery. |
| SUBSTRING (ciąg, start [, długość]) | Zwraca część wyrażenia ciągu, zaczynając od pozycji określony znak liczony od zera i kontynuuje określonej długości lub końca ciągu. |
| INDEX_OF (ciąg, fragment) | Zwraca pozycję początkową pierwszego wystąpienia drugi ciąg wyrażenia w pierwszym wyrażeniu określony ciąg lub wartość -1, jeśli nie zostanie znaleziony ciąg.|
| STARTS_WITH (x, y) | Zwraca wartość Boolean wskazującą, czy pierwszy wyrażenia ciągu rozpoczyna się od drugiego. |
| ENDS_WITH (x, y) | Zwraca wartość Boolean wskazującą, czy pierwszy wyrażenia ciągu kończy się na sekundę. |
| CONTAINS(x,y) | Zwraca wartość Boolean wskazującą, czy pierwszy wyrażenia ciągu zawiera drugi. |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak wykonywać zapytania w aplikacjach za pomocą [Azure IoT SDK][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
