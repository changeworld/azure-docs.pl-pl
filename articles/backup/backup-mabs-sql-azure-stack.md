---
title: Tworzenie kopii zapasowej obciążeń programu SQL Server na stosie Azure
description: Serwer kopii zapasowej Azure umożliwia ochronę obciążeń programu SQL Server na stosie Azure.
services: backup
author: pvrk
manager: Shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: pullabhk
ms.openlocfilehash: ca7da7ab048b6f7bfdba81aac9bc7702b20ff967
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751801"
---
# <a name="back-up-sql-server-on-stack"></a>Tworzenie kopii zapasowych programu SQL Server na stosie
Umożliwia skonfigurowanie programu Microsoft Azure kopii zapasowej serwera (MABS) do ochrony baz danych serwera SQL na stosie Azure w tym artykule.

Zarządzanie kopii zapasowej bazy danych programu SQL Server na platformie Azure oraz odzyskiwania z platformy Azure obejmuje trzy kroki:

1. Tworzenie zasad tworzenia kopii zapasowej w celu ochrony baz danych programu SQL Server
2. Tworzenie kopii zapasowych na żądanie
3. Odzyskiwanie bazy danych z dysków i z platformy Azure

## <a name="before-you-start"></a>Przed rozpoczęciem

[Instalowanie i przygotowanie serwera kopii zapasowej Azure](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Tworzenie zasad tworzenia kopii zapasowej w celu ochrony baz danych serwera SQL na platformie Azure
1. W Interfejsie użytkownika serwera kopii zapasowej Azure, kliknij polecenie **ochrony** obszaru roboczego.

2. Na wstążce narzędzi kliknij **nowy** do tworzenia nowej grupy ochrony.

    ![Tworzenie grupy ochrony](./media/backup-azure-backup-sql/protection-group.png)

    Serwer kopii zapasowej systemu Azure uruchamia Kreatora grupy ochrony, który poprowadzi Cię przez proces tworzenia **grupy ochrony**. Kliknij przycisk **Dalej**.

3. W **wybierz typ grupy ochrony** wybierz **serwerów**.

    ![Wybierz typ grupy ochrony — "Serwery"](./media/backup-azure-backup-sql/pg-servers.png)

4. W **Wybierz członków grupy** różnych źródeł danych wyświetla ekran, na liście dostępne elementy członkowskie. Kliknij przycisk **+** aby rozwinąć folder i ujawnić podfoldery. Kliknij pole wyboru, aby wybrać element.

    ![Wybierz bazy danych SQL.](./media/backup-azure-backup-sql/pg-databases.png)

    Wszystkie wybrane elementy znajdują się na liście elementów członkowskich wybrane. Po wybraniu serwerów lub baz danych, które chcesz chronić, kliknij przycisk **dalej**.

5. W **wybierz metodę ochrony danych** ekranu, podaj nazwę grupy ochrony i wybierz **chcę uzyskać ochronę online** wyboru.

    ![Metodę ochrony danych - krótkoterminowej dysku & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. W **Określ cele krótkoterminowe** ekranu, zawierają niezbędne dane wejściowe, aby utworzyć punktów kopii zapasowej na dysku, a następnie kliknij przycisk **dalej**.

    W tym przykładzie **zakres przechowywania** jest **5 dni**, **częstotliwość synchronizacji** jest raz co **15 minut**, która jest tworzenie kopii zapasowej częstotliwość. **Ekspresowa pełna kopia zapasowa** ustawiono **godzinach od 8:00**.

    ![Długoterminowe cele](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > W podanym przykładzie o 20:00:00 każdego dnia punktu kopii zapasowej została utworzona przez przeniesienie zmodyfikowane dane z punktu kopii zapasowej poprzedniego dnia 8:00 PM. Ten proces jest nazywany **Express pełnej kopii zapasowej**. Dzienniki transakcji są synchronizowane co 15 minut. Jeśli potrzebujesz przywrócić bazę danych na 21:00:00, punkt jest tworzony z dzienników w ciągu ostatnich ekspresowych pełnych kopii zapasowych punktu (20: 00 w tym przypadku).
   >
   >

7. Na **Przejrzyj przydział dysku** ekranu, sprawdź ogólną miejsca do magazynowania dostępne i miejsca na dysku. Kliknij przycisk **Dalej**.

8. W **wybierz metodę tworzenia repliki**, wybierz sposób utworzyć pierwszy punkt odzyskiwania. Aby uniknąć przeciążenia przepustowość można przenieść początkowa kopia zapasowa ręcznie (poza sieci) lub za pośrednictwem sieci. Wybranie opcji oczekiwania na transfer pierwszej kopii zapasowej, można określić czas przesyłania początkowej. Kliknij przycisk **Dalej**.

    ![Replikacja początkowa — metoda](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga przesyłania całego źródła danych (bazy danych programu SQL Server) z serwera produkcyjnego (komputer serwera SQL) do serwera kopii zapasowej Azure. Te dane mogą być duże i przesyłania danych za pośrednictwem sieci może przekroczyć przepustowości. Z tego powodu, użytkownik może przenieść początkowa kopia zapasowa: **ręcznie** (przy użyciu nośnika wymiennego) aby uniknąć przeciążenia przepustowości lub **automatycznie przez sieć** (w określonym czasie).

    Po zakończeniu tworzenia początkowej kopii zapasowej pozostałe kopie zapasowe są przyrostowych kopii zapasowych w początkowej kopii zapasowej. Przyrostowe kopie zapasowe są małe i łatwo są transferowane za pośrednictwem sieci.

9. Wybrać podczas sprawdzania spójności do uruchomienia, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    Serwer kopii zapasowej systemu Azure przeprowadza sprawdzanie spójności na integralność punktu kopii zapasowej. Serwer kopii zapasowej systemu Azure oblicza sumę kontrolną pliku kopii zapasowej na serwerze produkcyjnym (SQL Server maszyny w tym scenariuszu) i kopia zapasowa danych dla tego pliku. Jeśli występuje konflikt, zakłada się, że plik kopii zapasowej na serwerze kopii zapasowej Azure jest uszkodzony. Serwer kopii zapasowej systemu Azure rectifies kopię zapasową danych, wysyłając bloki odpowiadający błąd sumy kontrolnej. Ponieważ sprawdzenie spójności intensywnie wydajności, można planować Sprawdzanie spójności lub automatycznego uruchamiania.

10. Do określania ochrony źródeł danych w trybie online, wybierz bazy danych mają być chronione na platformie Azure oraz kliknij **dalej**.

    ![Wybierz źródła danych](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Wybierz harmonogramy tworzenia kopii zapasowej i zasady przechowywania, które wskazują zasady organizacji.

    ![Harmonogram i przechowywania](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są przyjmowane raz dziennie na 12:00 a 20: 00 (dolnej części ekranu)

    > [!NOTE]
    > Jest dobrą praktyką jest ma kilka punktów odzyskiwania krótkoterminowego na dysku, aby uzyskać szybkie odzyskiwanie. Te punkty odzyskiwania są używane do operacyjnych dotyczących odzyskiwania. Azure służy jako lokalizacji dobrej poza siedzibą firmy o wyższych SLA i gwarancji dostępności.
    >
    >

    **Najlepsze praktyki**: Jeśli zaplanowane tworzenie kopii zapasowych na platformie Azure, aby uruchomić po zakończeniu kopii zapasowych na dysku lokalnym, najnowszej kopii zapasowych na dysku zawsze są kopiowane do platformy Azure.

12. Wybierz harmonogram zasad przechowywania. Szczegółowe informacje dotyczące sposobu działania zasad przechowywania są udostępniane na [kopia zapasowa Azure używany do zastąpienia infrastruktury taśm artykuł](backup-azure-backup-cloud-as-tape.md).

    ![Zasady przechowywania](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są przyjmowane raz dziennie na 12:00 a 20: 00 (dolnej części ekranu) i są przechowywane przez 180 dni.
    * Tworzenie kopii zapasowej sobotę o godzinie 12:00 w dniu został zachowany na potrzeby 104 tygodni
    * Tworzenie kopii zapasowej ostatniego sobotę o godzinie 12:00 w dniu są przechowywane przez 60 miesięcy
    * Tworzenie kopii zapasowej Ostatnia sobota marca, 12:00 w dniu jest zachowywana 10 lat
13. Kliknij przycisk **dalej** i wybierz odpowiednią opcję w celu przekazania początkowa kopia zapasowa Azure. Możesz wybrać **automatycznie przez sieć**

14. Po szczegółowe zasady w **Podsumowanie** kliknij **Utwórz grupę** ukończenia przepływu pracy. Możesz kliknąć **Zamknij** i monitorować postęp zadania monitorowania obszaru roboczego.

    ![Tworzenie grupy ochrony w toku](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Na żądanie kopii zapasowej bazy danych programu SQL Server
Poprzednie kroki tworzenia zasad tworzenia kopii zapasowej, "punkt odzyskiwania" jest tworzony tylko wtedy, gdy występuje pierwszej kopii zapasowej. Zamiast oczekiwania na zaczną działać harmonogramu, czynności wyzwalacza tworzenie odzyskiwania punktu ręcznie.

1. Poczekaj, aż stan grupy ochrony **OK** dla bazy danych przed utworzeniem punktu odzyskiwania.

    ![Członkowie grupy ochrony](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kliknij prawym przyciskiem myszy na bazę danych i wybierz **Utwórz punkt odzyskiwania**.

    ![Tworzenie punktu odzyskiwania Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Wybierz **ochrony w trybie Online** menu rozwijanego i kliknij przycisk **OK** można uruchomić tworzenia punktu odzyskiwania na platformie Azure.

    ![Utwórz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Wyświetlić postęp zadania w **monitorowanie** obszaru roboczego.

    ![Konsola monitorowania](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych programu SQL Server z platformy Azure
Poniższe kroki są wymagane do odzyskania jednostki chronionej (baza danych programu SQL Server) z platformy Azure.

1. Otwórz konsolę zarządzania serwerem kopii zapasowej Azure. Przejdź do **odzyskiwania** roboczym, w którym można zobaczyć Serwery chronione. Przeglądaj wymaganej bazy danych (w tym przypadku ReportServer$ MSDPM2012). Wybierz **odzyskanie** czas, który jest określony jako **Online** punktu.

    ![Wybierz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kliknij prawym przyciskiem myszy nazwę bazy danych, a następnie kliknij przycisk **odzyskać**.

    ![Odzyskiwanie z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS przedstawia szczegóły punktu odzyskiwania. Kliknij przycisk **Dalej**. Aby zastąpić bazy danych, wybierz typ odzyskiwania **Odzyskaj do oryginalnego wystąpienia programu SQL Server**. Kliknij przycisk **Dalej**.

    ![Odzyskaj do oryginalnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie MABS odzyskuje bazę danych do innego wystąpienia programu SQL Server lub do autonomicznej folderu sieciowego.

4. W **opcji odzyskiwania określ** ekranu, można wybrać opcje odzyskiwania takie jak ograniczanie wykorzystania przepustowości sieci do ograniczania przepustowości, używany przez odzyskiwania. Kliknij przycisk **Dalej**.

5. W **Podsumowanie** ekranu, zobacz wszystkie konfiguracje odzyskiwania dostarczony do tej pory. Kliknij przycisk **odzyskać**.

    Stan odzyskiwania zawiera bazy danych ma zostać przeprowadzone odzyskiwanie. Możesz kliknąć **zamknąć** aby zamknąć kreatora i wyświetlić informację o postępie w **monitorowanie** obszaru roboczego.

    ![Zainicjuj proces odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po ukończeniu odzyskiwania przywróconej bazy danych jest zgodny.

## <a name="next-steps"></a>Następne kroki

Zobacz [kopii zapasowej plików i aplikacji](backup-mabs-files-applications-azure-stack.md) artykułu.
Zobacz [SharePoint kopii zapasowej na stosie Azure](backup-mabs-sharepoint-azure-stack.md) artykułu.
