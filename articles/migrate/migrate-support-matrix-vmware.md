---
title: Usługa Azure Migrate macierzy obsługi VMware oceny i migracji
description: Zawiera podsumowanie ustawień obsługi i ograniczenia dotyczące oceny i migracji maszyn wirtualnych programu VMware do platformy Azure przy użyciu usługi Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: raynew
ms.openlocfilehash: 567a6582e193208a7ff4aa37bafefe1dec4f4e8f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811585"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Macierz obsługi dla oceny programu VMware i migracji

Możesz użyć [usługi Azure Migrate](migrate-overview.md) ocenianie i Migrowanie maszyn do chmury Microsoft Azure. Ten artykuł zawiera podsumowanie ustawień obsługi i ograniczenia dotyczące oceny i migracji maszyn wirtualnych programu VMware w środowisku lokalnym.


## <a name="vmware-scenarios"></a>Scenariusze programu VMware

Tabela zawiera podsumowanie obsługiwanych scenariuszy dla maszyn wirtualnych VMware.

**Wdrożenie** | **Szczegóły** 
--- | --- 
**Ocenianie lokalnych maszyn wirtualnych z programu VMware** | [Konfigurowanie](tutorial-prepare-vmware.md) przy pierwszej ocenie.<br/><br/> [Uruchom](scale-vmware-assessment.md) oceny na dużą skalę.
**Migrowanie maszyn wirtualnych VMware** | Można przeprowadzić migrację za pomocą funkcji migracji z pewnymi ograniczeniami, lub użyć migracji do usługi oparte na agentach. [Dowiedz się więcej](server-migrate-overview.md)


    


## <a name="azure-migrate-projects"></a>Projekty migracji platformy Azure

**Pomoc techniczna** | **Szczegóły**
--- | ---
Uprawnienia platformy Azure | Musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji, aby utworzyć projekt usługi Azure Migrate.
Ograniczenia oprogramowania VMware  | Oceń maksymalnie 35 000 maszyn wirtualnych programu VMware w jednym projekcie.

Projekt może zawierać zarówno maszyny wirtualne VMware i maszyn wirtualnych funkcji Hyper-V, w granicach oceny.

## <a name="assessment-vmware-server-requirements"></a>Wymagań dotyczących serwerów VMware oceny

Ta tabela zawiera podsumowanie oceny pomocy technicznej i ograniczenia dotyczące serwerów wirtualizacji oprogramowania VMware.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Serwer vCenter** | Maszyny wirtualne VMware, którą chcesz ocenić musi być zarządzany przez jeden lub więcej serwerów vCenter z 5.5, 6.0, 6.5 lub 6.7.

## <a name="assessment-vcenter-server-permissions"></a>Uprawnienia serwera vCenter oceny

Tylko do oceny należy tylko do odczytu konta programu vCenter Server.

## <a name="assessment-appliance-requirements"></a>Wymagania dotyczące oceny urządzenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**ESXi** | Urządzenie maszyny Wirtualnej musi zostać wdrożony na hoście ESXi z wersją 5.5 lub nowszej.
**Projekt migracji platformy Azure** | Urządzenie może być skojarzony z jednym projektem.
**vCenter Server** | Urządzenie można odnajdywać maksymalnie 10 000 maszyn wirtualnych programu VMware w programie vcenter Server.<br/> Urządzenia mogą łączyć się z jednego serwera vCenter.


## <a name="assessment-url-access-requirements"></a>Adres URL oceny wymagań w zakresie dostępu

Urządzenie Azure Migrate wymaga połączenia internetowego z Internetu.

- Podczas wdrażania urządzenia usługi Azure Migrate wykonuje sprawdzenie łączności z adresami URL podsumowane w poniższej tabeli.
- Jeśli używasz firewall.proxy opartego na adresach URL umożliwiają dostęp do tych adresów URL, upewniając się, że serwer proxy usuwa wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.com  | Przejdź do usługi Azure Migrate w witrynie Azure portal.
*.windows.net | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com | Tworzenie aplikacji usługi Active Directory dla urządzenia do komunikowania się z usługą Azure Migrate.
management.azure.com | Tworzenie aplikacji usługi Active Directory dla urządzenia do komunikowania się z usługą Azure Migrate.
dc.services.visualstudio.com | Przekaż Dzienniki aplikacji, używany do monitorowania wewnętrznego.
*.vault.azure.net | Zarządzanie wpisami tajnymi w usłudze Azure Key Vault.
*.servicebus.windows.net | Komunikacja między urządzeniem i usługa Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Połącz z adresami URL usługi Azure Migrate.
*.blob.core.windows.net | Przekazywanie danych do konta magazynu.


## <a name="assessment-port-requirements"></a>Wymagania dotyczące portów oceny

**urządzenia** | **połączenia**
--- | --- 
Urządzenia | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego do urządzenia.<br/> Liczba przychodzących połączeń na porcie 44368 zdalnie uzyskiwać dostęp do aplikacji zarządzania urządzeniem przy użyciu adresu URL: https://<appliance-ip-or-name>:44368 <br/>Połączenia wychodzące na porcie 443 w celu wysyłania metadanych odnajdywania i wydajności do usługi Azure Migrate.
Serwer vCenter | Liczba przychodzących połączeń na porcie TCP 443, aby zezwolić na urządzenia, które można zebrać metadanych konfiguracji i wydajności dla oceny. <br/> Urządzenie łączy się vCenter na porcie 443, domyślnie. Jeśli serwer vCenter nasłuchuje na innym porcie, port można zmodyfikować po skonfigurowaniu odnajdowania.


## <a name="agentless-migration-vmware-server-requirements"></a>Wymagania dotyczące serwera bez wykorzystania agentów VMware migracji

Ta tabela zawiera podsumowanie oceny pomocy technicznej i ograniczenia dotyczące serwerów wirtualizacji oprogramowania VMware.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Serwer vCenter** | Przeprowadzić migrację, za pomocą funkcji migracji maszyn wirtualnych programu VMware musi być zarządzane przez jeden lub więcej serwerów vCenter z 5.5, 6.0, 6.5 lub 6.7.

## <a name="agentless-migration-vcenter-server-permissions"></a>Uprawnienia serwera bez wykorzystania agentów vCenter migracji

**Uprawnienia** | **Szczegóły**
--- | --- 
Datastore.Browse | Zezwalaj na przeglądanie plików dziennika maszyny Wirtualnej rozwiązywania problemów z migawki, tworzenia i usuwania.
Datastore.LowLevelFileOperations | Zezwalaj na operacje odczytu/zapisu/usuwania/zmiany nazwy, w przeglądarce magazynu danych, rozwiązywać problemy z migawki, tworzenia i usuwania.
VirtualMachine.Configuration.DiskChangeTracking | Zezwalaj na Włącz lub wyłącz śledzenie zmian dysków maszyny Wirtualnej, aby ściągnąć zmienionych bloków danych między migawkami
VirtualMachine.Configuration.DiskLease | Zezwalaj na operacje dzierżawy dysków dla maszyny Wirtualnej, można odczytać dysku przy użyciu oprogramowania VMware vSphere wirtualnego dysku Development Kit (VDDK).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Zezwalaj na otwieranie dysku na maszynie Wirtualnej, można odczytać dysku przy użyciu VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Umożliwia wykonywanie operacji odczytu dla plików skojarzonych z maszyną Wirtualną, aby pobrać dzienniki i rozwiązywanie problemów w przypadku niepowodzenia. 
VirtualMachine.SnapshotManagement.* | Zezwalaj na tworzenie i zarządzanie migawki maszyny Wirtualnej na potrzeby replikacji. 
Wirtualne Machine.Interaction.Power wyłączone | Zezwalaj na maszyna wirtualna może być wyłączone podczas migracji na platformę Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Wymagania dotyczące maszyny Wirtualnej bez wykorzystania agentów VMware migracji

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane systemy operacyjne** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) systemów operacyjnych obsługiwanych przez platformę Azure można poddać migracji za pomocą migracji bez wykorzystania agentów.
**Wymagane zmiany na platformie Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, dzięki czemu mogą uruchamiać na platformie Azure. Usługa Azure Migrate sprawia, że te zmiany automatycznie dla następujących systemów operacyjnych:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> — 7,8 debian<br/><br/> Dla innych systemów operacyjnych należy wprowadzić korekty ręcznie przed migracją. Odpowiednie artykuły zawierają instrukcje dotyczące sposobu wykonania tego zadania.
**Rozruch systemu Linux** | Jeśli/Boot znajduje się na dedykowanym partycji, powinien znajdować się na dysku systemu operacyjnego, a nie być rozmieszczone na wielu dyskach.<br/> Jeśli/Boot jest częścią partycji głównej (/), następnie partycji "/" powinien znajdować się na dysku systemu operacyjnego, a nie obejmować inne dyski.
**Rozruch z interfejsem UEFI** | Maszyny wirtualne z rozruchem z interfejsem UEFI nie są obsługiwane na potrzeby migracji.
**Zaszyfrowanych dysków/woluminów** | Maszyny wirtualne z zaszyfrowanych dysków/woluminów nie są obsługiwane na potrzeby migracji.
**Dyski RDM/przekazujące** | Jeśli maszyny wirtualne dyski RDM lub atrybut passthrough, te dyski nie będą replikowane na platformie Azure.
**NFS** | Woluminy systemu plików NFS zainstalowany jako woluminy na maszynach wirtualnych nie będą replikowane.
**Dysk docelowy** | Tylko maszyny wirtualne można migrować do usługi managed disks (HHD standardowa, premium SSD) na platformie Azure.


## <a name="agentless-migration-appliance-requirements"></a>Wymagania dotyczące migracji urządzenie bez wykorzystania agentów


**Pomoc techniczna** | **Szczegóły**
--- | ---
**ESXi** | Urządzenie maszyny Wirtualnej musi zostać wdrożony na hoście ESXi z wersją 5.5 lub nowszej.
**Projekt migracji platformy Azure** | Urządzenie może być skojarzony z jednym projektem.
**vCenter Server** | Urządzenie można odnajdywać maksymalnie 10 000 maszyn wirtualnych programu VMware w programie vcenter Server.<br/> Urządzenia mogą łączyć się z jednego serwera vCenter.
**VDDK** | Jeśli korzystasz z funkcji migracji do usługi za pomocą usługi Azure Migration migracji serwera, programu VMware vSphere wirtualnego dysku Development Kit (VDDK) musi być zainstalowany na urządzeniu maszyny Wirtualnej.

## <a name="agentless-migration-url-access-requirements"></a>Wymagania dotyczące dostępu do adresu URL migracji bez wykorzystania agentów

Urządzenie Azure Migrate wymaga połączenia internetowego z Internetu.

- Podczas wdrażania urządzenia usługi Azure Migrate wykonuje sprawdzenie łączności z adresami URL podsumowane w poniższej tabeli.
- Jeśli używasz firewall.proxy opartego na adresach URL umożliwiają dostęp do tych adresów URL, upewniając się, że serwer proxy usuwa wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.

**Adres URL** | **Szczegóły**  
--- | --- 
*.portal.azure.com | Przejdź do usługi Azure Migrate w witrynie Azure portal.
*.windows.net | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com | Tworzenie aplikacji usługi Active Directory dla urządzenia do komunikowania się z usługą Azure Migrate.
management.azure.com | Tworzenie aplikacji usługi Active Directory dla urządzenia do komunikowania się z usługą Azure Migrate.
dc.services.visualstudio.com | Przekaż Dzienniki aplikacji, używany do monitorowania wewnętrznego.
*.vault.azure.net | Zarządzanie wpisami tajnymi w usłudze Azure Key Vault.
*.servicebus.windows.net | Komunikacja między urządzeniem i usługa Azure Migrate.
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | Połącz z adresami URL usługi Azure Migrate.
*.blob.core.windows.net | Przekazywanie danych do konta magazynu.


## <a name="agentless-migration-port-requirements"></a>Wymagania dotyczące migracji portu bez wykorzystania agentów

**urządzenia** | **połączenia**
--- | --- 
Urządzenia | Wychodzącego portu 3389 protokołu TCP do przekazania danych replikowanych do platformy Azure, a także nawiązać połączenia z usługą Azure Migrate na potrzeby replikacji i migracji.
Serwer vCenter | Połączenia przychodzące na porcie TCP 443, aby zezwolić na urządzenia w celu organizowania replikacji — Tworzenie migawki kopiowania danych wersji migawek
host vSphere/EXSI | Dla ruchu przychodzącego na porcie TCP 902 dla urządzenia do replikacji danych z migawki. 


## <a name="agent-based-migration-vmware-server-requirements"></a>Wymagań dotyczących serwerów VMware migracji opartej o agentów

Ta tabela zawiera podsumowanie oceny pomocy technicznej i ograniczenia dotyczące serwerów wirtualizacji oprogramowania VMware.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Serwer vCenter/ESXI** | Maszyny wirtualne VMware, które można migrować musi być zarządzany przez jeden lub więcej serwerów vCenter z 5.5, 6.0, 6.5 lub 6.7 lub uruchomiona na hoście ESXI vSphere w wersji 5.5, 6.0, 6.5 lub 6.7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Uprawnienia serwera vCenter migracji opartej o agentów

**Uprawnienia** | **Szczegóły**
--- | --- 
Datastore.AllocateSpace | Zezwalaj na przydzielenie miejsca na na magazyn danych dla maszyny Wirtualnej, migawki, klonowanie lub wirtualnego dysku. 
Datastore.Browse | Zezwalaj na przeglądanie plików dziennika maszyny Wirtualnej rozwiązywania problemów z migawki, tworzenia i usuwania.
Datastore.LowLevelFileOperations | Zezwalaj na odczyt, zapis, usuwanie i Zmień nazwę działania w przeglądarce magazynu danych i rozwiązywanie problemów z migawki tworzenia/usuwania.
Datastore.UpdateVirtualMachineFiles | Zezwalaj na aktualizowanie ścieżki do plików maszyny Wirtualnej na magazyn danych po resignatured magazynu danych.
Network.AssignNetwork | Zezwalaj na przypisywanie sieci do zasobu maszyny Wirtualnej.
AssignVirtualMachineToResourcePool | Zezwalaj na przypisanie maszyny wirtualnej z pulą zasobów.
Resource.MigratePoweredOffVirtualMachine | Zezwalaj na migracja wyłączonej maszyny Wirtualnej do hosta lub inną pulę zasobów.
Resource.MigratePoweredOnVirtualMachine | Zezwalaj na migracji przy użyciu narzędzia vMotion włączane na maszyny wirtualnej do hosta lub inną pulę zasobów.
Tasks.CreateTask | Zezwalaj na rozszerzenie do utworzenia zadania zdefiniowane przez użytkownika.
Tasks.UpdateTask | Zezwalaj na rozszerzenie można zaktualizować zadania zdefiniowane przez użytkownika.
VirtualMachine.Configuration. | Umożliwia konfigurowanie opcji maszyny Wirtualnej i urządzeń.
Machine.Interaction.AnswerQuestion wirtualny | Zezwalaj na rozwiązywanie problemów ze Stanami maszyny Wirtualnej lub błędy czasu wykonywania.
Virtual Machine.Interaction.DeviceConnection | Zezwalaj na zmienianie połączony odłączonym urządzeń wirtualnych maszyny Wirtualnej.
Virtual Machine.Interaction.ConfigureCDMedia | Umożliwia skonfigurowanie urządzenia wirtualnego dysku DVD lub CD.
Machine.Interaction.ConfigureFloppyMedia wirtualny | Umożliwia skonfigurowanie urządzenia wirtualnego dyskietek.
Machine.Interaction.PowerOff wirtualny | Umożliwia maszyna wirtualna może być wyłączone podczas migracji na platformę Azure.
Machine.Interaction.PowerOn wirtualny | Zezwalaj na włączeniem wyłączenia zasilania maszyny Wirtualnej i wznawianie wstrzymanej maszyny Wirtualnej.
Machine.Interaction.VMwareToolsInstall wirtualny | Zezwalaj na instalowanie i odinstalowywanie Instalatora ciągłego wdrażania narzędzia VMware jako dysku CD systemu operacyjnego gościa.
VirtualMachine.Inventory.CreateNew | Zezwalaj na tworzenie maszyny Wirtualnej i alokacji wymaganych zasobów.
VirtualMachine.Inventory.Register | Zezwalaj na dodawanie istniejącej maszyny Wirtualnej do serwera vCenter lub hosta spisu.
VirtualMachine.Inventory.Unregister | Zezwalaj na wyrejestrowywanie VMe z serwera vCenter lub hosta spisu.
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | Zezwalaj na operacje zapisu na pliki skojarzone z maszyny Wirtualnej z systemem, vmx, dysków, dzienniki i nvram.
VirtualMachine.Provisioning.AllowVirtualMachineDownload | Zezwalaj na operacje odczytu dla plików skojarzonych z maszyną Wirtualną, aby pobrać dzienniki do rozwiązywania problemów.
VirtualMachine.SnapshotManagement.RemoveSnapshot | Zezwalaj na usuwanie migawki z historii migawki.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Wymagania dotyczące urządzenia replikacji migracji opartej o agentów

Wymagania dotyczące [urządzenia replikacji](migrate-replication-appliance.md) używany podczas migracji opartej o agentów maszyn wirtualnych VMware i fizycznych serwerów przy użyciu usługi Azure Migration migracji serwera są podsumowane w tabeli.

> [!NOTE]
> Podczas konfigurowania urządzenia replikacji przy użyciu szablonu OVA dostępnego w Centrum usługi Azure Migrate urządzenie działa w systemie Windows Server 2016 i jest zgodna z wymaganiami dotyczącymi obsługi. Jeśli możesz skonfigurować urządzenia replikacji ręcznie na serwerze fizycznym, a następnie upewnij się, że spełnia on wymagania.



**Składnik** | **Wymaganie** 
--- | ---
 | **Ustawienia oprogramowania VMware** (urządzeniem maszyny Wirtualnej VMware)
**Interfejs PowerCLI** | [Interfejs PowerCLI w wersji 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) powinien być zainstalowany, jeśli urządzenie replikacji jest uruchomiona na maszynie Wirtualnej VMware.
**Typ karty NIC** | Innego VMXNET3 (jeśli jest to urządzenie jest maszyny Wirtualnej VMware)
 | **Ustawienia sprzętu** 
Rdzenie procesora CPU | 8 
Pamięć RAM | 16 GB
Liczba dysków | Trzy: Dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania.
Wolne miejsce na dysku (pamięć podręczna) | 600 GB
Wolnego miejsca na dysku (dysk przechowywania) | 600 GB
**Ustawienia oprogramowania** | 
System operacyjny | Windows Server 2016 lub Windows Server 2012 R2
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
TLS | Powinien być włączony protokół TLS 1.2.
.NET Framework | .NET framework 4.6 lub nowszym należy zainstalować na maszynie (z włączoną silnej kryptografii.
MySQL | MySQL powinien być zainstalowany na urządzeniu.<br/> Powinien być zainstalowany MySQL. Można zainstalować ręcznie lub Usługa Site Recovery można zainstalować go podczas wdrażania urządzenia. 
Inne aplikacje | Nie należy uruchamiać inne aplikacje na urządzeniu replikacji.
Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Zasady grupy | Nie włączaj tych zasad grupy: <br> -Zapobiegaj dostępowi do wiersza polecenia. <br> -Uniemożliwić dostęp do narzędzi edycji rejestru. <br> — Logika zaufania dla plików załączników. <br> -Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Brak wcześniej istniejącej domyślnej witryny sieci Web <br> -Brak przeniosła istniejące wcześniej witryny sieci Web/aplikacja nasłuchuje na porcie 443 <br>-Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie 
**Ustawienia sieci** | 
Typ adresu IP | Static 
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych) 
Typ karty NIC | VMXNET3 

### <a name="replication-appliance-url-access"></a>Dostęp do adresu URL urządzenia replikacji

Urządzenie replikacji musi mieć dostęp do tych adresów URL.

**Adres URL** | **Szczegóły**
--- | ---
\*.backup.windowsazure.com | Używany do transferowania i koordynacji replikowanych danych.
\*.store.core.windows.net | Używany do transferowania i koordynacji replikowanych danych.
\*.blob.core.windows.net | Umożliwia dostęp do konta magazynu przechowującego zreplikowane dane
\*.hypervrecoverymanager.windowsazure.com | Służy do operacji zarządzania replikacją i koordynacji
https:\//management.azure.com | Służy do operacji zarządzania replikacją i koordynacji 
*.services.visualstudio.com | Używane do celów danych telemetrycznych (jest to opcjonalne)
time.nist.gov | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
time.windows.com | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Instalator pakietu OVF musi mieć dostęp do tych adresów URL. Są one używane do kontrolowania dostępu i tożsamości zarządzania przez usługę Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Aby zakończyć pobieranie MySQL


#### <a name="mysql-installation-options"></a>Opcje instalacji MySQL

MySQL można zainstalować na urządzeniu replikacji przy użyciu jednej z tych metod.

**Instalowanie** | **Szczegóły**
--- | ---
Pobierz i zainstaluj ręcznie | Pobierz aplikację MySQL i umieść go w folderze C:\Temp\ASRSetup, a następnie ręcznie zainstalować.<br/> Po skonfigurowaniu urządzenia MySQL będzie wyświetlana jako już zainstalowane. 
Nie pobieraj w trybie online | Umieść aplikacji Instalatora programu MySQL w folderze C:\Temp\ASRSetup. Po zainstalowaniu urządzenia i kliknij, aby pobrać i zainstalować program MySQL, Instalator użyje Instalatora, który został dodany. 
Migrowanie pobierania z platformy Azure | Po zainstalowaniu urządzenia i monit o podanie MySQL, wybierz **Pobierz i zainstaluj**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Wymagania dotyczące maszyny Wirtualnej VMware migracji opartej o agentów

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obciążenie maszyny** | Usługa Azure Migrate obsługuje migrację z dowolnym obciążeniu (Załóżmy, że usługi Active Directory, SQL server, itp.) uruchomione na obsługiwanej maszynie.
**Systemy operacyjne** | Aby uzyskać najnowsze informacje, przejrzyj [obsługi systemów operacyjnych](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) usługi Site Recovery. Usługa Azure Migrate obsługuje identycznych maszyn wirtualnych systemu operacyjnego.
**Magazyn systemu/gościa pliku systemu Linux** | Aby uzyskać najnowsze informacje, przejrzyj [Obsługa systemu plików w systemie Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) usługi Site Recovery. Usługa Azure Migrate ma identyczne Obsługa systemu plików w systemie Linux.
**Sieci i magazynowania** | Aby uzyskać najnowsze informacje, przejrzyj [sieci](../site-recovery/vmware-physical-azure-support-matrix.md#network) i [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#storage) wstępnie wymagane składniki usługi Site Recovery. Usługa Azure Migrate zawiera wymagania identyczne sieci i magazynowania.
**Wymagania platformy Azure** | Aby uzyskać najnowsze informacje, przejrzyj [sieci platformy Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage), i [obliczenia](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) wymagania dotyczące usługi Site Recovery. Usługa Azure Migrate ma identyczne wymagania dotyczące migracji programu VMware.
**Usługa mobilności** | Musi być zainstalowany agent usługi mobilności na każdej maszynie Wirtualnej, które mają zostać zmigrowane.
**Dysk docelowy** | Tylko maszyny wirtualne można migrować do usługi managed disks (HHD standardowa, premium SSD) na platformie Azure.

   

## <a name="agent-based-migration-url-access-requirements"></a>Wymagania dotyczące dostępu oparte na agentach migracji adresu URL

Usługi mobilności uruchomioną na maszynach wirtualnych VMware wymaga połączenia internetowego z Internetu.

- Podczas wdrażania usługi mobilności, wykonuje sprawdzenie łączności z adresami URL podsumowane w poniższej tabeli.
- Jeśli używasz firewall.proxy opartego na adresach URL umożliwiają dostęp do tych adresów URL, upewniając się, że serwer proxy usuwa wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.

**Adres URL** | **Szczegóły**  
--- | --- 
*.portal.azure.com | Przejdź do usługi Azure Migrate w witrynie Azure portal.
*.windows.net | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com | Tworzenie aplikacji usługi Active Directory dla urządzenia do komunikowania się z usługą Azure Migrate. 
management.azure.com | Tworzenie aplikacji usługi Active Directory dla urządzenia do komunikowania się z usługą Azure Migrate.
dc.services.visualstudio.com | Przekaż Dzienniki aplikacji, używany do monitorowania wewnętrznego.
*.vault.azure.net | Zarządzanie wpisami tajnymi w usłudze Azure Key Vault.
*.servicebus.windows.net | Komunikacja między urządzeniem i usługa Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Połącz z adresami URL usługi Azure Migrate.
*.blob.core.windows.net | Przekazywanie danych do konta magazynu.

## <a name="agent-based-migration-port-requirements"></a>Wymagania portu migracji opartej o agentów

**urządzenia** | **połączenia**
--- | --- 
Maszyny wirtualne | Usługi mobilności uruchomioną na maszynach wirtualnych komunikuje się z lokalnym serwerem konfiguracji na porcie HTTPS 443 dla ruchu przychodzącego na potrzeby zarządzania replikacją.<br/><br/> Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 dla ruchu przychodzącego. Ten port może być modyfikowany.
Urządzenie replikacji | Urządzenie replikacji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
Serwer przetwarzania | Serwer przetwarzania odbiera dane replikacji, optymalizuje je szyfruje i wysyła je do usługi Azure storage za pośrednictwem portu 443 wychodzących.<br/> Domyślnie serwer przetwarzania jest uruchamiany na urządzeniu replikacji.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny Wirtualnej platformy Azure

Wszystkie lokalne maszyny wirtualne replikowane na platformie Azure muszą spełniać wymagania maszyny Wirtualnej platformy Azure podsumowane w poniższej tabeli. Po uruchomieniu sprawdzania wymagań wstępnych w przypadku replikacji usługa Site Recovery wyboru zakończy się niepowodzeniem, jeśli niektóre wymagania nie są spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Sprawdź obsługiwane systemy operacyjne dla [maszyn wirtualnych VMware przy użyciu agentów replikacji](#agentless-migration-vmware-vm-requirements)oraz [maszyn wirtualnych VMware przy użyciu replikacji opartej o agentów](#agent-based-migration-vmware-vm-requirements).<br/> Można migrować dowolne obciążenia uruchomione na obsługiwanym systemie operacyjnym. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Architektura systemu operacyjnego gościa | 64-bitowych. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Rozmiar dysku systemu operacyjnego | Do 2048 GB. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Liczba dysków systemu operacyjnego | 1 | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Liczba dysków danych | 64 lub mniej. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Rozmiar dysku danych | Do 4095 GB. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Karty sieciowe | Wiele kart sieciowych, są obsługiwane. | 
Udostępniony wirtualny dysk twardy | Nieobsługiwane. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
Dysk FC | Nieobsługiwane. | Sprawdzenie nie powiedzie się, jeśli jest nieobsługiwany.
BitLocker | Nieobsługiwane. | Przed włączeniem replikacji dla maszyny, należy wyłączyć funkcję BitLocker. 
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa maszyny musi zaczynać i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach maszyny w usłudze Site Recovery.
Połącz po migracji Windows | Połączyć się z systemem Windows po migracji maszyn wirtualnych platformy Azure:<br/> — Przed migracją Włącz protokół RDP na lokalnej maszyny Wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.<br/> Aby uzyskać dostęp do sieci VPN typu lokacja lokacja, Włącz protokół RDP i Zezwalaj na RDP w **zapory Windows** -> **dozwolone aplikacje i funkcje** dla **domena i prywatne** sieci. Ponadto należy sprawdzić, czy zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135). | 
Połącz po migracji — Linux | Aby podłączyć się do maszyn wirtualnych platformy Azure po migracji przy użyciu protokołu SSH:<br/> Przed migracją na maszynie lokalnej sprawdź, czy Usługa Secure Shell jest ustawiona na początku i reguły zapory zezwalają na połączenie SSH.<br/> Po przejściu w tryb failover na maszynie Wirtualnej platformy Azure, Zezwalaj na połączenia przychodzące do portu SSH dla reguły sieciowej grupy zabezpieczeń w trybie Failover maszyny Wirtualnej i podsieci platformy Azure, do którego jest podłączony. Ponadto należy dodać publiczny adres IP dla maszyny Wirtualnej. |  


## <a name="next-steps"></a>Następne kroki

[Przygotowanie do programu VMware](tutorial-prepare-vmware.md) oceny i migracji.








