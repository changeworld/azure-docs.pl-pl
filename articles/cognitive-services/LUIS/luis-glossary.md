---
title: Słowniczek — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Słowniczek wyjaśnia terminy, które mogą wystąpić podczas pracy z usługą interfejsu API usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220953"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Rozumienie języka słowniczek wspólnego słownictwa i pojęć
Słownik rozumienia języka (LUIS) wyjaśnia terminy, które mogą wystąpić podczas pracy z usługą interfejsu API usługi LUIS.

## <a name="active-version"></a><a name="active-version"></a>Wersja aktywna

Aktywna wersja usługi LUIS jest wersją, która odbiera wszelkie zmiany w modelu. W portalu [usługi LUIS,](luis-reference-regions.md) jeśli chcesz wprowadzić zmiany w wersji, która nie jest aktywną wersją, musisz najpierw ustawić tę wersję jako aktywną.

## <a name="authoring"></a><a name="authoring"></a>Tworzenie

Tworzenie to możliwość tworzenia aplikacji usługi [LUIS,](#luis-app)zarządzania nią i wdrażania jej za pomocą portalu [usługi LUIS](luis-reference-regions.md) lub [interfejsów API.](https://go.microsoft.com/fwlink/?linkid=2092087)

## <a name="authoring-key"></a><a name="authoring-key"></a>Klucz tworzenia

Poprzednio nazwany klucz "Programmatic". Służy do tworzenia aplikacji. Nie jest używany dla zapytań o punkt końcowy na poziomie produkcji. Aby uzyskać więcej informacji, zobacz [Limity kluczy](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Plik JSON testu wsadowego

Testowanie wsadowe to możliwość sprawdzania poprawności modelu bieżącej aplikacji usługi LUIS za pomocą spójnego i znanego zestawu testów wypowiedzi użytkowników. Test wsadowy jest zdefiniowany w [pliku sformatowanym json](luis-concept-batch-test.md#batch-file-format).

Zobacz też:
* [Pojęcia](luis-concept-batch-test.md)
* [Instrukcje](luis-how-to-batch-test.md)
* [Samouczek](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Współpracownik

Współpracownik/współautor, nie jest [właścicielem](#owner) aplikacji, ale ma takie same uprawnienia, aby dodać, edytować i usuwać intencje, jednostki, wypowiedzi.

## <a name="contributor"></a><a name="contributor"></a>Współautor

Współpracownik jest tym samym, co [współpracownik](#collaborator).

## <a name="descriptor"></a><a name="descriptor"></a>Deskryptora

Deskryptor jest [funkcją](#features) zastosowaną do modelu w czasie szkolenia, w tym [listami fraz](#phrase-list) i [encjami](#entity). 

## <a name="domain"></a><a name="domain"></a>Domain

W kontekście usługi LUIS **domena** jest obszarem wiedzy. Twoja domena jest specyficzna dla obszaru wiedzy aplikacji. Może to być ogólny obszar, taki jak aplikacja biura podróży. Aplikacja biura podróży może być również specyficzna tylko dla obszarów informacji dla twojej firmy, takich jak określone lokalizacje geograficzne, języki i usługi.

## <a name="endpoint"></a><a name="endpoint"></a>Punktu końcowego

Adres URL [punktu końcowego usługi LUIS](https://go.microsoft.com/fwlink/?linkid=2092356) jest, gdzie przesyłane zapytania usługi LUIS po aplikacji usługi [LUIS](#luis-app) jest autorstwa i opublikowane. Adres URL punktu końcowego zawiera region opublikowanej aplikacji, a także identyfikator aplikacji. Punkt końcowy można znaleźć na **[klucze i punkty końcowe](luis-how-to-azure-subscription.md)** strony aplikacji lub można uzyskać adres URL punktu końcowego z interfejsu API [Pobierz informacje o aplikacji.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)

## <a name="entity"></a><a name="entity"></a>Jednostka

[Jednostki](luis-concept-entity-types.md) są ważne słowa w [wypowiedzi,](luis-concept-utterance.md) które opisują informacje istotne dla [intencji](luis-concept-intent.md), a czasami są one niezbędne do niego. Jednostka jest zasadniczo rodzajem danych w usłudze LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>Środek F

W [badaniu wsadowym](luis-interactive-test.md#batch-testing)miara dokładności badania.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Fałsz ujemny (FN)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych reprezentują wypowiedzi, w których aplikacja niepoprawnie przewidziała brak docelowej intencji/jednostki.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Fałszywie dodatni (FP)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych reprezentują wypowiedzi, w których aplikacja niepoprawnie przewidziała istnienie docelowej intencji/jednostki.

## <a name="features"></a><a name="features"></a>Funkcje

W uczeniu maszynowym [funkcja](luis-concept-feature.md) jest cechą wyróżniającą lub atrybutem danych, które obserwuje system.

## <a name="intent"></a><a name="intent"></a>Intencja

[Intencja](luis-concept-intent.md) reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub cel wyrażony w danych wejściowych użytkownika, takich jak rezerwacja lotu, płacenie rachunku lub znalezienie artykułu informacyjnego. W usłudze LUIS przewidywanie intencji opiera się na całej wypowiedź. Jednostki, dla porównania, są fragmenty wypowiedź.

## <a name="labeling"></a><a name="labeling"></a>Etykietowania

Etykietowanie lub znakowanie jest procesem kojarzenia wyrazu lub frazy w [wypowiedź](#utterance) intencji z [jednostką](#entity) (rodzajem danych).

## <a name="luis-app"></a><a name="luis-app"></a>Aplikacja USŁUGI LUIS

Aplikacja usługi LUIS to zbiór modeli języków do przetwarzania języka naturalnego, w tym [intencji,](#intent) [jednostek](#entity)i [wypowiedzi oznaczonych etykietą.](#utterance)

## <a name="owner"></a><a name="owner"></a>Właściciel

Każda aplikacja ma jednego właściciela, który jest osobą, która utworzyła aplikację. Właściciel może dodać [współpracowników](#collaborator).

## <a name="patterns"></a><a name="pattern"></a>Wzorce
Poprzednia operacja Szyku zostanie zastąpiona [wzorami](luis-concept-patterns.md). Użyj wzorców, aby poprawić dokładność przewidywania, podając mniej przykładów treningu.

## <a name="phrase-list"></a><a name="phrase-list"></a>Lista fraz

[Lista fraz](luis-concept-feature.md) zawiera grupę wartości (słów lub fraz), które należą do tej samej klasy i muszą być traktowane podobnie (na przykład nazwy miast lub produktów). Wymienna lista jest traktowana jako synonimy.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Wstępnie skompilowana domena

[Wstępnie skompilowana domena](luis-how-to-use-prebuilt-domains.md) to aplikacja usługi LUIS skonfigurowana dla określonej domeny, takiej jak automatyka domowa (HomeAutomation) lub rezerwacje restauracji (RestaurantReservation). Intencje, wypowiedzi i jednostki są skonfigurowane dla tej domeny.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Wstępnie skompilowana jednostka

[Wstępnie skompilowana jednostka](luis-prebuilt-entities.md) jest jednostką, która udostępnia typowe typy informacji, takie jak numer, adres URL i adres e-mail. Można dodać wstępnie utworzone jednostki do aplikacji.

## <a name="precision"></a><a name="precision"></a>Dokładność
W [testach wsadowych](luis-interactive-test.md#batch-testing)dokładność (nazywana również dodatnią wartością predykcyjną) jest ułamkiem odpowiednich wypowiedzi między pobranymi wypowiedziami.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Klucz programowy

Zmieniono nazwę na [klucz tworzenia](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Publikowanie

Publikowanie oznacza udostępnienie aktywnej wersji usługi LUIS w punkcie końcowym przejściowym lub [produkcyjnym.](#endpoint)  

## <a name="quota"></a><a name="quota"></a>Limit przydziału

Przydział usługi LUIS jest ograniczeniem [warstwy subskrypcji platformy Azure.](https://aka.ms/luis-price-tier) Przydział usługi LUIS może być ograniczony zarówno przez żądania na sekundę (stan HTTP 429), jak i całkowitą liczbę żądań w miesiącu (stan HTTP 403).

## <a name="recall"></a><a name="recall"></a>Przypomnieć
W [testach wsadowych](luis-interactive-test.md#batch-testing), przypomnieć (znany również jako czułość), jest zdolność usługi LUIS do uogólniania.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Słownik semantyczny
Słownik semantyczny znajduje się na stronie Encja lista, a także na stronie listy Frazy. Słownik semantyczny zawiera sugestie słów na podstawie bieżącego zakresu.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Analiza tonacji
Analiza tonacji zapewnia pozytywne lub ujemne wartości wypowiedzi dostarczanych przez [analizę tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Gruntowanie mowy

Tworzenie zasysania mowy umożliwia zagruntowanie usługi mowy za pomocą modelu usługi LUIS.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Korekta pisowni

Włącz sprawdzanie pisowni Bing, aby poprawić błędnie napisane wyrazy w wypowiedziach przed przewidywaniem.

## <a name="starter-key"></a><a name="starter-key"></a>Klucz startowy

Wolny klucz do użycia podczas pierwszego uruchamiania przy użyciu usługi LUIS.

## <a name="structure"></a><a name="structure"></a>Struktura

Dodaj strukturę do jednostki nauczonej maszynowo, aby zapewnić podskładniki z deskryptorami (funkcjami) i ograniczeniami (wyrażenia regularne lub jednostki listy).

## <a name="subscription-key"></a><a name="subscription-key"></a>Klucz subskrypcji

Klucz subskrypcji to klucz **końcowy przewidywania** skojarzony z usługą usługi LUIS [utworzoną na platformie Azure.](luis-how-to-azure-subscription.md) Ten klucz nie jest [kluczem autora](#programmatic-key). Jeśli masz klucz punktu końcowego, powinien być używany dla wszystkich żądań punktu końcowego zamiast klucza tworzenia. Bieżący klucz punktu końcowego wewnątrz adresu URL punktu końcowego u dołu strony [ **Klucze i punkty końcowe** ](luis-how-to-azure-subscription.md) w [witrynie sieci Luis.](luis-reference-regions.md) Jest to wartość pary nazwa/wartość **klucza subskrypcji.**

## <a name="test"></a><a name="test"></a>Test

[Testowanie](luis-interactive-test.md#test-your-app) aplikacji usługi LUIS oznacza przekazywanie wypowiedź do usługi LUIS i wyświetlanie wyników JSON.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Przesunięcie strefy czasowej

Punkt końcowy zawiera timezoneOffset. Jest to liczba w minutach, które chcesz dodać lub usunąć z prekompilowany element datetimeV2. Na przykład jeśli wypowiedź jest "o której godzinie jest teraz?", datetimeV2 zwrócony jest bieżący czas dla żądania klienta. Jeśli żądanie klienta pochodzi z bota lub innej aplikacji, która nie jest taka sama jak użytkownik bota, należy przekazać w przesunięciu między botem a użytkownikiem.

Zobacz [Zmienianie strefy czasowej wstępnie utworzonej encji datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Tokenu
Token jest najmniejszą jednostką, która może być oznaczona etykietą w jednostce. Tokenizacja jest oparta na [kulturze](luis-language-support.md#tokenization)aplikacji.

## <a name="train"></a><a name="train"></a>Pociąg

Szkolenie jest procesem nauczania usługi LUIS o wszelkich zmianach w aktywnej wersji od ostatniego szkolenia.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Prawdziwy ujemny (TN)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych reprezentują wypowiedzi, w których aplikacja poprawnie przewidziała brak docelowej intencji/jednostki.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Prawdziwy pozytyw (TP)

W [testach wsadowych](luis-interactive-test.md#batch-testing)punkty danych reprezentują wypowiedzi, w których aplikacja poprawnie przewidziała istnienie docelowej intencji/jednostki.

## <a name="utterance"></a><a name="utterance"></a>Wypowiedź

Wypowiedź jest wyrażeniem języka naturalnego, takim jak "book 2 tickets to Seattle next Tuesday". Przykładowe wypowiedzi są dodawane do intencji.

## <a name="version"></a><a name="version"></a>Wersja

[Wersja](luis-how-to-manage-versions.md) usługi LUIS to określony model danych skojarzony z identyfikatorem aplikacji usługi LUIS i opublikowanym punktem końcowym. Każda aplikacja usługi LUIS ma co najmniej jedną wersję.
