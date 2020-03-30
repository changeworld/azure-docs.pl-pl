---
title: Obsługa migracji serwera fizycznego w usłudze Azure Migrate
description: Dowiedz się więcej o obsłudze migracji serwerów fizycznych w usłudze Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269551"
---
# <a name="support-matrix-for-physical-server-migration"></a>Macierz obsługi migracji serwera fizycznego

W tym artykule podsumowano ustawienia pomocy technicznej i ograniczenia dotyczące migracji serwerów fizycznych za pomocą [programu Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Jeśli szukasz informacji na temat oceny serwerów fizycznych do migracji na platformę Azure, zapoznaj się z [macierzą pomocy technicznej oceny](migrate-support-matrix-physical.md).


## <a name="overview"></a>Omówienie

Maszyny lokalne można migrować jako serwery fizyczne przy użyciu replikacji opartej na agentach. Za pomocą tego narzędzia można migrować szeroką gamę komputerów na platformę Azure:

- Lokalne serwery fizyczne.
- Maszyny wirtualne zwirtualizowane przez platformy takie jak Xen, KVM.
- Maszyny wirtualne funkcji Hyper-V lub maszyny wirtualne VMware, jeśli z jakiegoś powodu nie chcesz używać standardowych przepływów [funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub [VMware.](server-migrate-overview.md)
- Maszyny wirtualne działające w chmurach prywatnych.
- Maszyny wirtualne działające w chmurach publicznych, takich jak Amazon Web Services (AWS) lub Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Ograniczenia migracji

Do replikacji można wybrać do 10 maszyn jednocześnie. Jeśli chcesz przeprowadzić migrację większej liczby komputerów, należy replikować w grupach po 10.


## <a name="physical-server-requirements"></a>Wymagania dotyczące serwera fizycznego

W tabeli podsumowano obsługę serwerów fizycznych, które mają zostać migrowane przy użyciu migracji opartej na agentach.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obciążenie komputera** | Usługa Azure Migrate obsługuje migrację dowolnego obciążenia (na przykład usługi Active Directory, serwera SQL itp.) uruchomionego na obsługiwanym komputerze.
**Systemy operacyjne** | Aby uzyskać najnowsze informacje, zapoznaj się z [obsługą systemu operacyjnego](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) dla usługi Site Recovery. Usługa Azure Migrate zapewnia identyczną obsługę systemu operacyjnego.
**System plików Linux/pamięć gościnna** | Aby uzyskać najnowsze informacje, zapoznaj się z [obsługą systemu plików Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) dla usługi Site Recovery. Usługa Azure Migrate zapewnia identyczną obsługę systemu plików systemu Linux.
**Sieć/pamięć masowa** | Aby uzyskać najnowsze informacje, zapoznaj się z wymaganiami wstępnymi [sieci](../site-recovery/vmware-physical-azure-support-matrix.md#network) i [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#storage) dla odzyskiwania witryny. Usługa Azure Migrate zapewnia identyczne wymagania dotyczące sieci/magazynu.
**Wymagania platformy Azure** | Aby uzyskać najnowsze informacje, zapoznaj się z [wymaganiami](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)dotyczącymi sieci, [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)i [danych obliczeniowych](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) platformy Azure dotyczącymi odzyskiwania witryny. Usługa Azure Migrate ma identyczne wymagania dotyczące migracji serwera fizycznego.
**Usługa mobilności** | Agent usługi mobilności musi być zainstalowany na każdym komputerze, który ma zostać zmigrowany.
**Rozruch UEFI** | Zmigrowany komputer na platformie Azure zostanie automatycznie przekonwertowany na maszynę wirtualną platformy Azure z rozruchem systemu BIOS.<br/><br/> Dysk systemu operacyjnego powinien mieć maksymalnie cztery partycje, a woluminy powinny być sformatowane za pomocą systemu plików NTFS.
**Dysk docelowy** | Maszyny można migrować tylko do dysków zarządzanych (standardowy dysk twardy, dysk SSD w wersji premium) na platformie Azure.
**Rozmiar dysku** | Dysk systemu operacyjnego o pojemności 2 TB; 8 TB dla dysków z danymi.
**Limity dysków** |  Do 63 dysków na komputer.
**Zaszyfrowane dyski/woluminy** |  Maszyny z zaszyfrowanymi dyskami/woluminami nie są obsługiwane do migracji.
**Klaster dysków udostępnionych** | Bez pomocy technicznej.
**Dyski niezależne** | Obsługiwane.
**Dyski przekazywania** | Obsługiwane.
**NFS** | Woluminy NFS zainstalowane jako woluminy na maszynach nie będą replikowane.
**Cele iSCSI** | Maszyny z celami iSCSI nie są obsługiwane w przypadku migracji bez agenta.
**Wielościeżkowy io** | Bez pomocy technicznej.
**Pamięć vMotion magazynu** | Obsługiwane
**Karty sieciowe z zespołami** | Bez pomocy technicznej.
**Protokół IPv6** | Bez pomocy technicznej.



## <a name="replication-appliance-requirements"></a>Wymagania urządzenia replikacji

Jeśli urządzenie replikacji zostało skonfigurowane ręcznie na serwerze fizycznym, upewnij się, że jest ono zgodne z wymaganiami podsumowanym w tabeli. Po skonfigurowaniu urządzenia replikacji migracji platformy Azure jako maszyny wirtualnej VMware przy użyciu szablonu ova dostępnego w centrum migracji platformy Azure urządzenie jest skonfigurowane w systemie Windows Server 2016 i spełnia wymagania pomocy technicznej. 

- Dowiedz się więcej o [wymaganiach urządzeń replikacji](migrate-replication-appliance.md#appliance-requirements).
- MySQL musi być zainstalowany na urządzeniu. Dowiedz się więcej o [opcjach instalacji](migrate-replication-appliance.md#mysql-installation).
- Dowiedz się więcej o [adresach URL,](migrate-replication-appliance.md#url-access) do które urządzenie replikacji musi uzyskać dostęp.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Wszystkie lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania maszyny wirtualnej platformy Azure podsumowane w tej tabeli. Gdy usługa Site Recovery uruchamia sprawdzanie wymagań wstępnych pod kątem replikacji, sprawdzanie zakończy się niepowodzeniem, jeśli niektóre wymagania nie są spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Weryfikuje obsługiwane systemy operacyjne.<br/> Można migrować dowolne obciążenie uruchomione w obsługiwanym systemie operacyjnym. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Architektura systemu operacyjnego gościa | 64-bitowy. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Rozmiar dysku systemu operacyjnego | Do 2048 GB. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Liczba dysków systemu operacyjnego | 1 | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Liczba dysków danych | 64 lub mniej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Rozmiar dysku danych | Do 4095 GB | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Karty sieciowe | Obsługiwanych jest wiele kart. |
Udostępniony wirtualny dysk twardy | Bez pomocy technicznej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Dysk FC | Bez pomocy technicznej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
BitLocker | Bez pomocy technicznej. | Funkcja BitLocker musi być wyłączona przed włączeniem replikacji komputera.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa urządzenia musi zaczynać się i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach komputera w odzyskiwaniu witryny.
Łączenie się po migracji-Windows | Aby połączyć się z maszynami wirtualnymi platformy Azure z systemem Windows po migracji:<br/> - Przed migracją włącza RDP na lokalnej maszynie wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.<br/> Aby uzyskać dostęp do sieci VPN między lokacjami, włącz prow i zezwalaj na prow w **zaporze** -> systemu Windows**Dozwolone aplikacje i funkcje** dla sieci domen i sieci **prywatnych.** Ponadto sprawdź, czy zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](prepare-for-migration.md). |
Połącz się po migracji-Linux | Aby połączyć się z maszynami wirtualnymi platformy Azure po migracji przy użyciu funkcji SSH:<br/> Przed migracją na komputerze lokalnym sprawdź, czy usługa Bezpieczne powłoki jest ustawiona na Start i czy reguły zapory zezwalają na połączenie SSH.<br/> Po przełączeniu awaryjnym na maszynie Wirtualnej platformy Azure zezwalaj na połączenia przychodzące z portem SSH dla reguł sieciowej grupy zabezpieczeń na maszynie wirtualnej po awarii i dla podsieci platformy Azure, z którą jest nawiązycona. Ponadto dodaj publiczny adres IP maszyny Wirtualnej. |  


## <a name="next-steps"></a>Następne kroki

[Migrowanie](tutorial-migrate-physical-virtual-machines.md) serwerów fizycznych.
