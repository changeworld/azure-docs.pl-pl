---
title: Przygotowywanie lokalnych serwerów VMware do odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure| Microsoft Docs
description: Dowiedz się, jak przygotować lokalne serwery VMware do odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 739f1a9a3a75123c0273dc958b4ba1fd7231f3c3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268623"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Przygotowywanie lokalnych serwerów VMware do odzyskiwania po awarii na platformie Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

- Z drugiego samouczka z serii dowiesz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware. W pierwszym samouczku [konfigurowaliśmy składniki Azure](tutorial-prepare-azure.md) potrzebne do odzyskiwania po awarii programu VMware.


> [!NOTE]
> Samouczki pokazują najprostsze ścieżki wdrożenia scenariusza. Jeśli to możliwe, używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z sekcją **Instrukcje** odpowiedniego scenariusza.

W tym artykule opisano, jak lokalnie przygotować środowisko VMware, w którym można replikować maszyny wirtualne VMware do platformy Azure przy użyciu usługi Azure Site Recovery. Omawiane kwestie:

> [!div class="checklist"]
> * Przygotowywanie konta na serwerze vCenter lub hoście vSphere ESXi w celu zautomatyzowania odnajdowania maszyn wirtualnych
> * Przygotowywanie konta do automatycznej instalacji usługi Mobility na maszynach wirtualnych VMware
> * Przegląd wymagań dotyczących serwerów VMware i maszyn wirtualnych
> * Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover



## <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebne jest konto, na którym można uruchamiać operacje, takie jak tworzenie i usuwanie dysków, a także włączanie maszyn wirtualnych.

Utwórz konto w następujący sposób:

1. Aby użyć dedykowanego konta, utwórz rolę na poziomie vCenter. Nadaj roli nazwę, taką jak **Azure_Site_Recovery**.
2. Przypisz do roli uprawnienia podsumowane w poniższej tabeli.
3. Utwórz użytkownika na serwerze vCenter lub hoście vSphere. Przypisz tę rolę temu użytkownikowi.

### <a name="vmware-account-permissions"></a>Uprawnienia konta VMware

**Zadanie** | **Rola lub wymagane uprawnienia** | **Szczegóły**
--- | --- | ---
**Odnajdywanie maszyny Wirtualnej** | Co najmniej użytkownik tylko do odczytu<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).
**Pełna replikacja, tryb failover i powrotu po awarii** |  Utwórz rolę (Azure_Site_Recovery) z wymaganymi uprawnieniami, a następnie przypisz ją użytkownikowi lub grupie VMware<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = Azure_Site_Recovery<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługę Mobility należy zainstalować na każdej maszynie, która ma być replikowana. Usługa Site Recovery może wykonać instalację typu push tej usługi po włączeniu replikacji dla maszyny. Można też zainstalować usługę ręcznie lub przy użyciu narzędzi instalacji.

- W tym samouczku zamierzamy zainstalować usługę Mobility przy użyciu instalacji typu push.
- Ta instalacja typu push wymaga przygotowania konta, za pomocą którego usługa Site Recovery może uzyskiwać dostęp do maszyny wirtualnej. To konto określa się podczas konfigurowania odzyskiwania po awarii w konsoli platformy Azure.

Przygotuj konto w następujący sposób:

Przygotuj domenę lub konto lokalne z uprawnieniami do instalowania na maszynie wirtualnej.

- **Maszyny wirtualne z systemem Windows**: Aby przeprowadzić instalację na maszynach wirtualnych z systemem Windows, jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na komputerze lokalnym. Aby to zrobić, w kluczu rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** dodaj wpis DWORD **LocalAccountTokenFilterPolicy** o wartości 1.
- **Maszyny wirtualne z systemem Linux**: Aby zainstalować na maszynach wirtualnych z systemem Linux, przygotuj konto superużytkownika na serwerze źródłowym z systemem Linux.


## <a name="check-vmware-requirements"></a>Sprawdzanie wymagań dotyczących programu VMware

Upewnij się, że serwery VMware i maszyny wirtualne spełniają wymagania.

1. [Zweryfikuj](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) wymagania dotyczące serwerów VMware.
2. W przypadku systemu Linux [sprawdź](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) wymagania dotyczące systemu plików i magazynu. 
3. Sprawdź obsługę lokalnej [sieci](vmware-physical-azure-support-matrix.md#network) i [magazynu](vmware-physical-azure-support-matrix.md#storage). 
4. Sprawdź obsługę [sieci](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [magazynu](vmware-physical-azure-support-matrix.md#azure-storage) i [usług obliczeniowych](vmware-physical-azure-support-matrix.md#azure-compute) platformy Azure po przejściu do trybu failover.
5. Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać [wymagania dotyczące maszyn wirtualnych platformy Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
6. W przypadku maszyn wirtualnych z systemem Linux nazwa urządzenia lub nazwa punktu instalacji powinny być unikatowe. Upewnij się, że żadna para urządzeń/punktów instalacji nie ma takich samych nazw. Pamiętaj, że w nazwach nie jest uwzględniana wielkość liter. Na przykład nie jest dozwolone nadanie dwóm urządzeniom dla tej samej maszyny wirtualnej nazw _urządzenie1_ i _Urządzenie1_.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu do trybu failover można połączyć się z maszynami wirtualnymi Azure z sieci lokalnej.

Aby nawiązać połączenie z maszynami wirtualnymi z systemem Windows przy użyciu protokołu RDP po przejściu do trybu failover, wykonaj następujące czynności:

- **Dostęp do Internetu**. Przed włączeniem trybu failover włącz protokół RDP na lokalnej maszynie wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.
- **Dostęp do sieci VPN typu lokacja-lokacja**:
    - Przed przejściem do trybu failover włącz protokół RDP na maszynie lokalnej.
    - Używanie protokołu RDP powinno być dozwolone w pozycji **Zapora systemu Windows** -> **Dozwolone aplikacje i funkcje** dla sieci typu **Domena i prywatne**.
    - Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).
- Podczas wyzwalania trybu failover na maszynie wirtualnej nie powinno być żadnych oczekujących aktualizacji systemu Windows. W przeciwnym razie nie będzie można zalogować się na maszynie wirtualnej do momentu ukończenia aktualizacji.
- Na maszynie wirtualnej platformy Azure z systemem Windows po przejściu do trybu failover sprawdź **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli nie możesz się połączyć, upewnij się, że maszyna wirtualna jest uruchomiona, i przejrzyj te [porady dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Aby nawiązać połączenie z maszynami wirtualnymi z systemem Linux przy użyciu powłoki SSH po przejściu do trybu failover, wykonaj następujące czynności:

- Na komputerze lokalnym przed włączeniem trybu failover upewnij się, że skonfigurowano automatyczne uruchamianie usługi Secure Shell przy rozruchu systemu.
- Sprawdź, czy reguły zapory zezwalają na połączenie SSH.
- Na maszynie wirtualnej platformy Azure po przejściu do trybu failover zezwól na połączenia przychodzące do portu SSH w regułach grupy zabezpieczeń sieci na maszynie wirtualnej w trybie failover i w podsieci platformy Azure.
- [Dodaj publiczny adres IP](site-recovery-monitoring-and-troubleshooting.md) dla maszyny wirtualnej.
- Możesz sprawdzić **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej.


## <a name="failback-requirements"></a>Wymagania dotyczące powrotu po awarii
Jeśli planujesz powrót po awarii do środowiska lokalnego, musisz zapewnić [spełnienie niektórych wymagań wstępnych](vmware-azure-reprotect.md##before-you-begin). Ich spełnienie **nie jest jednak wymagane w celu rozpoczęcia pracy z włączaniem odzyskiwania po awarii** dla Twoich maszyn wirtualnych. Możesz o to zadbać także po przełączeniu w tryb failover na platformę Azure.

## <a name="useful-links"></a>Przydatne łącza

Jeśli przeprowadzasz replikację wielu maszyn wirtualnych, przed rozpoczęciem należy zaplanować pojemność i wdrożenie. [Dowiedz się więcej](site-recovery-deployment-planner.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych VMware](vmware-azure-tutorial.md)
