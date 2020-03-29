---
title: "3."
description: Użyj usługi Azure Backup Server, aby wykonać kopię zapasową i przywrócić dane programu SharePoint w usłudze Azure Stack. Ten artykuł zawiera informacje, aby skonfigurować farmę programu SharePoint, tak aby żądane dane mogły być przechowywane na platformie Azure. Chronione dane programu SharePoint można przywrócić z dysku lub platformy Azure.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d080605022cadf121fa6be99c9758fe9c0d878ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673038"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>3.

Tworzenie kopii zapasowych farmy programu SharePoint w usłudze Azure Stack na platformie Microsoft Azure przy użyciu programu Microsoft Azure Backup Server (MABS) w taki sam sposób, jak tworzenie kopii zapasowych innych źródeł danych. Usługa Azure Backup zapewnia elastyczność w harmonogramie tworzenia kopii zapasowych w celu tworzenia dziennych, tygodniowych, miesięcznych lub 5 000 punktów kopii zapasowych i udostępnia opcje zasad przechowywania dla różnych punktów kopii zapasowych. Zapewnia również możliwość przechowywania kopii dysków lokalnych dla celów szybkiego odzyskiwania czasu (RTO) i do przechowywania kopii na platformie Azure dla ekonomicznych, długoterminowego przechowywania.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint i powiązane scenariusze ochrony

Usługa Azure Backup for MABS obsługuje następujące scenariusze:

| Obciążenie | Wersja | Wdrażanie programu SharePoint | Ochrona i odzyskiwanie |
| --- | --- | --- | --- |
| Program SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |Program SharePoint wdrożony jako maszyna wirtualna usługi Azure Stack <br> -------------- <br> Program SQL AlwaysOn | Chroń opcje odzyskiwania farmy programu SharePoint Farm: Farma odzyskiwania, baza danych i element pliku lub listy z punktów odzyskiwania dysku.  Odzyskiwanie farmy i bazy danych z punktów odzyskiwania platformy Azure. |

## <a name="before-you-start"></a>Przed rozpoczęciem

Istnieje kilka rzeczy, które należy potwierdzić przed utworzeniem kopii zapasowej farmy programu SharePoint na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że [zainstalowano i przygotowano serwer kopii zapasowej platformy Azure](backup-mabs-install-azure-stack.md) w celu ochrony obciążeń.

### <a name="protection-agent"></a>Środek ochrony

Agent usługi Azure Backup musi być zainstalowany na serwerze z uruchomionym programem SharePoint, na serwerach z uruchomionym programem SQL Server i na wszystkich innych serwerach, które są częścią farmy programu SharePoint. Aby uzyskać więcej informacji na temat konfigurowania agenta ochrony, zobacz [Agent ochrony instalatora](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).  Jedynym wyjątkiem jest zainstalowanie agenta tylko na jednym serwerze frontu sieci web (WFE). Usługa Azure Backup Server potrzebuje agenta na jednym serwerze WFE tylko służyć jako punkt wejścia do ochrony.

### <a name="sharepoint-farm"></a>Farma programu SharePoint

Na każde 10 milionów elementów w farmie musi być co najmniej 2 GB miejsca na woluminie, na którym znajduje się folder MABS. Miejsce to jest wymagane na potrzeby generowania wykazów. Aby usługa MABS odzyskała określone elementy (zbiory witryn, witryny, listy, biblioteki dokumentów, foldery, pojedyncze dokumenty i elementy listy), generowanie katalogu tworzy listę adresów URL zawartych w każdej bazie danych zawartości. Listę adresów URL można wyświetlić w okienku elementów, które można odzyskać, w obszarze zadań **Odzyskiwanie** w konsoli administratora systemu MABS.

### <a name="sql-server"></a>SQL Server

Serwer kopii zapasowej platformy Azure działa jako konto LocalSystem. Aby uzyskać kopii zapasowej baz danych programu SQL Server, mabs potrzebuje uprawnień sysadmin na tym koncie dla serwera, na który jest uruchomiony program SQL Server. Ustaw nt authority\system *do sysadmin* na serwerze, na który jest uruchomiony program SQL Server przed utworzeniem kopii zapasowej.

Jeśli farma programu SharePoint ma bazy danych programu SQL Server skonfigurowane z aliasami programu SQL Server, zainstaluj składniki klienta programu SQL Server na serwerze sieci Web frontonu, który będzie chronić program MABS.

### <a name="whats-not-supported"></a>Jakie operacje nie są obsługiwane

* Usługa MABS, która chroni farmę programu SharePoint, nie chroni indeksów wyszukiwania ani baz danych usług aplikacji. Należy skonfigurować ochronę tych baz danych oddzielnie.
* Usługa MABS nie udostępnia kopii zapasowych baz danych programu SharePoint SQL Server, które są hostowane w udziałach serwera plików skalowanych w poziomie (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurowanie ochrony programu SharePoint

Przed użyciem usługi MABS do ochrony programu SharePoint należy skonfigurować usługę SharePoint VSS Writer (usługa WSS Writer) przy użyciu **programu ConfigureSharePoint.exe**.

**Pozycję Konfiguruj program SharePoint.exe** można znaleźć w folderze [Ścieżka instalacji MABS]\bin na serwerze sieci Web frontu. To narzędzie udostępnia agentowi ochrony poświadczenia dla farmy programu SharePoint. Uruchom go na jednym serwerze WFE. Jeśli masz wiele serwerów WFE, wybierz tylko jeden podczas konfigurowania grupy ochrony.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Aby skonfigurować usługę Programu SharePoint VSS Writer

1. Na serwerze WFE w wierszu polecenia przejdź do pozycji [Lokalizacja instalacji MABS]\bin\
2. Wprowadź pozycję ConfigureSharePoint -EnableSharePointProtection.
3. Wprowadź poświadczenia administratora farmy. To konto musi należeć do lokalnej grupy administratorów na serwerze WFE. Jeśli administrator farmy nie jest administratorem lokalnym, przyznaj następujące uprawnienia na serwerze WFE:
   * Nadaj grupie WSS_Admin_WPG pełną kontrolę folderowi programu DPM (%Program Files%\Microsoft Azure Backup\DPM).
   * Przyznaj grupie WSS_Admin_WPG dostęp do odczytu klucza rejestru programu DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Musisz ponownie uruchomić plik ConfigureSharePoint.exe za każdym razem, gdy nastąpi zmiana poświadczeń administratora farmy programu SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Tworzenie kopii zapasowych farmy programu SharePoint przy użyciu usługi MABS

Po skonfigurowaniu usługi MABS i farmy programu SharePoint, jak wyjaśniono wcześniej, program SharePoint może być chroniony przez program MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Aby chronić farmę programu SharePoint

1. Na karcie **Ochrona** konsoli administratora mabs kliknij pozycję **Nowy**.
    ![Nowa karta Ochrona](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na stronie **Wybierz typ grupy ochrony** **kreatora Tworzenie nowej grupy ochrony** wybierz pozycję **Serwery**, a następnie kliknij przycisk **Dalej**.

    ![Wybierz typ grupy ochrony](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na ekranie **Wybieranie członków grupy** zaznacz pole wyboru dla serwera programu SharePoint, który chcesz chronić, i kliknij przycisk **Dalej**.

    ![Wybieranie członków grupy](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Po zainstalowaniu agenta ochrony można wyświetlić serwer w kreatorze. MABS pokazuje również swoją strukturę. Ponieważ uruchomiono program ConfigureSharePoint.exe, program MABS komunikuje się z usługą programu SharePoint VSS Writer i odpowiadającymi jej bazami danych programu SQL Server i rozpoznaje strukturę farmy programu SharePoint, skojarzone bazy danych zawartości i wszystkie odpowiednie elementy.
   >
   >
4. Na stronie **Wybierz metodę ochrony danych** wprowadź nazwę grupy **ochrony**i wybierz preferowane *metody ochrony*. Kliknij przycisk **alej**.

    ![Wybieranie metody ochrony danych](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Metoda ochrony dysku pomaga osiągnąć krótkie cele czasu odzyskiwania.
   >
   >
5. Na stronie **Określanie celów krótkoterminowych** wybierz **preferowany zakres przechowywania**i określ, kiedy mają być zajmą się tworzenie kopii zapasowych.

    ![Określanie celów krótkoterminowych](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Ponieważ odzyskiwanie jest najczęściej wymagane dla danych, które mają mniej niż pięć dni, wybraliśmy zakres przechowywania pięciu dni na dysku i upewniliśmy się, że kopia zapasowa odbywa się w godzinach nieprodukcyjnych, w tym przykładzie.
   >
   >
6. Przejrzyj miejsce na dysku puli magazynów przydzielone dla grupy ochrony, a następnie kliknij przycisk **Dalej**.
7. Dla każdej grupy ochrony usługa MABS przydziela miejsce na dysku do przechowywania replik i zarządzania nimi. W tym momencie maseć musi utworzyć kopię wybranych danych. Wybierz sposób i czas utworzenia repliki, a następnie kliknij przycisk **Dalej**.

    ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Aby upewnić się, że ruch sieciowy nie jest realizowany, wybierz czas poza godzinami produkcji.
   >
   >
8. Usługa MABS zapewnia integralność danych, przeprowadzając kontrole spójności repliki. Dostępne są dwie opcje. Można zdefiniować harmonogram uruchamiania kontroli spójności lub program DPM można automatycznie uruchamiać sprawdzanie spójności w replice, gdy stanie się niespójna. Wybierz preferowaną opcję, a następnie kliknij przycisk **Dalej**.

    ![Kontrola spójności](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na stronie **Określanie danych ochrony online** wybierz farmę programu SharePoint, którą chcesz chronić, a następnie kliknij przycisk **Dalej**.

    ![Ochrona programu SharePoint programu DPM1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych online** wybierz preferowany harmonogram, a następnie kliknij przycisk **Dalej**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > Usługa MABS udostępnia maksymalnie dwa dzienne kopie zapasowe na platformie Azure z dostępnego wówczas najnowszego punktu kopii zapasowej dysku. Usługa Azure Backup może również kontrolować przepustowość sieci WAN, która może być używana do tworzenia kopii zapasowych w godzinach szczytu i poza szczytem przy użyciu [ograniczania przepustowości sieci kopii zapasowych platformy Azure.](backup-windows-with-mars-agent.md#enable-network-throttling)
    >
    >
11. W zależności od wybranego harmonogramu tworzenia kopii zapasowych na stronie **Określanie zasad przechowywania w trybie online** wybierz zasady przechowywania dla dziennych, tygodniowych, miesięcznych i 15 punktów kopii zapasowych.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > Mabs używa schematu przechowywania dziadek-ojciec-syn, w którym można wybrać inną zasady przechowywania dla różnych punktów kopii zapasowej.
    >
    >
12. Podobnie jak dysk, początkowa replika punktu odniesienia musi zostać utworzona na platformie Azure. Wybierz preferowaną opcję tworzenia początkowej kopii zapasowej na platformie Azure, a następnie kliknij przycisk **Dalej**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Przejrzyj wybrane ustawienia na stronie **Podsumowanie,** a następnie kliknij pozycję **Utwórz grupę**. Po utworzeniu grupy ochrony zostanie wyświetlony komunikat o powodach.

    ![Podsumowanie](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Przywracanie elementu programu SharePoint z dysku przy użyciu usługi MABS

W poniższym przykładzie *element Odzyskiwanie programu SharePoint* został przypadkowo usunięty i wymaga odzyskania.
![Ochrona programu SHAREPOINT 4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otwórz **konsolę administratora programu DPM**. Wszystkie farmy programu SharePoint, które są chronione przez program DPM są wyświetlane na karcie **Ochrona.**

    ![Ochrona programu SHAREPOINT mabs3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Aby rozpocząć odzyskiwanie elementu, wybierz kartę **Odzyskiwanie.**

    ![Ochrona programu SHAREPOINT 100 000000000](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Program SharePoint można wyszukać w programie *SharePoint, aby odzyskać element programu SharePoint* przy użyciu wyszukiwania opartego na symbolach wieloznacznych w zakresie punktów odzyskiwania.

    ![Ochrona programu SHAREPOINT mabs6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Wybierz odpowiedni punkt odzyskiwania z wyników wyszukiwania, kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie **Odzyskaj**.
5. Można również przeglądać różne punkty odzyskiwania i wybrać bazę danych lub element do odzyskania. Wybierz **pozycję Data > czas odzyskiwania**, a następnie wybierz właściwy punkt > punktu odzyskiwania **farmy programu SharePoint > > baza danych .**

    ![Ochrona programu SHAREPOINT 7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie **Odzyskaj,** aby otworzyć **Kreatora odzyskiwania**. Kliknij przycisk **alej**.

    ![Przejrzyj wybór odzyskiwania](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Wybierz typ odzyskiwania, który chcesz wykonać, a następnie kliknij przycisk **Dalej**.

    ![Typ odzyskiwania](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Wybór **opcji Odzyskaj do oryginału** w przykładzie odzyskuje element w oryginalnej witrynie programu SharePoint.
   >
   >
8. Wybierz **proces odzyskiwania,** którego chcesz użyć.

   * Wybierz **pozycję Odzyskaj bez użycia farmy odzyskiwania,** jeśli farma programu SharePoint nie uległa zmianie i jest taka sama jak przywracany punkt odzyskiwania.
   * Wybierz **pozycję Odzyskaj przy użyciu farmy odzyskiwania,** jeśli farma programu SharePoint uległa zmianie od czasu utworzenia punktu odzyskiwania.

     ![Proces odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Podaj tymczasową lokalizację wystąpienia programu SQL Server, aby tymczasowo odzyskać bazę danych, i podaj przemieszczania udział plików na mabs i serwerze, na którym jest uruchomiony program SharePoint, aby odzyskać element.

    ![Lokalizacja tymczasowa1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    Usługa MABS dołącza bazę danych zawartości hostującą element programu SharePoint do tymczasowego wystąpienia programu SQL Server. Z bazy danych zawartości odzyskuje element i umieszcza go w lokalizacji pliku przemieszczania na mabs. Odzyskany element, który znajduje się w lokalizacji tymczasowej, musi teraz zostać wyeksportowany do lokalizacji przejściowej w farmie programu SharePoint.

    ![Lokalizacja tymczasowa2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Wybierz **pozycję Określ opcje odzyskiwania**i zastosuj ustawienia zabezpieczeń do farmy programu SharePoint lub zastosuj ustawienia zabezpieczeń punktu odzyskiwania. Kliknij przycisk **alej**.

    ![Opcje odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Można ograniczyć użycie przepustowości sieci. Minimalizuje to wpływ na serwer produkcyjny w godzinach produkcji.
    >
    >
11. Przejrzyj informacje podsumowujące, a następnie kliknij przycisk **Odzyskaj,** aby rozpocząć odzyskiwanie pliku.

    ![Podsumowanie odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Teraz wybierz kartę **Monitorowanie** w **konsoli administratora mabs,** aby wyświetlić **stan** odzyskiwania.

    ![Stan odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Plik został przywrócony. Można odświeżyć witrynę programu SharePoint, aby sprawdzić przywrócony plik.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Przywracanie bazy danych programu SharePoint z platformy Azure przy użyciu programu DPM

1. Aby odzyskać bazę danych zawartości programu SharePoint, przejrzyj różne punkty odzyskiwania (jak pokazano wcześniej) i wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![Ochrona programu SHAREPOINT 100 000000000](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania programu SharePoint, aby wyświetlić dostępne informacje o wykazie programu SharePoint.

   > [!NOTE]
   > Ponieważ farma programu SharePoint jest chroniona pod kątem długoterminowego przechowywania na platformie Azure, żadne informacje o katalogu (metadane) nie są dostępne w usłudze MABS. W rezultacie za każdym razem, gdy baza danych zawartości programu SharePoint w programie SharePoint musi zostać odzyskana, należy ponownie skatalogować farmę programu SharePoint.
   >
   >
3. Kliknij **pozycję Ponownie kataloguj**.

    ![Ochrona programu SHAREPOINT mabs10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Zostanie otwarte okno stanu **Recatalog** w chmurze.

    ![Ochrona programu SHAREPOINT mabs11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu katalogowania stan zmienia się na *Sukces*. Kliknij przycisk **Zamknij**.

    ![Ochrona programu SHAREPOINT mabs12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknij obiekt programu SharePoint pokazany na karcie **Odzyskiwanie** MABS, aby uzyskać strukturę bazy danych zawartości. Kliknij prawym przyciskiem myszy element, a następnie kliknij polecenie **Odzyskaj**.

    ![Ochrona programu SHAREPOINT mabs13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. W tym momencie wykonaj kroki odzyskiwania wcześniej w tym artykule, aby odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="faqs"></a>Często zadawane pytania

Pyt.: Czy można odzyskać element programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu programu SQL AlwaysOn (z ochroną na dysku)?<br>
Odp.: Tak, element można odzyskać w oryginalnej witrynie programu SharePoint.

Pyt.: Czy można odzyskać bazę danych programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu programu SQL AlwaysOn?<br>
Odp.: Ponieważ bazy danych programu SharePoint są skonfigurowane w programie SQL AlwaysOn, nie można ich modyfikować, chyba że grupa dostępności zostanie usunięta. W rezultacie mabs nie można przywrócić bazy danych do oryginalnej lokalizacji. Bazę danych programu SQL Server można odzyskać w innym wystąpieniu programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Zobacz [plik kopii zapasowej i](backup-mabs-files-applications-azure-stack.md) artykuł aplikacji.
Zobacz [tworzenie kopii zapasowej programu SQL Server w usłudze Azure Stack.](backup-mabs-sql-azure-stack.md)
