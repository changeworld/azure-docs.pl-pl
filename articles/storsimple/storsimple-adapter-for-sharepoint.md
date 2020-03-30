---
title: Instalowanie karty StorSimple dla programu SharePoint | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób instalowania i konfigurowania lub usuwania karty StorSimple dla programu SharePoint w farmie serwerów programu SharePoint.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: twooley
ms.openlocfilehash: a841ce8b664389ccd8fdf55de9965f09412fecf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930223"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalowanie i konfigurowanie karty StorSimple dla programu SharePoint
## <a name="overview"></a>Omówienie
Karta StorSimple dla programu SharePoint jest składnikiem, który umożliwia zapewnienie elastycznej pamięci masowej i ochrony danych usługi Microsoft Azure StorSimple farmom serwerów sharepoint. Za pomocą karty można przenieść zawartość binary large object (BLOB) z baz danych zawartości programu SQL Server do urządzenia magazynującego w chmurze hybrydowej Microsoft Azure StorSimple.

Karta StorSimple dla programu SharePoint działa jako dostawca zdalnego magazynu obiektów BLOB (RBS) i używa funkcji zdalnego magazynu obiektów BLOB programu SQL Server do przechowywania zawartości programu SharePoint bez struktury (w postaci bloków BLOB) na serwerze plików, który jest wspierany przez urządzenie StorSimple.

> [!NOTE]
> Karta StorSimple dla programu SharePoint obsługuje zdalny magazyn obiektów BLOB programu SharePoint Server 2010 (RBS). Nie obsługuje zewnętrznego magazynu obiektów BLOB programu SharePoint Server 2010 (EBS).


* Aby pobrać kartę StorSimple dla programu SharePoint, przejdź do [programu StorSimple Adapter for SharePoint][1] w Centrum pobierania Microsoft.
* Aby uzyskać informacje na temat planowania ograniczeń SPZ i SPZ, przejdź [do tematu Podejmowanie decyzji o użyciu SPZ w programie SharePoint 2013][2] lub [Plan dla SPZ (SharePoint Server 2010).][3]

W dalszej części tego przeglądu opisano pokrótce rolę karty StorSimple dla programu SharePoint oraz limity pojemności i wydajności programu SharePoint, o których należy pamiętać przed zainstalowaniem i skonfigurowaniem karty. Po zapoznaniu się z tymi informacjami przejdź do [instalacji karty StorSimple dla programu SharePoint,](#storsimple-adapter-for-sharepoint-installation) aby rozpocząć konfigurowanie karty.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Karta StorSimple dla korzyści programu SharePoint
W witrynie programu SharePoint zawartość jest przechowywana jako nieustrukturyzowane dane obiektu BLOB w co najmniej jednej bazie danych zawartości. Domyślnie te bazy danych są hostowane na komputerach z systemem SQL Server i znajdują się w farmie serwerów programu SharePoint. Bloków BLOB można szybko zwiększyć rozmiar, zużywając duże ilości magazynu lokalnego. Z tego powodu warto znaleźć inne, tańsze rozwiązanie pamięci masowej. SQL Server zapewnia technologię o nazwie Remote Blob Storage (RBS), która umożliwia przechowywanie zawartości obiektu BLOB w systemie plików, poza bazą danych programu SQL Server. W sprozs obiekty BLOBs mogą znajdować się w systemie plików na komputerze z uruchomionym programem SQL Server lub mogą być przechowywane w systemie plików na innym komputerze serwera.

SPZ wymaga użycia dostawcy SPZ, takiego jak Karta StorSimple dla programu SharePoint, do włączania SPZ w programie SharePoint. Karta StorSimple dla programu SharePoint współpracuje z spzem, umożliwiając przenoszenie bloków BLOB na serwer kopii zapasowej przez system Microsoft Azure StorSimple. Usługa Microsoft Azure StorSimple następnie przechowuje dane obiektu BLOB lokalnie lub w chmurze na podstawie użycia. Obiekty BLOB, które są bardzo aktywne (zwykle określane jako warstwy 1 lub gorące dane) znajdują się lokalnie. Mniej aktywne dane i dane archiwalne znajdują się w chmurze. Po włączeniu SPZ w bazie danych zawartości każda nowa zawartość obiektu BLOB utworzona w programie SharePoint jest przechowywana na urządzeniu StorSimple, a nie w bazie danych zawartości.

Implementacja spz. spz.

* Przenosząc zawartość obiektu BLOB na oddzielny serwer, można zmniejszyć obciążenie zapytania na programie SQL Server, co może poprawić szybkość reakcji programu SQL Server. 
* Usługa Azure StorSimple używa deduplikacji i kompresji w celu zmniejszenia rozmiaru danych.
* Usługa Azure StorSimple zapewnia ochronę danych w postaci migawek lokalnych i w chmurze. Ponadto jeśli umieścisz samą bazę danych na urządzeniu StorSimple, można wywrzeć wspólną zawartość bazy danych i bloków BLOB razem w sposób spójny z awarią. (Przenoszenie bazy danych zawartości do urządzenia jest obsługiwane tylko dla urządzenia z serii StorSimple 8000. Ta funkcja nie jest obsługiwana w przypadku serii 5000 lub 7000).)
* Usługa Azure StorSimple zawiera funkcje odzyskiwania po awarii, w tym tryb failover, odzyskiwanie plików i woluminów (w tym odzyskiwanie testowe) i szybkie przywracanie danych.
* Za pomocą oprogramowania do odzyskiwania danych, takiego jak Kroll Ontrack PowerControls, można używać migawek danych blob storsimple do odzyskiwania zawartości programu SharePoint na poziomie elementu. (To oprogramowanie do odzyskiwania danych jest osobnym zakupem).
* Karta StorSimple dla programu SharePoint jest podłączona do portalu administracji centralnej programu SharePoint, umożliwiając zarządzanie całym rozwiązaniem programu SharePoint z centralnej lokalizacji.

Przenoszenie zawartości obiektu BLOB do systemu plików może zapewnić inne oszczędności i korzyści. Na przykład przy użyciu SPZ może zmniejszyć zapotrzebowanie na kosztowne tier 1 magazynu i, ponieważ zmniejsza bazy danych zawartości, SPZ można zmniejszyć liczbę baz danych wymaganych w farmie serwerów programu SharePoint. Jednak inne czynniki, takie jak limity rozmiaru bazy danych i ilość zawartości innych niż RBS, mogą również wpływać na wymagania dotyczące magazynu. Aby uzyskać więcej informacji na temat kosztów i korzyści związanych z korzystaniem z SPZ, zobacz [Plan dla SPZ (SharePoint Foundation 2010)][4] i [Decydując się na użycie SPZ w programie SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Limity pojemności i wydajności
Przed rozważeniem użycia SPZ w rozwiązaniu programu SharePoint należy zapoznać się z testowanymi limitami wydajności i pojemności programów SharePoint Server 2010 i SharePoint Server 2013 oraz jak te limity odnoszą się do dopuszczalnej wydajności. Aby uzyskać więcej informacji, zobacz [Granice i limity oprogramowania dla programu SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Przed skonfigurowaniem SPZ zapoznaj się z poniższymi opiniami:

* Upewnij się, że całkowity rozmiar zawartości (rozmiar bazy danych zawartości plus rozmiar wszystkich skojarzonych zewnętrznych bloków BLOB) nie przekracza limitu rozmiaru SPZ obsługiwanego przez program SharePoint. Ten limit wynosi 200 GB. 
  
    **Aby zmierzyć rozmiar bazy danych zawartości i obiektu BLOB**
  
  1. Uruchom tę kwerendę w administracji centralnej WFE. Uruchom powłokę zarządzania programem SharePoint, a następnie wprowadź następujące polecenie Programu Windows PowerShell, aby uzyskać rozmiar baz danych zawartości:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Ten krok pobiera rozmiar bazy danych zawartości na dysku.
  2. Uruchom jeden z następujących zapytań SQL w programie SQL Management Studio w polu serwera SQL w każdej bazie danych zawartości i dodaj wynik do liczby uzyskanej w kroku 1.
     
     W bazach danych zawartości programu SharePoint 2013 wprowadź:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     W bazach danych zawartości programu SharePoint 2010 wprowadź:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Ten krok pobiera rozmiar bloków BLOB, które zostały eksternalizowane.
* Zaleca się przechowywanie wszystkich obiektów BLOB i zawartości bazy danych lokalnie na urządzeniu StorSimple. Urządzenie StorSimple jest klastrem dwuwęzłowym o wysokiej dostępności. Umieszczenie baz danych zawartości i obiektów BLOB na urządzeniu StorSimple zapewnia wysoką dostępność.
  
    Skorzystaj z tradycyjnych najlepszych rozwiązań dotyczących migracji programu SQL Server, aby przenieść bazę danych zawartości na urządzenie StorSimple. Przenieś bazę danych tylko wtedy, gdy cała zawartość obiektu BLOB z bazy danych została przeniesiona do udziału plików za pośrednictwem SPZ. Jeśli zdecydujesz się przenieść bazę danych zawartości do urządzenia StorSimple, zaleca się skonfigurowanie magazynu bazy danych zawartości na urządzeniu jako woluminu podstawowego.
* W witrynie Microsoft Azure StorSimple, jeśli przy użyciu woluminów warstwowych, nie ma sposobu, aby zagwarantować, że zawartość przechowywana lokalnie na urządzeniu StorSimple nie będzie warstwowa do magazynu w chmurze platformy Microsoft Azure. W związku z tym zaleca się używanie storsimple lokalnie przypięte woluminy w połączeniu z SharePoint SPZ. Zapewni to, że cała zawartość obiektu BLOB pozostaje lokalnie na urządzeniu StorSimple i nie zostanie przeniesiona na platformę Microsoft Azure.
* Jeśli nie przechowujesz baz danych zawartości na urządzeniu StorSimple, należy użyć tradycyjnych sql server wysokiej dostępności najlepszych rozwiązań, które obsługują SPZ. Klastrowanie programu SQL Server obsługuje SPZ, podczas gdy dublowanie programu SQL Server nie. 

> [!WARNING]
> Jeśli nie włączono SPZ, nie zaleca się przenoszenia bazy danych zawartości do urządzenia StorSimple. Jest to konfiguracja nieprzetestowana.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Karta StorSimple do instalacji programu SharePoint
Przed zainstalowaniem karty StorSimple dla programu SharePoint należy skonfigurować urządzenie StorSimple i upewnić się, że farma programu SharePoint i wystąpienia programu SQL Server spełniają wszystkie wymagania wstępne. W tym samouczku opisano wymagania dotyczące konfiguracji, a także procedury instalowania i uaktualniania karty StorSimple dla programu SharePoint.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych
Przed zainstalowaniem karty StorSimple dla programu SharePoint upewnij się, że wystąpienie urządzenia StorSimple, farmy serwerów programu SharePoint i programu SQL Server spełniają następujące wymagania wstępne.

### <a name="system-requirements"></a>Wymagania systemowe
Karta StorSimple dla programu SharePoint współpracuje z następującym sprzętem i oprogramowaniem:

* Obsługiwany system operacyjny — Windows Server 2008 R2 z sp1, Windows Server 2012 lub Windows Server 2012 R2
* Obsługiwane wersje programu SharePoint — sharepoint server 2010 lub SharePoint Server 2013
* Obsługiwane wersje programu SQL Server — SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition lub SQL Server 2012 Enterprise Edition
* Obsługiwane urządzenia StorSimple — StorSimple serii 8000, StorSimple serii 7000 lub StorSimple serii 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Wymagania wstępne konfiguracji urządzenia StorSimple
Urządzenie StorSimple jest urządzeniem blokującym i jako takie wymaga serwera plików, na którym mogą być hostowane dane. Zaleca się używanie oddzielnego serwera, a nie istniejącego serwera z farmy programu SharePoint. Ten serwer plików musi znajdować się w tej samej sieci lokalnej (LAN) co komputer programu SQL Server, na którym znajdują się bazy danych zawartości.

> [!TIP]
> * Jeśli skonfigurujesz farmę programu SharePoint pod kątem wysokiej dostępności, należy również wdrożyć serwer plików w celu zapewnienia wysokiej dostępności.
> * Jeśli nie przechowujesz bazy danych zawartości na urządzeniu StorSimple, należy użyć tradycyjnych najlepszych rozwiązań o wysokiej dostępności, które obsługują SPZ. Klastrowanie programu SQL Server obsługuje SPZ, podczas gdy dublowanie programu SQL Server nie. 


Upewnij się, że urządzenie StorSimple jest poprawnie skonfigurowane i że odpowiednie woluminy do obsługi wdrożenia programu SharePoint są skonfigurowane i dostępne z komputera programu SQL Server. Przejdź do [wdrażania lokalnego urządzenia StorSimple,](storsimple-8000-deployment-walkthrough-u2.md) jeśli urządzenie StorSimple nie zostało jeszcze wdrożone i skonfigurowane. Zanotuj adres IP urządzenia StorSimple; będzie potrzebny podczas storsimple adapter do instalacji programu SharePoint.

Ponadto upewnij się, że wolumin używany do eksternalizacji obiektu BLOB spełnia następujące wymagania:

* Wolumin musi być sformatowany z rozmiarem jednostki alokacji 64 KB.
* Serwery frontowe (WFE) i aplikacje muszą mieć dostęp do woluminu za pośrednictwem ścieżki UNC (Universal Naming Convention).
* Farma serwerów programu SharePoint musi być skonfigurowana do zapisu na woluminie.

> [!NOTE]
> Po zainstalowaniu i skonfigurowaniu karty wszystkie eksternalizacja obiektu BLOB muszą przejść przez urządzenie StorSimple (urządzenie będzie prezentować woluminy w programie SQL Server i zarządzać warstwami magazynu). Nie można użyć żadnych innych obiektów docelowych dla eksternalizacji obiektu BLOB.


Jeśli planujesz używać StorSimple Snapshot Manager do robienia migawek obiektu BLOB i danych bazy danych, należy zainstalować StorSimple Snapshot Manager na serwerze bazy danych, dzięki czemu można użyć usługi SQL Writer Service do zaimplementowania usługi kopiowania woluminów w tle systemu Windows (VSS).

> [!IMPORTANT]
> Menedżer migawek StorSimple nie obsługuje modułu zapisującego usługi SharePoint VSS i nie może przyjmować spójnych z aplikacjami migawek danych programu SharePoint. W scenariuszu programu SharePoint StorSimple Snapshot Manager udostępnia tylko kopie zapasowe spójne z awariami.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Wymagania wstępne konfiguracji farmy programu SharePoint
Upewnij się, że farma serwerów programu SharePoint jest poprawnie skonfigurowana w następujący sposób:

* Sprawdź, czy farma serwerów programu SharePoint jest w dobrej kondycji, i sprawdź następujące kwestie:
* Wszystkie serwery WFE programu SharePoint i aplikacje zarejestrowane w farmie są uruchomione i mogą być pingowane z serwera, na którym będzie instalowana karta StorSimple dla programu SharePoint.
* Usługa timer programu SharePoint (SPTimerV3 lub SPTimerV4) jest uruchomiona na każdym serwerze WFE i serwerze aplikacji.
* Zarówno usługa czasomierz programu SharePoint, jak i pula aplikacji usług IIS, w ramach której działa centralna witryna administracji programu SharePoint, mają uprawnienia administracyjne.
* Upewnij się, że program Internet Explorer Enhanced Security Context (IE ESC) jest wyłączony. Wykonaj następujące kroki, aby wyłączyć IE ESC:
  
  1. Zamknij wszystkie wystąpienia programu Internet Explorer.
  2. Uruchom Menedżera serwera.
  3. W lewym okienku kliknij pozycję **Serwer lokalny**.
  4. W prawym okienku obok pozycji **Konfiguracja zwiększonych zabezpieczeń IE**kliknij przycisk **Włącz**.
  5. W obszarze **Administratorzy**kliknij pozycję **Wył.**
  6. Kliknij przycisk **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Wymagania wstępne zdalnego magazynu obiektów BLOB (RBS)
Upewnij się, że używasz obsługiwanej wersji programu SQL Server. Obsługiwane są tylko następujące wersje i mogą korzystać z SPZ:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Obiekty BLOB mogą być eksternalizowane tylko na tych woluminach, które urządzenie StorSimple przedstawia programowi SQL Server. Nie są obsługiwane żadne inne obiekty docelowe dla eksternalizacji obiektu BLOB.

Po wykonaniu wszystkich czynności konfiguracyjnych wymaganych, przejdź do [strony Instalowanie karty StorSimple dla programu SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalowanie karty StorSimple dla programu SharePoint
Aby zainstalować kartę StorSimple dla programu SharePoint, należy wykonać następujące czynności. W przypadku ponownej instalacji oprogramowania zobacz [Uaktualnianie lub ponowne instalowanie karty StorSimple dla programu SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Czas wymagany do instalacji zależy od całkowitej liczby baz danych programu SharePoint w farmie serwerów programu SharePoint.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurowanie SPZ
Po zainstalowaniu karty StorSimple dla programu SharePoint należy skonfigurować SPZ zgodnie z opisem w poniższej procedurze.

> [!TIP]
> Karta StorSimple dla programu SharePoint jest włączona do strony Administracja centralna programu SharePoint, umożliwiając włączenie lub wyłączenie spz. w każdej bazie danych zawartości w farmie programu SharePoint. Jednak włączenie lub wyłączenie SPZ w bazie danych zawartości powoduje resetowanie usług IIS, co w zależności od konfiguracji farmy może na chwilę zakłócić dostępność frontonu sieci Web programu SharePoint (WFE). (Czynniki, takie jak użycie modułu równoważenia obciążenia frontowego, bieżące obciążenie serwera itd.) Aby chronić użytkowników przed zakłóceniami, zaleca się włączenie lub wyłączenie SPZ tylko podczas okna planowanej konserwacji.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurowanie wyrzucania elementów bezużytecznych
Gdy obiekty są usuwane z witryny programu SharePoint, nie są automatycznie usuwane z woluminu magazynu SPZ. Zamiast tego asynchroniczne, w tle program konserwacji usuwa oddzielone bloków BLOB z magazynu plików. Administratorzy systemu mogą zaplanować okresowe uruchamianie tego procesu lub w razie potrzeby uruchomić go.

Ten program konserwacji (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) jest automatycznie instalowany na wszystkich serwerach WFE programu SharePoint i serwerach aplikacji po włączeniu sp.is. Program jest instalowany w następującej lokalizacji: *dysk rozruchowy*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

Aby uzyskać informacje dotyczące konfigurowania i używania programu konserwacyjnego, zobacz [Obsługa spz. w programie SharePoint Server 2013][8].

> [!IMPORTANT]
> Program opiekuna SPZ jest intensywnie korzystający z zasobów. Należy zaplanować jego uruchamianie tylko w okresach lekkiej aktywności w farmie programu SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Natychmiastowe usuwanie osieroconych bloków BLOB
Jeśli musisz natychmiast usunąć osierocone bloki BLOB, można użyć następujących instrukcji. Należy zauważyć, że te instrukcje są przykładem tego, jak można to zrobić w środowisku programu SharePoint 2013 z następującymi składnikami:

* Nazwa bazy danych zawartości jest WSS_Content.
* Nazwa programu SQL Server to SHRPT13-SQL12\SHRPT13.
* Nazwa aplikacji sieci Web to SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Uaktualnianie lub ponowne instalowanie karty StorSimple dla programu SharePoint
Poniższa procedura umożliwia uaktualnienie serwera programu SharePoint, a następnie ponowne zainstalowanie karty StorSimple adapter dla programu SharePoint lub po prostu uaktualnienie lub ponowne zainstalowanie karty w istniejącej farmie serwerów programu SharePoint.

> [!IMPORTANT]
> Zapoznaj się z następującymi informacjami przed próbą uaktualnienia oprogramowania programu SharePoint i/lub uaktualnienia lub ponownego zainstalowania karty StorSimple dla programu SharePoint:
> 
> * Wszystkie pliki, które zostały wcześniej przeniesione do magazynu zewnętrznego za pośrednictwem SPZ, nie będą dostępne do czasu zakończenia ponownej instalacji i ponownego włączenia funkcji SPZ. Aby ograniczyć wpływ na użytkownika, wykonaj wszelkie uaktualnienia lub ponownej instalacji podczas okna planowanej konserwacji.
> * Czas wymagany do uaktualnienia/ponownej instalacji może się różnić w zależności od całkowitej liczby baz danych programu SharePoint w farmie serwerów programu SharePoint.
> * Po zakończeniu uaktualniania/ponownej instalacji należy włączyć SPZ dla baz danych zawartości. Aby uzyskać więcej [informacji, zobacz Konfigurowanie SPZ.](#configure-rbs)
> * Jeśli konfigurujesz SPZ dla farmy programu SharePoint, która ma bardzo dużą liczbę baz danych (większą niż 200), strona **Administracja centralna programu SharePoint** może się przekreślić. W takim przypadku odśwież stronę. Nie ma to wpływu na proces konfiguracji.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple Adapter do usuwania programu SharePoint
W poniższych procedurach opisano sposób przenoszenia bloków BLOB z powrotem do baz danych zawartości programu SQL Server, a następnie odinstalowywania karty StorSimple dla programu SharePoint. 

> [!IMPORTANT]
> Przed odinstalowaniem oprogramowania karty należy przenieść obiekty BLOB z powrotem do baz danych zawartości.


### <a name="before-you-begin"></a>Przed rozpoczęciem
Zbierz następujące informacje przed przeniesieniem danych z powrotem do baz danych zawartości programu SQL Server i rozpocznij proces usuwania karty:

* Nazwy wszystkich baz danych, dla których włączono SPZ
* Ścieżka UNC skonfigurowanego magazynu obiektów BLOB

### <a name="move-the-blobs-back-to-the-content-databases"></a>Przenoszenie obiektów BLOB z powrotem do baz danych zawartości
Przed odinstalowaniem oprogramowania StorSimple Adapter for SharePoint należy przeprowadzić migrację wszystkich bloków BLOB, które zostały z zewnątrz z powrotem do baz danych zawartości programu SQL Server. Jeśli spróbujesz odinstalować kartę StorSimple dla programu SharePoint przed przeniesieniem wszystkich obiektów BLOB z powrotem do baz danych zawartości, zostanie wyświetlony następujący komunikat ostrzegawczy.

![Komunikat ostrzegawczy](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Aby przenieść obiekty BLOB z powrotem do baz danych zawartości
1. Pobierz każdy z zewnętrznych obiektów.
2. Otwórz stronę **Administracja centralna programu SharePoint** i przejdź do **pozycji Ustawienia systemowe**.
3. W obszarze **Azure StorSimple**kliknij pozycję **Konfiguruj kartę StorSimple**.
4. Na stronie **Konfigurowanie karty StorSimple** kliknij przycisk **Wyłącz** poniżej każdej bazy danych zawartości, które chcesz usunąć z zewnętrznego magazynu obiektów BLOB. 
5. Usuń obiekty z programu SharePoint, a następnie przekaż je ponownie.

Alternatywnie można użyć polecenia `RBS Migrate()` cmdlet programu Microsoft PowerShell dołączonego do programu SharePoint. Aby uzyskać więcej informacji, zobacz [Migrowanie zawartości do lub z SPZ](https://technet.microsoft.com/library/ff628255.aspx).

Po przeniesieniu bloków BLOB z powrotem do bazy danych zawartości przejdź do następnego kroku: [Odinstalowywanie karty](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Odinstalowywanie karty
Po przeniesieniu obiektów BLOB z powrotem do baz danych zawartości programu SQL Server należy odinstalować kartę StorSimple adapter dla programu SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Aby odinstalować kartę za pomocą programu instalacyjnego
1. Użyj konta z uprawnieniami administratora, aby zalogować się do serwera front-endu sieci web (WFE).
2. Kliknij dwukrotnie instalator StorSimple adapter dla programu SharePoint. Zostanie uruchomiony Kreator instalacji.
   
    ![Kreator instalacji](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Kliknij przycisk **alej**. Zostanie wyświetlona następna strona.
   
    ![Kreator instalacji usuwa stronę](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Kliknij **przycisk Usuń,** aby wybrać proces usuwania. Zostanie wyświetlona następna strona.
   
    ![Strona potwierdzenia kreatora instalacji](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Kliknij **przycisk Usuń,** aby potwierdzić usunięcie. Zostanie wyświetlona następująca strona postępu.
   
    ![Strona postępu kreatora instalacji](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Po zakończeniu usuwania zostanie wyświetlona strona zakończenia. Kliknij **przycisk Zakończ,** aby zamknąć Kreatora instalacji.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Aby odinstalować kartę za pomocą Panelu sterowania
1. Otwórz Panel sterowania, a następnie kliknij pozycję **Programy i funkcje**.
2. Wybierz pozycję **StorSimple Adapter for SharePoint**, a następnie kliknij przycisk **Odinstaluj**.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
