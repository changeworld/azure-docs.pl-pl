---
title: Instalowanie adaptera StorSimple dla programu SharePoint | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zainstalować i skonfigurować lub usunąć kartę StorSimple dla programu SharePoint w farmie serwerów programu SharePoint.
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
ms.openlocfilehash: 2e1b231a5cf13d2655ff66c7e48752729c580f48
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalowanie i konfigurowanie karty StorSimple dla programu SharePoint
## <a name="overview"></a>Przegląd
Karta StorSimple dla programu SharePoint jest składnikiem, który umożliwia elastyczne magazynu Microsoft Azure StorSimple i ochronę danych na farmach serwerów programu SharePoint. Karta umożliwia przenieść zawartość dużych obiektu binarnego (BLOB) z bazy danych zawartości programu SQL Server do urządzenia magazynującego chmury hybrydowej Microsoft Azure StorSimple.

Karta StorSimple dla programu SharePoint działa jako dostawca magazynu obiektów BLOB zdalnego (SPZ) i korzysta z funkcji zdalnego magazynu obiektów BLOB SQL Server do przechowywania danych zawartości programu SharePoint (w formie obiektów blob) na serwerze plików, który nie jest obsługiwana przez urządzenia StorSimple.

> [!NOTE]
> Karta StorSimple dla programu SharePoint obsługuje SharePoint Server 2010 zdalnego obiektu BLOB magazynu (SPZ). Nie obsługuje programu SharePoint Server 2010 zewnętrznego obiektu BLOB magazynu (EBS).


* Aby pobrać karty StorSimple dla programu SharePoint, przejdź do [karty StorSimple dla programu SharePoint] [ 1] w programie Microsoft Download Center.
* Aby uzyskać informacje dotyczące planowania SPZ i SPZ ograniczenia, przejdź do tematu [podejmowaniu decyzji o użyciu SPZ w SharePoint 2013] [ 2] lub [planowanie SPZ (SharePoint Server 2010)] [ 3].

Pozostała część tego omówienie krótko opisano rolę karty StorSimple dla programu SharePoint i SharePoint pojemność i wydajność granice, które należy zwrócić uwagę przed zainstalowaniem i skonfigurowaniem karty. Po przejrzeniu tych informacji, przejdź do [kartę StorSimple dla instalacji programu SharePoint](#storsimple-adapter-for-sharepoint-installation) aby rozpocząć konfigurowanie karty.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Karta StorSimple korzyści programu SharePoint
W witrynie programu SharePoint zawartość jest przechowywana jako bez struktury danych obiektów BLOB w jedną lub więcej baz danych zawartości. Domyślnie te bazy danych są obsługiwane na komputerach z programem SQL Server, które znajdują się w farmie serwerów programu SharePoint. Obiekty BLOB można szybko wzrost rozmiaru zużywa duże ilości magazynu lokalnego. Z tego powodu można znaleźć mniej kosztowne, pamięci masowej. Program SQL Server stanowi mechanizmu zdalnego obiektu Blob magazynu (SPZ), która umożliwia przechowywanie zawartości obiektu BLOB w systemie plików, poza bazy danych programu SQL Server. SPZ obiekty BLOB może znajdować się w systemie plików na komputerze, na którym działa program SQL Server, lub mogą być przechowywane w systemie plików na innym serwerze.

SPZ wymaga używania dostawcy SPZ, takie jak karta StorSimple dla programu SharePoint, aby włączyć SPZ w programie SharePoint. Karta StorSimple dla programu SharePoint działa z SPZ, dzięki czemu można przenieść obiekty BLOB do serwera kopii zapasowej w systemie Microsoft Azure StorSimple. Microsoft Azure StorSimple następnie przechowuje dane obiektów BLOB lokalnie lub w chmurze, na podstawie użycia. Obiekty BLOB, które są bardzo aktywny (zazwyczaj zwane warstwy 1 lub gorących danych) znajdują się lokalnie. Mniej aktywne dane i dane archiwalne znajdują się w chmurze. Po włączeniu SPZ na bazę danych zawartości, zawartość obiektu BLOB utworzone w programie SharePoint są przechowywane na urządzeniu StorSimple, a nie w bazie danych zawartości.

Microsoft Azure StorSimple stosowania SPZ zapewnia następujące korzyści:

* Dzięki przenoszenia zawartości obiektu BLOB na osobnym serwerze można zmniejszyć obciążenie zapytania programu SQL Server, co poprawia czas odpowiedzi serwera SQL. 
* Azure StorSimple używa deduplikacji i ich kompresji, aby zredukować rozmiar danych.
* Azure StorSimple zapewnia ochronę danych w formie lokalne i migawki w chmurze. Ponadto jeśli sama baza danych na urządzeniu StorSimple, można tworzyć kopie zapasowe bazy danych zawartości i obiekty BLOB ze sobą w awaria spójny sposób. (Przenoszenie bazy danych zawartości na urządzeniu jest obsługiwana tylko dla urządzenia serii StorSimple 8000. Ta funkcja nie jest obsługiwana dla serii 5000 i 7000.)
* Azure StorSimple obejmuje funkcje odzyskiwania po awarii, w tym tryb failover, odzyskiwanie plików i woluminów (w tym testowe odzyskiwanie) i szybkie przywrócenie danych.
* Oprogramowanie odzyskiwania danych, takich jak Kroll Ontrack PowerControls, służy z migawkami StorSimple danych obiektów BLOB, aby przeprowadzić odzyskiwanie na poziomie elementu zawartości programu SharePoint. (To oprogramowanie odzyskiwania danych jest oddzielnego zakupu).
* Karta StorSimple dla programu SharePoint podłącza się do portalu Administracja centralna programu SharePoint, co umożliwia zarządzanie całego rozwiązania programu SharePoint z centralnej lokalizacji.

Przenoszenie zawartości obiektu BLOB w systemie plików może zapewnić innych kosztach i korzyści. Na przykład przy użyciu SPZ można ograniczyć potrzebę tańsze magazynowanie warstwy 1 i, ponieważ jej zmniejsza bazę danych zawartości, SPZ może zmniejszyć liczbę baz danych wymagane do farmy programu SharePoint server. Jednak inne czynniki, takie jak limity rozmiaru bazy danych oraz ilości zawartości z systemem innym niż SPZ, może również wpływać na wymagania dotyczące magazynu. Aby uzyskać więcej informacji na temat kosztów i korzyści wynikające ze stosowania SPZ zobacz [planowanie SPZ (SharePoint Foundation 2010)] [ 4] i [podejmowaniu decyzji o użyciu SPZ w SharePoint 2013] [5].

### <a name="capacity-and-performance-limits"></a>Limity pojemności i wydajności
Przed uznaniem przy użyciu SPZ rozwiązania programu SharePoint, należy zwrócić uwagę wydajności przetestowane i limity pojemności programu SharePoint Server 2010 i SharePoint Server 2013 oraz jak te limity dotyczą akceptowalną wydajność. Aby uzyskać więcej informacji, zobacz [granic oprogramowania i limity dla programu SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Przejrzyj następujący kod, aby skonfigurować SPZ:

* Upewnij się, że łączny rozmiar zawartości (rozmiar bazy danych zawartości) oraz rozmiar wszystkie skojarzone obiekty BLOB externalized nie przekracza limit rozmiaru SPZ obsługiwane przez program SharePoint. To ograniczenie jest 200 GB. 
  
    **Baza danych zawartości miary i rozmiar obiektu BLOB**
  
  1. Uruchom to zapytanie na centralnej WFE administracji. Uruchom powłokę zarządzania programu SharePoint, a następnie wprowadź następujące polecenie programu Windows PowerShell, aby uzyskać rozmiaru bazy danych zawartości:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Ten krok pobiera rozmiar bazy danych zawartości na dysku.
  2. Uruchom następujące kwerendy SQL w programie SQL Management Studio w oknie programu SQL server w każdej bazie danych zawartości i Dodaj wynik do numer uzyskany w kroku 1.
     
     W bazach danych zawartości programu SharePoint 2013 wprowadź:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     W bazach danych zawartości programu SharePoint 2010 wprowadź:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Ten krok pobiera rozmiar obiektów blob, które zostały externalized.
* Firma Microsoft zaleca przechowywania wszystkich obiektów BLOB i bazy danych zawartości lokalnie na urządzeniu StorSimple. Urządzenie StorSimple, jest dwa węzły klastra w celu zapewnienia wysokiej dostępności. Umieszczenie bazy danych zawartości i obiekty BLOB na urządzeniu StorSimple zapewnia wysoką dostępność.
  
    Aby przenieść bazę danych zawartości na urządzeniu StorSimple, należy stosować tradycyjnych programu SQL Server migracji najlepsze rozwiązania. Przenoszenie bazy danych tylko wtedy, gdy cała zawartość obiektu BLOB z bazy danych została przeniesiona do udziału plików za pośrednictwem SPZ. Jeśli wybierzesz przenieść bazę danych zawartości na urządzeniu StorSimple, zaleca się Konfigurowanie magazynu bazy danych zawartości na urządzeniu jako podstawowy wolumin.
* W programie Microsoft Azure StorSimple, jeśli przy użyciu woluminów warstwowych nie ma ma gwarancji tej zawartości lokalnie przechowywane na StorSimple, urządzenie nie będzie można warstwy do magazynu w chmurze Microsoft Azure. W związku z tym zaleca się korzystanie z woluminów StorSimple przypięty lokalnie w połączeniu z SPZ programu SharePoint. To zapewnia, że cała zawartość obiektu BLOB pozostaje lokalnie na urządzeniu StorSimple i nie jest przenoszony do systemu Microsoft Azure.
* Jeśli bazy danych zawartości nie są przechowywane na urządzeniu StorSimple, stosować tradycyjnych programu SQL Server wysokiej dostępności najlepsze rozwiązania obsługujące SPZ. Klaster programu SQL Server obsługuje SPZ, podczas SQL Server dublowanie nie. 

> [!WARNING]
> Jeśli nie włączono SPZ, nie zaleca się przenoszenie bazy danych zawartości na urządzeniu StorSimple. To jest Konfiguracja zastosowaniem.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Karta StorSimple dla instalacji programu SharePoint
Przed zainstalowaniem karty StorSimple dla programu SharePoint, należy skonfigurować urządzenie StorSimple i upewnij się, że farmy programu SharePoint server i wystąpienia programu SQL Server spełniają wszystkie wymagania wstępne. W tym samouczku opisano wymagania dotyczące konfiguracji, a także procedury dotyczące instalowania i uaktualniania karty StorSimple dla programu SharePoint.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych
Przed zainstalowaniem karty StorSimple dla programu SharePoint, upewnij się, że urządzenia StorSimple, farmy programu SharePoint server i wystąpienia serwera SQL spełniają następujące wymagania wstępne.

### <a name="system-requirements"></a>Wymagania systemowe
Karta StorSimple dla programu SharePoint działa z następujący sprzęt i oprogramowanie:

* Obsługiwany system operacyjny — Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2012 lub Windows Server 2012 R2
* Obsługiwane wersje programu SharePoint — SharePoint Server 2010 lub SharePoint Server 2013
* Obsługiwane wersje programu SQL Server — SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition lub SQL Server 2012 Enterprise Edition
* Obsługiwane urządzenia StorSimple — z serii StorSimple 8000, StorSimple 7000 serii lub z serii StorSimple 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji urządzenia StorSimple
Urządzenia StorSimple jest urządzeniem bloku i jako taki wymaga serwera plików, na którym dane mogą być hostowane. Zalecane jest użycie osobnego serwera, a nie z istniejącego serwera z farmy programu SharePoint. Ten serwer plików musi być w tej samej sieci (LAN), komputer serwera SQL, który jest hostem bazy danych zawartości.

> [!TIP]
> * Jeśli możesz skonfigurować farmę programu SharePoint w celu zapewnienia wysokiej dostępności, należy wdrożyć także serwera plików w celu zapewnienia wysokiej dostępności.
> * Jeśli baza danych zawartości nie są przechowywane na urządzeniu StorSimple, użyj tradycyjnych wysokiej dostępności najlepsze rozwiązania, które obsługują SPZ. Klaster programu SQL Server obsługuje SPZ, podczas SQL Server dublowanie nie. 


Upewnij się, że urządzenia StorSimple jest poprawnie skonfigurowany i czy odpowiednich woluminów do obsługi wdrożenia programu SharePoint są skonfigurowane i dostępny z komputera programu SQL Server. Przejdź do [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md) Jeśli nie zostały jeszcze wdrożone i skonfigurowane na urządzeniu StorSimple. Zanotuj adres IP urządzenia StorSimple; będą one potrzebne podczas karty StorSimple dla instalacji programu SharePoint.

Ponadto upewnij się, że wolumin do użycia dla obiekt BLOB externalization spełnia następujące wymagania:

* Musi być sformatowany woluminu o rozmiarze jednostki alokacji 64 KB.
* Twoje frontonu sieci web (WFE) i serwerów aplikacji musi mieć możliwość dostępu do woluminu za pomocą ścieżki Universal Naming Convention (UNC).
* Farmy serwerów programu SharePoint musi być skonfigurowana do zapisu w woluminie.

> [!NOTE]
> Po zainstalowaniu i skonfigurowaniu karty, wszystkie externalization obiektu BLOB musi przechodzić przez urządzenia StorSimple (urządzenia będą przekazujących woluminy do programu SQL Server i zarządzać nimi warstw magazynowania). Nie można użyć innych celów externalization obiektu BLOB.


Jeśli planujesz użycie StorSimple Snapshot Manager w celu przejęcia migawki danych obiektów BLOB i bazy danych, należy zainstalować StorSimple Snapshot Manager na serwerze bazy danych, dzięki czemu można użyć do wdrożenia systemu Windows woluminów w tle kopii Service (VSS) Składnik zapisywania usługi SQL.

> [!IMPORTANT]
> StorSimple Snapshot Manager nie obsługuje składnik zapisywania usługi VSS programu SharePoint i nie można wykonać spójnych z aplikacją migawki danych programu SharePoint. W przypadku programu SharePoint StorSimple Snapshot Manager zapewnia tylko spójna w razie awarii kopie zapasowe.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji farmy programu SharePoint
Upewnij się, że farmę programu SharePoint server został poprawnie skonfigurowany, w następujący sposób:

* Sprawdź, czy farmy serwerów programu SharePoint jest w dobrej kondycji i sprawdź następujące kwestie:
* Wszystkie WFE programu SharePoint i zarejestrowanych w farmie serwerów aplikacji są uruchomione i reaguje na operację ping z serwera, na którym będzie instalowany karty StorSimple dla programu SharePoint.
* Usługa czasomierza programu SharePoint (SPTimerV3 lub SPTimerV4) działa na każdym serwerze WFE i aplikacji.
* Zarówno usługa czasomierza programu SharePoint i pula aplikacji usług IIS, na którym uruchomiono witryny Administracja centralna programu SharePoint należy mieć uprawnienia administratora.
* Upewnij się, że kontekst zabezpieczeń rozszerzone Eksploratora Internet (nazywana konfiguracją IE ESC) jest wyłączona. Wykonaj następujące kroki, aby wyłączyć konfigurację IE ESC:
  
  1. Zamknij wszystkie wystąpienia programu Internet Explorer.
  2. Uruchom Menedżera serwera.
  3. W okienku po lewej stronie kliknij **lokalnego serwera**.
  4. W okienku po prawej stronie obok pozycji **Konfiguracja zwiększonych zabezpieczeń**, kliknij przycisk **na**.
  5. W obszarze **Administratorzy**, kliknij przycisk **poza**.
  6. Kliknij przycisk **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Zdalnego magazynu obiektów BLOB (SPZ) wymagania wstępne
Upewnij się, że używasz obsługiwanej wersji programu SQL Server. Tylko następujące wersje są obsługiwane i mogli używać SPZ:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Obiekty BLOB można externalized tylko tych woluminach, które urządzenia StorSimple przedstawia do programu SQL Server. Nie innych elementów docelowych dla obiekt BLOB externalization są obsługiwane.

Po zakończeniu wszystkich kroków konfiguracji wymagań wstępnych, przejdź do [zainstalować StorSimple Adapter dla programu SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalowanie adaptera StorSimple dla programu SharePoint
Wykonaj następujące kroki, aby zainstalować StorSimple Adapter dla programu SharePoint. Jeśli instalujesz oprogramowanie, zobacz [uaktualnić lub ponownie zainstaluj karty StorSimple dla programu SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Czas wymagany do instalacji zależy od całkowitej liczby baz danych programu SharePoint w farmie serwerów programu SharePoint.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Skonfiguruj SPZ
Po zainstalowaniu karty StorSimple dla programu SharePoint, należy skonfigurować SPZ zgodnie z opisem w poniższej procedurze.

> [!TIP]
> Karta StorSimple dla programu SharePoint podłącza się do strony Administracja centralna programu SharePoint, dzięki czemu SPZ włączone lub wyłączone w każdej bazie danych zawartości do farmy programu SharePoint. Jednak włączenie lub wyłączenie SPZ na bazę danych zawartości powoduje resetowanie usług IIS, która w zależności od konfiguracji farmy, na chwilę może zakłócać dostępności SharePoint frontonu sieci web (WFE). (Czynników, takich jak korzystanie z usługi równoważenia obciążenia frontonu, bieżące obciążenie serwera i tak dalej, można ograniczyć lub wyeliminować ten przerw w działaniu.) Aby chronić użytkowników przed zakłócenia, zaleca się, że można włączyć lub wyłączyć SPZ tylko podczas okna zaplanowanej konserwacji.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Skonfiguruj wyrzucanie elementów bezużytecznych
Usunięcie obiektów z witryny programu SharePoint nie automatycznie są usuwane z woluminu magazynu SPZ. Zamiast tego asynchroniczna, program obsługi tła usuwania obiektów blob oddzielone z magazynu plików. Administratorzy systemu mogą zaplanować okresowo uruchomienia tego procesu lub można ją w razie konieczności uruchamiania.

Ten program obsługi (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) jest automatycznie instalowany na wszystkich serwerów WFE programu SharePoint i serwerów aplikacji, po włączeniu SPZ. Program jest zainstalowany w następującej lokalizacji: *dysk rozruchowy*: \Program Files\Microsoft 10.50\Maintainer\ zdalnego magazynu obiektów Blob SQL

Aby uzyskać informacje o konfigurowaniu i używaniu programu obsługi, zobacz [Obsługa SPZ w SharePoint Server 2013][8].

> [!IMPORTANT]
> Element utrzymujący SPZ programu jest ilości zasobów. Należy zaplanować jego uruchomienie tylko podczas okresów niewielka aktywność na farmie programu SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Natychmiast usunąć osierocone obiekty BLOB
Jeśli chcesz natychmiast usunąć osierocone obiekty BLOB, można użyć poniższych instrukcji. Należy pamiętać, że te instrukcje są przykładem jak to zrobić w środowisku programu SharePoint 2013 z następujących składników:

* Nazwa bazy danych zawartości jest WSS_Content.
* Nazwa serwera SQL jest SHRPT13 SQL12\SHRPT13.
* Nazwa aplikacji sieci web jest program SharePoint — 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Uaktualnić lub ponownie zainstaluj karty StorSimple dla programu SharePoint
Umożliwia poniższą procedurę uaktualniania programu SharePoint server i ponowne zainstalowanie karty StorSimple dla programu SharePoint lub po prostu uaktualnić lub ponownie zainstaluj karty w istniejącej farmy serwerów programu SharePoint.

> [!IMPORTANT]
> Przed przystąpieniem do uaktualniania oprogramowania SharePoint i/lub uaktualnienia lub ponownego zainstalowania karty StorSimple dla programu SharePoint, przejrzyj następujące informacje:
> 
> * Wszystkie pliki, które wcześniej zostały przeniesione do magazynu zewnętrznego za pośrednictwem SPZ nie będą dostępne dopiero po zakończeniu ponowną instalację i ponownie włączono funkcję SPZ. Aby ograniczyć wpływ użytkownika, należy wykonać wszelkie uaktualnienia lub ponownej instalacji w oknie zaplanowanej konserwacji.
> * Czas wymagany do uaktualnienia/ponownej instalacji jest zależna od całkowita liczba baz danych programu SharePoint w farmie serwerów programu SharePoint.
> * Po zakończeniu uaktualniania/ponowną instalację należy włączyć SPZ dla bazy danych zawartości. Zobacz [skonfigurować SPZ](#configure-rbs) Aby uzyskać więcej informacji.
> * Jeśli konfigurujesz SPZ dla farmy programu SharePoint, który jest bardzo duża liczba baz danych (większe niż 200), **Administracja centralna programu SharePoint** strony może upłynął limit czasu. Jeśli to miejsce, Odśwież stronę. Nie dotyczy to proces konfiguracji.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Karta StorSimple do usunięcia programu SharePoint
Poniższe procedury opisują jak przenieść obiekty BLOB z powrotem do bazy danych zawartości programu SQL Server, a następnie odinstaluj karty StorSimple dla programu SharePoint. 

> [!IMPORTANT]
> Trzeba przenieść obiekty BLOB powrotem do bazy danych zawartości przed odinstalowaniem oprogramowania karty.


### <a name="before-you-begin"></a>Przed rozpoczęciem
Zbierz następujące informacje, aby przenieść dane z powrotem do bazy danych zawartości programu SQL Server i rozpocząć proces usuwania karty:

* Nazwy wszystkich baz danych, dla których włączono SPZ
* Ścieżka UNC skonfigurowanego magazynu obiektów BLOB

### <a name="move-the-blobs-back-to-the-content-databases"></a>Przenieść obiekty BLOB z powrotem do bazy danych zawartości
Przed odinstalowaniem karty StorSimple dla oprogramowania SharePoint, należy przeprowadzić migrację wszystkich obiektów blob, które zostały externalized do bazy danych zawartości programu SQL Server. Jeśli odinstalowanie karty StorSimple dla programu SharePoint, aby przeniesieniu wszystkich obiektów blob z powrotem do bazy danych zawartości, pojawi się następujący komunikat ostrzegawczy.

![Komunikat ostrzeżenia](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Aby przenieść obiekty BLOB z powrotem do bazy danych zawartości
1. Każdy z obiektów externalized Pobierz.
2. Otwórz **Administracja centralna programu SharePoint** strony, a następnie przejdź do **ustawienia systemu**.
3. W obszarze **Azure StorSimple**, kliknij przycisk **skonfigurować karty StorSimple**.
4. Na **skonfigurować karty StorSimple** kliknij przycisk **wyłączyć** znajdujący się poniżej każdej z baz danych zawartości, które chcesz usunąć z zewnętrznego magazynu obiektów BLOB. 
5. Usuń obiekty z programu SharePoint, a następnie przekazać je ponownie.

Alternatywnie można użyć programu Microsoft` RBS Migrate()` polecenia cmdlet programu PowerShell dołączony do programu SharePoint. Aby uzyskać więcej informacji, zobacz [migrowania zawartości do lub wychodzący SPZ](https://technet.microsoft.com/library/ff628255.aspx).

Po przeniesieniu obiektów blob z powrotem do bazy danych zawartości, przejdź do następnego kroku: [odinstalowanie karty](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Odinstalowanie karty
Po przeniesieniu obiektów blob z powrotem do bazy danych zawartości programu SQL Server, użyj jednej z następujących opcji odinstalować karty StorSimple dla programu SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Na potrzeby programu instalacyjnego odinstalowanie karty
1. Użyj konta z uprawnieniami administratora do logowania się do serwera sieci web (WFE) frontonu.
2. Instalator programu SharePoint, kliknij dwukrotnie kartę StorSimple. Uruchamia Kreatora instalacji.
   
    ![Kreator instalacji](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Kliknij przycisk **Dalej**. Jest wyświetlana następująca strona.
   
    ![Usuń Kreatora instalacji](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Kliknij przycisk **Usuń** wybierz proces usuwania. Jest wyświetlana następująca strona.
   
    ![Strona potwierdzenia Kreatora instalacji](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Kliknij przycisk **Usuń** aby potwierdzić usunięcie. Jest wyświetlana następująca strona postępu.
   
    ![Strona postępu Kreatora konfiguracji](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Po zakończeniu usuwania zostanie wyświetlona strona Zakończ. Kliknij przycisk **Zakończ** aby zamknąć kreatora instalacji.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Odinstalowanie karty przy użyciu Panelu sterowania
1. Otwórz Panel sterowania, a następnie kliknij przycisk **programy i funkcje**.
2. Wybierz **karty StorSimple dla programu SharePoint**, a następnie kliknij przycisk **Odinstaluj**.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
