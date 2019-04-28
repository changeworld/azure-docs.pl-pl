---
title: Limity
titleSuffix: Language Understanding - Azure Cognitive Services
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
ms.openlocfilehash: 58f6d6cf8bf16f7c35bab35a69cfcdf8759f66ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814041"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Granice dla modelu usługi LUIS i klucze
Usługa LUIS ma kilka obszarów granic. Pierwsza to [granic modelu](#model-boundaries), która kontroluje intencji, jednostek i funkcji usługi LUIS. Drugi ma [limitów przydziału](#key-limits) na podstawie typu klucza. Trzecie pole granice wynosi [klawiatury kombinacji](#keyboard-controls) do kontrolowania witryny sieci Web usługi LUIS. Czwarty obszar jest [mapowanie regionów świata](luis-reference-regions.md) między LUIS tworzenia witryny sieci Web i LUIS [punktu końcowego](luis-glossary.md#endpoint) interfejsów API. 


## <a name="model-boundaries"></a>Granice modelu

Jeśli aplikacja przekracza limity modelu usługi LUIS i granice, należy wziąć pod uwagę przy użyciu [wysyłania usługi LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) aplikacji lub za pomocą [kontenera usługi LUIS](luis-container-howto.md). 

|Obszar|Limit|
|--|:--|
| [Nazwa aplikacji][luis-get-started-create-app] | * Znak Domyślna maksymalna |
| [Testowanie usługi Batch][batch-testing]| 10 zestawów danych, 1000 wypowiedzi dla zestawu danych|
| Lista jawnego | 50 na aplikację|
| [Intencji][intents]|500 na aplikację: 499 niestandardowe intencje i wymagane _Brak_ intencji.<br>[Na podstawie wysyłania](https://aka.ms/dispatch-tool) aplikacja ma odpowiedni źródeł 500 wysyłania.|
| [Lista jednostek](./luis-concept-entity-types.md) | Element nadrzędny: 50, podrzędne: 20 000 elementów. Nazwa kanoniczna jest * domyślna maksymalna liczba znaków. Synonim wartości ma żadnych ograniczeń długości. |
| [Maszyny do opanowania jednostek](./luis-concept-entity-types.md):<br> Złożone,<br>  Hierarchiczna<br> Proste|Limit jednostek 100 nadrzędnej (nie w tym hierarchiczny elementy podrzędne) lub 330 jednostki (w tym hierarchiczny elementy podrzędne), zależnie od ograniczenia trafień użytkownika najpierw.<br><br>Przykład hierarchii będzie 30 hierarchie z 10 elementów podrzędnych.  Elementy podrzędne zużyje 300 całkowita, a elementy hierarchii zajmie pozostałe 30. |
| [Wzorce](luis-concept-patterns.md)|500 wzorce dla aplikacji.<br>Maksymalna długość wzorca to 400 znaków.<br>3 jednostki Pattern.any na wzorzec<br>Maksymalnie 2 zagnieżdżonych opcjonalnych teksty we wzorcu|
| [Pattern.any](./luis-concept-entity-types.md)|100 na aplikację, 3 jednostki pattern.any na wzorzec |
| [Lista fraz][phrase-list]|10 frazę list, 5000 elementów na liście|
| [Wstępnie utworzonych jednostek](./luis-prebuilt-entities.md) | bez ograniczeń|
| [Wyrażenie regularne jednostek](./luis-concept-entity-types.md)|20 jednostek<br>Maksymalna liczba znaków to 500. na jednostkę definicję wzorca wyrażenia regularnego|
| [Role](luis-concept-roles.md)|300 role na aplikację. 10 ról na jednostkę|
| [Wypowiedź][utterances] | 500 znaków|
| [Wypowiedzi][utterances] | 15 000 danej aplikacji — a nie ma żadnego limitu liczby wypowiedzi na intencje|
| [Wersje](luis-concept-version.md)| bez ograniczeń |
| [Nazwa wersji][luis-how-to-manage-versions] | 10 znaków dotyczy alfanumeryczne oraz kropki (.) |

* Znak Domyślna maksymalna to 50 znaków. 

<a name="intent-and-entity-naming"></a>

## <a name="object-naming"></a>Obiekt nazewnictwa

Następujące znaki nie należy używać w następujących nazw.

|Object|Wykluczenia znaków|
|--|--|
|Nazwy przeznaczenie, jednostki i roli|`:`<br>`$`|
|Nazwa wersji|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Użycie klucza

Poznawanie języka ma oddzielne klucze, jednego typu na potrzeby tworzenia i jeden typ podczas wykonywania zapytań do endpoint prognoz. Aby dowiedzieć się więcej o różnicach między typy kluczy, zobacz [tworzenie zapytań prognozowania punktu końcowego kluczy i w LUIS](luis-concept-keys.md).

## <a name="key-limits"></a>Ograniczenia klucza

Tworzenia klucza ma różne limity dotyczące tworzenia i punktu końcowego. Klucz punktu końcowego usługi LUIS jest prawidłowy tylko dla punktu końcowego zapytań.


|Klucz|Tworzenie|Endpoint|Przeznaczenie|
|--|--|--|--|
|Language Understanding tworzenia/modułu uruchamiającego|1 mln/miesiąc, 5/s|tysiące 1/miesiąc, 5/s|Tworzenie aplikacji usługi LUIS|
|Language Understanding [subskrypcji] [ pricing] - F0 — warstwa bezpłatna |nieprawidłowy|10 tysięcy/miesiąc, 5/s|Wykonywanie zapytań punktu końcowego usługi LUIS|
|Language Understanding [subskrypcji] [ pricing] - S0 - warstwa podstawowa|nieprawidłowy|50/sekundę|Wykonywanie zapytań punktu końcowego usługi LUIS|
|Usługi cognitive Services [subskrypcji] [ pricing] - S0 - w warstwie standardowa|nieprawidłowy|50/sekundę|Wykonywanie zapytań punktu końcowego usługi LUIS|
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
