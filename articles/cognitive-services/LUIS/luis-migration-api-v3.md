---
title: Zmiany przewidywanych punktów końcowych w interfejsie API v3
titleSuffix: Azure Cognitive Services
description: Interfejsy API funkcji przewidywania zapytań w wersji 3 zostały zmienione. Skorzystaj z tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do interfejsów API punktu końcowego w wersji 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bb2255a9a68a499ff3e77c1fbd35081a2474cf1d
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961937"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Zmiany przewidywanych punktów końcowych dla wersji 3

Interfejsy API funkcji przewidywania zapytań w wersji 3 zostały zmienione. Skorzystaj z tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do interfejsów API punktu końcowego w wersji 3. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Ogólnie dostępny stan** — ten interfejs API v3 obejmuje znaczące zmiany żądania JSON i odpowiedzi z interfejsu API v2.

Interfejs API v3 udostępnia następujące nowe funkcje:

* [Jednostki zewnętrzne](#external-entities-passed-in-at-prediction-time)
* [Listy dynamiczne](#dynamic-lists-passed-in-at-prediction-time)
* [Wstępnie skompilowane zmiany JSON jednostki](#prebuilt-entity-changes)

[Żądanie](#request-changes) i [odpowiedź](#response-changes) punktu końcowego przewidywania mają znaczące zmiany, aby obsługiwać nowe funkcje wymienione powyżej, w tym następujące:

* [Zmiany obiektu odpowiedzi](#top-level-json-changes)
* [Odwołania do nazwy roli jednostki zamiast nazwy jednostki](#entity-role-name-instead-of-entity-name)
* [Właściwości do oznaczania jednostek w wyrażenia długości](#marking-placement-of-entities-in-utterances)

[Dokumentacja referencyjna](https://aka.ms/luis-api-v3) jest dostępna w wersji 3.

## <a name="v3-changes-from-preview-to-ga"></a>Wersja 3 zmian z wersji zapoznawczej na GA

Wersja 3 wprowadziła następujące zmiany w ramach przechodzenia do wersji GA: 

* Następujące wbudowane jednostki mają różne odpowiedzi JSON: 
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Wymierna nazwa klucza jednostki z `units` do `unit`

* Zmiana JSON treści żądania:
    * od `preferExternalEntities` do `preferExternalEntities`
    * opcjonalny `score` parametr dla jednostek zewnętrznych

* Zmiany JSON treści odpowiedzi:
    * `normalizedQuery` usunięte

## <a name="suggested-adoption-strategy"></a>Sugerowana strategia wdrażania

Jeśli używasz platformy bot Framework, sprawdzanie pisowni Bing wersji 7 lub chcesz migrować tylko tworzenie aplikacji LUIS, Kontynuuj korzystanie z punktu końcowego v2. 

Jeśli nie znasz żadnej aplikacji klienckiej ani integracji (bot Framework i sprawdzanie pisowni Bing wersji 7), to na pewno chcesz migrować Tworzenie aplikacji LUIS i punkt końcowy przewidywania w tym samym czasie, zacznij korzystać z punktu końcowego przewidywania v3. Punkt końcowy przewidywania w wersji 2 nadal będzie dostępny i jest to dobra strategia obniżenia poziomu. 

## <a name="not-supported"></a>Nieobsługiwane

* Interfejs sprawdzanie pisowni Bing API nie jest obsługiwany w punkcie końcowym przewidywania v3 — Kontynuuj korzystanie z punktu końcowego przewidywania interfejsu API V2 na potrzeby poprawek pisowni

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework i Azure Bot Service aplikacje klienckie

Kontynuuj korzystanie z punktu końcowego przewidywania interfejsu API v2 do momentu wydania wersji bot Framework. 

## <a name="v2-api-deprecation"></a>Przestarzałe interfejsy API v2 

Interfejs API przewidywania w wersji 2 nie będzie przestarzały przez co najmniej 9 miesięcy od wersji zapoznawczej v3, 8 czerwca 2020. 

## <a name="endpoint-url-changes"></a>Zmiany adresu URL punktu końcowego 

### <a name="changes-by-slot-name-and-version-name"></a>Zmiany według nazwy miejsca i nazwy wersji

Format wywołania HTTP punktu końcowego v3 został zmieniony.

Jeśli chcesz wykonać zapytanie według wersji, musisz najpierw [opublikować za pośrednictwem interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) z `"directVersionPublish":true`. Zbadaj punkt końcowy odwołujący się do identyfikatora wersji zamiast nazwy gniazda.

|WERSJA INTERFEJSU API PRZEWIDYWANIA|METHOD|Adres URL|
|--|--|--|
|V3|GET|https://<b>{region}</b>. API.Cognitive.Microsoft.com/Luis/<b>prognozowanie</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Slots/<b>{slot-Name}</b>/Predict? Query =<b>{Query}</b>|
|V3|POST|https://<b>{region}</b>. API.Cognitive.Microsoft.com/Luis/<b>prognozowanie</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Slots/<b>{slot-Name}</b>/Predict|
|Wersja 2|GET|https://<b>{region}</b>. API.Cognitive.Microsoft.com/Luis/<b>prognozowanie</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Versions/<b>{Version-ID}</b>/Predict? Query =<b>{Query}</b>|
|Wersja 2|POST|https://<b>{region}</b>. API.Cognitive.Microsoft.com/Luis/<b>prognozowanie</b><b>v 3.0</b>/Apps/<b>{App-ID}</b>/Versions/<b>{Version-ID}</b>/Predict|

|Prawidłowe wartości dla `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Żądanie zmian 

### <a name="query-string-changes"></a>Zmiany ciągu zapytania

Interfejs API v3 ma inne parametry ciągu zapytania.

|Nazwa parametru|Typ|Wersja|Domyślne|Przeznaczenie|
|--|--|--|--|--|
|`log`|wartość logiczna|Wersja 2 & v3|false|Zapisz zapytanie w pliku dziennika. Wartość domyślna to false.| 
|`query`|ciąg|Tylko wersja 3|Brak domyślnej — jest to wymagane w żądaniu GET|**W wersji 2**wypowiedź do przewidywania jest parametrem `q`. <br><br>**W wersji 3**funkcja jest przenoszona w parametrze `query`.|
|`show-all-intents`|wartość logiczna|Tylko wersja 3|false|Zwróć wszystkie intencje z odpowiednim wynikiem w obiekcie **przewidywania. intencje** . Intencje są zwracane jako obiekty w obiekcie nadrzędnym `intents`. Pozwala to na dostęp programistyczny bez potrzeby znajdowania zamiaru w tablicy: `prediction.intents.give`. W wersji 2 te zostały zwrócone w tablicy. |
|`verbose`|wartość logiczna|Wersja 2 & v3|false|**W wersji 2**, gdy ustawiono wartość true, wszystkie przewidywane intencje zostały zwrócone. Jeśli potrzebujesz wszystkich przewidywanych intencji, użyj parametru v3 `show-all-intents`.<br><br>**W wersji 3**ten parametr zawiera tylko szczegóły metadanych jednostki przewidywania jednostek.  |
|`timezoneOffset`|ciąg|Wersja 2|-|Strefa czasowa zastosowana do jednostek datetimeV2.|
|`datetimeReference`|ciąg|V3|-|[Strefa czasowa](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) zastosowana do jednostek datetimeV2. Zastępuje `timezoneOffset` w wersji 2.|


### <a name="v3-post-body"></a>Treść wpisu v3

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Właściwość|Typ|Wersja|Domyślne|Przeznaczenie|
|--|--|--|--|--|
|`dynamicLists`|tablica|Tylko wersja 3|Nie jest wymagane.|[Listy dynamiczne](#dynamic-lists-passed-in-at-prediction-time) umożliwiają rozbudowa istniejącej, przeszkolonej i opublikowanej jednostki listy, już w aplikacji Luis.|
|`externalEntities`|tablica|Tylko wersja 3|Nie jest wymagane.|[Jednostki zewnętrzne](#external-entities-passed-in-at-prediction-time) zapewniają aplikacji Luis możliwość identyfikowania i etykietowania jednostek podczas środowiska uruchomieniowego, które mogą być używane jako funkcje istniejących jednostek. |
|`options.datetimeReference`|ciąg|Tylko wersja 3|Brak domyślnego|Służy do określania [przesunięcia datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Format datetimeReference to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|wartość logiczna|Tylko wersja 3|false|Określa, czy jest używana [Jednostka zewnętrzna użytkownika (o takiej samej nazwie jak istniejąca jednostka)](#override-existing-model-predictions) lub istniejąca jednostka w modelu jest używana do przewidywania. |
|`query`|ciąg|Tylko wersja 3|Wymagany.|**W wersji 2**wypowiedź do przewidywania jest parametrem `q`. <br><br>**W wersji 3**funkcja jest przenoszona w parametrze `query`.|



## <a name="response-changes"></a>Zmiany odpowiedzi

Plik JSON odpowiedzi zapytania został zmieniony, aby umożliwić większy dostęp programistyczny do danych najczęściej używanych. 

### <a name="top-level-json-changes"></a>Zmiany w formacie JSON najwyższego poziomu



Najważniejsze właściwości JSON dla V2 to, gdy `verbose` jest ustawiona na wartość true, co zwraca wszystkie intencje i ich wyniki we właściwości `intents`:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Najważniejsze właściwości JSON dla wersji v3 to:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

Obiekt `intents` jest listą nieuporządkowaną. Nie należy zakładać pierwszego elementu podrzędnego w `intents` odpowiadającego `topIntent`. Zamiast tego należy użyć wartości `topIntent`, aby znaleźć wynik:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Zmiany schematu JSON odpowiedzi są dozwolone dla:

* Wyczyść rozróżnienie między oryginalnymi wypowiedź, `query`i zwróconymi przewidywaniami `prediction`.
* Łatwiejszy dostęp programistyczny do przewidywanych danych. Zamiast wyliczania za pomocą tablicy w wersji 2, można uzyskać dostęp do wartości według **nazwy** dla intencji i jednostek. W przypadku przewidywanych ról jednostek nazwa roli jest zwracana, ponieważ jest unikatowa w całej aplikacji.
* Typy danych, jeśli zostały określone, są przestrzegane. Wartości numeryczne nie są już zwracane jako ciągi.
* Rozróżnienie między informacjami prognozowania pierwszego priorytetu a dodatkowymi metadanymi zwracanymi w obiekcie `$instance`. 

### <a name="entity-response-changes"></a>Zmiany odpowiedzi jednostki

#### <a name="marking-placement-of-entities-in-utterances"></a>Oznaczanie rozmieszczenia jednostek w wyrażenia długości

**W wersji 2**jednostka została oznaczona w wypowiedź z `startIndex` i `endIndex`. 

**W wersji 3**jednostka jest oznaczona `startIndex` i `entityLength`.

#### <a name="access-instance-for-entity-metadata"></a>`$instance` dostępu do metadanych jednostki

Jeśli potrzebujesz metadanych jednostki, ciąg zapytania musi używać flagi `verbose=true` i odpowiedź zawiera metadane w obiekcie `$instance`. Przykłady przedstawiono w odpowiedziach JSON w poniższych sekcjach.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Każda jednostka predykcyjna jest reprezentowana jako tablica

Obiekt `prediction.entities.<entity-name>` zawiera tablicę, ponieważ każda jednostka może być przewidywalna więcej niż jeden raz w wypowiedź. 

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Wstępnie utworzone zmiany jednostek

Obiekt odpowiedzi v3 zawiera zmiany w wstępnie skompilowanych jednostkach. Przejrzyj [określone wstępnie skompilowane jednostki](luis-reference-prebuilt-entities.md) , aby dowiedzieć się więcej. 

#### <a name="list-entity-prediction-changes"></a>Wyświetl zmiany przewidywania jednostek

Plik JSON dla przewidywania jednostek listy zmienił się na tablicę tablic:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Każda tablica wewnętrzna odnosi się do tekstu wewnątrz wypowiedź. Obiekt wnętrza jest tablicą, ponieważ ten sam tekst może znajdować się na więcej niż jednej podliście jednostki listy. 

Podczas mapowania między obiektem `entities` do obiektu `$instance`, kolejność obiektów jest zachowywana dla prognoz jednostek listy.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nazwa roli jednostki zamiast nazwy jednostki 

W wersji 2 Tablica `entities` zwróciła wszystkie przewidywane jednostki o nazwie jednostki, która jest unikatowym identyfikatorem. W wersji 3, jeśli jednostka używa ról, a Prognoza dotyczy roli jednostki, identyfikator podstawowy to nazwa roli. Jest to możliwe, ponieważ nazwy ról jednostek muszą być unikatowe w całej aplikacji, w tym w przypadku innych nazw modelu (intencje, jednostki).

W poniższym przykładzie: Rozważmy wypowiedź, który zawiera tekst, `Yellow Bird Lane`. Ten tekst jest przewidywany jako rola niestandardowego `Location` jednostki `Destination`.

|Wypowiedź tekst|Nazwa jednostki|Nazwa roli|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

W wersji 2 jednostka jest identyfikowana przez _nazwę jednostki_ z rolą jako właściwość obiektu:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

W wersji 3 jednostka jest przywoływana przez _rolę jednostki_, jeśli przewidywana jest rola:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

W wersji 3, ten sam wynik z flagą `verbose` do zwrócenia metadanych jednostki:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Jednostki zewnętrzne przekazały w czasie przewidywania

Jednostki zewnętrzne zapewniają aplikacji LUIS możliwość identyfikowania i etykietowania jednostek podczas środowiska uruchomieniowego, które mogą być używane jako funkcje istniejących jednostek. Dzięki temu można używać oddzielnych i niestandardowych wyodrębniania jednostek przed wysłaniem zapytań do punktu końcowego przewidywania. Ponieważ odbywa się to w punkcie końcowym przewidywania zapytań, nie trzeba ponownie przeszkolić ani opublikować modelu.

Klient-aplikacja dostarcza własne ekstraktory jednostek przez zarządzanie dopasowaniem jednostek i Określanie lokalizacji w wypowiedź tej dopasowanej jednostki, a następnie wysłanie tych informacji przy użyciu żądania. 

Jednostki zewnętrzne są mechanizmem rozszerzania dowolnego typu jednostki, gdy nadal są używane jako sygnały do innych modeli, takich jak role, złożone i inne.

Jest to przydatne w przypadku jednostki, która ma dane dostępne tylko w środowisku uruchomieniowym przewidywania zapytań. Przykładami danych tego typu są stale zmieniające się dane lub specyficzne dla użytkownika. Jednostkę Contact LUIS można rozciągnąć z informacjami zewnętrznymi z listy kontaktów użytkownika. 

### <a name="entity-already-exists-in-app"></a>Jednostka już istnieje w aplikacji

Wartość `entityName` jednostki zewnętrznej, która została przeprowadzona w treści wpisu żądania punktu końcowego, musi już istnieć w przeszkolonej i opublikowanej aplikacji w momencie zgłoszenia żądania. Typ jednostki nie ma znaczenia, obsługiwane są wszystkie typy.

### <a name="first-turn-in-conversation"></a>Najpierw włącz konwersację

Weź pod uwagę pierwszy wypowiedź w rozmowie bot rozmowy, w której użytkownik wprowadza następujące niepełne informacje:

`Send Hazem a new message`

Żądanie od bot rozmowy do LUIS może przekazać informacje w treści wpisu dotyczące `Hazem`, aby było ono bezpośrednio dopasowane jako jeden z kontaktów użytkownika.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Odpowiedź przewidywania obejmuje tę jednostkę zewnętrzną, z innymi przewidzianymi jednostkami, ponieważ jest zdefiniowana w żądaniu.  

### <a name="second-turn-in-conversation"></a>Druga włączona konwersacja

Następny użytkownik wypowiedź do rozmowy bot używa bardziej niejasnego terminu:

`Send him a calendar reminder for the party.`

W poprzednim wypowiedź wypowiedź używa `him` jako odniesienia do `Hazem`. Rozmowa z konwersacją bot w treści wpisu umożliwia mapowanie `him` do wartości jednostki wyodrębnionej z pierwszego wypowiedź, `Hazem`.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Odpowiedź przewidywania obejmuje tę jednostkę zewnętrzną, z innymi przewidzianymi jednostkami, ponieważ jest zdefiniowana w żądaniu.  

### <a name="override-existing-model-predictions"></a>Zastąp istniejące przewidywania modelu

Właściwość `preferExternalEntities` Options określa, że jeśli użytkownik wysyła zewnętrzną jednostkę, która nakłada się na przewidywaną jednostkę o tej samej nazwie, LUIS wybiera jednostkę przekazaną lub jednostkę istniejącą w modelu. 

Rozważmy na przykład `today I'm free`zapytań. LUIS wykrywa `today` jako datetimeV2 z następującą odpowiedzią:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Jeśli użytkownik wyśle jednostkę zewnętrzną:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Jeśli `preferExternalEntities` jest ustawiona na `false`, LUIS zwraca odpowiedź, tak jakby zewnętrzna jednostka nie została wysłana. 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Jeśli `preferExternalEntities` jest ustawiona na `true`, LUIS zwraca odpowiedź, w tym:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Rozwiązanie

_Opcjonalna_ Właściwość `resolution` zwraca w odpowiedzi predykcyjnej, co pozwala na przekazywanie metadanych skojarzonych z jednostką zewnętrzną, a następnie otrzymywanie jej z powrotem w odpowiedzi. 

Podstawowym celem jest rozszerzanie wstępnie utworzonych jednostek, ale nie jest to ograniczone do tego typu jednostki. 

Właściwość `resolution` może być liczbą, ciągiem, obiektem lub tablicą:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Listy dynamiczne przechodzą w czasie przewidywania

Listy dynamiczne umożliwiają rozbudowa istniejącej, przeszkolonej i opublikowanej jednostki listy, już w aplikacji LUIS. 

Użyj tej funkcji, gdy wartości jednostki listy muszą być okresowo zmieniane. Ta funkcja umożliwia przeszerzenie już przeszkolonej i opublikowanej jednostki listy:

* W czasie żądania punktu końcowego przewidywania zapytań.
* Dla pojedynczego żądania.

Jednostka listy może być pusta w aplikacji LUIS, ale musi istnieć. Jednostka listy w aplikacji LUIS nie została zmieniona, ale zdolność przewidywania w punkcie końcowym zostanie rozszerzona, aby dołączyć do 2 list z około 1 000 elementami.

### <a name="dynamic-list-json-request-body"></a>Treść żądania JSON listy dynamicznej

Wyślij w następującej treści JSON, aby dodać nową podlistę z synonimami do listy, i przewidzieć jednostkę listy dla tekstu `LUIS`przy użyciu żądania prognozowania zapytań `POST`:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Odpowiedź przewidywania obejmuje tę jednostkę listy wraz ze wszystkimi innymi przewidywanymi jednostkami, ponieważ jest zdefiniowana w żądaniu. 

## <a name="deprecation"></a>Przestarzałe 

Interfejs API v2 nie będzie przestarzały przez co najmniej 9 miesięcy od wersji zapoznawczej v3. 

## <a name="next-steps"></a>Następne kroki

Użyj dokumentacji interfejsu API v3, aby zaktualizować istniejące wywołania REST do interfejsów API LUIS [punktów końcowych](https://aka.ms/luis-api-v3) . 
