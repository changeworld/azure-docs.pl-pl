---
title: Słownik
titleSuffix: Language Understanding - Azure Cognitive Services
description: Glosariusz opisano terminy, które można napotkać podczas pracy z usługą interfejsu API usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: ced83b83c2d64ba5c4816f378c66dae9f4210391
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193632"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Słownik interpretacji języka wspólnego słownika i pojęcia
Language Understanding (LUIS) słownik wyjaśnia, warunki, które można napotkać podczas pracy z usługą interfejsu API usługi LUIS.

## <a name="active-version"></a>Wersja aktywna

Wersja usługi LUIS aktywna jest wersji, który odbiera wszystkie zmiany w modelu. W [LUIS](luis-reference-regions.md) witryny sieci Web, jeśli chcesz wprowadzić zmiany do wersji, która nie jest aktywny wersji, musisz najpierw ustawić tej wersji jako aktywny.

## <a name="authoring"></a>Tworzenie

Tworzenie jest możliwość tworzenia, zarządzania i wdrażania [aplikacją usługi LUIS](#luis-app), albo za pomocą [LUIS](luis-reference-regions.md) witryny sieci Web lub [Tworzenie interfejsów API](https://aka.ms/luis-authoring-api).

## <a name="authoring-key"></a>Tworzenie klucza

Poprzednia nazwa klucza "Programistyczne". Używany do tworzenia aplikacji. Nie są używane dla punktu końcowego poziom produkcji zapytań. Aby uzyskać więcej informacji, zobacz [klucza limity](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Plik JSON tekst w partii

Plik wsadowy jest tablicą JSON. Każdy element w tablicy ma trzy właściwości: `text`, `intent`, i `entities`. `entities` Właściwość jest tablicą. Tablica może być pusta. Jeśli `entities` tablicy nie jest pusta, należy dokładnie sprawdzić jednostek.

```JSON
[
    {
        "text": "drive me home",
        "intent": "None",
        "entities": []
    },
    {
        "text": "book a flight to orlando on the 25th",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "orlando",
                "type": "Location",
                "startIndex": 18,
                "endIndex": 25
            }
        ]
    }
]

```


## <a name="collaborator"></a>Współautor

Współpracownik jest [właściciela](#owner) aplikacji, ale ma takie same uprawnienia do dodawania, edytowania i usuwanie intencji, jednostki, wypowiedzi.

## <a name="currently-editing"></a>Trwa edytowanie

Taki sam jak [aktywnej wersji](#active-version)

## <a name="domain"></a>Domeny

W kontekście usługi LUIS **domeny** jest obszarem wiedzy. Twoja domena to specyficzne dla Twojej okolicy aplikacji wiedzy. Może to być obszarze Ogólne aplikacji agenta podróży. Aplikacja podróży agent może być również specyficzne dla tylko obszarów informacje dla swojej firmy, takich jak określonych lokalizacjach geograficznych, języków i usług.

## <a name="endpoint"></a>Punkt końcowy

[Punktu końcowego usługi LUIS](https://aka.ms/luis-endpoint-apis) adres URL jest, dokąd wysyłać zapytania usługi LUIS po [aplikacją usługi LUIS](#luis-app) jest utworzonych i opublikowanych. Adres URL punktu końcowego zawiera region opublikowanej aplikacji, a także identyfikator aplikacji. Można znaleźć punktu końcowego na **[kluczy i punktów końcowych](luis-how-to-azure-subscription.md)** strony aplikacji, lub można uzyskać adresu URL punktu końcowego z [uzyskać informacje o aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) interfejsu API.

Przykładowy punkt końcowy wygląda następująco:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|Parametr QueryString|description|
|--|--|
|region| [opublikowane regionu](luis-reference-regions.md#publishing-regions) |
|appID | Identyfikator aplikacji usługi LUIS |
|Identyfikator subskrypcji | Usługa LUIS klucza punktu końcowego (subskrypcja), utworzone w witrynie Azure portal |
|q | Wypowiedź |
|timezoneOffset| minutes|

## <a name="entity"></a>Jednostki

[Jednostki](luis-concept-entity-types.md) są ważne słów w [wypowiedzi](luis-concept-utterance.md) opisują informacje istotne dla [intencji](luis-concept-intent.md), i czasami są niezbędne do niego. Jednostka jest zasadniczo typu danych w usługi LUIS.

## <a name="f-measure"></a>Miara F

W [testowania partii](luis-interactive-test.md#batch-testing), to miara dokładności testu.

## <a name="false-negative"></a>Fałszywie ujemnych (TN)

W [testowania partii](luis-interactive-test.md#batch-testing), punktów danych reprezentują wypowiedzi, w których aplikacja niepoprawnie przewidzieć braku intencji/jednostkę docelową.

## <a name="false-positive"></a>Wynik fałszywie dodatni (TP)

W [testowania partii](luis-interactive-test.md#batch-testing), punktów danych reprezentują wypowiedzi, w których aplikacja niepoprawnie przewidzieć istnienie intencji/jednostkę docelową.

## <a name="features"></a>Funkcje

W usłudze machine learning [funkcji](luis-concept-feature.md) wyróżniający cechy lub atrybutu danych, która przestrzega systemu.

## <a name="intent"></a>Przeznaczenie

[Intencji](luis-concept-intent.md) reprezentuje zadania lub akcji, które użytkownik chce wykonać. Jest to cel lub celem wyrażone w danych wejściowych użytkownika, takie jak rezerwacji lotu, zwracając rachunku lub wyszukiwanie artykułu z wiadomościami. W usługi LUIS funkcja prognozowania jest oparta na cały wypowiedź. Jednostki, natomiast to fragmenty wypowiedź.

## <a name="labeling"></a>Etykietowania

Etykietowanie jest proces kojarzenia wyraz lub frazę w intencji [wypowiedź](#utterance) z [jednostki](#entity) (typ danych).

## <a name="luis-app"></a>Aplikacją usługi LUIS

Aplikacją usługi LUIS jest uczony modelem przetwarzania języka naturalnego, w tym [intencji](#intent), [jednostek](#entity)i etykietami [wypowiedzi](#utterance).

## <a name="owner"></a>Właściciel

Każda aplikacja ma jednego właściciela, który jest osobie, która utworzyła aplikację. Właściciel subskrypcji może dodać [współpracowników](#collaborator).

## <a name="pattern"></a>Wzorce
Poprzednie funkcja wzorca jest zastępowany [wzorców](luis-concept-patterns.md). Użycie wzorców w celu zwiększenia dokładności prognozy, podając mniejszą liczbę przykładów szkoleniowych.

## <a name="phrase-list"></a>Lista fraz

A [listy fraz](luis-concept-feature.md#what-is-a-phrase-list-feature) znajduje się grupa wartości (słów i fraz), które należą do tej samej klasy i muszą być traktowane w podobny sposób (na przykład nazwy miasta lub produktów). Wymienne listy jest traktowany jako synonimów.

## <a name="prebuilt-domains"></a>Wstępnie utworzone domeny

A [ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md) jest aplikacją usługi LUIS skonfigurowane dla określonej domeny, takich jak Automatyzacja domu (HomeAutomation) lub zastrzeżeń restauracji (RestaurantReservation). Intencji, wypowiedzi oraz jednostki są skonfigurowane dla tej domeny.

## <a name="prebuilt-entity"></a>Wstępnie utworzone jednostki

A [wstępnie utworzone jednostki](luis-prebuilt-entities.md) jest jednostką usługi LUIS udostępnia typowe rodzaje informacje, takie jak numer, adres URL i wiadomości e-mail. Istnieje możliwość dodawania wstępnie utworzone jednostki do aplikacji.

## <a name="precision"></a>Precyzja
W [testowania partii](luis-interactive-test.md#batch-testing), dokładność (nazywane również wartości dodatniej predykcyjnej) jest ułamek odpowiednie wypowiedzi między wypowiedzi pobrane.

## <a name="programmatic-key"></a>Klucz programowy

Nowa nazwa: [tworzenia klucza](#authoring-key).

## <a name="publish"></a>Publikowanie

Publikowanie oznacza, dzięki czemu usługa LUIS [aktywnej wersji](#active-version) dostępne na przejściowe lub produkcyjne [punktu końcowego](#endpoint).  

## <a name="quota"></a>Limit przydziału

Limit przydziału usługi LUIS jest ograniczenie [warstwy subskrypcji platformy Azure](https://aka.ms/luis-price-tier). Limit przydziału usługi LUIS można ograniczyć, używając zarówno w przypadku żądań na sekundę (429 stanu HTTP) i łączna liczba żądań w ciągu miesiąca (Stan HTTP 403).

## <a name="recall"></a>Odwołania
W [testowania partii](luis-interactive-test.md#batch-testing), odwołanie (znanego również jako ważność), jest możliwość usługi LUIS w celu uogólnienia.

## <a name="semantic-dictionary"></a>Słownik semantyczne
Słownik semantycznego znajduje się na stronie listy jednostek, a także na stronie listy fraz. Semantyczne słownik zawiera sugestie słów na podstawie bieżącego zakresu.

## <a name="sentiment-analysis"></a>Analiza tonacji
Analiza tonacji zawiera wartości dodatnie lub ujemne wypowiedzi dostarczone przez [analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Zalewanie mowy

Zalewanie mowy umożliwia usługi rozpoznawania mowy w celu zalewany, Twój model usługi LUIS.

## <a name="spelling-correction"></a>Korekty pisowni

Włączanie sprawdzania pisowni Bing do poprawianie błędnie napisanych wyrazów w wypowiedzi przed prognozy.

## <a name="starter-key"></a>Klucz Starter

Taki sam jak [klucza programowego](#programmatic-key), których nazwy zostały zmienione do tworzenia klucza.

## <a name="subscription-key"></a>Klucz subskrypcji

Klucz subskrypcji jest **punktu końcowego** klucz skojarzony z usługi LUIS [tworzenia na platformie Azure](luis-how-to-azure-subscription.md). Ten klucz nie jest [tworzenia klucza](#programmatic-key). W przypadku klucza punktu końcowego powinno być używane do wszelkie żądania punktu końcowego, zamiast tworzenia klucza. Możesz zobaczyć swoje bieżącego klucza punktu końcowego wewnątrz adres URL punktu końcowego w dolnej części [ **kluczy i punktów końcowych** strony](luis-how-to-azure-subscription.md) w [LUIS](luis-reference-regions.md) witryny sieci Web. Jest to wartość z **klucz subskrypcji** pary nazwa/wartość.

## <a name="test"></a>Test

[Testowanie](luis-interactive-test.md#test-your-app) aplikacją usługi LUIS oznacza, że powoduje przekazanie wypowiedź do usługi LUIS i przeglądanie za pomocą pliku JSON.

## <a name="timezoneoffset"></a>Przesunięcie strefy czasowej

Punkt końcowy zawiera timezoneOffset. Jest to liczba w ciągu kilku minut, aby dodawać i usuwać datetimeV2 wstępnie utworzone jednostki. Na przykład jeśli wypowiedź "czas, jaki jest ona teraz?", datetimeV2 zwracany jest bieżący czas dla żądania klienta. Jeśli żądania klienta pochodzą robota lub innych aplikacji, która nie jest taki sam jak Twój bot użytkownika, należy przekazać w przesunięcie między bot i użytkownika.

Zobacz [Zmień strefę czasową datetimeV2 wstępnie utworzone jednostki](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Token jest najmniejsza jednostka, która jest oznaczona etykietą w jednostce. Tokenizacji opiera się na aplikacji [kultury](luis-language-support.md#tokenization).

## <a name="train"></a>Szkolenie

Szkolenie to proces uczenia LUIS o wszelkich zmianach [aktywnej wersji](#active-version) od czasu ostatniego szkolenia.

## <a name="true-negative"></a>Ujemna wartość true (TN)

W [testowania partii](luis-interactive-test.md#batch-testing), punktów danych reprezentują wypowiedzi, w których aplikacja poprawnie przewidzieć braku intencji/jednostkę docelową.

## <a name="true-positive"></a>Prawdziwie dodatni (TP)

W [testowania partii](luis-interactive-test.md#batch-testing), punktów danych reprezentują wypowiedzi, w których aplikacja poprawnie przewidzieć istnienie intencji/jednostkę docelową.

## <a name="utterance"></a>Wypowiedź

Wypowiedź to wyrażenie języka naturalnego, takie jak "książki 2 bilety do Seattle następny wtorek". Przykład wypowiedzi są dodawane do intencji.

## <a name="version"></a>Wersja

Usługa LUIS [wersji](luis-how-to-manage-versions.md) jest modelem określonych danych skojarzonych z identyfikator aplikacji usługi LUIS i opublikowane punktu końcowego. Każda aplikacja usługi LUIS ma co najmniej jedną wersję.
