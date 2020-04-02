---
title: Limity — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera znane limity zrozumienia języka usługi Azure Cognitive Services (LUIS). Usługa LUIS ma kilka obszarów granicznych. Granica modelu kontroluje intencje, jednostki i funkcje w usłudze LUIS. Limity przydziałów na podstawie typu klucza. Kombinacja klawiatury steruje witryną sieci Luis w sieci Web.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6c021e68f8b76d8b0d3e6e9ff21c242580f53313
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520941"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Granice modelu usługi LUIS i kluczy
Usługa LUIS ma kilka obszarów granicznych. Pierwszym z nich jest [granica modelu](#model-boundaries), który kontroluje intencje, jednostki i funkcje w usłudze LUIS. Drugi obszar to [limity przydziałów](#key-limits) na podstawie typu klucza. Trzeci obszar granic to [kombinacja klawiatury](#keyboard-controls) do sterowania witryną sieci Luis. Czwarty obszar to [mapowanie regionu świata](luis-reference-regions.md) między witryną sieci Web tworzenia usługi LUIS a interfejsami API [punktu końcowego](luis-glossary.md#endpoint) usługi LUIS.


## <a name="model-boundaries"></a>Granice modelu

Jeśli aplikacja przekracza limity i granice modelu usługi LUIS, należy rozważyć użycie aplikacji [do wysyłania usługi LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) lub przy użyciu [kontenera usługi LUIS.](luis-container-howto.md)

|Obszar|Limit|
|--|:--|
| [Nazwa aplikacji][luis-get-started-create-app] | *Domyślny znak maks. |
| Aplikacje| 500 aplikacji na zasób autorski platformy Azure |
| [Testowanie wsadowe][batch-testing]| 10 zestawów danych, 1000 wypowiedzi na zestaw danych|
| Lista jawna | 50 na aplikację|
| Podmioty zewnętrzne | bez ograniczeń |
| [Intencje][intents]|500 na aplikację: 499 intencji niestandardowych i wymagane _intencje Brak._<br>[Aplikacja oparta na wysyłkach](https://aka.ms/dispatch-tool) ma odpowiednie 500 źródeł wysyłki.|
| [Lista jednostek](./luis-concept-entity-types.md) | Rodzic: 50 lat, dziecko: 20 000 szt. Nazwa kanoniczna to *domyślny znak maks. Wartości synonimów nie mają ograniczeń długości. |
| [Jednostki nauczane maszynowo + role:](./luis-concept-entity-types.md)<br> Kompozytowe<br>Prosty<br>rola jednostki|Limit 100 jednostek nadrzędnych lub 330 jednostek, w zależności od tego, który limit użytkownik osiągnie jako pierwszy. Rola liczy się jako jednostka na potrzeby tej granicy. Przykładem jest kompozyt z prostą jednostką, która ma 2 role to: 1 kompozyt + 1 proste + 2 role = 4 z 330 jednostek.<br>Podskładne mogą być zagnieżdżone do 5 poziomów.|
|Model jako obiekt| Maksymalna liczba modeli, które mogą być używane jako deskryptor (operacja) do określonego modelu do 10 modeli. Maksymalna liczba list fraz używanych jako deskryptor (operacja) dla określonego modelu ma być 10 list fraz.|
| [Podgląd — elementy listy dynamicznej](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listy ~1k na żądanie punktu końcowego prognozowania kwerendy|
| [Wzorce](luis-concept-patterns.md)|500 wzorów na aplikację.<br>Maksymalna długość wzoru wynosi 400 znaków.<br>3 Pattern.any elementów na wzorzec<br>Maksymalnie 2 zagnieżdżone teksty opcjonalne we wzorze|
| [Wzór.any](./luis-concept-entity-types.md)|100 na aplikację, 3 pattern.any jednostek na wzorzec |
| [Lista fraz][phrase-list]|500 list fraz. 10 globalnych list fraz ze względu na model jako limit funkcji. Lista frazeów niewymiennych ma maksymalnie 5000 fraz. Wymienna lista frazeów ma maksymalnie 50 000 fraz. Maksymalna liczba fraz na aplikację 500 000 fraz.|
| [Wstępnie utworzone jednostki](./luis-prebuilt-entities.md) | bez limitu|
| [Jednostki wyrażenia regularnego](./luis-concept-entity-types.md)|20 podmiotów<br>500 znaków max. na wzorzec jednostki wyrażenia regularnego|
| [Role](luis-concept-roles.md)|300 ról na aplikację. 10 ról na encję|
| [Wypowiedź][utterances] | 500 znaków|
| [Wypowiedzi][utterances] | 15 000 na aplikację - nie ma limitu liczby wypowiedzi na intencję|
| [Wersje](luis-concept-version.md)| 100 wersji na aplikację |
| [Nazwa wersji][luis-how-to-manage-versions] | 10 znaków ograniczonych do alfanumerycznego i kropki (.) |

*Domyślny znak max to 50 znaków.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Unikatowość nazwy

Użyj następujących reguł unikatowości nazw.

W aplikacji usługi LUIS muszą być unikatowe następujące elementy:

* nazwa wersji
* Zamiarem
* jednostka
* role

W zastosowanym zakresie muszą być unikatowe:

* lista fraz

## <a name="object-naming"></a>Nazewnictwo obiektów

Nie należy używać następujących znaków w następujących nazwach.

|Obiekt|Wykluczanie znaków|
|--|--|
|Nazwy intencji, encji i ról|`:`<br>`$` <br> `&`|
|Nazwa wersji|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Użycie klucza

Language Understand ma oddzielne klucze, jeden typ do tworzenia i jeden typ do wykonywania zapytań o punkt końcowy przewidywania. Aby dowiedzieć się więcej o różnicach między typami kluczy, zobacz [Tworzenie i przewidywanie zapytań kluczy końcowych punktów końcowych w usłudze LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Limity kluczy zasobów

Klucze zasobów mają różne limity tworzenia i punktu końcowego. Klucz punktu końcowego kwerendy przewidywania usługi LUIS jest prawidłowy tylko dla kwerend końcowych.

* 500 aplikacji na zasób autorski platformy Azure

|Klucz|Tworzenie|Endpoint|Przeznaczenie|
|--|--|--|--|
|Starter (początkowy)|1 milion/miesiąc, 5/sekundę|1 tysiąc/miesiąc, 5/sekundę|Tworzenie aplikacji usługi LUIS|
|F0 - Warstwa bezpłatna |1 milion/miesiąc, 5/sekundę|10 tysięcy/miesiąc, 5/sekundę|Wykonywanie zapytań o punkt końcowy usługi LUIS|
|S0 — warstwa podstawowa|-|50/sekundę|Wykonywanie zapytań o punkt końcowy usługi LUIS|
|S0 — warstwa standardowa|-|50/sekundę|Wykonywanie zapytań o punkt końcowy usługi LUIS|
|[Integracja analizy tonacji](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Dodawanie informacji o tonacji, w tym wyodrębniania danych fraz kluczowych, jest dostarczane bez konieczności tworzenia innego zasobu platformy Azure. |
|[Integracja mowy](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1 tysiąc żądań punktu końcowego na koszt jednostkowy|Konwertowanie wypowiedzi na wypowiedź tekstową i zwracanie wyników usługi LUIS|

[Dowiedz się więcej o cenach.][pricing]

## <a name="keyboard-controls"></a>Elementy sterujące klawiaturą

|Klawiatury | Opis |
|--|--|
|Sterowanie+E|przełącza między tokenami i jednostkami na liście wypowiedzi|

## <a name="website-sign-in-time-period"></a>Okres logowania do witryny

Dostęp do logowania trwa **60 minut.** Po tym okresie otrzymasz ten błąd. Musisz zalogować się ponownie.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
