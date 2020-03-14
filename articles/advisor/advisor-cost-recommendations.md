---
title: Zmniejsz koszty usługi przy użyciu Azure Advisor
description: Użyj Azure Advisor, aby zoptymalizować koszty wdrożeń platformy Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259697"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Zmniejsz koszty usługi przy użyciu Azure Advisor

Usługa Advisor pomaga zoptymalizować i obniżyć ogólne wydatki na platformę Azure, identyfikując bezczynne i niedostateczne zasoby. Możesz uzyskać zalecenia dotyczące kosztów na karcie **koszt** na pulpicie nawigacyjnym usługi Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optymalizowanie wydatków maszyn wirtualnych przez zmianę rozmiarów lub zamykanie nieużywanych wystąpień 

Chociaż niektóre scenariusze aplikacji mogą powodować niskie użycie w projekcie, często możesz zaoszczędzić pieniądze, zarządzając rozmiarem i liczbą maszyn wirtualnych. Zaawansowane modele oceny usługi Advisor uwzględniają maszyny wirtualne do zamknięcia, gdy P95th maksymalnej maksymalnej wartości użycia procesora CPU wynosi mniej niż 3%, a wykorzystanie sieci jest mniejsze niż 2% w okresie 7 dni. Maszyny wirtualne są uważane za odpowiedni rozmiar, gdy można dopasować bieżące obciążenie do mniejszej jednostki SKU (w ramach tej samej rodziny SKU) lub mniejszej liczby wystąpień w taki sposób, aby bieżące obciążenie nie przeszedł do 80% w przypadku obciążeń niezwiązanych z użytkownikiem, a nie powyżej 40% w przypadku obciążenia związanego z użytkownikiem. W tym miejscu typ obciążenia jest określany przez analizowanie charakterystyki użycia procesora CPU.

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
Aby obniżyć koszt o 60%, zalecamy przechowywanie migawek w usłudze Standard Storage niezależnie od typu magazynu na dysku nadrzędnym. Ta opcja jest opcją domyślną dla migawek Managed Disks. Azure Advisor zidentyfikuje migawki, które są przechowywane Premium Storage i zaleca Migrowanie migawki z magazynu w warstwie Premium do warstwy Standardowa. [Dowiedz się więcej o cenach dysku zarządzanego](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Korzystanie z zarządzania cyklem życia
Azure Advisor będzie używać analizy dotyczącej liczby obiektów usługi Azure Blob Storage, łącznego rozmiaru i transakcji w celu wykrycia, czy co najmniej jedno konto magazynu najlepiej nadaje się do włączenia zarządzania cyklem życia do danych warstwy. Spowoduje to wyświetlenie monitu o utworzenie reguł zarządzania cyklem życia w celu automatycznego przechowania danych w celu zoptymalizowania lub archiwizacji, aby zoptymalizować koszty magazynowania podczas zachowywania danych w usłudze Azure Blob Storage w celu zapewnienia zgodności aplikacji.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących kosztów w Azure Advisor

1. Zaloguj się do [Azure portal](https://portal.azure.com).

1. Wyszukaj i wybierz opcję [**Advisor**](https://aka.ms/azureadvisordashboard) z dowolnej strony.

1. Na pulpicie nawigacyjnym usługi **Advisor** wybierz kartę **koszt** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Rozpoczęcie pracy](advisor-get-started.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
