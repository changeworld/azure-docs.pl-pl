---
title: Instalowanie adaptera StorSimple dla programu SharePoint | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zainstalować i skonfigurować lub usunąć adaptera StorSimple dla programu SharePoint w farmie serwerów programu SharePoint.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.openlocfilehash: a2f8e75578e396085e7d80f43c1180e158967061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633422"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalowanie i Konfigurowanie adaptera StorSimple dla programu SharePoint
## <a name="overview"></a>Omówienie
Adaptera StorSimple dla programu SharePoint jest składnikiem, która umożliwia dostarczanie elastycznego magazynu Microsoft Azure StorSimple i ochrony danych do farmy serwerów programu SharePoint. Adapter umożliwia przeniesienie dużych obiektów binarnych (BLOB) zawartości z baz danych zawartości programu SQL Server do urządzenie magazynujące Microsoft Azure StorSimple w chmurze hybrydowej.

Adaptera StorSimple dla programu SharePoint działa jako dostawca magazynu obiektów BLOB zdalnego (SPZ) i korzysta z funkcji zdalnego magazynu obiektów BLOB programu SQL Server do przechowywania zawartości bez struktury programu SharePoint (w formie obiektów blob) na serwerze plików, która jest wspierana przez urządzenia StorSimple.

> [!NOTE]
> Adaptera StorSimple dla programu SharePoint obsługuje programu SharePoint Server 2010 zdalnego obiektu BLOB magazynu (SPZ). Nie obsługuje programu SharePoint Server 2010 zewnętrznych obiektów BLOB Storage (EBS).


* Aby pobrać adaptera StorSimple dla programu SharePoint, przejdź do [adaptera StorSimple dla programu SharePoint] [ 1] w programie Microsoft Download Center.
* Aby uzyskać informacje o planowaniu SPZ i SPZ ograniczenia, przejdź do [podjęciem decyzji o użyciu SPZ w programie SharePoint 2013] [ 2] lub [planowanie SPZ (SharePoint Server 2010)] [ 3].

Pozostała część tego omówienia krótko opisano rolę adaptera StorSimple dla programu SharePoint i SharePoint pojemność i wydajność ograniczeń, które należy wiedzieć, przed zainstalowaniem i skonfigurowaniem karty. Po przejrzeniu tych informacji, przejdź do [adaptera StorSimple dla instalacji programu SharePoint](#storsimple-adapter-for-sharepoint-installation) aby rozpocząć konfigurowanie karty.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Adapter usługi StorSimple do korzyści z programu SharePoint
W witrynie programu SharePoint zawartość jest przechowywana jako danych obiektów BLOB bez struktury w jeden lub więcej baz danych zawartości. Domyślnie te bazy danych znajdują się na komputerach z programem SQL Server, które znajdują się w farmie serwerów programu SharePoint. Obiekty BLOB szybko zwiększyć rozmiar, zużywa bardzo dużą ilość magazynu w środowisku lokalnym. Z tego powodu można znaleźć rozwiązania magazynu w innym, mniej kosztowne. SQL Server udostępnia technologia o nazwie zdalnego obiektu Blob magazynu (SPZ) umożliwiająca przechowywanie zawartości obiektu BLOB w systemie plików, poza bazą danych programu SQL Server. Za pomocą SPZ obiekty BLOB mogą znajdować się w systemie plików na komputerze, na którym działa program SQL Server lub mogą być przechowywane w systemie plików na innym komputerze serwera.

SPZ wymaga użycia dostawcę SPZ, takich jak adaptera StorSimple dla programu SharePoint, aby umożliwić SPZ w programie SharePoint. Adaptera StorSimple dla programu SharePoint w programach SPZ, co umożliwia przenoszenie obiektów blob na serwer kopii zapasowej przez system Microsoft Azure StorSimple. Usługa Microsoft Azure StorSimple następnie przechowuje dane w obiekcie BLOB lokalnie lub w chmurze, na podstawie użycia. Obiekty BLOB, które bardzo aktywnych (zwykle określane jako warstwy 1 lub gorących danych) znajdują się lokalnie. Mniej aktywne dane i dane archiwalne znajdują się w chmurze. Po włączeniu SPZ na bazę danych zawartości każdej nowej zawartości obiektu BLOB, utworzone w programie SharePoint są przechowywane na urządzeniu StorSimple, a nie w bazie danych zawartości.

Implementacja Microsoft Azure StorSimple SPZ zapewnia następujące korzyści:

* Przez przenoszenie zawartości obiektu BLOB na osobnym serwerze można zmniejszyć obciążenie zapytaniami w programie SQL Server, co może poprawić czas odpowiedzi programu SQL Server. 
* Usługa StorSimple używa deduplikacja i kompresja zmniejszyć rozmiar danych.
* Usługa StorSimple zapewnia ochronę danych w formie lokalnej i migawki w chmurze. Ponadto jeśli umieścisz sama baza danych na urządzeniu StorSimple Twojej kopi zapasowej bazy danych zawartości i obiekty BLOB ze sobą w taki sposób, spójnego na poziomie awarii. (Przenoszenie bazy danych zawartości na urządzeniu jest obsługiwana tylko dla urządzenia StorSimple 8000 series. Ta funkcja nie jest obsługiwana w przypadku serii 5000 i 7000.)
* Usługa StorSimple systemu Azure zawiera funkcje odzyskiwania po awarii, w tym tryb failover, odzyskiwanie plików i woluminów (w tym testowe odzyskiwanie) i szybkie przywracanie danych.
* Oprogramowanie do odzyskiwania danych, takich jak Kroll Ontrack PowerControls, służy przy użyciu migawek StorSimple danych obiektów BLOB do przeprowadzenia odzyskiwania na poziomie elementu zawartości programu SharePoint. (To oprogramowanie do odzyskiwania danych jest oddzielnego zakupu).
* Adaptera StorSimple dla programu SharePoint podłącza się do portalu administracji centralnej programu SharePoint, dzięki czemu możesz zarządzać całego rozwiązania programu SharePoint z centralnej lokalizacji.

Przenoszenie zawartości obiektu BLOB w systemie plików może zapewnić inne oszczędności kosztów i korzyści. Na przykład za pomocą SPZ można zmniejszyć zapotrzebowanie na tańsze magazynowanie warstwy 1, a ponieważ zmniejsza bazy danych zawartości, SPZ pozwala zmniejszyć liczbę baz danych wymagane w farmie serwerów programu SharePoint. Jednak inne czynniki, takie jak limity rozmiaru bazy danych i ilości zawartości bez SPZ może również wpływać na wymagania dotyczące magazynu. Aby uzyskać więcej informacji na temat koszty i korzyści z używania SPZ zobacz [planowanie SPZ (SharePoint Foundation 2010)] [ 4] i [podjęciem decyzji o użyciu SPZ w programie SharePoint 2013] [5].

### <a name="capacity-and-performance-limits"></a>Limity pojemności i wydajności
Przed należy wziąć pod uwagę przy użyciu SPZ w rozwiązaniu programu SharePoint należy pamiętać o wydajności przetestowane i limitów pojemności programu SharePoint Server 2010 i SharePoint Server 2013 oraz jak te limity dotyczą akceptowalny poziom wydajności. Aby uzyskać więcej informacji, zobacz [granic oprogramowania i limity dla programu SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Przed skonfigurowaniem SPZ, przejrzyj następujący kod:

* Upewnij się, że łączny rozmiar zawartości (rozmiar bazy danych zawartości) plus rozmiar wszystkie skojarzone obiekty BLOB zewnętrznych nie przekracza limit rozmiaru SPZ, które są obsługiwane przez program SharePoint. To ograniczenie wynosi 200 GB. 
  
    **Baza danych zawartości miary i rozmiar obiektu BLOB**
  
  1. Uruchom to zapytanie na centralnym WFE administracji. Uruchom powłokę zarządzania programu SharePoint, a następnie wprowadź następujące polecenie programu Windows PowerShell, aby uzyskać rozmiar bazy danych zawartości:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      W tym kroku pobiera rozmiar bazy danych zawartości na dysku.
  2. Uruchom następujące zapytania SQL w programie SQL Management Studio w oknie programu SQL server w poszczególnych bazach danych zawartości i Dodaj wynik do numeru uzyskaną w kroku 1.
     
     W bazach danych zawartości programu SharePoint 2013 wpisz:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     W bazach danych zawartości programu SharePoint 2010 wpisz:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     W tym kroku pobiera rozmiar obiektów blob, które mają zostać zewnętrznych.
* Firma Microsoft zaleca przechowywania całej zawartości obiektów BLOB i bazy danych lokalnie na urządzeniu StorSimple. Urządzenie StorSimple jest dwa węzły klastra w celu zapewnienia wysokiej dostępności. Wprowadzenie do bazy danych zawartości i obiektów blob na urządzeniu StorSimple zapewnia wysoką dostępność.
  
    Tradycyjnych SQL migracji najlepsze rozwiązania dotyczące serwera umożliwia przeniesienie bazy danych zawartości z urządzeniem StorSimple. Przenoszenie bazy danych, tylko wtedy, gdy cała zawartość obiektu BLOB z bazy danych została przeniesiona do udziału plików za pośrednictwem SPZ. Jeśli chcesz przenieść bazę danych zawartości z urządzeniem StorSimple, firma Microsoft zaleca, skonfigurować Magazyn bazy danych zawartości na urządzenia jako wolumin podstawowy.
* Usługa Microsoft Azure StorSimple, jeśli za pomocą woluminów warstwowych istnieje ma gwarancji tej zawartości, przechowywane lokalnie na StorSimple, urządzenia nie będą umieszczane do magazynu w chmurze Microsoft Azure. Z tego powodu zaleca się przy użyciu usługi StorSimple lokalnie przypięte woluminy w połączeniu z SPZ programu SharePoint. Pozwoli to zagwarantować, że cała zawartość obiektu BLOB pozostaje lokalnie na urządzeniu StorSimple i nie jest przenoszony do systemu Microsoft Azure.
* Jeśli bazy danych zawartości nie są przechowywane na urządzeniu StorSimple, należy użyć tradycyjnego programu SQL Server o wysokiej dostępności najlepsze rozwiązania, które obsługują SPZ. Klaster programu SQL Server obsługuje SPZ, podczas SQL Server dublowanie nie jest. 

> [!WARNING]
> Jeśli nie włączono SPZ, zaleca się przeniesienie bazy danych zawartości z urządzeniem StorSimple. To jest Konfiguracja nieprzetestowanych.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Adapter usługi StorSimple dla instalacji programu SharePoint
Przed zainstalowaniem adaptera StorSimple dla programu SharePoint, należy skonfigurować urządzenie StorSimple i upewnij się, że farma serwerów programu SharePoint, a wystąpienia programu SQL Server spełniają wszystkie wymagania wstępne. W tym samouczku opisano wymagania dotyczące konfiguracji, a także procedury dotyczące instalowania i uaktualniania adaptera StorSimple dla programu SharePoint.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych
Przed zainstalowaniem adaptera StorSimple dla programu SharePoint, upewnij się, że urządzenia StorSimple, farmy serwerów programu SharePoint i wystąpienia programu SQL Server spełniają następujące wymagania wstępne.

### <a name="system-requirements"></a>Wymagania systemowe
Adaptera StorSimple dla programu SharePoint w programach następujący sprzęt i oprogramowanie:

* Obsługiwany system operacyjny — Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2012 lub Windows Server 2012 R2
* Obsługiwane wersje programu SharePoint — program SharePoint Server 2010 lub SharePoint Server 2013
* Obsługiwane wersje programu SQL Server — program SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition lub SQL Server 2012 Enterprise Edition
* Obsługiwane — urządzeń StorSimple z serii StorSimple 8000, StorSimple 7000 series lub serii StorSimple 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji urządzenia StorSimple
Urządzenie StorSimple jest urządzeniem bloku i jako takie wymaga serwera plików, na którym dane mogą być hostowane. Zalecamy użycie osobnego serwera, a nie z istniejącego serwera z farmy programu SharePoint. Ten serwer plików musi być w tej samej sieci (LAN) jako komputerem programu SQL Server, który jest hostem bazy danych zawartości.

> [!TIP]
> * Jeśli skonfigurujesz farmę programu SharePoint w celu zapewnienia wysokiej dostępności, również należy wdrożyć serwer plików w celu zapewnienia wysokiej dostępności.
> * Jeśli baza danych zawartości nie są przechowywane na urządzeniu StorSimple, należy stosować najlepsze rozwiązania tradycyjnych wysokiej dostępności, obsługujące SPZ. Klaster programu SQL Server obsługuje SPZ, podczas SQL Server dublowanie nie jest. 


Upewnij się, że urządzenie StorSimple jest prawidłowo skonfigurowany i że skonfigurowano odpowiednie woluminy do obsługi wdrożenia programu SharePoint i dostępny z komputera programu SQL Server. Przejdź do [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md) Jeśli nie zostały jeszcze wdrożone i skonfigurowane urządzenie StorSimple. Zanotuj adres IP urządzenia StorSimple; będzie potrzebny podczas adaptera StorSimple dla instalacji programu SharePoint.

Ponadto upewnij się, że wolumin, który ma być używany dla obiektów BLOB o eksternalizację spełnia następujące wymagania:

* Wolumin musi być sformatowany przy użyciu rozmiaru jednostki alokacji 64 KB.
* Usługi frontonu sieci web (WFE) i serwerów aplikacji musi mieć możliwość dostępu do woluminu, za pośrednictwem ścieżki Universal Naming Convention (UNC).
* Farmy serwerów programu SharePoint musi być skonfigurowany do zapisu do woluminu.

> [!NOTE]
> Po zainstalowaniu i skonfigurowaniu karty, wszystkich obiektów BLOB o eksternalizację musi przechodzić przez urządzenia StorSimple (urządzenie będzie prezentują woluminy do programu SQL Server i zarządzanie warstwami magazynowania). Nie można użyć innych obiektów docelowych o eksternalizację obiektu BLOB.


Jeśli planujesz wykonać migawki danych obiektów BLOB oraz bazy danych za pomocą Menedżera migawek StorSimple, należy zainstalować przystawki StorSimple Snapshot Manager na serwerze bazy danych, tak, aby składnik zapisywania usługi SQL można użyć do wdrożenia Windows woluminów w tle kopii Service (VSS).

> [!IMPORTANT]
> Przystawki StorSimple Snapshot Manager nie obsługuje składnik zapisywania usługi VSS programu SharePoint i nie można wykonać migawki spójne z aplikacjami danych programu SharePoint. W przypadku programu SharePoint programu StorSimple Snapshot Manager zawiera tylko kopie zapasowe spójne na poziomie awarii.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji farmy programu SharePoint
Upewnij się, że farmę programu SharePoint server jest prawidłowo skonfigurowany, w następujący sposób:

* Sprawdź, czy farmy serwerów programu SharePoint jest w dobrej kondycji i sprawdź następujące kwestie:
* Wszystkie frontonie WFE programu SharePoint i zarejestrowanych w farmie serwerów aplikacji działają i reaguje na operację ping z serwera, na którym będzie instalowany adaptera StorSimple dla programu SharePoint.
* Usługa czasomierza programu SharePoint (SPTimerV3 lub SPTimerV4) działa na każdym serwerze WFE i serwera aplikacji.
* Zarówno usługa czasomierza programu SharePoint, jak i puli aplikacji usług IIS, na którym uruchomiono witryny Administracja centralna programu SharePoint należy mieć uprawnienia administracyjne.
* Upewnij się, że kontekst zabezpieczeń rozszerzone Eksploratora Internet (IE ESC) jest wyłączona. Wykonaj następujące kroki, aby wyłączyć konfigurację IE ESC:
  
  1. Zamknij wszystkie wystąpienia programu Internet Explorer.
  2. Uruchom Menedżera serwera.
  3. W okienku po lewej stronie kliknij **lokalnego serwera**.
  4. W okienku po prawej stronie obok pozycji **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**, kliknij przycisk **na**.
  5. W obszarze **Administratorzy**, kliknij przycisk **poza**.
  6. Kliknij przycisk **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Zdalnego magazynu obiektów BLOB (SPZ) wymagania wstępne
Upewnij się, że używasz obsługiwanej wersji programu SQL Server. Tylko następujące wersje są obsługiwane i można używać SPZ:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Obiekty BLOB mogą externalized na tych woluminach, które urządzenia StorSimple prezentują do programu SQL Server. Nie innych elementów docelowych dla obiektów BLOB o eksternalizację są obsługiwane.

Po zakończeniu wszystkich kroków konfiguracji wymagania wstępnego, pozycji [zainstalować Adapter usługi StorSimple dla programu SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalowanie adaptera StorSimple dla programu SharePoint
Wykonaj następujące kroki, aby zainstalować Adapter usługi StorSimple dla programu SharePoint. Jeśli ponownej instalacji oprogramowania, zobacz [uaktualnić lub ponownie zainstalować Adapter usługi StorSimple dla programu SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Czas wymagany do instalacji zależy od łącznej liczby baz danych programu SharePoint w farmie serwerów programu SharePoint.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurowanie SPZ
Po zainstalowaniu adaptera StorSimple dla programu SharePoint, należy skonfigurować SPZ zgodnie z opisem w poniższej procedurze.

> [!TIP]
> Adaptera StorSimple dla programu SharePoint podłącza się do strony administracji centralnej programu SharePoint, dzięki czemu SPZ można włączać lub wyłączać w poszczególnych bazach danych zawartości w farmie programu SharePoint. Jednak włączenie lub wyłączenie SPZ zawartości bazy danych powoduje, że resetowanie usług IIS, która w zależności od konfiguracji farmy, chwilowo może zakłócać dostępności SharePoint frontonu sieci web (WFE). (Czynników, takich jak korzystanie z modułu równoważenia obciążenia frontonu, bieżące obciążenie serwera i tak dalej, można ograniczyć lub wyeliminować ten przerw w działaniu). Aby chronić użytkowników przed zakłócenia, zaleca się, że można włączyć lub wyłączyć SPZ tylko podczas okna zaplanowanej konserwacji.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurowanie wyrzucania elementów bezużytecznych
Usunięcie obiektów z witryny programu SharePoint mogą nie są automatycznie usuwane z SPZ woluminu magazynu. Zamiast tego asynchroniczny, tło konserwacji usunie oddzielonych obiektów blob z magazynu plików. Administratorzy systemu można zaplanować okresowe uruchamianie tego procesu lub mogą rozpocząć go, jeśli zajdzie taka potrzeba.

Ten program obsługi (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) jest automatycznie instalowany na wszystkich serwerach Frontonu programu SharePoint i serwery aplikacji, po włączeniu SPZ. Program jest zainstalowany w następującej lokalizacji: *dysk rozruchowy*: 10.50\Maintainer\ zdalnego magazynu obiektów Blob SQL \Program Files\Microsoft

Aby uzyskać informacji na temat konfigurowania i używania programu obsługi, zobacz [Obsługa SPZ w programie SharePoint Server 2013][8].

> [!IMPORTANT]
> Program Element utrzymujący SPZ jest dużej ilości zasobów. Należy zaplanować jego uruchomienie tylko podczas okresów nasilonej aktywności światła w farmie programu SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Natychmiast usunąć oddzielonych obiektów blob
Jeśli musisz natychmiast usunąć oddzielonych obiektów blob, można użyć zgodnie z poniższymi instrukcjami. Należy zwrócić uwagę na to, że te instrukcje są przykładem przedstawiającym sposób można to zrobić w środowisku programu SharePoint 2013 za pomocą następujących składników:

* Nazwa bazy danych zawartości jest WSS_Content.
* Nazwa serwera SQL jest SHRPT13 SQL12\SHRPT13.
* Nazwa aplikacji sieci web jest SharePoint — 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Uaktualnić lub ponownie zainstalować Adapter usługi StorSimple dla programu SharePoint
Użyj poniższej procedury uaktualniania serwera programu SharePoint, a następnie ponownie zainstalować Adapter usługi StorSimple dla programu SharePoint lub aby po prostu uaktualnić lub ponownie zainstalować adapter w istniejącej farmy serwerów programu SharePoint.

> [!IMPORTANT]
> Przed przystąpieniem do uaktualniania oprogramowania SharePoint i/lub uaktualnienia lub ponownej instalacji adaptera StorSimple dla programu SharePoint, przejrzyj następujące informacje:
> 
> * Wszystkie pliki, które wcześniej zostały przeniesione do magazynu zewnętrznego za pośrednictwem SPZ nie będą dostępne do czasu ponownej instalacji zostanie zakończony i SPZ jest aktywna, ponownie. Aby ograniczyć wpływu na użytkowników, wykonaj uaktualnienie ani ponowna instalacja podczas okna zaplanowanej konserwacji.
> * Czas wymagany do uaktualnienia/ponowna instalacja może się różnić w zależności od całkowitej liczby baz danych programu SharePoint w farmie serwerów programu SharePoint.
> * Po zakończeniu uaktualniania/ponownej instalacji należy włączyć SPZ dla bazy danych zawartości. Zobacz [skonfigurować SPZ](#configure-rbs) Aby uzyskać więcej informacji.
> * Jeśli konfigurujesz SPZ dla farmy programu SharePoint, która ma bardzo dużej liczby baz danych (większe niż 200), **administracji centralnej programu SharePoint** strony może upłynąć limit czasu. Jeśli ma to miejsce, należy odświeżyć stronę. Nie wpływa to na proces konfiguracji.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Adapter usługi StorSimple do usunięcia programu SharePoint
W poniższych procedurach opisano jak przenieść obiekty BLOB z powrotem do bazy danych zawartości programu SQL Server, a następnie odinstaluj adaptera StorSimple dla programu SharePoint. 

> [!IMPORTANT]
> Masz wróć do obiektów blob do bazy danych zawartości przed odinstalowaniem oprogramowanie adaptera.


### <a name="before-you-begin"></a>Przed rozpoczęciem
Zbierz następujące informacje, aby przenieść dane z powrotem do bazy danych zawartości programu SQL Server i rozpocząć proces usuwania karty:

* Nazwy wszystkich baz danych dla których włączono SPZ
* Ścieżka UNC skonfigurowanego magazynu obiektów BLOB

### <a name="move-the-blobs-back-to-the-content-databases"></a>Przenieść obiekty BLOB z powrotem do bazy danych zawartości
Przed odinstalowaniem adaptera StorSimple dla oprogramowania SharePoint, należy przeprowadzić migrację wszystkich obiektów blob, które zostały zewnętrznych do bazy danych zawartości programu SQL Server. Jeśli użytkownik podejmie próbę odinstalowania adaptera StorSimple dla programu SharePoint, zanim przeniesieniu wszystkich obiektów blob z powrotem do bazy danych zawartości, zobaczą następujący komunikat ostrzegawczy.

![Komunikat ostrzeżenia](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Aby przenieść z powrotem do bazy danych zawartości obiektów blob
1. Pobierz, każdy z obiektów zewnętrznych.
2. Otwórz **administracji centralnej programu SharePoint** strony, a następnie przejdź do **ustawień systemowych**.
3. W obszarze **Azure StorSimple**, kliknij przycisk **skonfigurować Adapter usługi StorSimple**.
4. Na **skonfigurować Adapter usługi StorSimple** kliknij **wyłączyć** znajdujący się poniżej każdej z baz danych zawartości, które chcesz usunąć z zewnętrznej usługi storage BLOB. 
5. Usuwanie obiektów z programu SharePoint, a następnie przekaż je ponownie.

Alternatywnie, można użyć programu Microsoft `RBS Migrate()` polecenia cmdlet programu PowerShell, dołączone do programu SharePoint. Aby uzyskać więcej informacji, zobacz [migracji zawartości do lub z SPZ](https://technet.microsoft.com/library/ff628255.aspx).

Po przeniesieniu obiektów blob z powrotem do bazy danych zawartości, przejdź do następnego kroku: [Odinstalowywanie karty](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Odinstalowywanie karty
Po przeniesieniu obiektów blob z powrotem do bazy danych zawartości programu SQL Server, użyj jednej z następujących opcji do odinstalowania adaptera StorSimple dla programu SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Odinstalowywanie karty przy użyciu programu instalacyjnego
1. Użyj konta z uprawnieniami administratora do logowania się na serwerze sieci web (WFE) frontonu.
2. Kliknij dwukrotnie adaptera StorSimple dla Instalatora programu SharePoint. Zostanie uruchomiony Kreator instalacji.
   
    ![Kreator instalacji](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Kliknij przycisk **Dalej**. Zostanie wyświetlona następująca strona.
   
    ![Strona usuwanie Kreatora instalacji](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Kliknij przycisk **Usuń** wybrać proces usuwania. Zostanie wyświetlona następująca strona.
   
    ![Strona potwierdzenia Kreatora instalacji](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Kliknij przycisk **Usuń** aby potwierdzić usunięcie. Zostanie wyświetlona następująca strona postępu.
   
    ![Na stronie Postęp instalacji Kreator](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Po zakończeniu usuwania zostanie wyświetlona strona Zakończ. Kliknij przycisk **Zakończ** aby zamknąć kreatora instalacji.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Aby odinstalować karty za pomocą Panelu sterowania
1. Otwórz Panel sterowania, a następnie kliknij przycisk **programy i funkcje**.
2. Wybierz **adaptera StorSimple dla programu SharePoint**, a następnie kliknij przycisk **Odinstaluj**.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat rozwiązania StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
