---
title: Przykłady i scenariusze typowe — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Przykłady, scenariusze, samouczki i przewodniki dla usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 01/31/2018
ms.openlocfilehash: 89e0294db3178cedd3b14aada0b505787b17c75e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303693"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Typowe scenariusze, przykłady, samouczki i przewodniki dla usługi Azure Logic Apps

[Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) pomaga organizować i integrować różne usługi, zapewniając [ponad 100 gotowych do użycia łączników](../connectors/apis-list.md)— od programu SQL Server w środowisku lokalnym lub SAP do usług Microsoft Cognitive Services. Usługa Logic Apps jest "bezserwerowych", więc nie trzeba już martwić się o skalowanie lub wystąpienia. To wszystko, co należy zrobić, zdefiniuj przepływ pracy przy użyciu wyzwalaczy i akcji wykonywanych przez przepływ pracy. Podstawowej platformy obsługuje skalowanie, dostępnością i wydajnością. Usługa Logic Apps jest szczególnie przydatne w przypadku przypadków użycia i scenariuszy, w których trzeba koordynować wiele akcji w wielu systemach.

Aby dowiedzieć się więcej o wielu wzorców i możliwości, [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) obsługuje, poniżej przedstawiono typowe przykłady i scenariusze.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Popularne punktami startowymi dla przepływów pracy aplikacji logiki

Każda aplikacja logiki rozpoczyna się od [ *wyzwalacza*](../logic-apps/logic-apps-overview.md#logic-app-concepts)i tylko jeden wyzwalacz, uruchamia przepływ pracy aplikacji logiki, która powoduje przekazanie wszelkich danych jako część tego wyzwalacza. Niektóre łączniki udostępniają wyzwalacze, które są oferowane w rodzaju:

* *Wyzwalacze sondowania*: Regularnie sprawdza, czy punkt końcowy usługi dla nowych danych. Jeśli istnieją nowe dane, wyzwalacz tworzy i uruchamia nowe wystąpienie przepływu pracy z danymi wejściowymi.

* *Wyzwalacze wypychania*: Nasłuchuje danych punktu końcowego usługi, a następnie czeka, aż określonego zdarzenia. Po wystąpieniu zdarzenia wyzwalacza natychmiast, tworzenie i uruchamianie nowego wystąpienia przepływu pracy, która wykorzystuje wszystkie dostępne dane jako dane wejściowe.

Poniżej przedstawiono kilka przykładów popularny wyzwalacz:

* Sondowania: 

  * [**Harmonogram — cyklicznie** wyzwalacza](../connectors/connectors-native-recurrence.md) umożliwia ustawienie daty rozpoczęcia i czasu oraz cyklu dla uruchamiania aplikacji logiki. 
  Można na przykład wybierz dni tygodnia i pory dnia, służącą do wyzwalania aplikacji logiki.

  * Wyzwalacz "Po odebraniu wiadomości e-mail" umożliwia aplikacji logiki na sprawdzanie dla nowych wiadomości e-mail od dowolnego dostawcy poczty, która jest obsługiwana przez usługę Logic Apps, na przykład [usługi Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [ Outlook.com](https://docs.microsoft.com/connectors/outlook/)i tak dalej.

  * [ **HTTP** wyzwalacza](../connectors/connectors-native-http.md) umożliwia aplikacji logiki na sprawdzanie określonego punktu końcowego, komunikując się za pośrednictwem protokołu HTTP.
  
* Wypchnij:

  * [ **Żądanie / odpowiedź — żądanie** wyzwalacza](../connectors/connectors-native-reqres.md) umożliwia aplikacji logiki odbierania żądań HTTP i odpowiedzi w czasie rzeczywistym na zdarzenia w jakiś sposób.

  * [ **Element Webhook protokołu HTTP** wyzwalacza](../connectors/connectors-native-webhook.md) subskrybuje punktu końcowego usługi, rejestrując *adresów URL wywołania zwrotnego* przy użyciu tej usługi. 
  W ten sposób usługa tylko powiadamiać wyzwalacz po wystąpieniu określonego zdarzenia, aby wyzwalacz nie musi sondować usługę.

Po otrzymaniu powiadomienia o nowych danych lub zdarzenia, wyzwalacz jest uruchamiany, tworzy nowe wystąpienie przepływu pracy aplikacji logiki i uruchamia akcje przepływu pracy. Możesz uzyskać dostęp wszystkie dane z wyzwalacza w przepływie pracy. Na przykład wyzwalacz "na nowy tweet" przekazuje treść tweetu do uruchamiania aplikacji logiki. 

## <a name="respond-to-triggers-and-extend-actions"></a>Odpowiadania na wyzwalacze i akcje rozszerzyć

Dla systemów i usług, które może nie zostały opublikowane łączniki można także rozszerzyć aplikacje logiki.

* [Tworzenie niestandardowych wyzwalaczy i akcji](../logic-apps/logic-apps-create-api-app.md)
* [Konfigurowanie długo trwające akcje dla przebiegów przepływu pracy](../logic-apps/logic-apps-create-api-app.md)
* [Odpowiadanie na zdarzenia zewnętrzne i akcji przy użyciu elementów webhook](../logic-apps/logic-apps-create-api-app.md)
* [Wywoływanie, wyzwalacza lub zagnieżdżanie przepływami pracy przy użyciu synchronicznej odpowiedzi na żądania HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Samouczek: Bazujących na sztucznej Inteligencji społecznościowego pulpitu nawigacyjnego w zaledwie kilka minut za pomocą aplikacji logiki i usługą Power BI](https://aka.ms/logicappsdemo)
* [Wideo: Odpowiadanie na wiadomości SMS usługi Twilio, elementy webhook i czy wysłać odpowiedź tekstu](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Sterowanie przepływem, obsługa błędów i możliwości rejestrowania zdarzeń

Aplikacje logiki zawierają wiele możliwości do przepływu sterowania zaawansowanych, takich jak warunki, przełączniki, pętli i zakresy. Aby zapewnić odporność rozwiązania, możesz również wdrożyć błędów i obsługa wyjątków w swoich przepływach pracy. Powiadomienia i dzienniki diagnostyczne na potrzeby przepływu pracy stan uruchomienia usługi Azure Logic Apps zawiera także monitorowanie i generowanie alertów.

* Wykonują różne akcje na podstawie [instrukcje warunkowe](../logic-apps/logic-apps-control-flow-conditional-statement.md) i [instrukcje switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Powtórz kroki lub elementów proces w tablicach i kolekcjach z pętli](../logic-apps/logic-apps-control-flow-loops.md)
* [Akcje grupy wraz z zakresami](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Autor błędów i obsługa wyjątków w przepływie pracy](../logic-apps/logic-apps-exception-handling.md)
* [Przypadek użycia: Jak firmy ochrony zdrowia mogły używa obsługi dla przepływów pracy HL7 FHIR następującą liczbę wyjątków aplikacji logiki](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Włącz monitorowanie, rejestrowanie i alerty dla istniejących aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Włączanie monitorowania i rejestrowania diagnostycznego, podczas tworzenia aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Wdrażanie i zarządzanie aplikacjami logiki

Można w pełni tworzenia i wdrażania aplikacji logiki za pomocą programu Visual Studio, DevOps platformy Azure lub innych kontroli źródła i narzędzi automatycznej kompilacji. Do obsługi wdrożenia dla przepływów pracy i połączeń zależnych w szablonie zasobu, aplikacje logiki korzystanie z szablonów wdrażania zasobów platformy Azure. Narzędzia programu Visual Studio automatycznie generować tych szablonów, które można sprawdzić kontroli źródła pod kątem przechowywania wersji.

* [Tworzenie i wdrażanie aplikacji logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Włącz monitorowanie, rejestrowanie i alerty dla istniejących aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Tworzenie szablonów automatycznego wdrażania](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Typy zawartości, konwersji i przekształceń w przebiegu

Masz dostęp, konwersji i przekształcić wiele typów zawartości za pomocą wielu funkcji w usłudze Azure Logic Apps [język definicji przepływów pracy](https://aka.ms/logicappsdocs). Na przykład, można przekonwertować ciągu, JSON i XML przy użyciu `@json()` i `@xml()` wyrażeń przepływu pracy. Aparat usługi Logic Apps zachowuje typy zawartości do obsługi transferu zawartości w sposób bezstratne między usługami.

* [Jak działają wyrażenia przepływu pracy w usłudze logic apps](../logic-apps/logic-apps-author-definitions.md)
* [Obsługa typów zawartości-JSON](../logic-apps/logic-apps-content-type.md), takiej jak `application/xml`, `application/octet-stream`, i `multipart/formdata`
* [Schemat języka definicji przepływów pracy dla usługi Azure Logic Apps](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Inne integracje i możliwości

Funkcja Logic apps oferuje również integrację z wieloma usługami, takie jak usługi Azure Functions, Azure API Management, Azure App Services i niestandardowych punktów końcowych HTTP, na przykład, REST i SOAP.

* [Tworzenie w czasie rzeczywistym społecznościowego pulpitu nawigacyjnego za pomocą platformy Azure bez użycia serwera](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Wywołanie usługi Azure Functions z aplikacji logiki](../logic-apps/logic-apps-azure-functions.md)
* [Samouczek: Wyzwalacz aplikacji logiki za pomocą usługi Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Samouczek: Monitorowanie zmian maszyn wirtualnych za pomocą usługi Azure Event Grid i Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Samouczek: Tworzenie funkcji integrującej się z usługą Azure Logic Apps i Microsoft Cognitive Services do analizowania opinii wpisu w serwisie Twitter](../azure-functions/functions-twitter-email.md)
* [Samouczek: Zdalne monitorowanie IoT i powiadomień za pomocą usługi Azure Logic Apps, łącząc usługę IoT hub i skrzynki pocztowej](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Wywoływanie punktów końcowych protokołu SOAP z aplikacji logiki](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Kompleksowe scenariusze

* [Oficjalny dokument: Integracja zarządzania przypadkami end-to-end z usługami platformy Azure, takich jak Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Historie klientów

Dowiedz się, jak usługi Azure Logic Apps, wraz z innymi usługami platformy Azure i produktów firmy Microsoft pomogły [tych firm](https://aka.ms/logic-apps-customer-stories) zwiększyć swoją elastyczność i skoncentrować się na swoich podstawowych zadaniach biznesowych, upraszczając, organizowanie, automatyzowanie i organizowanie złożone procesy.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie definicji aplikacji logiki za pomocą kodu JSON](../logic-apps/logic-apps-author-definitions.md)
* [Obsługa błędów i wyjątków w usłudze logic apps](../logic-apps/logic-apps-exception-handling.md)
* [Przesyłanie komentarzy, pytania, opinię lub sugestie dotyczące poprawy usługi Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)
