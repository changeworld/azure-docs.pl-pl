---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76749004"
---
W tym artykule przedstawiono omówienie funkcji dostępności maszyn wirtualnych platformy Azure (maszyn wirtualnych).

## <a name="high-availability"></a>Wysoka dostępność

Obciążenia są zazwyczaj rozłożone na różnych maszynach wirtualnych, aby uzyskać wysoką przepływność, wydajność i utworzyć nadmiarowość w przypadku, gdy maszyna wirtualna ma wpływ na aktualizację lub inne zdarzenie. 

Istnieje kilka opcji, które platforma Azure zapewnia w celu osiągnięcia wysokiej dostępności. Najpierw porozmawiajmy o podstawowych konstrukcjach. 

### <a name="availability-zones"></a>Strefy dostępności

[Strefy dostępności](../articles/availability-zones/az-overview.md) rozszerzają poziom kontroli, który należy zachować dostępność aplikacji i danych na maszynach wirtualnych. Strefa dostępności jest fizycznie oddzielną strefą w obrębie regionu platformy Azure. Istnieją trzy strefy dostępności dla obsługiwanego regionu platformy Azure. 

Każda strefa dostępności ma oddzielne źródło zasilania, sieć i chłodzenie. Projektując rozwiązania do używania replikowanych maszyn wirtualnych w strefach, można chronić aplikacje i dane przed utratą centrum danych. Jeśli jedna strefa zostanie naruszona, zreplikowane aplikacje i dane są natychmiast dostępne w innej strefie. 

![Strefy dostępności](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Dowiedz się więcej o wdrażaniu maszyny Wirtualnej [systemu Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) lub [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) w strefie dostępności.


### <a name="fault-domains"></a>Domeny błędów

Domena błędów to logiczne grupowanie odpowiednich elementów sprzętu, które współdzielą źródła zasilania i przełącznik sieciowy, podobnie jak w przypadku regału w lokalnym centrum danych. 

### <a name="update-domains"></a>Domeny aktualizacji

Domena aktualizacji to logiczne grupowanie odpowiednich elementów sprzętu, które mogą być w tym samym czasie poddawane konserwacji lub ponownie uruchamiane. 

To podejście zapewnia, że zawsze działa co najmniej jedno wystąpienie aplikacji, gdy platforma Azure jest poddawana okresowej konserwacji. Kolejność ponownego uruchamiania domen aktualizacji może nie przebiegać sekwencyjnie podczas konserwacji, ale tylko jedna domena aktualizacji jest ponownie uruchamiana jednocześnie.


## <a name="virtual-machines-scale-sets"></a>Zestawy skalowania maszyn wirtualnych 

Zestawy skalowania maszyny wirtualnej platformy Azure umożliwiają tworzenie grup maszyn wirtualnych z równoważenia obciążenia i zarządzanie nimi. Liczba wystąpień maszyn wirtualnych może automatycznie zwiększać lub zmniejszać się w reakcji na zapotrzebowanie lub zdefiniowany harmonogram. Zestawy skalowania zapewniają wysoką dostępność aplikacji i umożliwiają centralne zarządzanie, konfigurowanie i aktualizowanie wielu maszyn wirtualnych. Firma Microsoft zaleca, aby dwie lub więcej maszyn wirtualnych były tworzone w ramach zestawu skalowania, aby zapewnić wysoką dostępną aplikację i spełnić [99,95% umowy SLA platformy Azure.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Nie ma żadnych kosztów dla samego zestawu skalowania, płacisz tylko za każde wystąpienie maszyny Wirtualnej, które tworzysz. Gdy pojedyncza maszyna wirtualna używa [ssd w wersji Premium platformy Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)usługa Azure SLA ma zastosowanie do nieplanowanych zdarzeń konserwacji. Maszyny wirtualne w zestawie skalowania można wdrożyć w wielu domenach aktualizacji i domenach błędów, aby zmaksymalizować dostępność i odporność na awarie spowodowane awariami centrum danych oraz planowanymi lub nieplanowanymi zdarzeniami konserwacji. Maszyny wirtualne w zestawie skalowania można również wdrożyć w jednej strefie dostępności lub regionalnie. Opcje wdrażania strefy dostępności mogą się różnić w zależności od trybu aranżacji.

### <a name="preview-orchestration-mode-preview"></a>Podgląd: Podgląd trybu aranżacji
Zestawy skalowania maszyn wirtualnych umożliwiają określenie trybu aranżacji.  Za pomocą trybu aranżacji zestawu skalowania maszyny wirtualnej (wersja zapoznawcza) można teraz wybrać, czy zestaw skalowania powinien aranżować maszyny wirtualne, które są tworzone jawnie poza modelem konfiguracji zestawu skalowania, czy wystąpienia maszyn wirtualnych utworzone niejawnie w oparciu o model konfiguracji. Wybierz tryb aranżacji, który model aranżacji maszyny wirtualnej umożliwia grupowanie jawnie zdefiniowanych maszyn wirtualnych razem w regionie lub w strefie dostępności. Maszyny wirtualne wdrożone w strefie dostępności zapewnia strefową izolację maszyn wirtualnych, są one powiązane z granicą strefy dostępności i nie są narażone na żadne błędy, które mogą wystąpić w innej strefie dostępności w regionie. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Model konfiguracji maszyny Wirtualnej| Brak. VirtualMachineProfile jest niezdefiniowany w modelu zestawu skalowania. | Wymagany. VirtualMachineProfile jest wypełniona w modelu zestawu skalowania. |
| Dodawanie nowej maszyny Wirtualnej do zestawu skalowania| Maszyny wirtualne są jawnie dodawane do zestawu skalowania podczas tworzenia maszyny wirtualnej. | Maszyny wirtualne są niejawnie tworzone i dodawane do zestawu skalowania na podstawie modelu konfiguracji maszyny Wirtualnej, liczby wystąpień i reguł skalowania automatycznego. |
| Strefy dostępności| Obsługuje wdrażanie regionalne lub maszyny wirtualne w jednej strefie dostępności| Obsługuje wdrażanie regionalne lub wiele stref dostępności; Może definiować strategię równoważenia stref |
| Domeny błędów| Można zdefiniować liczbę domen błędów. 2 lub 3 w oparciu o wsparcie regionalne i 5 dla strefy dostępności. Przypisana domena błędów maszyny Wirtualnej będzie zachowywać się z cyklem życia maszyny Wirtualnej, w tym przydzielić i ponownie uruchomić. | Można zdefiniować 1, 2 lub 3 domeny błędów dla wdrożeń niestonalnych i 5 dla wdrożeń strefy dostępności. Przypisana domena błędów maszyny Wirtualnej nie jest zachowywana z cyklem życia maszyny wirtualnej, maszyny wirtualne są przypisywane domenie błędów w czasie alokacji. |
| Domeny aktualizacji| Nie dotyczy. Domeny aktualizacji są automatycznie mapowane na domeny błędów| Nie dotyczy. Domeny aktualizacji są automatycznie mapowane na domeny błędów |

**Domeny błędów i domeny aktualizacji**

Zestawy skalowania maszyny wirtualnej upraszczają projektowanie dla wysokiej dostępności, dostosowując domeny błędów i aktualizują domeny. Będziesz musiał zdefiniować tylko liczbę domen błędów dla zestawu skalowania. Liczba domen błędów dostępnych dla zestawów skalowania może się różnić w zależności od regionu. Zobacz [Zarządzanie dostępnością maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).


## <a name="availability-sets"></a>Zestawy dostępności
Zestaw dostępności to logiczne grupowanie maszyn wirtualnych w centrum danych, które umożliwia platformie Azure zrozumienie sposobu tworzenia aplikacji w celu zapewnienia nadmiarowości i dostępności. Firma Microsoft zaleca, aby dwie lub więcej maszyn wirtualnych były tworzone w ramach zestawu dostępności, aby zapewnić wysoką dostępną aplikację i spełnić [99,95% umowy SLA platformy Azure.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Nie ma żadnych kosztów dla zestawu dostępności, płacisz tylko za każde wystąpienie maszyny Wirtualnej, które tworzysz. Gdy pojedyncza maszyna wirtualna używa [ssd w wersji Premium platformy Azure,](../articles/virtual-machines/windows/disks-types.md#premium-ssd)usługa Azure SLA ma zastosowanie do nieplanowanych zdarzeń konserwacji.

W zestawie dostępności maszyny wirtualne są automatycznie dystrybuowane między tymi domenami błędów. To podejście ogranicza wpływ potencjalnych awarii sprzętu fizycznego, przestojów sieci lub przerw w dostawie prądu.

Maszyny wirtualne korzystające z usługi [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) są przydzielane do domen błędów dysków zarządzanych w przypadku korzystania z zarządzanego zestawu dostępności. Dzięki takiemu dopasowaniu wszystkie dyski zarządzane dołączone do maszyny wirtualnej działają w tej samej domenie błędów dysku zarządzanego. 

W zarządzanym zestawie dostępności można tworzyć tylko maszyny wirtualne z użyciem dysków zarządzanych. Liczba domen błędów dysku zarządzanego zależy od regionu — dwie lub trzy domeny błędów dysku zarządzanego na region. Możesz przeczytać więcej o tych domenach błędów dysku zarządzanego dla [maszyn wirtualnych z systemem Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) lub maszyn [wirtualnych z systemem Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Zestaw dostępności zarządzanej](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Maszyny wirtualne w ramach zestawu dostępności są również automatycznie dystrybuowane między domenami aktualizacji. 

![Zestawy dostępności](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Następne kroki
Możesz teraz rozpocząć korzystanie z tych funkcji dostępności i nadmiarowości podczas kompilowania środowiska platformy Azure. Aby uzyskać informacje o najlepszych rozwiązaniach, zobacz [Azure availability best practices](/azure/architecture/checklist/resiliency-per-service) (Najlepsze rozwiązania dotyczące dostępności platformy Azure).

