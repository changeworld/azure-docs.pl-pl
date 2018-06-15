---
title: Przygotowywanie lokalnych serwerów VMware do odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure| Microsoft Docs
description: Dowiedz się, jak przygotować lokalne serwery VMware do odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4fecd5f8ddb4a6f432995a7779e29479b5b1a7c0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677519"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Przygotowywanie lokalnych serwerów VMware do odzyskiwania po awarii na platformie Azure

W tym samouczku pokazano, jak przygotować infrastrukturę lokalną VMware do replikowania maszyn wirtualnych VMware na platformie Azure. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowywanie konta na serwerze vCenter lub hoście vSphere ESXi w celu zautomatyzowania odnajdowania maszyn wirtualnych
> * Przygotowywanie konta do automatycznej instalacji usługi Mobility na maszynach wirtualnych VMware
> * Przegląd wymagań dotyczących serwerów VMware
> * Przegląd wymagań dotyczących maszyn wirtualnych VMware

W tej serii samouczków pokazano, jak utworzyć kopię zapasową jednej maszyny wirtualnej za pomocą usługi Azure Site Recovery. Jeśli planujesz ochronę wielu maszyn wirtualnych VMware, pobierz [narzędzie Planista wdrażania](https://aka.ms/asr-deployment-planner) na potrzeby replikacji maszyn wirtualnych VMware. To narzędzie pozwala zebrać informacje o zgodności maszyn wirtualnych, liczbie dysków przypadających na maszynę wirtualną oraz współczynniku zmian danych przypadających na dysk. Narzędzie uwzględnia również wymagania dotyczące przepustowości sieci i infrastruktury platformy Azure potrzebnej do pomyślnej replikacji i testowania trybu failover. [Dowiedz się więcej](site-recovery-deployment-planner.md) o uruchamianiu narzędzia.

Jest to drugi samouczek z tej serii. Upewnij się, że są już [skonfigurowane składniki platformy Azure](tutorial-prepare-azure.md) zgodnie z opisem w poprzednim samouczku.

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


## <a name="check-vmware-server-requirements"></a>Sprawdzanie wymagań dotyczących serwerów VMware

Upewnij się, że serwery VMware spełniają następujące wymagania.

**Składnik** | **Wymaganie**
--- | ---
**Serwer vCenter** | vCenter 6.5, 6.0 lub 5.5
**Host vSphere** | vSphere 6.5, 6.0, 5.5

## <a name="check-vmware-vm-requirements"></a>Sprawdzanie wymagań dotyczących maszyn wirtualnych VMware

Upewnij się, że maszyna wirtualna spełnia wymagania platformy Azure podsumowane w poniższej tabeli.

**Wymaganie dotyczące maszyny wirtualnej** | **Szczegóły**
--- | ---
**Rozmiar dysku systemu operacyjnego** | Do 2048 GB.
**Liczba dysków systemu operacyjnego** | 1
**Liczba dysków danych** | 64 lub mniej
**Rozmiar wirtualnego dysku twardego dysku danych** | Do 4095 GB
**Karty sieciowe** | Obsługiwana jest konfiguracja z wieloma kartami sieciowymi
**Udostępniony wirtualny dysk twardy** | Nieobsługiwane
**Dysk FC** | Nieobsługiwane
**Format dysku twardego** | Plik VHD lub VHDX.<br/><br/> Chociaż pliki VHDX nie są obecnie obsługiwane na platformie Azure, usługa Site Recovery automatycznie konwertuje pliki VHDX na wirtualne dyski twarde w przypadku trybu failover na platformie Azure. Po powrocie po awarii do środowiska lokalnego maszyny wirtualne nadal używają formatu VHDX.
**Bitlocker** | Nieobsługiwane. Wyłącz przed włączeniem replikacji dla maszyny wirtualnej.
**Nazwa maszyny wirtualnej** | Od 1 do 63 znaków.<br/><br/> Ograniczone do liter, cyfr i łączników. Nazwa maszyny wirtualnej musi zaczynać się i kończyć literą lub cyfrą.
**Typ maszyny wirtualnej** | Generacja 1 — Linux lub Windows<br/><br/>Generacja 2 — tylko Windows

Na maszynie wirtualnej musi również działać obsługiwany system operacyjny. Zobacz [macierz obsługi usługi Site Recovery](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions), aby zapoznać się z pełną listą obsługiwanych wersji.

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
> [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych VMware](tutorial-vmware-to-azure.md)
