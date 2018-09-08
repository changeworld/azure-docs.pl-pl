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
ms.date: 09/06/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e46503f8dc97f58db1cd5acfd2122e2895fb15b0
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162312"
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

Ten przewodnik stanowi część dokumentacji na implementowanie i wdrażanie oprogramowania SAP w systemie Microsoft Azure. Przed przeczytaniu tego przewodnika przeczytaj [Podręcznik planowania i implementacji][planning-guide]. W tym dokumencie opisano aspekty wdrożenia ogólnego systemów powiązane SAP DBMS na Microsoft Azure Virtual Machines (VMs) przy użyciu infrastruktury platformy Azure jako możliwości usługi (IaaS).

Ten dokument stanowi uzupełnienie dokumentacji instalacji SAP i SAP Notes, którą reprezentują podstawowe zasoby dla instalacji i wdrożenia oprogramowania SAP na podanych platformach.

W tym dokumencie są wprowadzane kwestii związanych z systemami powiązane SAP DBMS na maszynach wirtualnych Azure. Istnieje kilka odwołań do określonych systemów DBMS, w tym rozdziale. Zamiast tego określonych systemów DBMS są obsługiwane w tym dokumencie po tym dokumencie.

## <a name="definitions-upfront"></a>Definicje ponoszonych z góry kosztów
W dokumencie używane są następujące terminy:

* IaaS: Infrastruktura jako usługa.
* PaaS: Platforma jako usługa.
* SaaS: Oprogramowanie jako usługa.
* SAP składnik: Poszczególnych aplikacji SAP ECC, BW, Menedżer rozwiązania lub EP.  Składniki SAP mogą być oparte na tradycyjnych technologii ABAP i Java lub aplikacji innych niż NetWeaver na podstawie takich jak obiekty biznesowych.
* Środowisko SAP: co najmniej jednego składnika SAP logicznie pogrupowane do wykonywania funkcji biznesowych, takich jak rozwój, QAS, szkolenia, odzyskiwania po awarii lub produkcji.
* Środowisko SAP: Określenie to odnosi się do całego zasoby SAP klientów pozioma IT. Środowisko SAP obejmuje wszystkie produkcji i środowisk nieprodukcyjnych.
* SAP System: Kombinacja warstwy system DBMS i warstwy aplikacji, na przykład SAP ERP i przeniesieniu jej rozwoju systemu SAP BW system testowy, system produkcyjny SAP CRM, itp. W przypadku wdrożeń platformy Azure go nie jest obsługiwane do dzielenia tych dwóch warstw między lokalną i platformą Azure. To oznacza, że system SAP jest wdrożony w środowisku lokalnym lub jest ona wdrożona na platformie Azure. Można jednak wdrożyć różnych systemów środowisko SAP na platformie Azure lub lokalnie. Na przykład możesz wdrożyć rozwoju SAP CRM i systemy testowe platformie Azure, ale SAP CRM produkcji systemu lokalnego.
* Między lokalizacjami: w tym artykule opisano scenariusz, w której maszyny wirtualne są wdrażane z subskrypcją platformy Azure, site to site, obejmujące wiele lokacji lub połączenia usługi ExpressRoute między zasobom w środowisku lokalnym i platformą Azure. Dokumentacja wspólnych platformy Azure, tego rodzaju wdrożenia są również opisać jako scenariuszy obejmujących wiele lokalizacji. Przyczyna połączenie ma rozszerzone domen lokalnych, w lokalnej usłudze Active Directory i DNS w środowisku lokalnym na platformę Azure. Pozioma w środowisku lokalnym jest rozszerzony do zasobów platformy Azure w subskrypcji. Problemy to rozszerzenie, maszyn wirtualnych może być częścią domeny w środowisku lokalnym. Użytkownicy domeny lokalnej domeny mogą uzyskiwać dostęp do serwerów i można uruchomić usługi na tych maszynach wirtualnych (np. usługi DBMS). Komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożony w środowisku lokalnym i maszyn wirtualnych wdrożonych na platformie Azure jest możliwe. Ten scenariusz jest najbardziej typowym scenariuszem wdrażania zasobów SAP na platformie Azure. Aby uzyskać więcej informacji, zobacz [planowania i projektowania dla bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Wdrożenia obejmujące systemów SAP, w których członkowie lokalnej domeny usługi Azure Virtual Machines z systemami SAP są obsługiwane dla systemów SAP w środowisku produkcyjnym. Konfiguracje obejmujące są obsługiwane w przypadku wdrażania części lub zakończyć krajobrazów SAP na platformie Azure. Jeszcze uruchomione pełne środowisko SAP na platformie Azure wymaga posiadanie tych maszyn wirtualnych, które są częścią domeny w środowisku lokalnym i usługą AD/LDAP. W wcześniejsze wersje dokumentacji, IT hybrydowych scenariuszy zostały wymienione, gdy termin *hybrydowego* jest ścieżką z faktu, że istnieje łączność między lokalizacjami w środowisku lokalnym i platformą Azure. W tym przypadku *hybrydowego* oznacza, że maszyny wirtualne na platformie Azure należą do lokalnej usługi Active Directory.
> 
> 

Dokumentacji firmy Microsoft w tym artykule opisano scenariusze obejmujące nieco inaczej, szczególnie w przypadku systemu DBMS zaświadczanie o kondycji konfiguracji. W przypadku dokumentów związanych z SAP scenariusza obejmującego wrzała w dół do lokacja lokacja lub prywatnego [ExpressRoute](https://azure.microsoft.com/services/expressroute/) łączności i na fakt, że środowisko SAP jest rozdzielona między lokalną i platformą Azure.

## <a name="resources"></a>Zasoby
Są wydawane różne artykuły dotyczące obciążeń SAP na platformie Azure.  Zalecane jest, aby rozpocząć w [obciążeń SAP na platformie Azure — wprowadzenie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) a następnie wybierz obszar zainteresowań

Poniższe uwagi SAP odnoszą się do SAP na platformie Azure dotyczące obszaru omówione w tym dokumencie:

| Numer | Stanowisko |
| --- | --- |
| [1928533] |Aplikacje środowiska SAP na platformie Azure: obsługiwane produkty i maszyny Wirtualnej platformy Azure |
| [2015553] |SAP na platformie Microsoft Azure: wymagania wstępne dotyczące obsługi |
| [1999351] |Rozwiązywanie problemów z rozszerzonego monitorowania platformy Azure dla rozwiązania SAP |
| [2178632] |Klucz metryki monitorowania dla rozwiązania SAP na platformie Microsoft Azure |
| [1409604] |Wirtualizacji na Windows: Enhanced Monitoring |
| [2191498] |SAP w systemie Linux przy użyciu platformy Azure: Enhanced Monitoring |
| [2039619] |Aplikacje środowiska SAP na Microsoft Azure przy użyciu bazy danych programu Oracle: obsługiwane produkty i wersje |
| [2233094] |DB6: Aplikacje środowiska SAP na platformie Azure przy użyciu programu IBM DB2 dla systemów Linux, UNIX i Windows — informacje dodatkowe |
| [2243692] |Systemu Linux w systemie Microsoft Azure (IaaS) maszyn wirtualnych: problemy dotyczące licencji SAP |
| [1984787] |Systemie SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalowanie i uaktualnianie |
| [2069760] |Oracle Linux 7.x SAP instalacji i uaktualniania |
| [1597355] |Zalecenie obszaru wymiany w systemie Linux |
| [2171857] |Oracle Database 12c - Obsługa systemu plików w systemie Linux |
| [1114181] |Bazą danych Oracle 11g - Obsługa systemu plików w systemie Linux |


Przeczytaj również [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zawierający wszystkie uwagi SAP dla systemu Linux.

Należy mieć praktyczną wiedzę na temat o architektura platformy Microsoft Azure i sposób wdrożenia i działania systemu Microsoft Azure Virtual Machines. Więcej informacji można znaleźć [dokumentacji platformy Azure](https://docs.microsoft.com/azure/).

Chociaż Omawiając IaaS, ogólnie rzecz biorąc Windows, Linux i DBMS instalacji i konfiguracji są zasadniczo taka sama jak dowolnej maszynie wirtualnej lub bez systemu operacyjnego, możesz zainstalować w środowisku lokalnym. Istnieją jednak pewne architekturę i system zarządzania implementacji decyzji, które są inne w przypadku modelu IaaS platformy Azure. Ten dokument ma na celu wyjaśnić dotyczące architektury i systemu zarządzania różnice, które muszą być przygotowane, na korzystając z modelu IaaS platformy Azure.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktury magazynu maszyny wirtualnej w przypadku wdrożeń RDBMS
Aby można było wykonać instrukcje opisane w tym rozdziale, jest to konieczne zrozumieć, co zostało przedstawione [to] [ deployment-guide-3] rozdziału [przewodnik wdrażania][deployment-guide]. Wiedzę na temat różnych serię maszyn wirtualnych oraz różnice i różnic Standard platformy Azure i [usługi Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) powinien rozumieć i znane przed odczytaniem w tym rozdziale.

Pod względem usługi Azure Storage dla maszyn wirtualnych platformy Azure należy się zapoznać z artykułami:

- [Magazyn dysków dla maszyn wirtualnych Windows Azure — informacje](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds)
- [Magazyn dysków dla maszyn wirtualnych systemu Linux platformy Azure — informacje](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds) 

W konfiguracji podstawowej zaleca się zwykle strukturę wdrażania, w którym system operacyjny, system DBMS i ostateczną SAP pliki binarne są niezależne od plików bazy danych. Dlatego zaleca się systemów SAP uruchomionym w usłudze Azure Virtual Machines wirtualnego dysku twardego podstawowy (lub dysku) zainstalowane z systemem operacyjnym, pliki wykonywalne systemu zarządzania bazy danych i plików wykonywalnych SAP. System DBMS plików danych i dziennika są przechowywane w usłudze Azure Storage (wersja standardowa lub Premium Storage) w oddzielnych dyskach i dołączone jako dyski logiczne w systemie do oryginalnego obrazu systemu operacyjnego platformy Azure maszyna wirtualna. Szczególnie w przypadku wdrożeń systemu Linux może być różne zalecenia udokumentowane. Zwłaszcza w części dotyczącej platformy SAP HANA.  

Podczas planowania układ dysku, należy znaleźć najlepszą równowagę między następujące elementy:

* Liczba plików danych.
* Liczba dysków, które zawierają pliki.
* Przydziały operacje We/Wy pojedynczego dysku.
* Przepływność danych na dysku.
* Liczba dysków z danymi dodatkowych możliwości na rozmiar maszyny Wirtualnej.
* Ogólną przepływność magazynu maszyny Wirtualnej może zapewnić.
* Czas oczekiwania, podane dla różnych typów usługi Azure Storage.
* Umowy SLA maszyn wirtualnych

Azure wymusza przydziału operacji We/Wy na dysk z danymi. Te przydziały dotyczą różnych dysków w serwisie Azure magazynu w warstwie standardowa i Premium Storage. Czas oczekiwania operacji We/Wy również różni się między tymi dwoma typami magazynu.  Dzięki usłudze Premium Storage, zapewniając czynniki lepiej czas oczekiwania operacji We/Wy. Każda z różnych typów maszyn wirtualnych ma ograniczoną liczbę dysków z danymi, które można dołączyć. Ograniczenie innego jest, że niektóre typy maszyn wirtualnych mogą korzystać z usługi Azure Premium Storage. W rezultacie decyzji dla określonego typu maszyny Wirtualnej może nie tylko uzależniona wymagania Procesora i pamięci, ale także przez operacje We/Wy, opóźnienia i dysku wymagań w zakresie przepływności, które zwykle są skalowane za pomocą liczbę dysków lub typu dysków usługi Premium Storage. Szczególnie w przypadku usługi Premium Storage rozmiaru dysku również mogą być definiowane według liczby operacji We/Wy i przepływność, który ma być osiągnięte przez każdego dysku.

> [!NOTE]
> W przypadku wdrożeń systemu DBMS zdecydowanie zaleca się użycie magazynu Premium Storage wszelkie dane, dziennik transakcji lub plików ponownego wykonywania. Dzięki temu nie ma znaczenia, czy mają zostać wdrożone w środowisku produkcyjnym lub systemów nieprodukcyjnych.

> [!NOTE]
> Aby można było korzystać z platformy Azure przez unikatowy [pojedynczy umowę SLA maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) wszystkie dyski dołączone muszą być typu usługi Azure Premium Storage, w tym podstawowy dysk VHD.
>

Umieszczanie plików bazy danych i plików dziennika/ponownego wykonywania i typ magazynu platformy Azure, powinien być zdefiniowany przez wymagania dotyczące operacji We/Wy, opóźnienia i przepływności. Aby mogła mieć wystarczającej liczby operacji We/Wy, może być zmuszony do korzystać z wielu dysków lub użyj większy dysk usługi Premium Storage. W przypadku używania wielu dysków, czy tworzysz stripe oprogramowania na dyskach, które zawierają pliki danych lub plików dziennika/Ponów. W takiej sytuacji operacje We/Wy i przepływność dysków umowy SLA podstawowych dysków usługi Premium Storage lub Maksymalna osiągalna dysków operacje We/Wy z usługi Azure Standard Storage są kumulacyjne wynikowego zestawu usługi stripe. 

Jak już wspomniano Jeśli Twoje wymagania operacje We/Wy przekraczają, co może zapewnić pojedynczego wirtualnego dysku twardego, musisz równomierny podział liczby operacji We/Wy jest wymagany dla plików bazy danych dla pewnej liczby wirtualnych dysków twardych. Najprostszym sposobem na rozdzielenie obciążenia operacji We/Wy na dyskach jest tworzenie stripe oprogramowania za pośrednictwem różnych dyskach. Następnie umieścić pliki danych z systemu SAP DBMS na jednostkach LUN używać poza stripe oprogramowania. Liczba dysków w stripe jest wymuszany przez żądania operacji We/Wy, potrzebom związanym z przepływnością dysku i woluminu zapotrzebowania. 


- - -
> ![Windows][Logo_Windows] Windows
> 
> Firma Microsoft zaleca tworzenie takich zestawów rozłożonych w wielu dysków VHD z platformy Azure za pomocą magazynowania w systemie Windows. Zalecane jest użycie co najmniej Windows Server 2012 R2 lub Windows Server 2016.
> 
> ![Linux][Logo_Linux] Linux
> 
> Tworzenie macierzy RAID oprogramowania w systemie Linux są obsługiwane tylko MDADM i LVM (Menedżer woluminów logicznych). Aby uzyskać więcej informacji przeczytaj następujące artykuły:
> 
> - [Konfigurowanie RAID oprogramowania w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) przy użyciu MDADM
> - [Konfigurowanie LVM na Maszynę wirtualną systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) przy użyciu LVM
> 
> 

- - -
 
> [!NOTE]
> Ponieważ usługi Azure Storage może być utrzymywanie trzy obrazy dysków VHD, go nie ma sensu skonfigurować stosowanie nadmiarowości. Wystarczy skonfigurować stosowanie tak, że operacji We/Wy są wprowadzenie rozpowszechniane za pośrednictwem różnych wirtualnych dysków twardych.
>

### <a name="managed-or-non-managed-disks"></a>Dysków zarządzanych lub niezarządzanych
Konto usługi Azure Storage jest nie tylko konstrukcja administracyjne, ale również przedmiotem ograniczeń. Ograniczenia różnią się między Azure Standard Storage Accounst i kont usługi Azure Premium Storage. Dokładnych możliwości i ograniczenia są wymienione w artykule [usługi Azure Storage dotyczące skalowalności i elementy docelowe wydajności](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)

Dla usługi Azure Standard Storage, jest ważne, pamiętaj, że istnieje limit na operacje We/Wy na konto magazynu (wiersz zawierający **całkowita liczba żądań** w artykule [usługi Azure Storage cele skalowalności i wydajności](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)). Ponadto jest początkowa limit liczby kont magazynu na subskrypcję platformy Azure. W związku z tym należy zrównoważyć wirtualne dyski twarde dla większych środowisko SAP na kontach innego magazynu, aby uniknąć osiągnięcia limitów te konta magazynu. Tedious pracy, gdy mówimy o kilku kilkuset maszyny wirtualne z więcej niż tysięcy wirtualnych dysków twardych. 

Ponieważ nie jest to zalecane do użycia usługi Azure Standard Storage dla systemu DBMS wdrożeń w połączeniu z obciążeniami SAP, odwołania i zalecenia dotyczące usługi Azure Standard storage są ograniczone do tym krótki [artykułu](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Aby uniknąć pracy administracyjnej planowania i wdrażania wirtualnych dysków twardych na różnych kontach magazynu Azure, firma Microsoft wprowadziła, co jest nazywane [Managed Disks](https://azure.microsoft.com/services/managed-disks/) w 2017 r. Managed Disks są dostępne dla usługi Azure Standard Storage, a także usługi Azure Premium Storage. Główne zalety w porównaniu do listy dysków niezarządzanych, takich jak dyski Managed Disks:

- Dla dysków Managed Disks platforma Azure dystrybuuje różnych wirtualnych dysków twardych na różnych kont magazynu z automatycznie w czasie wdrażania i tym samym pozwala uniknąć osiągnięcia limitów konta usługi Azure Storage w zakresie ilości danych, przepustowość operacji We/Wy i operacje We/Wy.
- Przy użyciu dysków zarządzanych, usługi Azure Storage jest zapewniane pojęcia związane z zestawami dostępności Azure i z tym wdraża podstawowy dysk VHD i dołączono dysk maszyny Wirtualnej w różnych domenach błędów i aktualizacji, jeśli maszyna wirtualna jest częścią zestawu dostępności platformy Azure.


> [!IMPORTANT]
> Biorąc pod uwagę zalety usługi Azure Managed Disks, jest zalecane na potrzeby usługi Azure Managed Disks usługi i wdrożeń SAP DBMS na ogół.
>

Aby konwertowanie z dysków niezarządzanych do dysków zarządzanych, zapoznaj się z tych artykułów:

- [Konwertuj maszynę wirtualną Windows z dysków niezarządzanych do usługi managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Konwertuj maszynę wirtualną z systemem Linux z dysków niezarządzanych do usługi managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Buforowanie dla maszyn wirtualnych i dysków z danymi
W przypadku zainstalowania dysków maszyn wirtualnych, można wybrać, czy ruch we/wy między te dyski, znajduje się w usłudze Azure storage i maszyny Wirtualnej są buforowane. Usługa Azure Standard i Premium Storage na użytek dwa różne technologie ten typ pamięci podręcznej. 

Poniższe zalecenia są zakładając, że te cechy operacji We/Wy systemu DBMS, standardowe:

- Przede wszystkim jest do odczytu obciążenia względem danych plików bazy danych. Te operacje odczytu są wydajność krytycznych dla systemu DBMS
- Zapisywanie względem plików danych mają bogate doświadczenie w wzrosty na podstawie punktów kontrolnych lub stały strumień. Niemniej jednak uśrednione dnia, operacje zapisu są mniejszy niż operacji odczytu. W przeciwnym do odczytu z plików danych, te zapisy są asynchroniczne i nie wstrzymują wszystkich transakcji użytkownika.
- Jest bardzo trudno wszelkie odczyty z transakcji dziennika lub powtórz plików. Wyjątki są duże operacji We/Wy podczas wykonywania kopii zapasowej dziennika transakcji. 
- Główne obciążenia względem plików dziennika transakcji lub Powtórz to zapisy. Zależy od rodzaju obciążenia, możesz mieć operacji We/Wy tak małej, jako 4 KB lub w innych przypadkach rozmiarów operacji We/Wy 1 MB lub większej.
- Wszystkie operacje zapisu muszą zostać utrwalony na dysku w sposób niezawodny

Dla usługi Azure Standard Storage dostępne są następujące typy możliwe pamięci podręcznej:

* Brak
* Odczyt
* Odczyt/zapis

Aby uzyskać spójny i jednoznaczna wydajności, należy skonfigurować buforowanie na usługi Azure Standard Storage dla wszystkich dysków zawierających **pliki danych związanych z bazami danych dziennika/ponawianie plików i miejsca do tabeli "NONE"**. Buforowanie elementu bazowego wirtualnego dysku twardego może pozostać przy użyciu domyślnego.

Dla usługi Azure Premium Storage istnieją następujące opcje pamięci podręcznej:

* Brak
* Odczyt 
* Odczyt/zapis 
* Brak i akcelerator zapisu (tylko dla maszyn wirtualnych platformy Azure serii M)
* Odczyt + zapis akceleratora (tylko dla maszyn wirtualnych platformy Azure serii M)

Zalecenia dotyczące usługi Azure Premium Storage jest wykorzystanie **buforowania plików danych na potrzeby zapisu** bazy danych SAP i wybierz opcję **Brak buforowania dysków pliki dziennika**.

W przypadku serii M wdrożeń zaleca akcelerator zapisu platformy Azure na potrzeby usługi wdrażania systemu DBMS. Aby uzyskać szczegółowe informacje, ograniczenia i wdrożenia akcelerator zapisu platformy Azure zapoznaj się z dokumentem [akceleratorem zapisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator). 


### <a name="azure-non-persistent-disks"></a>Usługi Azure disks — trwały
Maszyny wirtualne platformy Azure oferują dyski trwałe, po wdrożeniu maszyny Wirtualnej. W przypadku ponownego uruchomienia maszyny Wirtualnej zostaną wyczyszczone całą zawartość na tych dyskach. To dlatego, biorąc pod uwagę, że pliki danych i pliki dziennika/ponów baz danych powinien w żadnym wypadku nie znajdować się na tych dyskach nietrwałe. Mogą wystąpić wyjątki dotyczące niektórych baz danych, gdzie te dyski nieutrwaloną może być odpowiednie dla bazy danych tempdb i obszary tabel tymczasowych. Należy jednak unikać przy użyciu tych dysków dla maszyn wirtualnych z serii A, ponieważ te dyski nieutrwaloną jest ograniczona przepływność z tej rodziny maszyn wirtualnych. Aby uzyskać więcej informacji, przeczytaj artykuł [opis dysku tymczasowego na maszynach wirtualnych Windows Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

- - -
> ![Windows][Logo_Windows] Windows
> 
> Dysku D:\ w Maszynie wirtualnej platformy Azure jest nieutrwaloną dysku, która jest wspierana przez niektóre dyski lokalne na węźle obliczeniowym platformy Azure. Ponieważ jest nieutrwaloną, oznacza to, że wszelkie zmiany wprowadzone do zawartości na dysku D:\, zostaną utracone, gdy maszyna wirtualna jest uruchamiany ponownie. Przez "wszelkie zmiany", takich jak pliki przechowywane, katalogi utworzone, zainstalowane, aplikacje itd.
> 
> ![Linux][Logo_Linux] Linux
> 
> Maszyny wirtualne systemu Linux platformy Azure automatycznie zainstalować dysk w /mnt/resource, który jest dyskiem nietrwałych wspierana przez dyski lokalne na węźle obliczeniowym platformy Azure. Ponieważ jest nieutrwaloną, oznacza to, wszelkie zmiany wprowadzone do zawartości w /mnt/resource zostaną utracone, gdy maszyna wirtualna jest uruchamiany ponownie. Za wszelkie zmiany, takie jak pliki przechowywane, katalogi utworzone, zainstalowane, aplikacje itd.
> 
> 

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Odporność usługi Microsoft Azure Storage
Usługa Microsoft Azure Storage przechowuje podstawowy dysk VHD (z systemem operacyjnym) i dołączonych dysków lub obiektów blob na co najmniej trzech oddzielnych węzłów do magazynowania. Fakt ten nosi nazwę lokalny magazyn nadmiarowy (LRS). Magazyn LRS jest domyślnie dla wszystkich typów magazynu na platformie Azure. 

Dostępnych jest kilka więcej nadmiarowości metod, które są wszystkie opisane w artykule [replikacja usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Począwszy od usługi Azure Premium Storage, która jest zalecany typ magazynu dla systemu DBMS na maszynach wirtualnych i dysków, w których są przechowywane pliki bazy danych i dziennika/ponawiania, dostępny tylko metody to LRS. W rezultacie musisz skonfigurować metody bazy danych, takich jak SQL Server Always On, środowiska Oracle Data Guard i replikacji systemu HANA do włączenia replikacji danych bazy danych do innego regionu platformy Azure lub do innej strefy dostępności platformy Azure.


> [!NOTE]
> W przypadku wdrożeń systemu DBMS użycie magazyn geograficznie nadmiarowy jako dostępny za pomocą usługi Azure Standard Storage nie jest zalecane, ponieważ ma wpływ na zmniejszenie wydajności, a nie przestrzegał kolejności zapisu w różnych wirtualnych dysków twardych, które są dołączone do maszyny Wirtualnej. Fakt nie zapewniane kolejności zapisu w różnych wirtualnych dysków twardych ponosi duże ryzyko do znajdą się w niespójnym baz danych na stronie docelowej replikacji, jeśli pliki bazy danych i dziennika/ponawiania zostały rozmieszczone w obrębie wielu dysków VHD (jako przede wszystkim przypadek) na maszynę Wirtualną po stronie "source".

 

## <a name="vm-node-resiliency"></a>Konfiguracji odporności maszyny Wirtualnej węzła
Platforma Azure oferuje kilka różnych warunków umów SLA dla maszyn wirtualnych. Szczegółowymi informacjami na temat można znaleźć w najnowszej wersji programu [umowy SLA dla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Ponieważ warstwy system DBMS jest zazwyczaj dostępności kluczową częścią systemu SAP, musisz przyznać sobie znasz koncepcji zestawów dostępności, strefy dostępności i obsługi zdarzeń. Artykuły, które opisują te pojęcia jest [Zarządzanie dostępnością maszyn wirtualnych Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) i [Zarządzanie dostępnością maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).  

Minimalne zalecane na potrzeby scenariuszy DBMS produkcyjnych z obciążeniami SAP jest:

- Wdróż dwie maszyny wirtualne w osobnym zestawie dostępności, w tym samym regionie platformy Azure.
- Te dwie maszyny wirtualne zostanie uruchomiony w tej samej sieci wirtualnej platformy Azure i będzie mieć karty sieciowe dołączone z tej samej podsieci.
- Użyj metody bazy danych, aby zachować rezerwy przy użyciu drugiej maszyny Wirtualnej. Metody mogą być program SQL Server Always On programu Oracle Data Guard i replikacji systemu HANA.

Ponadto możesz wdrażać trzecią maszynę Wirtualną w innym regionie platformy Azure i użyj tych samych metod bazy danych, aby określić asynchronicznego repliki w innym regionie platformy Azure.

Przedstawiono sposób konfigurowania zestawów dostępności platformy Azure w tym [samouczek](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Zagadnienia dotyczące sieci platformy Azure 
W przypadku dużych wdrożeń SAP, zalecane jest, że używasz planu z [wirtualnego centrum danych Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) dla sieci wirtualnej przydziałów konfiguracji oraz uprawnień i ról do różnych części organizacji.

Istnieje kilka najlepszych rozwiązań, które spowodowało poza setki wdrożeń klienta:

- Sieci wirtualne aplikacji SAP jest wdrażana w, nie ma dostępu do Internetu.
- Baza danych maszyn wirtualnych są uruchomione w tej samej sieci wirtualnej jako warstwy aplikacji.
- Maszyny wirtualne w sieci wirtualnej mają statyczny przydziału prywatnego adresu IP. Zapoznaj się z artykułem [typy adresów IP i metody alokacji na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) jako odwołanie.
- Ograniczenia routingu do i z maszyn wirtualnych z systemem DBMS **nie** zestawu przy użyciu zapór zainstalowanych na lokalnych maszynach wirtualnych z systemem DBMS. Zamiast tego routing ruchu jest zdefiniowana za pomocą [grup zabezpieczeń platformy Azure w sieci (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)
- Na potrzeby oddzielenie i izolowania ruchu sieciowego do maszyny Wirtualnej systemu DBMS, możesz przypisać różne karty sieciowe z maszyną wirtualną. Gdzie co karta sieciowa ma inny adres IP, a każdej karty Sieciowej jest przypisany do innej podsieci sieci wirtualnej, która ponownie ma różne reguły sieciowej grupy zabezpieczeń. Należy pamiętać, izolacja i rozdzielania ruchu sieciowego jest po prostu miarą routingu i nie zezwala na ustawianie limitów dla przepustowość sieci.

> [!NOTE]
> Należy przypisać statyczne adresy IP za pośrednictwem platformy Azure oznacza, że do poszczególnych kart sieciowych. Nie należy przypisywać statyczne adresy IP w ramach systemu operacyjnego gościa do wirtualnej karty sieciowej. Niektórych usług platformy Azure, takich jak usługa Kopia zapasowa Azure opierają się na fakcie, że w co najmniej podstawowego wirtualnej karty sieciowej jest ustawiony, DHCP, a nie statycznych adresów IP. Zobacz też dokumentu [kopii zapasowej maszyny wirtualnej Azure Rozwiązywanie problemów z](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Musisz przypisać wiele statycznych adresów IP do maszyny Wirtualnej, należy przypisać wiele kart sieciowych do maszyny Wirtualnej.
>
>

Używa dwóch maszyn wirtualnych do wdrażania systemu DBMS, w ramach zestawu dostępności platformy Azure, a także osobne routingu dla warstwy aplikacji SAP i zarządzanie i operacje ruchu do dwóch maszyn wirtualnych z systemem DBMS w środowisku produkcyjnym, diagram nierównej będzie wyglądać:

![Diagram przedstawiający dwóch maszyn wirtualnych w dwie podsieci](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="azure-load-balancer-for-redirecting-traffic"></a>Usługa Azure load balancer przekierowywania ruchu
Użycie prywatne wirtualne adresy IP używane w funkcje, takie jak SQL Server Always On HANA systemu lub replikacji wymaga konfiguracji usługi Azure Load Balancer. Równoważenie obciążenia Azure jest w stanie za pośrednictwem portów sondy, aby ustalić aktywnego węzła DBMS i kierować ruchem wyłącznie do tego węzła aktywnej bazy danych. W przypadku przejścia w tryb failover węzła bazy danych, nie ma potrzeby dla aplikacji SAP zmienić konfigurację. Zamiast tego najbardziej typowe architektury aplikacji SAP nawiąże połączenie przed wirtualnej prywatny adres IP. W międzyczasie równoważenia obciążenia platformy Azure był w trybie failover węzła przez przekierowywanie ruchu względem wirtualnej prywatny adres IP do drugiego węzła.

Platforma Azure oferuje dwa różne [jednostki SKU modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Podstawowa jednostka SKU i standardowej jednostki SKU. Chyba że chcesz wdrożyć w różnych strefach dostępności platformy Azure, modułu równoważenia obciążenia podstawowa jednostka SKU jest prawidłowo. 

Czy ruch między maszynami wirtualnymi systemu DBMS i warstwy aplikacji SAP, które są zawsze kierowane za pośrednictwem modułu równoważenia obciążenia platformy Azure przez cały czas? Odpowiedź zależy od tego, jak skonfigurować moduł równoważenia obciążenia. W tym momencie ruch przychodzący do maszyny Wirtualnej z systemem DBMS zawsze będą kierowane za pośrednictwem modułu równoważenia obciążenia platformy Azure. Wychodzący ruch trasy z maszyny Wirtualnej systemu DBMS do warstwy aplikacji, które maszyny Wirtualnej zależy od konfiguracji modułu równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia zapewnia możliwość DirectServerReturn. Jeśli ta opcja jest skonfigurowana, ruch skierowany z maszyny Wirtualnej systemu DBMS na warstwie aplikacji SAP będzie **nie** kierowane za pośrednictwem modułu równoważenia obciążenia platformy Azure. Zamiast tego zostanie bezpośrednio przejść do warstwy aplikacji. Jeśli nie skonfigurowano DirectServerReturn, ruch powrotny z warstwą aplikacji SAP odbywa się za pośrednictwem modułu równoważenia obciążenia platformy Azure

Zalecane jest skonfigurowanie DirectServerReturn w połączeniu z modułami równoważenia obciążenia platformy Azure, które są pozycjonowane między warstwą aplikacji SAP i warstwy system DBMS, aby zmniejszyć opóźnienie sieciowe między dwoma warstwami

Przykład konfigurowania taka konfiguracja jest publikowany wokół programu SQL server zawsze na w [w tym artykule](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Jeśli zdecydujesz się używać szablonów JSON opublikowane w witrynie github jako odwołanie dla wdrożeń infrastruktury SAP na platformie Azure, należy to badanie [szablonu systemu 3-warstwowej SAP](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). W tym szablonie możesz również mogą badać poprawne ustawienia równoważenia obciążenia platformy Azure.

### <a name="azure-accelerated-networking"></a>Azure Accelerated Networking
Aby dodatkowo zmniejszyć opóźnienie sieciowe między maszynami wirtualnymi platformy Azure, zalecane jest wybranie możliwość [usługę Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) podczas wdrażania maszyn wirtualnych platformy Azure w przypadku obciążeń SAP. Szczególnie w przypadku warstwy aplikacji SAP i SAP DBMS warstwy. 

> [!NOTE]
> Nie wszystkie typy maszyn wirtualnych znajdują się pomocne przyspieszonej sieci. Ów artykuł jest lista typów maszyn wirtualnych, które obsługują, przyspieszonej sieci. 
>  

- - -
> ![Windows][Logo_Windows] Windows
> 
> Dla Windows, zapoznaj się z artykułem [Utwórz maszynę wirtualną Windows dzięki przyspieszonej sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) , aby zrozumieć pojęcia i sposób sposób wdrażania maszyn wirtualnych dzięki przyspieszonej sieci
> 
> ![Linux][Logo_Linux] Linux
> 
> Dla systemu Linux, przeczytaj artykuł [Utwórz maszynę wirtualną systemu Linux przy użyciu Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) Aby uzyskać szczegółowe informacje dla dystrybucji systemu Linux. 
> 
> 

- - -

> [!NOTE]
> W przypadku SUSE i Red Hat, Oracle Linux przyspieszonej sieci jest obsługiwana przy użyciu najnowsze wersje. Usługę Azure Accelerated Networking nie obsługują starszych wersji, takich jak SLES 12 z dodatkiem SP2 lub systemu RHEL 7.2 
>  


## <a name="deployment-of-host-monitoring"></a>Wdrożenie hosta monitorowania
W środowisku produkcyjnym aplikacje SAP na maszynach wirtualnych platformy Azure środowisko SAP wymaga możliwość skorzystania z hosta dane monitorowania z hosty fizyczne uruchomionych maszyn wirtualnych platformy Azure. Określonego poziomu poprawki SAP Agent hosta jest wymagana, pozwalający tę funkcję w SAPOSCOL i Agent hosta SAP. Poziom poprawki dokładnie jest udokumentowany w Uwaga SAP [1409604].

Aby uzyskać szczegółowe informacje dotyczące wdrażania składników, które dostarczają dane hosta SAPOSCOL i Agent hosta SAP i zarządzanie cyklem życia tych składników, zapoznaj się [Podręcznik wdrażania][deployment-guide]


## <a name="next-steps"></a>Następne kroki
Dokumentację w szczególności DBMS można znaleźć w tych artykułach:

- [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu SQL Server dla obciążenia SAP](dbms_guide_sqlserver.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines oprogramowania Oracle dla obciążenia SAP](dbms_guide_oracle.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM DB2 dla obciążenia SAP](dbms_guide_ibm.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP](dbms_guide_sapase.md)
- [SAP maxDB, na żywo pamięci podręcznej i serwer zawartości wdrożenia na platformie Azure](dbms_guide_maxdb.md)
- [Przewodnik obsługi oprogramowania SAP HANA na platformie Azure](hana-vm-operations.md)
- [SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](sap-hana-availability-overview.md)
- [Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-backup-guide.md)

