---
title: Limity — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera znanych ograniczeń dla usługi Azure Cognitive Services Language Understanding (LUIS). Usługa LUIS ma kilka obszarów granic. Model granic steruje intencji, jednostek i funkcji usługi LUIS. Limity przydziału, w oparciu o typ klucza. Kombinacja klawiszy kontrolki witryny sieci Web usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/18/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5dd49035414c7e717c3d60ab9bc185f42dcac408
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560862"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Granice modelu i kluczy LUIS
Usługa LUIS ma kilka obszarów granic. Pierwsza to [granic modelu](#model-boundaries), która kontroluje intencji, jednostek i funkcji usługi LUIS. Drugi ma [limitów przydziału](#key-limits) na podstawie typu klucza. Trzecie pole granice wynosi [klawiatury kombinacji](#keyboard-controls) do kontrolowania witryny sieci Web usługi LUIS. Czwarty obszar jest [mapowanie regionów świata](luis-reference-regions.md) między LUIS tworzenia witryny sieci Web i LUIS [punktu końcowego](luis-glossary.md#endpoint) interfejsów API. 


## <a name="model-boundaries"></a>Granice modelu

Jeśli aplikacja przekracza limity i granice modelu LUIS, należy rozważyć użycie aplikacji do [wysyłania Luis](luis-concept-enterprise.md#dispatch-tool-and-model) lub [kontenera Luis](luis-container-howto.md). 

|Obszar|Limit|
|--|:--|
| [Nazwa aplikacji][luis-get-started-create-app] | \* Znak Domyślna maksymalna |
| [Testy wsadowe][batch-testing]| 10 zestawów danych, 1000 wypowiedzi dla zestawu danych|
| Lista jawnego | 50 na aplikację|
| Jednostki zewnętrzne | Brak limitów |
| [Intencji][intents]|500 na aplikację: 499 niestandardowych założeń i niewymaganego zamiaru _none_ .<br>Aplikacja [oparta na wysyłce](https://aka.ms/dispatch-tool) ma odpowiednie źródła wysyłania 500.|
| [Lista jednostek](./luis-concept-entity-types.md) | Nadrzędny 50, element podrzędny: 20 000 elementów. Nazwa kanoniczna jest * domyślna maksymalna liczba znaków. Synonim wartości ma żadnych ograniczeń długości. |
| Jednostki, które zostały wyznanie [maszyn i role](./luis-concept-entity-types.md):<br> złożone<br>ułatwia<br>rola jednostki|Limit 100 jednostek nadrzędnych lub 330 jednostek, w zależności od liczby trafień użytkownika. Rola jest traktowana jako jednostka na potrzeby tej granicy. Przykładem jest projekt złożony z prostą jednostką, która ma 2 role: 1 złożona + 1 prosta + 2 role = 4 jednostek 330.|
| [Wersja zapoznawcza — jednostki listy dynamicznej](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listy ~ 1K na żądanie punktu końcowego przewidywania zapytań|
| [Wzorce](luis-concept-patterns.md)|500 wzorce dla aplikacji.<br>Maksymalna długość wzorca to 400 znaków.<br>3 jednostki Pattern.any na wzorzec<br>Maksymalnie 2 zagnieżdżonych opcjonalnych teksty we wzorcu|
| [Pattern.any](./luis-concept-entity-types.md)|100 na aplikację, 3 jednostki pattern.any na wzorzec |
| [Lista fraz][phrase-list]|10 frazę list, 5000 elementów na liście|
| [Wstępnie utworzonych jednostek](./luis-prebuilt-entities.md) | bez ograniczeń|
| [Wyrażenie regularne jednostek](./luis-concept-entity-types.md)|20 jednostek<br>Maksymalna liczba znaków to 500. na jednostkę definicję wzorca wyrażenia regularnego|
| [Role](luis-concept-roles.md)|300 role na aplikację. 10 ról na jednostkę|
| [Wypowiedź][utterances] | 500 znaków|
| [Wyrażenia długości][utterances] | 15 000 na aplikację — nie ma limitu liczby wyrażenia długości na zamiar|
| [Wersje](luis-concept-version.md)| bez ograniczeń |
| [Nazwa wersji][luis-how-to-manage-versions] | 10 znaków dotyczy alfanumeryczne oraz kropki (.) |

\* Znak Domyślna maksymalna to 50 znaków. 

<a name="intent-and-entity-naming"></a>

## <a name="object-naming"></a>Nazywanie obiektów

Nie należy używać następujących znaków w następujących nazwach.

|Object|Wyklucz znaki|
|--|--|
|Zamierzenia, jednostki i nazwy ról|`:`<br>`$`|
|Nazwa wersji|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Użycie klucza

Zrozumienie języka ma oddzielne klucze, jeden typ do tworzenia i jeden typ do wykonywania zapytań dotyczących punktu końcowego przewidywania. Aby dowiedzieć się więcej o różnicach między typami kluczy, zobacz [Tworzenie kluczy punktu końcowego prognozowanie i wykonywanie zapytań w Luis](luis-concept-keys.md).

## <a name="key-limits"></a>Ograniczenia klucza

Tworzenia klucza ma różne limity dotyczące tworzenia i punktu końcowego. Klucz punktu końcowego usługi LUIS jest prawidłowy tylko dla punktu końcowego zapytań.


|Klucz|Tworzenie|Endpoint|Cel|
|--|--|--|--|
|Language Understanding tworzenie/Starter|1 mln/miesiąc, 5/s|tysiące 1/miesiąc, 5/s|Tworzenie aplikacji usługi LUIS|
|Language Understanding [Subscription][pricing] — F0 — warstwa Bezpłatna |nieprawidłowy|10 tysięcy/miesiąc, 5/s|Wykonywanie zapytań punktu końcowego usługi LUIS|
|Language Understanding [Subscription][pricing] — S0 — warstwa podstawowa|nieprawidłowy|50/sekundę|Wykonywanie zapytań punktu końcowego usługi LUIS|
|[Usługa poznawcze][pricing] — S0 — warstwa standardowa|nieprawidłowy|50/sekundę|Wykonywanie zapytań punktu końcowego usługi LUIS|
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
