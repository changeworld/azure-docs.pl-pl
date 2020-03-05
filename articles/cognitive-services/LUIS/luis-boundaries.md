---
title: Limity — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera znanych ograniczeń dla usługi Azure Cognitive Services Language Understanding (LUIS). Usługa LUIS ma kilka obszarów granic. Model granic steruje intencji, jednostek i funkcji usługi LUIS. Limity przydziału, w oparciu o typ klucza. Kombinacja klawiszy kontrolki witryny sieci Web usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d584b00caef628eb9dfd085b1fdce2bb7b353988
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273518"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Granice modelu i kluczy LUIS
Usługa LUIS ma kilka obszarów granic. Pierwsza to [granica modelu](#model-boundaries), która steruje intencjami, jednostkami i funkcjami w Luis. Drugi obszar ma [limity przydziału](#key-limits) na podstawie typu klucza. Trzeci obszar granic jest [kombinacją klawiatury](#keyboard-controls) służącą do kontrolowania witryny sieci Web Luis. Czwarty obszar to [Mapowanie regionów świata](luis-reference-regions.md) między witryną sieci Web Luis Authoring a interfejsem API Luis [Endpoint](luis-glossary.md#endpoint) .


## <a name="model-boundaries"></a>Granice modelu

Jeśli aplikacja przekracza limity i granice modelu LUIS, należy rozważyć użycie aplikacji do [wysyłania Luis](luis-concept-enterprise.md#dispatch-tool-and-model) lub [kontenera Luis](luis-container-howto.md).

|Obszar|Limit|
|--|:--|
| [Nazwa aplikacji][luis-get-started-create-app] | \* Znak Domyślna maksymalna |
| Aplikacje| 500 aplikacji na zasób tworzenia na platformie Azure |
| [Testy wsadowe][batch-testing]| 10 zestawów danych, 1000 wypowiedzi dla zestawu danych|
| Lista jawnego | 50 na aplikację|
| Jednostki zewnętrzne | Brak limitów |
| [Intencji][intents]|500 na aplikację: 499 niestandardowe intencje i wymagane opcje _none_ .<br>Aplikacja [oparta na wysyłce](https://aka.ms/dispatch-tool) ma odpowiednie źródła wysyłania 500.|
| [Lista jednostek](./luis-concept-entity-types.md) | : 50, podrzędny: 20 000 elementów. Nazwa kanoniczna jest * domyślna maksymalna liczba znaków. Synonim wartości ma żadnych ograniczeń długości. |
| Jednostki, które zostały [wyznanie maszyn i role](./luis-concept-entity-types.md):<br> złożone<br>ułatwia<br>rola jednostki|Limit 100 jednostek nadrzędnych lub 330 jednostek, w zależności od liczby trafień użytkownika. Rola jest traktowana jako jednostka na potrzeby tej granicy. Przykładem jest kompozyt z prostą jednostką, która ma 2 role: 1 złożona + 1 prosta + 2 role = 4 jednostek 330.<br>Podskładniki można zagnieżdżać do 5 poziomów.|
|Model jako funkcja| Maksymalna liczba modeli, które mogą być używane jako deskryptor (funkcja) do określonego modelu do 10 modeli. Maksymalna liczba list fraz używanych jako deskryptor (funkcja) dla określonego modelu do 10 list fraz.|
| [Wersja zapoznawcza — jednostki listy dynamicznej](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listy ~ 1K na żądanie punktu końcowego przewidywania zapytań|
| [Modele](luis-concept-patterns.md)|500 wzorce dla aplikacji.<br>Maksymalna długość wzorca to 400 znaków.<br>3 jednostki Pattern.any na wzorzec<br>Maksymalnie 2 zagnieżdżonych opcjonalnych teksty we wzorcu|
| [Wzorzec. any](./luis-concept-entity-types.md)|100 na aplikację, 3 jednostki pattern.any na wzorzec |
| [Lista fraz][phrase-list]|500 list fraz. PhraseList niewymienne ma maksymalnie 5 000 fraz. PhraseList zamienne ma maksymalnie 50 000 fraz. Maksymalna liczba fraz dla każdej aplikacji 500 000 fraz.|
| [Wstępnie utworzone jednostki](./luis-prebuilt-entities.md) | bez ograniczeń|
| [Jednostki wyrażenia regularnego](./luis-concept-entity-types.md)|20 jednostek<br>Maksymalna liczba znaków to 500. na jednostkę definicję wzorca wyrażenia regularnego|
| [Role](luis-concept-roles.md)|300 role na aplikację. 10 ról na jednostkę|
| [Wypowiedź][utterances] | 500 znaków|
| [Wyrażenia długości][utterances] | 15 000 na aplikację — nie ma limitu liczby wyrażenia długości na zamiar|
| [Wersje](luis-concept-version.md)| 100 wersji na aplikację |
| [Nazwa wersji][luis-how-to-manage-versions] | 10 znaków dotyczy alfanumeryczne oraz kropki (.) |

\* Znak Domyślna maksymalna to 50 znaków.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Unikatowość nazw

Użyj następujących reguł unikatowości nazewnictwa.

Następujące elementy muszą być unikatowe w ramach aplikacji LUIS:

* Nazwa wersji
* intencji
* jednostka
* role

Następujące elementy muszą być unikatowe w ramach zastosowanego zakresu:

* Lista fraz

## <a name="object-naming"></a>Nazywanie obiektów

Nie należy używać następujących znaków w następujących nazwach.

|Obiekt|Wyklucz znaki|
|--|--|
|Zamierzenia, jednostki i nazwy ról|`:`<br>`$` <br> `&`|
|Nazwa wersji|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Użycie klucza

Zrozumienie języka ma oddzielne klucze, jeden typ do tworzenia i jeden typ do wykonywania zapytań dotyczących punktu końcowego przewidywania. Aby dowiedzieć się więcej o różnicach między typami kluczy, zobacz [Tworzenie kluczy punktu końcowego prognozowanie i wykonywanie zapytań w Luis](luis-concept-keys.md).

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Limity kluczy zasobów

Klucze zasobów mają różne limity dla tworzenia i punktu końcowego. Klucz punktu końcowego zapytania predykcyjnego LUIS jest prawidłowy tylko dla zapytań punktów końcowych.

* 500 aplikacji na zasób tworzenia na platformie Azure

|Klucz|Tworzenie|Endpoint|Przeznaczenie|
|--|--|--|--|
|Starter (początkowy)|1 mln/miesiąc, 5/s|tysiące 1/miesiąc, 5/s|Tworzenie aplikacji usługi LUIS|
|F0 — warstwa Bezpłatna |1 mln/miesiąc, 5/s|10 tysięcy/miesiąc, 5/s|Wykonywanie zapytań punktu końcowego usługi LUIS|
|S0 — warstwa podstawowa|-|50/sekundę|Wykonywanie zapytań punktu końcowego usługi LUIS|
|S0 — warstwa standardowa|-|50/sekundę|Wykonywanie zapytań punktu końcowego usługi LUIS|
|[Integracja z analizą tonacji](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Dodawanie informacji tonacji z uwzględnieniem wyodrębniania danych o kluczowym postanowień, które nie wymagają innego zasobu platformy Azure. |
|[Integracja mowy](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 żądań punktów końcowych według kosztu jednostki|Konwertowanie mowy wypowiedź tekstowe wypowiedź i LUIS zwracają.|

[Dowiedz się więcej o cenach.][pricing]

## <a name="keyboard-controls"></a>Klawiatury

|Dane wejściowe z klawiatury | Opis |
|--|--|
|Kontrolka + E|Przełącza między tokenów i jednostki na liście wypowiedzi|

## <a name="website-sign-in-time-period"></a>Okres logowania do witryny sieci Web

Dostęp do logowania wynosi od **60 minut**. Po tym okresie wystąpi błąd. Musisz zalogować się ponownie.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
