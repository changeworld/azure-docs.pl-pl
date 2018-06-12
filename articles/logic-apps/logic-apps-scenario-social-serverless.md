---
title: Scenariusz niekorzystającą — Utwórz pulpit nawigacyjny szczegółowych informacji klienta przy użyciu platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można zarządzać opinie klientów, dane mediów społecznościowych i innych Tworzenie pulpitu nawigacyjnego klienta z usługi Azure Logic Apps i usługi Azure Functions
keywords: ''
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jehollan; LADocs
ms.openlocfilehash: 3ee3ec3107cf8aad834e8201405c9aa833d838af
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299964"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Utwórz przesyłania strumieniowego pulpit nawigacyjny szczegółowych informacji klienta z usługi Azure Logic Apps i usługi Azure Functions

System Azure oferuje niekorzystającą narzędzia, które ułatwiają szybkie kompilacji i hosta aplikacji w chmurze, bez myśleć o infrastruktury. W tym samouczku można utworzyć pulpit nawigacyjny, który wyzwala na opinie klientów, analizuje Reaguj uczenia maszynowego i publikuje wgląd w źródła, takich jak usługi Power BI lub usługi Azure Data Lake.

Dla tego rozwiązania, użyj tych najważniejsze składniki platformy Azure dla aplikacji niekorzystającą: [usługi Azure Functions](https://azure.microsoft.com/services/functions/) i [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Aplikacje logiki platformy Azure udostępnia aparatem niekorzystającą przepływu pracy w chmurze, dzięki czemu można tworzyć orchestrations między składnikami niekorzystającą i łączyć się z 200 + usług i interfejsów API. Środowisko Azure Functions zapewnia niekorzystającą obliczeniowych w chmurze. To rozwiązanie używa usługi Azure Functions dla Flagowanie tweetów klienta na podstawie wstępnie zdefiniowanych słów kluczowych.

W tym scenariuszu utworzysz aplikację logiki, które wyzwala znajdowanie opinie od klientów. Niektóre czy pomoc w odpowiedzi na opinie klientów obejmują Outlook.com, usługi Office 365, ankiety małp, Twitter, łączniki i [żądania HTTP za pomocą formularza sieci web](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Przepływ pracy, który można utworzyć monitoruje hasztagiem w serwisie Twitter.

Możesz [skompilować całe rozwiązanie w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) i [wdrażanie rozwiązania z szablonem usługi Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md). Przewodnik wideo przedstawiający sposób tworzenia tego rozwiązania [Obejrzyj ten film Channel 9](http://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Wyzwalanie w zależności od danych klientów

1. W portalu Azure lub programu Visual Studio tworzenie aplikacji logiki puste. 

   Jeśli jesteś nowym użytkownikiem aplikacji logiki, przejrzyj [Szybki Start dla portalu Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md) lub [szybkiego startu dla programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. W Projektancie aplikacji logiki, odnaleźć i dodać wyzwalaczy usługi Twitter, które ma tę akcję: **po jest przesyłana z nowego tweet**

3. Konfigurowanie wyzwalacz do nasłuchiwania na podstawie — słowo kluczowe lub hasztagiem tweetów.

   Na podstawie sondowania wyzwalaczy, takich jak wyzwalacza Twitter właściwość cyklu określa, jak często sprawdza aplikacji logiki dla nowych elementów.

   ![Przykład wyzwalacza Twitter][1]

Ta aplikacja logiki teraz generowane na wszystkich nowych tweetów. Można wykonać i analizować dane tweet, dzięki czemu można lepiej zrozumieć opinie wyrażone. 

## <a name="analyze-tweet-text"></a>Analizowanie tekstu tweet

Aby wykryć wskaźniki nastrojów klientów za część tekstu, można użyć [kognitywnych usług Azure](https://azure.microsoft.com/services/cognitive-services/).

1. W Projektancie aplikacji logiki, w obszarze wyzwalacza, wybierz **nowy krok**.

2. Znajdź **Analiza tekstu** łącznika.

3. Wybierz **wykrywa wskaźniki nastrojów klientów** akcji.

4. Po wyświetleniu monitu podaj prawidłowy klucz kognitywnych usług dla usługi Analiza tekstu.

5. W obszarze **treść żądania**, wybierz pozycję **tekst Tweet** pola, które zawiera tekst tweet jako dane wejściowe do analizy.

Po uzyskaniu tweet dane i informacje o tweet można teraz używać kilka innych odpowiednich łączników i działań:

* **Power BI — Dodawanie wierszy do przesyłania strumieniowego zestawu danych**: Wyświetl tweetów przychodzących na pulpicie nawigacyjnym usługi Power BI.
* **Azure Data Lake — Dołącz plik**: Dodaj do zestawu danych usługi Azure Data Lake do uwzględnienia w zadania usługi analiza danych klienta.
* **SQL — Dodawanie wierszy**: przechowywanie danych w bazie danych do nowszej pobierania.
* **Slack — wysyłanie wiadomości**: powiadomić kanału Slack o negatywnej opinii, który może wymagać podjęcia akcji.

Można również tworzyć i Azure działać tak, aby można było wykonać niestandardowe przetwarzanie na podstawie danych. 

## <a name="process-data-with-azure-functions"></a>Proces danych za pomocą usługi Azure Functions

Przed utworzeniem funkcji tworzenia aplikacji funkcji w ramach subskrypcji platformy Azure. Ponadto aplikacji logiki do bezpośredniego wywoływania funkcji, funkcji muszą być HTTP wyzwolenia powiązanie, na przykład, użyj **HttpTrigger** szablonu. Dowiedz się [tworzenie pierwszej aplikacji funkcji i funkcji w portalu Azure](../azure-functions/functions-create-first-azure-function-azure-portal.md).

W tym scenariuszu należy użyć tekstu tweet jako treści żądania dla funkcji Azure. W kodzie funkcja zdefiniować logikę, która określa, czy tekst tweet zawiera słowo kluczowe lub wyrażenie. Zachowaj funkcji jako prostymi lub złożonymi zgodnie z potrzebami dla scenariusza.
Na końcu funkcja zwraca odpowiedź do aplikacji logiki z niektórych danych, na przykład, wartość logiczna dla prosty przykład `containsKeyword` lub obiekt złożony.

> [!TIP]
> Aby uzyskać dostęp do złożonych odpowiedzi przez funkcję aplikacji logiki, użyj **przeanalizować JSON** akcji.

Gdy wszystko będzie gotowe, Zapisz funkcji, a następnie dodać funkcję jako akcji w aplikacji logiki, które tworzysz.

## <a name="add-azure-function-to-logic-app"></a>Dodawanie funkcji platformy Azure do aplikacji logiki

1. W Projektancie aplikacji logiki w obszarze **wykrywa wskaźniki nastrojów klientów** akcji, wybierz **nowy krok**.

2. Znajdź **usługi Azure Functions** łącznika, a następnie wybierz funkcję, która zostanie utworzona.

3. W obszarze **treść żądania**, wybierz pozycję **tekst Tweet**.

![Skonfigurowany krok funkcji platformy Azure][2]

## <a name="run-and-monitor-your-logic-app"></a>Uruchomić i monitorować aplikację logiki

Aby wyświetlić wszystkie bieżące i poprzednie uruchomień aplikacji logiki, można użyć zaawansowanej debugowania i możliwości, które Azure Logic Apps oferuje w portalu Azure, programu Visual Studio lub za pośrednictwem interfejsów API REST Azure i zestawy SDK monitorowania.

Aby łatwo przetestuj aplikację logiki w Projektancie aplikacji logiki, wybierz **uruchomić wyzwalacz**. Wyzwalacz sonduje wyszukiwane tweety oparte na określonego harmonogramu, aż do znalezienia tweet, który spełnia kryteria. Podczas wykonywania realizowany Projektant przedstawiono na żywo dla tego uruchomienia.

Do widoku poprzedniego uruchomienia historii w programie Visual Studio lub w portalu Azure: 

* Otwórz Eksplorator chmury programu Visual Studio. Znajdź aplikację logiki, otwórz menu skrótów aplikacji. Wybierz **Historia uruchomień Otwórz**.

* W portalu Azure Znajdź aplikację logiki. W menu aplikację logiki, wybierz **omówienie**. 

## <a name="create-automated-deployment-templates"></a>Tworzenie szablonów automatycznego wdrażania

Po utworzeniu rozwiązania aplikacji logiki, można przechwycić i wdrożyć aplikację jako [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment) na dowolny region platformy Azure na świecie. Ta funkcja służy zarówno do modyfikowania parametrów do utworzenia różnych wersji aplikacji i integrowanie rozwiązania kompilacji i wydania potoku. Możesz również uwzględnić usługi Azure Functions w szablonie wdrożenia, dzięki czemu można zarządzać całe rozwiązanie wszystkich zależności jako jednego szablonu. Dowiedz się [jak utworzyć szablony wdrażania aplikacji logiki](../logic-apps/logic-apps-create-deploy-template.md).

Przykład wdrożenia szablonu z funkcją Azure, sprawdź [repozytorium szablonów Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Kolejne kroki

* [Znajdź inne przykłady i scenariusze dla usługi Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png