---
title: Obsługa typów zawartości — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sposób obsługi usługi Azure Logic Apps z typami zawartości projektu i środowiska wykonawczego
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 809cc8524bf0d9922aec1f88aa5bfe3b8f2f4d78
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297125"
---
# <a name="handle-content-types-in-logic-apps"></a>Dojście do typów zawartości w aplikacji logiki

Wiele różnych typów zawartości mogą przepływać przez aplikację logiki, w tym JSON, XML, plików prostych i danych binarnych. Gdy aparat aplikacje logiki obsługuje wszystkie typy zawartości, niektóre natywnie zrozumiane przez aparat aplikacji logiki. Inne osoby mogą wymagać rzutowania lub konwersji w razie potrzeby. W tym artykule opisano sposób aparat obsługi różnych typów zawartości oraz sposób prawidłowej obsługi tych typów, gdy jest to konieczne.

## <a name="content-type-header"></a>Nagłówek Content-Type

Aby uruchomić zasadniczo, Przyjrzyjmy się dwa `Content-Types` wymagają konwersji lub Rzutowanie używanego w aplikacji logiki: `application/json` i `text/plain`.

## <a name="applicationjson"></a>Application/JSON

Aparatu przepływu pracy polega na `Content-Type` nagłówka z protokołu HTTP wymaga aby określić odpowiednią obsługę. Wszystkie żądania z typem zawartości `application/json` są przechowywane i obsługiwane jako obiekt JSON. Ponadto zawartość JSON można przeanalizować domyślnie bez konieczności żadnych rzutowania. 

Na przykład, można przeanalizować żądanie ma nagłówek typu zawartości `application/json ` w przepływie pracy przy użyciu wyrażeń, takich jak `@body('myAction')['foo'][0]` można uzyskać wartość `bar` w takim przypadku:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

Nie dodatkowe rzutowanie jest niezbędne. Podczas pracy z danymi, które jest JSON, ale nie ma określonego nagłówka, należy go ręcznie rzutowania JSON przy użyciu `@json()` funkcji, na przykład: `@json(triggerBody())['foo']`.

### <a name="schema-and-schema-generator"></a>Schemat i generator schematu

Wyzwalacz żądania umożliwia wprowadzenie schematu JSON dla ładunku, który powinien być wyświetlany. Ten schemat umożliwia projektanta Generowanie tokenów, może korzystać z zawartości żądania. Jeśli nie masz schematu gotowe, wybierz **ładunku próbki używany do generowania schematu**, więc można wygenerować schematu JSON z ładunku próbki.

![Schemat](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>Akcja "Parse JSON"

`Parse JSON` Pozwala analizować zawartość JSON w tokenach przyjazną wykorzystania aplikacji logiki. Podobnie jak wyzwalacza żądania, ta akcja umożliwia wprowadź lub wygenerować schematu JSON dla zawartości, który chcesz przeanalizować. To narzędzie ułatwia danych korzystanie z usługi Service Bus, bazy danych Azure rozwiązania Cosmos i tak dalej, znacznie.

![Przeanalizuj dane JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>zwykły tekst

Podobnie jak `application/json`, wiadomości HTTP odebrane z `Content-Type` nagłówek `text/plain` są przechowywane w postaci raw. Ponadto komunikaty te są zawarte w kolejne czynności bez rzutowania, te żądania przejść z `Content-Type`: `text/plain` nagłówka. Na przykład podczas pracy z pliku prostego, można uzyskać ten HTTP zawartości jako `text/plain`:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

W następnej akcji w przypadku wysłania żądania jako treść inne żądanie (`@body('flatfile')`), żądanie będzie mieć `text/plain` nagłówka Content-Type. Podczas pracy z danymi, które jest w formacie zwykłego tekstu, ale nie ma określonego nagłówka ręcznie można rzutować danych przy użyciu tekstu `@string()` funkcji, na przykład: `@string(triggerBody())`.

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Aplikacja/xml i funkcji Application/octet-stream i konwertera

Aparat aplikacji logiki zawsze zachowuje `Content-Type` który otrzymano na żądanie lub odpowiedź HTTP. Dlatego jeśli aparat odbiera zawartość z `Content-Type` z `application/octet-stream`, i obejmują, że zawartość w kolejnych akcji bez rzutowania, żądania wychodzącego został `Content-Type`: `application/octet-stream`. W ten sposób aparat można zagwarantować, że utratę danych podczas przenoszenia przez przepływ pracy. Jednak stan akcji (wejścia i wyjścia) jest przechowywana w obiekcie JSON, ponieważ stan przechodzi przez przepływ pracy. Tak, aby zachować niektóre typy danych, aparat Konwertuje zawartość na ciąg kodowany w standardzie binary base64 odpowiednie metadane, która zachowuje zarówno `$content` i `$content-type`, które są automatycznie przekonwertować. 

* `@json()` -rzutuje danych `application/json`
* `@xml()` -rzutuje danych `application/xml`
* `@binary()` -rzutuje danych `application/octet-stream`
* `@string()` -rzutuje danych `text/plain`
* `@base64()` -Konwertuje zawartość na ciąg w formacie base64
* `@base64toString()` -Konwertuje ciąg kodowany w standardzie base64 `text/plain`
* `@base64toBinary()` -Konwertuje ciąg kodowany w standardzie base64 `application/octet-stream`
* `@encodeDataUri()` -koduje ciągiem w postaci tablicy bajtów dataUri
* `@decodeDataUri()` -dekoduje dataUri do tablicy typu byte

Na przykład w przypadku otrzymania żądania HTTP z `Content-Type`: `application/xml`:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Można rzutować i później użyć przypominać `@xml(triggerBody())`, lub w funkcji, takich jak `@xpath(xml(triggerBody()), '/CustomerName')`.

## <a name="other-content-types"></a>Inne typy zawartości

Inne typy zawartości są obsługiwane i pracy z usługą logic apps, ale może wymagać ręcznego pobierania treść komunikatu przez dekodowania `$content`. Na przykład, załóżmy, że użytkownik zainicjuje `application/x-www-url-formencoded` żądania where `$content` jest ładunku zakodowany jako ciąg base64, aby zachować wszystkie dane:

```
CustomerName=Frank&Address=123+Avenue
```

Ponieważ żądanie nie jest w postaci zwykłego tekstu lub JSON, żądanie jest przechowywane w akcji w następujący sposób:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Obecnie nie ma funkcji macierzystej dla danych formularza, więc można nadal używać tych danych w przepływie pracy, przez ręcznie podczas uzyskiwania dostępu do danych przy użyciu funkcji, takich jak `@string(body('formdataAction'))`. Jeśli potrzebujesz żądania wychodzącego musi mieć również `application/x-www-url-formencoded` nagłówek typu zawartości żądania można dodać do treści akcji bez żadnych rzutowania, takich jak `@body('formdataAction')`. Jednak ta metoda działa tylko, jeśli treść jest to jedyny parametr w `body` wejściowego. Jeśli spróbujesz użyć `@body('formdataAction')` w `application/json` żądania, pobrać błąd w czasie wykonywania, ponieważ jest wysyłany zakodowanej treści.

