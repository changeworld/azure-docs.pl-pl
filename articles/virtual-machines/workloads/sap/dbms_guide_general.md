---
title: Zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP | Dokumentacja firmy Microsoft
description: Zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP
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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b19c0fd8af2792a4ffb877e5c6a7fc6b3f94511
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836134"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ten przewodnik stanowi część dokumentacji na temat sposobu wdrożenia i wdrożenie oprogramowania SAP w systemie Microsoft Azure. Przed przeczytaniem tego przewodnika, przeczytaj [Podręcznik planowania i implementacji][planning-guide]. W tym dokumencie opisano aspekty wdrożenia ogólnego systemów powiązane SAP DBMS na maszynach wirtualnych Microsoft Azure (maszyny wirtualne) przy użyciu infrastruktury platformy Azure jako możliwości usługi (IaaS).

Ten dokument stanowi uzupełnienie dokumentacji instalacji SAP i SAP Notes, którą reprezentują podstawowe zasoby dla instalacji i wdrożenia oprogramowania SAP na podanych platformach.

W tym dokumencie są wprowadzane kwestii związanych z systemami powiązane SAP DBMS na maszynach wirtualnych Azure. Istnieje kilka odwołań do określonych systemów DBMS, w tym rozdziale. Zamiast tego określonych systemów DBMS są obsługiwane w tym dokumencie po tym dokumencie.

## <a name="definitions"></a>Definicje
W dokumencie używane są następujące warunki:

* **IaaS**: Infrastruktura jako usługa.
* **PaaS**: Platforma jako usługa.
* **SaaS**: Oprogramowanie jako usługa.
* **Składnik SAP**: Poszczególnych aplikacji SAP, takich jak ERP i przeniesieniu jej centralnej składnika (ECC), Business Warehouse (BW), Menedżer rozwiązania lub Enterprise Portal (EP). Składniki SAP mogą być oparte na tradycyjnych technologii ABAP i Java lub na podstawie NetWeaver aplikacji takich jak obiekty biznesowych.
* **Środowisko SAP**: Co najmniej jednego składnika SAP są logicznie pogrupowane do wykonywania funkcji biznesowych, takich jak rozwój, zapewniania jakości, szkolenia, odzyskiwania po awarii lub produkcji.
* **Środowisko SAP**: Określenie to odnosi się do całego zasoby SAP klientów pozioma IT. Środowisko SAP zawiera wszystkich środowisk produkcyjnych i obniżenia.
* **SAP system**: Kombinacja warstwy system DBMS i warstwy aplikacji, na przykład system rozwoju oprogramowania SAP ERP, SAP Business Warehouse system, lub testu systemu produkcyjnego SAP CRM. W przypadku wdrożeń platformy Azure dzielenia tych dwóch warstw między lokalną i platformą Azure nie jest obsługiwane. W związku z systemem SAP jest wdrożone w środowisku lokalnym lub w jego wdrożonych na platformie Azure. Możesz wdrożyć różnych systemów środowisko SAP na platformie Azure lub lokalnie. Na przykład można wdrożyć rozwoju SAP CRM i systemy testowe na platformie Azure ale wdrożenia SAP CRM produkcji systemu lokalnego.
* **Między środowiskami lokalnymi**: W tym artykule opisano scenariusz, w którym maszyny wirtualne są wdrażane z subskrypcją platformy Azure, która ma lokacja lokacja w wielu lokacjach lub centra Azure ExpressRoute łączność między lokalnymi danymi i platformy Azure. Dokumentacja wspólnych platformy Azure, tego rodzaju wdrożenia są również opisać jako scenariuszy obejmujących wiele lokalizacji. 

    Przyczyna połączenie ma rozszerzone domen lokalnych, w lokalnej usłudze Active Directory i DNS w środowisku lokalnym na platformę Azure. Pozioma w środowisku lokalnym jest rozszerzony do zasobów platformy Azure w subskrypcji. Dla tego rozszerzenia maszyn wirtualnych może być częścią domeny w środowisku lokalnym. Użytkownicy domeny z domeny lokalnej można uzyskiwać dostęp do serwerów i uruchamiania usług na tych maszynach wirtualnych, takich jak usługi systemu DBMS. Komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożony w środowisku lokalnym i maszyn wirtualnych wdrożonych na platformie Azure jest możliwe. Ten scenariusz jest najbardziej typowym scenariuszem w używanych do wdrażania zasobów SAP na platformie Azure. Aby uzyskać więcej informacji, zobacz [planowania i projektowania dla bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Wdrożenia obejmujące systemów SAP przypadkach maszyn wirtualnych platformy Azure, systemami SAP są elementami członkowskimi domeny w środowisku lokalnym i są obsługiwane w przypadku systemów SAP w środowisku produkcyjnym. Konfiguracje obejmujące są obsługiwane w przypadku wdrażania części lub zakończyć krajobrazów SAP na platformie Azure. Jeszcze uruchomione pełne środowisko SAP na platformie Azure wymaga tych maszyn wirtualnych należeć do domeny w środowisku lokalnym i Active Directory/LDAP. 
>
> W poprzednich wersjach dokumentacji IT hybrydowych scenariuszy zostały wymienione. Termin *hybrydowego* jest ścieżką z faktu, że istnieje łączność między lokalizacjami w środowisku lokalnym i platformą Azure. W tym przypadku hybrydowych oznacza również, że maszyny wirtualne na platformie Azure należą do lokalnej usługi Active Directory.
>
>

Dokumentacji firmy Microsoft w tym artykule opisano scenariusze obejmujące nieco inaczej, szczególnie w przypadku konfiguracji o wysokiej dostępności systemu DBMS. W przypadku dokumentów związanych z SAP scenariusza obejmującego wrzała w dół do lokacja lokacja lub prywatnej [ExpressRoute](https://azure.microsoft.com/services/expressroute/) łączności i środowisko SAP dystrybuowanego między lokalną i platformą Azure.

## <a name="resources"></a>Zasoby
Istnieją inne artykuły na obciążeń SAP na platformie Azure. Rozpoczynać [obciążeń SAP na platformie Azure: Rozpoczynanie pracy](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) , a następnie wybierz obszarach swoich zainteresowań.

Poniższe uwagi SAP są powiązane SAP na platformie Azure w odniesieniu do obszaru w niniejszym dokumencie.

| Numer | Tytuł |
| --- | --- |
| [1928533] |Aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] |SAP na platformie Microsoft Azure: Wymagania wstępne dotyczące obsługi |
| [1999351] |Rozwiązywanie problemów z rozszerzonego monitorowania dla rozwiązania SAP platformy Azure |
| [2178632] |Klucz metryki monitorowania dla rozwiązania SAP w systemie Microsoft Azure |
| [1409604] |Wirtualizacja na Windows: Rozszerzone monitorowanie |
| [2191498] |SAP w systemie Linux przy użyciu platformy Azure: Rozszerzone monitorowanie |
| [2039619] |Aplikacje środowiska SAP na Microsoft Azure przy użyciu bazy danych programu Oracle: Obsługiwane produkty i wersje |
| [2233094] |DB6: Aplikacje środowiska SAP na platformie Azure przy użyciu programu IBM DB2 dla systemów Linux, UNIX i Windows: Dodatkowe informacje |
| [2243692] |Systemu Linux na platformie Microsoft Azure (IaaS) maszyny Wirtualnej: Problemy dotyczące licencji SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalacja i uaktualnianie |
| [2069760] |Oracle Linux 7.x SAP instalacji i uaktualniania |
| [1597355] |Zalecenie obszaru wymiany w systemie Linux |
| [2171857] |Oracle Database 12c: Obsługa systemu plików w systemie Linux |
| [1114181] |Oracle Database 11g: Obsługa systemu plików w systemie Linux |


Aby uzyskać informacji na temat SAP Notes dla systemu Linux, zobacz [wiki społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Należy praktyczną wiedzę na temat architektury Microsoft Azure i sposób wdrożenia i działania maszyn wirtualnych Microsoft Azure. Aby uzyskać więcej informacji, zobacz [dokumentacji platformy Azure](https://docs.microsoft.com/azure/).

Ogólnie rzecz biorąc Windows, Linux i DBMS instalacji i konfiguracji są zasadniczo taka sama jak dowolnej maszynie wirtualnej lub bez systemu operacyjnego należy zainstalować w środowisku lokalnym. Istnieją pewne systemu i architekturę implementacji decyzji związanych z zarządzaniem, które są różne, korzystając z modelu IaaS platformy Azure. W tym dokumencie wyjaśniono dotyczące architektury i systemu zarządzania różnice, by przygotować do pracy, korzystając z modelu IaaS platformy Azure.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktury magazynu maszyny wirtualnej w przypadku wdrożeń RDBMS
Aby wykonać instrukcje opisane w tym rozdziale, należy przeczytać i sprawdzić informacje znajdujące się w [w tym rozdziale] [ deployment-guide-3] z [przewodnik wdrażania][deployment-guide]. Musisz zrozumieć i wiedzieć o różnych, serię maszyn wirtualnych oraz różnice między magazynowania standard i premium, przed przeczytaniem w tym rozdziale. 

Aby dowiedzieć się więcej o usłudze Azure Storage dla maszyn wirtualnych platformy Azure, zobacz:

- [Wprowadzenie do usługi managed disks dla maszyn wirtualnych Windows Azure](../../windows/managed-disks-overview.md).
- [Wprowadzenie do usługi managed disks dla maszyn wirtualnych systemu Linux platformy Azure](../../linux/managed-disks-overview.md).

W konfiguracji podstawowej zaleca się zwykle struktury wdrożenia, którym system operacyjny, system DBMS i ostateczną SAP pliki binarne są niezależne od plików bazy danych. Zaleca się, że systemów SAP, które są uruchamiane na maszynach wirtualnych platformy Azure mają podstawowy wirtualny dysk twardy lub dysk, zainstalowane z systemem operacyjnym, pliki wykonywalne systemu zarządzania bazy danych i plików wykonywalnych SAP. 

System DBMS plików danych i dziennika są przechowywane w magazynu w warstwie standardowa lub premium storage. Są przechowywane w oddzielnych dysków i dołączone jako dyski logiczne w systemie do oryginalnego obrazu systemu operacyjnego platformy Azure maszyna wirtualna. W przypadku wdrożeń systemu Linux różne zalecenia są udokumentowane, szczególnie w przypadku oprogramowania SAP HANA.

Planując układ dysku, należy znaleźć najlepszą równowagę między tymi elementami:

* Liczba plików danych.
* Liczba dysków, które zawierają pliki.
* Przydziały operacje We/Wy pojedynczego dysku.
* Przepływność danych na dysku.
* Liczba dysków z danymi dodatkowych możliwości na rozmiar maszyny Wirtualnej.
* Ogólną przepływność magazynu maszyny Wirtualnej może zapewnić.
* Czas oczekiwania, podane dla różnych typów usługi Azure Storage.
* Maszyna wirtualna umowy SLA.

Azure wymusza przydziału operacji We/Wy na dysk z danymi. Te przydziały dotyczą różnych dysków w serwisie magazynu w warstwie standardowa i premium storage. Czas oczekiwania operacji We/Wy również różni się między tymi dwoma typami magazynu. Premium storage zapewnia lepsze opóźnienia we/wy. 

Każda z różnych typów maszyn wirtualnych ma ograniczoną liczbę dysków z danymi, które można dołączyć. Innym ograniczeniem jest tylko niektóre typy maszyn wirtualnych można usługi premium storage. Zazwyczaj użytkownik zdecyduje się użyć określonego typu maszyny Wirtualnej na podstawie wymagań procesora CPU i pamięci. Można również rozważyć operacje We/Wy, czas oczekiwania i wymagania dotyczące przepływności dysku, które zwykle są skalowane za pomocą liczbę dysków lub typu dysków magazynu premium storage. Liczba operacji We/Wy i przepływność, które mają być osiągnięte przez każdego dysku może określać rozmiar dysku, szczególnie w przypadku usługi premium storage.

> [!NOTE]
> W przypadku wdrożeń systemu DBMS firma Microsoft zaleca się korzystanie z usługi premium storage dla dowolnych danych dziennika transakcji lub ponowne wykonywanie plików. Nieważne, czy chcesz przeprowadzić wdrożenie produkcyjne lub obniżenia systemów.

> [!NOTE]
> Aby korzystać z platformy Azure z zasadami zachowania unikatowy [pojedynczy umowę SLA maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), wszystkie dyski, które są dołączone musi być typu magazynu premium, który obejmuje podstawowy dysk VHD.

> [!NOTE]
> Zawierających pliki głównej bazy danych, takich jak pliki danych i dziennika, baz danych SAP, na sprzęcie pamięci masowej, który znajduje się w innych producentów danych ze wspólną lokalizacją centra przylegające do centrów danych platformy Azure nie jest obsługiwane. W przypadku obciążeń SAP tylko magazyn, który jest reprezentowany jako natywnej usługi platformy Azure jest obsługiwane dla plików dziennika transakcji i danych baz danych SAP.

Umieszczanie plików bazy danych i plików dziennika i ponów i typu Azure Storage, możesz użyć jest definiowany przez wymagania dotyczące operacji We/Wy, opóźnienia i przepływności. Mieć wystarczającej liczby operacji We/Wy, może być zmuszony do należy użyć wielu dysków lub większy dysk magazynu premium. Jeśli używasz wielu dysków, twórz stripe oprogramowania na dyskach, które zawierają pliki danych lub dziennika i wykonaj ponownie plików. W takich przypadkach, operacje We/Wy i przepływność dysków umowy SLA podstawowego magazynu premium dysków lub maksymalną wartość IOPS osiągalna z dysków magazynu w warstwie standardowa jest kumulacyjne wynikowego zestawu usługi stripe.

Jak już wspomniano Jeśli zgodnie z wymaganiami potrzebna operacje We/Wy przekracza, co może zapewnić pojedynczego wirtualnego dysku twardego, równomierny podział liczby operacji We/Wy, które są wymagane dla plików bazy danych dla pewnej liczby wirtualnych dysków twardych. Najprostszym sposobem na rozdzielenie obciążenia operacji We/Wy na dyskach jest tworzenie stripe oprogramowania za pośrednictwem różnych dyskach. Następnie umieścić pliki danych z systemu SAP DBMS na jednostkach LUN używać poza stripe oprogramowania. Liczba dysków w stripe jest wymuszany przez żądania operacji We/Wy, potrzebom związanym z przepływnością dysku i woluminu zapotrzebowania.


- - -
> ![Windows][Logo_Windows] Windows
>
> Zalecamy użycie funkcji miejsca do magazynowania systemu Windows do tworzenia zestawów rozłożonych w wielu dysków VHD z platformy Azure. Użyj co najmniej Windows Server 2012 R2 lub Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Tworzenie macierzy RAID oprogramowania w systemie Linux są obsługiwane tylko MDADM i Menedżer woluminów logicznych (LVM). Aby uzyskać więcej informacji, zobacz:
>
> - [Konfigurowanie programowej macierzy RAID w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) przy użyciu MDADM
> - [Konfigurowanie LVM na Maszynę wirtualną systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) przy użyciu LVM
>
>

- - -

> [!NOTE]
> Ponieważ usługi Azure Storage przechowuje trzy obrazy dysków VHD, nie ma sensu do skonfigurowania nadmiarowość, jeśli użytkownik stripe. Musisz skonfigurować rozkładanie operacji We/Wy są dystrybuowane za pośrednictwem różnych wirtualnych dysków twardych.
>

### <a name="managed-or-nonmanaged-disks"></a>Dysków zarządzanych lub niezarządzanych
Konto usługi Azure storage jest konstrukcja administracyjne, a także przedmiotem ograniczeń. Ograniczenia różnią się między kontami magazynu w warstwie standardowa i kont usługi premium storage. Aby uzyskać informacji na temat możliwości i ograniczeń, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Dla magazynu w warstwie standardowa Pamiętaj, że na operacje We/Wy na konto magazynu ma limit. Zobacz wiersz, który zawiera **całkowita liczba żądań** w artykule [cele dotyczące skalowalności i wydajności usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Istnieje również początkową limit liczby kont magazynu na subskrypcję platformy Azure. Saldo wirtualne dyski twarde dla większych środowisko SAP na kontach magazynu różnych Aby uniknąć osiągnięcia limitów te konta magazynu. Jest to konieczność, gdy mówisz o kilku kilkuset maszyn wirtualnych z dyskami VHD przekracza tysiąc.

Ponieważ nie jest zalecane w przypadku wdrożeń systemu DBMS w połączeniu z obciążeniami SAP za pomocą magazynu w warstwie standardowa, odwołania i zalecenia dotyczące magazynu w warstwie standardowa są ograniczone do tym krótki [artykułu](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Aby uniknąć pracy administracyjnej planowania i wdrażania wirtualnych dysków twardych na kontach innego magazynu platformy Azure, firma Microsoft wprowadziła [usługi Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) w 2017 r. Dyski zarządzane są dostępne dla magazynu w warstwie standardowa i premium storage. Główne zalety dysków zarządzanych, w porównaniu z dysków niezarządzanych są następujące:

- Za dyski zarządzane Azure rozkłada różnych wirtualnych dysków twardych na różnych kont magazynu automatycznie w czasie wdrażania. W ten sposób konto magazynu limity dotyczące ilości danych, przepustowości operacji We/Wy i operacje We/Wy nie są trafień.
- Używanie dysków zarządzanych, usługi Azure Storage honoruje pojęcia związane z zestawami dostępności platformy Azure. Jeśli maszyna wirtualna jest częścią zestawu dostępności platformy Azure, podstawowy dysk VHD i dołączono dysk maszyny Wirtualnej są wdrażane w różnych domenach błędów i aktualizacji.


> [!IMPORTANT]
> Biorąc pod uwagę zalety usługi Azure Managed Disks, zalecamy użycie usługi Azure Managed Disks dla usługi i wdrożeń SAP DBMS na ogół.
>

Konwertowanie z dysków niezarządzanych do dysków zarządzanych, zobacz:

- [Konwertuj maszynę wirtualną Windows z dysków niezarządzanych do usługi managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Konwertuj maszynę wirtualną z systemem Linux z dysków niezarządzanych do usługi managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Buforowanie dla maszyn wirtualnych i dysków z danymi
W przypadku zainstalowania dysków maszyn wirtualnych, można wybrać, czy ruch we/wy między te dyski, znajduje się w usłudze Azure storage i maszyny Wirtualnej są buforowane. Standardowa i premium storage na użytek dwa różne technologie ten typ pamięci podręcznej.

Poniższe zalecenia założono następujące cechy operacji We/Wy systemu DBMS, standardowe:

- To przede wszystkim obciążenia odczytu dla plików danych bazy danych. Te operacje odczytu są krytyczne dla systemu DBMS na wydajność.
- Zapisywanie względem plików danych występuje w wzrosty na podstawie punktów kontrolnych lub stały strumień. Uśrednionej za jeden dzień, występują zapisy mniej niż odczytów. Przeciwnym do odczytu z plików danych te zapisy są asynchroniczne i nie wstrzymać wszystkich transakcji użytkownika.
- Jest bardzo trudno wszelkie odczyty z transakcji dziennika lub powtórz plików. Wyjątki są duże operacji We/Wy, gdy wykonujesz kopie zapasowe dziennika transakcji.
- Główne obciążenie pliki dziennika transakcji lub Powtórz to zapisy. Zależy od rodzaju obciążenia, możesz mieć operacji We/Wy tak małej, jak 4 KB, lub w innych przypadkach, operacji We/Wy pojemniki o rozmiarach 1 MB lub większej.
- Wszystkie operacje zapisu musi zostać utrwalony na dysku w sposób niezawodny.

Dla magazynu w warstwie standardowa są typy możliwe pamięci podręcznej:

* Brak
* Odczyt
* Odczyt/zapis

Aby uzyskać spójny i jednoznaczna wydajności, ustaw buforowania w magazynie standard storage dla wszystkich dysków, które zawierają pliki danych związanych z bazami danych logowania i wykonaj ponownie plików i miejsca tabeli, aby **NONE**. Buforowanie elementu bazowego wirtualnego dysku twardego może pozostać przy użyciu domyślnego.

Dla usługi premium storage istnieją następujące opcje pamięci podręcznej:

* Brak
* Odczyt
* Odczyt/zapis
* Brak i akcelerator zapisu, który jest przeznaczony tylko dla maszyn wirtualnych serii M platformy Azure
* Odczytu i akcelerator zapisu, który jest przeznaczony tylko dla maszyn wirtualnych serii M platformy Azure

Dla usługi premium storage firma Microsoft zaleca użycie **buforowania plików danych na potrzeby zapisu** SAP bazy danych, a następnie wybierz **Brak buforowania dysków pliki dziennika**.

W przypadku wdrożeń z serii M zaleca się użycia akcelerator zapisu platformy Azure dla danego wdrożenia systemu DBMS. Aby uzyskać szczegółowe informacje, ograniczenia i wdrożenia akcelerator zapisu platformy Azure, zobacz [włączyć akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Usługa Azure disks nietrwałych
Maszyny wirtualne platformy Azure oferują nietrwałych dysków, po wdrożeniu maszyny Wirtualnej. W przypadku ponownego uruchomienia maszyny Wirtualnej cała zawartość na tych dyskach zostaną wyczyszczone. Jest biorąc pod uwagę, że pliki danych i pliki dziennika i ponów baz danych powinien w żadnym wypadku nie znajdować się na tych dyskach nieutrwalona. Mogą wystąpić wyjątki dotyczące niektórych baz danych, gdzie te dyski nieutrwalona może być odpowiednie dla bazy danych tempdb i obszary tabel tymczasowych. Należy unikać używania tych dysków dla maszyn wirtualnych z serii A, ponieważ te dyski nieutrwalona jest ograniczona przepływność z tej rodziny maszyn wirtualnych. 

Aby uzyskać więcej informacji, zobacz [dysku tymczasowego na Windows maszyn wirtualnych na platformie Azure zrozumienie](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

- - -
> ![Windows][Logo_Windows] Windows
>
> Dysk D w Maszynie wirtualnej platformy Azure jest nieutrwaloną dysku, która jest wspierana przez niektóre dyski lokalne na węźle obliczeniowym platformy Azure. Ponieważ jest nieutrwaloną wszelkie zmiany wprowadzone do zawartości na dysku D zostaną utracone w przypadku, gdy maszyna wirtualna jest uruchamiany ponownie. Zmiany obejmują pliki, które były przechowywane, katalogi, które zostały utworzone i aplikacje, które zostały zainstalowane.
>
> ![Linux][Logo_Linux] Linux
>
> Maszyny wirtualne systemu Linux platformy Azure automatycznie zainstalować dysk w /mnt/resource, który jest dyskiem nieutrwalona wspierana przez dyski lokalne na węźle obliczeniowym platformy Azure. Ponieważ jest nieutrwaloną wszelkie zmiany wprowadzone do zawartości w zasobie/mnt/zostaną utracone w przypadku, gdy maszyna wirtualna jest uruchamiany ponownie. Zmiany obejmują pliki, które były przechowywane, katalogi, które zostały utworzone i aplikacje, które zostały zainstalowane.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Odporność usługi Microsoft Azure Storage
Usługa Microsoft Azure Storage przechowuje podstawowy dysk VHD z systemu operacyjnego i dyskami dołączonymi lub obiektów blob, na co najmniej trzech oddzielnych węzłów do magazynowania. Ten typ magazynu jest wywoływana magazyn lokalnie nadmiarowy (LRS). Magazyn LRS jest ustawieniem domyślnym dla wszystkich typów magazynu na platformie Azure.

Istnieją inne metody nadmiarowości. Aby uzyskać więcej informacji, zobacz [replikacja usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Usługa Premium storage jest zalecany typ magazynu dla systemu DBMS na maszynach wirtualnych i dysków, które przechowywania dziennika i bazy danych i wykonaj ponownie plików. Metoda nadmiarowości dostępne tylko dla usługi premium storage to LRS. W rezultacie musisz skonfigurować metody bazy danych w celu umożliwienia replikacji danych bazy danych do innej strefy Azure regionu lub dostępności. Metody bazy danych obejmują program SQL Server Always On programu Oracle Data Guard i replikacji systemu HANA.


> [!NOTE]
> W przypadku wdrożeń systemu DBMS na korzystanie z magazynu geograficznie nadmiarowego (GRS) nie jest zalecany dla magazynu w warstwie standardowa. GRS poważnie wpływa na wydajność i nie podlegają kolejności zapisu w różnych wirtualnych dysków twardych, które są dołączone do maszyny Wirtualnej. Nie zapewniane kolejności zapisu w różnych wirtualnych dysków twardych potencjalnie prowadzi do niespójne bazy danych po stronie docelowej replikacji. Ta sytuacja występuje, jeśli pliki bazy danych i dziennika i ponów są rozproszone między wiele wirtualnych dysków twardych, tak jak zwykle w przypadku, na stronie maszyny Wirtualnej "source".



## <a name="vm-node-resiliency"></a>Konfiguracji odporności maszyny Wirtualnej węzła
Platforma Azure oferuje kilka różnych warunków umów SLA dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz najnowszej wersji programu [umowy SLA dla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Warstwa DBMS jest zazwyczaj mają kluczowe znaczenie dla dostępności w systemie SAP, dlatego musisz zrozumieć zestawów dostępności, strefy dostępności i obsługi zdarzeń. Aby uzyskać więcej informacji dotyczących tych pojęć, zobacz [Zarządzanie dostępnością maszyn wirtualnych Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) i [Zarządzanie dostępnością maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Minimalne zalecane na potrzeby scenariuszy DBMS produkcyjnych z obciążeniami SAP jest:

- Wdróż dwie maszyny wirtualne w oddzielny zestaw dostępności w tym samym regionie platformy Azure.
- Uruchom te dwie maszyny wirtualne w tej samej sieci wirtualnej platformy Azure i karty sieciowe podłączone z tej samej podsieci.
- Użyj metody bazy danych, aby zachować rezerwy przy użyciu drugiej maszyny Wirtualnej. Metody mogą być program SQL Server Always On programu Oracle Data Guard i replikacji systemu HANA.

Możesz również wdrożyć trzecią maszynę Wirtualną w innym regionie platformy Azure i używać tych samych metod bazy danych umożliwiają określanie wartości asynchronicznego repliki w innym regionie platformy Azure.

Aby uzyskać informacje na temat sposobu konfigurowania zestawami dostępności platformy Azure, zobacz [w tym samouczku](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Zagadnienia dotyczące sieci platformy Azure
W przypadku dużych wdrożeń SAP, użyj planu z [wirtualnego centrum danych Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Użyj go dla przypisania uprawnienia i konfiguracji oraz roli sieci wirtualnej do różnych części organizacji.

Następujące najlepsze rozwiązania są wyniku setkami wdrożeń klienta:

- Sieci wirtualne, których aplikacja SAP jest wdrażana w nie mają dostępu do Internetu.
- Bazy danych, maszyny wirtualne uruchomione w tej samej sieci wirtualnej jako warstwy aplikacji.
- Maszyny wirtualne w sieci wirtualnej mają statyczny przydziału prywatnego adresu IP. Aby uzyskać więcej informacji, zobacz [typy adresów IP i metody alokacji na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Ograniczenia routingu do i z maszyn wirtualnych z systemem DBMS *nie* zestawu przy użyciu zapór zainstalowanych na lokalnych maszynach wirtualnych z systemem DBMS. Zamiast tego routing ruchu jest zdefiniowana za pomocą [sieciowe grupy zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Aby oddzielić i izolacji ruchu do maszyny Wirtualnej systemu DBMS, należy przypisać różne karty sieciowe z maszyną wirtualną. Każdą kartą Sieciową pobiera inny adres IP i każdej karty Sieciowej jest przypisany do podsieci innej sieci wirtualnej. Każdej podsieci ma różne reguły sieciowej grupy zabezpieczeń. Izolacja i rozdzielania ruchu sieciowego jest miarą dla routingu. Nie służy do ustawiania przydziałów dla przepustowość sieci.

> [!NOTE]
> Oznacza przypisanie statycznych adresów IP za pośrednictwem platformy Azure przypisać je do poszczególnych wirtualnych kart sieciowych. Nie przypisuj statycznych adresów IP w ramach systemu operacyjnego gościa do wirtualnej karty sieciowej. Niektórych usług platformy Azure, takich jak usługa Azure Backup opierają się na fakcie, że w co najmniej podstawowego wirtualnej karty Sieciowej jest ustawiony, DHCP, a nie statycznych adresów IP. Aby uzyskać więcej informacji, zobacz [kopii zapasowej maszyny wirtualnej Azure Rozwiązywanie problemów z](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Aby przypisać wiele statycznych adresów IP do maszyny Wirtualnej, należy przypisać wiele wirtualnych kart sieciowych do maszyny Wirtualnej.
>


> [!IMPORTANT]
> Konfigurowanie [sieciowych urządzeń wirtualnych](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP DBMS warstwy programu SAP NetWeaver - Hybris- lub system SAP S/4HANA-based nie jest obsługiwane. To ograniczenie jest powodów funkcjonalność i wydajność. Ścieżka komunikacji między warstwą aplikacji SAP i warstwy system DBMS musi być bezpośrednich. Ograniczenie nie obejmuje [grupy zabezpieczeń aplikacji (ASG) i reguły sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview) Jeśli te reguły ASG i sieciowej grupy zabezpieczeń zezwalają ścieżki bezpośrednią komunikację. 
>
> Inne scenariusze, w których wirtualne urządzenia sieciowe nie są obsługiwane należą:
>
> * Ścieżki komunikacji między maszynami wirtualnymi platformy Azure, które reprezentują program Pacemaker w systemie Linux węzłów i klastra interwencja urządzeń zgodnie z opisem w [wysoką dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Ścieżki komunikacji między maszynami wirtualnymi platformy Azure i serwera plików skalowalnego w poziomie (SOFS) systemu Windows Server Ustaw zgodnie z opisem w górę [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Wirtualne urządzenia sieciowe w zaufanych ścieżek komunikacji, można łatwo double opóźnienie sieci między partnerami dwóch komunikacji. Mogą również ograniczyć przepustowość w krytyczne ścieżki między warstwą aplikacji SAP i warstwy system DBMS. W niektórych przypadkach klient wirtualnych urządzeń sieciowych może spowodować program Pacemaker w systemie Linux klastrów nie powiedzie się. Są to przypadki, gdzie komunikacji między węzłami klastra program Pacemaker w systemie Linux, komunikują się na ich urządzenia interwencja za pośrednictwem wirtualnego urządzenia sieciowego.
>

> [!IMPORTANT]
> Innego projektu, który firmy *nie* jest oddzielenie warstwy aplikacji SAP i warstwą DBMS w różnych sieciach wirtualnych platformy Azure, które nie są obsługiwane [skomunikowane równorzędnie](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ze sobą. Firma Microsoft zaleca segregowanie warstwy aplikacji SAP i warstwy system DBMS za pomocą podsieci w sieci wirtualnej platformy Azure zamiast przy użyciu różnych sieciach wirtualnych platformy Azure. 
>
> Jeśli zdecydujesz się na zalecenia i zamiast tego oddzielenie czynności związanych z dwoma warstwami w różnych sieciach wirtualnych, musi być dwie sieci wirtualne [skomunikowane równorzędnie](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Należy pamiętać, że ruchem sieciowym między dwoma [skomunikowane równorzędnie](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sieciami wirtualnymi platformy Azure podlega postanowieniom koszty transferu. Wolumin olbrzymich ilościach danych, który składa się z wielu terabajtów, są wymieniane między warstwy aplikacji SAP i jej systemu DBMS. Jeśli warstwa aplikacji SAP i warstwy system DBMS są rozdzielone między dwiema równorzędnymi sieciami wirtualnymi platformy Azure, może wzrosnąć znaczne koszty.

Ustaw dwie maszyny wirtualne użycia dla wdrażania systemu DBMS, w ramach dostępności platformy Azure w środowisku produkcyjnym. Również używać oddzielnych routingu dla warstwy aplikacji SAP i zarządzanie i operacje ruch do dwóch maszyn wirtualnych systemu DBMS. Zobacz poniższy obraz:

![Diagram przedstawiający dwóch maszyn wirtualnych w dwie podsieci](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Użyj usługi Azure Load Balancer w celu przekierowania ruchu
Korzystanie z prywatnej wirtualne adresy IP używane w funkcje, takie jak SQL Server Always On lub replikacji systemu HANA wymaga konfiguracji usługi Azure load balancer. Moduł równoważenia obciążenia używa sondy portów, aby określić aktywnego węzła DBMS i kierować ruchem wyłącznie do tego węzła bazy danych dla aktywnej. 

W przypadku pracy w trybie failover węzeł bazy danych, nie ma potrzeby dla aplikacji SAP zmienić konfigurację. Zamiast tego najbardziej typowe architektury aplikacji SAP ponownie połączyć przeciwko wirtualnej prywatny adres IP. W międzyczasie modułu równoważenia obciążenia reaguje na pracę awaryjną węzła przez przekierowywanie ruchu względem wirtualnej prywatny adres IP do drugiego węzła.

Platforma Azure oferuje dwa różne [jednostki SKU modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): podstawowa jednostka SKU i standardowej jednostki SKU. Chyba że chcesz wdrożyć w różnych strefach dostępności platformy Azure, podstawowego modułu równoważenia obciążenia jednostki SKU jest prawidłowo.

Czy ruch między maszynami wirtualnymi systemu DBMS i warstwy aplikacji SAP, które są zawsze kierowane za pośrednictwem modułu równoważenia obciążenia przez cały czas? Odpowiedź zależy od tego, jak skonfigurować moduł równoważenia obciążenia. 

W tej chwili ruch przychodzący do maszyny Wirtualnej z systemem DBMS zawsze odbywa się za pomocą modułu równoważenia obciążenia. Wychodzący ruch trasy z maszyny Wirtualnej systemu DBMS do warstwy aplikacji, które maszyny Wirtualnej zależy od konfiguracji modułu równoważenia obciążenia. 

Moduł równoważenia obciążenia zapewnia możliwość DirectServerReturn. Jeśli ta opcja jest skonfigurowana, ruch skierowany z maszyny Wirtualnej systemu DBMS na warstwie aplikacji SAP jest *nie* kierowane za pośrednictwem modułu równoważenia obciążenia. Zamiast tego trafia bezpośrednio do warstwy aplikacji. Jeśli nie zostanie skonfigurowane DirectServerReturn, ruch powrotny z warstwą aplikacji SAP odbywa się za pomocą modułu równoważenia obciążenia.

Firma Microsoft zaleca skonfigurowanie DirectServerReturn w połączeniu z modułami równoważenia obciążenia, które są pozycjonowane między warstwą aplikacji SAP i warstwy system DBMS. Ta konfiguracja ogranicza opóźnienia sieci między warstwami.

Na przykład sposobu konfigurowania tę konfigurację przy użyciu programu SQL Server Always On zobacz [Konfigurowanie odbiornika ILB dla zawsze włączonych grup dostępności na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Jeśli używasz opublikowane szablony JSON usługi GitHub jako odwołanie dla wdrożeń infrastruktury SAP na platformie Azure, badania to [szablonu systemu 3-warstwowej SAP](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). W tym szablonie również widoczne poprawne ustawienia dla modułu równoważenia obciążenia.

### <a name="azure-accelerated-networking"></a>Azure Accelerated Networking
Aby dodatkowo zmniejszyć opóźnienie sieciowe między maszynami wirtualnymi platformy Azure, firma Microsoft zaleca, możesz wybrać [usługę Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Użyć go podczas wdrażania maszyn wirtualnych platformy Azure w przypadku obciążeń SAP, szczególnie w przypadku warstwy aplikacji SAP i SAP DBMS warstwy.

> [!NOTE]
> Nie wszystkie typy maszyn wirtualnych obsługują przyspieszonej sieci. Poprzednim artykule wymieniono typy maszyn wirtualnych, które obsługują przyspieszonej sieci.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> Aby dowiedzieć się, jak wdrożyć maszyny wirtualne z Accelerated Networking for Windows, zobacz [Utwórz maszynę wirtualną Windows dzięki przyspieszonej sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Aby uzyskać więcej informacji na temat dystrybucji systemu Linux, zobacz [Utwórz maszynę wirtualną systemu Linux przy użyciu Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

- - -

> [!NOTE]
> W przypadku SUSE i Red Hat, Oracle Linux przyspieszonej sieci jest obsługiwana przy użyciu najnowsze wersje. Starsze wersje systemu SLES 12 z dodatkiem SP2 lub systemu RHEL 7.2 nie obsługują usługę Azure Accelerated Networking.
>


## <a name="deployment-of-host-monitoring"></a>Wdrożenie hosta monitorowania
Do użytku produkcyjnego aplikacji SAP na maszynach wirtualnych platformy Azure środowisko SAP wymaga możliwość skorzystania z hosta dane monitorowania z hostów fizycznych, działających maszyn wirtualnych platformy Azure. Określonego poziomu poprawki SAP Agent hosta jest wymagana, pozwalający tę funkcję w SAPOSCOL i Agent hosta SAP. Poziom poprawki dokładnie jest udokumentowany w Uwaga SAP [1409604].

Aby uzyskać więcej informacji na temat wdrażania składników, które dostarczają dane hosta SAPOSCOL i Agent hosta SAP i zarządzanie cyklem życia tych składników, zobacz [przewodnik wdrażania][deployment-guide].


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat określonego systemu DBMS zobacz:

- [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu SQL Server dla obciążenia SAP](dbms_guide_sqlserver.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines oprogramowania Oracle dla obciążenia SAP](dbms_guide_oracle.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM DB2 dla obciążenia SAP](dbms_guide_ibm.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP](dbms_guide_sapase.md)
- [SAP maxDB, na żywo z pamięci podręcznej i wdrażania serwera zawartości na platformie Azure](dbms_guide_maxdb.md)
- [Przewodnik obsługi oprogramowania SAP HANA na platformie Azure](hana-vm-operations.md)
- [SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](sap-hana-availability-overview.md)
- [Przewodniku kopia zapasowa dla oprogramowania SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-backup-guide.md)

