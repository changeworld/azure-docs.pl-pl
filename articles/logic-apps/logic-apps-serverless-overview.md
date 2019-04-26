---
title: Omówienie platformy Azure bez użycia serwera | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat tworzenia zaawansowanych rozwiązań w chmurze bez konieczności martwienia się o infrastrukturę
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 9cd1946d8fa670764bcc95b82298d07cc70417bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684112"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Omówienie: Azure bez użycia serwera za pomocą usługi Azure Logic Apps i Azure Functions

[Bez użycia serwera](https://azure.microsoft.com/solutions/serverless/) aplikacje mają zalety zwiększenie szybkości rozwoju, zmniejszenie kod wymagany i uproszczenia skalę.  W tym artykule przechodzi w stan różnych atrybutów w rozwiązaniach nieużywających serwera i oferty bezserwerowe platformy Azure.

## <a name="what-is-serverless"></a>Co to jest bez użycia serwera?

Aplikacje niewymagające użycia serwera nie oznacza brak serwerów — po prostu oznacza to, że deweloper nie ma już martwić się o serwery.  Dużą część tworzenia tradycyjnych aplikacji jest udzielenie odpowiedzi na pytania dotyczące skalowania, hostowania go i monitorowanie rozwiązań, aby spełniać wymagania aplikacji.  Przy użyciu Bezserwerowej te pytania są wykonywane jako część rozwiązania.  Ponadto aplikacji niewymagających użycia serwera są rozliczane w ramach planu na podstawie użycia.  Jeśli aplikacja nigdy nie jest używany, opłaty nigdy nie są naliczane.  Te funkcje umożliwiają deweloperom skoncentrowanie się wyłącznie na logice biznesowej rozwiązania.

Podstawowych usług na platformie Azure w całym aplikacje niewymagające użycia serwera są [usługi Azure Functions](https://azure.microsoft.com/services/functions/) i [usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Oba te rozwiązania postępuj zgodnie z powyższym zasad i umożliwia deweloperom tworzenie aplikacji w chmurze niezawodne za pomocą minimalnej ilości kodu.

## <a name="what-are-azure-functions"></a>Co to są usługi Azure Functions?

Azure Functions to rozwiązanie umożliwiające łatwe uruchamianie małych fragmentów kodu („funkcji”) w chmurze. Możesz napisać tylko kod rozwiązujący aktualny problem, nie martwiąc się o całą aplikację ani infrastrukturę do jej uruchomienia. Functions programowanie może być jeszcze wydajniejsze i można użyć z wybranym języka programowania, takich jak C#, F#, Node.js, Python lub PHP. Płać tylko za czas, jaki kod jest wykonywany i platformy Azure jest skalowana zgodnie z potrzebami.

Jeśli chcesz od razu rozpocząć korzystanie z usługi Azure Functions, skorzystaj z artykułu [Tworzenie pierwszej funkcji platformy Azure](../azure-functions/functions-create-first-azure-function.md). Jeśli chcesz uzyskać informacje techniczne o usłudze Functions, zobacz [dokumentację dla deweloperów](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Co to są usługi Azure Logic Apps?

Usługa Azure Logic Apps umożliwia uproszczenie i wdrożyć skalowalne integracje i przepływy pracy w chmurze. Zapewnia Projektant wizualny pozwala modelować i automatyzować swój proces jako szereg kroków o nazwie przepływu pracy.  Istnieją [wiele łączników](../connectors/apis-list.md) w usługach w chmurze i lokalnych do szybkiego łączenia z aplikacji bez użycia serwera do innych interfejsów API.  Aplikacja logiki rozpoczyna się od wyzwalacza (na przykład „Gdy konto zostanie dodane do oprogramowania Dynamics CRM”), po uruchomieniu którego można użyć wielu akcji łączenia, konwersji i logiki warunkowej.  Usługa Logic Apps jest doskonałym wyborem w przypadku organizowanie różnych funkcji platformy Azure w ramach procesu — szczególnie w przypadku, gdy proces wymaga interakcji z systemu zewnętrznego lub interfejsu API.

Aby rozpocząć pracę z usługą Logic Apps, zacznij od [: tworzenie pierwszej aplikacji logiki](quickstart-create-first-logic-app-workflow.md).  Jeśli chcesz uzyskać więcej informacji technicznych o usłudze Logic Apps, zobacz [dokumentacja dla deweloperów](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Jak tworzyć i wdrażać aplikacje bezserwerowe na platformie Azure?

System Azure oferuje bogaty zestaw narzędzi w całym programowania, wdrażania i zarządzania aplikacji bez użycia serwera.  Aplikacje mogą być wbudowane bezpośrednio w witrynie Azure portal lub za pomocą [narzędzi w programie Visual Studio](logic-apps-serverless-get-started-vs.md).  Gdy aplikacja została utworzona może być [natychmiast wdrożyć](logic-apps-create-deploy-template.md).  System Azure oferuje również monitorowanie aplikacji bez użycia serwera.  Monitorowanie są dostępne w witrynie Azure portal, za pomocą zestawów SDK lub interfejsu API lub za pomocą zintegrowanych narzędzi do dzienników usługi Azure Monitor i usługi Application Insights.

## <a name="next-steps"></a>Kolejne kroki

* [Rozpocznij tworzenie aplikacji Bezserwerowej w programie Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Utwórz pulpit nawigacyjny szczegółowych informacji klientów przy użyciu Bezserwerowej](logic-apps-scenario-social-serverless.md)
* [Tworzenie szablonu wdrożenia dla aplikacji logiki](logic-apps-create-deploy-template.md)
