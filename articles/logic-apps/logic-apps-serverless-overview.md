---
title: Omówienie usługi Azure Niekorzystającą | Dokumentacja firmy Microsoft
description: Tworzenie zaawansowanych rozwiązań w chmurze bez myśleć o infrastruktury.
keywords: ''
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 30c7dd36efca5ffcae770fe310a43a107d9136af
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Omówienie usługi Azure Niekorzystającą z funkcjami i Logic Apps

Niekorzystającą aplikacje mają zalety zwiększenie szybkości rozwoju, zmniejszenie kodu i prostota o skali.  W tym artykule przechodzi w stan różnych atrybutów pliki rozwiązań i niekorzystającą ofertami platformy Azure.

## <a name="what-is-serverless"></a>Co to jest niekorzystającą?

Pliki nie oznacza brak serwerów — wystarczy oznacza to, że deweloper nie trzeba się martwić o serwerów.  Dużą część projektowanie aplikacji tradycyjnych jest udzielenie odpowiedzi na pytania wokół skalowania, hosting i monitorowanie rozwiązań do spełnienia wymagań aplikacji.  Z Serverless te pytania są podejmowane obsługę w ramach rozwiązania.  Ponadto pliki aplikacji są rozliczane na podstawie zużycia planu.  Jeśli aplikacja nigdy nie jest używany, nigdy nie jest z naliczeniem opłat.  Te funkcje umożliwiają deweloperom skoncentrować się wyłącznie na logice biznesowej, rozwiązania.

Podstawowe usługi na platformie Azure wokół Serverless są [usługi Azure Functions](https://azure.microsoft.com/services/functions/) i [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Oba te rozwiązania zasadach powyżej i umożliwiają deweloperom tworzenie aplikacji niezawodne chmury z minimalnym kodu.

## <a name="what-are-azure-functions"></a>Co to są usługi Azure Functions?

Azure Functions to rozwiązanie umożliwiające łatwe uruchamianie małych fragmentów kodu („funkcji”) w chmurze. Możesz napisać tylko kod rozwiązujący aktualny problem, nie martwiąc się o całą aplikację ani infrastrukturę do jej uruchomienia. Funkcje programowanie może być jeszcze wydajniejsze i można użyć język programowania wyboru, takich jak C#, F #, Node.js, Python lub PHP. Płać tylko za czas działania kodu i Azure skaluje zgodnie z potrzebami.

Jeśli chcesz od razu rozpocząć korzystanie z usługi Azure Functions, skorzystaj z artykułu [Tworzenie pierwszej funkcji platformy Azure](../azure-functions/functions-create-first-azure-function.md). Jeśli chcesz uzyskać informacje techniczne o usłudze Functions, zobacz [dokumentację dla deweloperów](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Co to jest Azure Logic Apps?

Aplikacje logiki platformy Azure pozwala uprościć i wdrażanie skalowalnej integracji i przepływów pracy w chmurze. Zapewnia on wizualnego projektowania modelu i Automatyzowanie procesu jako serię kroków o nazwie przepływu pracy.  Brak [wiele łączników](../connectors/apis-list.md) w usługach w chmurze i lokalnych szybko nawiązać niekorzystającą aplikacji do innych interfejsów API.  Aplikacja logiki rozpoczyna się od wyzwalacza (na przykład „Gdy konto zostanie dodane do oprogramowania Dynamics CRM”), po uruchomieniu którego można użyć wielu akcji łączenia, konwersji i logiki warunkowej.  Logic Apps jest doskonałym wyborem w przypadku organizowanie różnych funkcji Azure w ramach procesu — szczególnie w przypadku, gdy proces wymaga interakcji z systemu zewnętrznego lub interfejsu API.

Aby rozpocząć pracę z usługą Logic Apps, Rozpocznij od [tworzenie pierwszej aplikacji logiki](quickstart-create-first-logic-app-workflow.md).  Jeśli chcesz uzyskać więcej informacji technicznych dotyczących Logic Apps, zobacz [dokumentacja dla deweloperów](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Jak tworzenie i wdrażanie aplikacji Niekorzystającą na platformie Azure?

Platforma Azure oferuje bogaty zestaw narzędzi na projektowanie, wdrażanie i zarządzanie aplikacjami bez serwera.  Aplikacje mogą być wbudowane bezpośrednio w portalu Azure lub z [narzędzi z programu Visual Studio](logic-apps-serverless-get-started-vs.md).  Gdy aplikacja została utworzona może być [natychmiast wdrożyć](logic-apps-create-deploy-template.md).  Platforma Azure udostępnia również monitorowanie bez serwera aplikacji.  Monitorowania są dostępne w portalu Azure, za pośrednictwem interfejsu API lub zestawów SDK lub zintegrowane narzędzia do analizy dzienników i usługi Application Insights.

## <a name="next-steps"></a>Kolejne kroki

* [Rozpoczynanie pracy z tworzeniem Niekorzystającą aplikacji w programie Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Utwórz pulpit nawigacyjny szczegółowych informacji klienta z Serverless](logic-apps-scenario-social-serverless.md)
* [Tworzenie szablonu wdrożenia dla aplikacji logiki](logic-apps-create-deploy-template.md)