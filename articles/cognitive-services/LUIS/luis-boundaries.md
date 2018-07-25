---
title: Language Understanding (LUIS) granice | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera znanych ograniczeń usługi LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: ea4f31094e27f1abbe57c212f262845fd16dd984
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225282"
---
# <a name="luis-boundaries"></a>Granice usługi LUIS
Usługa LUIS ma kilka obszarów granic. Pierwsza to [granic modelu](#model-boundaries), która kontroluje intencji, jednostek i funkcji usługi LUIS. Drugi ma [limitów przydziału](#key-limits) na podstawie typu klucza. Trzecie pole granice wynosi [klawiatury kombinacji](#keyboard-controls) do kontrolowania witryny sieci Web usługi LUIS. Czwarty obszar jest [mapowanie regionów świata](luis-reference-regions.md) między LUIS tworzenia witryny sieci Web i LUIS [punktu końcowego](luis-glossary.md#endpoint) interfejsów API. 


## <a name="model-boundaries"></a>Granice modelu

|Obszar|Limit|
|--|:--|--|
| [Nazwa aplikacji][luis-get-started-create-app] | * Znak Domyślna maksymalna |
| [Testowanie usługi Batch][batch-testing]| 10 zestawów danych, 1000 wypowiedzi dla zestawu danych|
| **[Złożone](./luis-concept-entity-types.md)|100 z maksymalnie 10 elementów podrzędnych |
| Lista jawnego | 50 na aplikację|
| **[Hierarchiczna](./luis-concept-entity-types.md) |100 z maksymalnie 10 elementów podrzędnych |
| [Intencji][intents]|500 na aplikację<br>[Na podstawie wysyłania](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) aplikacja ma odpowiedni źródeł 500 wysyłania|
| [Lista jednostek](./luis-concept-entity-types.md) | : 50, podrzędny: 20 000 elementów. Nazwa kanoniczna jest * domyślna maksymalna liczba znaków. Synonimy nie ma żadnych ograniczeń długości. |
| [Wzorce](luis-concept-patterns.md)|500 wzorce dla aplikacji.<br>Maksymalna długość wzorca to 400 znaków.<br>3 jednostki Pattern.any na wzorzec<br>Maksymalnie 2 zagnieżdżonych opcjonalnych teksty we wzorcu|
| [Pattern.any](./luis-concept-entity-types.md)|100 na aplikację, 3 jednostki pattern.any na wzorzec |
| [Lista fraz][phrase-list]|10 frazę list, 5000 elementów na liście|
| [Wstępnie utworzonych jednostek](./luis-prebuilt-entities.md) | bez ograniczeń|
| [Wyrażenie regularne jednostek](./luis-concept-entity-types.md)|20 jednostek<br>Maksymalna liczba znaków to 500. na jednostkę definicję wzorca wyrażenia regularnego|
| [Role](luis-concept-roles.md)|300 role na aplikację. 10 ról na jednostkę|
| **[Proste](./luis-concept-entity-types.md)| 100 jednostek|
| [Wypowiedź][utterances] | 500 znaków|
| [Wypowiedzi][utterances] | 15 000 na aplikacji|
| [Nazwa wersji][luis-how-to-manage-versions] | 10 znaków dotyczy alfanumeryczne oraz kropki (.) |

* Znak Domyślna maksymalna to 50 znaków. 

** Łącznej liczby jednostek prosty, hierarchicznych i złożone nie może przekraczać 100. Łączna liczba hierarchiczne jednostek, złożonego jednostek, jednostki proste i hierarchiczne elementy podrzędne jednostki nie może przekraczać 330. 

## <a name="intent-and-entity-naming"></a>Przeznaczenie i nazewnictwa jednostki
Nie należy używać następujących znaków w nazwach intencji i jednostki:

|Znak|Name (Nazwa)|
|--|--|
|`{`|Lewy nawias klamrowy|
|`}`|Prawy nawias klamrowy|
|`[`|Lewy nawias kwadratowy|
|`]`|Prawy nawias kwadratowy|
|`\`|Ukośnik odwrotny|

## <a name="key-limits"></a>Ograniczenia klucza
Tworzenia klucza ma różne limity dotyczące tworzenia i punktu końcowego. Klucz punktu końcowego usługi LUIS jest prawidłowy tylko dla punktu końcowego zapytań.

|Klucz|Tworzenie|Endpoint|Przeznaczenie|
|--|--|--|--|
|Tworzenie Starter|1 mln/miesiąc, 5/s|tysiące 1/miesiąc, 5/s|Tworzenie aplikacji usługi LUIS|
|[Subskrypcja] [ pricing] - F0 — warstwa bezpłatna |nieprawidłowy|10 tysięcy/miesiąc, 5/s|Wykonywanie zapytań punktu końcowego usługi LUIS|
|[Subskrypcja] [ pricing] - S0 - warstwa podstawowa|nieprawidłowy|50/sekundę|Wykonywanie zapytań punktu końcowego usługi LUIS|
|[Integracja analizy tonacji](luis-how-to-publish-app.md#enable-sentiment-analysis)|nieprawidłowy|Brak opłat|Dodawanie informacji opinii, w tym kluczowe frazy |
|Integracja funkcji rozpoznawania mowy|nieprawidłowy|Żądania punktu końcowego 5.50 USD/1 tysięcy|Konwertowanie mowy wypowiedź tekstowe wypowiedź i LUIS zwracają.|

## <a name="keyboard-controls"></a>Klawiatury

|Dane wejściowe z klawiatury | Opis | 
|--|--|
|Kontrolka + E|Przełącza między tokenów i jednostki na liście wypowiedzi|

## <a name="website-sign-in-time-period"></a>Witryny sieci Web zaloguj się w przedziale czasu

Dotyczy dostępu logowania **60 minut**. Po tym okresie wystąpi błąd. Musisz zalogować się ponownie.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
