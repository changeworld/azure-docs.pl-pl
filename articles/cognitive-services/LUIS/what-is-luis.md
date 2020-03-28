---
title: Co to jest usługa Language Understanding (LUIS)?
description: Language Understanding (LUIS) to oparta na chmurze usługa interfejsu API, która stosuje dostosowane techniki analizy i uczenia maszynowego do wypowiedzi użytkownika w języku naturalnym, aby rozpoznać ich ogólne znaczenie i wydobyć istotne szczegółowe informacje.
ms.topic: overview
ms.date: 02/23/2020
ms.openlocfilehash: 98fb936422f8e23e728efea19fa2cd75d90fac57
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053352"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest usługa Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) to oparta na chmurze usługa interfejsu API, która stosuje dostosowane techniki analizy i uczenia maszynowego do wypowiedzi użytkownika w języku naturalnym, aby rozpoznać ich ogólne znaczenie i wydobyć istotne szczegółowe informacje.

Aplikacją kliencką dla usługi LUIS może być dowolna aplikacja konwersacyjna, która komunikuje się z użytkownikiem w naturalnym języku, aby wykonać zadanie. Przykładami aplikacji klienckich są aplikacje mediów społecznościowych, czatboty i aplikacje klasyczne z funkcją rozpoznawania mowy.

![Obraz koncepcyjny 3 aplikacji klienckich pracujących z funkcją Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Obraz koncepcyjny 3 aplikacji klienckich pracujących z funkcją Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Korzystanie z usługi LUIS z czatbotami

<a name="Accessing-LUIS"></a>

Po opublikowaniu aplikacji usługi LUIS aplikacja kliencka wysyła wypowiedzi (tekst) do [interfejsu API][endpoint-apis] punktu końcowego przetwarzania języka naturalnego usługi LUIS i otrzymuje wyniki w formie odpowiedzi w formacie JSON. Typową aplikacją kliencką dla usługi LUIS jest czatbot.


![Koncepcyjne obrazy usługi LUIS współpracującej z botem czatu w celu przewidywania tekstu użytkownika ze zrozumieniem języka naturalnego (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Koncepcyjne obrazy usługi LUIS współpracującej z botem czatu w celu przewidywania tekstu użytkownika ze zrozumieniem języka naturalnego (NLP")

|Krok|Akcja|
|:--|:--|
|1|Aplikacja kliencka wysyła _wypowiedź_ użytkownika (tekst wypowiadany własnymi słowami): „I want to call my HR rep” („Chcę zadzwonić do mojego przedstawiciela działu kadr”) do punktu końcowego usługi LUIS jako żądanie HTTP.|
|2|Usługa LUIS umożliwia tworzenie niestandardowych modeli języka w celu dodania analizy do aplikacji. Modele języków nauczonych maszynowo przyjmują nieustrukturyzowanego tekstu wejściowego użytkownika i zwracają `HRContact`odpowiedź w formacie JSON z najwyższym zamiarem. Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Można również wyodrębnić dane, takie jak _jednostka Typ kontaktu._|
|3|Aplikacja kliencka używa odpowiedzi w formacie JSON do podejmowania decyzji dotyczących sposobu realizacji żądania użytkownika. Decyzje te mogą obejmować drzewo decyzji w kodzie struktury bota i wywołania innych usług. |

Aplikacja usługi LUIS przeprowadza analizy, dzięki którym aplikacja kliencka może dokonać inteligentnego wyboru. Sama usługa LUIS nie dokonuje tych wyborów.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Przetwarzanie języka naturalnego

Aplikacja usługi LUIS zawiera model języka naturalnego specyficzny dla domeny. Możesz uruchomić aplikację LUIS ze wstępnie utworzonym modelem domeny, utworzyć własny model lub połączyć elementy wstępnie utworzonej domeny z własnymi informacjami.

* **Model wstępnie utworzony** usługi LUIS zawiera wiele wstępnie utworzonych modeli domeny, w tym intencje, wypowiedzi i wstępnie utworzone jednostki. Możesz skorzystać z wstępnie utworzonych jednostek bez konieczności korzystania z intencji i wypowiedzi wstępnie utworzonego modelu. [Wstępnie utworzone modele domeny](luis-how-to-use-prebuilt-domains.md) zawierają kompletny projekt i są świetnym sposobem, aby szybko rozpocząć korzystanie z usługi LUIS.

* **Model niestandardowy** Usługa LUIS udostępnia kilka sposobów identyfikowania własnych modeli niestandardowych, w tym intencji i jednostek. Jednostki obejmują jednostki nauczone maszynowo, określone lub dosłowne jednostki oraz kombinację maszynowo nauczone i literału.

## <a name="build-the-luis-model"></a>Tworzenie modelu w usłudze LUIS
Tworzenie modelu za pomocą [interfejsów](https://go.microsoft.com/fwlink/?linkid=2092087) API tworzenia lub za pomocą [portalu usługi LUIS](https://www.luis.ai).

Tworzenie modelu w usłudze LUIS rozpoczyna się od kategorii intencji użytkownika o nazwie **[Intencje](luis-concept-intent.md)**. Każda intencja musi zawierać przykłady **[wypowiedzi](luis-concept-utterance.md)** użytkownika. Każdy wypowiedź może dostarczyć danych, które muszą zostać wyodrębnione.

|Przykładowa wypowiedź użytkownika|Intencja|Wyodrębnione dane|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation|open|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|1pm, Bob|

## <a name="query-prediction-endpoint"></a>Punkt końcowy przewidywania zapytania

Po aplikacji jest przeszkolony i opublikowane w punkcie końcowym, aplikacja kliencka wysyła wypowiedzi do interfejsu API [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) przewidywania. Interfejs API stosuje aplikację do wypowiedź do analizy i odpowiada z wyników przewidywania w formacie JSON.

Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Można również wyodrębnić dane, takie jak następująca jednostka **typ kontaktu** i ogólna tonację.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Doskonalenie przewidywania z użyciem modelu

Po opublikowaniu aplikacji usługi LUIS i odbiera wypowiedzi rzeczywistych użytkowników, usługa LUIS zapewnia [aktywne uczenie się](luis-concept-review-endpoint-utterances.md) wypowiedzi punktu końcowego w celu zwiększenia dokładności przewidywania.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Cykl projektowania oprogramowania
Usługa LUIS udostępnia narzędzia, przechowywanie wersji i współpracę z innymi autorami usługi LUIS w celu zintegrowania się z pełnym [cyklem życia rozwoju.](luis-concept-app-iteration.md)

## <a name="implementing-luis"></a>Wdrażanie usługi LUIS
Rozumienie języka (LUIS), jako interfejs API REST, może być używane z dowolnego produktu, usługi lub struktury z żądaniem HTTP. Poniższa lista zawiera główne produkty i usługi firmy Microsoft, których można używać z usługą LUIS.

Najważniejszą aplikacją kliencką dla usługi LUIS jest:
* [Bot usługi Web Apps](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) szybko tworzy czatbota obsługującego usługę LUIS, który rozmawia z użytkownikiem za pośrednictwem tekstowych danych wejściowych. Używa [bot framework][bot-framework] w wersji [4.x](https://github.com/Microsoft/botbuilder-dotnet) dla pełnego doświadczenia bota.

Narzędzia do szybkiego i łatwego korzystania z usługi LUIS z botem:
* [Wiersz polecenia interfejsu wiersza polecenia usługi LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Pakiet NPM zapewnia tworzenie i przewidywanie jako autonomiczne narzędzie wiersza polecenia lub jako importowanie.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) — to narzędzie do generowania silnie typizowanego języka C# i kodu źródłowego języka Typescript z wyeksportowanego modelu usługi LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) — umożliwia używanie kilku aplikacji LUIS lub QnA Maker z poziomu aplikacji nadrzędnej za pomocą modelu dyspozytora.
* [LuDown ( LUDown )](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown to narzędzie wiersza polecenia, które pomaga zarządzać modelami językowymi dla bota.
* [Struktura botów — Kompozytor](https://github.com/microsoft/BotFramework-Composer) — zintegrowane narzędzie programistyczne dla programistów i zespołów multidyscyplinarnych do tworzenia botów i konwersacyjnych doświadczeń z platformą Microsoft Bot Framework

Inne usługi Cognitive Services używane z usługą LUIS:
* [QnA Maker][qnamaker] umożliwia łączenie kilku rodzajów tekstu w bazę wiedzy w formie pytań i odpowiedzi.
* [Usługa rozpoznawania mowy](../Speech-Service/overview.md) konwertuje żądania w języku mówionym na tekst.
* [Uczeń konwersacji](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umożliwia stworzenie rozmów z botem szybciej niż w usłudze LUIS.

Przykłady korzystające z usługi LUIS:
* Repozytorium GitHub [Conversational AI](https://github.com/Microsoft/AI)
* [Struktura botów — przykłady botów](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Następne kroki

* [Co nowego](whats-new.md)
* Utwórz nową aplikację usługi LUIS za pomocą domeny [wstępnie utworzonej](luis-get-started-create-app.md) lub [niestandardowej](luis-quickstart-intents-only.md).
* [Wyślij zapytanie do punktu końcowego przewidywania](luis-get-started-get-intent-from-browser.md) publicznej aplikacji IoT.
* [Zasoby deweloperów](developer-reference-resource.md) dla usługi LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
