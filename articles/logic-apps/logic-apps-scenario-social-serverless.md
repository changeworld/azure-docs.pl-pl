---
title: Scenariusz bez użycia serwera — Utwórz pulpit nawigacyjny szczegółowych informacji klientów z usługami platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie opinie klientów, danych społecznościowych i nie tylko przez utworzenie pulpitu nawigacyjnego klienta za pomocą usługi Azure Logic Apps i Azure Functions
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 5dd9d8eac7d65815d7c98a28b0d5af55f19cec47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65464431"
---
# <a name="create-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Utwórz pulpit nawigacyjny szczegółowych informacji z klientów przesyłania strumieniowego za pomocą usługi Azure Logic Apps i Azure Functions

Platforma Azure oferuje [bezserwerowe](https://azure.microsoft.com/solutions/serverless/) narzędzia, które ułatwiają szybkie tworzenie i hostowanie aplikacji w chmurze, bez myśleć o infrastrukturę. W ramach tego samouczka możesz utworzyć pulpit nawigacyjny, który wyzwala na opinie klientów, analizuje informacji zwrotnych za pomocą usługi machine learning i publikuje informacje do źródła, takich jak usługa Power BI lub Azure Data Lake.

W przypadku tego rozwiązania można użyć tych kluczowych składników platformy Azure aplikacje niewymagające użycia serwera: [Usługa Azure Functions](https://azure.microsoft.com/services/functions/) i [usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Usługa Azure Logic Apps udostępnia aparatu przepływu pracy bez użycia serwera w chmurze, dzięki czemu można aranżacje są składnikami bez użycia serwera i nawiązać połączenie z ponad 200 usług i interfejsów API. Usługa Azure Functions zapewnia bezserwerowych obliczeń w chmurze. To rozwiązanie używa usługi Azure Functions dla Oflagowanie klienta tweety na podstawie wstępnie zdefiniowanych słów kluczowych.

W tym scenariuszu utworzysz aplikację logiki, która wyzwala w znajdowaniu informacji zwrotnych od klientów. Niektóre łączniki, że pomoc odpowiadania na opinie klientów obejmują usługi Outlook.com, Office 365, małp ankiety, Twitter, a [żądania HTTP za pomocą formularza sieci web](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Przepływ pracy, którą tworzysz monitoruje hasztag w serwisie Twitter.

Możesz [skompilować całe rozwiązanie w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) i [wdrożyć rozwiązanie za pomocą szablonu usługi Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md). Przewodnik wideo pokazuje, jak utworzyć to rozwiązanie [Obejrzyj ten film wideo Channel 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Wyzwalanie na dane klienta

1. W witrynie Azure portal lub programu Visual Studio należy utworzyć pustej aplikacji logiki. 

   Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [Przewodnik Szybki start dotyczący witryny Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) lub [Szybki Start dla programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. W Projektancie aplikacji logiki Znajdź, a następnie dodaj wyzwalacz usługi Twitter, który ma tę akcję: **Po wysłaniu nowego tweetu**

3. Skonfiguruj wyzwalacz, aby nasłuchiwać tweety na podstawie słów kluczowych lub hasztagiem.

   Na wyzwalacze oparte na sondowanie, na przykład z wyzwalaczem usługi Twitter właściwości cyklu określa, jak często aplikacja logiki sprawdza istnienie nowych elementów.

   ![Przykład wyzwalacza usługi Twitter][1]

Ta aplikacja logiki teraz jest uruchamiana na wszystkich nowych tweetów. Można wykonać i analizowanie danych tweetu, tak, aby można lepiej zrozumieć tonacji wyrażone. 

## <a name="analyze-tweet-text"></a>Analizuj tekst tweetu

W celu wykrywania tonacji za jakiś tekst, można użyć [usług Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. W Projektancie aplikacji logiki, w obszarze wyzwalacza wybierz **nowy krok**.

2. Znajdź **analizy tekstu** łącznika.

3. Wybierz **wykrywania tonacji** akcji.

4. Po wyświetleniu monitu podaj prawidłowy klucz usługi Cognitive Services, usługi analizy tekstu.

5. W obszarze **treść żądania**, wybierz opcję **tekst Tweetu** pola, które zawiera tekst tweetu jako dane wejściowe do analizy.

Po uzyskaniu tweet danych i szczegółowych informacji dotyczących tweetu teraz użyć kilku istotnych łączniki i ich działania:

* **Power BI — Dodawanie wierszy do zestawu danych przesyłania strumieniowego**: Wyświetl przychodzących tweety na pulpicie nawigacyjnym usługi Power BI.
* **Azure Data Lake, Dołącz plik**: Dodaj dane klienta do zestawu danych usługi Azure Data Lake do uwzględnienia w zadaniach usługi analytics.
* **SQL — Dodawanie wierszy**: Store danych w bazie danych do nowszej pobierania.
* **Slack — Wyślij wiadomość**: Powiadom kanał Slack o negatywną opinię, które mogą wymagać reakcji.

Można również tworzyć i funkcji platformy Azure, tak, aby można było wykonywać niestandardowe przetwarzanie na podstawie posiadanych danych. 

## <a name="process-data-with-azure-functions"></a>Przetwarzanie danych za pomocą usługi Azure Functions

Przed utworzeniem funkcji, należy utworzyć aplikację funkcji w subskrypcji platformy Azure. Ponadto dla twojej aplikacji logiki bezpośrednio wywoływać funkcję funkcji muszą być HTTP wyzwolić powiązanie, na przykład, należy użyć **HttpTrigger** szablonu. Dowiedz się, [instrukcje tworzenia pierwszej aplikacji funkcji i funkcji w witrynie Azure portal](../azure-functions/functions-create-first-azure-function-azure-portal.md).

W tym scenariuszu na użytek tekst tweetu jako treści żądania funkcji platformy Azure. W kodzie funkcji zdefiniować logikę, która określa, czy tekst tweetu zawiera słowa kluczowego lub frazy. Zachowanie funkcji jako proste lub złożone zgodnie z potrzebami dla scenariusza.
Na końcu funkcji, zwraca odpowiedź do aplikacji logiki z danymi, na przykład, wartość logiczna, prosty przykład `containsKeyword` lub obiekt złożony.

> [!TIP]
> Aby uzyskać dostęp złożone reakcji z funkcji w aplikacji logiki, użyj **Przeanalizuj dane JSON** akcji.

Gdy wszystko będzie gotowe, Zapisz funkcję i następnie dodać funkcję jako akcję w aplikacji logiki, które tworzysz.

## <a name="add-azure-function-to-logic-app"></a>Dodaj funkcję platformy Azure z aplikacją logiki

1. W Projektancie aplikacji logiki w obszarze **wykrywania tonacji** akcji, wybierz **nowy krok**.

2. Znajdź **usługi Azure Functions** łącznika, a następnie wybierz funkcję, który został utworzony.

3. W obszarze **treść żądania**, wybierz opcję **tekst Tweetu**.

![Skonfigurowane kroku funkcji platformy Azure][2]

## <a name="run-and-monitor-your-logic-app"></a>Uruchamianie i monitorowanie aplikacji logiki

Aby przejrzeć wszystkie przebiegi bieżącej lub poprzedniej aplikacji logiki, można użyć zaawansowanych, debugowania i monitorowania funkcji udostępnianych przez usługi Azure Logic Apps w witrynie Azure portal, programu Visual Studio lub za pośrednictwem interfejsów API REST platformy Azure i zestawów SDK.

Aby łatwo przetestować aplikację logiki w Projektancie aplikacji logiki, wybierz **uruchomienia wyzwalacza**. Wyzwalacz sonduje pod kątem tweety na podstawie zgodnie z określonym harmonogramem, aż do znalezienia tweetu, który spełnia Twoje kryteria. Podczas wykonywania postępów Projektant Pokazuje widok na żywo dla tego uruchomienia.

Do wyświetlania poprzednich historie uruchamiania w programie Visual Studio lub witryny Azure portal: 

* Otwórz Eksploratora chmury programu Visual Studio. Znajdź swoją aplikację logiki, otwórz menu skrótów w aplikacji. Wybierz **historii uruchamiania Open**.

  > [!NOTE]
  > Dla programu Visual Studio 2019 r Eksploratora chmury można otworzyć projektanta aplikacji logiki w witrynie Azure portal, ale nie można jeszcze Otwórz osadzonego projektanta aplikacji logiki lub historii uruchamiania.

* W witrynie Azure portal Znajdź swoją aplikację logiki. W menu aplikacji logiki wybierz **Przegląd**. 

## <a name="create-automated-deployment-templates"></a>Tworzenie szablonów automatycznego wdrażania

Po utworzeniu rozwiązania aplikacji logiki, można przechwytywać i wdrożyć aplikację jako [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment) do dowolnego regionu systemu Azure na całym świecie. Ta możliwość zarówno umożliwia modyfikowanie parametrów do utworzenia różne wersje aplikacji i integrowanie rozwiązania Azure potoków. Można także dodać usługi Azure Functions w szablonie wdrożenia, tak, aby można było zarządzać całe rozwiązanie ze wszystkimi zależnościami jako pojedynczy szablon. Dowiedz się, [jak utworzyć szablony wdrożenia aplikacji logiki](../logic-apps/logic-apps-create-deploy-template.md).

Szablon wdrożenia przykład za pomocą funkcji platformy Azure można znaleźć [repozytorium szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Kolejne kroki

* [Znajdź inne przykłady i scenariusze dla usługi Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
