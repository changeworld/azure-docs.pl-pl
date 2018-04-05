---
title: Wybieranie między usługami Flow, Logic Apps, Functions i WebJobs | Microsoft Docs
description: Porównaj i zestaw ze sobą usługi integracji z chmurą firmy Microsoft, a następnie zdecyduj, których usług chcesz używać.
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: wpickett
tags: ''
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, event processing, dynamic compute, serverless architecture
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 577031c58e95781dc97721acc71fb22114b1c606
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Wybieranie między przepływem, aplikacjami logiki, funkcjami a zadaniami WebJob

W tym artykule porównano i przeciwstawiono sobie następujące usługi w chmurze firmy Microsoft:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

Wszystkie te usługi umożliwiają rozwiązywanie problemów z integracją i automatyzację procesów biznesowych. Mogą one definiować dane wejściowe, akcje, warunki i dane wyjściowe. Każdą z nich można uruchomić zgodnie z harmonogramem lub za pomocą wyzwalacza. Jednak każda usługa ma unikatowe zalety, a w tym artykule opisano różnice między nimi.

## <a name="flow-vs-logic-apps"></a>Flow a Logic Apps

Microsoft Flow i Azure Logic Apps to usługi integracji typu *najpierw konfiguracja*. Obie umożliwiają tworzenie przepływów pracy, które integrują się z różnymi aplikacjami SaaS i aplikacjami dla przedsiębiorstw. 

Usługa Flow bazuje na usłudze Logic Apps. Mają tego samego projektanta przepływów pracy i te same [łączniki](../connectors/apis-list.md). 

Usługa Flow umożliwia każdemu pracownikowi biurowemu wykonywanie prostych integracji (na przykład procesu zatwierdzania w bibliotece dokumentów programu SharePoint) bez konieczności zwracania się do deweloperów lub działu IT. Z drugiej strony usługa Logic Apps umożliwia korzystanie z zaawansowanych integracji (na przykład procesów B2B), w których są wymagane operacje deweloperskie i praktyki dotyczące zabezpieczeń na poziomie przedsiębiorstw. Typowe dla biznesowego przepływu pracy jest zwiększanie się stopnia skomplikowania wraz z upływem czasu. W związku z tym możesz zacząć od przepływu, a następnie przekonwertować go na aplikację logiki odpowiednio do potrzeb.

Poniższa tabela ułatwia określenie, czy dla danej integracji najlepsza jest usługa Flow, czy Logic Apps.

|  | Ruch | Logic Apps |
| --- | --- | --- |
| Użytkownicy |Pracownicy biurowi, użytkownicy biznesowi, administratorzy programu SharePoint |Profesjonalni integratorzy i programiści, informatycy |
| Scenariusze |Samoobsługa |Zaawansowane integracje |
| Narzędzie do projektowania |Aplikacja w przeglądarce i aplikacja mobilna, tylko interfejs użytkownika |Aplikacja w przeglądarce i program [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), dostępny [widok kodu](../logic-apps/logic-apps-author-definitions.md) |
| Zarządzanie cyklem życia aplikacji |Projektowanie i testowanie w środowiskach nieprodukcyjnych, przenoszenie do środowiska produkcyjnego, gdy aplikacja będzie gotowa. |Operacje deweloperskie: kontrola kodu źródłowego, testowanie, pomoc techniczna, automatyzacja oraz możliwości zarządzania w usłudze [Azure Resource Management](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Doświadczenie administratora |Zarządzanie środowiskami usługi Flow i zasadami ochrony przed utratą danych, śledzenie licencjonowania [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Zarządzanie grupami zasobów, połączeniami, zarządzanie dostępem i rejestrowanie [https://portal.azure.com](https://portal.azure.com) |
| Bezpieczeństwo |Dzienniki inspekcji dotyczące zabezpieczeń i zgodności usługi Office 365, ochrona przed utratą danych, [szyfrowanie w spoczynku](https://wikipedia.org/wiki/Data_at_rest#Encryption) poufnych danych itp. |Zapewnienie bezpieczeństwa platformy Azure: [zabezpieczenia platformy Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [dzienniki inspekcji](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) itd. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions a Zadania WebJob

Podobnie jak usługa Azure Functions, usługa Azure App Service WebJobs z zestawem SDK usługi WebJobs jest usługą integracji typu *najpierw kod* przeznaczoną dla deweloperów. Obie bazują na usłudze [Azure App Service](../app-service/app-service-web-overview.md) i obsługują funkcje, takie jak [integracja kontroli kodu źródłowego](../app-service/app-service-continuous-deployment.md), [uwierzytelnianie](../app-service/app-service-authentication-overview.md) i [monitorowanie z integracją usługi Application Insights](functions-monitoring.md).

### <a name="webjobs-vs-the-webjobs-sdk"></a>Usługa WebJobs a zestaw SDK usługi WebJobs

Funkcja *WebJobs* usługi App Service umożliwia uruchamianie skryptu lub kodu w kontekście aplikacji internetowej usługi App Service. *Zestaw SDK usługi WebJobs* to struktura przeznaczona dla usługi WebJobs, która upraszcza kod pisany w celu reagowania na zdarzenia w usługach platformy Azure. Można na przykład zareagować na utworzenie obiektu blob obrazu w usłudze Azure Storage, tworząc obraz miniatury. Zestaw SDK usługi WebJobs działa jako aplikacja konsoli .NET, którą można wdrożyć w zadaniu WebJob. 

Usługa WebJobs i zestaw SDK usługi WebJobs najlepiej działają razem, ale można używać usługi WebJobs bez zestawu SDK usługi WebJobs i na odwrót. Zadanie WebJob może uruchomić dowolny program lub skrypt, który można uruchomić w piaskownicy usługi App Service. Aplikację konsoli zestawu SDK usługi WebJobs można uruchomić w każdym miejscu, w którym jest możliwe uruchamianie aplikacji konsoli, np. na serwerach lokalnych.

### <a name="comparison-table"></a>Tabela porównawcza

Usługa Azure Functions bazuje na zestawie SDK usługi WebJobs, dlatego współużytkuje z innymi usługami platformy Azure liczne wyzwalacze i połączenia. Poniżej przedstawiono kilka czynników, które należy wziąć pod uwagę, wybierając między usługą Azure Functions i usługą WebJobs z zestawem SDK usługi WebJobs:

|  | Funkcje | Usługa WebJobs z zestawem SDK usługi WebJobs |
| --- | --- | --- |
|[Model aplikacji bez użycia serwera](https://azure.microsoft.com/overview/serverless-computing/) z [automatycznym skalowaniem](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Programowanie i testowanie w przeglądarce](functions-create-first-azure-function.md) |✔||
|[Płatność za użycie](functions-scale.md#consumption-plan)|✔||
|[Integracja z usługą Logic Apps](functions-twitter-email.md)|✔||
| Zdarzenia wyzwalacza |[Czasomierz](functions-bindings-timer.md)<br>[Kolejki i obiekty blob usługi Azure Storage](functions-bindings-storage-blob.md)<br>[Kolejki i tematy usługi Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Czasomierz](functions-bindings-timer.md)<br>[Kolejki i obiekty blob usługi Azure Storage](functions-bindings-storage-blob.md)<br>[Kolejki i tematy usługi Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[System plików](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Obsługiwane języki  |C#<br>F#<br>JavaScript<br>Java (wersja zapoznawcza) |C#<sup>1</sup>|
|Menedżer pakietów|NPM i NuGet|NuGet<sup>2</sup>|

<sup>1</sup> Usługa WebJobs (bez zestawu SDK usługi WebJobs) obsługuje narzędzia C#, JavaScript, Bash, cmd, bat, PowerShell, PHP, TypeScript, Python itd. Nie jest to pełna lista. Zadanie WebJob może uruchomić dowolny program lub skrypt, który można uruchomić w piaskownicy usługi App Service.

<sup>2</sup> Usługa WebJobs (bez zestawu SDK usługi WebJobs) obsługuje narzędzia NPM i NuGet.

### <a name="summary"></a>Podsumowanie

Usługa Azure Functions zapewnia większą produktywność dla deweloperów, więcej opcji języków programowania, więcej opcji środowisk programowania, więcej opcji integracji usług platformy Azure i więcej opcji cen. W przypadku większości scenariuszy jest najlepszym wyborem.

Poniżej przedstawiono dwa scenariusze, w których usługa WebJobs może być najlepszym wyborem:

* Potrzebujesz większej kontroli nad kodem nasłuchującym zdarzeń, obiektem `JobHost`. Usługa Functions oferuje ograniczoną liczbę sposobów dostosowywania zachowania obiektu `JobHost` w pliku [host.json](functions-host-json.md). Czasami trzeba wykonać czynności, których nie można określić za pomocą ciągu w pliku JSON. Na przykład tylko zestaw SDK usługi WebJobs pozwala skonfigurować niestandardowe zasady ponawiania dla usługi Azure Storage.
* Masz aplikację usługi App Service, dla której chcesz uruchamiać fragmenty kodu, i chcesz zarządzać nimi razem w tym samym środowisku DevOps.

W innych scenariuszach, w których chcesz uruchamiać fragmenty kodu na potrzeby integracji platformy Azure lub usług innych firm, wybierz usługę Azure Functions, a nie usługę WebJobs z zestawem SDK usługi WebJobs.

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>Usługi Flow, Logic Apps, Functions i WebJobs razem

Nie musisz wybierać tylko jednej z tych usług. Integrują się one ze sobą tak samo jak z usługami zewnętrznymi.

Przepływ może wywołać aplikację logiki. Aplikacja logiki może wywołać funkcję, a funkcja może wywołać aplikację logiki. Zobacz na przykład [Tworzenie funkcji integrującej się z usługą Azure Logic Apps](functions-twitter-email.md).

Integracja usług Flow, Logic Apps i Functions jest cały czas ulepszana. Możesz utworzyć jakiś element w jednej usłudze i używać go w innych usługach.

## <a name="next-steps"></a>Następne kroki

Rozpoczynanie pracy od utworzenia pierwszego przepływu, aplikacji logiki lub funkcji aplikacji. Kliknij dowolny z następujących linków:

* [Rozpoczynanie pracy z platformą Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md)

Możesz również uzyskać więcej informacji na temat tych usług integracji, korzystając z następujących linków:

* [Korzystanie z usług Azure Functions i Azure App Service w scenariuszach integracji (autor: Christopher Anderson)](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Uproszczone integracje (autor: Charles Lamanna)](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps — emisja internetowa na żywo](http://aka.ms/logicappslive)
* [Microsoft Flow — często zadawane pytania](https://flow.microsoft.com/documentation/frequently-asked-questions/)
