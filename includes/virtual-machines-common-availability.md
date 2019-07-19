---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1582f61befb9b6d71adbfda9482175d67874ffb9
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850277"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Opcje dostępności dla maszyn wirtualnych na platformie Azure
Ten artykuł zawiera omówienie funkcji dostępności maszyn wirtualnych platformy Azure.


## <a name="availability-sets"></a>Zestawy dostępności
Zestaw dostępności to logiczna Grupa maszyn wirtualnych w centrum danych, która pozwala platformie Azure zrozumieć, w jaki sposób aplikacja została skompilowana w celu zapewnienia nadmiarowości i dostępności. Zalecamy, aby co najmniej dwie maszyny wirtualne zostały utworzone w ramach zestawu dostępności, aby zapewnić wysoką dostępność aplikacji i spełnić warunki [umowy SLA na 99,95%](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Dla samego zestawu dostępności opłaty są naliczane tylko za każde utworzone wystąpienie maszyny wirtualnej. Jeśli jedna maszyna wirtualna korzysta z [usługi Azure Premium dysków SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd), umowa SLA platformy Azure ma zastosowanie do nieplanowanych zdarzeń konserwacji.

Zestaw dostępności składa się z dwóch dodatkowych grup, które chronią przed awariami sprzętu i umożliwiają bezpieczne stosowanie aktualizacji — domen błędów (domenami błędów) i domen aktualizacji. Zapoznaj się z dodatkowymi informacjami na temat zarządzania dostępnością [maszyn wirtualnych z systemem Linux](../articles/virtual-machines/linux/manage-availability.md) lub [maszyn wirtualnych z systemem Windows](../articles/virtual-machines/windows/manage-availability.md).

### <a name="fault-domains"></a>Domeny błędów
Domena błędów to logiczne grupowanie odpowiednich elementów sprzętu, które współdzielą źródła zasilania i przełącznik sieciowy, podobnie jak w przypadku regału w lokalnym centrum danych. Podczas tworzenia maszyn wirtualnych w zestawie dostępności platforma Azure automatycznie rozdziela maszyny wirtualne między domeny błędów. To podejście ogranicza wpływ potencjalnych awarii sprzętu fizycznego, przestojów sieci lub przerw w dostawie prądu.

### <a name="update-domains"></a>Domeny aktualizacji
Domena aktualizacji to logiczne grupowanie odpowiednich elementów sprzętu, które mogą być w tym samym czasie poddawane konserwacji lub ponownie uruchamiane. Podczas tworzenia maszyn wirtualnych w zestawie dostępności platforma Azure automatycznie rozdziela maszyny wirtualne między domeny aktualizacji. To podejście zapewnia, że zawsze działa co najmniej jedno wystąpienie aplikacji, gdy platforma Azure jest poddawana okresowej konserwacji. Podczas planowanej konserwacji domeny aktualizacji mogą nie być ponownie uruchamiane kolejno, ale w danym momencie tylko jedna domena aktualizacji jest uruchamiana ponownie.

![Zestawy dostępności](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains"></a>Domeny błędów dysku zarządzanego
Maszyny wirtualne korzystające z usługi [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) są przydzielane do domen błędów dysków zarządzanych w przypadku korzystania z zarządzanego zestawu dostępności. Dzięki takiemu dopasowaniu wszystkie dyski zarządzane dołączone do maszyny wirtualnej działają w tej samej domenie błędów dysku zarządzanego. W zarządzanym zestawie dostępności można tworzyć tylko maszyny wirtualne z użyciem dysków zarządzanych. Liczba domen błędów dysku zarządzanego zależy od regionu — dwie lub trzy domeny błędów dysku zarządzanego na region. Więcej informacji o tych domenach błędów dysków zarządzanych można znaleźć na [maszynach wirtualnych systemu Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) lub maszynach wirtualnych z [systemem Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Zarządzany zestaw dostępności](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="availability-zones"></a>Strefy dostępności

[Strefy dostępności](../articles/availability-zones/az-overview.md), alternatywy dla zestawów dostępności, rozszerzają poziom kontroli, aby zachować dostępność aplikacji i danych na maszynach wirtualnych. Strefa dostępności to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Istnieją trzy Strefy dostępności na obsługiwany region platformy Azure. Każda strefa dostępności ma oddzielne źródło zasilania, sieć i chłodzenie. Dzięki zaprojektowaniu rozwiązań do korzystania z replikowanych maszyn wirtualnych w strefach można chronić aplikacje i dane przed utratą centrum danych. W przypadku naruszenia zabezpieczeń jednej strefy zreplikowane aplikacje i dane są natychmiast dostępne w innej strefie. 

![Strefy dostępności](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Dowiedz się więcej o wdrażaniu maszyny wirtualnej z [systemem Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) lub [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) w strefie dostępności.


## <a name="next-steps"></a>Następne kroki
Możesz teraz rozpocząć korzystanie z tych funkcji dostępności i nadmiarowości podczas kompilowania środowiska platformy Azure. Aby uzyskać informacje o najlepszych rozwiązaniach, zobacz [Azure availability best practices](../articles/best-practices-availability-checklist.md) (Najlepsze rozwiązania dotyczące dostępności platformy Azure).

