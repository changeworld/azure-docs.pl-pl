---
title: Usługa Azure Backup dla obciążeń SQL Server przy użyciu programu DPM
description: Wprowadzenie do tworzenia kopii zapasowych baz danych programu SQL Server przy użyciu usługi Kopia zapasowa Azure
services: backup
author: adigan
manager: Nkolli
ms.service: backup
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: adigan
ms.openlocfilehash: cebbe532b5d1b13588604c61ac10bf3c56a85e07
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604968"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Wykonywanie kopii zapasowej serwera SQL na platformie Azure jako obciążenia programu DPM
W tym artykule poprowadzi Cię przez kroki konfiguracyjne do wykonania kopii zapasowej bazy danych programu SQL Server przy użyciu usługi Kopia zapasowa Azure.

Aby utworzyć kopię zapasową bazy danych programu SQL Server na platformie Azure, potrzebne jest konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w tylko kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

Zarządzanie kopii zapasowej bazy danych programu SQL Server na platformie Azure oraz odzyskiwania z platformy Azure obejmuje trzy kroki:

1. Tworzenie zasad tworzenia kopii zapasowej w celu ochrony baz danych serwera SQL na platformie Azure.
2. Tworzenie kopii zapasowych na żądanie w systemie Azure.
3. Odzyskiwanie bazy danych z platformy Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem
Przed rozpoczęciem upewnij się, że wszystkie [wymagania wstępne](backup-azure-dpm-introduction.md#prerequisites) narzędzia Kopia zapasowa Microsoft Azure do ochrony obciążeń zostały spełnione. Wymagania wstępne obejmują zadania, takie jak: Tworzenie magazynu kopii zapasowych, pobrać poświadczenia magazynu instalowania agenta kopii zapasowej Azure i rejestrowanie serwera w magazynie.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Tworzenie zasad tworzenia kopii zapasowej w celu ochrony baz danych serwera SQL na platformie Azure
1. Na serwerze programu DPM, kliknij przycisk **ochrony** obszaru roboczego.
2. Na wstążce narzędzi kliknij **nowy** do tworzenia nowej grupy ochrony.

    ![Tworzenie grupy ochrony](./media/backup-azure-backup-sql/protection-group.png)
3. Program DPM wyświetla ekranu startowego z wskazówki dotyczące tworzenia **grupy ochrony**. Kliknij przycisk **Dalej**.
4. Wybierz **serwerów**.

    ![Wybierz typ grupy ochrony — "Serwery"](./media/backup-azure-backup-sql/pg-servers.png)
5. Rozwiń węzeł maszyna programu SQL Server, gdzie znajdują się bazy danych do wykonania kopii zapasowej. Program DPM Wyświetla różnych źródeł danych, których kopię zapasową można z tego serwera. Rozwiń węzeł **wszystkie udziały SQL** i wybierz bazy danych (w tym przypadku Wybraliśmy ReportServer$ MSDPM2012 i ReportServer$ MSDPM2012TempDB) do wykonania kopii zapasowej. Kliknij przycisk **Dalej**.

    ![Wybierz bazy danych SQL.](./media/backup-azure-backup-sql/pg-databases.png)
6. Podaj nazwę grupy ochrony i wybierz **chcę uzyskać ochronę online** wyboru.

    ![Metodę ochrony danych - dysku krótkoterminowa & Online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. W **Określ cele krótkoterminowe** ekranu, zawierają niezbędne dane wejściowe, aby utworzyć punktów kopii zapasowej na dysku.

    Tutaj przedstawiono który **zakres przechowywania** ustawiono *5 dni*, **częstotliwość synchronizacji** ma ustawioną wartość raz co *15 minut* czyli częstotliwości, w którym wykonywana jest kopia zapasowa. **Ekspresowa pełna kopia zapasowa** ustawiono *godzinach od 8:00*.

    ![Cele krótkoterminowe](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Na 8:00 PM (zgodnie z danych wejściowych ekranu) punktu kopii zapasowej jest tworzony codziennie przez transferu danych, który został zmodyfikowany od punktu kopii zapasowej poprzedniego dnia 8:00 PM. Ten proces jest nazywany **Express pełnej kopii zapasowej**. Podczas transakcji, dzienniki są synchronizowane co 15 minut Jeśli konieczne jest odzyskanie bazy danych na 21:00:00 – punkt jest stworzony przez powtarzanie dzienniki w ciągu ostatnich express punktu pełnej kopii zapasowej (20: 00 w tym przypadku).
   >
   >

8. Kliknij przycisk **Dalej**

    Program DPM Wyświetla ogólną miejsca do magazynowania dostępne i potencjalne wykorzystanie miejsca na dysku.

    ![Przydział dysku](./media/backup-azure-backup-sql/pg-storage.png)

    Domyślnie program DPM tworzy jednego woluminu dla źródła danych (bazy danych programu SQL Server), który służy do początkowej kopii zapasowej. W ten sposób Menedżer dysków logicznych (LDM) ogranicza ochrony programu DPM ze źródłami danych 300 (baz danych programu SQL Server). Aby obejść to ograniczenie, wybierz **kolokuj dane w puli magazynu DPM**, opcja. Użycie tej opcji, program DPM używa pojedynczy wolumin wiele źródeł danych, dzięki czemu program DPM można chronić maksymalnie 2000 baz danych.

    Jeśli **automatycznie rozszerzaj woluminy** opcja jest zaznaczona, program DPM może konta zwiększenia woluminu kopii zapasowej, wraz z rozwojem danych produkcyjnych. Jeśli **automatycznie rozszerzaj woluminy** nie wybrano opcji, program DPM ogranicza magazynu kopii zapasowych, używany do źródeł danych w grupie ochrony.
9. Umożliwia nadanie administratorom wybór transferowania tej początkowej kopii zapasowej ręcznie (Wyłącz sieciowych) aby uniknąć przeciążenia przepustowości lub za pośrednictwem sieci. Można również skonfigurować czasu, jaką początkowy transfer może się zdarzyć. Kliknij przycisk **Dalej**.

    ![Replikacja początkowa — metoda](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga przesyłanie całego źródła danych (bazy danych programu SQL Server) z serwera produkcyjnego (komputer serwera SQL) do serwera DPM. Te dane mogą być duże i przesyłania danych za pośrednictwem sieci może przekroczyć przepustowości. Z tego powodu, Administratorzy mogą wybrać opcję transferu początkowa kopia zapasowa: **ręcznie** (przy użyciu nośnika wymiennego) aby uniknąć przeciążenia przepustowości lub **automatycznie przez sieć** (w określonym czasie).

    Po zakończeniu tworzenia początkowej kopii zapasowej pozostałe kopie zapasowe są przyrostowych kopii zapasowych w początkowej kopii zapasowej. Przyrostowe kopie zapasowe są małe i łatwo są transferowane za pośrednictwem sieci.
10. Wybrać podczas sprawdzania spójności do uruchomienia, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    Program DPM może wykonywać spójności wyboru, aby sprawdzić integralność punktu kopii zapasowej. Obliczanie sumy kontrolnej pliku kopii zapasowej na serwerze produkcyjnym (SQL Server maszyny w tym scenariuszu) i kopia zapasowa danych dla tego pliku w programu DPM. W przypadku konfliktu zakłada się, że plik kopii zapasowej w lokalizacji DPM jest uszkodzony. Program DPM rectifies kopię zapasową danych, wysyłając bloki odpowiadający błąd sumy kontrolnej. Sprawdzanie spójności jest operacją intensywnie wydajności, Administratorzy mają możliwość Planowanie sprawdzania spójności lub uruchomić je automatycznie.
11. Do określania ochrony źródeł danych w trybie online, wybierz bazy danych mają być chronione na platformie Azure oraz kliknij **dalej**.

    ![Wybierz źródła danych](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Administratorzy mogą określić harmonogramy tworzenia kopii zapasowej i zasady przechowywania, które wskazują ich zasady organizacji.

    ![Harmonogram i przechowywania](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są przyjmowane raz dziennie na 12:00 a 20: 00 (dolnej części ekranu)

    > [!NOTE]
    > Jest dobrą praktyką jest ma kilka punktów odzyskiwania krótkoterminowego na dysku, aby uzyskać szybkie odzyskiwanie. Te punkty odzyskiwania są używane do "operacyjnych dotyczących odzyskiwania". Azure służy jako lokalizacji dobrej poza siedzibą firmy o wyższych SLA i gwarancji dostępności.
    >
    >

    **Najlepsze praktyki**: Upewnij się, że zaplanowane kopie zapasowe Azure po ukończeniu tworzenia kopii zapasowych na dysku lokalnym za pomocą programu DPM. Dzięki temu najnowszej kopii zapasowej do skopiowania na platformie Azure.

13. Wybierz harmonogram zasad przechowywania. Szczegółowe informacje dotyczące sposobu działania zasad przechowywania są udostępniane na [kopia zapasowa Azure używany do zastąpienia infrastruktury taśm artykuł](backup-azure-backup-cloud-as-tape.md).

    ![Zasady przechowywania](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są przyjmowane raz dziennie na 12:00 a 20: 00 (dolnej części ekranu) i są przechowywane przez 180 dni.
    * Tworzenie kopii zapasowej sobotę o godzinie 12:00 w dniu został zachowany na potrzeby 104 tygodni
    * Tworzenie kopii zapasowej ostatniego sobotę o godzinie 12:00 w dniu są przechowywane przez 60 miesięcy
    * Tworzenie kopii zapasowej Ostatnia sobota marca, 12:00 w dniu jest zachowywana 10 lat
14. Kliknij przycisk **dalej** i wybierz odpowiednią opcję w celu przekazania początkowa kopia zapasowa Azure. Możesz wybrać **automatycznie przez sieć** lub **w trybie Offline z kopii zapasowej**.

    * **Automatycznie przez sieć** przesyła dane kopii zapasowej Azure zgodnie z harmonogramem wybranym dla kopii zapasowej.
    * Jak **w trybie Offline z kopii zapasowej** works opisanej w [przepływu pracy w trybie Offline z kopii zapasowej w programie Kopia zapasowa Azure](backup-azure-backup-import-export.md).

    Wybierz mechanizm transferu odpowiednie do wysłania początkowej kopii zapasowej do platformy Azure i kliknij przycisk **dalej**.
15. Po szczegółowe zasady w **Podsumowanie** ekranu, kliknij pozycję **Utwórz grupę** przycisk, aby zakończyć przepływ pracy. Możesz kliknąć **Zamknij** przycisk i monitorować postęp zadania monitorowania obszaru roboczego.

    ![Tworzenie grupy ochrony w toku](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Na żądanie kopii zapasowej bazy danych programu SQL Server
Poprzednie kroki tworzenia zasad tworzenia kopii zapasowej, "punkt odzyskiwania" jest tworzony tylko wtedy, gdy występuje pierwszej kopii zapasowej. Zamiast oczekiwania na zaczną działać harmonogramu, czynności wyzwalacza tworzenie odzyskiwania punktu ręcznie.

1. Poczekaj, aż stan grupy ochrony **OK** dla bazy danych przed utworzeniem punktu odzyskiwania.

    ![Członkowie grupy ochrony](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kliknij prawym przyciskiem myszy na bazę danych i wybierz **Utwórz punkt odzyskiwania**.

    ![Tworzenie punktu odzyskiwania Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Wybierz **ochrony w trybie Online** menu rozwijanego i kliknij przycisk **OK**. Spowoduje to uruchomienie tworzenia punktu odzyskiwania na platformie Azure.

    ![Utwórz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Możesz wyświetlić postęp zadania w **monitorowanie** zadania obszaru roboczego, gdzie można znaleźć w toku, tak jak to przedstawiono następny rysunek.

    ![Konsola monitorowania](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych programu SQL Server z platformy Azure
Poniższe kroki są wymagane do odzyskania jednostki chronionej (baza danych programu SQL Server) z platformy Azure.

1. Otwórz konsolę zarządzania serwera programu DPM. Przejdź do **odzyskiwania** roboczym, w którym można zobaczyć serwery kopii zapasowej przez program DPM. Przeglądaj wymaganej bazy danych (w tym przypadku ReportServer$ MSDPM2012). Wybierz **odzyskanie** czas, który kończy się **Online**.

    ![Wybierz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kliknij prawym przyciskiem myszy nazwę bazy danych, a następnie kliknij przycisk **odzyskać**.

    ![Odzyskiwanie z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. Program DPM Wyświetla szczegóły punktu odzyskiwania. Kliknij przycisk **Dalej**. Aby zastąpić bazy danych, wybierz typ odzyskiwania **Odzyskaj do oryginalnego wystąpienia programu SQL Server**. Kliknij przycisk **Dalej**.

    ![Odzyskaj do oryginalnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie program DPM umożliwia odzyskiwanie bazy danych do innego wystąpienia programu SQL Server lub do autonomicznej folderu sieciowego.
4. W **opcji odzyskiwania określ** ekranu, można wybrać opcje odzyskiwania takie jak ograniczanie wykorzystania przepustowości sieci do ograniczania przepustowości, używany przez odzyskiwania. Kliknij przycisk **Dalej**.
5. W **Podsumowanie** ekranu, zobacz wszystkie konfiguracje odzyskiwania dostarczony do tej pory. Kliknij przycisk **odzyskać**.

    Stan odzyskiwania zawiera bazy danych ma zostać przeprowadzone odzyskiwanie. Możesz kliknąć **zamknąć** aby zamknąć kreatora i wyświetlić informację o postępie w **monitorowanie** obszaru roboczego.

    ![Zainicjuj proces odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po ukończeniu odzyskiwania przywróconej bazy danych jest zgodny.

### <a name="next-steps"></a>Następne kroki:
• [Azure często zadawane pytania dotyczące tworzenia kopii zapasowej](backup-azure-backup-faq.md)
