---
title: Przykłady & typowych scenariuszy
description: Znajdowanie przykładów, typowych scenariuszy, samouczków i przewodników dla aplikacji Logika Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164631"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Typowe scenariusze, przykłady, samouczki i wskazówki dotyczące usługi Azure Logic Apps

[Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) ułatwia organizowanie i integrowanie różnych usług, udostępniając [setki gotowych do użycia łączników,](../connectors/apis-list.md)od lokalnego programu SQL Server lub SAP po usługi Azure Cognitive Services. Usługa Logic Apps jest "bezserwerowa", więc nie musisz się martwić o skalę lub wystąpienia. Wszystko, co musisz zrobić, to zdefiniować przepływ pracy za pomocą wyzwalacza i akcji, które wykonuje przepływ pracy. Podstawowa platforma obsługuje skalę, dostępność i wydajność. Aplikacje logiki jest szczególnie przydatne w przypadkach użycia i scenariuszy, w których należy koordynować akcje w wielu systemach i usługach.

Aby ułatwić zapoznanie się z możliwościami i wzorcami, które obsługuje usługa Azure Logic Apps, w tym artykule opisano typowe punkty początkowe, przykłady i scenariusze.

## <a name="common-starting-points-for-logic-app-workflows"></a>Typowe punkty początkowe dla przepływów pracy aplikacji logiki

Każda aplikacja logiki rozpoczyna się od [*wyzwalacza*](../logic-apps/logic-apps-overview.md#logic-app-concepts)i tylko jeden wyzwalacz, który uruchamia przepływ pracy aplikacji logiki i przekazuje w dowolnych danych jako część tego wyzwalacza. Niektóre łączniki zapewniają wyzwalacze, które są dostępne w następujących typach:

* *Wyzwalacze sondowania:* regularnie sprawdza punkt końcowy usługi dla nowych danych. Gdy istnieją nowe dane, wyzwalacz tworzy i uruchamia nowe wystąpienie przepływu pracy z danymi jako dane wejściowe.

* *Wyzwalacze wypychania:* nasłuchuje danych w punkcie końcowym usługi i czeka, aż nastąpi określone zdarzenie. Gdy zdarzenie się stanie, wyzwalacz uruchamia się natychmiast, tworząc i uruchamiając nowe wystąpienie przepływu pracy, które używa wszelkich dostępnych danych jako dane wejściowe.

Oto przykłady opisujące często używane wyzwalacze:

* *Wyzwalacze sondowania:*

  * [Wyzwalacz **cyklu** ](../connectors/connectors-native-recurrence.md) umożliwia ustawienie daty i godziny rozpoczęcia oraz cyklu do uruchamiania aplikacji logiki. Na przykład można wybrać dni tygodnia i godziny wyzwolenia aplikacji logiki. Więcej informacji można znaleźć w następujących tematach:<p>

    * [Planowanie i uruchamianie cyklicznych automatycznych zadań, procesów i przepływów pracy przy użyciu usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Samouczek: tworzenie zautomatyzowanych, opartych na harmonogramie cyklicznych przepływów pracy przy użyciu aplikacji Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Wyzwalacz **Po odebraniu wiadomości e-mail** umożliwia aplikacji logiki sprawdzanie nowych wiadomości e-mail od dowolnego dostawcy poczty obsługiwanego przez aplikacje logiki, na przykład [usługi Office 365 Outlook,](../connectors/connectors-create-api-office365-outlook.md) [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/)itd. Więcej informacji można znaleźć w następujących tematach:<p>

    * [Samouczek: tworzenie zautomatyzowanych przepływów pracy opartych na zatwierdzaniu przy użyciu aplikacji Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Samouczek: Automatyzuj zadania do przetwarzania wiadomości e-mail przy użyciu aplikacji Azure Logic Apps, Azure Functions i Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [Wyzwalacz **HTTP** ](../connectors/connectors-native-http.md) może wywołać punkt końcowy usługi za pośrednictwem protokołu HTTP lub HTTPS. Aby uzyskać więcej informacji, zobacz [Wywołanie, wyzwalanie lub zagnieżdżanie przepływów pracy przy użyciu punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md).

* *Wyzwalacze* wypychania:

  * [ **Wyzwalacz żądania** ](../connectors/connectors-native-reqres.md) może odbierać przychodzące żądania HTTPS.

  * [Wyzwalacz **http webhook** ](../connectors/connectors-native-webhook.md) subskrybuje punkt końcowy usługi, rejestrując *adres URL wywołania zwrotnego* w tej usłudze. W ten sposób usługa może po prostu powiadomić wyzwalacz, gdy zdarzenie określone ma miejsce, tak aby wyzwalacz nie musi sondować usługi.

Po wystąpieniu określonego zdarzenia wyzwalacz jest uruchamiany, co tworzy nowe wystąpienie przepływu pracy aplikacji logiki i uruchamia akcje w przepływie pracy. Dostęp do dowolnych danych z wyzwalacza w całym przepływie pracy. Na przykład Twitter **Na nowy** wyzwalacz ćwierkanie przekazuje zawartość ćwierkanie do uruchomienia aplikacji logiki. Aby rozpocząć korzystanie z usługi Azure Logic Apps, wypróbuj następujące tematy szybkiego startu:

* [Szybki start: tworzenie pierwszego zautomatyzowanego przepływu pracy przy użyciu usługi Azure Logic Apps — Witryna Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Szybki start: tworzenie zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu aplikacji Azure Logic Apps — Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Szybki start: tworzenie zautomatyzowanych przepływów pracy aplikacji logiki i zarządzanie nimi przy użyciu kodu programu Visual Studio](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Sterowanie przepływem i możliwościami obsługi błędów

Aplikacje logiki obejmują zaawansowane funkcje przepływu sterowania, takie jak warunki, przełączniki, pętle i zakresy. Aby zapewnić odporne rozwiązania, można również zaimplementować obsługę błędów i wyjątków w przepływach pracy.

* Wykonywanie różnych akcji na podstawie [instrukcji warunkowych](../logic-apps/logic-apps-control-flow-conditional-statement.md) i [przełączania instrukcji](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Powtarzanie kroków lub przetwarzanie elementów w tablicach i kolekcjach za pomocą pętli](../logic-apps/logic-apps-control-flow-loops.md)
* [Grupowanie działań wraz z zakresami](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Dodawanie obsługi błędów i wyjątków do przepływu pracy](../logic-apps/logic-apps-exception-handling.md)
* [Przypadek użycia: Jak firma opieki zdrowotnej używa obsługi wyjątków aplikacji logiki dla przepływów pracy HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Tworzenie niestandardowych interfejsów API i łączników

W przypadku systemów i usług, które nie mają opublikowanych łączników, można również rozszerzyć aplikacje logiki.

* [Tworzenie niestandardowych interfejsów API do wywoływania z aplikacji Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Regularnie sprawdzaj dostępność nowych danych lub zdarzeń za pomocą wzorca wyzwalacza sondowania](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Czekaj i nasłuchuj nowych danych lub zdarzeń przy użyciu wzorca wyzwalacza elementu webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Wykonywanie długotrwałych zadań przy użyciu wzorca akcji sondowania](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Wykonywanie długotrwałych zadań przy użyciu wzorca akcji elementu webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Łączniki niestandardowe w aplikacjach logiki platformy Azure](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Tworzenie rozwiązań typu "business-to-business" (B2B)

Rozwiązania do integracji przedsiębiorstw i bezproblemowa komunikacja między organizacjami umożliwiają tworzenie zautomatyzowanych skalowalnych przepływów pracy dla tych scenariuszy przy użyciu pakietu Enterprise Integration Pack (EIP) z aplikacjami Azure Logic Apps. Chociaż organizacje używają różnych protokołów i formatów, mogą wymieniać wiadomości drogą elektroniczną. PROTOKÓŁ EIP przekształca różne formaty w format, który systemy organizacji mogą przetwarzać i obsługuje protokoły standardowe branżowe, w tym AS2, X12, EDIFACT i RosettaNet. Aby utworzyć te rozwiązania, należy utworzyć konto integracji, który jest oddzielnym zasobem platformy Azure, który zapewnia bezpieczny, skalowalny i łatwe do zarządzania kontener dla artefaktów, które można zdefiniować i używać z przepływów pracy aplikacji logiki. Na przykład artefakty obejmują partnerów handlowych, umowy, mapy, schematy, certyfikaty i konfiguracje wsadowe.

* [Omówienie: Rozwiązania do integracji przedsiębiorstw B2B z aplikacjami Azure Logic Apps i pakietem integracji dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Tworzenie kont integracji dla integracji przedsiębiorstw B2B w usłudze Azure Logic Apps i zarządzanie nimi](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Dostęp do zasobów sieci wirtualnej platformy Azure

Czasami aplikacje logiki i kont integracji potrzebują dostępu do zabezpieczonych zasobów, takich jak maszyny wirtualne (maszyny wirtualne) i innych systemów lub usług, które znajdują się w sieci wirtualnej platformy Azure. Aby skonfigurować ten dostęp, można utworzyć środowisko usługi integracji (ISE), w którym można tworzyć i uruchamiać aplikacje logiki. ISE jest prywatnym i izolowanym wystąpieniem usługi Logic Apps, która używa dedykowanych zasobów, takich jak magazyn, i jest uruchamiana oddzielnie od publicznej, "globalnej", usługi aplikacji logiki z wieloma dzierżawami. Oddzielenie izolowanego wystąpienia prywatnego i publiczne wystąpienie globalne pomaga również zmniejszyć wpływ innych dzierżaw platformy Azure na wydajność aplikacji, który jest również znany jako efekt "hałaśliwych sąsiadów".

* [Omówienie: Dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Łączenie się z sieciami wirtualnymi platformy Azure za pomocą aplikacji Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Wdrażanie aplikacji logiki, zarządzanie nimi i monitorowanie ich

Można w pełni tworzyć i wdrażać aplikacje logiki za pomocą programu Visual Studio, Azure DevOps lub innych narzędzi kontroli źródła i zautomatyzowanych narzędzi kompilacji. Aby obsługiwać wdrażanie przepływów pracy i połączeń zależnych w szablonie zasobu, aplikacje logiki używają szablonów wdrażania zasobów platformy Azure. Narzędzia programu Visual Studio automatycznie generują te szablony, które można zaewidencjonować do kontroli źródła dla przechowywania wersji. W przypadku dzienników powiadomień i diagnostyki stanu przebiegu pracy usługa Azure Logic Apps udostępnia również monitorowanie i alerty.

### <a name="deploy"></a>Wdrożenie

* [Szybki start: tworzenie zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu aplikacji Azure Logic Apps — Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Omówienie: Automatyzacja wdrażania aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Tworzenie szablonów usługi Azure Resource Manager w celu automatyzacji wdrażania aplikacji logiki azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Wdrażanie szablonów usługi Azure Resource Manager dla aplikacji logiki azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Przykład: Łączenie się z kolejkami usługi Azure Service Bus z aplikacji logiki azure i wdrażanie za pomocą potoków platformy Azure w usłudze Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Łączenie się z kontami usługi Azure Storage z usługi Azure Logic Apps i wdrażanie za pomocą potoków platformy Azure w usłudze Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Konfigurowanie akcji aplikacji funkcji dla usługi Azure Logic Apps i wdrażanie za pomocą potoków platformy Azure w usłudze Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Połącz się z kontem integracji z usługi Azure Logic Apps i wdrażaj za pomocą potoków platformy Azure w usłudze Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Przykład: organizowanie potoków platformy Azure przy użyciu aplikacji logiki azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Zarządzaj

* [Zarządzanie aplikacjami logiki przy użyciu programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Tworzenie kont integracyjnych i zarządzanie nimi dla integracji przedsiębiorstw B2B](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Zarządzanie środowiskiem usługi integracji (ISE) w usłudze Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitorowanie

* [Monitorowanie stanu uruchamiania, przeglądanie historii wyzwalaczy i konfigurowanie alertów dla aplikacji Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dla aplikacji logiki azure](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dla komunikatów B2B w usłudze Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Wyświetlanie i tworzenie zapytań do monitorowania i śledzenia w dziennikach usługi Azure Monitor dla aplikacji logiki Azure](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Obsługa typów zawartości, konwersji i przekształceń

Można uzyskiwać dostęp, konwertować i przekształcać wiele typów zawartości przy użyciu wielu funkcji w [języku definicji przepływu pracy](https://aka.ms/logicappsdocs)usługi Azure Logic Apps. Na przykład można konwertować między ciągiem, JSON `@json()` `@xml()` i XML z wyrażeniami i przepływem pracy. Aparat aplikacji logiki zachowuje typy zawartości do obsługi transferu zawartości w sposób bezstratny między usługami.

* [Obsługa typów zawartości w usłudze Azure Logic Apps](../logic-apps/logic-apps-content-type.md), takich jak `application/`, `application/octet-stream`i ,`multipart/formdata`
* [Przewodnik dotyczący używania funkcji w wyrażeniach dla aplikacji Azure Logic Apps i Power Automate](../logic-apps/workflow-definition-language-functions-reference.md)
* [Schemat języka definicji przepływu pracy dla aplikacji logiki azure](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Inne integracje i możliwości

Usługa Azure Logic Apps integruje się z wieloma usługami, takimi jak usługi Azure Functions, usługa Azure API Management, usługa Azure App Service i niestandardowe punkty końcowe HTTP, na przykład REST i SOAP.

* [Wywoływanie funkcji platformy Azure z aplikacji logiki platformy Azure](../logic-apps/logic-apps-azure-functions.md)
* [Samouczek: wywoływanie lub wyzwalanie aplikacji logiki przy użyciu usług Azure Functions i usługi Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Samouczek: tworzenie pulpitu nawigacyjnego informacji o klientach przesyłania strumieniowego za pomocą aplikacji logiki azure i usług Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Samouczek: Tworzenie funkcji, która integruje się z usługami Azure Logic Apps i usługami Azure Cognitive Services w celu analizowania tonacji postów na Twitterze](../azure-functions/functions-twitter-email.md)
* [Samouczek: tworzenie pulpitu nawigacyjnego społecznościowego opartego na sztucznej inteligencji przy użyciu usługi Power BI i aplikacji Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Samouczek: Monitorowanie zmian na maszynie wirtualnej przy użyciu usługi Azure Event Grid i aplikacji logiki](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Samouczek: zdalne monitorowanie IoT i powiadomienia za pomocą aplikacji Logika Azure łącząca centrum IoT i skrzynkę pocztową](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Wywoływanie usług SOAP przy użyciu aplikacji Azure Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Kompleksowe scenariusze

* [Oficjalny dokument: kompleksowa integracja z zarządzaniem sprawami z usługami platformy Azure, takimi jak aplikacje logiki](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Historie klientów

Dowiedz się, jak usługa Azure Logic Apps, wraz z innymi usługami platformy Azure i produktami firmy Microsoft, pomogła [tym firmom](https://aka.ms/logic-apps-customer-stories) poprawić ich elastyczność i skupić się na swoich podstawowych firmach, upraszczając, organizując, automatyzując i organizując złożone procesy.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [łącznikach aplikacji logiki](../connectors/apis-list.md)
* Dowiedz się więcej o [scenariuszach integracji przedsiębiorstwa B2B za pomocą usługi Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
