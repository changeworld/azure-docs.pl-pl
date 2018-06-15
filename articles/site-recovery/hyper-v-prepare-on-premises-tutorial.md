---
title: Przygotowanie serwera funkcji Hyper-V lokalnego odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V do platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować maszyn wirtualnych funkcji Hyper-V lokalnych nie są zarządzane przez program System Center VMM, odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7e0219a662483ef123bdc2889a43dd3d93d23ac2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31413232"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Przygotowywanie serwerów funkcji Hyper-V lokalnych do odzyskiwania awaryjnego na platformie Azure

Ten samouczek pokazuje, jak przygotować o lokalnej infrastrukturze do funkcji Hyper-V umożliwia replikowanie maszyn wirtualnych funkcji Hyper-V do platformy Azure, na potrzeby odzyskiwania po awarii. Hosty funkcji Hyper-V mogą być zarządzane przez System Center Virtual Machine Manager (VMM), ale nie jest to wymagane.  Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przejrzyj wymagania dotyczące funkcji Hyper-V i wymagania dotyczące programu VMM, jeśli ma to zastosowanie.
> * Przygotowanie programu VMM, jeśli to konieczne
> * Sprawdź dostęp do Internetu dla lokalizacji platformy Azure
> * Przygotowywanie maszyn wirtualnych, dzięki czemu będą dostępne po przejściu w tryb failover Azure

Jest to drugi samouczek z tej serii. Upewnij się, że są już [skonfigurowane składniki platformy Azure](tutorial-prepare-azure.md) zgodnie z opisem w poprzednim samouczku.



## <a name="review-requirements-and-prerequisites"></a>Przejrzyj wymagania i wymagania wstępne

Upewnij się, że hosty funkcji Hyper-V i maszyny wirtualne są zgodne z wymaganiami.

1. [Sprawdź](hyper-v-azure-support-matrix.md#on-premises-servers) lokalne wymagania dotyczące serwera.
2. [Sprawdź wymagania](hyper-v-azure-support-matrix.md#replicated-vms) dla maszyn wirtualnych funkcji Hyper-V, którą chcesz replikować do platformy Azure.
3. Sprawdź hosta funkcji Hyper-V [sieci](hyper-v-azure-support-matrix.md#hyper-v-network-configuration); i hosta, jak i gościa [magazynu](hyper-v-azure-support-matrix.md#hyper-v-host-storage) obsługę lokalnymi hostami funkcji Hyper-V.
4. Sprawdź obsługę [sieci](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [magazynu](hyper-v-azure-support-matrix.md#azure-storage) i [usług obliczeniowych](hyper-v-azure-support-matrix.md#azure-compute-features) platformy Azure po przejściu do trybu failover.
5. Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać [wymagania dotyczące maszyn wirtualnych platformy Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Przygotowanie programu VMM (opcjonalnie)

Hosty funkcji Hyper-V są zarządzane przez program VMM, należy przygotować na lokalnym serwerze VMM. 

- Upewnij się, że serwer VMM ma co najmniej jedna chmura z co najmniej jedną grupę hostów. Host funkcji Hyper-V, na którym są uruchomione maszyny wirtualne powinien znajdować się w chmurze.
- Przygotuj serwer programu VMM do mapowania sieci.

### <a name="prepare-vmm-for-network-mapping"></a>Przygotowanie programu VMM do mapowania sieci

Jeśli używasz programu VMM, [mapowania sieci](site-recovery-network-mapping.md) mapowania między lokalnymi sieciami maszyny Wirtualnej VMM i sieci wirtualnych platformy Azure. Mapowanie gwarantuje, że maszynach wirtualnych platformy Azure są połączone siecią prawo podczas są tworzone po pracy awaryjnej.

Przygotowanie programu VMM do mapowania sieci w następujący sposób:

1. Upewnij się, że masz [sieć logiczna VMM](https://docs.microsoft.com/system-center/vmm/network-logical) która jest skojarzona z chmury, w którym znajdują się hosty funkcji Hyper-V.
2. Upewnij się, masz [sieci maszyny Wirtualnej](https://docs.microsoft.com/system-center/vmm/network-virtual) połączony z siecią logiczną.
3. W programie VMM należy połączyć z maszyn wirtualnych z siecią maszyny Wirtualnej.

## <a name="verify-internet-access"></a>Sprawdź dostęp do Internetu

1. Na potrzeby tego samouczka najprostsza konfiguracja jest dla hostów funkcji Hyper-V i serwer VMM ma bezpośredni dostęp do Internetu bez użycia serwera proxy. 
2. Upewnij się, że hosta funkcji Hyper-V i serwer programu VMM, jeśli jest to istotne, mogą uzyskiwać dostęp do tych adresów URL: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Jeśli jest kontrola dostępu za pomocą adresu IP, upewnij się, że:
    - Reguły zapory oparte na adresie IP mogą łączyć się z [zakresów IP centrum danych Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)oraz numer portu HTTPS (port 443).
    - Zezwalaj na zakresy adresów IP dla regionu Azure Twojej subskrypcji i zachodnie stany USA (używanych do zarządzania tożsamości i kontroli dostępu).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

W scenariuszu trybu failover można połączyć się z siecią zreplikowanych lokalnymi.

Aby połączyć się za pomocą protokołu RDP po pracy awaryjnej maszyn wirtualnych systemu Windows, Zezwalaj na dostęp w następujący sposób:

1. Aby uzyskać dostęp przez Internet, włącz protokół RDP na lokalnej maszynie wirtualnej przed włączeniem trybu failover. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.
2. Aby uzyskać dostęp za pośrednictwem połączenia VPN typu lokacja-lokacja, włącz protokół RDP na maszynie lokalnej. Używanie protokołu RDP powinno być dozwolone w pozycji **Zapora systemu Windows** -> **Dozwolone aplikacje i funkcje** dla sieci typu **Domena i prywatne**.
   Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135). Podczas wyzwalania trybu failover na maszynie wirtualnej nie powinno być żadnych oczekujących aktualizacji systemu Windows. W przeciwnym razie nie będzie można zalogować się na maszynie wirtualnej do momentu ukończenia aktualizacji.
3. Na maszynie wirtualnej platformy Azure z systemem Windows po przejściu do trybu failover sprawdź **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli nie możesz się połączyć, upewnij się, że maszyna wirtualna jest uruchomiona, i przejrzyj te [porady dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Po przejściu w tryb failover można uzyskać dostępu do maszyn wirtualnych platformy Azure przy użyciu tego samego adresu IP jako wirtualna zreplikowanych lokalnymi lub innego adresu IP. [Dowiedz się więcej](concepts-on-premises-to-azure-networking.md) Konfigurowanie adresów IP pracy w trybie Failover.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii do platformy Azure dla maszyn wirtualnych funkcji Hyper-V](tutorial-hyper-v-to-azure.md)
> [Konfigurowanie odzyskiwania po awarii do platformy Azure dla maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM](tutorial-hyper-v-vmm-to-azure.md)
