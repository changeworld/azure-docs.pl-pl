---
title: Omówienie rozwiązania serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: W tym artykule opisano obsługę poziomów StorSimple, urządzenia, urządzenia wirtualnego, usług i zarządzania magazynem i wprowadza kluczowe terminy używane w StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: v-sharos@microsoft.com
ms.openlocfilehash: b0d6a797e4fe84ce2cb969d001ff96177f6ac641
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097050"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Z serii StorSimple 8000: rozwiązania magazynu hybrydowego chmury
## <a name="overview"></a>Przegląd
Witamy w Microsoft Azure StorSimple, zintegrowane pamięci masowej, który zarządza zadań magazynu między urządzeniami lokalnymi i magazynu w chmurze Microsoft Azure. StorSimple to rozwiązanie sieci SAN obszaru wydajne, ekonomiczne i łatwością zarządzać magazynu, które eliminuje wiele problemów i koszty związane z ochroną magazyn i dane przedsiębiorstwa. Go używa zastrzeżonych urządzenia serii StorSimple 8000, integruje się z usługami w chmurze i zapewnia zestaw narzędzi do zarządzania dla bezproblemowego widoku wszystkie magazynu przedsiębiorstwa, w tym magazynie w chmurze. (Informacje o wdrożenia StorSimple opublikowane w witrynie sieci Web Microsoft Azure dotyczy tylko urządzeń z serii StorSimple 8000. Jeśli używasz urządzenia z serii StorSimple 5000/7000, przejdź do [pomocy StorSimple](http://onlinehelp.storsimple.com/).)

Używa StorSimple [warstwy magazynowania](#automatic-storage-tiering) do zarządzania dane przechowywane w różnych nośników magazynowania. Bieżący zestaw roboczy jest przechowywanego lokalnie na dyskach półprzewodnikowych (SSD), rzadziej używane dane są przechowywane na dyskach twardych (HDD) i dane archiwalne zostanie przeniesiona do chmury. Ponadto StorSimple używa kompresji i deduplikacji Aby zmniejszyć ilość miejsca w magazynie, który wykorzystuje dane. Aby uzyskać więcej informacji, przejdź do [kompresji i deduplikacji](#deduplication-and-compression). Definicje inne kluczowe terminy i pojęcia używane w dokumentacji serii StorSimple 8000, przejdź do [terminologii StorSimple](#storsimple-terminology) na końcu tego artykułu.

Oprócz zarządzania magazynem funkcji ochrony danych StorSimple umożliwiają tworzenie na żądanie i zaplanowane tworzenie kopii zapasowych, a następnie zapisać je lokalnie lub w chmurze. Kopie zapasowe są pobierane w formularzu przyrostowe migawki, co oznacza, że można je utworzyć i szybko przywrócić. Migawki w chmurze może być bardzo ważny w scenariuszach odzyskiwania po awarii, ponieważ zastąpić systemy dodatkowej magazynu (na przykład kopii zapasowej na taśmie) i umożliwiają przywrócenie danych do centrum danych lub alternatywnej witryn, w razie potrzeby.

![ikona wideo](./media/storsimple-overview/video_icon.png) Obejrzyj film szybkie wprowadzenie do programu Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Dlaczego warto używać StorSimple?
W poniższej tabeli opisano niektóre najważniejsze zalety dostępnych w Microsoft Azure StorSimple.

| Cecha | Korzyść |
| --- | --- |
| Integracja przezroczyste |Używa protokołu iSCSI, aby połączyć w sposób niewidoczny magazynów danych. To zapewnia, że dane przechowywane w chmurze, w centrum danych, lub na serwerach zdalnych wydaje się być przechowywane w jednym miejscu. |
| Magazyn zmniejszenie kosztów |Przydziela wystarczające lokalnego lub magazynu w chmurze, do spełnienia wymagań bieżącego i rozszerza magazynu w chmurze tylko wtedy, gdy jest to konieczne. Dodatkowo zmniejsza wymagania dotyczące magazynu i kosztów przez wyeliminowanie nadmiarowe wersje tych samych danych (deduplikacji) i przy użyciu kompresji. |
| Uproszczone zarządzanie magazynem |Zapewnia system narzędzi administracji do konfigurowania i zarządzania dane przechowywane lokalnie, na zdalnym serwerze i w chmurze. Ponadto można zarządzać kopii zapasowej i przywracanie z przystawką Microsoft Management Console (MMC).|
| Ulepszonych funkcji odzyskiwania i zgodność |Nie wymaga godzina rozszerzoną odzyskiwania. Zamiast tego należy go przywraca dane, jest wymagana. Oznacza to, że minimalnego przerw w działaniu można kontynuować wykonywania normalnych operacji. Ponadto można skonfigurować zasady, aby określić harmonogramy tworzenia kopii zapasowej i przechowywania danych. |
| Przenoszenia danych |Dane przekazane do usługi w chmurze Microsoft Azure są dostępne w innych witrynach celów odzyskiwania i migracji. StorSimple można użyć do konfigurowania urządzeń chmury StorSimple na maszynach wirtualnych (VM) działają na platformie Microsoft Azure. Maszyn wirtualnych można następnie użyć urządzeń wirtualnych dostępu do przechowywanych danych na potrzeby testu lub odzyskiwania. |
| Ciągłość działalności biznesowej |Umożliwia użytkownikom serii 5000 7000 migracji ich danych na urządzeniu z serii StorSimple 8000 StorSimple. |
| Dostępność w portalu Azure dla instytucji rządowych |StorSimple jest dostępna w portalu Azure dla instytucji rządowych. Aby uzyskać więcej informacji, zobacz [wdrażanie lokalnego urządzenia StorSimple w portalu dla instytucji rządowych](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Ochrona danych i dostępność |Serii StorSimple 8000 obsługuje strefy nadmiarowego magazynu (ZRS), oprócz magazynu lokalnie nadmiarowego magazynu (LRS) i geograficznie nadmiarowego (GRS). Zapoznaj się [w tym artykule na temat usługi Azure Storage nadmiarowość opcji](https://azure.microsoft.com/documentation/articles/storage-redundancy/) szczegółowe ZRS. |
| Obsługa kluczowych aplikacji |StorSimple umożliwia zidentyfikowanie odpowiednich woluminów przypiętych lokalnie, który z kolei zapewnia, że dane wymagane przez aplikacje krytyczne jest nie do warstwy do chmury. Woluminów przypiętych lokalnie nie są chmury opóźnienia i problemy z połączeniem. Aby uzyskać więcej informacji na temat woluminów przypiętych lokalnie, zobacz [Zarządzanie woluminami przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manage-volumes-u2.md). |
| Małe opóźnienia i wysokiej wydajności |Można utworzyć chmury urządzenia, które korzystają z wysoka wydajność, małe opóźnienia funkcji magazynu Azure premium. Aby uzyskać więcej informacji na temat StorSimple premium chmury urządzenia, zobacz [wdrażanie i zarządzanie nimi urządzenia StorSimple chmury Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Składniki StorSimple
Rozwiązanie Microsoft Azure StorSimple obejmuje następujące składniki:

* **Microsoft Azure StorSimple urządzenia** — lokalnej tablicy magazynu hybrydowego zawierający dyski SSD i dysków twardych, wraz z nadmiarowe kontrolery i funkcji automatycznej pracy awaryjnej. Kontrolery zarządzania magazynem, dodając funkcje warstw, umieszczania aktualnie używane (lub gorących) danych w magazynie lokalnym (w lokalnej lub urządzenia serwerów), podczas przenoszenia rzadziej używanych danych do chmury.
* **Urządzenia chmury StorSimple** — znanej także jako urządzenie wirtualne StorSimple jest wersję oprogramowania urządzenia StorSimple, który replikuje architektury i większości funkcji na urządzeniu magazynu fizycznego hybrydowego. Urządzenia StorSimple w chmurze jest uruchamiany w jednym węźle w maszynie wirtualnej platformy Azure. Urządzeń wirtualnych Premium, których skorzystać z magazynu Azure premium, są dostępne w wersji Update 2 lub nowszy.
* **Usługa Menedżera urządzeń StorSimple** — jako rozszerzenia portalu Azure, umożliwiająca zarządzanie urządzenia StorSimple lub urządzenia do chmury StorSimple z interfejsem w jednej sieci web. Usługę Menedżer StorSimple urządzenia umożliwia tworzenie i zarządzanie usługami, wyświetlanie i zarządzanie urządzeniami, wyświetlanie alertów, zarządzanie woluminami i wyświetlić i zarządzanie zasad tworzenia kopii zapasowych i katalogu kopii zapasowej.
* **Program Windows PowerShell dla StorSimple** — interfejsu wiersza polecenia, który służy do zarządzania urządzeniem StorSimple. Program Windows PowerShell dla StorSimple zawiera funkcje, które umożliwiają rejestrowanie urządzenia StorSimple, skonfiguruj interfejs sieciowy na urządzeniu, zainstalować niektórych typów aktualizacji, rozwiązywania problemów z urządzeniem, uzyskując dostęp do sesji pomocy technicznej i zmienić urządzenie Stan. Nawiązując połączenie z konsolą szeregową lub przy użyciu komunikacji zdalnej programu Windows PowerShell miały dostęp do programu Windows PowerShell dla StorSimple.
* **Polecenia cmdlet systemu Azure PowerShell StorSimple** — zbiór poleceń cmdlet programu Windows PowerShell, które umożliwiają automatyzację zadań poziomu usług i migrację z poziomu wiersza polecenia. Aby uzyskać więcej informacji na temat poleceń cmdlet programu Azure PowerShell dla urządzenia StorSimple, przejdź do [dokumentacji poleceń cmdlet](/powershell/module/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** — przystawki programu MMC, korzysta z grup woluminu i usługi kopiowania woluminów systemu Windows w tle do generowania kopie zapasowe spójnych z aplikacją. Ponadto można użyć programu StorSimple Snapshot Manager do tworzenia harmonogramów kopii zapasowych oraz klonowania lub Przywracanie woluminów.
* **Karta StorSimple dla programu SharePoint** — gospodarstwa narzędziem niewidocznie rozszerza Microsoft Azure StorSimple magazynu i ochrony danych na serwer programu SharePoint, podczas tworzenia magazynu StorSimple można przeglądać i łatwiejsze w zarządzaniu z centralna programu SharePoint Portalu administracyjnego.

Poniższy diagram przedstawia ogólny widok architektury Microsoft Azure StorSimple i jego składniki.

![Architektura StorSimple](./media/storsimple-overview/overview-big-picture.png)

W poniższych sekcjach opisano każdy z tych składników bardziej szczegółowo i wyjaśnić sposób rozwiązania rozmieszcza danych przydziela magazynu i ułatwia zarządzanie magazynami i ochrony danych. Ostatnia sekcja zawiera definicje dla niektórych ważnych terminów i pojęć związanych z składników StorSimple oraz zarządzania nimi.

## <a name="storsimple-device"></a>Urządzenia StorSimple
Urządzenia Microsoft Azure StorSimple jest tablicą magazynu hybrydowego lokalnymi, zapewniająca podstawowego magazynu i iSCSI dostęp do danych przechowywanych na nim. Zarządza komunikacją z magazynu w chmurze i pomaga zapewnić bezpieczeństwo i poufność wszystkich danych przechowywanych w ramach rozwiązania Microsoft Azure StorSimple.

Urządzenia StorSimple zawiera dyski SSD i HDD dyski twarde, a także obsługę klastrowania i automatycznej pracy awaryjnej. Zawiera on procesora udostępnionego, Magazyn udostępniony i dwa kontrolery dublowany. Każdy kontroler zapewnia następujące korzyści:

* Połączenie z komputerem hosta
* Maksymalnie sześć portów sieciowych do nawiązania połączenia sieci lokalnej (LAN)
* Monitorowanie sprzętu
* Pamięci RAM non-volatile (NVRAM), który zachowuje informacje, nawet jeśli zasilania zostało przerwane
* Typu cluster-aware aktualizowanie w celu zarządzania aktualizacjami oprogramowania na serwerach w klastrze trybu failover, tak, aby aktualizacje zostały minimalnego lub nie mają wpływu na dostępność usługi
* Usługa klastrowania, który działa podobnie jak klaster zaplecza, wysokiej dostępności oraz minimalizując niepożądanych, które mogą wystąpić, jeśli dysk twardy lub dysk SSD ulegnie awarii lub zostanie przełączony w tryb offline

Tylko jeden kontroler jest aktywny w dowolnym momencie w czasie. W przypadku niepowodzenia aktywnego kontrolera drugiego kontrolera automatycznie staje się aktywny.

Aby uzyskać więcej informacji, przejdź do [StorSimple składniki sprzętowe i stan](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>Urządzenie StorSimple w chmurze
StorSimple umożliwia utworzenie urządzenia chmury, który replikuje architektury i funkcji na urządzeniu magazynu fizycznego hybrydowego. Urządzenia chmury StorSimple (znanej także jako urządzenia wirtualnego StorSimple) jest uruchamiany w jednym węźle w maszynie wirtualnej platformy Azure. (Urządzenia chmury można tworzyć tylko na maszynie wirtualnej platformy Azure. Nie można utworzyć na urządzeniu StorSimple lub serwera lokalnego.)

Urządzenia chmury zawiera następujące funkcje:

* Zachowuje się jak urządzenia fizycznego, a mogą oferować interfejsu iSCSI do maszyn wirtualnych w chmurze.
* Można utworzyć nieograniczoną liczbę urządzeń chmury w chmurze i włączyć je włączać i wyłączać zgodnie z potrzebami.
* Mogą pomóc symulowania środowisk lokalnego odzyskiwania po awarii, rozwoju i scenariusze testowania i może pomóc w poziomie elementu pobierania z kopii zapasowych.

Urządzenia StorSimple w chmurze są dostępne w dwóch modeli: urządzenia 8010 (wcześniej znane jako 1100 model) i urządzenia 8020. Urządzenie 8010 ma maksymalnej pojemności 30 TB. Urządzenia 8020, które korzysta z magazynu Azure premium, ma maksymalną pojemność 64 TB. (W warstwach lokalnych magazynu Azure premium przechowuje dane na dyskach SSD należy magazynu w warstwie standardowa przechowuje dane na dyskach HDD.) Należy pamiętać, że musi mieć konto magazynu Azure premium do użycia magazyn w warstwie premium. Aby uzyskać więcej informacji na temat magazyn w warstwie premium, przejdź do [magazyn w warstwie Premium: magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure](../virtual-machines/windows/premium-storage.md).

Aby uzyskać więcej informacji dotyczących urządzenia StorSimple w chmurze, przejdź do [wdrażanie i zarządzanie nimi urządzenia StorSimple chmury Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Usługa menedżera urządzeń StorSimple
Microsoft Azure StorSimple udostępnia interfejs użytkownika sieci web (usługa Menedżera urządzeń StorSimple) umożliwia centralne zarządzanie Centrum danych i magazynu w chmurze. Usługę Menedżer StorSimple urządzenia umożliwia wykonywanie następujących zadań:

* Skonfiguruj ustawienia systemu dla urządzenia StorSimple.
* Konfigurowanie i zarządzanie ustawieniami zabezpieczeń urządzenia StorSimple.
* Konfigurowanie poświadczeń w chmurze i właściwości.
* Konfigurowanie i zarządzanie nimi woluminów na serwerze.
* Konfigurowanie grup woluminu.
* Tworzenie kopii zapasowej i przywracania danych.
* Monitorowanie wydajności.
* Przejrzyj ustawienia systemu i identyfikowania potencjalnych problemów.

Usługę Menedżer StorSimple urządzenia służy do wykonywania wszystkich zadań administracyjnych, z wyjątkiem tych, które wymagają systemu czasie, takiej jak początkowej konfiguracji i instalację aktualizacji.

Aby uzyskać więcej informacji, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Program Windows PowerShell dla StorSimple
Program Windows PowerShell dla StorSimple udostępnia interfejs wiersza polecenia, który umożliwia tworzenie i zarządzanie usługą Microsoft Azure StorSimple i konfigurowanie i monitorowanie urządzeń StorSimple. Jest oparte na środowisku Windows PowerShell, interfejsu wiersza polecenia, zawierającą dedykowanych polecenia cmdlet do zarządzania urządzeniem StorSimple. Program Windows PowerShell dla StorSimple zawiera funkcje, które pozwalają na:

* Zarejestruj urządzenie.
* Skonfiguruj interfejs sieciowy na urządzeniu.
* Zainstalować niektórych typów aktualizacji.
* Rozwiązywania problemów z urządzeniem, uzyskując dostęp do sesji pomocy technicznej.
* Zmiany stanu urządzenia.

Jest dostępne środowisko Windows PowerShell dla urządzenia StorSimple z konsoli szeregowej (na komputerze hosta podłączonym bezpośrednio do urządzenia) lub zdalnie przy użyciu komunikacji zdalnej programu Windows PowerShell. Należy pamiętać, że niektóre środowiska Windows PowerShell dla StorSimple zadania, takie jak Rejestracja wstępna urządzenia jest możliwe tylko na konsoli szeregowej.

Aby uzyskać więcej informacji, przejdź do [Użyj środowiska Windows PowerShell dla urządzenia StorSimple do administrowania urządzenia](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Polecenia cmdlet programu PowerShell StorSimple systemu Azure
Polecenia cmdlet programu Azure PowerShell StorSimple to zbiór poleceń cmdlet programu Windows PowerShell, które umożliwiają automatyzację zadań poziomu usług i migrację z poziomu wiersza polecenia. Aby uzyskać więcej informacji na temat poleceń cmdlet programu Azure PowerShell dla urządzenia StorSimple, przejdź do [dokumentacji poleceń cmdlet](/powershell/module/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC) używanego w celu utworzenia spójnych, kopie zapasowe w momencie lokalnych i danych w chmurze. Przystawka działa na hoście z systemem Windows Server. Można użyć programu StorSimple Snapshot Manager do:

* Konfigurowanie, tworzenie kopii zapasowej i usuwanie woluminów.
* Konfigurowanie grup woluminu, aby upewnić się, którego kopię zapasową danych jest spójne z aplikacjami.
* Zarządzanie zasad tworzenia kopii zapasowych, tak aby dane kopii zapasowej na uprzednio określonym harmonogramem i przechowywane w wyznaczonym lokalizacji (lokalnie lub w chmurze).
* Przywracanie woluminów i poszczególnych plików.

Kopie zapasowe są przechwytywane jako migawki, które rejestrowania tylko zmian, ponieważ została wykonana ostatnia migawka i wymaga znacznie mniej miejsca niż pełne kopie zapasowe. Można utworzyć harmonogramy tworzenia kopii zapasowej lub korzystać z bezpośrednim kopii zapasowych zgodnie z potrzebami. Ponadto służy StorSimple Snapshot Manager do ustanowienia zasad przechowywania tego formantu, ile migawki zostaną zapisane. Następnie należy przywrócić dane z kopii zapasowej, umożliwia StorSimple Snapshot Manager wybranie z katalogu lokalnego lub migawki w chmurze. 

W przypadku katastrofy lub poważnej awarii, lub jeśli potrzebujesz przywrócić dane z innego powodu, StorSimple Snapshot Manager przywracania go przyrostowo jest wymagana. Przywracanie danych nie wymaga zamknąć całego systemu podczas przywrócić plik, Zastąp sprzętu lub operacje są przenoszone do innej lokacji.

Aby uzyskać więcej informacji, przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adapter usługi StorSimple dla programu SharePoint
Microsoft Azure StorSimple zawiera kartę StorSimple dla programu SharePoint, opcjonalnym składnikiem, rozszerzający niewidocznie StorSimple magazynu i danych funkcji ochrony do farmy serwerów programu SharePoint. Karta współpracuje z dostawcy zdalnego magazynu obiektów Blob (SPZ) i funkcji programu SQL Server SPZ, co umożliwia przenoszenie obiektów blob na serwer kopii zapasowej w systemie Microsoft Azure StorSimple. Microsoft Azure StorSimple następnie przechowuje dane obiektów BLOB lokalnie lub w chmurze, na podstawie użycia.

Karta StorSimple dla programu SharePoint są obsługiwane w systemie portalu Administracja centralna programu SharePoint. W rezultacie pozostanie scentralizowanego zarządzania programu SharePoint, a wszystkie magazyny wydaje się być w farmie programu SharePoint.

Aby uzyskać więcej informacji, przejdź do [karty StorSimple dla programu SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologie magazynowania zarządzania
Oprócz dedykowanego urządzenia StorSimple, urządzenie wirtualne i inne składniki Microsoft Azure StorSimple używa następujące technologie zapewniają szybki dostęp do danych i zmniejszenie zużycia pamięci masowej:

* [Warstwy magazynowania automatyczne](#automatic-storage-tiering) 
* [Alokowanie elastyczne](#thin-provisioning) 
* [Na potrzeby deduplikacji i kompresji](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Warstwy magazynowania automatyczne
Microsoft Azure StorSimple automatycznie rozmieszcza danych w warstwach logiczną na podstawie bieżącego użycia, wieku i relacji z innymi danymi. Dane, które są najbardziej aktywne są przechowywane lokalnie, podczas gdy mniej aktywnych i nieaktywnych dane są automatycznie migrowane do chmury. Na poniższym diagramie przedstawiono takie podejście magazynu.

![Warstwy magazynowania StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Aby włączyć dostęp, StorSimple przechowuje dane bardzo aktywny (gorących danych) na dyskach SSD w urządzeniu StorSimple. Przechowuje dane, które jest czasami używana (ogrzewać danych) na dysków twardych w urządzeniu lub na serwerach w centrum danych. Powoduje przeniesienie nieaktywnych, dane kopii zapasowej, a dane przechowywane przez archiwizacji lub celów zgodności do chmury. 

> [!NOTE]
> W wersji Update 2 lub nowszym można określić wolumin przypięty lokalnie, w którym to przypadku danych pozostaje na urządzeniu lokalnym, a nie do warstwy do chmury. 


StorSimple można dostosować i Reorganizuje dane i zmień przydziałów magazynowania jako wzorców użycia. Na przykład niektóre informacje mogą stać się mniej aktywne w czasie. Ponieważ staje się stopniowo mniej aktywne, dyski twarde, a następnie w chmurze jest migrowana z dysków SSD. Jeśli ten sam danych stanie się ponownie aktywna, jej migrowania do urządzenia magazynującego.

Proces warstw magazynu odbywa się w następujący sposób:

1. Administrator systemu konfiguruje konto magazynu w chmurze Microsoft Azure.
2. Administrator używa konsoli szeregowej i usługę Menedżer StorSimple urządzenia (uruchomionego w portalu Azure) do konfigurowania urządzenia i serwerów plików, tworzenie zasad ochrony woluminy i dane. Maszyny lokalnej (na przykład serwery plików) umożliwia dostępu do urządzenia StorSimple Internet Small Computer System Interface (iSCSI).
3. Początkowo StorSimple przechowuje dane w warstwie dysków SSD szybkiego urządzenia.
4. Jak pojemności zbliża się do warstwy dysków SSD, StorSimple deduplicates kompresuje najstarsze bloki danych i przenosi je do warstwy dysków HDD.
5. Jako pojemność podejścia warstwy dysków HDD StorSimple szyfruje najstarsze bloki danych i wysyła je bezpiecznie do konta magazynu Microsoft Azure za pośrednictwem protokołu HTTPS.
6. Microsoft Azure tworzy wiele replik dane w swoim centrum danych i zdalnego centrum danych, zapewniając, że dane mogą zostać odzyskane w przypadku awarii.
7. Gdy serwer plików żąda danych przechowywanych w chmurze, StorSimple zwraca go bezproblemowo i przechowywanie kopii w warstwie dysków SSD urządzenia StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Jak StorSimple zarządza danych w chmurze

StorSimple deduplicates danych klienta na wszystkich migawek i danych podstawowych (dane zapisane przez hosty). Podczas deduplikacji jest doskonały dla wydajność magazynu, ułatwia pytanie "co to jest w chmurze" skomplikowane. Warstwowych danych podstawowych i dane migawki nakładają się na siebie. Pojedynczy fragmentów danych w chmurze mogą być użyte jako warstwowych danych podstawowych i się też odwoływać linie kilka migawki. Co migawka w chmurze zapewnia, że kopię wszystkich danych w chwili jest zablokowany do chmury, aż do usunięcia tej migawki.

Danych jest tylko usunąć z chmury, gdy nie są żadnych odwołań do tych danych. Na przykład, jeśli traktujemy migawkę chmury wszystkich danych, który znajduje się w urządzeniu StorSimple, a następnie usuń niektóre podstawowe dane będą widzimy _danych podstawowych_ porzucić natychmiast. _Danych w chmurze_ w tym warstwowych danych i tworzenie kopii zapasowych, pozostaje taki sam. Jest tak, ponieważ nadal odwołuje się do danych w chmurze migawki. Po chmurze migawka jest usuwana (i innych migawki, która odwołuje się do tych samych danych), zużycia porzucania w chmurze. Przed usuwamy danych w chmurze, sprawdzamy, czy nie migawki nadal odwołują się dane. Ten proces jest nazywany _wyrzucanie elementów bezużytecznych_ i jest uruchomiona usługa tła na urządzeniu. Usunięcie danych w chmurze nie jest bezpośrednim usługi kolekcji garbage sprawdza, czy inne odwołania do tych danych przed usunięciem. Całkowitej liczby migawek i wszystkich danych zależy od szybkości operacji wyrzucania elementów bezużytecznych. Zazwyczaj danych w chmurze jest czyszczona w mniej niż tydzień.


### <a name="thin-provisioning"></a>Alokowanie elastyczne
Alokowanie elastyczne jest technologii wirtualizacji, dostępny magazyn prawdopodobnie przekracza zasobów fizycznych. Zamiast z wyprzedzeniem rezerwowania wystarczającej ilości miejsca, StorSimple używa alokowania elastycznego można przydzielić tylko za mało miejsca, aby spełniały bieżące wymagania. Elastyczne rodzaj magazynu w chmurze ułatwia takie podejście, ponieważ StorSimple można zwiększyć lub zmniejszyć do zmieniających się wymagań dotyczących magazynu w chmurze.

> [!NOTE]
> Woluminów przypiętych lokalnie nie są alokowane udostępnione. Magazyn przydzielony do woluminu tylko lokalne jest obsługiwana w całości, podczas tworzenia woluminu.


### <a name="deduplication-and-compression"></a>Na potrzeby deduplikacji i kompresji
Microsoft Azure StorSimple używa kompresji danych i deduplikacji w celu dalszego ograniczenia wymagania dotyczące magazynu.

Funkcja deduplikacji zmniejsza ogólną ilość danych przechowywanych przez wyeliminowanie nadmiarowości w zestawie przechowywanych danych. Informacje o zmian StorSimple ignoruje niezmienione dane i przechwytywanie tylko zmiany. Ponadto StorSimple zmniejsza ilość przechowywanych danych poprzez identyfikowanie i usuwanie niepotrzebnych informacji. 

> [!NOTE]
> Dane dotyczące woluminów przypiętych lokalnie nie jest deduplikowany lub skompresowane. Jednak kopii zapasowych woluminów przypiętych lokalnie jest deduplikowany i skompresowane.


## <a name="storsimple-workload-summary"></a>Podsumowanie obciążenia StorSimple
Podsumowanie obsługiwanych obciążeniach StorSimple jest przedstawione w poniższej tabeli.

| Scenariusz | Obciążenie | Obsługiwane | Ograniczenia | Wersja |
| --- | --- | --- | --- | --- |
| Współpraca |Udostępnianie plików |Yes | |Wszystkie wersje |
| Współpraca |Udostępnianie plików rozproszonych |Yes | |Wszystkie wersje |
| Współpraca |Sharepoint |Tak* |Obsługiwane tylko w przypadku woluminów przypiętych lokalnie |Aktualizacja 2 lub nowszej |
| Archiwizacja |Archiwizowanie pliku prostego |Yes | |Wszystkie wersje |
| Wirtualizacja |Maszyny wirtualne |Tak* |Obsługiwane tylko w przypadku woluminów przypiętych lokalnie |Aktualizacja 2 lub nowszej |
| Database (Baza danych) |SQL |Tak* |Obsługiwane tylko w przypadku woluminów przypiętych lokalnie |Aktualizacja 2 lub nowszej |
| Monitorowania wideo |Monitorowania wideo |Tak* |Obsługiwane, gdy urządzenie StorSimple, jest przeznaczona wyłącznie do tego obciążenia |Aktualizacja 2 lub nowszej |
| Backup |Podstawowy docelowy kopii zapasowej |Tak* |Obsługiwane, gdy urządzenie StorSimple, jest przeznaczona wyłącznie do tego obciążenia |Aktualizacja 3 lub nowszym |
| Backup |Dodatkowej docelowy kopii zapasowej |Tak* |Obsługiwane, gdy urządzenie StorSimple, jest przeznaczona wyłącznie do tego obciążenia |Aktualizacja 3 lub nowszym |

*Tak&#42; — ograniczenia i wskazówki dotyczące rozwiązania powinny być stosowane.*

Następujące obciążenia nie są obsługiwane przez urządzenia z serii StorSimple 8000. Jeśli wdrażana na StorSimple, te obciążenia spowoduje nieobsługiwaną konfiguracją.

* Medyczne imaging
* Exchange
* VDI
* Oracle
* SAP
* Dane Big Data
* Dystrybucja zawartości
* Rozruch z SCSI

Poniżej znajduje się lista składników infrastruktury StorSimple obsługiwane.

| Scenariusz | Obciążenie | Obsługiwane | Ograniczenia | Wersja |
| --- | --- | --- | --- | --- |
| Ogólne |ExpressRoute |Yes | |Wszystkie wersje |
| Ogólne |DataCore FC |Tak* |Obsługiwane z DataCore SANsymphony |Wszystkie wersje |
| Ogólne |DFSR |Tak* |Obsługiwane tylko w przypadku woluminów przypiętych lokalnie |Wszystkie wersje |
| Ogólne |Indeksowanie |Tak* |Dla woluminów warstwowych, jest obsługiwane tylko metadane indeksowania (a nie dane).<br>Dla woluminów przypiętych lokalnie pełną indeksowania jest obsługiwane. |Wszystkie wersje |
| Ogólne |Oprogramowanie antywirusowe |Tak* |Dla woluminów warstwowych jest obsługiwane tylko skanowanie podczas otwierania i zamknij.<br> Dla woluminów przypiętych lokalnie pełne skanowanie jest obsługiwane. |Wszystkie wersje |

*Tak&#42; — ograniczenia i wskazówki dotyczące rozwiązania powinny być stosowane.*

Poniżej znajduje się lista innego oprogramowania, które są używane przez StorSimple do tworzenia rozwiązań.

| Typ obciążenia | Oprogramowanie używane z StorSimple | Obsługiwane wersje|Łącze do przewodnika dotyczącego rozwiązań| 
| --- | --- | --- | --- |
| Miejsce docelowe kopii zapasowej |Veeam |V Veeam 9 lub nowszy |[StorSimple jako miejsce docelowe kopii zapasowej z Veaam](storsimple-configure-backup-target-veeam.md)|
| Miejsce docelowe kopii zapasowej |Exec VERITAS kopii zapasowej |Exec kopii zapasowej 16 i nowsze |[StorSimple jako miejsce docelowe kopii zapasowej z kopii zapasowej Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Miejsce docelowe kopii zapasowej |VERITAS NetBackup |NetBackup 7.7.x i nowsze  |[StorSimple jako miejsce docelowe kopii zapasowej z NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Udostępnianie plików globalne <br></br> Współpraca |Talon  |[StorSimple z Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologia StorSimple
Przed wdrożeniem rozwiązania Microsoft Azure StorSimple, firma Microsoft zaleca zapoznanie się następujące pojęcia i definicje.

### <a name="key-terms-and-definitions"></a>Kluczowe terminy i definicje
| Termin (akronim lub skrót) | Opis |
| --- | --- |
| rekord kontroli dostępu (ACR) |Rekord skojarzony z woluminu na urządzenia Microsoft Azure StorSimple, który określa, które hosty mogą się z nim połączyć. Określenie jest oparty na protokole iSCSI kwalifikowana nazwa (IQN) z hostów (zawarte w ACR) łączących się z urządzeniem StorSimple. |
| AES-256 |Algorytm Advanced Encryption (Standard AES) 256-bitowego szyfrowania danych przesyłane do i z chmury. |
| rozmiar jednostki alokacji (AUS) |Najmniejsza ilość miejsca na dysku, która może być przydzielona do przechowywania pliku w oknach systemy plików. Jeśli rozmiar pliku nie jest wielokrotnością rozmiaru klastra, należy użyć dodatkowego miejsca do przechowywania plików (do następnego wielokrotnością rozmiaru klastra) co spowoduje utratę miejsca i fragmentacji dysku twardego. <br>Zalecane AUS woluminów Azure StorSimple to 64 KB, ponieważ działa ona również z algorytmami deduplikacji. |
| warstwy magazynowania automatycznych |Automatycznie przenoszenie mniej aktywnych danych z dysków SSD dyski twarde, a następnie warstwy w chmurze, a następnie włączenie zarządzania wszystkie magazyny z interfejsem użytkownika centralnej. |
| katalog kopii zapasowej |Kolekcja kopii zapasowych, zwykle są powiązane przez typ aplikacji, która została użyta. Ta kolekcja zostanie wyświetlona w bloku katalogu kopii zapasowej usługi Menedżer StorSimple urządzenia interfejsu użytkownika. |
| Plik kopii zapasowej katalogu |Plik zawierający listę dostępnych migawek obecnie przechowywane w kopii zapasowej bazy danych programu StorSimple Snapshot Manager. |
| zasady tworzenia kopii zapasowej |Wybór woluminów, typ kopii zapasowej i harmonogram, która pozwala na tworzenie kopii zapasowych na wstępnie zdefiniowanego harmonogramu. |
| duże obiekty binarne (BLOB) |Kolekcja przechowywane jako pojedynczej jednostki w system zarządzania bazami danych binarnych. Obiekty BLOB są zwykle obrazy, audio lub inne obiekty multimedialne, chociaż czasami binarnego kodu wykonywalnego są przechowywane jako obiekt BLOB. |
| Protokół uwierzytelniania typu Challenge Handshake (CHAP) |Protokół używany do uwierzytelniania elementu równorzędnego połączenia oparte na element równorzędny udostępnianie hasła lub klucza tajnego. Protokół CHAP mogą być jednokierunkowe lub wzajemnego. Za pomocą jednokierunkowego protokołu CHAP element docelowy uwierzytelnia inicjatora. Wzajemne CHAP wymaga aby obiekt docelowy uwierzytelniania inicjatora i że inicjatora uwierzytelnia obiektu docelowego. |
| Klonowania |Duplikat woluminu. |
| Chmura warstwy (CaaT) |Magazyn zintegrowana warstwy w ramach architektury magazynu, dzięki czemu wszystkie magazyny wydaje się być częścią sieci magazynu jednego przedsiębiorstwa w chmurze. |
| dostawcy usług w chmurze (CSP) |Dostawca rozwiązań usług w chmurze. |
| Migawka w chmurze |W momencie kopię danych woluminu, który jest przechowywany w chmurze. Migawka w chmurze jest odpowiednikiem migawki replikowane w systemie magazynu innych, poza nim. Migawki w chmurze są szczególnie przydatne w scenariuszach odzyskiwania po awarii. |
| klucz szyfrowania magazynu w chmurze |Hasło lub klucz używany przez urządzenie StorSimple dostępu do zaszyfrowanych danych wysłanych przez urządzenia do chmury. |
| Aktualizacja typu cluster-aware |Zarządzanie aktualizacje oprogramowania na serwerach w klastrze pracy awaryjnej, aby aktualizacje zostały minimalnego lub nie mają wpływu na dostępność usługi. |
| ścieżki danych |Kolekcja jednostki organizacyjne, które wykonują operacje wzajemnie połączonych przetwarzania danych. |
| dezaktywuj |Stałe akcja, która spowoduje przerwanie połączenia między urządzeniem StorSimple i usługę skojarzonej chmury. Migawki w chmurze urządzenia po ten proces i można sklonować lub używanych na potrzeby odzyskiwania po awarii. |
| dublowanie dysków |Replikacji woluminów dysku logicznego na oddzielnym twardych dysków w czasie rzeczywistym w celu zapewnienia ciągłej dostępności. |
| dublowanie dysku dynamicznego |Replikacja dysku logicznego woluminów dysków dynamicznych. |
| dyski dynamiczne |Format wolumin dysku, który używa Menedżera dysków logicznych (LDM) do przechowywania danych i zarządzać nimi na wielu dyskach fizycznych. Dyski dynamiczne można uruchomić zapewniają więcej wolnego miejsca. |
| Rozszerzone obudowa grupy dysków (EBOD) |Dodatkowej obudowy urządzenia Microsoft Azure StorSimple, który zawiera dyski dodatkowego dysku twardego dla dodatkowego miejsca do magazynowania. |
| Inicjowanie obsługi administracyjnej FAT |Konwencjonalne Inicjowanie obsługi administracyjnej magazynu w magazynie, które przydzielone miejsce na podstawie przewidywanych potrzeb (i zazwyczaj znajduje się poza bieżący konieczność). Zobacz też *alokowanie*. |
| dysk twardy (HDD) |Dysk, który używa obracania płyt do przechowywania danych. |
| Magazyn w chmurze hybrydowej |Architektura magazynu, która używa zasobów lokalnych i poza nim, w tym magazynie w chmurze. |
| Internet Small Computer System Interface (iSCSI) |Magazynu opartego na protokole IP standard sieciowy do łączenia urządzeń magazynu danych lub urządzeń. |
| Inicjator iSCSI |Składnik oprogramowania, który umożliwia komputerze hosta z systemem Windows do nawiązania połączenia sieci zewnętrznych magazynu opartego na protokole iSCSI. |
| iSCSI kwalifikowana nazwa (IQN) |Unikatowa nazwa identyfikująca obiektów docelowych iSCSI lub inicjatora. |
| obiekt docelowy iSCSI |Składnik oprogramowania, który zapewnia scentralizowane iSCSI podsystemów dysków w sieci magazynowania. |
| na żywo archiwizacji |Podejście magazynu, w którym dane archiwalne jest dostępny przez cały czas (nie są przechowywane wcześniejsze jego na taśmie, na przykład). Microsoft Azure StorSimple używa archiwizacji na żywo. |
| woluminu przypiętego lokalnie |wolumin, który znajduje się na urządzeniu, a nie do warstwy do chmury. |
| Migawka lokalna |W momencie kopię danych woluminu, który jest przechowywany na urządzeniu Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Wydajnym rozwiązaniem, składające się z urządzenie magazynu centrum danych i oprogramowania, która umożliwia organizacjom IT wykorzystanie magazynu w chmurze tak, jakby była centrach danych. StorSimple upraszcza ochrony danych i zarządzanie danymi, ograniczając jednocześnie koszty. Rozwiązanie konsoliduje podstawowego magazynu, archiwum, kopia zapasowa i odzyskiwanie po awarii (DR) za pośrednictwem bezproblemową integrację z chmurą. Połączenie SAN zarządzanie danymi i magazynu w chmurze na platformie klasy korporacyjnej, urządzenia StorSimple umożliwić szybkości, prostota i niezawodność wszystkie potrzeby związane z magazynowaniem. |
| Zasilania i chłodzenia modułu (PCM) |Składniki sprzętowe urządzenia StorSimple składające się z zasilacze i wentylatorów, dlatego nazwa zasilania i chłodzenia modułu. Podstawowy Obudowa urządzenia ma dwa PCMs 764W obudowa EBOD ma dwa PCMs 580W. |
| Obudowa podstawowego |Główne Obudowa urządzenia StorSimple, który zawiera kontrolery platformy aplikacji. |
| celu czasu odzyskiwania (RTO) |Pełni po przywróceniu maksymalną ilość czasu, który powinien czyszcząca przed proces biznesowy lub systemu po awarii. |
| magistrali Serial attached SCSI (SAS) |Typ stacji dysków twardych (HDD). |
| klucz szyfrowania danych usługi |Klucz udostępnione do każdego nowego urządzenia StorSimple zarejestrowanie w usłudze Menedżer StorSimple urządzenia. Dane konfiguracji usługi Menedżer StorSimple urządzenia i urządzenia jest szyfrowana przy użyciu klucza publicznego, a następnie mogły być odszyfrowane tylko na urządzeniu przy użyciu klucza prywatnego. Klucz szyfrowania danych usługi umożliwia usłudze uzyskać klucz prywatny do odszyfrowywania. |
| klucz rejestracji usługi |Klucz, który ułatwia rejestrowanie urządzenia StorSimple za pomocą usługi Menedżera urządzeń StorSimple, aby był on wyświetlany w portalu Azure, aby uzyskać dodatkowe akcje zarządzania. |
| Small Computer System Interface (SCSI) |Zestaw standardy dotyczące fizycznego łączenia komputerów i przekazywania danych między nimi. |
| dysków półprzewodnikowych (SSD) |Dysk, który nie zawiera żadnych części przenoszenie; na przykład dysku flash. |
| konto magazynu |Zestaw poświadczeń dostępu, połączony z kontem magazynu dla określonej chmury dostawcy usług. |
| Adapter usługi StorSimple dla programu SharePoint |Składnik Microsoft Azure StorSimple niewidocznie rozszerza StorSimple magazynu i ochronę danych do farmy serwerów programu SharePoint. |
| Usługa menedżera urządzeń StorSimple |Rozszerzenie portalu Azure, która pozwala na zarządzanie Azure StorSimple lokalnymi i urządzeniami wirtualnego. |
| StorSimple Snapshot Manager |Microsoft Management Console (MMC) przystawki do zarządzania operacje tworzenia kopii zapasowej i przywracania w Microsoft Azure StorSimple. |
| Pobierz kopię zapasową |Funkcja, która umożliwia użytkownikowi przejęcie interakcyjne kopii zapasowej woluminu. To alternatywny sposób ręcznego kopii zapasowej woluminu zamiast biorąc automatyczne kopie zapasowe za pośrednictwem zdefiniowane zasady. |
| alokowanie elastyczne |Metoda optymalizacji wydajności, z którym dostępne miejsce jest używana w systemów pamięci masowej. W alokacji elastycznej magazynu jest przydzielany przez wielu użytkowników oparte na minimalny odstęp wymagane przez każdego użytkownika w danym momencie. Zobacz też *fat inicjowania obsługi administracyjnej*. |
| Obsługa poziomów |Organizowanie danych w logiczne grupy na podstawie bieżącego użycia, wieku i relacji z innymi danymi. StorSimple automatycznie rozmieszcza danych w warstwach. |
| wolumin |Obszary magazynu logicznego prezentowane w postaci dysków. Woluminy StorSimple odpowiadają woluminów zainstalowanych w wyniku hosta, łącznie z tymi wykryte za pomocą inicjatora iSCSI i urządzenia StorSimple. |
| kontener woluminów |Grupowanie woluminów i ustawienia, które są stosowane do nich. Wszystkie woluminy w urządzeniu StorSimple są pogrupowane w kontenery woluminów. Ustawienia kontenera woluminów obejmują kont magazynu, ustawienia szyfrowania dla danych przesyłanych do chmury za pomocą kluczy szyfrowania skojarzone i przepustowości operacji dotyczących chmury. |
| grupy woluminu |W systemie StorSimple Snapshot Manager grupy woluminu jest kolekcją skonfigurowany tak, aby ułatwić przetwarzania kopii zapasowych woluminów. |
| Usługa kopiowania woluminów w tle (VSS) |Usługa systemu operacyjnego Windows Server, która ułatwia komunikując się z aplikacji obsługujących usługę VSS do koordynowania tworzenie przyrostowych migawek spójności aplikacji. Usługa VSS zapewnia, że aplikacje są tymczasowo nieaktywne, przy tworzeniu migawek woluminów. |
| Program Windows PowerShell dla StorSimple |Oparte na programie Windows PowerShell interfejsu wiersza polecenia używane do obsługi i zarządzania urządzeniem StorSimple. Przy zachowaniu niektóre podstawowe funkcje programu Windows PowerShell, ten interfejs ma dodatkowe dedykowanych poleceń cmdlet, które są przeznaczone dla zarządzania urządzeniem StorSimple. |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).

