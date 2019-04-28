---
title: Przenoszenie aplikacji z usługi BizTalk Services do usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Migrowanie z usługi Azure BizTalk Services (MABS) do usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: f813cb5d8d5c442fc17f126c3a2ff6de7b0bdde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61321251"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>Migrowanie z usługi BizTalk Services do usługi Azure Logic Apps

Trwa wycofywanie usługi Microsoft Azure BizTalk Services (MABS). Aby przenieść swoje rozwiązania integracji serwera usługi Mab do [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md), postępuj zgodnie ze wskazówkami w tym artykule. 

## <a name="introduction"></a>Wprowadzenie

Usługa BizTalk Services składa się z dwóch subservices:

* Microsoft BizTalk Services połączeń hybrydowych
* Integracja EAI i EDI bazującej na mostku

[Połączenia hybrydowe usługi Azure App Service](../app-service/app-service-hybrid-connections.md) zastępuje połączenia hybrydowe usługi BizTalk. Połączenia hybrydowe Azure jest dostępne w usłudze Azure App Service w witrynie Azure portal. Ta usługa zapewnia Menedżera połączeń hybrydowych, tak aby mogli zarządzać istniejące połączenia hybrydowe usługi BizTalk Services, a także nowe połączenia hybrydowe, które tworzysz w portalu. 

[Usługa Logic Apps](../logic-apps/logic-apps-overview.md) zamienia integracji EAI i EDI bazującej na mostku wszystkich tych samych możliwości usługi BizTalk Services i nie tylko. Ta usługa zapewnia skali chmury na podstawie użycia przepływu pracy i aranżacji funkcje umożliwiające szybkie i łatwe tworzenie rozwiązań integracji złożonych, za pośrednictwem przeglądarki lub za pomocą programu Visual Studio.

Ta tabela mapuje możliwości usługi BizTalk Services do usługi Logic Apps.

| BizTalk Services   | Logic Apps            | Przeznaczenie                      |
| ------------------ | --------------------- | ---------------------------- |
| Łącznik          | Łącznik             | Wysyłanie i odbieranie danych   |
| Mostek             | Aplikacja logiki             | Procesor potoku           |
| Sprawdź poprawność etapu     | Sprawdzanie poprawności kodu XML akcji | Sprawdź poprawność dokumentu XML ze schematem | 
| Wzbogacanie etapu       | Tokeny danych           | Podwyższanie poziomu właściwości do wiadomości lub decyzje w kwestii routingu |
| Przekształcanie etapu    | Przekształcanie akcji      | Konwertuj wiadomości XML z jednego formatu na inny |
| Dekodowanie etapu       | Akcja dekodowania pliku prostego | Konwertowanie z pliku prostego do pliku XML |
| Kodowanie etapu       | Akcja kodowania pliku prostego | Konwertowanie z pliku XML do pliku prostego |
| Inspektor wiadomości  | Usługa Azure Functions i API Apps | Uruchomiony niestandardowy kod w swojej integracji |
| Akcja trasy       | Warunek lub przełącznika | Routing komunikatów do jednego z określonym łączników |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefakty usługi BizTalk Services

Usługa BizTalk Services ma kilka rodzajów artefaktów. 

## <a name="connectors"></a>Łączniki

Łączniki usługi BizTalk Services pomagają mostków wysyłania i odbierania danych, w tym mostków dwukierunkowe, umożliwiające interakcje oparte na protokole HTTP żądania/odpowiedzi. Usługa Logic Apps używa tego samego terminologii i ma ponad 180 łączników, które służą do tych samych celów, łącząc się z szerokiej gamy technologii i usług. Na przykład łączniki są dostępne dla chmur SaaS i PaaS usług, takich jak OneDrive, Office 365, Dynamics CRM i uzyskać więcej informacji, a także lokalnych systemów za pośrednictwem lokalnej bramy danych, która zastępuje usługi BizTalk Adapter usługi BizTalk Services. Źródła w usłudze BizTalk Services są ograniczone do subskrypcji FTP, SFTP i kolejki usługi Service Bus lub temat.

![](media/logic-apps-move-from-mabs/sources.png)

Domyślnie każdy Mostek ma punkt końcowy HTTP, który jest skonfigurowany przy użyciu adresu środowiska uruchomieniowego i właściwości adresu względnego mostka. Aby osiągnąć takie same wyniki z usługą Logic Apps, należy użyć [żądań i odpowiedzi](../connectors/connectors-native-reqres.md) akcji.

## <a name="xml-processing-and-bridges"></a>Przetwarzanie XML i mostków

W usłudze BizTalk Services Most jest analogiczne do potoku przetwarzania. Mostek może potrwać dane otrzymane z łącznika, wykonaj niektóre pracować z danymi i wysyłać wyniki do innego systemu. Usługa Logic Apps działa tak samo obsługi tych samych wzorców oparty na potokach interakcji jako usługi BizTalk Services, a jednocześnie zapewniając inne wzorce integracji. [XML "żądanie-odpowiedź" Mostek](https://msdn.microsoft.com/library/azure/hh689781.aspx) w usłudze BizTalk Services jest znany jako potok VETER, który składa się z etapów, które wykonują następujące zadania:

* (V) weryfikowanie
* (E) wzbogacanie
* (T) przekształcenia
* (E) wzbogacanie
* (R) trasy

Ten rysunek pokazuje, jak przetwarzanie jest podzielony między żądania i odpowiedzi, który zapewnia kontrolę nad żądaniem i ścieżkach odpowiedzi oddzielnie, na przykład za pomocą różnych mapy dla każdej ścieżki:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

XML jednokierunkowe Mostek dodaje również dekodowanie i kodowanie na początku i końcu przetwarzania. Mostek przekazywanego zawiera jednego etapu Enrich.

### <a name="message-processing-decoding-and-encoding"></a>Przetwarzanie komunikatów, dekodowanie i kodowanie

W usłudze BizTalk Services można odbierać różne typy komunikatów XML i określić pasującego schematu dla odebrany komunikat. Ta praca jest wykonywana w *typy komunikatów* etapu potoku przetwarzania receive. Na etapie dekodowania jest używany typ komunikatu wykryte do dekodowania komunikatu przy użyciu podanego schematu. Jeśli schemat jest schematu pliku prostego, ten etap konwertuje przychodzące pliku prostego do pliku XML. 

Usługa Logic Apps zapewnia podobne funkcje. Możesz odbierać pliku prostego za pośrednictwem różnych protokołów, przy użyciu wyzwalaczy inny łącznik (System plików, FTP, HTTP i tak dalej) i używać [dekodowania pliku prostego](../logic-apps/logic-apps-enterprise-integration-flatfile.md) działania w celu przekonwertowania przychodzące dane na dane XML. Można przenieść swoje istniejące płaskie schematy plików bezpośrednio do usługi Logic Apps bez konieczności wprowadzania zmian, a następnie przekaż schematy na koncie integracji.

### <a name="validation"></a>Walidacja

Po przekonwertowaniu przychodzących danych XML (lub jeśli XML został odebrany format wiadomości), sprawdzanie poprawności działa w celu ustalenia, jeśli komunikat jest zgodna schematu XSD. Aby wykonać to zadanie w usłudze Logic Apps, należy użyć [sprawdzanie poprawności kodu XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) akcji. Możesz użyć tego samego schematów z usługi BizTalk Services bez wprowadzania żadnych zmian.

### <a name="transform-messages"></a>Przekształcanie komunikatów

W usłudze BizTalk Services etapu przekształcenia konwertuje jeden format wiadomości oparty na formacie XML do drugiego. Ta operacja jest wykonywana przez zastosowanie mapy, przy użyciu mapowania na podstawie TRFM. W usłudze Logic Apps proces jest podobny. Działania przekształcania wykonuje mapę z Twojego konta integracji. Główna różnica polega na czy mapy w usłudze Logic Apps są w formacie XSLT. XSLT obejmuje możliwość ponownego użycia istniejącej XSLT już istnieje, łącznie z mapy utworzone za program BizTalk Server, które zawierają functoids. 

### <a name="routing-rules"></a>Reguły routingu

Usługa BizTalk Services sprawia, że decyzje dotyczące routingu w których punkt końcowy lub łącznik do wysyłania wiadomości przychodzących lub danych. Możliwość dokonania wyboru z wstępnie skonfigurowane punkty końcowe jest możliwe przy użyciu routingu opcji filtru:

![](media/logic-apps-move-from-mabs/route-filter.png)

W usłudze BizTalk Services przy użyciu w przypadku tylko dwie opcje *warunek* to najlepszy sposób konwertowania filtrów routingu w usłudze BizTalk Services. Jeśli istnieje więcej niż dwa, należy użyć **Przełącz**.

Usługa Logic Apps oferuje możliwości z zaawansowaną logikę oraz Zaawansowane sterowanie przepływem i routing za pomocą [instrukcje warunkowe](../logic-apps/logic-apps-control-flow-conditional-statement.md) i [instrukcje switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Wzbogacanie

Podczas przetwarzania usługi BizTalk Services na etapie Enrich dodaje właściwości do kontekst komunikatu skojarzonego z danymi odebrane. Na przykład promocji właściwości na potrzeby routingu z wyszukiwania w bazie danych lub dzięki możliwości wyodrębniania wartości za pomocą wyrażenia XPath. Usługa Logic Apps zapewnia dostęp do wszystkich danych kontekstowych danych wyjściowych z poprzednich akcji, dzięki czemu proste w celu replikowania tego samego zachowania. Na przykład za pomocą `Get Row` czynność połączenia SQL, zwróć dane z bazy danych programu SQL Server i użyć danych w ramach akcji decyzji dla routingu. Podobnie, przychodzące usługi Service Bus we właściwościach wiadomości w kolejce przez wyzwalacz są adresy, a także XPath przy użyciu wyrażenia języka definicji przepływu pracy xpath.

### <a name="run-custom-code"></a>Uruchamianie kodu niestandardowego

Usługa BizTalk Services umożliwia [uruchomiony niestandardowy kod](https://msdn.microsoft.com/library/azure/dn232389.aspx) który jest przekazywany w utworzonych przez użytkownika. Ta funkcja jest implementowany przez [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) interfejsu. Każdy z etapów mostka zawiera dwie właściwości (na inspektora wprowadź i na inspektora zakończenia), zapewniających typ architektury .NET, utworzone, który implementuje ten interfejs. Niestandardowy kod pozwala wykonać bardziej złożone przetwarzanie na dane i umożliwia ponowne używanie istniejącego kodu w zestawach, które wykonują typowe logiki biznesowej. 

Usługa Logic Apps oferuje dwa podstawowe sposoby wykonania niestandardowego kodu: Usługa Azure Functions i aplikacje API Apps. Usługa Azure Functions można tworzyć i wywoływać z aplikacji logiki. Zobacz [Dodaj i uruchamiania kodu niestandardowego dla usługi logic apps za pomocą usługi Azure Functions](../logic-apps/logic-apps-azure-functions.md). Użyj aplikacji interfejsu API, część usługi Azure App Service, aby utworzyć własne wyzwalacze i akcje. Dowiedz się więcej o [tworzenia niestandardowego interfejsu API do użycia z usługą Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Jeśli masz kod niestandardowy w zestawach, które wywołują z usługi BizTalk Services, możesz przenieść ten kod do usługi Azure Functions lub tworzenie niestandardowych interfejsów API dzięki funkcji API Apps w zależności od tego, co w przypadku wdrażania. Na przykład jeśli masz kod, który otacza innej usługi, dla którego Logic Apps nie już łącznik, a następnie tworzenie aplikacji interfejsu API i akcje aplikacji interfejsu API udostępnia w aplikacjach logiki. W przypadku funkcji pomocnika lub bibliotek, następnie usługi Azure Functions prawdopodobnie jest najlepszym rozwiązaniem.

### <a name="edi-processing-and-trading-partner-management"></a>EDI, przetwarzania i zarządzania partnerami handlowymi

Usługa BizTalk Services i Logic Apps obejmują przetwarzanie EDI i B2B z obsługą AS2 (zastosowania instrukcji 2) X12 i EDIFACT. W usłudze BizTalk Services usługi Tworzenie mostków integracji EDI i tworzyć ani nimi zarządzać umowami w specjalnym portalem zarządzania i śledzenia i partnerami handlowymi.
W usłudze Logic Apps Pobierz tę funkcję za pośrednictwem [pakietu integracji dla przedsiębiorstw (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). Udostępnia pakiet EIP [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i akcji B2B dla przetwarzanie EDI i B2B. Możesz również użyć konta integracji tworzenie i zarządzanie nimi [partnerami handlowymi](../logic-apps/logic-apps-enterprise-integration-partners.md) i [umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md). Po utworzeniu konta integracji, co najmniej jedną aplikację logiki można połączyć konta. Można następnie użyć akcji B2B dostępu do informacji o partnerze handlowym z aplikacji logiki. Dostępne są następujące akcje:

* Kodowanie AS2
* Dekodowanie AS2
* Kodowanie X12
* Dekodowanie X12
* Kodowanie EDIFACT
* Dekodowanie EDIFACT

W przeciwieństwie do usługi BizTalk Services te akcje są całkowicie niezależni od protokołów transportowych. Podczas tworzenia aplikacji usługi logic apps pozwala uzyskać większą elastyczność w łącznikach, których używasz, aby wysyłać i odbierać dane. Na przykład można otrzymywać X12 pliki jako załączniki z wiadomości e-mail, a następnie proces tych plików w aplikacji logiki. 

## <a name="manage-and-monitor"></a>Zarządzanie i monitorowanie

W usłudze BizTalk Services specjalnym portalem, pod warunkiem możliwości śledzenia w celu monitorowania i rozwiązywania problemów. Usługa Logic Apps umożliwia bogatszych śledzenia i monitorowania możliwości za pośrednictwem [witryny Azure portal](../logic-apps/logic-apps-monitor-your-logic-apps.md)i zawiera aplikację mobilną w celu śledzeniu rzeczami pracując mobilnie.

## <a name="high-availability"></a>Wysoka dostępność

Wysokiej dostępności (HA) w usłudze BizTalk Services możesz udostępniać obciążenie przetwarzania przy użyciu więcej niż jedno wystąpienie w określonym regionie. Usługa Logic Apps oferuje wysokiej dostępności w regionie, bez ponoszenia dodatkowych kosztów. 

W usłudze BizTalk Services odzyskiwanie po awarii poza regionem dla przetwarzanie B2B wymaga procesu tworzenia kopii zapasowych i przywracania. W celu zachowania ciągłości, Logic Apps oferuje między regionami aktywny/pasywny [możliwości odzyskiwania po awarii](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), który umożliwia synchronizowanie danych B2B dla kont integracji w różnych regionach.

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) lub szybko rozpocząć pracę przy użyciu [wstępnie utworzonego szablonu](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Wyświetlanie wszystkich dostępnych łączników](../connectors/apis-list.md) używanego w usłudze logic apps