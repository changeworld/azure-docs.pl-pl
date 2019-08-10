---
title: Migracja interfejsu API z wersji 2 do v3
titleSuffix: Azure Cognitive Services
description: Interfejsy API punktu końcowego w wersji 3 zostały zmienione. Skorzystaj z tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do interfejsów API punktu końcowego w wersji 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: diberry
ms.openlocfilehash: 82285b27822b6c93f8efc24579bb99c308649ac0
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932670"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Wersja zapoznawcza: Migrowanie do interfejsu API w wersji 3. x dla aplikacji LUIS

Interfejsy API prognozy punktu końcowego przewidywania zapytań zostały zmienione. Skorzystaj z tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do interfejsów API punktu końcowego w wersji 3. 

Ten interfejs API v3 udostępnia następujące nowe funkcje, takie jak znaczące zmiany żądania i/lub odpowiedzi JSON: 

* [Jednostki zewnętrzne](#external-entities-passed-in-at-prediction-time)
* [Listy dynamiczne](#dynamic-lists-passed-in-at-prediction-time)
* [Wstępnie skompilowane zmiany JSON jednostki](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

[Żądanie](#request-changes) punktu końcowego przewidywania zapytań i [odpowiedź](#response-changes) ma znaczące zmiany w zakresie obsługi nowych funkcji wymienionych powyżej, w tym:

* [Zmiany obiektu odpowiedzi](#top-level-json-changes)
* [Odwołania do nazwy roli jednostki zamiast nazwy jednostki](#entity-role-name-instead-of-entity-name)
* [Właściwości do oznaczania jednostek w wyrażenia długości](#marking-placement-of-entities-in-utterances)

Następujące funkcje LUIS nie są **obsługiwane** w interfejsie API v3:

* sprawdzanie pisowni Bing wersji 7

[Dokumentacja referencyjna](https://aka.ms/luis-api-v3) jest dostępna w wersji 3.

## <a name="endpoint-url-changes-by-slot-name"></a>Zmiany adresu URL punktu końcowego według nazwy miejsca

Format wywołania HTTP punktu końcowego v3 został zmieniony.

|METHOD|URL|
|--|--|
|GET|https://<b>{region}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0 — wersja</b>zapoznawcza/Apps/<b>{App-ID}</b>/Slots/<b>{slot-Name}</b>/Predict? Query =<b>{Query}</b>|
|POST|https://<b>{region}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0 — wersja</b>zapoznawcza/Apps/<b>{App-ID}</b>/Slots/<b>{slot-Name}</b>/Predict|
|||

Prawidłowe wartości dla gniazd:

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>Zmiany adresu URL punktu końcowego według identyfikatora wersji

Jeśli chcesz wykonać zapytanie według wersji, musisz najpierw [opublikować za pośrednictwem interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) za pomocą `"directVersionPublish":true`. Zbadaj punkt końcowy odwołujący się do identyfikatora wersji zamiast nazwy gniazda.


|METHOD|URL|
|--|--|
|GET|https://<b>{region}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0 — wersja</b>zapoznawcza/Apps/<b>{App-ID}</b>/Versions/<b>{Version-ID}</b>/Predict? Query =<b>{Query}</b>|
|POST|https://<b>{region}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0 — wersja</b>zapoznawcza/Apps/<b>{App-ID}</b>/Versions/<b>{Version-ID}</b>/Predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Wstępnie utworzone jednostki z nowym kodem JSON

Zmiany obiektu odpowiedzi v3 obejmują [wstępnie skompilowane jednostki](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Żądanie zmian 

### <a name="query-string-parameters"></a>Parametry ciągu zapytania

Interfejs API v3 ma inne parametry ciągu zapytania.

|Nazwa parametru|Type|Version|Domyślny|Cel|
|--|--|--|--|--|
|`log`|boolean|WERSJA 2 & V3|false|Zapisz zapytanie w pliku dziennika.| 
|`query`|ciąg|Tylko wersja 3|Brak domyślnej — jest to wymagane w żądaniu GET|**W wersji 2**wypowiedź do przewidywania jest `q` parametrem. <br><br>**W wersji 3**funkcja jest przenoszona do `query` parametru.|
|`show-all-intents`|boolean|Tylko wersja 3|false|Zwróć wszystkie intencje z odpowiednim wynikiem w obiekcie **przewidywania. intencje** . Intencje są zwracane jako obiekty w obiekcie nadrzędnym `intents` . Pozwala to na dostęp programistyczny bez konieczności wyszukiwania zamiaru w tablicy: `prediction.intents.give`. W wersji 2 te zostały zwrócone w tablicy. |
|`verbose`|boolean|WERSJA 2 & V3|false|**W wersji 2**, gdy ustawiono wartość true, wszystkie przewidywane intencje zostały zwrócone. Jeśli potrzebujesz wszystkich przewidywanych intencji, użyj parametru `show-all-intents`v3.<br><br>**W wersji 3**ten parametr zawiera tylko szczegóły metadanych jednostki przewidywania jednostek.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>Treść pliku JSON przewidywania zapytania dla `POST` żądania

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

|Właściwość|Type|Version|Domyślny|Cel|
|--|--|--|--|--|
|`dynamicLists`|array|Tylko wersja 3|Nie jest wymagane.|[Listy dynamiczne](#dynamic-lists-passed-in-at-prediction-time) umożliwiają rozbudowa istniejącej, przeszkolonej i opublikowanej jednostki listy, już w aplikacji Luis.|
|`externalEntities`|array|Tylko wersja 3|Nie jest wymagane.|[Jednostki zewnętrzne](#external-entities-passed-in-at-prediction-time) zapewniają aplikacji Luis możliwość identyfikowania i etykietowania jednostek podczas środowiska uruchomieniowego, które mogą być używane jako funkcje istniejących jednostek. |
|`options.datetimeReference`|ciąg|Tylko wersja 3|Brak domyślnego|Służy do określania [przesunięcia datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Format datetimeReference to [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.overridePredictions`|boolean|Tylko wersja 3|false|Określa, czy jest używana [Jednostka zewnętrzna użytkownika (o takiej samej nazwie jak istniejąca jednostka)](#override-existing-model-predictions) lub istniejąca jednostka w modelu jest używana do przewidywania. |
|`query`|ciąg|Tylko wersja 3|Wymagane.|**W wersji 2**wypowiedź do przewidywania jest `q` parametrem. <br><br>**W wersji 3**funkcja jest przenoszona do `query` parametru.|



## <a name="response-changes"></a>Zmiany odpowiedzi

Plik JSON odpowiedzi zapytania został zmieniony, aby umożliwić większy dostęp programistyczny do danych najczęściej używanych. 

### <a name="top-level-json-changes"></a>Zmiany w formacie JSON najwyższego poziomu

Najważniejsze właściwości JSON dla V2 to, gdy `verbose` ma wartość true, co zwraca wszystkie intencje i ich wyniki `intents` we właściwości:

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
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

<!--
The `alteredQuery` contains spelling corrections. This corresponds to the V2 API property `alteredQuery`.  
-->

`intents` Obiekt jest listą nieuporządkowaną. Nie należy zakładać pierwszego elementu podrzędnego `intents` w odnosi `topIntent`się do. Zamiast tego należy użyć `topIntent` wartości, aby znaleźć wynik:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Zmiany schematu JSON odpowiedzi są dozwolone dla:

* Wyczyść rozróżnienie między oryginalnymi wypowiedź `query`i zwróciło `prediction`prognozowanie.
* Łatwiejszy dostęp programistyczny do przewidywanych danych. Zamiast wyliczania za pomocą tablicy w wersji 2, można uzyskać dostęp do wartości według **nazwy** dla intencji i jednostek. W przypadku przewidywanych ról jednostek nazwa roli jest zwracana, ponieważ jest unikatowa w całej aplikacji.
* Typy danych, jeśli zostały określone, są przestrzegane. Wartości numeryczne nie są już zwracane jako ciągi.
* Rozróżnienie między informacjami prognozowania pierwszego priorytetu a dodatkowymi metadanymi `$instance` zwracanymi w obiekcie. 

### <a name="access-instance-for-entity-metadata"></a>Dostęp `$instance` do metadanych jednostki

Jeśli potrzebujesz metadanych jednostki, ciąg zapytania musi używać `verbose=true` flagi, a odpowiedź zawiera metadane `$instance` w obiekcie. Przykłady przedstawiono w odpowiedziach JSON w poniższych sekcjach.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Każda jednostka predykcyjna jest reprezentowana jako tablica

`prediction.entities.<entity-name>` Obiekt zawiera tablicę, ponieważ każda jednostka może być przewidywalna więcej niż jeden raz w wypowiedź. 

### <a name="list-entity-prediction-changes"></a>Wyświetl zmiany przewidywania jednostek

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

Podczas mapowania między `entities` obiektem `$instance` a obiektem kolejność obiektów jest zachowywana dla prognoz jednostek listy.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Nazwa roli jednostki zamiast nazwy jednostki 

W wersji 2 `entities` tablica zwróciła wszystkie przewidywane jednostki o nazwie jednostki, która jest unikatowym identyfikatorem. W wersji 3, jeśli jednostka używa ról, a Prognoza dotyczy roli jednostki, identyfikator podstawowy to nazwa roli. Jest to możliwe, ponieważ nazwy ról jednostek muszą być unikatowe w całej aplikacji, w tym w przypadku innych nazw modelu (intencje, jednostki).

W poniższym przykładzie: Rozważmy wypowiedź, który zawiera tekst, `Yellow Bird Lane`. Ten tekst jest przewidywany jako `Location` `Destination`rola jednostki niestandardowej.

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

W wersji 3 jednostka jest przywoływana przez _rolę jednostki_ , jeśli przewidywana jest rola:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

W wersji 3, ten sam wynik z `verbose` flagą do zwrócenia metadanych jednostki:

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

Wartość `entityName` dla jednostki zewnętrznej, która została przeniesiona do treści żądania punktu końcowego, musi już istnieć w nauczonej i opublikowanej aplikacji w momencie zgłoszenia żądania. Typ jednostki nie ma znaczenia, obsługiwane są wszystkie typy.

### <a name="first-turn-in-conversation"></a>Najpierw włącz konwersację

Weź pod uwagę pierwszy wypowiedź w rozmowie bot rozmowy, w której użytkownik wprowadza następujące niepełne informacje:

`Send Hazem a new message`

Żądanie od bot rozmowy do Luis może przekazać informacje w treści `Hazem` wpisu, aby było ono bezpośrednio dopasowane jako jeden z kontaktów użytkownika.

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

W poprzednim wypowiedź, wypowiedź używa `him` jako odwołanie do. `Hazem` Konwersacja rozmówcy bot w treści wpisu można mapować `him` na wartość jednostki wyodrębnioną z pierwszego wypowiedź,. `Hazem`

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

Właściwość `overridePredictions` Options określa, że jeśli użytkownik wysyła jednostkę zewnętrzną, która pokrywa się z przewidywaną jednostką o tej samej nazwie, Luis wybiera jednostkę przekazaną lub jednostkę istniejącą w modelu. 

Rozważmy na przykład zapytanie `today I'm free`. Luis wykrywa `today` jako datetimeV2 z następującą odpowiedzią:

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

Jeśli jest ustawiona na `false`, Luis zwraca odpowiedź, tak jakby zewnętrzna jednostka nie została wysłana. `overridePredictions` 

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

Jeśli jest ustawiona na `true`, Luis zwraca odpowiedź, w tym: `overridePredictions`

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Rozwiązanie

_Opcjonalna_ `resolution` Właściwość zwraca w odpowiedzi predykcyjnej, co pozwala na przekazywanie metadanych skojarzonych z jednostką zewnętrzną, a następnie otrzymywanie jej z powrotem w odpowiedzi. 

Podstawowym celem jest rozszerzanie wstępnie utworzonych jednostek, ale nie jest to ograniczone do tego typu jednostki. 

`resolution` Właściwość może być liczbą, ciągiem, obiektem lub tablicą:

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

Wyślij w następującej treści JSON, aby dodać nową podlistę z synonimami do listy i przewidzieć jednostkę listy dla tekstu, `LUIS` `POST` przy użyciu żądania przewidywania zapytania:

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

Odpowiedź przewidywania obejmuje tę jednostkę listy wraz ze wszystkimi innymi przewidywanymi jednostkami, ponieważ jest zdefiniowana w żądaniu. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset zmieniono nazwę na datetimeReference

**W wersji 2** `timezoneOffset` [parametr](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) jest wysyłany w żądaniu predykcyjnym jako parametr ciągu zapytania, niezależnie od tego, czy żądanie jest wysyłane jako żądanie Get lub post. 

**W wersji 3**ta sama funkcjonalność jest dostarczana z parametrem `datetimeReference`post Body. 

## <a name="marking-placement-of-entities-in-utterances"></a>Oznaczanie rozmieszczenia jednostek w wyrażenia długości

**W wersji 2**jednostka została oznaczona w wypowiedź z `startIndex` i. `endIndex` 

**W wersji 3**jednostka jest oznaczona za pomocą `startIndex` i `entityLength`.

## <a name="deprecation"></a>Przestarzałe 

Interfejs API v2 nie będzie przestarzały przez co najmniej 9 miesięcy od wersji zapoznawczej v3. 

## <a name="next-steps"></a>Następne kroki

Użyj dokumentacji interfejsu API v3, aby zaktualizować istniejące wywołania REST do interfejsów API LUIS [punktów końcowych](https://aka.ms/luis-api-v3) . 