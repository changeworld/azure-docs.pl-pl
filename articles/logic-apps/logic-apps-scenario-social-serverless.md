---
title: Tworzenie pulpitu nawigacyjnego szczegółowych informacji o klientach
description: Zarządzanie opiniami klientów, danymi mediów społecznościowych i nie tylko, tworząc pulpit nawigacyjny klienta za pomocą aplikacji Azure Logic Apps i usługi Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: e300bf9c9aa0acf0bed6426eb73f690f9a38bd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980420"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Tworzenie pulpitu nawigacyjnego informacji o klientach przesyłania strumieniowego za pomocą aplikacji Azure Logic Apps i usługi Azure Functions

Platforma Azure oferuje narzędzia [bezserwerowe,](https://azure.microsoft.com/solutions/serverless/) które ułatwią szybkie tworzenie i hostowanie aplikacji w chmurze bez konieczności myślenia o infrastrukturze. W tym samouczku można utworzyć pulpit nawigacyjny, który wyzwala opinie klientów, analizuje opinie za pomocą uczenia maszynowego i publikuje szczegółowe informacje do źródła, takiego jak Power BI lub Azure Data Lake.

W tym rozwiązaniu należy użyć tych kluczowych składników platformy Azure dla aplikacji bezserwerowych: [Usługi Azure Functions](https://azure.microsoft.com/services/functions/) i Usługi Azure Logic [Apps.](https://azure.microsoft.com/services/logic-apps/)
Usługa Azure Logic Apps udostępnia aparat przepływu pracy bezserwerowym w chmurze, dzięki czemu można tworzyć aranżacje między składnikami bezserwerowymi i łączyć się z ponad 200 usługami i interfejsami API. Usługa Azure Functions zapewnia przetwarzanie bezserwerowe w chmurze. To rozwiązanie używa usługi Azure Functions do oznaczania tweetów klientów na podstawie wstępnie zdefiniowanych słów kluczowych.

W tym scenariuszu utworzysz aplikację logiki, która wyzwala na znalezienie opinii od klientów. Niektóre łączniki, które pomagają odpowiedzieć na opinie klientów, obejmują Outlook.com, Office 365, Survey Monkey, Twitter i [żądanie HTTP z formularza sieci Web.](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/) Utworzony przepływ pracy monitoruje hashtag na Twitterze.

Możesz [utworzyć całe rozwiązanie w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) i [wdrożyć rozwiązanie za pomocą szablonu usługi Azure Resource Manager.](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md) Aby uzyskać przewodnik wideo, który pokazuje, jak utworzyć to rozwiązanie, [obejrzyj ten film na kanale 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Wyzwalacz danych klientów

1. W witrynie Azure portal lub visual studio utwórz pustą aplikację logiki. 

   Jeśli jesteś nowym użytkownikiem aplikacji logiki, zapoznaj się z [przewodnikiem Szybki start w witrynie Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) lub [przewodnikiem Szybki start dla programu Visual Studio.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

2. W logic app designer, znajdź i dodaj wyzwalacz Twitter, który ma tę akcję: **Po opublikowaniu nowego tweeta**

3. Skonfiguruj wyzwalacz, aby słuchać tweetów na podstawie słowa kluczowego lub hashtagu.

   W wyzwalaczach opartych na sondowaniu, takich jak wyzwalacz Twitter, właściwość cyklu określa, jak często aplikacja logiki sprawdza nowe elementy.

   ![Przykład wyzwalacza Twittera][1]

Ta aplikacja logiki teraz uruchamia się na wszystkich nowych tweets. Następnie możesz wziąć i przeanalizować dane tweeta, aby lepiej zrozumieć wyrażone uczucia. 

## <a name="analyze-tweet-text"></a>Analizowanie tekstu tweeta

Aby wykryć tonację niektórych tekstów, można użyć [usługi Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. W logic app designer, w obszarze wyzwalacz, wybierz nowy **krok**.

2. Znajdź łącznik **analizy tekstu.**

3. Wybierz akcję **Wykrywanie tonacji.**

4. Jeśli zostanie wyświetlony monit, podaj prawidłowy klucz usług Cognitive Services dla usługi analizy tekstu.

5. W obszarze **Treść żądania**zaznacz pole **Tweet Text,** które udostępnia tekst tweeta jako dane wejściowe do analizy.

Po dokonaniu danych tweeta i szczegółowych informacji na temat tweeta możesz teraz użyć kilku innych odpowiednich łączników i ich działań:

* **Power BI — dodawanie wierszy do zestawu danych przesyłania strumieniowego:** wyświetlanie przychodzących tweetów na pulpicie nawigacyjnym usługi Power BI.
* **Usługa Azure Data Lake — plik dołączania:** Dodaj dane klientów do zestawu danych usługi Azure Data Lake w celu uwzględnienia ich w zadaniach analizy.
* **SQL - Dodawanie wierszy:** Przechowywanie danych w bazie danych do późniejszego pobierania.
* **Slack - Wyślij wiadomość:** Powiadom kanał Slack o negatywnych opinii, które mogą wymagać działania.

Można również utworzyć i funkcję platformy Azure, dzięki czemu można wykonać niestandardowe przetwarzanie danych. 

## <a name="process-data-with-azure-functions"></a>Przetwarzanie danych za pomocą funkcji platformy Azure

Przed utworzeniem funkcji należy utworzyć aplikację funkcji w ramach subskrypcji platformy Azure. Ponadto dla aplikacji logiki, aby bezpośrednio wywołać funkcję, funkcja musi mieć powiązanie wyzwalacza HTTP, na przykład użyj szablonu **HttpTrigger.** Dowiedz [się, jak utworzyć pierwszą aplikację i funkcję funkcji w witrynie Azure.](../azure-functions/functions-create-first-azure-function-azure-portal.md)

W tym scenariuszu użyj tekstu tweet jako treści żądania dla funkcji platformy Azure. W kodzie funkcji zdefiniuj logikę, która określa, czy tekst tweet zawiera słowo kluczowe lub frazę. Zachowaj funkcję tak proste lub złożone, jak to konieczne dla scenariusza.
Na końcu funkcji, zwraca odpowiedź do aplikacji logiki z niektórych danych, na `containsKeyword` przykład proste wartości logicznej, takich jak lub złożony obiekt.

> [!TIP]
> Aby uzyskać dostęp do złożonej odpowiedzi z funkcji w aplikacji logiki, należy użyć **akcji Parse JSON.**

Po zakończeniu zapisz funkcję, a następnie dodaj funkcję jako akcję w aplikacji logiki, którą budujesz.

## <a name="add-azure-function-to-logic-app"></a>Dodawanie funkcji platformy Azure do aplikacji logiki

1. W logic app designer w obszarze **Akcja Wykrywanie tonacji** wybierz pozycję **Nowy krok**.

2. Znajdź łącznik **usługi Azure Functions,** a następnie wybierz utworzoną funkcję.

3. W obszarze **Treść żądania**wybierz pozycję **Tweet Text**.

![Skonfigurowany krok funkcji platformy Azure][2]

## <a name="run-and-monitor-your-logic-app"></a>Uruchamianie i monitorowanie aplikacji logiki

Aby przejrzeć wszystkie bieżące lub poprzednie przebiegi aplikacji logiki, można użyć rozbudowanych funkcji debugowania i monitorowania, które usługa Azure Logic Apps udostępnia w witrynie Azure Portal, Visual Studio lub za pośrednictwem interfejsów API resturonu i zestawów SDK platformy Azure REST.

Aby łatwo przetestować aplikację logiki, w logic app designer wybierz pozycję **Uruchom wyzwalacz**. Sondy wyzwalające dla tweetów na podstawie określonego harmonogramu, dopóki nie zostanie znaleziony tweet spełniający Twoje kryteria. Podczas postępu uruchamiania projektant pokazuje widok na żywo dla tego uruchomienia.

Aby wyświetlić poprzednie historie uruchamiania w programie Visual Studio lub w witrynie Azure portal: 

* Otwórz Eksploratora chmury programu Visual Studio. Znajdź aplikację logiki, otwórz menu skrótów aplikacji. Wybierz **pozycję Otwórz historię przebiegu**.

  > [!TIP]
  > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

* W witrynie Azure portal znajdź aplikację logiki. W menu aplikacji logiki wybierz polecenie **Przegląd**. 

## <a name="create-automated-deployment-templates"></a>Tworzenie szablonów automatycznego wdrażania

Po utworzeniu rozwiązania aplikacji logiki, można przechwytywać i wdrażać aplikację jako [szablon usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md) do dowolnego regionu platformy Azure na świecie. Tej funkcji można użyć zarówno do modyfikowania parametrów do tworzenia różnych wersji aplikacji i integracji rozwiązania z usługi Azure Potoki. Można również dołączyć usługi Azure Functions w szablonie wdrażania, dzięki czemu można zarządzać całym rozwiązaniem ze wszystkimi zależnościami jako pojedynczy szablon. Dowiedz się, jak [zautomatyzować wdrażanie aplikacji logiki](logic-apps-azure-resource-manager-templates-overview.md).

Przykładowy szablon wdrożenia z funkcją platformy Azure można sprawdzić [repozytorium szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Następne kroki

* [Znajdź inne przykłady i scenariusze dla usługi Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
