---
title: Zmniejsz koszty usługi przy użyciu Azure Advisor | Microsoft Docs
description: Użyj Azure Advisor, aby zoptymalizować koszty wdrożeń platformy Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 96e939f8e3da58a123d9a6733b71b74c2ff0ba87
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311910"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Zmniejsz koszty usługi przy użyciu Azure Advisor

Usługa Advisor pomaga zoptymalizować i obniżyć ogólne wydatki na platformę Azure, identyfikując bezczynne i niedostateczne zasoby. Możesz uzyskać zalecenia dotyczące kosztów na karcie **koszt** na pulpicie nawigacyjnym usługi Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optymalizowanie wydatków maszyn wirtualnych przez zmianę rozmiarów lub zamykanie nieużywanych wystąpień 

Chociaż niektóre scenariusze aplikacji mogą powodować niskie użycie w projekcie, często możesz zaoszczędzić pieniądze, zarządzając rozmiarem i liczbą maszyn wirtualnych. Usługa Advisor monitoruje użycie maszyn wirtualnych przez 7 dni, a następnie identyfikuje maszyny wirtualne o niskiej przepustowości. Maszyny wirtualne są uznawane za niskie wykorzystanie, jeśli ich użycie procesora CPU wynosi 5% lub mniej, a ich użycie sieci jest mniejsze niż 2% lub jeśli bieżące obciążenie może być dostosowane do mniejszego rozmiaru maszyny wirtualnej.

Program Advisor pokazuje szacowany koszt kontynuowania działania maszyny wirtualnej, dzięki czemu można wybrać opcję zamknięcia lub zmiany rozmiaru.

Jeśli chcesz bardziej agresywnie identyfikować nieużywane maszyny wirtualne, możesz dostosować regułę użycia procesora CPU dla każdej subskrypcji.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Zmniejsz koszty, eliminując niezainicjowane obwody usługi ExpressRoute

Program Advisor identyfikuje obwody usługi ExpressRoute, które były w stanie  dostawcy nieobsługiwanym przez więcej niż jeden miesiąc, i zaleca usunięcie obwodu, jeśli nie planujesz zainicjowania obsługi obwodu u usługodawcy.

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
