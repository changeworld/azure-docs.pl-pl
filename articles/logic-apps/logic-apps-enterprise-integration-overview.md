---
title: Integracja przedsiębiorstw B2B
description: Dowiedz się więcej o tworzeniu zautomatyzowanych przepływów pracy B2B dla integracji z przedsiębiorstwem przy użyciu aplikacji Azure Logic Apps i pakietu Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9356cecb22672ae15505d14b5ac73d5baeef09e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191370"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Rozwiązania do integracji przedsiębiorstw B2B z aplikacjami Azure Logic Apps i pakietem integracji dla przedsiębiorstw

W przypadku rozwiązań typu business-to-business (B2B) i bezproblemowej komunikacji między organizacjami można tworzyć zautomatyzowane skalowalne przepływy pracy integracji przedsiębiorstwa przy użyciu pakietu Enterprise Integration Pack (EIP) z [aplikacjami Azure Logic Apps.](../logic-apps/logic-apps-overview.md) Chociaż organizacje używają różnych protokołów i formatów, mogą wymieniać wiadomości drogą elektroniczną. EIP przekształca różne formaty w format, który systemy organizacji mogą przetwarzać i obsługuje protokoły standardowe branżowe, w tym [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)i [EDIFACT.](../logic-apps/logic-apps-enterprise-integration-edifact.md) Można również zwiększyć bezpieczeństwo wiadomości przy użyciu zarówno szyfrowania, jak i podpisów cyfrowych. EIP wspiera te [złącza integracyjne dla przedsiębiorstw](../connectors/apis-list.md#integration-account-connectors) i następujące standardy branżowe:

* Elektroniczna wymiana danych (EDI)
* Integracja aplikacji w przedsiębiorstwie (EAI)

Jeśli znasz program Microsoft BizTalk Server lub usługi Azure BizTalk Services, EIP jest zgodny z podobnymi pojęciami, dzięki czemu funkcje są łatwe w użyciu. Jednak jedną z głównych różnic jest to, że EPI jest architektonicznie opiera się na "kont integracji", aby uprościć przechowywanie i zarządzanie artefaktami używanymi w komunikacji B2B. Te konta są kontenerami opartymi na chmurze, które przechowują wszystkie artefakty, takie jak partnerzy, umowy, schematy, mapy i certyfikaty. 

## <a name="why-use-the-enterprise-integration-pack"></a>Dlaczego warto skorzystać z pakietu Enterprise Integration Pack?

* Dzięki EIP możesz przechowywać wszystkie swoje artefakty w jednym miejscu - koncie integracyjnym.

* Przepływy pracy B2B można tworzyć i integrować się z aplikacjami typu oprogramowanie jako usługa (SaaS), aplikacjami lokalnymi i aplikacjami niestandardowymi za pomocą aplikacji i łączników azure logic.

* Można utworzyć kod niestandardowy dla aplikacji logiki za pomocą funkcji platformy Azure.

## <a name="how-do-i-get-started"></a>Jak rozpocząć?

Przed rozpoczęciem tworzenia przepływów pracy aplikacji logiki B2B za pomocą EIP potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z artefaktami, których chcesz użyć

* Aby utworzyć mapy i schematy, można użyć [narzędzi integracji microsoft Azure Logic Apps Enterprise Integration Tools dla programu Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) i Visual Studio 2015.

Po utworzeniu konta integracji i dodać artefakty, można rozpocząć tworzenie przepływów pracy B2B z tych artefaktów, tworząc aplikację logiki w witrynie Azure portal. Jeśli jesteś nowy w aplikacjach logiki, spróbuj [utworzyć podstawową aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby pracować z tymi artefaktami, upewnij się, że najpierw połączyć konto integracji z aplikacją logiki. Po tym aplikacji logiki można uzyskać dostęp do konta integracji. Aplikacje logiki można również tworzyć, zarządzać nimi i wdrażać je za pomocą programu Visual Studio lub [programu PowerShell.](https://docs.microsoft.com/powershell/module/az.logicapp)

Oto kroki wysokiego poziomu, aby rozpocząć tworzenie aplikacji logiki B2B:

![Wymagania wstępne dotyczące tworzenia aplikacji logiki B2B](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Wypróbuj teraz

[Wdrażanie w pełni operacyjnej przykładowej aplikacji logiki, która wysyła i odbiera komunikaty AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie partnerów handlowych](logic-apps-enterprise-integration-partners.md)
* [Tworzenie umów](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Dodawanie schematów](logic-apps-enterprise-integration-schemas.md)
* [Dodawanie map](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrowanie z usługi BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)
