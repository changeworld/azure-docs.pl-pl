---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f57c2cacca9bb3e4526ec6261b8aa0ff6c18448a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66164488"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Omówienie ponownych rozruchów maszyn wirtualnych — konserwacja a przestój
Istnieją trzy scenariusze, które mogą prowadzić do maszyny wirtualnej platformy Azure: nieplanowana konserwacja sprzętu, nieoczekiwany Przestój i planowana konserwacja.

* **Zdarzenie nieplanowanej konserwacji sprzętu** ma miejsce, gdy platforma Azure przewidzi zbliżającą się awarię sprzętu lub dowolnego składnika platformy powiązanego z maszyną fizyczną. Gdy platforma przewidzi awarię, wygeneruje zdarzenie nieplanowanej konserwacji sprzętu, aby zmniejszyć wpływ tej awarii na maszyny wirtualne hostowane na tym sprzęcie. Usługa Azure korzysta [migracji na żywo](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) technologii do migrowania maszyn wirtualnych ze sprzętu ulegającego awarii do dobrej kondycji maszyny fizycznej. Migracja na żywo to operacja zachowywania maszyny wirtualnej, która jedynie wstrzymuje maszynę wirtualną na krótki czas. Pamięć, otwarte pliki i połączenia sieciowe są utrzymywane, ale wydajność przed i/lub po zdarzeniu może zostać ograniczona. W przypadkach, gdy nie można użyć migracji na żywo, maszyna wirtualna doświadczy nieoczekiwanego przestoju zgodnie z opisem poniżej.


* **Nieoczekiwany przestój** jest w przypadku sprzętu lub infrastruktury fizycznej dla maszyny wirtualnej nie powiedzie się nieoczekiwanie. Może to obejmować awarii sieci lokalnej, awariami dysków lokalnych lub inne błędy poziomu stojaka. Po wykryciu, platforma Azure automatycznie migruje (naprawia ją) maszyna wirtualna ma dobrej kondycji komputera fizycznego, w tym samym centrum danych. Podczas wykonywania procedury naprawiania maszyny wirtualne doświadczają przestoju (ponownego rozruchu), a w niektórych przypadkach dochodzi do utraty dysku tymczasowego. Dołączone dyski systemu operacyjnego i danych są zawsze zachowywane. 

  Maszyny wirtualne mogą także doświadczają przestoju, w razie mało prawdopodobnej niedostępność lub awaria, który wpływa na całe centrum danych lub nawet cały region. Dla tych scenariuszy, platforma Azure udostępnia opcje ochrony w tym [strefy dostępności](../articles/availability-zones/az-overview.md) i [sparowane regiony](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* **Zdarzenia planowanej konserwacji** to okresowe aktualizacje przeprowadzane przez firmę Microsoft dla podstawowej platformy Azure w celu zwiększenia ogólnej niezawodności, wydajności i bezpieczeństwa infrastruktury platformy, na której działają maszyny wirtualne. Większość tych aktualizacji jest przeprowadzana bez żadnego wpływu na działanie maszyn wirtualnych ani usług w chmurze (zobacz [konserwacja zachowująca maszyny wirtualne](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Platforma Azure próbuje przeprowadzać konserwację zachowującą maszynę wirtualną przy wszystkich możliwych okazjach, w rzadkich przypadkach jednak aktualizacje wymagają ponownego rozruchu w celu zaktualizowania infrastruktury podstawowej. W takiej sytuacji można przeprowadzić planowaną konserwację platformy Azure z operacją ponownego wdrożenia po konserwacji, inicjując konserwację maszyn wirtualnych w odpowiednim przedziale czasu. Aby uzyskać więcej informacji, zobacz [Planned maintenance for virtual machines in Azure (Planowana konserwacja maszyn wirtualnych na platformie Azure)](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Aby zmniejszyć wpływ przestoju spowodowanego co najmniej jednym z tych zdarzeń, zalecamy zastosowanie następujących najlepszych rozwiązań zapewniających wysoką dostępność maszyn wirtualnych:

* [Konfigurowanie wielu maszyn wirtualnych w zestawie dostępności w celu zapewnienia nadmiarowości]
* [Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności]
* [Używanie zaplanowanych zdarzeń do proaktywnego reagowania na zdarzenia wpływające na maszynie wirtualnej](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurowanie każdej warstwy aplikacji w osobnych zestawach dostępności]
* [Łączenie modułu równoważenia obciążenia z zestawami dostępności]
* [Strefy dostępności umożliwiają ochronę przed awariami na poziomie centrum danych]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurowanie wielu maszyn wirtualnych w zestawie dostępności w celu zapewnienia nadmiarowości
Aby zapewnić nadmiarowość aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Tę konfigurację w centrum danych gwarantuje, że podczas każdej planowanego lub nieplanowanego zdarzenia konserwacji, co najmniej jedna maszyna wirtualna jest dostępna i spełnia 99,95% umowy SLA platformy Azure. Aby uzyskać więcej informacji, zobacz [Virtual Machines — umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Unikaj pozostawiania pojedynczego wystąpienia maszyny wirtualnej w zestawie dostępności. Maszyny wirtualne w tej konfiguracji zakwalifikować się do gwarancji umowy SLA i nie może wystąpić przestój podczas zdarzeń planowana konserwacja platformy Azure, z wyjątkiem sytuacji, gdy pojedyncza maszyna wirtualna jest przy użyciu [Azure — wersja premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd). Dla pojedynczych maszyn wirtualnych przy użyciu dysków SSD w warstwie premium ma zastosowanie umowa SLA platformy Azure.

Każda maszyna wirtualna w zestawie dostępności ma przypisaną **domenę aktualizacji** i **domenę błędów** z odpowiedniej platformy Azure. Dany zestaw dostępności ma pięć domyślnie przypisanych domen aktualizacji, których użytkownik nie może konfigurować (następnie można zwiększyć liczbę wystąpień usługi Resource Manager, aby oferowała do 20 domen aktualizacji). Umożliwia to wskazanie grup maszyn wirtualnych i odpowiedniego sprzętu fizycznego, które mogą być uruchamiane równocześnie. Jeśli w pojedynczym zestawie dostępności skonfigurowano więcej niż pięć maszyn wirtualnych, szósta maszyna wirtualna jest umieszczana w tej samej domenie aktualizacji, co pierwsza maszyna wirtualna, siódma — w tej samej domenie aktualizacji co druga maszyna wirtualna itd. Podczas planowanej konserwacji domeny aktualizacji mogą nie być ponownie uruchamiane kolejno, ale w danym momencie tylko jedna domena aktualizacji jest uruchamiana ponownie. Domena aktualizacji po ponownym rozruchu otrzymuje 30 minut na odzyskanie sprawności zanim konserwacja zostanie zainicjowana w innej domenie aktualizacji.

Domeny błędów definiują grupę maszyn wirtualnych, które korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Domyślnie maszyny wirtualne skonfigurowane w zestawie dostępności są rozdzielane między maksymalnie trzy domeny błędów w przypadku wdrożeń usługi Resource Manager (dwie domeny błędów w przypadku modelu klasycznego). Umieszczenie maszyn wirtualnych w zestawie dostępności nie chroni aplikacji przed błędami związanymi z systemem operacyjnym lub daną aplikacją, ale ogranicza wpływ potencjalnych awarii sprzętu fizycznego, awarii sieci i przerw w dostawie prądu.

<!--Image reference-->
   ![Rysunek koncepcyjny przedstawiający konfigurację domeny domeny i odporności aktualizacji](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności
Jeśli obecnie korzystasz z maszyn wirtualnych z dyskami niezarządzanymi, zdecydowanie zalecamy [konwersję maszyn wirtualnych w zestawie dostępności do korzystania z dysków zarządzanych](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Dyski zarządzane](../articles/virtual-machines/windows/managed-disks-overview.md) zapewniają większą niezawodność zestawów dostępności przez zagwarantowanie, że dyski maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczych punktów awarii. Odbywa się to przez automatyczne umieszczenie dysków w domenach błędów innego magazynu (klastrów magazynu) i dostosowywaniu je w domenie błędów maszyny Wirtualnej. Domena błędów magazynu zakończy się niepowodzeniem z powodu awarii sprzętu lub oprogramowania, tylko z wystąpieniem maszyny Wirtualnej z dyskami w domenie błędów magazynu kończy się niepowodzeniem.
![Usługa Managed disks błędów](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Liczba domen błędów dla zarządzanych zestawów dostępności jest zależna od regionu i może wynosić dwa lub trzy na region. W poniższej tabeli przedstawiono liczbę na region

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Jeśli planujesz używanie maszyn wirtualnych z dyskami niezarządzanymi, postępuj zgodnie z najlepszymi rozwiązaniami dla kont magazynu, gdzie wirtualne dyski twarde (VHD) maszyn wirtualnych są przechowywane jako [stronicowe obiekty BLOB](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Obsługuj wszystkie dyski (systemu operacyjnego i danych) skojarzone z maszyną wirtualną na tym samym koncie magazynu.**
2. Przed dodaniem kolejnych wirtualnych dysków twardych do konta magazynu **przejrzyj [limity](../articles/storage/common/storage-scalability-targets.md) dotyczące liczby niezarządzanych dysków na koncie magazynu**.
3. **Używaj oddzielnego konta magazynu dla każdej maszyny wirtualnej w zestawie dostępności.** Nie stosuj współużytkowania kont magazynu przez wiele maszyn wirtualnych w tym samym zestawie dostępności. Dopuszczalne jest dla maszyn wirtualnych w różnych zestawach dostępności do współużytkowania kont magazynu, jeśli są przestrzegane powyżej najlepszych rozwiązań ![niezarządzane dyski błędów](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Aktywnie odpowiadać na zdarzenia wpływające na maszynie wirtualnej przy użyciu zaplanowanych zdarzeń

Po zasubskrybowaniu [zaplanowane zdarzenia](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), maszyna wirtualna jest powiadamiany o zdarzeniach nadchodzącej konserwacji, które mogą mieć wpływ na maszynie Wirtualnej. Gdy zaplanowanych zdarzeń są włączone, maszyna wirtualna znajduje się minimalną ilość czasu przed wykonaniem związanych z konserwacją. Na przykład aktualizacje systemu operacyjnego hosta, które mogą mieć wpływ na maszynie Wirtualnej są kolejkowane w górę jako zdarzenia, które określają wpływ, jak i czasu, jaką konserwacji zostanie wykonana, jeśli nie podjęto żadnej akcji. Planowanie zdarzeń są kolejkowane również, gdy platforma Azure wykryje awaria sprzętowa bezpośrednie, która może mieć wpływ na Twoje maszyny Wirtualnej, co pozwala określić, kiedy korygujący powinno być przeprowadzane. Klienci mogą użyć zdarzenia do wykonywania zadań, przed konserwacji, takich jak zapisywanie, przechodzenie w tryb failover do regionu pomocniczego i tak dalej. Po wykonaniu logikę do poprawnego działania obsługi zdarzeń związanych z konserwacją, należy zatwierdzić oczekujące zaplanowane zdarzenie umożliwia platformy kontynuować wykonywanie konserwacji.

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Konfigurowanie każdej warstwy aplikacji w osobnych zestawach dostępności
Jeśli wszystkie maszyny wirtualne są niemal identyczne i służą temu samemu celowi związanemu z aplikacją, zalecamy skonfigurowanie zestawu dostępności dla każdej warstwy aplikacji.  Jeśli umieścisz dwie różne warstwy w tym samym zestawie dostępności, wszystkie maszyny wirtualne w tej samej warstwie aplikacji będzie można jednocześnie uruchomić ponownie. Skonfigurowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności dla każdej warstwy gwarantuje, że co najmniej jedna maszyna wirtualna w każdej warstwie jest dostępna.

Możesz na przykład umieścić wszystkie maszyny wirtualne w frontonie aplikacji uruchamiającej usługi IIS, Apache i Nginx w pojedynczym zestawie dostępności. Upewnij się, że tylko maszyny wirtualne frontonu są umieszczane w tym samym zestawie dostępności. Upewnij się również, że tylko maszyny wirtualne warstwy danych, takie jak replikowane maszyny wirtualne programu SQL Server lub maszyny wirtualne języka MySQL, są umieszczane we własnym zestawie dostępności.

<!--Image reference-->
   ![Warstwy aplikacji](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Łączenie modułu równoważenia obciążenia z zestawami dostępności
Połącz usługę [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) z zestawem dostępności, aby uzyskać najlepszą odporność aplikacji. Usługa Azure Load Balancer dystrybuuje ruch między wieloma maszynami wirtualnymi. W przypadku naszych maszyn wirtualnych w warstwie Standardowa usługa Azure Load Balancer jest uwzględniana. Nie wszystkie warstwy maszyn wirtualnych obejmują usługę Azure Load Balancer. Aby uzyskać więcej informacji na temat równoważenia obciążenia maszyn wirtualnych, zobacz [Load Balancing virtual machines](../articles/virtual-machines/virtual-machines-linux-load-balance.md) (Równoważenie obciążenia maszyn wirtualnych).

Jeśli moduł równoważenia obciążenia nie został skonfigurowany do równoważenia ruchu między wieloma maszynami wirtualnymi, każde zdarzenie planowanej konserwacji wpływa tylko na maszynę wirtualną obsługującą ruch, co powoduje awarię w warstwie aplikacji. Umieszczenie wielu maszyn wirtualnych z tej samej warstwy w tym samym module równoważenia obciążenia i zestawie dostępności umożliwia stałą obsługę ruchu przez co najmniej jedno wystąpienie.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Strefy dostępności umożliwiają ochronę przed awariami na poziomie centrum danych

[Strefy dostępności](../articles/availability-zones/az-overview.md), ustawia alternatywa dostępności, rozwiń poziom kontroli trzeba utrzymywać dostępność aplikacji i danych na maszynach wirtualnych. Strefa dostępności to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Istnieją trzy strefy dostępności na obsługiwany region platformy Azure. Każda strefa dostępności ma oddzielne zasilania źródła, sieć i chłodzenie i jest logicznie oddzielone od innych strefach dostępności w obrębie regionu świadczenia usługi Azure. Za projektowanie rozwiązania do użycia replikowane maszyny wirtualne w strefach, można chronić aplikacje i dane z utraty możliwości Centrum danych. W przypadku naruszenia zabezpieczeń jedną strefę następnie replikowane aplikacje i dane są natychmiast dostępne w innej strefie. 

![Strefy dostępności](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Dowiedz się więcej o wdrażaniu [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) lub [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) maszyny Wirtualnej w strefie dostępności.


<!-- Link references -->
[Konfigurowanie wielu maszyn wirtualnych w zestawie dostępności w celu zapewnienia nadmiarowości]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurowanie każdej warstwy aplikacji w osobnych zestawach dostępności]: #configure-each-application-tier-into-separate-availability-sets
[Łączenie modułu równoważenia obciążenia z zestawami dostępności]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności]: #use-managed-disks-for-vms-in-an-availability-set
[Strefy dostępności umożliwiają ochronę przed awariami na poziomie centrum danych]: #use-availability-zones-to-protect-from-datacenter-level-failures
