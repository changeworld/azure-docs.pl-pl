---
title: Przygotowywanie maszyn do migracji za pomocą usługi Azure Migrate
description: Dowiedz się, jak przygotować maszyny lokalne do migracji za pomocą usługi Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78927469"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Przygotowywanie maszyn lokalnych do migracji na platformę Azure

W tym artykule opisano sposób przygotowania komputerów lokalnych przed rozpoczęciem migracji na platformę Azure przy użyciu [usługi Azure Migrate: Migracja serwera.](migrate-services-overview.md#azure-migrate-server-migration-tool)

W tym artykule opisano następujące zagadnienia:
> [!div class="checklist"]
> * Sprawdź ograniczenia migracji.
> * Sprawdź wymagania systemu operacyjnego i ograniczenia pomocy technicznej.
> * Przejrzyj adres URL i dostęp do portów dla komputerów, które chcesz przeprowadzić migrację.
> * Przejrzyj zmiany, które mogą być konieczne przed rozpoczęciem migracji.
> * Skonfiguruj ustawienia tak, aby litery dysków były zachowywane po migracji.
> * Przygotuj maszyny, aby można było połączyć się z maszynami wirtualnymi platformy Azure po migracji.

## <a name="verify-migration-limitations"></a>Weryfikowanie ograniczeń migracji

- Maszyn wirtualnych vmware/funkcji Hyper-V można ocenić do 35 000 maszyn wirtualnych z użyciem funkcji HyperMware w jednym projekcie migracji platformy Azure przy użyciu migracji serwera migracji usługi Azure. Projekt można łączyć maszyny wirtualne VMware i maszyny wirtualne hyper-V, do granic dla każdego.
- Można wybrać maksymalnie 10 maszyn wirtualnych naraz do migracji. Jeśli chcesz replikować więcej, replikuj w grupach po 10.
- W przypadku migracji bezagentowej VMware można jednocześnie uruchomić do 100 replikacji.

## <a name="verify-operating-system-requirements"></a>Weryfikowanie wymagań systemu operacyjnego

- Sprawdź, czy [twoje systemy operacyjne Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) są obsługiwane na platformie Azure.
- Sprawdź, czy [twoje dystrybucje systemu Linux](../virtual-machines/linux/endorsed-distros.md) są obsługiwane na platformie Azure.

## <a name="see-whats-supported"></a>Zobacz, co jest obsługiwane

W przypadku maszyn wirtualnych VMware migracja serwera obsługuje [migrację bez agenta lub na podstawie agenta](server-migrate-overview.md).

- **Maszyny wirtualne VMware**: Sprawdź [wymagania dotyczące migracji i obsługę](migrate-support-matrix-vmware-migration.md) maszyn wirtualnych VMware.
- **Maszyny wirtualne funkcji Hyper-V:** Sprawdź [wymagania dotyczące migracji i obsługę](migrate-support-matrix-hyper-v-migration.md) maszyn wirtualnych funkcji Hyper-V.
- **Komputery fizyczne:** Sprawdź [wymagania dotyczące migracji i obsługę](migrate-support-matrix-physical-migration.md) lokalnych komputerów fizycznych i innych zwirtualizowanych serwerów. 

## <a name="review-url-and-port-access"></a>Przeglądanie adresu URL i dostępu do portu

Maszyny mogą potrzebować dostępu do Internetu podczas migracji.

- **Urządzenie migracji platformy Azure:** przejrzyj [adresy URL](migrate-appliance.md#url-access) i [porty](migrate-support-matrix-vmware-migration.md#agentless-ports) urządzenia usługi Azure Migrate, które muszą uzyskać dostęp podczas migracji bez agenta.
- **Migracja oparta na maszynach VMware na podstawie agenta maszyn**wirtualnych: przejrzyj [adresy URL](migrate-replication-appliance.md#url-access) i [porty](migrate-replication-appliance.md#port-access) używane przez urządzenie replikacji podczas migracji opartej na agentach VMware VM. 
- **Hosty funkcji Hyper-V:** przeglądanie [adresów URL i portów,](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) do których muszą uzyskać dostęp hosty funkcji Hyper-V podczas migracji. 
- **Serwery fizyczne:** przeglądanie [adresów URL](migrate-replication-appliance.md#url-access) i [portów](migrate-replication-appliance.md#port-access) używanych przez urządzenie replikacji podczas migracji serwera fizycznego.

## <a name="verify-required-changes-before-migrating"></a>Weryfikowanie wymaganych zmian przed migracją

Niektóre maszyny wirtualne mogą wymagać zmian, aby mogły działać na platformie Azure. Usługa Azure Migrate wprowadza te zmiany automatycznie dla maszyn wirtualnych, które są uruchomione te systemy operacyjne:

- Red Hat Enterprise Linux 7.0+, 6.5+
- CentOS 7.0+, 6.5+
- SUSE Linux Enterprise Server 12 zw.
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7

W przypadku innych systemów operacyjnych należy ręcznie przygotować maszyny przed migracją. 

### <a name="prepare-windows-machines"></a>Przygotowywanie maszyn z systemem Windows

Jeśli przeprowadzasz migrację komputera z systemem Windows, przed migracją należy wprowadzić następujące zmiany. Jeśli przed wprowadzeniem zmian zostanie migrowanie maszyny wirtualnej, maszyna wirtualna może nie zostać uruchomiony na platformie Azure.

1. [Włącz konsolę szeregową platformy Azure](../virtual-machines/troubleshooting/serial-console-windows.md) dla maszyny Wirtualnej platformy Azure. Włączenie konsoli ułatwia rozwiązywanie problemów. Nie trzeba ponownie uruchomić maszyny Wirtualnej. Maszyna wirtualna platformy Azure uruchomi się przy użyciu obrazu dysku. Rozruch obrazu dysku jest odpowiednikiem ponownego uruchomienia nowej maszyny Wirtualnej. 
2. W przypadku migracji komputerów z systemem Windows Server 2003 należy zainstalować usługi integracji gościa funkcji Hyper-V w systemie operacyjnym maszyny wirtualnej. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Przygotowanie maszyn z systemem Linux

1. Zainstaluj usługi integracji systemu Hyper-V Linux. Większość nowych wersji dystrybucji linuksa zawiera domyślnie usługi integracji systemu Hyper-V Linux.
2. Odbuduj obraz linuxowy, aby zawierał niezbędne sterowniki Hyper-V. Przebudowa obrazu init gwarantuje, że maszyna wirtualna zostanie uruchomiony na platformie Azure (wymagane tylko w niektórych dystrybucjach).
3. [Włącz rejestrowanie konsoli szeregowego platformy Azure](../virtual-machines/troubleshooting/serial-console-linux.md). Włączenie rejestrowania konsoli ułatwia rozwiązywanie problemów. Nie trzeba ponownie uruchomić maszyny Wirtualnej. Maszyna wirtualna platformy Azure uruchomi się przy użyciu obrazu dysku. Rozruch obrazu dysku jest odpowiednikiem ponownego uruchomienia nowej maszyny Wirtualnej.
4. Zaktualizuj plik mapy urządzenia za pomocą skojarzeń nazwa-wolumin urządzenia, aby użyć trwałych identyfikatorów urządzeń.
5. Zaktualizuj wpisy w pliku fstab, aby używać trwałych identyfikatorów woluminów.
6. Usuń wszystkie reguły udev, które rezerwują nazwy interfejsu na podstawie adresu MAC i tak dalej.
7. Zaktualizuj interfejsy sieciowe, aby odbierać adres IP z usługi DHCP.

Dowiedz się więcej o [krokach uruchamiania maszyny Wirtualnej z systemem Linux na platformie Azure](../virtual-machines/linux/create-upload-generic.md)i uzyskaj instrukcje dotyczące niektórych popularnych dystrybucji systemu Linux.

## <a name="preserve-drive-letters-after-migration"></a>Zachowywanie liter dysków po migracji

Podczas migracji komputera lokalnego do platformy Microsoft Azure litery dysków dodatkowych dysków danych mogą ulec zmianie w stosunku do ich oryginalnych wartości. 

Domyślnie maszyny wirtualne platformy Azure są przypisane dysk D do użycia jako magazyn tymczasowy. To przypisanie dysku powoduje, że wszystkie inne przypisania dysków dołączonego magazynu zwiększa się o jedną literę. Jeśli na przykład instalacja lokalna używa dysku danych przypisanego do dysku D dla instalacji aplikacji, przypisanie tego dysku zwiększa się do dysku E po migracji maszyny Wirtualnej na platformę Azure. Aby zapobiec temu automatycznemu przypisywaniu i upewnić się, że platforma Azure przypisuje następną literę wolnego dysku do tymczasowego woluminu, ustaw zasady sieci obszarów magazynowania (SAN) na **OnlineAll:**

1. Na komputerze lokalnym (nie na serwerze hosta) otwórz wiersz polecenia z podwyższonym poziomem uprawnień.
2. Wprowadź **diskpart**.
3. Wprowadź **SAN**. Jeśli litera dysku systemu operacyjnego gościa nie jest obsługiwana, zwracana jest usługa **Offline All** lub **Offline Shared.**
4. W wierszu **DISKPART** wprowadź zasadę **SAN=OnlineAll**. To ustawienie gwarantuje, że dyski są przenoszone do trybu online i zapewnia, że można odczytywać i zapisywać na obu dyskach.
5. Podczas migracji testowej można sprawdzić, czy litery dysków są zachowywane.

## <a name="check-azure-vm-requirements"></a>Sprawdzanie wymagań dotyczących maszyn wirtualnych platformy Azure

Maszyny lokalne, które są replikowane na platformie Azure, muszą spełniać wymagania dotyczące maszyny wirtualnej platformy Azure dla systemu operacyjnego i architektury, dysków, ustawień sieciowych i nazewnictwa maszyn wirtualnych. Przed migracją sprawdź wymagania dotyczące [maszyn wirtualnych i serwerów fizycznych](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)oraz maszyn [wirtualnych funkcji Hyper-V.](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)

## <a name="prepare-to-connect-after-migration"></a>Przygotowanie do połączenia po migracji

Maszyny wirtualne platformy Azure są tworzone podczas migracji na platformę Azure. Po migracji musisz mieć możliwość nawiązania połączenia z nowymi maszynami wirtualnymi platformy Azure. Aby połączyć się pomyślnie, wymagane jest wiele kroków.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Przygotowanie do nawiązania połączenia z maszynami wirtualnymi systemu Windows platformy Azure

Na lokalnych komputerach z systemem Windows:

1. Skonfiguruj ustawienia systemu Windows. Ustawienia obejmują usunięcie wszystkich statycznych tras trwałych lub serwera proxy WinHTTP.
2. Upewnij [się,](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) że są uruchomione wymagane usługi.
3. Włącz pulpit zdalny (RDP), aby zezwolić na zdalne połączenia z komputerem lokalnym. Dowiedz się, jak [włączyć program RDP za pomocą programu PowerShell.](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
4. Aby uzyskać dostęp do maszyny Wirtualnej platformy Azure za pośrednictwem Internetu po migracji, w Zaporze systemu Windows na komputerze lokalnym, zezwalaj na protokół TCP i UDP w profilu publicznym i ustawiaj protokół RDP jako dozwoloną aplikację dla wszystkich profili.
5. Jeśli chcesz uzyskać dostęp do maszyny Wirtualnej platformy Azure za pośrednictwem sieci VPN lokacji lokacji po migracji, w Zaporze systemu Windows na komputerze lokalnym zezwalaj na prowętlogię RDP dla profilów domeny i prywatnych. Dowiedz się, jak [zezwolić na ruch RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Upewnij się, że podczas migracji nie ma żadnych aktualizacji systemu Windows oczekujących na lokalną maszynę wirtualną. Jeśli tak, aktualizacje mogą rozpocząć instalację na maszynie Wirtualnej platformy Azure po migracji i nie będzie można zalogować się do maszyny Wirtualnej, dopóki aktualizacje zakończą się.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Przygotowanie do nawiązania połączenia z maszynami wirtualnymi platformy Azure z systemem Linux

Na lokalnych komputerach z systemem Linux:

1. Sprawdź, czy usługa Secure Shell jest skonfigurowana do automatycznego uruchamiania po rozruchu systemu.
2. Sprawdź, czy reguły zapory zezwalają na połączenie SSH.

### <a name="configure-azure-vms-after-migration"></a>Konfigurowanie maszyn wirtualnych platformy Azure po migracji

Po migracji wykonaj następujące kroki na maszynach wirtualnych platformy Azure, które są tworzone:

1. Aby połączyć się z maszyną wirtualną przez Internet, przypisz publiczny adres IP do maszyny Wirtualnej. Należy użyć innego publicznego adresu IP dla maszyny Wirtualnej platformy Azure niż używane dla komputera lokalnego. [Dowiedz się więcej](../virtual-network/virtual-network-public-ip-address.md).
2. Sprawdź, czy reguły sieciowej grupy zabezpieczeń (NSG) na maszynie wirtualnej zezwalają na przychodzące połączenia z portem RDP lub SSH.
3. Sprawdź [diagnostykę rozruchu,](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) aby wyświetlić maszynę wirtualną.

> [!NOTE]
> Usługa Azure Bastion oferuje prywatny dostęp rdp i SSH do maszyn wirtualnych platformy Azure. [Dowiedz się więcej](../bastion/bastion-overview.md) o tej usłudze.

## <a name="next-steps"></a>Następne kroki

Zdecyduj, której metody chcesz użyć do [migracji maszyn wirtualnych na](server-migrate-overview.md) platformę Azure lub rozpocznij migrację maszyn [wirtualnych funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub [serwerów fizycznych lub zwirtualizowanych lub chmurowych maszyn wirtualnych.](tutorial-migrate-physical-virtual-machines.md)
