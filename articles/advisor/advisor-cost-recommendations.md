---
title: Zmniejsz koszty usługi przy użyciu usługi Azure Advisor | Dokumentacja firmy Microsoft
description: Azure Advisor umożliwiają optymalizację kosztu wdrożeń platformy Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 188a79df99a174436808acd3d964abf9357cf4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467940"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Zmniejsz koszty usługi przy użyciu usługi Azure Advisor

Advisor pomaga zoptymalizować i zmniejszyć ogólną platformy Azure możesz wydać identyfikując bezczynności i niedostatecznie używanych zasobów. Pobierz mogą kosztować z zaleceniami **koszt** karty Pulpit nawigacyjny usługi Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optymalizowanie maszyny wirtualnej, który możesz wydać przez zmianą rozmiaru lub zamykanie niedostatecznie używanych wystąpień 

Mimo że niektóre scenariusze aplikacji może spowodować niewielkie wykorzystanie zgodnie z projektem, często można oszczędzić pieniądze, zarządzając rozmiaru i liczby maszyn wirtualnych. Klasyfikator monitoruje wykorzystanie maszyny wirtualnej przez 7 dni, a następnie identyfikuje niskiego wykorzystania maszyn wirtualnych. Wirtualne wykorzystanie niski, jeśli użycie Procesora wynosi 5% lub mniej, są traktowane jako maszyn i ich wykorzystania sieci jest mniejsza niż % 2, lub jeśli bieżące obciążenie może zostać uwzględniony w obrębie mniejszego rozmiaru maszyny wirtualnej.

Klasyfikator pokazuje szacowany koszt kontynuowani e uruchamiania maszyny wirtualnej, tak, aby można było wybrać zamknij go, lub zmienić jego rozmiar.

Jeśli chcesz mieć wyższe na identyfikowanie niedostatecznie używanych maszyn wirtualnych, można dostosować średni reguły wykorzystanie procesora CPU na podstawie każdej subskrypcji.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Obniżenie kosztów przez wyeliminowanie nieudostępniane obwodów usługi ExpressRoute

Klasyfikator identyfikuje obwodów usługi ExpressRoute, które są w stanie dostawcy *nie zainicjowano obsługi administracyjnej* dla więcej niż jednego miesiąca i zaleca usunięcie obwodu, jeżeli nie planujesz go aprowizować z łącznością Dostawca.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Obniżenie kosztów przez usunięcie lub ponowne konfigurowanie bezczynne bramy sieci wirtualnej

Klasyfikator identyfikuje bram sieci wirtualnej, które mają bezczynna przez ponad 90 dni. Ponieważ bram są naliczane godzinowo, należy rozważyć ponowne konfigurowanie lub usuwając je, jeśli nie ma do nich już dłużej korzystać. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Kup wystąpienia zarezerwowane maszyn wirtualnych, aby zaoszczędzić pieniądze w porównaniu z kosztami płatności

Klasyfikator spowoduje wśród danych użycia maszyny wirtualnej w ciągu ostatnich 30 dni i określić, jeśli można zapisać pieniędzy za zakup rezerwacji platformy Azure. Klasyfikator opisano regionów i rozmiarach, w którym potencjalnie ma większość oszczędności i pokaże Szacowane oszczędności z zakupu rezerwacji. Za pomocą rezerwacji platformy Azure możesz wstępnie zakup podstawowych kosztów dla maszyn wirtualnych. Rabaty zostaną automatycznie zastosowane do nowego lub istniejącego maszyn wirtualnych, które mają ten sam rozmiar i region rezerwacji. [Dowiedz się więcej o zarezerwowanych wystąpieniach maszyn wirtualnych platformy Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor również powiadomi wystąpień zarezerwowanych, do których masz które wygaśnie w ciągu następnych 30 dni. Oferuje rekomendacje, zakup wystąpienia zarezerwowane nowy, aby uniknąć płacenia zgodnie z rzeczywistym użyciem.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Usuń nieskojarzonego publiczne adresy IP w celu zaoszczędzenia pieniędzy

Klasyfikator identyfikuje publiczne adresy IP, które nie są aktualnie skojarzone z zasobami platformy Azure, takich jak moduły równoważenia obciążenia lub maszyn wirtualnych. Publiczny adres IP na te adresy pochodzą z za symboliczną cenę. Jeśli użytkownik nie chce ich używać, usuwając je może spowodować obniżenie kosztów.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących kosztów w usłudze Azure Advisor

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor kliknij **koszt** kartę.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zalecenia usługi Advisor, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Rozpoczęcie pracy](advisor-get-started.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-cost-recommendations.md)
