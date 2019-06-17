---
title: Usługa Azure Backup dla obciążeń SQL Server przy użyciu programu DPM
description: Wprowadzenie do tworzenia kopii zapasowych baz danych programu SQL Server za pomocą usługi Azure Backup
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: d7d94c7b238f8d413d8837c3c34468c6cd653fe3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60644138"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Twórz kopie zapasowe programu SQL Server na platformie Azure jako obciążenia programu DPM
W tym artykule poprowadzi Cię przez kroki konfiguracyjne do wykonania kopii zapasowej bazy danych programu SQL Server za pomocą usługi Azure Backup.

Aby utworzyć kopię zapasową bazy danych programu SQL Server na platformie Azure, potrzebne jest konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne tylko kilka minut. Aby uzyskać więcej informacji, zobacz [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/pricing/free-trial/).

Zarządzanie kopii zapasowej bazy danych programu SQL Server na platformę Azure i odzyskiwanie z platformy Azure obejmuje trzy kroki:

1. Tworzenie zasad kopii zapasowych w celu ochrony baz danych programu SQL Server na platformie Azure.
2. Tworzenie kopii zapasowych na żądanie na platformie Azure.
3. Odzyskiwanie bazy danych z platformy Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem
Przed rozpoczęciem upewnij się, że wszystkie [wymagania wstępne](backup-azure-dpm-introduction.md#prerequisites-and-limitations) narzędzia Kopia zapasowa Microsoft Azure do ochrony obciążeń zostały spełnione. Wymagania wstępne obejmują zadania, takie jak: Tworzenie magazynu kopii zapasowych, pobierając poświadczenia magazynu, zainstalowanie agenta usługi Azure Backup i rejestrowania serwera w magazynie.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Tworzenie zasad kopii zapasowych w celu ochrony baz danych programu SQL Server na platformie Azure
1. Na serwerze programu DPM kliknij **ochrony** obszaru roboczego.
2. Na wstążce narzędzi kliknij polecenie **New** do tworzenia nowej grupy ochrony.

    ![Utwórz grupę ochrony](./media/backup-azure-backup-sql/protection-group.png)
3. Program DPM wyświetla na ekranie startowym ze wskazówkami na temat tworzenia **grupy ochrony**. Kliknij przycisk **Dalej**.
4. Wybierz **serwerów**.

    ![Wybierz typ grupy ochrony — "Serwery"](./media/backup-azure-backup-sql/pg-servers.png)
5. Rozwiń węzeł maszyna programu SQL Server, gdzie można utworzyć kopię zapasową baz danych są obecne. Program DPM Wyświetla różnych źródeł danych, których można utworzyć kopię zapasową, z tego serwera. Rozwiń **wszystkie udziały SQL** i wybierz bazy danych (w tym przypadku Wybraliśmy ReportServer$ MSDPM2012 i ReportServer$ MSDPM2012TempDB) do wykonania kopii zapasowej. Kliknij przycisk **Dalej**.

    ![Wybierz bazy danych SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Podaj nazwę dla grupy ochrony, a następnie wybierz **chcę uzyskać ochronę online** pola wyboru.

    ![Metoda ochrony danych — krótkoterminowa dysku & Online dotyczącym platformy Azure](./media/backup-azure-backup-sql/pg-name.png)
7. W **Określ cele krótkoterminowe** ekranu, należy umieścić niezbędne dane wejściowe, aby utworzyć punktów kopii zapasowej na dysku.

    Tutaj widzimy, że **zakres przechowywania** ustawiono *5 dni*, **częstotliwość synchronizacji** jest ustawiona na jeden raz każdego *15 minut* czyli częstotliwość, w którym jest wykonywana kopia zapasowa. **Ekspresowa pełna kopia zapasowa** ustawiono *godzinach od 8:00*.

    ![Cele krótkoterminowe](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > O 8:00 (zgodnie z ekranu wprowadzania) punktu kopii zapasowej jest tworzony co dzień za transfer danych, który został zmodyfikowany z punktu kopii zapasowej 20:00:00 poprzedniego dnia. Ten proces jest nazywany **Express pełnej kopii zapasowej**. Podczas transakcji, które dzienniki są synchronizowane co 15 minut, jeśli konieczne jest odzyskanie bazy danych o 9:00 – punkt jest utworzony przez odtworzenie dzienniki z ostatnich express punktu pełnej kopii zapasowej (20: 00 w tym przypadku).
   >
   >

8. Kliknij przycisk **Dalej**

    Program DPM Wyświetla ogólną miejsca do magazynowania dostępne i potencjalne wykorzystanie miejsca na dysku.

    ![Przydział dysku](./media/backup-azure-backup-sql/pg-storage.png)

    Domyślnie program DPM tworzy jeden wolumin na źródło danych (bazy danych programu SQL Server), który jest używany dla początkowej kopii zapasowej. W ten sposób Menedżera dysków logicznych (LDM) ogranicza ochrony programu DPM ze źródłami danych 300 (baz danych programu SQL Server). Aby obejść to ograniczenie, wybierz **kolokuj dane w puli magazynów DPM**, opcja. Użycie tej opcji, program DPM używa jednego woluminu dla wielu źródeł danych, co pozwala chronić maksymalnie 2000 baz danych SQL w programie DPM.

    Jeśli **automatycznie rozszerzaj woluminy** opcja jest zaznaczona, program DPM może uwzględnić zwiększone wielkość kopii zapasowej wzrostem ilości danych w środowisku produkcyjnym. Jeśli **automatycznie rozszerzaj woluminy** opcja nie jest zaznaczone, program DPM ogranicza magazynu kopii zapasowych, używany do źródeł danych w grupie ochrony.
9. Administratorzy są podane wybór przesyłania początkowej kopii zapasowej ręcznie (funkcja wyłączona sieci), aby uniknąć przeciążenia przepustowość lub za pośrednictwem sieci. Można również skonfigurować czasu, jaką może się zdarzyć transferu początkowego. Kliknij przycisk **Dalej**.

    ![Metoda replikacji początkowej](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga przesyłanie całego źródła danych (bazy danych programu SQL Server) z serwera produkcyjnego (SQL Server machine) do serwera programu DPM. Te dane mogą być duże, a przepustowość przesyłania danych za pośrednictwem sieci może przekroczyć. Z tego powodu administratorzy mogą wybrać opcję transferu tworzenia początkowej kopii zapasowej: **Ręcznie** (przy użyciu nośnika wymiennego) aby uniknąć przeciążenia przepustowość lub **automatycznie przez sieć** (o określonej godzinie).

    Po zakończeniu tworzenia początkowej kopii zapasowej, pozostałe kopie zapasowe są przyrostowych kopii zapasowych w początkowej kopii zapasowej. Przyrostowe kopie zapasowe są małe i łatwo są przesyłane przez sieć.
10. Wybierz, kiedy chcesz kontrolę spójności, aby uruchomić, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    Program DPM może wykonywać spójności wyboru, aby sprawdzić integralność punktu kopii zapasowej. Obliczanie sumy kontrolnej pliku kopii zapasowej na serwerze produkcyjnym (SQL Server maszyny w tym scenariuszu) i kopia zapasowa danych dla tego pliku w programie DPM. W przypadku konfliktu zakłada się, że plik kopii zapasowej w programie DPM jest uszkodzony. Program DPM rectifies kopię zapasową danych, wysyłając bloki odpowiadający niezgodność sumy kontrolnej. Jak Kontrola spójności jest operacją intensywnie, Administratorzy mają możliwość Planowanie sprawdzania spójności lub uruchamiając go automatycznie.
11. Aby określić ochrony źródeł danych w trybie online, wybierz bazy danych mają być chronione na platformie Azure i kliknij przycisk **dalej**.

    ![Wybierz źródła danych](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Administratorzy mogą określić harmonogramy tworzenia kopii zapasowych i zasad przechowywania, spełniające ich zasadami organizacji.

    ![Harmonogram i okres przechowywania](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są pobierane raz dziennie, 12:00 PM i 20: 00 (dolnej części ekranu)

    > [!NOTE]
    > Jest dobrą praktyką ma kilka punkty odzyskiwania krótkoterminowego na dysku, szybkie odzyskiwanie. Te punkty odzyskiwania są używane do "Odzyskiwanie operacyjne". Platforma Azure służy jako lokalizacji poza siedzibą w dobrym o wyższych umowy SLA i gwarantowana dostępność.
    >
    >

    **Najlepsze rozwiązanie**: Upewnij się, że zaplanowane tworzenie kopii zapasowych platformy Azure po zakończeniu tworzenia kopii zapasowych dysk lokalny przy użyciu programu DPM. Dzięki temu najnowszej kopii zapasowej dysku do skopiowania na platformie Azure.

13. Wybierz harmonogram zasad przechowywania. Szczegółowe informacje o sposobie działania zasady przechowywania są podane na [użycia usługi Azure Backup do zastąpienia infrastruktury taśmy artykułu](backup-azure-backup-cloud-as-tape.md).

    ![Zasady przechowywania](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są wykonywane raz dziennie o 12:00 PM i 20: 00 (dolnej części ekranu) i są przechowywane przez 180 dni.
    * Kopia zapasowa w sobotę o godzinie 12:00 w dniu został zachowany na potrzeby 104 tygodni
    * Kopia zapasowa na ostatni sobotę o godzinie 12:00 w dniu są przechowywane przez 60 miesięcy
    * Kopia zapasowa w sobotę ostatniego marca o 12:00 są zachowywane przez okres 10 lat
14. Kliknij przycisk **dalej** i wybierz odpowiednią opcję przesyłania początkowa kopia zapasowa na platformie Azure. Możesz wybrać **automatycznie przez sieć** lub **kopii zapasowej Offline**.

    * **Automatycznie przez sieć** przesyła dane kopii zapasowej na platformie Azure zgodnie z harmonogramem wybrany do utworzenia kopii zapasowej.
    * Jak **kopii zapasowej Offline** działa zostało wyjaśnione w [workflow kopii zapasowej Offline w usłudze Azure Backup](backup-azure-backup-import-export.md).

    Wybierz mechanizm istotne przeniesienia wysyłać początkowa kopia zapasowa Azure i kliknij przycisk **dalej**.
15. Po przejrzeniu szczegółów zasad w **Podsumowanie** ekranu, kliknij przycisk na **Utwórz grupę** przycisk, aby dokończyć przepływu pracy. Możesz kliknąć pozycję **Zamknij** przycisk, a następnie monitorować postęp zadania w obszarze roboczym monitorowanie.

    ![Tworzenie grupy ochrony w toku](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Na żądanie kopii zapasowej bazy danych programu SQL Server
Podczas poprzednich kroków utworzono zasady tworzenia kopii zapasowych, "punkt odzyskiwania" jest tworzony tylko wtedy, gdy występuje pierwszej kopii zapasowej. Bez czekania harmonogram, który ma Nashville, kroki przedstawione poniżej wyzwalacza tworzenia odzyskiwania punktu ręcznie.

1. Zaczekaj, aż stan grupy ochrony jest wyświetlany **OK** dla bazy danych przed utworzeniem punktu odzyskiwania.

    ![Członkowie grupy ochrony](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kliknij prawym przyciskiem myszy bazę danych i wybierz pozycję **Tworzenie punktu odzyskiwania**.

    ![Tworzenie punktu odzyskiwania Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Wybierz **ochrony w trybie Online** menu rozwijane i kliknij przycisk **OK**. Spowoduje to uruchomienie tworzenia punktu odzyskiwania na platformie Azure.

    ![Utwórz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Można wyświetlić postęp zadania w **monitorowanie** obszar roboczy, gdzie można znaleźć w toku zadania, takie jak to przedstawiono w następnej ilustracji.

    ![Konsola monitorowania](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych programu SQL Server na platformie Azure
Poniższe kroki są wymagane do odzyskiwanie chronionej jednostki (baza danych programu SQL Server) z platformy Azure.

1. Otwórz konsolę zarządzania serwera programu DPM. Przejdź do **odzyskiwania** obszar roboczy, w którym można zobaczyć serwery kopii zapasowej za pomocą programu DPM. Przeglądaj wymaganej bazy danych (w tym przypadków ReportServer$ MSDPM2012). Wybierz **odzyskanie danych będzie** czas, który kończy się **Online**.

    ![Wybierz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kliknij prawym przyciskiem myszy nazwę bazy danych, a następnie kliknij przycisk **odzyskać**.

    ![Odzyskiwanie z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. Program DPM Wyświetla szczegóły punktu odzyskiwania. Kliknij przycisk **Dalej**. Aby zastąpić bazy danych, wybierz typ odzyskiwania **Odzyskaj do oryginalnego wystąpienia programu SQL Server**. Kliknij przycisk **Dalej**.

    ![Odzyskaj do oryginalnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie program DPM umożliwia odzyskiwanie bazy danych do innego wystąpienia programu SQL Server lub do folderu sieciowego autonomicznych.
4. W **opcji odzyskiwania określ** ekranu, można wybrać opcje odzyskiwania takich jak ograniczenie przepustowości ograniczania przepustowości, używane przez odzyskiwania. Kliknij przycisk **Dalej**.
5. W **Podsumowanie** ekranu, zobacz wszystkie konfiguracje odzyskiwania dostarczane do tej pory. Kliknij przycisk **odzyskać**.

    Stan odzyskiwania to odzyskiwanej bazy danych. Możesz kliknąć pozycję **Zamknij** aby zamknąć kreatora i wyświetlić postęp w **monitorowanie** obszaru roboczego.

    ![Zainicjuj proces odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po ukończeniu odzyskiwania przywróconej bazy danych jest spójne na poziomie aplikacji.

### <a name="next-steps"></a>Następne kroki:
• [Usługi azure Backup — często zadawane pytania](backup-azure-backup-faq.md)
