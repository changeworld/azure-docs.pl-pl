---
title: Architektura urządzenia usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Zawiera omówienie usługi Azure Migrate urządzenia
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811455"
---
# <a name="azure-migrate-appliance"></a>Urządzenie Azure Migrate

W tym artykule opisano urządzenie Azure Migrate. Jeśli używasz narzędzia do oceny migracji platformy Azure i migracji do odnajdywania, oceny i migracji aplikacji, infrastruktury i obciążeń w systemie Microsoft Azure możesz wdrożyć urządzenie. 

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralną Centrum do śledzenia wykrywania, oceny i migracji aplikacji lokalnych i obciążeń i chmury prywatne/publiczne maszyn wirtualnych na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm.



## <a name="appliance-overview"></a>Przegląd urządzenia

Usługa Azure Migrate typy urządzenia i użycia są w następujący sposób.

**Wdrożony jako** | **Używane dla** | **Szczegóły**
--- | --- |  ---
Maszyna wirtualna VMware | Ocena maszyny Wirtualnej VMware za pomocą narzędzia oceny migracji platformy Azure.<br/><br/> Migracja bez wykorzystania agentów maszyny Wirtualnej VMware za pomocą narzędzia migracji serwera migracji platformy Azure | Pobierz szablon OVA i zaimportować do programu vCenter Server, aby utworzyć urządzenie maszyny Wirtualnej.
Maszyna wirtualna funkcji Hyper-V | Ocena maszyny Wirtualnej funkcji Hyper-V za pomocą narzędzia oceny migracji platformy Azure. | Pobierz zip wirtualnego dysku twardego i zaimportuj do funkcji Hyper-V, aby utworzyć urządzenie maszyny Wirtualnej.

## <a name="appliance-access"></a>Dostęp do urządzenia

Po skonfigurowaniu urządzenia można uzyskiwać zdalny dostęp urządzenia maszyny Wirtualnej za pośrednictwem portu TCP 3389. Mogą również zdalnie dostęp do aplikacji sieci web zarządzania dla urządzenia, na porcie 44368 za pomocą adresu URL: ``` https://<appliance-ip-or-name>:44368 ```.

## <a name="appliance-license"></a>Licencja urządzenia
Urządzenie jest powiązana z licencji ewaluacyjnej systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres próbny jest bliski wygaśnięcia, firma Microsoft zaleca, możesz pobrać i wdrożyć nowe urządzenie lub aktywacji licencji systemu operacyjnego urządzenia maszyny Wirtualnej.

## <a name="appliance-agents"></a>Agenci urządzenia
Urządzenie ma zainstalowanych tych agentów.

**Agent** | **Szczegóły**
--- | ---
Agent odnajdowania | Zbiera dane konfiguracyjne z lokalnych maszyn wirtualnych.
Agenta do oceny | Profile w lokalnym środowisku w celu zbierania danych wydajności maszyny Wirtualnej.
Karta migracji | Organizuje replikację maszyny Wirtualnej i służy do koordynowania komunikacji między maszynami wirtualnymi i platformy Azure.
Brama migracji | Wysyła replikowanych danych maszyny Wirtualnej na platformie Azure.


## <a name="appliance-deployment-requirements"></a>Wymagania dotyczące wdrażania urządzenia

- [Przegląd](migrate-support-matrix-vmware.md#assessment-appliance-requirements) wymagania dotyczące wdrażania dla urządzenia VMware, a także adresy URL, które urządzenie musi mieć dostęp.
- [Przegląd](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) wymagania dotyczące wdrażania dla urządzenia w funkcji Hyper-V i adresów URL, które urządzenie musi mieć dostęp.


## <a name="collected-performance-data-vmware"></a>Dane wydajności zebrane-VMware

Poniżej przedstawiono dane wydajności maszyn wirtualnych programu VMware, które urządzenie zbiera i wysyła do usługi Azure.

**Dane** | **Counter** | **Ocena wpływu**
--- | --- | ---
Użycie procesora CPU | cpu.usage.average | Zalecany rozmiar i kosztów maszyn wirtualnych
Użycie pamięci przez program | mem.usage.average | Zalecany rozmiar i kosztów maszyn wirtualnych
Odczyt z dysku przepływność (MB / s) | virtualDisk.read.average | Obliczanie rozmiaru dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
Przepływność zapisu na dysku (MB / s) | virtualDisk.write.average | Obliczanie rozmiaru dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
Operacje na sekundę odczytu z dysku | virtualDisk.numberReadAveraged.average | Obliczanie rozmiaru dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
Dysk zapisu na sekundę | virtualDisk.numberWriteAveraged.average  | Obliczanie rozmiaru dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
Przepływność (MB na sekundę) zapisu karty Sieciowej | net.received.average | Obliczanie rozmiaru maszyny Wirtualnej
Przepływność zapisu karty Sieciowej (w MB na sekundę) | net.transmitted.average  |Obliczanie rozmiaru maszyny Wirtualnej


## <a name="collected-metadata-vmware"></a>Zebrane metadane programu VMware

Oto Pełna lista metadanych maszyny Wirtualnej VMware, które urządzenie zbiera i wysyła do usługi Azure.

**Dane** | **Counter**
--- | --- 
**Szczegóły maszyny** | 
IDENTYFIKATOR MASZYNY WIRTUALNEJ | vm.Config.InstanceUuid 
Nazwa maszyny wirtualnej | Maszyna wirtualna. Config.Name
Identyfikator serwera vCenter | VMwareClient.Instance.Uuid
Opis maszyn wirtualnych | Maszyna wirtualna. Summary.Config.Annotation
Nazwa produktu licencji | vm.Client.ServiceContent.About.LicenseProductName
Typ systemu operacyjnego | vm.SummaryConfig.GuestFullName
Typ rozruchu | vm.Config.Firmware
Liczba rdzeni | Maszyna wirtualna. Config.Hardware.NumCPU
Pamięć (MB) | Maszyna wirtualna. Config.Hardware.MemoryMB
Liczba dysków | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
Lista rozmiar dysku | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
Lista kart sieciowych | Maszyna wirtualna. Config.Hardware.Device.ToList(). FindAll(x => is VirtualEthernet).count
Użycie procesora CPU | cpu.usage.average
Użycie pamięci przez program |mem.usage.average
**Na szczegóły dysku** | 
Wartość klucza na dysku | disk.Key
Liczba Dikunit | disk.UnitNumber
Wartość klucza kontrolera dysku | dysk. ControllerKey.Value
Gigabajty zainicjowano obsługę administracyjną | virtualDisk.DeviceInfo.Summary
Nazwa dysku | Wartość wygenerowany za pomocą dysku. UnitNumber, dysku. Dysk klucza. ControllerKey.VAlue
Operacje odczytu na sekundę | virtualDisk.numberReadAveraged.average
Zapisu na sekundę | virtualDisk.numberWriteAveraged.average
Przepływność odczytu (w MB na sekundę) | virtualDisk.read.average
Zapis przepływność (MB / s) | virtualDisk.write.average
**Na szczegóły karty Sieciowej** | 
Nazwa karty sieciowej | karty sieciowej. Klucz
Adres MAC | ((VirtualEthernetCard)nic).MacAddress
Adresy IPv4 | vm.Guest.Net
Adresy IPv6 | vm.Guest.Net
Przepływność odczytu (w MB na sekundę) | net.received.average
Zapis przepływność (MB / s) | net.transmitted.average
**Szczegóły ścieżki magazynu** | 
Name (Nazwa) | container.GetType().Name
Typ obiektu podrzędnego | container.ChildType
Szczegóły odwołania | kontener. MoRef
Szczegóły nadrzędnego | Container.Parent
Szczegóły folderu na maszynę Wirtualną | ((Folder)container).ChildEntity.Type
Szczegóły centrum danych na maszynę Wirtualną | ((Datacenter)container).VmFolder
Szczegóły centrum danych w folderze hosta | ((Datacenter)container).HostFolder
Szczegóły klastra na hoście | ((ClusterComputeResource)container).Host
Szczegóły hosta dla maszyny Wirtualnej | ((HostSystem) kontenera). MASZYNA WIRTUALNA



## <a name="collected-performance-data-hyper-v"></a>Wydajności zebranych danych niż Hyper-V

Poniżej przedstawiono dane wydajności maszyn wirtualnych programu VMware, które urządzenie zbiera i wysyła do usługi Azure.

**Klasa licznika wydajności** | **Counter** | **Ocena wpływu**
--- | --- | ---
Procesor wirtualny funkcji Hypervisor Hyper-V | Czas działania gościa % | Zalecany rozmiar i kosztów maszyn wirtualnych
Pamięć dynamiczna funkcji Hyper-V maszyny Wirtualnej | Bieżące wykorzystanie (%)<br/> Widoczna pamięć fizyczną gościa (MB) | Zalecany rozmiar i kosztów maszyn wirtualnych
Funkcji Hyper-V wirtualne urządzenie magazynujące | Przeczytaj bajtów na sekundę | Obliczanie rozmiaru dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
Funkcji Hyper-V wirtualne urządzenie magazynujące | Zapis bajtów na sekundę | Obliczanie rozmiaru dysku, koszt przechowywania, rozmiar maszyny Wirtualnej
Funkcji Hyper-V wirtualnej karty sieciowej | Bajty odebrane/s | Obliczanie rozmiaru maszyny Wirtualnej
Funkcji Hyper-V wirtualnej karty sieciowej | Bajty wysłane/s | Obliczanie rozmiaru maszyny Wirtualnej

- Użycie procesora CPU jest sumą całe użycie dla wszystkich procesorów wirtualnych, dołączony do maszyny Wirtualnej.
- Wykorzystanie pamięci (bieżące wykorzystanie * widoczna pamięć fizyczną gościa) / 100.
- Wartości wykorzystania dysku i sieci są zbierane z wymienionych liczniki wydajności funkcji Hyper-V.

## <a name="collected-metadata-hyper-v"></a>Zebrane metadane niż Hyper-V

Oto Pełna lista metadanych maszyny Wirtualnej funkcji Hyper-V, które urządzenie zbiera i wysyła do usługi Azure.

**Dane** | **Klasy usługi WMI** | **Właściwość klasy usługi WMI**
--- | --- | ---
**Szczegóły maszyny** | 
Numer seryjny systemu BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Typ maszyny Wirtualnej (Gen 1 lub 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nazwa wyświetlana maszyny Wirtualnej | Msvm_VirtualSystemSettingData | ElementName
Wersja maszyny Wirtualnej | Msvm_ProcessorSettingData | VirtualQuantity
Pamięć (w bajtach) | Msvm_MemorySettingData | VirtualQuantity
Maksymalna ilość pamięci, które mogą być używane przez maszynę Wirtualną | Msvm_MemorySettingData | Limit
Pamięci dynamicznej | Msvm_MemorySettingData | DynamicMemoryEnabled
Nazwa/wersja/nazwę FQDN systemu operacyjnego | Msvm_KvpExchangeComponent | GuestIntrinsciExchangeItems Name Data
Stan zasilania maszyny Wirtualnej | Msvm_ComputerSystem | Atrybut EnabledState
**Na szczegóły dysku** | 
Identyfikator dysku | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | Type
Rozmiar wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Nadrzędny wirtualny dysk twardy | Msvm_VirtualHardDiskSettingData | ParentPath
**Na szczegóły karty Sieciowej** | 
Adresy IP (syntetycznych kart sieciowych) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
Protokół DHCP włączony (syntetycznych kart sieciowych) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
Identyfikator karty Sieciowej (syntetycznych kart sieciowych) | Msvm_SyntheticEthernetPortSettingData | Identyfikator wystąpienia
Adres MAC karty Sieciowej (syntetycznych kart sieciowych) | Msvm_SyntheticEthernetPortSettingData | Adres
Identyfikator kontrolera interfejsu Sieciowego (NIC starszej wersji) | MsvmEmulatedEthernetPortSetting Data | Identyfikator wystąpienia
Identyfikator MAC karty Sieciowej (starszych kart sieciowych) | MsvmEmulatedEthernetPortSetting Data | Adres




## <a name="discovery-and-collection-process"></a>Proces odnajdywania i kolekcji

Urządzenia komunikuje się z serwery vCenter i hosty funkcji Hyper-V/klaster przy użyciu następującego procesu.


1. **Uruchom odnajdowanie**:
    - Po uruchomieniu odnajdywania na urządzeniu funkcji Hyper-V komunikuje się z hostami funkcji Hyper-V na portów usługi WinRM 5985 (HTTP) i 5986 (HTTPS).
    - Po uruchomieniu odnajdywania na urządzeniu VMware komunikuje się z oprogramowaniem vCenter server przez port 443 protokołu TCP, domyślnie. Jeśli serwer vCenter nasłuchuje na innym porcie, można skonfigurować go w aplikacji sieci web urządzenia.
2. **Zbieranie danych wydajności i metadanych**:
    - Urządzenie używa sesji modelu wspólnych informacji (CIM) w celu zbierania danych funkcji Hyper-V maszyny Wirtualnej z hosta funkcji Hyper-V na portach 5985 i 5986.
    - Urządzenia komunikuje się z portu 443, aby domyślnie zbierania danych maszyn wirtualnych VMware z programu vCenter Server.
3. **Wysyłanie danych**: Urządzenie wysyła zebrane dane do usługi Azure migracji serwera oceny i migracji serwera migracji platformy Azure za pośrednictwem portu SSL 443.
    - Dane dotyczące wydajności urządzenie zbiera dane użycia w czasie rzeczywistym.
        - Dane wydajności są zbierane, co 20 sekund dla oprogramowania VMware i funkcji Hyper-v, co 30 sekund dla każdego metryki wydajności.
        - Zebrane dane są rzutowane do utworzenia pojedynczego punktu danych przez dziesięć minut.
        - Wybrano wartość Użycie szczytowe we wszystkich 20/30 drugie punktów danych i wysyłane do platformy Azure na potrzeby obliczania oceny.
        - Na podstawie percentyla wartości określonej we właściwościach oceny (50/90/95/99th), punkty 10 minutowe są sortowane w kolejności rosnącej, a wartość odpowiednią jest używana do obliczenia oceny
    - Podczas migracji serwera urządzenie rozpoczyna zbieranie danych maszyny Wirtualnej i replikuje ją na platformie Azure.
4. **Ocenianie i Migrowanie**: Teraz można utworzyć ocen z metadanych zebrany przez urządzenia przy użyciu oceny Server migracji platformy Azure. Ponadto możesz także uruchomić migracji maszyn wirtualnych VMware przy użyciu usługi Azure Migration migracji serwera do organizowania replikacji maszyny Wirtualnej bez wykorzystania agentów.


![Architektura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Uaktualnienia urządzenia

Urządzenie jest uaktualniany, jak agentów usługi Azure Migrate uruchomiona na urządzeniu są aktualizowane.

- Zdarza się to automatycznie automatycznej aktualizacji jest domyślnie włączone na urządzeniu.
- Można zmienić to ustawienie domyślne, aby ręcznie zaktualizować agentów.
- Aby wyłączyć automatyczne aktualizacje, należy ustawić klucz rejestru Autoaktualizacja urządzenia w HKLM\SOFTWAREMicrosoft\Azure.

### <a name="set-agent-updates-to-manual"></a>Aktualizacje agenta zestaw ręczny

Ręczne aktualizacje, pamiętaj o zaktualizowaniu wszystkich agentów na urządzeniu w tym samym czasie za pomocą **aktualizacji** przycisku dla każdego agenta przestarzałe na urządzeniu. Ustawienie aktualizacji Przełącz się do aktualizacji automatycznych w dowolnym momencie.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) skonfigurować urządzenie dla oprogramowania VMware.
[Dowiedz się, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) skonfigurować urządzenie dla funkcji Hyper-V.

