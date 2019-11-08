---
title: Limity — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera znane limity usługi Azure Cognitive Services Language Understanding (LUIS). LUIS ma kilka obszarów granicznych. Granice modelu sterują intencjami, jednostkami i funkcjami w LUIS. Limity przydziału na podstawie typu klucza. Kombinacja klawiatury kontroluje witrynę sieci Web LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0654916b344cf47cf9942b883d62d392c0552979
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818931"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Granice modelu i kluczy LUIS
LUIS ma kilka obszarów granicznych. Pierwsza to [granica modelu](#model-boundaries), która steruje intencjami, jednostkami i funkcjami w Luis. Drugi obszar ma [limity przydziału](#key-limits) na podstawie typu klucza. Trzeci obszar granic jest [kombinacją klawiatury](#keyboard-controls) służącą do kontrolowania witryny sieci Web Luis. Czwarty obszar to [Mapowanie regionów świata](luis-reference-regions.md) między witryną sieci Web Luis Authoring a interfejsem API Luis [Endpoint](luis-glossary.md#endpoint) . 


## <a name="model-boundaries"></a>Granice modelu

Jeśli aplikacja przekracza limity i granice modelu LUIS, należy rozważyć użycie aplikacji do [wysyłania Luis](luis-concept-enterprise.md#dispatch-tool-and-model) lub [kontenera Luis](luis-container-howto.md). 

|Obszar|Limit|
|--|:--|
| [Nazwa aplikacji][luis-get-started-create-app] | \* Maksimum znaku domyślnego |
| Aplikacje| 500 aplikacji na zasób tworzenia na platformie Azure |
| [Testy wsadowe][batch-testing]| 10 zestawów danych, 1000 wyrażenia długości na zestaw danych|
| Lista jawna | 50 na aplikację|
| Jednostki zewnętrzne | Brak limitów |
| [Intencji][intents]|500 na aplikację: 499 niestandardowe intencje i wymagane opcje _none_ .<br>Aplikacja [oparta na wysyłce](https://aka.ms/dispatch-tool) ma odpowiednie źródła wysyłania 500.|
| [Lista jednostek](./luis-concept-entity-types.md) | Element nadrzędny: 50, element podrzędny: 20 000 elementów. Nazwa kanoniczna to * domyślny znak maksymalny. Wartości synonimów nie mają ograniczenia długości. |
| Jednostki, które zostały [wyznanie maszyn i role](./luis-concept-entity-types.md):<br> złożone<br>ułatwia<br>rola jednostki|Limit 100 jednostek nadrzędnych lub 330 jednostek, w zależności od liczby trafień użytkownika. Rola jest traktowana jako jednostka na potrzeby tej granicy. Przykładem jest kompozyt z prostą jednostką, która ma 2 role: 1 złożona + 1 prosta + 2 role = 4 jednostek 330.<br>Podskładniki można zagnieżdżać do 5 poziomów.|
|Model jako funkcja| Maksymalna liczba modeli, które mogą być używane jako deskryptor (funkcja) do określonego modelu do 10 modeli. Maksymalna liczba list fraz używanych jako deskryptor (funkcja) dla określonego modelu do 10 list fraz.|
| [Wersja zapoznawcza — jednostki listy dynamicznej](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listy ~ 1K na żądanie punktu końcowego przewidywania zapytań|
| [Modele](luis-concept-patterns.md)|500 wzorców na aplikację.<br>Maksymalna długość wzorca to 400 znaków.<br>3 wzorzec. wszystkie jednostki na wzorzec<br>Maksymalnie 2 zagnieżdżonych dodatkowych tekstów w wzorcu|
| [Wzorzec. any](./luis-concept-entity-types.md)|100 na aplikację, 3 wzorce. wszystkie jednostki na wzorzec |
| [Lista fraz][phrase-list]|500 list fraz. PhraseList niewymienne ma maksymalnie 5 000 fraz. PhraseList zamienne ma maksymalnie 50 000 fraz. Maksymalna liczba fraz dla każdej aplikacji 500 000 fraz.|
| [Wstępnie utworzone jednostki](./luis-prebuilt-entities.md) | bez limitu|
| [Jednostki wyrażenia regularnego](./luis-concept-entity-types.md)|20 jednostek<br>maksymalnie 500 znaków. na wzorzec jednostki wyrażenia regularnego|
| [Role](luis-concept-roles.md)|300 ról na aplikację. 10 ról na jednostkę|
| [Wypowiedź][utterances] | 500 znaków|
| [Wyrażenia długości][utterances] | 15 000 na aplikację — nie ma limitu liczby wyrażenia długości na zamiar|
| [Wersje](luis-concept-version.md)| 100 wersji na aplikację |
| [Nazwa wersji][luis-how-to-manage-versions] | 10 znaków z ograniczeniami alfanumerycznymi i kropką (.) |

\* Wartość domyślna to 50 znaków. 

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Unikatowość nazw

Użyj następujących reguł unikatowości nazewnictwa.

Następujące elementy muszą być unikatowe w ramach aplikacji LUIS:

* Nazwa wersji
* intencji
* Podmiotów
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
|Starter (początkowy)|1 milion/miesiąc, 5/s|1 tysiąc/miesiąc, 5/s|Tworzenie aplikacji LUIS|
|F0 — warstwa Bezpłatna |1 milion/miesiąc, 5/s|10 tysięcy/miesiąc, 5/sekundę|Wykonywanie zapytania dotyczącego punktu końcowego LUIS|
|S0 — warstwa podstawowa|-|50/sekundę|Wykonywanie zapytania dotyczącego punktu końcowego LUIS|
|S0 — warstwa standardowa|-|50/sekundę|Wykonywanie zapytania dotyczącego punktu końcowego LUIS|
|[Integracja z analizą tonacji](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Dodawanie informacji tonacji z uwzględnieniem wyodrębniania danych o kluczowym postanowień, które nie wymagają innego zasobu platformy Azure. |
|[Integracja mowy](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 żądań punktów końcowych według kosztu jednostki|Konwertuj mówione wypowiedź na wypowiedź tekstowe i zwracają wyniki LUIS|

[Dowiedz się więcej o cenach.][pricing]

## <a name="keyboard-controls"></a>Kontrolki klawiatury

|Wprowadzanie z klawiatury | Opis | 
|--|--|
|Control + E|przełącza między tokenami i jednostkami na liście wyrażenia długości|

## <a name="website-sign-in-time-period"></a>Okres logowania do witryny sieci Web

Dostęp do logowania wynosi od **60 minut**. Ten błąd zostanie wyświetlony po upływie tego czasu. Musisz zalogować się ponownie.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
