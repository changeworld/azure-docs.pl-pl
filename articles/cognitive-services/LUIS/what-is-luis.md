---
title: Co to jest usługa Language Understanding (LUIS)?
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) to oparta na chmurze usługa interfejsu API, która stosuje dostosowane techniki analizy i uczenia maszynowego do wypowiedzi użytkownika w języku naturalnym, aby rozpoznać ich ogólne znaczenie i wydobyć istotne szczegółowe informacje.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 3d4251613ac2a00ddc56d5e573b49ced01adf61c
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703111"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest usługa Language Understanding (LUIS)?

Language Understanding (LUIS) to oparta na chmurze usługa interfejsu API, która stosuje dostosowane techniki analizy i uczenia maszynowego do wypowiedzi użytkownika w języku naturalnym, aby rozpoznać ich ogólne znaczenie i wydobyć istotne szczegółowe informacje. 

Aplikacją kliencką dla usługi LUIS może być dowolna aplikacja konwersacyjna, która komunikuje się z użytkownikiem w naturalnym języku, aby wykonać zadanie. Przykładami aplikacji klienckich są aplikacje mediów społecznościowych, czatboty i aplikacje klasyczne z funkcją rozpoznawania mowy.  

![Ilustracja przedstawiająca 3 aplikacje klienckie współdziałające z usługą Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Ilustracja przedstawiająca 3 aplikacje klienckie współdziałające z usługą Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Korzystanie z usługi LUIS z czatbotami

<a name="Accessing-LUIS"></a>

Po opublikowaniu aplikacji LUIS aplikacja kliencka wysyła wyrażenia długości (tekst) do [interfejsu API][endpoint-apis] punktu końcowego Luis języka naturalnego przetwarzania i otrzymuje wyniki jako odpowiedzi JSON. Typową aplikacją kliencką dla usługi LUIS jest czatbot.


![Ilustracje przedstawiające współdziałanie usługi LUIS z czatbotem w celu przewidywania tekstu użytkownika z użyciem przetwarzania języka naturalnego](./media/luis-overview/luis-overview-process-2.png "Ilustracje przedstawiające współdziałanie usługi LUIS z czatbotem w celu przewidywania tekstu użytkownika z użyciem przetwarzania języka naturalnego")

|Czynność|Działanie|
|:--|:--|
|1|Aplikacja kliencka wysyła _wypowiedź_ użytkownika (tekst wypowiadany własnymi słowami): „I want to call my HR rep” („Chcę zadzwonić do mojego przedstawiciela działu kadr”) do punktu końcowego usługi LUIS jako żądanie HTTP.|
|2|Usługa LUIS stosuje model wyuczony do tekstu w języku naturalnym, aby umożliwić inteligentne zrozumienie danych wejściowych użytkownika. Usługa LUIS zwraca odpowiedź w formacie JSON z najwyżej ocenioną intencją: „HRContact”. Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również zawierać wyodrębnione dane, takie jak jednostka Typ kontaktu.|
|3|Aplikacja kliencka używa odpowiedzi w formacie JSON do podejmowania decyzji dotyczących sposobu realizacji żądania użytkownika. Te decyzje mogą obejmować drzewo decyzyjne w kodzie struktury bota i wywołania innych usług. |

Aplikacja usługi LUIS przeprowadza analizy, dzięki którym aplikacja kliencka może dokonać inteligentnego wyboru. Sama usługa LUIS nie dokonuje tych wyborów. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Przetwarzanie języka naturalnego

Aplikacja usługi LUIS zawiera model języka naturalnego specyficzny dla domeny. Możesz uruchomić aplikację LUIS ze wstępnie utworzonym modelem domeny, utworzyć własny model lub połączyć elementy wstępnie utworzonej domeny z własnymi informacjami.

* **Model wstępnie utworzony** usługi LUIS zawiera wiele wstępnie utworzonych modeli domeny, w tym intencje, wypowiedzi i wstępnie utworzone jednostki. Możesz skorzystać z wstępnie utworzonych jednostek bez konieczności korzystania z intencji i wypowiedzi wstępnie utworzonego modelu. [Wstępnie utworzone modele domeny](luis-how-to-use-prebuilt-domains.md) zawierają kompletny projekt i są świetnym sposobem, aby szybko rozpocząć korzystanie z usługi LUIS.

* **Jednostki niestandardowe** usługi LUIS oferują kilka sposobów określania własnych niestandardowych intencji i jednostek, w tym jednostek uczenia maszynowego, jednostek specyficznych lub literałów oraz kombinacji uczenia maszynowego i literałów.

## <a name="build-the-luis-model"></a>Tworzenie modelu w usłudze LUIS
Utwórz model za pomocą interfejsów API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) lub portalu LUIS.

Tworzenie modelu w usłudze LUIS rozpoczyna się od kategorii intencji użytkownika o nazwie **[Intencje](luis-concept-intent.md)** . Każda intencja musi zawierać przykłady **[wypowiedzi](luis-concept-utterance.md)** użytkownika. Każda wypowiedź może zawierać szereg danych do wyodrębnienia, określonych za pomocą **[jednostek](luis-concept-entity-types.md)** . 

|Przykładowa wypowiedź użytkownika|Intencja|Jednostki|
|-----------|-----------|-----------|
|„Book a flight to __Seattle__?” („Zarezerwuj lot do Seattle”)|BookFlight|Seattle|
|„When does your store __open__?” („Jakie są godziny otwarcia sklepu?”)|StoreHoursAndLocation|open|
|„Schedule a meeting at __1pm__ with __Bob__ in Distribution” („Zaplanuj spotkanie o 13 z Bobem z działu dystrybucji”)|ScheduleMeeting|1pm, Bob|

## <a name="query-prediction-endpoint"></a>Punkt końcowy przewidywania zapytania

Gdy model zostanie utworzony i opublikowany w punkcie końcowym, aplikacja kliencka wysyła wypowiedzi do interfejsu API opublikowanego [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) przewidywania. Interfejs API wykorzystuje ten model do analizy tekstu. Interfejs API przesyła odpowiedzi zawierające wyniki przewidywania w formacie JSON.  

Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również zawierać wyodrębnione dane, takie jak jednostka **Typ kontaktu**. 

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

## <a name="improve-model-prediction"></a>Doskonalenie przewidywania z użyciem modelu

Po opublikowaniu modelu usługi LUIS i rozpoczęciu odbierania rzeczywistych wypowiedzi użytkowników usługa LUIS udostępnia kilka metod poprawy dokładności przewidywania: [aktywne uczenie](luis-concept-review-endpoint-utterances.md) na podstawie wypowiedzi z punktu końcowego, [listy fraz](luis-concept-feature.md) pozwalające dołączyć słowa do domeny oraz [wzorce](luis-concept-patterns.md) zmniejszające liczbę potrzebnych wypowiedzi.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Cykl projektowania oprogramowania
Usługa LUIS zapewnia narzędzia, możliwość przechowywania wersji i współpracę z innymi twórcami usługi LUIS w celu integracji pełnego cyklu projektowania oprogramowania na poziomie aplikacji klienckiej i modelu językowego. 

## <a name="implementing-luis"></a>Wdrażanie usługi LUIS
Usługa LUIS, tak jak interfejs API REST, może być używana z dowolnymi produktami, usługami lub strukturami wysyłającymi żądania HTTP. Poniższa lista zawiera główne produkty i usługi firmy Microsoft, których można używać z usługą LUIS.

Najważniejszą aplikacją kliencką dla usługi LUIS jest:
* [Bot usługi Web Apps](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) szybko tworzy czatbota obsługującego usługę LUIS, który rozmawia z użytkownikiem za pośrednictwem tekstowych danych wejściowych. Używa programu [bot Framework][bot-framework] w wersji [4. x](https://github.com/Microsoft/botbuilder-dotnet) , aby uzyskać pełny bot środowisko.

Narzędzia do szybkiego i łatwego korzystania z usługi LUIS z botem:
* [interfejs wiersza polecenia Luis](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Pakiet NPM zapewnia tworzenie i prognozowanie za pomocą autonomicznego narzędzia wiersza polecenia lub jako importu. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) — to narzędzie do generowania silnie typizowanego języka C# i kodu źródłowego języka Typescript z wyeksportowanego modelu usługi LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) — umożliwia używanie kilku aplikacji LUIS lub QnA Maker z poziomu aplikacji nadrzędnej za pomocą modelu dyspozytora.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) — to narzędzie wiersza polecenia, które pomaga zarządzać modelami językowymi bota.

Inne usługi Cognitive Services używane z usługą LUIS:
* [QNA Maker][qnamaker] umożliwia łączenie kilku typów tekstu z bazą wiedzy pytań i odpowiedzi.
* [Interfejs API sprawdzania pisowni Bing](../bing-spell-check/proof-text.md) zapewnia korektę tekstu przed przewidywaniem. 
* [Usługa rozpoznawania mowy](../Speech-Service/overview.md) konwertuje żądania w języku mówionym na tekst. 
* [Uczeń konwersacji](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umożliwia stworzenie rozmów z botem szybciej niż w usłudze LUIS.
* [Projekt rozmowy dotyczącej osobowości](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) do obsługi niezobowiązujących rozmów z botami.

Przykłady korzystające z usługi LUIS:
* Repozytorium GitHub [Conversational AI](https://github.com/Microsoft/AI)
* Przykłady platformy Azure [Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding)

## <a name="next-steps"></a>Następne kroki

Utwórz nową aplikację usługi LUIS za pomocą domeny [wstępnie utworzonej](luis-get-started-create-app.md) lub [niestandardowej](luis-quickstart-intents-only.md). [Wyślij zapytanie do punktu końcowego przewidywania](luis-get-started-get-intent-from-browser.md) publicznej aplikacji IoT.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
