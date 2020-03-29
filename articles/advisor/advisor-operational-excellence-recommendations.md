---
title: Zwiększ ekscelencję operacyjną subskrypcji platformy Azure dzięki usłudze Azure Advisor
description: Korzystanie z funkcji Doradcy w celu optymalizacji i osiągnięcia dojrzałości operacyjnej w zakresie subskrypcji platformy Azure
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443068"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Osiągnij doskonałość operacyjną dzięki usłudze Azure Advisor

Zalecenia dotyczące doskonałości operacyjnej usługi Azure Advisor pomagają klientowi w zakresie wydajności procesu i przepływu pracy, zarządzania zasobami i najlepszych rozwiązań dotyczących wdrażania. Te zalecenia można uzyskać od doradcy na karcie **Doskonałość operacyjna** na pulpicie nawigacyjnym doradcy.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Tworzenie alertów usługi Azure Service Health, które mają być powiadamiane, gdy problemy z platformą Azure wpływają na Ciebie

Zalecamy skonfigurowanie alertów usługi Azure Service Health, które mają być powiadamiane, gdy problemy z usługą Platformy Azure mają wpływ na Ciebie. [Usługa Azure Service Health](https://azure.microsoft.com/features/service-health/) to bezpłatna usługa, która zapewnia spersonalizowane wskazówki i pomoc techniczną w przypadku wystąpienia problemu z usługą Azure. Doradca identyfikuje subskrypcje, które nie mają skonfigurowanych alertów i zaleca ich utworzenie.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Zaprojektuj konta magazynu, aby zapobiec osiągnięciu maksymalnego limitu subskrypcji

Region platformy Azure może obsługiwać maksymalnie 250 kont magazynu na subskrypcję. Po osiągnięciu limitu nie będzie można utworzyć więcej kont magazynu w tej kombinacji regionu/subskrypcji. Doradca sprawdzi subskrypcje i zalecenia dotyczące powierzchni, aby zaprojektować mniejszą liczbę kont magazynu dla wszystkich, które są bliskie osiągnięcia maksymalnego limitu.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Upewnij się, że masz dostęp do ekspertów w chmurze platformy Azure, gdy jej potrzebujesz

Podczas uruchamiania obciążenia o krytycznym znaczeniu dla firmy, ważne jest, aby mieć dostęp do pomocy technicznej w razie potrzeby. Doradca identyfikuje potencjalne subskrypcje o znaczeniu krytycznym dla firmy, które nie mają pomocy technicznej zawartej w planie pomocy technicznej, i zaleca uaktualnienie do opcji obejmującej pomoc techniczną.

## <a name="repair-invalid-log-alert-rules"></a>Naprawianie nieprawidłowych reguł alertów dziennika

Usługa Azure Advisor wykryje reguły alertów, które mają nieprawidłowe zapytania określone w sekcji warunków. Reguły alertów dziennika są tworzone w usłudze Azure Monitor i służą do uruchamiania zapytań analitycznych w określonych odstępach czasu. Wyniki zapytania określają, czy trzeba wyzwolić alert. Zapytania analityczne z upływem czasu mogą stać się nieprawidłowe z powodu zmian w zasobach, tabelach lub poleceniach, do których się odwołują. Advisor zaleci poprawienie kwerendy w regule alertów, aby uniemożliwić jej automatyczne wyłączenie i zapewnić monitorowanie zasobów na platformie Azure. [Dowiedz się więcej o rozwiązywaniu problemów z regułami alertów](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Postępuj zgodnie z najlepszymi praktykami przy użyciu zasad platformy Azure

Azure Policy to usługa platformy Azure, która umożliwia tworzenie i przypisywanie zasad oraz zarządzanie nimi. Te zasady wymuszają różne reguły i efekty względem zasobów. Poniżej znajdują się zalecenia dotyczące zasad platformy Azure, które pomogą Ci osiągnąć ekscelencję operacyjną: 
1. Zarządzanie tagami przy użyciu zasad platformy Azure: ta zasada dodaje lub zastępuje określony tag i wartość podczas tworzenia lub aktualizowanie dowolnego zasobu. Istniejące zasoby można skorygować, wyzwalając zadanie korygowania. Ponadto nie modyfikuje tagów w grupach zasobów.
2. Wymuszaj wymagania dotyczące zgodności geograficznej przy użyciu zasad platformy Azure: zasady umożliwiają ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. 
3. Określ dozwolone jednostki SKU maszyny wirtualnej dla wdrożeń: Ta zasada umożliwia określenie zestawu jednostek SKU maszyny wirtualnej, które organizacja może wdrożyć.
4. Wymuszanie "Inspekcji maszyn wirtualnych, które nie używają dysków zarządzanych" przy użyciu zasad platformy Azure
5. Użyj "Dziedzicz tag z grup zasobów" przy użyciu zasad platformy Azure: zasady dodaje lub zastępuje określony tag i wartość z nadrzędnej grupy zasobów, gdy dowolny zasób jest tworzony lub aktualizowany. Istniejące zasoby można skorygować, wyzwalając zadanie korygowania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach doradcy, zobacz:
* [Wprowadzenie do doradcy](advisor-overview.md)
* [Rozpocząć](advisor-get-started.md)
* [Rekomendacje dotyczące kosztów doradcy](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności doradcy](advisor-performance-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń doradcy](advisor-security-recommendations.md)
