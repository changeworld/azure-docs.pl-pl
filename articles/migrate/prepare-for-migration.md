---
title: Przygotuj maszyny do migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować maszyny lokalne do migracji za pomocą Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f5535a57fae847c8a376b8b39e43955675da739
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974788"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Przygotowywanie maszyn lokalnych do migracji na platformę Azure

W tym artykule opisano sposób przygotowania maszyn lokalnych przed rozpoczęciem migracji na platformę Azure przy użyciu [migracji Azure Migrate Server](migrate-services-overview.md#azure-migrate-server-migration-tool).


W tym artykule opisano następujące zagadnienia:
> [!div class="checklist"]
> * Sprawdź ograniczenia migracji.
> * Sprawdź wymagania systemu operacyjnego i ograniczenia dotyczące obsługi.
> * Przejrzyj dostęp do adresu URL/portu dla maszyn, które chcesz zmigrować.
> * Przejrzyj zmiany, które należy wykonać przed rozpoczęciem migracji.
> * Skonfiguruj ustawienia, aby litery dysku były zachowywane po migracji.
> * Przygotuj maszyny, aby można było połączyć się z maszynami wirtualnymi platformy Azure po migracji.


## <a name="verify-migration-limitations"></a>Weryfikuj ograniczenia migracji

- W jednym Azure Migrate projekcie można ocenić do 35 000 maszyn wirtualnych VMware/maszyn wirtualnych funkcji Hyper-V przy użyciu Azure Migrate migracji serwera. Projekt może łączyć zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, a także limity dla każdej z nich.
- Do migracji można wybrać maksymalnie 10 maszyn wirtualnych. Jeśli chcesz przeprowadzić replikację więcej, Replikuj w grupach o wartości 10.
- W przypadku migracji programu VMware bez agenta można jednocześnie uruchomić do 100 replikacji.

## <a name="verify-operating-system-requirements"></a>Sprawdź wymagania dotyczące systemu operacyjnego

- Sprawdź, czy [systemy operacyjne Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) są obsługiwane na platformie Azure.
- Sprawdź, czy [dystrybucje systemu Linux](../virtual-machines/linux/endorsed-distros.md) są obsługiwane na platformie Azure.


## <a name="check-whats-supported"></a>Sprawdź, co jest obsługiwane

- W przypadku maszyn wirtualnych VMware Migracja serwera Azure Migrate obsługuje [migrację bezagentową lub opartą na agencie](server-migrate-overview.md). Sprawdź wymagania/obsługę maszyn wirtualnych VMware na potrzeby migracji [bez agentów](migrate-support-matrix-vmware.md#migration---limitations) i [opartych na agentach](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) .
- Sprawdź [wymagania dotyczące migracji i obsługę](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) maszyn wirtualnych funkcji Hyper-V.
- Sprawdź [wymagania dotyczące migracji i obsługę](migrate-support-matrix-physical.md) lokalnych maszyn fizycznych lub innych zwirtualizowanych serwerów. 




## <a name="review-urlport-access"></a>Przejrzyj dostęp do adresu URL/portu

Podczas migracji maszyny mogą potrzebować dostępu do Internetu.

- Przejrzyj adresy URL, do których maszyny wirtualne VMware muszą uzyskać dostęp podczas migracji na podstawie [agentów](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) lub [z agentów](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) .
- Przejrzyj adresy URL, na które hosty funkcji Hyper-V muszą uzyskać dostęp podczas migracji. Maszyny wirtualne funkcji Hyper-V nie potrzebują dostępu do Internetu.
- [Przejrzyj adresy URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) , których maszyny fizyczne lub inne serwery zwirtualizowane muszą uzyskać dostęp podczas migracji.
- W przypadku migrowania maszyn wirtualnych VMware/serwerów fizycznych na podstawie agenta usługa mobilności działająca na komputerach musi mieć dostęp do składników Azure Migrate. W przypadku zarządzania replikacją Usługa uruchomiona na komputerze komunikuje się z lokalnym urządzeniem replikacji Azure Migrate na porcie HTTPS 443. Maszyny wysyłają dane replikacji do serwera przetwarzania Azure Migrate na porcie HTTPS 9443 przychodzącego. Ten port może być modyfikowany.


## <a name="verify-required-changes-before-migration"></a>Sprawdź wymagane zmiany przed migracją

Niektóre maszyny wirtualne mogą wymagać zmian, aby mogły być uruchamiane na platformie Azure. Azure Migrate automatycznie wprowadza te zmiany dla maszyn wirtualnych z tymi systemami operacyjnymi:
- Red Hat Enterprise Linux 6.5 +, 7.0 +
- CentOS 6.5+, 7.0+
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS
- Debian 7, 8

W przypadku innych systemów operacyjnych należy przygotować maszyny ręcznie przed migracją. 

### <a name="prepare-windows-machines"></a>Przygotowywanie maszyn z systemem Windows

W przypadku migrowania maszyny z systemem Windows przed migracją wprowadź te zmiany. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmian może się zdarzyć, że maszyna wirtualna nie uruchomi się na platformie Azure.

1. [Włącz konsolę dostępu szeregowego platformy Azure](../virtual-machines/troubleshooting/serial-console-windows.md) dla maszyny wirtualnej platformy Azure. Ułatwia to rozwiązywanie problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Na maszynie wirtualnej platformy Azure zostanie wykonany rozruch przy użyciu obrazu dysku. Jest to równoznaczne z ponownym uruchomieniem nowej maszyny wirtualnej. 
2. W przypadku migrowania maszyn z systemem Windows Server 2003 należy zainstalować usługi integracji gościa funkcji Hyper-V w systemie operacyjnym maszyny wirtualnej. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Przygotowywanie maszyn z systemem Linux

1. Zainstaluj usługi integracji funkcji Hyper-V w systemie Linux. Większość nowych wersji dystrybucji systemu Linux domyślnie obejmuje ten system.
2. Ponownie skompiluj obraz init systemu Linux, aby zawierał wymagane sterowniki funkcji Hyper-V. Dzięki temu maszyna wirtualna zostanie uruchomiona na platformie Azure i będzie wymagana tylko w przypadku niektórych dystrybucji.
3. [Włącz rejestrowanie konsoli szeregowej platformy Azure](../virtual-machines/troubleshooting/serial-console-linux.md). Ułatwia to rozwiązywanie problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Na maszynie wirtualnej platformy Azure zostanie wykonany rozruch przy użyciu obrazu dysku. Jest to równoznaczne z ponownym uruchomieniem nowej maszyny wirtualnej.
4. Zaktualizuj plik mapy urządzeń za pomocą nazwy urządzenia do skojarzeń woluminów, aby użyć trwałych identyfikatorów urządzeń.
5. Aktualizowanie wpisów fstab w celu korzystania z identyfikatorów woluminów trwałych.
6. Usuń wszystkie reguły udev, które zastrzegają nazwy interfejsów na podstawie adresu MAC itp.
7. Zaktualizuj interfejsy sieciowe, aby odbierać adresy IP z serwera DHCP.
8. [Dowiedz się więcej](../virtual-machines/linux/create-upload-generic.md) o czynnościach wymaganych do uruchomienia maszyny wirtualnej z systemem Linux na platformie Azure i uzyskaj instrukcje dotyczące niektórych popularnych dystrybucji systemu Linux.

## <a name="preserve-drive-letters-after-migration"></a>Zachowaj litery dysku po migracji

W przypadku migrowania maszyny lokalnej do Microsoft Azure litery dysku dodatkowych dysków z danymi mogą ulec zmianie z poprzednich wartości. Domyślnie maszyny wirtualne platformy Azure są przypisane do dysku D do użycia jako magazyn tymczasowy. To przypisanie dysku powoduje, że wszystkie pozostałe przydziały przyłączone do dysku magazynu są zwiększane o jedną literę.

Na przykład jeśli instalacja lokalna używa dysku z danymi, który jest przypisany do dysku D dla instalacji aplikacji, przypisanie dla tego dysku jest zwiększane do dysku E po przeprowadzeniu migracji maszyny wirtualnej na platformę Azure. Aby zapobiec automatycznemu przypisaniu i upewnić się, że system Azure przypisuje kolejną literę dysku do jego woluminu tymczasowego, ustaw zasady sieci magazynowania (SAN) na OnlineAll w następujący sposób:

1. Na maszynie lokalnej (nie na serwerze hosta) Otwórz wiersz polecenia z podwyższonym poziomem uprawnień.
2. Wpisz polecenie **diskpart**.
3. Typ **sieci San**. Jeśli litera dysku systemu operacyjnego gościa nie jest zachowywana, zwracany jest **tryb offline wszystkie** lub **offline udostępnione** .
4. W wierszu polecenia **diskpart** wpisz **zasady sieci San = OnlineAll**. To ustawienie zapewnia, że dyski są przełączane w tryb online i są możliwe do odczytu i do zapisu.
5. Podczas migracji testów można sprawdzić, czy litery dysku są zachowywane.


## <a name="check-azure-vm-requirements"></a>Sprawdź wymagania dotyczące maszyny wirtualnej platformy Azure

Maszyny lokalne replikowane na platformę Azure muszą być zgodne z wymaganiami maszyny wirtualnej platformy Azure dotyczącymi systemu operacyjnego i architektury, dysków, ustawień sieciowych i nazw maszyn wirtualnych. Przed migracją Sprawdź wymagania dotyczące [maszyn wirtualnych VMware/serwerów fizycznych](migrate-support-matrix-vmware.md#azure-vm-requirements)oraz [maszyn wirtualnych funkcji Hyper-V](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) .


## <a name="prepare-to-connect-after-migration"></a>Przygotowywanie do nawiązywania połączenia po migracji

Maszyny wirtualne platformy Azure są tworzone podczas migracji na platformę Azure. Po migracji musisz mieć możliwość nawiązania połączenia z nowymi maszynami wirtualnymi platformy Azure. Istnieje kilka kroków wymaganych do pomyślnego nawiązania połączenia.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Przygotowywanie się do łączenia się z maszynami wirtualnymi platformy Microsoft Azure

Na lokalnych maszynach z systemem Windows wykonaj następujące czynności:

1. Skonfiguruj ustawienia systemu Windows. Obejmują one usuwanie wszelkich statycznych tras trwałych lub serwera proxy WinHTTP.
2. Upewnij się, że [te usługi](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) działają.
3. Włącz funkcję pulpitu zdalnego (RDP), aby zezwolić na zdalne połączenia z maszyną lokalną. [Dowiedz się, jak](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) włączyć protokół RDP przy użyciu programu PowerShell.
4. Aby uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem Internetu po migracji, w zaporze systemu Windows na maszynie lokalnej Zezwalaj na protokoły TCP i UDP w profilu publicznym i ustaw protokół RDP jako dozwoloną aplikację dla wszystkich profilów.
5. Jeśli chcesz uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem sieci VPN typu lokacja-lokacja po migracji, w zaporze systemu Windows na maszynie lokalnej, Zezwól na protokół RDP dla domeny i profilów prywatnych. [Dowiedz się](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) , jak zezwolić na ruch RDP. 
6. Upewnij się, że nie ma żadnych oczekujących aktualizacji systemu Windows na lokalnej maszynie wirtualnej podczas migracji. W takim przypadku aktualizacje mogą rozpocząć instalację na maszynie wirtualnej platformy Azure po migracji i nie będzie można zalogować się do maszyny wirtualnej do momentu zakończenia aktualizacji.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Przygotowywanie się do łączenia się z maszynami wirtualnymi systemu Linux Azure

Na lokalnych maszynach z systemem Linux wykonaj następujące czynności:

1. Sprawdź, czy usługa Secure Shell jest uruchamiana automatycznie przy rozruchu systemu.
2. Sprawdź, czy reguły zapory zezwalają na połączenie SSH.

### <a name="configure-azure-vms-after-migration"></a>Konfigurowanie maszyn wirtualnych platformy Azure po migracji

Po migracji wykonaj następujące czynności na maszynach wirtualnych platformy Azure, które zostały utworzone.

1. Aby nawiązać połączenie z maszyną wirtualną za pośrednictwem Internetu, przypisz publiczny adres IP do maszyny wirtualnej. Nie można użyć tego samego publicznego adresu IP dla maszyny wirtualnej platformy Azure, która była używana przez maszynę lokalną. [Dowiedz się więcej](../virtual-network/virtual-network-public-ip-address.md).
2. Sprawdź, czy reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) na maszynie wirtualnej zezwalają na połączenia przychodzące do portu RDP lub SSH.
3. Sprawdź [diagnostykę rozruchu](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) , aby wyświetlić maszynę wirtualną.

> [!NOTE]
> Usługa Azure bastionu oferuje prywatny dostęp RDP i SSH do maszyn wirtualnych platformy Azure. [Dowiedz się więcej](../bastion/bastion-overview.md) o tej usłudze.



## <a name="next-steps"></a>Następne kroki

Zdecyduj, która metoda ma być używana do [migrowania maszyn wirtualnych VMware](server-migrate-overview.md) na platformę Azure, lub Rozpocznij Migrowanie [maszyn wirtualnych funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub [serwerów fizycznych lub zwirtualizowanych/chmurowych maszyn](tutorial-migrate-physical-virtual-machines.md)wirtualnych.
