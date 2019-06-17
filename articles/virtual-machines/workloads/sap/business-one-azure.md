---
title: SAP Business jeden na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: SAP Business jednej na Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 101710b5a57faa37be77ff4b059fa0d494f4e617
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60835655"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One na maszynach wirtualnych platformy Azure
Ten dokument zawiera wskazówki dotyczące wdrażania SAP Business One na maszynach wirtualnych platformy Azure. Dokumentacja nie jest zamiennikiem dokumentacji instalacji działalności jeden dla rozwiązania SAP. Dokumentacja obejmuje podstawowe wskazówki dotyczące planowania i wdrażania infrastruktury platformy Azure do uruchamiania aplikacji Business One na.

Firma co obsługuje dwóch różnych bazach danych:
- SQL Server — zobacz [928839 # Uwaga SAP - planowania wydania programu Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA — dokładnie SAP Business One macierzy pomocy technicznej dla oprogramowania SAP HANA wyewidencjonowania [macierzy dostępności produktów SAP](https://support.sap.com/pam)

Dotyczące programu SQL Server, zagadnienia związane z wdrażaniem podstawowe, zgodnie z opisem w [wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) ma zastosowanie. dla oprogramowania SAP HANA zagadnienia są wymienione w tym dokumencie.

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego przewodnika, potrzebujesz podstawową wiedzę na temat następujących składników platformy Azure:

- [Maszyny wirtualne platformy Azure na Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Maszyny wirtualne platformy Azure w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure, sieci i wirtualnych sieci zarządzania przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Sieci platformy Azure i sieci wirtualnych za pomocą interfejsu wiersza polecenia](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Zarządzanie dyskami platformy Azure przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Nawet jeśli interesuje Cię w firmie, jeden tylko dokumentu [planowanie maszyn wirtualnych platformy Azure i wdrażanie środowiska SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) może być dobrym źródłem informacji.

Zakłada się, jako wystąpienie wdrażanie SAP Business One się:

- Znasz instalowanie platformy SAP HANA na danej infrastruktury, takich jak maszyny Wirtualnej
- Znany instalowania aplikacji SAP Business One na infrastruktury, takich jak maszyny wirtualne platformy Azure
- Masz działające oprogramowanie SAP Business One i wybrany system DBMS
- Zapoznać się z wdrożeniem infrastruktury na platformie Azure

Te obszary będzie nieuwzględnione w tym dokumencie.

Oprócz dokumentacji platformy Azure warto wiedzieć głównego SAP Notes, które odnoszą się do jednej firmy lub które są centralnej uwagi SAP dla firm, jeden:

- [528296 — ogólne omówienie Uwaga dotycząca wersji jednej firmy SAP i powiązanych produktów](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - aktualizacji informacje o wersji dla SAP Business jeden 9.2 wersji platformy SAP Hana](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 — Uwaga środkowa for SAP Business jeden 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 — wydanie aktualizacji Uwaga SAP Business jeden 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - Uwaga zbiorowe for SAP Business ogólnych problemów z jednego 9.3](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - zbiorowe doradcze uwagi SAP HANA-Related tematy programu SAP Business One, SAP HANA w wersji](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Jeden architektury biznesowej
Firma jest aplikacja, która ma dwie warstwy:

- Warstwa klienta, za pomocą klienta "systemu plików fat"
- Warstwa bazy danych, który zawiera schemat bazy danych dla dzierżawy

Lepsze — omówienie, które składniki są uruchomione w ramach klienta, a które części są uruchomione w ramach serwera jest udokumentowany w [SAP Business jednej dla administratorów](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Ponieważ występuje duże opóźnienie krytyczne interakcji między warstwami klienta i DBMS, obu warstwach muszą znajdować się na platformie Azure, w przypadku wdrażania na platformie Azure. jest to zwykle, czy użytkownicy, a następnie usług pulpitu zdalnego do jednego lub wielu maszyn wirtualnych z systemem usług pulpitu zdalnego usług Business One składników klienta.

### <a name="sizing-vms-for-sap-business-one"></a>Rozmiary maszyn wirtualnych dla SAP Business jeden

Dotyczące rozmiaru klienta maszyn wirtualnych, wymagania dotyczące zasobów są opisane przez firmę SAP w dokumencie [SAP Business jednego sprzętu Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Dla platformy Azure należy skoncentrować się i obliczeń z wymagania określone w rozdziale 2.4 dokumentu.

Jako maszyn wirtualnych platformy Azure do hostowania Business One składniki klienta i hosta systemu DBMS dozwolone są tylko maszyny wirtualne, które są oprogramowanie SAP NetWeaver obsługiwane. Aby znaleźć listę oprogramowania SAP NetWeaver obsługiwanych maszynach wirtualnych platformy Azure, przeczytaj [1928533 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/1928533).

Uruchamiania oprogramowania SAP HANA jako DBMS wewnętrznej bazy danych dla jednej firmy tylko maszyny wirtualne, które są wymienione dla firm na platformie HANA w [listy platform IaaS certifeid HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) są obsługiwane w przypadku platformy HANA. Składniki klienta programu Business One nie dotyczy to ograniczenie silniejsze platformy SAP Hana jako DBMS system.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Wersje systemu operacyjnego do użycia rozwiązanie SAP Business One

W zasadzie jest zawsze używać najnowszej wersji systemu operacyjnego. Szczególnie w tym obszarze nowe funkcje platformy Azure została wprowadzona w systemach różnych nowszą pomocnicza wydań Suse i Red Hat. Na stronie Windows przy użyciu systemu Windows Server 2016 zdecydowanie zaleca się.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Wdrażanie infrastruktury na platformie Azure for SAP Business One
W następnym rozdziale kilka infrastruktura kawałki służącego do wdrożenia SAP.

### <a name="azure-network-infrastructure"></a>Infrastruktura sieci platformy Azure
Infrastruktury sieci, które należy wdrożyć na platformie Azure, zależy od tego, czy wdrożyć jeden system Business One dla siebie. Lub czy dostawcy usług hostingowych, który obsługuje wielu systemów biznesowych jeden dla klientów. Istnieje również może być drobne zmiany w projekcie na czy sposobie nawiązywania połączenia platformy Azure. Przechodzenia przez różne możliwości, jeden z projektem, w którym masz połączenie z siecią VPN na platformie Azure i gdzie Rozszerzanie usługi Active Directory za pośrednictwem [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) na platformie Azure.

![Prostą konfigurację sieci z Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

Uproszczona konfiguracja prezentowane wprowadza kilka wystąpień zabezpieczeń, które umożliwiają kontrolę i limit routingu. Zaczyna się od 

- Router/Zapora po stronie klienta w środowisku lokalnym.
- Kolejne wystąpienie ma miejsce [sieciowej grupy zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) użyć wprowadzenie reguł routingu i zabezpieczeń dla sieci wirtualnej platformy Azure, uruchamianego jedna konfiguracja usługi SAP Business w.
- Aby uniknąć przez użytkowników biznesowych jednego klienta można zobaczyć także serwera, na którym działa serwer Business One działa baza danych, należy oddzielić maszyn wirtualnych hostujących firm jednego klienta, jak i biznesowych jeden serwer, który w dwóch różnych podsieci w sieci wirtualnej.
- Należy ponownie użyć sieciowej grupy zabezpieczeń platformy Azure przypisanych do dwóch różnych podsieci w celu ograniczenia dostępu do jednego serwera firmy.

Nieco bardziej zaawansowanej konfiguracji sieci platformy Azure opiera się na platformie Azure [opisano najlepsze rozwiązania architektury gwiazdy](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Wzorzec architektury gwiazdy zmieniłby pierwszy uproszczona konfiguracja do jednego następująco:


![Gwiazda konfiguracji za pomocą Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

W przypadkach, w których użytkownicy nawiązują połączenie za pośrednictwem sieci internet bez prywatnej łączności do platformy Azure, projekt sieci na platformie Azure, powinno być wyrównane z zasadami udokumentowane w architektura referencyjna platformy Azure dla [strefy DMZ między platformą Azure a Internetem](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Serwer jedna baza danych biznesowych
Typ bazy danych programu SQL Server i programu SAP HANA są dostępne. Niezależnie od systemu DBMS, powinien przeczytać dokument [zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) Aby uzyskać ogólne omówienie wdrożeń systemu DBMS na maszynach wirtualnych platformy azure i powiązane sieci i magazynu tematy.

Chociaż już zostać w dokumentach ogólnych i specyficznych bazy danych, należy upewnić się, samodzielnie zapoznać się z:

- [Zarządzanie dostępnością maszyn wirtualnych Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) i [Zarządzanie dostępnością maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [Umowa SLA dla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Te dokumenty powinny ułatwić podjęcie decyzji o wyborze typów magazynów i konfiguracja wysokiej dostępności.

W zasadzie następujące czynności:

- Użyj dysków SSD w warstwie Premium za pośrednictwem standardowych dysków twardych. Aby dowiedzieć się więcej na temat typów dostępnego miejsca na dysku, zobacz nasze artykuł [wybierz typ dysku](../../windows/disks-types.md)
- Użyj usługi Azure Managed disks za pośrednictwem dysków niezarządzanych
- Upewnij się, że masz wystarczającą przepustowość operacje We/Wy i operacje We/Wy skonfigurowane za pomocą konfiguracji dysku
- Łączenie/hana/dane i /hana/log woluminu, aby mogła mieć konfigurację magazynu wydajne kosztów


#### <a name="sql-server-as-dbms"></a>SQL Server jako system DBMS
Do wdrożenia programu SQL Server jako system DBMS dla jednej firmy, przejść wzdłuż dokumentu [wdrażania systemu DBMS na maszyny wirtualne SQL Server Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

Oszacowania nierównej rozmiaru strony bazami danych programu SQL Server są:

| Liczba użytkowników | procesorów wirtualnych | Memory (Pamięć) | Przykładowe typy maszyn wirtualnych |
| --- | --- | --- | --- |
| do 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| maksymalnie 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| do 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| maksymalnie 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

Ustalanie rozmiaru wymienionych powyżej powinien zapewnić pomysł, gdzie zacząć. Być może potrzebujesz mniejszej lub większej liczby zasobów, w którym to przypadku dostosowania na platformie azure jest łatwe. Zmiana między typami maszyny Wirtualnej jest możliwe dzięki właśnie ponownego uruchomienia maszyny wirtualnej.

#### <a name="sap-hana-as-dbms"></a>SAP HANA jako systemu DBMS
Zagadnienia dotyczące dokumentu przy użyciu oprogramowania SAP HANA DBMS następujące sekcje należy powinien być zgodny [platformy SAP HANA w podręczniku obsługi platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

O wysokiej dostępności i konfiguracjami odzyskiwania po awarii całego oprogramowania SAP HANA jako bazy danych dla Business One na platformie Azure, należy przeczytać dokumentację [platformy SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) i dokumentacji wskazywał z tego dokumentu.

Dla oprogramowania SAP HANA kopia zapasowa i przywracanie strategie, powinni przeczytać dokument [przewodniku kopia zapasowa dla oprogramowania SAP HANA na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) i dokumentacji wskazywał z tego dokumentu.

 
### <a name="business-one-client-server"></a>Business jeden klient serwer
Składniki te zagadnienia dotyczące magazynu nie są podstawową kwestią. Niemniej jednak chcesz mieć niezawodnej platformie. Dlatego należy używać usługi Azure Premium Storage dla tej maszyny Wirtualnej, nawet w przypadku podstawowy dysk VHD. Ustalanie rozmiaru maszyny Wirtualnej, z danymi w [SAP Business jednego sprzętu Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Dla platformy Azure należy skoncentrować się i obliczeń z wymagania określone w rozdziale 2.4 dokumentu. Podczas obliczania wymagania należy porównać w poniższych dokumentach można znaleźć idealne maszyny Wirtualnej:

- [Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [Uwaga SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Porównaj liczbę procesorów i pamięci potrzebnej do dokumentacji, przez firmę Microsoft. Również pamiętać przepustowość sieci podczas wybierania maszyny wirtualne.








