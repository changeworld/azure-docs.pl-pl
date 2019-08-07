---
title: Co to są usługi Microsoft Flow, Logic Apps, Functions i WebJobs? — Azure
description: 'Porównanie usług w chmurze firmy Microsoft zoptymalizowanych pod kątem zadań integracji: Microsoft Flow, Logic Apps, Functions i WebJobs.'
services: functions, logic-apps
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, event processing, dynamic compute, serverless architecture
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 04/09/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 7bfe586294aec5938e2245c38fbe88aa5e57a66c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839501"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>Co to są usługi Microsoft Flow, Logic Apps, Functions i WebJobs?

W tym artykule porównano następujące usługi w chmurze firmy Microsoft:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Wszystkie te usługi umożliwiają rozwiązywanie problemów z integracją i automatyzację procesów biznesowych. Mogą one definiować dane wejściowe, akcje, warunki i dane wyjściowe. Każdą z nich można uruchomić zgodnie z harmonogramem lub za pomocą wyzwalacza. Każda usługa ma unikatowe zalety, a w tym artykule opisano różnice między nimi.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Porównanie usług Microsoft Flow i Azure Logic Apps

Usługi Microsoft Flow i Logic Apps to usługi integracji *zorientowane na projektanta* i służące do tworzenia przepływów. Obie te usługi służą do integracji z różnymi aplikacjami SaaS i aplikacjami dla przedsiębiorstw. 

Usługa Microsoft Flow bazuje na usłudze Logic Apps. Współdzielą one tego samego projektanta przepływów pracy i te same [łączniki](../connectors/apis-list.md). 

Usługa Microsoft Flow umożliwia każdemu pracownikowi biurowemu wykonywanie prostych integracji (na przykład utworzenie procesu zatwierdzania w bibliotece dokumentów programu SharePoint) bez konieczności zwracania się do deweloperów lub działu IT. Usługa Logic Apps umożliwia także korzystanie z zaawansowanych integracji (na przykład procesów B2B) wymagających usługi Azure DevOps i rozwiązań dotyczących zabezpieczeń na poziomie przedsiębiorstwa. Typowe dla biznesowego przepływu pracy jest zwiększanie się stopnia skomplikowania wraz z upływem czasu. W związku z tym możesz zacząć od przepływu, a następnie przekonwertować go na aplikację logiki odpowiednio do potrzeb.

Poniższa tabela ułatwia określenie, czy dla danej integracji najlepsza jest usługa Microsoft Flow, czy Logic Apps:

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| Użytkownicy |Pracownicy biurowi, użytkownicy biznesowi, administratorzy programu SharePoint |Profesjonalni integratorzy i programiści, informatycy |
| Scenariusze |Samoobsługa |Zaawansowane integracje |
| Narzędzie do projektowania |Aplikacja w przeglądarce i aplikacja mobilna, tylko interfejs użytkownika |Aplikacja w przeglądarce i program [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), dostępny [widok kodu](../logic-apps/logic-apps-author-definitions.md) |
| Zarządzanie cyklem życia aplikacji |Projektowanie i testowanie w środowiskach nieprodukcyjnych, przenoszenie do środowiska produkcyjnego, gdy aplikacja jest gotowa |Usługa Azure DevOps: kontrola kodu źródłowego, testowanie, pomoc techniczna, automatyzacja oraz możliwości zarządzania w usłudze [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Środowisko administratora |Zarządzanie środowiskami usługi Microsoft Flow i zasadami ochrony przed utratą danych, śledzenie licencjonowania: [Centrum administracyjne usługi Microsoft Flow](https://admin.flow.microsoft.com) |Zarządzanie grupami zasobów, połączeniami, dostępem oraz rejestrowanie: [Azure Portal](https://portal.azure.com) |
| Bezpieczeństwo |Dzienniki inspekcji dotyczące zabezpieczeń i zgodności usługi Office 365, ochrona przed utratą danych, [szyfrowanie magazynowanych](https://wikipedia.org/wiki/Data_at_rest#Encryption) danych poufnych |Zapewnienie bezpieczeństwa platformy Azure: [Zabezpieczenia platformy Azure](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [dzienniki inspekcji](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Porównanie usług Azure Functions i Azure Logic Apps

Usługi Functions i Logic Apps to usługi platformy Azure umożliwiające użycie obciążeń bezserwerowych. Azure Functions to bezserwerowa usługa obliczeniowa platformy Azure, natomiast Azure Logic Apps to usługa udostępniająca bezserwerowe przepływy pracy. Obie umożliwiają tworzenie złożonych *orkiestracji*. Aranżacja to zbiór funkcji i kroków, nazywanych *akcjami* w usłudze Logic Apps, które są wykonywane w celu wykonywania złożonego zadania. Na przykład w celu przetworzenia partii zamówień możesz równolegle wykonać wiele wystąpień funkcji, poczekać na zakończenie wszystkich wystąpień, a następnie wykonać funkcję, która obliczy zagregowany wynik.

W przypadku usługi Azure Functions tworzenie aranżacji odbywa się przez pisanie kodu i używanie [rozszerzenia funkcji trwałych](durable/durable-functions-concepts.md). Na potrzeby usługi Logic Apps aranżacje są tworzone za pomocą graficznego interfejsu użytkownika lub przez edycję plików konfiguracyjnych.

Można mieszać i dopasowywać usługi podczas tworzenia aranżacji, wywołując usługę Functions z usługi Logic Apps i na odwrót. Wybierz sposób tworzenia poszczególnych aranżacji na podstawie możliwości usług lub własnych preferencji. W poniższej tabeli wymieniono niektóre kluczowe różnice między tymi usługami:
 
|  | Trwałe funkcje | Logic Apps |
| --- | --- | --- |
| Opracowywanie zawartości | Najpierw kod (imperatywne) | Najpierw projektant (deklaratywne) |
| Łączność | [Około dwunastu wbudowanych typów powiązań](functions-triggers-bindings.md#supported-bindings), pisanie kodu dla powiązań niestandardowych | [Duża kolekcja łączników](../connectors/apis-list.md), [pakiet integracyjny dla przedsiębiorstw dla scenariuszy B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [tworzenie łączników niestandardowych](../logic-apps/custom-connector-overview.md) |
| Akcje | Każde działanie jest funkcją platformy Azure; pisanie kodu dla funkcji działań |[Duża kolekcja gotowych do użycia akcji](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Monitorowanie | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [witryna Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [dzienniki usługi Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Zarządzanie | [interfejs API REST](durable/durable-functions-http-api.md), [program Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [witryna Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [interfejs API REST](https://docs.microsoft.com/rest/api/logic/), [program PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [program Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Kontekst wykonywania | Może działać [lokalnie](functions-runtime-overview.md) lub w chmurze | Działa tylko w chmurze|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Porównanie usług Functions i WebJobs

Podobnie jak usługa Azure Functions, usługa Azure App Service WebJobs z zestawem SDK usługi WebJobs jest usługą integracji typu *najpierw kod* przeznaczoną dla deweloperów. Obie bazują na usłudze [Azure App Service](../app-service/overview.md) i obsługują funkcje, takie jak [integracja kontroli kodu źródłowego](../app-service/deploy-continuous-deployment.md), [uwierzytelnianie](../app-service/overview-authentication-authorization.md) i [monitorowanie z integracją usługi Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>Usługa WebJobs i zestaw SDK usługi WebJobs

Funkcja *WebJobs* usługi App Service umożliwia uruchamianie skryptu lub kodu w kontekście aplikacji internetowej usługi App Service. *Zestaw SDK usługi WebJobs* to struktura przeznaczona dla usługi WebJobs, która upraszcza kod pisany w celu reagowania na zdarzenia w usługach platformy Azure. Na przykład możesz zareagować na utworzenie obiektu blob obrazu w usłudze Azure Storage, tworząc obraz miniatury. Zestaw SDK usługi WebJobs działa jako aplikacja konsoli .NET, którą można wdrożyć w zadaniu WebJob. 

Usługa WebJobs i zestaw SDK usługi WebJobs najlepiej działają razem, ale można używać usługi WebJobs bez zestawu SDK usługi WebJobs i na odwrót. Zadanie WebJob może uruchomić dowolny program lub skrypt działający w piaskownicy usługi App Service. Aplikację konsolową zestawu SDK usługi WebJobs można uruchomić w każdym miejscu, w którym jest uruchomiona aplikacja konsolowa, np. na serwerach lokalnych.

### <a name="comparison-table"></a>Tabela porównawcza

Usługa Azure Functions bazuje na zestawie SDK usługi WebJobs, dlatego współużytkuje z innymi usługami platformy Azure liczne wyzwalacze i połączenia. Poniżej przedstawiono kilka czynników, które należy wziąć pod uwagę, wybierając między usługą Azure Functions a usługą WebJobs z zestawem WebJobs SDK:

|  | Funkcje | Usługa WebJobs z zestawem SDK usługi WebJobs |
| --- | --- | --- |
|[Model aplikacji bez użycia serwera](https://azure.microsoft.com/solutions/serverless/) z [automatycznym skalowaniem](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Programowanie i testowanie w przeglądarce](functions-create-first-azure-function.md) |✔||
|[Płatność za użycie](functions-scale.md#consumption-plan)|✔||
|[Integracja z usługą Logic Apps](functions-twitter-email.md)|✔||
| Zdarzenia wyzwalacza |[Timer](functions-bindings-timer.md)<br>[Kolejki i obiekty blob usługi Azure Storage](functions-bindings-storage-blob.md)<br>[Kolejki i tematy usługi Azure Service Bus](functions-bindings-service-bus.md)<br>[Usługi Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Kolejki i obiekty blob usługi Azure Storage](functions-bindings-storage-blob.md)<br>[Kolejki i tematy usługi Azure Service Bus](functions-bindings-service-bus.md)<br>[Usługi Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[System plików](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Obsługiwane języki  |C#<br>F#<br>JavaScript<br>Java<br>Python (wersja zapoznawcza) |C#<sup>1</sup>|
|Menedżer pakietów|NPM i NuGet|NuGet<sup>2</sup>|

<sup>1</sup> Zadania WebJob (bez zestawu SDK WebJob) obsługują C#, Java, JavaScript, bash,. cmd,. bat, PowerShell, php, TypeScript, Python i innych. To nie jest pełna lista. Zadanie WebJob może uruchomić dowolny program lub skrypt, który można uruchomić w piaskownicy usługi App Service.

<sup>2</sup> Usługa WebJobs (bez zestawu WebJobs SDK) obsługuje narzędzia NPM i NuGet.

### <a name="summary"></a>Podsumowanie

Usługa Azure Functions zapewnia większą efektywność pracy deweloperów niż usługa Azure App Service WebJobs. Udostępnia ona także więcej opcji dotyczących języków programowania, środowisk programistycznych, integracji usług platformy Azure i cen. W przypadku większości scenariuszy jest najlepszym wyborem.

Poniżej przedstawiono dwa scenariusze, w których usługa WebJobs może być najlepszym wyborem:

* Potrzebujesz większej kontroli nad kodem nasłuchującym zdarzeń, obiektem `JobHost`. Usługa Functions oferuje ograniczoną liczbę sposobów dostosowywania zachowania obiektu `JobHost` w pliku [host.json](functions-host-json.md). Czasami trzeba wykonać czynności, których nie można określić za pomocą ciągu w pliku JSON. Na przykład tylko zestaw SDK usługi WebJobs pozwala skonfigurować niestandardowe zasady ponawiania dla usługi Azure Storage.
* Masz aplikację usługi App Service, dla której chcesz uruchamiać fragmenty kodu, i chcesz zarządzać nimi razem w tym samym środowisku usługi Azure DevOps.

W innych scenariuszach, w których chcesz uruchamiać fragmenty kodu na potrzeby integracji platformy Azure lub usług innych firm, wybierz usługę Azure Functions, a nie usługę WebJobs z zestawem SDK usługi WebJobs.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Usługi Microsoft Flow, Logic Apps, Functions i WebJobs razem

Nie trzeba wybierać tylko jednej z tych usług. Integrują się one ze sobą tak dobrze, jak z usługami zewnętrznymi.

Przepływ może wywołać aplikację logiki. Aplikacja logiki może wywołać funkcję, a funkcja może wywołać aplikację logiki. Zobacz na przykład [Tworzenie funkcji integrującej się z usługą Azure Logic Apps](functions-twitter-email.md).

Integracja usług Microsoft Flow, Logic Apps i Functions jest cały czas ulepszana. Możesz utworzyć jakiś element w jednej usłudze i używać go w innych usługach.

Więcej informacji na temat tych usług integracji możesz uzyskać, korzystając z następujących linków:

* [Korzystanie z usług Azure Functions i Azure App Service w scenariuszach integracji (autor: Christopher Anderson)](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Uproszczone integracje (autor: Charles Lamanna)](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps — emisja internetowa na żywo](https://aka.ms/logicappslive)
* [Microsoft Flow — często zadawane pytania](https://flow.microsoft.com/documentation/frequently-asked-questions/)

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy od utworzenia pierwszego przepływu, aplikacji logiki lub funkcji aplikacji. Wybierz dowolny z następujących linków:

* [Rozpoczynanie pracy z platformą Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md)
