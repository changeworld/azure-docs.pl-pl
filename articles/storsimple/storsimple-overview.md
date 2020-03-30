---
title: Przegląd rozwiązań serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule StorSimple warstwy, urządzenia, urządzenia wirtualnego, usług i zarządzania magazynem i wprowadza kluczowe terminy używane w StorSimple.
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
ms.author: timlt
ms.openlocfilehash: 2a6650cac975c575415a329361da00d4fbfcaa9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965115"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Seria StorSimple 8000: hybrydowe rozwiązanie pamięci masowej w chmurze

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie
Witamy w usłudze Microsoft Azure StorSimple, zintegrowanym rozwiązaniu magazynu, które zarządza zadaniami magazynowania między urządzeniami lokalnymi a magazynem w chmurze platformy Microsoft Azure. StorSimple to wydajne, ekonomiczne i łatwe w zarządzaniu rozwiązanie sieci magazynowania (SAN), które eliminuje wiele problemów i wydatków związanych z pamięcią masową i ochroną danych w przedsiębiorstwie. Używa ona zastrzeżonego urządzenia z serii StorSimple 8000, integruje się z usługami w chmurze i udostępnia zestaw narzędzi do zarządzania w celu zapewnienia bezproblemowego widoku wszystkich magazynów przedsiębiorstwa łącznie z magazynem w chmurze. (Informacje o wdrożeniu StorSimple opublikowane w witrynie sieci Web platformy Microsoft Azure dotyczą tylko urządzeń z serii StorSimple 8000. Jeśli używasz urządzenia z serii StorSimple 5000/7000, przejdź do [pomocy StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple używa [warstwy magazynu](#automatic-storage-tiering) do zarządzania przechowywanymi danymi na różnych nośnikach magazynu. Bieżący zestaw roboczy jest przechowywany lokalnie na dyskach PÓŁPRZEWODNIKOWYCH (SSD), dane, które są rzadziej używane, są przechowywane na dyskach twardych (HDD), a dane archiwalne są wypychane do chmury. Ponadto StorSimple używa deduplikacji i kompresji, aby zmniejszyć ilość magazynu, który zużywa dane. Aby uzyskać więcej informacji, przejdź do [deduplikacji i kompresji](#deduplication-and-compression). Definicje innych kluczowych terminów i pojęć używanych w dokumentacji serii StorSimple 8000, przejdź do [storsimple terminologii](#storsimple-terminology) na końcu tego artykułu.

Oprócz zarządzania magazynem funkcje ochrony danych StorSimple umożliwiają tworzenie kopii zapasowych na żądanie i zaplanowane, a następnie przechowywanie ich lokalnie lub w chmurze. Kopie zapasowe są pobierane w postaci migawek przyrostowych, co oznacza, że można je szybko utworzyć i przywrócić. Migawki w chmurze mogą mieć kluczowe znaczenie w scenariuszach odzyskiwania po awarii, ponieważ zastępują pomocnicze systemy magazynowania (takie jak kopia zapasowa na taśmie) i umożliwiają przywracanie danych do centrum danych lub w razie potrzeby do alternatywnych lokacji.

![ikona wideo](./media/storsimple-overview/video_icon.png) Obejrzyj klip wideo, aby uzyskać krótkie wprowadzenie do usługi Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Dlaczego warto skorzystać z StorSimple?
W poniższej tabeli opisano niektóre z kluczowych korzyści, które zapewnia usługa Microsoft Azure StorSimple.

| Funkcja | Korzyść |
| --- | --- |
| Przejrzysta integracja |Używa protokołu iSCSI do niewidocznie łączenia obiektów przechowywania danych. Dzięki temu dane przechowywane w chmurze, w centrum danych lub na serwerach zdalnych wydają się być przechowywane w jednej lokalizacji. |
| Niższe koszty magazynowania |Przydziela wystarczającą ilość magazynu lokalnego lub w chmurze, aby sprostać bieżącym wymaganiom i rozszerza magazyn w chmurze tylko wtedy, gdy jest to konieczne. Ponadto zmniejsza wymagania dotyczące magazynu i wydatków, eliminując nadmiarowe wersje tych samych danych (deduplikacji) i za pomocą kompresji. |
| Uproszczone zarządzanie pamięcią masową |Udostępnia narzędzia do administrowania systemem do konfigurowania danych przechowywanych lokalnie, na serwerze zdalnym i w chmurze oraz zarządzania nimi. Ponadto można zarządzać funkcjami tworzenia kopii zapasowych i przywracania z przystawki programu Microsoft Management Console (MMC).|
| Ulepszone odzyskiwanie po awarii i zgodność z przepisami |Nie wymaga wydłużonego czasu odzyskiwania. Zamiast tego przywraca dane w razie potrzeby. Oznacza to, że normalne operacje mogą być kontynuowane przy minimalnych zakłóceniach. Ponadto można skonfigurować zasady, aby określić harmonogramy tworzenia kopii zapasowych i przechowywania danych. |
| Mobilność danych |Dostęp do danych przekazanych do usług w chmurze platformy Microsoft Azure można uzyskać z innych witryn w celu odzyskania i migracji. Ponadto można użyć StorSimple do konfigurowania urządzeń storsimple w chmurze na maszynach wirtualnych (VM) uruchomionych na platformie Microsoft Azure. Maszyny wirtualne mogą następnie używać urządzeń wirtualnych do uzyskiwania dostępu do przechowywanych danych do celów testowych lub odzyskiwania. |
| Ciągłość działalności biznesowej |Umożliwia użytkownikom serii StorSimple 5000-7000 migrację danych do urządzenia z serii StorSimple 8000. |
| Dostępność w portalu azure dla instytucji rządowych |StorSimple jest dostępny w portalu Azure Government Portal. Aby uzyskać więcej informacji, zobacz [Wdrażanie lokalnego urządzenia StorSimple w portalu rządowym](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Ochrona i dostępność danych |Seria StorSimple 8000 obsługuje strefową nadmiarową pamięć masową (ZRS), oprócz magazynu lokalnie nadmiarowego (LRS) i magazynu geograficznie nadmiarowego (GRS). Zapoznaj się z [tym artykułem na temat opcji nadmiarowości usługi Azure Storage,](https://azure.microsoft.com/documentation/articles/storage-redundancy/) aby uzyskać szczegółowe informacje. |
| Obsługa krytycznych aplikacji |StorSimple umożliwia identyfikowanie odpowiednich woluminów jako przypiętych lokalnie, co z kolei zapewnia, że dane wymagane przez krytyczne aplikacje nie są warstwowe w chmurze. Woluminy przypięte lokalnie nie podlegają opóźnieniom w chmurze ani problemom z łącznością. Aby uzyskać więcej informacji na temat woluminów przypiętych lokalnie, zobacz [Zarządzanie woluminami za pomocą usługi StorSimple Device Manager](storsimple-8000-manage-volumes-u2.md). |
| Niskie opóźnienia i wysoka wydajność |Można tworzyć urządzenia w chmurze, które korzystają z wysokiej wydajności, niskie opóźnienia funkcji usługi Azure magazynu w wersji premium. Aby uzyskać więcej informacji na temat urządzeń chmury premium StorSimple, zobacz [Wdrażanie urządzenia w chmurze StorSimple i zarządzanie nim na platformie Azure.](storsimple-8000-cloud-appliance-u2.md) |


## <a name="storsimple-components"></a>Składniki StorSimple
Rozwiązanie Microsoft Azure StorSimple zawiera następujące składniki:

* **Urządzenie Microsoft Azure StorSimple** — lokalna hybrydowa macierz magazynu zawierająca dyski SSD i dyski twarde, a także nadmiarowe kontrolery i funkcje automatycznego pracy awaryjnej. Kontrolery zarządzają warstwami magazynu, umieszczając obecnie używane (lub gorące) dane w magazynie lokalnym (na urządzeniu lub serwerach lokalnych), jednocześnie przenosząc rzadziej używane dane do chmury.
* **StorSimple Cloud Appliance** — znany również jako StorSimple Virtual Appliance, jest to wersja oprogramowania urządzenia StorSimple, który replikuje architekturę i większość możliwości fizycznego hybrydowego urządzenia pamięci masowej. Urządzenie storsimple cloud działa w jednym węźle na maszynie wirtualnej platformy Azure. Urządzenia wirtualne klasy premium, które korzystają z magazynu w ramach usługi Azure w wersji premium, są dostępne w aktualizacji 2 i nowszych wersjach.
* **Usługa StorSimple Device Manager** — rozszerzenie portalu Azure, które umożliwia zarządzanie urządzeniem StorSimple lub urządzeniem StorSimple Cloud Appliance z jednego interfejsu sieci web. Za pomocą usługi StorSimple Device Manager można tworzyć usługi, przeglądać i zarządzać urządzeniami, wyświetlać alerty, zarządzać woluminami oraz wyświetlać i zarządzać zasadami tworzenia kopii zapasowych oraz katalogiem kopii zapasowych.
* **Windows PowerShell for StorSimple** – interfejs wiersza polecenia, którego można używać do zarządzania urządzeniem StorSimple. Program Windows PowerShell for StorSimple ma funkcje, które umożliwiają zarejestrowanie urządzenia StorSimple, skonfigurowanie interfejsu sieciowego na urządzeniu, zainstalowanie niektórych typów aktualizacji, rozwiązywanie problemów z urządzeniem przez dostęp do sesji pomocy technicznej i zmianę urządzenia Państwa. Dostęp do programu Windows PowerShell for StorSimple można uzyskać, łącząc się z konsolą szeregową lub korzystając z komunikacji zdalnej programu Windows PowerShell.
* **Polecenia cmdlet usługi Azure PowerShell StorSimple** — kolekcja poleceń cmdlet programu Windows PowerShell, które umożliwiają automatyzację zadań na poziomie usługi i migracji z wiersza polecenia. Aby uzyskać więcej informacji na temat poleceń cmdlet programu Azure PowerShell dla StorSimple, przejdź do [odwołania do polecenia cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** — przystawka programu MMC, która używa grup woluminów i usługi kopiowania woluminów w tle systemu Windows do generowania kopii zapasowych spójnych z aplikacjami. Ponadto można użyć StorSimple Snapshot Manager do tworzenia harmonogramów tworzenia kopii zapasowych i klonowania lub przywracania woluminów.
* **StorSimple Adapter for SharePoint** — narzędzie, które w sposób przejrzysty rozszerza magazyn i ochronę danych usługi Microsoft Azure StorSimple do farm programu SharePoint Server, jednocześnie dzięki czemu magazyn StorSimple jest widoczny i zarządzania z portalu administracji centralnej programu SharePoint.

Poniższy diagram zawiera widok wysokiego poziomu architektury i składników usługi Microsoft Azure StorSimple.

![Architektura StorSimple](./media/storsimple-overview/overview-big-picture.png)

W poniższych sekcjach opisano każdy z tych składników bardziej szczegółowo i wyjaśnić, jak rozwiązanie organizuje dane, przydziela magazyn i ułatwia zarządzanie magazynem i ochrony danych. Ostatnia sekcja zawiera definicje dla niektórych ważnych terminów i pojęć związanych z StorSimple składników i ich zarządzania.

## <a name="storsimple-device"></a>Urządzenie StorSimple
Urządzenie Microsoft Azure StorSimple to lokalna hybrydowa macierz magazynu, która zapewnia podstawowy magazyn i dostęp do danych przechowywanych na nim przez usługę ISCSI. Zarządza komunikacją z magazynem w chmurze i pomaga zapewnić bezpieczeństwo i poufność wszystkich danych przechowywanych w rozwiązaniu Microsoft Azure StorSimple.

Urządzenie StorSimple zawiera dyski SSD i dyski twarde, a także obsługę klastrowania i automatycznego trybu failover. Zawiera współużytkowany procesor, współdzieloną pamięć masową i dwa kontrolery dublowane. Każdy kontroler zapewnia następujące właściwości:

* Połączenie z komputerem-hostem
* Do sześciu portów sieciowych do podłączenia do sieci lokalnej (LAN)
* Monitorowanie sprzętu
* Nieulotna pamięć dostępu losowego (NVRAM), która przechowuje informacje nawet w przypadku przerwania zasilania
* Aktualizacja z uwzględnieniem klastra w celu zarządzania aktualizacjami oprogramowania na serwerach w klastrze trybu failover, tak aby aktualizacje miały minimalny lub żaden wpływ na dostępność usługi
* Usługa klastrowania, która działa jak klaster zaplecza, zapewniając wysoką dostępność i minimalizując wszelkie niekorzystne skutki, które mogą wystąpić, jeśli dysk twardy lub dysk SSD ulegnie awarii lub zostanie przełączona w tryb offline

Tylko jeden kontroler jest aktywny w dowolnym momencie. Jeśli aktywny kontroler ulegnie awarii, drugi kontroler staje się aktywny automatycznie.

Aby uzyskać więcej informacji, przejdź do [pliku StorSimple składniki sprzętowe i stan](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>Urządzenie w chmurze StorSimple
StorSimple służy do tworzenia urządzenia w chmurze, który replikuje architekturę i możliwości fizycznego hybrydowego urządzenia magazynującego. Urządzenie storsimple cloud (znany również jako Urządzenie wirtualne StorSimple) działa na jednym węźle na maszynie wirtualnej platformy Azure. (Urządzenie w chmurze można utworzyć tylko na maszynie wirtualnej platformy Azure. Nie można utworzyć go na urządzeniu StorSimple lub na serwerze lokalnym.)

Urządzenie w chmurze ma następujące funkcje:

* Zachowuje się jak urządzenie fizyczne i może oferować interfejs iSCSI do maszyn wirtualnych w chmurze.
* Można utworzyć nieograniczoną liczbę urządzeń w chmurze i włączyć je i wyłączyć w razie potrzeby.
* Może pomóc symulować środowiska lokalne w scenariuszach odzyskiwania po awarii, rozwoju i testowania i może pomóc w pobieraniu na poziomie elementu z kopii zapasowych.

Urządzenie StorSimple Cloud Appliance jest dostępne w dwóch modelach: urządzeniu 8010 (dawniej znanym jako model 1100) i urządzeniu 8020. Maksymalna pojemność urządzenia 8010 wynosi 30 TB. Urządzenie 8020, które korzysta z usługi Azure premium storage, ma maksymalną pojemność 64 TB. (W warstwach lokalnych usługa Azure w warstwie premium przechowuje dane na dyskach SSD, podczas gdy standardowy magazyn przechowuje dane na dyskach twardych). Należy pamiętać, że musisz mieć konto magazynu w wersji Premium platformy Azure, aby korzystać z magazynu w wersji premium.

Aby uzyskać więcej informacji na temat urządzenia StorSimple Cloud Appliance, przejdź do [wdrażania urządzenia w chmurze StorSimple na platformie Azure i zarządzania nim.](storsimple-8000-cloud-appliance-u2.md)

## <a name="storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Usługa Microsoft Azure StorSimple udostępnia internetowy interfejs użytkownika (usługę StorSimple Device Manager), który umożliwia centralne zarządzanie centrum danych i magazynem w chmurze. Za pomocą usługi StorSimple Device Manager można wykonywać następujące zadania:

* Skonfiguruj ustawienia systemowe dla urządzeń StorSimple.
* Konfigurowanie ustawień zabezpieczeń urządzeń StorSimple i zarządzanie nimi.
* Konfigurowanie poświadczeń i właściwości w chmurze.
* Konfigurowanie woluminów na serwerze i zarządzanie nimi.
* Konfigurowanie grup woluminów.
* Twoij kopię zapasową i przywracaj dane.
* Monitoruj wydajność.
* Przejrzyj ustawienia systemu i zidentyfikuj możliwe problemy.

Za pomocą usługi StorSimple Device Manager można wykonywać wszystkie zadania administracyjne, z wyjątkiem tych, które wymagają czasu przestoju systemu, takich jak początkowa konfiguracja i instalacja aktualizacji.

Aby uzyskać więcej informacji, przejdź do [witryny Zarządzanie urządzeniem StorSimple za pomocą usługi StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell for StorSimple
Program Windows PowerShell for StorSimple udostępnia interfejs wiersza polecenia, którego można używać do tworzenia usługi Microsoft Azure StorSimple i zarządzania nią oraz konfigurowania i monitorowania urządzeń StorSimple. Jest to oparty na programie Windows PowerShell interfejs wiersza polecenia, który zawiera dedykowane polecenia cmdlet do zarządzania urządzeniem StorSimple. Program Windows PowerShell for StorSimple zawiera funkcje, które umożliwiają:

* Zarejestruj urządzenie.
* Skonfiguruj interfejs sieciowy na urządzeniu.
* Zainstaluj niektóre typy aktualizacji.
* Rozwiązywanie problemów z urządzeniem, uzyskując dostęp do sesji pomocy technicznej.
* Zmień stan urządzenia.

Dostęp do programu Windows PowerShell for StorSimple można uzyskać z konsoli szeregowej (na komputerze-hoście podłączonym bezpośrednio do urządzenia) lub zdalnie przy użyciu komunikacji zdalnej programu Windows PowerShell. Należy zauważyć, że niektóre zadania programu Windows PowerShell dla storsimple, takie jak początkowa rejestracja urządzenia, można wykonać tylko na konsoli szeregowej.

Aby uzyskać więcej informacji, przejdź do [witryny Zarządzanie urządzeniem za pomocą programu Windows PowerShell for StorSimple](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Polecenia cmdlet usługi Azure PowerShell StorSimple
Polecenia cmdlet usługi Azure PowerShell StorSimple to kolekcja poleceń cmdlet programu Windows PowerShell, które umożliwiają automatyzację zadań na poziomie usługi i migracji z wiersza polecenia. Aby uzyskać więcej informacji na temat poleceń cmdlet programu Azure PowerShell dla StorSimple, przejdź do [odwołania do polecenia cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager to przystawka programu Microsoft Management Console (MMC), której można używać do tworzenia spójnych kopii zapasowych danych lokalnych i danych w chmurze w czasie. Przystawka jest uruchamiana na hoście opartym na systemie Windows Server. Menedżera migawek StorSimple można użyć do:

* Konfigurowanie woluminów, ich utworzenie kopii zapasowej i usuwanie.
* Skonfiguruj grupy woluminów, aby upewnić się, że kopie zapasowe danych są spójne z aplikacjami.
* Zarządzanie zasadami tworzenia kopii zapasowych, tak aby dane były archiwizowane zgodnie z ustalonym harmonogramem i przechowywane w wyznaczonej lokalizacji (lokalnie lub w chmurze).
* Przywracanie woluminów i pojedynczych plików.

Kopie zapasowe są przechwytywane jako migawki, które rejestrują tylko zmiany od czasu ostatniej migawki i wymagają znacznie mniej miejsca do magazynowania niż pełne kopie zapasowe. W razie potrzeby można utworzyć harmonogramy tworzenia kopii zapasowych lub wykonać natychmiastowe kopie zapasowe. Ponadto można użyć StorSimple Snapshot Manager do ustanowienia zasad przechowywania, które kontrolują, ile migawek zostaną zapisane. Jeśli następnie trzeba przywrócić dane z kopii zapasowej, StorSimple Snapshot Manager umożliwia wybranie z katalogu migawek lokalnych lub w chmurze. 

Jeśli wystąpi awaria lub jeśli trzeba przywrócić dane z innego powodu, StorSimple Snapshot Manager przywraca go przyrostowo, jak jest to potrzebne. Przywracanie danych nie wymaga zamknięcia całego systemu podczas przywracania pliku, zastępowania sprzętu ani przenoszenia operacji do innej lokacji.

Aby uzyskać więcej informacji, przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adapter usługi StorSimple dla programu SharePoint
Usługa Microsoft Azure StorSimple zawiera kartę StorSimple dla programu SharePoint, opcjonalny składnik, który w sposób przejrzysty rozszerza funkcje przechowywania i ochrony danych StorSimple do farm serwerów programu SharePoint. Karta współpracuje z dostawcą zdalnego magazynu obiektów blob (SPZ) i funkcją SPZ programu SQL Server, umożliwiając przenoszenie obiektów BLOB na serwer, którego kopię zapasową jest system Microsoft Azure StorSimple. Usługa Microsoft Azure StorSimple następnie przechowuje dane obiektu BLOB lokalnie lub w chmurze na podstawie użycia.

Kartą StorSimple dla programu SharePoint zarządza portal administracji centralnej programu SharePoint. W związku z tym zarządzanie programem SharePoint pozostaje scentralizowane, a cały magazyn wydaje się znajdować w farmie programu SharePoint.

Aby uzyskać więcej informacji, przejdź do [pliku StorSimple Adapter for SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologie zarządzania pamięcią masową
Oprócz dedykowanego urządzenia StorSimple, urządzenia wirtualnego i innych składników usługa Microsoft Azure StorSimple korzysta z następujących technologii oprogramowania w celu zapewnienia szybkiego dostępu do danych i zmniejszenia zużycia pamięci masowej:

* [Automatyczne tworzenie warstw pamięci masowej](#automatic-storage-tiering) 
* [Alokowanie elastyczne](#thin-provisioning) 
* [Deduplikacja i kompresja](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatyczne tworzenie warstw pamięci masowej
Usługa Microsoft Azure StorSimple automatycznie rozmieszcza dane w warstwach logicznych na podstawie bieżącego użycia, wieku i relacji z innymi danymi. Dane, które są najbardziej aktywne, są przechowywane lokalnie, podczas gdy mniej aktywne i nieaktywne dane są automatycznie migrowane do chmury. Na poniższym diagramie przedstawiono to podejście magazynu.

![Warstwy magazynu StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Aby umożliwić szybki dostęp, StorSimple przechowuje bardzo aktywne dane (gorące dane) na ssd w urządzeniu StorSimple. Przechowuje dane, które są używane od czasu do czasu (ciepłe dane) na dyskach twardych w urządzeniu lub na serwerach w centrum danych. Przenosi nieaktywne dane, dane kopii zapasowych i dane przechowywane do celów archiwizacji lub zgodności do chmury. 

> [!NOTE]
> W aktualizacji 2 lub nowszej można określić wolumin jako przypięty lokalnie, w którym to przypadku dane pozostają na urządzeniu lokalnym i nie są warstwowe w chmurze. 


StorSimple dostosowuje i zmienia przydziały danych i magazynu w miarę zmiany wzorców użycia. Na przykład niektóre informacje mogą stać się mniej aktywne w czasie. W miarę jak staje się stopniowo mniej aktywny, jest migrowany z dysków SSD na dyski twarde, a następnie do chmury. Jeśli te same dane ponownie staną się aktywne, zostaną zmigrowane z powrotem do urządzenia magazynującego.

Proces warstwowania magazynu występuje w następujący sposób:

1. Administrator systemu konfiguruje konto magazynu w chmurze platformy Microsoft Azure.
2. Administrator używa konsoli szeregowej i usługi StorSimple Device Manager (uruchomionej w witrynie Azure portal) do konfigurowania urządzenia i serwera plików, tworzenia woluminów i zasad ochrony danych. Maszyny lokalne (takie jak serwery plików) korzystają z internetowego interfejsu systemu komputerowego iSCSI w celu uzyskania dostępu do urządzenia StorSimple.
3. Początkowo StorSimple przechowuje dane na warstwie szybkiej warstwy SSD urządzenia.
4. Gdy warstwa SSD zbliża się do pojemności, StorSimple deduplikuje i kompresuje najstarsze bloki danych i przenosi je do warstwy HDD.
5. W miarę zbliżania się do pojemności warstwy dysków twardych storsimple szyfruje najstarsze bloki danych i wysyła je bezpiecznie do konta magazynu platformy Microsoft Azure za pośrednictwem protokołu HTTPS.
6. Platforma Microsoft Azure tworzy wiele replik danych w centrum danych i w zdalnym centrum danych, zapewniając, że dane mogą zostać odzyskane w przypadku wystąpienia awarii.
7. Gdy serwer plików żąda danych przechowywanych w chmurze, StorSimple zwraca je bezproblemowo i przechowuje kopię na warstwie SSD urządzenia StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Jak StorSimple zarządza danymi w chmurze

StorSimple deduplikuje dane klienta we wszystkich migawek i danych podstawowych (dane zapisywane przez hosty). Chociaż deduplikacja jest świetna dla wydajności pamięci masowej, komplikuje kwestię "co jest w chmurze". Warstwowe dane podstawowe i dane migawki pokrywają się ze sobą. Pojedynczy fragment danych w chmurze może służyć jako warstwowe dane podstawowe, a także odwoływać się do kilku migawek. Każda migawka w chmurze zapewnia, że kopia wszystkich danych typu punkt w czasie jest zablokowana w chmurze, dopóki ta migawka nie zostanie usunięta.

Dane są usuwane z chmury tylko wtedy, gdy nie ma żadnych odwołań do tych danych. Na przykład jeśli zrobimy migawkę chmury wszystkich danych, które znajduje się w urządzeniu StorSimple, a następnie usunąć niektóre dane podstawowe, zobaczymy spadek _danych podstawowych_ natychmiast. _Dane w chmurze,_ która zawiera dane warstwowe i kopie zapasowe, pozostają takie same. Jest tak, ponieważ istnieje migawka nadal odwołując się do danych w chmurze. Po usunięciu migawki chmury (i innych migawek, które odwoływały się do tych samych danych), zużycie chmury spada. Zanim usuniemy dane w chmurze, sprawdzamy, czy żadne migawki nadal nie odwołują się do tych danych. Ten proces jest nazywany _wyrzucania elementów bezużytecznych_ i jest usługą w tle uruchomiony na urządzeniu. Usuwanie danych w chmurze nie jest natychmiastowe, ponieważ usługa wyrzucania elementów bezużytecznych sprawdza inne odwołania do tych danych przed usunięciem. Szybkość wyrzucania elementów bezużytecznych zależy od całkowitej liczby migawek i całkowitych danych. Zazwyczaj dane w chmurze są czyszczone w mniej niż tydzień.


### <a name="thin-provisioning"></a>Alokowanie elastyczne
Elastyczne inicjowanie obsługi administracyjnej to technologia wirtualizacji, w której dostępny magazyn wydaje się przekraczać zasoby fizyczne. Zamiast rezerwować wystarczającą ilość miejsca z wyprzedzeniem, StorSimple używa elastycznej inicjowania obsługi administracyjnej, aby przydzielić wystarczającą ilość miejsca, aby spełnić bieżące wymagania. Elastyczny charakter magazynu w chmurze ułatwia to podejście, ponieważ StorSimple może zwiększyć lub zmniejszyć magazyn w chmurze, aby sprostać zmieniającym się wymaganiom.

> [!NOTE]
> Woluminy przypięte lokalnie nie są zaaprowizowane w sposób cienki. Magazyn przydzielony do woluminu tylko lokalnego jest aprowizowana w całości podczas tworzenia woluminu.


### <a name="deduplication-and-compression"></a>Deduplikacja i kompresja
Usługa Microsoft Azure StorSimple używa deduplikacji i kompresji danych w celu dalszego zmniejszenia wymagań dotyczących magazynu.

Deduplikacja zmniejsza ogólną ilość przechowywanych danych, eliminując nadmiarowość w przechowywanym zestawie danych. W miarę zmiany informacji StorSimple ignoruje niezmienione dane i przechwytuje tylko zmiany. Ponadto StorSimple zmniejsza ilość przechowywanych danych, identyfikując i usuwając niepotrzebne informacje. 

> [!NOTE]
> Dane na woluminach przypiętych lokalnie nie są deduplikowane ani kompresowane. Jednak kopie zapasowe woluminów przypiętych lokalnie są deduplikowane i kompresowane.


## <a name="storsimple-workload-summary"></a>StorSimple podsumowanie obciążenia
Podsumowanie obsługiwanych obciążeń StorSimple jest tabelaryczne poniżej.

| Scenariusz | Obciążenie | Obsługiwane | Ograniczenia | Wersja |
| --- | --- | --- | --- | --- |
| Współpraca |Udostępnianie plików |Tak | |Wszystkie wersje |
| Współpraca |Rozproszone udostępnianie plików |Tak | |Wszystkie wersje |
| Współpraca |Program SharePoint |Tak* |Obsługiwane tylko z woluminami przypiętymi lokalnie |Aktualizacja 2 i nowsze |
| Archiwalnych |Prosta archiwizacja plików |Tak | |Wszystkie wersje |
| Wirtualizacja |Maszyny wirtualne |Tak* |Obsługiwane tylko z woluminami przypiętymi lokalnie |Aktualizacja 2 i nowsze |
| baza danych |SQL |Tak* |Obsługiwane tylko z woluminami przypiętymi lokalnie |Aktualizacja 2 i nowsze |
| Monitorowanie wideo |Monitorowanie wideo |Tak* |Obsługiwane, gdy urządzenie StorSimple jest dedykowane tylko do tego obciążenia |Aktualizacja 2 i nowsze |
| Tworzenie kopii zapasowych |Podstawowa kopia zapasowa docelowa |Tak* |Obsługiwane, gdy urządzenie StorSimple jest dedykowane tylko do tego obciążenia |Aktualizacja 3 i nowsze |
| Tworzenie kopii zapasowych |Pomocnicza kopia zapasowa docelowa |Tak* |Obsługiwane, gdy urządzenie StorSimple jest dedykowane tylko do tego obciążenia |Aktualizacja 3 i nowsze |

*Tak&#42; — należy stosować wytyczne dotyczące rozwiązań i ograniczenia.*

Następujące obciążenia nie są obsługiwane przez urządzenia z serii StorSimple 8000. Jeśli wdrożony na StorSimple, te obciążenia spowoduje nieobsługiwał konfiguracji.

* Obrazowanie medyczne
* Exchange
* Vdi
* Oracle
* SAP
* Dane Big Data
* Dystrybucja zawartości
* Rozruch z SCSI

Poniżej znajduje się lista StorSimple obsługiwane składniki infrastruktury.

| Scenariusz | Obciążenie | Obsługiwane | Ograniczenia | Wersja |
| --- | --- | --- | --- | --- |
| Ogólne |ExpressRoute |Tak | |Wszystkie wersje |
| Ogólne |DataCore FC |Tak* |Obsługiwane przez datacore SANsymphony |Wszystkie wersje |
| Ogólne |DFSR |Tak* |Obsługiwane tylko z woluminami przypiętymi lokalnie |Wszystkie wersje |
| Ogólne |Indeksowanie |Tak* |W przypadku woluminów warstwowych obsługiwane jest tylko indeksowanie metadanych (brak danych).<br>W przypadku woluminów przypiętych lokalnie obsługiwane jest pełne indeksowanie. |Wszystkie wersje |
| Ogólne |Antywirusowe |Tak* |W przypadku woluminów warstwowych obsługiwane jest tylko skanowanie na otwartym i zamknięciu.<br> W przypadku woluminów przypiętych lokalnie obsługiwane jest pełne skanowanie. |Wszystkie wersje |

*Tak&#42; — należy stosować wytyczne dotyczące rozwiązań i ograniczenia.*

Poniżej znajduje się lista innych programów, które są używane z StorSimple do tworzenia rozwiązań.

| Typ obciążenia | Oprogramowanie używane z StorSimple | Obsługiwane wersje|Łącze do przewodnika po rozwiązaniach| 
| --- | --- | --- | --- |
| Miejsce docelowe kopii zapasowej |Veeam |Veeam v 9 i nowsze |[StorSimple jako miejsce docelowe tworzenia kopii zapasowych z Veaam](storsimple-configure-backup-target-veeam.md)|
| Miejsce docelowe kopii zapasowej |Veritas Backup Exec |Kopia zapasowa Exec 16 i nowsze |[StorSimple jako miejsce docelowe kopii zapasowej z backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Miejsce docelowe kopii zapasowej |Veritas NetBackup |NetBackup 7.7.x i nowsze  |[StorSimple jako miejsce docelowe kopii zapasowej z NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Globalne udostępnianie plików <br></br> Współpraca |Talon  |[StorSimple z talonem](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple terminologii
Przed wdrożeniem rozwiązania Microsoft Azure StorSimple zaleca się przejrzenie następujących terminów i definicji.

### <a name="key-terms-and-definitions"></a>Kluczowe terminy i definicje
| Termin (akronim lub skrót) | Opis |
| --- | --- |
| rekord kontroli dostępu (ACR) |Rekord skojarzony z woluminem na urządzeniu Microsoft Azure StorSimple, który określa, które hosty mogą się z nim łączyć. Określenie jest oparte na nazwie kwalifikowanej iSCSI (IQN) hostów (zawartych w ACR), które łączą się z urządzeniem StorSimple. |
| AES 256 |256-bitowy algorytm Advanced Encryption Standard (AES) do szyfrowania danych w miarę przenoszenia do i z chmury. |
| wielkość jednostki alokacji (AUS) |Najmniejsza ilość miejsca na dysku, które można przydzielić do przechowywania pliku w systemach plików systemu Windows. Jeśli rozmiar pliku nie jest nawet wielokrotnością rozmiaru klastra, do przechowywania pliku (do następnej wielokrotności rozmiaru klastra) należy użyć dodatkowego miejsca, co spowoduje utratę miejsca i fragmentację dysku twardego. <br>Zalecane woluminy AUS dla usługi Azure StorSimple ma 64 KB, ponieważ działa dobrze z algorytmami deduplikacji. |
| automatyczne tworzenie warstw pamięci masowej |Automatyczne przenoszenie mniej aktywnych danych z dysków SSD na dyski twarde, a następnie do warstwy w chmurze, a następnie umożliwia zarządzanie całą pamięcią masową z centralnego interfejsu użytkownika. |
| katalog kopii zapasowych |Kolekcja kopii zapasowych, zwykle związane przez typ aplikacji, który był używany. Ta kolekcja jest wyświetlana w bloku katalogu kopii zapasowych interfejsu użytkownika usługi StorSimple Device Manager. |
| plik katalogu kopii zapasowych |Plik zawierający listę dostępnych migawek aktualnie przechowywanych w zapasowej bazie danych StorSimple Snapshot Manager. |
| zasady tworzenia kopii zapasowych |Wybór woluminów, typ kopii zapasowej i harmonogram, który umożliwia tworzenie kopii zapasowych zgodnie ze wstępnie zdefiniowanym harmonogramem. |
| duże obiekty binarne (blob) |Zbiór danych binarnych przechowywanych jako pojedyncza jednostka w systemie zarządzania bazą danych. Obiekty BLOB są zazwyczaj obrazy, audio lub inne obiekty multimedialne, chociaż czasami binarny kod wykonywalny jest przechowywany jako blob. |
| Protokół uwierzytelniania typu Challenge Handshake (CHAP) |Protokół używany do uwierzytelniania elementu równorzędnego połączenia na podstawie elementu równorzędnego dzielącego hasło lub klucz tajny. Protokół CHAP może być jednokierunkowy lub wzajemny. W ujmująco protokół CHAP obiekt docelowy uwierzytelnia inicjatora. Wzajemne protokołu CHAP wymaga, aby obiekt docelowy uwierzytelnił inicjatora i że inicjator uwierzytelnia obiekt docelowy. |
| clone |Zduplikowana kopia woluminu. |
| Chmura jako warstwa (CaaT) |Magazyn w chmurze zintegrowany jako warstwa w architekturze magazynu, dzięki czemu cały magazyn wydaje się być częścią jednej sieci magazynu w przedsiębiorstwie. |
| dostawca usług w chmurze (CSP) |Dostawca usług przetwarzania w chmurze. |
| migawka w chmurze |Kopia danych woluminu w czasie, która jest przechowywana w chmurze. Migawka w chmurze jest odpowiednikiem migawki replikowanej w innym systemie magazynu poza siedzibą firmy. Migawki chmury są szczególnie przydatne w scenariuszach odzyskiwania po awarii. |
| klucz szyfrowania magazynu w chmurze |Hasło lub klucz używany przez urządzenie StorSimple w celu uzyskania dostępu do zaszyfrowanych danych wysyłanych przez urządzenie do chmury. |
| aktualizacja z uwzględnieniem klastra |Zarządzanie aktualizacjami oprogramowania na serwerach w klastrze trybu failover, tak aby aktualizacje miały minimalny lub żaden wpływ na dostępność usługi. |
| ścieżka danych |Kolekcja jednostek funkcjonalnych, które wykonują wzajemnie połączone operacje przetwarzania danych. |
| zdezaktywować |Stała akcja, która przerywa połączenie między urządzeniem StorSimple a skojarzoną usługą w chmurze. Migawki w chmurze urządzenia pozostają po tym procesie i mogą być klonowane lub używane do odzyskiwania po awarii. |
| dublowanie dysków |Replikacja woluminów dysków logicznych na oddzielnych dyskach twardych w czasie rzeczywistym w celu zapewnienia ciągłej dostępności. |
| dynamiczne dublowanie dysków |Replikacja woluminów dysków logicznych na dyskach dynamicznych. |
| dyski dynamiczne |Format woluminu dysku, który używa Menedżera dysków logicznych (LDM) do przechowywania danych i zarządzania nimi na wielu dyskach fizycznych. Dyski dynamiczne można powiększyć, aby zapewnić więcej wolnego miejsca. |
| Rozszerzona pęczka dysków (EBOD) obudowa |Dodatkowa obudowa urządzenia Microsoft Azure StorSimple zawierająca dodatkowe dyski twarde dla dodatkowego miejsca do magazynowania. |
| tworzenie tłuszczu |Konwencjonalne aprowizacji magazynu, w którym przestrzeń magazynowa jest przydzielana na podstawie przewidywanych potrzeb (i jest zwykle poza bieżącą potrzebą). Zobacz też *cienkie inicjowanie obsługi administracyjnej*. |
| dysk twardy (HDD) |Dysk, który używa obrotowych talerzy do przechowywania danych. |
| hybrydowa pamięć masowa w chmurze |Architektura magazynu, która używa zasobów lokalnych i poza siedzibą firmy, w tym magazynu w chmurze. |
| Interfejs internetowego małego systemu komputerowego (iSCSI) |Standard sieci pamięci masowej oparty na protokole internetowym (IP) do łączenia urządzeń lub urządzeń do przechowywania danych. |
| Inicjator iSCSI |Składnik oprogramowania, który umożliwia komputerowi hosta z systemem Windows łączenie się z zewnętrzną siecią pamięci masowej opartą na iSCSI. |
| Nazwa kwalifikowana iSCSI (IQN) |Unikatowa nazwa identyfikującya obiekt docelowy lub inicjatora iSCSI. |
| Cel iSCSI |Składnik oprogramowania, który zapewnia scentralizowane podsystemy dysków iSCSI w sieciach magazynowania. |
| archiwizacja na żywo |Podejście do przechowywania, w którym dane archiwalne są dostępne przez cały czas (nie są przechowywane poza siedzibą firmy na taśmie, na przykład). Usługa Microsoft Azure StorSimple korzysta z archiwizacji na żywo. |
| wolumin przypięty lokalnie |wolumin, który znajduje się na urządzeniu i nigdy nie jest warstwowy do chmury. |
| migawka lokalna |Kopia danych woluminu w czasie, która jest przechowywana na urządzeniu Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Zaawansowane rozwiązanie składające się z urządzenia i oprogramowania do przechowywania centrów danych, które umożliwia organizacjom IT korzystanie z magazynu w chmurze tak, jakby było to magazynowanie w centrum danych. StorSimple upraszcza ochronę danych i zarządzanie danymi przy jednoczesnym obniżeniu kosztów. Rozwiązanie konsoliduje podstawową pamięć masową, archiwum, tworzenie kopii zapasowych i odzyskiwanie po awarii (DR) dzięki bezproblemowej integracji z chmurą. Łącząc pamięć masową SAN i zarządzanie danymi w chmurze na platformie klasy korporacyjnej, urządzenia StorSimple umożliwiają szybkość, prostotę i niezawodność dla wszystkich potrzeb związanych z pamięcią masową. |
| Moduł zasilania i chłodzenia (PCM) |Komponenty sprzętowe urządzenia StorSimple składające się z zasilaczy i wentylatora chłodzącego, stąd nazwa Moduł zasilania i chłodzenia. Obudowa podstawowa urządzenia ma dwa pcm 764W, podczas gdy obudowa EBOD ma dwa pcm 580W. |
| obudowa podstawowa |Główna obudowa urządzenia StorSimple zawierająca kontrolery platformy aplikacji. |
| cel czasu odzyskiwania (RTO) |Maksymalny czas, który powinien zostać wydany przed procesem biznesowym lub systemem zostanie w pełni przywrócony po awarii. |
| scsi złączem szeregowym (SAS) |Typ dysku twardego (HDD). |
| klucz szyfrowania danych usługi |Klucz udostępniony każdemu nowemu urządzeniu StorSimple, które rejestruje się w usłudze StorSimple Device Manager. Dane konfiguracyjne przesyłane między usługą StorSimple Device Manager a urządzeniem są szyfrowane przy użyciu klucza publicznego, a następnie można je odszyfrować tylko na urządzeniu przy użyciu klucza prywatnego. Klucz szyfrowania danych usługi umożliwia usłudze uzyskanie tego klucza prywatnego do odszyfrowania. |
| klucz rejestracji usługi |Klucz, który pomaga zarejestrować urządzenie StorSimple z usługą StorSimple Device Manager, dzięki czemu jest wyświetlany w witrynie Azure portal dla dalszych akcji zarządzania. |
| Small Computer System Interface (SCSI) |Zestaw standardów fizycznego łączenia komputerów i przekazywania danych między nimi. |
| dysk półprzewodnikowy (SSD) |Dysk, który nie zawiera ruchomych części; na przykład dysk flash. |
| konto magazynu |Zestaw poświadczeń dostępu połączonych z kontem magazynu dla danego dostawcy usług w chmurze. |
| Adapter usługi StorSimple dla programu SharePoint |Składnik Microsoft Azure StorSimple, który w sposób przejrzysty rozszerza przechowywanie storproste i ochronę danych do farm serwerów programu SharePoint. |
| Usługa Menedżer urządzeń StorSimple |Rozszerzenie witryny Azure portal, która umożliwia zarządzanie urządzeniami lokalnymi i wirtualnymi usługi Azure StorSimple. |
| StorSimple Snapshot Manager |Przystawka Microsoft Management Console (MMC) do zarządzania operacjami tworzenia kopii zapasowych i przywracania w programie Microsoft Azure StorSimple. |
| wykonywanie kopii zapasowych |Funkcja, która umożliwia użytkownikowi wykonywanie interaktywnej kopii zapasowej woluminu. Jest to alternatywny sposób wykonywania ręcznej kopii zapasowej woluminu, w przeciwieństwie do wykonywania automatycznej kopii zapasowej za pośrednictwem zdefiniowanej zasady. |
| Alokowanie |Metoda optymalizacji wydajności, z jaką dostępna przestrzeń dyskowa jest wykorzystywana w systemach pamięci masowej. W przypadku elastycznej inicjowania obsługi administracyjnej magazyn jest przydzielany między wieloma użytkownikami na podstawie minimalnego miejsca wymaganego przez każdego użytkownika w danym momencie. Zobacz też *aprowizacji tłuszczu*. |
| warstwowe |Rozmieszczanie danych w grupach logicznych na podstawie bieżącego użycia, wieku i relacji z innymi danymi. StorSimple automatycznie rozmieszcza dane w warstwach. |
| wolumin |Logiczne obszary pamięci masowej prezentowane w postaci napędów. StorSimple woluminy odpowiadają woluminów zainstalowanych przez hosta, w tym tych wykrytych za pomocą iSCSI i urządzenia StorSimple. |
| pojemnik objętościowy |Grupowanie woluminów i ustawień, które mają do nich zastosowanie. Wszystkie woluminy w urządzeniu StorSimple są pogrupowane w kontenery woluminów. Ustawienia kontenera woluminów obejmują konta magazynu, ustawienia szyfrowania danych wysyłanych do chmury za pomocą skojarzonych kluczy szyfrowania oraz przepustowość zużywaną dla operacji związanych z chmurą. |
| grupa woluminów |W Menedżerze migawek StorSimple grupa woluminów jest zbiorem woluminów skonfigurowanych w celu ułatwienia przetwarzania kopii zapasowych. |
| Usługa kopiowania woluminów w tle (VSS) |Usługa systemu operacyjnego Windows Server, która ułatwia spójność aplikacji, komunikując się z aplikacjami obsługującymi usługę VSS w celu koordynowania tworzenia migawek przyrostowych. Usługa VSS zapewnia, że aplikacje są tymczasowo nieaktywne podczas robienia migawek. |
| Windows PowerShell for StorSimple |Interfejs wiersza polecenia opartego na programie Windows PowerShell używany do obsługi urządzenia StorSimple i zarządzania nim. Zachowując niektóre z podstawowych możliwości programu Windows PowerShell, ten interfejs ma dodatkowe dedykowane polecenia cmdlet, które są ukierunkowane na zarządzanie urządzeniem StorSimple. |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zabezpieczeniach StorSimple](storsimple-8000-security.md).

