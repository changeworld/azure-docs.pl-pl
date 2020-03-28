---
title: Przygotuj się na odzyskiwanie po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak przygotować lokalne maszyny wirtualne funkcji Hyper V do odzyskiwania po awarii na platformie Azure za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b36e4688ebd6e929a56869a6bb191d98b2f1f432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239840"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Przygotowywanie lokalnych serwerów funkcji Hyper-V do odzyskiwania po awarii do platformy Azure

W tym artykule opisano sposób przygotowania lokalnej infrastruktury funkcji Hyper-V, gdy chcesz skonfigurować odzyskiwanie po awarii funkcji Hyper-V na platformie Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)


Jest to drugi samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych z programem Hyper-V. W pierwszym samouczku firma Microsoft [skonfigurować składniki platformy Azure](tutorial-prepare-azure.md) potrzebne do odzyskiwania po awarii funkcji Hyper-V.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przejrzyj wymagania funkcji Hyper-V i programu VMM, jeśli hosty funkcji Hyper-V są zarządzane przez program System Center VMM.
> * Przygotuj vmm, jeśli dotyczy.
> * Weryfikuj dostęp do Internetu do lokalizacji platformy Azure.
> * Przygotuj maszyny wirtualne, aby uzyskać do nich dostęp po przewijaniu awaryjnym na platformie Azure.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułem w sekcji Jak to zrobić w spisie treści odzyskiwania witryny.

## <a name="before-you-start"></a>Przed rozpoczęciem

Upewnij się, że została przygotowana platforma Azure zgodnie z [opisem w pierwszym samouczku z tej serii.](tutorial-prepare-azure.md)

## <a name="review-requirements-and-prerequisites"></a>Przeglądanie wymagań i wymagań wstępnych

Upewnij się, że hosty funkcji Hyper-V i maszyny wirtualne spełniają wymagania.

1. [Sprawdź](hyper-v-azure-support-matrix.md#on-premises-servers) wymagania serwera lokalnego.
2. [Sprawdź wymagania dotyczące](hyper-v-azure-support-matrix.md#replicated-vms) maszyn wirtualnych funkcji Hyper-V, które chcesz replikować na platformie Azure.
3. Sprawdź sieć [hostów](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)funkcji Hyper-V; oraz obsługa magazynu hosta i [gościa](hyper-v-azure-support-matrix.md#hyper-v-host-storage) dla lokalnych hostów funkcji Hyper-V.
4. Sprawdź obsługę [sieci](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [magazynu](hyper-v-azure-support-matrix.md#azure-storage) i [usług obliczeniowych](hyper-v-azure-support-matrix.md#azure-compute-features) platformy Azure po przejściu do trybu failover.
5. Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać [wymagania dotyczące maszyn wirtualnych platformy Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Przygotowanie programu VMM (opcjonalnie)

Jeśli hosty funkcji Hyper-V są zarządzane przez program VMM, należy przygotować lokalny serwer programu VMM. 

- Upewnij się, że serwer programu VMM ma co najmniej jedną chmurę z co najmniej jedną grupą hostów. Host funkcji Hyper-V, na którym są uruchomione maszyny wirtualne, powinien znajdować się w chmurze.
- Przygotuj serwer programu VMM do mapowania sieci.

### <a name="prepare-vmm-for-network-mapping"></a>Przygotowanie programu VMM do mapowania sieci

Jeśli używasz programu VMM, [mapowanie sieci](site-recovery-network-mapping.md) mapuje między lokalnymi sieciami maszyn wirtualnych programu VMM i sieciami wirtualnymi platformy Azure. Mapowanie zapewnia, że maszyny wirtualne platformy Azure są połączone z odpowiednią siecią, gdy są tworzone po przełączeniu awaryjnym.

Przygotuj programu VMM do mapowania sieci w następujący sposób:

1. Upewnij się, że masz [sieć logiczną programu VMM](https://docs.microsoft.com/system-center/vmm/network-logical) skojarzoną z chmurą, w której znajdują się hosty funkcji Hyper-V.
2. Upewnij się, że [masz sieć maszyn wirtualnych](https://docs.microsoft.com/system-center/vmm/network-virtual) połączony z siecią logiczną.
3. W programie VMM połącz maszyny wirtualne z siecią maszyn wirtualnych.

## <a name="verify-internet-access"></a>Weryfikowanie dostępu do Internetu

1. Na potrzeby samouczka najprostszą konfiguracją jest to, aby hosty funkcji Hyper-V i serwer programu VMM miały bezpośredni dostęp do Internetu bez użycia serwera proxy. 
2. Upewnij się, że hosty funkcji Hyper-V i serwer programu VMM w stosownych przypadkach mogą uzyskać dostęp do wymaganych adresów URL poniżej.   
3. Jeśli kontrolujesz dostęp według adresu IP, upewnij się, że:
    - Reguły zapory oparte na adresie IP mogą łączyć się z [zakresami adresów IP usługi Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653)i portem HTTPS (443).
    - Zezwalaj na zakresy adresów IP dla regionu platformy Azure subskrypcji.
    
### <a name="required-urls"></a>Wymagane adresy URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

W scenariuszu pracy awaryjnej można połączyć się z replikowaną siecią lokalną.

Aby połączyć się z maszynami wirtualnymi systemu Windows przy użyciu protokołu RDP po przełączeniu w ten sposób, zezwalaj na dostęp w następujący sposób:

1. Aby uzyskać dostęp przez Internet, włącz protokół RDP na lokalnej maszynie wirtualnej przed włączeniem trybu failover. Upewnij się, że reguły TCP i UDP są dodawane dla profilu **publicznego** i że protokół RDP jest dozwolony w**aplikacjach dozwolonych** **zapory** > systemu Windows dla wszystkich profili.
2. Aby uzyskać dostęp za pośrednictwem połączenia VPN typu lokacja-lokacja, włącz protokół RDP na maszynie lokalnej. RDP powinno być dozwolone w **zaporze** -> systemu Windows**Dozwolone aplikacje i funkcje** dla **sieci domeny i sieci prywatnych.**
   Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135). Podczas wyzwalania trybu failover na maszynie wirtualnej nie powinno być żadnych oczekujących aktualizacji systemu Windows. Jeśli tak, nie będzie można zalogować się do maszyny wirtualnej, dopóki aktualizacja zostanie zakończona.
3. Na maszynie wirtualnej platformy Azure z systemem Windows po przejściu do trybu failover sprawdź **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli nie możesz się połączyć, upewnij się, że maszyna wirtualna jest uruchomiona, i przejrzyj te [porady dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Po przemienniu awaryjnym można uzyskać dostęp do maszyn wirtualnych platformy Azure przy użyciu tego samego adresu IP, co replikowana lokalna maszyna wirtualna lub inny adres IP. [Dowiedz się więcej](concepts-on-premises-to-azure-networking.md) o konfigurowaniu adresowania IP w celu pracy awaryjnej.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych z programem](tutorial-hyper-v-to-azure.md)
> Hyper-V[Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM](tutorial-hyper-v-vmm-to-azure.md)
