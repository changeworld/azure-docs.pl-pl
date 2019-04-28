---
title: Przygotowywanie lokalnych serwerów funkcji Hyper-V do odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować lokalne maszyny wirtualne z funkcją Hyper-V do odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6e57b629a0007b06af6e37f96e1466e35afafccc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996540"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Przygotowywanie lokalnych serwerów funkcji Hyper-V do odzyskiwania po awarii na platformie Azure

W tym artykule opisano sposób przygotowania infrastruktury funkcji Hyper-V w środowisku lokalnym skonfigurować odzyskiwanie po awarii maszyny wirtualne funkcji Hyper-v na platformę Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).


Jest to drugi samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V w środowisku lokalnym. W pierwszym samouczku mamy [skonfigurować składniki platformy Azure](tutorial-prepare-azure.md) potrzebne do odzyskiwania po awarii z funkcji Hyper-V.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jeśli hosty funkcji Hyper-V są zarządzane przez program System Center VMM, należy przejrzeć wymagania funkcji Hyper-V i wymagania dotyczące programu VMM.
> * Przygotowywanie programu VMM, jeśli ma to zastosowanie.
> * Sprawdź dostęp do lokalizacji platformy Azure przez internet.
> * Przygotowywanie maszyn wirtualnych, dzięki czemu można z nich korzystać po włączeniu trybu failover na platformie Azure.

> [!NOTE]
> W samouczkach pokazano to najprostsza ścieżka wdrażania scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Szczegółowe informacje na ten temat można znaleźć w artykule w sekcji jak tabeli odzyskiwania lokacji zawartości.

## <a name="before-you-start"></a>Przed rozpoczęciem

Upewnij się, że przygotowanymi Azure zgodnie z opisem w [pierwszym samouczku tej serii](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Przejrzyj wymagania i wymagania wstępne

Upewnij się, że hosty funkcji Hyper-V i maszyny wirtualne spełniają wymagania.

1. [Sprawdź](hyper-v-azure-support-matrix.md#on-premises-servers) wymagania dotyczące serwera lokalnego.
2. [Sprawdź wymagania](hyper-v-azure-support-matrix.md#replicated-vms) dla maszyn wirtualnych funkcji Hyper-V, którą chcesz replikować do platformy Azure.
3. Sprawdź hosta funkcji Hyper-V [sieci](hyper-v-azure-support-matrix.md#hyper-v-network-configuration); i hosta i gościa [magazynu](hyper-v-azure-support-matrix.md#hyper-v-host-storage) obsługi dla hostów funkcji Hyper-V w środowisku lokalnym.
4. Sprawdź obsługę [sieci](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [magazynu](hyper-v-azure-support-matrix.md#azure-storage) i [usług obliczeniowych](hyper-v-azure-support-matrix.md#azure-compute-features) platformy Azure po przejściu do trybu failover.
5. Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać [wymagania dotyczące maszyn wirtualnych platformy Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Przygotowywanie programu VMM (opcjonalnie)

Jeśli hosty funkcji Hyper-V są zarządzane przez program VMM, należy przygotować na lokalnym serwerze programu VMM. 

- Upewnij się, że serwer VMM ma co najmniej jedną chmurę z co najmniej jedną grupę hostów. Hosta funkcji Hyper-V, na którym są uruchomione maszyny wirtualne powinny znajdować się w chmurze.
- Przygotuj serwer programu VMM do mapowania sieci.

### <a name="prepare-vmm-for-network-mapping"></a>Przygotowanie programu VMM do mapowania sieci

Jeśli używasz programu VMM, [mapowania sieci](site-recovery-network-mapping.md) mapowania między sieciami maszyny Wirtualnej VMM lokalnymi i sieciami wirtualnymi platformy Azure. Mapowanie gwarantuje, że maszyny wirtualne platformy Azure są połączone z odpowiednią siecią, po ich utworzeniu po włączeniu trybu failover.

Przygotowywanie programu VMM do mapowania sieci w następujący sposób:

1. Upewnij się, że masz [sieć logiczna programu VMM](https://docs.microsoft.com/system-center/vmm/network-logical) skojarzony z chmury, w którym znajdują się hosty funkcji Hyper-V.
2. Upewnij się, że [sieci maszyny Wirtualnej](https://docs.microsoft.com/system-center/vmm/network-virtual) połączone z siecią logiczną.
3. W programie VMM należy połączyć maszyny wirtualne z siecią maszyny Wirtualnej.

## <a name="verify-internet-access"></a>Sprawdź dostęp do Internetu

1. Na potrzeby tego samouczka najprostsza konfiguracja jest dla hostów funkcji Hyper-V i serwer VMM ma bezpośredni dostęp do Internetu bez użycia serwera proxy. 
2. Należy upewnić się, który jest hostem funkcji Hyper-V i serwer programu VMM, jeśli jest to istotne, mają dostęp do wymaganych poniższych adresów URL.   
3. Jeśli jest kontrolowanie dostępu przy użyciu adresu IP, wykonaj następujące czynności:
    - Reguły zapory oparte na adresie IP mogą łączyć się z [zakresów adresów IP centrum danych Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)oraz portu HTTPS (443).
    - Zezwalaj na zakresy adresów IP dla regionu platformy Azure Twojej subskrypcji.
    
### <a name="required-urls"></a>Wymagane adresy URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Podczas scenariusza trybu failover można połączyć z siecią replikowanych w środowisku lokalnym.

Aby połączyć z Windows maszyn wirtualnych przy użyciu protokołu RDP po włączeniu trybu failover, Zezwalaj na dostęp w następujący sposób:

1. Aby uzyskać dostęp przez Internet, włącz protokół RDP na lokalnej maszynie wirtualnej przed włączeniem trybu failover. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.
2. Aby uzyskać dostęp za pośrednictwem połączenia VPN typu lokacja-lokacja, włącz protokół RDP na maszynie lokalnej. Używanie protokołu RDP powinno być dozwolone w pozycji **Zapora systemu Windows** -> **Dozwolone aplikacje i funkcje** dla sieci typu **Domena i prywatne**.
   Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135). Podczas wyzwalania trybu failover na maszynie wirtualnej nie powinno być żadnych oczekujących aktualizacji systemu Windows. Jeśli, nie można zalogować się do maszyny wirtualnej, do momentu ukończenia aktualizacji.
3. Na maszynie wirtualnej platformy Azure z systemem Windows po przejściu do trybu failover sprawdź **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli nie możesz się połączyć, upewnij się, że maszyna wirtualna jest uruchomiona, i przejrzyj te [porady dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Po przejściu w tryb failover można uzyskać dostęp przy użyciu tego samego adresu IP jako maszyny Wirtualnej zreplikowanej w środowisku lokalnym lub innym adresem IP maszyn wirtualnych platformy Azure. [Dowiedz się więcej](concepts-on-premises-to-azure-networking.md) o konfigurowaniu adresowania IP na potrzeby trybu failover.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V](tutorial-hyper-v-to-azure.md)
> [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM](tutorial-hyper-v-vmm-to-azure.md)
