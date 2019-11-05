---
title: Słownik — LUIS
titleSuffix: Azure Cognitive Services
description: Słownik objaśnia warunki, które mogą wystąpić podczas pracy z usługą interfejsu API LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 22e8fa8fb6999828076ea5f8f34b1f601b920013
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499572"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Słownik dotyczący typowego słownictwa i koncepcji
W słowniku Language Understanding (LUIS) objaśniono warunki, które mogą wystąpić podczas pracy z usługą interfejsu API LUIS.

## <a name="active-version"></a>Aktywna wersja

Aktywna wersja usługi LUIS to wersja, która otrzymuje zmiany w modelu. Jeśli chcesz wprowadzić zmiany w wersji, która nie jest wersją aktywną, w portalu [Luis](luis-reference-regions.md) należy najpierw ustawić tę wersję jako aktywną.

## <a name="authoring"></a>Projekt

Tworzenie umożliwia tworzenie i wdrażanie [aplikacji Luis](#luis-app)oraz zarządzanie nią przy użyciu portalu [Luis](luis-reference-regions.md) lub [interfejsów API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a>Klucz tworzenia

Wcześniej nazwany kluczem "programowy". Służy do tworzenia aplikacji. Nieużywane dla zapytań punktów końcowych na poziomie produkcyjnym. Aby uzyskać więcej informacji, zobacz [limity kluczy](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Plik JSON tekstu wsadowego

Testowanie wsadowe to możliwość weryfikacji bieżącego modelu aplikacji LUIS przy użyciu spójnego i znanego zestawu testów wyrażenia długości użytkownika. Test wsadowy jest zdefiniowany w [pliku sformatowanym w formacie JSON](luis-concept-batch-test.md#batch-file-format).

Zobacz też: 
* [Pojęcia](luis-concept-batch-test.md)
* [Instrukcje](luis-how-to-batch-test.md)
* [Samouczek](luis-tutorial-batch-testing.md)


## <a name="collaborator"></a>Współpracownika

Współpracownik/współautor nie jest [właścicielem](#owner) aplikacji, ale ma takie same uprawnienia, aby dodawać, edytować i usuwać intencje, jednostki, wyrażenia długości.

## <a name="contributor"></a>Trybu

Współautor jest tym samym elementem co [współpracownik](#collaborator).

## <a name="descriptor"></a>Opis

Deskryptor jest [funkcją](#features) zastosowana do modelu w czasie uczenia, w tym [listy zwrotów](#phrase-list) i [jednostek](#entity). 

## <a name="domain"></a>Domeny

W kontekście LUIS **domena** jest obszarem wiedzy. Twoja domena jest specyficzna dla Twojej dziedziny Twojej aplikacji. Może to być ogólny obszar, taki jak aplikacja agenta podróży. Aplikacja agenta podróży może być również specyficzna dla tylko obszarów informacji dla firmy, takich jak określone lokalizacje geograficzne, Języki i usługi.

## <a name="endpoint"></a>Punktu końcowego

Adres URL [punktu końcowego Luis](https://go.microsoft.com/fwlink/?linkid=2092356) to miejsce, w którym przesyłane są zapytania Luis po utworzeniu i opublikowaniu [aplikacji Luis](#luis-app) . Adres URL punktu końcowego zawiera region opublikowanej aplikacji oraz identyfikator aplikacji. Punkt końcowy można znaleźć na stronie **[klucze i punkty końcowe](luis-how-to-azure-subscription.md)** aplikacji lub uzyskać adres URL punktu końcowego za pomocą interfejsu API [pobierania informacji o aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

## <a name="entity"></a>Podmiotów

[Jednostki](luis-concept-entity-types.md) są ważnymi wyrazami w [wyrażenia długości](luis-concept-utterance.md) , które opisują informacje istotne dla [zamiaru](luis-concept-intent.md)i czasami są dla nich niezbędne. Jednostka jest zasadniczo typem danych w LUIS.

## <a name="f-measure"></a>Miara F

W [testach wsadowych](luis-interactive-test.md#batch-testing)mierzy się dokładność testu.

## <a name="false-negative"></a>Wartość fałszywa ujemna (FN)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych przedstawiają wyrażenia długości, w których aplikacja niepoprawnie przewidywalna nieobecność docelowego celu/jednostki.

## <a name="false-positive"></a>Fałszywie dodatnie (FP)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych reprezentują wyrażenia długości, w których aplikacja niepoprawnie przewidywalna istnienie docelowego celu/jednostki.

## <a name="features"></a>Oferowanych

W uczeniu maszynowym [Funkcja](luis-concept-feature.md) jest odróżnianą cechą lub atrybutem danych, które są przestrzegane przez system.

## <a name="intent"></a>Intencji

[Cel](luis-concept-intent.md) reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub cel wyrażony w danych wejściowych użytkownika, na przykład w przypadku rezerwacji lotu, płacenia rachunku lub znajdowania artykułu z wiadomościami. W LUIS prognozowanie zamierzania opiera się na całym wypowiedź. Jednostki, porównując, są elementami wypowiedź.

## <a name="labeling"></a>Etykietowania

Oznacza to, że jest to proces kojarzenia wyrazu lub frazy w [wypowiedź](#utterance) zamiaru z [jednostką](#entity) (DataType).

## <a name="luis-app"></a>Aplikacja LUIS

Aplikacja LUIS to zbiór modeli języka do przetwarzania języka naturalnego, w tym [intencje](#intent), [podmioty](#entity)i etykiety [wyrażenia długości](#utterance).

## <a name="owner"></a>Właociciela

Każda aplikacja ma jednego właściciela, który jest osobą, która utworzyła aplikację. Właściciel może dodawać [współpracowników](#collaborator).

## <a name="pattern"></a>Modele
Funkcja poprzedniego wzorca jest zastępowana [wzorcami](luis-concept-patterns.md). Użyj wzorców, aby poprawić dokładność przewidywania, oferując mniejszą liczbę przykładów szkoleniowych.

## <a name="phrase-list"></a>Lista fraz

[Lista fraz](luis-concept-feature.md) zawiera grupę wartości (wyrazów lub fraz) należących do tej samej klasy i musi być traktowana podobnie (na przykład nazwy miast lub produktów). Lista zamienna jest traktowana jako synonimy.

## <a name="prebuilt-domains"></a>Preskompilowana domena

[Preskompilowana domena](luis-how-to-use-prebuilt-domains.md) jest aplikacją Luis skonfigurowaną dla konkretnej domeny, taką jak Automatyzacja główna (HomeAutomation) lub rezerwacje restauracji (RestaurantReservation). Zamiary, wyrażenia długości i jednostki są skonfigurowane dla tej domeny.

## <a name="prebuilt-entity"></a>Wstępnie utworzona jednostka

[Wstępnie utworzona jednostka](luis-prebuilt-entities.md) to Luis jednostki zapewniające wspólne typy informacji, takie jak Number, URL i email. Wybierzesz opcję dodania wstępnie skompilowanej jednostki do aplikacji.

## <a name="precision"></a>Dokładne
W [testach wsadowych](luis-interactive-test.md#batch-testing)precyzja (zwana również dodatnią wartością predykcyjną) jest częścią odpowiednich wyrażenia długości wśród pobranego wyrażenia długościu.

## <a name="programmatic-key"></a>Klucz programistyczny

Zmieniono nazwę [klucza tworzenia](#authoring-key).

## <a name="publish"></a>Wycofywan

Opublikowanie oznacza, że dostępna jest LUIS aktywna wersja na etapie przejściowym [lub produkcyjnym](#endpoint).  

## <a name="quota"></a>Działa

Limit przydziału LUIS to ograniczenie [warstwy subskrypcji platformy Azure](https://aka.ms/luis-price-tier). Limit przydziału LUIS może być ograniczony przez oba żądania na sekundę (stan HTTP 429) i łączne żądania w miesiącu (stan HTTP 403).

## <a name="recall"></a>Wycofaniu
W [testach wsadowych](luis-interactive-test.md#batch-testing)funkcja odwoływania (znana także jako czułość) jest Luis do uogólniania.

## <a name="semantic-dictionary"></a>Słownik semantyczny
Słownik semantyczny jest dostępny na stronie jednostki listy, a także na stronie Lista fraz. Słownik semantyczny zawiera sugestie wyrazów w oparciu o bieżący zakres.

## <a name="sentiment-analysis"></a>analiza tonacji
Tonacji Analysis zawiera wartości dodatnie lub ujemne wyrażenia długości dostarczone przez [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Napełnianiu mowy

Funkcja Speech napełnianiu umożliwia korzystanie z usługi mowy z modelem LUIS.

## <a name="spelling-correction"></a>Poprawianie pisowni

Włączenie sprawdzania pisowni Bing w celu skorygowania błędnie napisanych wyrazów w wyrażenia długości przed przewidywaniam.

## <a name="starter-key"></a>Klucz początkowy

Bezpłatny klucz, który będzie używany podczas pierwszego uruchamiania przy użyciu LUIS.

## <a name="structure"></a>XML

Dodaj strukturę do jednostki uczenia maszynowego, aby zapewnić podskładniki z deskryptorami (funkcjami) i ograniczeniami (wyrażenie regularne lub Lista jednostek).

## <a name="subscription-key"></a>Klucz subskrypcji

Klucz subskrypcji to klucz **punktu końcowego przewidywania** skojarzony z usługą Luis [utworzoną na platformie Azure](luis-how-to-azure-subscription.md). Ten klucz nie jest [kluczem autorskim](#programmatic-key). Jeśli masz klucz punktu końcowego, powinien on być używany w przypadku żądań punktu końcowego zamiast klucza tworzenia. Bieżący klucz punktu końcowego można zobaczyć w adresie URL punktu końcowego u dołu strony [ **klucze i punkty końcowe** ](luis-how-to-azure-subscription.md) w witrynie sieci Web [Luis](luis-reference-regions.md) . Jest to wartość pary nazwa/wartość **klucza subskrypcji** .

## <a name="test"></a>Badan

[Testowanie](luis-interactive-test.md#test-your-app) aplikacji Luis oznacza przekazywanie wypowiedź do Luis i wyświetlanie wyników JSON.

## <a name="timezoneoffset"></a>Przesunięcie strefy czasowej

Punkt końcowy zawiera timezoneOffset. Jest to liczba minut, którą chcesz dodać lub usunąć ze wstępnie skompilowanej jednostki datetimeV2. Na przykład, jeśli wypowiedź jest "jaki jest teraz?", zwracana wartość datetimeV2 to bieżąca godzina żądania klienta. Jeśli żądanie klienta pochodzi z bot lub innej aplikacji, która nie jest taka sama jak użytkownika bot, należy przekazać przesunięcie między bot i użytkownikiem.

Zobacz [Zmiana strefy czasowej prekompilowanej jednostki datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Klucza
Token jest najmniejszą jednostką, która może być oznaczona w jednostce. Tokenizacji jest oparta na [kulturze](luis-language-support.md#tokenization)aplikacji.

## <a name="train"></a>Trasy

Szkolenie to proces uczenia LUIS o wszelkich zmianach w aktywnej wersji od ostatniego szkolenia.

## <a name="true-negative"></a>True ujemna (TN)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych reprezentują wyrażenia długości, w których aplikacja prawidłowo przewidywalna nieobecność docelowego celu/jednostki.

## <a name="true-positive"></a>Prawda dodatnia (TP)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych przedstawiają wyrażenia długości, w którym aplikacja prawidłowo przewidywalna istnienie docelowego celu/jednostki.

## <a name="utterance"></a>Wypowiedź

Wypowiedź to wyrażenie języka naturalnego, takie jak "Book 2 bilety do Seattle następnego wtorku". Do zamiaru są dodawane przykładowe wyrażenia długości.

## <a name="version"></a>Wersja

LUIS [wersja](luis-how-to-manage-versions.md) jest określonym modelem danych skojarzonym z identyfikatorem aplikacji Luis i opublikowanym punktem końcowym. Każda aplikacja LUIS ma co najmniej jedną wersję.
