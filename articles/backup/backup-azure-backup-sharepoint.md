---
title: Ochrona serwera platformy Azure i programu DPM kopii zapasowej farmy programu SharePoint na platformie Azure
description: Ten artykuł zawiera omówienie platformy Azure i programu DPM kopii zapasowej serwera ochrony farmy programu SharePoint na platformie Azure
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 7e8043badbc0accd38ad618a7d455729ab6606b2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60644339"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Tworzenie kopii zapasowych farmy programu SharePoint na platformie Azure
Wykonywania kopii zapasowych farmy programu SharePoint w systemie Microsoft Azure za pomocą System Center Data Protection Manager (DPM) w podobny sposób, należy utworzyć kopię zapasową innych źródeł danych. Usługa Azure Backup zapewnia elastyczność harmonogram tworzenia kopii zapasowych, aby utworzyć codziennie, co tydzień, miesięczny lub roczny kopii zapasowej wskazuje i udostępnia opcje zasad przechowywania dla różnych punktów kopii zapasowej. Program DPM udostępnia możliwości, aby przechowywać kopie dysk lokalny na szybkie cele czasu odzyskiwania (RTO) i do przechowywania kopii na platformie Azure, ekonomiczne, długoterminowego przechowywania.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint, a powiązane scenariusze ochrony
Usługa Azure Backup, program DPM obsługuje następujące scenariusze:

| Obciążenie | Version | Wdrażanie programu SharePoint | Typ wdrożenia programu DPM | DPM — System Center 2012 R2 | Ochrona i odzyskiwanie |
| --- | --- | --- | --- | --- | --- |
| Sharepoint |SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |Program SharePoint jest wdrożony jako serwer fizyczny lub maszyna wirtualna funkcji Hyper-V/VMware <br> -------------- <br> Funkcji SQL AlwaysOn |Serwer lub w środowisku lokalnym funkcji Hyper-V wirtualnych maszyny fizycznej |Obsługuje tworzenie kopii zapasowej na platformie Azure z pakietem zbiorczym aktualizacji 5 |Ochrona farmy programu SharePoint do odzyskania: Farmy odzyskiwania bazy danych i plik lub element listy z punktów odzyskiwania na dysku.  Farmy i odzyskiwanie bazy danych z punktów odzyskiwania platformy Azure. |

## <a name="before-you-start"></a>Przed rozpoczęciem
Istnieje kilka kwestii, czego potrzebujesz, aby upewnić się, zanim wykona kopii zapasowej farmy programu SharePoint na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że zostały spełnione wszystkie [wymagania wstępne dotyczące korzystania z programu Kopia zapasowa Microsoft Azure](backup-azure-dpm-introduction.md#prerequisites-and-limitations) do ochrony obciążeń. Niektóre zadania, wymagania wstępne obejmują: Tworzenie magazynu kopii zapasowych, Pobierz poświadczenia magazynu, zainstaluj agenta usługi Azure Backup i Zarejestruj serwer kopii zapasowych platformy Azure i programu DPM w magazynie.

### <a name="dpm-agent"></a>Agent programu DPM
Musi być zainstalowany agent programu DPM na serwerze, który jest uruchomiony SharePoint, serwery z programem SQL Server i wszystkich serwerów, które są częścią farmy programu SharePoint. Aby uzyskać więcej informacji o sposobie konfigurowania agenta ochrony, zobacz [instalacji agenta ochrony](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Jedynym wyjątkiem jest, zainstaluj agenta tylko na jednym serwerze sieci web serwer sieci Web (WFE). Program DPM wymaga agenta na jednym serwerze WFE tylko po to, która będzie służyć jako punkt wejścia ochrony.

### <a name="sharepoint-farm"></a>Farma programu SharePoint
Dla każdych 10 milionów elementów w farmie musi istnieć co najmniej 2 GB miejsca na woluminie, w którym znajduje się folder programu DPM. Ta przestrzeń jest wymagana na potrzeby generowania katalogu. Programu DPM odzyskać określone elementy (zbiory witryn, witryny, listy, biblioteki dokumentów, folderów, pojedyncze dokumenty i elementy listy) generowania wykazu tworzy listę adresów URL, które są zawarte w poszczególnych bazach danych zawartości. Można wyświetlić listę adresów URL w okienku elementów możliwych do odzyskania w **odzyskiwania** zadań obszarów konsoli administratora programu DPM.

### <a name="sql-server"></a>Oprogramowanie SQL Server
Program DPM działa jako konto systemu lokalnego. Aby utworzyć kopię zapasową bazy danych programu SQL Server, program DPM wymaga uprawnień administratora systemu na tym koncie dla serwera, na którym działa program SQL Server. Ustaw NT AUTHORITY\SYSTEM *sysadmin* na serwerze, na którym działa program SQL Server przed jej kopii zapasowej.

Jeśli farma programu SharePoint zawiera bazy danych SQL Server, które są skonfigurowane z użyciem aliasów programu SQL Server, należy zainstalować składniki klienta programu SQL Server na serwerze frontonu sieci Web, które program DPM będzie chronić.

### <a name="sharepoint-server"></a>Oprogramowanie SharePoint Server
Gdy wydajność zależy od wielu czynników, takich jak rozmiar farmy programu SharePoint, stanowią ogólne wskazówki jeden serwer DPM może chronić farmy programu SharePoint 25 TB.

### <a name="dpm-update-rollup-5"></a>Program DPM pakietem zbiorczym aktualizacji 5
Aby rozpocząć ochronę farmy programu SharePoint na platformie Azure, musisz zainstalować pakiet zbiorczy aktualizacji programu DPM 5 lub nowszej. Pakiet zbiorczy aktualizacji 5 zapewnia możliwość ochrony farmy programu SharePoint na platformie Azure, jeśli farmy jest konfigurowana przy użyciu funkcji SQL AlwaysOn.
Aby uzyskać więcej informacji, zobacz blog post, w której wprowadzono [DPM Update Rollup 5](https://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### <a name="whats-not-supported"></a>Jakie operacje nie są obsługiwane
* Program DPM, który zapewnia ochronę farmy programu SharePoint nie chroni indeksy wyszukiwania lub bazy danych usługi aplikacji. Należy skonfigurować osobno ochrony tych baz danych.
* Program DPM nie zapewnia kopii zapasowych baz danych programu SharePoint SQL Server, które znajdują się w udziałach (SOFS) serwera plików skalowalnego w poziomie.

## <a name="configure-sharepoint-protection"></a>Konfigurowanie ochrony programu SharePoint
Zanim użyjesz programu DPM do ochrony programu SharePoint, należy skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint (usługa składnika zapisywania programu WSS) przy użyciu **ConfigureSharePoint.exe**.

Możesz znaleźć **ConfigureSharePoint.exe** w folderze [ścieżka instalacji programu DPM] \bin na serwerze frontonu sieci web. To narzędzie udostępnia agentowi ochrony poświadczenia farmy programu SharePoint. Możesz uruchomić na jednym serwerze WFE. Jeśli masz wiele serwerów WFE, należy wybrać jeden z nich w ramach konfigurowania grupy ochrony.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Aby skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint
1. Na serwerze WFE Otwórz wiersz polecenia i przejdź do \bin\ [Lokalizacja instalacji programu DPM]
2. Wprowadź ConfigureSharePoint - EnableSharePointProtection.
3. Wprowadź poświadczenia administratora farmy. To konto powinno należeć do lokalnej grupy administratorów na serwerze WFE. Jeśli administrator farmy nie jest lokalnym administratorem, przyznaj następujące uprawnienia na serwerze WFE:
   * Przyznaj grupie WSS_ADMIN_WPG uprawnienia Pełna kontrola nad folderem programu DPM (% Program Files%\Microsoft Data Protection Manager\DPM).
   * Przyznaj grupie WSS_ADMIN_WPG uprawnienia odczytu do klucza rejestru programu DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Należy ponownie uruchomić ConfigureSharePoint.exe, zawsze, gdy występuje zmiana poświadczeń administratora farmy programu SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>Tworzenie kopii zapasowych farmy programu SharePoint przy użyciu programu DPM
Po skonfigurowaniu programu DPM i farmy programu SharePoint, jak wyjaśniono wcześniej, programu SharePoint mogą być chronione przez program DPM.

### <a name="to-protect-a-sharepoint-farm"></a>Ochrona farmy programu SharePoint
1. Z **ochrony** kartę konsoli administratora programu DPM, kliknij przycisk **New**.
    ![Nowa karta ochrony](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na **wybierz typ grupy ochrony** strony **Utwórz nową grupę ochrony** kreatora, wybierz opcję **serwerów**, a następnie kliknij przycisk **dalej**.

    ![Typ grupy ochrony wybrać](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na **Wybierz członków grupy** ekranu, zaznacz pole wyboru dla serwera programu SharePoint chcesz chronić, a następnie kliknij przycisk **dalej**.

    ![Wybierz członków grupy](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Z zainstalowanym agentem DPM widać serwera w kreatorze. Program DPM wyświetla również jego strukturę. Ponieważ uruchomiono ConfigureSharePoint.exe, program DPM komunikuje się za pomocą usługi składnika zapisywania usługi VSS programu SharePoint i jej odpowiednie baz danych programu SQL Server i rozpoznaje struktura farmy programu SharePoint, skojarzone bazy danych zawartości i wszystkich odpowiednich elementów.
   >
   >
4. Na **wybierz metodę ochrony danych** strony, wprowadź nazwę **grupy ochrony**i wybierz preferowany *metody ochrony*. Kliknij przycisk **Dalej**.

    ![Wybierz metodę ochrony danych](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Metoda ochrony dysku pozwala spełniać krótki cele czasu odzyskiwania. Platforma Azure jest docelowym ekonomiczne, długoterminowa ochrona taśmy. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure Backup do zastąpienia infrastruktury taśmy](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   >
   >
5. Na **Określ cele krótkoterminowe** stronie, wybierz preferowany **zakres przechowywania** i zidentyfikować, kiedy chcesz, aby tworzenie kopii zapasowej.

    ![Określ cele krótkoterminowe](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Ponieważ odzyskiwanie w większości przypadków jest wymagane dla danych, które są mniej niż pięć dni, firma Microsoft wybrany zakres przechowywania wynosi pięć dni na dysku i zapewnić, że kopia zapasowa będzie się działo podczas godzin spoza środowiska produkcyjnego, w tym przykładzie.
   >
   >
6. Przegląd puli magazynu przydzielone danej grupie ochrony miejsce na dysku, a następnie kliknij przycisk **dalej**.
7. Dla każdej grupy ochrony program DPM przydzieli miejsce na dysku do przechowywania i zarządzania nimi replik. W tym momencie Program DPM należy utworzyć kopię wybranych danych. Wybierz, jak i kiedy ma repliki utworzony, a następnie kliknij **dalej**.

    ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Aby upewnić się, że ruch sieciowy nie jest wykonywane, wybierz godzinę poza godzinami produkcji.
   >
   >
8. Program DPM zapewnia integralność danych, wykonując sprawdzania spójności w replice. Dostępne są dwie opcje dostępne. Można zdefiniować harmonogram w celu uruchomienia sprawdzania spójności lub programu DPM można uruchomić sprawdzania spójności automatycznie w replice zawsze wtedy, gdy stanie się niespójna. Wybierz preferowaną opcję, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na **Określ dane ochrony Online** farmy programu SharePoint, który chcesz chronić, a następnie kliknij przycisk Wybierz **dalej**.

    ![Program DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na **Określ harmonogram tworzenia kopii zapasowych Online** strony, wybierz preferowany harmonogram, a następnie kliknij przycisk **dalej**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > Program DPM udostępnia maksymalnie dwóch codzienne kopie zapasowe na platformie Azure w różnym czasie. Usługa Azure Backup można także kontrolować ilość przepustowości sieci WAN, która może służyć do tworzenia kopii zapasowych w szczycie i poza godzinami pracy przy użyciu [ograniczania sieci usługi Azure Backup](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. W zależności od harmonogramu tworzenia kopii zapasowej, na wybrany **Określanie zasad przechowywania Online** wybierz zasady przechowywania dla punktów kopii zapasowej dzienne, tygodniowe, miesięczne i roczne.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > Program DPM używa schematu przechowywania dziadek ojciec syn., w którym można wybrać zasad inną wartość przechowywania dla różnych punktów kopii zapasowej.
    >
    >
12. Podobnie jak na dysku, replikę punktu początkowego odwołanie musi zostać utworzona na platformie Azure. Wybierz preferowaną opcję tworzenia początkowej kopii zapasowej na platformie Azure, a następnie kliknij przycisk **dalej**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Przejrzyj wybrane ustawienia na **Podsumowanie** strony, a następnie kliknij przycisk **Utwórz grupę**. Po utworzeniu grupy ochrony, zobaczą komunikat o powodzeniu.

    ![Podsumowanie](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Przywróć element programu SharePoint z dysku przy użyciu programu DPM
W poniższym przykładzie *element odzyskiwanie programu SharePoint* został przypadkowo usunięty, a następnie mają zostać odzyskane.
![Program DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otwórz **konsoli administratora programu DPM**. Wszystkie farmy programu SharePoint, które są chronione przez program DPM są pokazane w **ochrony** kartę.

    ![Program DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Aby rozpocząć odzyskać element, wybierz **odzyskiwania** kartę.

    ![Program DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Możesz wyszukiwać programu SharePoint dla *element odzyskiwanie programu SharePoint* przy użyciu symboli wieloznacznych przeszukiwanie odzyskiwania punkt zakresu.

    ![Program DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Wybierz punkt odzyskiwania odpowiednie w wynikach wyszukiwania, kliknij prawym przyciskiem myszy element, a następnie wybierz **odzyskać**.
5. Można również przeglądać różne punkty odzyskiwania i wybierz bazę danych lub element do odzyskania. Wybierz **daty > czas odzyskiwania**, a następnie wybierz poprawny **bazy danych > farmy programu SharePoint > punktu odzyskiwania > element**.

    ![Program DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kliknij prawym przyciskiem myszy element, a następnie wybierz **odzyskać** otworzyć **Kreatora odzyskiwania**. Kliknij przycisk **Dalej**.

    ![Przejrzyj elementy wybrane do odzyskania](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Wybierz typ odzyskiwania, który chcesz wykonać, a następnie kliknij przycisk **dalej**.

    ![Typ odzyskiwania](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Wybór **Odzyskaj do oryginalnego** w przykładzie odzyskuje elementu do oryginalnej witryny programu SharePoint.
   >
   >
8. Wybierz **proces odzyskiwania** , którego chcesz użyć.

   * Wybierz **odzyskiwania bez używania farmy odzyskiwania** Jeśli farma programu SharePoint nie został zmieniony i jest taki sam jak punkt odzyskiwania, która jest przywracana.
   * Wybierz **odzyskać za pomocą farmy odzyskiwania** Jeśli farma SharePoint zmieniła się od czasu utworzenia punktu odzyskiwania.

     ![Proces odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Podaj lokalizację tymczasową wystąpienia programu SQL Server do odzyskania bazy danych, tymczasowo i zapewniają przejściowym udziału plików na serwerze DPM i serwera, na którym uruchomiono program SharePoint, aby odzyskać element.

    ![Location1 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location1.png)

    Program DPM dołącza bazy danych zawartości, który jest hostem element programu SharePoint do tymczasowego wystąpienia programu SQL Server. Z bazy danych zawartości serwer programu DPM odzyskuje elementu i umieszcza go w tymczasowej lokalizacji pliku na serwerze programu DPM. Odzyskiwanego elementu, który teraz jest w tymczasowej lokalizacji serwera programu DPM musi zostać wyeksportowane do lokalizacji tymczasowej w farmie programu SharePoint.

    ![Location2 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Wybierz **Określ opcje odzyskiwania**i Zastosuj ustawienia zabezpieczeń do farmy programu SharePoint lub Zastosuj ustawienia zabezpieczeń punktu odzyskiwania. Kliknij przycisk **Dalej**.

    ![Opcje odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Istnieje możliwość ograniczania użycia przepustowości sieci. Pozwala to zmniejszyć wpływ na serwerze produkcyjnym poza godzinami produkcji.
    >
    >
11. Przejrzyj dane podsumowania, a następnie kliknij przycisk **odzyskać** aby rozpocząć odzyskiwanie pliku.

    ![Podsumowanie odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Teraz wybierz **monitorowanie** karcie **konsoli administratora programu DPM** do wyświetlania **stan** odzyskiwania.

    ![Stan odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Teraz po przywróceniu pliku. Możesz odświeżyć witryny programu SharePoint, aby sprawdzić przywrócono plik.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Przywracanie bazy danych programu SharePoint na platformie Azure przy użyciu programu DPM
1. Aby odzyskać bazę danych zawartości programu SharePoint, przejrzyj różne punkty odzyskiwania (jak pokazano wcześniej), a następnie wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![Program DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania SharePoint, aby wyświetlić dostępne informacje katalogu programu SharePoint.

   > [!NOTE]
   > Ponieważ w farmie programu SharePoint jest chroniony do długoterminowego przechowywania danych na platformie Azure, nie wykazu informacjami (metadanymi) jest dostępna na serwerze programu DPM. W rezultacie zawsze wtedy, gdy baza danych zawartości programu SharePoint w momencie mają zostać odzyskane, należy ponownie katalogu farmy programu SharePoint.
   >
   >
3. Kliknij przycisk **ponownego katalogowania**.

    ![Program DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **Ponowne katalogowanie chmury** zostanie otwarte okno stanu.

    ![Program DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu katalogowanie stan zmieni się na *Powodzenie*. Kliknij przycisk **Zamknij**.

    ![Program DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknij obiekt programu SharePoint wyświetlana w programie DPM **odzyskiwania** kartę, aby pobrać struktury bazy danych zawartości. Kliknij prawym przyciskiem myszy element, a następnie kliknij przycisk **odzyskać**.

    ![Program DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. W tym momencie wykonać kroki odzyskiwania, w tym artykule, aby odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="faqs"></a>Często zadawane pytania

### <a name="which-versions-of-dpm-support-sql-server-2014-and-sql-2012-sp2"></a>Które wersje programu DPM obsługuje program SQL Server 2014 i SQL 2012 (SP2)?
Program DPM 2012 R2 z pakietem zbiorczym aktualizacji 4 obsługuje obie.

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Czy można odzyskać element programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu funkcji SQL AlwaysOn (Ochrona na dysku)?
Tak, element można odzyskać do oryginalnej witryny programu SharePoint.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Czy można odzyskać bazy danych programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu funkcji SQL AlwaysOn?
Bazy danych programu SharePoint są skonfigurowane w funkcji SQL AlwaysOn, nie można modyfikować, chyba że grupa dostępności zostanie usunięta. W rezultacie program DPM nie można przywrócić bazę danych do oryginalnej lokalizacji. Może odzyskać bazę danych programu SQL Server do innego wystąpienia programu SQL Server.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat ochrony programu DPM z programu SharePoint — zobacz [seria wideo — program DPM ochronę programu SharePoint](https://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Przegląd [informacje o wersji programu System Center 2012 — Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
* Przegląd [informacje o wersji programu Data Protection Manager w programie System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)
