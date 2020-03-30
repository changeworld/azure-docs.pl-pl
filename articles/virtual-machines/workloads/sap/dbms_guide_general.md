---
title: Zagadnienia dotyczące wdrażania usługi DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP | Dokumenty firmy Microsoft
description: Zagadnienia dotyczące wdrażania usługi DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP
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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101367"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Zagadnienia dotyczące wdrażania usługi DBMS maszyn wirtualnych platformy Azure dla obciążenia SAP
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

Ten przewodnik jest częścią dokumentacji dotyczącej sposobu implementowania i wdrażania oprogramowania SAP na platformie Microsoft Azure. Zanim przeczytasz ten przewodnik, przeczytaj [przewodnik planowania i wdrażania][planning-guide]. Ten dokument obejmuje ogólne aspekty wdrażania systemów DBMS związanych z SAP na maszynach wirtualnych platformy Microsoft Azure przy użyciu infrastruktury platformy Azure jako usługi (IaaS) możliwości.

Dokument uzupełnia dokumentację instalacji SAP i notatki SAP, które reprezentują podstawowe zasoby dla instalacji i wdrożeń oprogramowania SAP na danych platformach.

W tym dokumencie przedstawiono zagadnienia dotyczące uruchamiania systemów DBMS związanych z SAP na maszynach wirtualnych platformy Azure. W tym rozdziale istnieje kilka odniesień do konkretnych systemów DBMS. Zamiast tego konkretne systemy DBMS są obsługiwane w tym dokumencie, po tym dokumencie.

## <a name="definitions"></a>Definicje
W całym dokumencie terminy te są używane:

* **IaaS**: Infrastruktura jako usługa.
* **PaaS**: Platforma jako usługa.
* **SaaS**: Oprogramowanie jako usługa.
* **Składnik SAP**: Pojedyncza aplikacja SAP, taka jak ERP Central Component (ECC), Business Warehouse (BW), Solution Manager lub Enterprise Portal (EP). Składniki SAP mogą być oparte na tradycyjnych technologiach ABAP lub Java lub na aplikacji nieopartej na NetWeaver, takiej jak Obiekty biznesowe.
* **Środowisko SAP:** Jeden lub więcej składników SAP logicznie pogrupowane w celu wykonywania funkcji biznesowych, takich jak rozwój, zapewnienie jakości, szkolenia, odzyskiwanie po awarii lub produkcji.
* **Krajobraz SAP:** Termin ten odnosi się do całych zasobów SAP w krajobrazie IT klienta. Krajobraz SAP obejmuje wszystkie środowiska produkcyjne i nieprodukcyjne.
* **System SAP**: Połączenie warstwy DBMS i warstwy aplikacji, na przykład systemu rozwoju SAP ERP, systemu testowego SAP Business Warehouse lub systemu produkcyjnego SAP CRM. W wdrożeniach platformy Azure dzielenie tych dwóch warstw między lokalnie i platformy Azure nie jest obsługiwane. W rezultacie system SAP jest wdrażany lokalnie lub jest wdrażany na platformie Azure. Można wdrożyć różne systemy środowiska SAP na platformie Azure lub lokalnie. Na przykład można wdrożyć rozwiązania i testy systemów SAP CRM na platformie Azure, ale wdrożyć system produkcyjny SAP CRM lokalnie.
* **Między środowiskiem:** w tym artykule opisano scenariusz, w którym maszyny wirtualne są wdrażane w ramach subskrypcji platformy Azure, która ma łączność lokacja-lokacja, wiele lokacji lub usługi Azure ExpressRoute między lokalnymi centrami danych a platformą Azure. We wspólnej dokumentacji platformy Azure tego rodzaju wdrożenia są również opisane jako scenariusze międzylokacyjnymi. 

    Powodem połączenia jest rozszerzenie domen lokalnych, lokalnej usługi Active Directory i lokalnego systemu DNS na platformę Azure. Lokalny krajobraz jest rozszerzany na zasoby platformy Azure subskrypcji. Dzięki temu rozszerzeniu maszyny wirtualne mogą być częścią domeny lokalnej. Użytkownicy domeny lokalnej mogą uzyskiwać dostęp do serwerów i uruchamiać usługi na tych maszynach wirtualnych, takich jak usługi DBMS. Komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożonymi lokalnie i maszynami wirtualnymi wdrożonymi na platformie Azure jest możliwe. Ten scenariusz jest najbardziej typowym scenariuszem używanym do wdrażania zasobów SAP na platformie Azure. Aby uzyskać więcej informacji, zobacz [Planowanie i projektowanie bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Wdrożenia międzylokacyjne systemów SAP to miejsca, w których maszyny wirtualne platformy Azure, które uruchamiają systemy SAP, są członkami domeny lokalnej i są obsługiwane w systemach SAP produkcyjnych. Konfiguracje międzylokacyjne są obsługiwane w celu wdrażania części lub kompletnych krajobrazów SAP na platformie Azure. Nawet uruchomienie pełnego środowiska SAP na platformie Azure wymaga, aby te maszyny wirtualne były częścią domeny lokalnej i usługi Active Directory/LDAP. 
>
> W poprzednich wersjach dokumentacji wspomniano o scenariuszach hybrydowych it. Termin *hybrydowy* jest zakorzeniona w tym, że istnieje łączność między lokalizacjami między środowiskiem lokalnym a platformą Azure. W takim przypadku hybryda oznacza również, że maszyny wirtualne na platformie Azure są częścią lokalnej usługi Active Directory.
>
>

W niektórych dokumentach firmy Microsoft opisano scenariusze międzylokacyjne nieco inaczej, szczególnie w przypadku konfiguracji o wysokiej dostępności systemu DBMS. W przypadku dokumentów związanych z SAP scenariusz między lokalizacjami sprowadza się do połączenia usługi lokacja-lokacja lub prywatna usługa [ExpressRoute](https://azure.microsoft.com/services/expressroute/) oraz środowiska SAP, który jest dystrybuowany między środowiskiem lokalnym a platformą Azure.

## <a name="resources"></a>Resources
Istnieją inne artykuły dostępne na temat obciążenia SAP na platformie Azure. Rozpocznij [od obciążenia SAP na platformie Azure: rozpocznij pracę,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) a następnie wybierz obszar zainteresowania.

Następujące uwagi SAP są związane z SAP na platformie Azure w odniesieniu do obszaru objętego tym dokumentem.

| Numer notatki | Tytuł |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] |SAP na platformie Microsoft Azure: wymagania wstępne dotyczące pomocy technicznej |
| [1999351] |Rozwiązywanie problemów z rozszerzonym monitorowaniem platformy Azure dla systemu SAP |
| [2178632] |Kluczowe metryki monitorowania sap na platformie Microsoft Azure |
| [1409604] |Wirtualizacja w systemie Windows: ulepszone monitorowanie |
| [2191498] |SAP na Linuksie z platformą Azure: Ulepszone monitorowanie |
| [2039619] |Aplikacje SAP na platformie Microsoft Azure przy użyciu bazy danych Oracle: Obsługiwane produkty i wersje |
| [2233094] |DB6: Aplikacje SAP na platformie Azure przy użyciu IBM DB2 dla systemów Linux, UNIX i Windows: Dodatkowe informacje |
| [2243692] |Maszyna wirtualna systemu Linux na platformie Microsoft Azure (IaaS): problemy z licencją SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalacja i aktualizacja |
| [2069760] |Instalacja i uaktualnienie systemu Sap oracle Linux 7.x |
| [1597355] |Zalecenie dotyczące przestrzeni wymiany dla systemu Linux |
| [2171857] |Oracle Database 12c: Obsługa systemu plików w systemie Linux |
| [1114181] |Oracle Database 11g: Obsługa systemu plików w systemie Linux |


Aby uzyskać informacje na temat wszystkich uwag SAP dla systemu Linux, zobacz [wiki społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Potrzebujesz działającej wiedzy na temat architektury platformy Microsoft Azure i sposobu wdrażania i obsługi maszyn wirtualnych platformy Microsoft Azure. Aby uzyskać więcej informacji, zobacz [dokumentację platformy Azure](https://docs.microsoft.com/azure/).

Ogólnie rzecz biorąc instalacja i konfiguracja systemu Windows, Linux i DBMS są zasadniczo takie same jak każda maszyna wirtualna lub maszyna z systemem bez systemu operacyjnego, którą instalujesz lokalnie. Istnieją pewne architektury i zarządzania systemem decyzji implementacji, które są różne podczas korzystania z usługi Azure IaaS. W tym dokumencie wyjaśniono specyficzne różnice w zarządzaniu architekturą i systemem, na które należy przygotować podczas korzystania z usługi Azure IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktura magazynu maszyny Wirtualnej dla wdrożeń RDBMS
Aby postępować zgodnie z tym rozdziałem, przeczytaj i zrozum informacje przedstawione w [tym rozdziale][deployment-guide-3] [Przewodnika po wdrożeniach][deployment-guide]. Przed przeczytaniem tego rozdziału należy zrozumieć i dowiedzieć się o różnych seriach maszyn wirtualnych i różnicach między magazynem standardowym i w wersji premium. 

Aby dowiedzieć się więcej o usłudze Azure Storage for Azure VMs, zobacz:

- [Wprowadzenie do dysków zarządzanych dla maszyn wirtualnych systemu Windows platformy Azure](../../windows/managed-disks-overview.md).
- [Wprowadzenie do dysków zarządzanych dla maszyn wirtualnych z systemem Azure Linux](../../linux/managed-disks-overview.md).

W konfiguracji podstawowej zwykle zaleca się strukturę wdrażania, w której system operacyjny, system DBMS i ewentualne pliki binarne SAP są oddzielone od plików bazy danych. Zaleca się, że systemy SAP, które działają na maszynach wirtualnych platformy Azure mają podstawowy dysk WIRTUALNY lub dysk, zainstalowany z systemem operacyjnym, pliki wykonywalne systemu zarządzania bazami danych i pliki wykonywalne SAP. 

Pliki danych i dzienników dbms są przechowywane w standardowej pamięci masowej lub pamięci masowej w wersji premium. Są one przechowywane w oddzielnych dyskach i dołączone jako dyski logiczne do oryginalnej maszyny Wirtualnej obrazu systemu operacyjnego platformy Azure. W przypadku wdrożeń systemu Linux są dokumentowane różne zalecenia, szczególnie dla sap HANA.

Podczas planowania układu dysku znajdź najlepszą równowagę między tymi elementami:

* Liczba plików danych.
* Liczba dysków zawierających pliki.
* Przydziały we/wy na jednym dysku.
* Przepływność danych na dysku.
* Liczba dodatkowych dysków danych możliwe na rozmiar maszyny Wirtualnej.
* Ogólna przepływność magazynu, która może zapewnić maszynę wirtualną.
* Opóźnienie różnych typów usługi Azure Storage może zapewnić.
* 600 000 000 000 000 00

Platforma Azure wymusza przydział we/wys. Te przydziały różnią się w przypadku dysków hostowanych w magazynie standardowym i magazynie w wersji premium. Opóźnienie we/wy różni się również między dwoma typami magazynu. Pamięć masowa w jakości Premium zapewnia lepsze opóźnienie we/wy. 

Każdy z różnych typów maszyn wirtualnych ma ograniczoną liczbę dysków z danymi, które można dołączyć. Innym ograniczeniem jest to, że tylko niektóre typy maszyn wirtualnych można użyć magazynu w wersji premium. Zazwyczaj zdecydujesz się użyć określonego typu maszyny Wirtualnej na podstawie wymagań procesora CPU i pamięci. Można również wziąć pod uwagę wymagania dotyczące usług We/Wy, opóźnienia i przepływności dysku, które zwykle są skalowane z liczbą dysków lub typem dysków magazynu w wersji premium. Liczba we/wy i przepływność, które mają zostać osiągnięte przez każdy dysk, może dyktować rozmiar dysku, szczególnie w przypadku magazynu w wersji premium.

> [!NOTE]
> W przypadku wdrożeń systemu dbms zaleca się użycie magazynu w wersji premium dla dowolnych danych, dziennika transakcji lub plików ponawianych. Nie ma znaczenia, czy chcesz wdrożyć systemy produkcyjne, czy nieprodukcyjne.

> [!NOTE]
> Aby korzystać z unikatowej [pojedynczej umowy SLA platformy](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)Azure, wszystkie dyski, które są dołączone, muszą być typu magazynu w warstwie Premium, który zawiera podstawowy dysk WIRTUALNY.

> [!NOTE]
> Hostowanie głównych plików bazy danych, takich jak pliki danych i dzienników, baz danych SAP na sprzęcie magazynu, który znajduje się w zlokalizowanych centrach danych innych firm sąsiadujących z centrami danych platformy Azure, nie jest obsługiwane. W przypadku obciążeń SAP tylko magazyn, który jest reprezentowany jako natywna usługa platformy Azure jest obsługiwany dla plików dziennika danych i transakcji w bazach danych SAP.

Rozmieszczenie plików bazy danych i plików dziennika i ponawiać oraz typ usługi Azure Storage, którego używasz, jest definiowany przez usługi We/Wy, opóźnienia i przepływności. Aby mieć wystarczającą liczbę we/wy, możesz być zmuszony do użycia wielu dysków lub użycia większego dysku magazynu w wersji premium. Jeśli używasz wielu dysków, skompiluj pasek oprogramowania na dyskach zawierających pliki danych lub pliki dziennika i ponawiaj pliki. W takich przypadkach usługi We/Wy i skryty przepływności dysku podstawowych dysków magazynu w jakości premium lub maksymalne osiągalne we/wy we/wy standardowych dysków magazynu są kumulacyjne dla wynikowego zestawu rozłożonego.

Jak już wspomniano, jeśli wymagania We/Wy przekracza to, co pojedynczy dysk VHD może zapewnić, zrównoważyć liczbę We/Wy, które są potrzebne dla plików bazy danych w wielu vhds. Najprostszym sposobem dystrybucji obciążenia we/wy na dyskach jest zbudowanie paska oprogramowania na różnych dyskach. Następnie umieść kilka plików danych SAP DBMS na LUN wyryte z paska oprogramowania. Liczba dysków w pasku jest napędzana przez żądania IOPs, wymagania przepływności dysku i wymagania woluminu.


---
> ![Windows][Logo_Windows] Windows
>
> Zaleca się, aby używać miejsca do magazynowania systemu Windows do tworzenia zestawów rozłożonych w wielu dyskach VHD platformy Azure. Użyj co najmniej systemu Windows Server 2012 R2 lub Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Tylko MDADM i Logical Volume Manager (LVM) są obsługiwane do tworzenia macierzy RAID oprogramowania na Linuksie. Aby uzyskać więcej informacji, zobacz:
>
> - [Konfigurowanie macierzy RAID oprogramowania w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) przy użyciu mdadm
> - [Konfigurowanie lvm na maszynie wirtualnej z systemem Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) przy użyciu lvm
>
>

---

> [!NOTE]
> Ponieważ usługa Azure Storage przechowuje trzy obrazy dysków VHD, nie ma sensu konfigurować nadmiarowości podczas paska. Wystarczy skonfigurować rozkładanie tak, aby we/wy były rozmieszczone na różnych dyskach VHD.
>

### <a name="managed-or-nonmanaged-disks"></a>Dyski zarządzane lub niezarządzane
Konto magazynu platformy Azure jest konstrukcją administracyjną, a także podlega ograniczeniom. Ograniczenia różnią się między kontami magazynu standardowego a kontami magazynu w wersji premium. Aby uzyskać informacje na temat możliwości i ograniczeń, zobacz [Cele skalowalności i wydajności usługi Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)

W przypadku magazynu standardowego należy pamiętać, że istnieje limit na koncie we/wy na konto magazynu. Zobacz wiersz zawierający **łączną szybkość żądań** w artykule [Cele skalowalności i wydajności usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Istnieje również początkowy limit liczby kont magazynu na subskrypcję platformy Azure. Równoważenie dysków VHD dla większego środowiska SAP na różnych kontach magazynu, aby uniknąć osiągnięcia limitów tych kont magazynu. Jest to żmudna praca, gdy mówimy o kilkuset maszynach wirtualnych z ponad tysiącem VHD.

Ponieważ używanie standardowego magazynu dla wdrożeń systemu dbms w połączeniu z obciążeniem SAP nie jest zalecane, odwołania i zalecenia dotyczące standardowego magazynu są ograniczone do tego krótkiego [artykułu](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Aby uniknąć administracyjnej pracy związanej z planowaniem i wdrażaniem dysków VHD na różnych kontach magazynu platformy Azure, firma Microsoft wprowadziła [dyski zarządzane platformy Azure](https://azure.microsoft.com/services/managed-disks/) w 2017 r. Dyski zarządzane są dostępne dla magazynu standardowego i magazynu w wersji premium. Główne zalety dysków zarządzanych w porównaniu z dyskami niezarządzanymi to:

- W przypadku dysków zarządzanych platforma Azure automatycznie dystrybuuje różne dyski VHD na różnych kontach magazynu w czasie wdrażania. W ten sposób limity kont magazynu dla woluminu danych, przepływności we/wy i we/wy nie są trafione.
- Korzystając z dysków zarządzanych, usługa Azure Storage honoruje pojęcia zestawów dostępności platformy Azure. Jeśli maszyna wirtualna jest częścią zestawu dostępności platformy Azure, podstawowy dysk WIRTUALNY i dołączony dysk maszyny Wirtualnej są wdrażane w różnych domenach błędów i aktualizacji.


> [!IMPORTANT]
> Biorąc pod uwagę zalety dysków zarządzanych platformy Azure, zaleca się używanie dysków zarządzanych platformy Azure do wdrożeń dbms i ogólnie wdrożeń SAP.
>

Aby przekonwertować z dysków niezarządzanych na dyski zarządzane, zobacz:

- [Konwertowanie maszyny wirtualnej systemu Windows z dysków niezarządzanych na dyski zarządzane](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Konwertuj maszynę wirtualną systemu Linux z dysków niezarządzanych na dyski zarządzane](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Buforowanie maszyn wirtualnych i dysków z danymi
Podczas instalowania dysków na maszynach wirtualnych można wybrać, czy ruch we/wy między maszyną wirtualną a dyskami znajdującymi się w magazynie platformy Azure jest buforowany. Magazyn standardowy i premium używają dwóch różnych technologii dla tego typu pamięci podręcznej.

W poniższych zaleceniach przyjęto założenie, że te cechy we/wy dla standardowych obiektów w zakresie dbms:

- Jest to głównie obciążenie odczytu względem plików danych bazy danych. Odczyty te mają kluczowe znaczenie dla wydajności systemu DBMS.
- Zapisywanie plików danych odbywa się w seriach opartych na punktach kontrolnych lub ciągłym strumieniu. Uśrednionych w ciągu jednego dnia, jest mniej zapisów niż odczytów. W przeciwieństwie do odczytów z plików danych, te zapisy są asynchroniczne i nie posiadają żadnych transakcji użytkownika.
- Prawie nie ma żadnych odczytów z dziennika transakcji lub ponownie plików. Wyjątkami są duże operacje we/wy podczas wykonywania kopii zapasowych dziennika transakcji.
- Głównym obciążeniem dla plików dziennika transakcji lub ponawianie jest zapis. W zależności od charakteru obciążenia można mieć we/wy tak małe, jak 4 KB lub, w innych przypadkach, we/wy rozmiary 1 MB lub więcej.
- Wszystkie zapisy muszą być zachowywane na dysku w niezawodny sposób.

W przypadku magazynu standardowego możliwe typy pamięci podręcznej to:

* Brak
* Odczyt
* Odczyt/Zapis

Aby uzyskać spójną i deterministyczną wydajność, ustaw buforowanie standardowego magazynu dla wszystkich dysków zawierających pliki danych związane z programem DBMS, pliki dziennika i ponownego rejestrowania oraz miejsce w tabeli na **BRAK**. Buforowanie podstawowego dysku VHD może pozostać z wartością domyślną.

W przypadku magazynu w wersji premium istnieją następujące opcje buforowania:

* Brak
* Odczyt
* Odczyt/zapis
* Brak + Akcelerator zapisu, który jest przeznaczony tylko dla maszyn wirtualnych z serii M platformy Azure
* Akcelerator odczytu + zapisu, który jest przeznaczony tylko dla maszyn wirtualnych z serii M platformy Azure

W przypadku magazynu w wersji premium zaleca się używanie **buforowania odczytu dla plików danych** z bazy danych SAP i **wybieranie opcji Brak buforowania dysków plików dziennika**.

W przypadku wdrożeń z serii M zaleca się używanie akceleratora zapisu azure dla wdrożenia systemu dbms. Aby uzyskać szczegółowe informacje, ograniczenia i wdrożenie akceleratora zapisu platformy Azure, zobacz [Włączanie akceleratora zapisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Dyski nietrwisane platformy Azure
Maszyny wirtualne platformy Azure oferują dyski nietrwałe po wdrożeniu maszyny wirtualnej. W przypadku ponownego uruchomienia maszyny Wirtualnej cała zawartość na tych dyskach jest wymazana. Jest to biorąc pod uwagę, że pliki danych i dziennika i ponownego plików baz danych nie powinny w żadnym wypadku znajdować się na tych dyskach nietrwałych. Mogą istnieć wyjątki dla niektórych baz danych, gdzie te dyski nietrwałe mogą być odpowiednie dla tempdb i temp tablespaces. Należy unikać używania tych dysków dla maszyn wirtualnych serii A, ponieważ te dyski nietrwałe są ograniczone w przepływności z tej rodziny maszyn wirtualnych. 

Aby uzyskać więcej informacji, zobacz [Opis tymczasowego dysku na maszynach wirtualnych z systemem Windows na platformie Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> Dysk D w maszynie wirtualnej platformy Azure jest dyskiem nietrwałym, który jest wspierany przez niektóre dyski lokalne w węźle obliczeniowym platformy Azure. Ponieważ jest nietrwałe, wszelkie zmiany wprowadzone do zawartości na dysku D zostaną utracone po ponownym uruchomieniu maszyny Wirtualnej. Zmiany obejmują pliki, które zostały zapisane, katalogi, które zostały utworzone i aplikacje, które zostały zainstalowane.
>
> ![Linux][Logo_Linux] Linux
>
> Maszyny wirtualne platformy Azure z systemem Linux automatycznie montują dysk w /mnt/resource, który jest nietrwałym dyskiem wspieranym przez dyski lokalne w węźle obliczeniowym platformy Azure. Ponieważ jest nietrwałe, wszelkie zmiany wprowadzone do zawartości w /mnt/resource są tracone po ponownym uruchomieniu maszyny Wirtualnej. Zmiany obejmują pliki, które zostały zapisane, katalogi, które zostały utworzone i aplikacje, które zostały zainstalowane.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Odporność usługi Microsoft Azure Storage
Usługa Microsoft Azure Storage przechowuje podstawowy dysk VHD z systemem operacyjnym i dołączonymi dyskami lub obiektami blob na co najmniej trzech oddzielnych węzłach magazynu. Ten typ magazynu jest nazywany magazynem lokalnie nadmiarowym (LRS). LRS jest domyślnym dla wszystkich typów magazynu na platformie Azure.

Istnieją inne metody nadmiarowości. Aby uzyskać więcej informacji, zobacz [Replikacja usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Magazyn w stanie Premium jest zalecanym typem magazynu dla maszyn wirtualnych dbms i dysków, które przechowują bazę danych i dzienniki i ponawiać pliki. Jedyną dostępną metodą nadmiarowości dla magazynu w wersji premium jest LRS. W rezultacie należy skonfigurować metody bazy danych, aby włączyć replikację danych bazy danych do innego regionu platformy Azure lub strefy dostępności. Metody bazy danych obejmują SQL Server Always On, Oracle Data Guard i HANA System Replication.


> [!NOTE]
> W przypadku wdrożeń systemu dbms użycie magazynu geograficznie nadmiarowego (GRS) nie jest zalecane w przypadku magazynu standardowego. GRS poważnie wpływa na wydajność i nie honoruje kolejności zapisu w różnych vhds, które są dołączone do maszyny Wirtualnej. Nie honorowanie kolejności zapisu w różnych vhds potencjalnie prowadzi do niespójnych baz danych po stronie docelowej replikacji. Taka sytuacja występuje, jeśli pliki bazy danych i dziennika i ponawiać są rozłożone na wiele vhds, jak to zwykle bywa, po stronie źródłowej maszyny Wirtualnej.



## <a name="vm-node-resiliency"></a>Odporność węzła maszyny Wirtualnej
Platforma Azure oferuje kilka różnych zestawów SLA dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz najnowszą wersję [umowy SLA dla maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Ponieważ warstwa DBMS jest zwykle krytyczna dla dostępności w systemie SAP, należy zrozumieć zestawy dostępności, strefy dostępności i zdarzenia konserwacji. Aby uzyskać więcej informacji na temat tych pojęć, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) i [Zarządzanie dostępnością maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)na platformie Azure .

Minimalne zalecenie dla scenariuszy dbms produkcji z obciążeniem SAP jest:

- Wdrażanie dwóch maszyn wirtualnych w osobnym zestawie dostępności w tym samym regionie platformy Azure.
- Uruchom te dwie maszyny wirtualne w tej samej sieci wirtualnej platformy Azure i mieć karty sieciowe dołączone z tych samych podsieci.
- Użyj metod bazy danych, aby zachować gorąca rezerwa przy użyciu drugiej maszyny Wirtualnej. Metody mogą być SQL Server Always On, Oracle Data Guard lub HANA System Replication.

Można również wdrożyć trzecią maszynę wirtualną w innym regionie platformy Azure i użyć tych samych metod bazy danych do dostarczania repliki asynchroniiowej w innym regionie platformy Azure.

Aby uzyskać informacje na temat konfigurowania zestawów dostępności platformy Azure, zobacz [ten samouczek](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Zagadnienia dotyczące sieci platformy Azure
W przypadku wdrożeń SAP na dużą skalę użyj planu [wirtualnego centrum danych platformy Azure.](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) Użyj go do konfiguracji sieci wirtualnej oraz uprawnień i przypisań ról do różnych części organizacji.

Te najlepsze rozwiązania są wynikiem setek wdrożeń klientów:

- Sieci wirtualne, w których jest wdrażana aplikacja SAP, nie mają dostępu do Internetu.
- Maszyny wirtualne bazy danych są uruchamiane w tej samej sieci wirtualnej co warstwa aplikacji.
- Maszyny wirtualne w sieci wirtualnej mają statyczną alokację prywatnego adresu IP. Aby uzyskać więcej informacji, zobacz [typy adresów IP i metody alokacji na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Ograniczenia routingu do i z maszyn wirtualnych systemu dbms *nie* są ustawiane z zapór zainstalowanych na lokalnych maszynach wirtualnych DBMS. Zamiast tego routing ruchu jest definiowany za pomocą [sieciowych grup zabezpieczeń (NSG).](https://docs.microsoft.com/azure/virtual-network/security-overview)
- Aby oddzielić i wyizolować ruch do maszyny Wirtualnej DBMS, należy przypisać różne karty sieciowe do maszyny Wirtualnej. Każda karta sieciowa otrzymuje inny adres IP, a każda karta sieciowa jest przypisana do innej podsieci sieci wirtualnej. Każda podsieć ma różne reguły sieciowej sieciowej sieciowej. Izolacja lub oddzielenie ruchu sieciowego jest miarą routingu. Nie jest używany do ustawiania przydziałów dla przepływności sieci.

> [!NOTE]
> Przypisywanie statycznych adresów IP za pośrednictwem platformy Azure oznacza przypisanie ich do poszczególnych wirtualnych kart sieciowych. Nie przypisuj statycznych adresów IP w systemu operacyjnego gościa do wirtualnej karty sieciowej. Niektóre usługi platformy Azure, takie jak usługa Azure Backup, opierają się na tym, że co najmniej podstawowa wirtualna karta sieciowa jest ustawiona na dhcp, a nie na statyczne adresy IP. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z tworzeniem kopii zapasowej maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Aby przypisać wiele statycznych adresów IP do maszyny Wirtualnej, należy przypisać wiele wirtualnych kart sieciowych do maszyny Wirtualnej.
>


> [!IMPORTANT]
> Konfigurowanie [sieciowych urządzeń wirtualnych](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP a warstwą DBMS systemu SAP opartego na systemie SAP sap firmy SAP firmy SAP firmy SAP firmy SAP firmy SAP firmy SAP. To ograniczenie jest ze względu na funkcjonalność i wydajność. Ścieżka komunikacji między warstwą aplikacji SAP a warstwą DBMS musi być bezpośrednia. Ograniczenie nie obejmuje [reguł grupy zabezpieczeń aplikacji (ASG) i sieciowej grupy zabezpieczeń,](https://docs.microsoft.com/azure/virtual-network/security-overview) jeśli te reguły sieciowej grupy ZABEZPIECZEŃ i sieciowej sieciowej zezwalają na bezpośrednią ścieżkę komunikacji. 
>
> Inne scenariusze, w których urządzenia wirtualne sieci nie są obsługiwane, są:
>
> * Ścieżki komunikacji między maszynami wirtualnymi platformy Azure reprezentującymi węzły klastra rozrusznika rozrusznika systemu Linux a urządzeniami SBD zgodnie z opisem w [sekcji Wysoka dostępność dla sap NetWeaver na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Ścieżki komunikacji między maszynami wirtualnymi platformy Azure a skalowanym w poziomie serwera plików systemu Windows Server (SOFS) skonfigurowane zgodnie z opisem w [programie Cluster wystąpienie SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Sieciowe urządzenia wirtualne w ścieżkach komunikacyjnych mogą łatwo podwoić opóźnienie sieci między dwoma partnerami komunikacyjnymi. Mogą również ograniczać przepływność w ścieżkach krytycznych między warstwą aplikacji SAP a warstwą DBMS. W niektórych scenariuszach klienta wirtualne urządzenia sieciowe mogą powodować niepowodzenie klastrów systemu Windows z systemem Windows. Są to przypadki, w których komunikacja między węzłami klastra rozrusznika systemu Linux komunikuje się z urządzeniem SBD za pośrednictwem wirtualnego urządzenia sieciowego.
>

> [!IMPORTANT]
> Inny projekt, który *nie* jest obsługiwany, to segregacja warstwy aplikacji SAP i warstwy DBMS na różne sieci wirtualne platformy Azure, które nie są ze sobą [równorzędne.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Zaleca się segregować warstwę aplikacji SAP i warstwę DBMS przy użyciu podsieci w sieci wirtualnej platformy Azure, a nie przy użyciu różnych sieci wirtualnych platformy Azure. 
>
> Jeśli zdecydujesz się nie stosować się do zalecenia i zamiast tego segregować dwie warstwy w różnych sieciach wirtualnych, dwie sieci wirtualne muszą być [równorzędne](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Należy pamiętać, że ruch sieciowy między dwiema [równorzędnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sieci wirtualnych platformy Azure podlega kosztom transferu. Ogromny wolumin danych, który składa się z wielu terabajtów jest wymieniany między warstwą aplikacji SAP a warstwą DBMS. Można zgromadzić znaczne koszty, jeśli warstwa aplikacji SAP i warstwa DBMS są oddzielone między dwiema równorzędnych sieci wirtualnych platformy Azure.

Użyj dwóch maszyn wirtualnych do wdrożenia dbms produkcji w ramach zestawu dostępności platformy Azure. Użyj również oddzielnego routingu dla warstwy aplikacji SAP oraz ruchu zarządzania i operacji do dwóch maszyn wirtualnych dbms. Zobacz poniższy obraz:

![Diagram dwóch maszyn wirtualnych w dwóch podsieciach](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Przekieruj ruch za pomocą modułu Azure Load Balancer
Użycie prywatnych wirtualnych adresów IP używanych w funkcjach, takich jak SQL Server Always On lub HANA System Replication wymaga konfiguracji modułu równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia używa portów sondy do określenia aktywnego węzła DBMS i kierowania ruchu wyłącznie do tego aktywnego węzła bazy danych. 

Jeśli istnieje przewijenie awaryjne węzła bazy danych, nie ma potrzeby ponownego konfigurowania przez aplikację SAP. Zamiast tego najbardziej typowe architektury aplikacji SAP ponownie połączyć się z prywatnych wirtualny adres IP. Tymczasem moduł równoważenia obciążenia reaguje na tryb failover węzła, przekierowując ruch względem prywatnego wirtualnego adresu IP do drugiego węzła.

Platforma Azure oferuje dwa różne [jednostki SKU równoważenia obciążenia:](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)podstawową jednostkę SKU i standardową jednostkę SKU. Jeśli nie chcesz wdrażać w strefach dostępności platformy Azure, podstawowa jednostka SKU modułu równoważenia obciążenia działa prawidłowo.

Czy ruch między maszynami wirtualnymi systemu dbms a warstwą aplikacji SAP jest zawsze cały czas kierowany przez moduł równoważenia obciążenia? Odpowiedź zależy od sposobu skonfigurowania modułu równoważenia obciążenia. 

W tej chwili ruch przychodzący do maszyny Wirtualnej DBMS jest zawsze kierowane za pośrednictwem modułu równoważenia obciążenia. Trasa ruchu wychodzącego z maszyny Wirtualnej DBMS do warstwy aplikacji VM zależy od konfiguracji modułu równoważenia obciążenia. 

Moduł równoważenia obciążenia oferuje opcję DirectServerReturn. Jeśli ta opcja jest skonfigurowana, ruch kierowany z maszyny Wirtualnej DBMS do warstwy aplikacji SAP *nie* jest kierowany przez moduł równoważenia obciążenia. Zamiast tego przechodzi bezpośrednio do warstwy aplikacji. Jeśli funkcja DirectServerReturn nie jest skonfigurowana, ruch zwrotny do warstwy aplikacji SAP jest kierowany przez moduł równoważenia obciążenia.

Zaleca się skonfigurowanie funkcji DirectServerReturn w połączeniu z modułami równoważenia obciążenia umieszczonymi między warstwą aplikacji SAP a warstwą DBMS. Ta konfiguracja zmniejsza opóźnienie sieci między dwiema warstwami.

Aby uzyskać przykład konfigurowania tej konfiguracji za pomocą programu SQL Server Always On, zobacz [Konfigurowanie odbiornika równoważenia obciążeniacza dla grup dostępności zawsze włączone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Jeśli używasz opublikowanych szablonów JSON GitHub jako odniesienia dla wdrożeń infrastruktury SAP na platformie Azure, przestudiuj ten [szablon dla systemu SAP 3-warstwowego.](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md) W tym szablonie można również wyświetlić poprawne ustawienia modułu równoważenia obciążenia.

### <a name="azure-accelerated-networking"></a>Przyspieszona sieć platformy Azure
Aby jeszcze bardziej zmniejszyć opóźnienia sieci między maszynami wirtualnymi platformy Azure, zaleca się wybranie opcji [Przyspieszona sieć platformy Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Użyj go podczas wdrażania maszyn wirtualnych platformy Azure dla obciążenia SAP, szczególnie dla warstwy aplikacji SAP i warstwy SAP DBMS.

> [!NOTE]
> Nie wszystkie typy maszyn wirtualnych obsługują przyspieszoną sieć. W poprzednim artykule wymieniono typy maszyn wirtualnych obsługujące przyspieszoną sieć.
>

---
> ![Windows][Logo_Windows] Windows
>
> Aby dowiedzieć się, jak wdrażać maszyny wirtualne z przyspieszoną siecią dla systemu Windows, zobacz [Tworzenie maszyny wirtualnej systemu Windows z przyspieszoną siecią](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Aby uzyskać więcej informacji na temat dystrybucji systemu Linux, zobacz [Tworzenie maszyny wirtualnej systemu Linux z przyspieszoną siecią](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> W przypadku SUSE, Red Hat i Oracle Linux, Przyspieszone sieci są obsługiwane w najnowszych wersjach. Starsze wersje, takie jak SLES 12 SP2 lub RHEL 7.2, nie obsługują przyspieszonej sieci platformy Azure.
>


## <a name="deployment-of-host-monitoring"></a>Wdrażanie monitorowania hosta
Do użytku produkcyjnego aplikacji SAP na maszynach wirtualnych platformy Azure SAP wymaga możliwości uzyskania danych monitorowania hosta z hostów fizycznych, które uruchamiają maszyny wirtualne platformy Azure. Wymagany jest określony poziom poprawki agenta hosta SAP, który umożliwia tę funkcję w saposcol i SAP Host Agent. Dokładny poziom poprawki jest udokumentowany w SAP Note [1409604].

Aby uzyskać więcej informacji na temat wdrażania składników dostarczających dane hosta do saposcol i SAP Host Agent oraz zarządzania cyklem życia tych składników, zobacz [Przewodnik wdrażania][deployment-guide].


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat określonego dbms, zobacz:

- [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu SQL Server dla obciążenia SAP](dbms_guide_sqlserver.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines oprogramowania Oracle dla obciążenia SAP](dbms_guide_oracle.md)
- [Wdrożenie dbms maszyn wirtualnych platformy IBM DB2 Azure dla obciążenia SAP](dbms_guide_ibm.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP](dbms_guide_sapase.md)
- [Wdrożenie SAP maxDB, live cache i content server na platformie Azure](dbms_guide_maxdb.md)
- [Przewodnik obsługi oprogramowania SAP HANA na platformie Azure](hana-vm-operations.md)
- [Wysoka dostępność sap hana dla maszyn wirtualnych platformy Azure](sap-hana-availability-overview.md)
- [Przewodnik po kopiach zapasowych dla systemu SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-backup-guide.md)

