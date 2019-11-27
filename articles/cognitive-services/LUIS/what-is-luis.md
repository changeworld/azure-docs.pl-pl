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
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456566"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest usługa Language Understanding (LUIS)?

Language Understanding (LUIS) to oparta na chmurze usługa interfejsu API, która umożliwia stosowanie niestandardowych analiz uczenia maszynowego do tekstu w języku naturalnym w celu przewidywania ogólnego znaczenia i ściągania odpowiednich szczegółowych informacji. 

Na przykład, gdy aplikacja kliencka wysyła tekst, `find me a wireless keyboard for $30`LUIS reaguje na następujący obiekt JSON. 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
W powyższym przykładzie _**zamierzenie**_ lub ogólne znaczenie frazy polega na tym, że użytkownik próbuje znaleźć element. Szczegółowe fragmenty informacji, które LUIS wyodrębnia są nazywane _**jednostkami**_ . W takim przypadku jednostki są nazwą elementu, którego szuka użytkownik, i ilością pieniędzy, które chcą wydać.

Aplikacje klienckie używają LUIS zwracanego kodu JSON, _zamiaru_ (kategorii) i _jednostek_ (wyodrębnione informacje szczegółowe) do wykonywania akcji w aplikacji klienckiej. Aplikacja kliencka dla LUIS jest często aplikacją do konwersacji, która komunikuje się z użytkownikiem w języku naturalnym w celu ukończenia zadania. Przykładami aplikacji klienckich są aplikacje mediów społecznościowych, czatboty i aplikacje klasyczne z funkcją rozpoznawania mowy. 

![Obraz przedstawiający koncepcję 3 aplikacji klienckich pracujących z Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Obraz przedstawiający koncepcję 3 aplikacji klienckich pracujących z Cognitive Services Language Understanding (LUIS)")

## <a name="example-use-luis-in-a-chat-bot"></a>Przykład użycia LUIS w rozmowie bot

<a name="Accessing-LUIS"></a>

Aplikacja kliencka wysyła wyrażenia długości (tekst) do opublikowanego [interfejsu API][endpoint-apis] programu Endpoint Processing języka naturalnego Luis i odbiera wyniki jako odpowiedzi JSON. Typową aplikacją kliencką dla usługi LUIS jest czatbot.


![Obraz przedstawiający koncepcje LUIS pracy z usługą Chat bot w celu przewidywania tekstu użytkownika za pomocą interpretacji języka naturalnego (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Obraz przedstawiający koncepcje LUIS pracy z usługą Chat bot w celu przewidywania tekstu użytkownika przy użyciu funkcji interpretacji języka naturalnego (NLP")

|Krok|Akcja|
|:--|:--|
|1|Aplikacja kliencka wysyła _wypowiedź_ użytkownika (tekst wypowiadany własnymi słowami): „I want to call my HR rep” („Chcę zadzwonić do mojego przedstawiciela działu kadr”) do punktu końcowego usługi LUIS jako żądanie HTTP.|
|2|LUIS stosuje modele języka z obsługą maszyn, do tekstu wejściowego bez struktury użytkownika i zwraca odpowiedź sformatowaną w formacie JSON z zamiarem najwyższego poziomu `HRContact`. Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również wyodrębnić dane, takie jak jednostka _typu kontaktu_ .|
|3|Aplikacja kliencka używa odpowiedzi w formacie JSON do podejmowania decyzji dotyczących sposobu realizacji żądania użytkownika. Decyzje te mogą obejmować drzewo decyzyjne w bot i wywołania do innych usług. |

Aplikacja usługi LUIS przeprowadza analizy, dzięki którym aplikacja kliencka może dokonać inteligentnego wyboru. Sama usługa LUIS nie dokonuje tych wyborów. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Przetwarzanie języka naturalnego

Twoja aplikacja LUIS zawiera modele języka naturalnego specyficzne dla domeny, które współpracują ze sobą. Możesz uruchomić aplikację LUIS z jednym lub wieloma wstępnie skompilowanymi modelami, utworzyć własny model lub zmieszać wbudowane modele ze swoimi niestandardowymi informacjami.

* **Prekompilowany model** LUIS ma wiele wbudowanych domen, które obejmują modele założeń i jednostek, które współpracują ze sobą w celu wykonania typowych scenariuszy użycia. Domeny te zawierają etykiety wyrażenia długości, które mogą być kontrolowane i edytowane, co pozwala na ich dostosowanie. [Wstępnie utworzone modele domeny](luis-how-to-use-prebuilt-domains.md) zawierają kompletny projekt i są świetnym sposobem, aby szybko rozpocząć korzystanie z usługi LUIS. Ponadto istnieją wstępnie skompilowane jednostki, takie jak waluta i numer, których można użyć niezależnie od wstępnie skompilowanych domen.

* **Model niestandardowy** LUIS oferuje kilka sposobów tworzenia własnych modeli niestandardowych, w tym intencje i jednostki. Jednostki obejmują jednostki, które są związane z maszyną, jednostki zgodne ze wzorcem i kombinację dopasowywania maszyn i wzorców.

## <a name="build-the-luis-app"></a>Kompilowanie aplikacji LUIS
Utwórz aplikację za pomocą interfejsów API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) lub [portalu Luis](https://www.luis.ai).

Aplikacja LUIS rozpoczyna się od kategorii tekstu wejściowego o nazwie **[intencje](luis-concept-intent.md)** . Każda intencja musi zawierać przykłady **[wypowiedzi](luis-concept-utterance.md)** użytkownika. Każdy wypowiedź może dostarczyć dane, które muszą zostać wyodrębnione. 

|Przykładowa wypowiedź użytkownika|Intencja|Wyodrębnione dane|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation|open|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|1pm, Bob|

## <a name="query-prediction-endpoint"></a>Punkt końcowy przewidywania zapytania

Gdy aplikacja zostanie przeszkolone i opublikowana w punkcie końcowym, aplikacja kliencka wysyła wyrażenia długości do [interfejsu API](https://go.microsoft.com/fwlink/?linkid=2092356) usługi przewidywania. Interfejs API stosuje aplikację do wypowiedź na potrzeby analizy i reaguje na wyniki prognozowania w formacie JSON.  

Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również wyodrębnić dane, takie jak następująca jednostka **typu kontaktu** i ogólna tonacji. 

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

Po opublikowaniu aplikacji LUIS i otrzymaniu rzeczywistej wyrażenia długości użytkownika, LUIS zapewnia [aktywną naukę](luis-concept-review-endpoint-utterances.md) punktu końcowego wyrażenia długości, aby poprawić dokładność przewidywania. 

<a name="using-luis"></a>

## <a name="iterative-development-lifecycle"></a>Cykl życia programowania iteracyjnego
LUIS oferuje narzędzia, przechowywanie wersji i współpracę z innymi autorami LUIS w celu zintegrowania z pełnym [cyklem życia](luis-concept-app-iteration.md). 

## <a name="implementing-luis"></a>Wdrażanie usługi LUIS
Language Understanding (LUIS) jako interfejs API REST może być używany z dowolnym produktem, usługą lub platformą z żądaniem HTTP. Poniższa lista zawiera główne produkty i usługi firmy Microsoft, których można używać z usługą LUIS.

Najważniejszą aplikacją kliencką dla usługi LUIS jest:
* [Bot usługi Web Apps](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) szybko tworzy czatbota obsługującego usługę LUIS, który rozmawia z użytkownikiem za pośrednictwem tekstowych danych wejściowych. Używa programu [bot Framework][bot-framework] w wersji [4. x](https://github.com/Microsoft/botbuilder-dotnet) , aby uzyskać pełny bot środowisko.

Narzędzia do szybkiego i łatwego korzystania z usługi LUIS z botem:
* [interfejs wiersza polecenia Luis](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Pakiet NPM zapewnia tworzenie i prognozowanie za pomocą autonomicznego narzędzia wiersza polecenia lub jako importu. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) — to narzędzie do generowania silnie typizowanego języka C# i kodu źródłowego języka Typescript z wyeksportowanego modelu usługi LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) — umożliwia używanie kilku aplikacji LUIS lub QnA Maker z poziomu aplikacji nadrzędnej za pomocą modelu dyspozytora.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) — to narzędzie wiersza polecenia, które pomaga zarządzać modelami językowymi bota.
* [Bot Framework — układacz](https://github.com/microsoft/BotFramework-Composer) — zintegrowane narzędzie programistyczne dla deweloperów i zespołów z wielodyscyplinarnymi kompilacjami botów i konwersacjami w środowisku Microsoft bot Framework

Inne usługi Cognitive Services używane z usługą LUIS:
* [QNA Maker][qnamaker] umożliwia łączenie kilku typów tekstu z bazą wiedzy pytań i odpowiedzi.
* [Usługa rozpoznawania mowy](../Speech-Service/overview.md) konwertuje żądania w języku mówionym na tekst. 
* [Uczeń konwersacji](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umożliwia stworzenie rozmów z botem szybciej niż w usłudze LUIS.

Przykłady korzystające z usługi LUIS:
* Repozytorium GitHub [Conversational AI](https://github.com/Microsoft/AI)
* [Bot Framework — przykłady bot](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Następne kroki

* [Co nowego](whats-new.md)
* Tworzenie nowej aplikacji LUIS przy użyciu [wstępnie skompilowanej](luis-get-started-create-app.md) lub [niestandardowej](luis-quickstart-intents-only.md) domeny
* [Wyślij zapytanie do punktu końcowego przewidywania](luis-get-started-get-intent-from-browser.md) publicznej aplikacji IoT. 
* [Zasoby dla deweloperów](developer-reference-resource.md) dla Luis. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
