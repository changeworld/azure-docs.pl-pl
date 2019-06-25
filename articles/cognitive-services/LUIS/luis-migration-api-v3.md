---
title: Aby migracja interfejsu API w wersji 3 w wersji 2
titleSuffix: Azure Cognitive Services
description: Punkt końcowy w wersji 3 interfejsy API zostały zmienione. Użyj tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do endpoint w wersji 3 interfejsów API.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: fb4cf119195b3be23dc8f2cb98bd019769583473
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341835"
---
# <a name="preview-migrate-to-api-version-3x--for-luis-apps"></a>Wersja zapoznawcza: Migrowanie do interfejsu API w wersji 3.x dla aplikacji usługi LUIS

Zmieniły się interfejsów API endpoint prognoz zapytania. Użyj tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do endpoint w wersji 3 interfejsów API. 

Ten interfejs API w wersji 3 zawiera poniższe nowe funkcje, które obejmują znaczne zmiany żądań i/lub w odpowiedzi JSON: 

* [Podmiotów zewnętrznych](#external-entities-passed-in-at-prediction-time)
* [Listy dynamiczne](#dynamic-lists-passed-in-at-prediction-time)
* [Zmiany JSON wstępnie utworzone jednostki](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

Endpoint prognoz zapytania [żądania](#request-changes) i [odpowiedzi](#response-changes) ma znaczące zmiany i obsługują nowe funkcje wymienione powyżej, w tym następujące czynności:

* [Zmiany obiektu odpowiedzi](#top-level-json-changes)
* [Odwołania do nazwy roli jednostki zamiast nazwy jednostki](#entity-role-name-instead-of-entity-name)
* [Właściwości, aby oznaczyć jednostek w wypowiedzi](#marking-placement-of-entities-in-utterances)

Następujące funkcje usługi LUIS są **nieobsługiwane** w interfejsie API w wersji 3:

* Sprawdzanie pisowni Bing w wersji 7

[Dokumentację referencyjną](https://aka.ms/luis-api-v3) jest dostępna dla V3.

## <a name="prebuilt-entities-with-new-json"></a>Wstępnie utworzone jednostki z nowego formatu JSON.

Zmiany obiektu odpowiedzi V3 obejmują [ze wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Żądanie zmian 

### <a name="query-string-parameters"></a>Parametry ciągu zapytania

Interfejs API w wersji 3 ma parametry ciągu zapytania różne.

|Nazwy parametrów|Typ|Version|Domyślne|Przeznaczenie|
|--|--|--|--|--|
|`log`|boolean|V2 I V3|false|Query Store w pliku dziennika.| 
|`query`|string|Tylko w wersji 3|Brak wartości domyślnej — jest to wymagane w przypadku żądania GET|**W wersji 2**, trwa wypowiedź do można przewidzieć `q` parametru. <br><br>**W wersji 3**, funkcji jest przekazywany w `query` parametru.|
|`show-all-intents`|boolean|Tylko w wersji 3|false|Zwróć wszystkie intencji z odpowiedni wynik w **prediction.intents** obiektu. Intents są zwracane jako obiekty w obiekcie nadrzędnym `intents` obiektu. Dzięki temu dostęp programowy bez konieczności znaleźć zamiar w tablicy: `prediction.intents.give`. W wersji 2 te zostały zwrócone w tablicy. |
|`verbose`|boolean|V2 I V3|false|**W wersji 2**, gdy jest ustawiona na wartość true, wszystkie przewidywane intencji zostały zwrócone. Wszystkie przewidywane intencji, należy użyć param V3 z `show-all-intents`.<br><br>**W wersji 3**, ten parametr tylko szczegółów jednostki metadanych jednostki przewidywania.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>Zapytanie prediction JSON treści dla `POST` żądania

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Właściwość|Typ|Version|Domyślne|Przeznaczenie|
|--|--|--|--|--|
|`dynamicLists`|tablica|Tylko w wersji 3|Nie jest wymagane.|[Listy dynamiczne](#dynamic-lists-passed-in-at-prediction-time) pozwalają rozszerzyć istniejącą jednostkę listy przeszkolony i opublikowanych już w aplikacji usługi LUIS.|
|`externalEntities`|tablica|Tylko w wersji 3|Nie jest wymagane.|[Podmiotów zewnętrznych](#external-entities-passed-in-at-prediction-time) pozwalają aplikacją usługi LUIS na identyfikowanie i oznaczanie jednostek podczas wykonywania, który może służyć jako funkcje do istniejących jednostek. |
|`options.datetimeReference`|string|Tylko w wersji 3|Brak wartości domyślnej|Używany do określenia [przesunięcie datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity).|
|`options.overridePredictions`|boolean|Tylko w wersji 3|false|Określa, jeśli użytkownika [zewnętrznej jednostki (na taką samą nazwę jak istniejąca jednostka)](#override-existing-model-predictions) jest używany lub istniejącej jednostki w modelu służy do prognozowania. |
|`query`|string|Tylko w wersji 3|Wymagany.|**W wersji 2**, trwa wypowiedź do można przewidzieć `q` parametru. <br><br>**W wersji 3**, funkcji jest przekazywany w `query` parametru.|



## <a name="response-changes"></a>Zmiany w odpowiedzi

Odpowiedzi na zapytanie JSON zmienić, aby umożliwić większą programowy dostęp do danych najczęściej używany. 

### <a name="top-level-json-changes"></a>Najważniejsze zmiany poziomu JSON

Najważniejsze właściwości JSON w wersji 2, gdy `verbose` jest ustawiona na wartość true, które zwraca wszystkie intencje i ich wyniki w `intents` właściwości:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Najważniejsze właściwości JSON w wersji 3 są:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

`intents` Obiekt jest nieuporządkowaną listę. Nie należy zakładać, pierwszy element podrzędny w `intents` odpowiada `topIntent`. Zamiast tego należy użyć `topIntent` wartość do znalezienia wynik:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Zezwalaj na zmiany schematu JSON odpowiedzi dla:

* Wyczyść rozróżnienie między oryginalnego wypowiedź `query`i zwrócony przewidywania, `prediction`.
* Łatwiejsze programowy dostęp do przewidywanych danych. Zamiast wyliczania tablicy w wersji 2, mogą uzyskać dostęp do wartości przez **o nazwie** zarówno intencje i podmioty. Dla ról jednostki przewidywane Nazwa roli jest zwracany, ponieważ jest ona unikatowa w obrębie całej aplikacji.
* Typy danych, jeśli określony, są przestrzegane. Wartości numeryczne już nie są zwracane jako ciągi.
* Rozróżnienie między pierwszej priorytet prognozowania informacji i dodatkowe metadane są zwracane w `$instance` obiektu. 

### <a name="access-instance-for-entity-metadata"></a>Dostęp `$instance` metadanych jednostki

Jeśli potrzebujesz metadane jednostki, ciąg zapytania musi używać `verbose=true` flagę i odpowiedzi zawiera metadane w `$instance` obiektu. W odpowiedzi JSON w poniższych sekcjach przedstawiono przykłady.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Każda jednostka przewidywany jest reprezentowany jako tablica

`prediction.entities.<entity-name>` Obiektu zawiera tablicę, ponieważ każda jednostka można przewidzieć więcej niż jeden raz w wypowiedź. 

### <a name="list-entity-prediction-changes"></a>Lista jednostki prognozowania zmian

Plik JSON do prognozowania jednostki lista została zmieniona w postaci tablicy tablic:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Każda tablica posługiwanie się nimi odpowiada tekst wewnątrz wypowiedź. Posługiwanie się nimi obiekt jest tablicą, ponieważ ten sam tekst może znajdować się w więcej niż jeden podlistę obiektami listy. 

Podczas mapowania między `entities` obiekt `$instance` obiektu kolejność obiektów są zachowywane na potrzeby prognoz jednostki listy.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Nazwa roli jednostki zamiast nazwy jednostki 

W wersji 2 `entities` tablica zwrócona przewidywane jednostki o nazwie jednostki jest unikatowy identyfikator. W wersji 3 Jeśli jednostka ról i Prognozowanie dla roli jednostki podstawowy identyfikator jest nazwa roli. Jest to możliwe, ponieważ nazwy ról jednostki musi być unikatowa w całej aplikacji, łącznie z nazwami innych modelu (, podmiotów).

W poniższym przykładzie: należy wziąć pod uwagę wypowiedź, który zawiera tekst, `Yellow Bird Lane`. Ten tekst jest przewidzieć rolę niestandardową `Location` roli jednostki `Destination`.

|Tekstowe wypowiedź|Nazwa jednostki|Nazwa roli|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

W wersji 2 jednostki jest identyfikowany przez _nazwa jednostki_ z rolą jako właściwość obiektu:

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

W wersji 3, odwołuje się jednostki _roli jednostki_, jeśli jest prognozowanie, dla roli:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

W wersji 3, jest taka sama wyniku o `verbose` flagi do zwracania metadanych jednostki:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Podmiotów zewnętrznych przekazany w czasie prognoz

Podmiotów zewnętrznych pozwalają aplikacją usługi LUIS na identyfikowanie i oznaczanie jednostek podczas wykonywania, który może służyć jako funkcje do istniejących jednostek. Dzięki temu można używać własnego w oddzielnych i niestandardowe wyodrębniające przed wysłaniem kwerendy do punktu końcowego usługi prognozowania. Odbywa się w punkcie końcowym prognozowania zapytania, dlatego nie musisz wytrenujesz i opublikowaniu modelu.

Aplikacja kliencka jest zapewnienie ekstraktor własne jednostki, umożliwiając zarządzanie dopasowania jednostki i określanie lokalizacji w ramach wypowiedź tej jednostki dopasowane, a następnie wysyłając te informacje z żądaniem. 

Podmiotów zewnętrznych jest mechanizm rozszerzania dowolnego typu jednostki, gdy był używany jako sygnały z innymi modelami, takich jak role, złożone i inne.

Jest to przydatne dla jednostki, która ma danych dostępnych tylko w czasie wykonywania prognoz zapytania. Przykłady tego typu dane są często zmieniane, danych lub dla poszczególnych użytkowników. Można rozszerzyć, skontaktuj się z jednostką usługi LUIS z zewnętrznych informacji z listy kontaktów użytkownika. 

### <a name="entity-already-exists-in-app"></a>Jednostka już istnieje w aplikacji

Wartość `entityName` dla jednostki zewnętrznych, przekazanego żądania punktu końcowego treść wpisu, musi już istnieć w aplikacji przeszkolony i opublikowane w czasie, żądanie jest wysyłane. Obsługiwane są wszystkie typy, typ jednostki nie ma znaczenia.

### <a name="first-turn-in-conversation"></a>Najpierw włączyć konwersacji

Należy wziąć pod uwagę pierwszy wypowiedź, w którym użytkownik wprowadza następujące informacje niekompletne konwersacji bot rozmowy:

`Send Hazem a new message`

Żądanie czatbot LUIS można przekazać informacje zawarte w treści wpisu o `Hazem` więc bezpośrednio jest dopasowywany jako jeden z kontaktów użytkownika.

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

Prognozowanie odpowiedzi zawiera tej jednostki zewnętrznych, za pomocą wszystkich innych przewidywane jednostek, ponieważ jest on zdefiniowany w żądaniu.  

### <a name="second-turn-in-conversation"></a>Po drugie, wyłącz w konwersacji

Dalej wypowiedź użytkownika do czatbot używa bardziej niejasne termin:

`Send him a calendar reminder for the party.`

W poprzednim wypowiedź, używa wypowiedź `him` jako odwołanie do `Hazem`. Treść wpisu na bota konwersacji rozmowy można mapować `him` wartości jednostki wyodrębnione z pierwszym wypowiedź, `Hazem`.

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

Prognozowanie odpowiedzi zawiera tej jednostki zewnętrznych, za pomocą wszystkich innych przewidywane jednostek, ponieważ jest on zdefiniowany w żądaniu.  

### <a name="override-existing-model-predictions"></a>Zastąp istniejące określane są przewidywania modelu

`overridePredictions` Właściwość opcje Określa, że jeśli użytkownik prześle jednostki zewnętrznej, która nakłada się na dostęp do przewidywanych jednostki o takiej samej nazwie, LUIS wybiera jednostki przekazanej lub istnieje w modelu. 

Rozważmy na przykład zapytanie `today I'm free`. Usługa LUIS wykrywa `today` jako datetimeV2 z następującą odpowiedź:

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

Jeśli użytkownik prześle zewnętrznej jednostki:

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

Jeśli `overridePredictions` ustawiono `false`, LUIS, zwraca odpowiedź tak, jakby nie zostały wysłane zewnętrznej jednostki. 

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

Jeśli `overridePredictions` ustawiono `true`, LUIS, zwraca odpowiedź, w tym:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Rozwiązanie

_Opcjonalne_ `resolution` właściwość zwraca w odpowiedzi prognozowania, dzięki czemu możesz przekazać metadane skojarzone z zewnętrznej jednostki, a następnie otrzymasz go wycofać w odpowiedzi. 

Głównym celem jest rozszerzenie wstępnie utworzone jednostki, ale nie jest ograniczona do tego typu jednostki. 

`resolution` Właściwość może być liczbą, ciągiem, obiekt lub tablica:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Listy dynamiczne przekazany w czasie prognoz

Listy dynamiczne pozwalają rozszerzyć istniejącą jednostkę listy przeszkolony i opublikowanych już w aplikacji usługi LUIS. 

Użyj tej funkcji, gdy wartości jednostki listy muszą okresowo zmieniane. Ta funkcja umożliwia rozszerzenie jednostki już uczony i opublikowane listy:

* W momencie żądania punktu końcowego prognozy zapytania.
* Dla pojedynczego żądania.

Jednostka listy może być pusty w aplikacji usługi LUIS, ale nie istnieje. Jednostka listy w aplikacji usługi LUIS nie ulega zmianie, ale rozszerzone możliwości prognozowania w punkcie końcowym, aby maksymalnie 2 listy z około 1000 elementów.

### <a name="dynamic-list-json-request-body"></a>Dynamiczna lista treści żądania JSON

Wyślij w następujących treść kodu JSON można dodać nowego podlisty przy użyciu synonimów do listy i przewidywanie obiektami listy dla tekstu, `LUIS`, za pomocą `POST` żądania prognozy kwerendy:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
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

Prognozowanie odpowiedzi zawiera danej jednostce listy, przy użyciu wszystkich innych przewidywane jednostek, ponieważ jest on zdefiniowany w żądaniu. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset zmieniona na datetimeReference

**W wersji 2**, `timezoneOffset` [parametru](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) wysyłane żądania prognozy jako parametr ciągu zapytania, niezależnie od tego, jeśli żądanie jest wysyłane jako żądania GET lub POST. 

**W wersji 3**, taką samą funkcjonalność jest dostarczana z parametr treści wpisu `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Oznaczanie umieszczanie jednostek w wypowiedzi

**W wersji 2**, jednostki został oznaczony w wypowiedź z `startIndex` i `endIndex`. 

**W wersji 3**, jednostka jest oznaczona za pomocą `startIndex` i `entityLength`.

## <a name="deprecation"></a>Przestarzałe 

Interfejsy API wersji 2 nie zostaną wycofane przez co najmniej 9 miesięcy od wersji 3 w wersji zapoznawczej. 

## <a name="next-steps"></a>Kolejne kroki

Użyj dokumentację API w wersji 3, aby zaktualizować istniejące REST wywołania usługi LUIS [punktu końcowego](https://aka.ms/luis-api-v3) interfejsów API. 