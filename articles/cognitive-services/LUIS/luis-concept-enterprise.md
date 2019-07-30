---
title: Koncepcje przedsiębiorstwa — LUIS
titleSuffix: Azure Cognitive Services
description: Omówienie pojęć dotyczących projektowania dla dużych aplikacji LUIS lub wiele aplikacji, w tym usługi LUIS i usługi QnA Maker ze sobą.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619942"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Strategie Enterprise aplikacją usługi LUIS
Przejrzyj te Strategie projektowania dla aplikacji przedsiębiorstwa.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Jeśli oczekujesz, że usługi LUIS żądania po przekroczeniu limitu przydziału

LUIS ma miesięczny limit przydziału, a także przydział na sekundę na podstawie warstwy cenowej zasobu platformy Azure. 

Jeśli częstotliwość żądań aplikacji LUIS przekracza dozwoloną [liczbę limitów przydziału](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), możesz:

* Rozłożenie obciążenia na więcej aplikacji LUIS z [tą samą definicją aplikacji](#use-multiple-apps-with-same-app-definition). Obejmuje to, opcjonalnie, uruchamianie LUIS z [kontenera](luis-container-howto.md). 
* Utwórz i [Przypisz wiele kluczy](#assign-multiple-luis-keys-to-same-app) do aplikacji. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Wiele aplikacji za pomocą tej samej definicji aplikacji
Eksportuj oryginalnej aplikacji LUIS, a następnie importowania aplikacji do osobnych aplikacji. Każda aplikacja ma swój własny identyfikator aplikacji. Podczas publikowania, zamiast korzystać z tego samego klucza dla wszystkich aplikacji, należy utworzyć oddzielny klucz dla każdej aplikacji. Równoważenie obciążenia wśród wszystkich aplikacji, tak aby nie pojedynczej aplikacji jest przeciążony. Dodaj [usługi Application Insights](luis-tutorial-bot-csharp-appinsights.md) monitorowania użycia. 

Aby uzyskać ten sam intencji najważniejsze między wszystkie aplikacje, upewnij się, że funkcja prognozowania między pierwszym i drugim celem jest dostatecznie szeroka, że usługa LUIS nie jest mylące, co daje różne wyniki aplikacji dla niewielkich zmian w wypowiedzi. 

W przypadku szkolenia tych elementów równorzędnych upewnij się, że nauczysz się [ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

Należy określić pojedynczej aplikacji jako wzorzec. Wypowiedzi, które są zalecane dla przeglądu powinien dodane do aplikacji głównej, następnie przeniesiony z powrotem do innych aplikacji. To jest pełny eksportowania aplikacji, lub ładowania etykietami wypowiedzi ze wzorca do elementów podrzędnych. Ładowanie może odbywać się za pomocą albo [LUIS](luis-reference-regions.md) witryny sieci Web lub tworzenia interfejsu API dla [pojedynczy wypowiedź](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) lub [partii](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Zaplanuj okresowe przeglądy, na przykład co dwa tygodnie, [punktu końcowego wyrażenia długości](luis-how-to-review-endpoint-utterances.md) na potrzeby aktywnego uczenia, a następnie ponownie nauczenie i ponowne opublikowanie. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Przypisywanie klawiszy wielu usługi LUIS do tej samej aplikacji
Jeśli aplikacją usługi LUIS odbiera więcej punktu końcowego trafień, niż zezwala limit przydziału jednego klucza, utworzyć i przypisać więcej kluczy do aplikacji usługi LUIS. Tworzenie Menedżera ruchu lub zarządzać zapytaniami punktu końcowego różnych kluczy punktu końcowego usługi równoważenia obciążenia. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Gdy aplikacji monolitycznych zwraca niewłaściwy intencji
Jeśli aplikacja jest przeznaczona do przewidywania szerokiej gamy wypowiedzi użytkownika, rozważ zaimplementowanie [modelu wysyłania](#dispatch-tool-and-model). Podzielenie monolityczną aplikację umożliwia LUIS wykrywania fokus między opcjami pomyślnie zamiast pobieranie mylić między opcjami w aplikacji nadrzędnej i podrzędnej aplikacji. 

Zaplanować okresowe [przeglądu wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md) dla aktywne uczenie się, co dwa tygodnie, np. następnie ponownie ucz i ponownie opublikuj. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Jeśli musisz mieć więcej niż 500 intencji
Załóżmy, że opracowujesz Asystenta pakietu Office, który ma ponad 500 intencji. Jeśli 200 intencji odnoszą się do planowania spotkań, 200 nastąpi przypomnienia 200 nastąpi uzyskiwanie informacji dotyczących współpracowników i 200 służą do wysyłania wiadomości e-mail, intencji grupy tak, aby każda grupa znajduje się w jednej aplikacji, następnie utwórz aplikację najwyższego poziomu zawierającą każdy intencji. Użyj [modelu wysyłania](#dispatch-tool-and-model) , aby skompilować aplikację najwyższego poziomu. Następnie zmień bot tak, aby korzystał z wywołania kaskadowego, jak pokazano w [samouczku modelu wysyłania](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Kiedy trzeba połączyć aplikacje maker kilka LUIS i pytań i odpowiedzi
Jeśli masz kilka aplikacji LUIS i QnA Maker, które muszą odpowiedzieć na bot, użyj [modelu wysyłania](#dispatch-tool-and-model) , aby skompilować aplikację najwyższego poziomu.  Następnie zmień bot tak, aby korzystał z wywołania kaskadowego, jak pokazano w [samouczku modelu wysyłania](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Narzędzia wysyłki i model
Za pomocą narzędzia wiersza polecenia [wysyłania][dispatch-tool] dostępnego w [BotBuilder-Tools](https://github.com/Microsoft/botbuilder-tools) można łączyć wiele aplikacji LUIS i/lub QNA Maker w aplikacji nadrzędnej Luis. Takie podejście umożliwia domeny nadrzędnej, w tym wszystkich tematów i różnych domen podmiotu w osobnych aplikacji. 

![Obrazu koncepcyjnego architektury wysyłania](./media/luis-concept-enterprise/dispatch-architecture.png)

Domena nadrzędna została przedstawiona w LUIS w wersji o nazwie `Dispatch` na liście aplikacji. 

Usługa Chat bot odbiera wypowiedź, a następnie wysyła do aplikacji nadrzędnej LUIS w celu przewidywania. Najczęściej przewidywane zamierzenie z aplikacji nadrzędnej Określa, która aplikacja podrzędna LUIS jest wywoływana dalej. Bot rozmowy wysyła wypowiedź do aplikacji podrzędnej w celu uzyskania bardziej szczegółowego przewidywania.

Dowiedz się, w jaki sposób ta hierarchia wywołań jest wykonywana z poziomu programu Dyspozytor Bot Builder v4 [— samouczek aplikacji](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Limity intencji w modelu wysyłania
Aplikacja wysyłania ma 500 źródeł wysyłania równoważne do 500 intencji jako wartość maksymalna. 

## <a name="more-information"></a>Więcej informacji

* [Bot Framework SDK](https://github.com/Microsoft/botframework)
* [Samouczek dotyczący modelu wysyłania](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Interfejs wiersza polecenia wysyłania](https://github.com/Microsoft/botbuilder-tools)
* Przykład bot model wysyłania — [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [test partii](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
