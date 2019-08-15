---
title: Program DPM/Azure Backup ochronę farmy programu SharePoint na platformie Azure
description: Ten artykuł zawiera omówienie ochrony programu DPM/Azure Backup serwera w farmie programu SharePoint na platformie Azure
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: dacurwin
ms.openlocfilehash: 875c2002d477a95b44ad1491cb716e2ef70697e7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954847"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Tworzenie kopii zapasowej farmy programu SharePoint na platformie Azure przy użyciu programu DPM
Można utworzyć kopię zapasową farmy programu SharePoint w celu Microsoft Azure przy użyciu programu System Center Data Protection Manager (DPM) w taki sam sposób, jak w przypadku tworzenia kopii zapasowych innych źródeł danych. Azure Backup zapewnia elastyczność harmonogramu tworzenia kopii zapasowych w celu tworzenia codziennych, cotygodniowych, comiesięcznych i corocznych punktów kopii zapasowych oraz zapewnia opcje zasad przechowywania dla różnych punktów kopii zapasowych. Program DPM oferuje możliwość przechowywania kopii dysków lokalnych w celu uzyskania krótkich celów związanych z odzyskiwaniem (RTO) oraz przechowywania kopii na platformie Azure w celu zapewnienia ekonomicznego i długoterminowego przechowywania.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint i powiązane z nimi scenariusze ochrony
Azure Backup programu DPM obsługuje następujące scenariusze:

| Obciążenie | Version | Wdrożenie programu SharePoint | Typ wdrożenia programu DPM | DPM — System Center 2012 R2 | Ochrona i odzyskiwanie |
| --- | --- | --- | --- | --- | --- |
| Program SharePoint |SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |Program SharePoint wdrożony jako serwer fizyczny lub maszyna wirtualna z funkcją Hyper-V/VMware <br> -------------- <br> SQL AlwaysOn |Serwer fizyczny lub lokalna maszyna wirtualna funkcji Hyper-V |Obsługuje tworzenie kopii zapasowych na platformie Azure z pakietu zbiorczego aktualizacji 5 |Ochrona farmy programu SharePoint — opcje odzyskiwania: Farma odzyskiwania, baza danych i plik lub element listy z punktów odzyskiwania dysku.  Odzyskiwanie farmy i bazy danych z punktów odzyskiwania platformy Azure. |

## <a name="before-you-start"></a>Przed rozpoczęciem
Przed utworzeniem kopii zapasowej farmy programu SharePoint na platformie Azure istnieje kilka rzeczy, które należy potwierdzić.

### <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że zostały spełnione wszystkie [wymagania wstępne dotyczące używania Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) w celu ochrony obciążeń. Niektóre zadania związane z wymaganiami wstępnymi obejmują: Tworzenie magazynu kopii zapasowych, pobieranie poświadczeń magazynu, instalowanie Azure Backup agenta i rejestrowanie programu DPM/Azure Backup Server z magazynem.

### <a name="dpm-agent"></a>Agent programu DPM
Agent programu DPM musi być zainstalowany na serwerze, na którym działa program SharePoint, serwery z systemem SQL Server i wszystkie inne serwery, które są częścią farmy programu SharePoint. Więcej informacji o sposobie konfigurowania agenta ochrony znajduje się w temacie [Setup Protection Agent](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Jedynym wyjątkiem jest zainstalowanie agenta tylko na jednym serwerze frontonu sieci Web (WFE). Program DPM wymaga agenta na jednym serwerze WFE tylko jako punktu wejścia do ochrony.

### <a name="sharepoint-farm"></a>Farma programu SharePoint
Dla każdego 10 000 000 elementów w farmie musi znajdować się co najmniej 2 GB miejsca na woluminie, na którym znajduje się folder programu DPM. To miejsce jest wymagane do generacji katalogu. Aby program DPM odzyskał określone elementy (Kolekcje witryn, witryny, listy, biblioteki dokumentów, foldery, pojedyncze dokumenty i elementy listy), generacja wykazu tworzy listę adresów URL zawartych w poszczególnych bazach danych zawartości. Listę adresów URL można wyświetlić w okienku element możliwy do odzyskania w obszarze zadania **odzyskiwania** Konsola administratora programu DPM.

### <a name="sql-server"></a>SQL Server
Program DPM działa jako konto LocalSystem. Aby utworzyć kopię zapasową SQL Server baz danych, program DPM musi mieć uprawnienia administratora systemu na tym koncie dla serwera, na którym działa program SQL Server. Przed utworzeniem kopii zapasowej należy ustawić NT NT\SYSTEM na serwerze, na którym działa SQL Server.

Jeśli farma programu SharePoint ma SQL Server baz danych, które są skonfigurowane przy użyciu aliasów SQL Server, Zainstaluj składniki klienta SQL Server na serwerze frontonu sieci Web, który będzie chroniony przez program DPM.

### <a name="sharepoint-server"></a>Oprogramowanie SharePoint Server
Chociaż wydajność jest zależna od wielu czynników, takich jak rozmiar farmy programu SharePoint, jako ogólne wskazówki jeden serwer DPM może chronić 25 TB farmy programu SharePoint.

### <a name="dpm-update-rollup-5"></a>Pakiet zbiorczy aktualizacji programu DPM 5
Aby rozpocząć ochronę farmy programu SharePoint na platformie Azure, musisz zainstalować pakiet zbiorczy aktualizacji programu DPM 5 lub nowszy. Pakiet zbiorczy aktualizacji 5 umożliwia ochronę farmy programu SharePoint na platformie Azure, jeśli Farma jest skonfigurowana przy użyciu funkcji SQL AlwaysOn.
Aby uzyskać więcej informacji, zapoznaj się z wpisem w blogu zawierającym [pakiet zbiorczy aktualizacji programu DPM 5](https://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### <a name="whats-not-supported"></a>Jakie operacje nie są obsługiwane
* Program DPM, który chroni farmę programu SharePoint, nie chroni indeksów wyszukiwania lub baz danych usług aplikacji. Należy osobno skonfigurować ochronę tych baz danych.
* Program DPM nie udostępnia kopii zapasowych baz danych programu SharePoint SQL Server hostowanych w udziałach serwera plików skalowalnego w poziomie (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurowanie ochrony programu SharePoint
Przed użyciem programu DPM do ochrony programu SharePoint należy skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint (usługę składnika zapisywania usługi WSS) przy użyciu programu **ConfigureSharePoint. exe**.

**ConfigureSharePoint. exe** można znaleźć w folderze [ścieżka instalacji programu DPM] \Bin na serwerze frontonu sieci Web. To narzędzie udostępnia agentowi ochrony poświadczenia farmy programu SharePoint. Jest on uruchamiany na jednym serwerze WFE. Jeśli masz wiele serwerów WFE, wybierz opcję tylko jeden podczas konfigurowania grupy ochrony.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Aby skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint
1. Na serwerze WFE, w wierszu polecenia, przejdź do [lokalizacja instalacji programu DPM] \Bin\
2. Wprowadź ConfigureSharePoint-EnableSharePointProtection.
3. Wprowadź poświadczenia administratora farmy. To konto musi być członkiem lokalnej grupy administratorów na serwerze WFE. Jeśli administrator farmy nie jest administratorem lokalnym, przyznaj następujące uprawnienia na serwerze WFE:
   * Przyznaj grupie WSS_Admin_WPG pełną kontrolę do folderu programu DPM (% Program Files%\Microsoft Data Protection Manager\DPM).
   * Przyznaj grupie WSS_Admin_WPG dostęp do odczytu do klucza rejestru programu DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Po zmianie poświadczeń administratora farmy programu SharePoint należy ponownie uruchomić program ConfigureSharePoint. exe.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>Tworzenie kopii zapasowej farmy programu SharePoint przy użyciu programu DPM
Po skonfigurowaniu programu DPM i farmy programu SharePoint w sposób opisany wcześniej program SharePoint może być chroniony przez program DPM.

### <a name="to-protect-a-sharepoint-farm"></a>Aby chronić farmę programu SharePoint
1. Na karcie **Ochrona** w Konsola administratora programu DPM kliknij pozycję **Nowy**.
    ![Karta Nowa ochrona](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na stronie **Wybierz typ grupy ochrony** w kreatorze **tworzenia nowej grupy ochrony** wybierz pozycję **serwery**, a następnie kliknij przycisk **dalej**.

    ![Wybierz typ grupy ochrony](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na ekranie **Wybieranie członków grupy** zaznacz pole wyboru dla serwera programu SharePoint, który ma być chroniony, a następnie kliknij przycisk **dalej**.

    ![Wybierz członków grupy](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Po zainstalowaniu agenta programu DPM można zobaczyć serwer w kreatorze. Program DPM wyświetla również strukturę. Ponieważ uruchomiono ConfigureSharePoint. exe, program DPM komunikuje się z usługą składnika zapisywania usługi VSS programu SharePoint i odpowiadającymi jej SQL Server bazami danych i rozpoznaje strukturę farmy programu SharePoint, skojarzone bazy danych zawartości i wszelkie odpowiadające im elementy.
   >
   >
4. Na stronie **Wybierz metodę ochrony danych** wprowadź nazwę **grupy ochrony**i wybierz preferowane *metody ochrony*. Kliknij przycisk **Dalej**.

    ![Wybierz metodę ochrony danych](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Metoda ochrony dysku pomaga spełnić krótkie cele czasu odzyskiwania. Azure to ekonomiczny, długoterminowy cel ochrony w porównaniu z taśmami. Aby uzyskać więcej informacji, zobacz [używanie Azure Backup do zastępowania infrastruktury taśmowej](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   >
   >
5. Na stronie **Określ cele krótkoterminowe** wybierz swój preferowany **Zakres przechowywania** i określ, kiedy mają być wykonywane kopie zapasowe.

    ![Określ cele krótkoterminowe](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Ponieważ odzyskiwanie jest najczęściej wymagane w przypadku danych, które są starsze niż pięć dni, w tym przykładzie wybrano zakres przechowywania równy pięć dni i upewnił się, że kopia zapasowa występuje w godzinach nieprodukcyjnych.
   >
   >
6. Przejrzyj miejsce na dysku w puli magazynów przydzieloną dla grupy ochrony, a następnie kliknij przycisk **dalej**.
7. W przypadku każdej grupy ochrony program DPM przydziela miejsce na dysku do przechowywania replik i zarządzania nimi. W tym momencie program DPM musi utworzyć kopię wybranych danych. Wybierz, jak i Kiedy chcesz utworzyć replikę, a następnie kliknij przycisk **dalej**.

    ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Aby upewnić się, że ruch sieciowy nie jest zastosowany, wybierz czas poza godzinami produkcji.
   >
   >
8. Program DPM zapewnia integralność danych przez wykonywanie kontroli spójności repliki. Dostępne są dwie opcje. Można zdefiniować harmonogram uruchamiania kontroli spójności lub uruchomić testy spójności automatycznie w replice, gdy stanie się niespójna. Wybierz preferowaną opcję, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na stronie **Określ dane ochrony w trybie online** wybierz farmę programu SharePoint, którą chcesz chronić, a następnie kliknij przycisk **dalej**.

    ![Protection1 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych online** wybierz preferowany harmonogram, a następnie kliknij przycisk **dalej**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > Program DPM oferuje maksymalnie dwa codzienne kopie zapasowe na platformie Azure w różnych godzinach. Azure Backup może również kontrolować ilość przepustowości sieci WAN, która może być używana w przypadku kopii zapasowych w godzinach szczytu i poza szczytem przy użyciu funkcji [ograniczania przepustowości sieci Azure Backup](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. W zależności od wybranego harmonogramu tworzenia kopii zapasowych na stronie **Określanie zasad przechowywania danych online** wybierz zasady przechowywania codziennie, co tydzień, co miesiąc i roczne punkty kopii zapasowej.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > Program DPM używa schematu przechowywania z dziadkiem-ojciec-syn, w którym można wybrać różne zasady przechowywania dla różnych punktów kopii zapasowych.
    >
    >
12. Podobnie jak w przypadku dysku, należy utworzyć początkową replikę punktu odniesienia na platformie Azure. Wybierz preferowaną opcję, aby utworzyć początkową kopię zapasową na platformie Azure, a następnie kliknij przycisk **dalej**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Przejrzyj wybrane ustawienia na stronie **Podsumowanie** , a następnie kliknij przycisk **Utwórz grupę**. Po utworzeniu grupy ochrony zostanie wyświetlony komunikat o powodzeniu.

    ![Podsumowanie](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Przywracanie elementu programu SharePoint z dysku przy użyciu programu DPM
W poniższym przykładzie *element odzyskiwania programu SharePoint* został przypadkowo usunięty i należy go odzyskać.
![Protection4 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otwórz **Konsola administratora programu DPM**. Wszystkie farmy programu SharePoint, które są chronione przez program DPM, są wyświetlane na karcie **Ochrona** .

    ![Protection3 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Aby rozpocząć odzyskiwanie elementu, wybierz kartę **odzyskiwanie** .

    ![Protection5 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Możesz wyszukać program SharePoint pod kątem *odzyskiwania elementu programu SharePoint* , używając wyszukiwania opartego na symbolu wieloznacznego w zakresie punktów odzyskiwania.

    ![Protection6 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Wybierz odpowiedni punkt odzyskiwania z wyników wyszukiwania, kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie **Odzyskaj**.
5. Można także przeglądać różne punkty odzyskiwania i wybierać bazę danych lub element do odzyskania. Wybierz **datę > czas odzyskiwania**, a następnie wybierz prawidłową **bazę danych > farmy programu SharePoint > > elementu punktu odzyskiwania**.

    ![Protection7 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie Odzyskaj, aby otworzyć **Kreatora odzyskiwania**. Kliknij przycisk **Dalej**.

    ![Przegląd wyboru odzyskiwania](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Wybierz typ odzyskiwania, który chcesz wykonać, a następnie kliknij przycisk **dalej**.

    ![Typ odzyskiwania](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Wybór opcji **Odzyskaj do oryginału** w przykładzie odzyskuje element do oryginalnej witryny programu SharePoint.
   >
   >
8. Wybierz **proces odzyskiwania** , którego chcesz użyć.

   * Wybierz opcję **Odzyskaj bez używania farmy odzyskiwania** , jeśli farma programu SharePoint nie uległa zmianie i jest taka sama jak przywracany punkt odzyskiwania.
   * Wybierz opcję **Odzyskaj przy użyciu farmy odzyskiwania** , jeśli farma programu SharePoint została zmieniona od czasu utworzenia punktu odzyskiwania.

     ![Proces odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Podaj lokalizację wystąpienia SQL Server przemieszczania w celu tymczasowej odzyskania bazy danych i Udostępnij udział plików przemieszczania na serwerze programu DPM oraz na serwerze, na którym działa program SharePoint w celu odzyskania elementu.

    ![Location1 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location1.png)

    Program DPM dołącza bazę danych zawartości, która hostuje element programu SharePoint do wystąpienia tymczasowego SQL Server. Z bazy danych zawartości serwer programu DPM odzyska element i umieści go w lokalizacji pliku przemieszczania na serwerze programu DPM. Odzyskany element znajdujący się w lokalizacji tymczasowej serwera programu DPM należy teraz wyeksportować do lokalizacji tymczasowej w farmie programu SharePoint.

    ![Location2 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Wybierz opcję **Określ opcje odzyskiwania**i Zastosuj ustawienia zabezpieczeń do farmy programu SharePoint lub Zastosuj ustawienia zabezpieczeń punktu odzyskiwania. Kliknij przycisk **Dalej**.

    ![Opcje odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Możesz wybrać opcję ograniczenia użycia przepustowości sieci. Pozwala to zminimalizować wpływ na serwer produkcyjny w godzinach produkcyjnych.
    >
    >
11. Przejrzyj informacje podsumowujące, a następnie kliknij przycisk Odzyskaj, aby rozpocząć odzyskiwanie pliku.

    ![Podsumowanie odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Teraz wybierz kartę **monitorowanie** w **Konsola administratora programu DPM** , aby wyświetlić **stan** odzyskiwania.

    ![Stan odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Plik zostanie przywrócony. Można odświeżyć witrynę programu SharePoint, aby sprawdzić przywrócony plik.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Przywracanie bazy danych programu SharePoint z platformy Azure przy użyciu programu DPM
1. Aby odzyskać bazę danych zawartości programu SharePoint, Przejrzyj różne punkty odzyskiwania (jak pokazano wcześniej) i wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![Protection8 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania programu SharePoint, aby wyświetlić dostępne informacje dotyczące katalogu programu SharePoint.

   > [!NOTE]
   > Ponieważ farma programu SharePoint jest chroniona do długoterminowego przechowywania na platformie Azure, na serwerze DPM nie są dostępne żadne informacje o katalogu (metadane). W związku z tym zawsze, gdy baza danych zawartości programu SharePoint do punktu w czasie musi zostać odzyskana, należy ponownie wykazać farmy programu SharePoint.
   >
   >
3. Kliknij pozycję **ponownie katalog**.

    ![Protection10 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Zostanie otwarte okno stanu usługi **Cloud Catalog** .

    ![Protection11 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu wykazania stan zmieni się na *powodzenie*. Kliknij przycisk **Zamknij**.

    ![Protection12 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknij obiekt programu SharePoint wyświetlony na karcie **odzyskiwanie** programu DPM, aby uzyskać strukturę bazy danych zawartości. Kliknij prawym przyciskiem myszy element, a następniekliknij polecenie Odzyskaj.

    ![Protection13 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. W tym momencie wykonaj kroki odzyskiwania opisane wcześniej w tym artykule, aby odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o ochronie programu SharePoint w programie DPM — zobacz [serie wideo — ochrona programu SharePoint w programie DPM](https://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Przejrzyj [Informacje o wersji programu System Center 2012 — Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
* Przejrzyj [Informacje o wersji Data Protection Manager w programie System Center 2012 z dodatkiem SP1](https://technet.microsoft.com/library/jj860394.aspx)
