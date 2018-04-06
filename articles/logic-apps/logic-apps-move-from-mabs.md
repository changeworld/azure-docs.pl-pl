---
title: Przenoszenie aplikacji z usługi BizTalk Services do usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Przenieś lub migrację usług BizTalk Azure (MABS) do usługi Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: jonfancey
manager: anneta
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: jonfan; LADocs
ms.openlocfilehash: f5ad003189d70216198f2d5c7719a971c16d5479
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="move-from-biztalk-services-to-azure-logic-apps"></a>Przenoszenie z usługi BizTalk Services do usługi Azure Logic Apps

Trwa wycofywanie Microsoft Azure BizTalk usług (MABS). Aby przenieść rozwiązań integracji MABS do [Azure Logic Apps](../logic-apps/logic-apps-overview.md), postępuj zgodnie ze wskazówkami w tym artykule. 

## <a name="introduction"></a>Wprowadzenie

Usługi BizTalk Services składa się z dwóch subservices:

* Połączeń hybrydowych usług Microsoft BizTalk
* Usługi EAI i EDI integracji na podstawie Mostek

[Azure App Service połączeń hybrydowych było możliwe](../app-service/app-service-hybrid-connections.md) zastępuje połączeń hybrydowych usługi BizTalk. Azure połączeń hybrydowych było możliwe jest dostępna w usłudze Azure App Service za pośrednictwem portalu Azure. Ta usługa udostępnia Menedżera połączeń hybrydowych, dzięki czemu można zarządzać istniejących połączeń hybrydowych usługi BizTalk Services, a także nowych połączeń hybrydowych utworzonych w portalu. 

[Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje EAI i EDI integracji na podstawie Mostek takie same możliwości usługi BizTalk Services i więcej. Ta usługa udostępnia skali chmury zużycie funkcje oparte na przepływie pracy i aranżacji do szybkiego i łatwego tworzenia rozwiązań integracji złożonych za pośrednictwem przeglądarki lub z programem Visual Studio.

Ta tabela mapuje możliwości usługi BizTalk Services Logic Apps.

| BizTalk Services   | Logic Apps            | Przeznaczenie                      |
| ------------------ | --------------------- | ---------------------------- |
| Łącznik          | Łącznik             | Wysyłanie i odbieranie danych   |
| Mostek             | Aplikacja logiki             | Procesor potoku           |
| Sprawdź poprawność etap     | Sprawdzanie poprawności kodu XML akcji | Sprawdzanie poprawności względem schematu dokumentu XML | 
| Dodawanie etapu       | Tokeny danych           | Podwyższ poziom właściwości do wiadomości lub decyzje dotyczące routingu |
| Przekształć etap    | Przekształć akcji      | Konwertowanie wiadomości XML z jednego formatu na inny |
| Dekodowanie etap       | Płaski akcji dekodowanie pliku | Konwertowanie pliku prostego na XML |
| Kodowanie etap       | Płaski akcji kodowanie pliku | Konwertowanie z pliku XML do pliku prostego |
| Inspektor wiadomości  | Środowisko Azure Functions lub aplikacje interfejsu API | Uruchamianie niestandardowego kodu w Twojej integracji |
| Akcja trasy       | Warunek lub przełącznika | Rozsyłanie wiadomości do jednej z określonych łączników |
|||| 

## <a name="biztalk-services-artifacts"></a>Artefakty usługi BizTalk Services

Usługi BizTalk Services ma kilka rodzajów artefaktów. 

## <a name="connectors"></a>Łączniki

Usługi BizTalk Services łączniki pomocy mostków wysyłania i odbierania danych, w tym mostków dwukierunkowe, umożliwiające interakcje oparte na protokole HTTP żądania/odpowiedzi. Logic Apps używa tego samego terminologii i ma 180 + łączniki, które służą do tego samego celów, łącząc się z wielu technologii i usług. Na przykład łączniki są dostępne dla chmury SaaS i usług, takich jak OneDrive, usługi Office 365, Dynamics CRM i inne, PaaS plus lokalnych systemów za pośrednictwem bramy danych lokalnego, która zastępuje usługę karty BizTalk dla usługi BizTalk Services. Źródeł w usługi BizTalk Services są ograniczone do subskrypcji FTP, SFTP i kolejką usługi Service Bus lub temat.

![](media/logic-apps-move-from-mabs/sources.png)

Domyślnie każdy Mostek ma punkt końcowy HTTP, który jest skonfigurowany z adresem środowiska uruchomieniowego i właściwości adres względny mostka. Aby uzyskać takie same wyniki z usługą Logic Apps, użyć [żądań i odpowiedzi](../connectors/connectors-native-reqres.md) akcje.

## <a name="xml-processing-and-bridges"></a>Przetwarzanie kodu XML i mostków

W usługach BizTalk mostka jest odpowiednikiem potoku przetwarzania. Mostek może zająć danych otrzymywanych z łącznika, czy niektóre pracować z danymi i wysyłać wyniki do innego systemu. Logic Apps działa identycznie Obsługa tego samego wzorców interakcji z potoku jako usługi BizTalk i zapewniając innymi wzorami integracji. [Mostek żądanie-odpowiedź XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) w usługi BizTalk Services nosi nazwę potoku VETER, która składa się z etapów służących do wykonywania tych zadań:

* V sprawdzania poprawności
* (E) Enrich
* (T) transform
* (E) Enrich
* (R) trasy

Ten obraz przedstawia sposób przetwarzania są dzielone na żądanie i odpowiedź, która zapewnia kontrolę nad żądaniem i ścieżkach odpowiedzi oddzielnie, na przykład za pomocą innego mapowania dla każdej ścieżki:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

XML jednokierunkowe Mostek dodaje również dekodowania i kodowanie na początku i końca przetwarzania. Mostek przekazywanego zawiera jeden etap Enrich.

### <a name="message-processing-decoding-and-encoding"></a>Przetwarzanie komunikatów, dekodowania i kodowania

W usługi BizTalk Services może odbierać różne typy komunikatów XML i określenia zgodnego schematu odebranej wiadomości. Tej pracy jest wykonywane w *typów komunikatów* etap potoku przetwarzania receive. Na etapie dekodowania jest używany typ wykryto komunikat zdekodować komunikatu przy użyciu podanego schematu. Jeśli schemat jest schematu pliku prostego, ten etap konwertuje przychodzące pliku prostego do pliku XML. 

Logic Apps oferuje podobne możliwości. Odbieranie pliku prostego za pośrednictwem różnych protokołów przy użyciu wyzwalaczy różnych łącznika (systemu plików, FTP, HTTP i tak dalej) i używać [płaskiej dekodowanie pliku](../logic-apps/logic-apps-enterprise-integration-flatfile.md) akcji, aby przekonwertować przychodzących danych XML. Można przenieść z istniejących schematów pliku prostego bezpośrednio do aplikacji logiki bez wprowadzania żadnych zmian, a następnie przekaż schematy do swojego konta integracji.

### <a name="validation"></a>Walidacja

Po przekonwertowaniu przychodzących danych XML (lub jeśli XML został odebrany format komunikatu), sprawdzanie poprawności uruchamia ustalenie, czy komunikat odpowiada schematu XSD. Aby wykonać to zadanie w aplikacjach logiki, użyj [sprawdzanie poprawności kodu XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) akcji. Możesz użyć tego samego schematów z usługi BizTalk Services bez wprowadzania żadnych zmian.

### <a name="transform-messages"></a>Przekształć wiadomości

W usługi BizTalk Services etapu przekształcenia konwertuje jeden format komunikatu opartych na języku XML do innego. Ta operacja jest wykonywana, stosując mapę, za pomocą TRFM na podstawie mapowania. W aplikacjach logiki proces jest podobny. Akcja przekształcenia wykonuje mapy z Twojego konta integracji. Główna różnica polega na tym, że mapy w aplikacjach logiki są w formacie XSLT. XSLT obejmuje możliwość ponownego użycia istniejącej XSLT już istnieje, w tym mapy utworzone dla serwera BizTalk, zawierające functoids. 

### <a name="routing-rules"></a>Reguły routingu

Usługi BizTalk Services sprawia, że decyzje dotyczące routingu na których punkt końcowy lub łącznika do wysyłania wiadomości przychodzących lub danych. Możliwe, za pomocą opcji filtr routingu jest możliwość wybrania z wstępnie skonfigurowane punkty końcowe:

![](media/logic-apps-move-from-mabs/route-filter.png)

Usługi BizTalk, jeśli dostępne są tylko dwie opcje, z *warunku* to najlepszy sposób konwertowania filtrów routingu w usługi BizTalk Services. Jeśli istnieje więcej niż dwa, użyj **przełącznika**.

Logic Apps oferuje możliwości zaawansowaną logikę plus przepływu sterowania zaawansowane i routingu z [warunkowe instrukcje](../logic-apps/logic-apps-control-flow-conditional-statement.md) i [przełącznika instrukcje](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Dodawanie

Podczas przetwarzania usługi BizTalk Services etap Enrich dodaje właściwości do kontekst komunikat skojarzony z odebrane dane. Na przykład podwyższenie poziomu właściwości do użycia na potrzeby routingu z wyszukiwania w bazie danych lub wyodrębniając wartość za pomocą wyrażenia XPath. Logic Apps umożliwia dostęp do wszystkie dane kontekstowe dane wyjściowe z poprzedzających akcje, ułatwiając prostego do replikowania takie samo zachowanie. Na przykład za pomocą `Get Row` akcji połączenia SQL, zwróć dane z bazy danych programu SQL Server i używania danych w ramach akcji decyzji dla routingu. Podobnie, właściwości przychodzące usługi Service Bus wiadomości w kolejce przez wyzwalacz są adresowanego, a także XPath przy użyciu wyrażenia języka definicji przepływu pracy xpath.

### <a name="run-custom-code"></a>Uruchom kod niestandardowy

Usługi BizTalk Services umożliwia [uruchomienia niestandardowego kodu](https://msdn.microsoft.com/library/azure/dn232389.aspx) który jest przekazywany w własne zestawy. Ta funkcja jest implementowany przez [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) interfejsu. Na każdym z etapów mostka obejmuje dwie właściwości (na inspektora wprowadź i na inspektora zakończenia) zapewniające typ architektury .NET, utworzony, który implementuje ten interfejs. Kod niestandardowy umożliwia bardziej złożonych przetwarzania danych i umożliwia ponowne używanie istniejącego kodu w zestawach, wykonujących wspólnej logiki biznesowej. 

Logic Apps oferuje dwa podstawowe sposoby wykonania kodu niestandardowego: usługi Azure Functions i aplikacji API Apps. Azure Functions można utworzyć i wywoływać z aplikacji logiki. Zobacz [Dodaj i wykonywania kodu niestandardowego dla usługi logic apps za pomocą usługi Azure Functions](../logic-apps/logic-apps-azure-functions.md). Aplikacje interfejsu API, część usługi Azure App Service umożliwia tworzenie własnych wyzwalacze i akcje. Dowiedz się więcej o [Tworzenie niestandardowego interfejsu API do użycia z usługą Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Jeśli masz kod niestandardowy w zestawy, które można wywoływać z usługi BizTalk Services należy Przenieś ten kod do usługi Azure Functions lub tworzenie niestandardowych interfejsów API przy użyciu aplikacji interfejsu API, w zależności od tego, co to jest wdrożenie. Na przykład jeśli masz kod, który opakowuje innej usługi, dla którego Logic Apps nie łącznik, a następnie utwórz aplikację interfejsu API i akcje aplikacji interfejsu API udostępnia w ramach aplikacji logiki. Jeśli masz funkcje pomocnicze lub bibliotek usługi Azure Functions wówczas prawdopodobnie najlepszego dopasowania.

### <a name="edi-processing-and-trading-partner-management"></a>EDI przetwarzania i Zarządzanie partnerami handlowymi

Usługi BizTalk Services i Logic Apps obejmują przetwarzanie EDI i B2B z obsługą AS2 (zastosowania instrukcji 2), X12 i EDIFACT. W usługach BizTalk Twojej Tworzenie mostków EDI i tworzyć ani nimi zarządzać handlowym partnerów i umów w dedykowanym portalu zarządzania i śledzenia.
W aplikacjach logiki uzyskać tę funkcję za pomocą [pakiet integracji przedsiębiorstwa (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). Udostępnia element EIP [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i akcje B2B EDI i B2B przetwarzania. Konta integracji również użyć do tworzenia i zarządzania [partnerami handlowymi](../logic-apps/logic-apps-enterprise-integration-partners.md) i [umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md). Po utworzeniu konta integracji usługa logic apps można połączyć konto. Następnie można akcje B2B dostęp do informacji partnerem handlowym z aplikacji logiki. Dostępne są następujące akcje:

* Kodowanie AS2
* Dekodowanie AS2
* Kodowanie X12
* Dekodowanie X12
* Kodowanie EDIFACT
* Dekodowanie EDIFACT

W przeciwieństwie do usługi BizTalk Services te akcje są całkowicie niezależna od protokołów transportu. Dlatego podczas tworzenia aplikacji logiki, w które łączniki uzyskuje się większą elastyczność służącego do wysyłania i odbierania danych. Na przykład można otrzymywać X12 pliki jako załączniki poczty e-mail, a następnie proces tych plików w aplikacji logiki. 

## <a name="manage-and-monitor"></a>Monitorowanie i zarządzanie nim

W usługi BizTalk Services dedykowanego portalu przedstawione możliwości śledzenia do monitorowania i rozwiązywania problemów. Logic Apps oferuje bardziej rozbudowane śledzenie i monitorowanie możliwości za pomocą [portalu Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md)i zawiera aplikację mobilną dla śledzeniu na rzeczy podczas pracy w ruchu.

## <a name="high-availability"></a>Wysoka dostępność

Wysokiej dostępności (HA) w usługach BizTalk można udostępniać obciążenia przy użyciu więcej niż jedno wystąpienie w określonym regionie. Logic Apps oferuje wysokiej dostępności w regionie bez ponoszenia dodatkowych kosztów. 

Usługi BizTalk Services odzyskiwania po awarii poza regionem przetwarzania B2B wymaga procesu tworzenia kopii zapasowej i przywracania. Ciągłość biznesowa Logic Apps oferuje region między aktywny/pasywny [możliwości DR](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), co umożliwia synchronizację danych B2B konta integracji w różnych regionach.

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) lub szybko rozpocząć pracę przy użyciu [wstępnie utworzonego szablonu](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Wyświetl wszystkie dostępne łączniki](../connectors/apis-list.md) używanego w aplikacji logiki