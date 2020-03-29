---
title: Pojęcia dotyczące przedsiębiorstwa — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Zapoznaj się z pojęciami projektowymi dla dużych aplikacji usługi LUIS lub wielu aplikacji, w tym usługi LUIS i QnA Maker.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221063"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Strategie dla przedsiębiorstw dla aplikacji usługi LUIS
Przejrzyj te strategie projektowania aplikacji dla przedsiębiorstwa.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Gdy oczekujesz żądań usługi LUIS poza przydziałem

Usługa LUIS ma przydział miesięczny, a także przydział na sekundę, na podstawie warstwy cenowej zasobu platformy Azure. 

Jeśli szybkość żądania aplikacji usługi LUIS przekracza dozwoloną [stawkę przydziału,](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)można:

* Rozłóż obciążenie na więcej aplikacji usługi LUIS z [tą samą definicją aplikacji](#use-multiple-apps-with-same-app-definition). Obejmuje to opcjonalnie uruchamianie usługi LUIS z [kontenera.](luis-container-howto.md) 
* Utwórz i [przypisz wiele kluczy](#assign-multiple-luis-keys-to-same-app) do aplikacji. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Używanie wielu aplikacji z tą samą definicją aplikacji
Wyeksportuj oryginalną aplikację usługi LUIS, a następnie zaimportuj aplikację z powrotem do oddzielnych aplikacji. Każda aplikacja ma swój własny identyfikator aplikacji. Podczas publikowania zamiast używać tego samego klucza we wszystkich aplikacjach, utwórz osobny klucz dla każdej aplikacji. Zrównoważyć obciążenie we wszystkich aplikacjach, tak aby żadna pojedyncza aplikacja nie jest przytłoczona. Dodaj [usługa Application Insights,](luis-tutorial-bot-csharp-appinsights.md) aby monitorować użycie. 

Aby uzyskać ten sam top intencji między wszystkimi aplikacjami, upewnij się, że przewidywanie intencji między pierwszym i drugim zamiarem jest wystarczająco szeroki, że usługa LUIS nie jest mylona, co daje różne wyniki między aplikacjami dla drobnych zmian w wypowiedziach. 

Podczas szkolenia tych aplikacji rodzeństwa, upewnij się, że [trenuje ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

Wyznacz pojedynczą aplikację jako wzorca. Wszelkie wypowiedzi, które są sugerowane do przeglądu powinny zostać dodane do aplikacji wzorcowej, a następnie przeniesione z powrotem do wszystkich innych aplikacji. Jest to pełny eksport aplikacji lub ładowanie wypowiedzi z etykietą z wzorca do podstawowych. Ładowanie można wykonać z witryny [sieci Web usługi LUIS](luis-reference-regions.md) lub interfejsu API tworzenia dla [pojedynczej wypowiedzi](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) lub dla [partii.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) 

Zaplanuj okresowy przegląd, na przykład co dwa tygodnie, [wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md) dla aktywnego uczenia się, a następnie ponownie skolonować i ponownie opublikować. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Przypisywanie wielu kluczy usługi LUIS do tej samej aplikacji
Jeśli aplikacja usługi LUIS otrzymuje więcej trafień punktu końcowego niż przydział pojedynczego klucza pozwala, należy utworzyć i przypisać więcej kluczy do aplikacji usługi LUIS. Utwórz menedżera ruchu lub moduł równoważenia obciążenia, aby zarządzać zapytaniami punktów końcowych w kluczach punktów końcowych. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Gdy monolityczna aplikacja zwraca błędne intencje
Jeśli aplikacja jest przeznaczona do przewidywania szerokiej gamy wypowiedzi użytkownika, należy rozważyć zaimplementowanie [modelu wysyłki.](#dispatch-tool-and-model) Podział monolitycznych aplikacji umożliwia usługi LUIS fokus wykrywania między intencjami pomyślnie zamiast coraz mylić intencji między intencjami w aplikacji nadrzędnej i aplikacji podrzędnych. 

Zaplanuj okresowy [przegląd wypowiedzi punktów końcowych](luis-how-to-review-endpoint-utterances.md) dla aktywnego uczenia się, na przykład co dwa tygodnie, a następnie ponownie skolonować i ponownie opublikować. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Kiedy musisz mieć więcej niż 500 intencji
Załóżmy, że pracujesz nad asystentem biurowym, który ma ponad 500 intencji. Jeśli 200 intencji odnoszą się do planowania spotkań, 200 dotyczy przypomnień, 200 dotyczy uzyskiwania informacji o współpracownikach, a 200 dotyczy wysyłania wiadomości e-mail, intencji grupy, aby każda grupa była w jednej aplikacji, a następnie utworzyć aplikację najwyższego poziomu zawierającą każdy zamiar. Użyj [modelu wysyłki](#dispatch-tool-and-model) do tworzenia aplikacji najwyższego poziomu. Następnie zmień bota, aby użyć połączenia kaskadowego, jak pokazano w [samouczku modelu wysyłki](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Gdy musisz połączyć kilka aplikacji usługi LUIS i QnA maker
Jeśli masz kilka aplikacji programu LUIS i QnA maker, które muszą odpowiedzieć na bota, użyj [modelu wysyłki](#dispatch-tool-and-model) do tworzenia aplikacji najwyższego poziomu.  Następnie zmień bota, aby użyć połączenia kaskadowego, jak pokazano w [samouczku modelu wysyłki](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Narzędzie i model wysyłki
Użyj narzędzia wiersza polecenia [Dispatch,][dispatch-tool] znajdującego się w [narzędziach BotBuilder,](https://github.com/Microsoft/botbuilder-tools) aby połączyć wiele aplikacji usługi LUIS i/lub QnA Maker w nadrzędną aplikację usługi LUIS. Takie podejście umożliwia domenę nadrzędną obejmującą wszystkie tematy i różne domeny tematów podrzędnych w oddzielnych aplikacjach. 

![Obraz koncepcyjny architektury wysyłki](./media/luis-concept-enterprise/dispatch-architecture.png)

Domena nadrzędna jest zanotowana `Dispatch` w usłudze LUIS z wersją nazwaną na liście aplikacji. 

Bot czatu odbiera wypowiedź, a następnie wysyła do nadrzędnej aplikacji usługi LUIS do przewidywania. Górna przewidywana intencja z aplikacji nadrzędnej określa, która aplikacja podrzędna usługi LUIS jest wywoływana dalej. Bot czatu wysyła wypowiedź do aplikacji podrzędnej dla bardziej szczegółowych prognozowania.

Dowiedz się, jak ta hierarchia połączeń jest nawiązywana z botbuildera v4 [dyspozytora-aplikacji-samouczka](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Limity intencji w modelu wysyłki
Aplikacja wysyłki ma 500 źródeł wysyłki, co odpowiada 500 intencji, jako maksimum. 

## <a name="more-information"></a>Więcej informacji

* [SDK w ramach bota](https://github.com/Microsoft/botframework)
* [Samouczek modelu wysyłki](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Wysyłka interfejsu wiersza polecenia](https://github.com/Microsoft/botbuilder-tools)
* Przykład bota modelu wysyłki — [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przetestować partię](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
