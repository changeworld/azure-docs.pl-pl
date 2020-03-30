---
title: Dane klasyfikatora na wykresie zasobów platformy Azure
description: Wykonuj kwerendy dotyczące danych klasyfikatora w programie Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502453"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Kwerenda o dane klasyfikatora w Eksploratorze wykresu zasobów (azure resource graph)

Zasoby klasyfikatora są teraz dołączane do [programu Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Stanowi to podstawę dla wielu scenariuszy klientów na dużą skalę dla zaleceń doradcy. Kilka scenariuszy, które nie były możliwe przed zrobić na dużą skalę, a teraz można osiągnąć za pomocą wykresu zasobów są:
* Zapewnia możliwość wykonywania złożonych zapytań dla wszystkich subskrypcji w witrynie Azure portal
* Zalecenia podsumowane według typów kategorii (takich jak wysoka dostępność, wydajność) i typów uderzeń (wysoki, średni, niski)
* Wszystkie zalecenia dotyczące określonego typu rekomendacji
* Liczba zasobów, na które ma wpływ, według kategorii rekomendacji

![Doradca w eksploratorze wykresów zasobów platformy Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Typy zasobów klasyfikatora w programie Azure Graph

Dostępne typy zasobów klasyfikatora na [wykresie zasobów:](https://docs.microsoft.com/azure/governance/resource-graph/)Dostępne są 3 typy zasobów do wykonywania zapytań w obszarze Zasoby klasyfikatora. Oto lista zasobów, które są teraz dostępne do wykonywania zapytań w wykresie zasobów.
* Microsoft.Advisor/konfiguracje
* Microsoft.Advisor/recommendations
* Microsoft.Advisor/tłumienie

Te typy zasobów są wyświetlane w nowej tabeli o nazwie AdvisorResources, które można również kwerendy w Eksploratorze wykresu zasobów w witrynie Azure portal.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń advisor, zobacz:
* [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Rekomendacje dotyczące kosztów doradcy](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności doradcy](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń doradcy](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej doradcy](advisor-operational-excellence-recommendations.md)
* [Interfejs API REST doradcy](https://docs.microsoft.com/rest/api/advisor/)
