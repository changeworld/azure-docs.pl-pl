---
title: Obsługa języków
titleSuffix: Azure Cognitive Services
description: Usługa LUIS ma wiele funkcji w ramach usługi. Nie wszystkie funkcje są w tej samej parzystości języka. Upewnij się, że funkcje, których jesteś zainteresowany są obsługiwane w kulturze języka, które są przeznaczone dla. Aplikacją usługi LUIS jest specyficzne dla kultury i nie można zmienić po jej ustawieniu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/19/2019
ms.author: diberry
ms.openlocfilehash: 8f067bc005c4de9ddc87ed598b1717f8fbb29a6a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072373"
---
# <a name="language-and-region-support-for-luis"></a>Obsługa języka i regionu dla usługi LUIS

Usługa LUIS ma wiele funkcji w ramach usługi. Nie wszystkie funkcje są w tej samej parzystości języka. Upewnij się, że funkcje, których jesteś zainteresowany są obsługiwane w kulturze języka, które są przeznaczone dla. Aplikacją usługi LUIS jest specyficzne dla kultury i nie można zmienić po jej ustawieniu.

## <a name="multi-language-luis-apps"></a>Usługa LUIS wielojęzycznych aplikacji

Jeśli potrzebujesz aplikacji klienckiej usługi LUIS wielu języków, takich jak czatbota, masz kilka opcji. Jeśli usługa LUIS obsługuje wszystkie języki, możesz tworzyć aplikacją usługi LUIS dla każdego języka. Każda aplikacja usługi LUIS ma identyfikator unikatowy aplikacji i punkt końcowy dziennika. Jeśli konieczne jest zapewnienie zrozumienia dla języka nie obsługuje usługi LUIS, można użyć języka [interfejsu API usługi Microsoft Translator](../Translator/translator-info-overview.md) tłumaczenie wypowiedź w obsługiwanym języku, Prześlij wypowiedź do punktu końcowego usługi LUIS i odbierania Wynikowy wyniki.

## <a name="languages-supported"></a>Obsługiwane języki

Usługa LUIS rozumie wypowiedzi w następujących językach:

| Język |Ustawienia regionalne  |  Wstępnie utworzone domeny | Wstępnie utworzone jednostki | Zalecenia dotyczące listy fraz | **[Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Tonacji i<br>Słowa kluczowe)|
|--|--|:--:|:--:|:--:|:--:|
| Angielski |`en-US` | ✔ | ✔  |✔|✔|
| *[Chiński](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holenderski |`nl-NL` |-|  -   |-|✔|
| Francuski (Francja) |`fr-FR` |-| ✔ |✔ |✔|
| Francuski (Kanada) |`fr-CA` |-|   -   |-|✔|
| Niemiecki |`de-DE` |-| ✔ |✔ |✔|
| Włoski |`it-IT` |-| ✔ |✔|✔|
| *[Japoński](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Tylko frazy kluczowe|
| Koreański |`ko-KR` |-|   -   |-|Tylko frazy kluczowe|
| Portugalski (Brazylia) |`pt-BR` |-| ✔ |✔ |nie wszystkie podrzędne kultur|
| Hiszpański (Hiszpania) |`es-ES` |-| ✔ |✔|✔|
| Hiszpański (Meksyk)|`es-MX` |-|  -   |✔|✔|
| Turecki | `tr-TR` |-|-|-|Tylko tonacji|


Obsługa języka jest różny dla [ze wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md) i [ze wstępnie utworzonych domen](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>* Informacje o pomocy technicznej chiński

 - W `zh-cn` kultury, LUIS oczekuje zestawu zamiast zestawu znaków tradycyjnych uproszczone znaków chińskich.
 - Nazwy intencji, jednostki, funkcje i wyrażenia regularne mogą być znakami języka chińskiego lub litery rzymskie.
 - Zobacz [odwołania ze wstępnie utworzonych domen](luis-reference-prebuilt-domains.md) informacje obsługiwanych ze wstępnie utworzonych domen w `zh-cn` kultury.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Informacje o pomocy technicznej japoński

 - LUIS nie zapewnia analizy składni i nie będzie zrozumieć różnicę między Keigo i japoński nieformalnej, dlatego należy zastosować różne poziomy formalności jako przykłady szkolenia dla aplikacji.
     - でございます nie jest taka sama jak です.
     - です nie jest taka sama jak だ.

### <a name="text-analytics-support-notes"></a>** Analiza tekstu obsługuje uwagi
Analiza tekstu zawiera keyPhrase wstępnie utworzone jednostki i wskaźniki nastrojów klientów analizy. Tylko portugalski jest obsługiwana w przypadku podhodowli: `pt-PT` i `pt-BR`. Wszystkie inne kultury są obsługiwane na poziomie podstawowym kultury. Dowiedz się więcej na temat analizy tekstu [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).

### <a name="speech-api-supported-languages"></a>Języki obsługiwane interfejsu API mowy
Zobacz mowy [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) dla języków tryb dyktowania mowy.

### <a name="bing-spell-check-supported-languages"></a>Sprawdzanie pisowni Bing obsługiwane języki
Zobacz, sprawdzanie pisowni Bing [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) Aby uzyskać listę obsługiwanych języków i stan.

## <a name="rare-or-foreign-words-in-an-application"></a>Rzadkie lub obce słowa w aplikacji
W `en-us` kultury, uzyskuje informacje o odróżnić najbardziej wyrazami, w tym żargonu usługi LUIS. W `zh-cn` kultury, uzyskuje informacje o odróżnić większości znaków chińskich usługi LUIS. Jeśli używasz rzadkich wyrazu w `en-us` lub znaków w pliku `zh-cn`, zobaczyć LUIS jest prawdopodobnie do odróżnienia tego wyrazu lub znaków, Dodaj ten wyraz lub znak do [funkcji listy fraz](luis-how-to-add-features.md). Na przykład słowa poza kulturę aplikacji — czyli obce słowa — należy dodać do funkcji listy fraz. Ta lista frazy powinna być oznaczona jako innego niż wymienne, aby wskazać, że zbiór słów rzadkich formularzy klasę, która LUIS powinien Naucz się rozpoznawać, ale nie są one synonimy lub wymienne ze sobą.

### <a name="hybrid-languages"></a>Języki hybrydowe
Języki hybrydowego łączyć wyrazów dwie kultury, takie jak angielski i chińskim. Te języki nie są obsługiwane usługi LUIS, ponieważ aplikacja jest oparta na jednej kulturze.

## <a name="tokenization"></a>Tokenizacji
Do przeprowadzenia uczenia maszynowego, usługa LUIS dzieli wypowiedź na [tokenów](luis-glossary.md#token) na podstawie kultury.

|Język|  Każdy spacji lub znaku specjalnego | poziom znaków|wyrazy złożone|[Jednostka tokenami zwracana](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chiński||✔||✔|
|Holenderski|||✔|✔|
|Angielski (en-us)|✔ ||||
|Francuski (fr-FR)|✔||||
|Francuski (fr-CA)|✔||||
|Niemiecki|||✔|✔|
|Włoski|✔||||
|Japoński||||✔|
|Koreański||✔||✔|
|Portugalski (Brazylia)|✔||||
|Hiszpański (es-ES)|✔||||
|Hiszpański (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Wersje tokenizatora niestandardowe

Następujących kultur są wersje tokenizatora niestandardowe:

|Kultura|Wersja|Przeznaczenie|
|--|--|--|
|Niemiecki<br>`de-de`|1.0.0|Tokenizes wyrazy, dzieląc je przy użyciu maszyny oparte na nauce maszynowej tokenizator który podejmie próbę podziału wyrazy złożone do ich pojedynczego składników.<br>Jeśli użytkownik wprowadzi `Ich fahre einen krankenwagen` jako wypowiedź jest przekształcane w `Ich fahre einen kranken wagen`. Zezwolenie oznakowania `kranken` i `wagen` niezależnie jako różnymi jednostkami.|
|Niemiecki<br>`de-de`|1.0.2|Tokenizes wyrazy, dzieląc je na miejsca do magazynowania.<br> Jeśli użytkownik wprowadzi `Ich fahre einen krankenwagen` jako wypowiedź pozostaje pojedynczy token. Ten sposób `krankenwagen` jest oznaczony jako pojedynczy element. |

### <a name="migrating-between-tokenizer-versions"></a>Migrowanie między wersjami tokenizatora
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID. 

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

Tokenizacji odbywa się na poziomie aplikacji. Nie jest obsługiwane dla tokenizacji poziomie wersji. 

[Importowanie pliku jako nową aplikację](luis-how-to-start-new-app.md#import-an-app-from-file), zamiast wersji. Ta akcja oznacza, że nowa aplikacja ma identyfikator innej aplikacji, ale korzysta z wersji tokenizatora określone w pliku. 