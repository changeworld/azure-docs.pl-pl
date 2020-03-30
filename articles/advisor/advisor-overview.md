---
title: Wprowadzenie do usługi Azure Advisor
description: Użyj usługi Azure Advisor, aby zoptymalizować wdrożenia platformy Azure.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443096"
---
# <a name="introduction-to-azure-advisor"></a>Wprowadzenie do usługi Azure Advisor

Dowiedz się więcej o kluczowych możliwościach usługi Azure Advisor i uzyskaj odpowiedzi na często zadawane pytania.

## <a name="what-is-advisor"></a>Co to jest Doradca?
Advisor to spersonalizowany konsultant w chmurze, który pomaga postępować zgodnie z najlepszymi rozwiązaniami w celu optymalizacji wdrożeń platformy Azure. Analizuje konfigurację zasobów i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu wysokiej dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

Z Advisor, można:
* uzyskiwanie proaktywnych, spersonalizowanych zaleceń dotyczących najlepszych rozwiązań umożliwiających podjęcie działań; 
* Zwiększ wydajność, zabezpieczenia i wysoką dostępność zasobów, identyfikując możliwości zmniejszenia ogólnych wydatków na platformę Azure.
* uzyskiwanie zaleceń dotyczących proponowanych wbudowanych akcji.

Dostęp do advisora można uzyskać za pośrednictwem [portalu Azure.](https://aka.ms/azureadvisordashboard) Zaloguj się do [portalu](https://portal.azure.com), znajdź **doradcę** w menu nawigacyjnym lub wyszukaj go w menu **Wszystkie usługi.**

Pulpit nawigacyjny klasyfikatora wyświetla spersonalizowane rekomendacje dla wszystkich subskrypcji.  Można zastosować filtry do wyświetlania zaleceń dla określonych subskrypcji i typów zasobów.  Rekomendacje są podzielone na cztery kategorie: 

* **Wysoka dostępność:** Aby zapewnić i poprawić ciągłość aplikacji o krytycznym znaczeniu dla firmy. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące wysokiej dostępności dla doradcy](advisor-high-availability-recommendations.md).
* **Bezpieczeństwo:** Wykrywanie zagrożeń i luk w zabezpieczeniach, które mogą prowadzić do naruszenia zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące zabezpieczeń advisora](advisor-security-recommendations.md).
* **Wydajność:** Aby zwiększyć szybkość aplikacji. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące wydajności klasyfikatora](advisor-performance-recommendations.md).
* **Koszt:** aby zoptymalizować i zmniejszyć ogólne wydatki na platformę Azure. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące kosztów doradcy](advisor-cost-recommendations.md).
* **Doskonałość operacyjna**: Aby pomóc Ci osiągnąć wydajność procesu i przepływu pracy, zarządzanie zasobami i najlepsze praktyki wdrożeniowe. . Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące doskonałości operacyjnej doradcy](advisor-operational-excellence-recommendations.md).

  ![Typy rekomendacji doradcy](./media/advisor-overview/advisor-dashboard.png)

Możesz kliknąć kategorię, aby wyświetlić listę rekomendacji w tej kategorii, i wybrać zalecenie, aby dowiedzieć się więcej na ten temat.  Można również dowiedzieć się o działaniach, które można wykonać, aby skorzystać z okazji lub rozwiązać problem.

![Kategoria rekomendacji doradcy](./media/advisor-overview/advisor-ha-category-example.png) 

Wybierz zalecane działanie dla zalecenia, aby zaimplementować zalecenie.  Otworzy się prosty interfejs, który umożliwia wdrożenie zalecenia lub odsyłać do dokumentacji, która pomaga w implementacji.  Po zaimplementowanie zalecenia, może upłynąć do jednego dnia dla doradcy, aby to rozpoznać.

Jeśli nie zamierzasz podjąć natychmiastowych działań w sprawie zalecenia, możesz odroczyć je na określony czas lub odrzucić.  Jeśli nie chcesz otrzymywać rekomendacji dla określonej subskrypcji lub grupy zasobów, możesz skonfigurować program Advisor tak, aby wygenerował tylko zalecenia dotyczące określonych subskrypcji i grup zasobów.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-do-i-access-advisor"></a>Jak uzyskać dostęp do Advisora?
Dostęp do advisora można uzyskać za pośrednictwem [portalu Azure.](https://aka.ms/azureadvisordashboard) Zaloguj się do [portalu](https://portal.azure.com), znajdź **doradcę** w menu nawigacyjnym lub wyszukaj go w menu **Wszystkie usługi.**

Można również wyświetlić zalecenia klasyfikatora za pośrednictwem interfejsu zasobów maszyny wirtualnej. Wybierz maszynę wirtualną, a następnie przewiń do rekomendacji advisor w menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Jakich uprawnień potrzebuję, aby uzyskać dostęp do Advisora?
 
Możesz uzyskać dostęp do rekomendacji doradcy jako *właściciel,* *współautor*lub *czytelnik* subskrypcji.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Jakie zasoby zawiera doradca zalecenia dla?

Doradca zawiera zalecenia dotyczące bramy aplikacji, usług aplikacji, zestawów dostępności, pamięci podręcznej Azure, usługi Azure Data Factory, bazy danych platformy Azure dla mysql, bazy danych platformy Azure dla postgreSQL, bazy danych platformy Azure dla MariaDB, usługi Azure ExpressRoute, usługi Azure Cosmos DB, usługi Azure public Adresy IP, magazyn danych SQL, serwery SQL, konta magazynu, profile usługi Traffic Manager i maszyny wirtualne.

Usługa Azure Advisor zawiera również zalecenia z [usługi Azure Security Center,](https://docs.microsoft.com/azure/security-center/security-center-recommendations) które mogą zawierać zalecenia dotyczące dodatkowych typów zasobów.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Czy mogę odroczyć lub odrzucić zalecenie?

Aby odroczyć lub odrzucić zalecenie, kliknij **łącze Odłóż.** Można określić okres odroczenia lub wybierz **nigdy nie** odrzucić zalecenia.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach doradcy, zobacz:

* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące wysokiej dostępności advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń doradcy](advisor-security-recommendations.md)
* [Zalecenia dotyczące wydajności doradcy](advisor-performance-recommendations.md)
* [Rekomendacje dotyczące kosztów doradcy](advisor-cost-recommendations.md)
