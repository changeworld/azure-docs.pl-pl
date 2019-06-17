---
title: Użyj usługi Azure Backup server do tworzenia kopii zapasowych farmy programu SharePoint na platformie Azure
description: Usługi Azure Backup Server umożliwia tworzenie kopii zapasowej i przywracanie danych programu SharePoint. Ten artykuł zawiera informacje, aby skonfigurować farmę programu SharePoint, więc, że żądane dane mogą być przechowywane na platformie Azure. Chronione dane SharePoint można przywrócić z dysku lub na platformie Azure.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: kasinh
ms.openlocfilehash: 7fa68e11ccac69db9335e589f5048264df9d0a47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60645334"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Tworzenie kopii zapasowych farmy programu SharePoint na platformie Azure
Wykonywania kopii zapasowych farmy programu SharePoint w systemie Microsoft Azure za pomocą Microsoft Azure Backup Server (MABS) w podobny sposób, należy utworzyć kopię zapasową innych źródeł danych. Usługa Azure Backup zapewnia elastyczność harmonogram tworzenia kopii zapasowych, aby utworzyć codziennie, co tydzień, miesięczny lub roczny kopii zapasowej wskazuje i udostępnia opcje zasad przechowywania dla różnych punktów kopii zapasowej. Umożliwia także przechowywać kopie dysk lokalny dla szybkiego cele czasu odzyskiwania (RTO), a następnie do przechowywania kopii na platformie Azure, ekonomiczne, długoterminowego przechowywania.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint, a powiązane scenariusze ochrony
Usługa Azure Backup, program DPM obsługuje następujące scenariusze:

| Obciążenie | Version | Wdrażanie programu SharePoint | Ochrona i odzyskiwanie |
| --- | --- | --- | --- |
| Sharepoint |SharePoint 2016, SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |Program SharePoint jest wdrożony jako serwer fizyczny lub maszyna wirtualna funkcji Hyper-V/VMware <br> -------------- <br> Funkcji SQL AlwaysOn | Ochrona farmy programu SharePoint do odzyskania: Farmy odzyskiwania bazy danych i plik lub element listy z punktów odzyskiwania na dysku.  Farmy i odzyskiwanie bazy danych z punktów odzyskiwania platformy Azure. |

## <a name="before-you-start"></a>Przed rozpoczęciem
Istnieje kilka kwestii, czego potrzebujesz, aby upewnić się, zanim wykona kopii zapasowej farmy programu SharePoint na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że masz [zainstalowane i przygotowanie serwera usługi Azure Backup](backup-azure-microsoft-azure-backup.md) do ochrony obciążeń.

### <a name="protection-agent"></a>Agent ochrony
Musi być zainstalowany agent usługi Azure Backup na serwerze, który jest uruchomiony SharePoint, serwery z programem SQL Server i wszystkich serwerów, które są częścią farmy programu SharePoint. Aby uzyskać więcej informacji o sposobie konfigurowania agenta ochrony, zobacz [instalacji agenta ochrony](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Jedynym wyjątkiem jest, zainstaluj agenta tylko na jednym serwerze sieci web serwer sieci Web (WFE). Usługa Azure Backup Server wymaga agenta na jednym serwerze WFE tylko po to, która będzie służyć jako punkt wejścia ochrony.

### <a name="sharepoint-farm"></a>Farma programu SharePoint
Dla każdych 10 milionów elementów w farmie musi istnieć co najmniej 2 GB miejsca na woluminie, w którym znajduje się folder serwera usługi MAB. Ta przestrzeń jest wymagana na potrzeby generowania katalogu. Dla serwera usługi Mab odzyskać określone elementy (zbiory witryn, witryny, listy, biblioteki dokumentów, folderów, pojedyncze dokumenty i elementy listy) generowania wykazu tworzy listę adresów URL, które są zawarte w poszczególnych bazach danych zawartości. Można wyświetlić listę adresów URL w okienku elementów możliwych do odzyskania w **odzyskiwania** zadań obszarów konsoli administratora serwera usługi MAB.

### <a name="sql-server"></a>Oprogramowanie SQL Server
Usługa Azure Backup Server działa jako konto systemu lokalnego. Aby utworzyć kopię zapasową bazy danych programu SQL Server, serwera usługi Mab musi uprawnienia administratora na tym koncie dla serwera, na którym działa program SQL Server. Ustaw NT AUTHORITY\SYSTEM *sysadmin* na serwerze, na którym działa program SQL Server przed jej kopii zapasowej.

Jeśli farma programu SharePoint zawiera bazy danych SQL Server, które są skonfigurowane z użyciem aliasów programu SQL Server, należy zainstalować składniki klienta programu SQL Server na serwerze frontonu sieci Web, który będzie chronić serwera usługi MAB.

### <a name="sharepoint-server"></a>Oprogramowanie SharePoint Server
Gdy wydajność zależy od wielu czynników, takich jak rozmiar farmy programu SharePoint, stanowią ogólne wskazówki jednego serwera usługi Mab może chronić farmy programu SharePoint 25 TB.

### <a name="whats-not-supported"></a>Jakie operacje nie są obsługiwane
* Serwera usługi Mab, który zapewnia ochronę farmy programu SharePoint nie chroni indeksy wyszukiwania lub bazy danych usługi aplikacji. Należy skonfigurować osobno ochrony tych baz danych.
* Serwera usługi Mab nie zawiera kopię zapasową bazy danych programu SharePoint SQL Server, które znajdują się w udziałach (SOFS) serwera plików skalowalnego w poziomie.

## <a name="configure-sharepoint-protection"></a>Konfigurowanie ochrony programu SharePoint
Zanim MABS można użyć do ochrony programu SharePoint, należy skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint (usługa składnika zapisywania programu WSS) przy użyciu **ConfigureSharePoint.exe**.

Możesz znaleźć **ConfigureSharePoint.exe** w folderze [ścieżka instalacji serwera usługi Mab] \bin na serwerze frontonu sieci web. To narzędzie udostępnia agentowi ochrony poświadczenia farmy programu SharePoint. Możesz uruchomić na jednym serwerze WFE. Jeśli masz wiele serwerów WFE, należy wybrać jeden z nich w ramach konfigurowania grupy ochrony.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Aby skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint
1. Na serwerze WFE Otwórz wiersz polecenia i przejdź do \bin\ [Lokalizacja instalacji serwera usługi Mab]
2. Wprowadź ConfigureSharePoint - EnableSharePointProtection.
3. Wprowadź poświadczenia administratora farmy. To konto powinno należeć do lokalnej grupy administratorów na serwerze WFE. Jeśli administrator farmy nie jest lokalnym administratorem, przyznaj następujące uprawnienia na serwerze WFE:
   * Przyznaj grupie WSS_ADMIN_WPG uprawnienia Pełna kontrola nad folderem programu DPM (% Program Files%\Microsoft Azure Backup\DPM).
   * Przyznaj grupie WSS_ADMIN_WPG uprawnienia odczytu do klucza rejestru programu DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Należy ponownie uruchomić ConfigureSharePoint.exe, zawsze, gdy występuje zmiana poświadczeń administratora farmy programu SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Tworzenie kopii zapasowych farmy programu SharePoint za pomocą serwera usługi Mab
Po skonfigurowaniu serwera usługi Mab i farmy programu SharePoint, jak wyjaśniono wcześniej, programu SharePoint mogą być chronione przez serwera usługi MAB.

### <a name="to-protect-a-sharepoint-farm"></a>Ochrona farmy programu SharePoint
1. Z **ochrony** kartę w konsoli administratora serwera usługi Mab, kliknij przycisk **New**.
    ![Nowa karta ochrony](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na **wybierz typ grupy ochrony** strony **Utwórz nową grupę ochrony** kreatora, wybierz opcję **serwerów**, a następnie kliknij przycisk **dalej**.

    ![Typ grupy ochrony wybrać](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na **Wybierz członków grupy** ekranu, zaznacz pole wyboru dla serwera programu SharePoint chcesz chronić, a następnie kliknij przycisk **dalej**.

    ![Wybierz członków grupy](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Z zainstalowanym agentem ochrony widać serwera w kreatorze. Serwera usługi Mab pokazuje również jego strukturę. Ponieważ uruchomiono ConfigureSharePoint.exe serwera usługi Mab komunikuje się za pomocą usługi składnika zapisywania usługi VSS programu SharePoint i jej odpowiednie baz danych programu SQL Server i rozpoznaje struktura farmy programu SharePoint, skojarzone bazy danych zawartości i wszystkich odpowiednich elementów.
   >
   >
4. Na **wybierz metodę ochrony danych** strony, wprowadź nazwę **grupy ochrony**i wybierz preferowany *metody ochrony*. Kliknij przycisk **Dalej**.

    ![Wybierz metodę ochrony danych](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Metoda ochrony dysku pozwala spełniać krótki cele czasu odzyskiwania.
   >
   >
5. Na **Określ cele krótkoterminowe** stronie, wybierz preferowany **zakres przechowywania** i zidentyfikować, kiedy chcesz, aby tworzenie kopii zapasowej.

    ![Określ cele krótkoterminowe](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Ponieważ odzyskiwanie w większości przypadków jest wymagane dla danych, które są mniej niż pięć dni, firma Microsoft wybrany zakres przechowywania wynosi pięć dni na dysku i zapewnić, że kopia zapasowa będzie się działo podczas godzin spoza środowiska produkcyjnego, w tym przykładzie.
   >
   >
6. Przegląd puli magazynu przydzielone danej grupie ochrony miejsce na dysku, a następnie kliknij przycisk **dalej**.
7. Dla każdej grupy ochrony serwera usługi Mab przydziela miejsce na dysku do przechowywania i zarządzania nimi replik. W tym momencie serwera usługi Mab musi utworzyć kopię wybranych danych. Wybierz, jak i kiedy ma repliki utworzony, a następnie kliknij **dalej**.

    ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Aby upewnić się, że ruch sieciowy nie jest wykonywane, wybierz godzinę poza godzinami produkcji.
   >
   >
8. Serwera usługi Mab zapewnia integralność danych, wykonując sprawdzania spójności w replice. Dostępne są dwie opcje dostępne. Można zdefiniować harmonogram w celu uruchomienia sprawdzania spójności lub programu DPM można uruchomić sprawdzania spójności automatycznie w replice zawsze wtedy, gdy stanie się niespójna. Wybierz preferowaną opcję, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na **Określ dane ochrony Online** farmy programu SharePoint, który chcesz chronić, a następnie kliknij przycisk Wybierz **dalej**.

    ![Program DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na **Określ harmonogram tworzenia kopii zapasowych Online** strony, wybierz preferowany harmonogram, a następnie kliknij przycisk **dalej**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > Serwera usługi Mab zawiera maksymalnie dwóch codzienne kopie zapasowe na platformie Azure, z następnie dostępne najnowszego punktu kopii zapasowej dysku. Usługa Azure Backup można także kontrolować ilość przepustowości sieci WAN, która może służyć do tworzenia kopii zapasowych w szczycie i poza godzinami pracy przy użyciu [ograniczania sieci usługi Azure Backup](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. W zależności od harmonogramu tworzenia kopii zapasowej, na wybrany **Określanie zasad przechowywania Online** wybierz zasady przechowywania dla punktów kopii zapasowej dzienne, tygodniowe, miesięczne i roczne.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > Serwera usługi Mab używa schematu przechowywania dziadek ojciec syn., w którym można wybrać zasad inną wartość przechowywania dla różnych punktów kopii zapasowej.
    >
    >
12. Podobnie jak na dysku, replikę punktu początkowego odwołanie musi zostać utworzona na platformie Azure. Wybierz preferowaną opcję tworzenia początkowej kopii zapasowej na platformie Azure, a następnie kliknij przycisk **dalej**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Przejrzyj wybrane ustawienia na **Podsumowanie** strony, a następnie kliknij przycisk **Utwórz grupę**. Po utworzeniu grupy ochrony, zobaczą komunikat o powodzeniu.

    ![Podsumowanie](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Przywróć element programu SharePoint z dysku przy użyciu serwera usługi Mab
W poniższym przykładzie *element odzyskiwanie programu SharePoint* został przypadkowo usunięty, a następnie mają zostać odzyskane.
![SharePoint Protection4 serwera usługi Mab](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otwórz **konsoli administratora programu DPM**. Wszystkie farmy programu SharePoint, które są chronione przez program DPM są pokazane w **ochrony** kartę.

    ![SharePoint Protection3 serwera usługi Mab](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Aby rozpocząć odzyskać element, wybierz **odzyskiwania** kartę.

    ![SharePoint Protection5 serwera usługi Mab](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Możesz wyszukiwać programu SharePoint dla *element odzyskiwanie programu SharePoint* przy użyciu symboli wieloznacznych przeszukiwanie odzyskiwania punkt zakresu.

    ![SharePoint Protection6 serwera usługi Mab](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Wybierz punkt odzyskiwania odpowiednie w wynikach wyszukiwania, kliknij prawym przyciskiem myszy element, a następnie wybierz **odzyskać**.
5. Można również przeglądać różne punkty odzyskiwania i wybierz bazę danych lub element do odzyskania. Wybierz **daty > czas odzyskiwania**, a następnie wybierz poprawny **bazy danych > farmy programu SharePoint > punktu odzyskiwania > element**.

    ![SharePoint Protection7 serwera usługi Mab](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
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
9. Podaj lokalizację tymczasową wystąpienia programu SQL Server tymczasowo odzyskać bazę danych i zapewniają przejściowym udziału plików serwera usługi Mab i na serwerze, na którym uruchomiony jest SharePoint, aby odzyskać element.

    ![Location1 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location1.png)

    Serwera usługi Mab dołącza bazy danych zawartości, który jest hostem element programu SharePoint do tymczasowego wystąpienia programu SQL Server. Z bazy danych zawartości odzyskuje elementu i umieszcza go w tymczasowej lokalizacji pliku serwera usługi MAB. Odzyskiwanego elementu, który znajduje się w lokalizacji tymczasowej w teraz musi zostać wyeksportowane do lokalizacji tymczasowej w farmie programu SharePoint.

    ![Location2 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Wybierz **Określ opcje odzyskiwania**i Zastosuj ustawienia zabezpieczeń do farmy programu SharePoint lub Zastosuj ustawienia zabezpieczeń punktu odzyskiwania. Kliknij przycisk **Dalej**.

    ![Opcje odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Istnieje możliwość ograniczania użycia przepustowości sieci. Pozwala to zmniejszyć wpływ na serwerze produkcyjnym poza godzinami produkcji.
    >
    >
11. Przejrzyj dane podsumowania, a następnie kliknij przycisk **odzyskać** aby rozpocząć odzyskiwanie pliku.

    ![Podsumowanie odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Teraz wybierz **monitorowanie** karcie **konsoli administratora serwera usługi Mab** do wyświetlania **stan** odzyskiwania.

    ![Stan odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Teraz po przywróceniu pliku. Możesz odświeżyć witryny programu SharePoint, aby sprawdzić przywrócono plik.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Przywracanie bazy danych programu SharePoint na platformie Azure przy użyciu programu DPM
1. Aby odzyskać bazę danych zawartości programu SharePoint, przejrzyj różne punkty odzyskiwania (jak pokazano wcześniej), a następnie wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![SharePoint Protection8 serwera usługi Mab](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania SharePoint, aby wyświetlić dostępne informacje katalogu programu SharePoint.

   > [!NOTE]
   > Ponieważ w farmie programu SharePoint jest chroniony do długoterminowego przechowywania danych na platformie Azure, nie wykazu informacjami (metadanymi) jest dostępna w serwera usługi MAB. W rezultacie zawsze wtedy, gdy baza danych zawartości programu SharePoint w momencie mają zostać odzyskane, należy ponownie katalogu farmy programu SharePoint.
   >
   >
3. Kliknij przycisk **ponownego katalogowania**.

    ![SharePoint Protection10 serwera usługi Mab](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **Ponowne katalogowanie chmury** zostanie otwarte okno stanu.

    ![SharePoint Protection11 serwera usługi Mab](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu katalogowanie stan zmieni się na *Powodzenie*. Kliknij przycisk **Zamknij**.

    ![SharePoint Protection12 serwera usługi Mab](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknij obiekt SharePoint objętego serwera usługi Mab **odzyskiwania** kartę, aby pobrać struktury bazy danych zawartości. Kliknij prawym przyciskiem myszy element, a następnie kliknij przycisk **odzyskać**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. W tym momencie wykonać kroki odzyskiwania, w tym artykule, aby odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="faqs"></a>Często zadawane pytania
Pyt.: Czy można odzyskać element programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu funkcji SQL AlwaysOn (Ochrona na dysku)?<br>
Odp.: Tak, element można odzyskać do oryginalnej witryny programu SharePoint.

Pyt.: Czy można odzyskać bazy danych programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu funkcji SQL AlwaysOn?<br>
Odp.: Bazy danych programu SharePoint są skonfigurowane w funkcji SQL AlwaysOn, nie można modyfikować, chyba że grupa dostępności zostanie usunięta. W rezultacie serwera usługi Mab, nie można przywrócić bazę danych do oryginalnej lokalizacji. Może odzyskać bazę danych programu SQL Server do innego wystąpienia programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Zobacz [tworzenie kopii zapasowej serwerów Exchange](backup-azure-exchange-mabs.md) artykułu.
Zobacz [tworzenie kopii zapasowych programu SQL Server](backup-azure-sql-mabs.md) artykułu.
