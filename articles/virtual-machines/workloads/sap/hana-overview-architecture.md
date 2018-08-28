---
title: Omówienie i architektura oprogramowania SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Omówienie architektury sposób wdrażania oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d28b6ea5612a3db539c51d2603c3f12282ca519
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090420"
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Omówienie platformy SAP HANA (duże wystąpienia) i architektury na platformie Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Co to jest oprogramowanie SAP HANA na platformie Azure (duże wystąpienia)?

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) to unikatowe rozwiązanie na platformie Azure. Oprócz zapewniania maszyn wirtualnych umożliwiające wdrażanie i uruchamianie oprogramowania SAP HANA, platforma Azure oferuje możliwość uruchamiania i wdrażania oprogramowania SAP HANA na serwerach bez systemu operacyjnego, które są przeznaczone dla Ciebie. SAP HANA na platformie Azure (duże wystąpienia) rozwiązanie opiera się na sprzętu bez systemu operacyjnego hosta nieudostępniane/serwer, który jest przypisany do Ciebie. Sprzęt serwera jest osadzony w większych sygnatur, zawierające obliczeń/serwera, sieci i infrastruktury magazynu. Jako połączenie jest HANA dostosowane Centrum integracji danych (TDI) certyfikat. Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) oferuje jednostki SKU z innego serwera lub rozmiarów. Jednostki może mieć 36 rdzeni procesora CPU Intel i 768 GB pamięci, a następnie przejść do jednostek, które mają maksymalnie 480 rdzeni procesora CPU Intel i maksymalnie 24 TB pamięci.

Izolacja klienta w ramach sygnatury infrastruktury odbywa się w dzierżawach, który wygląda następująco:

- **Sieć**: izolacji klientów w ramach infrastruktury stack za pośrednictwem sieci wirtualnych w dzierżawie klienta przypisany. Dzierżawa jest przypisany do jednego klienta. Klient może mieć wielu dzierżaw. Izolacja sieci dzierżaw zabrania komunikacji sieciowej między dzierżawami, na poziomie sygnatury infrastruktury, nawet, jeśli dzierżawcy należą do tego samego klienta.
- **Składniki magazynu**: izolacji za pośrednictwem magazynu maszyn wirtualnych, które mają woluminy magazynu przypisane do nich. Woluminy magazynu można przypisać do jednego magazynu tylko maszyny wirtualnej. Magazyn maszyny wirtualnej jest przypisane wyłącznie do jednego pojedynczej dzierżawy w stosie certyfikowanych infrastruktury SAP HANA TDI. W rezultacie woluminów magazynu przypisane do maszyny wirtualnej magazynu są dostępne w jednej określonej i pokrewnych dzierżawy tylko. Nie są one widoczne między różnych dzierżawach wdrożone.
- **Serwera lub hosta**: jednostka serwera lub hosta nie jest udostępniana między klientami lub dzierżawcy. Serwer lub hoście z klientem jest jednostki atomic obliczeń bez systemu operacyjnego, przypisany do jednego pojedynczej dzierżawy. *Nie* partycjonowanie sprzętowe lub nietrwałego Partycjonowanie jest używany, może prowadzić do współużytkowania hosta lub serwer z innego klienta. Woluminy magazynu, które są przypisane do magazynu maszyny wirtualnej określonej dzierżawy są instalowane do takiego serwera. Dzierżawca może mieć jeden do wielu jednostek serwera różne jednostki SKU przypisana wyłącznie.
- W ramach oprogramowania SAP HANA w sygnaturze infrastruktury platformy Azure (duże wystąpienia) wielu różnych dzierżawach są wdrażane i odizolowane od siebie wzajemnie koncepcjami dzierżawy w sieci, magazynu i poziomu obliczeniowego. 


Te jednostki bez systemu operacyjnego serwera są obsługiwane tylko uruchamianie oprogramowania SAP HANA. Warstwy aplikacji SAP lub warstwy pośredniczącym obciążenie działa na maszynach wirtualnych. Sygnatury infrastruktury z systemem SAP HANA na platformie Azure (duże wystąpienia) jednostki są podłączone do szkieletowymi usług sieci platformy Azure. W ten sposób znajduje się połączenie o małych opóźnieniach między maszyny wirtualne i SAP HANA na platformie Azure (duże wystąpienia) jednostki.

W tym dokumencie jest jednym z kilku dokumentów, które obejmują oprogramowanie SAP HANA na platformie Azure (duże wystąpienia). Ten dokument wprowadza podstawowej architektury, obowiązkach i usług udostępnianych przez to rozwiązanie. Omówiono także ogólne możliwości rozwiązania. Dla większości innych obszarów, takich jak sieć i łączność cztery inne dokumenty, które obejmują szczegółowe informacje o Przechodzenie do szczegółów i. Dokumentacja środowiska SAP Hana na platformie Azure (duże wystąpienia) nie obejmuje aspektów instalacji oprogramowania SAP NetWeaver lub wdrożenia oprogramowania SAP NetWeaver na maszynach wirtualnych. Oprogramowanie SAP NetWeaver na platformie Azure są omówione w osobnych dokumentów, w tym samym kontenerze dokumentacji platformy Azure. 


Różne dokumenty wskazówek dotyczących dużych wystąpień HANA obejmują następujące obszary:

- [Omówienie platformy SAP HANA (duże wystąpienia) i architektury na platformie Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalowanie i konfigurowanie oprogramowania SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Rozwiązywanie problemów z platformą SAP HANA (duże wystąpienia) i monitorowania na platformie Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Wysoka dostępność, skonfigurować w systemie SUSE przy użyciu pomocą metody STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Kopia zapasowa systemu operacyjnego i przywracania dla jednostek SKU typu II](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definicje

Kilka wspólnych definicji są powszechnie używane w zakresie architektury i technicznej Deployment Guide. Należy zwrócić uwagę, następujące warunki i ich znaczenie:

- **IaaS**: infrastruktura jako usługa.
- **PaaS**: platforma jako usługa.
- **SaaS**: oprogramowanie jako usługa.
- **Składnik SAP**: poszczególnych aplikacji SAP, takich jak ERP i przeniesieniu jej centralnej składnika (ECC), Business Warehouse (BW), Menedżer rozwiązania lub Enterprise Portal (EP). Składniki SAP mogą być oparte na tradycyjnych technologii ABAP i Java lub aplikacji innych niż NetWeaver na podstawie takich jak obiekty biznesowych.
- **Środowisko SAP**: co najmniej jednego składnika SAP są logicznie pogrupowane w celu wykonywania funkcji biznesowych, takich jak rozwój, zapewniania jakości, szkolenia, odzyskiwania po awarii lub produkcji.
- **Środowisko SAP**: odwołuje się do całego zasoby SAP w orientacji poziomej usługi IT. Środowisko SAP obejmuje wszystkie produkcji i środowisk nieprodukcyjnych.
- **SAP system**: kombinacja warstwy system DBMS i warstwy aplikacji, na przykład system SAP ERP i przeniesieniu jej rozwoju, system SAP BW testu i system produkcyjny SAP CRM. Wdrożeń platformy Azure nie obsługują dzielenia tych dwóch warstw między lokalną i platformą Azure. System SAP jest wdrożone w środowisku lokalnym lub w jego wdrożonych na platformie Azure. Możesz wdrożyć różnych systemów środowisko SAP na platformie Azure lub lokalnie. Na przykład można wdrożyć rozwoju SAP CRM i systemy testowe na platformie Azure, podczas wdrażania SAP CRM produkcji systemu lokalnego. Dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) jest on przeznaczony hosta z warstwy aplikacji SAP systemów SAP na maszynach wirtualnych i powiązanych wystąpienie SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia) sygnatury jednostce.
- **Duże wystąpienia sygnatury**: stos infrastruktury sprzętu, który jest certyfikatem TDI i programu SAP HANA i dedykowane do uruchamiania wystąpień oprogramowania SAP HANA w systemie Azure.
- **Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia):** oficjalną nazwą języka dla oferty na platformie Azure do uruchamiania oprogramowania HANA wystąpienia w na sprzęcie z certyfikatem TDI i programu SAP HANA, które zostało wdrożone w dużych wystąpień sygnatury w różnych regionach platformy Azure. Powiązane termin *dużych wystąpień HANA* jest mała w przypadku *platformy SAP HANA na platformie Azure (duże wystąpienia)* i jest powszechnie używana w tym przewodniku wdrożenia technicznego.
- **Między środowiskami lokalnymi**: w tym artykule opisano scenariusz, w której maszyny wirtualne są wdrażane z subskrypcją platformy Azure, site to site, obejmujące wiele lokacji lub połączenie usługi Azure ExpressRoute między centrami danych w środowisku lokalnym i platformą Azure. Dokumentacja wspólnych platformy Azure, tego rodzaju wdrożenia są również opisać jako scenariuszy obejmujących wiele lokalizacji. Przyczyna połączenie ma rozszerzone na platformę Azure w domenach lokalnych, lokalnej usługi Azure Active Directory/OpenLDAP i DNS w środowisku lokalnym. Pozioma w środowisku lokalnym jest rozszerzony do zasobów platformy Azure w subskrypcji platformy Azure. Dla tego rozszerzenia maszyn wirtualnych może być częścią domeny w środowisku lokalnym. 

   Użytkownicy domeny z domeny lokalnej można uzyskiwać dostęp do serwerów i uruchamiania usług na tych maszynach wirtualnych (takiej jak DBMS usługi). Komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożony w środowisku lokalnym i maszyn wirtualnych wdrożonych przez Azure jest możliwe. Ten scenariusz jest typowy sposób, w którym większość zasobów SAP są wdrażane. Aby uzyskać więcej informacji, zobacz [planowania i projektowania dla bramy sieci VPN Azure](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i [tworzenie sieci wirtualnej za pomocą połączenia typu lokacja lokacja przy użyciu witryny Azure portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Dzierżawy**: klient wdrożony w sygnaturze dużych wystąpień HANA pobiera izolowane do *dzierżawy.* Dzierżawa jest odizolowany w sieci, magazynu i warstwy obliczeniowej od pozostałych dzierżawców. Jednostki magazynu i mocy obliczeniowej, przypisane do różnych dzierżawach nie widzę, siebie nawzajem i komunikować się ze sobą na poziomie sygnatury dużych wystąpień HANA. Klient może wybrać wdrożenie w różnych dzierżawach. Nawet wówczas nie ma komunikacji między dzierżawami, na poziomie sygnatury dużych wystąpień HANA.
- **Jednostka SKU kategorii**: dla platformy HANA duże wystąpienie, są dostępne w następujących dwóch kategorii jednostek SKU:
    - **Typ I klasa**: S72, S72m, S144, S144m, platformie S192, platformie S192m i S192xm
    - **Typ klasy II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm i S960m


Szereg dodatkowych zasobów są dostępne w sposób wdrażania obciążeń SAP w chmurze. Jeśli planujesz wykonać wdrożenie oprogramowania SAP Hana na platformie Azure, musisz być doświadczenie w pracy z i korzystają z zasadami modelu IaaS platformy Azure i wdrażanie obciążeń SAP na platformie IaaS Azure. Przed kontynuowaniem, zobacz [rozwiązania użycia adapterów SAP na maszynach wirtualnych Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby uzyskać więcej informacji. 

## <a name="certification"></a>Certyfikacja

Oprócz certyfikacji NetWeaver środowisko SAP wymaga specjalnych certyfikacji platformy SAP Hana do obsługi platformy SAP HANA na niektórych infrastruktury, takich jak Azure IaaS.

Core Uwaga SAP w oprogramowaniu NetWeaver oraz stopień certyfikacji platformy SAP HANA, to [SAP Uwaga #1928533 — aplikacje środowiska SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533).

Rekordy certyfikacji dla platformy SAP HANA na platformie Azure (duże wystąpienia) jednostki można znaleźć w [platform IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lokacji. 

Oprogramowanie SAP HANA na typy Azure (duże wystąpienia), zgodnie z platformy SAP HANA certyfikat witryny IaaS platformy zapewnia firmy Microsoft i SAP klientom możliwość wdrażania dużych SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA lub innych obciążeń SAP HANA na platformie Azure. To rozwiązanie jest oparte na sygnatury certyfikowanym sprzęcie dedykowanym platformy SAP HANA ([platformy SAP HANA dostosowane integrację centrum danych — TDI](https://scn.sap.com/docs/DOC-63140)). Jeśli uruchamiasz rozwiązanie SAP HANA TDI skonfigurowane, wszystkie aplikacje oparte na oprogramowanie SAP HANA (takie jak oprogramowanie SAP Business Suite na platformie SAP HANA, SAP BW na BW4/HANA, SAP HANA i S4/HANA) działa na infrastruktury sprzętowej.

W porównaniu do uruchamiania oprogramowania SAP HANA na maszynach wirtualnych, to rozwiązanie ma jedną z korzyści. Oferuje ona dużo większe woluminy pamięci. Aby włączyć to rozwiązanie, należy zrozumieć następujące kluczowe aspekty:

- Uruchom aplikacje warstwy i innych niż SAP aplikacji SAP na maszynach wirtualnych, które są hostowane w sygnatury zwykle sprzętowych platformy Azure.
- Klient w obrębie lokalnej infrastruktury, centrów danych oraz wdrożenia aplikacji są podłączone do platforma usług w chmurze za pośrednictwem usługi ExpressRoute (zalecane) lub wirtualnej sieci prywatnej (VPN). Usługa Active Directory i DNS również zostały rozszerzone na platformę Azure.
- Uruchamia wystąpienie bazy danych SAP HANA w przypadku obciążeń oprogramowania HANA na platformie SAP HANA na platformie Azure (duże wystąpienia). Sygnatura duże wystąpienie jest podłączony do sieci platformy Azure, aby oprogramowanie uruchomione na maszynach wirtualnych mogą wchodzić w interakcje z wystąpieniem platformy HANA w dużych wystąpień HANA.
- Sprzęt platformy SAP Hana na platformie Azure (duże wystąpienia) jest dedykowany sprzęt w usłudze IaaS z systemem SUSE Linux Enterprise Server lub Red Hat Enterprise Linux, na których wstępnie zainstalowano. Podobnie jak w przypadku maszyn wirtualnych, dalsze aktualizacje i obsługa, aby system operacyjny jest odpowiedzialny za.
- Instalacja oprogramowania HANA lub wszelkie dodatkowe składniki niezbędne do uruchamiania oprogramowania SAP HANA na jednostkach po dużych wystąpień HANA jest odpowiedzialny za. Wszystkie odpowiednie trwających operacji i administracyjnej platformy SAP HANA na platformie Azure są również odpowiedzialny za.
- Oprócz rozwiązania opisane w tym miejscu można zainstalować inne składniki w Twojej subskrypcji platformy Azure, który nawiązuje połączenie z platformą SAP HANA na platformie Azure (duże wystąpienia). Przykłady to składniki umożliwiające komunikację z lub bezpośrednio do platformy SAP HANA serwery baz danych, takich jak serwery przeskoku protokołu RDP, SAP HANA Studio, usług danych SAP dla scenariuszy SAP BI rozwiązania do monitorowania sieci.
- Jak Azure duże wystąpienie oprogramowania HANA oferuje obsługę wysokiej dostępności i funkcje odzyskiwania po awarii.

## <a name="architecture"></a>Architektura

Na wysokim poziomie SAP HANA na platformie Azure (duże wystąpienia) rozwiązanie ma warstwy aplikacji SAP znajdującej się na maszynach wirtualnych. Warstwa bazy danych znajduje się na sprzęcie skonfigurowane SAP TDI, znajduje się w sygnaturze dużych wystąpień, w tym samym regionie platformy Azure, która jest połączona z infrastrukturą IaaS usługi Azure.

> [!NOTE]
> Wdróż warstwę aplikacji SAP, w tym samym regionie platformy Azure jako warstwa SAP DBMS. Ta reguła jest dobrze udokumentowana w opublikowanych informacji na temat obciążeń SAP na platformie Azure. 

Ogólna Architektura środowiska SAP Hana na platformie Azure (duże wystąpienia) udostępnia konfigurację sprzęt z certyfikatem TDI i SAP, czyli poziomie wirtualizacji, komputerach bez systemu operacyjnego, serwer o wysokiej wydajności dla bazy danych SAP HANA. Zapewnia również możliwość i elastyczność platformy Azure, skalować zasoby warstwy aplikacji SAP do własnych potrzeb.

![Omówienie architektury oprogramowania SAP Hana na platformie Azure (duże wystąpienia)](./media/hana-overview-architecture/image1-architecture.png)

Architektura, wyświetlane jest podzielony na trzy sekcje:

- **Po prawej stronie**: Pokazuje infrastruktury lokalnej, uruchamianego z różnych aplikacji w danych centra tak, aby użytkownicy końcowi mogą uzyskiwać dostęp aplikacji biznesowych, takich jak SAP. W idealnym przypadku tej lokalnej infrastruktury następnie jest połączony z platformą Azure za pomocą [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centrum**: Pokazuje IaaS platformy Azure i w tym przypadku korzystanie z maszyn wirtualnych w celu hostowania SAP lub inne aplikacje korzystające z platformy SAP HANA jako DBMS system. Mniejsze wystąpień HANA, które funkcją pamięci, które maszyny wirtualne są wdrażane na maszynach wirtualnych wraz z ich warstwy aplikacji. Aby uzyskać więcej informacji na temat maszyn wirtualnych, zobacz [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/).

   Usługi sieci platformy Azure są używane do grupowania systemów SAP wraz z innych aplikacji w sieciach wirtualnych. Te sieci wirtualne nawiązać połączenie systemów lokalnych, jak również do platformy SAP HANA na platformie Azure (duże wystąpienia).

   Oprogramowanie SAP NetWeaver aplikacje i bazy danych, które są obsługiwane na platformie Azure, zobacz [SAP pomocy technicznej Uwaga #1928533 — aplikacje środowiska SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533). Aby uzyskać dokumentację na temat wdrażania rozwiązań SAP na platformie Azure zobacz:

  -  [Używanie SAP na maszynach wirtualnych Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Korzystanie z rozwiązań SAP na maszynach wirtualnych platformy Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Po lewej stronie**: przedstawia sprzęt z certyfikatem TDI i programu SAP HANA w sygnaturze dużego wystąpienia platformy Azure. Jednostki dużych wystąpień HANA są połączone z sieciami wirtualnymi w subskrypcji przy użyciu tej samej technologii co łączność ze środowiska lokalnego na platformę Azure.

Sygnatura dużego wystąpienia platformy Azure, sama obejmuje następujące składniki:

- **Przetwarzanie**: serwery, które są oparte na procesorach Intel Xeon E7-8890v3 lub Intel Xeon E7-8890v4, zapewniają wymagane możliwości obliczeniowych, które zostały SAP HANA z certyfikatem platformy.
- **Sieć**: A unified szybkich sieci szkieletowej, łączącym obliczeń, magazynu i składniki sieci LAN.
- **Magazyn**: infrastruktury magazynu, który jest dostępny za pośrednictwem jednolitego sieci szkieletowej. Określonej pojemności, który znajduje się zależy od określonego oprogramowania SAP HANA na konfiguracji platformy Azure (duże wystąpienia), który jest wdrożony. Większa pojemność magazynu jest dostępna za dodatkową opłatą miesięcznych.

W ramach infrastruktury wielodostępnych sygnatury dużych wystąpień klienci są wdrażane jako dzierżaw izolowane. Podczas wdrażania dzierżawy nadaj nazwę subskrypcji platformy Azure w ramach rejestracji na platformie Azure. Tej subskrypcji platformy Azure jest rozliczany w dużych wystąpień HANA. Te dzierżawy mają relację 1:1 z subskrypcją platformy Azure. Dla sieci jest możliwe uzyskanie dostępu Jednostka dużych wystąpień HANA wdrożony w jednej dzierżawy w jednym regionie platformy Azure z różnych sieci wirtualnych, które należą do różnych subskrypcji platformy Azure. Te subskrypcje platformy Azure muszą należeć do tej samej rejestracji na platformie Azure. 

Podobnie jak w przypadku maszyn wirtualnych, SAP HANA na platformie Azure (duże wystąpienia) jest oferowana w wielu regionach platformy Azure. Aby zaoferować awarii funkcje odzyskiwania, można zgodzić się na. Różnych sygnaturach duże wystąpienie w ramach jednego regionu polityczne geograficznej są połączone ze sobą. Na przykład HANA duże wystąpienia sygnatury w zachodnich stanach USA i wschodnie stany USA są połączone za pośrednictwem dedykowanej sieci łącza replikacji odzyskiwania po awarii. 

Tak samo, jak można wybrać między różnymi typami maszyny Wirtualnej z usługą Azure Virtual Machines, możesz wybrać różne jednostki SKU z platformy HANA duże wystąpienie, które są dostosowane do typów różnych obciążeń SAP Hana. SAP dotyczy stosunek pamięci do procesorów gniazda dla różnych obciążeń, oparte na generacje procesor Intel. W poniższej tabeli przedstawiono oferowanych typów jednostek SKU.

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) usługi jest dostępny w wielu konfiguracji w regionach platformy Azure zachodnie stany USA i wschodnie stany USA, Australia Wschodnia, Australia Południowo-Wschodnia, Europa Zachodnia, Europa Północna, Japonia, część wschodnia i Japonia, część zachodnia.

[Duże wystąpienia jednostki SKU HANA z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) listy, takich jak:

| Rozwiązanie SAP | Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| --- | --- | --- | --- | --- |
| Optymalizacja pod kątem OLAP: oprogramowanie SAP BW, BW/4HANA<br /> lub SAP HANA dla ogólnych obciążeń OLAP | Oprogramowanie SAP HANA na platformie Azure S72<br /> — 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 rdzeni procesora CPU i 72 wątki procesora CPU |  768 GB |  3 TB | Dostępne |
| --- | Oprogramowanie SAP HANA na platformie Azure S144<br /> — 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 rdzeni procesora CPU i 144 wątki procesora CPU |  1,5 TB |  6 TB | Nie jest już dostępna |
| --- | Oprogramowanie SAP HANA na platformie Azure S192<br /> — 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora CPU i 192 wątki procesora CPU |  W WERSJI 2.0 TB |  8 TB | Dostępne |
| --- | Oprogramowanie SAP HANA na platformie Azure S384<br /> — 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora CPU |  4.0 TB |  16 TB | Dostępne |
| Optymalizacja pod kątem OLTP: oprogramowanie SAP Business Suite<br /> SAP HANA lub S/4HANA (OLTP)<br /> Ogólne OLTP | Oprogramowanie SAP HANA na platformie Azure S72m<br /> — 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 rdzeni procesora CPU i 72 wątki procesora CPU |  1,5 TB |  6 TB | Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S144m<br /> — 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 rdzeni procesora CPU i 144 wątki procesora CPU |  3.0 TB |  12 TB | Nie jest już dostępna |
|---| Oprogramowanie SAP HANA na platformie Azure S192m<br /> — 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora CPU i 192 wątki procesora CPU  |  4.0 TB |  16 TB | Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S384m<br /> — 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora CPU |  W WERSJI 6.0 TB |  18 TB | Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S384xm<br /> — 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora CPU |  8.0 TB |  22 TB |  Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S576m<br /> — 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątki procesora CPU |  12.0 TB |  28 TB | Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S768m<br /> — 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzeni procesora CPU i 768 wątki procesora CPU |  16.0 TB |  36 TB | Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S960m<br /> — 20 x procesor Intel® Xeon® E7-8890 v4<br /> 480 rdzeni procesora CPU i 960 wątki procesora CPU |  20.0 TB |  46 TB | Dostępne |


W obszarze TDIv5 HANA SAP SAP umożliwia skonfigurowanie rozmiaru właściwe dla klienta i projektów specyficznych dla klientów, które mogą prowadzić do konfiguracji serwera, które nie są wymienione jako certyfikat w procesie:

- [Oprogramowanie SAP HANA certyfikowane urządzenia](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Platform IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

W wielu przypadkach te konfiguracje specyficzne dla klienta serwera wykonać więcej pamięci niż jednostek serwera, certyfikat z oprogramowaniem SAP. W pracy z SAP, klienci mają możliwość uzyskanie pomocy technicznej SAP, a także zaświadczasz, w przypadku ich konfiguracji serwera wielkości właściwe dla klienta. Na platformie Azure następujące dużych wystąpień HANA standardowej jednostki SKU są dostępne, a w programie Microsoft cennik dla projektów TDIv5 rozmiaru właściwe dla klienta.


| Oryginalnej jednostki SKU, które mogą być <br /> rozszerzony w pamięci | Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| --- | --- | --- | --- | --- |
| Platformie S192m może zostać rozszerzony do | Oprogramowanie SAP HANA na S192xm platformy Azure<br /> — 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora CPU i 192 wątki procesora CPU |  W WERSJI 6.0 TB |  16 TB | Dostępne |
| S384xm może zostać rozszerzony do | Oprogramowanie SAP HANA na S384xxm platformy Azure<br /> — 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora CPU |  12.0 TB |  28 TB | Dostępne |
| S576m może zostać rozszerzony do | Oprogramowanie SAP HANA na S576xm platformy Azure<br /> — 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątki procesora CPU |  WERSJACH 18.0 TB |  41 TB | Dostępne |
| S768m może zostać rozszerzony do | Oprogramowanie SAP HANA na S768xm platformy Azure<br /> — 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzeni procesora CPU i 768 wątki procesora CPU |  24.0 TB |  56 TB | Dostępne |

- Liczba rdzeni procesora CPU = suma nie hyper Threading rdzeni procesora CPU sumy liczby procesorów jednostki serwera.
- Wątki procesora CPU = Suma wątków obliczeniowe udostępniane przez hyper Threading rdzeni procesora CPU sumy liczby procesorów jednostki serwera. Większość jednostek są domyślnie skonfigurowane, aby użyć technologię hiperwątkowości.
- Na podstawie dostawcy zaleceń S768m, S768xm i S960m nie są skonfigurowane do użycia funkcji Hyper-Threading na potrzeby uruchamiania oprogramowania SAP HANA.


Konkretne konfiguracje wybrane są zależne od obciążenia, zasobów procesora CPU i pamięci żądaną. Jest możliwe w przypadku obciążeń OLTP do użycia jednostek SKU, które są zoptymalizowane na potrzeby obciążeń OLAP. 

Podstawowy dla oferty, z wyjątkiem jednostki dla projektów właściwe dla klienta, ustalanie rozmiaru sprzętu są z certyfikatem TDI i programu SAP HANA. Dwoma różnymi klasami sprzętu Podziel jednostki SKU do:

- S72, S72m, S144, S144m, platformie S192, platformie S192m i S192xm, które są określane jako "Type I klasy" jednostek SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm i S960m, które są określane jako "Typ klasy II" jednostek SKU.

Pełną sygnaturę dużych wystąpień HANA nie jest przeznaczona wyłącznie przydzielone dla jednego klienta&#39;s użycia. Fakt ten dotyczy regałów zasoby obliczeniowe i magazynowe, które są połączone za pośrednictwem sieci szkieletowej wdrożonych na platformie Azure, jak również. Duże wystąpienie oprogramowania HANA infrastruktury, takich jak Azure, wdraża różnych klientów &quot;dzierżaw&quot; , które są odizolowane od siebie nawzajem w następujących trzech poziomach:

- **Sieć**: izolacji za pośrednictwem sieci wirtualnych w ramach sygnatury dużych wystąpień HANA.
- **Magazyn**: izolacji przy użyciu maszyn wirtualnych magazynu, które mają woluminy magazynu przypisany i izolowania woluminów magazynu między dzierżawami.
- **Obliczenia**: dedykowane przypisanie jednostek serwera do pojedynczej dzierżawy. Nie trudne lub nietrwałe partycjonowanie jednostek serwera. Bez udostępniania pojedynczego serwera lub hosta jednostka między dzierżawami. 

Wdrażanie dużych wystąpień HANA jednostek między różnych dzierżawach nie są widoczne ze sobą. Jednostki dużych wystąpień HANA wdrożonych w różnych dzierżawach nie mogą komunikować się bezpośrednio ze sobą na poziomie sygnatury dużych wystąpień HANA. Tylko dużych wystąpień HANA jednostek w ramach jednej dzierżawy mogą komunikować się ze sobą na poziomie sygnatury dużych wystąpień HANA.

Dzierżawcy wdrożone w sygnaturze duże wystąpienie jest przypisany do jednej subskrypcji platformy Azure na potrzeby rozliczeń. Dla sieci może on dostępny z sieci wirtualnych z innych subskrypcji platformy Azure w ramach tej samej rejestracji na platformie Azure. W przypadku wdrożenia przy użyciu innej subskrypcji platformy Azure, w tym samym regionie platformy Azure, również możesz poprosić o rozdzielonych dzierżawy dużych wystąpień HANA.

Istnieją znaczne różnice między systemem SAP HANA na dużych wystąpień HANA lub SAP HANA, działające na maszynach wirtualnych wdrożonych na platformie Azure:

- Nie ma żadnych warstwa wirtualizacji dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia). Możesz uzyskać wydajność bazowego sprzętu bez systemu operacyjnego.
- W przeciwieństwie do platformy Azure SAP HANA na platformie Azure (duże wystąpienia) serwera jest dedykowany dla określonego klienta. Istnieje możliwość, jednostki serwera lub hosta jest trudne lub nietrwałe podzielona na partycje. W rezultacie duże wystąpienie oprogramowania HANA jest używany przypisany jako całość do dzierżawy i który do Ciebie. Ponowne uruchomienie lub zamknięcie serwera nie automatycznie prowadzić do systemu operacyjnego i oprogramowania SAP HANA wdrażane na innym serwerze. (Dla typu klasy I jednostki SKU, jedynym wyjątkiem jest, jeśli serwer napotyka problemy i ponownego wdrożenia komputera musi zostać wykonana na innym serwerze.)
- W przeciwieństwie do platformy Azure, w których typy procesorów hosta są wybrane dla najlepsze stosunek ceny do wydajności, typy procesorów, wybrany dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) są najwyższy wykonywania wierszu procesor Intel E7v3 i E7v4.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Uruchamianie wielu wystąpień oprogramowania SAP HANA w jednej jednostce dużych wystąpień HANA
Istnieje możliwość hostowania więcej niż jednego aktywnego wystąpienia platformy SAP HANA w jednostkach dużych wystąpień HANA. Zapewnienie możliwości migawki magazynu i odzyskiwania po awarii, taka konfiguracja wymaga woluminu, który został ustawiony na wystąpienie. Obecnie dużych wystąpień HANA jednostki można podzielić w następujący sposób:

- **S72, S72m, S144, platformie S192**: przyrostem wynoszącym 256 GB z 256 GB najmniejszy początkowa jednostki. Można łączyć wielokrotności np. 256 GB do 512 GB do maksymalnej pamięci jednostki.
- **S144m i platformie S192m**: przyrostem wynoszącym 256 GB i 512 GB najmniejsza jednostka. Można łączyć wielokrotności np. 512 GB i 768 GB do maksymalnej pamięci jednostki.
- **Typ klasy II**: Z przyrostem równym 512 GB do najmniejszego początkowy jednostki 2 TB. Wielokrotności np. 512 GB, 1 TB i 1,5 TB, można połączyć do maksymalnej pamięci jednostki.

Niektóre przykłady uruchamianie wielu wystąpień oprogramowania SAP HANA może wyglądać następująco.

| SKU | Rozmiar pamięci | Rozmiar magazynu | Rozmiary z wieloma bazami danych |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | wystąpienie oprogramowania HANA 1 x 768 GB<br /> lub wystąpienia 1 x 512 GB + 1 x 256 GB wystąpienia<br /> lub wystąpień 3 x 256 GB | 
| S72m | 1,5 TB | 6 TB | 3x512GB wystąpień HANA<br />lub wystąpienia 1 x 512 GB + 1 x 1 TB pojemności wystąpienia<br />lub wystąpień 6 x 256 GB<br />lub wystąpienie 1x1.5 TB | 
| S192m | 4 TB | 16 TB | 8 x 512 GB wystąpień<br />lub wystąpień 4 x 1 TB pojemności<br />lub wystąpień 4 x 512 GB + 2 x 1 TB pojemności wystąpień<br />lub wystąpień 4 x 768 GB + 2 x 512 GB wystąpień<br />lub wystąpienie 1 x 4 TB |
| S384xm | 8 TB | 22 TB | 4 x 2 TB wystąpień<br />lub wystąpień 2 x 4 TB<br />lub wystąpień 2 x 3 TB + 1 x 2 TB wystąpień<br />lub wystąpień 2x2.5 TB + 1 x 3 TB wystąpień<br />lub wystąpienia. 8 x 1 TB |


Istnieją także inne różnice. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Używanie węzłów warstw i rozszerzenie danych SAP HANA
SAP obsługuje model warstw danych SAP BW różne wersje oprogramowania SAP NetWeaver i SAP BW/4HANA. Więcej informacji na temat modelu warstw danych znajduje się w dokumencie SAP [SAP BW/4HANA i SAP BW on HANA, SAP HANA rozszerzenia węzłów](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Przy użyciu dużych wystąpień HANA można użyć konfiguracji opcja 1 węzłów rozszerzenia platformy SAP HANA, jak wyjaśniono w dokumentach blogu — często zadawane pytania i SAP. Opcja 2 konfiguracje można skonfigurować za pomocą następujących HANA dużych wystąpień jednostek SKU: S72m, platformie S192, platformie S192m, S384 i S384m. 

Kiedy wyświetlasz dokumentację korzyści może nie być widoczne od razu. Ale jeśli przyjrzymy się wytyczne dotyczące określania rozmiaru SAP, możesz zobaczyć zaletą przy użyciu opcji-1 i 2 opcji węzłów rozszerzenia platformy SAP HANA. Poniżej przedstawiono przykłady:

- Wytyczne dotyczące określania rozmiaru platformy SAP HANA zwykle wymagają double ilość ilość danych jako pamięć. Po uruchomieniu wystąpienia platformy SAP HANA z gorących danych mają tylko 50% lub mniej pamięci wypełniany danymi. W pozostałej części pamięci najlepiej jest przechowywany dla oprogramowania SAP HANA, wykonując pracę.
- Oznacza to w jednostce platformie HANA duże wystąpienie S192 o pojemności 2 TB pamięci, uruchomienie bazy danych SAP BW, wystarczy tylko 1 TB jako wolumin danych.
- Jeśli używasz dodatkowego węzła Rozszerzenia platformy SAP HANA — opcja 1, również platformie S192 HANA duże wystąpienie jednostki SKU, daje dodatkowej pojemności 2 TB dla woluminów danych. W konfiguracji opcji 2 dodatkowe 4 TB uzyskać ilości danych bez wyłączania zasilania. W porównaniu do węzła gorąca, pojemności pamięci pełną węzła "ciepłych" rozszerzenia może służyć do przechowywania danych, dla opcji-1. Double pamięci może służyć do ilości danych w konfiguracji węzła Rozszerzenia platformy SAP HANA — opcja 2.
- Na końcu 3 TB pojemności dla danych i współczynnika hot na ciepło 1:2 dla opcji-1. Masz 5 TB danych i współczynnika 1:4 z konfiguracją węzła Rozszerzenia opcja 2.

Im większa ilość danych w porównaniu do pamięci, tym większe prawdopodobieństwo to, że ciepło dane, które są monitowania o są przechowywane na magazyn dyskowy.


## <a name="operations-model-and-responsibilities"></a>Model działania i obowiązki

Usługi za pomocą platformy SAP HANA na platformie Azure (duże wystąpienia) jest powiązana z usługami IaaS platformy Azure. Uzyskasz wystąpienie oprogramowania HANA, duże wystąpienie z zainstalowanym systemem operacyjnym, który jest zoptymalizowany pod kątem oprogramowania SAP HANA. Przy użyciu maszyn wirtualnych IaaS platformy Azure, większość zadań wzmacniania ochrony systemu operacyjnego, instalowania dodatkowego oprogramowania, instalowanie oprogramowania HANA, obsługi systemu operacyjnego i platformy HANA i aktualizowanie systemu operacyjnego i platformy HANA jest odpowiedzialny za. Microsoft nie siły aktualizacje systemu operacyjnego lub aktualizacje oprogramowania HANA należy.

![Obowiązki platformy SAP HANA na platformie Azure (duże wystąpienia)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak pokazano na diagramie, SAP HANA na platformie Azure (duże wystąpienia) to wielodostępne, które oferują IaaS. W większości przypadków podział odpowiedzialności jest na granicy infrastruktury systemu operacyjnego. Firma Microsoft jest odpowiedzialna za wszystkimi aspektami usługi pod linią systemu operacyjnego. Odpowiedzialność za wszystkie aspekty usługi powyżej wiersza. System operacyjny jest odpowiedzialny za. Można nadal używać najnowszej metody w środowisku lokalnym, które mogą stosować zgodności, zabezpieczeń, zarządzanie aplikacjami, podstawy i systemu operacyjnego zarządzania. Systemy są wyświetlane, tak jakby znajdują się w Twojej sieci w odniesieniu do wszystkich.

Ta usługa jest zoptymalizowany do platformy SAP HANA, istnieje więc obszary, w których trzeba pracować z firma Microsoft ma wysyłać podstawowych możliwości infrastruktury w celu uzyskania najlepszych wyników.

Poniższa lista zawiera więcej szczegółów dotyczących każdej z warstw i Twoich obowiązkach:

**Sieć**: sieci wewnętrznych dla sygnatury duże wystąpienie uruchamiania oprogramowania SAP HANA. Odpowiedzialny za obejmuje dostęp do przechowywania danych, łączność między wystąpień (dla skalowalnego w poziomie i inne funkcje), łączność krajobrazu i połączeń z platformą Azure, w którym jest hostowana warstwa aplikacji SAP na maszynach wirtualnych. Zawiera on również połączenia z siecią WAN między centrami danych platformy Azure do replikacji celów odzyskiwania po awarii. Wszystkie sieci są partycjonowane na podstawie dzierżawy, a jakość usługi stosowane.

**Magazyn**: zwirtualizowany podzielona na partycje magazynu dla wszystkich woluminów wymagane przez serwery SAP HANA, a także migawki. 

**Serwery**: dedykowanych serwerów fizycznych do uruchamiania bazy danych HANA SAP, które zostały przypisane do dzierżawcy. Serwery typu I klasy jednostek SKU są sprzętowe wyodrębnione. Za pomocą następujących typów serwerów konfiguracji serwera są zbierane i przechowywane w profilach, mogą zostać przeniesione z jednego fizycznego sprzętu do innego sprzętu fizycznego. Takie (ręczne) przeniesienie profilu przez operacje można porównać coś do naprawianiem usług platformy Azure. Serwery jednostek SKU typu II klasy nie oferuje taką funkcję.

**SDDC**: oprogramowanie do zarządzania, który służy do zarządzania danymi centra jako jednostki zdefiniowanych przez oprogramowanie. Umożliwia firmy Microsoft do puli zasobów w celu zapewnienia skalowalności, dostępności i ze względu na wydajność.

**Systemu operacyjnego**: Wybierz (SUSE Linux lub systemie Red Hat Linux) systemu operacyjnego uruchomionego na serwerze. Obrazy systemu operacyjnego, które są dostarczane z dostarczonych przez indywidualne dostawcę systemu Linux do firmy Microsoft uruchamiania oprogramowania SAP HANA. Musi mieć subskrypcję u dostawcy systemu Linux dla określonego obrazu zoptymalizowane pod kątem oprogramowania SAP HANA. Ponosisz odpowiedzialność za zarejestrowanie obrazy z dostawcą systemu operacyjnego. 

Od momentu przekazania przez firmę Microsoft odpowiedzialność za wszelkie dalsze stosowanie poprawek systemu operacyjnego Linux. Tej poprawki, które obejmuje dodatkowych pakietów, która może być konieczne do pomyślnej instalacji oprogramowania SAP HANA i które nie zostały zawarte przez określonego dostawcę systemu Linux w ich platformy SAP HANA zoptymalizowanych obrazów systemu operacyjnego. (Aby uzyskać więcej informacji, zobacz dokumentacji instalacji oprogramowania HANA SAP i SAP Notes). 

Odpowiedzialność za stosowania poprawek systemu operacyjnego z powodu nieprawidłowego działania lub Optymalizacja systemu operacyjnego i jego sterowniki względem sprzętu określonego serwera. Możesz również są odpowiedzialne za bezpieczeństwo lub funkcjonalności stosowanie poprawek systemu operacyjnego. 

Odpowiedzialny za zawiera także monitorowania i planowania wydajności:

- Użycie zasobów Procesora.
- Zużycie pamięci.
- Ilość wolnego miejsca, operacje We/Wy i opóźnienia związane z woluminami.
- Wolumin ruchem sieciowym między dużych wystąpień HANA i warstwy aplikacji SAP.

Podstawowa infrastruktura dużych wystąpień HANA oferuje funkcję tworzenia kopii zapasowych i przywracanie woluminu systemu operacyjnego. Za pomocą tej funkcji jest również odpowiedzialny za.

**Oprogramowanie pośredniczące**: przede wszystkim wystąpienia platformy SAP HANA. Administracja, operacji i monitorowania, spoczywa wyłącznie usługi. Funkcjonalność podana służy do użycia magazynu migawek dla celów odzyskiwania kopii zapasowej i przywracanie i odzyskiwanie po awarii. Te możliwości są zapewniane przez infrastrukturę. Twoje obowiązki obejmują również projektowania stopnia dostępności i odzyskiwania po awarii dzięki takim funkcjom korzystanie z nich i monitorowania w celu określenia, czy migawek magazynu wykonany pomyślnie.

**Dane**: danych zarządzanych przez platformę SAP HANA i inne dane, takie jak kopie zapasowe udziałów plików znajdujących się na woluminach lub pliku. Twoje obowiązki obejmują monitorowania wolnego miejsca na dysku i zarządzania zawartością w woluminach. Możesz również są odpowiedzialne za monitorowanie pomyślne wykonanie kopii zapasowych woluminów dysków i migawek magazynu. Pomyślne wykonanie replikacji danych do lokacji odzyskiwania po awarii jest odpowiedzialność firmy Microsoft.

**Aplikacje:** wystąpień aplikacji SAP lub, w przypadku aplikacji innych niż SAP, warstwy aplikacji w tych aplikacjach. Twoje obowiązki obejmują wdrażania, administrowania, operacji i monitorowania tych aplikacji. Odpowiedzialność za planowanie pojemności zużycia zasobów procesora CPU, użycie pamięci, użycie usługi Azure Storage i zużycie przepustowości sieci w ramach sieci wirtualnej. Możesz również odpowiadają za planowanie pojemności dla zużycia zasobów z sieci wirtualnych do platformy SAP HANA na platformie Azure (duże wystąpienia).

**Sieci WAN**: połączenia ustanowić ze środowiska lokalnego do wdrożeń platformy Azure dla obciążeń. Wszyscy klienci z dużych wystąpień HANA usługa Azure ExpressRoute dla łączności. To połączenie nie jest częścią platformy SAP HANA na platformie Azure (duże wystąpienia) rozwiązanie. Odpowiedzialność za instalację tego połączenia.

**Archiwum**: można wybrać do archiwizacji kopii danych przy użyciu własnych metod w ramach kont magazynu. Archiwizacja wymaga zarządzania, zgodności, koszty i operacji. Odpowiedzialność za Generowanie archiwum i kopii zapasowych na platformie Azure i przechowywania ich w sposób zgodny z.

Zobacz [umowa SLA dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Zmiana rozmiaru

Ustalanie rozmiaru dla dużych wystąpień HANA nie różni się od ogólnie rzecz biorąc ustalania rozmiaru dla platformy HANA. Dla istniejących i wdrażane systemy, które chcesz przenieść z innych RDBMS do platformy HANA, SAP udostępnia szereg raportów, które działają w istniejących systemów SAP. Jeśli baza danych zostanie przeniesiony do platformy HANA, te raporty, sprawdź dane i obliczyć wymagania dotyczące pamięci dla wystąpienia platformy HANA. Aby uzyskać więcej informacji na temat sposobu uruchamiania tych raportów i uzyskać ich najnowsze poprawki lub wersji należy przeczytać następujące uwagi SAP:

- [Uwaga SAP #1793345 - rozmiaru pakietu SAP na platformie HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Uwaga 1872170 # - Suite on HANA i S/4 HANA raportu zmiany rozmiaru](https://launchpad.support.sap.com/#/notes/1872170)
- [Uwaga SAP #2121330 — często zadawane pytania: SAP BW on HANA, zmiany rozmiaru raportu](https://launchpad.support.sap.com/#/notes/2121330)
- [Uwaga #1736976 - raport ustalania rozmiaru dla programu BW on HANA, SAP](https://launchpad.support.sap.com/#/notes/1736976)
- [Uwaga 2296290 # - nowe zmiany rozmiaru raportu dla BW on HANA, SAP](https://launchpad.support.sap.com/#/notes/2296290)

Implementacje zielone pole SAP szybkiego Rozmiar symboli jest dostępna do obliczania wymagania dotyczące pamięci wdrażania oprogramowania SAP HANA — na.

Wymagania dotyczące pamięci dla platformy HANA zwiększyć wzrostem ilości danych. Należy pamiętać o bieżące zużycie pamięci, aby ułatwić przewidywanie, co będzie znajdował się w przyszłości. Oparte na wymagania dotyczące pamięci, następnie można mapować zapotrzebowanie na jeden z jednostki SKU HANA dużych wystąpień.

## <a name="requirements"></a>Wymagania

Ta lista składa się wymagania dotyczące uruchamiania oprogramowania SAP HANA na platformie Azure (wystąpienia większy).

**Microsoft Azure**

- Subskrypcja platformy Azure, które mogą być połączone z platformą SAP HANA na platformie Azure (duże wystąpienia).
- Umowę na pomoc techniczną Premium firmy Microsoft. Aby uzyskać szczegółowe informacje związane z systemem SAP na platformie Azure, zobacz [2015553 # Uwaga SAP pomocy technicznej — SAP w systemie Microsoft Azure: wymagania wstępne dotyczące obsługi](https://launchpad.support.sap.com/#/notes/2015553). Jeśli używasz jednostek dużych wystąpień HANA 384 i większej liczby procesorów CPU, również należy rozszerzyć Premier umowę pomocy technicznej, aby uwzględnić Azure Rapid Response.
- Rozpoznawanie HANA duże wystąpienie jednostki SKU należy po wykonaniu wykonywania zmiany rozmiaru, korzystając z oprogramowania SAP.

**Połączenie sieciowe**

- Usługa ExpressRoute między środowiska lokalnego do platformy Azure: połączyć lokalne centrum danych na platformie Azure, upewnij się, że kolejność połączenia o wielkości co najmniej 1 GB/s od usługodawcy internetowego. 

**System operacyjny**

- Licencje dla SUSE Linux Enterprise Server 12 dla aplikacji SAP.

   > [!NOTE] 
   > System operacyjny, dostarczone przez firmę Microsoft nie jest zarejestrowany w usłudze SUSE. Nie jest ona dołączona do wystąpienia narzędzia do zarządzania subskrypcją.

- SUSE Linux subskrypcji narzędzia do zarządzania wdrożonymi na platformie Azure na maszynie Wirtualnej. To narzędzie zapewnia funkcje platformy SAP Hana na platformie Azure (duże wystąpienia) były rejestrowane i odpowiednio aktualizowana przez SUSE. (Brak jest Brak dostępu do Internetu w centrum danych dużych wystąpień HANA). 
- Licencji Red Hat Enterprise Linux 6.7 lub 7.x dla oprogramowania SAP HANA.

   > [!NOTE]
   > System operacyjny, dostarczone przez firmę Microsoft nie jest zarejestrowana z firmą Red Hat. Nie jest ona dołączona do wystąpienia systemu Red Hat subskrypcji Menedżera.

- Red Hat subskrypcji Menedżera wdrożonych na platformie Azure na maszynie Wirtualnej. Menedżer subskrypcji Red Hat zapewnia funkcje platformy SAP Hana na platformie Azure (duże wystąpienia), aby były rejestrowane i odpowiednio aktualizowane przez firmy Red Hat. (Nie jest brak bezpośredniego dostępu do Internetu z w ramach dzierżawy wdrożone w sygnaturze dużego wystąpienia platformy Azure).
- Środowisko SAP wymaga posiadania pomocy technicznej kontraktu u swojego dostawcy systemu Linux. To wymaganie nie są usuwane przez to rozwiązanie, dużych wystąpień HANA lub faktu uruchomienia systemu Linux na platformie Azure. W przeciwieństwie do niektórych obrazów Galeria Azure dla systemu Linux, opłata za usługę jest *nie* uwzględnione w ofercie rozwiązania dużych wystąpień HANA. Jest odpowiedzialny za spełniają wymagania SAP dotyczące umowy pomocy technicznej za pomocą dystrybutora systemu Linux. 
   - W przypadku systemu SUSE Linux wyszukać wymagania umowy pomocy technicznej w [1984787 # Uwaga SAP — SUSE Linux Enterprise Server 12: uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787) i [1056161 # Uwaga SAP — SUSE priorytetowa pomoc techniczna dla aplikacji SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Dla systemu Red Hat Linux musisz mieć poziomy poprawną subskrypcję, które obejmują pomoc techniczną i aktualizowaniem, systemów operacyjnych, HANA, duże wystąpienia usługi. Red Hat zaleca Red Hat Enterprise Linux [rozwiązań SAP] (https://access.redhat.com/solutions/3082481 subskrypcji. 

Macierz obsługi różnych wersji platformy SAP HANA z użyciem różnych wersji systemu Linux, można zobaczyć [2235581 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/2235581).

Macierz zgodności systemu operacyjnego i wersji oprogramowania układowego/driver HLI, można znaleźć [uaktualnienia systemu operacyjnego dla HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Dla typu II jednostek tylko w systemie SLES 12 z dodatkiem SP2 systemu operacyjnego wersja jest obsługiwana w tym momencie. 


**Baza danych**

- Licencje i składniki instalacyjne oprogramowania dla oprogramowania SAP HANA (platforma lub enterprise edition).

**Aplikacje**

- Licencje i składniki instalacyjne oprogramowania dla wszystkich aplikacji SAP, łączące się z platformą SAP HANA i powiązane SAP umowy dotyczące pomocy technicznej.
- Licencje i składniki instalacyjne oprogramowania dla wszystkich aplikacji innych niż SAP, używane w odniesieniu do oprogramowania SAP HANA na platformie Azure (duże wystąpienia) środowiska i związane z umowami pomocy technicznej.

**Umiejętności**

- Środowisko i wiedzy na temat infrastruktury IaaS platformy Azure i jego składników.
- Doświadczenia i wiedzy na temat sposobu wdrażania obciążeń SAP na platformie Azure.
- Instalacja SAP HANA certyfikowane personel.
- SAP Architekt umiejętności, aby zaprojektować wysokiej dostępności i odzyskiwania po awarii całego oprogramowania SAP HANA.

**SAP**

- Oczekuje się, że jesteś klientem usługi SAP i masz pomocy technicznej umowy z oprogramowaniem SAP.
- Szczególnie w przypadku implementacji klasy typu II jednostek SKU platformy HANA duże wystąpienie zapoznaj się z oprogramowaniem SAP wersje oprogramowania SAP HANA i ostateczną konfiguracje sprzętu skalowanie w górę o dużych rozmiarach.


## <a name="storage"></a>Magazyn

Układ magazynu dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) jest skonfigurowany przez platformę SAP HANA w klasycznym modelu wdrażania przy użyciu SAP zalecane wytyczne. Wytyczne są udokumentowane w artykule [wymagania dotyczące magazynu oprogramowania SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) oficjalny dokument.

Duże wystąpienie typu klasy I HANA zawiera cztery razy woluminie pamięci jako woluminu magazynu. W klasie typu II jednostek dużych wystąpień HANA magazynu nie jest cztery razy więcej. Jednostki są dostarczane z woluminu, który jest przeznaczony do przechowywania kopii zapasowych dziennika transakcji platformy HANA. Aby uzyskać więcej informacji, zobacz [zainstalować i skonfigurować oprogramowanie SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zobacz poniższą tabelę pod względem przydziału magazynu. W tabeli wymieniono nierównej pojemności dla różnych woluminach, które są dostarczane z różnych jednostek dużych wystąpień HANA.

| Duże wystąpienie oprogramowania HANA jednostki SKU | dane oprogramowania Hana / | Hana/log | Hana/udostępnione | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S192 | 4,608 GB | 1024 GB | 1,536 GB | 1024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4096 GB | 2048 GB | 4096 GB |
| S768m | UŻYWANE 28 000 GB | 3,100 GB | 2050 GB | 3,100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2050 GB | 4,100 GB |


Rzeczywistej objętości wdrożonej może się różnić w zależności od wdrożenia i narzędzie, które służy do pokazywania rozmiary woluminów.

Jeśli dzielisz jednostki SKU HANA duże wystąpienie może wyglądać kilka przykładów dzielenia możliwych elementów:

| Partycja pamięci w GB | dane oprogramowania Hana / | Hana/log | Hana/udostępnione | Hana / / kopia zapasowa dziennika |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


Te rozmiary są liczbami nierównej woluminów, które może różnić się nieco w zależności od wdrażania i narzędzia służące do wzięcia pod woluminów. Istnieją także inne rozmiary partycji, takie jak 2,5 TB. Te rozmiary pamięci masowej są obliczane przy użyciu formuły podobny do poprzedniego partycje. Termin "partycje" nie oznacza, że system operacyjny, pamięć lub zasobów procesora CPU są w żaden sposób na partycje. Oznacza to, partycji magazynu dla różnych wystąpieniach HANA, które można wdrożyć w jednej jednostce dużych wystąpień HANA. 

Możesz potrzebować więcej pamięci masowej. Magazyn można dodać przy zakupie dodatkowego magazynu w jednostkach 1 TB. Ten dodatkowy magazyn może być dodany jako dodatkowe woluminu. Również może służyć do rozszerzenia co najmniej jedną z istniejących woluminów. Nie można zmniejszyć rozmiaru woluminu pierwotnie wdrożone, a przede wszystkim opisane w poprzednich tabelach. Również nie można zmienić nazwy woluminów lub zainstalować nazwy. Woluminy magazynu opisany wcześniej są dołączone do jednostek dużych wystąpień HANA jako woluminy NFS4.

Migawek magazynu służy do celów odzyskiwania kopii zapasowej i przywracanie i odzyskiwanie po awarii. Aby uzyskać więcej informacji, zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zapoznaj się [HLI obsługiwane scenariusze](hana-supported-scenario.md) szczegóły układu magazynu dla danego scenariusza.

### <a name="encryption-of-data-at-rest"></a>Szyfrowanie nieużywanych danych
Magazyn używany dla dużych wystąpień HANA umożliwia przezroczyste szyfrowanie danych, ponieważ są przechowywane na dyskach. Po wdrożeniu jednostki dużych wystąpień HANA można włączyć ten rodzaj szyfrowania. Można także zmienić zaszyfrowanych woluminów po wdrożeniu. Przejście z protokołem szyfrowania zaszyfrowanych woluminach jest niewidoczna i bez przerywania. 

O typie I klasy jednostek SKU, wolumin rozruchowy jednostki LUN są przechowywane na został zaszyfrowany. Dla klasy typu II jednostki SKU z duże wystąpienie HANA należy zaszyfrować rozruchu jednostki LUN z metodami systemu operacyjnego. Aby uzyskać więcej informacji skontaktuj się z zespołu zarządzania usługami firmy Microsoft.


## <a name="networking"></a>Networking

Architektura usług sieci platformy Azure jest kluczowym elementem pomyślne wdrożenie aplikacji SAP na dużych wystąpień HANA. Zazwyczaj środowiska SAP HANA na wdrożeń platformy Azure (duże wystąpienia) mają większe środowisko SAP w kilku różnych rozwiązaniach SAP o różnych rozmiarach, między bazami danych, zużycia zasobów procesora CPU i użycie pamięci przez program. Istnieje prawdopodobieństwo, że nie wszystkie te systemy SAP są oparte na platformie SAP HANA. Środowiska SAP jest prawdopodobnie hybrydowego, który używa:

- Wdrażane oprogramowanie SAP systemów lokalnych. Ze względu na ich rozmiary te systemy aktualnie nie mogą być hostowane na platformie Azure. Przykładem jest system SAP ERP, który działa w programie SQL Server (co baza danych) i wymaga większej ilości zasobów procesora CPU lub pamięci niż są dostępne maszyny wirtualne w środowisku produkcyjnym.
- Wdrażane na podstawie platformy SAP HANA SAP systemów lokalnych.
- Wdrożonych systemów SAP na maszynach wirtualnych. Te systemy mogą być tworzenia/testowania piaskownicy, lub produkcji wystąpienia dla każdej aplikacji opartych na oprogramowanie SAP NetWeaver, które pomyślnie wdrożyć na platformie Azure (na maszynach wirtualnych), zgodnie z potrzebami zasobów zużycia i pamięci. Te systemy również może bazować na baz danych, takich jak SQL Server. Aby uzyskać więcej informacji, zobacz [SAP pomocy technicznej Uwaga #1928533 — aplikacje środowiska SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533/E). A te systemy mogą być oparte na baz danych, takich jak SAP HANA. Aby uzyskać więcej informacji, zobacz [platform IaaS z certyfikatem SAP HANA](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Wdrożone serwery aplikacji SAP na platformie Azure (na maszynach wirtualnych), korzystających z platformy SAP HANA na platformie Azure (duże wystąpienia) w sygnatury dużego wystąpienia platformy Azure.

Typowe jest hybrydowego środowiska SAP za pomocą co najmniej czterech różnych scenariuszy wdrażania. Istnieje również wiele przypadków klienta pełną SAP zapewniały realizację niezbędnych zadań, które działają na platformie Azure. W miarę bardziej wydajne maszyny wirtualne zwiększa liczby klientów, które przenoszą swoje rozwiązania SAP na platformie Azure.

Azure networking w kontekście systemów SAP wdrożonych na platformie Azure nie jest skomplikowane. Jest on oparty na następujące zasady:

- Sieci wirtualne platformy Azure musi być połączony z obwodem usługi ExpressRoute, który nawiązuje połączenie z siecią lokalną.
- Obwód usługi ExpressRoute, który nawiązuje połączenie środowiska lokalnego na platformę Azure zazwyczaj mają przepustowości 1 GB/s lub wyższej. Tej minimalnej przepustowości umożliwia odpowiedniej przepustowości do transferu danych między systemami lokalnymi i systemów, które są uruchamiane na maszynach wirtualnych. Umożliwia także odpowiedniej przepustowości dla połączenia z systemami Azure od użytkowników w środowisku lokalnym.
- Wszystkie systemy SAP na platformie Azure należy ustawić w sieciach wirtualnych, do komunikowania się ze sobą.
- Usługi Active Directory i DNS hostowanych lokalnie zostały rozszerzone na platformę Azure za pośrednictwem usługi ExpressRoute ze środowiska lokalnego.


> [!NOTE] 
> Z rozliczeń punktu widzenia tylko jedną subskrypcję platformy Azure można połączyć tylko jednej dzierżawy w sygnaturze dużych wystąpień, w określonym regionie platformy Azure. Z drugiej strony pojedynczej dzierżawy sygnatury duże wystąpienie można połączyć tylko jedną subskrypcję platformy Azure. To wymaganie jest spójna z innymi obiektami płatnych na platformie Azure.

Jeśli oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) jest wdrażana w wielu różnych regionach platformy Azure, oddzielna dzierżawa jest wdrażany w sygnaturze dużego wystąpienia. Można uruchomić zarówno w ramach tej samej subskrypcji platformy Azure, tak długo, jak te wystąpienia są częścią tej samej środowiskiem SAP. 

> [!IMPORTANT] 
> Tylko wdrożenie usługi Azure Resource Manager jest obsługiwana przez oprogramowanie SAP HANA na platformie Azure (duże wystąpienia).

 

### <a name="additional-virtual-network-information"></a>Informacje dodatkowe sieci wirtualnej

Aby połączyć sieć wirtualną z usługi ExpressRoute, należy utworzyć bramę platformy Azure. Aby uzyskać więcej informacji, zobacz [o bramach sieci wirtualnej dla usługi ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

Brama platformy Azure może służyć przy użyciu usługi ExpressRoute do infrastruktury spoza platformy Azure lub do sygnatury dużego wystąpienia platformy Azure. Brama platformy Azure można również nawiązać połączenia między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [skonfigurować połączenie do sieci dla usługi Resource Manager przy użyciu programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Brama platformy Azure do maksymalnie czterech różnych połączeń usługi ExpressRoute można połączyć, tak długo, jak te połączenia pochodzą z różnych routery graniczne enterprise firmy Microsoft. Aby uzyskać więcej informacji, zobacz [infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Przepływność, brama platformy Azure zapewnia różni się dla obu przypadków użycia. Aby uzyskać więcej informacji, zobacz [VPN Gateway — informacje](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Maksymalna przepływność, którą można uzyskać za pośrednictwem bramy sieci wirtualnej jest 10 GB/s za pośrednictwem połączenia usługi ExpressRoute. Kopiowanie plików między maszyną Wirtualną, która znajduje się w sieci wirtualnej i systemem lokalnego (jako strumień o pojedynczej kopii) nie osiągać pełną przepływność różne jednostki SKU bramy. Korzystanie z pełną przepustowość bramy sieci wirtualnej, należy użyć wielu strumieni. Lub należy skopiować różne pliki w strumieniach równoległe pojedynczego pliku.


### <a name="networking-architecture-for-hana-large-instance"></a>Architektura sieci dla dużych wystąpień HANA
Architektura sieci dużych wystąpień HANA można podzielić na cztery elementy:

- Sieć lokalną i połączenie usługi ExpressRoute z platformą Azure. Ta część jest domeną klienta i jest połączony z platformą Azure za pośrednictwem usługi ExpressRoute. Zobacz prawym dolnym rogu na poniższej ilustracji.
- Usługi sieci platformy Azure, jak wspomniano, przy użyciu sieci wirtualnych, których ponownie bram. Ta część jest obszar, w którym trzeba znaleźć odpowiednie projekty dla wymagań aplikacji, zabezpieczeń i zgodności z wymaganiami. Czy są używane duże wystąpienie oprogramowania HANA jest inny punkt do należy wziąć pod uwagę pod względem liczby sieci wirtualnych i jednostki SKU bramy platformy Azure do wyboru. Zobacz prawym górnym rogu na rysunku.
- Łączność z dużych wystąpień HANA za pomocą usługi ExpressRoute technologii na platformie Azure. Ta część jest wdrażane i obsługiwane przez firmę Microsoft. To wszystko, co należy zrobić, podaj niektóre zakresy adresów IP po wdrożeniu Twoich zasobów w dużych wystąpień HANA połączenia obwodu usługi ExpressRoute z sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Sieć w dużych wystąpień HANA, która jest głównie przezroczyste dla Ciebie.

![Sieć wirtualna połączona z platformą SAP HANA na platformie Azure (duże wystąpienia) i w środowisku lokalnym](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Wymóg, aby połączyć z zasobów lokalnych za pośrednictwem usługi ExpressRoute na platformie Azure nie powoduje zmiany, ponieważ używasz dużych wystąpień HANA. Wymóg posiadania jednego lub wielu sieci wirtualnych, które uruchamiania maszyn wirtualnych, które hostują warstwy aplikacji, który nawiązuje połączenie z wystąpień HANA hostowanych w jednostkach dużych wystąpień HANA, również nie ulegnie zmianie. 

Dostępne są następujące różnice, by wdrożenia SAP na platformie Azure:

- Jednostki dużych wystąpień HANA dzierżawy klienta są połączone za pośrednictwem innego obwodu usługi ExpressRoute w sieciach wirtualnych. Do oddzielania warunkach obciążenia, lokalnej sieci wirtualnej usługi ExpressRoute łączy i łącza między sieciami wirtualnymi i dużych wystąpień HANA nie udostępniaj tego samego routery.
- Przechowujących nieregularnie między warstwą aplikacji SAP i HANA, duże wystąpienie jest innego rodzaju, z wielu małych żądań i bursts, takie jak transfer danych (zestawy wyników) z platformy SAP HANA w warstwie aplikacji.
- Architektura aplikacji SAP jest bardziej wrażliwy na opóźnienia sieci niż typowych scenariuszy, w którym dane są wymieniane między lokalną i platformą Azure.
- Brama sieci wirtualnej ma co najmniej dwóch połączeń usługi ExpressRoute. Zarówno połączenia współużytkują maksymalna przepustowość dla danych przychodzących bramy sieci wirtualnej.

Opóźnienie sieci reprezentatywne między maszynami wirtualnymi i dużych wystąpień HANA jednostki mogą być większe niż typowy opóźnienie Rundy sieci maszyny Wirtualnej do maszyny Wirtualnej. Zależy od regionu platformy Azure, wartości mierzone może przekroczyć opóźnienie obustronne ms 0,7 sklasyfikowane jako poniżej średniej w [1100926 # Uwaga SAP — często zadawane pytania: wydajność sieci](https://launchpad.support.sap.com/#/notes/1100926/E). Niemniej jednak klienci wdrażać aplikacje SAP oparte na oprogramowanie SAP HANA produkcyjne pomyślnie na duże wystąpienie SAP HANA. Klienci, którzy doskonałe ulepszenia raportów, uruchamiając swoje aplikacje SAP na platformie SAP HANA przy użyciu dużych wystąpień HANA jednostki. Upewnij się, że należy dokładnie przetestować procesy biznesowe w dużych wystąpień HANA platformy Azure.
 
Aby zapewnić deterministyczne opóźnienie między maszynami wirtualnymi i dużych wystąpień HANA, wybór bramy sieci wirtualnej jednostka SKU jest niezbędne. W przeciwieństwie do wzorców ruchu między magazynami lokalnymi i maszynami wirtualnymi wzorzec ruchu między maszynami wirtualnymi i dużych wystąpień HANA można tworzyć małe, ale wysokie wzrosty żądania i dane woluminów, które mają być przekazywane. Aby obsłużyć takie wzrosty dobrze, zdecydowanie zalecamy użycie jednostki SKU bramy UltraPerformance. W klasie typu II jednostek SKU platformy HANA duże wystąpienie użycie jednostki SKU bramy UltraPerformance jako brama sieci wirtualnej jest obowiązkowe.

> [!IMPORTANT] 
> Biorąc pod uwagę ogólną ruch sieciowy między aplikacji SAP i warstwy bazy danych, tylko HighPerformance lub jednostki SKU bramy UltraPerformance dla sieci wirtualnych są obsługiwane w przypadku nawiązywania połączenia z platformą SAP HANA na platformie Azure (duże wystąpienia). Brama sieci wirtualnej platformy HANA dużych wystąpień jednostek SKU typu II, obsługiwane są tylko jednostki SKU bramy UltraPerformance.



### <a name="single-sap-system"></a>Pojedynczy system SAP

Infrastruktury lokalnej z wcześniejszymi jest połączony za pośrednictwem usługi ExpressRoute na platformie Azure. Obwód usługi ExpressRoute łączy do router brzegowy przedsiębiorstwa. Aby uzyskać więcej informacji, zobacz [ExpressRoute — opis techniczny](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po nawiązaniu trasy łączy do sieci szkieletowej platformy Azure, a wszystkie regiony platformy Azure są dostępne.

> [!NOTE] 
> Aby uruchomić krajobrazów SAP na platformie Azure, nawiązać najbliżej regionu platformy Azure w orientacji poziomej SAP router brzegowy przedsiębiorstwa. Azure sygnatury dużych wystąpień są połączone za pośrednictwem urządzenia routera brzegowe dedykowanej, enterprise, aby zminimalizować opóźnienie sieci między maszynami wirtualnymi w modelu IaaS platformy Azure i duże wystąpienie sygnatury.

Brama sieci wirtualnej dla maszyn wirtualnych, w których są hostowane wystąpienia aplikacji SAP jest połączona z obwodem usługi ExpressRoute. Tej samej sieci wirtualnej jest połączony z router brzegowy przedsiębiorstwa oddzielnych dedykowanych do nawiązywania połączenia z sygnaturami dużego wystąpienia.

Ten system jest prosty przykład pojedynczego systemu SAP. Warstwa aplikacji SAP jest hostowany na platformie Azure. Uruchamia bazy danych SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia). Zakłada się, że przepustowość bramy sieci wirtualnej 2 GB lub 10 GB/s przepływności nie stanowią "wąskie gardło".

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Wiele systemów SAP lub dużych systemów SAP

Wiele systemów SAP lub dużych systemów SAP są wdrażane do łączenia z platformą SAP HANA na platformie Azure (duże wystąpienia), mogą stać się wąskim gardłem przez przepływność bramy sieci wirtualnej. W takim przypadku należy podzielić warstw aplikacji na wielu sieci wirtualnych. Można również utworzyć specjalne sieci wirtualnej, która łączy się z dużych wystąpień HANA w przypadkach, takich jak:

- Wykonywanie kopii zapasowych bezpośrednio w wystąpieniach oprogramowania HANA w dużych wystąpień HANA do maszyny Wirtualnej na platformie Azure, która hostuje udziały NFS.
- Kopiowanie dużych kopii zapasowych lub innych plików z dużych wystąpień HANA jednostek do miejsca na dysku zarządzanego na platformie Azure.

Użyj oddzielnych sieci wirtualnej do hosta maszyny wirtualne, które zarządzania magazynem. To rozwiązanie pozwala uniknąć dużych plików lub przesyłanie danych z dużych wystąpień HANA na platformie Azure w bramie sieci wirtualnej, pełniącą maszyn wirtualnych działających w warstwie aplikacji SAP. 

Aby uzyskać bardziej Skalowalna architektura sieci:

- Korzystać z wielu sieci wirtualnych w jednym, większy warstwy aplikacji SAP.
- Wdrożyć oddzielny jednej sieci wirtualnej dla każdego systemu SAP wdrożone, w porównaniu do łączenia tych systemów SAP w oddzielnych podsieciach w ramach tej samej sieci wirtualnej.

 Bardziej Skalowalna architektura sieci dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia):

![Wdrażanie warstwy aplikacji SAP za pośrednictwem wielu sieci wirtualnych](./media/hana-overview-architecture/image4-networking-architecture.png)

Na rysunku przedstawiono warstwy aplikacji SAP, czyli składników, wdrażanych za pośrednictwem wielu sieci wirtualnych. Ta konfiguracja wprowadzona nieuniknione opóźnienie, który wystąpił podczas komunikacji między aplikacjami, hostowane w tych sieciach wirtualnych. Domyślnie ruch sieciowy między maszynami wirtualnymi w różnych sieciach wirtualnych Rozsyłanie za pomocą routerami granicznymi enterprise w tej konfiguracji. Sposób, aby optymalizować i zmniejszyć opóźnienia w komunikacji między dwiema sieciami wirtualnymi jest komunikacji równorzędnej sieci wirtualnych w tym samym regionie. Ta metoda działa, nawet jeśli te sieci wirtualne należą do różnych subskrypcji. Za pomocą komunikacji równorzędnej sieci wirtualnej, komunikacja między maszynami wirtualnymi w dwóch różnych sieci wirtualnych można użyć sieci szkieletowej sieci platformy Azure może komunikować się bezpośrednio ze sobą. Opóźnienie pokazuje tak, jakby maszyny wirtualne znajdują się w tej samej sieci wirtualnej. Ruch, odnoszący się do zakresów adresów IP, które są połączone za pośrednictwem bramy sieci wirtualnej platformy Azure odbywa się za pośrednictwem bramy sieci wirtualnej poszczególnych sieci wirtualnej. 

Aby uzyskać więcej informacji na temat komunikacji równorzędnej sieci wirtualnej, zobacz [komunikacja równorzędna sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Routing na platformie Azure

Trzy routingu zagadnienia dotyczące sieci są ważne w przypadku oprogramowania SAP HANA na platformie Azure (duże wystąpienia):

* Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) są dostępne wyłącznie za pośrednictwem maszyny wirtualne i dedykowane połączenie usługi ExpressRoute nie są bezpośrednio ze środowiska lokalnego. Bezpośredni dostęp ze środowiska lokalnego do jednostek duże wystąpienie oprogramowania HANA w dostarczonym przez firmę Microsoft, nie jest możliwe natychmiast. Przechodnie ograniczenia routingu są spowodowane przez bieżący architektury sieci platformy Azure używane do duże wystąpienie SAP HANA. Niektórzy klienci administracji i wszelkie aplikacje, które muszą bezpośredni dostęp, takich jak SAP rozwiązania Manager uruchomiony w środowisku lokalnym, nie można nawiązać połączenia bazy danych SAP HANA.

* W przypadku dużych wystąpień HANA jednostki wdrożona w dwóch różnych regionach platformy Azure w celu odzyskiwania po awarii, mają zastosowanie te same ograniczenia routingu przejściowy. Innymi słowy adresy IP jednostki duże wystąpienie oprogramowania HANA w jednym regionie (na przykład zachodnie stany USA) nie są kierowane do jednostki dużych wystąpień HANA, wdrożyć w innym regionie (na przykład wschodnie stany USA). To ograniczenie jest niezależny od użycia sieci platformy Azure komunikację równorzędną między regionami lub wielu łączenie obwody usługi ExpressRoute jednostki dużych wystąpień HANA nawiązać połączenie z sieciami wirtualnymi. Aby uzyskać graficzną reprezentację zobacz rysunek w sekcji "Dużych wystąpień HANA użycia jednostek w wielu regionach." To ograniczenie, którego dołączono wdrożonej architektury, zabrania natychmiastowe korzystanie z replikacji systemu HANA jako funkcja odzyskiwania po awarii.

* Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) jednostki ma przypisanego adresu IP z zakresu adresów puli adresów IP serwera, przesłane. Aby uzyskać więcej informacji, zobacz [infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ten adres IP jest dostępny za pośrednictwem subskrypcji platformy Azure i usługi ExpressRoute, który nawiązuje połączenie sieci wirtualnych do platformy HANA na platformie Azure (duże wystąpienia). Przypisany adres IP z zakresu adresów puli adresów IP serwera jest przypisane bezpośrednio do jednostki sprzętu. Ma ona *nie* przypisane przy użyciu translatora adresów Sieciowych, jak w przypadku pierwszego wdrożenia tego rozwiązania. 

> [!NOTE] 
> Aby wyeliminować ograniczeń w routingu przejściowy, jak wyjaśniono w elementy: pierwsze dwie listy, należy użyć dodatkowych składników routingu. Składniki, które mogą służyć do pokonania ograniczenia może być:

> * Zwrotnego serwera proxy do kierowania danych do i z. Na przykład F5 BIG-IP, serwer NGINX z usługą Traffic Manager wdrożonych na platformie Azure jako rozwiązanie zapory/ruch wirtualnej routingu.
> * Za pomocą [reguły IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) na maszynie wirtualnej systemu Linux, aby umożliwić routing między lokalizacjami lokalnymi i dużych wystąpień HANA jednostki lub między jednostkami duże wystąpienie oprogramowania HANA w różnych regionach.

> Należy pamiętać, że wdrożenia i obsługę niestandardowego rozwiązania innych firm urządzeń sieciowych lub IPTables nie jest obsługiwane przez firmę Microsoft. Obsługa musi być podana przez dostawcę składnik używany lub integrator. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Łączność z Internetem z dużych wystąpień HANA
Duże wystąpienie oprogramowania HANA jest *nie* mieć bezpośrednie połączenie z Internetem. Na przykład to ograniczenie może ograniczyć możliwość rejestrowania obrazu systemu operacyjnego bezpośrednio z dostawcą systemu operacyjnego. Może być konieczne z lokalnego serwera SUSE Linux Enterprise Server subskrypcji zarządzania narzędzia lub Red Hat Enterprise Linux subskrypcji Menedżera.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Szyfrowanie danych między maszynami wirtualnymi i dużych wystąpień HANA
Dane przesyłane między dużych wystąpień HANA i maszyny wirtualne nie są szyfrowane. Jednak służyć wyłącznie do programu exchange między po stronie platformy HANA DBMS i aplikacje oparte na JDBC/ODBC, można włączyć szyfrowanie ruchu. Aby uzyskać więcej informacji, zobacz [tej dokumentacji SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Użyj jednostki duże wystąpienie oprogramowania HANA w wielu regionach

Konieczne może być powodów wdrażania oprogramowania SAP HANA na platformie Azure (duże wystąpienia) w wielu regionach platformy Azure innych niż do odzyskiwania po awarii. Chcecie dostępu do dużych wystąpień HANA w każdej z maszyn wirtualnych wdrożonych w różnych sieciach wirtualnych w regionach. Adresy IP przypisane do różnych jednostek dużych wystąpień HANA nie są przenoszone poza sieci wirtualne, które są podłączone bezpośrednio za pośrednictwem ich bramy do wystąpień. W rezultacie to niewielkie zmiany wprowadzane do projektu sieci wirtualnej. Brama sieci wirtualnej może obsługiwać czterech różnych obwodów usługi ExpressRoute z routerami granicznymi różnych enterprise. Każdej sieci wirtualnej podłączonej do jednej z sygnaturami duże wystąpienie może być połączona z sygnaturą duże wystąpienie w innym regionie platformy Azure.

![Sieć wirtualna połączona sygnatury dużego wystąpienia platformy Azure w różnych regionach platformy Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Na rysunku przedstawiono sposób połączenia różnych sieciach wirtualnych w obu regionach do dwóch różnych obwodów usługi ExpressRoute, które są używane do łączenia z platformą SAP HANA na platformie Azure (duże wystąpienia), w obu regionach platformy Azure. Nowo wprowadzonych połączenia są prostokątne czerwone linie. W przypadku tych połączeń z sieciami wirtualnymi maszyn wirtualnych uruchomionych w jednym z tych sieci wirtualnych są dostępne różne jednostki dużych wystąpień HANA wdrożona w dwóch regionach. Jak pokazano na rysunku, zakłada się, że masz dwa połączenia usługi ExpressRoute ze środowiska lokalnego do dwóch regionach platformy Azure. Taki układ jest zalecane w przypadku przyczyny odzyskiwania po awarii.

> [!IMPORTANT] 
> Jeśli używasz wielu obwodów usługi ExpressRoute, ustawienia lokalnej preferencji BGP i dołączania ścieżki AS powinny służyć do zapewnienia prawidłowego przekierowania ruchu.


