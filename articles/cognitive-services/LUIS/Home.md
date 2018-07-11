---
title: Temat Language Understanding (LUIS) na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Language Understanding (LUIS) do korzystania z możliwości uczenia maszynowego do aplikacji.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: 1c68c586fb799a540f70804d181aa66b2bda9e97
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952542"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest Language Understanding (LUIS)?
Language Understanding (LUIS) to usługa oparta na chmurze, stosowaną niestandardowego uczenia maszynowego do użytkownika konwersacji, tekstu języka naturalnego do przewidywania ogólnego znaczenia i pobierania ich istotne, szczegółowe informacje. 

Aplikację kliencką dla usługi LUIS może być dowolnym konwersacji aplikację, która komunikuje się z użytkownikiem w języku naturalnym do ukończenia zadania. Przykładami aplikacji klienckich aplikacje mediów społecznościowych, czatbotów i obsługujących mowę aplikacji klasycznych.  

![Obrazu koncepcyjnego 3 aplikacji, wprowadzając informacje info usługi LUIS](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Co to jest aplikacją usługi LUIS?
Aplikacją usługi LUIS zawiera model języka naturalnego specyficznego dla domeny, które projektowania. Można rozpoczynać aplikacją usługi LUIS modelu domeny ze wstępnie utworzonych, Utwórz swoje własne lub blend rodzajów domeny wstępnie utworzone z niestandardowych informacji.

[Domeny wstępnie utworzone modele](luis-how-to-use-prebuilt-domains.md) obejmują wszystkie te fragmenty i to doskonały sposób, aby rozpocząć korzystanie z usługi LUIS szybko.

Aplikacja usługi LUIS zawiera także ustawienia integracji [współpracowników](luis-concept-collaborator.md), i [wersji](luis-concept-version.md).

## <a name="using-a-luis-app"></a>Za pomocą aplikacji usługi LUIS
<a name="Accessing-LUIS"></a> Po opublikowaniu aplikacją usługi LUIS aplikacja kliencka wysyła wypowiedzi do usługi LUIS [punktu końcowego interfejsu API] [ endpoint-apis] i otrzymuje wyniki przewidywań jako odpowiedzi JSON.

Na poniższym diagramie najpierw chatbot Twojego klienta wysyła tekst użytkownika, z czego chce dana osoba własnymi słowami do usługi LUIS w żądaniu HTTP. Po drugie usługi LUIS dotyczy nauczony model języka naturalnego, aby analizować dane wejściowe użytkownika i zwraca odpowiedź do formatu JavaScript Object Notation (JSON). Po trzecie chatbot Twojego klienta używa odpowiedź JSON do spełnienia żądania użytkownika. 

![Koncepcyjny obrazach Praca z Chatbot usługi Luis](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>Przykład odpowiedź w formacie JSON punktu końcowego

Odpowiedzi JSON punktu końcowego, co najmniej zawiera wypowiedź zapytania i oceniania intencji u góry. 

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
Model zaczyna się od listy intencji ogólnymi użytkowników o nazwie _intencji_, takich jak "Lot książki" lub "Skontaktuj się z pomocą techniczną." Podaj tekst przykład użytkownika, o nazwie _wypowiedzi przykład_ dla intencji. Następnie oznaczyć znaczące wyrazy lub frazy w wypowiedź, o nazwie _jednostek_.


Model obejmuje:

* **[intencji](#intents)**: kategorii użytkownika intencji (zamierzonej akcji lub wyników)
* **[jednostki](#entities)**: określonych typów danych w wypowiedzi, takie jak liczba, adres e-mail lub nazwa
* **[przykład wypowiedzi](#example-utterances)**: przykładowego tekstu przez użytkownika w aplikacji klienckiej

### <a name="intents"></a>Intencje 
[Intencji](luis-how-to-add-intents.md), mała w przypadku _zamiar_, jest cel lub celem wyrażone w wypowiedź użytkownika, takie jak rezerwacji lotu, zwracając rachunku lub wyszukiwanie artykułu z wiadomościami. Możesz utworzyć intencji dla każdej akcji. Aplikacją usługi LUIS podróży mogą definiować intencji o nazwie "BookFlight." Aplikacja kliencka służy górnej oceniania celem ma wyzwolić akcję. Na przykład gdy celem "BookFlight" jest zwracany z usługi LUIS, Twoja aplikacja kliencka może wywołać wywołanie interfejsu API do usługi zewnętrznej dla rezerwacji bilet płaszczyzny.

### <a name="entities"></a>Jednostki
[Jednostki](luis-how-to-add-entities.md) przedstawia szczegółowe informacje można znaleźć w wypowiedź, która jest odpowiednia dla żądania użytkownika. Na przykład w wypowiedź "Book biletu do Paryża" pojedynczy bilet jest wymagane, a "Paryż" prowadzi do lokalizacji. "Biletu" pojedynczy bilet, a "Paryż", wskazująca miejsce docelowe znajdują się dwie jednostki. 

Po LUIS zwraca jednostek w wypowiedź użytkownika, listę jednostek jako parametry można użyć w aplikacji klienckiej ma wyzwolić akcję. Na przykład rezerwacji lotu wymaga jednostki, takie jak docelowy podróży, datę i linie lotnicze.

Usługa LUIS oferuje kilka sposobów na identyfikowanie i klasyfikowanie jednostek.

* **Wstępnie utworzonych jednostek** LUIS ma wiele modeli domeny wbudowanych, łącznie z opcjami, wypowiedzi, i [ze wstępnie utworzonych jednostek](luis-prebuilt-entities.md). Za pomocą wstępnie utworzonych jednostek bez konieczności używania intencje i wypowiedzi wbudowanych modelu. Wstępnie utworzone jednostki zaoszczędzić czas.

* **Jednostki niestandardowe** LUIS oferuje kilka sposobów, aby zidentyfikować Twoje własne, niestandardowe [jednostek](luis-concept-entity-types.md) m.in. maszyny do opanowania jednostki określonych lub literałem jednostek i kombinację przedstawiono maszyny i literału.

### <a name="example-utterances"></a>Przykładowe wypowiedzi
Przykład [wypowiedź](luis-how-to-add-example-utterances.md) jest wprowadzanie tekstu przez użytkownika, który aplikacja kliencka musi zrozumieć. Może być dowolne zdanie, takich jak "Zarezerwuj biletu do Paryża" lub fragment zdania, takich jak "Rezerwacji" lub "Paryż lot." Wypowiedzi nie zawsze są poprawnie sformułowany i może istnieć wiele odmiany wypowiedź konkretnego zamiaru. Dodawanie wypowiedzi przykład 10-20 na każdy intencje i oznaczanie jednostek, w każdym wypowiedź.

|Przykład użytkownika wypowiedź|Przeznaczenie|Jednostki|
|-----------|-----------|-----------|
|"Zarezerwuj lot __Seattle__?"|BookFlight|Seattle|
|"Gdy jest sklepu __Otwórz__?"|StoreHoursAndLocation|otwórz|
|"Zaplanuj spotkanie w __13: 00__ z __Bob__ w dystrybucji"|ScheduleMeeting|13: 00, Roberta|

## <a name="improve-prediction-accuracy"></a>Zwiększanie dokładności przewidywania
Po opublikowaniu aplikacją usługi LUIS odbiera wypowiedzi rzeczywistego użytkownika, LUIS udostępnia kilka metod w celu zwiększenia dokładności prognozy: [aktywne uczenie](#active-learning) z wypowiedzi punktu końcowego [frazę list](#phrase-lists) dla domeny Word operacji dołączania i [wzorców](#patterns) do zmniejszenia liczby wypowiedzi potrzebne.

### <a name="active-learning"></a>Aktywne uczenie
W [aktywne uczenie](luis-how-to-review-endoint-utt.md) procesu usługi LUIS pozwala na dostosowanie aplikacją usługi LUIS do rzeczywistych wypowiedzi wybierając wypowiedzi otrzymał w punkcie końcowym do przejrzenia. Można zaakceptować lub Popraw endpoint prognoz, ponownego próbkowania i ponownie opublikować. Usługa LUIS uczy się szybko z tym procesem iteracyjnym, biorąc minimalna ilość czasu i wysiłku. 

### <a name="phrase-lists"></a>Listy fraz 
Udostępnia usługi LUIS [określającego list](luis-concept-feature.md) , dzięki czemu można wskazać ważne słów i fraz, które znajdują się do modelu domeny. Usługa LUIS używa tych list można dodać dodatkowe znaczenie tych słów i fraz, które mogłyby w przeciwnym razie nie można znaleźć w modelu.

### <a name="patterns"></a>Wzorce 
Wzorce pozwalające na uproszczenie intencji wypowiedź kolekcji do postaci typowych [szablony](luis-concept-patterns.md) word wybór i kolejność słów. Dzięki temu usługa LUIS dowiedzieć się więcej dzięki szybszemu przez wymagające mniejszej liczby wypowiedzi przykład dla intencji. Wzorce są systemu hybrydowego wyrażeń regularnych i wyrażeń maszyny do opanowania. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Tworzenie i uzyskiwanie dostępu do usługi LUIS
Tworzenie aplikacji usługi LUIS z witryny sieci Web usługi LUIS lub programowo przy użyciu [tworzenia](https://aka.ms/luis-authoring-apis) interfejsów API oraz korzystanie z obu w zależności od konieczności tworzenia pakietów administracyjnych. Dostęp do opublikowanej aplikacji LUIS przez zapytanie [punktu końcowego](https://aka.ms/luis-endpoint-apis). 

Usługa LUIS zawiera trzy witryn sieci Web na świecie, w zależności od regionu tworzenia pakietów administracyjnych. Tworzenia region określa region platformy Azure, w którym można opublikować aplikacją usługi LUIS.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Dowiedz się, [więcej](luis-reference-regions.md) tworzenie i publikowanie regionów.

## <a name="what-technologies-work-with-luis"></a>Jakich technologii działa z użyciem usługi LUIS?
Wiele technologii firmy Microsoft działają z użyciem usługi LUIS:

* [Interfejs API sprawdzania pisowni Bing](../bing-spell-check/proof-text.md) zapewnia korekcji tekstu przed prognozy. 
* [Bot Framework] [ bot-framework] umożliwia chatbot rozmawiać z użytkownikiem za pośrednictwem wprowadzanie tekstu. Wybierz [3.x](https://github.com/Microsoft/BotBuilder) lub [4.x](https://github.com/Microsoft/botbuilder-dotnet) zestawu SDK dla środowiska bot kompletny.
* [Usługa QnA Maker] [ qnamaker] umożliwia wielu typom tekstu, aby połączyć w bazie wiedzy pytań i odpowiedzi.
* [Mowy](../Speech/home.md) konwertuje żądań mowy na tekst. Po konwersji na tekst, LUIS przetwarza żądania. Zobacz [zestaw SDK rozpoznawania mowy](https://aka.ms/csspeech) Aby uzyskać więcej informacji.
* [Analiza tekstu](../text-analytics/overview.md) zawiera wskaźniki nastrojów klientów analizy i klucza danych fraz.

## <a name="next-steps"></a>Kolejne kroki
Utwórz nową aplikację usługi LUIS z [wstępnie](luis-get-started-create-app.md) lub [niestandardowe](luis-quickstart-intents-only.md) domeny.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/