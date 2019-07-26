---
title: Przykłady & typowych scenariuszach — Azure Logic Apps | Microsoft Docs
description: Przykłady, scenariusze, samouczki i wskazówki dotyczące Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 01/31/2018
ms.openlocfilehash: 95eca4c7f3e8170f6559799fc4c706e95df70e9e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385508"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Typowe scenariusze, przykłady, samouczki i przewodniki dotyczące Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ułatwia organizację i integrację różnych usług, zapewniając [setki gotowych do użycia łączników](../connectors/apis-list.md), od lokalnych SQL Server lub SAP do platformy Azure Cognitive Services. Usługa Logic Apps jest "bezserwerowa", więc nie trzeba martwić się o skalowanie ani wystąpienia. Wystarczy zdefiniować przepływ pracy z wyzwalaczem i akcjami wykonywanymi przez przepływ pracy. Bazowa Platforma obsługuje skalowanie, dostępność i wydajność. Logic Apps jest szczególnie przydatna w przypadku przypadków użycia i scenariuszy, w których należy koordynować wiele akcji w wielu systemach.

Aby uzyskać więcej informacji na temat wielu wzorców i możliwości obsługiwanych przez [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , poniżej przedstawiono typowe przykłady i scenariusze.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Popularne punkty wyjścia dla przepływów pracy aplikacji logiki

Każda aplikacja logiki rozpoczyna się od [*wyzwalacza*](../logic-apps/logic-apps-overview.md#logic-app-concepts)i tylko jeden wyzwalacz, który uruchamia przepływ pracy aplikacji logiki i przekazuje dane w ramach tego wyzwalacza. Niektóre łączniki udostępniają wyzwalacze, które są dostępne w następujących typach:

* *Wyzwalacze sondowania*: Regularnie sprawdza punkt końcowy usługi pod kątem nowych danych. Gdy istnieją nowe dane, wyzwalacz tworzy i uruchamia nowe wystąpienie przepływu pracy z danymi jako dane wejściowe.

* *Wyzwalacze wypychania*: Nasłuchuje danych w punkcie końcowym usługi i czeka, aż wystąpi określone zdarzenie. Gdy wystąpi zdarzenie, wyzwalacz jest uruchamiany natychmiast, tworząc i uruchamiając nowe wystąpienie przepływu pracy, które używa dowolnych dostępnych danych jako danych wejściowych.

Oto kilka popularnych przykładów wyzwalacza:

* Sondowania 

  * [Wyzwalacz **harmonogram-cykl** ](../connectors/connectors-native-recurrence.md) pozwala ustawić datę i godzinę rozpoczęcia oraz cykl uruchamiania aplikacji logiki. 
  Na przykład możesz wybrać dni tygodnia i godziny, w których ma być wyzwalana aplikacja logiki.

  * Wyzwalacz "po odebraniu wiadomości e-mail" umożliwia aplikacji logiki Sprawdzanie nowej wiadomości e-mail od dowolnego dostawcy poczty obsługiwanego przez Logic Apps, na przykład [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/)i tak dalej.

  * Wyzwalacz [ **http** ](../connectors/connectors-native-http.md) umożliwia aplikacji logiki sprawdzanie określonego punktu końcowego usługi przez komunikację za pośrednictwem protokołu HTTP.
  
* Wydajności

  * Wyzwalacz [ **żądania/odpowiedzi-żądanie** ](../connectors/connectors-native-reqres.md) umożliwia aplikacji logiki odbieranie żądań HTTP i reagowanie w czasie rzeczywistym na zdarzenia w jakiś sposób.

  * Wyzwalacz [ **elementu webhook protokołu HTTP** ](../connectors/connectors-native-webhook.md) subskrybuje punkt końcowy usługi przez zarejestrowanie *adresu URL wywołania zwrotnego* przy użyciu tej usługi. 
  Dzięki temu usługa może po prostu powiadomić wyzwalacz, gdy wystąpi określone zdarzenie, aby wyzwalacz nie musiał sondować usługi.

Po odebraniu powiadomienia o nowych danych lub zdarzeniu wyzwalany jest wyzwalacz, tworzy nowe wystąpienie przepływu pracy aplikacji logiki i uruchamia akcje w przepływie pracy. Możesz uzyskać dostęp do dowolnych danych z wyzwalacza w przepływie pracy. Na przykład wyzwalacz "w nowym tweetie" przekazuje zawartość tweetu do przebiegu aplikacji logiki. 

## <a name="respond-to-triggers-and-extend-actions"></a>Odpowiedz na wyzwalacze i rozwiń akcje

W przypadku systemów i usług, które mogą nie mieć opublikowanych łączników, można także rozciągnąć Aplikacje logiki.

* [Tworzenie niestandardowych wyzwalaczy lub akcji](../logic-apps/logic-apps-create-api-app.md)
* [Konfigurowanie długotrwałych akcji dla przebiegów przepływu pracy](../logic-apps/logic-apps-create-api-app.md)
* [Reagowanie na zdarzenia zewnętrzne i akcje z elementami webhook](../logic-apps/logic-apps-create-api-app.md)
* [Wywoływanie, wyzwalanie lub zagnieżdżanie przepływów pracy z odpowiedziami synchronicznymi na żądania HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Samouczek: Tworzenie pulpitu nawigacyjnego społecznościowego opartego na systemie AI w kilka minut dzięki Logic Apps i Power BI](https://aka.ms/logicappsdemo)
* [Wideo: Odpowiadanie na elementy webhook programu SMS Twilio i wysyłanie odpowiedzi na tekst](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Przepływ sterowania, obsługa błędów i możliwości rejestrowania

Aplikacje logiki obejmują bogate możliwości dla zaawansowanego przepływu sterowania, takie jak warunki, przełączniki, pętle i zakresy. Aby zapewnić odporne rozwiązania, można również zaimplementować obsługę błędów i wyjątków w przepływach pracy. W przypadku dzienników powiadomień i diagnostyki dla stanu przebiegu przepływu pracy Azure Logic Apps udostępnia również monitorowanie i alerty.

* Wykonywanie różnych akcji na podstawie [instrukcji warunkowych](../logic-apps/logic-apps-control-flow-conditional-statement.md) i [instrukcji switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Powtórz kroki lub przetwórz elementy w tablicach i kolekcjach za pomocą pętli](../logic-apps/logic-apps-control-flow-loops.md)
* [Grupuj akcje razem z zakresami](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Tworzenie obsługi błędów i wyjątków w przepływie pracy](../logic-apps/logic-apps-exception-handling.md)
* [Przypadek użycia: Jak firma opieki zdrowotnej używa obsługi wyjątków aplikacji logiki dla przepływów pracy HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Włączanie monitorowania, rejestrowania i alertów dla istniejących aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Włącz monitorowanie i rejestrowanie diagnostyczne podczas tworzenia aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Wdrażanie aplikacji logiki i zarządzanie nimi

Możesz w pełni opracowywać i wdrażać aplikacje logiki za pomocą programu Visual Studio, platformy Azure DevOps lub dowolnej innej kontroli źródła i zautomatyzowanych narzędzi kompilacji. Aby obsługiwać wdrożenie dla przepływów pracy i połączeń zależnych w szablonie zasobu, Aplikacje logiki używają szablonów wdrażania zasobów platformy Azure. Narzędzia Visual Studio Tools automatycznie generują te szablony, które można zaewidencjonować do kontroli źródła w celu przechowywania wersji.

* [Tworzenie i wdrażanie aplikacji logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Włączanie monitorowania, rejestrowania i alertów dla istniejących aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Automatyzowanie wdrożenia aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Typy zawartości, konwersje i przekształcenia w ramach przebiegu

Można uzyskać dostęp do, konwertować i przetwarzać wiele typów zawartości za pomocą wielu funkcji w Azure Logic Apps [języku definicji przepływu pracy](https://aka.ms/logicappsdocs). Na przykład można dokonać konwersji między ciągami, JSON i XML za pomocą `@json()` wyrażeń i. `@xml()` Aparat Logic Apps zachowuje typy zawartości obsługujące transfer zawartości w sposób bezstratny między usługami.

* [Jak działają wyrażenia przepływu pracy w usłudze Logic Apps](../logic-apps/logic-apps-author-definitions.md)
* [Obsługa typów zawartości innych niż JSON](../logic-apps/logic-apps-content-type.md), takich `application/xml`jak `application/octet-stream`, i`multipart/formdata`
* [Schemat języka definicji przepływu pracy dla Azure Logic Apps](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Inne integracje i możliwości

Usługa Logic Apps oferuje także integrację z wieloma usługami, takimi jak Azure Functions, Azure API Management, Azure App Services i niestandardowe punkty końcowe HTTP, na przykład REST i SOAP.

* [Utwórz pulpit nawigacyjny społecznościowych w czasie rzeczywistym, korzystając z serwera platformy Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Wywołaj Azure Functions z usługi Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Samouczek: Wyzwalanie aplikacji logiki za pomocą Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Samouczek: Monitoruj zmiany maszyny wirtualnej przy użyciu Azure Event Grid i Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Samouczek: Tworzenie funkcji, która integruje się z usługami Azure Logic Apps i Azure Cognitive Services do analizowania tonacji post usługi Twitter](../azure-functions/functions-twitter-email.md)
* [Samouczek: Zdalne monitorowanie i powiadomienia w usłudze IoT przy użyciu Azure Logic Apps łączenia Centrum IoT i skrzynki pocztowej](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Wywoływanie punktów końcowych protokołu SOAP z usługi Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Kompleksowe scenariusze

* [Dokumentacji Kompleksowa integracja z zarządzaniem przypadkami z usługami platformy Azure, takimi jak Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Historie klientów

Dowiedz się, w jaki sposób Azure Logic Apps, wraz z innymi usługami platformy Azure i produktami firmy Microsoft, które [pomogły zwiększyć](https://aka.ms/logic-apps-customer-stories) elastyczność i skupić się na podstawowych firmach dzięki uproszczeniu, organizowaniu, automatyzowaniu i organizowania złożonych procesów.

## <a name="next-steps"></a>Kolejne kroki

* [Kompiluj w definicjach aplikacji logiki za pomocą formatu JSON](../logic-apps/logic-apps-author-definitions.md)
* [Obsługa błędów i wyjątków w usłudze Logic Apps](../logic-apps/logic-apps-exception-handling.md)
* [Prześlij swoje komentarze, pytania, opinie lub sugestie, aby usprawnić Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)
