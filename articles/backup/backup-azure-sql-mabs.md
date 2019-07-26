---
title: Azure Backup SQL Server obciążeń przy użyciu Azure Backup Server
description: Wprowadzenie do tworzenia kopii zapasowych baz danych SQL Server przy użyciu Azure Backup Server
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 0aff75e37ec8184b6c23ff00cbf61e5cbe045b5f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465440"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Tworzenie kopii zapasowych SQL Server na platformie Azure przy użyciu Azure Backup Server
Ten artykuł przeprowadzi Cię przez kroki konfiguracji tworzenia kopii zapasowych baz danych SQL Server przy użyciu programu Microsoft Azure Backup Server (serwera usługi MAB).

Zarządzanie kopią zapasową bazy danych SQL Server na platformie Azure i odzyskiwanie z platformy Azure obejmuje trzy kroki:

1. Tworzenie zasad tworzenia kopii zapasowych w celu ochrony baz danych SQL Server na platformie Azure.
2. Tworzenie kopii zapasowych na żądanie na platformie Azure.
3. Odzyskaj bazę danych z platformy Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem
Przed rozpoczęciem upewnij się, że zainstalowano [i przygotowano Azure Backup Server](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Tworzenie zasad tworzenia kopii zapasowych w celu ochrony baz danych SQL Server na platformie Azure
1. W interfejsie użytkownika Azure Backup Server kliknij obszar roboczy **Ochrona** .
2. Na Wstążce narzędzi kliknij przycisk **New (nowy** ), aby utworzyć nową grupę ochrony.

    ![Utwórz grupę ochrony](./media/backup-azure-backup-sql/protection-group.png)
3. SERWERA usługi MAB wyświetla ekran startowy z wskazówkami dotyczącymi tworzenia **grupy ochrony**. Kliknij przycisk **Dalej**.
4. Wybierz pozycję **serwery**.

    ![Wybierz typ grupy ochrony — "serwery"](./media/backup-azure-backup-sql/pg-servers.png)
5. Rozwiń maszynę SQL Server, na której znajdują się bazy danych, których kopia zapasowa ma zostać utworzona. SERWERA usługi MAB pokazuje różne źródła danych, których kopię zapasową można utworzyć z tego serwera. Rozwiń **wszystkie udziały SQL** i wybierz bazy danych (w tym przypadku została wybrana opcja REPORTSERVER $ MSDPM2012 i reportserver $ MSDPM2012TempDB), aby utworzyć kopię zapasową. Kliknij przycisk **Dalej**.

    ![Wybierz bazę danych SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Podaj nazwę grupy ochrony i zaznacz pole wyboru Chcę uzyskać **ochronę online** .

    ![Metoda ochrony danych — dysk krótkoterminowy & online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. Na ekranie **Określ cele krótkoterminowe** Uwzględnij niezbędne dane wejściowe, aby utworzyć punkty kopii zapasowej na dysku.

    W tym miejscu widać, że **Zakres przechowywania** jest ustawiony na *5 dni*, **częstotliwość synchronizacji** jest ustawiona na co *15 minut* , która jest częstotliwością wykonywania kopii zapasowej. **Ekspresowa pełna kopia zapasowa** jest ustawiona na *8:00 P. M*.

    ![Cele krótkoterminowe](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > O godzinie 8:00 PM (zgodnie z danymi wejściowymi ekranu) punkt tworzenia kopii zapasowej jest tworzony codziennie przez przeniesienie danych, które zostały zmodyfikowane z punktu ostatniej kopii zapasowej 8:00 PM. Ten proces jest nazywany **ekspresową pełną kopią zapasową**. Dzienniki transakcji są synchronizowane co 15 minut, jeśli istnieje potrzeba odzyskania bazy danych o 9:00 PM — następnie punkt jest tworzony przez odtwarzanie dzienników z ostatniego ekspresowej pełnej kopii zapasowej (8pm w tym przypadku).
   >
   >

8. Kliknij przycisk **Dalej**

    SERWERA usługi MAB pokazuje ogólne dostępne miejsce do magazynowania oraz potencjalne użycie miejsca na dysku.

    ![Przydział dysku](./media/backup-azure-backup-sql/pg-storage.png)

    Domyślnie serwera usługi MAB tworzy jeden wolumin na źródło danych (SQL Server Database), które jest używane do początkowej kopii zapasowej. Korzystając z tego podejścia, Menedżer dysków logicznych (LDM) ogranicza ochronę serwera usługi MAB do źródeł danych 300 (bazy danych SQL Server). Aby obejść to ograniczenie, wybierz opcję **wspólne lokalizowanie danych w puli magazynów DPM**. W przypadku użycia tej opcji serwera usługi MAB korzysta z pojedynczego woluminu dla wielu źródeł danych, co umożliwia serwera usługi MAB ochronę do 2000 baz danych SQL.

    W przypadku wybrania opcji **automatycznie Zwiększ liczbę woluminów** serwera usługi MAB może obsłużyć zwiększony wolumin kopii zapasowej w miarę rozwoju danych produkcyjnych. Jeśli opcja **automatycznie rośnie woluminy** nie jest ZAZNACZONA, serwera usługi MAB ogranicza magazyn kopii zapasowych używany do źródeł danych w grupie ochrony.
9. Administratorzy mogą wybrać transfer tej początkowej kopii zapasowej ręcznie (poza siecią), aby uniknąć przeciążenia przepustowości lub sieci. Mogą również skonfigurować czas, w którym może się zdarzyć przeniesienie początkowe. Kliknij przycisk **Dalej**.

    ![Metoda replikacji początkowej](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga przeniesienia całego źródła danych (SQL Server Database) z serwera produkcyjnego (SQL Server Machine) do serwera usługi MAB. Te dane mogą być duże, a transfer danych przez sieć może przekroczyć przepustowość. Z tego powodu Administratorzy mogą zdecydować się na przeniesienie początkowej kopii zapasowej: **Ręcznie** (przy użyciu nośnika wymiennego), aby uniknąć przeciążenia przepustowości lub **automatycznie za pośrednictwem sieci** (w określonym czasie).

    Po zakończeniu początkowej kopii zapasowej pozostałe kopie zapasowe są przyrostowymi kopiami zapasowymi na początkowej kopii zapasowej. Przyrostowe kopie zapasowe mają być małe i łatwo przesyłane przez sieć.
10. Wybierz, kiedy ma być uruchamiane sprawdzanie spójności, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    SERWERA usługi MAB może przeprowadzić kontrolę spójności, aby sprawdzić integralność punktu kopii zapasowej. Oblicza sumę kontrolną pliku kopii zapasowej na serwerze produkcyjnym (SQL Server komputerze w tym scenariuszu) oraz dane kopii zapasowej dla tego pliku w serwera usługi MAB. W przypadku konfliktu zakłada się, że plik kopii zapasowej w serwera usługi MAB jest uszkodzony. SERWERA usługi MAB zastępuje dane kopii zapasowej, wysyłając bloki odpowiadające niezgodności sumy kontrolnej. Gdy sprawdzanie spójności jest operacją intensywnie wykorzystującą wydajność, Administratorzy mogą wybrać opcję planowania kontroli spójności lub automatycznego uruchamiania.
11. Aby określić ochronę w trybie online dla źródeł danych, wybierz te, które mają być chronione na platformie Azure, a następnie kliknij przycisk **dalej**.

    ![Wybierz źródła danych](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Administratorzy mogą wybrać harmonogramy kopii zapasowych i zasady przechowywania odpowiednie dla zasad organizacji.

    ![Harmonogram i przechowywanie](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są wykonywane raz dziennie o godzinie 12:00 PM i 8 PM (Dolna część ekranu).

    > [!NOTE]
    > Dobrym sposobem jest posiadanie kilku krótkoterminowych punktów odzyskiwania na dysku, co pozwala na szybkie odzyskiwanie. Te punkty odzyskiwania są używane do "odzyskiwania operacyjnego". Platforma Azure służy jako dobra lokalizacja poza siedzibą firmy z wyższym umowy SLA i gwarantowaną dostępnością.
    >
    >

    **Najlepsze rozwiązanie**: Upewnij się, że kopie zapasowe platformy Azure są zaplanowane po zakończeniu wykonywania kopii zapasowych na dysku lokalnym przy użyciu programu DPM. Dzięki temu najnowsza kopia zapasowa dysku będzie kopiowana na platformę Azure.

13. Wybierz harmonogram zasad przechowywania. Szczegółowe informacje na temat sposobu działania zasad przechowywania są podane w [Azure Backup w celu zastąpienia artykułu infrastruktury taśm](backup-azure-backup-cloud-as-tape.md).

    ![Zasady przechowywania](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są wykonywane raz dziennie o godzinie 12:00 PM i 8 PM (Dolna część ekranu) i są zachowywane przez 180 dni.
    * Kopia zapasowa w sobotę o godzinie 12:00 jest zachowywany przez 104 tygodni
    * Kopia zapasowa w ostatniej sobotę o godzinie 12:00 jest zachowywany przez 60 miesięcy
    * Kopia zapasowa w ostatniej sobotę marca o godzinie 12:00 jest przechowywany przez 10 lat
14. Kliknij przycisk **dalej** i wybierz odpowiednią opcję transferu początkowej kopii zapasowej na platformę Azure. Możesz wybrać opcję **automatycznie za pośrednictwem sieci** lub **kopii zapasowej offline**.

    * **Automatycznie przez sieć** transferuje dane kopii zapasowej na platformę Azure zgodnie z harmonogramem wybranym dla kopii zapasowej.
    * Jak działa **kopia zapasowa offline** jest omówiona w [przepływie pracy kopii zapasowej offline w Azure Backup](backup-azure-backup-import-export.md).

    Wybierz odpowiedni mechanizm transferu, aby wysłać początkową kopię zapasową na platformę Azure, a następnie kliknij przycisk **dalej**.
15. Po przejrzeniu szczegółów zasad na ekranie **Podsumowanie** kliknij przycisk **Utwórz grupę** , aby zakończyć przepływ pracy. Możesz kliknąć przycisk **Zamknij** i monitorować postęp zadania w obszarze roboczym Monitorowanie.

    ![Tworzenie grupy ochrony w toku](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Tworzenie kopii zapasowej bazy danych SQL Server na żądanie
Podczas poprzednich kroków zostały utworzone zasady tworzenia kopii zapasowych, "punkt odzyskiwania" jest tworzony tylko w przypadku wystąpienia pierwszej kopii zapasowej. Zamiast czekać, aż harmonogram zostanie rozpoczęty, poniższe kroki wyzwalają ręczne utworzenie punktu odzyskiwania.

1. Przed utworzeniem punktu odzyskiwania poczekaj, aż w grupie ochrony zostanie wyświetlony stan **OK** dla bazy danych.

    ![Członkowie grupy ochrony](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Utwórz punkt odzyskiwania**.

    ![Utwórz punkt odzyskiwania w trybie online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. W menu rozwijanym wybierz pozycję **Ochrona online** , a następnie kliknij przycisk **OK**. Spowoduje to rozpoczęcie tworzenia punktu odzyskiwania na platformie Azure.

    ![Utwórz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Postęp zadania można wyświetlić w obszarze roboczym **monitorowanie** , gdzie można znaleźć zadanie w toku podobne do przedstawionego na następnej ilustracji.

    ![Konsola monitorowania](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych SQL Server z platformy Azure
Poniższe kroki są wymagane do odzyskania chronionej jednostki (SQL Server Database) z platformy Azure.

1. Otwórz konsolę zarządzania serwerem programu DPM. Przejdź do obszaru roboczego **odzyskiwania** , w którym można zobaczyć serwery z kopią zapasową programu DPM. Przeglądaj wymaganą bazę danych (w tym przypadku ReportServer $ MSDPM2012). Wybierz **odzyskiwanie od** momentu, w którym kończą się w **trybie online**.

    ![Wybierz punkt odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kliknij prawym przyciskiem myszy nazwę bazy danych , a następnie kliknij polecenie Odzyskaj.

    ![Odzyskaj z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. Program DPM wyświetla szczegóły punktu odzyskiwania. Kliknij przycisk **Dalej**. Aby zastąpić bazę danych, wybierz typ odzyskiwania **Odzyskaj do oryginalnego wystąpienia SQL Server**. Kliknij przycisk **Dalej**.

    ![Odzyskaj do oryginalnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie program DPM umożliwia odzyskiwanie bazy danych do innego wystąpienia SQL Server lub do autonomicznego folderu sieciowego.
4. Na ekranie **Określ opcje odzyskiwania** możesz wybrać opcje odzyskiwania, takie jak ograniczanie przepustowości sieci, aby ograniczyć przepustowość używaną przez funkcję odzyskiwania. Kliknij przycisk **Dalej**.
5. Na ekranie **Podsumowanie** widoczne są wszystkie konfiguracje odzyskiwania. Kliknij przycisk Odzyskaj.

    Stan odzyskiwania przedstawia odzyskaną bazę danych. Możesz kliknąć przycisk **Zamknij** , aby zamknąć kreatora i wyświetlić postęp w obszarze roboczym **monitorowanie** .

    ![Inicjowanie procesu odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po zakończeniu odzyskiwania przywrócona baza danych jest spójna z aplikacją.

### <a name="next-steps"></a>Następne kroki:
• [Azure Backup często zadawane pytania](backup-azure-backup-faq.md)
