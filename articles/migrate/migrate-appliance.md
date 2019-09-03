---
title: Architektura urządzenia Azure Migrate | Microsoft Docs
description: Zawiera przegląd urządzenia Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: e2faa47a58283623747ae569de22e1c57df1a51f
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231134"
---
# <a name="azure-migrate-appliance"></a>Urządzenie usługi Azure Migrate

W tym artykule opisano urządzenie Azure Migrate. Urządzenie jest wdrażane w przypadku korzystania z narzędzi do oceny i migracji Azure Migrate w celu odnajdywania, oceniania i migrowania aplikacji, infrastruktury i obciążeń do Microsoft Azure. 

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmur prywatnych/publicznych na platformie Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.



## <a name="appliance-overview"></a>Przegląd urządzenia

Typy i użycie urządzenia Azure Migrate są następujące.

**Wdrożone jako** | **Używane dla** | **Szczegóły**
--- | --- |  ---
Maszyna wirtualna VMware | Ocena maszyn wirtualnych VMware za pomocą narzędzia do oceny Azure Migrate.<br/><br/> Migracja bezagentowa maszyn wirtualnych VMware za pomocą narzędzia migracji Azure Migrate Server | Pobierz szablon komórki jajowe i Importuj do vCenter Server, aby utworzyć maszynę wirtualną z urządzeniem.
Maszyna wirtualna funkcji Hyper-V | Ocena maszyn wirtualnych funkcji Hyper-V za pomocą narzędzia do oceny Azure Migrate. | Pobierz spakowany dysk VHD i zaimportuj go do funkcji Hyper-V, aby utworzyć maszynę wirtualną z urządzeniem.

## <a name="appliance-access"></a>Dostęp do urządzenia

Po skonfigurowaniu urządzenia można zdalnie uzyskać dostęp do maszyny wirtualnej urządzenia za pośrednictwem portu TCP 3389. Możesz również zdalnie uzyskać dostęp do aplikacji do zarządzania siecią Web dla urządzenia, na porcie 44368 z adresem `https://<appliance-ip-or-name>:44368`URL:.

## <a name="appliance-license"></a>Licencja urządzenia
Urządzenie jest dostarczane z licencją ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres próbny zbliża się do wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia albo Aktywowanie licencji na maszynę wirtualną urządzenia.

## <a name="appliance-agents"></a>Agenci urządzeń
Urządzenie ma zainstalowanych tych agentów.

**Agent** | **Szczegóły**
--- | ---
Agent odnajdywania | Zbiera dane konfiguracyjne lokalnych maszyn wirtualnych
Agent oceny | Profiluje środowisko lokalne w celu zbierania danych wydajności maszyn wirtualnych.
Adapter migracji | Organizuje replikację maszyny wirtualnej i koordynuje komunikację między maszynami wirtualnymi i platformą Azure.
Brama migracji | Wysyła zreplikowane dane maszyn wirtualnych do platformy Azure.


## <a name="appliance-deployment-requirements"></a>Wymagania dotyczące wdrażania urządzeń

- [Zapoznaj](migrate-support-matrix-vmware.md#assessment-appliance-requirements) się z wymaganiami dotyczącymi wdrożenia urządzenia VMware i adresami URL, do których urządzenie musi uzyskać dostęp.
- [Zapoznaj](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) się z wymaganiami dotyczącymi wdrożenia urządzenia funkcji Hyper-V i adresami URL, do których urządzenie musi uzyskać dostęp.


## <a name="collected-performance-data-vmware"></a>Zebrane dane wydajności — VMware

Oto dane wydajności maszyny wirtualnej VMware, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Counter** | **Wpływ oceny**
--- | --- | ---
Użycie procesora CPU | cpu.usage.average | Zalecany rozmiar maszyny wirtualnej/koszt
Użycie pamięci | mem.usage.average | Zalecany rozmiar maszyny wirtualnej/koszt
Przepływność odczytu dysku (MB na sekundę) | virtualDisk.read.average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Przepływność zapisu na dysku (MB na sekundę) | virtualDisk.write.average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Operacje odczytu z dysku na sekundę | virtualDisk.numberReadAveraged.average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Operacje zapisu na dysku na sekundę | virtualDisk.numberWriteAveraged.average  | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Przepływność odczytu karty sieciowej (MB na sekundę) | NET. Receive. Average | Obliczanie rozmiaru maszyny wirtualnej
Przepływność zapisu karty sieciowej (MB na sekundę) | net.transmitted.average  |Obliczanie rozmiaru maszyny wirtualnej


## <a name="collected-metadata-vmware"></a>Zebrane metadane — VMware

> [!NOTE]
> Metadane wykryte przez urządzenie Azure Migrate są używane, aby pomóc Ci w odpowiednim rozmiarze aplikacji podczas migrowania ich do platformy Azure, przeprowadzać analizę przydatności na platformie Azure, analizę zależności aplikacji i planowanie kosztów. Firma Microsoft nie używa tych danych w odniesieniu do żadnej inspekcji zgodności licencji.

Poniżej znajduje się pełna lista metadanych maszyn wirtualnych VMware, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Counter**
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
Użycie procesora CPU | cpu.usage.average
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
Name | container.GetType().Name
Typ obiektu podrzędnego | wbudowane. Typ podrzędny
Szczegóły odwołania | wbudowane. MoRef
Szczegóły nadrzędne | Kontener. Parent
Szczegóły folderu na maszynę wirtualną | ((Folder)container).ChildEntity.Type
Szczegóły centrum danych na maszynę wirtualną | ((Centrum danych) kontener). VmFolder
Szczegóły centrum danych na folder hosta | ((Centrum danych) kontener). HostFolder
Szczegóły klastra na hosta | ((ClusterComputeResource)container).Host
Szczegóły hosta na maszynę wirtualną | ((HostSystem) kontener). MASZYN



## <a name="collected-performance-data-hyper-v"></a>Zebrane dane wydajności — funkcja Hyper-V

> [!NOTE]
> Metadane wykryte przez urządzenie Azure Migrate są używane, aby pomóc Ci w odpowiednim rozmiarze aplikacji podczas migrowania ich do platformy Azure, przeprowadzać analizę przydatności na platformie Azure, analizę zależności aplikacji i planowanie kosztów. Firma Microsoft nie używa tych danych w odniesieniu do żadnej inspekcji zgodności licencji.

Oto dane wydajności maszyny wirtualnej VMware, które urządzenie zbiera i wysyła do platformy Azure.

**Klasa licznika wydajności** | **Counter** | **Wpływ oceny**
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

## <a name="collected-metadata-hyper-v"></a>Zebrane metadane — funkcja Hyper-V

Poniżej znajduje się pełna lista metadanych maszyn wirtualnych funkcji Hyper-V, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Klasa WMI** | **Właściwość klasy usługi WMI**
--- | --- | ---
**Szczegóły maszyny** | 
Numer seryjny systemu BIOS _ Msvm_BIOSElement | BIOSSerialNumber
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
Typ wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | Type
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
3. **Wyślij dane**: Urządzenie wysyła zebrane dane w celu Azure Migrate oceny serwera i migracji serwera Azure Migrate przez Port SSL 443.
    - W przypadku danych dotyczących wydajności urządzenie zbiera dane użycia w czasie rzeczywistym.
        - Dane dotyczące wydajności są zbierane co 20 sekund dla programu VMware i co 30 sekund dla funkcji Hyper-V dla każdej metryki wydajności.
        - Zebrane dane są zestawiane w celu utworzenia jednego punktu danych przez dziesięć minut.
        - Wartość szczytowego wykorzystania jest wybierana ze wszystkich 20/30 drugiego punktu danych i wysyłanych do platformy Azure w celu obliczenia oceny.
        - W oparciu o wartość percentylu określoną we właściwościach oceny (pięćdziesiąt/90/używany 95./99), dziesięć minutowe punkty są sortowane w kolejności rosnącej, a odpowiednia wartość percentylu jest używana do obliczania oceny
    - W przypadku migracji serwera urządzenie uruchamia zbieranie danych maszyny wirtualnej i replikuje je na platformie Azure.
4. **Ocenianie i migrowanie**: Teraz można tworzyć oceny z metadanych zebranych przez urządzenie przy użyciu funkcji oceny serwera Azure Migrate. Ponadto można rozpocząć Migrowanie maszyn wirtualnych VMware przy użyciu migracji serwera Azure Migrate, aby organizować replikację maszyn wirtualnych bez agentów.


![Architektura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Uaktualnienia urządzeń

Urządzenie zostanie uaktualnione w miarę aktualizowania Azure Migrate agentów działających na urządzeniu.

- Dzieje się tak automatycznie, ponieważ automatyczna aktualizacja jest domyślnie włączona na urządzeniu.
- Można zmienić to ustawienie domyślne, aby ręcznie zaktualizować agentów.
- Aby wyłączyć automatyczne aktualizowanie, przejdź do edytora rejestru > HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance i Ustaw klucz rejestru "AutoUpdate" na 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Ustaw aktualizacje agenta na ręczne

W przypadku aktualizacji ręcznych upewnij się, że wszystkie agenci na urządzeniu zostały zaktualizowane w tym samym czasie, przy użyciu przycisku **Aktualizuj** dla każdego nieaktualnego agenta na urządzeniu. Możesz w dowolnym momencie zmienić ustawienie aktualizacji na aktualizacje automatyczne.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak](tutorial-assess-vmware.md#set-up-the-appliance-vm) skonfigurować urządzenie dla programu VMware.
[Dowiedz się, jak](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) skonfigurować urządzenie dla funkcji Hyper-V.

