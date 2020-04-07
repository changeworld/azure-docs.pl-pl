---
title: Obsługa języka — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS ma wiele funkcji w ramach usługi. Nie wszystkie funkcje są w tym samym parzystości języka. Upewnij się, że funkcje, które Cię interesują, są obsługiwane w kulturze języka, na którą kierujesz reklamy. Aplikacja usługi LUIS jest specyficzna dla kultury i nie można jej zmienić po jej ustawieniu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 82efa70b30e829cfedd0b1fa7a21fd06949aa6d5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744147"
---
# <a name="language-and-region-support-for-luis"></a>Obsługa języka i regionu usługi LUIS

Usługa LUIS ma wiele funkcji w ramach usługi. Nie wszystkie funkcje są w tym samym parzystości języka. Upewnij się, że funkcje, które Cię interesują, są obsługiwane w kulturze języka, na którą kierujesz reklamy. Aplikacja usługi LUIS jest specyficzna dla kultury i nie można jej zmienić po jej ustawieniu.

## <a name="multi-language-luis-apps"></a>Aplikacje usługi LUIS w wielu językach

Jeśli potrzebujesz wielojęzycznej aplikacji klienckiej usługi LUIS, takiej jak chatbot, masz kilka opcji. Jeśli usługa LUIS obsługuje wszystkie języki, należy opracować aplikację usługi LUIS dla każdego języka. Każda aplikacja usługi LUIS ma unikatowy identyfikator aplikacji i dziennika punktu końcowego. Jeśli trzeba zapewnić zrozumienie języka dla języka usługi LUIS nie obsługuje, można użyć [interfejsu API usługi Microsoft Translator](../Translator/translator-info-overview.md) do tłumaczenia wypowiedź na obsługiwany język, przesłać wypowiedź do punktu końcowego usługi LUIS i otrzymać wynikowe wyniki.

## <a name="languages-supported"></a>Obsługiwane języki

Usługa LUIS rozumie wypowiedzi w następujących językach:

| Język |Ustawienia regionalne  |  Wstępnie skompilowana domena | Wstępnie skompilowana jednostka | Rekomendacje listy fraz | **[Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentyment i<br>Słowa kluczowe)|
|--|--|:--:|:--:|:--:|:--:|
| amerykański angielski |`en-US` | ✔ | ✔  |✔|✔|
| Arabski (podgląd - nowoczesny standard arabski) |`ar-AR`|-|-|-|-|
| *[Chiński](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Niderlandzki |`nl-NL` |✔|-|-|✔|
| Francuski (Francja) |`fr-FR` |✔| ✔ |✔ |✔|
| francuski (Kanada) |`fr-CA` |-|-|-|✔|
| Niemiecki |`de-DE` |✔| ✔ |✔ |✔|
| Gudżarati | `gu-IN`|-|-|-|-|
| Hindi | `hi-IN`|-|✔|-|-|
| Włoski |`it-IT` |✔| ✔ |✔|✔|
| *[Japoński](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Tylko fraza kluczowa|
| Koreański |`ko-KR` |✔|-|-|Tylko fraza kluczowa|
| Marathi | `mr-IN`|-|-|-|-|
| portugalski (Brazylia) |`pt-BR` |✔| ✔ |✔ |nie wszystkie podkultury|
| hiszpański (Hiszpania) |`es-ES` |✔| ✔ |✔|✔|
| Hiszpański (Meksyk)|`es-MX` |-|-|✔|✔|
| Tamilski | `ta-IN`|-|-|-|-|
| Telugu | `te-IN`|-|-|-|-|
| Turecki | `tr-TR` |✔|✔|-|Tylko sentyment|




Obsługa języka różni się w przypadku [wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md) i [wstępnie utworzonych domen](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*Japońskie notatki o wsparciu

 - Ponieważ usługa LUIS nie zapewnia analizy składniowej i nie rozumie różnicy między Keigo i nieformalnym japońskim, należy uwzględnić różne poziomy formalności jako przykłady szkolenia dla aplikacji.
     - Japonki nie są takie same jak japonki.
     - To nie to samo co ja.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Języki obsługiwane przez interfejs API mowy
Zobacz [języki obsługiwane przez](../speech-service/speech-to-text.md) mowę dla języków trybu dyktowania mowy.

### <a name="bing-spell-check-supported-languages"></a>Obsługiwane języki sprawdzania pisowni bing
Zobacz Obsługiwane [języki](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) sprawdzania pisowni bing, aby uzyskać listę obsługiwanych języków i stanu.

## <a name="rare-or-foreign-words-in-an-application"></a>Rzadkie lub obce słowa w aplikacji
W `en-us` kulturze usługi LUIS uczy się rozróżniać większość angielskich słów, w tym slangu. W `zh-cn` kulturze usługi LUIS uczy się rozróżniać większość chińskich znaków. Jeśli używasz rzadkiego `en-us` wyrazu `zh-cn`w programie , a usługa LUIS wydaje się nie zdolna do odróżnienia tego wyrazu lub znaku, można dodać to słowo lub znak do [funkcji listy fraz](luis-how-to-add-features.md). Na przykład wyrazy spoza kultury aplikacji - czyli słowa obce - powinny zostać dodane do funkcji listy fraz.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Języki hybrydowe
Języki hybrydowe łączą słowa z dwóch kultur, takich jak angielski i chiński. Te języki nie są obsługiwane w usłudze LUIS, ponieważ aplikacja jest oparta na jednej kultury.

## <a name="tokenization"></a>Tokenizacja
Aby wykonać uczenie maszynowe, usługa LUIS dzieli wypowiedź na [tokeny](luis-glossary.md#token) na podstawie kultury.

|Język|  każda przestrzeń lub znak specjalny | poziom znaku|wyrazy złożone
|--|:--:|:--:|:--:|
|Arabski|✔|||
|Chiński||✔||
|Niderlandzki|✔||✔|
|Angielski (en-us)|✔ |||
|Francuski (fr-FR)|✔|||
|Francuski (fr-CA)|✔|||
|Niemiecki|✔||✔|
|Gudżarati|✔|||
|Hindi|✔|||
|Włoski|✔|||
|Japoński|||✔
|Koreański||✔||
|Marathi|✔|||
|portugalski (Brazylia)|✔|||
|Hiszpański (es-ES)|✔|||
|Hiszpański (es-MX)|✔|||
|Tamilski|✔|||
|Telugu|✔|||
|Turecki|✔|||


### <a name="custom-tokenizer-versions"></a>Niestandardowe wersje tokenizatora

Następujące kultury mają niestandardowe wersje tokenizatora:

|Culture|Wersja|Przeznaczenie|
|--|--|--|
|Niemiecki<br>`de-de`|1.0.0|Tokenizes wyrazów przez dzielenie ich przy użyciu tokenizatora opartego na uczeniu maszynowym, który próbuje podzielić wyrazy złożone na ich pojedyncze składniki.<br>Jeśli użytkownik wprowadzi `Ich fahre einen krankenwagen` jako wypowiedź, zostanie `Ich fahre einen kranken wagen`on przekierowyny do . Zezwalanie na `kranken` `wagen` oznaczanie i niezależnie jako różne podmioty.|
|Niemiecki<br>`de-de`|1.0.2|Tokenizes wyrazy przez dzielenie ich na przestrzenie.<br> Jeśli użytkownik wprowadzi `Ich fahre einen krankenwagen` jako wypowiedź, pozostaje pojedynczy token. W `krankenwagen` ten sposób jest oznaczony jako jeden podmiot. |
|Niderlandzki<br>`de-de`|1.0.0|Tokenizes wyrazów przez dzielenie ich przy użyciu tokenizatora opartego na uczeniu maszynowym, który próbuje podzielić wyrazy złożone na ich pojedyncze składniki.<br>Jeśli użytkownik wprowadzi `Ik ga naar de kleuterschool` jako wypowiedź, zostanie `Ik ga naar de kleuter school`on przekierowyny do . Zezwalanie na `kleuter` `school` oznaczanie i niezależnie jako różne podmioty.|
|Niderlandzki<br>`de-de`|1.0.1|Tokenizes wyrazy przez dzielenie ich na przestrzenie.<br> Jeśli użytkownik wprowadzi `Ik ga naar de kleuterschool` jako wypowiedź, pozostaje pojedynczy token. W `kleuterschool` ten sposób jest oznaczony jako jeden podmiot. |


### <a name="migrating-between-tokenizer-versions"></a>Migracja między wersjami tokenizatora
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

Tokenizacja odbywa się na poziomie aplikacji. Nie ma obsługi tokenizacji na poziomie wersji.

[Zaimportuj plik jako nową aplikację](luis-how-to-start-new-app.md)zamiast wersji. Ta akcja oznacza, że nowa aplikacja ma inny identyfikator aplikacji, ale używa wersji tokenizatora określonej w pliku.
