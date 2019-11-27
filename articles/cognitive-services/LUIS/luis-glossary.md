---
title: Słownik — LUIS
titleSuffix: Azure Cognitive Services
description: Glosariusz opisano terminy, które można napotkać podczas pracy z usługą interfejsu API usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325942"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Słownik dotyczący typowego słownictwa i koncepcji
Language Understanding (LUIS) słownik wyjaśnia, warunki, które można napotkać podczas pracy z usługą interfejsu API usługi LUIS.

## <a name="active-version"></a>Aktywna wersja

Wersja usługi LUIS aktywna jest wersji, który odbiera wszystkie zmiany w modelu. Jeśli chcesz wprowadzić zmiany w wersji, która nie jest wersją aktywną, w portalu [Luis](luis-reference-regions.md) należy najpierw ustawić tę wersję jako aktywną.

## <a name="authoring"></a>Projekt

Tworzenie umożliwia tworzenie i wdrażanie [aplikacji Luis](#luis-app)oraz zarządzanie nią przy użyciu portalu [Luis](luis-reference-regions.md) lub [interfejsów API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a>Klucz tworzenia

Poprzednia nazwa klucza "Programistyczne". Używany do tworzenia aplikacji. Nie są używane dla punktu końcowego poziom produkcji zapytań. Aby uzyskać więcej informacji, zobacz [limity kluczy](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a>Plik JSON testu wsadowego

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

W kontekście LUIS **domena** jest obszarem wiedzy. Twoja domena to specyficzne dla Twojej okolicy aplikacji wiedzy. Może to być obszarze Ogólne aplikacji agenta podróży. Aplikacja podróży agent może być również specyficzne dla tylko obszarów informacje dla swojej firmy, takich jak określonych lokalizacjach geograficznych, języków i usług.

## <a name="endpoint"></a>Punktu końcowego

Adres URL [punktu końcowego Luis](https://go.microsoft.com/fwlink/?linkid=2092356) to miejsce, w którym przesyłane są zapytania Luis po utworzeniu i opublikowaniu [aplikacji Luis](#luis-app) . Adres URL punktu końcowego zawiera region opublikowanej aplikacji, a także identyfikator aplikacji. Punkt końcowy można znaleźć na stronie **[klucze i punkty końcowe](luis-how-to-azure-subscription.md)** aplikacji lub uzyskać adres URL punktu końcowego za pomocą interfejsu API [pobierania informacji o aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

## <a name="entity"></a>Podmiotów

[Jednostki](luis-concept-entity-types.md) są ważnymi wyrazami w [wyrażenia długości](luis-concept-utterance.md) , które opisują informacje istotne dla [zamiaru](luis-concept-intent.md)i czasami są dla nich niezbędne. Jednostka jest zasadniczo typu danych w usługi LUIS.

## <a name="f-measure"></a>Miara F

W [testach wsadowych](luis-interactive-test.md#batch-testing)mierzy się dokładność testu.

## <a name="false-negative"></a>Wartość fałszywa ujemna (FN)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych przedstawiają wyrażenia długości, w których aplikacja niepoprawnie przewidywalna nieobecność docelowego celu/jednostki.

## <a name="false-positive"></a>Fałszywie dodatnie (FP)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych reprezentują wyrażenia długości, w których aplikacja niepoprawnie przewidywalna istnienie docelowego celu/jednostki.

## <a name="features"></a>Oferowanych

W uczeniu maszynowym [Funkcja](luis-concept-feature.md) jest odróżnianą cechą lub atrybutem danych, które są przestrzegane przez system.

## <a name="intent"></a>Intencji

[Cel](luis-concept-intent.md) reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub celem wyrażone w danych wejściowych użytkownika, takie jak rezerwacji lotu, zwracając rachunku lub wyszukiwanie artykułu z wiadomościami. W usługi LUIS funkcja prognozowania jest oparta na cały wypowiedź. Jednostki, natomiast to fragmenty wypowiedź.

## <a name="labeling"></a>Etykietowania

Oznacza to, że jest to proces kojarzenia wyrazu lub frazy w [wypowiedź](#utterance) zamiaru z [jednostką](#entity) (DataType).

## <a name="luis-app"></a>Aplikacja LUIS

Aplikacja LUIS to zbiór modeli języka do przetwarzania języka naturalnego, w tym [intencje](#intent), [podmioty](#entity)i etykiety [wyrażenia długości](#utterance).

## <a name="owner"></a>Właociciela

Każda aplikacja ma jednego właściciela, który jest osobie, która utworzyła aplikację. Właściciel może dodawać [współpracowników](#collaborator).

## <a name="pattern"></a>Modele
Funkcja poprzedniego wzorca jest zastępowana [wzorcami](luis-concept-patterns.md). Użycie wzorców w celu zwiększenia dokładności prognozy, podając mniejszą liczbę przykładów szkoleniowych.

## <a name="phrase-list"></a>Lista fraz

[Lista fraz](luis-concept-feature.md) zawiera grupę wartości (wyrazów lub fraz) należących do tej samej klasy i musi być traktowana podobnie (na przykład nazwy miast lub produktów). Wymienne listy jest traktowany jako synonimów.

## <a name="prebuilt-domains"></a>Preskompilowana domena

[Preskompilowana domena](luis-how-to-use-prebuilt-domains.md) jest aplikacją Luis skonfigurowaną dla konkretnej domeny, taką jak Automatyzacja główna (HomeAutomation) lub rezerwacje restauracji (RestaurantReservation). Intencji, wypowiedzi oraz jednostki są skonfigurowane dla tej domeny.

## <a name="prebuilt-entity"></a>Wstępnie utworzona jednostka

[Wstępnie utworzona jednostka](luis-prebuilt-entities.md) to Luis jednostki zapewniające wspólne typy informacji, takie jak Number, URL i email. Istnieje możliwość dodawania wstępnie utworzone jednostki do aplikacji.

## <a name="precision"></a>Dokładne
W [testach wsadowych](luis-interactive-test.md#batch-testing)precyzja (zwana również dodatnią wartością predykcyjną) jest częścią odpowiednich wyrażenia długości wśród pobranego wyrażenia długościu.

## <a name="programmatic-key"></a>Klucz programistyczny

Zmieniono nazwę [klucza tworzenia](#authoring-key).

## <a name="publish"></a>Wycofywan

Opublikowanie oznacza, że dostępna jest LUIS aktywna wersja na etapie przejściowym [lub produkcyjnym](#endpoint).  

## <a name="quota"></a>Działa

Limit przydziału LUIS to ograniczenie [warstwy subskrypcji platformy Azure](https://aka.ms/luis-price-tier). Limit przydziału usługi LUIS można ograniczyć, używając zarówno w przypadku żądań na sekundę (429 stanu HTTP) i łączna liczba żądań w ciągu miesiąca (Stan HTTP 403).

## <a name="recall"></a>Wycofaniu
W [testach wsadowych](luis-interactive-test.md#batch-testing)funkcja odwoływania (znana także jako czułość) jest Luis do uogólniania.

## <a name="semantic-dictionary"></a>Słownik semantyczny
Słownik semantycznego znajduje się na stronie listy jednostek, a także na stronie listy fraz. Semantyczne słownik zawiera sugestie słów na podstawie bieżącego zakresu.

## <a name="sentiment-analysis"></a>analiza tonacji
Tonacji Analysis zawiera wartości dodatnie lub ujemne wyrażenia długości dostarczone przez [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Napełnianiu mowy

Zalewanie mowy umożliwia usługi rozpoznawania mowy w celu zalewany, Twój model usługi LUIS.

## <a name="spelling-correction"></a>Poprawianie pisowni

Włączanie sprawdzania pisowni Bing do poprawianie błędnie napisanych wyrazów w wypowiedzi przed prognozy.

## <a name="starter-key"></a>Klucz początkowy

Bezpłatny klucz, który będzie używany podczas pierwszego uruchamiania przy użyciu LUIS.

## <a name="structure"></a>XML

Dodaj strukturę do jednostki uczenia maszynowego, aby zapewnić podskładniki z deskryptorami (funkcjami) i ograniczeniami (wyrażenie regularne lub Lista jednostek).

## <a name="subscription-key"></a>Klucz subskrypcji

Klucz subskrypcji to klucz **punktu końcowego przewidywania** skojarzony z usługą Luis [utworzoną na platformie Azure](luis-how-to-azure-subscription.md). Ten klucz nie jest [kluczem autorskim](#programmatic-key). W przypadku klucza punktu końcowego powinno być używane do wszelkie żądania punktu końcowego, zamiast tworzenia klucza. Bieżący klucz punktu końcowego można zobaczyć w adresie URL punktu końcowego u dołu strony [ **klucze i punkty końcowe** ](luis-how-to-azure-subscription.md) w witrynie sieci Web [Luis](luis-reference-regions.md) . Jest to wartość pary nazwa/wartość **klucza subskrypcji** .

## <a name="test"></a>Badan

[Testowanie](luis-interactive-test.md#test-your-app) aplikacji Luis oznacza przekazywanie wypowiedź do Luis i wyświetlanie wyników JSON.

## <a name="timezoneoffset"></a>Przesunięcie strefy czasowej

Punkt końcowy zawiera timezoneOffset. Jest to liczba w ciągu kilku minut, aby dodawać i usuwać datetimeV2 wstępnie utworzone jednostki. Na przykład jeśli wypowiedź "czas, jaki jest ona teraz?", datetimeV2 zwracany jest bieżący czas dla żądania klienta. Jeśli żądania klienta pochodzą robota lub innych aplikacji, która nie jest taki sam jak Twój bot użytkownika, należy przekazać w przesunięcie między bot i użytkownika.

Zobacz [Zmiana strefy czasowej prekompilowanej jednostki datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Klucza
Token jest najmniejsza jednostka, która jest oznaczona etykietą w jednostce. Tokenizacji jest oparta na [kulturze](luis-language-support.md#tokenization)aplikacji.

## <a name="train"></a>Trasy

Szkolenie to proces uczenia LUIS o wszelkich zmianach w aktywnej wersji od ostatniego szkolenia.

## <a name="true-negative"></a>True ujemna (TN)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych reprezentują wyrażenia długości, w których aplikacja prawidłowo przewidywalna nieobecność docelowego celu/jednostki.

## <a name="true-positive"></a>Prawda dodatnia (TP)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych przedstawiają wyrażenia długości, w którym aplikacja prawidłowo przewidywalna istnienie docelowego celu/jednostki.

## <a name="utterance"></a>Wypowiedź

Wypowiedź to wyrażenie języka naturalnego, takie jak "książki 2 bilety do Seattle następny wtorek". Przykład wypowiedzi są dodawane do intencji.

## <a name="version"></a>Wersja

LUIS [wersja](luis-how-to-manage-versions.md) jest określonym modelem danych skojarzonym z identyfikatorem aplikacji Luis i opublikowanym punktem końcowym. Każda aplikacja usługi LUIS ma co najmniej jedną wersję.
