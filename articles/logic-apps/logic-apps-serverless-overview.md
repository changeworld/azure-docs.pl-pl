---
title: Omówienie — usługa Azure Serverless dla aplikacji i rozwiązań opartych na chmurze
description: Dowiedz się, jak tworzyć aplikacje i rozwiązania oparte na chmurze bez martwienia się o infrastrukturę przy użyciu aplikacji Azure Logic Apps i usługi Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666554"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Usługa Azure Serverless: Omówienie tworzenia aplikacji i rozwiązań opartych na chmurze za pomocą aplikacji logiki Azure i funkcji platformy Azure

Aplikacje [bezserwerowe](https://azure.microsoft.com/solutions/serverless/) oferują korzyści, takie jak zwiększona szybkość programist, zmniejszony kod, prostota i skala. W tym artykule omówiono różne atrybuty rozwiązań bezserwerowych i oferty platformy Azure bez serwerów.

## <a name="what-is-serverless"></a>Co to jest bezserwerowe?

Bezserwerowe nie oznacza, że nie ma serwerów, ale deweloperzy nie muszą się martwić o serwery. Duża część tradycyjnego tworzenia aplikacji odpowiada na pytania dotyczące skalowania, hostingu i monitorowania rozwiązań w celu spełnienia wymagań aplikacji. W systemie bezserwerowym te pytania są traktowane jako część rozwiązania. Ponadto aplikacje bez użycia serwera są rozliczane zgodnie z planem opartym na zużyciu. Jeśli aplikacja nigdy nie jest używana, nie naliczane są żadne opłaty. Te funkcje pomagają deweloperom skupić się wyłącznie na logice biznesowej rozwiązania.

Podstawowe usługi platformy Azure dla bezserwerowych to [Usługi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) i usługi Azure [Functions.](https://azure.microsoft.com/services/functions/) Oba rozwiązania są zgodne z wcześniej opisanymi zasadami i pomagają deweloperom tworzyć niezawodne aplikacje w chmurze przy użyciu minimalnego kodu.

## <a name="what-is-azure-logic-apps"></a>Co to jest usługa Azure Logic Apps?

[Usługa Azure Logic Apps](logic-apps-overview.md) umożliwia uproszczenie i wdrożenie skalowalnych integracji i przepływów pracy w chmurze. Ta usługa zapewnia projektanta wizualnego do modelowania i automatyzacji procesu jako serii kroków nazywanych przepływem pracy. Istnieje wiele [łączników](../connectors/apis-list.md) w usługach w chmurze i systemach lokalnych, które szybko łączą aplikację bezserwerową z innymi interfejsami API. Każda aplikacja logiki zaczyna się od wyzwalacza, takiego jak "Po dodaniu konta do programu Dynamics CRM". Po uruchomieniu wyzwalacza przepływ pracy może uruchamiać kombinacje akcji, konwersji i logiki warunkowej. Aplikacje logiki jest doskonałym wyborem podczas organizowania różnych funkcji platformy Azure w procesie, zwłaszcza gdy proces wymaga interakcji z systemem zewnętrznym lub interfejsu API.

Aby rozpocząć korzystanie z aplikacji logiki, zacznij od [tworzenia pierwszej aplikacji logiki](quickstart-create-first-logic-app-workflow.md). Aby uzyskać więcej informacji technicznych na temat aplikacji logiki, zobacz [odwołanie dewelopera](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Co to jest usługa Azure Functions?

Usługa Azure Functions to usługa do łatwego uruchamiania fragmentów kodu lub "funkcji" w chmurze. Możesz napisać tylko kod niezbędny dla bieżącego problemu, nie martwiąc się o całą aplikację lub wymaganą infrastrukturę. Funkcje mogą sprawić, że rozwój będzie jeszcze bardziej produktywny i można użyć wybranego języka programowania, takiego jak C#, F#, Node.js, Python lub PHP. Płacisz tylko za czas, w której kod jest uruchamiany, a platforma Azure jest skalowana w razie potrzeby.

Aby rozpocząć korzystanie z usługi Azure Functions, zacznij od [tworzenia pierwszej funkcji platformy Azure](../azure-functions/functions-create-first-azure-function.md). Aby uzyskać więcej informacji technicznych na temat funkcji, zobacz [odwołanie dewelopera](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Jak tworzyć i wdrażać aplikacje bezserwerowe na platformie Azure?

Platforma Azure udostępnia zaawansowane narzędzia do tworzenia, wdrażania i zarządzania aplikacjami bez użycia serwera. Aplikacje można tworzyć bezpośrednio w witrynie Azure portal, za pomocą [narzędzi w programie Visual Studio](logic-apps-serverless-get-started-vs.md)lub visual studio [code](quickstart-create-logic-apps-visual-studio-code.md). Po utworzeniu aplikacji można [szybko wdrożyć tę aplikację za pomocą szablonów usługi Azure Resource Manager](logic-apps-deploy-azure-resource-manager-templates.md). Platforma Azure zapewnia również monitorowanie, do którego można uzyskać dostęp za pośrednictwem witryny Azure portal, za pośrednictwem zestawu API lub zestawu SDK lub za pomocą zintegrowanego narzędzia dla dzienników usługi Azure Monitor i usługi Application Insights.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie aplikacji bezserwerowej w programie Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Tworzenie pulpitu nawigacyjnego informacji o klientach bez serwera](logic-apps-scenario-social-serverless.md)
* [Tworzenie wdrożenia aplikacji logiki](logic-apps-azure-resource-manager-templates-overview.md)
