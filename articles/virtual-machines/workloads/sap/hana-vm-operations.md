---
title: SAP HANA konfiguracje i operacje związane z infrastrukturą na platformie Azure | Microsoft Docs
description: Przewodnik obsługi dla systemów SAP HANA wdrażanych na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1547f0e600031f558dcc0157df2a35fdf3f9db2c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224686"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure
Ten dokument zawiera wskazówki dotyczące konfigurowania infrastruktury platformy Azure i systemów SAP HANA operacyjnych wdrożonych na natywnych maszynach wirtualnych platformy Azure. Dokument zawiera również informacje o konfiguracji SAP HANA skalowania w poziomie dla jednostki SKU maszyny wirtualnej M128s. Ten dokument nie jest przeznaczony do zastępowania standardowej dokumentacji SAP, która obejmuje następującą zawartość:

- [Przewodnik administrowania SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Przewodniki instalacji SAP](https://service.sap.com/instguides)
- [Uwagi dotyczące oprogramowania SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Wymagania wstępne
Aby skorzystać z tego przewodnika, potrzebna jest podstawowa znajomość następujących składników platformy Azure:

- [Maszyny wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Sieć wirtualna i sieci wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Aby dowiedzieć się więcej na temat oprogramowania SAP NetWeaver i innych składników SAP na platformie Azure, zapoznaj się z sekcją [SAP w systemie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) w [dokumentacji platformy Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Podstawowe zagadnienia dotyczące konfiguracji
W poniższych sekcjach opisano podstawowe zagadnienia dotyczące wdrażania systemów SAP HANA na maszynach wirtualnych platformy Azure.

### <a name="connect-into-azure-virtual-machines"></a>Łączenie się z maszynami wirtualnymi platformy Azure
Zgodnie z opisem w [przewodniku planowania usługi Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)istnieją dwie podstawowe metody łączenia się z maszynami wirtualnymi platformy Azure:

- Połącz się za pomocą Internetu i publicznych punktów końcowych na maszynie wirtualnej skoku lub na maszynie wirtualnej, na której działa SAP HANA.
- Nawiązywanie połączenia za pośrednictwem [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) lub usługi Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Łączność między lokacjami za pośrednictwem sieci VPN lub ExpressRoute jest niezbędna w scenariuszach produkcyjnych. Ten typ połączenia jest również wymagany w scenariuszach nieprodukcyjnych, które są źródłem do scenariuszy produkcyjnych, w których jest używane oprogramowanie SAP. Na poniższej ilustracji przedstawiono przykład łączności między lokacjami:

![Łączność między lokacjami](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Wybierz typy maszyn wirtualnych platformy Azure
Typy maszyn wirtualnych platformy Azure, które mogą być używane na potrzeby scenariuszy produkcyjnych, są wymienione w [dokumentacji SAP dla IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). W przypadku scenariuszy innych niż produkcyjne dostępna jest szeroka gama natywnych typów maszyn wirtualnych platformy Azure.

>[!NOTE]
> W przypadku scenariuszy innych niż produkcyjne należy użyć typów maszyn wirtualnych, które znajdują się na liście w [notatce SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Aby sprawdzić użycie maszyn wirtualnych platformy Azure na potrzeby scenariuszy produkcyjnych, zapoznaj się z tematem SAP HANA certyfikowane maszyny wirtualne na [liście opublikowanych certyfikowanych platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)w systemie SAP.

Wdróż maszyny wirtualne na platformie Azure przy użyciu programu:

- Azure Portal.
- Polecenia cmdlet Azure PowerShell.
- Interfejs wiersza polecenia platformy Azure.

Możesz również wdrożyć kompletną SAP HANA platformę w usługach maszyn wirtualnych platformy Azure za pomocą [platformy SAP Cloud Platform](https://cal.sap.com/). Proces instalacji został opisany w artykule [wdrażanie oprogramowania SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) lub z usługą Automation wydaną [tutaj](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

>[!IMPORTANT]
> Aby można było korzystać z maszyn wirtualnych M208xx_v2, należy zachować ostrożność wybierania obrazu systemu Linux z galerii obrazów maszyn wirtualnych platformy Azure. Aby zapoznać się ze szczegółami, zapoznaj się z artykułem [rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series). 
> 


### <a name="storage-configuration-for-sap-hana"></a>Konfiguracja magazynu dla SAP HANA
Aby uzyskać informacje o konfiguracjach magazynu i typach magazynów, które mają być używane z SAP HANA na platformie Azure, Przeczytaj dokument [SAP HANA konfiguracje magazynu maszyn wirtualnych platformy Azure](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Konfigurowanie sieci wirtualnych platformy Azure
W przypadku połączenia między lokacjami z platformą Azure za pośrednictwem sieci VPN lub ExpressRoute należy mieć co najmniej jedną sieć wirtualną platformy Azure, która jest połączona za pośrednictwem bramy wirtualnej do obwodu sieci VPN lub usługi ExpressRoute. W prostych wdrożeniach można wdrożyć bramę wirtualną w podsieci sieci wirtualnej platformy Azure, która obsługuje również wystąpienia SAP HANA. Aby zainstalować SAP HANA, należy utworzyć dwie dodatkowe podsieci w ramach sieci wirtualnej platformy Azure. Jedna podsieć hostuje maszyny wirtualne do uruchamiania wystąpień SAP HANA. Inna podsieć uruchamia maszyny wirtualne serwera przesiadkowego lub Management do hostowania SAP HANA Studio, innego oprogramowania do zarządzania lub oprogramowania aplikacji.

> [!IMPORTANT]
> Poza funkcjonalnością, ale bardziej istotny ze względu na wydajność, nie jest obsługiwane Konfigurowanie [urządzeń wirtualnych sieci platformy Azure](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP a warstwą DBMS systemu SAP NetWeaver, Hybris lub S/4HANA. Komunikacja między warstwą aplikacji SAP a warstwą DBMS musi być jedną bezpośrednią. Ograniczenie nie obejmuje [reguł usługi Azure ASG i sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview) , o ile te reguły ASG i sieciowej grupy zabezpieczeń umożliwiają bezpośrednią komunikację. Dalsze scenariusze, w których urządzeń WUS nie są obsługiwane, znajdują się w ścieżkach komunikacyjnych między maszynami wirtualnymi platformy Azure, które reprezentują węzły klastra systemu Linux Pacemaker i urządzenia SBD, zgodnie z opisem w temacie [wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) Lub w ścieżkach komunikacyjnych między maszynami wirtualnymi platformy Azure i systemem Windows Server SOFS skonfigurowanym zgodnie z opisem w artykule [klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Urządzeń WUS w ścieżkach komunikacyjnych mogą łatwo podwójnie opóźniać opóźnienia sieci między dwoma partnerami komunikacyjnymi, co pozwala ograniczyć przepływność w ścieżkach krytycznych między warstwą aplikacji SAP i warstwą DBMS. W niektórych scenariuszach obserwowanych przez klientów urządzeń WUS może spowodować awarię klastrów Pacemaker systemu Linux w przypadkach, gdy komunikacja między węzłami klastra Pacemaker systemu Linux musi komunikować się z urządzeniem SBD za pośrednictwem urządzenie WUS.  
> 

> [!IMPORTANT]
> Innym **nieobsługiwanym projektem jest podział** warstwy aplikacji SAP i warstwy DBMS na różne sieci wirtualne platformy Azure, [które nie są](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) połączone między sobą. Zaleca się rozdzielenie warstwy aplikacji SAP i systemu DBMS przy użyciu podsieci w sieci wirtualnej platformy Azure, a nie za pomocą różnych sieci wirtualnych platformy Azure. Jeśli użytkownik zdecyduje się nie przestrzegać zalecenia, a następnie dzieli dwie warstwy na inną sieć wirtualną, muszą być połączone za pomocą [komunikacji równorzędnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Należy pamiętać, że ruch sieciowy między dwiema [równorzędnymi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sieciami wirtualnymi platformy Azure podlega kosztom transferu. Dzięki dużej ilości danych w wielu terabajtach wymienianych między warstwą aplikacji SAP i warstwą DBMS istotne koszty mogą być gromadzone, jeśli warstwa aplikacji SAP i warstwa DBMS są segregowane między dwiema równorzędnymi sieciami wirtualnymi platformy Azure. 

Gdy instalujesz maszyny wirtualne do uruchamiania SAP HANA, maszyny wirtualne muszą:

- Zainstalowane dwie wirtualne karty sieciowe: jedna karta sieciowa do łączenia się z podsiecią zarządzania i jedna karta sieciowa do łączenia się z siecią lokalną lub innymi sieciami do wystąpienia SAP HANA na maszynie wirtualnej platformy Azure.
- Statyczne prywatne adresy IP wdrożone dla obu wirtualnych kart sieciowych.

> [!NOTE]
> Statyczne adresy IP należy przypisać do poszczególnych vNICs na platformie Azure. Nie należy przypisywać statycznych adresów IP w systemie operacyjnym gościa do wirtualnej karty sieciowej. Niektóre usługi platformy Azure, takie jak Azure Backup, korzystają z faktu, że co najmniej podstawowy wirtualnej karty sieciowej jest ustawiony na wartość DHCP, a nie na statyczne adresy IP. Zobacz również dokument [Rozwiązywanie problemów z kopiami zapasowymi maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Jeśli musisz przypisać wiele statycznych adresów IP do maszyny wirtualnej, musisz przypisać wiele vNICs do maszyny wirtualnej.
>
>

Jednak w przypadku wdrożeń, które są trwałe, należy utworzyć wirtualną architekturę sieci centrum danych na platformie Azure. Ta architektura zaleca rozdzielenie bramy sieci wirtualnej platformy Azure, która łączy się z siecią lokalną w oddzielną sieć wirtualną platformy Azure. Ta osobna sieć wirtualna powinna obsługiwać cały ruch, który opuszcza lokalne lub internetowe. Takie podejście umożliwia wdrożenie oprogramowania na potrzeby inspekcji i rejestrowania ruchu, który przechodzi do wirtualnego centrum danych na platformie Azure w tej osobnej sieci wirtualnej. Dzięki temu masz jedną sieć wirtualną, która obsługuje wszystkie programy i konfiguracje, które odnoszą się do ruchu przychodzącego i wychodzącego do wdrożenia platformy Azure.

Artykuły [wirtualnego centrum danych platformy Azure: perspektywa sieci](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) i [wirtualne centrum danych platformy Azure oraz płaszczyzny kontroli przedsiębiorstwa](https://docs.microsoft.com/azure/architecture/vdc/) zawierają więcej informacji na temat podejścia wirtualnego centrum danych i powiązanego projektu sieci wirtualnej platformy Azure.


>[!NOTE]
>Ruch przesyłany między centralną siecią wirtualną a siecią wirtualną szprych przy użyciu [sieci równorzędnej Azure VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) jest przedmiotem dodatkowych [kosztów](https://azure.microsoft.com/pricing/details/virtual-network/). Na podstawie tych kosztów warto rozważyć nadanie kompromisów między działaniem ścisłego projektu sieci Hub i szprych i uruchomienie wielu [bram usługi Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) , które są połączone z usługą "szprychs" w celu obejścia wirtualnych sieci równorzędnych. Jednak bramy usługi Azure ExpressRoute wymagają również dodatkowych [kosztów](https://azure.microsoft.com/pricing/details/vpn-gateway/) . Mogą również wystąpić dodatkowe koszty dotyczące oprogramowania innej firmy używanego do rejestrowania, inspekcji i monitorowania ruchu sieciowego. Zależnie od kosztów wymiany danych za pośrednictwem komunikacji równorzędnej sieci wirtualnych po jednej stronie i kosztów utworzonych przez dodatkowe bramy usługi Azure ExpressRoute i dodatkowe licencje na oprogramowanie, możesz zdecydować o mikrosegmentacji w jednej sieci wirtualnej przy użyciu podsieci jako jednostki izolacji zamiast sieci wirtualnych.


Aby zapoznać się z omówieniem różnych metod przypisywania adresów IP, zobacz [typy adresów IP i metody alokacji na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

W przypadku maszyn wirtualnych z systemem SAP HANA należy pracować z przypisanymi statycznymi adresami IP. Przyczyną jest to, że niektóre atrybuty konfiguracji adresów IP odwołań HANA.

[Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) są używane do kierowania ruchu kierowanego do wystąpienia SAP HANA lub serwera przesiadkowego. Sieciowych grup zabezpieczeń i ostatecznie [grupy zabezpieczeń aplikacji](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) są skojarzone z podsiecią SAP HANA i podsiecią zarządzania.

Na poniższej ilustracji przedstawiono ogólny schemat wdrażania dla SAP HANA po architekturze sieci wirtualnej typu Hub i szprych:

![Schemat wdrożenia nieprzewidziany dla SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Aby wdrożyć SAP HANA na platformie Azure bez połączenia lokacja-lokacja, nadal chcesz włączyć ochronę wystąpienia SAP HANA z publicznej sieci Internet i ukryć je za serwerem proxy przesyłania dalej. W tym scenariuszu podstawowym wdrożenie wykorzystuje wbudowane usługi DNS platformy Azure do rozpoznawania nazw hostów. W bardziej złożonych wdrożeniach, w których są używane publiczne adresy IP, wbudowane usługi DNS platformy Azure są szczególnie ważne. Korzystając z usług Azure sieciowych grup zabezpieczeń i [Azure urządzeń WUS](https://azure.microsoft.com/solutions/network-appliances/) , można kontrolować i monitorować Routing z Internetu do architektury sieci wirtualnej platformy Azure na platformie Azure. Na poniższej ilustracji przedstawiono schemat do wdrażania SAP HANA bez połączenia lokacja-lokacja w architekturze sieci wirtualnej typu Hub i szprych:
  
![Surowy schemat wdrażania dla SAP HANA bez połączenia typu lokacja-lokacja](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Inny opis sposobu używania usługi Azure urządzeń WUS do kontrolowania i monitorowania dostępu z Internetu bez architektury sieci wirtualnej Hub i gwiazdy można znaleźć w artykule [Wdrażanie wirtualnych urządzeń sieciowych o wysokiej](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)dostępności.


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurowanie infrastruktury platformy Azure dla SAP HANA skalowanie w poziomie
Firma Microsoft ma jedną jednostkę SKU maszyn wirtualnych serii M z certyfikatem dla SAP HANA skalowalnej w poziomie konfiguracji. Typ maszyny wirtualnej M128s został certyfikowany w celu skalowania do maksymalnie 16 węzłów. Aby zmienić SAP HANA certyfikaty skalowalne w poziomie na maszynach wirtualnych platformy Azure, zapoznaj się z [listą certyfikowanych platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Minimalne wersje systemu operacyjnego służące do wdrażania konfiguracji skalowalnych w poziomie na maszynach wirtualnych platformy Azure:

- SUSE Linux 12 z dodatkiem SP3
- Red Hat Linux 7,4

Certyfikacji skalowalnego w poziomie z 16 węzłami

- Jeden węzeł jest węzłem głównym
- Węzły procesu roboczego są maksymalnie 15 węzłów

>[!NOTE]
>W przypadku wdrożeń skalowania maszyn wirtualnych na platformie Azure nie ma możliwości korzystania z węzła rezerwy
>

Mimo że platforma Azure ma natywną usługę NFS z [Azure NetApp Files](https://azure.microsoft.com/services/netapp/), usługa NFS, ale obsługiwana dla warstwy aplikacji SAP, nie jest jeszcze certyfikowana do SAP HANA. W efekcie udziały NFS nadal muszą być skonfigurowane z pomocą funkcji innych firm. 


W związku z tym woluminy **/Hana/Data** i **/Hana/log** nie mogą być udostępniane. Te woluminy nie są współużytkowane przez pojedyncze węzły, co uniemożliwia użycie węzła rezerwy SAP HANA w konfiguracji skalowania w poziomie.

W związku z tym podstawowa konstrukcja jednego węzła w konfiguracji skalowania w poziomie ma wyglądać następująco:

![Podstawowe skalowanie w poziomie jednego węzła](media/hana-vm-operations/scale-out-basics.PNG)

Podstawowa konfiguracja węzła maszyny wirtualnej dla SAP HANA skalowanie w poziomie wygląda następująco:

- W przypadku **/Hana/Shared**należy utworzyć udział NFS o wysokiej dostępności. Do tej pory istnieją różne możliwości w celu uzyskania takiego udziału o wysokiej dostępności. Są one udokumentowane w połączeniu z usługą SAP NetWeaver:
    - [Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
    - [System GlusterFS na maszynach wirtualnych platformy Azure z systemem Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
    - [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
    - [Platforma Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
- Wszystkie inne woluminy dysków **nie** są współdzielone między różnymi węzłami i **nie** są oparte na systemie plików NFS. Konfiguracje instalacji i kroki dla instalacji HANA skalowalnych w poziomie z nieudostępnionymi **/Hana/Data** i **/Hana/log** są dostępne w dalszej sekcji tego dokumentu.

>[!NOTE]
>Klaster NFS o wysokiej dostępności, który jest wyświetlany w grafice, jest opisany w temacie [wysoka dostępność dla systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Inne możliwości są udokumentowane na powyższej liście.

Ustalanie rozmiaru woluminów dla węzłów jest takie samo jak w przypadku skalowania w górę, z wyjątkiem **/Hana/Shared**. W przypadku jednostki SKU maszyny wirtualnej M128s sugerowane rozmiary i typy wyglądają następująco:

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | /hana/data | /hana/log | wolumin/root | /usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Sprawdź, czy przepływność magazynu dla różnych sugerowanych woluminów spełnia obciążenie, które chcesz uruchomić. Jeśli obciążenie wymaga wyższych woluminów dla **/Hana/Data** i **/Hana/log**, należy zwiększyć liczbę wirtualnych dysków twardych Premium Storage platformy Azure. Ustalanie wielkości woluminu o większej liczbie dysków VHD nie powoduje zwiększenia przepływności operacji we/wy w ramach limitów typu maszyny wirtualnej platformy Azure. Zastosuj również akcelerator zapisu platformy Azure do dysków, które tworzą wolumin **/Hana/log** .
 
W dokumencie [SAP HANA wymagania dotyczące magazynu TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), formuła nosi nazwę, która definiuje rozmiar woluminu **/Hana/Shared** do skalowania w poziomie jako rozmiar pamięci jednego węzła procesu roboczego na cztery węzły procesu roboczego.

Przy założeniu, że SAP HANAą maszynę wirtualną platformy Azure z certyfikatem M128s skalowalnym w poziomie z około 2 TB pamięci, zalecenia dotyczące oprogramowania SAP mogą być podsumowywane na przykład:

- Jednym węzłem głównym i maksymalnie czterema węzłami roboczymi wolumin **/Hana/Shared** musi mieć rozmiar 2 TB. 
- Jeden węzeł główny i pięć do ośmiu węzłów procesu roboczego, rozmiar **/Hana/Shared** powinien wynosić 4 TB. 
- Jeden węzeł główny i 9 do 12 węzłów procesu roboczego musi być wymagany rozmiar 6 TB dla **/Hana/Shared** . 
- Jednym węzłem głównym i używaniem między 12 i 15 węzłami roboczymi jest wymagane udostępnienie woluminu **/Hana/Shared** o rozmiarze 8 TB.

Innym ważnym projektem, który jest wyświetlany w grafice konfiguracji pojedynczego węzła dla maszyny wirtualnej SAP HANA skalowalnej w poziomie, jest sieć wirtualna lub lepsza konfiguracja podsieci. SAP zdecydowanie zaleca oddzielenie ruchu związanego z klientem/aplikacją od komunikacji między węzłami HANA. Jak pokazano na grafice, cel ten jest osiągany przez posiadanie dwóch różnych vNICs podłączonych do maszyny wirtualnej. Oba vNICs znajdują się w różnych podsieciach, mają dwa różne adresy IP. Następnie można sterować przepływem ruchu z regułami routingu za pomocą sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika.

Szczególnie na platformie Azure nie ma środków i metod wymuszania jakości usług i przydziałów dla określonych vNICs. W związku z tym rozdzielenie komunikacji między klientem i aplikacją w węźle nie powoduje otwarcia żadnych możliwości określania priorytetów jednego strumienia ruchu. Zamiast tego separacja pozostanie miarą zabezpieczeń w celu osłony w komunikacji między węzłami w konfiguracjach skalowalnych w poziomie.  

>[!IMPORTANT]
>System SAP zdecydowanie zaleca oddzielenie ruchu sieciowego między stroną klient/aplikacja i ruch wewnątrz węzła zgodnie z opisem w tym dokumencie. W związku z tym umieszczenie architektury w miejscu, jak pokazano w ostatniej grafice, jest zdecydowanie zalecane.
>

Z punktu widzenia sieci należy określić minimalną wymaganą architekturę sieci:

![Podstawowe skalowanie w poziomie jednego węzła](media/hana-vm-operations/scale-out-networking-overview.PNG)

Limity obsługiwane do tej pory to 15 procesów roboczych dodatkowych do jednego węzła głównego.

W punkcie magazynu widoku architektura magazynu będzie wyglądać następująco:


![Podstawowe skalowanie w poziomie jednego węzła](media/hana-vm-operations/scale-out-storage-overview.PNG)

Wolumin **/Hana/Shared** znajduje się w konfiguracji udziału NFS o wysokiej dostępności. Wszystkie inne dyski są instalowane lokalnie na poszczególnych maszynach wirtualnych. 

### <a name="highly-available-nfs-share"></a>Udział NFS o wysokiej dostępności
Klaster NFS o wysokiej dostępności działa tylko w systemie SUSE Linux. [Wysoka dostępność dokumentu dla systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) zawiera opis sposobu jego konfiguracji. Jeśli klaster NFS nie jest udostępniany innym konfiguracjom platformy HANA poza siecią wirtualną Azure, na której są uruchomione wystąpienia SAP HANA, należy zainstalować go w tej samej sieci wirtualnej. Zainstaluj ją we własnej podsieci i upewnij się, że nie wszystkie dostępne ruchu mogą uzyskać dostęp do podsieci. Zamiast tego chcesz ograniczyć ruch do tej podsieci do adresów IP maszyny wirtualnej, która wykonuje ruch do woluminu **/Hana/Shared** .

W odniesieniu do wirtualnej karty sieciowej maszyny wirtualnej platformy HANA w poziomie, która powinna kierować ruchem **/Hana/Shared** , zalecenia są następujące:

- Ponieważ ruch do **/Hana/Shared** jest umiarkowany, należy go skierować przez wirtualnej karty sieciowej, który jest przypisany do sieci klienta w konfiguracji minimalnej
- Ostatecznie w przypadku ruchu do **/Hana/Shared**należy wdrożyć trzecią podsieć w sieci wirtualnej, wdrażając SAP HANA skalowanie w poziomie i przypisując trzecią wirtualnej karty sieciowej, która jest hostowana w tej podsieci. Użyj trzeciego wirtualnej karty sieciowej i powiązanego adresu IP dla ruchu do udziału NFS. Następnie można zastosować osobne reguły dostępu i routingu.

>[!IMPORTANT]
>Ruch sieciowy między maszynami wirtualnymi SAP HANA w sposób wdrożony w sposób skalowalny w poziomie i w systemie plików NFS o wysokiej dostępności może nie być kierowany przez [urządzenie WUS](https://azure.microsoft.com/solutions/network-appliances/) lub podobne urządzenia wirtualne. Na platformie Azure sieciowych grup zabezpieczeń nie ma takich urządzeń. Sprawdź reguły routingu, aby upewnić się, że urządzeń WUS lub podobne urządzenia wirtualne są rozwiązane podczas uzyskiwania dostępu do udziału NFS o wysokiej dostępności z maszyn wirtualnych z systemem SAP HANA.
> 

Jeśli chcesz udostępnić klaster NFS o wysokiej dostępności między konfiguracjami SAP HANA, Przenieś wszystkie te konfiguracje HANA do tej samej sieci wirtualnej. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalowanie SAP HANA skalowanie w poziomie n Azure
Instalując konfigurację oprogramowania SAP skalowalnego w poziomie, należy wykonać następujące czynności:

- Wdrażanie nowej lub adaptacji istniejącej infrastruktury sieci wirtualnej platformy Azure
- Wdrażanie nowych maszyn wirtualnych przy użyciu woluminów Premium Storage zarządzanych przez platformę Azure
- Wdrażanie nowego lub adaptacji istniejącego klastra NFS o wysokiej dostępności
- Dostosuj Routing sieciowy, aby upewnić się, że na przykład komunikacja między maszynami wirtualnymi nie odbywa się za pośrednictwem [urządzenie WUS](https://azure.microsoft.com/solutions/network-appliances/). Ta sama wartość dotyczy ruchu między maszynami wirtualnymi i klastrem systemu plików NFS o wysokiej dostępności.
- Zainstaluj SAP HANA węzeł główny.
- Dostosowywanie parametrów konfiguracji węzła głównego SAP HANA
- Kontynuuj instalację SAP HANA węzłów procesu roboczego

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalacja SAP HANA w konfiguracji skalowania w poziomie
Po wdrożeniu infrastruktury maszyny wirtualnej platformy Azure i zakończeniu wszystkich innych przygotowań należy zainstalować SAP HANA konfiguracje skalowania w poziomie w następujących krokach:

- Zainstaluj SAP HANA węzeł główny zgodnie z dokumentacją SAP
- **Po instalacji należy zmienić plik Global. ini i dodać parametr "basepath_shared = No" do pliku Global. ini**. Ten parametr umożliwia uruchamianie SAP HANA w poziomie skalowania bez współużytkowanych woluminów **/Hana/Data** i **/Hana/log** między węzłami. Szczegółowe informacje znajdują się w temacie [SAP uwagi #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Po zmianie parametru Global. ini Uruchom ponownie wystąpienie SAP HANA
- Dodaj dodatkowe węzły procesu roboczego. Zobacz również <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Określ wewnętrzną sieć do SAP HANA komunikacji między węzłami podczas instalacji lub później, używając na przykład lokalnego hdblcm. Aby uzyskać bardziej szczegółową dokumentację, zobacz również temat [SAP uwagi #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Zgodnie z tą procedurą konfiguracji zainstalowana konfiguracja skalowalna w poziomie korzysta z dysków nieudostępnionych do uruchamiania **/Hana/Data** i **/Hana/log**. Wolumin **/Hana/Shared** należy umieścić w udziale NFS o wysokiej dostępności.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dynamiczne warstwy 2,0 dla usługi Azure Virtual Machines

Oprócz SAP HANA certyfikatów na maszynach wirtualnych z serii M platformy Azure, SAP HANA dynamiczne warstwy 2,0 są również obsługiwane w Microsoft Azure (SAP HANA Zobacz dodatkowe linki do dokumentacji dynamicznej warstwy). Chociaż nie ma żadnych różnic w instalowaniu produktu lub działaniu, na przykład za pośrednictwem SAP HANA panelu sterowania wewnątrz maszyny wirtualnej platformy Azure, istnieje kilka ważnych elementów, które są wymagane do oficjalnego wsparcia na platformie Azure. Te kluczowe punkty opisano poniżej. W całym artykule skrót "DT 2,0" ma zostać użyty zamiast dynamicznej obsługi warstw o pełnej nazwie 2,0.

SAP HANA warstwy dynamicznej 2,0 nie jest obsługiwana przez SAP BW lub S4HANA. Główne przypadki użycia teraz są obecnie natywnymi aplikacjami platformy HANA.


### <a name="overview"></a>Omówienie

Na poniższej ilustracji przedstawiono omówienie obsługi systemu DT 2,0 na Microsoft Azure. Istnieje zestaw obowiązkowych wymagań, które należy wykonać, aby przestrzegać oficjalnych certyfikatów:

- DT 2,0 musi być zainstalowana na dedykowanej maszynie wirtualnej platformy Azure. Może nie działać na tej samej maszynie wirtualnej, na której uruchomiono SAP HANA
- Maszyny wirtualne SAP HANA i DT 2,0 muszą zostać wdrożone w ramach tej samej sieci wirtualnej platformy Azure
- Maszyny wirtualne SAP HANA i DT 2,0 muszą zostać wdrożone przy użyciu przyspieszonej sieci platformy Azure, która jest włączona
- Typem magazynu dla maszyn wirtualnych DT 2,0 musi być usługa Azure Premium Storage
- Należy dołączyć wiele dysków platformy Azure do maszyny wirtualnej DT 2,0
- Jest wymagane, aby utworzyć wolumin rozłożony/RAID oprogramowania (za pośrednictwem LVM lub mdadm) przy użyciu rozłożenia na dyskach platformy Azure

Więcej szczegółów można wyjaśnić w poniższych sekcjach.

![Omówienie architektury SAP HANA DT 2,0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedykowana maszyna wirtualna platformy Azure dla SAP HANA DT 2,0

Na platformie Azure IaaS, DT 2,0 jest obsługiwana tylko na dedykowanej maszynie wirtualnej. Nie można uruchomić DT 2,0 na tej samej maszynie wirtualnej platformy Azure, w której uruchomiono wystąpienie platformy HANA. Początkowe dwa typy maszyn wirtualnych mogą służyć do uruchamiania SAP HANA DT 2,0:

- M64-32ms 
- E32sv3 

[Tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) Zobacz opis typu maszyny wirtualnej

Zgodnie z podstawową koncepcją DT 2,0, która odnosi się do odciążania "grzanych" danych w celu oszczędności kosztów, warto użyć odpowiednich rozmiarów maszyn wirtualnych. Nie istnieje ścisła reguła dotycząca możliwych kombinacji. Zależy to od obciążenia określonego klienta.

Zalecane konfiguracje:

| Typ maszyny wirtualnej SAP HANA | Typ maszyny wirtualnej DT 2,0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Możliwe są wszystkie kombinacje SAP HANA-certyfikowanych maszyn wirtualnych z serii M z obsługiwanymi maszynami wirtualnymi DT 2,0 (M64-32ms i E32sv3).


### <a name="azure-networking-and-sap-hana-dt-20"></a>Sieci platformy Azure i SAP HANA DT 2,0

Zainstalowanie DT 2,0 na dedykowanej maszynie wirtualnej wymaga przepływności sieci między maszyną wirtualną DT 2,0 a maszyną wirtualną SAP HANA o pojemności 10 GB. W związku z tym należy umieścić wszystkie maszyny wirtualne w tej samej sieci wirtualnej platformy Azure i włączyć usługę Azure przyspieszoną.

Zobacz dodatkowe informacje o przyspieszonej sieci platformy Azure [tutaj](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Magazyn maszyny wirtualnej dla SAP HANA DT 2,0

Zgodnie z najlepszymi rozwiązaniami dotyczącymi najlepszych rozwiązań o 2,0, przepustowość we/wy dysku powinna wynosić co najmniej 50 MB na sekundę na rdzeń fizyczny. Przeglądając specyfikację dla dwóch typów maszyn wirtualnych platformy Azure, które są obsługiwane dla DT 2,0 maksymalny limit przepływności we/wy dysku dla maszyny wirtualnej wygląda następująco:

- E32sv3:768 MB/s (pamięć podręczna), co oznacza stosunek 48 MB/s na rdzeń fizyczny
- M64-32ms: 1000 MB/s (pamięć podręczna), co oznacza stosunek 62,5 MB/s na rdzeń fizyczny

Należy dołączyć wiele dysków platformy Azure do maszyny wirtualnej DT 2,0 i utworzyć oprogramowanie RAID (Stripe) na poziomie systemu operacyjnego w celu osiągnięcia maksymalnego limitu przepływności dysku na maszynę wirtualną. Pojedynczy dysk platformy Azure nie może zapewnić przepływności do osiągnięcia maksymalnego limitu dla maszyny wirtualnej w tym zakresie. Usługa Azure Premium Storage jest wymagana do uruchomienia DT 2,0. 

- Szczegółowe informacje o dostępnych typach dysków platformy Azure można znaleźć [tutaj](../../windows/disks-types.md)
- Szczegółowe informacje o tworzeniu RAID oprogramowania za pośrednictwem mdadm można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Szczegółowe informacje o konfigurowaniu LVM w celu utworzenia woluminu rozłożonego na potrzeby maksymalnej przepływności można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

W zależności od wymagań dotyczących rozmiaru dostępne są różne opcje umożliwiające osiągnięcie maksymalnej przepływności maszyny wirtualnej. Oto możliwe konfiguracje dysków woluminów danych dla każdego typu maszyny wirtualnej DT 2,0, aby osiągnąć górny limit przepływności maszyny wirtualnej. Maszyna wirtualna E32sv3 powinna być traktowana jako poziom wpisu dla mniejszych obciążeń. W przypadku gdy należy wymusić, że nie jest ona wystarczająco szybko dostępna, może być konieczna zmiana rozmiaru maszyny wirtualnej na M64-32ms.
Ponieważ maszyna wirtualna M64-32ms ma dużo pamięci, obciążenie we/wy może nie dotrzeć do limitu szczególnie w przypadku obciążeń intensywnie korzystających z operacji odczytu. W związku z tym mniejsza liczba dysków w zestawie rozłożonym może być wystarczająca w zależności od obciążenia określonego przez klienta. Jednak aby mieć pewność, że te konfiguracje dysków zostały wybrane w celu zagwarantowania maksymalnej przepływności:


| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Konfiguracja dysku 1 | Konfiguracja dysku 2 | Konfiguracja dysku 3 | Konfiguracja dysku 4 | Konfiguracja dysku 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 > 16 TB | 4 x P40 — > 8 TB | 5 x P30 > 5 TB | 7 x P20 > 3,5 TB | 8 x P15 — > 2 TB | 
| E32sv3 | 3 x P50 — > 12 TB | 3 x P40 — > 6 TB | 4 x P30 — > 4 TB | 5 x P20 > 2,5 TB | 6 x P15 > 1,5 TB | 


Szczególnie w przypadku, gdy obciążenie jest w trakcie odczytu, może zwiększyć wydajność operacji we/wy, aby włączyć pamięć podręczną hosta platformy Azure, która jest zalecana dla woluminów danych oprogramowania bazy danych. W przypadku dziennika transakcji pamięć podręczna dysku hosta platformy Azure musi mieć wartość "Brak". 

W odniesieniu do rozmiaru woluminu dziennika zalecany punkt początkowy jest algorytmem heurystycznym 15% rozmiaru danych. Tworzenie woluminu dziennika można wykonać przy użyciu różnych typów dysków platformy Azure, w zależności od kosztów i przepływności. Dla woluminu dziennika jest wymagana Wysoka przepływność we/wy.  W przypadku używania maszyny wirtualnej typu M64-32ms zdecydowanie zaleca się włączenie [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Usługa Azure akcelerator zapisu zapewnia optymalne opóźnienie zapisu na dysku dla dziennika transakcji (dostępne tylko dla serii M). Istnieją pewne elementy, które należy wziąć pod uwagę, gdy jest to maksymalna liczba dysków na maszynę wirtualną. Szczegóły dotyczące akcelerator zapisu można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Poniżej przedstawiono kilka przykładów dotyczących określania wielkości woluminu dziennika:

| rozmiar woluminu danych i typ dysku | wolumin dziennika i typ dysku konfiguracja 1 | wolumin dziennika i typ dysku konfiguracja 2 |
| --- | --- | --- |
| 4 x P50 > 16 TB | 5 x P20 > 2,5 TB | 3 x P30 — > 3 TB |
| 6 x P15 > 1,5 TB | 4 x P6 > 256 GB | 1 x P15 -> 256 GB |


Podobnie jak w przypadku SAP HANA skalowania w poziomie, katalog/Hana/Shared musi być współużytkowany między maszyną wirtualną SAP HANA i maszyną wirtualną DT 2,0. Taka sama architektura jak w przypadku SAP HANA skalowanie w poziomie przy użyciu dedykowanych maszyn wirtualnych, które działają jako serwer NFS o wysokiej dostępności. Aby zapewnić udostępniony wolumin kopii zapasowej, można użyć identycznego projektu. Jest jednak do klienta, o ile jest to konieczne, lub jeśli wystarczy użyć dedykowanej maszyny wirtualnej o wystarczającej pojemności magazynu do działania jako serwer zapasowy.



### <a name="links-to-dt-20-documentation"></a>Linki do dokumentacji DT 2,0 

- [SAP HANA Podręcznik instalacji i aktualizacji warstw dynamicznych](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA dynamiczne samouczki i zasoby dotyczące warstw](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA koncepcji weryfikacji dynamicznej warstwy](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Udoskonalenia dotyczące warstw dynamicznego w programie SAP HANA 2,0 SPS 02](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operacje wdrażania SAP HANA na maszynach wirtualnych platformy Azure
W poniższych sekcjach opisano niektóre operacje związane z wdrażaniem systemów SAP HANA na maszynach wirtualnych platformy Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Wykonywanie kopii zapasowych i przywracanie operacji na maszynach wirtualnych platformy Azure
W poniższych dokumentach opisano sposób tworzenia kopii zapasowych i przywracania SAP HANA wdrożenia:

- [Omówienie kopii zapasowych oprogramowania SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA kopii zapasowej na poziomie pliku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Wzorzec migawek magazynu SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Uruchom i ponownie uruchom maszyny wirtualne, które zawierają SAP HANA
W przypadku korzystania z chmury publicznej na platformie Azure jest naliczana opłata tylko za minuty obliczeniowe. Na przykład po wyłączeniu maszyny wirtualnej, na której działa SAP HANA, opłaty są naliczane tylko za koszty magazynowania w tym czasie. Inna funkcja jest dostępna w przypadku określenia statycznych adresów IP dla maszyn wirtualnych w początkowym wdrożeniu. Po ponownym uruchomieniu maszyny wirtualnej, która ma SAP HANA, maszyna wirtualna zostanie ponownie uruchomiona z poprzednimi adresami IP. 


### <a name="use-saprouter-for-sap-remote-support"></a>Korzystanie z SAProuter dla zdalnej pomocy technicznej SAP
Jeśli masz połączenie lokacja-lokacja między lokalizacjami lokalnymi i platformą Azure i używasz składników SAP, prawdopodobnie jest już uruchomione SAProuter. W takim przypadku należy wykonać następujące czynności w celu uzyskania pomocy zdalnej:

- Obsługa prywatnego i statycznego adresu IP maszyny wirtualnej, która hostuje SAP HANA w konfiguracji SAProuter.
- Skonfiguruj sieciowej grupy zabezpieczeń podsieci, która hostuje maszynę wirtualną HANA w taki sposób, aby zezwalała na ruch przez port TCP/IP 3299.

Jeśli łączysz się z platformą Azure za pomocą Internetu i nie masz routera SAP dla maszyny wirtualnej z SAP HANA, musisz zainstalować składnik. Zainstaluj program SAProuter na oddzielnej maszynie wirtualnej w podsieci zarządzania. Na poniższej ilustracji przedstawiono surowy schemat wdrażania SAP HANA bez połączenia lokacja-lokacja i z SAProuter:

![Surowy schemat wdrażania dla SAP HANA bez połączenia typu lokacja-lokacja i SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Pamiętaj, aby zainstalować program SAProuter na oddzielnej maszynie wirtualnej, a nie na maszynie wirtualnej serwera przesiadkowego. Oddzielna maszyna wirtualna musi mieć statyczny adres IP. Aby połączyć SAProuter z SAProuter, który jest hostowany przez SAP, skontaktuj się z SAP dla adresu IP. (SAProuter hostowany przez SAP to odpowiednik wystąpienia SAProuter, które zostało zainstalowane na maszynie wirtualnej). Użyj adresu IP z SAP, aby skonfigurować wystąpienie usługi SAProuter. W ustawieniach konfiguracji jedynym wymaganym portem jest port TCP 3299.

Aby uzyskać więcej informacji o konfigurowaniu i obsłudze połączeń pomocy zdalnej za pomocą usługi SAProuter, zobacz [dokumentację oprogramowania SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Wysoka dostępność dzięki SAP HANA na natywnych maszynach wirtualnych platformy Azure
W przypadku korzystania z SUSE Linux Enterprise Server dla aplikacji SAP 12 z dodatkiem SP1 lub nowszym można nawiązać klaster Pacemaker z urządzeniami STONITH. Za pomocą urządzeń można skonfigurować konfigurację SAP HANA, która używa replikacji synchronicznej z replikacją systemu HANA i automatycznym trybem failover. Aby uzyskać więcej informacji na temat procedury instalacji, zobacz [SAP HANA przewodniku o wysokiej dostępności dla usługi Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
