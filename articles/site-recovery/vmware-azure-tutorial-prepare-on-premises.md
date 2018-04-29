---
title: Przygotowywanie lokalnych serwerów VMware do odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure| Microsoft Docs
description: Dowiedz się, jak przygotować lokalne serwery VMware do odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f7722891af15111fd0151055c35bf24100ed79b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Przygotowywanie lokalnych serwerów VMware do odzyskiwania po awarii na platformie Azure

W tym samouczku pokazano, jak przygotować infrastrukturę lokalną VMware do replikowania maszyn wirtualnych VMware na platformie Azure. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowywanie konta na serwerze vCenter lub hoście vSphere ESXi w celu zautomatyzowania odnajdowania maszyn wirtualnych
> * Przygotowywanie konta do automatycznej instalacji usługi Mobility na maszynach wirtualnych VMware
> * Przegląd wymagań dotyczących serwerów VMware
> * Przegląd wymagań dotyczących maszyn wirtualnych VMware

W tej serii samouczków pokazano, jak zreplikować pojedynczą maszynę wirtualną za pomocą usługi Azure Site Recovery. 

Jest to drugi samouczek z tej serii. Upewnij się, że są już [skonfigurowane składniki platformy Azure](tutorial-prepare-azure.md) zgodnie z opisem w poprzednim samouczku.

W przypadku replikowania wielu maszyn wirtualnych pobierz [narzędzie Planista wdrażania](https://aka.ms/asr-deployment-planner) na potrzeby replikacji maszyn wirtualnych programu VMware. [Dowiedz się więcej](site-recovery-deployment-planner.md).


## <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdowanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebne jest konto, na którym można uruchamiać operacje, takie jak tworzenie i usuwanie dysków, a także włączanie maszyn wirtualnych.

Utwórz konto w następujący sposób:

1. Aby użyć dedykowanego konta, utwórz rolę na poziomie vCenter. Nadaj roli nazwę, taką jak **Azure_Site_Recovery**.
2. Przypisz do roli uprawnienia podsumowane w poniższej tabeli.
3. Utwórz użytkownika na serwerze vCenter lub hoście vSphere. Przypisz tę rolę temu użytkownikowi.

### <a name="vmware-account-permissions"></a>Uprawnienia konta VMware

**Zadanie podrzędne** | **Rola/uprawnienia** | **Szczegóły**
--- | --- | ---
**Odnajdowanie maszyn wirtualnych** | Co najmniej użytkownik tylko do odczytu<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).
**Pełna replikacja, tryb failover i powrót po awarii** |  Utwórz rolę (Azure_Site_Recovery) z wymaganymi uprawnieniami, a następnie przypisz ją użytkownikowi lub grupie VMware<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = Azure_Site_Recovery<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdej maszynie wirtualnej, która ma być replikowana. Usługa Site Recovery automatycznie instaluje tę usługę po włączeniu replikacji dla danej maszyny wirtualnej. Instalacja automatyczna wymaga przygotowania konta, za pomocą którego usługa Site Recovery będzie uzyskiwać dostęp do maszyny wirtualnej. To konto określa się podczas konfigurowania odzyskiwania po awarii w konsoli platformy Azure.

1. Przygotuj domenę lub konto lokalne z uprawnieniami do instalowania na maszynie wirtualnej.
2. Aby zainstalować na maszynach wirtualnych z systemem Windows, jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na komputerze lokalnym.
   - W kluczu rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** dodaj wpis DWORD **LocalAccountTokenFilterPolicy** o wartości 1.
3. Aby zainstalować na maszynach wirtualnych z systemem Linux, przygotuj konto superużytkownika na serwerze źródłowym z systemem Linux.


## <a name="check-vmware-requirements"></a>Sprawdzanie wymagań dotyczących programu VMware

Upewnij się, że serwery VMware i maszyny wirtualne spełniają wymagania.

1. [Zweryfikuj](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) wymagania dotyczące serwerów VMware.
2. W przypadku systemu Linux [sprawdź](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) wymagania dotyczące systemu plików i magazynu. 
3. Sprawdź obsługę lokalnej [sieci](vmware-physical-azure-support-matrix.md#network) i [magazynu](vmware-physical-azure-support-matrix.md#storage). 
4. Sprawdź obsługę [sieci](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [magazynu](vmware-physical-azure-support-matrix.md#azure-storage) i [usług obliczeniowych](vmware-physical-azure-support-matrix.md#azure-compute) platformy Azure po przejściu do trybu failover.
5. Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać [wymagania dotyczące maszyn wirtualnych platformy Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Podczas scenariusza trybu failover może zaistnieć potrzeba połączenia się z replikowanymi maszynami wirtualnymi na platformie Azure z sieci lokalnej.

Aby nawiązać połączenie z maszynami wirtualnymi z systemem Windows przy użyciu protokołu RDP po przejściu do trybu failover, wykonaj następujące czynności:

1. Aby uzyskać dostęp przez Internet, włącz protokół RDP na lokalnej maszynie wirtualnej przed włączeniem trybu failover. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.
2. Aby uzyskać dostęp za pośrednictwem połączenia VPN typu lokacja-lokacja, włącz protokół RDP na maszynie lokalnej. Używanie protokołu RDP powinno być dozwolone w pozycji **Zapora systemu Windows** -> **Dozwolone aplikacje i funkcje** dla sieci typu **Domena i prywatne**.
   Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135). Podczas wyzwalania trybu failover na maszynie wirtualnej nie powinno być żadnych oczekujących aktualizacji systemu Windows. W przeciwnym razie nie będzie można zalogować się na maszynie wirtualnej do momentu ukończenia aktualizacji.
3. Na maszynie wirtualnej platformy Azure z systemem Windows po przejściu do trybu failover sprawdź **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli nie możesz się połączyć, upewnij się, że maszyna wirtualna jest uruchomiona, i przejrzyj te [porady dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Aby nawiązać połączenie z maszynami wirtualnymi z systemem Linux przy użyciu powłoki SSH po przejściu do trybu failover, wykonaj następujące czynności:

1. Na komputerze lokalnym przed włączeniem trybu failover upewnij się, że skonfigurowano automatyczne uruchamianie usługi Secure Shell przy rozruchu systemu. Sprawdź, czy reguły zapory zezwalają na połączenie SSH.

2. Na maszynie wirtualnej platformy Azure po przejściu do trybu failover zezwól na połączenia przychodzące do portu SSH w regułach grupy zabezpieczeń sieci na maszynie wirtualnej w trybie failover i w podsieci platformy Azure.
   [Dodaj publiczny adres IP](site-recovery-monitoring-and-troubleshooting.md) dla maszyny wirtualnej. Możesz sprawdzić **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych VMware](vmware-azure-tutorial.md)
