---
title: Omówienie i architektura SAP HANA na platformie Azure (wystąpienia duże) | Dokumentacja firmy Microsoft
description: Omówienie architektury wdrażanie SAP HANA na platformie Azure (wystąpienia duże).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc446dfeee86fa1183892cba8068709f8fd277d5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Architektura na platformie Azure i SAP HANA (duże wystąpień) — omówienie

## <a name="what-is-sap-hana-on-azure-large-instances"></a>Co to jest SAP HANA na platformie Azure (wystąpienia duże)?

SAP HANA na platformie Azure (wystąpienia duże) to rozwiązanie unikatowa na platformie Azure. Oprócz zapewnienia maszyn wirtualnych do wdrażania i uruchamiania SAP HANA, system Azure oferuje możliwości do uruchamiania i wdrożenia SAP HANA na serwerach bez systemu operacyjnego, które są przeznaczone dla użytkownika. SAP HANA dla rozwiązania Azure (wystąpienia duże) oparty na sprzętu bez systemu operacyjnego nieudostępnione/serwera hosta, który jest przypisane do Ciebie. Sprzęt serwera jest osadzony w większych sygnatury, które zawiera compute/serwer, sieci i infrastruktury magazynu. Jako połączenie jest certyfikowane HANA dostosowane danych integracji center (TDI). SAP HANA na platformie Azure (wystąpienia duże) oferuje jednostki SKU inny serwer lub rozmiary. Jednostki można mieć 72 procesorów i 768 GB pamięci i do góry do jednostki, które mają 960 procesorów i 20 TB pamięci.

Izolacji klienta w ramach infrastruktury sygnatury jest wykonywane w dzierżaw, która wygląda następująco:

- **Sieć**: izolacji klientów w ramach infrastruktury stosu za pośrednictwem sieci wirtualnych dla poszczególnych dzierżawców odbiorcy przypisany. Dzierżawa jest przypisany do jednego odbiorcy. Klient może mieć wielu dzierżawców. Izolacja sieci dzierżawców uniemożliwia komunikację sieciową między dzierżaw w poziomie sygnatury infrastruktury, nawet wtedy, gdy dzierżawcy należą do tego samego klienta.
- **Składniki magazynu**: izolacji za pomocą magazynu maszyn wirtualnych, które mają do nich przypisane woluminów magazynu. Woluminy magazynu można przypisać do jednego magazynu tylko maszyny wirtualnej. Wyłącznie do jednego pojedynczej dzierżawy w stosie certyfikowanych infrastruktury SAP HANA TDI przypisano magazynu maszyny wirtualnej. W związku z tym woluminów magazynu przypisana do maszyny wirtualnej magazynu są dostępne w jednym określonym i pokrewnych dzierżawy tylko. Nie są one widoczne od różnych dzierżawców wdrożone.
- **Serwera lub hosta**: jednostka serwera lub host nie jest udostępniana między klientów lub dzierżawców. Serwera lub hoście z klientem jest jednostką atomic obliczeniowe bez systemu operacyjnego, przypisane do jednej pojedynczej dzierżawy. *Nie* są używane partycjonowanie sprzętowe lub nietrwałego partycjonowania może być skutkiem współużytkowania hosta lub serwer z innego klienta. Do takiego serwera są zainstalowane woluminy magazynu, przypisanych do maszyny wirtualnej magazynu określonym dzierżawcy. Dzierżawca może zawierać jeden do wielu jednostek serwera różne jednostki magazynowe przypisana wyłącznie.
- W ramach SAP HANA w sygnaturze infrastruktury platformy Azure (wystąpienia duże) wiele różnych dzierżaw są wdrożone i odizolowane od siebie wzajemnie za pośrednictwem pojęcia dzierżawy w sieci, magazynu i poziomu obliczeniowej. 


Te jednostki bez systemu operacyjnego serwera są obsługiwane tylko uruchamianie SAP HANA. SAP warstwy aplikacji lub obciążeń pośredniczącym działa na maszynach wirtualnych. Sygnatury infrastruktury systemie SAP HANA jednostki Azure (wystąpienia duże) są połączone z szkieletowymi usług sieci platformy Azure. W ten sposób podano małych opóźnieniach łączności między maszynami wirtualnymi i SAP HANA w jednostkach Azure (wystąpienia duże).

Ten dokument jest jednym z kilku dokumentów, które obejmują SAP HANA na platformie Azure (wystąpienia duże). Tym dokumencie przedstawiono podstawową architekturę, obowiązki i usług udostępnianych przez to rozwiązanie. Omówiono także ogólne możliwości rozwiązania. Dla większości innych obszarach, takich jak sieć i łączność cztery inne dokumenty, które obejmują szczegóły i informacje przechodzenia. Dokumentację SAP HANA na platformie Azure (wystąpienia duże) nie obejmuje aspektów instalacji SAP NetWeaver lub wdrożenia SAP NetWeaver na maszynach wirtualnych. SAP NetWeaver na platformie Azure zostało opisane w oddzielnych dokumentów, w tym samym kontenerze dokumentacji platformy Azure. 


Różnych dokumentów wystąpienia dużych HANA wskazówek dotyczących opisano następujące kwestie:

- [Architektura na platformie Azure i SAP HANA (duże wystąpień) — omówienie](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruktura SAP HANA (duże wystąpień) i łączność na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalowanie i konfigurowanie SAP HANA (duże wystąpień) w systemie Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (duże wystąpień) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Rozwiązywanie problemów z SAP HANA (duże wystąpień) i monitorowania na platformie Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Wysoka dostępność — Konfiguracja w systemie SUSE przy użyciu STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Kopia zapasowa systemu operacyjnego i przywracania dla jednostki SKU II typu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definicje

Kilka wspólnych definicji są powszechnie używane w zakresie architektury i technicznej Deployment Guide. Zanotuj następujące pojęcia i ich znaczenie:

- **IaaS**: infrastruktura jako usługa.
- **PaaS**: platforma jako usługa.
- **SaaS**: oprogramowanie jako usługa.
- **Składnik programu SAP**: poszczególnych aplikacji SAP, takie jak ERP centralnej składnika (ECC), Business Warehouse (BW), Menedżer rozwiązania lub Enterprise Portal (EP). Składniki programu SAP może bazować na tradycyjnych technologii ABAP lub Java lub aplikacji z systemem innym niż NetWeaver na podstawie takich jak obiektów biznesowych.
- **Środowisko SAP**: jeden lub więcej składników programu SAP logicznie pogrupowane do wykonywania funkcji biznesowych, takich jak projektowanie, jakości, szkolenia, odzyskiwania po awarii lub produkcji.
- **Poziomo SAP**: odwołuje się do całego zasoby SAP w orientacji poziomej Twojej IT. Poziomo SAP obejmuje wszystkie produkcyjnych i środowiskach nieprodukcyjnych.
- **Systemu SAP**: kombinacja systemu DBMS i warstwy aplikacji na przykład programistycznej SAP ERP, system testowy programu SAP BW i SAP CRM systemu produkcji. Wdrożeń platformy Azure nie obsługuje dzielenia te dwie warstwy między lokalną i platformą Azure. Systemu SAP jest wdrożony lokalnymi lub jego wdrożona na platformie Azure. Można wdrożyć różnych systemów pozioma SAP do platformy Azure lub lokalnie. Można na przykład wdrożyć programowanie SAP CRM i systemy testowe na platformie Azure, podczas wdrażania SAP CRM produkcji systemu lokalnego. Dla SAP HANA na platformie Azure (wystąpienia duże) jest on przeznaczony hosta z warstwy aplikacji SAP systemów SAP na maszynach wirtualnych i powiązane wystąpieniem SAP HANA jednostkę SAP HANA w sygnaturze Azure (wystąpienia duże).
- **Duże sygnatura wystąpienia**: stos infrastruktury sprzęt jest certyfikowany SAP HANA TDI i dedykowane do uruchomienia wystąpień SAP HANA w obrębie platformy Azure.
- **SAP HANA na platformie Azure (duże wystąpienia):** oficjalną nazwą języka dla oferty na platformie Azure uruchomić HANA wystąpień w certyfikowane SAP HANA TDI sprzętu, które zostało wdrożone w dużych wystąpienia sygnatury w różnych regionach platformy Azure. Powiązane termin *wystąpienia dużych HANA* jest skrót *SAP HANA na platformie Azure (wystąpienia duże)* i jest powszechnie używany w tym przewodniku wdrożenia technicznego.
- **Między różnymi lokalizacjami**: opisano scenariusz, gdy maszyny wirtualne są wdrażane z subskrypcją platformy Azure, lokacja lokacja, obejmujący wiele lokacji lub usługa Azure ExpressRoute łączność między lokalnymi centrami danych i usługi Azure. Dokumentacja wspólnych Azure rodzaju wdrożenia również są opisane jako scenariusze między lokalizacjami. Przyczyna połączenia jest rozszerzenie domenami lokalnymi, lokalnej usługi Azure Active Directory/OpenLDAP i DNS lokalnej na platformie Azure. Pozioma lokalnej jest rozszerzony do platformy Azure zasobów subskrypcji platformy Azure. Dla tego rozszerzenia maszyn wirtualnych może być częścią domeny lokalnej. 

   Użytkownicy domeny w domenie lokalnej można dostępu do serwerów i uruchamiania usług na tych maszynach wirtualnych (takie jak usługi systemu DBMS). Komunikację i rozpoznawania nazw między maszynami wirtualnymi wdrożona lokalnie i wdrożone Azure maszyn wirtualnych jest możliwe. Ten scenariusz jest typowy sposób, w której wdrożono zasobów SAP. Aby uzyskać więcej informacji, zobacz [planowania i projektowania dla bramy sieci VPN Azure](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i [tworzenie sieci wirtualnej za pomocą połączenia lokacja lokacja za pomocą portalu Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Dzierżawy**: klient wdrożone w sygnatura wystąpienia dużych HANA pobiera izolowane do *dzierżawy.* Dzierżawa jest izolowana w sieci, magazynu i warstwy obliczeniowej od pozostałych dzierżawców. Jednostki magazynu i zasobów obliczeniowych, przypisane do różnych dzierżaw nie widać siebie lub komunikują się ze sobą na poziomie wystąpienia dużych HANA sygnatury. Klient może wybrać wdrożenia w różnych dzierżawców. Nawet wówczas nie występuje komunikacja między dzierżawcami na poziomie wystąpienia dużych HANA sygnatury.
- **Jednostka SKU kategorii**: dla HANA dużych wystąpienia, dostępnych w następujących dwóch kategorii jednostki SKU:
    - **Typ klasy I**: S72, S72m S144, S144m, S192 i S192m
    - **Typ klasy II**: S384, S384m S384xm, S576m, S768m i S960m


Szereg dodatkowe zasoby są dostępne na temat wdrażania SAP obciążenia w chmurze. Jeśli planujesz wykonanie wdrożenia SAP HANA na platformie Azure, należy być doświadczenia w pracy z i korzystają z zasad IaaS platformy Azure i wdrożenia SAP obciążeń na platformie Azure IaaS. Przed kontynuowaniem, zobacz [rozwiązań Użyj SAP na maszynach wirtualnych Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby uzyskać więcej informacji. 

## <a name="certification"></a>Certyfikacja

Oprócz certyfikacji NetWeaver SAP wymaga specjalnych certyfikacji dla SAP HANA do obsługi SAP HANA na niektórych infrastruktury, takich jak Azure IaaS.

Podstawowe SAP Uwaga na NetWeaver i certyfikacji SAP HANA stopnia jest [1928533 # Uwaga SAP — aplikacje SAP na platformie Azure: obsługiwane produktów i typy maszyny Wirtualnej Azure](https://launchpad.support.sap.com/#/notes/1928533).

[SAP Uwaga #2316233 - SAP HANA w systemie Microsoft Azure (wystąpienia duże)](https://launchpad.support.sap.com/#/notes/2316233/E) również jest znacząca. Obejmuje ona rozwiązania opisane w tym przewodniku. Ponadto są obsługiwane do uruchamiania w typie GS5 maszyny Wirtualnej Azure SAP HANA. Informacje w tym przypadku jest opublikowany w [SAP witryny sieci Web](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

SAP HANA na określone w 2316233 # Uwaga SAP rozwiązania Azure (wystąpienia duże) zapewnia klientom firmy Microsoft i SAP możliwość wdrażania duży pakiet Business SAP, programu SAP BW, S/4 HANA, BW/4HANA lub innych obciążeń SAP HANA na platformie Azure. Rozwiązanie opiera się na sygnatury certyfikowanym sprzętem dedykowanym SAP HANA ([SAP HANA dostosowane integracji centrum danych — TDI](https://scn.sap.com/docs/DOC-63140)). Po uruchomieniu rozwiązanie skonfigurowane SAP HANA TDI wszystkich SAP HANA aplikacji (np. SAP Business Suite na SAP HANA, programu SAP BW SAP HANA, S4/HANA i BW4/HANA) działa w infrastrukturze sprzętu.

W porównaniu do uruchomionego SAP HANA na maszynach wirtualnych, to rozwiązanie ma korzyści. Zapewnia znacznie większych woluminów pamięci. Aby włączyć to rozwiązanie, należy zrozumieć następujące kluczowe aspekty:

- Aplikacje warstwy i z systemem innym niż SAP aplikacji SAP działają w maszyn wirtualnych, które znajdują się w sygnatury zwykle Azure sprzętu.
- Odbiorcy lokalnej infrastruktury, centra danych i wdrożeń aplikacji są podłączone do platformy w chmurze za pośrednictwem usługi ExpressRoute (zalecane) lub wirtualnej sieci prywatnej (VPN). Usługa Active Directory i DNS również zostały rozszerzone na platformie Azure.
- Wystąpienie bazy danych SAP HANA HANA obciążenia działa na SAP HANA na platformie Azure (wystąpienia duże). Sygnatura wystąpienia dużych jest połączony w sieci Azure, więc oprogramowanie na maszynach wirtualnych zakłócają działania w przypadku dużych HANA wystąpienia HANA.
- Sprzęt SAP HANA na platformie Azure (wystąpienia duże) jest dedykowany sprzęt w IaaS z SUSE Linux Enterprise Server lub Red Hat Enterprise Linux preinstalowany. Podobnie jak w przypadku maszyn wirtualnych, dalsze aktualizacje i obsługa systemu operacyjnego odpowiada użytkownik.
- Instalacja HANA lub dodatkowe składniki niezbędne do uruchomienia w jednostkach wystąpienia dużych HANA SAP HANA odpowiada użytkownik. Wszystkich odpowiednich trwających operacji i administrowanie SAP HANA na platformie Azure są również z odpowiedzialności.
- Oprócz rozwiązania opisane w tym miejscu można zainstalować inne składniki w Twojej subskrypcji platformy Azure, łączącego się SAP HANA na platformie Azure (wystąpienia duże). Przykłady są składniki, które umożliwiają komunikację z lub bezpośrednio do SAP HANA serwery baz danych, takich jak serwery przeskoku RDP, SAP HANA Studio usług danych SAP dla scenariuszy analizy Biznesowej programu SAP lub sieci rozwiązań w zakresie monitorowania.
- Jak Azure wystąpienie dużych HANA oferuje obsługę wysokiej dostępności i odzyskiwania po awarii.

## <a name="architecture"></a>Architektura

Na wysokim poziomie SAP HANA dla rozwiązania Azure (wystąpienia duże) ma warstwy aplikacji SAP znajdującej się na maszynach wirtualnych. Warstwa bazy danych znajduje się na skonfigurowane SAP TDI sprzętu znajduje się w sygnaturze dużych wystąpienia, w tym samym regionie Azure, podłączoną do IaaS platformy Azure.

> [!NOTE]
> Wdrożenie SAP warstwy aplikacji w tym samym regionie Azure jako warstwa SAP DBMS. Ta reguła jest dobrze udokumentowane w opublikowanych informacji o SAP obciążeń na platformie Azure. 

Ogólna architektura SAP HANA na platformie Azure (wystąpienia duże) zapewnia konfiguracji sprzętu certyfikowanego SAP TDI, czyli niezwirtualizowanych, systemu operacyjnego, wysokiej wydajności serwera bazy danych SAP HANA. Zapewnia także możliwość i elastyczność Azure do zasobów skalowania warstwy aplikacji SAP do własnych potrzeb.

![Omówienie architektury SAP HANA na platformie Azure (wystąpienia duże)](./media/hana-overview-architecture/image1-architecture.png)

Architektura wyświetlany jest podzielony na trzy części:

- **Prawo**: Pokazuje, umożliwiając użytkownikom dostęp do aplikacji biznesowych, takich jak SAP w centrach lokalnej infrastruktury z systemem różnych aplikacji w danych. W idealnym przypadku to lokalnej infrastruktury następnie jest podłączony do platformy Azure z [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centrum**: Pokazuje IaaS platformy Azure i w takim przypadku użyj maszyn wirtualnych do hostów SAP lub inne aplikacje, które używają SAP HANA jako DBMS system. Mniejsze wystąpień HANA działające z pamięci, które zapewniają maszyny wirtualne są wdrażane na maszynach wirtualnych wraz z ich warstwy aplikacji. Aby uzyskać więcej informacji o maszynach wirtualnych, zobacz [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/).

   Usługi w sieci platformy Azure są używane do grupowania systemów SAP wraz z innych aplikacji do sieci wirtualnych. Te sieci wirtualne nawiązać połączenia z systemów lokalnych, jak również dotyczące SAP HANA na platformie Azure (wystąpienia duże).

   Dla programu SAP NetWeaver aplikacji i baz danych, które są obsługiwane na platformie Azure, zobacz [1928533 # SAP Obsługa Uwaga — aplikacje SAP na platformie Azure: obsługiwane produktów i typy maszyny Wirtualnej Azure](https://launchpad.support.sap.com/#/notes/1928533). Dokumentacja na temat wdrażania rozwiązania SAP na platformie Azure zobacz:

  -  [Użyj SAP na maszynach wirtualnych systemu Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Użycie programu SAP rozwiązań na maszynach wirtualnych Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Po lewej**: zawiera sprzętu certyfikowanego SAP HANA TDI w sygnatura wystąpienia dużych Azure. Jednostki HANA dużych wystąpienia są połączone do sieci wirtualnych subskrypcji przy użyciu tej samej technologii co łączność z lokalnymi na platformie Azure.

Sygnatura wystąpienia dużych Azure sam zawierają następujące składniki:

- **Obliczanie**: serwery, które są oparte na Intel Xeon E7-8890v3 lub Intel Xeon E7-8890v4 procesorów, które zawierają niezbędne możliwości obliczeniowych i są SAP HANA certyfikowane.
- **Sieci**: A unified szybkich sieci szkieletowej, łączącego przetwarzania danych, magazynu i składników sieci LAN.
- **Magazyn**: infrastruktury magazynu, który jest dostępny za pośrednictwem ujednoliconego sieci szkieletowej. Określonej pojemności, który został dostarczony zależy od określonego SAP HANA w konfiguracji Azure (wystąpienia duże), które zostało wdrożone. Większej pojemności magazynu jest dostępny w dodatkowych kosztów miesięcznych.

W wielodostępnej infrastrukturze sygnatura wystąpienia dużych klienci są wdrażane jako izolowanych dzierżaw. Na wdrożenie dzierżawcy nadaj nazwę subskrypcji platformy Azure w ramach Twojej rejestracji platformy Azure. Tej subskrypcji platformy Azure jest to, że jest on rozliczany wystąpienia dużych HANA przed. Te dzierżawy mają relację 1:1 z subskrypcją platformy Azure. Dla sieci jest możliwe do jednostki wystąpienia dużych HANA wdrożone w jednej dzierżawy w jednym regionie Azure z różnych sieciach wirtualnych, które należą do różnych subskrypcji platformy Azure. Te subskrypcje platformy Azure musi należeć do tej samej rejestracji platformy Azure. 

Podobnie jak w przypadku maszyn wirtualnych, SAP HANA na platformie Azure (wystąpienia duże) jest oferowana w wielu regionach platformy Azure. Aby oferują możliwości odzyskiwania po awarii, można włączyć. Różne sygnatury dużych wystąpienie w ramach jednego regionu politycznej geograficznie są połączone ze sobą. Na przykład HANA dużych wystąpienia sygnatury nam zachód i nam wschodnie są połączone za pośrednictwem dedykowanej sieci łącza replikacji odzyskiwania po awarii. 

Tak samo, jak można wybrać różne typy maszyny Wirtualnej z maszyn wirtualnych platformy Azure, są dostępne różne jednostki SKU z HANA dużych wystąpienie, które są dostosowane do obciążenia różne rodzaje SAP HANA. SAP stosuje współczynnik pamięci do procesora gniazda dla różnych obciążeń oparte na Intel generacje procesora. W poniższej tabeli przedstawiono typy jednostki SKU oferowane.

Począwszy od 2017 lipca SAP HANA na platformie Azure (wystąpienia duże) są dostępne w kilka konfiguracji w regionach platformy Azure nam zachodnie nam wschodnie, Australia Wschodnia, Australia Południowo-Wschodnia, Europa Zachodnia i Europa Północna.

| Rozwiązania SAP | Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| --- | --- | --- | --- | --- |
| Zoptymalizowana pod kątem OLAP: programu SAP BW BW/4HANA<br /> lub SAP HANA dla ogólnych obciążeń OLAP | SAP HANA na Azure S72<br /> — 2 procesor Intel Xeon® x E7 8890 v3<br /> 36 rdzeni Procesora i 72 wątków CPU |  768 GB |  3 TB | Dostępna |
| --- | SAP HANA na Azure S144<br /> – 4 procesor Intel Xeon® x E7 8890 v3<br /> 72 rdzeni Procesora i 144 wątków CPU |  1,5 TB |  6 TB | Nieoferowane już |
| --- | SAP HANA na Azure S192<br /> – 4 procesor Intel Xeon® x E7 8890 v4<br /> 96 rdzeni Procesora i 192 wątków CPU |  2.0 TB |  8 TB | Dostępna |
| --- | SAP HANA na Azure S384<br /> – 8 procesor Intel Xeon® x E7 8890 v4<br /> 192 rdzeni Procesora i 384 wątków CPU |  4.0 TB |  16 TB | Dostępna |
| Zoptymalizowana pod kątem OLTP: SAP Business Suite<br /> SAP HANA lub S/4HANA (OLTP)<br /> ogólny OLTP | SAP HANA na Azure S72m<br /> — 2 procesor Intel Xeon® x E7 8890 v3<br /> 36 rdzeni Procesora i 72 wątków CPU |  1,5 TB |  6 TB | Dostępna |
|---| SAP HANA na Azure S144m<br /> – 4 procesor Intel Xeon® x E7 8890 v3<br /> 72 rdzeni Procesora i 144 wątków CPU |  3.0 TB |  12 TB | Nieoferowane już |
|---| SAP HANA na Azure S192m<br /> – 4 procesor Intel Xeon® x E7 8890 v4<br /> 96 rdzeni Procesora i 192 wątków CPU  |  4.0 TB |  16 TB | Dostępna |
|---| SAP HANA na Azure S384m<br /> – 8 procesor Intel Xeon® x E7 8890 v4<br /> 192 rdzeni Procesora i 384 wątków CPU |  6.0 TB |  18 TB | Dostępna |
|---| SAP HANA na Azure S384xm<br /> – 8 procesor Intel Xeon® x E7 8890 v4<br /> 192 rdzeni Procesora i 384 wątków CPU |  8.0 TB |  22 TB |  Dostępna |
|---| SAP HANA na Azure S576m<br /> – 12 procesor Intel Xeon® x E7 8890 v4<br /> 288 rdzeni Procesora i 576 wątków CPU |  12.0 TB |  28 TB | Dostępna |
|---| SAP HANA na Azure S768m<br /> – 16 procesor Intel Xeon® x E7 8890 v4<br /> 384 rdzeni Procesora i 768 wątków CPU |  16.0 TB |  36 TB | Dostępna |
|---| SAP HANA na Azure S960m<br /> – 20 procesor Intel Xeon® x E7 8890 v4<br /> 480 rdzeni Procesora i 960 wątków CPU |  20.0 TB |  46 TB | Dostępna |

- Rdzenie procesora CPU = suma nie-Procesora rdzeni hiperwątkowych sumy procesorów jednostki serwera.
- Wątków procesora CPU = Suma wątków obliczeniowe udostępniane przez hiperwątkowych rdzeni procesora CPU sumy procesorów jednostki serwera. Wszystkie jednostki są domyślnie skonfigurowane, aby używać technologii Hyper-Threading.


Konkretne konfiguracje wybrane są zależne od obciążenia, zasobów procesora CPU i pamięci żądany. Istnieje możliwość dla obciążenia OLTP użyć wersji produktu, które są zoptymalizowane dla obciążeń OLAP. 

Podstawowa dla wszystkich ofert sprzętu są certyfikowane SAP HANA TDI. Dwóch różnych klas sprzętu dzielą jednostki SKU do:

- S72, S72m S144, S144m, S192 i S192m, które są określane jako "Typ klasy I" z jednostki SKU.
- S384, S384m S384xm, S576m, S768m i S960m, które są określane jako "Typ klasy II" z jednostki SKU.

Pełną sygnatury wystąpienia dużych HANA wyłącznie nie jest przydzielona dla jednego odbiorcy&#39;Użyj s. Ten fakt dotyczy stojakami zasoby obliczeniowe i magazynujące, które są połączone za pośrednictwem sieci szkieletowej również wdrożona na platformie Azure. Wystąpienie dużych HANA infrastruktury, takich jak Azure, wdraża różnych klientów &quot;dzierżawców&quot; które są odizolowane od siebie w następujących trzech poziomach:

- **Sieci**: izolacji za pośrednictwem sieci wirtualnych w ramach sygnatura wystąpienia dużych HANA.
- **Magazyn**: izolacji za pomocą magazynu maszyn wirtualnych, które zostały przypisane woluminów magazynu i izolowanie woluminów magazynu między dzierżawcami.
- **Obliczenia bazy danych**: dedykowany przypisania jednostek serwera do pojedynczej dzierżawy. Nie twarde lub soft partycjonowanie jednostek serwera. Bez udostępniania jednego serwera lub hosta jednostki między dzierżawcami. 

Wdrożenia HANA wystąpienia dużych jednostek od różnych dzierżawców nie są widoczne dla siebie nawzajem. Jednostki wystąpienia dużych HANA wdrożone w różnych dzierżaw nie mogą komunikować się bezpośrednio ze sobą na poziomie wystąpienia dużych HANA sygnatury. Tylko HANA dużych wystąpienia jednostek w ramach jednego dzierżawcy mogą komunikować się ze sobą na poziomie wystąpienia dużych HANA sygnatury.

Dzierżawcy wdrożonej w sygnatura wystąpienia dużych jest przypisany do jedną subskrypcją platformy Azure na potrzeby rozliczeń. Dla sieci jest dostępny z sieci wirtualnych z innych subskrypcji platformy Azure w ramach tej samej rejestracji platformy Azure. W przypadku wdrożenia z innej subskrypcji platformy Azure, w tym samym regionie Azure, również możesz poprosić o rozdzielonych dzierżawy HANA dużych wystąpienia.

Brak istotnych różnic między systemami SAP HANA wystąpieniu dużych HANA i SAP HANA działające na maszynach wirtualnych wdrożonych na platformie Azure:

- Nie ma żadnych warstwa wirtualizacji dla SAP HANA na platformie Azure (wystąpienia duże). Otrzymasz wydajności używanego sprzętu bez systemu operacyjnego.
- W przeciwieństwie do usługi Azure SAP HANA na serwerze Azure (wystąpienia duże) jest dedykowany do określonego klienta. Nie było możliwości czy jednostki serwera lub hosta jest mocno lub soft podzielone na partycje. W związku z tym jednostki HANA dużych wystąpienia jest używany jako przypisane jako całość do dzierżawy i z tym. Ponowne uruchomienie lub zamknięcie serwera nie automatycznie prowadzić do systemu operacyjnego i SAP HANA wdrażany na innym serwerze. (Dla typu klasy I jednostki SKU, jedynym wyjątkiem jest, jeśli serwer napotka problemów i ponowne wdrożenie musi zostać wykonana na innym serwerze.)
- W przeciwieństwie do usługi Azure, których typy procesora hosta w przypadku wybrania najlepszych stosunku ceny do wydajności są typy procesora wybrany dla SAP HANA na platformie Azure (wystąpienia duże) najwyższy wykonywanie wierszu procesor Intel E7v3 i E7v4.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Uruchamianie wielu wystąpień SAP HANA na jedną jednostkę HANA dużych wystąpienia
Istnieje możliwość hostowania więcej niż jedno wystąpienie SAP HANA aktywne, w jednostkach HANA dużych wystąpienia. Zapewnienie możliwości magazynu migawek i odzyskiwania po awarii, taka konfiguracja wymaga zestawu dla każdego wystąpienia woluminów. Obecnie HANA dużych wystąpienia jednostki można podzielić w następujący sposób:

- **S72, S72m, S144, S192**: W przyrostach 256 GB, 256 GB najmniejszą początkowa jednostki. Maksymalna pamięć jednostki łączenie wielokrotności np. 256 GB i 512.
- **S144m i S192m**: W przyrostach 256 GB, 512 GB najmniejsza jednostka. Maksymalna pamięć jednostki łączenie wielokrotności np. 512 GB i 768.
- **Typ klasy II**: wielokrotnością 512 GB najmniejszą początkowa jednostka 2 TB. Maksymalna pamięć jednostki łączenie wielokrotności np. 512 GB, 1 TB i 1,5 TB.

Przykłady uruchamianie wielu wystąpień SAP HANA może wyglądać następująco.

| SKU | Rozmiar pamięci | Rozmiar magazynu | Rozmiary z wieloma bazami danych |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | wystąpienie HANA 1 x 768 GB<br /> lub wystąpienie 1 x 512 GB + 1 x 256 GB wystąpienia<br /> lub wystąpień 3 x 256 GB | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA wystąpień<br />lub wystąpienie 1 x 512 GB + 1 x 1 TB wystąpienia<br />lub wystąpień 6 x 256 GB<br />lub wystąpienie 1x1.5 TB | 
| S192m | 4 TB | 16 TB | 8 x 512 GB wystąpień<br />lub wystąpień 4 x 1 TB<br />lub wystąpień 4 x 512 GB + 2 x 1 TB wystąpień<br />lub wystąpień 4 x 768 GB + 2 x 512 GB wystąpień<br />lub wystąpienie. 1 x 4 TB |
| S384xm | 8 TB | 22 TB | 4 x 2 TB wystąpień<br />lub wystąpień 2 x 4 TB<br />lub wystąpień 2 x 3 TB + 1 x 2 TB wystąpień<br />lub wystąpień 2x2.5 TB + 1 x 3 TB wystąpień<br />lub wystąpienie. 1 x 8 TB |


Istnieją także inne różnice. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Użyj danych SAP HANA węzły warstwy i rozszerzenia
SAP obsługuje warstw modelu danych dla programu SAP BW różne wersje programu SAP NetWeaver i SAP BW/4HANA. Aby uzyskać więcej informacji na temat warstw modelu danych, zobacz dokument SAP [SAP BW/4HANA i programu SAP BW na HANA z węzłami rozszerzenia SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Z wystąpieniem dużych HANA można użyć konfiguracji opcja 1 SAP HANA rozszerzenia węzłów, zgodnie z objaśnieniem w dokumentach blogu — często zadawane pytania i SAP. Konfiguracje opcja 2 można można skonfigurować przy użyciu następujących HANA dużych wystąpienia jednostki SKU: S72m, S192 S192m, S384 i S384m. 

Przeglądając dokumentację zaletą mogą nie być widoczne natychmiast. Jednak po wyświetleniu wskazówki dotyczące rozmiaru SAP widzą korzyści przy użyciu opcji-1 i 2 opcja węzłów rozszerzenia SAP HANA. Poniżej przedstawiono przykłady:

- Wskazówki dotyczące rozmiaru SAP HANA zwykle wymaga double ilości ilość danych jako pamięci. Po uruchomieniu wystąpieniem SAP HANA z gorących danych mają tylko 50% lub mniej pamięci wypełnione z danymi. W pozostałej części pamięci w idealnym przypadku odbywa się dla SAP HANA podczas pracy.
- To oznacza w jednostce HANA dużych S192 wystąpienia z 2 TB pamięci z bazy danych programu SAP BW, wystarczy tylko 1 TB jako ilość danych.
- Użycie dodatkowego węzła Rozszerzenia SAP HANA opcji-1, również S192 HANA dużych wystąpienia jednostki SKU, udostępnia dodatkowe 2 TB pojemności dla woluminów danych. W konfiguracji opcji-2 możesz uzyskać dodatkowe 4 TB ciepłych danych woluminu. W porównaniu do węzła gorących pojemności pamięci pełne węzeł rozszerzenia "ciepłych" może służyć do przechowywania danych dla opcji-1. Podwójna pamięci może służyć do woluminów danych w konfiguracji węzła Rozszerzenia SAP HANA opcja 2.
- Na końcu 3 TB pojemności dla danych i stosunku hot przez ciepłych 1:2 dla opcji-1. Masz 5 TB danych i stosunek 1:4 z konfiguracją węzła Rozszerzenia opcja 2.

Im większa ilość danych w porównaniu do pamięci, im wyższa szanse są że ciepłych dane, które są pytania o są przechowywane na dysku magazynu.


## <a name="operations-model-and-responsibilities"></a>Operacje modelu i jego obowiązki

Usługi z SAP HANA na platformie Azure (wystąpienia duże) jest wyrównywana z usług Azure IaaS. Możesz pobrać wystąpienia wystąpienia dużych HANA z zainstalowanym systemem operacyjnym jest zoptymalizowany pod kątem SAP HANA. Jako z maszyn wirtualnych IaaS platformy Azure, większość zadań ograniczenie funkcjonalności systemu operacyjnego, instalowania dodatkowego oprogramowania, instalowanie HANA, systemu operacyjnego i HANA i aktualizowania systemu operacyjnego i HANA odpowiada użytkownik. Microsoft nie wymusić aktualizacje systemu operacyjnego lub HANA aktualizacje na użytkownik.

![Obowiązki SAP HANA na platformie Azure (wystąpienia duże)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak pokazano na diagramie, SAP HANA na platformie Azure (wystąpienia duże) jest wieloma dzierżawcami, które oferty IaaS. W większości przypadków podział odpowiedzialności jest na granicy infrastruktury systemu operacyjnego. Microsoft jest odpowiedzialny za wszystkie aspekty usługi poniżej wiersza systemu operacyjnego. Użytkownik jest odpowiedzialny za wszystkie aspekty usługi powyżej linii. System operacyjny odpowiada użytkownik. Można nadal używać najnowszej metody lokalnego może stosować zgodności, zabezpieczeń, zarządzanie aplikacjami, podstawy i systemu operacyjnego zarządzania. Systemy są wyświetlane tak, jakby znajdują się w sieci w odniesieniu do wszystkich.

Ta usługa jest zoptymalizowana pod kątem SAP HANA, więc obszary, których potrzebujesz do pracy z firmy Microsoft do wykorzystania podstawowych możliwości infrastruktury w celu uzyskania najlepszych wyników.

Poniższa lista zawiera więcej szczegółów na poszczególnych warstw i Twoje obowiązki:

**Sieć**: sieciach wewnętrznych dla sygnatury dużych wystąpienia systemem SAP HANA. Twoje odpowiedzialność obejmuje dostęp do magazynu, łączność między wystąpień (na potrzeby skalowania w poziomie i inne funkcje), łączności pozioma i łączności do platformy Azure, gdzie jest hostowana warstwy aplikacji SAP na maszynach wirtualnych. Zawiera także połączenia sieci WAN między centrach danych platformy Azure dla replikacji celów odzyskiwania po awarii. Wszystkie sieci są podzielone na partycje przez dzierżawcę a jakości usług zastosowane.

**Magazyn**: zwirtualizowany magazynu dla wszystkich woluminów wymagane przez serwery SAP HANA, a także migawek na partycje. 

**Serwery**: dedykowanych serwerów fizycznych do uruchomienia baz danych HANA SAP, które zostały przypisane do dzierżawcy. Serwery o typie I klas jednostek magazynowych są pobieranej sprzętu. Z tych typów serwerów konfiguracji serwera jest zbierane i zarządzania profilami, które mogą zostać przeniesione z jednego fizycznego sprzętu do innego sprzętu fizycznego. Takie (ręczna) przenoszenie profilu przez operacje można porównać coś do naprawą usługi Azure. Serwery jednostki SKU klasy typu II nie oferuje takiej możliwości.

**SDDC**: oprogramowanie do zarządzania, który służy do zarządzania danymi w centrach jako jednostek zdefiniowanych przez oprogramowanie. Umożliwia firmy Microsoft do puli zasobów dla skali, dostępności i optymalnej wydajności.

**Systemu operacyjnego**: Wybierz (SUSE Linux lub Red Hat Linux) systemu operacyjnego uruchomionego na serwerze. Obrazy systemu operacyjnego, które są dostarczane z dostarczonych przez poszczególne dostawcy Linux do firmy Microsoft systemem SAP HANA. Musisz mieć subskrypcję z dostawcą systemu Linux dla określonego obrazu zoptymalizowanych pod kątem SAP HANA. Przydadzą się osobom odpowiedzialnym za rejestrację obrazów z dostawcą systemu operacyjnego. 

Z punktu przekazanie przez firmę Microsoft i ponosisz odpowiedzialność za wszelkie dodatkowe stosowanie poprawek systemu operacyjnego Linux. Tej poprawki obejmuje dodatkowe pakiety, które mogą być wymagane do pomyślnej instalacji SAP HANA i które nie zostały zawarte w określonym dostawcy systemu Linux w ich SAP HANA zoptymalizowanych obrazów systemu operacyjnego. (Aby uzyskać więcej informacji, zobacz w SAP HANA instalacji dokumentacji i uwagi SAP). 

Jest odpowiedzialny za poprawek systemu operacyjnego z powodu nieprawidłowego działania lub optymalizacji systemu operacyjnego i sterowników względem sprzętu określonego serwera. Możesz również są odpowiedzialne za zabezpieczeń lub funkcjonalności stosowania poprawek systemu operacyjnego. 

Twoje odpowiedzialność zawiera również monitorowania i planowania pojemności:

- Użycie zasobów Procesora.
- Zużycie pamięci.
- Woluminy na dyskach związane z wolnego miejsca, IOPS i opóźnień.
- Wolumin ruchem sieciowym między wystąpienia dużych HANA i SAP warstwy aplikacji.

Podstawowej infrastruktury wystąpienia dużych HANA udostępnia funkcje tworzenia kopii zapasowych i przywracania woluminu systemu operacyjnego. Z tej funkcji jest również obowiązkiem użytkownika.

**Oprogramowanie pośredniczące**: głównie wystąpienia SAP HANA. Administracji, operacje i monitorowania są Twoje odpowiedzialności. Podana funkcja umożliwia przy użyciu migawek magazynu na potrzeby odzyskiwania kopii zapasowej i przywracania i odzyskiwaniem po awarii. Te możliwości są udostępniane przez infrastrukturę. Twoje obowiązki również obejmować projektowanie wysokiej dostępności i odzyskiwania po awarii dzięki takim funkcjom korzystania z nich i monitorowania można określić, czy Magazyn migawek wykonane pomyślnie.

**Dane**: danych zarządzanych przez SAP HANA i innych danych, takich jak kopie zapasowe udziałów plików znajdujących się na woluminach lub pliku. Twoje obowiązki obejmują monitorowania wolnego miejsca na dysku i zarządzania zawartością w woluminach. Możesz również są odpowiedzialne za monitorowanie pomyślne wykonanie kopii zapasowych woluminów dysków i pamięci masowej migawki. Pomyślne wykonanie replikacji danych do lokacji odzyskiwania po awarii jest odpowiedzialny za firmy Microsoft.

**Aplikacje:** SAP wystąpienia aplikacji lub w przypadku aplikacji z systemem innym niż SAP, warstwy aplikacji tych aplikacji. Twoje obowiązki obejmują wdrożenia, administracji, operacje i monitorowania tych aplikacji. Jest odpowiedzialny za planowanie pojemności użycia zasobów procesora CPU, zużycie pamięci, użycia magazynu Azure i zużycie przepustowości sieci w ramach sieci wirtualnej. Możesz również są odpowiedzialni za planowanie wydajności dla zużycie zasobów z sieci wirtualnych do SAP HANA na platformie Azure (wystąpienia duże).

**WAN**: połączeń ustanowić z lokalnych do wdrożeń platformy Azure dla obciążeń. Wszystkich klientów z wystąpieniem dużych HANA Użyj Azure ExpressRoute dla połączenia. To połączenie nie jest częścią SAP HANA dla rozwiązania Azure (wystąpienia duże). Jest odpowiedzialny za konfigurację tego połączenia.

**Archiwum**: łączyli się do archiwizacji kopii danych za pomocą własnych metod na kontach magazynu. Archiwizowanie wymaga zarządzania, zgodności kosztów i operacji. Jest odpowiedzialny za Generowanie kopie archiwum i kopii zapasowych na platformie Azure i przechowywania ich w sposób zgodny z.

Zobacz [umowy SLA dla SAP HANA na platformie Azure (wystąpienia duże)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Zmiana rozmiaru

Zmiany rozmiaru dla wystąpienia dużych HANA nie różni się od rozmiaru dla HANA ogólnie. Dla istniejących i wdrażane systemy, które chcesz przenieść z innych RDBMS HANA, SAP udostępnia wiele raportów, które Uruchom istniejących systemach SAP. Jeśli baza danych jest przenoszona do HANA, te raporty sprawdzenie, czy dane i obliczyć wymagania dotyczące pamięci dla wystąpienia HANA. Aby uzyskać więcej informacji na temat sposobu uruchamiania tych raportów i uzyskać ich najnowsze poprawki lub wersji należy przeczytać następujące informacje o SAP:

- [Uwaga SAP #1793345 - zmiany rozmiaru pakietu SAP na HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Uwaga 1872170 # - pakiet w raporcie zmiany rozmiaru HANA i S/4 HANA SAP](https://launchpad.support.sap.com/#/notes/1872170)
- [Uwaga SAP #2121330 — często zadawane pytania: Programu SAP BW na HANA zmiany rozmiaru raportu](https://launchpad.support.sap.com/#/notes/2121330)
- [Uwaga #1736976 - raport rozmiaru dla BW na HANA SAP](https://launchpad.support.sap.com/#/notes/1736976)
- [Uwaga 2296290 # - nowe zmiany rozmiaru raportu dla BW na HANA SAP](https://launchpad.support.sap.com/#/notes/2296290)

Implementacje zielony pola SAP szybkie Rozmiar symboli jest dostępna obliczyć wymagania dotyczące pamięci implementacji oprogramowania SAP na górze HANA.

Wymagania dotyczące pamięci dla HANA zwiększyć wraz z rozwojem ilość danych. Należy pamiętać o bieżącego użycia pamięci na to, co ma to być w przyszłości. Oparte na wymagania dotyczące pamięci, następnie można mapować Twoje żądanie do jednej jednostki SKU HANA dużych wystąpienia.

## <a name="requirements"></a>Wymagania

Ta lista składana wymagania dotyczące systemu SAP HANA na platformie Azure (wystąpienia większy).

**Microsoft Azure**

- Subskrypcji platformy Azure, który może odnosić się do SAP HANA na platformie Azure (wystąpienia duże).
- Microsoft Premier umowę dotyczącą pomocy technicznej. Aby uzyskać szczegółowe informacje związane z uruchamianiem SAP na platformie Azure, zobacz [2015553 # SAP Obsługa Uwaga — SAP w systemie Microsoft Azure: wymagania wstępne dotyczące obsługi](https://launchpad.support.sap.com/#/notes/2015553). Jeśli używasz wystąpienia dużych HANA jednostki 384 i większej liczby procesorów CPU, również należy rozszerzyć Premier umowę dotyczącą pomocy technicznej, aby uwzględnić odpowiedzi szybkie Azure.
- Rozpoznawanie HANA dużych SKU wystąpienia muszą po wykonaniu wykonywania zmiany rozmiaru, z programu SAP.

**Połączenie sieciowe**

- ExpressRoute między lokalnymi Azure: nawiązać połączenia z lokalnego centrum danych Azure, upewnij się, że kolejność co najmniej 1 GB połączenie od usługodawcy internetowego. 

**System operacyjny**

- Licencji w systemie SUSE Linux Enterprise Server 12 SAP aplikacji.

   > [!NOTE] 
   > System operacyjny dostarczane przez firmę Microsoft nie jest zarejestrowany w usłudze SUSE. Nie jest ona dołączona do wystąpienia narzędzia do zarządzania subskrypcją.

- SUSE Linux subskrypcji narzędzia do zarządzania wdrożona na platformie Azure na maszynie Wirtualnej. To narzędzie udostępnia możliwości dla SAP HANA na platformie Azure (wystąpienia duże) były rejestrowane i odpowiednio aktualizowane przez SUSE. (Brak Brak dostępu do Internetu w obrębie centrum danych wystąpienia dużych HANA.) 
- Licencje Red Hat Enterprise Linux 6.7 lub 7.2 dla SAP HANA.

   > [!NOTE]
   > Red Hat systemu operacyjnego dostarczane przez firmę Microsoft nie jest zarejestrowany. Nie jest ona dołączona do wystąpienia Red Hat subskrypcji Menedżera.

- Red Hat subskrypcji Menedżera wdrożona na platformie Azure na maszynie Wirtualnej. Red Hat subskrypcji Menedżera zapewnia SAP HANA na platformie Azure (wystąpienia duże) były rejestrowane i odpowiednio aktualizowane przez Red Hat. (Nie jest brak bezpośredni dostęp do Internetu z w ramach dzierżawy wdrożone w sygnaturze wystąpienia dużych Azure).
- SAP wymaga obsługi kontraktu u swojego dostawcy systemu Linux. To wymaganie nie są usuwane przez rozwiązanie HANA wystąpienia dużych lub faktu uruchomienia systemu Linux na platformie Azure. W przeciwieństwie do niektórych obrazów galerii Linux Azure, usługa opłata jest *nie* uwzględniona w ofercie rozwiązania HANA dużych wystąpienia. Jest obowiązek spełnienie wymagań SAP dotyczących umów pomocy technicznej z dystrybutorem systemu Linux. 
   - W systemie SUSE Linux, wyszukiwanie wymagania umowy pomocy technicznej w [SAP Uwaga #1984787 - SUSE Linux Enterprise Server 12: informacje o instalacji](https://launchpad.support.sap.com/#/notes/1984787) i [SAP Uwaga #1056161 - SUSE priorytet obsługę aplikacji programu SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Red Hat Linux musisz mieć poziomy poprawną subskrypcję, które obsługują i aktualizacji do systemów operacyjnych HANA dużych wystąpienia usługi. Red Hat zaleca Red Hat Enterprise Linux [SAP rozwiązań] (https://access.redhat.com/solutions/3082481 subskrypcji. 

W macierzy pomocy technicznej w różnych wersjach SAP HANA z różnymi wersjami systemu Linux, zobacz [SAP Uwaga #2235581](https://launchpad.support.sap.com/#/notes/2235581).


**Baza danych**

- Licencje i składników instalacji oprogramowania SAP HANA (platforma lub enterprise edition).

**Aplikacje**

- Licencje i składniki instalacji oprogramowania aplikacje SAP łączący się SAP HANA i SAP powiązane umowy dotyczące pomocy technicznej.
- Licencje i składniki instalacji oprogramowania dla aplikacji z systemem innym niż SAP używana w celu SAP HANA w środowiskach Azure (wystąpienia duże) i powiązanych umów pomocy technicznej.

**Umiejętności**

- Środowisko z i wiedzy IaaS platformy Azure i jego składniki.
- Środowisko z i wiedzę na temat sposobu wdrażania SAP obciążenia na platformie Azure.
- Certyfikowane personelu instalacji SAP HANA.
- SAP umiejętności architektów do projektowania wysokiej dostępności i odzyskiwania po awarii wokół SAP HANA.

**SAP**

- Oczekuje się, w przypadku klienta programu SAP i obsługuje kontraktu o SAP.
- Szczególnie w przypadku implementacji klasy II typu jednostki SKU HANA dużych wystąpienia zapoznaj się z programu SAP w wersjach programu SAP HANA i ostatecznego konfiguracje sprzętu dużych skalowanie w pionie.


## <a name="storage"></a>Magazyn

Układ magazynu dla SAP HANA na platformie Azure (wystąpienia duże) jest konfigurowana przy SAP HANA w klasycznym modelu wdrażania przy użyciu SAP zalecane wytyczne. Wytyczne są udokumentowane w artykule [wymagania dotyczące magazynu SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) oficjalny dokument.

Duże HANA wystąpienie typu klasy I zawiera cztery razy wielkość pamięci jako wolumin magazynu. W klasie typu II HANA wystąpienia dużych jednostek magazynu nie ma cztery razy więcej. Jednostki są dostarczane z woluminu, który jest przeznaczony do przechowywania kopii zapasowej dziennika transakcji HANA. Aby uzyskać więcej informacji, zobacz [zainstalować i skonfigurować SAP HANA (duże wystąpień) w systemie Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zobacz poniższą tabelę pod względem Alokacja magazynu. W tabeli wymieniono nierównej pojemności dla różnych woluminach wyposażone w różnych jednostkach HANA dużych wystąpienia.

| HANA dużych wystąpienia jednostki SKU | Hana/danych | Hana/dziennika | Hana/udostępnionych | Hana / / kopii zapasowej dziennika |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S192 | 4,608 GB | 1024 GB | 1,536 GB | 1024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12 000 GB | 2050 GB | 2050 GB | 2,040 GB |
| S384xm | 16 000 GB | 2050 GB | 2050 GB | 2,040 GB |
| S576m | 20,000 GB | 3,100 GB | 2050 GB | 3,100 GB |
| S768m | UŻYWANE 28 000 GB | 3,100 GB | 2050 GB | 3,100 GB |
| S960m | 36,000 GB | 4,100 GB | 2050 GB | 4,100 GB |


Rzeczywiste wdrożonej woluminy mogą różnić w zależności od wdrożenia i narzędzie, które służy do wyświetlenia rozmiary woluminów.

Jeśli podzielić SKU wystąpienia z dużych HANA, może wyglądać kilka przykładów części dzielenia możliwe:

| Pamięć partycji w GB | Hana/danych | Hana/dziennika | Hana/udostępnionych | Hana / / kopii zapasowej dziennika |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


Rozmiary są liczbami nierównej woluminów, które może różnić się nieznacznie na podstawie wdrożenia i narzędzia służące do przyjrzeć się woluminów. Istnieją także inne rozmiarów partycji, takich jak 2,5 TB. Rozmiary magazynu są obliczane przy użyciu formuły podobny do tego użyć dla poprzedniej partycji. Termin "partycji" nie oznacza, że system operacyjny, pamięć lub zasoby procesora CPU są w żadnym podzielone na partycje. Wskazuje on partycji magazynu dla różnych wystąpień HANA, które można wdrożyć w jednej jednostce HANA dużych wystąpienia. 

Mogą wymagać więcej pamięci masowej. Magazyn można dodać przy zakupie dodatkowego magazynu w jednostkach 1 TB. To dodatkowe Magazyn można dodać jako dodatkowy wolumin. Również może służyć do rozszerzenia co najmniej jeden z istniejących woluminów. Nie można zmniejszyć rozmiar woluminu pierwotnie wdrożone, a przede wszystkim opisane w poprzednich tabelach. Ponadto nie można zmienić nazwy woluminów lub instalacji nazwy. Woluminy magazynu opisany powyżej są dołączone do jednostki HANA dużych wystąpienia jako woluminy NFS4.

Przy użyciu migawek magazynu na potrzeby odzyskiwania kopii zapasowej i przywracania i odzyskiwaniem po awarii. Aby uzyskać więcej informacji, zobacz [SAP HANA (duże wystąpień) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Szyfrowanie nieużywanych danych
Magazyn używany dla wystąpienia dużych HANA umożliwia przezroczystego szyfrowania danych są przechowywane na dyskach. Po wdrożeniu jednostki wystąpienia dużych HANA można włączyć tego rodzaju szyfrowania. Możesz również można zmienić na zaszyfrowanych woluminach po wdrożeniu. Przenoszenie z niezaszyfrowane do zaszyfrowanych woluminach działa w sposób niewidoczny i nie wymaga przestojów. 

O typie I klas jednostek magazynowych, woluminu rozruchowego, jednostka LUN jest przechowywany na są szyfrowane. Dla klasy typu II jednostki SKU z HANA dużych wystąpienia należy zaszyfrować rozruchu jednostki LUN z metodami systemu operacyjnego. Aby uzyskać więcej informacji skontaktuj się z zespołu zarządzania usługami firmy Microsoft.


## <a name="networking"></a>Networking

Architektura usług sieci platformy Azure jest kluczowym składnikiem pomyślne wdrożenie aplikacji SAP HANA dużych wystąpienia. Zazwyczaj SAP HANA we wdrożeniach Azure (wystąpienia duże) mają większy pozioma SAP z kilku różnych rozwiązań SAP o różnych rozmiarach baz danych, użycie zasobów procesora CPU i użycie pamięci. Istnieje prawdopodobieństwo, że nie wszystkie systemy SAP są oparte na SAP HANA. Twoje pozioma SAP jest prawdopodobnie hybrydowych, która używa:

- Wdrażane SAP systemów lokalnych. Z powodu ich rozmiary tych systemów aktualnie nie może być umieszczona na platformie Azure. Przykładem jest produkcji systemu SAP ERP, który działa w programie SQL Server (co baza danych) i wymaga więcej zasobów procesora CPU lub pamięci niż maszyny wirtualne.
- Wdrożona na podstawie SAP HANA SAP systemów lokalnych.
- Systemy SAP wdrożonych na maszynach wirtualnych. Te systemy mogą być programowanie i testowanie piaskownicy, lub produkcji wystąpień na podstawie SAP NetWeaver aplikacji, które można pomyślnie wdrożyć na platformie Azure (na maszynach wirtualnych), na podstawie zapotrzebowania zasobów konsumenckich i pamięci. Te systemy mogą również oparte na baz danych, takich jak SQL Server. Aby uzyskać więcej informacji, zobacz [1928533 # SAP Obsługa Uwaga — aplikacje SAP na platformie Azure: obsługiwane produktów i typy maszyny Wirtualnej Azure](https://launchpad.support.sap.com/#/notes/1928533/E). I systemy te mogą być oparte na baz danych, takie jak SAP HANA. Aby uzyskać więcej informacji, zobacz [SAP HANA certyfikowane platform IaaS](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Wdrożenia serwerów aplikacji SAP na platformie Azure (na maszynach wirtualnych), korzystających z SAP HANA na platformie Azure (duże wystąpień) w Azure dużych wystąpienia sygnatury.

Typowe jest hybrydowego SAP w orientacji poziomej co najmniej czterech różnych scenariuszy wdrażania. Istnieją także często klienta pełną krajobrazów SAP działających na platformie Azure. Jak maszyny wirtualne są bardziej wydajne, powoduje zwiększenie liczby klientów, łączące ich rozwiązania SAP na platformie Azure.

Azure networking w kontekście systemu SAP wdrożona na platformie Azure nie jest skomplikowane. Jest on oparty na następujące zasady:

- Sieci wirtualnych platformy Azure musi być podłączony do obwodu ExpressRoute, który nawiązuje połączenie z siecią lokalną.
- Obwód usługi expressroute zwykle łączy lokalnego do platformy Azure ma przepustowości 1 GB/s lub wyższy. Ta minimalnej przepustowości umożliwia odpowiedniej przepustowości transferu danych między lokalnymi i systemami uruchamianych na maszynach wirtualnych. Umożliwia także odpowiedniej przepustowości połączenia z systemami Azure od użytkowników lokalnych.
- Wszystkie systemy SAP na platformie Azure muszą zostać skonfigurowane w sieci wirtualnych do komunikowania się ze sobą.
- Usługi Active Directory i DNS obsługiwanego lokalnie zostały rozszerzone na platformie Azure za pośrednictwem usługi ExpressRoute z lokalnymi.


> [!NOTE] 
> Z rozliczeń punktu widzenia tylko jedną subskrypcją platformy Azure może odnosić się do tylko jednej dzierżawy w dużych wystąpienia sygnatury w określonym regionie Azure. Z drugiej strony pojedynczej dzierżawy sygnatury dużych wystąpienia może odnosić się do tylko jednej subskrypcji platformy Azure. To wymaganie jest zgodny z innymi obiektami rozliczeniowy na platformie Azure.

Jeśli SAP HANA na platformie Azure (wystąpienia duże) jest wdrażana w wielu różnych regionach platformy Azure, oddzielne dzierżawy zostanie wdrożona w dużych wystąpienia sygnatury. Można uruchomić jednocześnie w ramach tej samej subskrypcji platformy Azure tak długo, jak te wystąpienia są częścią tej samej pozioma SAP. 

> [!IMPORTANT] 
> Tylko wdrożenia usługi Azure Resource Manager jest obsługiwana z SAP HANA na platformie Azure (wystąpienia duże).

 

### <a name="additional-virtual-network-information"></a>Informacje dodatkowe sieci wirtualnej

Aby połączyć sieć wirtualną usługi expressroute, można utworzyć bramy usługi Azure. Aby uzyskać więcej informacji, zobacz [temat bram sieci wirtualnej dla usługi ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 

Brama usługi Azure może służyć z ExpressRoute infrastruktury poza platformą Azure lub sygnatura wystąpienia dużych Azure. Brama usługi Azure można również nawiązać połączenia między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [Skonfiguruj połączenie z siecią sieci dla Menedżera zasobów przy użyciu programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Azure bramy do maksymalnie czterech różnych połączeń ExpressRoute można łączyć tych połączeń pochodzą z różnych routerów krawędzi przedsiębiorstwa firmy Microsoft. Aby uzyskać więcej informacji, zobacz [SAP HANA (duże wystąpień) infrastruktury i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Przepływność bramy Azure zapewnia jest inna oba przypadki użycia. Aby uzyskać więcej informacji, zobacz [o bramy sieci VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Maksymalna przepustowość, który można uzyskać z bramą sieci wirtualnej jest 10 GB/s przy użyciu połączenia ExpressRoute. Kopiowanie plików między maszynę Wirtualną, która znajduje się w sieci wirtualnej i systemu lokalnego (jako strumień o pojedynczej kopii) nie osiągnięcia pełnej przepływności różne jednostki SKU bramy. Aby korzystać z pełnej przepustowości bramy sieci wirtualnej, użyj wielu strumieni. Lub różnych plików należy skopiować w strumieniach równoległych pojedynczego pliku.


### <a name="networking-architecture-for-hana-large-instance"></a>Architektura sieci dla wystąpienia dużych HANA
Architektura sieci dla wystąpienia dużych HANA można podzielić na cztery elementy:

- Sieci lokalne i połączenia ExpressRoute na platformie Azure. Ta część jest domeny klienta i jest połączona z platformą Azure za pośrednictwem usługi ExpressRoute. Zobacz prawej dolnej na poniższej ilustracji.
- Usługi sieci platformy Azure, jak wcześniej wspomniano, z sieciami wirtualnymi, które ponownie bram. Ta część jest obszar, w których należy znaleźć odpowiednie projekty do wymagań aplikacji, zabezpieczeń i zgodności z przepisami. Czy używać wystąpienia dużych HANA jest inny punkt wziąć pod uwagę pod względem liczby sieci wirtualnych Azure bramy jednostki SKU do wyboru. Zobacz prawym górnym rogu na rysunku.
- Łączność HANA dużych wystąpienia za pomocą technologii ExpressRoute na platformie Azure. Ta część jest wdrożony i obsługiwane przez firmę Microsoft. To wszystko, co należy zrobić, podaj niektórych zakresów adresów IP po ich wdrożeniu zasobów w przypadku dużych HANA nawiązać połączenia z obwodem usługi ExpressRoute sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [SAP HANA (duże wystąpień) infrastruktury i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Sieci w przypadku dużych HANA, która jest głównie przezroczysty dla Ciebie.

![Połączony SAP HANA Azure (wystąpienia duże) i lokalnej sieci wirtualnej](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Z wymaganiem, aby połączyć zasobów lokalnych za pomocą usługi Azure nie powoduje zmiany, ponieważ używasz wystąpienia dużych HANA. Wymóg posiadania jednego lub wielu sieci wirtualnych działających maszyn wirtualnych, które hosta warstwa aplikacji, która łączy się wystąpień HANA hostowanych w HANA dużych wystąpienia jednostki, również nie zmienia się. 

Różnice w celu wdrożenia SAP na platformie Azure są następujące:

- Jednostki wystąpienia dużych HANA dzierżawy klienta są połączone za pośrednictwem innego obwodu usługi expressroute w sieci wirtualne. Aby rozdzielić warunkach obciążenia, lokalną łączy ExpressRoute sieci wirtualnych i linki między sieciami wirtualnymi i wystąpienia dużych HANA nie udostępniaj tego samego routery.
- Profilu obciążenia między warstwy aplikacji SAP i wystąpieniem dużych HANA ma charakter różnych z dużą liczbą żądań małe i ulega zapaleniu jak transferu danych (zestawy wyników) z SAP HANA do warstwy aplikacji.
- Architektura SAP jest bardziej podatna na opóźnienia sieci niż typowy scenariusz, w którym dane są wymieniane między lokalną i platformą Azure.
- Brama sieci wirtualnej ma co najmniej dwa połączenia ExpressRoute. Oba połączenia udostępnianie maksymalna przepustowość dla danych przychodzących z bram sieci wirtualnej.

Opóźnienie sieci działa między maszyną wirtualną a HANA wystąpienia dużych jednostek może być większa niż typowe opóźnienia obustronne sieci maszyn wirtualnych do maszyny Wirtualnej. Zależne od regionu Azure, wartości mierzone może przekroczyć opóźnienia obustronne ms 0,7 sklasyfikowane jako poniżej średniej w [SAP Uwaga #1100926 — często zadawane pytania: wydajność sieci](https://launchpad.support.sap.com/#/notes/1100926/E). Jednakże klienci wdrażanie aplikacji SAP produkcji na podstawie SAP HANA pomyślnie na wystąpienia dużych SAP HANA. Klienci, którzy wdrażana ulepszenia dużą raportu za ich aplikacje SAP na SAP HANA przy użyciu HANA dużych wystąpienia jednostki. Upewnij się, że dokładnie przetestować procesy biznesowe w przypadku dużych HANA Azure.
 
Zapewnienie opóźnienia deterministyczne sieci między maszynami wirtualnymi i wystąpieniem dużych HANA wybór bramy sieci wirtualnej jednostka SKU jest istotne. W odróżnieniu od wzorce ruchu między lokalnymi i maszyn wirtualnych wzorzec ruchu między maszynami wirtualnymi i wystąpieniem dużych HANA można programować seria małych jednak dużych woluminów żądania i danych przekazywanych. Do obsługi takich seria dobrze, zdecydowanie zaleca się użycie jednostka SKU bramy UltraPerformance. Dla klasy typu II jednostki SKU HANA dużych wystąpienia użycie jednostka SKU bramy UltraPerformance jako brama sieci wirtualnej jest obowiązkowe.

> [!IMPORTANT] 
> Podana ogólną ruch sieciowy między aplikacją SAP i warstwy bazy danych, tylko wysokowydajną lub jednostki SKU bramy UltraPerformance dla sieci wirtualnych są obsługiwane w celu nawiązania SAP HANA na platformie Azure (wystąpienia duże). Jako brama sieci wirtualnej HANA dużych wystąpienia typu II jednostki SKU, obsługiwane są tylko bramy UltraPerformance jednostki SKU.



### <a name="single-sap-system"></a>Pojedynczego systemu SAP

Infrastruktury lokalnej wcześniej wyświetlany jest połączony za pośrednictwem usługi na platformie Azure. Obwód usługi expressroute łączy do router brzegowy przedsiębiorstwa. Aby uzyskać więcej informacji, zobacz [opis techniczny ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Po ustanowieniu trasy łączy do platformy Azure sieci szkieletowej, a wszystkie regiony platformy Azure są dostępne.

> [!NOTE] 
> Aby uruchomić krajobrazów SAP na platformie Azure, nawiązać najbliższy region platformy Azure w orientacji poziomej SAP router brzegowy przedsiębiorstwa. Sygnatury dużych wystąpienia platformy Azure są połączone za pośrednictwem dedykowanych przez przedsiębiorstwo urządzeń z routera krawędzi aby zminimalizować opóźnienie sieci między maszynami wirtualnymi w Azure IaaS oraz wystąpienia dużych sygnatury.

Brama sieci wirtualnej dla maszyn wirtualnych obsługujących instancje aplikacji SAP jest połączony z obwodem usługi ExpressRoute. Router brzegowy przedsiębiorstwa oddzielnych dedykowanych do nawiązywania połączenia z sygnatury dużych wystąpienia tej samej sieci wirtualnej jest połączona.

Ten system jest proste przykład pojedynczego systemu SAP. Warstwa aplikacji SAP jest hostowany na platformie Azure. Bazy danych SAP HANA działa na SAP HANA na platformie Azure (wystąpienia duże). Zakłada się, że przepustowość 2 GB lub 10 GB przepustowości bramy sieci wirtualnej nie reprezentują wąskiego gardła.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Wiele systemów SAP lub dużych systemów SAP

W przypadku wielu systemów SAP lub dużych systemach SAP wdrożenia nawiązać połączenia z programem SAP HANA na platformie Azure (wystąpienia duże) przepływność bramy sieci wirtualnej mogą stać się wąskiego gardła. W takim przypadku Podziel warstwy aplikacji na wiele sieci wirtualnych. Można również utworzyć specjalne sieci wirtualnej, która łączy się z HANA dużych wystąpienia w przypadkach, takich jak:

- Wykonywanie kopii zapasowych bezpośrednio z wystąpień HANA w przypadku dużych HANA do maszyny Wirtualnej na platformie Azure, który jest hostem udziałów NFS.
- Kopiowanie dużych kopii zapasowych lub innych plików z HANA wystąpienia dużych jednostek zarządzanych na platformie Azure miejsca na dysku.

Użyj oddzielnych sieci wirtualnej do hosta maszyny wirtualne, które zarządzania magazynem. To rozmieszczenie pozwala uniknąć dużego pliku lub transfer danych z wystąpienia dużych HANA na platformie Azure w bramie sieci wirtualnej pełniącą maszyn wirtualnych z systemem SAP warstwy aplikacji. 

Aby uzyskać większą skalowalność architektury sieci:

- Korzystaj z wieloma sieciami wirtualnymi pojedynczego, większy warstwy aplikacji SAP.
- Wdróż oddzielne jedną sieć wirtualną dla każdego systemu SAP wdrożone, w porównaniu do łączenia tych systemów SAP w różnych podsieciach w tej samej sieci wirtualnej.

 Skalowalność architektury sieci SAP HANA na platformie Azure (wystąpienia duże):

![Wdrażanie programu SAP warstwy aplikacji przez wiele sieci wirtualnych](./media/hana-overview-architecture/image4-networking-architecture.png)

Na rysunku przedstawiono SAP warstwy aplikacji lub składników wdrożone przez wiele sieci wirtualnych. Ta konfiguracja wprowadzono koszty nieuniknione opóźnienia, który wystąpił podczas komunikacji między aplikacjami hostowanej w tych sieciach wirtualnych. Domyślnie ruch sieciowy między maszynami wirtualnymi znajdującymi się w różnych sieciach wirtualnych Rozsyłanie za pomocą routery brzegowe przedsiębiorstwa w tej konfiguracji. Od września 2016 r. mogą być optymalizowane marszruty. 

Sposobem optymalizacji i zmniejszyć opóźnienia w łączności między dwiema sieciami wirtualnymi jest komunikacji równorzędnej sieci wirtualnych w tym samym regionie. Ta metoda działa, nawet jeśli te sieci wirtualne są w różnych subskrypcji. Z sieci wirtualnej komunikacji równorzędnej, komunikacja między maszynami wirtualnymi w dwóch różnych sieciach wirtualnych może być bezpośrednio komunikować się ze sobą szkieletu sieci platformy Azure. Czas oczekiwania pokazuje tak, jakby maszyn wirtualnych znajdują się w tej samej sieci wirtualnej. Ruch, który dotyczy zakresów adresów IP, które są połączone za pośrednictwem bramy sieci wirtualnej platformy Azure jest kierowany przez poszczególnych sieci wirtualnej bramy sieci wirtualnej. 

Aby uzyskać więcej informacji o sieci wirtualnej komunikacji równorzędnej, zobacz [równorzędna sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Routing na platformie Azure

Trzy routingu zagadnienia dotyczące sieci są ważne w przypadku SAP HANA na platformie Azure (wystąpienia duże):

* SAP HANA na platformie Azure (wystąpienia duże) jest możliwy tylko za pośrednictwem maszyn wirtualnych i dedykowanego połączenia ExpressRoute, nie są bezpośrednio z lokalnego. Bezpośredni dostęp z lokalnej do jednostki HANA dużych wystąpienia w dostarczonym przez firmę Microsoft, nie jest możliwe natychmiast. Przechodnie ograniczenia routingu są spowodowane bieżącej architektury sieci platformy Azure używana dla wystąpienia dużych SAP HANA. Niektórzy klienci administracji i wszystkie aplikacje, które wymagają bezpośredni dostęp, takie jak SAP rozwiązania Menedżera uruchomione w siedzibie firmy, nie może połączyć się bazy danych SAP HANA.

* Jeśli masz jednostki wystąpienia dużych HANA wdrożone w dwóch różnych regionach platformy Azure dla odzyskiwania po awarii tego samego przejściowej routingu ograniczenia są stosowane. Innymi słowy adresy IP HANA dużych wystąpienia jednostki w jednym regionie (na przykład nam zachód) nie są kierowane do jednostki wystąpienia dużych HANA wdrożony w innym regionie (na przykład nam wschodnie). To ograniczenie jest niezależny od użycia sieci platformy Azure komunikację równorzędną w regionach lub między łączenie obwody usługi ExpressRoute, łączących HANA dużych wystąpienie jednostki do sieci wirtualnych. Dla graficzną reprezentację zobacz rysunek w sekcji "Użyj HANA dużych wystąpienia jednostek w wielu regionach". To ograniczenie, która pochodzi z architekturą wdrożone, zabrania użycia replikacji systemu HANA jako funkcji odzyskiwania po awarii.

* SAP HANA Azure (duże wystąpień) w jednostkach mieć przypisanego adresu IP z zakresu adresów puli IP serwera, który zostanie przesłany. Aby uzyskać więcej informacji, zobacz [SAP HANA (duże wystąpień) infrastruktury i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ten adres IP jest dostępny za pośrednictwem subskrypcji platformy Azure i usługi ExpressRoute, łączący sieci wirtualnych HANA na platformie Azure (wystąpienia duże). Przypisany adres IP z zakresu adresów puli adresów IP serwera jest przypisanej bezpośrednio do jednostki sprzętu. Ma ona *nie* przypisane przez NAT już, jak w przypadku pierwszego wdrożenia tego rozwiązania. 

> [!NOTE] 
> Aby wyeliminować ograniczeń w przejściowych routingu, zgodnie z objaśnieniem w pierwszym elementy dwie listy, użyj dodatkowe składniki routingu. Składniki, które pozwala pokonać ograniczenia mogą być:

> * Reverse — serwer proxy można przekierować danych do i z. Na przykład F5 BIG-IP, NGINX Menedżera ruchu wdrożona na platformie Azure jako rozwiązanie routingu wirtualne zapory/ruchu.
> * Przy użyciu [reguły IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) w maszyny Wirtualnej systemu Linux, aby włączyć routing między lokalizacji lokalnej i HANA dużych wystąpienia jednostki lub wystąpienie dużych HANA jednostki w różnych regionach.

> Należy pamiętać, że urządzenia sieciowe wdrożenia i obsługę niestandardowych rozwiązań dotyczących innych firm lub IPTables nie jest obsługiwane przez firmę Microsoft. Obsługa musi zapewniać dostawcy używany składnik lub integrator. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Połączenie z Internetem HANA dużych wystąpienia
Obiekt dużych HANA *nie* mieć bezpośrednie połączenie z Internetem. Na przykład to ograniczenie może ograniczyć możliwość rejestrowania obrazu systemu operacyjnego bezpośrednio z dostawcą systemu operacyjnego. Konieczne może pracować z lokalnego serwera SUSE Linux Enterprise Server subskrypcji zarządzania narzędzia lub Red Hat Enterprise Linux subskrypcji Menedżera.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Szyfrowanie danych między maszynami wirtualnymi i HANA dużych wystąpienia
Danych przesyłanych między wystąpieniem dużych HANA i maszyny wirtualne nie są szyfrowane. Jednak wyłącznie w celu wymiany między po stronie HANA DBMS i JDBC/ODBC — aplikacje, można włączyć szyfrowanie ruchu. Aby uzyskać więcej informacji, zobacz [tej dokumentacji przez SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Za pomocą HANA dużych wystąpienia jednostek w wielu regionach

Konieczne może być powodów wdrażania SAP HANA na platformie Azure (duże wystąpień) w wielu regionach platformy Azure innych niż odzyskiwania po awarii. Może chcesz uzyskać dostęp do wystąpienia dużych HANA z poszczególnych maszyn wirtualnych wdrożonych w różnych sieciach wirtualnych w regionach. Adresy IP przypisane do różnych jednostek HANA dużych wystąpienia nie są przenoszone poza sieci wirtualne, które były bezpośrednio podłączone za pośrednictwem ich bramy do wystąpień. W związku z tym drobne zmiany wprowadzane do projektu sieci wirtualnej. Brama sieci wirtualnej może obsługiwać czterech różnych obwody usługi ExpressRoute poza routery krawędzi różnych organizacji. Każdej sieci wirtualnej, który jest połączony z jednym z sygnaturami dużych wystąpienia można podłączyć do sygnatury dużych wystąpienia w innym regionie Azure.

![Sieci wirtualnej podłączonej do wystąpienia dużych Azure sygnatur w różnych regionach platformy Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

Na rysunku przedstawiono, jak różnych sieciach wirtualnych w obu regionach są połączone z dwóch różnych obwody usługi ExpressRoute, które są używane do nawiązania połączenia SAP HANA na platformie Azure (duże wystąpień) w obu regionach platformy Azure. Nowo wprowadzonych połączenia są prostokątne czerwonych linii. Przy użyciu tych połączeń z sieciami wirtualnymi maszyn wirtualnych uruchomionych w jednej z tych sieci wirtualne są dostępne różne jednostki wystąpienia dużych HANA wdrożone w dwóch regionach. Jak pokazano na rysunku, zakłada się, że masz dwa połączenia ExpressRoute z lokalnymi dwóch regionach platformy Azure. To rozmieszczenie zaleca się ze względów odzyskiwania po awarii.

> [!IMPORTANT] 
> Jeśli używasz wielu obwody usługi ExpressRoute, dołączanie ścieżki AS i ustawienia lokalnych BGP preferencji powinien służyć do zapewnienia prawidłowego przekierowania ruchu.


