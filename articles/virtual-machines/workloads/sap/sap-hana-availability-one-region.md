---
title: SAP HANA dostępności w ramach jednego regionu Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano SAP HANA operacje na maszynach wirtualnych natywnego Azure w jednym regionie Azure.
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
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47dba73a6c22d11953485a69435000d3d2fe6f55
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192802"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA dostępności w ramach jednego regionu Azure
W tym artykule opisano kilka scenariuszy dostępności w ramach jednego regionu platformy Azure. Platforma Azure ma wiele regionów, rozprzestrzeniające się na całym świecie. Lista regionów platformy Azure, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/). Do wdrożenia SAP HANA na maszynach wirtualnych w ramach jednego regionu Azure, firma Microsoft oferuje wdrażanie jednej maszyny Wirtualnej przy użyciu wystąpienia HANA. Zwiększona dostępność, można wdrożyć dwóch maszyn wirtualnych z dwoma wystąpieniami HANA w [zestawu dostępności Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) używającą replikacji systemu HANA dostępności. 

Obecnie usługa Azure oferuje publicznej wersji zapoznawczej programu [stref dostępności Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/availability-zones/az-overview). W tym artykule nie opisano stref dostępności szczegółowo. Ale możemy zawierają ogólne omówienie przy użyciu zestawów dostępności w zależności od dostępności strefy.

Jaka jest różnica między zestawu dostępności i strefy dostępności na platformie Azure? Regiony platformy Azure, gdzie są oferowane stref dostępności ma wiele centrów danych. Centrach danych są niezależne dostaw źródła zasilania, sieci i chłodzenia. Przyczyna oferty różnych stref w pojedynczym regionie Azure, jest więc aplikacje można wdrożyć w różnych strefach dostępności dwóch lub trzech, które są oferowane. Przy założeniu, że problemy dotyczące źródła lub sieci zasilania mających wpływ na tylko jedną infrastruktury strefy dostępności, wdrażania aplikacji w obrębie regionu Azure jest nadal pełni funkcjonalny, jeśli używasz stref dostępności. Może wystąpić pewne zmniejszenie wydajności. Na przykład maszyny wirtualne w jednej strefie mogą zostać utracone, ale maszyny wirtualne w dwie strefy będą nadal działa prawidłowo. 
 
Zestaw dostępności Azure jest możliwość grupę logiczną, która pomaga zapewnienia zasobów maszyny Wirtualnej, które można umieścić w zestawie dostępności awarii odizolowane od siebie, gdy są wdrożone w centrum danych Azure. Azure zapewnia maszyn wirtualnych, umieść dostępność ustawić wykonywania między wieloma serwerami fizycznymi, stojakami obliczeniowe jednostek magazynowych i przełączniki sieciowe. W części dokumentacji platformy Azure, ta konfiguracja jest określana jako rozmieszczanie w różnych [domen aktualizacji i odporność](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Rozmieszczanie te są zwykle w obrębie centrum danych Azure. Przy założeniu, że problemy zasilania źródła i sieci mających wpływ na centrum danych, w której wdrażasz, czy pojemność w jednym regionie Azure wpłynęłoby.

Umieszczanie centrów danych, reprezentujące stref dostępności Azure stanowi kompromis między dostarczania opóźnienie sieci między usługami wdrożone w różnych strefach, które stosowane dla większości aplikacji, w określonej odległości między centrami danych. Fizyczne catastrophes najlepiej nie wpływa na zasilania, sieci dostaw i infrastruktury dla wszystkich stref dostępności w tym regionie. Jednak pokazany pomnikowe catastrophes fizycznych mają stref dostępności może nie zawsze zawiera dostępności, który ma w ramach jednego regionu. Zastanów się Maria Huragan, która osiągnęła Wyspy Portoryko na 20 września 2017 r. Huragan zasadniczo spowodował prawie 100 procent niedostępności na Wyspy 90 mil na poziomie.

## <a name="single-vm-scenario"></a>Maszyna wirtualna na jednym scenariusza

W przypadku maszyn wirtualnych na jednym możesz utworzyć maszyny Wirtualnej platformy Azure dla wystąpienia programu SAP HANA. Usługa Azure Premium Storage służy do obsługi dysku systemu operacyjnego i wszystkich dysków danych. Azure przestojów SLA 99,9 procent i umowy SLA z innymi składnikami systemu Azure jest wystarczające do pełnienia jego dostępność umowy SLA dla klientów. W tym scenariuszu użytkownik nie ma potrzeby wykorzystać Azure zbiór dostępności dla maszyn wirtualnych warstwy systemu DBMS. W tym scenariuszu użytkownik korzysta z dwóch różnych funkcji:

- Azure VM automatycznego ponownego uruchamiania (również nazywany naprawą usługi Azure)
- SAP HANA automatycznego ponownego uruchamiania

Azure VM automatyczne ponowne uruchamianie lub naprawą usługi, jest funkcji na platformie Azure, która działa na dwóch poziomach:

- Hosta serwera Azure sprawdza kondycję maszynę Wirtualną, która znajduje się na serwerze głównym.
- Kontroler sieci szkieletowej Azure monitoruje kondycję i dostępność serwera hosta.

Funkcja sprawdzania kondycji monitoruje kondycję każdej maszyny Wirtualnej, który znajduje się na hoście serwera Azure. Maszynę wirtualną należącą do stanu nonhealthy, ponowne uruchomienie maszyny wirtualnej może być inicjowane przez agenta hosta platformy Azure, który umożliwia sprawdzenie kondycji maszyny wirtualnej. Kontroler sieci szkieletowej sprawdza kondycji hosta, sprawdzając wiele różnych parametrów, które mogą wskazywać na problemy z sprzętu hosta. Sprawdza również na dostępność hosta za pośrednictwem sieci. Wskazanie problemy z hostem może prowadzić do następujących zdarzeń:

- Jeśli host sygnalizuje zła kondycja, ponowne uruchomienie hosta i ponowne uruchomienie maszyn wirtualnych, które były uruchomione na hoście.
- Jeśli host nie jest w dobrej kondycji po ponownym uruchomieniu komputera, ponowne uruchomienie hosta i ponowne uruchomienie maszyn wirtualnych, które były wcześniej obsługiwane na hoście, na hoście działa prawidłowo. W takim przypadku hosta jest oznaczony jako nie działa prawidłowo. Nie można użyć dla dalszego wdrożeń, dopóki jej został wyczyszczony lub wymiany.
- Jeśli hosta jest zła, ma problemy podczas procesu ponownego uruchomienia natychmiastowego ponownego uruchamiania maszyn wirtualnych na hoście działa prawidłowo. 

Z hosta i maszyny Wirtualnej monitorowanie oferowane przez Azure maszynach wirtualnych platformy Azure, w których występują problemy dotyczące hosta są automatycznie uruchamiane ponownie w dobrej kondycji host platformy Azure. 

Drugi funkcja, która zależne w tym scenariuszu jest fakt, że usługa HANA, która działa na maszynie Wirtualnej uruchomić ją ponownie uruchamia się automatycznie po maszyny Wirtualnej wykonuje ponowny rozruch. Możesz skonfigurować [HANA usługi automatycznego ponownego uruchamiania](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) za pośrednictwem usług programu alarmowego różnych usług HANA.

W tym scenariuszu maszyn wirtualnych na jednym można zwiększyć przez dodanie węzła zimnych trybu failover do konfiguracji programu SAP HANA. W dokumentacji programu SAP HANA tej instalacji jest nazywany [hosta pracy awaryjnej automatycznie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Ta konfiguracja może być uzasadnione w sytuacji lokalnego wdrożenia gdy sprzęt serwera jest ograniczona, a dedykować węzła pojedynczego serwera jako host węzła trybu failover automatycznie dla zestawu hostów produkcyjnych. Jednak na platformie Azure, w którym podstawowej infrastruktury Azure zapewnia serwer docelowy dobrej kondycji dla powiodło się ponowne uruchomienie maszyny Wirtualnej, go nie ma sensu do wdrożenia SAP HANA hosta automatycznej pracy w trybie failover. W związku z tym nie mamy żadnych architektura referencyjna struktury przewiduje wstrzymania węzła HANA hosta auto-pracy w trybie Failover. Dotyczy to również konfiguracje skalowalnego w poziomie SAP HANA.

## <a name="availability-scenarios-for-two-different-vms"></a>Scenariusze dostępności dla dwóch różnych maszyn wirtualnych

Użycie dwóch maszyn wirtualnych platformy Azure w ramach zestawu dostępności Azure może zwiększyć przestojów między tych dwóch maszyn wirtualnych, jeśli są one umieszczone w zestawie w ramach jednego regionu Azure dostępności Azure. Podstawowej instalacji platformy Azure będzie wyglądać następująco:

![Diagram dwóch maszyn wirtualnych z warstwami wszystkie](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Aby zilustrować w scenariuszach różnych dostępności, są pomijane kilku warstw na diagramie. Na diagramie przedstawiono tylko warstwy, które przedstawiać maszyn wirtualnych, hostów zestawów dostępności i regiony platformy Azure. Azure wystąpień, grupy zasobów i subskrypcje sieci wirtualnej nie są odtwarzane roli w scenariuszach opisanych w tej sekcji.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikacja kopii zapasowych do drugiej maszyny wirtualnej

Jedną z najbardziej podstawowe ustawienia jest użycie kopii zapasowych. W szczególności konieczne może być wysłane z jednej maszyny Wirtualnej do innej maszyny Wirtualnej Azure kopie zapasowe dziennika transakcji. Można wybrać typ usługi Azure Storage. W tej konfiguracji jest odpowiedzialny za wykonywanie skryptów kopii zaplanowanych kopii zapasowych, które są wykonywane na pierwszej maszynie Wirtualnej do drugiej maszyny Wirtualnej. Należy użyć drugiej wystąpień maszyn wirtualnych, do punktu, który należy należy przywrócić pełnej, przyrostowej/różnicowej i kopie zapasowe dziennika transakcji. 

Architektura wygląda następująco:

![Diagram dwóch maszyn wirtualnych z replikacji magazynu](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Ta konfiguracja nie jest dobrze nadają się do osiągnięcia dużą czas cel punktu odzyskiwania (RPO) i celu czasu odzyskiwania (RTO). Czasy RTO szczególnie może się pogorszyć ze względu na konieczność pełnego przywrócenia pełnej bazy danych za pomocą skopiowanego kopii zapasowych. Jednak ta konfiguracja jest przydatna do odzyskiwanie przed usunięciem niezamierzone danych na głównym wystąpień. Dzięki takiej konfiguracji, w dowolnym momencie można przywrócić niektórych punktu w czasie, wyodrębniania danych i zaimportuj usunięte dane do wystąpienia głównego. W związku z tym może być uzasadnione do korzystania z metody kopii zapasowej w połączeniu z innymi funkcjami wysokiej dostępności. 

Podczas tworzenia kopii zapasowych są kopiowane, można użyć maszyny Wirtualnej mniejsze niż główny maszynę Wirtualną, która jest zasilany z wystąpieniem SAP HANA. Należy pamiętać, że mniejszej liczby dysków VHD można dołączyć do mniejszych maszyn wirtualnych. Informacje o granicach poszczególnych typach maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replikacji systemu SAP HANA bez automatycznej pracy awaryjnej

Scenariusze opisane w tej sekcji używać replikacji systemu SAP HANA. Dokumentacja programu SAP, zobacz [replikacji systemu](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Dwóch maszyn wirtualnych Azure w pojedynczym regionie Azure mają różne konfiguracje, istnieją pewne różnice w celu. Ogólnie rzecz biorąc scenariusze bez automatycznej pracy awaryjnej nie mogą być stosowane do maszyn wirtualnych w jednym regionie Azure. Jest tak, ponieważ większość awarii w infrastrukturze Azure naprawą usługi Azure uruchomieniu podstawowej maszyny Wirtualnej na innego hosta. Istnieją przypadki krawędzi, którym ta konfiguracja może pomóc w przypadku pewnych scenariuszy awarii. Lub, w niektórych przypadkach warto należy pamiętać więcej wydajności klienta.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replikacji systemu SAP HANA bez automatycznej pracy awaryjnej i bez wstępnego ładowania danych

W tym scenariuszu replikacji systemu SAP HANA służy do przenoszenia danych w sposób synchroniczne, aby osiągnąć cel punktu odzyskiwania równą 0. Z drugiej strony masz wystarczająco długie, RTO nie ma potrzeby trybu failover lub danych wstępnie ładowane do pamięci podręcznej wystąpienia HANA. W tym przypadku jest to możliwe do uzyskania dalszego gospodarka w konfiguracji, wykonując następujące czynności:

- Uruchomione inne wystąpienie SAP HANA w drugiej maszyny Wirtualnej. Wystąpienie SAP HANA w drugiej maszyny Wirtualnej ma większość pamięci maszyny wirtualnej. Zazwyczaj jest to w przypadku wystąpienia pracy awaryjnej do drugiej maszyny Wirtualnej. Drugi maszynę Wirtualną można zamknąć tak, aby replikowane dane mogą zostać załadowane do pamięci podręcznej docelowego wystąpienia HANA w drugiej maszyny Wirtualnej.
- Użyj mniejszego rozmiaru maszyny Wirtualnej na Maszynie wirtualnej drugiego. Jeśli do pracy awaryjnej, należy dodatkowych czynności przed ręcznego przełączania trybu failover. W tym kroku Zmień rozmiar maszyny Wirtualnej do rozmiaru źródłowej maszyny Wirtualnej. Scenariusz wygląda następująco:

![Diagram dwóch maszyn wirtualnych z replikacji magazynu](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Nawet jeśli nie używasz wstępnego ładowania danych w celu replikacji HANA systemu należy co najmniej 64 GB pamięci. Należy również wystarczającej ilości pamięci, oprócz 64 GB do przechowywania danych magazynu wierszy w pamięci obiektu docelowego.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replikacji systemu SAP HANA bez automatycznej pracy awaryjnej i wstępnego ładowania danych

W tym scenariuszu są wstępnie ładowane dane, które są replikowane do wystąpienia HANA w drugiej maszyny Wirtualnej. Eliminuje to dwa zalet nie wstępnego ładowania danych. W takim przypadku nie można uruchomić innego systemu SAP HANA na drugiej maszyny Wirtualnej. Nie można również użyć mniejszego rozmiaru maszyny Wirtualnej. W związku z tym klienci rzadko wdrożenia tego scenariusza.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replikacji systemu SAP HANA z automatycznej pracy awaryjnej

Standard i najbardziej typowych konfiguracji dostępności w ramach jednego regionu Azure, dwie maszyny wirtualne Azure systemem SLES Linux ma zdefiniowany klastra pracy awaryjnej. Klaster systemu Linux SLES opiera się na [rozrusznik](http://www.linux-ha.org/wiki/Pacemaker) framework w połączeniu z [STONITH](http://linux-ha.org/wiki/STONITH) urządzenia. 

Z perspektywy SAP HANA używany tryb replikacji jest synchronizowany i skonfigurowano automatycznej pracy awaryjnej. W drugiej maszyny Wirtualnej z wystąpieniem SAP HANA działa jako węzeł rezerwy dynamicznej. Rezerwy węzeł odbiera strumień synchroniczne zmian rekordów z podstawowego wystąpienia SAP HANA. Jako transakcje są zatwierdzone przez aplikację w węźle podstawowym HANA, węzła podstawowego HANA oczekuje się, aby potwierdzić zatwierdzenia aplikacji, aż do węzła pomocniczego SAP HANA potwierdza, że odebrała rekordu zatwierdzania. SAP HANA oferuje dwa tryby synchronicznej replikacji. Aby uzyskać szczegółowe informacje i opis różnic między tych dwóch trybów Replikacja synchroniczna, zobacz artykuł SAP [tryby replikacji dla replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Ogólna konfiguracja wygląda następująco:

![Diagram dwóch maszyn wirtualnych z replikacji magazynu i pracy awaryjnej](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Można to rozwiązanie, ponieważ pozwala na osiągnięcie RPO = 0 i bardzo małych RTO. Skonfiguruj łączności klienta SAP HANA tak, aby klienci SAP HANA połączyć HANA konfiguracji replikacji systemu za pomocą wirtualnego adresu IP. Eliminuje to konieczność zmiany konfiguracji aplikacji, jeśli występuje trybu failover w węźle pomocniczym. W tym scenariuszu jednostki SKU maszyny Wirtualnej platformy Azure dla maszyn wirtualnych podstawowe i pomocnicze muszą być takie same.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania tych konfiguracji na platformie Azure zobacz:

- [Konfigurowanie replikacji systemu SAP HANA w maszynach wirtualnych platformy Azure](sap-hana-high-availability.md)
- [Wysoka dostępność dla programu SAP HANA za pomocą replikacji systemu](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Aby uzyskać więcej informacji na temat dostępności SAP HANA w regionach platformy Azure zobacz:

- [Dostępność SAP HANA w regionach platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

