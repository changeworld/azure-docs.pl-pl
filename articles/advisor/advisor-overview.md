---
title: Wprowadzenie do usługi Azure Advisor | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Advisor do optymalizacji wdrożeń platformy Azure.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: makohli
ms.openlocfilehash: e1d7edef304dc91829066f19b6974f0bcf0be0a5
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054129"
---
# <a name="introduction-to-azure-advisor"></a>Wprowadzenie do usługi Azure Advisor

Dowiedz się więcej na temat kluczowych funkcji usługi Azure Advisor i Uzyskaj odpowiedzi na często zadawane pytania.

## <a name="what-is-advisor"></a>Co to jest usługa Advisor?
Advisor to spersonalizowany konsultant ds. chmury, który ułatwia stosowanie najlepszych rozwiązań do optymalizacji wdrożeń platformy Azure. Analizuje konfigurację zasobów i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu wysokiej dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

Usługa Advisor można wykonywać następujące czynności:
* Skorzystaj z aktywnego, informacje z możliwością działania i zalecenia dotyczące spersonalizowane najlepszych rozwiązań. 
* Poprawa wydajności, bezpieczeństwa i wysokiej dostępności zasobów, identyfikując możliwości, aby zmniejszyć ogólną platformy Azure możesz wydać.
* Uzyskiwanie zaleceń dotyczących za pomocą wbudowanego proponowanych działań.

Możesz uzyskać dostęp Advisor za pomocą [witryny Azure portal](https://aka.ms/azureadvisordashboard). Zaloguj się do [portal](https://portal.azure.com), zlokalizuj **Advisor** w menu nawigacji, lub wyszukaj go w **wszystkich usług** menu.

Pulpit nawigacyjny usługi Advisor zawierał spersonalizowane zalecenia dotyczące wszystkich subskrypcji.  Można zastosować filtry, aby wyświetlić zalecenia dla określonej subskrypcji i typów zasobów.  Zalecenia są podzielone na cztery kategorie: 

* **Wysoka dostępność**: Upewnij się, i ciągłość aplikacje krytyczne dla prowadzonej działalności. Aby uzyskać więcej informacji, zobacz [zaleceń dotyczących wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md).
* **Zabezpieczenia**: wykrywanie zagrożeń i luk w zabezpieczeniach, które mogą prowadzić do naruszenia zabezpieczeń. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md).
* **Wydajność**: przyspieszyć aplikacji. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md).
* **Koszt**: do optymalizacji i Zmniejsz wydatki ogólną platformy Azure. Aby uzyskać więcej informacji, zobacz [rekomendacji dotyczących kosztu Advisor](advisor-cost-recommendations.md).

  ![Typy zalecenia usługi Advisor](./media/advisor-overview/advisor-dashboard.png)

Kliknij kategorię, aby wyświetlić listę zaleceń w ramach tej kategorii i wybierz zalecenie, aby dowiedzieć się więcej na ten temat.  Można także dowiesz się o akcjach, które należy wykonać, aby skorzystać z szansy sprzedaży lub rozwiązać problem.

![Kategoria zalecenia usługi Advisor](./media/advisor-overview/advisor-ha-category-example.png) 

Wybierz zalecaną akcję dotyczącą zalecenie, aby zaimplementować zalecenia.  Spowoduje to otwarcie umożliwiające zaimplementowanie zalecenie lub dotyczą dokumentacji, która pomaga w implementacji za pomocą prostego interfejsu.  Po implementacji zalecenia może potrwać dzień doradcy, rozpoznawał.

Jeśli nie zamierzasz wykonać natychmiastowego działania zgodnie z zaleceniem, można odłożyć w określonym przedziale czasu lub go odrzucić.  Jeśli nie chcesz otrzymywać zalecenia dla określonej subskrypcji lub grupy zasobów, można skonfigurować usługę Advisor, aby generować jedynie zaleceń dotyczących określonej subskrypcji i grup zasobów.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-do-i-access-advisor"></a>Jak uzyskać dostęp do usługi Advisor?
Możesz uzyskać dostęp Advisor za pomocą [witryny Azure portal](https://aka.ms/azureadvisordashboard). Zaloguj się do [portal](https://portal.azure.com), zlokalizuj **Advisor** w menu nawigacji, lub wyszukaj go w **wszystkich usług** menu.

Można również wyświetlić zalecenia usługi Advisor za pośrednictwem interfejsu zasobów maszyny wirtualnej. Wybierz maszynę wirtualną, a następnie przewiń listę do zalecenia usługi Advisor w menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Jakie uprawnienia muszę dostęp do usługi Advisor?
 
Możesz uzyskać dostęp zalecenia usługi Advisor jako *właściciela*, *Współautor*, lub *czytnika* subskrypcji.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Jakie zasoby usługi Advisor zapewnia zalecenia dotyczące?

Usługa Advisor udostępnia zalecenia dotyczące maszyn wirtualnych, zestawy dostępności, bramy application Gateway, App Services, serwery SQL i Redis Cache.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Można odroczyć lub odrzucić zalecenie?

Aby odłożyć lub odrzucić zalecenia, kliknij **Odłóż** łącza. Można określić Odłóż okresu lub wybierz opcję **nigdy** odrzucać zalecenia.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zalecenia usługi Advisor, zobacz:

* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Rekomendacji dotyczących kosztu usługi Advisor](advisor-cost-recommendations.md)
