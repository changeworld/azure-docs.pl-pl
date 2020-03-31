---
title: SAP Business One na maszynach wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: SAP Business One na platformie Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18409f93ab50f7d031ec78a55b9eaf8ad1b85a49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101411"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One na maszynach wirtualnych platformy Azure
Ten dokument zawiera wskazówki dotyczące wdrażania sap Business One na maszynach wirtualnych platformy Azure. Dokumentacja nie zastępuje dokumentacji instalacyjnej firmy Business one dla sap. Dokumentacja powinna obejmować podstawowe wskazówki dotyczące planowania i wdrażania infrastruktury platformy Azure do uruchamiania aplikacji Business One.

Business One obsługuje dwie różne bazy danych:
- SQL Server — zobacz [SAP Note #928839 — planowanie wersji dla programu Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA — dla dokładnej macierzy obsługi SAP Business One dla SAP HANA, wyewidencjonuj [macierz dostępności produktów SAP](https://support.sap.com/pam)

W odniesieniu do programu SQL Server zastosowanie mają podstawowe zagadnienia dotyczące wdrażania zgodnie z dokumentami [we wdrożeniu dbms maszyn wirtualnych platformy Azure dla systemu SAP NetWeaver.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) dla SAP HANA, zagadnienia są wymienione w tym dokumencie.

## <a name="prerequisites"></a>Wymagania wstępne
Aby skorzystać z tego przewodnika, potrzebujesz podstawowej wiedzy na temat następujących składników platformy Azure:

- [Maszyny wirtualne platformy Azure w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Maszyny wirtualne platformy Azure w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Zarządzanie sieciami i sieciami wirtualnymi platformy Azure za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Sieci sieci platformy Azure i sieci wirtualne z interfejsem wiersza polecenia](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Nawet jeśli jesteś zainteresowany tylko w firmie Jeden, dokument [Azure Virtual Machines planowania i implementacji dla SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) może być dobrym źródłem informacji.

Założenie jest takie, że jako wystąpienie wdrażania SAP Business One są:

- Zaznajomiony z instalacją SAP HANA na danej infrastrukturze, takiej jak maszyna wirtualna
- Znane instalowanie aplikacji SAP Business One na infrastrukturze takiej jak maszyny wirtualne platformy Azure
- Zaznajomiony z obsługą SAP Business One i wybranym systemem DBMS
- Znajomość infrastruktury na platformie Azure

Wszystkie te obszary nie zostaną uwzględnione w niniejszym dokumencie.

Oprócz dokumentacji platformy Azure należy pamiętać o głównych uwagach SAP, które odnoszą się do Business One lub które są głównymi uwagami firmy SAP dla firm One:

- [528296 - Ogólna uwaga przeglądowa dla wersji SAP Business One i powiązanych produktów](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - Uwaga dotycząca aktualizacji wersji dla SAP Business One 9.2, wersja dla SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - Uwaga centralna dla SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - Informacja o aktualizacjach wersji dla SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - Uwaga zbiorowa dotycząca SAP Business One 9.3 Problemy ogólne](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - Zbiorcza nota konsultingowa dla tematów związanych z SAP HANA SAP Business One, wersja dla SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Architektura Business One
Business One to aplikacja, która ma dwie warstwy:

- Warstwa klienta z klientem "tłuszczu"
- Warstwa bazy danych zawierająca schemat bazy danych dla dzierżawcy

Lepszy przegląd składników, które są uruchomione w części klienta i które części są uruchomione w części serwera, jest udokumentowany w [przewodniku administratora SAP Business One](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Ponieważ istnieje duże opóźnienie krytyczne interakcje między warstwą klienta i warstwy DBMS, obie warstwy muszą znajdować się na platformie Azure podczas wdrażania na platformie Azure. zwykle użytkownicy następnie RDS do jednego lub wielu maszyn wirtualnych z uruchomioną usługą RDS dla składników klienta Business One.

### <a name="sizing-vms-for-sap-business-one"></a>Rozmiary maszyn wirtualnych dla SAP Business One

Jeśli chodzi o rozmiary maszyn wirtualnych klienta, wymagania dotyczące zasobów są dokumentowane przez firmę SAP w dokumencie [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). W przypadku platformy Azure należy skupić się i obliczyć zgodnie z wymaganiami określonymi w rozdziale 2.4 dokumentu.

Jako maszyny wirtualne platformy Azure do obsługi składników klienta Business One i hosta dbms, tylko maszyny wirtualne, które są obsługiwane SAP NetWeaver są dozwolone. Aby znaleźć listę maszyn wirtualnych obsługiwanych przez usługę Azure firmy SAP NetWeaver, przeczytaj #1928533 [notatki SAP](https://launchpad.support.sap.com/#/notes/1928533).

Uruchamianie SAP HANA jako zaplecza DBMS dla Business One, tylko maszyny wirtualne, które są wymienione dla firm na HANA na [liście platform certyfikatów HANA IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) są obsługiwane dla hana. Składniki klienta Business One nie są dotknięte tym silniejszym ograniczeniem dla sap HANA jako system DBMS.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Wersje systemu operacyjnego używane dla SAP Business One

Zasadniczo zawsze najlepiej jest używać najnowszych wersji systemu operacyjnego. Szczególnie w przestrzeni systemu Linux wprowadzono nową funkcjonalność platformy Azure z różnymi nowszymi wersjami pomocniczymi Suse i Red Hat. Po stronie systemu Windows zaleca się korzystanie z systemu Windows Server 2016.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Wdrażanie infrastruktury na platformie Azure dla sap business one
W następnych kilku rozdziałach elementy infrastruktury, które mają znaczenie dla wdrażania SAP.

### <a name="azure-network-infrastructure"></a>Infrastruktura sieci platformy Azure
Infrastruktura sieciowa, którą należy wdrożyć na platformie Azure, zależy od tego, czy wdrożysz jeden system Business One dla siebie. Albo czy jesteś hosterem, który obsługuje dziesiątki systemów Business One dla klientów. Mogą również wystąpić niewielkie zmiany w projekcie, czy sposób łączenia się z platformą Azure. Przechodzenie przez różne możliwości, jeden projekt, w którym masz łączność SIECI VPN na platformie Azure i gdzie rozszerzyć usługę Active Directory za pośrednictwem [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) lub [usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) na platformę Azure.

![Prosta konfiguracja sieci z programem Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

Przedstawiona uproszczona konfiguracja wprowadza kilka wystąpień zabezpieczeń, które umożliwiają kontrolowanie i ograniczanie routingu. Zaczyna się od 

- Router/zapora po stronie klienta lokalnego.
- Następnym wystąpieniem jest [grupa zabezpieczeń sieciowej platformy Azure,](https://docs.microsoft.com/azure/virtual-network/security-overview) której można użyć do wprowadzenia reguł routingu i zabezpieczeń dla sieci wirtualnej platformy Azure, w której uruchomisz jedną konfigurację SAP Business.
- Aby uniknąć, że użytkownicy klienta Business One można również zobaczyć serwer, który uruchamia serwer Business One, który uruchamia bazę danych, należy oddzielić maszynę wirtualną hosting business jeden klient i biznes jeden serwer w dwóch różnych podsieci w sieci wirtualnej.
- Należy użyć usługi Azure NSG przypisane do dwóch różnych podsieci ponownie w celu ograniczenia dostępu do serwera Business one.

Bardziej zaawansowana wersja konfiguracji sieci platformy Azure jest oparta na sprawdzonych najlepszych praktykach platformy Azure [dotyczących architektury koncentratora i szprychy.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Wzorzec architektury koncentratora i szprychy zmieni pierwszą uproszczoną konfigurację na taką:


![Konfiguracja koncentratora i szprychy za pomocą programu Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

W przypadkach, gdy użytkownicy łączą się za pośrednictwem Internetu bez żadnej prywatnej łączności z platformą Azure, projekt sieci na platformie Azure powinien być zgodny z zasadami udokumentowanymi w architekturze referencyjnej platformy Azure dla [strefy DMZ między platformą Azure a Internetem.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)

### <a name="business-one-database-server"></a>Serwer bazy danych Business One
Dla typu bazy danych dostępne są program SQL Server i SAP HANA. Niezależnie od systemu dbms, należy przeczytać zagadnienia dotyczące dokumentu [dla wdrożenia DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) aby uzyskać ogólne zrozumienie wdrożeń DBMS w systemie azure VMs i powiązanych tematów sieci i magazynu.

Choć już podkreślono w konkretnych i ogólnych dokumentach bazy danych, powinieneś zapoznać się z:

- [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) i [zarządzanie dostępnością maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [Umowa SLA dla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Dokumenty te powinny pomóc w podjęciu decyzji o wyborze typów magazynu i konfiguracji o wysokiej dostępności.

Zasadniczo należy:

- Używaj dysków SSD premium na standardowych dyskach twardych. Aby dowiedzieć się więcej o dostępnych typach dysków, zobacz nasz artykuł [Wybieranie typu dysku](../../windows/disks-types.md)
- Używanie dysków zarządzanych platformy Azure na dyskach niezarządzanych
- Upewnij się, że masz wystarczającą przepustowość we/wy i we/wy skonfigurowane w konfiguracji dysku
- Połącz /hana/data i /hana/log volume, aby uzyskać ekonomiczną konfigurację pamięci masowej


#### <a name="sql-server-as-dbms"></a>SQL Server jako system dbms
Aby wdrożyć program SQL Server jako usługę DBMS dla firmy Business One, przejdź do dokumentu [SQL Server Azure Virtual Machines DBMS wdrożenia dla SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

Przybliżone szacowanie rozmiaru po stronie DBMS dla programu SQL Server to:

| Liczba użytkowników | Procesory wirtualne | Memory (Pamięć) | Przykładowe typy maszyn wirtualnych |
| --- | --- | --- | --- |
| do 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| do 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| do 80 | 16 | 64 GB | D16s_v3 E16s_v3 E16s_v3 |
| do 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

Rozmiar wymienionych powyżej powinien dać wyobrażenie, od czego zacząć. Może się okazać, że potrzebujesz mniej lub więcej zasobów, w którym to przypadku adaptacja na platformie Azure jest łatwa. Zmiana między typami maszyn wirtualnych jest możliwe przy ponownym uruchomieniu maszyny Wirtualnej.

#### <a name="sap-hana-as-dbms"></a>SAP HANA jako dbms
Za pomocą SAP HANA jako DBMS następujące sekcje należy postępować zgodnie z uwagami dokumentu [SAP HANA na platformie Azure operations guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

Aby uzyskać wysoką dostępność i odzyskiwanie po awarii konfiguracji wokół SAP HANA jako bazy danych dla firmy Business One na platformie Azure, należy przeczytać dokumentację [SAP HANA wysokiej dostępności dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) i dokumentacji wskazanej w tym dokumencie.

W przypadku strategii tworzenia kopii zapasowych i przywracania sap HANA należy zapoznać się z [przewodnikiem po dokumencie Kopia zapasowa dla systemu SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) oraz dokumentacją wskazaną w tym dokumencie.

 
### <a name="business-one-client-server"></a>Serwer klienta Business One
Dla tych składników zagadnienia dotyczące magazynu nie są głównym problemem. niemniej jednak, chcesz mieć niezawodną platformę. W związku z tym należy użyć usługi Azure Premium Storage dla tej maszyny Wirtualnej, nawet dla podstawowego dysku wirtualnego. Zmiana rozmiaru maszyny Wirtualnej z danymi podanymi w [przewodniku wymagań sprzętowych SAP Business One](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). W przypadku platformy Azure należy skupić się i obliczyć zgodnie z wymaganiami określonymi w rozdziale 2.4 dokumentu. Podczas obliczania wymagań należy porównać je z następującymi dokumentami, aby znaleźć idealną maszynę wirtualną dla Ciebie:

- [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [#1928533 uwaga SAP](https://launchpad.support.sap.com/#/notes/1928533)

Porównaj liczbę procesorów i pamięci potrzebnych do tego, co jest udokumentowane przez firmę Microsoft. Należy również pamiętać o przepływności sieci przy wyborze maszyn wirtualnych.








