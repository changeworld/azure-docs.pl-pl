---
title: Integracji przedsiębiorstwa dla B2B — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Tworzenie przepływów pracy B2B i obsługuje scenariusze integracji przedsiębiorstwa dla usługi logic apps z pakiet integracyjny dla przedsiębiorstw
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 25349d48d88b64568fef0f5a6bac5950da15196b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298641"
---
# <a name="overview-b2b-scenarios-and-communication-with-the-enterprise-integration-pack"></a>Omówienie: Scenariusze B2B i komunikacji z pakiet integracyjny dla przedsiębiorstw

Bezproblemowe komunikacji z usługą Azure Logic Apps i przepływy pracy między firmami (B2B) można włączyć scenariuszy integracji przedsiębiorstwa z rozwiązania opartego na chmurze firmy Microsoft, pakiet integracyjny dla przedsiębiorstw. Organizacje mogą wymieniać komunikaty elektronicznie, nawet jeśli używają różnych protokołów i formaty. Pakiet przekształca różnych formatach w formacie, który systemów organizacji mogą interpretować i przetwarzać. Organizacje mogą wymieniać komunikaty za pośrednictwem standardowych protokołów, w tym [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), i [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Ponadto można zabezpieczyć komunikatów przy użyciu szyfrowania i podpisy cyfrowe.

Jeśli znasz BizTalk Server lub usług Microsoft Azure BizTalk, funkcje integracji przedsiębiorstwa są łatwe w użyciu, ponieważ większość pojęcia są podobne. Jeden główna różnica polega na tym, że integracji przedsiębiorstwa używa konta integracji uprościć magazynu i zarządzania używane w komunikacji B2B artefaktów. 

Pod względem architektury pakiet integracyjny dla przedsiębiorstw opiera się na "konta integracji". Te konta są oparte na chmurze kontenerów, które przechowują wszystkie Twoje artefaktów jak schematów, partnerów, certyfikaty, map i umów. Te artefakty służy do projektowania, wdrażania i konserwacji aplikacji B2B, a także do tworzenia przepływów pracy B2B dla usługi logic apps. Jednak przed użyciem tych artefaktów, należy najpierw połączyć konta integracji aplikacji logiki. Po wykonaniu tej aplikacji logiki mają dostęp do konta integracji artefaktów.

## <a name="why-should-you-use-enterprise-integration"></a>Dlaczego należy używać integracji przedsiębiorstwa?

* Dzięki integracji przedsiębiorstwa wszystkie artefakty użytkownika można przechowywać w jednym miejscu — konta integracji.
* Możesz skompilować B2B przepływów pracy i zintegrować z innych firm oprogramowanie jako usługa (SaaS) aplikacji, aplikacji lokalnych i aplikacji niestandardowych przy użyciu aparatu Azure Logic Apps i wszystkich jego łączników.
* Można utworzyć niestandardowego kodu dla aplikacji logiki z funkcjami platformy Azure.

## <a name="how-to-get-started-with-enterprise-integration"></a>Jak rozpocząć pracę z integracji przedsiębiorstwa?

Możesz skompilować i zarządzanie aplikacjami B2B w usłudze pakiet integracyjny dla przedsiębiorstw przy użyciu projektanta aplikacji logiki w **portalu Azure**. Można również zarządzać z aplikacji logiki [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Logic apps tematy PowerShell").

Poniżej przedstawiono ogólne kroki, które należy wykonać przed przystąpieniem do tworzenia aplikacji w portalu Azure:

![Obraz — omówienie](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Co to są niektórych typowych scenariuszy?

Integracja Enterprise obsługuje te standardy branżowe:

* EDI - elektronicznej wymiany danych
* Usługi EAI - integracji aplikacji dla przedsiębiorstw

## <a name="heres-what-you-need-to-get-started"></a>Oto wymagane do rozpoczęcia pracy

* Subskrypcja platformy Azure przy użyciu konta integracji
* Visual Studio 2015 do utworzenia mapy i schematy
* [Integracji przedsiębiorstwa aplikacje logiki platformy Microsoft Azure Tools dla programu Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Wypróbuj teraz

[Wdrażanie wysyłania AS2 próbki pełnej funkcjonalności i odbierać aplikacji logiki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) używającą funkcje B2B dla usługi Azure Logic Apps.

## <a name="learn-more"></a>Dowiedz się więcej
* [Umów](../logic-apps/logic-apps-enterprise-integration-agreements.md "więcej informacji na temat umowy integracji dla przedsiębiorstw")
* [Business-to-Business scenariusze B2B](../logic-apps/logic-apps-enterprise-integration-b2b.md "informacje o sposobie tworzenia aplikacji logiki z funkcjami B2B ")  
* [Certyfikaty](logic-apps-enterprise-integration-certificates.md "Dowiedz się więcej o certyfikatach integracji przedsiębiorstwa")
* [Płaskie pliku kodowania/dekodowania](logic-apps-enterprise-integration-flatfile.md "sposób kodowania i dekodowania zawartość pliku prostego")  
* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md "Dowiedz się więcej na temat integracji kont")
* [Mapuje](../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej na temat map integracji przedsiębiorstwa")
* [Partnerzy](logic-apps-enterprise-integration-partners.md "Dowiedz się więcej na temat partnerów integracji przedsiębiorstwa")
* [Schematy](logic-apps-enterprise-integration-schemas.md "Dowiedz się więcej na temat schematów integracji przedsiębiorstwa")
* [Sprawdzanie poprawności kodu XML wiadomości](logic-apps-enterprise-integration-xml.md "Dowiedz się, jak można sprawdzić poprawności wiadomości XML z usługą Logic apps")
* [Przekształcanie XML](logic-apps-enterprise-integration-transform.md "Dowiedz się więcej na temat map integracji przedsiębiorstwa")
* [Łączniki integracji dla przedsiębiorstw](../connectors/apis-list.md "Dowiedz się więcej o pakiecie łączniki integracji dla przedsiębiorstw")
* [Metadane konta integracji](../logic-apps/logic-apps-enterprise-integration-metadata.md "informacje o metadanych konta integracji")
* [Monitorowanie wiadomości B2B](logic-apps-monitor-b2b-message.md "Dowiedz się więcej na temat monitorowania wiadomości B2B")
* [Śledzenie wiadomości B2B w portalu OMS](logic-apps-track-b2b-messages-omsportal.md "Dowiedz się więcej o śledzenie wiadomości B2B w portalu OMS")

