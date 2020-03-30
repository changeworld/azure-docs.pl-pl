---
title: Łącze zapasowe obciążeń programu SQL Server w usłudze Azure Stack
description: W tym artykule dowiesz się, jak skonfigurować program Microsoft Azure Backup Server (MABS) w celu ochrony baz danych programu SQL Server w usłudze Azure Stack.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 03211e1147f96429a8406c4c95654161ed2bf308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172310"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Łącze kopii zapasowej programu SQL Server w usłudze Azure Stack

Ten artykuł służy do konfigurowania programu Microsoft Azure Backup Server (MABS) w celu ochrony baz danych programu SQL Server w usłudze Azure Stack.

Zarządzanie tworzeniem kopii zapasowej bazy danych programu SQL Server na platformie Azure i odzyskiwaniem z platformy Azure obejmuje trzy kroki:

1. Tworzenie zasad tworzenia kopii zapasowych w celu ochrony baz danych programu SQL Server
2. Tworzenie kopii zapasowych na żądanie
3. Odzyskiwanie bazy danych z dysków i z platformy Azure

## <a name="before-you-start"></a>Przed rozpoczęciem

[Zainstaluj i przygotuj serwer kopii zapasowej platformy Azure](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Tworzenie zasad tworzenia kopii zapasowych w celu ochrony baz danych programu SQL Server na platformie Azure

1. W interfejsie użytkownika serwera kopii zapasowych platformy Azure kliknij obszar roboczy **ochrony.**

2. Na wstążce narzędzia kliknij przycisk **Nowy,** aby utworzyć nową grupę ochrony.

    ![Utwórz grupę ochrony](./media/backup-azure-backup-sql/protection-group.png)

    Usługa Azure Backup Server uruchamia kreatora grupy ochrony, który prowadzi do utworzenia **grupy ochrony**. Kliknij przycisk **alej**.

3. Na ekranie **Wybierz typ grupy ochrony** wybierz pozycję **Serwery**.

    ![Wybierz typ grupy ochrony — 'Serwery'](./media/backup-azure-backup-sql/pg-servers.png)

4. Na ekranie **Wybierz członków grupy** na liście Dostępne elementy członkowskie są wyświetlane różne źródła danych. Kliknij, **+** aby rozwinąć folder i wyświetlić podfoldery. Kliknij to pole wyboru, aby zaznaczyć element.

    ![Wybieranie bazy danych SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Wszystkie zaznaczone elementy pojawią się na liście Wybrani członkowie. Po wybraniu serwerów lub baz danych, które chcesz chronić, kliknij przycisk **Dalej**.

5. Na ekranie **Wybierz metodę ochrony danych** podaj nazwę grupy ochrony i zaznacz pole wyboru Ochrona online **chcę.**

    ![Metoda ochrony danych — krótkoterminowe & dysków platformy Azure online](./media/backup-azure-backup-sql/pg-name.png)

6. Na ekranie **Określanie celów krótkoterminowych uwzględnij** niezbędne dane wejściowe do tworzenia punktów kopii zapasowej na dysku, a następnie kliknij przycisk **Dalej**.

    W przykładzie **Zakres przechowywania** wynosi **5 dni**, Częstotliwość **synchronizacji** jest raz na **15 minut**, co jest częstotliwością tworzenia kopii zapasowej. **Express Full Backup** jest ustawiony na **20:00**.

    ![Cele krótkoterminowe](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > W pokazanym przykładzie o godzinie 20:00 każdego dnia tworzony jest punkt kopii zapasowej, przesyłając zmodyfikowane dane z punktu kopii zapasowej poprzedniego dnia o godzinie 20:00. Ten proces nosi nazwę **Express Full Backup**. Dzienniki transakcji są synchronizowane co 15 minut. Jeśli trzeba odzyskać bazę danych o godzinie 21:00, punkt jest tworzony z dzienników z ostatniego punktu ekspresowej pełnej kopii zapasowej (8PM w tym przypadku).
   >
   >

7. Na ekranie **Przeglądanie alokacji dysku** sprawdź ogólną ilość dostępnego miejsca do magazynowania i potencjalne miejsce na dysku. Kliknij przycisk **alej**.

8. W metodzie **Wybieranie tworzenia repliki**wybierz sposób tworzenia pierwszego punktu odzyskiwania. Początkową kopię zapasową można przenieść ręcznie (poza siecią), aby uniknąć przeciążenia przepustowości lub w sieci. Jeśli zdecydujesz się poczekać na przeniesienie pierwszej kopii zapasowej, możesz określić godzinę początkowego transferu. Kliknij przycisk **alej**.

    ![Metoda replikacji początkowej](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga przeniesienia całego źródła danych (bazy danych programu SQL Server) z serwera produkcyjnego (komputera SQL Server) do serwera kopii zapasowej platformy Azure. Te dane mogą być duże, a przesyłanie danych przez sieć może przekroczyć przepustowość. Z tego powodu można przenieść początkową kopię zapasową: **Ręcznie** (przy użyciu nośnika wymiennego), aby uniknąć przeciążenia przepustowości, lub **Automatycznie przez sieć** (w określonym czasie).

    Po zakończeniu początkowej kopii zapasowej pozostałe kopie zapasowe są przyrostowymi kopiami zapasowymi na początkowej kopii zapasowej. Przyrostowe kopie zapasowe wydają się być małe i są łatwo przesyłane przez sieć.

9. Wybierz, kiedy chcesz uruchomić sprawdzanie spójności, a następnie kliknij przycisk **Dalej**.

    ![Kontrola spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    Usługa Azure Backup Server sprawdza spójność integralności punktu kopii zapasowej. Usługa Azure Backup Server oblicza sumę kontrolną pliku kopii zapasowej na serwerze produkcyjnym (komputer SQL Server w tym scenariuszu) i dane kopii zapasowej dla tego pliku. Jeśli występuje konflikt, zakłada się, że plik kopii zapasowej na serwerze kopii zapasowej platformy Azure jest uszkodzony. Usługa Azure Backup Server koryguje dane kopii zapasowej, wysyłając bloki odpowiadające niezgodności sumy kontrolnej. Ponieważ kontrole spójności są wymagające dużej wydajności, można zaplanować sprawdzanie spójności lub uruchomić go automatycznie.

10. Aby określić ochronę online źródeł danych, wybierz bazy danych, które mają być chronione na platformie Azure, a następnie kliknij przycisk **Dalej**.

    ![Wybieranie źródeł danych](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Wybierz harmonogramy tworzenia kopii zapasowych i zasady przechowywania, które odpowiadają zasadom organizacji.

    ![Harmonogram i przechowywanie](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są przyjmowane raz dziennie o 12:00 i 20:00 (dolna część ekranu)

    > [!NOTE]
    > Dobrą praktyką jest mieć kilka krótkoterminowych punktów odzyskiwania na dysku, aby szybko odzyskać. Te punkty odzyskiwania są używane do odzyskiwania operacyjnego. Platforma Azure służy jako dobra lokalizacja poza siedzibą firmy z wyższymi łatWami i gwarantowaną dostępnością.
    >
    >

    **Najlepsze rozwiązanie:** Jeśli planujesz tworzenie kopii zapasowych na platformie Azure, aby rozpocząć po zakończeniu tworzenia kopii zapasowych na dysku lokalnym, najnowsze kopie zapasowe dysku są zawsze kopiowane na platformę Azure.

12. Wybierz harmonogram zasad przechowywania. Szczegółowe informacje na temat działania zasad przechowywania znajdują się w [artykule Użyj usługi Azure Backup w celu zastąpienia infrastruktury taśmowej.](backup-azure-backup-cloud-as-tape.md)

    ![Zasady przechowywania](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są przyjmowane raz dziennie o 12:00 i 20:00 (dolna część ekranu) i są przechowywane przez 180 dni.
    * Kopia zapasowa w sobotę o godz. jest zachowywany przez 104 tygodnie
    * Kopia zapasowa w ostatnią sobotę o godz. jest utrzymywana przez 60 miesięcy
    * Kopia zapasowa w ostatnią sobotę marca o godz. jest utrzymywana przez 10 lat
13. Kliknij **przycisk Dalej** i wybierz odpowiednią opcję przesyłania początkowej kopii zapasowej na platformę Azure. Możesz wybrać **opcję Automatycznie przez sieć**

14. Po zapoznaniu się ze szczegółami zasad na ekranie **Podsumowanie** kliknij pozycję **Utwórz grupę,** aby ukończyć przepływ pracy. W obszarze roboczym Monitorowanie można kliknąć przycisk **Zamknij** i monitorować postęp zadania.

    ![Tworzenie grupy ochrony w toku](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Tworzenie kopii zapasowej bazy danych programu SQL Server na żądanie

Podczas gdy poprzednie kroki tworzyli zasady tworzenia kopii zapasowych, "punkt odzyskiwania" jest tworzony tylko wtedy, gdy wystąpi pierwsza kopia zapasowa. Zamiast czekać na harmonogram kopać w, poniższe kroki wyzwalają tworzenie punktu odzyskiwania ręcznie.

1. Przed utworzeniem punktu odzyskiwania poczekaj, aż stan grupy ochrony będzie **wyświetlany w porządku** dla bazy danych.

    ![Członkowie grupy ochrony](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kliknij prawym przyciskiem myszy bazę danych i wybierz pozycję **Utwórz punkt odzyskiwania**.

    ![Tworzenie punktu odzyskiwania online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Wybierz **pozycję Ochrona online** w menu rozwijanym i kliknij przycisk **OK,** aby rozpocząć tworzenie punktu odzyskiwania na platformie Azure.

    ![Tworzenie punktu odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Służy do wyświetlania postępu zadania w obszarze roboczym **Monitorowanie.**

    ![Konsola do monitorowania](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych programu SQL Server z platformy Azure

Poniższe kroki są wymagane do odzyskania chronionej jednostki (bazy danych programu SQL Server) z platformy Azure.

1. Otwórz konsolę zarządzania serwerem kopii zapasowych platformy Azure. Przejdź do obszaru roboczego **odzyskiwania,** gdzie można wyświetlić chronione serwery. Przejrzyj wymaganą bazę danych (w tym przypadku ReportServer$MSDPM2012). Wybierz **odzyskiwanie od** czasu, który jest określony jako punkt **online.**

    ![Wybierz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kliknij prawym przyciskiem myszy nazwę bazy danych i kliknij polecenie **Odzyskaj**.

    ![Odzyskiwanie z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS pokazuje szczegóły punktu odzyskiwania. Kliknij przycisk **alej**. Aby zastąpić bazę danych, wybierz typ odzyskiwania **Recover to original instance of SQL Server**. Kliknij przycisk **alej**.

    ![Odzyskiwanie do pierwotnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie usługa MABS odzyskuje bazę danych do innego wystąpienia programu SQL Server lub do autonomicznego folderu sieciowego.

4. Na ekranie **Opcje określania odzyskiwania** można wybrać opcje odzyskiwania, takie jak ograniczanie przepustowości sieci, aby ograniczyć przepustowość używaną przez odzyskiwanie. Kliknij przycisk **alej**.

5. Na ekranie **Podsumowanie** zobaczysz wszystkie konfiguracje odzyskiwania dostarczone do tej pory. Kliknij **przycisk Odzyskaj**.

    Stan odzyskiwania pokazuje odzyskiwana bazę danych. Można kliknąć przycisk **Zamknij,** aby zamknąć kreatora i wyświetlić postęp w obszarze roboczym **Monitorowanie.**

    ![Inicjowanie procesu odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po zakończeniu odzyskiwania przywrócona baza danych jest spójna z aplikacją.

## <a name="next-steps"></a>Następne kroki

Zobacz [plik kopii zapasowej i](backup-mabs-files-applications-azure-stack.md) artykuł aplikacji.
Zobacz [artykuł dotyczący tworzenia kopii zapasowych programu SharePoint w usłudze Azure Stack.](backup-mabs-sharepoint-azure-stack.md)
