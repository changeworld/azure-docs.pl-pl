---
title: O opis języka (LUIS) na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą języka opis (LUIS) możliwości aplikacji uczenia maszynowego.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: bbd0a532e54f9b221739c8ae9ff097fe44fdc4df
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751599"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest opis języka (LUIS)?
Opis języka (LUIS) to usługa oparta na chmurze, która dotyczy niestandardowej uczeniu maszynowym konwersacji, tekstu języka naturalnego użytkownika do prognozowania ogólnego znaczenia i wysunąć istotne, szczegółowe informacje. 

Aplikacja kliencka dla LUIS można dowolnej konwersacji aplikacji, która komunikuje się z użytkownikiem w języku naturalnym do wykonania zadania. Przykładami aplikacje klienckie aplikacji mediów społecznościowych, chatbots i aplikacje z obsługą mowy.  

![Obrazu koncepcyjnego 3 aplikacji informacje info LUIS zasilania](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Co to jest aplikacja LUIS?
Aplikacji LUIS zawiera model języka naturalnego specyficznego dla domeny, które projektowania. Można uruchomić aplikację LUIS z modelem domeny wbudowane, tworzenie własnych lub blend części wbudowane domeny niestandardowe informacje o.

[Modele wbudowane domeny](luis-how-to-use-prebuilt-domains.md) zawierają wszystkie te elementy i to doskonały sposób na rozpoczęcie korzystania z LUIS szybko.

Aplikacja LUIS zawiera także ustawienia integracji [współpracownicy](luis-concept-collaborator.md), i [wersji](luis-concept-version.md).

## <a name="using-a-luis-app"></a>Za pomocą aplikacji LUIS
<a name="Accessing-LUIS"></a> Po opublikowaniu aplikacji LUIS aplikacja kliencka wysyła zniesławiających LUIS [punkt końcowy interfejsu API] [ endpoint-apis] i otrzymuje wyniki prognozowania jako odpowiedzi JSON.

Na poniższym diagramie najpierw chatbot Twojego klienta wysyła tekst użytkownika osoba chce własnymi słowami do LUIS w żądaniu HTTP. Po drugie LUIS dotyczy nauczony model języka naturalnego rozsądnie danych wejściowych użytkownika i zwraca odpowiedź formacie JavaScript Object Notation (JSON). Trzecie z chatbot klienta używa odpowiedzi JSON odpowiedzi na żądania użytkownika. 

![Koncepcyjny obrazów z LUIS Praca z Chatbot](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>Przykład odpowiedź w formacie JSON punktu końcowego

Odpowiedzi JSON punktu końcowego, co najmniej zawiera utterance zapytania i oceniania zamiar górnej. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>Co to jest model języka naturalnego?
Model rozpoczyna się od listy zamiarach użytkownika ogólne, nazywany _intencje_, na przykład "Książki lot" lub "Skontaktuj się z pomocą techniczną." Podaj przykładowy tekst użytkownika, nazywany _zniesławiających przykład_ dla lokalizacji docelowych. Następnie oznacz znaczących słów ani fraz w utterance, nazywany _jednostek_.


Model zawiera następujące elementy:

* **[intencje](#intents)**: kategorie zamiarach użytkownika (zamierzonej akcji lub wynik)
* **[jednostki](#entities)**: określonych typów danych w zniesławiających, takie jak nazwa, wiadomości e-mail lub numer
* **[przykład zniesławiających](#example-utterances)**: Przykładowy tekst użytkownik wprowadzi w aplikacji klienta

### <a name="intents"></a>Intencje 
[Zamiar](luis-how-to-add-intents.md), skrót _zamiar_, jest celem lub celem wyrażone w utterance użytkownika, takie jak rezerwacji lot, zwracając rachunek lub wyszukiwanie artykułu wiadomości. Możesz utworzyć celem dla każdej akcji. Aplikacja podróży LUIS może zdefiniować celem o nazwie "BookFlight." Aplikacja kliencka służy górnej oceniania zamiar akcja wyzwalacza. Na przykład "BookFlight" zamiar zwracanie z LUIS aplikacji klienta może wyzwolić wywołanie interfejsu API dla rezerwacji biletu płaszczyzny zewnętrznej usługi.

### <a name="entities"></a>Jednostki
[Jednostki](luis-how-to-add-entities.md) reprezentuje szczegółowe informacje w utterance, istotnych dla żądania użytkownika. Na przykład w utterance "Książki bilet, aby Paryża" bilet pojedynczego żądania, a "Paryża" prowadzi do lokalizacji. Dwie jednostki znaleziono "biletu" pojedynczego biletu i wskazujący miejsce docelowe "Paryża". 

Po LUIS zwraca jednostki, w utterance użytkownika, aplikacja klienta może używać na liście obiektów jako parametry akcja wyzwalacza. Na przykład rezerwacji lot wymaga jednostek, takich jak celem podróży, datę i linii lotniczych.

LUIS udostępnia kilka sposobów, aby zidentyfikować i kategoryzowanie jednostek.

* **Wbudowane jednostek** LUIS ma wiele modeli domeny wbudowane profile, zniesławiających, w tym i [wbudowane jednostek](pre-builtentities.md). Wbudowane jednostek można użyć bez konieczności używania intencje i zniesławiających wbudowane modelu. Wbudowane jednostki zaoszczędzić czas.

* **Niestandardowych obiektów** LUIS udostępnia kilka sposobów, aby zidentyfikować własne [jednostek](luis-concept-entity-types.md) tym jednostek rozpoznane maszyny, określonych lub literałem jednostek i kombinacji rozpoznane maszyny i literału.

### <a name="example-utterances"></a>Przykład zniesławiających
Przykład [utterance](luis-how-to-add-example-utterances.md) jest wprowadzanie tekstu od użytkownika, który aplikacja kliencka musi zrozumieć. Może być zdania, takich jak "Zarezerwować biletu do Paryża" lub fragmentu zdania, takich jak "Rezerwacji" lub "Paryża transmitowane." Zniesławiających nie zawsze są poprawnie sformułowany i może istnieć wiele zmian utterance dla konkretnego zamiaru. Dodaj 10-20 przykład zniesławiających do każdego zamiar i oznacz jednostek w każdym utterance.

|Przykład utterance użytkownika|Celem|Jednostki|
|-----------|-----------|-----------|
|"Zarezerwować lot __Seattle__?"|BookFlight|Seattle|
|"Jeśli jest sklepu __Otwórz__?"|StoreHoursAndLocation|otwórz|
|"Zaplanuj spotkanie w __13 będzie__ z __Bob__ w dystrybucji"|ScheduleMeeting|13: 00, Roberta|

## <a name="improve-prediction-accuracy"></a>Poprawić precyzję przewidywania
Po aplikacji LUIS został opublikowany i odbiera rzeczywistego użytkownika zniesławiających, LUIS udostępnia kilka metod w celu zwiększenia dokładności prognozy: [active learning](#active-learning) z punktu końcowego zniesławiających, [frazę list](#phrase-lists) dla domeny Włączenie, Word i [wzorce](#patterns) Aby zmniejszyć liczbę zniesławiających potrzebne.

### <a name="active-learning"></a>Aktywne uczenie
W [active learning](label-suggested-utterances.md) procesu LUIS umożliwia dostosowanie aplikację LUIS, do rzeczywistych zniesławiających, wybierając zniesławiających odebrała w punkcie końcowym do przeglądu. Można zaakceptować lub Popraw prognozowania punktu końcowego, ponownego próbkowania i ponownie opublikować dane. LUIS uczy się szybko z tym procesem iteracyjnym biorąc minimalna ilość czasu i uwagi. 

### <a name="phrase-lists"></a>Wyrażenie listy 
Udostępnia LUIS [zwroty list](luis-concept-feature.md) tak ważne słów ani fraz może wskazywać do modelu domeny. LUIS używa tych list można dodać dodatkowe znaczenie do tych słów i wyrażeń, które mogłyby w przeciwnym razie nie można odnaleźć w modelu.

### <a name="patterns"></a>Wzorce 
Wzorce pozwalają uprościć zamiar utterance kolekcji do wspólnego [szablony](luis-concept-patterns.md) word wybór i kolejność słów. Dzięki temu LUIS dowiedzieć się przyspieszają przez wymagające mniejszej liczby zniesławiających przykład dla opcji. Wzorce mogą system hybrydowego rozpoznane maszyny wyrażeń i wyrażenia regularne. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Do tworzenia i uzyskiwania dostępu do LUIS
Tworzenie aplikacji LUIS z witryny sieci Web LUIS lub programowo z [tworzenia](https://aka.ms/luis-authoring-apis) interfejsów API lub użyj obu w zależności od konieczność tworzenia pakietów administracyjnych. Dostęp do opublikowanej aplikacji LUIS przez zapytanie [punktu końcowego](https://aka.ms/luis-endpoint-apis). 

LUIS udostępnia trzy witryn sieci Web na świecie, w zależności od regionu tworzenia pakietów administracyjnych. Tworzenia region określa region platformy Azure, w którym można publikować aplikację LUIS.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Dowiedz się [więcej](luis-reference-regions.md) tworzenie i publikowanie regionów.

## <a name="what-technologies-work-with-luis"></a>Które technologie współpracuje z LUIS?
Kilka technologii firmy Microsoft pracować z LUIS:

* [API sprawdzania pisowni usługi Bing](../bing-spell-check/proof-text.md) zapewnia korekty tekstu przed prognozowania. 
* [Bot Framework] [ bot-framework] umożliwia chatbot do komunikowania się z użytkownikiem za pośrednictwem wprowadzania tekstu. Wybierz [3.x](https://github.com/Microsoft/BotBuilder) lub [4.x](https://github.com/Microsoft/botbuilder-dotnet) zestawu SDK dla środowiska bot pełną.
* [Maker — strona główna] [ qnamaker] umożliwia kilka typów tekstu do łączenia w bazie wiedzy pytania i odpowiedzi.
* [Mowy](../Speech/home.md) konwertuje żądań używany na tekst. Po konwersji na tekst, LUIS przetwarza żądania. Zobacz [mowy SDK](https://aka.ms/csspeech) Aby uzyskać więcej informacji.
* [Analiza tekstu](../text-analytics/overview.md) zawiera wskaźniki nastrojów klientów analizy i klucz frazy danych wyodrębniania.

## <a name="next-steps"></a>Kolejne kroki
Utwórz nową aplikację LUIS z [wbudowane](luis-get-started-create-app.md) lub [niestandardowych](luis-quickstart-intents-only.md) domeny.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/