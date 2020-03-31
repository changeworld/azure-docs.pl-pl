---
title: Tworzenie kopii zapasowych programu SQL Server przy użyciu serwera kopii zapasowych platformy Azure
description: W tym artykule zapoznaj się z konfiguracją tworzenia kopii zapasowych baz danych programu SQL Server przy użyciu programu Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 4a4d4b7e70e2df0e014ea4b4d23027aa7c48f2fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505946"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Tworzenie kopii zapasowych programu SQL Server na platformie Azure przy użyciu usługi Azure Backup Server

Ten artykuł ułatwia konfigurowanie kopii zapasowych baz danych programu SQL Server przy użyciu programu Microsoft Azure Backup Server (MABS).

Aby zrobić kopii zapasowej bazy danych programu SQL Server i odzyskać ją z platformy Azure:

1. Tworzenie zasad tworzenia kopii zapasowych w celu ochrony baz danych programu SQL Server na platformie Azure.
1. Tworzenie kopii zapasowych na żądanie na platformie Azure.
1. Odzyskiwanie bazy danych na platformie Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem upewnij się, że [zainstalowano i przygotowano usługę Azure Backup Server](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych 

Aby chronić bazy danych programu SQL Server na platformie Azure, najpierw utwórz zasady tworzenia kopii zapasowych:

1. W programie Azure Backup Server wybierz obszar roboczy **ochrony.**
1. Wybierz **pozycję Nowy,** aby utworzyć grupę ochrony.

    ![Tworzenie grupy ochrony na serwerze kopii zapasowych platformy Azure](./media/backup-azure-backup-sql/protection-group.png)
1. Na stronie początkowej zapoznaj się ze wskazówkami dotyczącymi tworzenia grupy ochrony. Następnie wybierz **przycisk Dalej**.
1. Dla typu grupy ochrony wybierz pozycję **Serwery**.

    ![Wybierz typ grupy Ochrona serwerów](./media/backup-azure-backup-sql/pg-servers.png)
1. Rozwiń komputer programu SQL Server, na którym znajdują się bazy danych, których chcesz szesnastą. Zostaną wyświetlonych źródeł danych, których kopię zapasową można wyw. Rozwiń **rozwiń wszystkie udziały SQL,** a następnie wybierz bazy danych, których chcesz udostępnić. W tym przykładzie wybieramy ReportServer$MSDPM2012 i ReportServer$MSDPM2012TempDB. Wybierz **pozycję Dalej**.

    ![Wybieranie bazy danych programu SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Nazwij grupę ochrony, a następnie wybierz pozycję **Chcę chronić online**.

    ![Wybierz metodę ochrony danych — krótkoterminową ochronę dysku lub ochronę platformy Azure online](./media/backup-azure-backup-sql/pg-name.png)
1. Na stronie **Określanie celów krótkoterminowych** dołącz niezbędne dane wejściowe do tworzenia punktów kopii zapasowej na dysku.

    W tym przykładzie **zakres przechowywania** jest ustawiony na *5 dni*. Częstotliwość **synchronizacji** kopii zapasowej jest ustawiana na raz na *15 minut*. **Pełna kopia zapasowa Express** jest ustawiona na *20:00*.

    ![Konfigurowanie krótkoterminowych celów ochrony kopii zapasowych](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > W tym przykładzie punkt kopii zapasowej jest tworzony codziennie o godzinie 20:00. Dane, które zostały zmodyfikowane od poprzedniego dnia 8:00 PM punkt kopii zapasowej jest przesyłany. Ten proces nosi nazwę **Express Full Backup**. Mimo że dzienniki transakcji są synchronizowane co 15 minut, jeśli musimy odzyskać bazę danych o godzinie 21:00, punkt jest tworzony przez odtwarzanie dzienników z ostatniego ekspresowego pełnego punktu kopii zapasowej, który jest 8:00 PM w tym przykładzie.
   >
   >

1. Wybierz **pozycję Dalej**. MABS pokazuje ogólną dostępną przestrzeń dyskową. Pokazuje również potencjalne wykorzystanie miejsca na dysku.

    ![Konfigurowanie alokacji dysku w udziale mabs](./media/backup-azure-backup-sql/pg-storage.png)

    Domyślnie usługa MABS tworzy jeden wolumin na źródło danych (baza danych programu SQL Server). Wolumin jest używany do początkowej kopii zapasowej. W tej konfiguracji Menedżer dysków logicznych (LDM) ogranicza ochronę MABS do 300 źródeł danych (baz danych programu SQL Server). Aby obejść to ograniczenie, wybierz **opcję Co-locate data in DPM Storage Pool**. Jeśli używasz tej opcji, mabs używa jednego woluminu dla wielu źródeł danych. Ta konfiguracja umożliwia mabs do ochrony do 2000 baz danych programu SQL Server.

    Jeśli **wybierzesz Opcję Automatycznie powiększaj woluminy,** usługa MABS może uwzględnić zwiększony wolumin kopii zapasowej w miarę wzrostu danych produkcyjnych. Jeśli nie **wybierzesz opcji Automatycznie zwiększaj woluminy,** program MABS ograniczy magazyn kopii zapasowych do źródeł danych w grupie ochrony.
1. Jeśli jesteś administratorem, możesz przenieść tę początkową kopię zapasową **automatycznie przez sieć** i wybrać godzinę transferu. Możesz też ręcznie **przenieść** kopię zapasową. Następnie wybierz **przycisk Dalej**.

    ![Wybieranie metody tworzenia replik w ujmiju MABS](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga transferu całego źródła danych (bazy danych programu SQL Server). Dane kopii zapasowej są przenoszone z serwera produkcyjnego (sql server machine) do mabs. Jeśli ta kopia zapasowa jest duża, przesyłanie danych przez sieć może spowodować przeciążenie przepustowości. Z tego powodu administratorzy mogą ręcznie przesyłać początkową **kopię**zapasową za pomocą nośnika wymiennego. Lub mogą automatycznie przesyłać dane **za pośrednictwem sieci** w określonym czasie.

    Po zakończeniu początkowej kopii zapasowej kopie zapasowe są kontynuowane przyrostowo na początkowej kopii zapasowej. Przyrostowe kopie zapasowe wydają się być małe i są łatwo przesyłane przez sieć.
1. Wybierz, kiedy należy uruchomić sprawdzanie spójności. Następnie wybierz **przycisk Dalej**.

    ![Wybierz, kiedy należy uruchomić sprawdzanie spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    Usługa MABS może uruchomić sprawdzanie spójności integralności punktu kopii zapasowej. Oblicza sumę kontrolną pliku kopii zapasowej na serwerze produkcyjnym (komputer SQL Server w tym przykładzie) i dane kopii zapasowej dla tego pliku w programie MABS. Jeśli czek stwierdzi konflikt, zakłada się, że plik kopii zapasowej w systemie MABS jest uszkodzony. Usługa MABS naprawia dane kopii zapasowej, wysyłając bloki, które odpowiadają niezgodności sumy kontrolnej. Ponieważ sprawdzanie spójności jest operacją wymagającą dużej wydajności, administratorzy mogą zaplanować sprawdzanie spójności lub uruchomić ją automatycznie.
1. Wybierz źródła danych, które mają być chronione na platformie Azure. Następnie wybierz **przycisk Dalej**.

    ![Wybieranie źródeł danych do ochrony na platformie Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Jeśli jesteś administratorem, możesz wybrać harmonogramy tworzenia kopii zapasowych i zasady przechowywania, które odpowiadają zasadom organizacji.

    ![Wybieranie harmonogramów i zasad przechowywania](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są przyjmowane codziennie o 12:00 i 20:00.

    > [!TIP]
    > Aby szybko odzyskać, zachowaj kilka krótkoterminowych punktów odzyskiwania na dysku. Te punkty odzyskiwania są używane do odzyskiwania operacyjnego. Platforma Azure służy jako dobra lokalizacja poza siedzibą firmy, zapewniając wyższe łata i gwarantowaną dostępność.
    >
    > Użyj Menedżera ochrony danych (DPM), aby zaplanować tworzenie kopii zapasowych platformy Azure po zakończeniu tworzenia kopii zapasowych na dysku lokalnym. Zgodnie z tą praktyką najnowsza kopia zapasowa dysku jest kopiowana na platformę Azure.
    >


1. Wybierz harmonogram zasad przechowywania. Aby uzyskać więcej informacji na temat działania zasad przechowywania, zobacz [Używanie usługi Azure Backup do zastępowania infrastruktury taśm](backup-azure-backup-cloud-as-tape.md).

    ![Wybieranie zasad przechowywania w ujmiju MABS](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są przyjmowane codziennie o 12:00 i 20:00. Są przechowywane przez 180 dni.
    * Kopia zapasowa w sobotę o godzinie 12:00 jest przechowywana przez 104 tygodnie.
    * Kopia zapasowa z ostatniej soboty miesiąca o godzinie 12:00 jest przechowywana przez 60 miesięcy.
    * Kopia zapasowa z ostatniej soboty marca o godzinie 12:00 jest przechowywana przez 10 lat.

    Po wybraniu zasady przechowywania wybierz pozycję **Dalej**.
1. Wybierz sposób przenoszenia początkowej kopii zapasowej na platformę Azure.

    * Opcja **Automatycznie za pośrednictwem sieci** jest zgodna z harmonogramem tworzenia kopii zapasowych, aby przenieść dane na platformę Azure.
    * Aby uzyskać więcej informacji na temat **tworzenia kopii zapasowych w trybie offline,** zobacz [Omówienie kopii zapasowej w trybie offline](offline-backup-overview.md).

    Po wybraniu mechanizmu transferu wybierz przycisk **Dalej**.
1. Na stronie **Podsumowanie** przejrzyj szczegóły zasad. Następnie wybierz pozycję **Utwórz grupę**. Można wybrać **zamknij** i obserwować postęp zadania w obszarze roboczym **Monitorowanie.**

    ![Postępy w tworzeniu grupy ochrony](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Tworzenie kopii zapasowych bazy danych programu SQL Server na żądanie

Punkt odzyskiwania jest tworzony, gdy wystąpi pierwsza kopia zapasowa. Zamiast czekać na uruchomienie harmonogramu, można ręcznie wyzwolić utworzenie punktu odzyskiwania:

1. W grupie ochrony upewnij się, że stan bazy danych jest **prawidłowy**.

    ![Grupa ochrony przedstawiająca stan bazy danych](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Utwórz punkt odzyskiwania**.

    ![Wybieranie utworzenia punktu odzyskiwania online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Z menu rozwijanego wybierz pozycję **Ochrona online**. Następnie wybierz **przycisk OK,** aby rozpocząć tworzenie punktu odzyskiwania na platformie Azure.

    ![Rozpoczynanie tworzenia punktu odzyskiwania na platformie Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Postęp zadania można wyświetlić w obszarze roboczym **Monitorowanie.** 

    ![Wyświetlanie postępu zadania w konsoli Monitorowanie](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych programu SQL Server z platformy Azure

Aby odzyskać chronione jednostki, takie jak baza danych programu SQL Server, z platformy Azure:

1. Otwórz konsolę zarządzania serwerem programu DPM. Przejdź do obszaru roboczego **odzyskiwania,** aby wyświetlić serwery, których program DPM utworzy utwory kopii zapasowej. Wybierz bazę danych (w tym przykładzie ReportServer$MSDPM2012). Wybierz **czas odzyskiwania,** który kończy się na **online**.

    ![Wybieranie punktu odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Kliknij prawym przyciskiem myszy nazwę bazy danych i wybierz polecenie **Odzyskaj**.

    ![Odzyskiwanie bazy danych z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. Program DPM pokazuje szczegóły punktu odzyskiwania. Wybierz **pozycję Dalej**. Aby zastąpić bazę danych, wybierz typ odzyskiwania **Recover to original instance of SQL Server**. Następnie wybierz **przycisk Dalej**.

    ![Odzyskiwanie bazy danych do jej pierwotnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie program DPM umożliwia odzyskiwanie bazy danych do innego wystąpienia programu SQL Server lub do autonomicznego folderu sieciowego.
1. Na stronie **Określ opcje odzyskiwania** można wybrać opcje odzyskiwania. Na przykład można wybrać **ograniczenie użycia przepustowości sieci,** aby ograniczyć przepustowość używana przez odzyskiwanie. Następnie wybierz **przycisk Dalej**.
1. Na stronie **Podsumowanie** zostanie wyświetlona bieżąca konfiguracja odzyskiwania. Wybierz **pozycję Odzyskaj**.

    Stan odzyskiwania pokazuje odzyskiwana bazę danych. Można wybrać **zamknij,** aby zamknąć kreatora i wyświetlić postęp w obszarze roboczym **Monitorowanie.**

    ![Rozpoczynanie procesu odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po zakończeniu odzyskiwania przywrócona baza danych jest zgodna z aplikacją.

### <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące kopii zapasowej platformy Azure](backup-azure-backup-faq.md).
