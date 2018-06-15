---
title: Słownik dla usługi interfejsu API języka opis (LUIS) | Dokumentacja firmy Microsoft
description: Słownik opisano terminy, które można napotkać podczas pracy z usługą LUIS interfejsu API.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 420f268090dbcfcc4f2fa7383b8b8892952030ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349792"
---
# <a name="glossary"></a>Słownik

## <a name="active-version"></a>Wersja aktywna

Wersja LUIS aktywna jest wersji, który odbiera zmiany wprowadzone w modelu. W [LUIS](luis-reference-regions.md) witryny sieci Web, jeśli chcesz wprowadzić zmiany do wersji, która nie jest aktywna wersja, należy najpierw ustawić tej wersji jako aktywne. 

## <a name="authoring"></a>Tworzenie

Tworzenie jest możliwość tworzenia, zarządzania i wdrażania [aplikacji LUIS](#luis-app), albo za pomocą [LUIS](luis-reference-regions.md) witryny sieci Web lub [tworzenia interfejsów API](https://aka.ms/luis-authoring-api). 

## <a name="authoring-key"></a>Tworzenie klucza

Wcześniej o nazwie "Programmatic" klucza. Wykorzystany do tworzenia aplikacji. Nie są używane dla punktu końcowego poziom produkcji zapytań. Aby uzyskać więcej informacji, zobacz [klucza limity](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Plik JSON tekst partii

Plik wsadowy jest tablica JSON. Każdy element w tablicy ma trzy właściwości: `text`, `intent`, i `entities`. `entities` Właściwość jest tablicą. Tablica może być pusta. Jeśli `entities` tablicy nie jest pusta, należy dokładnie sprawdzić jednostek.

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

Nie jest współpracownika [właściciela](#owner) aplikacji, ale ma te same uprawnienia do dodawania, edytowania i usuwania, profile, jednostek, zniesławiających.

## <a name="currently-editing"></a>Obecnie edycji

Taki sam jak [aktywnej wersji](#active-version)

## <a name="domain"></a>Domeny

W kontekście LUIS **domeny** jest to obszar wiedzy. Twoja domena to określonego obszaru aplikacji wiedzy. Może to być ogólnego obszaru, takie jak aplikacja agenta podróży. Można też dotyczą tylko obszary informacje dla swojej firmy, takich jak określone lokalizacje geograficzne, języki i usługi aplikacji podróży agenta. 

## <a name="endpoint"></a>punkt końcowy

[Punktu końcowego LUIS](https://aka.ms/luis-endpoint-apis) adres URL jest, gdzie przesyłania zapytań LUIS po [aplikacji LUIS](#luis-app) zostaną utworzone i opublikowane. Adres URL punktu końcowego zawiera region opublikowanej aplikacji, jak również identyfikator aplikacji. Możesz znaleźć punkt końcowy na **[publikowania](publishapp.md)** strony aplikacji, w tabeli zasobów i kluczy lub możesz uzyskać adres URL punktu końcowego z [uzyskać informacje o aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) interfejsu API.

Punkt końcowy przykład wyglądają następująco:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|Parametr QueryString|description|
|--|--|
|region| [opublikowane regionu](luis-reference-regions.md#publishing-regions) |
|appID | Identyfikator aplikacji LUIS |
|Identyfikator subskrypcji | Klucz subskrypcji LUIS utworzone w portalu Azure |
|Q | utterance |
|wartości timezoneOffset| minutes|

## <a name="entity"></a>Jednostki

[Jednostki](luis-concept-entity-types.md) są ważne wyrazów w [zniesławiających](luis-concept-utterance.md) opisują informacje dotyczące [zamiar](luis-concept-intent.md), i czasami są niezbędne do niego. Jednostka jest zasadniczo elementu datatype LUIS. 

## <a name="f-measure"></a>F-miary

W [testowania partii][batch-testing], to miara dokładności uruchomienie testu.

## <a name="false-negative"></a>Ujemna wartość false (TN)

W [testowania partii][batch-testing], zniesławiających, w których aplikacja nieprawidłowo przewidzieć braku zamiar/Jednostka docelowa reprezentują punktów danych.

## <a name="false-positive"></a>Wynik fałszywie dodatni (TP)

W [testowania partii][batch-testing], zniesławiających, w których aplikacja nieprawidłowo przewidzieć istnienie zamiar/Jednostka docelowa reprezentują punktów danych.

## <a name="features"></a>Funkcje

W uczeniu maszynowym [funkcji](luis-concept-feature.md) wyróżniającą cechy lub atrybut danych obserwujące systemu.

## <a name="intent"></a>Celem

[Zamiar](luis-concept-intent.md) reprezentuje zadania lub akcji, które użytkownik chce wykonać. Jest to cel lub celem wyrażone w danych wejściowych użytkownika, takie jak rezerwacji lot, zwracając rachunek lub wyszukiwanie artykułu wiadomości. W LUIS konwersji prognozowania opiera się na cały utterance. Jednostki, to w porównaniu fragmenty utterance.

## <a name="labeling"></a>etykietowania

Etykietowanie jest proces kojarzenia słowo lub frazę w zamiar [utterance](#utterance) z [jednostki](#entity) (typ danych). 

## <a name="luis-app"></a>LUIS aplikacji

Modelu danych przeszkolone w celu uwzględnienia przetwarzania języka naturalnego jest aplikacji LUIS [intencje](#intent), [jednostek](#entity)i labeled [zniesławiających](#utterance).

## <a name="owner"></a>Właściciel

Każda aplikacja ma jednego właściciela, który jest osoba, która utworzona aplikacja. Można dodać właściciela [współpracownicy](#collaborator).

## <a name="pattern"></a>Wzorce
Poprzednia funkcja wzorzec jest zastępowany [wzorców](luis-concept-patterns.md). Użycie wzorców w celu zwiększenia dokładności prognozy podając mniej przykłady szkolenia. 

## <a name="phrase-list"></a>Lista fraz

A [listy frazy](luis-concept-feature.md#what-is-a-phrase-list-feature) znajduje się grupa wartości (słów ani fraz), które należą do tej samej klasy i muszą być traktowane podobnie (na przykład nazwy miasta lub produktów). Wymienne lista jest traktowana jako synonimy. 

## <a name="prebuilt-domains"></a>Wbudowane domeny

A [wbudowane domeny](luis-how-to-use-prebuilt-domains.md) jest to aplikacja LUIS skonfigurowane dla określonej domeny, takie jak macierzystego automatyzacji (HomeAutomation) lub zastrzeżeń restauracji (RestaurantReservation). Profile, zniesławiających i jednostki są skonfigurowane dla tej domeny. 

## <a name="prebuilt-entity"></a>Wbudowane jednostki

A [wbudowane jednostki](pre-builtentities.md) jest jednostką LUIS przewiduje typowych informacji takich jak numer, adres URL i poczty e-mail. Istnieje możliwość dodawania wbudowane jednostki do aplikacji. 

## <a name="precision"></a>dokładność
W [testowania partii][batch-testing], dokładność (nazywanych również wartość dodatnią predykcyjnej) jest część odpowiednich zniesławiających między zniesławiających pobrane.

## <a name="programmatic-key"></a>Klucz programowy

Zmieniono nazwę, aby [tworzenia klucza](#authoring-key). 

## <a name="publish"></a>Publikowanie

Publikowanie sposób LUIS [aktywnej wersji](#active-version) w przemieszczania lub w środowisku produkcyjnym [punktu końcowego](#endpoint).  

## <a name="quota"></a>limit przydziału

Przydział LUIS jest ograniczenie [warstwy subskrypcji platformy Azure](https://aka.ms/luis-price-tier). Przydział LUIS można ograniczone zarówno żądań na sekundę (429 stanu HTTP), a całkowita liczba żądań w miesiącu (Stan HTTP 403). 

## <a name="recall"></a>Odwołania
W [testowania partii][batch-testing], odwołaj (znanego również jako czułości), pozwala LUIS do uogólnienia. 

## <a name="semantic-dictionary"></a>Słownik semantycznego
Słownik semantycznego znajduje się na stronie jednostki listy, a także frazy strony listy. Słownik semantyczne zawiera propozycje słów na podstawie bieżącego zakresu.

## <a name="sentiment-analysis"></a>Analiza wskaźniki nastrojów klientów
Wskaźniki nastrojów klientów analizy zawiera wartości dodatnie lub ujemne zniesławiających dostarczonych przez [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/). 

## <a name="speech-priming"></a>Plucia mowy

Plucia mowy umożliwia być przygotowywany z modelem LUIS usługa mowy. Zobacz [włączyć plucia mowy ](publishapp.md#enable-speech-priming).

## <a name="spelling-correction"></a>Poprawa pisowni

Na stronie publikowania włączyć [sprawdzania pisowni usługi Bing](publishapp.md#enable-bing-spell-checker) można poprawić pisowni w zniesławiających przed prognozowania. 

## <a name="starter-key"></a>Klucz początkowe

Taki sam jak [klucza programowego](#programmatic-key), zmieniono nazwę do tworzenia klucza.

## <a name="subscription-key"></a>Klucz subskrypcji

Klucz subskrypcji jest skojarzony z usługą LUIS [utworzona na platformie Azure](luis-how-to-azure-subscription.md). Ten klucz nie jest [tworzenia klucza](#programmatic-key). Jeśli masz klucz subskrypcji, stosuje się dla wszystkich żądań punktu końcowego, zamiast tworzenia klucza. Widać bieżącego klucza subskrypcji wewnątrz adres URL punktu końcowego w dolnej części [ **publikowania aplikacji** strony](publishapp.md) w [LUIS](luis-reference-regions.md) witryny sieci Web. Jest to wartość **klucza subskrypcji** para nazwa/wartość. 

## <a name="test"></a>Test

[Testowanie](train-test.md#test-your-app) aplikacji LUIS oznacza powoduje przekazanie utterance do LUIS i wyświetlanie JSON.

## <a name="timezoneoffset"></a>Przesunięcie strefy czasowej

Punkt końcowy zawiera wartości timezoneOffset. Jest to liczba minut, aby dodać lub usunąć z datetimeV2 wbudowane jednostki. Na przykład jeśli jest utterance "czas, jaki jest teraz?", datetimeV2 zwrócił jest bieżący czas na żądanie klienta. Jeśli żądanie klienta jest pochodzi z robotów lub innej aplikacji, która nie jest taka sama jak Twoje bot użytkownika, należy przekazać w przesunięcie między bot i użytkownika. 

Zobacz [Zmień strefę czasową jednostki wbudowane datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Token jest najmniejsza jednostka, która może być oznaczony jako jednostki. Tokenizacji opiera się na aplikacji [kultury](luis-supported-languages.md#tokenization).

## <a name="train"></a>Pociągu

Szkolenie jest proces uczenia LUIS o wszelkich zmianach [aktywnej wersji](#active-version) od czasu ostatniego szkolenia.

## <a name="true-negative"></a>Ujemna wartość true (TN)

W [testowania partii][batch-testing], zniesławiających, w których aplikacja poprawnie przewidzieć braku zamiar/Jednostka docelowa reprezentują punktów danych.

## <a name="true-positive"></a>Dodatnia wartość true (TP)

W [testowania partii][batch-testing], zniesławiających, w których aplikacja poprawnie przewidzieć istnienie zamiar/Jednostka docelowa reprezentują punktów danych.

## <a name="utterance"></a>Utterance

Utterance oznacza języka naturalnego takie jak "biletów książki 2 do Seattle następny wtorek". Przykład zniesławiających są dodawane do celem. 

## <a name="version"></a>Wersja

LUIS [wersji](luis-how-to-manage-versions.md) to model określonych danych skojarzonych z Identyfikatorem aplikacji LUIS i opublikowanych punktu końcowego. Każda aplikacja LUIS ma co najmniej jedną wersję.

[batch-testing]: train-test.md#batch-testing
