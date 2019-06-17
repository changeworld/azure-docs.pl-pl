---
title: Omówienie rozwiązania StorSimple 8000 series | Dokumentacja firmy Microsoft
description: W tym artykule opisano warstw usługi StorSimple, urządzenie, urządzenie wirtualne, usługi i zarządzania magazynem i wprowadza na kluczowe terminy używane w urządzeniu StorSimple.
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
ms.openlocfilehash: 63906e65acb8e8aa836e6e59714bddca24ea21eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630215"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Macierz magazynowa serii StorSimple 8000: hybrydowe rozwiązanie magazynu w chmurze
## <a name="overview"></a>Omówienie
Witamy w Microsoft Azure StorSimple, zintegrowane rozwiązanie do magazynowania, który zarządza zadaniami magazynowania między urządzeniami lokalnymi i Magazyn w chmurze Microsoft Azure. Usługa StorSimple jest rozwiązanie sieci SAN obszaru magazynowania wydajne, ekonomiczne i łatwe w zarządzaniu, które eliminuje wiele problemów oraz kosztów związanych z ochroną magazynu i danych przedsiębiorstwa. Jego korzysta z własnościowego urządzenia serii StorSimple 8000, integruje się z usługami w chmurze i udostępnia zestaw narzędzi do zarządzania dla zapewnienia bezproblemowego widoku wszystkich magazynów przedsiębiorstwa łącznie z magazynem w chmurze. (Informacje o wdrożenia StorSimple, które zostały opublikowane w witrynie Microsoft Azure dotyczy tylko urządzeń z serii StorSimple 8000. Jeśli używasz urządzenia serii StorSimple 5000/7000, przejdź do strony [pomocy StorSimple](http://onlinehelp.storsimple.com/).)

Usługa StorSimple używa [obsługą poziomów magazynów](#automatic-storage-tiering) do zarządzania dane przechowywane w różnych nośników magazynowania. Bieżący zestaw roboczy jest przechowywane lokalnie na dyskach półprzewodnikowych (SSD), rzadziej używane dane są przechowywane dyski twarde (HDD) i używanych danych archiwalnych zostanie przypisany do chmury. Ponadto StorSimple korzysta na potrzeby deduplikacji i kompresji w celu zmniejszenia ilości miejsca do magazynowania, który wykorzystuje dane. Aby uzyskać więcej informacji, przejdź do [Deduplikacja i kompresja](#deduplication-and-compression). Aby uzyskać definicje inne kluczowe terminy i pojęcia używane w dokumentacja macierzy magazynowych serii StorSimple 8000, przejdź do [terminologii StorSimple](#storsimple-terminology) na końcu tego artykułu.

Oprócz zarządzania magazynem funkcji ochrony danych StorSimple umożliwiają tworzenie na żądanie i zaplanowane tworzenie kopii zapasowych, a następnie zapisać je lokalnie lub w chmurze. Kopie zapasowe są wykonywane w postaci migawek przyrostowych, co oznacza, że mogą być tworzone i przywracane szybko. Migawki w chmurze może być niezwykle istotne w przypadku scenariuszy odzyskiwania po awarii, ponieważ Zamień systemy pomocniczego magazynu (takie jak kopii zapasowej na taśmie) i umożliwiają przywrócenie danych do centrum danych lub alternatywnej witryny, w razie potrzeby.

![ikona wideo](./media/storsimple-overview/video_icon.png) Obejrzyj film dotyczący krótkie wprowadzenie do usługi Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Dlaczego warto używać usługi StorSimple?
W poniższej tabeli opisano niektóre z kluczowych korzyści, które oferuje usługa Microsoft Azure StorSimple.

| Cecha | Korzyść |
| --- | --- |
| Integracja przezroczyste |Używa protokołu iSCSI połączyć w sposób niewidoczny magazynów danych. To zapewnia, że — dane przechowywane w chmurze, w centrum danych, lub na serwerach zdalnych pojawia się mają być przechowywane w jednym miejscu. |
| Mniejsze koszty |Przydziela wystarczające lokalnego lub magazynu w chmurze, aby spełnić bieżące wymagania oraz rozszerza magazynu w chmurze tylko wtedy, gdy jest to konieczne. Dodatkowo zmniejsza wymagania dotyczące magazynu i kosztów, eliminując nadmiarowe wersje tych samych danych (deduplikacji) i przy użyciu kompresji. |
| Uproszczone zarządzanie magazynem |Zapewnia system narzędzia administracyjne służące do konfigurowania i zarządzania nimi — dane przechowywane lokalnie na serwerze zdalnym i w chmurze. Ponadto możesz Zarządzanie kopiami zapasowymi i przywrócić funkcje z przystawką Microsoft Management Console (MMC).|
| Odzyskiwanie po awarii ulepszone i zgodności |Nie wymaga czasu odzyskiwanie rozszerzone. Zamiast tego należy go przywraca dane, ponieważ jest to konieczne. Oznacza to, że przy minimalnym zakłóceniu kontynuować normalne działanie. Ponadto można skonfigurować zasady, aby określić harmonogramy tworzenia kopii zapasowych i przechowywanie danych. |
| Mobilność danych |Z innych witryn w celach recovery i migracji można uzyskać dostępu do danych przekazanych do usług w chmurze Microsoft Azure. Ponadto można użyć usługi StorSimple do konfigurowania urządzeń StorSimple w chmurze na maszynach wirtualnych (VM) działające na platformie Microsoft Azure. Maszyny wirtualne, następnie można użyć urządzenia wirtualne dostęp do danych przechowywanych w celach testowych lub odzyskiwania. |
| Ciągłość działalności biznesowej |Umożliwia StorSimple serii 5000 – 7000 użytkownikom migrować swoje dane do urządzenia serii StorSimple 8000. |
| Dostępność w portalu Azure dla instytucji rządowych |Usługa StorSimple jest dostępna w portalu Azure dla instytucji rządowych. Aby uzyskać więcej informacji, zobacz [wdrażanie lokalnego urządzenia StorSimple w portalu dla instytucji rządowych](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Ochrona danych i dostępność |Seria StorSimple 8000 obsługuje magazyn Strefowo nadmiarowy (ZRS), oprócz przechowywania magazyn lokalnie nadmiarowy (LRS) i magazynu geograficznie nadmiarowego (GRS). Zapoznaj się [w tym artykule na temat opcji nadmiarowości usługi Azure Storage](https://azure.microsoft.com/documentation/articles/storage-redundancy/) ZRS szczegółowe informacje. |
| Obsługa kluczowych aplikacji |Usługa StorSimple pozwala zidentyfikować odpowiednie woluminy przypięte lokalnie, który z kolei gwarantuje, że dane wymagane przez aplikacje o krytycznym dla nie jest warstwowe w chmurze. Woluminy przypięte lokalnie nie podlegają opóźnienia w chmurze lub problemy z łącznością. Aby uzyskać więcej informacji na temat woluminy przypięte lokalnie, zobacz [usługi Menedżer urządzeń StorSimple umożliwia zarządzanie woluminami](storsimple-8000-manage-volumes-u2.md). |
| Małe opóźnienia i wysoką wydajność |Można utworzyć urządzenia w chmurze, które wykorzystują o wysokiej wydajności i małych opóźnień funkcje usługi Azure premium storage. Aby uzyskać więcej informacji na temat urządzeń StorSimple w chmurze — wersja premium, zobacz [wdrażanie i zarządzanie nimi Storsimple w chmurze na platformie Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Składniki usługi StorSimple
Rozwiązanie StorSimple systemu Azure firmy Microsoft zawiera następujące składniki:

* **Usługa Microsoft Azure StorSimple, urządzenie** — hybrydową macierz magazynową w środowisku lokalnym, który zawiera dysków SSD i HDD, wraz z nadmiarowe kontrolery i funkcje automatycznej pracy awaryjnej. Kontrolery zarządzania magazynem warstw, umieszczania aktualnie używane (lub gorąca) danych w magazynie lokalnym (w urządzeniu lub w środowisku lokalnym serwerze), podczas przenoszenia rzadziej używanych danych do chmury.
* **Urządzenie StorSimple w chmurze** — znany także jako urządzenie wirtualne StorSimple to programowa wersja urządzenia StorSimple, która replikuje architekturę i możliwości większości fizycznych hybrydowe urządzenie magazynujące. Urządzenie StorSimple w chmurze jest uruchamiane w jednym węźle maszyny wirtualnej platformy Azure. Urządzenia wirtualne — wersja Premium, których korzystać z usługi Azure premium storage, są dostępne w wersji Update 2 lub nowszy.
* **Usługa Menedżer urządzeń StorSimple** — rozszerzenie portalu Azure, który umożliwia zarządzanie urządzeniem StorSimple lub urządzenia StorSimple w chmurze za pomocą interfejsu jednej sieci web. Usługa Menedżer urządzeń StorSimple umożliwia tworzenie i zarządzanie usługami, wyświetlanie i zarządzanie urządzeniami, wyświetlać alerty, zarządzanie woluminami i wyświetlić i zarządzanie zasadami tworzenia kopii zapasowych i wykazem kopii zapasowych.
* **Program Windows PowerShell dla usługi StorSimple** — interfejs wiersza polecenia, który służy do zarządzania urządzeniem StorSimple. Windows PowerShell dla magazynu StorSimple zapewnia funkcje, które umożliwiają rejestrowanie urządzenia StorSimple, skonfiguruj interfejs sieciowy na urządzeniu, zainstalować niektórych typów aktualizacji, problemów z urządzeniem, uzyskując dostęp do sesji pomocy technicznej i zmienić urządzenie Stan. Windows PowerShell dostęp dla urządzenia StorSimple przez nawiązanie połączenia konsoli szeregowej lub przy użyciu komunikacji zdalnej programu Windows PowerShell.
* **Polecenia cmdlet programu Azure PowerShell StorSimple** — zbierania poleceń cmdlet programu Windows PowerShell, które pozwalają na Automatyzowanie poziomu usługi oraz zadania migracji w wierszu polecenia. Aby uzyskać więcej informacji o poleceniach cmdlet programu Azure PowerShell dla usługi StorSimple, przejdź do [informacje o poleceniach cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **Przystawki StorSimple Snapshot Manager** — przystawkę programu MMC, korzystającą z grupami woluminów i usługi kopiowania w tle woluminów Windows do generowania spójnych z aplikacją kopii zapasowych. Ponadto można użyć przystawki StorSimple Snapshot Manager do tworzenia harmonogramów tworzenia kopii zapasowych oraz klonowania lub Przywróć woluminy.
* **Adapter usługi StorSimple dla programu SharePoint** — gospodarstwa to narzędzie, które przezroczyste rozszerza program SharePoint Server Microsoft Azure StorSimple magazynowania i ochronę danych, podczas dokonywania widoczne i łatwe w zarządzaniu magazynu StorSimple z centralna programu SharePoint Portal administrowania.

Poniższy diagram zawiera widok ogólny składnikach i architekturze Microsoft Azure StorSimple.

![Architektura usługi StorSimple](./media/storsimple-overview/overview-big-picture.png)

W poniższych sekcjach opisano każdą z tych składników, które bardziej szczegółowo i wyjaśniono, jak rozwiązanie rozmieszcza dane, przydziela pamięć i ułatwia zarządzanie magazynami i ochrony danych. Ostatnia sekcja zawiera definicje dla niektóre ważne terminy i pojęcia dotyczące składników usługi StorSimple oraz zarządzania nimi.

## <a name="storsimple-device"></a>Urządzenia StorSimple
Urządzenie StorSimple systemu Azure firmy Microsoft jest hybrydową macierz magazynową w środowisku lokalnym, które udostępnia podstawowy magazyn i iSCSI dostęp do danych przechowywanych na nim. Zarządza komunikacją z magazynem w chmurze i pomaga w celu zapewnienia bezpieczeństwa i poufności wszystkich danych, która jest przechowywana na rozwiązanie Microsoft Azure StorSimple.

Urządzenia StorSimple obejmuje dysków SSD i HDD dyski twarde, a także obsługę klastrów i automatycznej pracy awaryjnej. Zawiera on udostępnionego procesora, magazynu udostępnionego i dwa kontrolery dublowanego. Każdy kontroler zapewnia następujące korzyści:

* Połączenie z komputerem hosta
* Do sześciu portach sieciowych, aby nawiązać połączenie z sieci lokalnej (LAN)
* Monitorowanie sprzętu
* Non-volatile pamięci o dostępie losowym (NVRAM), który zachowuje informacje, nawet jeśli przerwaniu zasilania
* Typu cluster-aware aktualizowanie w celu zarządzania aktualizacjami oprogramowania, na serwerach w klastrze trybu failover, tak, aby aktualizacje zostały minimalny lub nie wpływa na dostępność usługi
* Usługa klastrowania, które funkcje, takie jak klaster zaplecza, zapewniając wysoką dostępność i zminimalizowania niekorzystnych skutków, które mogą wystąpić, jeśli dysk twardy lub dysk SSD ulegnie awarii lub zostanie przełączony w tryb offline

Tylko jeden kontroler jest aktywny w dowolnym momencie w czasie. W przypadku niepowodzenia aktywnego kontrolera drugiego kontrolera automatycznie staje się aktywny.

Aby uzyskać więcej informacji, przejdź do [StorSimple składniki sprzętowe i stan](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>Urządzenie StorSimple w chmurze
Usługa StorSimple można użyć do utworzenia urządzenia w chmurze, która replikuje architekturę i możliwości fizycznego hybrydowe urządzenie magazynujące. Urządzenie StorSimple w chmurze (znany także jako urządzenie wirtualne StorSimple) jest uruchamiane w jednym węźle maszyny wirtualnej platformy Azure. (Urządzenie w chmurze można tworzyć tylko na maszynie wirtualnej platformy Azure. Nie można utworzyć na urządzeniu StorSimple lub na serwerze lokalnym.)

Urządzenie w chmurze ma następujące cechy:

* On zachowuje się jak urządzenie fizyczne i zaoferować interfejsu iSCSI do maszyn wirtualnych w chmurze.
* Można tworzyć nieograniczoną liczbę urządzeń w chmurze w chmurze i przekształca je włączać i wyłączać zgodnie z potrzebami.
* Może pomóc w symulacji dla środowisk lokalnych w odzyskiwania po awarii, programowania i scenariuszy testowania i może pomóc w poziomie elementu pobierania z kopii zapasowych.

Urządzenie StorSimple w chmurze jest dostępna w dwóch modeli: urządzenie 8010 (wcześniej znane jako 1100 model), a urządzenie 8020. Urządzenie 8010 ma maksymalną pojemność wynoszącą 30 TB. Urządzenie 8020 korzysta z usługi Azure premium storage, ma maksymalną pojemność wynoszącą 64 TB. (W warstwach lokalnych, usługi Azure premium storage przechowuje dane na dyskach SSD natomiast magazynu standard storage przechowuje dane na dyski twarde.) Należy pamiętać, że musi mieć konto magazynu platformy Azure — wersja premium do użycia magazynu w warstwie premium.

Aby uzyskać więcej informacji dotyczących urządzenia StorSimple w chmurze, przejdź do [wdrażanie i zarządzanie nimi Storsimple w chmurze na platformie Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Usługa Microsoft Azure StorSimple udostępnia interfejs użytkownika oparty na sieci web (usługa Menedżer urządzeń StorSimple), który umożliwia centralne zarządzanie Centrum danych i Magazyn w chmurze. Usługa Menedżer urządzeń StorSimple umożliwia wykonywanie następujących zadań:

* Skonfiguruj ustawienia systemu dla urządzeń StorSimple.
* Konfigurowanie i zarządzanie ustawieniami zabezpieczeń dla urządzeń StorSimple.
* Skonfiguruj poświadczenia chmury i właściwości.
* Konfigurowanie i zarządzanie nimi woluminów na serwerze.
* Konfigurowanie grup woluminu.
* Tworzenie kopii zapasowej i przywracania danych.
* Monitorowanie wydajności.
* Przejrzyj ustawienia systemowe i identyfikowania możliwych problemów.

Usługa Menedżer urządzeń StorSimple umożliwia wykonywanie wszystkich zadań administracyjnych, z wyjątkiem tych, które wymagają systemu w czasie, takie jak początkowa Konfiguracja i instalacja aktualizacji w dół.

Aby uzyskać więcej informacji, przejdź do [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Program Windows PowerShell dla usługi StorSimple
Windows PowerShell dla magazynu StorSimple udostępnia interfejs wiersza polecenia, który umożliwia tworzenie i zarządzanie usługą Microsoft Azure StorSimple i konfigurowanie i monitorowanie urządzeń StorSimple. Jest oparte na programie Windows PowerShell, interfejsu wiersza polecenia, który zawiera polecenia cmdlet przeznaczone do zarządzania urządzeniem StorSimple. Program Windows PowerShell dla usługi StorSimple zawiera funkcje, które pozwalają na:

* Rejestrowanie urządzenia.
* Skonfiguruj interfejs sieciowy na urządzeniu.
* Zainstaluj niektóre typy aktualizacji.
* Rozwiązywanie problemów z urządzeniem, uzyskując dostęp do sesji pomocy technicznej.
* Zmienianie stanu urządzenia.

Możesz uzyskać dostęp programu Windows PowerShell dla usługi StorSimple z poziomu konsoli szeregowej (na komputerze hosta, podłączone bezpośrednio do urządzenia) lub zdalnie przy użyciu komunikacji zdalnej programu Windows PowerShell. Należy pamiętać, że niektóre programu Windows PowerShell dla StorSimple zadania, takie jak początkowa rejestracja urządzenia, może być przeprowadzone wyłącznie na konsoli szeregowej.

Aby uzyskać więcej informacji, przejdź do [Użyj programu Windows PowerShell dla usługi StorSimple do administrowania urządzeniem](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Polecenia cmdlet programu PowerShell usługi StorSimple systemu Azure
Polecenia cmdlet programu Azure PowerShell StorSimple to zbiór poleceń cmdlet programu Windows PowerShell, które pozwalają na Automatyzowanie poziomu usługi oraz zadania migracji w wierszu polecenia. Aby uzyskać więcej informacji o poleceniach cmdlet programu Azure PowerShell dla usługi StorSimple, przejdź do [informacje o poleceniach cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
Przystawki StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC), można użyć w celu utworzenia spójnych, kopie zapasowe w momencie lokalnych i danych w chmurze. Ta przystawka działa na hoście z systemem Windows Server. Można użyć przystawki StorSimple Snapshot Manager do:

* Konfigurowanie, tworzenie kopii zapasowej i Usuń woluminy.
* Konfigurowanie grup woluminu, aby upewnić się, którego kopię zapasową danych jest spójnych z aplikacją.
* Zarządzanie zasadami tworzenia kopii zapasowych, tak aby dane kopii zapasowej na uprzednio określonym harmonogramem i przechowywane w wyznaczonym miejscu (lokalnie lub w chmurze).
* Przywracanie woluminów i poszczególnych plików.

Kopie zapasowe są przechwytywane jako migawki, które rejestrowania tylko zmian, ponieważ została wykonana ostatnia migawka i wymaga znacznie mniej miejsca niż pełne kopie zapasowe. Można utworzyć harmonogramów tworzenia kopii zapasowych lub korzystać z natychmiastowej kopii zapasowych, zgodnie z potrzebami. Ponadto umożliwia programu StorSimple Snapshot Manager ustanawianie zasad przechowywania tę kontrolkę, ile migawki zostaną zapisane. Jeśli później trzeba przywrócić dane z kopii zapasowej, umożliwia programu StorSimple Snapshot Manager, należy wybrać z katalogu lokalnego lub migawki w chmurze. 

W przypadku poważnej awarii lub jeśli potrzebujesz przywrócić dane z innego powodu, przystawki StorSimple Snapshot Manager przywraca go stopniowo jest potrzebne. Przywracanie danych nie jest wymagane, zamknij całego systemu podczas przywracania pliku, Zastąp sprzętu lub operacje są przenoszone do innej lokacji.

Aby uzyskać więcej informacji, przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adapter usługi StorSimple dla programu SharePoint
Usługa Microsoft Azure StorSimple obejmuje adaptera StorSimple dla programu SharePoint, opcjonalnym składnikiem, który rozszerza w sposób niewidoczny dla użytkownika StorSimple funkcji ochrony danych i magazynu do farmy serwerów programu SharePoint. Adapter współpracuje z dostawcy zdalnego magazynu obiektów Blob (SPZ) i funkcja SPZ serwera SQL, co umożliwia przenoszenie obiektów blob na serwer kopii zapasowej przez system Microsoft Azure StorSimple. Usługa Microsoft Azure StorSimple następnie przechowuje dane w obiekcie BLOB lokalnie lub w chmurze, na podstawie użycia.

Adaptera StorSimple dla programu SharePoint jest zarządzana z portalu administracji centralnej programu SharePoint. W związku z tym scentralizowane pozostaje zarządzania programu SharePoint, a cały magazyn wydaje się być w farmie programu SharePoint.

Aby uzyskać więcej informacji, przejdź do [adaptera StorSimple dla programu SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologie zarządzania magazynu
Oprócz dedykowanego urządzenia StorSimple, urządzenie wirtualne i inne składniki Microsoft Azure StorSimple wykorzystuje następujące technologie oprogramowania zapewniają szybki dostęp do danych i zmniejszenie zużycia pamięci masowej:

* [Obsługa automatycznego przechowywania warstw](#automatic-storage-tiering) 
* [Alokowanie elastyczne](#thin-provisioning) 
* [Na potrzeby deduplikacji i kompresji](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Obsługa automatycznego przechowywania warstw
Usługa Microsoft Azure StorSimple automatycznie rozmieszcza dane w warstwach logicznych na podstawie bieżącego użycia, wieku i relacji z innymi danymi. Dane, które są najbardziej aktywne są przechowywane lokalnie, podczas gdy mniej aktywnych i nieaktywnych danych jest automatycznie migrację do chmury. Na poniższym diagramie przedstawiono to podejście do magazynu.

![Warstwy magazynu StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Aby umożliwić szybki dostęp, StorSimple przechowuje bardzo aktywna dane (gorąca) na dyskach SSD w urządzeniu StorSimple. Przechowuje dane, które jest używane od czasu do czasu (ogrzewać danych) na dyski twarde na urządzeniu lub na serwerach w centrum danych. Powoduje przeniesienie nieaktywnych danych, tworzenia kopii zapasowych danych i dane przechowywane przez archiwizacji lub do celów zgodności z chmurą. 

> [!NOTE]
> W wersji Update 2 lub nowszym można określić wolumin przypięty lokalnie, w którym to przypadku dane pozostają na urządzeniu lokalnym i nie jest warstwowe w chmurze. 


Rozwiązania StorSimple, dostosowuje i Reorganizuje dane i zmienić przypisania magazynu jako wzorców użycia. Na przykład niektóre informacje mogą stać się mniej aktywne wraz z upływem czasu. Staje się stopniowo mniej aktywne, na dyskach HDD, a następnie do chmury jest migrowana z dysków SSD. Jeśli tych samych danych ponownie staje się aktywny, jest ona migrowane z powrotem do urządzenia magazynującego.

Proces warstw magazynu odbywa się w następujący sposób:

1. Administrator systemu konfiguruje konta magazynu w chmurze Microsoft Azure.
2. Administrator używa konsoli szeregowej i usługi Menedżer urządzeń StorSimple (uruchomionego w witrynie Azure portal) do skonfigurowania urządzenia i serwerów plików, tworzenia zasad ochrony danych i woluminów. Maszyny lokalne (na przykład serwery plików) umożliwia dostęp do urządzenia StorSimple Internet Small Computer System Interface (iSCSI).
3. Początkowo StorSimple przechowuje dane na szybkie warstwy dysków SSD urządzenia.
4. Jak pojemność zbliża się do warstwy dysków SSD, StorSimple deduplicates kompresuje najstarsze bloków danych i przenosi je do warstwy dysków HDD.
5. Jak pojemność podejścia warstwę HDD StorSimple szyfruje najstarsze bloków danych i wysyła je bezpiecznie z kontem usługi Microsoft Azure storage za pośrednictwem protokołu HTTPS.
6. Microsoft Azure tworzy wiele replik danych w swoim centrum danych i w zdalnym centrum danych, zapewniając, że w przypadku poważnej awarii można odzyskać dane.
7. Gdy serwer plików żąda danych przechowywanych w chmurze, StorSimple zwraca go bezproblemowo i zapisuje ich kopię na warstwy dysków SSD urządzenia StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Jak StorSimple zarządza danych w chmurze

Usługa StorSimple deduplicates dane klientów we wszystkich migawek oraz dane podstawowe (danych zapisanych przez hosty). Chociaż deduplikacja to doskonałe rozwiązanie dla wydajności przechowywania, to sprawia, że pytanie "jaki jest w chmurze" skomplikowane. Podstawowe dane warstwowe i dane migawki nakładają się na siebie nawzajem. Jeden fragment danych w chmurze może być używany jako dane warstwowe podstawowego i również być przywoływane przez wiele migawek. Każdy migawki w chmurze zapewnia, że kopii wszystkich danych w momencie jest zablokowany do chmury aż do usunięcia tej migawki.

Dane są usuwane tylko z chmury, gdy istnieją żadnych odwołań do tych danych. Na przykład w przypadku możemy migawkę w chmurze wszystkich danych, który znajduje się w urządzeniu StorSimple, a następnie usuń niektóre podstawowe dane, mamy widział _danych podstawowych_ porzucić natychmiast. _Danych w chmurze_ zawierający dane warstwowe i kopie zapasowe, pozostaje taka sama. Jest tak, ponieważ ma migawki nadal odwołuje się do danych w chmurze. Po chmurze migawka zostanie usunięta i innych migawek, do którego odwołuje się te same dane, spadnie zużycia w chmurze. Przed usuwamy danych w chmurze, możemy sprawdzić, czy nie znaleziono migawek nadal odwołują się do tych danych. Ten proces jest nazywany _wyrzucania elementów bezużytecznych_ i jest uruchomiona usługa w tle na urządzeniu. Usuwanie danych w chmurze nie jest procesem natychmiastowym zgodnie z usług kolekcji wyrzucania elementów sprawdza, czy inne odwołania do tych danych, przed usunięciem. Szybkość operacji wyrzucania elementów bezużytecznych zależy od łącznej liczby migawek i łączna ilość danych. Zazwyczaj danych w chmurze jest czyszczony w ciągu niespełna tygodnia.


### <a name="thin-provisioning"></a>Alokowanie elastyczne
Alokowanie elastyczne jest technologia wirtualizacji, dostępny magazyn prawdopodobnie przekroczyć zasobów fizycznych. Zamiast rezerwacji z wyprzedzeniem wystarczającej ilości miejsca, usługa StorSimple używa alokowania elastycznego można przydzielić tylko za mało miejsca, aby spełniały bieżące wymagania. Elastyczny charakter magazynu w chmurze ułatwia to podejście, ponieważ StorSimple można zwiększyć lub zmniejszyć magazyn w chmurze, aby spełnić potrzeby zgłaszanych.

> [!NOTE]
> Woluminy przypięte lokalnie nie są alokowany elastycznie. Magazyn przydzielony do woluminu tylko lokalne są aprowizowane w całości, gdy tworzony jest wolumin.


### <a name="deduplication-and-compression"></a>Na potrzeby deduplikacji i kompresji
Usługa Microsoft Azure StorSimple używa deduplikacji i kompresji danych, aby jeszcze bardziej ograniczyć wymagania dotyczące magazynu.

Funkcja deduplikacji zmniejsza ogólną ilość danych przechowywanych przez wyeliminowanie nadmiarowości przechowywanej zestawu danych. Jak zmieni się informacji StorSimple ignoruje niezmienione dane i przechwytuje tylko zmiany. Ponadto StorSimple zmniejsza ilość przechowywanych danych, identyfikowanie i usuwanie niepotrzebnych informacji. 

> [!NOTE]
> Dane na woluminy przypięte lokalnie nie jest deduplikowany lub skompresowany. Jednak tworzenie kopii zapasowych woluminów przypiętych lokalnie są deduplikowane i skompresowany.


## <a name="storsimple-workload-summary"></a>Podsumowanie obciążenia usługi StorSimple
Podsumowanie obsługiwanych obciążeń StorSimple jest przedstawione w poniższej tabeli.

| Scenariusz | Obciążenie | Obsługiwane | Ograniczenia | Version |
| --- | --- | --- | --- | --- |
| Współpraca |Udostępnianie plików |Tak | |Wszystkie wersje |
| Współpraca |Udostępnianie plików rozproszonych |Tak | |Wszystkie wersje |
| Współpraca |Sharepoint |Tak* |Obsługiwane tylko w przypadku woluminów przypiętych lokalnie |Update 2 i nowsza |
| Archiwizacja |Archiwizowanie pliku prostego |Yes | |Wszystkie wersje |
| Wirtualizacja |Maszyny wirtualne |Tak* |Obsługiwane tylko w przypadku woluminów przypiętych lokalnie |Update 2 i nowsza |
| Database (Baza danych) |SQL |Tak* |Obsługiwane tylko w przypadku woluminów przypiętych lokalnie |Update 2 i nowsza |
| Monitorowania wideo |Monitorowania wideo |Tak* |Obsługiwane w przypadku urządzenia StorSimple jest przeznaczona wyłącznie do tego obciążenia |Update 2 i nowsza |
| Backup |Podstawowy cel kopii zapasowej |Tak* |Obsługiwane w przypadku urządzenia StorSimple jest przeznaczona wyłącznie do tego obciążenia |Update 3 i nowszych |
| Backup |Docelowy dodatkowej kopii zapasowej |Tak* |Obsługiwane w przypadku urządzenia StorSimple jest przeznaczona wyłącznie do tego obciążenia |Update 3 i nowszych |

*Tak&#42; — rozwiązanie wytycznych i ograniczeniach powinny być stosowane.*

Następujące obciążenia nie są obsługiwane przez urządzenia serii StorSimple 8000. Jeśli zostały wdrożone na StorSimple, te obciążenia spowoduje nieobsługiwaną konfigurację.

* Obrazowanie medyczne
* Exchange
* VDI
* Oracle
* SAP
* Dane Big Data
* Dystrybucja zawartości
* Rozruch z poziomu SCSI

Poniżej znajduje się lista składników infrastruktury usługi StorSimple jest obsługiwany.

| Scenariusz | Obciążenie | Obsługiwane | Ograniczenia | Version |
| --- | --- | --- | --- | --- |
| Ogólne |ExpressRoute |Yes | |Wszystkie wersje |
| Ogólne |DataCore FC |Tak* |Obsługiwane z DataCore SANsymphony |Wszystkie wersje |
| Ogólne |DFSR |Tak* |Obsługiwane tylko w przypadku woluminów przypiętych lokalnie |Wszystkie wersje |
| Ogólne |Indeksowanie |Tak* |Dla woluminów warstwowych, indeksowanie tylko metadanych jest obsługiwany (Brak danych).<br>Pełne indeksowanie jest obsługiwane dla woluminów przypiętych lokalnie. |Wszystkie wersje |
| Ogólne |Oprogramowanie antywirusowe |Tak* |Dla woluminy warstwowe jest obsługiwane tylko skanowanie pod kątem Otwórz i zamknij.<br> Dla woluminów przypiętych lokalnie pełne skanowanie jest obsługiwane. |Wszystkie wersje |

*Tak&#42; — rozwiązanie wytycznych i ograniczeniach powinny być stosowane.*

Poniżej przedstawiono listę innego oprogramowania, które służą do tworzenia rozwiązań, za pomocą usługi StorSimple.

| Typ obciążeń | Oprogramowanie używane z usługą StorSimple | Obsługiwane wersje|Łącze do przewodnika dotyczącego rozwiązań| 
| --- | --- | --- | --- |
| Docelowa kopia zapasowa |Veeam |V Veeam 9 lub nowszy |[Usługa StorSimple jako miejsce docelowe kopii zapasowej za pomocą Veaam](storsimple-configure-backup-target-veeam.md)|
| Docelowa kopia zapasowa |Oprogramowaniem Veritas Backup Exec |Backup Exec 16 lub nowszy |[Usługa StorSimple jako miejsce docelowe kopii zapasowej za pomocą Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Docelowa kopia zapasowa |Veritas NetBackup |NetBackup 7.7.x lub nowszy  |[Usługa StorSimple jako miejsce docelowe kopii zapasowej za pomocą NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Udostępnianie plików globalne <br></br> Współpraca |Talon  |[Usługa StorSimple z Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologia dotycząca usługi StorSimple
Przed wdrożeniem rozwiązania Microsoft Azure StorSimple, firma Microsoft zaleca, należy przejrzeć następujące terminy i definicje.

### <a name="key-terms-and-definitions"></a>Kluczowe terminy i definicje
| Okres (akronim lub skrót) | Opis |
| --- | --- |
| rekord kontroli dostępu (ACR) |Rekord, skojarzone z woluminu na urządzeniu z systemem Microsoft Azure StorSimple określającą hosta, którego można z nim połączyć. Oznaczanie jest oparty na protokole iSCSI kwalifikowana nazwa (IQN) z hostów (znajdujących się w rekordu ACR) łączących się z urządzeniem StorSimple. |
| AES-256 |256-bitowego algorytmu Advanced Encryption Standard (AES) do szyfrowania danych, kiedy przesuwa się on do i z chmury. |
| rozmiar jednostki alokacji (Australia) |Najmniejsza ilość miejsca na dysku, która może być przydzielona do przechowywania pliku w Windows swoje systemy plików. Jeśli rozmiar pliku nie jest wielokrotnością rozmiaru klastra, dodatkowe miejsce może służyć do przechowywania plików (maksymalnie kolejną wielokrotnością rozmiaru klastra) skutkuje utratę miejsca i fragmentacji dysku twardego. <br>Zalecane jednostek Alokacji dla woluminów StorSimple systemu Azure to 64 KB, ponieważ jest on dobrze działa z algorytmami deduplikacji. |
| obsługą poziomów magazynów automatycznych |Automatycznie przenosi mniej aktywne dane z dysków SSD, HDD, a następnie warstwy w chmurze, a następnie umożliwienie zarządzania cały magazyn z interfejsem użytkownika centralnej. |
| wykaz kopii zapasowych |Kolekcja kopie zapasowe, które są zazwyczaj powiązane przez typ aplikacji, który został użyty. Ta kolekcja zostanie wyświetlona w bloku katalogu kopii zapasowej interfejsu użytkownika usługi Menedżer urządzeń StorSimple. |
| Plik kopii zapasowej katalogu |Plik zawierający listę migawek dostępne obecnie przechowywane w kopii zapasowej bazy danych z programu StorSimple Snapshot Manager. |
| zasady tworzenia kopii zapasowej |Wybór woluminów, typ kopii zapasowej i harmonogram, który pozwala na tworzenie kopii zapasowych na ze wstępnie zdefiniowanym harmonogramem. |
| duże obiekty binarne (BLOB) |Kolekcja danych binarnych przechowywanych jako pojedynczą jednostkę w system zarządzania bazami danych. Obiekty BLOB są zazwyczaj obrazy, audio lub innych obiektów multimedialnych, chociaż czasami binarnego kodu wykonywalnego są przechowywane jako obiekt BLOB. |
| Protokół uwierzytelniania typu Challenge Handshake (CHAP) |Protokół używany do uwierzytelniania elementu równorzędnego połączenia, oparte na element równorzędny udostępnianie hasła lub klucza tajnego. Protokół CHAP mogą być jednokierunkowe lub wzajemnego. Przy użyciu jednokierunkowego CHAP inicjatora uwierzytelniany element docelowy. Wzajemnego CHAP wymaga, że obiekt docelowy uwierzytelniania inicjatora i że inicjator uwierzytelnia obiektu docelowego. |
| Klon |Duplikat woluminu. |
| Chmury w ramach warstwy (CaaT) |Magazyn zintegrować jako warstwy w ramach architektury magazynu, tak, aby wszystkie magazyny są widoczne jako część sieci magazynowania jednego przedsiębiorstwa w chmurze. |
| Dostawca usług w chmurze (CSP) |Dostawca usług chmury obliczeniowej. |
| Migawka w chmurze |W momencie kopia woluminu danych przechowywanych w chmurze. Migawka w chmurze jest równoznaczne z migawki replikowane w systemie magazynu inną, poza siedzibą firmy. Migawki w chmurze są szczególnie przydatne w przypadku scenariuszy odzyskiwania po awarii. |
| klucz szyfrowania magazynu w chmurze |Hasło lub klucz używany przez urządzenia StorSimple do dostępu do zaszyfrowanych danych, które są wysyłane przez urządzenie do chmury. |
| Aktualizacja typu cluster-aware |Zarządzanie aktualizacje oprogramowania na serwerach w klastrze trybu failover, aby aktualizacje zostały minimalny lub nie wpływa na dostępność usług. |
| ścieżki danych |Kolekcja jednostki organizacyjne, które wykonują operacje wzajemnie połączonych przetwarzania danych. |
| Dezaktywuj |Stałe akcji, która spowoduje przerwanie połączenia między urządzeniem StorSimple i usługą skojarzonej chmurze. Migawki urządzenia w chmurze pozostają po zakończeniu tego procesu i można sklonować lub używanej na potrzeby odzyskiwania po awarii. |
| dublowanie dysków |Replikację woluminów dysku logicznego do osobnych twardych dysków w czasie rzeczywistym, aby zapewnić ciągłą dostępność. |
| dublowanie dysk dynamiczny |Replikacja woluminów dysku logicznego w przypadku dysków dynamicznych. |
| dyski dynamiczne |Format woluminu dysku, który używa Menedżera dysków logicznych (LDM) do przechowywania danych i zarządzanie nimi na wielu dyskach fizycznych. Dyski dynamiczne można uruchomić zapewniają więcej wolnego miejsca. |
| Rozszerzone obudowy wiele dysków (EBOD) |Pomocniczy Obudowa urządzenia Microsoft Azure StorSimple, który zawiera dyski dodatkowego dysku twardego dla dodatkowego miejsca do magazynowania. |
| Inicjowanie obsługi administracyjnej FAT |Konwencjonalne Inicjowanie obsługi administracyjnej magazynu w magazynie, które miejsce jest przydzielane na podstawie przewidywanych potrzeb (i jest zwykle poza bieżącej potrzeby). Zobacz też *alokowanie*. |
| dysku twardym (HDD) |Dysk używa płyt rotacji do przechowywania danych. |
| hybrydowy magazyn w chmurze |Architektura magazynu, która korzysta z zasobów lokalnych i przechowywanych poza siedzibą firmy, w tym magazynie w chmurze. |
| Internet Small Computer System Interface (iSCSI) |Magazynu opartego na protokole IP sieci standard do łączenia urządzeń magazynu danych lub obiektów. |
| Inicjator iSCSI |Składnik oprogramowania, który umożliwia komputer hosta z systemem Windows z siecią zewnętrznego magazynu opartego na protokole iSCSI. |
| iSCSI kwalifikowana nazwa (IQN) |Unikatowa nazwa identyfikująca obiektu docelowego iSCSI lub inicjatora. |
| obiekt docelowy iSCSI |Składnik oprogramowania, który zapewnia scentralizowane iSCSI podsystemów dysków w sieci magazynowania. |
| archiwizowanie na żywo |Podejście magazynu, w którym dane archiwalne jest dostępny przez cały czas (nie są przechowywane siedzibą na taśmie, na przykład). Usługa Microsoft Azure StorSimple używa archiwizacji na żywo. |
| wolumin przypięty lokalnie |wolumin, który znajduje się na urządzeniu i nigdy nie jest warstwowe w chmurze. |
| Migawka lokalna |W momencie kopię danych woluminów przechowywanych na urządzeniu Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Zaawansowane rozwiązanie składające się z urządzenia pamięci masowej w centrum danych i oprogramowanie, które umożliwia organizacjom INFORMATYCZNYM wykorzystywać magazyn w chmurze, tak jakby był to magazynu centrum danych. StorSimple ułatwia ochronę danych i zarządzanie danymi przy jednoczesnym zmniejszeniu kosztów. Rozwiązanie konsoliduje podstawowy magazyn, archiwum, kopia zapasowa i odzyskiwanie po awarii odzyskiwanie (DR) dzięki bezproblemowej integracji z chmurą. Łącząc SAN zarządzania danymi i magazynu w chmurze na platformie klasy korporacyjnej, urządzeń StorSimple, Włącz szybkość, prostota i niezawodność, wszystkie potrzeby związane z magazynowaniem. |
| Zasilania i chłodzenia modułu (PCM) |Składniki sprzętowe urządzenia StorSimple składający się z źródła zasilania i chłodzenia wentylator, dlatego nazwa zasilania i chłodzenia modułu. Podstawowy Obudowa urządzenia ma dwa PCMs 764W obudowy EBOD ma dwa PCMs 580W. |
| Podstawowy obudowy |Obudowa główna urządzenia StorSimple, która zawiera kontrolery platformy aplikacji. |
| Cel czasu odzyskiwania (RTO) |Maksymalną ilość czasu, który powinna wydatkowany przed rozpoczęciem procesu biznesowego lub system jest w pełni przywrócone po awarii. |
| magistrali Serial attached SCSI (SAS) |Typ dysku twardym (HDD). |
| klucz szyfrowania danych usługi |Klucz, dostępne dla wszystkich nowych urządzeń StorSimple, których wykonywana jest rejestracja w usłudze Menedżer urządzeń StorSimple. Dane konfiguracji usługi Menedżer urządzeń StorSimple oraz urządzenia są szyfrowane przy użyciu klucza publicznego, a następnie odszyfrować je mogą tylko na urządzeniu przy użyciu klucza prywatnego. Klucz szyfrowania danych usługi umożliwia usłudze uzyskać ten klucz prywatny do odszyfrowywania. |
| klucz rejestracji usługi |Klucz, który ułatwia rejestrowanie urządzenia StorSimple za pomocą usługi Menedżer urządzeń StorSimple, aby była ona wyświetlana w witrynie Azure portal, aby uzyskać dalsze akcje z zakresu zarządzania. |
| Small Computer System Interface (SCSI) |Zestaw standardy dotyczące fizycznego łączenia komputerów i przekazywania danych między nimi. |
| dysku półprzewodnikowym (SSD) |Dysk, który zawiera nie ruchomych części; na przykład dysku flash. |
| konto magazynu |Zestaw poświadczeń dostępu połączony z kontem magazynu dla danej chmury dostawcy usług. |
| Adapter usługi StorSimple dla programu SharePoint |Składnik Microsoft Azure StorSimple, który rozszerza w sposób niewidoczny dla użytkownika StorSimple magazynowania i ochronę danych do farmy serwerów programu SharePoint. |
| Usługa Menedżer urządzeń StorSimple |Rozszerzenie portalu Azure, która pozwala na zarządzanie Azure StorSimple w środowisku lokalnym i urządzeń wirtualnych. |
| StorSimple Snapshot Manager |Program Microsoft Management Console (MMC) przystawki do zarządzania i przywracania kopii zapasowych operacje w Microsoft Azure StorSimple. |
| Wykonaj kopię zapasową |Funkcja, która umożliwia użytkownikowi przejęcie interakcyjnego wykonywania kopii zapasowej woluminu. To alternatywny sposób podejmowania ręcznego tworzenia kopii zapasowej woluminu zamiast wykonywania automatycznej obsługi kopii zapasowych przy użyciu zdefiniowanych zasad. |
| Alokowanie elastyczne |Metoda optymalizowania wydajności za pomocą którego dostępne miejsce jest używana w systemach magazynowania. W alokowanie elastyczne, Magazyn jest przydzielany przez wielu użytkowników, w oparciu o minimalna ilość miejsca, wymagane przez każdego użytkownika w dowolnym momencie. Zobacz też *fat aprowizacji*. |
| Obsługa warstw |Organizowanie danych w logiczne grupy w oparciu o bieżące użycie, wiek i relacji z innymi danymi. Usługa StorSimple automatycznie rozmieszcza dane w warstwach. |
| wolumin |Obszary magazynu logicznego prezentowane w postaci dysków. Woluminy StorSimple odpowiadać woluminów zainstalowanych w wyniku hosta, łącznie z tymi odnalezione za pomocą interfejsu iSCSI i urządzeń StorSimple. |
| kontener woluminów |Grupowanie woluminów i ustawienia, które ich dotyczą. Wszystkie woluminy w urządzeniu StorSimple są grupowane w kontenerach woluminów. Ustawienia kontenera woluminów obejmują kont magazynu, ustawienia szyfrowania dla danych przesyłanych do chmury przy użyciu kluczy szyfrowania skojarzonego i użytej przepustowości na potrzeby operacji dotyczących chmury. |
| grupy woluminów |Programie StorSimple Snapshot Manager woluminu grupy to zbiór woluminy skonfigurowany w celu ułatwienia tworzenia kopii zapasowej przetwarzania. |
| Usługa kopiowania woluminów w tle (VSS) |Usługa systemu operacyjnego Windows Server zapewnia spójność aplikacji, komunikując się z aplikacji rozpoznających usługę VSS. do zapewnienia koordynacji tworzenia migawek przyrostowych. Usługa VSS zapewnia, że aplikacje są tymczasowo nieaktywnych, przy tworzeniu migawek woluminów. |
| Program Windows PowerShell dla usługi StorSimple |Oparte na programie Windows PowerShell interfejsu wiersza polecenia używane do obsługi i zarządzania urządzeniem StorSimple. Przy zachowaniu niektórych podstawowych funkcji programu Windows PowerShell, ten interfejs ma dodatkowe dedykowanych poleceń cmdlet, które są przeznaczone dla zarządzania urządzeniem StorSimple. |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).

