---
title: SAP HANA dostępności w obrębie jednego regionu platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano operacje platformy SAP HANA na maszynach wirtualnych natywnego platformy Azure w jednym regionie platformy Azure.
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
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 687012e73b4b0c869b491ac1c9ea128662b23510
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391485"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA dostępności w obrębie jednego regionu platformy Azure
W tym artykule opisano kilka scenariuszy dostępności w obrębie jednego regionu platformy Azure. Platforma Azure oferuje wiele regionów, rozkładają się na całym świecie. Aby uzyskać listę regionów świadczenia usługi Azure, zobacz [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/). Do wdrażania oprogramowania SAP HANA na maszynach wirtualnych w ramach jednego regionu platformy Azure, firma Microsoft oferuje wdrożenia pojedynczej maszyny Wirtualnej z wystąpieniem platformy HANA. Aby uzyskać większą dostępność, można wdrożyć dwóch maszyn wirtualnych z dwoma wystąpieniami platformy HANA w ramach [zestawu dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) który używa replikacji systemu HANA dostępności. 

Obecnie usługa Azure oferuje [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). W tym artykule nie opisano szczegółowo w strefach dostępności. Ale obejmuje ogólne omówienie przy użyciu zestawów dostępności w stosunku do strefy dostępności.

Regiony platformy Azure, w których są oferowane strefy dostępności mają wiele centrów danych. Centra danych są niezależne dostawa źródła zasilania, chłodzenie i usługi sieciowe. Przyczyna oferty różnych stref w jednym regionie platformy Azure jest wdrażanie aplikacji w dwóch lub trzech strefach dostępności, które są oferowane. Wdrażanie w poszczególnych strefach, problemy w usłudze Power Bi i sieć, wpływających na tylko jedną strefę dostępności platformy Azure infrastruktury, wdrażania aplikacji w regionie platformy Azure nadal działa. Mogą wystąpić pewne zmniejszenie wydajności. Na przykład maszyny wirtualne w jednej strefie mogą zostać utracone, ale maszyn wirtualnych w innych strefach będzie nadal działa. 
 
Zestawu dostępności platformy Azure jest możliwość powodują ustawienie logicznego grupowania, który pomaga zagwarantować, że zasoby maszyny Wirtualnej, które można umieścić w zestawie dostępności są błąd odizolowane od siebie nawzajem gdy są one wdrażane w ramach centrum danych platformy Azure. Maszyny wirtualne platformy Azure umieszczone w zestawie dostępności korzystają z wielu serwerów fizycznych, regałów obliczeniowych, jednostek magazynowych i przełączników sieciowych. W dokumentacji platformy Azure, ta konfiguracja jest nazywany angażowania w różnych [aktualizacji i domeny błędów](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Te angażowania zwykle znajdują się w centrum danych platformy Azure. Przy założeniu, że problemy zasilania źródła i sieci mogłyby wpłynąć na centrum danych, które są wdrażane, będzie mieć wpływ na wszystkich pojemności w jednym regionie platformy Azure.

Położenie centrów danych, które reprezentują strefy dostępności platformy Azure stanowi kompromis między zapewniając akceptowalne opóźnienie między usługami wdrożonymi w różnych strefach i odległości między centrami danych. Fizyczne awarią najlepiej nie wpływa na zasilania, sieć dostaw i infrastruktury dla wszystkich stref dostępności w tym regionie. Jednak zgodnie z awarią naturalnych pomnikowe wykazały, strefy dostępności może nie zawsze zapewniają dostępności, który ma w ramach jednego regionu. Pomyśl o Maria Huragan, który trafień Wyspa Portoryko 20 września 2017 r. Huragan zasadniczo spowodowane prawie 100 procent niedostępności na wyspie 90 mil na poziomie.

## <a name="single-vm-scenario"></a>Scenariusz pojedynczej maszyny Wirtualnej

W przypadku maszyn wirtualnych na jednym utworzysz Maszynę wirtualną platformy Azure w ramach wystąpienia platformy SAP HANA. Azure Premium Storage umożliwia hostowanie dysku systemu operacyjnego i dysków danych. Azure przestojów SLA gwarantującą dostępność na poziomie 99,9% i umowy SLA z innymi składnikami platformy Azure jest wystarczające do spełnienia umów SLA dotyczących dostępności dla swoich klientów. W tym scenariuszu użytkownik nie ma potrzeby korzystanie z zestawu dostępności platformy Azure dla maszyn wirtualnych z systemem DBMS warstwy. W tym scenariuszu możesz polegać na dwie funkcje:

- Usługa Azure VM automatycznego uruchamiania ponownego dla (zwaną także naprawianiem usług platformy Azure)
- SAP HANA automatycznego uruchamiania ponownego dla zaplanowanych

Ponownego uruchamiania automatycznego maszyny Wirtualnej platformy Azure lub naprawianiem usług, jest funkcji na platformie Azure, która działa na dwóch poziomach:

- Host serwera platformy Azure sprawdza kondycję maszyny Wirtualnej, która jest hostowana na serwerze głównym.
- Kontroler sieci szkieletowej platformy Azure monitoruje kondycję i dostępność hosta serwera.

Funkcje sprawdzania kondycji monitoruje kondycję każdego maszynę Wirtualną, która znajduje się na hoście serwera usługi Azure. Maszynę wirtualną należącą do innej dobrej kondycji, ponowne uruchomienie maszyny wirtualnej może być inicjowane przez agenta hosta platformy Azure, która sprawdza kondycję maszyny Wirtualnej. Kontroler sieci szkieletowej sprawdza kondycję hosta, zaznaczając wiele różnych parametrów, które mogą wskazywać problemy z sprzętu hosta. Sprawdza również na dostępność hosta za pośrednictwem sieci. To wskazywać problemy z hostem może prowadzić do następujących zdarzeń:

- W przypadku hosta sygnalizuje złe kondycji, ponowny rozruch hosta i ponowne uruchomienie maszyn wirtualnych, które zostały uruchomione na hoście zostanie wywołany.
- Jeśli host nie znajduje się w dobrej kondycji po pomyślnym ponownym uruchomieniu, jest inicjowane ponownego wdrażania maszyn wirtualnych, które były pierwotnie w węźle teraz w złej kondycji na serwerze hosta w dobrej kondycji. W tym przypadku pierwotny host jest oznaczona jako nie działa prawidłowo. Nie będzie można użyć w przypadku dalszych wdrożeń, dopóki jego wyczyszczone lub zastąpiony.
- Jeśli w złej kondycji hosta występują problemy podczas procesu ponownego uruchomienia, zostanie wywołany natychmiastowe ponowne uruchomienie maszyn wirtualnych na hosta w dobrej kondycji. 

Z hosta i monitorowanie maszyn wirtualnych udostępnianych przez platformę Azure maszyny wirtualne platformy Azure, w których występują problemy dotyczące hosta są automatycznie uruchamiane ponownie w dobrej kondycji hosta platformy Azure. 

>[!IMPORTANT]
>Naprawianie usługi platformy Azure nie uruchamia ponownie maszyny wirtualne systemu Linux, w którym system operacyjny gościa jest w stanie alarm jądra. Ustawienia domyślne, które często używanych wersji systemu Linux, nie automatycznego ponownego uruchamiania maszyn wirtualnych lub serwera, gdzie jądra systemu Linux jest w stanie alarm. Zamiast tego domyślnie przewiduje się do zachowania systemu operacyjnego w stanie alarm jądra, aby można było dołączyć debuger jądra w celu analizowania. Azure jest zapewniane tego zachowania nie automatycznie uruchamiając Maszynę wirtualną przy użyciu systemu operacyjnego gościa taki stan. Jest założenie, że takie wystąpienia są bardzo rzadko. Można zastąpić domyślne zachowanie, aby umożliwić ponowne uruchomienie maszyny wirtualnej. Aby zmienić domyślny parametr "kernel.panic" w /etc/sysctl.conf włączony zachowanie. Czas, gdy ustawiasz dla tego parametru jest w sekundach. Typowe zalecane wartości są czekać 20 – 30 sekund przed wyzwoleniem ponowny rozruch przy użyciu tego parametru. Zobacz też <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

Druga funkcja, które działają na w tym scenariuszu jest fakt, że ponowne uruchomienie usługi HANA, która działa na maszynie Wirtualnej uruchomiono ponownie uruchamiany automatycznie po maszyny Wirtualnej. Możesz skonfigurować [HANA usługi automatycznego uruchamiania ponownego dla zaplanowanych](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) za pośrednictwem usług strażnika różnych usług platformy HANA.

W tym scenariuszu maszyn wirtualnych na jednym można zwiększyć przez dodanie węzła zimnych trybu failover do konfiguracji oprogramowania SAP HANA. W dokumentacji platformy SAP HANA, nosi nazwę tej konfiguracji [hosta automatyczny tryb failover](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Ta konfiguracja może mieć sens w lokalne wdrożenia taka sytuacja jest którym sprzęt serwera jest ograniczona, a dedykować węzła pojedynczy serwer jako host automatyczny tryb failover węzła dla zestawu hostów w środowisku produkcyjnym. Jednak na platformie Azure, w którym podstawowej infrastruktury platformy Azure zapewnia na serwerze docelowym działa prawidłowo dla pomyślnego ponownego uruchamiania maszyny Wirtualnej, nie ma sensu wdrażania oprogramowania SAP HANA hosta automatyczny tryb failover. Ze względu na naprawianiem usług platformy Azure, nie ma żadnych architektury referencyjnej, która przewiduje wstrzymania węzeł na potrzeby oprogramowania HANA hosta automatyczny tryb failover. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Szczególny przypadek konfiguracji skalowania platformy SAP HANA na platformie Azure
Wysoka dostępność dla konfiguracji skalowania platformy SAP HANA, powołuje się na usługę naprawiania maszyny wirtualne platformy Azure i ponowne uruchomienie wystąpienia platformy SAP HANA, zgodnie z ponownym uruchomieniu maszyny Wirtualnej i ponownie uruchomić. Architektury wysokiej dostępności oparte na replikacji systemu HANA mają być wprowadzone w późniejszym czasie. 


## <a name="availability-scenarios-for-two-different-vms"></a>Dostępność scenariuszy dla dwóch różnych maszyn wirtualnych

Jeśli używasz dwóch maszyn wirtualnych platformy Azure w ramach zestawu dostępności platformy Azure, możesz zwiększyć czas pracy między tymi dwiema maszynami wirtualnymi, jeśli są one umieszczane w zestawie dostępności platformy Azure w ramach jednego regionu platformy Azure. Wyglądałyby podstawowej instalacji platformy Azure:

![Diagram przedstawiający dwóch maszyn wirtualnych przy użyciu wszystkich warstw](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Aby zilustrować dostępność różnych scenariuszy, zostały pominięte kilka warstwy na diagramie. Na diagramie przedstawiono tylko warstwy, które przedstawiać maszyn wirtualnych, hostów, zestawy dostępności i regionów platformy Azure. Azure wystąpień sieci wirtualnej, grupy zasobów i subskrypcji nie są odtwarzane rolę w scenariuszach opisanych w tej sekcji.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikacja kopie zapasowe do drugiej maszyny wirtualnej

Jednym z najbardziej podstawowe konfiguracje jest tworzenie kopii zapasowych. W szczególności Niewykluczone, że kopie zapasowe dziennika transakcji wysłanych z jednej maszyny Wirtualnej do innej maszyny Wirtualnej platformy Azure. Można wybrać typ usługi Azure Storage. W tej konfiguracji odpowiedzialność za skrypty kopii zaplanowanych kopii zapasowych, które są wykonywane na pierwszej maszynie Wirtualnej do drugiej maszyny Wirtualnej. Jeśli musisz użyć drugiej wystąpień maszyn wirtualnych, należy przywrócić pełnej, przyrostowe/różnicowej i kopie zapasowe dziennika transakcji do punktu, które są potrzebne. 

Architektura wygląda następująco:

![Diagram przedstawiający dwóch maszyn wirtualnych przy użyciu replikacji magazynu](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Ta konfiguracja nie jest dobrze nadaje się do osiągnięcia doskonałe czas cel punktu odzyskiwania (RPO) i cel czasu odzyskiwania (RTO). Czasy RTO szczególnie mogłoby to spowodować obniżenie ze względu na konieczność pełnego przywrócenia pełnej bazy danych za pomocą skopiowanego kopii zapasowych. Jednak tej konfiguracji jest przydatne w przypadku odzyskiwania przed usunięciem danych niezamierzone w wystąpieniach głównego. W przypadku takiej konfiguracji, w dowolnym momencie można przywrócić do pewnego momentu w czasie, wyodrębniania danych i zaimportować usunięte dane do swojego głównego wystąpienia. Dzięki temu sensowne do korzystania z metody kopii zapasowej w połączeniu z inne funkcje wysokiej dostępności. 

Podczas tworzenia kopii zapasowych są kopiowane, może być za pomocą maszyny Wirtualnej mniejszych niż główny maszynę Wirtualną, która wystąpienie SAP HANA jest uruchomiona na. Należy pamiętać, możesz dołączyć mniejszą liczbę wirtualnych dysków twardych na mniejszych maszynach wirtualnych. Aby uzyskać informacji na temat limitów poszczególne typy maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replikacja systemu SAP HANA bez automatycznej pracy awaryjnej

Scenariusze opisane w tej sekcji Użyj replikacji systemu SAP HANA. W dokumentacji SAP, można zobaczyć [replikacji systemu](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Scenariusze bez automatycznej pracy awaryjnej nie są typowe dla konfiguracji w ramach jednego regionu platformy Azure. Konfiguracja bez automatycznej pracy awaryjnej, chociaż unikanie instalacji program Pacemaker obligates monitorowanie i trybu failover ręcznie. Od tego przyjmuje i wysiłki również większość klientów korzysta z usługi platformy Azure, naprawy. zamiast tego. Istnieją niektóre skrajne przypadki, w którym ta konfiguracja może ułatwić pod kątem scenariuszy awarii. Lub, w niektórych przypadkach klient może chcieć weź pod uwagę większe.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replikacja systemu SAP HANA, bez automatycznego trybu failover i bez wstępnego ładowania danych

W tym scenariuszu użyjesz Replikacja systemu SAP HANA do przenoszenia danych w sposób synchroniczne, aby osiągnąć cel punktu odzyskiwania równą 0. Z drugiej strony masz wystarczająco długi, RTO, który nie jest potrzebny trybu failover lub danych wstępnie ładowane do pamięci podręcznej wystąpienia platformy HANA. W takich przypadkach istnieje możliwość uzyskania dalszego gospodarki w konfiguracji, wykonując następujące czynności:

- Uruchomione inne wystąpienie SAP HANA w drugiej maszyny Wirtualnej. Wystąpienie SAP HANA w drugiej maszyny Wirtualnej zajmuje większość pamięci maszyny wirtualnej. W przypadku, gdy tryb failover do drugiej maszyny Wirtualnej, musisz zamknięcia uruchomionego wystąpienia platformy SAP HANA, zawierające dane, w pełni załadowany w drugiej maszyny Wirtualnej, tak aby replikowane dane mogą być ładowane do pamięci podręcznej docelowym wystąpienia oprogramowania HANA w drugiej maszyny Wirtualnej.
- Na drugiej maszynie Wirtualnej, należy użyć mniejszego rozmiaru maszyny Wirtualnej. W przypadku przejścia w tryb failover masz dodatkowy krok przed ręcznej pracy awaryjnej. W tym kroku możesz zmienić rozmiar maszyny Wirtualnej do rozmiaru źródłowej maszyny Wirtualnej. 
 
Scenariusz wygląda następująco:

![Diagram przedstawiający dwóch maszyn wirtualnych przy użyciu replikacji magazynu](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Nawet wtedy, gdy wstępne załadowanie danych nie jest używana w celu replikacji systemu HANA, potrzebujesz co najmniej 64 GB pamięci. Należy również wystarczającej ilości pamięci, oprócz 64 GB pamięci, aby zachować dane magazynu wierszy w pamięci obiektu docelowego.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replikacja systemu SAP HANA, bez automatycznego trybu failover i wstępnego ładowania danych

W tym scenariuszu są wstępnie ładowane dane, które są replikowane na wystąpienie oprogramowania HANA w drugiej maszyny Wirtualnej. Eliminuje to dwie zalety nie wstępnego ładowania danych. W tym przypadku nie można uruchomić innego systemu SAP HANA na drugiej maszynie Wirtualnej. Nie można również użyć mniejszego rozmiaru maszyny Wirtualnej. Dzięki temu klienci rzadko zaimplementować ten scenariusz.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replikacja systemu SAP HANA przy użyciu automatycznej pracy awaryjnej

W standardzie najbardziej typowych konfiguracji dostępności w obrębie jednego regionu platformy Azure, dwóch maszyn wirtualnych platformy Azure z systemem Linux w systemie SLES się zdefiniowane klastra trybu failover. Klaster systemu Linux SLES opiera się na [program Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) framework, w połączeniu z [pomocą metody STONITH](http://linux-ha.org/wiki/STONITH) urządzenia. 

Z perspektywy platformy SAP HANA używany tryb replikacji jest synchronizowana, i automatycznej pracy awaryjnej jest skonfigurowany. W drugiej maszyny Wirtualnej wystąpienie SAP HANA działa jako gorąca węzła wstrzymania. Węzeł wstrzymania odbiera strumienia synchronicznego, zmiany rekordów z podstawowego wystąpienia platformy SAP HANA. Jak transakcje są zatwierdzane przez aplikację z węzła podstawowego HANA, węzła podstawowego HANA czeka, aby potwierdzić zatwierdzania do aplikacji, dopóki w węźle pomocniczym platformy SAP HANA potwierdza, że otrzymał rekordem zatwierdzania. SAP HANA oferuje dwa tryby replikacji synchronicznej. Aby uzyskać szczegółowe informacje oraz opis różnic między tymi dwoma trybami replikacji synchronicznej, zobacz artykuł SAP [tryba replikacji dla replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Konfiguracją wygląda następująco:

![Diagram przedstawiający dwóch maszyn wirtualnych przy użyciu magazynu replikacji i trybu failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Można to rozwiązanie, ponieważ umożliwia osiągnięcie celu punktu odzyskiwania = 0 i niską wartość. Skonfiguruj łączności klienta SAP HANA, aby klienci platformy SAP HANA używać wirtualnego adresu IP, aby nawiązać połączenie konfiguracji replikacji systemu HANA. Taka konfiguracja pozwala wyeliminować konieczność zmiany konfiguracji aplikacji w przypadku przejścia do trybu failover w węźle pomocniczym. W tym scenariuszu jednostek SKU maszyn wirtualnych platformy Azure dla podstawowej i dodatkowej maszynie wirtualnej musi być taka sama.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać wskazówki krok po kroku dotyczące konfigurowania tych konfiguracji na platformie Azure zobacz:

- [Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych Azure](sap-hana-high-availability.md)
- [Wysoka dostępność dla oprogramowania SAP HANA przy użyciu replikacji systemu](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Aby uzyskać więcej informacji na temat dostępności platformy SAP HANA w różnych regionach platformy Azure zobacz:

- [Dostępność platformy SAP HANA w regionach platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

