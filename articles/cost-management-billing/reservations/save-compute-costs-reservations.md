---
title: Co to jest Azure Reservations?
description: Dowiedz się więcej o rezerwacjach platformy Azure i cenach, które umożliwiają oszczędzanie za kosztach maszyn wirtualnych, baz danych SQL, usługi Azure Cosmos DB i innych zasobów.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 6277a7e7dc5891a3bc67c298a31344284c92e31d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235636"
---
# <a name="what-are-azure-reservations"></a>Co to jest Azure Reservations?

Rezerwacje platformy Azure pomagają zaoszczędzić pieniądze dzięki rocznym lub 3-letnim planom dla wielu produktów. Zobowiązanie umożliwia uzyskanie rabatu dotyczącego używanych zasobów. Rezerwacje mogą znacznie obniżyć koszty zasobów, nawet o 72% w porównaniu do cen przy płatności zgodnie z rzeczywistym użyciem. Rezerwacje umożliwiają skorzystanie z rabatu na rozliczenia i nie mają wpływu na stan środowiska uruchomieniowego Twoich zasobów. Rabat na rezerwację zostanie automatycznie zastosowany do pasujących zasobów po zakupie rezerwacji.

Za rezerwację można zapłacić z góry lub w miesięcznych ratach. Łączny koszt rezerwacji w przypadku płatności z góry i miesięcznych rat jest taki sam, a wybór płatności miesięcznych nie pociąga za sobą dodatkowych opłat. Opcja płatności miesięcznych jest dostępna w przypadku rezerwacji platformy Azure, a nie produktów innych firm.

Rezerwację można kupić w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Dlaczego warto kupić rezerwację?

Jeśli masz spójne użycie zasobów z obsługą rezerwacji, zakup rezerwacji pozwala zmniejszyć koszty. Jeśli na przykład ciągle uruchamiasz wystąpienia usługi bez rezerwacji, opłaty są naliczone według stawek płatności zgodnie z rzeczywistym użyciem. Przy zakupie rezerwacji natychmiast uzyskasz rabat na rezerwację. Zasoby nie będą już obciążane opłatami według stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Po zakupie rabat związany z rezerwacją jest automatycznie stosowany do użycia zasobów pasującego do atrybutów wybranych podczas zakupu rezerwacji. Atrybuty obejmują jednostkę SKU, regiony (tam, gdzie ma to zastosowanie) i zakres. Zakres rezerwacji określa, gdzie wystąpią oszczędności z rezerwacji.

Aby uzyskać więcej informacji na temat sposobu stosowania rabatu, zobacz [Stosowanie rabatu za wystąpienie zarezerwowane](reservation-discount-application.md).

Aby uzyskać więcej informacji o tym, jak działa zakres rezerwacji, zobacz [Określanie zakresu rezerwacji](prepare-buy-reservation.md#scope-reservations).


## <a name="flexibility-with-azure-reservations"></a>Elastyczność dzięki rezerwacjom platformy Azure

Rezerwacje platformy Azure zapewniają elastyczność, dzięki której można łatwiej sprostać Twoim zmieniającym się potrzebom. Rezerwację można wymienić na inną rezerwację tego samego typu. Jeśli rezerwacja nie jest już potrzebna, można uzyskać zwrot jej kosztów, maksymalnie 50 000 USD w 12-miesięcznym oknie kroczącym. Maksymalny limit zwrotu dotyczy wszystkich rezerwacji w zakresie umowy z firmą Microsoft.

Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure)](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>Opłaty objęte rezerwacją

- **Wystąpienie zarezerwowane maszyny wirtualnej** — rezerwacja obejmuje tylko koszty obliczeń na maszynie wirtualnej. Nie dotyczy dodatkowych opłat za oprogramowanie, system Windows, sieć ani magazyn.
- **Pojemność zarezerwowana usługi Azure Storage** — rezerwacja obejmuje pojemność magazynu dla kont magazynu w warstwie Standardowa dla magazynu obiektów blob lub magazynu usługi Azure Data Lake Gen2. Rezerwacja nie obejmuje stawek za przepustowość lub transakcje.
- **Pojemność zarezerwowana usługi Azure Cosmos DB** — rezerwacja obejmuje przepływność przydzieloną dla zasobów. Nie dotyczy opłat za magazyn i sieć.
- **Zarezerwowany rdzeń wirtualny w usłudze SQL Database** — w rezerwacji są uwzględniane tylko koszty obliczeń. Opłata za licencję SQL jest naliczana oddzielnie.
- **SQL Data Warehouse** — rezerwacja obejmuje użycie jednostek cDWU. Nie pokrywa kosztów magazynu i sieci wynikających z użycia usługi SQL Data Warehouse.
- **Azure Databricks** — rezerwacja obejmuje tylko użycie jednostek DBU. Pozostałe opłaty, na przykład za obliczenia, magazyn i sieć, są naliczane oddzielnie.
- **Podatek od czynności cywilnoprawnych za korzystanie z usługi App Service** — rezerwacja obejmuje użycie sygnatury. Nie dotyczy to procesów roboczych, dlatego opłaty za inne zasoby związane z sygnaturą są naliczane oddzielnie.
- **Azure Database for MySQL** — w rezerwacji są uwzględniane tylko koszty obliczeń. Rezerwacja nie obejmuje opłat za oprogramowanie, sieć ani magazyn związane z serwerem bazy danych MySQL.
- **Azure Database for PostgreSQL** — w rezerwacji są uwzględniane tylko koszty obliczeń. Rezerwacja nie obejmuje opłat za oprogramowanie, sieć ani magazyn związane z serwerami bazy danych PostgreSQL.
- **Azure Database for MariaDB** — w rezerwacji są uwzględniane tylko koszty obliczeń. Rezerwacja nie obejmuje opłat za oprogramowanie, sieć ani magazyn związane z serwerem bazy danych MariaDB.
- **Azure Data Explorer** — rezerwacja obejmuje opłaty za znaczniki. Rezerwacja nie obejmuje opłat za obliczenia, sieć ani magazyn związane z klastrami.
- **Azure Cache for Redis** — w rezerwacji są uwzględniane tylko koszty obliczeń. Rezerwacja nie obejmuje opłat za sieć ani magazyn związanych z wystąpieniami pamięci podręcznej Redis Cache.
- **Azure Dedicated Host** — host dedykowany obejmuje tylko koszty obliczeń.
- **Rezerwacje usługi Azure Disk Storage** — rezerwacja obejmuje tylko dyski SSD w warstwie Premium o rozmiarze P30 lub większym. Nie obejmuje żadnych innych typów dysków ani rozmiarów mniejszych niż P30.

Plany oprogramowania:

- **SUSE Linux** — rezerwacja obejmuje koszty planu oprogramowania. Rabaty mają zastosowanie tylko do mierników SUSE, a nie do użycia maszyn wirtualnych.
- **Plany oprogramowania Red Hat** — rezerwacja obejmuje koszty planu oprogramowania. Rabaty mają zastosowanie tylko do mierników RedHat, a nie do użycia maszyn wirtualnych.
- **Azure VMware Solution by CloudSimple** — rezerwacja obejmuje węzły rozwiązania VMWare CloudSimple. Nadal obowiązują dodatkowe opłaty za oprogramowanie.
- **Azure Red Hat OpenShift** — rezerwacja ma zastosowanie do kosztów oprogramowania OpenShift, a nie do kosztów infrastruktury platformy Azure.

W przypadku maszyn wirtualnych z systemem Windows i usługi SQL Database rabat na rezerwacje nie dotyczy kosztów oprogramowania. Koszty licencjonowania można pokryć w ramach [Korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat rezerwacji na platformie Azure, czytając następujące artykuły:
    - [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
    - [Understand reservation usage for your subscription with pay-as-you-go rates](understand-reserved-instance-usage.md) (Informacje na temat użycia rezerwacji w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)
    - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](reserved-instance-windows-software-costs.md)
    - [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)

- Dowiedz się więcej na temat rezerwacji w przypadku planów usługi:
    - [Maszyny wirtualne w ramach usługi Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Zasoby Azure Cosmos DB w ramach zarezerwowanej pojemności Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Zasoby obliczeniowe usługi SQL Database w ramach zarezerwowanej pojemności usługi Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md) Dowiedz się więcej na temat rezerwacji w przypadku planów oprogramowania:
    - [Plany oprogramowania Red Hat w ramach rezerwacji platformy Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Plany oprogramowania SUSE w ramach rezerwacji platformy Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
