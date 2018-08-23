---
title: Integracja dla przedsiębiorstw dla modelu B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie przepływów pracy dla B2B i obsługują scenariusze integracji przedsiębiorstwa dla usługi logic apps z pakietem integracyjnym dla przedsiębiorstw
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
ms.openlocfilehash: c4f83d2bf082b5f1358b4eee6468d470b5cb8088
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054995"
---
# <a name="overview-b2b-scenarios-and-communication-with-the-enterprise-integration-pack"></a>Omówienie: Scenariuszy B2B i komunikacji z pakietem integracyjnym dla przedsiębiorstw

Dla przepływów pracy business-to-business (B2B) i bezproblemową komunikację z usługą Azure Logic Apps aby umożliwić scenariuszy integracji przedsiębiorstwa, rozwiązanie firmy Microsoft oparte na chmurze pakiet integracyjny dla przedsiębiorstw. Organizacje mogą wymieniać komunikaty elektronicznie, nawet jeśli używają różnych protokołów i formatów. Ten pakiet przekształca różnych formatach w formacie, który może być interpretowane i przetwarzane przez systemy w organizacji. Organizacje mogą wymieniać komunikaty za pośrednictwem standardowych protokołów, w tym [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), i [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Można również Zabezpieczanie komunikatów za pomocą szyfrowania i podpisów cyfrowych.

Osoby zaznajomione z programu BizTalk Server lub usługa Microsoft Azure BizTalk Services funkcje integracji dla przedsiębiorstw są łatwe w użyciu, ponieważ większość pojęcia są podobne. Co główna różnica polega na tym, że integracja dla przedsiębiorstw używa konta integracji do ułatwienie magazynowania i zarządzania artefaktów używany w komunikacji B2B. 

Po architektonicznie pakiet integracyjny dla przedsiębiorstw opiera się na "konta integracji". Te konta są oparte na chmurze kontenerów, które można przechowywać wszystkich artefaktów, takich jak schematy, partnerów, certyfikaty, mapy i umowy. Te artefakty służy do projektowania, wdrażania i konserwacji aplikacji B2B oraz tworzenie przepływów pracy dla B2B dla usługi logic apps. Jednak zanim będzie można użyć tych artefaktów, najpierw trzeba połączyć swoje konto integracji do aplikacji logiki. Po tym aplikacja logiki dostęp artefaktów konta integracji.

## <a name="why-should-you-use-enterprise-integration"></a>Dlaczego należy używać integracji dla przedsiębiorstw?

* Dzięki integracji przedsiębiorstwa można przechowywać Twoimi artefaktami w jednym miejscu — na koncie integracji.
* Można tworzyć przepływy pracy, B2B i integracja z innych firm oprogramowanie jako usługa (SaaS) aplikacji, aplikacje lokalne i aplikacje niestandardowe przy użyciu aparatu usługi Azure Logic Apps i wszystkich jego łączników.
* Możesz utworzyć niestandardowy kod dla aplikacji logiki za pomocą usługi Azure functions.

## <a name="how-to-get-started-with-enterprise-integration"></a>Jak rozpocząć pracę z usługą enterprise integration?

Można tworzyć i zarządzać nimi aplikacje B2B z pakietem integracyjnym dla przedsiębiorstw przy użyciu projektanta aplikacji logiki w **witryny Azure portal**. Można również zarządzać aplikacjami logiki w programie [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp "Logic apps PowerShell").

Poniżej przedstawiono ogólne kroki, które należy wykonać przed utworzeniem aplikacji w witrynie Azure portal:

![Obraz poglądowy](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Jakie są niektóre typowe scenariusze?

Integracja w przedsiębiorstwie obsługuje te standardy branżowe:

* EDI - elektronicznej wymiany danych
* EAI — integracja aplikacji przedsiębiorstwa

## <a name="heres-what-you-need-to-get-started"></a>Oto, co jest potrzebne rozpocząć pracę

* Subskrypcja platformy Azure przy użyciu konta integracji
* Program Visual Studio 2015 do utworzenia mapy i schematy
* [Integracji dla przedsiębiorstw aplikacji logiki — Microsoft Azure Tools dla programu Visual Studio 2015 w wersji 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Wypróbuj teraz

[Wdrażanie wysyłania AS2 próbki w pełni funkcjonalne i otrzymywać aplikacji logiki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) używającą funkcji B2B usługi Azure Logic Apps.

## <a name="learn-more"></a>Dowiedz się więcej
* [Umowy dotyczące](../logic-apps/logic-apps-enterprise-integration-agreements.md "Dowiedz się więcej o umowach dotyczących integracji przedsiębiorstw")
* [Firmami (B2B) scenariuszy](../logic-apps/logic-apps-enterprise-integration-b2b.md "Dowiedz się, jak tworzyć aplikacje logiki za pomocą funkcji B2B ")  
* [Certyfikaty](logic-apps-enterprise-integration-certificates.md "Dowiedz się więcej na temat certyfikatów integracji przedsiębiorstw")
* [Płaskie Kodowanie/dekodowanie pliku](logic-apps-enterprise-integration-flatfile.md "Dowiedz się, jak kodować i dekodować zawartość pliku prostego")  
* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md "więcej informacji na temat konta integracji")
* [Mapuje](../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej na temat map integracji przedsiębiorstw")
* [Partnerzy](logic-apps-enterprise-integration-partners.md "więcej informacji na temat partnerów integracji przedsiębiorstw")
* [Schematy](logic-apps-enterprise-integration-schemas.md "Dowiedz się więcej na temat schematów integracji przedsiębiorstw")
* [Sprawdzanie poprawności kodu XML komunikat](logic-apps-enterprise-integration-xml.md "informacje o sposobie weryfikacji wiadomości XML za pomocą aplikacji logiki")
* [Przekształceń danych XML](logic-apps-enterprise-integration-transform.md "Dowiedz się więcej na temat map integracji przedsiębiorstw")
* [Łączniki integracji dla przedsiębiorstw](../connectors/apis-list.md "Dowiedz się więcej o pakiecie łączniki integracji dla przedsiębiorstw")
* [Metadane konta integracji](../logic-apps/logic-apps-enterprise-integration-metadata.md "więcej informacji na temat metadanych konta integracji")
* [Monitorowanie komunikatów B2B](logic-apps-monitor-b2b-message.md "Dowiedz się więcej o monitorowanie komunikatów B2B")
* [Śledzenie komunikatów B2B w portalu pakietu OMS](logic-apps-track-b2b-messages-omsportal.md "Dowiedz się więcej na temat śledzenia komunikatów B2B w portalu pakietu OMS")

