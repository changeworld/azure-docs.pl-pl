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
ms.openlocfilehash: a4140ffc0d4e97afabb1c3080951eeb75c792a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76961451"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Omówienie ponownych rozruchów maszyn wirtualnych — konserwacja a przestój
Istnieją trzy scenariusze, które mogą prowadzić do wpływu na maszynę wirtualną na platformie Azure: nieplanowana konserwacja sprzętu, nieoczekiwane przestoje i planowana konserwacja.

* **Zdarzenie nieplanowanej konserwacji sprzętu** ma miejsce, gdy platforma Azure przewidzi zbliżającą się awarię sprzętu lub dowolnego składnika platformy powiązanego z maszyną fizyczną. Gdy platforma przewidzi awarię, wygeneruje zdarzenie nieplanowanej konserwacji sprzętu, aby zmniejszyć wpływ tej awarii na maszyny wirtualne hostowane na tym sprzęcie. Platforma Azure używa technologii [migracji na żywo](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) do migracji maszyn wirtualnych z nieudanego sprzętu na sprawną maszynę fizyczną. Migracja na żywo to operacja zachowywania maszyny wirtualnej, która jedynie wstrzymuje maszynę wirtualną na krótki czas. Pamięć, otwarte pliki i połączenia sieciowe są utrzymywane, ale wydajność przed i/lub po zdarzeniu może zostać ograniczona. W przypadkach, gdy nie można użyć migracji na żywo, maszyna wirtualna doświadczy nieoczekiwanego przestoju zgodnie z opisem poniżej.


* **Nieoczekiwany przestój** jest, gdy sprzęt lub infrastruktura fizyczna maszyny wirtualnej nieoczekiwanie nie powiedzie się. Może to obejmować awarie sieci lokalnej, awarie dysków lokalnych lub inne awarie na poziomie stelaża. Po wykryciu platforma Azure automatycznie migruje (leczy) maszynę wirtualną do dobrej kondycji komputera fizycznego w tym samym centrum danych. Podczas wykonywania procedury naprawiania maszyny wirtualne doświadczają przestoju (ponownego rozruchu), a w niektórych przypadkach dochodzi do utraty dysku tymczasowego. Dołączone dyski systemu operacyjnego i danych są zawsze zachowywane.

  Maszyny wirtualne mogą również wystąpić przestoje w mało prawdopodobnym przypadku awarii lub awarii, która wpływa na całe centrum danych, a nawet całego regionu. W tych scenariuszach platforma Azure udostępnia opcje ochrony, w tym [strefy dostępności](../articles/availability-zones/az-overview.md) i [sparowane regiony.](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)

* **Zdarzenia planowanej konserwacji** są okresowymi aktualizacjami wprowadzonymi przez firmę Microsoft na podstawowej platformie Azure w celu zwiększenia ogólnej niezawodności, wydajności i zabezpieczeń infrastruktury platformy, na których działają maszyny wirtualne. Większość tych aktualizacji jest przeprowadzana bez żadnego wpływu na działanie maszyn wirtualnych ani usług w chmurze (zobacz [konserwacja zachowująca maszyny wirtualne](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Platforma Azure próbuje przeprowadzać konserwację zachowującą maszynę wirtualną przy wszystkich możliwych okazjach, w rzadkich przypadkach jednak aktualizacje wymagają ponownego rozruchu w celu zaktualizowania infrastruktury podstawowej. W takiej sytuacji można przeprowadzić planowaną konserwację platformy Azure z operacją ponownego wdrożenia po konserwacji, inicjując konserwację maszyn wirtualnych w odpowiednim przedziale czasu. Aby uzyskać więcej informacji, zobacz [Planned maintenance for virtual machines in Azure (Planowana konserwacja maszyn wirtualnych na platformie Azure)](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Aby zmniejszyć wpływ przestoju spowodowanego co najmniej jednym z tych zdarzeń, zalecamy zastosowanie następujących najlepszych rozwiązań zapewniających wysoką dostępność maszyn wirtualnych:

* [Konfigurowanie wielu maszyn wirtualnych w zestawie dostępności w celu zapewnienia nadmiarowości]
* [Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności]
* [Używanie zaplanowanych zdarzeń do proaktywnej reakcji na zdarzenia wpływające na maszynę wirtualną](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurowanie każdej warstwy aplikacji w osobnych zestawach dostępności]
* [Łączenie modułu równoważenia obciążenia z zestawami dostępności]
* [Użyj stref dostępności, aby chronić się przed awariami na poziomie centrum danych]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Użyj stref dostępności, aby chronić się przed awariami na poziomie centrum danych

[Strefy dostępności](../articles/availability-zones/az-overview.md) rozszerzają poziom kontroli, który należy zachować dostępność aplikacji i danych na maszynach wirtualnych. Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Aby zapewnić odporność, istnieją co najmniej trzy oddzielne strefy we wszystkich włączonych regionach. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych. Usługi nadmiarowe strefowe replikują aplikacje i dane w strefach dostępności, aby chronić je przed pojedynczymi punktami awarii.

Strefa dostępności w regionie platformy Azure to połączenie **domeny błędów** i **domeny aktualizacji.** Jeśli na przykład utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie świadczenia usługi Azure, maszyny wirtualne będą rozproszone w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

Dzięki strefom dostępności platforma Azure oferuje najlepszą w branży umowę dotycząca poziomu usług (SLA) gwarantującą czas działania na poziomie 99,99%. Projektując rozwiązania do używania replikowanych maszyn wirtualnych w strefach, można chronić aplikacje i dane przed utratą centrum danych. Jeśli jedna strefa zostanie naruszona, zreplikowane aplikacje i dane są natychmiast dostępne w innej strefie.

![Strefy dostępności](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Dowiedz się więcej o wdrażaniu maszyny Wirtualnej [systemu Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) lub [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) w strefie dostępności.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurowanie wielu maszyn wirtualnych w zestawie dostępności w celu zapewnienia nadmiarowości
Zestawy dostępności to kolejna konfiguracja centrum danych zapewniająca nadmiarowość i dostępność maszyn wirtualnych. Ta konfiguracja w centrum danych zapewnia, że podczas zdarzenia planowanej lub nieplanowanej konserwacji dostępna jest co najmniej jedna maszyna wirtualna i spełnia 99,95% umowy SLA platformy Azure. Aby uzyskać więcej informacji, zobacz [Virtual Machines — umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Maszyna wirtualna pojedynczego wystąpienia w zestawie dostępności powinna używać dysków SSD premium lub ultradla wszystkich dysków systemu operacyjnego i dysków z danymi w celu zakwalifikowania się do umowy SLA dla łączności maszyny wirtualnej o wydajności co najmniej 99,9%.

Każda maszyna wirtualna w zestawie dostępności ma przypisaną **domenę aktualizacji** i **domenę błędów** z odpowiedniej platformy Azure. Dany zestaw dostępności ma pięć domyślnie przypisanych domen aktualizacji, których użytkownik nie może konfigurować (następnie można zwiększyć liczbę wystąpień usługi Resource Manager, aby oferowała do 20 domen aktualizacji). Umożliwia to wskazanie grup maszyn wirtualnych i odpowiedniego sprzętu fizycznego, które mogą być uruchamiane równocześnie. Jeśli w pojedynczym zestawie dostępności skonfigurowano więcej niż pięć maszyn wirtualnych, szósta maszyna wirtualna jest umieszczana w tej samej domenie aktualizacji, co pierwsza maszyna wirtualna, siódma — w tej samej domenie aktualizacji co druga maszyna wirtualna itd. Podczas planowanej konserwacji domeny aktualizacji mogą nie być ponownie uruchamiane kolejno, ale w danym momencie tylko jedna domena aktualizacji jest uruchamiana ponownie. Domena aktualizacji po ponownym rozruchu otrzymuje 30 minut na odzyskanie sprawności zanim konserwacja zostanie zainicjowana w innej domenie aktualizacji.

Domeny błędów definiują grupę maszyn wirtualnych, które korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Domyślnie maszyny wirtualne skonfigurowane w zestawie dostępności są rozdzielane między maksymalnie trzy domeny błędów w przypadku wdrożeń usługi Resource Manager (dwie domeny błędów w przypadku modelu klasycznego). Umieszczenie maszyn wirtualnych w zestawie dostępności nie chroni aplikacji przed błędami związanymi z systemem operacyjnym lub daną aplikacją, ale ogranicza wpływ potencjalnych awarii sprzętu fizycznego, awarii sieci i przerw w dostawie prądu.

<!--Image reference-->
   ![Rysunek koncepcyjny przedstawiający konfigurację domeny aktualizacji i domeny błędów](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności
Jeśli obecnie korzystasz z maszyn wirtualnych z dyskami niezarządzanymi, zdecydowanie zalecamy [konwersję maszyn wirtualnych w zestawie dostępności do korzystania z dysków zarządzanych](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Dyski zarządzane](../articles/virtual-machines/windows/managed-disks-overview.md) zapewniają większą niezawodność zestawów dostępności przez zagwarantowanie, że dyski maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczych punktów awarii. W tym celu automatycznie umieszcza dyski w różnych domenach błędów magazynu (klastry magazynu) i wyrównuje je z domeną błędów maszyny Wirtualnej. Jeśli domena błędów magazynu nie powiedzie się z powodu awarii sprzętu lub oprogramowania, tylko wystąpienie maszyny Wirtualnej z dyskami w domenie błędów magazynu nie powiedzie się.
![Dyski zarządzane FDs](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Liczba domen błędów dla zarządzanych zestawów dostępności jest zależna od regionu i może wynosić dwa lub trzy na region. Domenę błędów dla każdego regionu można wyświetlić, uruchamiając następujące skrypty.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> W pewnych okolicznościach 2 maszyny wirtualne w tym samym availabilityset może współużytkuje tę samą faultdomain. Można to potwierdzić, przechodząc do zestawu dostępności i sprawdzając kolumnę **Domena błędów.**
> Może to być przyczyną z następującej sekwencji podczas wdrażania maszyn wirtualnych:
> - Wdrażanie 1 maszyny Wirtualnej
> - Zatrzymaj/przydziel lokalizację 1 maszyny Wirtualnej
> - Wdrożenie drugiej maszyny Wirtualnej W tych okolicznościach dysk systemu operacyjnego drugiej maszyny Wirtualnej może zostać utworzony na tej samej domenie błędów co pierwsza maszyna wirtualna, a więc druga maszyna wirtualna również wyląduje na tej samej domenie FaultDomain. 
> Aby uniknąć tego problemu, zaleca się, aby nie zatrzymać/rozwiązać alokacji maszyn wirtualnych między wdrożeniami.

Jeśli planujesz używać maszyn wirtualnych z dyskami niezarządzanymi, postępuj zgodnie z poniższymi najlepszymi rozwiązaniami dla kont magazynu, na których wirtualne dyski twarde (VHD) maszyn wirtualnych są przechowywane jako [obiekty blob stron](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Obsługuj wszystkie dyski (systemu operacyjnego i danych) skojarzone z maszyną wirtualną na tym samym koncie magazynu.**
2. **Przejrzyj [limity](../articles/storage/blobs/scalability-targets-premium-page-blobs.md) liczby dysków niezarządzanych na koncie usługi Azure Storage** przed dodaniem większej liczby dysków VHD do konta magazynu
3. **Użyj osobnego konta magazynu dla każdej maszyny Wirtualnej w zestawie dostępności.** Nie stosuj współużytkowania kont magazynu przez wiele maszyn wirtualnych w tym samym zestawie dostępności. Jest dopuszczalne dla maszyn wirtualnych w różnych zestawów dostępności do ![udostępniania kont magazynu, jeśli powyższe najlepsze rozwiązania są przestrzegane dysków niezarządzanych FDs](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Używanie zaplanowanych zdarzeń do proaktywnego reagowania na zdarzenia wpływające na maszynę wirtualną

Podczas subskrybowania [zaplanowanych zdarzeń](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)maszyna wirtualna jest powiadamiana o nadchodzących zdarzeniach konserwacji, które mogą mieć wpływ na maszynę wirtualną. Po włączeniu zaplanowanych zdarzeń maszyna wirtualna otrzymuje minimalną ilość czasu przed wykonaniem czynności konserwacji. Na przykład aktualizacje systemu operacyjnego hosta, które mogą mieć wpływ na maszynę wirtualną są umieszczane w kolejce jako zdarzenia, które określają wpływ, a także czas, w którym konserwacja zostanie wykonana, jeśli nie zostanie podjęta żadna akcja. Planowanie zdarzeń są również umieszczane w kolejce, gdy platforma Azure wykryje nieuchronne awarie sprzętu, które mogą mieć wpływ na maszynę wirtualną, co pozwala zdecydować, kiedy należy wykonać leczenie. Klienci mogą używać zdarzenia do wykonywania zadań przed konserwacją, takich jak zapisywanie stanu, w pełni do pomocniczego i tak dalej. Po zakończeniu logiki do wdzięku obsługi zdarzenia konserwacji, można zatwierdzić zaległe zaplanowane zdarzenie, aby umożliwić platformie, aby kontynuować konserwację.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Konfigurowanie każdej warstwy aplikacji do oddzielnych stref dostępności lub zestawów dostępności
Jeśli wszystkie maszyny wirtualne są prawie identyczne i służą temu samemu celowi dla aplikacji, zaleca się skonfigurowanie strefy dostępności lub zestawu dostępności dla każdej warstwy aplikacji.  Jeśli umieścisz dwie różne warstwy w tej samej strefie dostępności lub zestawie, wszystkie maszyny wirtualne w tej samej warstwie aplikacji można ponownie uruchomić jednocześnie. Konfigurując co najmniej dwie maszyny wirtualne w strefie dostępności lub ustawić dla każdej warstwy, można zagwarantować, że co najmniej jedna maszyna wirtualna w każdej warstwie jest dostępna.

Na przykład można umieścić wszystkie maszyny wirtualne w przedniej części aplikacji z uruchomionymi usługami IIS, Apache i Nginx w jednej strefie dostępności lub zestawie. Upewnij się, że tylko maszyny wirtualne frontonu są umieszczane w tej samej strefie dostępności lub ustawione. Podobnie upewnij się, że tylko maszyny wirtualne warstwy danych są umieszczane we własnej strefie dostępności lub ustawione, takie jak replikowane maszyny wirtualne programu SQL Server lub maszyny wirtualne MySQL.

<!--Image reference-->
   ![Warstwy aplikacji](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Łączenie modułu równoważenia obciążenia ze strefami lub zestawami dostępności
Połącz [moduł równoważenia obciążenia platformy Azure](../articles/load-balancer/load-balancer-overview.md) ze strefą dostępności lub ustaw, aby uzyskać jak największą odporność aplikacji. Usługa Azure Load Balancer dystrybuuje ruch między wieloma maszynami wirtualnymi. W przypadku naszych maszyn wirtualnych w warstwie Standardowa usługa Azure Load Balancer jest uwzględniana. Nie wszystkie warstwy maszyn wirtualnych obejmują usługę Azure Load Balancer. Aby uzyskać więcej informacji na temat równoważenia obciążenia maszyn wirtualnych, zobacz [Load Balancing virtual machines](../articles/virtual-machines/virtual-machines-linux-load-balance.md) (Równoważenie obciążenia maszyn wirtualnych).

Jeśli moduł równoważenia obciążenia nie został skonfigurowany do równoważenia ruchu między wieloma maszynami wirtualnymi, każde zdarzenie planowanej konserwacji wpływa tylko na maszynę wirtualną obsługującą ruch, co powoduje awarię w warstwie aplikacji. Umieszczenie wielu maszyn wirtualnych z tej samej warstwy w tym samym module równoważenia obciążenia i zestawie dostępności umożliwia stałą obsługę ruchu przez co najmniej jedno wystąpienie.

Aby zapoznać się z samouczkiem na temat równoważenia obciążenia w strefach dostępności, zobacz [Równoważenie obciążenia maszyn wirtualnych we wszystkich strefach dostępności przy użyciu interfejsu wiersza polecenia platformy Azure](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md).


<!-- Link references -->
[Konfigurowanie wielu maszyn wirtualnych w zestawie dostępności w celu zapewnienia nadmiarowości]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurowanie każdej warstwy aplikacji w osobnych zestawach dostępności]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Łączenie modułu równoważenia obciążenia z zestawami dostępności]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności]: #use-managed-disks-for-vms-in-an-availability-set
[Użyj stref dostępności, aby chronić się przed awariami na poziomie centrum danych]: #use-availability-zones-to-protect-from-datacenter-level-failures
