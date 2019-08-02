---
title: Macierz obsługi Azure Migrate na potrzeby oceny i migracji oprogramowania VMware
description: Podsumowuje ustawienia i ograniczenia dotyczące oceny i migracji maszyn wirtualnych VMware na platformę Azure przy użyciu usługi Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: bbbec680cd2575cc63761c9fbe1335d548ec4d3b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640787"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Macierz obsługi dotycząca oceny i migracji środowiska VMware

Za pomocą [Azure Migrate](migrate-overview.md) można oceniać i migrować maszyny do chmury Microsoft Azure. Ten artykuł podsumowuje ustawienia i ograniczenia dotyczące obsługi oceniania i migrowania lokalnych maszyn wirtualnych VMware.


## <a name="vmware-scenarios"></a>Scenariusze dotyczące programu VMware

W tabeli zestawiono obsługiwane scenariusze dotyczące maszyn wirtualnych VMware.

**Wdrożenie** | **Szczegóły**
--- | ---
**Ocenianie lokalnych maszyn wirtualnych VMware** | [Skonfiguruj](tutorial-prepare-vmware.md) swoją pierwszą ocenę.<br/><br/> [Uruchom](scale-vmware-assessment.md) ocenę na dużą skalę.
**Migrowanie maszyn wirtualnych VMware** | Można przeprowadzić migrację za pomocą migracji bez wykorzystania agentów lub migracji opartej na agentach. [Dowiedz się więcej](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Projekty Azure Migrate

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Uprawnienia platformy Azure** | Aby utworzyć projekt Azure Migrate, musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji.
**Ograniczenia dotyczące oprogramowania VMware**  | Oceń do 35 000 maszyn wirtualnych VMware w jednym projekcie. Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure.
**Limity projektu** | Projekt może zawierać zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, a także limity oceny.
**Lokalizacja geograficzna** | Projekt Azure Migrate można utworzyć w wielu lokalizacje geograficzneach. Chociaż można tworzyć tylko projekty w tych lokalizacje geograficzne, można ocenić lub migrować maszyny dla innych lokalizacji docelowych. Lokalizacja geograficzna projektu służy tylko do przechowywania odnalezionych metadanych.

**Lokalizacja geograficzna** | **Lokalizacja magazynu metadanych**
--- | ---
Azure Government | Administracja USA — Wirginia
Azja i Pacyfik | Azja Południowo-Wschodnia lub Azja Wschodnia
Europa | Europa Południowa lub Europa Zachodnia
Zjednoczone Królestwo | Południowe Zjednoczone Królestwo lub Zachodnie Zjednoczone Królestwo
Stany Zjednoczone | Środkowe stany USA lub zachodnie stany USA 2


 > [!NOTE]
 > Obsługa Azure Government jest obecnie dostępna tylko dla starszej [wersji](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) programu Azure Migrate.


## <a name="assessment-vcenter-server-requirements"></a>Ocena — wymagania dotyczące vCenter Server

W tej tabeli zestawiono wsparcie oceny i ograniczenia dotyczące serwerów wirtualizacji VMware.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Serwer vCenter** | Maszyny wirtualne VMware, które chcesz ocenić, muszą być zarządzane przez co najmniej jeden serwer vCenter z systemem 5,5, 6,0, 6,5 lub 6,7.

## <a name="assessment-vcenter-server-permissions"></a>Ocena — uprawnienia vCenter Server

Do oceny potrzebne jest konto tylko do odczytu dla vCenter Server.

## <a name="assessment-appliance-requirements"></a>Ocena — wymagania dotyczące urządzenia

Urządzenie Azure Migrate dla oprogramowania VMware zostanie wdrożone przy użyciu szablonu komórki jajowe zaimportowanego do vCenter Server.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**vCenter Server** | Musisz mieć wystarczającą ilość zasobów na vCenter Server, aby przydzielić maszynę wirtualną z 32 GB pamięci, 4 procesorów wirtualnych vCPU i zewnętrznym przełącznikiem wirtualnym.<br/><br/> Urządzenie wymaga dostępu do Internetu, bezpośrednio lub za pomocą serwera proxy.
**ESXi** | Maszynę wirtualną urządzenia należy wdrożyć na hoście ESXi z systemem w wersji 5,5 lub nowszej.
**Projekt Azure Migrate** | Urządzenie może być skojarzone z pojedynczym projektem.
**vCenter Server** | Urządzenie może wykryć do 10 000 maszyn wirtualnych VMware na vCenter Server.<br/> Urządzenie może połączyć się z jednym vCenter Server.


## <a name="assessment-url-access-requirements"></a>Ocena — wymagania dotyczące dostępu do adresów URL

Urządzenie Azure Migrate wymaga połączenia z Internetem za pośrednictwem Internetu.

- Podczas wdrażania urządzenia Azure Migrate sprawdza połączenie adresów URL, które zostały podsumowane w poniższej tabeli.
- Jeśli używasz serwera proxy opartego na adresie URL do łączenia się z Internetem, Zezwól na dostęp do tych adresów URL, upewniając się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.com  | Przejdź do Azure Migrate w Azure Portal.
*.windows.net | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com | Utwórz Active Directory aplikacje dla urządzenia, aby komunikować się z usługą Azure Migrate.
management.azure.com | Utwórz Active Directory aplikacje dla urządzenia, aby komunikować się z usługą Azure Migrate.
dc.services.visualstudio.com | Przekaż Dzienniki aplikacji używane do wewnętrznego monitorowania.
*.vault.azure.net | Zarządzanie wpisami tajnymi w Azure Key Vault.
*.servicebus.windows.net | Komunikacja między urządzeniem a usługą Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Połącz się z adresami URL usługi Azure Migrate.
*.blob.core.windows.net | Przekazywanie danych do kont magazynu.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Używany do Azure Migrate aktualizacji urządzenia.

## <a name="assessment-port-requirements"></a>Ocena — wymagania dotyczące portów

**urządzenia** | **połączenia**
--- | ---
Urządzenie | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL:```https://<appliance-ip-or-name>:44368``` <br/><br/>Połączenia wychodzące na porcie 443 do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
vCenter Server | Połączenia przychodzące na porcie TCP 443 umożliwiające urządzeniu zbieranie metadanych dotyczących konfiguracji i wydajności dla ocen. <br/><br/> Urządzenie domyślnie łączy się z programem vCenter na porcie 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można zmodyfikować port podczas konfigurowania odnajdywania.


## <a name="agentless-migration-vmware-server-requirements"></a>Migracja bez agentów — wymagania dotyczące serwera VMware

W tej tabeli zestawiono wsparcie oceny i ograniczenia dotyczące serwerów wirtualizacji VMware.

**Pomoc techniczna** | **Szczegóły**
--- | ---
vCenter Server | Wersja 5,5, 6,0, 6,5 lub 6,7.
VMware vSphere | W wersji 5,5, 6,0, 6,5 lub 6,7,

## <a name="agentless-migration-vcenter-server-permissions"></a>Migracja bez agentów — uprawnienia vCenter Server

**Uprawnienia** | **Szczegóły**
--- | ---
Datastore.Browse | Zezwalaj na przeglądanie plików dzienników maszyn wirtualnych w celu rozwiązywania problemów z tworzeniem i usuwaniem migawek.
Datastore.LowLevelFileOperations | Zezwalaj na operacje odczytu/zapisu/usuwania/zmiany nazwy w przeglądarce magazynu danych, aby rozwiązywać problemy z tworzeniem i usuwaniem migawki.
VirtualMachine.Configuration.DiskChangeTracking | Zezwalaj na włączanie lub wyłączanie śledzenia zmian dysków maszyn wirtualnych w celu ściągania zmienionych bloków danych między migawkami
VirtualMachine.Configuration.DiskLease | Zezwalaj na operacje dzierżawy dysku dla maszyny wirtualnej w celu odczytania dysku przy użyciu VMware vSphere wirtualnego zestawu SDK (VDDK).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Zezwól na otwieranie dysku na maszynie wirtualnej w celu odczytania dysku przy użyciu VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Zezwala na operacje odczytu plików skojarzonych z maszyną wirtualną, pobieranie dzienników i rozwiązywanie problemów w przypadku wystąpienia błędu.
VirtualMachine.SnapshotManagement.* | Umożliwia tworzenie migawek maszyn wirtualnych i zarządzanie nimi na potrzeby replikacji.
Maszyna wirtualna. Interaction. Zasilanie wyłączone | Zezwalaj na wyłączenie maszyny wirtualnej podczas migracji na platformę Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Migracja bez agentów — wymagania dotyczące maszyny wirtualnej VMware

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane systemy operacyjne** | Systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , które są obsługiwane przez platformę Azure, można migrować za pomocą migracji bez wykorzystania agentów.
**Wymagane zmiany dotyczące platformy Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, aby mogły być uruchamiane na platformie Azure. Azure Migrate automatycznie wprowadza te zmiany w następujących systemach operacyjnych:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> - CentOS 6.5+, 7.0+</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> W przypadku innych systemów operacyjnych należy ręcznie wprowadzić zmiany przed migracją. Odpowiednie artykuły zawierają instrukcje, jak to zrobić.
**Rozruch systemu Linux** | Jeśli/Boot znajduje się na dedykowanej partycji, powinien znajdować się na dysku systemu operacyjnego i nie można go rozłożyć na wiele dysków.<br/> Jeśli/boot jest częścią partycji głównej (/), partycja "/" powinna znajdować się na dysku systemu operacyjnego i nie może obejmować innych dysków.
**Rozruch z interfejsem UEFI** | Maszyny wirtualne z rozruchem UEFI nie są obsługiwane w przypadku migracji.
**Rozmiar dysku** | dysk systemu operacyjnego: 2 TB; 4 TB dla dysków z danymi.
**Limity dysku** |  Do 60 dysków na maszynę wirtualną.
**Zaszyfrowane dyski/woluminy** | Maszyny wirtualne z szyfrowanymi dyskami/woluminami nie są obsługiwane na potrzeby migracji.
**Udostępniony klaster dysków** | Nieobsługiwane.
**Dyski niezależne** | Nieobsługiwane.
**RDM/przekazywanie dysków** | Jeśli maszyny wirtualne mają dyski RDM lub przekazujących, te dyski nie będą replikowane do platformy Azure.
**NFS** | Woluminy NFS zainstalowane jako woluminy na maszynach wirtualnych nie zostaną zreplikowane.
**obiekty docelowe iSCSI** | Maszyny wirtualne z obiektami docelowymi iSCSI nie są obsługiwane w przypadku migracji bez wykorzystania agentów.
**Wielościeżkowe we/wy** | Nieobsługiwane.
**VMotion magazynu** | Nieobsługiwane. Replikacja nie będzie działała, jeśli maszyna wirtualna korzysta z vMotion magazynu.
**Zespoły kart sieciowych** | Nieobsługiwane.
**If** | Nieobsługiwane.
**Dysk docelowy** | Maszyny wirtualne można migrować tylko do dysków zarządzanych (dysk twardy w warstwie Standardowa) na platformie Azure.
**Równoczesna replikacja** | 100 maszyn wirtualnych na vCenter Server. Jeśli masz więcej, Migruj je w partiach 100.


## <a name="agentless-migration-appliance-requirements"></a>Migracja bez wykorzystania agentów — wymagania dotyczące urządzeń


**Pomoc techniczna** | **Szczegóły**
--- | ---
**ESXi** | Maszynę wirtualną urządzenia należy wdrożyć na hoście ESXi z systemem w wersji 5,5 lub nowszej.
**Projekt Azure Migrate** | Urządzenie może być skojarzone z pojedynczym projektem.
**vCenter Server** | Urządzenie może wykryć do 10 000 maszyn wirtualnych VMware na vCenter Server.<br/> Urządzenie może połączyć się z jednym vCenter Server.
**VDDK** | W przypadku korzystania z migracji bez agenta z migracją Azure Migrate Server należy zainstalować na maszynie wirtualnej urządzenia VMware vSphere VDDK.

## <a name="agentless-migration-url-access-requirements"></a>Migracja bez agentów — wymagania dotyczące dostępu do adresów URL

Urządzenie Azure Migrate wymaga połączenia z Internetem za pośrednictwem Internetu.

- Podczas wdrażania urządzenia Azure Migrate sprawdza połączenie adresów URL, które zostały podsumowane w poniższej tabeli.
- Jeśli używasz serwera proxy opartego na adresie URL, Zezwól na dostęp do tych adresów URL, upewniając się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.

**Adres URL** | **Szczegóły**  
--- | ---
*.portal.azure.com | Przejdź do Azure Migrate w Azure Portal.
*.windows.net | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com | Utwórz Active Directory aplikacje dla urządzenia, aby komunikować się z usługą Azure Migrate.
management.azure.com | Utwórz Active Directory aplikacje dla urządzenia, aby komunikować się z usługą Azure Migrate.
dc.services.visualstudio.com | Przekaż Dzienniki aplikacji używane do wewnętrznego monitorowania.
*.vault.azure.net | Zarządzanie wpisami tajnymi w Azure Key Vault.
*.servicebus.windows.net | Komunikacja między urządzeniem a usługą Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Połącz się z adresami URL usługi Azure Migrate.
*.blob.core.windows.net | Przekazywanie danych do kont magazynu.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Używany do Azure Migrate aktualizacji urządzenia.


## <a name="agentless-migration-port-requirements"></a>Migracja bez agentów — wymagania dotyczące portów

**urządzenia** | **połączenia**
--- | ---
Urządzenie | Połączenia wychodzące na porcie 443 do przekazywania replikowanych danych na platformę Azure oraz do komunikowania się z usługami Azure Migrate organizowanie replikacji i migracji.
vCenter Server | Połączenia przychodzące na porcie 443, aby umożliwić organizowanie replikacji — tworzenie migawek, kopiowanie danych i migawki wersji
Host vSphere/EXSI | Ruch przychodzący na porcie TCP 902 dla urządzenia do replikowania danych z migawek.


## <a name="agent-based-migration-vmware-server-requirements"></a>Migracja oparta na agencie — wymagania dotyczące serwera VMware

W tej tabeli zestawiono wsparcie oceny i ograniczenia dotyczące serwerów wirtualizacji VMware.

**Pomoc techniczna** | **Szczegóły**
--- | ---
vCenter Server | Wersja 5,5, 6,0, 6,5 lub 6,7.
VMware vSphere | Wersja 5,5, 6,0, 6,5 lub 6,7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Migracja oparta na agencie — uprawnienia vCenter Server

Konto tylko do odczytu dla vCenter Server.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Migracja oparta na agencie — wymagania dotyczące urządzenia replikacji

W tabeli zestawiono wymagania dotyczące [urządzenia replikacji](migrate-replication-appliance.md) używanego do migracji maszyn wirtualnych VMware i serwerów fizycznych z systemem Azure Migrate migracji.

> [!NOTE]
> Po skonfigurowaniu urządzenia do replikacji przy użyciu szablonu komórki jajowe dostarczonego w centrum Azure Migrate na urządzeniu jest uruchomiony system Windows Server 2016 i jest zgodny z wymaganiami dotyczącymi obsługi. Jeśli urządzenie replikacji zostanie skonfigurowane ręcznie na serwerze fizycznym, upewnij się, że jest ono zgodne z wymaganiami.



**Składnik** | **Wymaganie**
--- | ---
 | **Ustawienia programu VMware** (Urządzenie maszyny wirtualnej VMware)
PowerCLI | Jeśli urządzenie replikacji jest uruchomione na maszynie wirtualnej VMware, należy zainstalować [PowerCLI w wersji 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) .
Typ karty sieciowej | VMXNET3 (Jeśli urządzenie jest maszyną wirtualną VMware)
 | **Ustawienia sprzętu**
Rdzenie procesora CPU | 8
Pamięć RAM | 16 GB
Liczba dysków | Trzecim Dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania.
Wolne miejsce na dysku (pamięć podręczna) | 600 GB
Wolne miejsce na dysku (dysk przechowywania) | 600 GB
**Ustawienia oprogramowania** |
System operacyjny | Windows Server 2016 lub Windows Server 2012 R2
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
TLS | Protokół TLS 1,2 powinien być włączony.
.NET Framework | Na maszynie należy zainstalować .NET Framework 4,6 lub nowszą (z włączonym silnym kryptografią.
MySQL | Baza danych MySQL powinna być zainstalowana na urządzeniu.<br/> Należy zainstalować MySQL. Można zainstalować go ręcznie lub Site Recovery można go zainstalować podczas wdrażania urządzenia.
Inne aplikacje | Nie uruchamiaj innych aplikacji na urządzeniu replikacji.
Role systemu Windows Server | Nie należy włączać tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Zasady grupy | Nie włączaj tych zasad grupy: <br> -Zapobiegaj dostępowi do wiersza polecenia. <br> — Uniemożliwia dostęp do narzędzi do edytowania rejestru. <br> — Logika zaufania dla plików załączników. <br> — Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Brak istniejącej domyślnej witryny sieci Web <br> — Żadna istniejąca witryna sieci Web/aplikacja nasłuchu na porcie 443 <br>-Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz ustawienie [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Ustawienia sieci** |
Typ adresu IP | Static
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych)
Typ karty sieciowej | VMXNET3

### <a name="replication-appliance-url-access"></a>Dostęp do adresu URL urządzenia replikacji

Urządzenie replikacji musi mieć dostęp do tych adresów URL.

**Adres URL** | **Szczegóły**
--- | ---
\*.backup.windowsazure.com | Używany do transferu i koordynacji replikowanych danych
\*.store.core.windows.net | Używany do transferu i koordynacji replikowanych danych
\*.blob.core.windows.net | Służy do uzyskiwania dostępu do konta magazynu przechowującego zreplikowane dane
\*.hypervrecoverymanager.windowsazure.com | Używany do operacji zarządzania replikacją i koordynacji
https:\//management.azure.com | Używany do operacji zarządzania replikacją i koordynacji
*.services.visualstudio.com | Używane na potrzeby telemetrii (jest opcjonalne)
time.nist.gov | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
time.windows.com | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Instalator OVF potrzebuje dostępu do tych adresów URL. Są one używane do kontroli dostępu i zarządzania tożsamościami przez Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Aby ukończyć pobieranie bazy danych MySQL


#### <a name="mysql-installation-options"></a>Opcje instalacji MySQL

Program MySQL można zainstalować na urządzeniu replikacji przy użyciu jednej z tych metod.

**— Metoda** | **Szczegóły**
--- | ---
Pobierz i zainstaluj ręcznie | Pobierz aplikację MySQL & Umieść ją w folderze C:\Temp\ASRSetup, a następnie zainstaluj ręcznie.<br/> Po skonfigurowaniu urządzenia MySQL będą wyświetlane jako już zainstalowane.
Bez pobierania online | Umieść aplikację instalatora MySQL w folderze C:\Temp\ASRSetup. Po zainstalowaniu urządzenia i kliknięciu w celu pobrania i zainstalowania programu MySQL Instalator użyje dodanego Instalatora.
Pobierz i zainstaluj w Azure Migrate | Po zainstalowaniu urządzenia i wyświetleniu monitu o wprowadzenie do bazy danych MySQL wybierz pozycję **Pobierz i zainstaluj**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Migracja oparta na agencie — wymagania dotyczące maszyn wirtualnych VMware

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obciążenie maszyny** | Azure Migrate obsługuje migrację dowolnego obciążenia (Powiedz Active Directory, SQL Server itp.) uruchomionego na obsługiwanej maszynie.
**Systemy operacyjne** | Aby uzyskać najnowsze informacje, zapoznaj się z tematem [Obsługa systemu operacyjnego](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) Site Recovery. Azure Migrate zapewnia identyczną obsługę systemu operacyjnego maszyny wirtualnej.
**System plików Linux/magazyn gościa** | Aby uzyskać najnowsze informacje, zapoznaj się z tematem [Obsługa systemu plików Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) dla Site Recovery. Azure Migrate ma identyczną obsługę systemu plików Linux.
**Sieć/magazyn** | Aby uzyskać najnowsze informacje, zapoznaj się z wymaganiami wstępnymi dotyczącymi [sieci](../site-recovery/vmware-physical-azure-support-matrix.md#network) i [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#storage) Site Recovery. Azure Migrate zapewnia identyczne wymagania dotyczące sieci/magazynu.
**Wymagania platformy Azure** | Aby uzyskać najnowsze informacje, zapoznaj się z wymaganiami dotyczącymi sieci, [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)i [mocy obliczeniowej](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) [platformy Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)dla Site Recovery. Azure Migrate ma identyczne wymagania dotyczące migracji oprogramowania VMware.
**Usługa mobilności** | Agent usługi mobilności musi być zainstalowany na każdej maszynie wirtualnej, która ma zostać poddana migracji.
**Rozruch z interfejsem UEFI** | Migrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową w systemie BIOS.<br/><br/> Dysk systemu operacyjnego powinien mieć maksymalnie cztery partycje, a woluminy powinny być sformatowane w systemie plików NTFS.
**Dysk docelowy** | Maszyny wirtualne można migrować tylko do dysków zarządzanych (dysk twardy w warstwie Standardowa) na platformie Azure.
**Rozmiar dysku** | dysk systemu operacyjnego: 2 TB; 8 TB dla dysków z danymi.
**Limity dysku** |  Do 63 dysków na maszynę wirtualną.
**Zaszyfrowane dyski/woluminy** | Maszyny wirtualne z szyfrowanymi dyskami/woluminami nie są obsługiwane na potrzeby migracji.
**Udostępniony klaster dysków** | Nieobsługiwane.
**Dyski niezależne** | Obsługiwane.
**Przekazywanie dysków** | Obsługiwane.
**NFS** | Woluminy NFS zainstalowane jako woluminy na maszynach wirtualnych nie zostaną zreplikowane.
obiekty docelowe iSCSI | Maszyny wirtualne z obiektami docelowymi iSCSI nie są obsługiwane w przypadku migracji bez wykorzystania agentów.
**Wielościeżkowe we/wy** | Nieobsługiwane.
**VMotion magazynu** | Obsługiwane
**Zespoły kart sieciowych** | Nieobsługiwane.
**If** | Nieobsługiwane.




## <a name="agent-based-migration-url-access-requirements"></a>Migracja oparta na agencie — wymagania dotyczące dostępu do adresów URL

Usługa mobilności działająca na maszynach wirtualnych VMware wymaga połączenia z Internetem.

Podczas wdrażania usługi mobilności jest ona sprawdzana pod kątem adresów URL, które zostały podsumowane w poniższej tabeli.


**Adres URL** | **Szczegóły**  
--- | ---
*.portal.azure.com | Przejdź do Azure Migrate w Azure Portal.
*.windows.net | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com | Utwórz Active Directory aplikacje dla urządzenia, aby komunikować się z usługą Azure Migrate.
management.azure.com | Utwórz Active Directory aplikacje dla urządzenia, aby komunikować się z usługą Azure Migrate.
dc.services.visualstudio.com | Przekaż Dzienniki aplikacji używane do wewnętrznego monitorowania.
*.vault.azure.net | Zarządzanie wpisami tajnymi w Azure Key Vault.
*.servicebus.windows.net | Komunikacja między urządzeniem a usługą Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Połącz się z adresami URL usługi Azure Migrate.
*.blob.core.windows.net | Przekazywanie danych do kont magazynu.

## <a name="agent-based-migration-port-requirements"></a>Migracja oparta na agencie — wymagania dotyczące portów

**urządzenia** | **połączenia**
--- | ---
maszyn wirtualnych | Usługa mobilności działająca na maszynach wirtualnych komunikuje się z lokalnym urządzeniem replikacji (serwer konfiguracji) na porcie HTTPS 443 przychodzącego na potrzeby zarządzania replikacją.<br/><br/> Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 w ruchu przychodzącym. Ten port może być modyfikowany.
Urządzenie replikacji | Urządzenie replikacji organizuje replikację za pomocą platformy Azure przez port HTTPS 443.
Serwer przetwarzania | Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage przez port 443 wychodzące.<br/> Domyślnie serwer przetwarzania jest uruchamiany na urządzeniu replikacji.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Wszystkie lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania dotyczące maszyny wirtualnej platformy Azure podsumowane w tej tabeli. Gdy Site Recovery uruchamia sprawdzanie wymagań wstępnych dotyczących replikacji, sprawdzenie zakończy się niepowodzeniem, jeśli niektóre wymagania nie zostaną spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Sprawdź Obsługiwane systemy operacyjne dla [maszyn wirtualnych VMware przy użyciu replikacji bez wykorzystania agentów](#agentless-migration-vmware-vm-requirements)oraz [maszyn wirtualnych VMware korzystających z replikacji opartej na agentach](#agent-based-migration-vmware-vm-requirements).<br/> Możliwe jest Migrowanie dowolnego obciążenia działającego w obsługiwanym systemie operacyjnym. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Architektura systemu operacyjnego gościa | 64-bitowa. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku systemu operacyjnego | Do 2 048 GB. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków systemu operacyjnego | 1 | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków danych | 64 lub mniej. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku danych | Do 4 095 GB | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Karty sieciowe | Obsługiwane są wiele kart. |
Udostępniony wirtualny dysk twardy | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Dysk FC | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
BitLocker | Nieobsługiwane. | Aby włączyć replikację dla maszyny, należy wyłączyć funkcję BitLocker.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa maszyny musi rozpoczynać się i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach komputera w Site Recovery.
Połącz po migracji — Windows | Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure z systemem Windows po migracji:<br/> -Przed migracją włącza protokół RDP na lokalnej maszynie wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.<br/> W celu uzyskania dostępu do sieci VPN typu lokacja-lokacja Włącz protokół RDP i Zezwalaj na używanie protokołu RDP w ->  **zaporze systemu Windows** **dozwolone aplikacje i funkcje** dla sieci **i** połączeń sieciowych. Ponadto sprawdź, czy zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135). |
Połącz po migracji — system Linux | Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure po migracji przy użyciu protokołu SSH:<br/> Przed migracją na maszynie lokalnej Sprawdź, czy usługa Secure Shell jest ustawiona do uruchamiania, oraz czy reguły zapory zezwalają na połączenie SSH.<br/> Po przejściu w tryb failover na maszynie wirtualnej platformy Azure Zezwól na połączenia przychodzące do portu SSH dla reguł sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover oraz dla podsieci platformy Azure, do której jest podłączona. Dodatkowo Dodaj publiczny adres IP dla maszyny wirtualnej. |  


## <a name="next-steps"></a>Następne kroki

[Przygotuj się do](tutorial-prepare-vmware.md) oceny oprogramowania VMware i migracji.
