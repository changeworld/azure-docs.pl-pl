---
title: Urządzenie usługi Azure Migrate
description: Zawiera omówienie urządzenia Azure Migrate używanego w ocenie i migracji serwera.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1b1e35c3b7a9d98e57ec4261f6f913c370bbb365
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269577"
---
# <a name="azure-migrate-appliance"></a>Urządzenie usługi Azure Migrate

W tym artykule opisano urządzenie Azure Migrate. Urządzenie jest wdrażane w przypadku korzystania z [Azure Migrate: narzędzia do oceny serwera](migrate-services-overview.md#azure-migrate-server-assessment-tool) do odnajdywania i oceniania aplikacji, infrastruktury i obciążeń na potrzeby migracji do Microsoft Azure. Urządzenie jest również używane w przypadku migrowania maszyn wirtualnych VMware na platformę Azure przy użyciu [Azure Migrate: Ocena serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) z [migracją bez agenta](server-migrate-overview.md).

## <a name="appliance-overview"></a>Przegląd urządzenia

Urządzenie Azure Migrate jest używane w następujących scenariuszach.

**Scenariusz** | **Narzędzie** | **Używane dla** 
--- | --- | ---
Maszyna wirtualna VMware | Azure Migrate: Ocena serwera<br/><br/> Azure Migrate: Migracja serwera | Odnajdywanie maszyn wirtualnych VMware<br/><br/> Odnajdywanie aplikacji i zależności maszyn<br/><br/> Zbieraj metadane maszyn i metadane wydajności dla ocen.<br/><br/> Replikowanie maszyn wirtualnych VMware z migracją bez agenta.
Maszyna wirtualna funkcji Hyper-V | Azure Migrate: Ocena serwera | Odnajdywanie maszyn wirtualnych funkcji Hyper-V<br/><br/> Zbieraj metadane maszyn i metadane wydajności dla ocen.
Maszyna fizyczna |  Azure Migrate: Ocena serwera |  Odnajdywanie serwerów fizycznych<br/><br/> Zbieraj metadane maszyn i metadane wydajności dla ocen.

## <a name="appliance---vmware"></a>Urządzenie — VMware 

**Wymaganie** | **VMware** 
--- | ---
**Pobierz format** | . JAJOWYM 
**Link pobierania** | https://aka.ms/migrate/appliance/vmware 
**Rozmiar pobieranych plików** | 11,2 GB
**Licencjonowan** | Pobrany szablon urządzenia zawiera licencję ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres próbny zbliża się do wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia albo Aktywowanie licencji na maszynę wirtualną urządzenia.
**Wdrożenie** | Urządzenie jest wdrażane jako maszyna wirtualna VMware. Musisz mieć wystarczającą ilość zasobów na vCenter Server, aby przydzielić maszynę wirtualną z 32 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym.<br/> Urządzenie wymaga dostępu do Internetu, bezpośrednio lub za pomocą serwera proxy.<br/> Urządzenie może połączyć się z pojedynczym vCenter Server.
**Sprzęt** | Zasoby w programie vCenter do przydzielenia maszyny wirtualnej z 32 GB pamięci RAM 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym. 
**Wartość skrótu** | Sprawdź [tutaj](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security)
**serwer vCenter/Host** | Maszynę wirtualną urządzenia należy wdrożyć na hoście ESXi z systemem w wersji 5,5 lub nowszej.<br/><br/> vCenter Server uruchomione 5,5, 6,0, 6,5 lub 6,7.
**Projekt Azure Migrate** | Urządzenie może być skojarzone z pojedynczym projektem. <br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem.<br/> 
**Odnajdowa** | Urządzenie może wykryć do 10 000 maszyn wirtualnych VMware na vCenter Server.<br/> Urządzenie może połączyć się z pojedynczym vCenter Server.
**Składniki urządzenia** | Aplikacja zarządzania: aplikacja sieci Web w urządzeniu do wprowadzania danych przez użytkownika podczas wdrażania.<br/> Agent odnajdywania: zbiera dane konfiguracji maszyny.<br/> Agent oceny: Zbierz dane wydajności.<br/> Agent DRA: organizuje replikację maszyny wirtualnej i koordynuje komunikację między maszynami/platformą Azure.<br/> Brama: wysyła zreplikowane dane na platformę Azure.<br/> Usługa aktualizacji AutoUpdate: aktualizuje składniki (są uruchamiane co 24 godziny).
**VDDK (migracja bez wykorzystania agentów)** | W przypadku korzystania z migracji bez agenta z migracją Azure Migrate Server należy zainstalować na maszynie wirtualnej urządzenia VMware vSphere VDDK.


## <a name="appliance---hyper-v"></a>Urządzenie-Hyper-V

**Wymaganie** | **Funkcja Hyper-V** 
--- | ---
**Pobierz format** | Folder spakowany (z dyskiem VHD)
**Link pobierania** | https://aka.ms/migrate/appliance/hyperv 
**Rozmiar pobieranych plików** | 10 GB
**Licencjonowan** | Pobrany szablon urządzenia zawiera licencję ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres próbny zbliża się do wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia albo Aktywowanie licencji na maszynę wirtualną urządzenia.
**Wdrażanie urządzenia**   |  Urządzenie jest wdrażane jako maszyna wirtualna funkcji Hyper-V.<br/> Maszyna wirtualna z urządzeniem Azure Migrate jest maszyną wirtualną funkcji Hyper-V w wersji 5,0.<br/> Na hoście funkcji Hyper-V musi być uruchomiony system Windows Server 2012 R2 lub nowszy.<br/> Host wymaga wystarczającej ilości miejsca, aby przydzielić 16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca do magazynowania oraz przełącznik zewnętrzny dla maszyny wirtualnej urządzenia.<br/> Urządzenie musi mieć statyczny lub dynamiczny adres IP oraz dostęp do Internetu.
**Sprzęt** | Zasoby na hoście funkcji Hyper-V przydzielają 16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca do magazynowania oraz przełącznik zewnętrzny dla maszyny wirtualnej urządzenia.
**Wartość skrótu** | Sprawdź [tutaj](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security)
**Host funkcji Hyper-V** | Uruchamianie systemu Windows Server 2012 R2 lub nowszego.
**Projekt Azure Migrate** | Urządzenie może być skojarzone z pojedynczym projektem. <br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem.<br/> 
**Odnajdowa** | Urządzenie może wykryć do 5000 maszyn wirtualnych funkcji Hyper-V.<br/> Urządzenie może połączyć się z maksymalnie 300 hostami funkcji Hyper-V.
**Składniki urządzenia** | Aplikacja zarządzania: aplikacja sieci Web w urządzeniu do wprowadzania danych przez użytkownika podczas wdrażania.<br/> Agent odnajdywania: zbiera dane konfiguracji maszyny.<br/> Agent oceny: Zbierz dane wydajności.<br/>  Usługa aktualizacji AutoUpdate: aktualizuje składniki (są uruchamiane co 24 godziny).


## <a name="appliance---physical"></a>Urządzenie — fizyczne

**Wymaganie** | **Bezpośrednim** 
--- | ---
**Pobierz format** | Folder spakowany (z skryptem Instalatora opartym na programie PowerShell)
**Link pobierania** | [Link pobierania](https://go.microsoft.com/fwlink/?linkid=2105112)
**Rozmiar pobieranych plików** | 59,7 MB
**Sprzęt** | Dedykowany komputer fizyczny lub Użyj maszyny wirtualnej. Urządzenie z systemem wymaga 16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca do magazynowania oraz przełącznik zewnętrzny.<br/> Urządzenie musi mieć statyczny lub dynamiczny adres IP oraz dostęp do Internetu.
**Wartość skrótu** | Sprawdź [tutaj](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical#verify-security)
**System operacyjny** | Na komputerze powinien działać system Windows Server 2016. 
**Wdrażanie urządzenia**   |  Skrypt Instalatora urządzenia jest pobierany z portalu (w folderze spakowanym). <br/> Rozpakujesz folder i uruchomisz skrypt programu PowerShell (AzureMigrateInstaller. ps1).
**Odnajdowa** | Urządzenie może wykryć do 250 serwerów fizycznych.
**Składniki urządzenia** | Aplikacja zarządzania: aplikacja sieci Web w urządzeniu do wprowadzania danych przez użytkownika podczas wdrażania.<br/> Agent odnajdywania: zbiera dane konfiguracji maszyny.<br/> Agent oceny: Zbierz dane wydajności.<br/>  Usługa aktualizacji AutoUpdate: aktualizuje składniki (są uruchamiane co 24 godziny).


## <a name="url-access"></a>Dostęp do adresu URL

Urządzenie Azure Migrate wymaga połączenia z Internetem.

- Podczas wdrażania urządzenia Azure Migrate sprawdza połączenie adresów URL, które zostały podsumowane w poniższej tabeli.
- Jeśli używasz serwera proxy opartego na adresie URL do łączenia się z Internetem, Zezwól na dostęp do tych adresów URL, upewniając się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.com  | Przejdź do Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Utwórz Active Directory aplikacje dla urządzenia w celu komunikowania się z Azure Migrate.
management.azure.com | Utwórz Active Directory aplikacje dla urządzenia, aby komunikować się z usługą Azure Migrate.
dc.services.visualstudio.com | Przekaż Dzienniki aplikacji używane do wewnętrznego monitorowania.
*.vault.azure.net | Zarządzanie wpisami tajnymi w Azure Key Vault.
aka.ms/* | Zezwalaj na dostęp do linków aliasów. Używany do Azure Migrate aktualizacji urządzenia.
download.microsoft.com/download | Zezwalaj na pobieranie z usługi Microsoft Download.
*.servicebus.windows.net | Komunikacja między urządzeniem a usługą Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Połącz się z adresami URL usługi Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Używane do migracji bez agentów programu VMware**<br/><br/> Połącz się z adresami URL usługi Azure Migrate.
*.blob.core.windows.net |  **Używane do migracji bez agentów programu VMware**<br/><br/>Przekaż dane do magazynu na potrzeby migracji.




## <a name="collected-data---vmware"></a>Zebrane dane — VMware

### <a name="collected-performance-data-vmware"></a>Zebrane dane wydajności — VMware

Oto dane wydajności maszyny wirtualnej VMware, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Przeciw** | **Wpływ oceny**
--- | --- | ---
Wykorzystanie procesora | cpu.usage.average | Zalecany rozmiar maszyny wirtualnej/koszt
Użycie pamięci | mem.usage.average | Zalecany rozmiar maszyny wirtualnej/koszt
Przepływność odczytu dysku (MB na sekundę) | virtualDisk.read.average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Przepływność zapisu na dysku (MB na sekundę) | virtualDisk.write.average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Operacje odczytu z dysku na sekundę | virtualDisk.numberReadAveraged.average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Operacje zapisu na dysku na sekundę | virtualDisk.numberWriteAveraged.average  | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Przepływność odczytu karty sieciowej (MB na sekundę) | NET. Receive. Average | Obliczanie rozmiaru maszyny wirtualnej
Przepływność zapisu kart sieciowych (MB na sekundę) | net.transmitted.average  |Obliczanie rozmiaru maszyny wirtualnej


### <a name="collected-metadata-vmware"></a>Zebrane metadane — VMware

> [!NOTE]
> Metadane wykryte przez urządzenie Azure Migrate są używane, aby pomóc Ci w odpowiednim rozmiarze aplikacji podczas migrowania ich do platformy Azure, przeprowadzać analizę przydatności na platformie Azure, analizę zależności aplikacji i planowanie kosztów. Firma Microsoft nie używa tych danych w odniesieniu do żadnej inspekcji zgodności licencji.

Poniżej znajduje się pełna lista metadanych maszyn wirtualnych VMware, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Przeciw**
--- | --- 
**Szczegóły maszyny** | 
IDENTYFIKATOR MASZYNY WIRTUALNEJ | vm.Config.InstanceUuid 
Nazwa maszyny wirtualnej | maszyn. Config.Name
Identyfikator vCenter Server | VMwareClient. Instance. UUID
Opis maszyny wirtualnej | maszyn. Summary. config. Annotation
Nazwa produktu licencji | vm.Client.ServiceContent.About.LicenseProductName
Typ systemu operacyjnego | vm.SummaryConfig.GuestFullName
Typ rozruchu | vm.Config.Firmware
Liczba rdzeni | maszyn. Config. Hardware. NumCPU
Pamięć (MB) | maszyn. Config. Hardware. MemoryMB
Liczba dysków | maszyn. Config. Hardware. Device. ToList — (). FindAll (x = > to VirtualDisk). Count
Lista rozmiarów dysku | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
Lista kart sieciowych | maszyn. Config. Hardware. Device. ToList — (). FindAll (x = > to VirtualEthernet). Count
Wykorzystanie procesora | cpu.usage.average
Użycie pamięci |mem.usage.average
**Szczegóły dysku** | 
Wartość klucza dysku | 3,5. Głównych
Numer Dikunit | 3,5. UnitNumber
Wartość klucza kontrolera dysku | 3,5. ControllerKey. Value
Gigabajty inicjowane | virtualDisk. DeviceInfo. Summary
Nazwa dysku | Wartość wygenerowana przy użyciu dysku. UnitNumber, dysk. Klucz, dysk. ControllerKey. VAlue
Operacje odczytu na sekundę | virtualDisk.numberReadAveraged.average
Operacje zapisu na sekundę | virtualDisk.numberWriteAveraged.average
Przepływność odczytu (MB na sekundę) | virtualDisk.read.average
Przepływność zapisu (MB na sekundę) | virtualDisk.write.average
**Na szczegóły karty sieciowej** | 
Nazwa karty sieciowej | 10/100/1000. Głównych
Adres MAC | (Karta sieciowa (VirtualEthernetCard)). MacAddress
Adresy IPv4 | vm.Guest.Net
Adresy IPv6 | vm.Guest.Net
Przepływność odczytu (MB na sekundę) | NET. Receive. Average
Przepływność zapisu (MB na sekundę) | net.transmitted.average
**Szczegóły ścieżki spisu** | 
Name (Nazwa) | container.GetType().Name
Typ obiektu podrzędnego | wbudowane. Typ podrzędny
Szczegóły odwołania | wbudowane. MoRef
Szczegóły nadrzędne | Kontener. Parent
Szczegóły folderu na maszynę wirtualną | ((Folder)container).ChildEntity.Type
Szczegóły centrum danych na maszynę wirtualną | ((Centrum danych) kontener). VmFolder
Szczegóły centrum danych na folder hosta | ((Centrum danych) kontener). HostFolder
Szczegóły klastra na hosta | ((ClusterComputeResource)container).Host
Szczegóły hosta na maszynę wirtualną | ((HostSystem) kontener). MASZYN

## <a name="collected-data---hyper-v"></a>Zebrane dane — funkcja Hyper-V

### <a name="collected-performance-data-hyper-v"></a>Zebrane dane wydajności — funkcja Hyper-V

> [!NOTE]
> Metadane wykryte przez urządzenie Azure Migrate są używane, aby pomóc Ci w odpowiednim rozmiarze aplikacji podczas migrowania ich do platformy Azure, przeprowadzać analizę przydatności na platformie Azure, analizę zależności aplikacji i planowanie kosztów. Firma Microsoft nie używa tych danych w odniesieniu do żadnej inspekcji zgodności licencji.

Poniżej przedstawiono dane wydajności maszyny wirtualnej funkcji Hyper-IT zbierane i wysyłane na platformę Azure.

**Klasa licznika wydajności** | **Przeciw** | **Wpływ oceny**
--- | --- | ---
Procesor wirtualny funkcji hypervisor funkcji Hyper-V | Czas działania gościa (%) | Zalecany rozmiar maszyny wirtualnej/koszt
Maszyna wirtualna pamięć dynamiczna funkcji Hyper-V | Bieżące ciśnienie (%)<br/> Ilość pamięci fizycznej widocznej dla gościa (MB) | Zalecany rozmiar maszyny wirtualnej/koszt
Wirtualne urządzenie magazynujące funkcji Hyper-V | Bajty odczytu/s | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Wirtualne urządzenie magazynujące funkcji Hyper-V | Bajty zapisu/s | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Karta Virtual Network funkcji Hyper-V | Bajty odebrane/s | Obliczanie rozmiaru maszyny wirtualnej
Karta Virtual Network funkcji Hyper-V | Bajty wysłane/s | Obliczanie rozmiaru maszyny wirtualnej

- Użycie procesora CPU jest sumą wszystkich zastosowań dla wszystkich procesorów wirtualnych podłączonych do maszyny wirtualnej.
- Użycie pamięci to (bieżące ciśnienie * widoczna pamięć fizyczna gościa)/100.
- Wartości wykorzystania dysku i sieci są zbierane z wymienionych liczników wydajności funkcji Hyper-V.

### <a name="collected-metadata-hyper-v"></a>Zebrane metadane — funkcja Hyper-V

Poniżej znajduje się pełna lista metadanych maszyn wirtualnych funkcji Hyper-V, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Klasa WMI** | **Właściwość klasy usługi WMI**
--- | --- | ---
**Szczegóły maszyny** | 
Numer seryjny BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Typ maszyny wirtualnej (Gen 1 lub 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nazwa wyświetlana maszyny wirtualnej | Msvm_VirtualSystemSettingData | ElementName
Wersja maszyny wirtualnej | Msvm_ProcessorSettingData | VirtualQuantity
Pamięć (w bajtach) | Msvm_MemorySettingData | VirtualQuantity
Maksymalna ilość pamięci, która może być używana przez maszynę wirtualną | Msvm_MemorySettingData | Limit
Włączono pamięć dynamiczną | Msvm_MemorySettingData | DynamicMemoryEnabled
Nazwa/wersja systemu operacyjnego/nazwa FQDN | Msvm_KvpExchangeComponent | Dane nazwy GuestIntrinsicExchangeItems
Stan zasilacza maszyny wirtualnej | Msvm_ComputerSystem | EnabledState
**Szczegóły dysku** | 
Identyfikator dysku | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | Typ
Rozmiar wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Element nadrzędny wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | ParentPath
**Na szczegóły karty sieciowej** | 
Adresy IP (syntetyczne karty sieciowe) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
Protokół DHCP włączony (syntetyczne karty sieciowe) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
Identyfikator karty sieciowej (syntetyczne karty sieciowe) | Msvm_SyntheticEthernetPortSettingData | Identyfikator wystąpienia
Adres MAC karty sieciowej (syntetyczne karty sieciowe) | Msvm_SyntheticEthernetPortSettingData | Adres
Identyfikator karty sieciowej (starsze karty sieciowe) | MsvmEmulatedEthernetPortSetting dane | Identyfikator wystąpienia
Identyfikator MAC karty sieciowej (starsze karty sieciowe) | MsvmEmulatedEthernetPortSetting dane | Adres




## <a name="discovery-and-collection-process"></a>Proces odnajdywania i kolekcjonowania

Urządzenie komunikuje się z serwerami vCenter i hostami/klastrami funkcji Hyper-V przy użyciu następującego procesu.

1. **Rozpocznij odnajdowanie**:
    - Po rozpoczęciu odnajdywania na urządzeniu funkcji Hyper-V komunikuje się z hostami funkcji Hyper-V na portach usługi WinRM 5985 (HTTP) i 5986 (HTTPS).
    - Po rozpoczęciu odnajdywania na urządzeniu VMware program domyślnie komunikuje się z serwerem vCenter na porcie TCP 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można go skonfigurować w aplikacji sieci Web urządzenia.
2. **Zbierz dane dotyczące metadanych i wydajności**:
    - Urządzenie używa sesji model wspólnych informacji (CIM) do zbierania danych maszyny wirtualnej funkcji Hyper-V z hosta funkcji Hyper-V na portach 5985 i 5986.
    - Urządzenie domyślnie komunikuje się z portem 443, aby zebrać dane maszyn wirtualnych VMware z vCenter Server.
3. **Wyślij dane**: urządzenie wysyła zebrane dane w celu Azure Migrate oceny serwera i migracji serwera Azure Migrate przez port SSL 443. Urządzenie może połączyć się z platformą Azure za pośrednictwem Internetu lub można użyć usługi ExpressRoute z usługą komunikacji równorzędnej Public/Microsoft.
    - W przypadku danych dotyczących wydajności urządzenie zbiera dane użycia w czasie rzeczywistym.
        - Dane dotyczące wydajności są zbierane co 20 sekund dla programu VMware i co 30 sekund dla funkcji Hyper-V dla każdej metryki wydajności.
        - Zebrane dane są zestawiane w celu utworzenia jednego punktu danych przez 10 minut.
        - Wartość szczytowego wykorzystania jest wybierana ze wszystkich 20/30-sekundowych punktów danych i wysyłana do platformy Azure w celu obliczenia oceny.
        - W oparciu o wartość percentylu określoną we właściwościach oceny (pięćdziesiąt/90/używany 95./99), dziesięć minutowe punkty są sortowane w kolejności rosnącej, a odpowiednia wartość percentylu jest używana do obliczania oceny
    - W przypadku migracji serwera urządzenie uruchamia zbieranie danych maszyny wirtualnej i replikuje je na platformie Azure.
4. **Ocenianie i migrowanie**: teraz można tworzyć oceny z metadanych zebranych przez urządzenie przy użyciu funkcji oceny serwera Azure Migrate. Ponadto można rozpocząć Migrowanie maszyn wirtualnych VMware przy użyciu migracji serwera Azure Migrate, aby organizować replikację maszyn wirtualnych bez agentów.


![Architektura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Uaktualnienia urządzeń

Urządzenie zostanie uaktualnione w miarę aktualizowania Azure Migrate agentów działających na urządzeniu.

- Dzieje się tak automatycznie, ponieważ automatyczna aktualizacja jest domyślnie włączona na urządzeniu.
- Można zmienić to ustawienie domyślne, aby ręcznie zaktualizować agentów.
- Aby wyłączyć automatyczne aktualizowanie, przejdź do edytora rejestru > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance i Ustaw klucz rejestru "AutoUpdate" na 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Ustaw aktualizacje agenta na ręczne

W przypadku aktualizacji ręcznych upewnij się, że wszystkie agenci na urządzeniu zostały zaktualizowane w tym samym czasie, przy użyciu przycisku **Aktualizuj** dla każdego nieaktualnego agenta na urządzeniu. Możesz w dowolnym momencie zmienić ustawienie aktualizacji na aktualizacje automatyczne.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) skonfigurować urządzenie dla programu VMware.
[Dowiedz się, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) skonfigurować urządzenie dla funkcji Hyper-V.

