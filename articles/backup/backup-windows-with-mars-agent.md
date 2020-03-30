---
title: Tworzenie kopii zapasowych maszyn z systemem Windows przy użyciu agenta MARS
description: Użyj agenta usług odzyskiwania platformy Microsoft Azure (MARS) do utworzenia kopii zapasowej maszyn z systemem Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408916"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>Tworzenie kopii zapasowych maszyn z systemem Windows przy użyciu agenta marsjańskiej kopii zapasowej platformy Azure

W tym artykule wyjaśniono, jak wykonać kopię zapasową maszyn z systemem Windows przy użyciu usługi [Azure Backup](backup-overview.md) i agenta usług microsoft azure recovery services (MARS). MARS jest również znany jako agent usługi Azure Backup.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
>
> * Sprawdzenie wymagań wstępnych
> * Utwórz zasady tworzenia kopii zapasowych i harmonogram.
> * Wykonaj kopię zapasową na żądanie.

## <a name="before-you-start"></a>Przed rozpoczęciem

* Dowiedz się, jak [usługa Kopia zapasowa platformy Azure używa agenta MARS do tworzenia kopii zapasowych maszyn z systemem Windows.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Dowiedz się więcej o [architekturze kopii zapasowej,](backup-architecture.md#architecture-back-up-to-dpmmabs) która uruchamia agenta MARS na pomocniczym serwerze MABS lub Data Protection Manager.
* Sprawdź, [co jest obsługiwane i co możesz zrobić kopii zapasowej](backup-support-matrix-mars-agent.md) przez agenta MARS.
* [Sprawdź dostęp do Internetu](install-mars-agent.md#verify-internet-access) na komputerach, których chcesz zrobić, aby uzyskać jego utworzenie.
* Jeśli agent MARS nie jest zainstalowany, dowiedz się, jak go zainstalować [tutaj](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określa, kiedy należy wykonać migawki danych, aby utworzyć punkty odzyskiwania. Określa również, jak długo zachować punkty odzyskiwania. Agenta MARS służy do konfigurowania zasad tworzenia kopii zapasowych.

Usługa Azure Backup nie uwzględnia automatycznie czasu letniego (DST). Ta wartość domyślna może spowodować pewną rozbieżność między rzeczywistym czasem a zaplanowanym czasem tworzenia kopii zapasowej.

Aby utworzyć nowe zasady kopii zapasowych:

1. Po pobraniu i zarejestrowaniu agenta MARS otwórz konsolę agenta. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.  

1. W obszarze **Akcje**wybierz pozycję **Zaplanuj kopię zapasową**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. W Kreatorze planowania tworzenia kopii zapasowych wybierz pozycję **Wprowadzenie** > **dalej**.
1. W obszarze **Wybierz elementy do utworzenia kopii zapasowej**wybierz pozycję Dodaj **elementy**.

    ![Dodawanie elementów do kopii zapasowej](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. W polu **Zaznacz elementy** zaznacz elementy do utworzenia kopii zapasowej, a następnie wybierz przycisk **OK**.

    ![Wybieranie elementów do utworzenia kopii zapasowej](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Na stronie **Wybieranie elementów do utworzenia kopii zapasowej** wybierz pozycję **Dalej**.
1. Na stronie **Określ harmonogram tworzenia kopii zapasowych** określ, kiedy należy wykonać codzienne lub tygodniowe kopie zapasowe. Następnie wybierz **przycisk Dalej**.

    * Punkt odzyskiwania jest tworzony podczas wykonywania kopii zapasowej.
    * Liczba punktów odzyskiwania utworzonych w środowisku zależy od harmonogramu tworzenia kopii zapasowych.
    * Można zaplanować maksymalnie trzy codzienne kopie zapasowe dziennie. W poniższym przykładzie występują dwie codzienne kopie zapasowe, jeden o północy i jeden o 18:00.

        ![Konfigurowanie dziennego harmonogramu tworzenia kopii zapasowych](./media/backup-configure-vault/day-schedule.png)

    * Można również uruchamiać cotygodniowe kopie zapasowe. W poniższym przykładzie kopie zapasowe są pobierane w każdą alternatywną niedzielę i środę o 9:30 AM i 1:00 AM.

        ![Konfigurowanie tygodniowego harmonogramu tworzenia kopii zapasowych](./media/backup-configure-vault/week-schedule.png)

1. Na stronie **Wybierz zasady przechowywania** określ sposób przechowywania historycznych kopii danych. Następnie wybierz **przycisk Dalej**.

    * Ustawienia przechowywania określają, które punkty odzyskiwania mają być przechowywane i jak długo je przechowywać.
    * W przypadku ustawienia dziennego przechowywania należy wskazać, że w czasie określonym dla dziennego przechowywania ostatni punkt odzyskiwania zostanie zachowany przez określoną liczbę dni. Można też określić miesięczne zasady przechowywania, aby wskazać, że punkt odzyskiwania utworzony 30 części każdego miesiąca powinien być przechowywany przez 12 miesięcy.
    * Przechowywanie dziennych i tygodniowych punktów odzyskiwania zwykle pokrywa się z harmonogramem tworzenia kopii zapasowych. Tak więc, gdy harmonogram wyzwala kopię zapasową, punkt odzyskiwania, który tworzy kopia zapasowa jest przechowywany przez czas określony przez zasady przechowywania dziennego lub tygodniowego.
    * W poniższym przykładzie:

        * Codzienne kopie zapasowe o północy i 18:00 są przechowywane przez siedem dni.
        * Kopie zapasowe wykonane w sobotę o północy i 18:00 są przechowywane przez cztery tygodnie.
        * Kopie zapasowe wykonane w ostatnią sobotę miesiąca o północy i 18:00 są przechowywane przez 12 miesięcy.
        * Kopie zapasowe wykonane w ostatnią sobotę marca są przechowywane przez 10 lat.

        ![Przykład zasad przechowywania](./media/backup-configure-vault/retention-example.png)

1. Na stronie **Wybieranie początkowego typu kopii zapasowej** zdecyduj, czy chcesz wykonać początkową kopię zapasową za pośrednictwem sieci, czy użyć kopii zapasowej w trybie offline. Aby wykonać początkową kopię zapasową przez sieć, wybierz **opcję Automatycznie przez sieć** > **Dalej**.

    Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych w trybie offline, zobacz [Używanie usługi Azure Data Box do wykonywania kopii zapasowych w trybie offline](offline-backup-azure-data-box.md).

    ![Wybieranie początkowego typu kopii zapasowej](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Na stronie **Potwierdzenie** przejrzyj informacje, a następnie wybierz pozycję **Zakończ**.

    ![Potwierdź typ kopii zapasowej](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Po zakończeniu tworzenia harmonogramu tworzenia kopii zapasowej przez kreatora wybierz pozycję **Zamknij**.

    ![Wyświetlanie postępu harmonogramu tworzenia kopii zapasowych](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Utwórz zasady na każdym komputerze, na którym jest zainstalowany agent.

### <a name="do-the-initial-backup-offline"></a>Wykonywanie początkowej kopii zapasowej w trybie offline

Początkową kopię zapasową można uruchomić automatycznie za pośrednictwem sieci lub wykonać kopię zapasową w trybie offline. Rozmieszczenie w trybie offline dla początkowej kopii zapasowej jest przydatne, jeśli masz duże ilości danych, które będą wymagały dużej przepustowości sieci do transferu.

Aby wykonać transfer w trybie offline:

1. Zapisz dane kopii zapasowej w lokalizacji tymczasowej.
1. Użyj narzędzia AzureOfflineBackupDiskPrep, aby skopiować dane z lokalizacji przejściowej na jeden lub więcej dysków SATA.

    Narzędzie tworzy zadanie importowania platformy Azure. Aby uzyskać więcej informacji, zobacz [Co to jest usługa importu/eksportowania platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).
1. Wyślij dyski SATA do centrum danych platformy Azure.

    W centrum danych dane dysku są kopiowane do konta magazynu platformy Azure. Usługa Azure Backup kopiuje dane z konta magazynu do magazynu, a przyrostowe kopie zapasowe są zaplanowane.

Aby uzyskać więcej informacji na temat rozmieszczania w trybie offline, zobacz [Używanie usługi Azure Data Box do tworzenia kopii zapasowych w trybie offline](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Włączanie ograniczania przepustowości sieci

Można kontrolować sposób, w jaki agent MARS używa przepustowości sieci, włączając ograniczanie przepustowości sieci. Ograniczanie jest przydatne, jeśli trzeba wykonać kopię zapasową danych w godzinach pracy, ale chcesz kontrolować, ile przepustowości używa działania kopii zapasowej i przywracania.

Ograniczanie przepustowości sieci w usłudze Azure Backup używa [jakości usługi (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) w lokalnym systemie operacyjnym.

Ograniczanie przepustowości sieci jest dostępne w systemie Windows Server 2012 i nowszych oraz w systemie Windows 8 i nowszych. W systemach operacyjnych powinny być uruchomione najnowsze dodatki Service Pack.

Aby włączyć ograniczanie przepustowości sieci:

1. W agencie MARS wybierz pozycję **Zmień właściwości**.
1. Na karcie **Ograniczanie wybierz** pozycję **Włącz ograniczanie przepustowości internetu w przypadku operacji tworzenia kopii zapasowych**.

    ![Konfigurowanie ograniczania przepustowości sieci dla operacji tworzenia kopii zapasowych](./media/backup-configure-vault/throttling-dialog.png)
1. Określ dozwoloną przepustowość w godzinach pracy i godzinach pracy. Wartości przepustowości zaczynają się od 512 Kb/s i sięgają nawet 1023 MB/s. Następnie wybierz przycisk **OK**.

## <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

1. W agencie MARS wybierz pozycję **Sprzedaj teraz**.

    ![Teraz zapasowy w systemie Windows Server](./media/backup-configure-vault/backup-now.png)

1. Jeśli wersja agenta MARS jest 2.0.9169.0 lub nowsza, można ustawić datę przechowywania niestandardowego. W sekcji **Zachowaj do tworzenia kopii zapasowych** wybierz datę z kalendarza.

   ![Dostosowywanie daty przechowywania za pomocą kalendarza](./media/backup-configure-vault/mars-ondemand.png)

1. Na stronie **Potwierdzenie** przejrzyj ustawienia i wybierz pozycję **Zapas zapasowa**.
1. Wybierz **przycisk Zamknij,** aby zamknąć kreatora. Jeśli kreator zostanie zamknięty przed zakończeniem tworzenia kopii zapasowej, kreator będzie nadal działał w tle.

Po zakończeniu początkowej kopii zapasowej stan **Zadanie zakończone** pojawia się w konsoli Kopia zapasowa.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Konfigurowanie zachowania przechowywania zasad tworzenia kopii zapasowych na żądanie

> [!NOTE]
> Te informacje dotyczą tylko wersji agenta MARS, które są starsze niż 2.0.9169.0.
>

| Opcja harmonogram tworzenia kopii zapasowych | Czas przechowywania danych
| -- | --
| Day | **Domyślne przechowywanie:** odpowiednik "przechowywania w dniach dla codziennych kopii zapasowych." <br/><br/> **Wyjątek:** Jeśli dzienna zaplanowana kopia zapasowa ustawiona na długoterminowe przechowywanie (tygodnie, miesiące lub lata) nie powiedzie się, kopia zapasowa na żądanie, która jest wyzwalana zaraz po awarii jest brana pod uwagę dla długoterminowego przechowywania. W przeciwnym razie następna zaplanowana kopia zapasowa jest brana pod uwagę dla długoterminowego przechowywania.<br/><br/> **Przykładowy scenariusz:** zaplanowana kopia zapasowa w czwartek o godzinie 8:00 nie powiodła się. Ta kopia zapasowa miała być brana pod uwagę przy tygodniowym, miesięcznym lub rocznym zatrzymaniu. Tak więc pierwsza kopia zapasowa na żądanie uruchomiona przed następną zaplanowaną kopią zapasową w piątek o godzinie 8:00 jest automatycznie oznaczana dla tygodniowego, miesięcznego lub rocznego przechowywania. Ta kopia zapasowa zastępuje czwartkową kopię zapasową o godzinie 8:00.
| Tydzień | **Domyślne zatrzymanie:** Jeden dzień. Kopie zapasowe na żądanie, które są pobierane dla źródła danych, które ma cotygodniowe zasady tworzenia kopii zapasowych, są usuwane następnego dnia. Są one usuwane, nawet jeśli są to najnowsze kopie zapasowe dla źródła danych. <br/><br/> **Wyjątek:** Jeśli cotygodniowa zaplanowana kopia zapasowa ustawiona na długoterminowe przechowywanie (tygodnie, miesiące lub lata) nie powiedzie się, kopia zapasowa na żądanie, która jest wyzwalana zaraz po awarii jest brana pod uwagę dla długoterminowego przechowywania. W przeciwnym razie następna zaplanowana kopia zapasowa jest brana pod uwagę dla długoterminowego przechowywania. <br/><br/> **Przykładowy scenariusz:** zaplanowana kopia zapasowa w czwartek o godzinie 8:00 nie powiodła się. Ta kopia zapasowa miała być brana pod uwagę przy miesięcznym lub rocznym zatrzymaniu. Tak więc pierwsza kopia zapasowa na żądanie, która jest wyzwalana przed następną zaplanowaną kopią zapasową w czwartek o 8:00, jest automatycznie oznaczana pod kątem miesięcznego lub rocznego przechowywania. Ta kopia zapasowa zastępuje czwartkową kopię zapasową o godzinie 8:00.

Aby uzyskać więcej informacji, zobacz [Tworzenie zasad tworzenia kopii zapasowych](#create-a-backup-policy).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić pliki na platformie Azure](backup-azure-restore-windows-server.md).
* Znajdowanie [typowych pytań dotyczących tworzenia kopii zapasowych plików i folderów](backup-azure-file-folder-backup-faq.md)

