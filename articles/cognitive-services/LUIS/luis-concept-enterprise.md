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
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 0d51778473dc033bce3c58b1572f1e514a8b6327
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560777"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Strategie Enterprise aplikacją usługi LUIS
Przejrzyj te Strategie projektowania dla aplikacji przedsiębiorstwa.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Jeśli oczekujesz, że usługi LUIS żądania po przekroczeniu limitu przydziału
Jeśli tempo wysyłania żądań aplikacji LUIS przekracza dozwoloną [współczynnik przydziału](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), rozłożenie obciążenia na więcej aplikacji usługi LUIS z [tej samej definicji aplikacji](#use-multiple-apps-with-same-app-definition) lub utworzyć i [przypisać wiele kluczy](#assign-multiple-luis-keys-to-same-app) do aplikacja. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Wiele aplikacji za pomocą tej samej definicji aplikacji
Eksportuj oryginalnej aplikacji LUIS, a następnie importowania aplikacji do osobnych aplikacji. Każda aplikacja ma swój własny identyfikator aplikacji. Podczas publikowania, zamiast korzystać z tego samego klucza dla wszystkich aplikacji, należy utworzyć oddzielny klucz dla każdej aplikacji. Równoważenie obciążenia wśród wszystkich aplikacji, tak aby nie pojedynczej aplikacji jest przeciążony. Dodaj [usługi Application Insights](luis-tutorial-bot-csharp-appinsights.md) monitorowania użycia. 

Aby uzyskać ten sam intencji najważniejsze między wszystkie aplikacje, upewnij się, że funkcja prognozowania między pierwszym i drugim celem jest dostatecznie szeroka, że usługa LUIS nie jest mylące, co daje różne wyniki aplikacji dla niewielkich zmian w wypowiedzi. 

Należy określić pojedynczej aplikacji jako wzorzec. Wypowiedzi, które są zalecane dla przeglądu powinien dodane do aplikacji głównej, następnie przeniesiony z powrotem do innych aplikacji. To jest pełny eksportowania aplikacji, lub ładowania etykietami wypowiedzi ze wzorca do elementów podrzędnych. Ładowanie może odbywać się za pomocą albo [LUIS](luis-reference-regions.md) witryny sieci Web lub tworzenia interfejsu API dla [pojedynczy wypowiedź](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) lub [partii](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Zaplanować okresowe [przeglądu wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md) dla aktywne uczenie się, co dwa tygodnie, np. następnie ponownie ucz i ponownie opublikuj. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Przypisywanie klawiszy wielu usługi LUIS do tej samej aplikacji
Jeśli aplikacją usługi LUIS odbiera więcej punktu końcowego trafień, niż zezwala limit przydziału jednego klucza, utworzyć i przypisać więcej kluczy do aplikacji usługi LUIS. Tworzenie Menedżera ruchu lub zarządzać zapytaniami punktu końcowego różnych kluczy punktu końcowego usługi równoważenia obciążenia. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Gdy aplikacji monolitycznych zwraca niewłaściwy intencji
Jeśli aplikacja jest przeznaczona do przewidywania szerokiej gamy wypowiedzi użytkownika, rozważ zaimplementowanie [modelu wysyłania](#dispatch-tool-and-model). Podzielenie monolityczną aplikację umożliwia LUIS wykrywania fokus między opcjami pomyślnie zamiast pobieranie mylić między opcjami w aplikacji nadrzędnej i podrzędnej aplikacji. 

Zaplanować okresowe [przeglądu wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md) dla aktywne uczenie się, co dwa tygodnie, np. następnie ponownie ucz i ponownie opublikuj. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Jeśli musisz mieć więcej niż 500 intencji
Załóżmy na przykład, że opracowujemy Asystent pakietu office, który ma ponad 500 intencji. Jeśli 200 intencji odnoszą się do planowania spotkań, 200 nastąpi przypomnienia 200 nastąpi uzyskiwanie informacji dotyczących współpracowników i 200 służą do wysyłania wiadomości e-mail, intencji grupy tak, aby każda grupa znajduje się w jednej aplikacji, następnie utwórz aplikację najwyższego poziomu zawierającą każdy intencji. Użyj [wysyłania narzędzia i architektury](#dispatch-tool-and-model) do tworzenia aplikacji najwyższego poziomu. Następnie zmień bot tak, aby korzystał z wywołania kaskadowego, jak pokazano w [samouczku wysyłania][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Kiedy trzeba połączyć aplikacje maker kilka LUIS i pytań i odpowiedzi
Jeśli masz kilka LUIS i pytań i odpowiedzi aplikacji producenta, które musisz odpowiedzieć na bot, użyj [narzędzia wysyłki](#dispatch-tool-and-model) do tworzenia aplikacji najwyższego poziomu. Następnie zmień bot tak, aby korzystał z wywołania kaskadowego, jak pokazano w [samouczku wysyłania][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Narzędzia wysyłki i model
Za pomocą narzędzia wiersza polecenia [wysyłania][dispatch-tool] dostępnego w [BotBuilder-Tools](https://github.com/Microsoft/botbuilder-tools) można łączyć wiele aplikacji LUIS i/lub QNA Maker w aplikacji nadrzędnej Luis. Takie podejście umożliwia domeny nadrzędnej, w tym wszystkich tematów i różnych domen podmiotu w osobnych aplikacji. 

![Obrazu koncepcyjnego architektury wysyłania](./media/luis-concept-enterprise/dispatch-architecture.png)

Domena nadrzędna została przedstawiona w LUIS w wersji o nazwie `Dispatch` na liście aplikacji. 

Czatbot zwiększający odbiera wypowiedź, a następnie wysyła do nadrzędnego aplikacją usługi LUIS w celu prognozowania. Najważniejsze intencji przewidywane z aplikacji nadrzędnej określa podrzędne, które aplikacją usługi LUIS nazywa się obok. Czatbot zwiększający wysyła wypowiedź do aplikacji podrzędnej, aby dokładniejszych prognoz.

Dowiedz się, w jaki sposób ta hierarchia wywołań jest wykonywana z poziomu programu Dyspozytor Bot Builder v4 [— samouczek aplikacji][dispatcher-application-tutorial].  

### <a name="intent-limits-in-dispatch-model"></a>Limity intencji w modelu wysyłania
Aplikacja wysyłania ma 500 źródeł wysyłania równoważne do 500 intencji jako wartość maksymalna. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [test partii](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
