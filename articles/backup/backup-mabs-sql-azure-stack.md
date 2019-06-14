---
title: Tworzenie kopii zapasowych obciążeń programu SQL Server w usłudze Azure Stack
description: Użyj usługi Azure Backup Server, aby chronić obciążenia programu SQL Server w usłudze Azure Stack.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: fb064c39fa014515fb2a3f4ccc96ce216f2f7b2e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60254477"
---
# <a name="back-up-sql-server-on-stack"></a>Tworzenie kopii zapasowych programu SQL Server na stosie
W tym artykule umożliwiają konfigurowanie Microsoft Azure Backup Server (MABS) ochrony baz danych programu SQL Server w usłudze Azure Stack.

Zarządzanie kopii zapasowej bazy danych programu SQL Server na platformę Azure i odzyskiwanie z platformy Azure obejmuje trzy kroki:

1. Tworzenie zasad kopii zapasowych, aby chronić bazy danych programu SQL Server
2. Tworzenie kopii zapasowych na żądanie
3. Odzyskiwanie bazy danych z dysków i z platformy Azure

## <a name="before-you-start"></a>Przed rozpoczęciem

[Instalowanie i przygotowanie serwera usługi Azure Backup](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Tworzenie zasad kopii zapasowych w celu ochrony baz danych programu SQL Server na platformie Azure
1. W Interfejsie użytkownika serwera kopii zapasowych usługi Azure, kliknij polecenie **ochrony** obszaru roboczego.

2. Na wstążce narzędzi kliknij polecenie **New** do tworzenia nowej grupy ochrony.

    ![Utwórz grupę ochrony](./media/backup-azure-backup-sql/protection-group.png)

    Usługa Azure Backup Server uruchamia Kreatora grupy ochrony, który poprowadzi Cię przez proces tworzenia **grupy ochrony**. Kliknij przycisk **Dalej**.

3. W **wybierz typ grupy ochrony** ekranu, wybierz **serwerów**.

    ![Wybierz typ grupy ochrony — "Serwery"](./media/backup-azure-backup-sql/pg-servers.png)

4. W **Wybierz członków grupy** ekranie, na liście dostępne elementy członkowskie są wyświetlane różne źródła danych. Kliknij przycisk **+** rozwiń folder i wyświetlić podfoldery. Kliknij pole wyboru, aby wybrać element.

    ![Wybierz bazy danych SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Wszystkie wybrane elementy pojawiają się na liście elementów członkowskich wybrane. Po wybraniu serwerów lub baz danych, które chcesz chronić, kliknij przycisk **dalej**.

5. W **wybierz metodę ochrony danych** ekranu, podaj nazwę grupy ochrony, a następnie wybierz **chcę uzyskać ochronę online** pola wyboru.

    ![Metoda ochrony danych — krótkoterminowa dysku & Online dotyczącym platformy Azure](./media/backup-azure-backup-sql/pg-name.png)

6. W **Określ cele krótkoterminowe** ekranu, należy umieścić niezbędne dane wejściowe, aby utworzyć punktów kopii zapasowej na dysku, a następnie kliknij przycisk **dalej**.

    W tym przykładzie **zakres przechowywania** jest **5 dni**, **częstotliwość synchronizacji** jest jeden raz każdego **15 minut**, czyli kopii zapasowej częstotliwość. **Ekspresowa pełna kopia zapasowa** ustawiono **godzinach od 8:00**.

    ![Cele krótkoterminowe](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > W tym przykładzie pokazano o 8:00 każdego dnia punktu kopii zapasowej jest tworzony przez przeniesienie zmodyfikowanych danych z punktu kopii zapasowej 20:00:00 poprzedniego dnia. Ten proces jest nazywany **Express pełnej kopii zapasowej**. Dzienniki transakcji są synchronizowane co 15 minut. Jeśli potrzebujesz odzyskać bazę danych o 9:00, punkt jest tworzony z dzienników w ciągu ostatnich express punktu pełnej kopii zapasowej (20: 00 w tym przypadku).
   >
   >

7. Na **Przejrzyj przydział dysku** ekranu, sprawdź ogólną miejsca do magazynowania dostępne i miejsca na dysku. Kliknij przycisk **Dalej**.

8. W **wybierz metodę tworzenia repliki**, wybierz, jak utworzyć pierwszy punkt odzyskiwania. Mogą przesyłać początkowa kopia zapasowa ręcznie (funkcja wyłączona sieci), aby uniknąć przeciążenia przepustowość lub za pośrednictwem sieci. Jeśli użytkownik chce czekać na przesyłanie pierwszej kopii zapasowej, wystarczy podać godzinę dla transferu początkowego. Kliknij przycisk **Dalej**.

    ![Metoda replikacji początkowej](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga przesyłania całego źródła danych (bazy danych programu SQL Server) z serwera produkcyjnego (SQL Server machine) do serwera usługi Azure Backup. Te dane mogą być duże, a przepustowość przesyłania danych za pośrednictwem sieci może przekroczyć. Z tego powodu można przenieść tworzenia początkowej kopii zapasowej: **Ręcznie** (przy użyciu nośnika wymiennego) aby uniknąć przeciążenia przepustowość lub **automatycznie przez sieć** (o określonej godzinie).

    Po zakończeniu tworzenia początkowej kopii zapasowej, pozostałe kopie zapasowe są przyrostowych kopii zapasowych w początkowej kopii zapasowej. Przyrostowe kopie zapasowe są małe i łatwo są przesyłane przez sieć.

9. Wybierz, kiedy chcesz kontrolę spójności, aby uruchomić, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    Usługa Azure Backup Server przeprowadza sprawdzanie spójności na integralności punktu kopii zapasowej. Usługa Azure Backup Server oblicza sumy kontrolnej pliku kopii zapasowej na serwerze produkcyjnym (SQL Server maszyny w tym scenariuszu) i kopia zapasowa danych dla tego pliku. Jeśli występuje konflikt, zakłada się, że plik kopii zapasowej w usłudze Azure Backup Server jest uszkodzony. Usługa Azure Backup Server rectifies kopię zapasową danych, wysyłając bloki odpowiadający niezgodność sumy kontrolnej. Ponieważ sprawdzania spójności znajdują się intensywnie, można planować Sprawdzanie spójności lub automatycznego uruchamiania.

10. Aby określić ochrony źródeł danych w trybie online, wybierz bazy danych mają być chronione na platformie Azure i kliknij przycisk **dalej**.

    ![Wybierz źródła danych](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Wybierz harmonogramy tworzenia kopii zapasowych i zasady przechowywania, które spełniają zasady organizacji.

    ![Harmonogram i okres przechowywania](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są pobierane raz dziennie, 12:00 PM i 20: 00 (dolnej części ekranu)

    > [!NOTE]
    > Jest dobrą praktyką ma kilka punkty odzyskiwania krótkoterminowego na dysku, szybkie odzyskiwanie. Te punkty odzyskiwania są używane do odzyskiwania operacyjnego. Platforma Azure służy jako lokalizacji poza siedzibą w dobrym o wyższych umowy SLA i gwarantowana dostępność.
    >
    >

    **Najlepsze rozwiązanie**: Jeśli zostanie zaplanowane kopie zapasowe na platformie Azure, aby uruchomić po zakończeniu tworzenia kopii zapasowych dysk lokalny, najnowsze kopie zapasowe dysków zawsze są kopiowane do platformy Azure.

12. Wybierz harmonogram zasad przechowywania. Szczegółowe informacje o sposobie działania zasady przechowywania są podane na [użycia usługi Azure Backup do zastąpienia infrastruktury taśmy artykułu](backup-azure-backup-cloud-as-tape.md).

    ![Zasady przechowywania](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są wykonywane raz dziennie o 12:00 PM i 20: 00 (dolnej części ekranu) i są przechowywane przez 180 dni.
    * Kopia zapasowa w sobotę o godzinie 12:00 w dniu został zachowany na potrzeby 104 tygodni
    * Kopia zapasowa na ostatni sobotę o godzinie 12:00 w dniu są przechowywane przez 60 miesięcy
    * Kopia zapasowa w sobotę ostatniego marca o 12:00 są zachowywane przez okres 10 lat
13. Kliknij przycisk **dalej** i wybierz odpowiednią opcję przesyłania początkowa kopia zapasowa na platformie Azure. Możesz wybrać **automatycznie przez sieć**

14. Po przejrzeniu szczegółów zasad w **Podsumowanie** ekranu, kliknij przycisk **Utwórz grupę** ukończenia przepływu pracy. Możesz kliknąć pozycję **Zamknij** i monitorować postęp zadania w obszarze roboczym monitorowanie.

    ![Tworzenie grupy ochrony w toku](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Na żądanie kopii zapasowej bazy danych programu SQL Server
Podczas poprzednich kroków utworzono zasady tworzenia kopii zapasowych, "punkt odzyskiwania" jest tworzony tylko wtedy, gdy występuje pierwszej kopii zapasowej. Bez czekania harmonogram, który ma Nashville, kroki przedstawione poniżej wyzwalacza tworzenia odzyskiwania punktu ręcznie.

1. Zaczekaj, aż stan grupy ochrony jest wyświetlany **OK** dla bazy danych przed utworzeniem punktu odzyskiwania.

    ![Członkowie grupy ochrony](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kliknij prawym przyciskiem myszy bazę danych i wybierz pozycję **Tworzenie punktu odzyskiwania**.

    ![Tworzenie punktu odzyskiwania Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Wybierz **ochrony w trybie Online** menu rozwijane i kliknij przycisk **OK** zacząć tworzenie punktu odzyskiwania na platformie Azure.

    ![Utwórz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Wyświetlić postęp zadania w **monitorowanie** obszaru roboczego.

    ![Konsola monitorowania](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych programu SQL Server na platformie Azure
Poniższe kroki są wymagane do odzyskiwanie chronionej jednostki (baza danych programu SQL Server) z platformy Azure.

1. Otwórz konsolę zarządzania usługi Azure Backup Server. Przejdź do **odzyskiwania** obszar roboczy, gdzie można zobaczyć Serwery chronione. Przeglądaj wymaganej bazy danych (w tym przypadków ReportServer$ MSDPM2012). Wybierz **odzyskanie danych będzie** czas, który jest określony jako **Online** punktu.

    ![Wybierz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kliknij prawym przyciskiem myszy nazwę bazy danych, a następnie kliknij przycisk **odzyskać**.

    ![Odzyskiwanie z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. Serwera usługi Mab pokazuje szczegóły punktu odzyskiwania. Kliknij przycisk **Dalej**. Aby zastąpić bazy danych, wybierz typ odzyskiwania **Odzyskaj do oryginalnego wystąpienia programu SQL Server**. Kliknij przycisk **Dalej**.

    ![Odzyskaj do oryginalnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie serwera usługi Mab odzyskuje bazę danych do innego wystąpienia programu SQL Server lub do folderu sieciowego autonomicznych.

4. W **opcji odzyskiwania określ** ekranu, można wybrać opcje odzyskiwania takich jak ograniczenie przepustowości ograniczania przepustowości, używane przez odzyskiwania. Kliknij przycisk **Dalej**.

5. W **Podsumowanie** ekranu, zobacz wszystkie konfiguracje odzyskiwania dostarczane do tej pory. Kliknij przycisk **odzyskać**.

    Stan odzyskiwania to odzyskiwanej bazy danych. Możesz kliknąć pozycję **Zamknij** aby zamknąć kreatora i wyświetlić postęp w **monitorowanie** obszaru roboczego.

    ![Zainicjuj proces odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po ukończeniu odzyskiwania przywróconej bazy danych jest spójne na poziomie aplikacji.

## <a name="next-steps"></a>Następne kroki

Zobacz [tworzyć kopie zapasowe plików i aplikacji](backup-mabs-files-applications-azure-stack.md) artykułu.
Zobacz [SharePoint kopii zapasowej w usłudze Azure Stack](backup-mabs-sharepoint-azure-stack.md) artykułu.
