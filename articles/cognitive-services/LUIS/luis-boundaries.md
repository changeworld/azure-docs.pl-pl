---
title: Limity — usługa LUIS
description: Ten artykuł zawiera znane limity zrozumienia języka usługi Azure Cognitive Services (LUIS). Usługa LUIS ma kilka obszarów granicznych. Granica modelu kontroluje intencje, jednostki i funkcje w usłudze LUIS. Limity przydziałów na podstawie typu klucza. Kombinacja klawiatury steruje witryną sieci Luis w sieci Web.
ms.topic: reference
ms.date: 04/02/2020
ms.openlocfilehash: 4aa69cb0fd36fe5bf4ea2928022aea602b8830d6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618864"
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
| [Lista fraz][phrase-list]|500 list fraz. 10 globalnych list fraz ze względu na model jako limit funkcji. Lista fraz niewymiennych ma maksymalnie 5000 fraz. Lista wymiennych fraz zawiera maksymalnie 50 000 fraz. Maksymalna liczba fraz na aplikację 500 000 fraz.|
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

## <a name="resource-usage-and-limits"></a>Użycie zasobów i limity

Language Understand ma oddzielne zasoby, jeden typ do tworzenia i jeden typ do wykonywania zapytań o punkt końcowy przewidywania. Aby dowiedzieć się więcej o różnicach między typami kluczy, zobacz [Tworzenie i przewidywanie zapytań kluczy końcowych punktów końcowych w usłudze LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Tworzenie limitów zasobów

Użyj _rodzaju_ `LUIS.Authoring`, , podczas filtrowania zasobów w witrynie Azure portal. Usługa LUIS ogranicza 500 aplikacji na zasób tworzenia platformy Azure.

|Tworzenie zasobu|Tworzenie TPS|
|--|--|
|Starter (początkowy)|1 milion/miesiąc, 5/sekundę|
|F0 - Warstwa bezpłatna |1 milion/miesiąc, 5/sekundę|

* TPS = Transakcje na sekundę

[Dowiedz się więcej o cenach.][pricing]

### <a name="query-prediction-resource-limits"></a>Limity zasobów prognozowania kwerend

Użyj _rodzaju_ `LUIS`, , podczas filtrowania zasobów w witrynie Azure portal. Zasób punktu końcowego przewidywania kwerend usługi LUIS, używany w czasie wykonywania, jest prawidłowy tylko dla kwerend punktu końcowego.

|Zasób przewidywanie kwerend|Zapytanie TPS|
|--|--|
|F0 - Warstwa bezpłatna |10 tysięcy/miesiąc, 5/sekundę|
|S0 — warstwa standardowa|50/sekundę|

### <a name="sentiment-analysis"></a>Analiza tonacji

[Integracja analizy tonacji](luis-how-to-publish-app.md#enable-sentiment-analysis), która zawiera informacje o tonacji, jest dostarczana bez konieczności innego zasobu platformy Azure.

### <a name="speech-integration"></a>Integracja mowy

[Integracja mowy](../speech-service/how-to-recognize-intents-from-speech-csharp.md) zapewnia 1 tysiąc żądań punktu końcowego na koszt jednostkowy.

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
