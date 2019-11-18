---
title: Zmniejsz koszty usługi przy użyciu Azure Advisor | Microsoft Docs
description: Użyj Azure Advisor, aby zoptymalizować koszty wdrożeń platformy Azure.
services: advisor
documentationcenter: NA
author: saket-ms
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: sagupt
ms.openlocfilehash: cae16430ce933b8d071696377b22f10c15b5eae5
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145393"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Zmniejsz koszty usługi przy użyciu Azure Advisor

Usługa Advisor pomaga zoptymalizować i obniżyć ogólne wydatki na platformę Azure, identyfikując bezczynne i niedostateczne zasoby. Możesz uzyskać zalecenia dotyczące kosztów na karcie **koszt** na pulpicie nawigacyjnym usługi Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optymalizowanie wydatków maszyn wirtualnych przez zmianę rozmiarów lub zamykanie nieużywanych wystąpień 

Chociaż niektóre scenariusze aplikacji mogą powodować niskie użycie w projekcie, często możesz zaoszczędzić pieniądze, zarządzając rozmiarem i liczbą maszyn wirtualnych. Zaawansowane modele oceny usługi Advisor traktują maszyny wirtualne do zamknięcia, gdy P95th maksimum maksymalnego użycia procesora CPU jest mniejsze niż 3%, a wykorzystanie sieci jest mniejsze niż 2% w okresie 7 dni. Maszyny wirtualne są uważane za odpowiedni rozmiar, gdy można dopasować bieżące obciążenie do mniejszej jednostki SKU (w ramach tej samej rodziny SKU) lub mniejszej liczby wystąpień w taki sposób, aby bieżące obciążenie nie przeszedł do 80% w przypadku obciążeń niezwiązanych z użytkownikiem, a nie powyżej 40% w przypadku obciążenia związanego z użytkownikiem. W tym miejscu typ obciążenia jest określany przez analizowanie charakterystyki użycia procesora CPU.

Zalecane akcje są zamykane lub zmieniane, w odniesieniu do zasobów zalecanych dla. Program Advisor pokazuje szacowane oszczędności kosztów dla zalecanych akcji — zmiana rozmiaru lub zamknięcie. Ponadto w przypadku akcji zalecanej do zmiany rozmiaru klasyfikator udostępnia bieżące i docelowe informacje o jednostce SKU. 

Jeśli chcesz bardziej agresywnie identyfikować nieużywane maszyny wirtualne, możesz dostosować regułę użycia procesora CPU dla każdej subskrypcji.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Zmniejsz koszty, eliminując niezainicjowane obwody usługi ExpressRoute

Program Advisor identyfikuje obwody usługi ExpressRoute, które były w stanie dostawcy *nieobsługiwanym przez więcej* niż jeden miesiąc, i zaleca usunięcie obwodu, jeśli nie planujesz zainicjowania obsługi obwodu u usługodawcy.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Obniżenie kosztów przez usunięcie lub ponowne skonfigurowanie bezczynnych bram sieci wirtualnej

Doradca identyfikuje bramy sieci wirtualnej, które są bezczynne przez ponad 90 dni. Ponieważ bramy są rozliczane co godzinę, należy rozważyć ponowne skonfigurowanie lub usunięcie ich, jeśli nie zamierzasz ich używać. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Kup zarezerwowane wystąpienia maszyn wirtualnych, aby zaoszczędzić pieniądze w porównaniu z kosztami płatność zgodnie z rzeczywistym użyciem

Usługa Advisor sprawdzi użycie maszyny wirtualnej w ciągu ostatnich 30 dni i określi, czy możesz zaoszczędzić pieniądze, kupując rezerwację na platformie Azure. W usłudze Advisor zostaną wyświetlone regiony i rozmiary, w których można było korzystać z najbardziej oszczędności i będą widoczne szacowane oszczędności wynikające z kupowania rezerwacji. Dzięki rezerwacji na platformie Azure można wstępnie zakupić podstawowe koszty dla maszyn wirtualnych. Rabaty będą automatycznie stosowane do nowych lub istniejących maszyn wirtualnych o tym samym rozmiarze i regionie co rezerwacje. [Dowiedz się więcej o Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Program Advisor powiadamia również użytkownika o wystąpieniach zarezerwowanych, które wygaśnie w ciągu następnych 30 dni. Zalecamy zakupienie nowych wystąpień zarezerwowanych, aby uniknąć płacenia cen płatności zgodnie z rzeczywistym użyciem.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Usuń nieskojarzone publiczne adresy IP, aby zaoszczędzić pieniądze

Usługa Advisor identyfikuje publiczne adresy IP, które nie są obecnie skojarzone z zasobami platformy Azure, takimi jak moduły równoważenia obciążenia lub maszyny wirtualne. Te publiczne adresy IP są naliczane przy użyciu stawki nominalnej. Jeśli nie planujesz ich używania, usunięcie ich może spowodować zmniejszenie kosztów.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Usuń potoki Azure Data Factory, które kończą się niepowodzeniem

Azure Advisor wykryje Azure Data Factory potoki, które wielokrotnie kończą się niepowodzeniem i zalecamy rozwiązanie problemów lub usunięcie potoków zakończonych niepowodzeniem, jeśli nie są już potrzebne. Za te potoki będą naliczane opłaty nawet wtedy, gdy nie są one obsługiwane w przypadku awarii. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Użyj standardowych migawek dla Managed Disks
Aby zaoszczędzić 60% kosztów, zalecamy przechowywanie migawek w magazynie w warstwie Standardowa, niezależnie od typu magazynu dysku nadrzędnego. Jest to opcja domyślna dla migawek Managed Disks. Azure Advisor zidentyfikuje migawki, które są przechowywane Premium Storage i zaleca Migrowanie migawki z magazynu w warstwie Premium do warstwy Standardowa. [Dowiedz się więcej o cenach dysku zarządzanego](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących kosztów w Azure Advisor

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor kliknij kartę **koszt** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie](advisor-get-started.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
