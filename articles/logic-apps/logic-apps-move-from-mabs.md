---
title: Migrowanie aplikacji z usług BizTalk do aplikacji logiki Azure
description: Jak przenieść aplikacje i rozwiązania z usług Microsoft Azure BizTalk Services (MABS) do aplikacji Logika Azure
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 97399635399c12022006ac95e60c5828bf2a9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905439"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migrowanie aplikacji i rozwiązań z usług BizTalk do aplikacji Logika Azure

Usługi Microsoft Azure BizTalk Services (MABS) przechodzi na emeryturę. Aby przenieść rozwiązania integracji MABS do [usługi Azure Logic Apps,](../logic-apps/logic-apps-overview.md)postępuj zgodnie ze wskazówkami zawartymi w tym artykule. 

## <a name="introduction"></a>Wprowadzenie

Usługi BizTalk składają się z dwóch podusług:

* Połączenia hybrydowe usług Microsoft BizTalk Services
* Integracja mostowa EAI i EDI

[Połączenia hybrydowe usługi Azure App Service zastępują](../app-service/app-service-hybrid-connections.md) połączenia hybrydowe usług BizTalk. Usługi Azure Hybrid Connections jest dostępna w usłudze Azure App Service za pośrednictwem witryny Azure portal. Ta usługa udostępnia Menedżera połączeń hybrydowych, dzięki czemu można zarządzać istniejącymi połączeniami hybrydowymi usług BizTalk Services, a także nowymi połączeniami hybrydowymi tworzonymi w portalu. 

[Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje integrację opartą na mostkach EAI i EDI z tymi samymi możliwościami w usługach BizTalk i nie tylko. Ta usługa zapewnia oparte na chmurze funkcje przepływu pracy i aranżacji oparte na zużyciu, aby szybko i łatwo tworzyć złożone rozwiązania integracji za pośrednictwem przeglądarki lub programu Visual Studio.

W tej tabeli mapuje możliwości usług BizTalk na aplikacje logiki.

| Usługi BizTalk   | Logic Apps            | Przeznaczenie                      |
| ------------------ | --------------------- | ---------------------------- |
| Łącznik          | Łącznik             | Wysyłanie i odbieranie danych   |
| Bridge             | Aplikacja logiki             | Procesor potoku           |
| Sprawdzanie poprawności etapu     | Akcja sprawdzania poprawności XML | Sprawdzanie poprawności dokumentu XML względem schematu | 
| Wzbogać scenę       | Tokeny danych           | Podwyższanie właściwości w wiadomości lub w przypadku decyzji dotyczących routingu |
| Etap przekształcania    | Akcja przekształcania      | Konwertowanie wiadomości XML z jednego formatu na inny |
| Etap dekodowania       | Akcja dekodowania płaskiego pliku | Konwertowanie z pliku płaskiego na XML |
| Zakodowanie etapu       | Akcja kodowania płaskiego pliku | Konwertowanie z pliku XML na płaski |
| Inspektor wiadomości  | Usługi azure lub aplikacje interfejsu API | Uruchamianie kodu niestandardowego w integracji |
| Akcja marszruty       | Warunek lub przełącznik | Rozsyłanie wiadomości do jednego z określonych łączników |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefakty usług BizTalk

Usługi BizTalk ma kilka rodzajów artefaktów. 

## <a name="connectors"></a>Łączniki

Łączniki usług BizTalk ułatwiają mostki wysyłania i odbierania danych, w tym mostków dwukierunkowych, które umożliwiają interakcje żądania/odpowiedzi oparte na protokezie HTTP. Logic Apps używa tej samej terminologii i ma setki łączników, które służą temu samejej celowi, łącząc się z szeroką gamą technologii i usług. Na przykład łączniki są dostępne dla usług SaaS i PaaS w chmurze, takich jak OneDrive, Office365, Dynamics CRM i innych, a także systemów lokalnych za pośrednictwem lokalnej bramy danych, która zastępuje usługę kart BizTalk dla usług BizTalk. Źródła w usługach BizTalk są ograniczone do subskrypcji FTP, SFTP i Service Bus lub Topic.

![](media/logic-apps-move-from-mabs/sources.png)

Domyślnie każdy mostek ma punkt końcowy HTTP, który jest skonfigurowany z adresu wykonawczego i względny adres właściwości mostka. Aby osiągnąć te same wyniki za pomocą aplikacji logiki, należy użyć akcji [Żądanie i odpowiedź.](../connectors/connectors-native-reqres.md)

## <a name="xml-processing-and-bridges"></a>Przetwarzanie XML i mostki

W usługach BizTalk most jest analogiczny do potoku przetwarzania. Mostek może odbierać dane odebrane z łącznika, wykonywać niektóre prace z danymi i wysyłać wyniki do innego systemu. Logic Apps robi to samo, obsługując te same wzorce interakcji oparte na potoku jako usługi BizTalk, a także zapewniając inne wzorce integracji. [Mostka żądań i odpowiedzi XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) w usługach BizTalk jest znany jako potok VETER, który składa się z etapów, które wykonują następujące zadania:

* (V) Walidacja poprawność
* (E) Wzbogać
* (T) Transformacja
* (E) Wzbogać
* (R) Trasa

Ten obraz pokazuje, jak przetwarzanie jest podzielone między żądanie i odpowiedź, co zapewnia kontrolę nad żądaniem i ścieżki odpowiedzi oddzielnie, na przykład przy użyciu różnych map dla każdej ścieżki:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Ponadto mostek jednokierunkowy XML dodaje etapy dekodowania i kodowania na początku i na końcu przetwarzania. Most przeładowywalny zawiera jeden etap Wzbogacania.

### <a name="message-processing-decoding-and-encoding"></a>Przetwarzanie, dekodowanie i kodowanie wiadomości

W usługach BizTalk można odbierać różne typy komunikatów XML i określać pasujący schemat odebranej wiadomości. Ta praca jest wykonywana na etapie *Typy wiadomości* potoku przetwarzania odbierania. Etap dekodowania następnie używa wykrytego typu wiadomości do dekodowania wiadomości przy użyciu dostarczonego schematu. Jeśli schemat jest schematem pliku płaskiego, ten etap konwertuje przychodzący plik płaski na XML. 

Logic Apps zapewnia podobne możliwości. Otrzymujesz płaski plik na różnych protokołach przy użyciu różnych wyzwalaczy łączników (system plików, FTP, HTTP itd.) i użyj akcji [Dekodowanie plików płaskich](../logic-apps/logic-apps-enterprise-integration-flatfile.md) do konwersji danych przychodzących na format XML. Istniejące schematy plików płaskich można przenieść bezpośrednio do aplikacji logiki bez żadnych zmian, a następnie przekazać schematy do konta integracji.

### <a name="validation"></a>Sprawdzanie poprawności

Po przekonwertowaniu danych przychodzących na xml (lub jeśli został odebrany format wiadomości) sprawdzanie poprawności jest uruchamiane w celu ustalenia, czy wiadomość jest zgodna ze schematem XSD. Aby wykonać to zadanie w aplikacjach logiki, należy użyć akcji [sprawdzania poprawności XML.](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) Można użyć tych samych schematów z usług BizTalk bez żadnych zmian.

### <a name="transform-messages"></a>Przekształcanie wiadomości

W usługach BizTalk etap przekształcania konwertuje jeden format wiadomości oparty na języku XML na inny. Ta praca jest wykonywana przez zastosowanie mapy przy użyciu mapera opartego na TRFM. W aplikacji logiki proces jest podobny. Akcja Przekształcanie wykonuje mapę z konta integracji. Główną różnicą jest to, że mapy w aplikacjach logiki są w formacie XSLT. XSLT zawiera możliwość ponownego użycia istniejących XSLT już masz, w tym mapy utworzone dla serwera BizTalk, które zawierają functoids. 

### <a name="routing-rules"></a>Reguły routingu

Usługi BizTalk podejmuje decyzję routingu, na którym punkcie końcowym lub łącznika do wysyłania wiadomości przychodzących lub danych. Możliwość wyboru ze wstępnie skonfigurowanych punktów końcowych jest możliwa przy użyciu opcji filtru routingu:

![](media/logic-apps-move-from-mabs/route-filter.png)

W usługach BizTalk, jeśli istnieją tylko dwie opcje, użycie *warunku* jest najlepszym sposobem konwertowania filtrów routingu w usługach BizTalk. Jeśli jest ich więcej niż dwa, użyj **przełącznika**.

Logic Apps zapewnia zaawansowane funkcje logiczne oraz zaawansowany przepływ sterowania i routing z [instrukcjami warunkowymi](../logic-apps/logic-apps-control-flow-conditional-statement.md) i [instrukcjami przełącznika.](../logic-apps/logic-apps-control-flow-switch-statement.md)

### <a name="enrich"></a>Wzbogacić

W przetwarzaniu usług BizTalk etap Wzbogacanie dodaje właściwości do kontekstu wiadomości skojarzonego z otrzymanymi danymi. Na przykład promowanie właściwości do użycia do routingu z wyszukiwania bazy danych lub przez wyodrębnianie wartości przy użyciu wyrażenia XPath. Logic Apps zapewnia dostęp do wszystkich danych kontekstowych danych wyjściowych z poprzednich akcji, dzięki czemu replikowanie tego samego zachowania jest proste. Na przykład za `Get Row` pomocą akcji połączenia SQL, zwracasz dane z bazy danych programu SQL Server i używać danych w akcji Decyzji do routingu. Podobnie właściwości przychodzących komunikatów usługi Service Bus w kolejce przez wyzwalacz są adresowalne, a także XPath przy użyciu wyrażenia języka definicji przepływu pracy xpath.

### <a name="run-custom-code"></a>Uruchamianie kodu niestandardowego

Usługi BizTalk umożliwia [uruchamianie kodu niestandardowego,](https://msdn.microsoft.com/library/azure/dn232389.aspx) który jest przekazyny we własnych zestawach. Ta funkcja jest implementowana przez interfejs [IMessageInspector.](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) Każdy etap w mostku zawiera dwie właściwości (On Enter Inspector i On Exit Inspector), które zapewniają utworzony typ .NET, który implementuje ten interfejs. Kod niestandardowy umożliwia wykonywanie bardziej złożonych przetwarzania danych i umożliwia ponowne użycie istniejącego kodu w zestawach, które wykonują wspólną logikę biznesową. 

Aplikacje logiki udostępnia dwa podstawowe sposoby wykonywania kodu niestandardowego: Usługi Azure i aplikacje interfejsu API. Usługi Azure Functions można tworzyć i wywoływać z aplikacji logiki. Zobacz [Dodawanie i uruchamianie kodu niestandardowego dla aplikacji logiki za pośrednictwem usługi Azure Functions](../logic-apps/logic-apps-azure-functions.md). Użyj aplikacji interfejsu API, część usługi Azure App Service, aby utworzyć własne wyzwalacze i akcje. Dowiedz się więcej o [tworzeniu niestandardowego interfejsu API do użycia z aplikacjami logiki](../logic-apps/logic-apps-create-api-app.md). 

Jeśli masz niestandardowy kod w zestawach, które można wywołać z usług BizTalk, można przenieść ten kod do usługi Azure Functions lub utworzyć niestandardowe interfejsy API z aplikacjami interfejsu API, w zależności od tego, co implementujesz. Na przykład jeśli masz kod, który otacza inną usługę, dla której aplikacje logiki nie ma łącznika, a następnie utworzyć aplikację interfejsu API i używać akcji aplikacji interfejsu API zapewnia w aplikacji logiki. Jeśli masz funkcje pomocnicze lub biblioteki, usługi Azure Functions jest prawdopodobnie najlepiej dopasować.

### <a name="edi-processing-and-trading-partner-management"></a>Przetwarzanie EDI i zarządzanie partnerami handlowymi

Usługi BizTalk i aplikacje logiczne obejmują przetwarzanie EDI i B2B z obsługą AS2 (Instrukcja stosowania 2), X12 i EDIFACT. W usługach BizTalk tworzysz mosty EDI i tworzysz partnerów handlowych i umowy lub zarządzasz nimi w dedykowanym portalu śledzenia i zarządzania.
W aplikacji logiki tę funkcję można uzyskać za pośrednictwem [pakietu Enterprise Integration Pack (EIP).](../logic-apps/logic-apps-enterprise-integration-overview.md) EIP zapewnia [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i działania B2B dla przetwarzania EDI i B2B. Konto integracji służy również do tworzenia [partnerów handlowych](../logic-apps/logic-apps-enterprise-integration-partners.md) i [umów](../logic-apps/logic-apps-enterprise-integration-agreements.md)oraz zarządzania nimi. Po utworzeniu konta integracji można połączyć jedną lub więcej aplikacji logiki z kontem. Następnie można użyć akcji B2B, aby uzyskać dostęp do informacji o partnerach handlowych z aplikacji logiki. Dostępne są następujące działania:

* As2 Kodowanie
* Dekodowanie AS2
* Kodowanie X12
* Dekodowanie X12
* Kodowanie EDIFACT
* Dekodowanie EDIFACT

W przeciwieństwie do usług BizTalk, te akcje są oddzielone od protokołów transportu. Dlatego podczas tworzenia aplikacji logiki, masz większą elastyczność, w których łączniki, które są używane do wysyłania i odbierania danych. Na przykład można odbierać pliki X12 jako załączniki z wiadomości e-mail, a następnie przetwarzać te pliki w aplikacji logiki. 

## <a name="manage-and-monitor"></a>Zarządzanie i monitorowanie

W usługach BizTalk dedykowany portal udostępniał funkcje śledzenia do monitorowania i rozwiązywania problemów. Logic Apps zapewnia bogatsze funkcje śledzenia i monitorowania do [monitorowania aplikacji logiki w witrynie Azure portal](../logic-apps/monitor-logic-apps.md)i zawiera aplikację mobilną, aby mieć oko na rzeczy, gdy jesteś w podróży.

## <a name="high-availability"></a>Wysoka dostępność

Aby uzyskać wysoką dostępność (HA) w usługach BizTalk, można udostępnić obciążenie przetwarzania przy użyciu więcej niż jednego wystąpienia w określonym regionie. Logic Apps zapewnia w regionie HA bez dodatkowych kosztów. 

W usługach BizTalk odzyskiwanie po awarii poza regionem w przypadku przetwarzania B2B wymaga procesu tworzenia kopii zapasowych i przywracania. Aby uzyskać ciągłość działania, aplikacje logiki zapewniają międzyregionową funkcję aktywnej/pasywnej [odzyskiwania po awarii,](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)która umożliwia synchronizowanie danych B2B między kontami integracji w różnych regionach.

## <a name="next-steps"></a>Następne kroki

* [Co to jest usługa Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) lub szybko rozpocząć pracę przy użyciu [wstępnie utworzonego szablonu](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Wyświetlanie wszystkich dostępnych łączników,](../connectors/apis-list.md) których można używać w aplikacjach logiki