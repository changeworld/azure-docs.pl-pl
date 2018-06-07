---
title: Zarządzanie Magazyny usług odzyskiwania Azure i serwerów
description: W tym artykule umożliwia zarządzanie Magazyny usług odzyskiwania Azure i serwerów.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 3d0404654631520909e63853d47b7de2b6cb4361
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606532"
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Monitorowanie magazynów i serwerów usługi Azure Recovery Services i zarządzanie nimi dla maszyn z systemem Windows

Ten artykuł zawiera omówienie kopii zapasowej zadania zarządzania i monitorowania, które są dostępne za pośrednictwem portalu Azure i agenta kopii zapasowej Microsoft Azure. W tym artykule przyjęto założenie, już mieć subskrypcję platformy Azure i został utworzony co najmniej jeden magazyn usług odzyskiwania.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Otwórz magazyn usług odzyskiwania

Na pulpicie nawigacyjnym magazynu usług odzyskiwania przedstawia szczegóły lub atrybuty z magazynu usług odzyskiwania.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu subskrypcji platformy Azure.
2. Kliknij opcję **Wszystkie usługi**. 

3. Chcesz otworzyć magazyn usług odzyskiwania. W oknie dialogowym zacznij pisać **usług odzyskiwania**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Kliknij przycisk **Magazyny usług odzyskiwania** do wyświetlenia na liście magazynów usług odzyskiwania w ramach subskrypcji.

     ![Otwórz listę magazynów usług odzyskiwania — krok 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

    Otwiera listę magazynów usług odzyskiwania.

    ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Z listy magazynów wybierz nazwę magazynu usług odzyskiwania, który chcesz otworzyć. Zostanie otwarte menu nawigacyjnym magazynu usług odzyskiwania.

    ![pulpitem nawigacyjnym magazynu usług odzyskiwania](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Po otwarciu magazyn usług odzyskiwania wypróbować zadań monitorowania lub zarządzania.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorowanie zadań tworzenia kopii zapasowej i alerty

Można monitorować, zadania i alerty na pulpicie nawigacyjnym magazynu usług odzyskiwania, w której występuje:

* Szczegóły alerty kopii zapasowej
* Pliki i foldery, a także chronione w chmurze maszyn wirtualnych platformy Azure
* Całkowita ilość miejsca zużyte na platformie Azure
* Stan zadania tworzenia kopii zapasowej

![Zadania tworzenia kopii zapasowej pulpitu nawigacyjnego](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Kliknięcie przycisku informacje w każdym z tych kafelków otwiera menu skojarzona, w których zarządzasz powiązanych zadań.

W górnej części pulpitu nawigacyjnego:

* Ustawienia — zapewnia dostęp dostępnych zadań tworzenia kopii zapasowej.
* Wykonywanie kopii zapasowej — umożliwia wykonanie kopii zapasowej nowe pliki i foldery (lub maszyn wirtualnych platformy Azure) w magazynie usług odzyskiwania.
* Usuń — Jeśli odzyskiwania magazynu usług nie jest już w użyciu, usuń go, aby zwolnić miejsce do magazynowania. DELETE jest włączona tylko po usunięciu wszystkich chronionych serwerach z magazynu.

![Zadania tworzenia kopii zapasowej pulpitu nawigacyjnego](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>Alerty kopii zapasowych za pomocą agenta kopii zapasowej systemu Azure:
| Poziom alertu | Wysyłania alertów |
| --- | --- |
| Krytyczne | niepowodzenie tworzenia kopii zapasowej, niepowodzenia odzyskiwania i Usuń odroczonego np. po zatrzymaniu ktoś ochronę za pomocą usuwania danych |
| Ostrzeżenie | do utworzenia kopii zapasowej zostało ukończone z ostrzeżeniami (jeśli < 100 nie kopii zapasowej plików z powodu problemów uszkodzenia, i pomyślnie kopii zapasowej plików > 1 000 000) |
| Informacyjne | obecnie alerty informacyjne nie są dostępne dla agenta kopii zapasowej systemu Azure |

## <a name="manage-backup-alerts"></a>Zarządzanie alertami kopii zapasowej
Kliknij przycisk **alerty kopii zapasowej** Kafelek, aby otworzyć **alerty kopii zapasowej** menu alerty i zarządzaj nimi.

![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

Kafelek alerty kopii zapasowej jest wyświetlana liczba:

* alerty krytyczne nierozpoznane w ostatnich 24 godzinach
* alerty ostrzegawcze nierozpoznane w ostatnich 24 godzinach

Kliknij łącze, aby wyświetlić **alerty kopii zapasowej** menu filtrowany widok alertów (krytyczna lub poważna).

Z menu alerty kopii zapasowej możesz:

* Wybierz odpowiednie informacje, aby uwzględnić z alertami.

    ![Wybieranie kolumn](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* Filtrowanie alertów dotyczących ważności, stan i godziny rozpoczęcia/zakończenia.

    ![Filtrowanie alertów](./media/backup-azure-manage-windows-server/filter-alerts.png)
* Konfigurowanie powiadomień o ważności, częstotliwości i adresatów, a także włączyć alerty lub wyłączyć.

    ![Filtrowanie alertów](./media/backup-azure-manage-windows-server/configure-notifications.png)

Jeśli **na alertu** został wybrany jako **powiadamiania** częstotliwości, nie grupowania lub zmniejszenia w wiadomościach e-mail. Każdy alert powoduje jedno powiadomienie (ustawienie domyślne), a następnie wysyłana wiadomość e-mail z rozwiązania natychmiast.

Jeśli **co godzinę szyfrowanego** został wybrany jako **powiadamiania** częstotliwości, zostanie wysłana wiadomość e-mail do użytkownika wyjaśniający nierozwiązane alerty zostały wygenerowane w ciągu ostatniej godziny. Wiadomość e-mail z rozwiązania jest wysyłane na koniec godziny.

Alerty mogą być wysyłane do następujących poziomów ważności:

* Krytyczne
* ostrzeżenie
* informacje

Dezaktywuj alert o **Dezaktywuj** przycisk menu szczegóły zadania. Po kliknięciu Dezaktywuj, możesz podać informacje o rozdzielczości.

Wybierz kolumny mają być wyświetlane jako część alert o **wybierz kolumny** przycisku.

> [!NOTE]
> Z **ustawienia** menu Zarządzaj alerty kopii zapasowej przez zaznaczenie **monitorowanie i Raporty > Alerty i zdarzenia > Alerty kopii zapasowej** , a następnie klikając polecenie **filtru** lub  **Konfigurowanie powiadomień**.
>
>

## <a name="manage-backup-items"></a>Zarządzaj elementami kopii zapasowej
Zarządzanie lokalnymi kopiami zapasowymi jest teraz dostępna w portalu zarządzania. W sekcji Backup pulpitu nawigacyjnego **elementów kopii zapasowych** kafelka pokazuje liczbę elementów kopii zapasowych chronionych w magazynie.

Kliknij przycisk **folderów plików** w elementach kopii zapasowej kafelka.

![Kafelek elementów kopii zapasowych](./media/backup-azure-manage-windows-server/backup-items-tile.png)

Otwiera menu elementów kopii zapasowych z zestaw do plików i folderów, w której występuje każdej kopii zapasowej elementów na liście filtrów.

![Elementy kopii zapasowej](./media/backup-azure-manage-windows-server/backup-item-list.png)

Jeśli określony element kopii zapasowej wybierz z listy, zobaczysz istotne szczegóły dla tego elementu.

> [!NOTE]
> Z **ustawienia** menu zarządzania plikami i folderami wybierając **chronione elementy > kopii zapasowej elementów** , a następnie wybierając **folderów plików** z menu rozwijanego.
>
>

![Elementy kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Zarządzanie zadaniami tworzenia kopii zapasowych
Kopia zapasowa zadania na lokalnie, (Jeśli serwer lokalny jest tworzenie kopii zapasowej na platformie Azure) i kopii zapasowych Azure są widoczne na pulpicie nawigacyjnym.

W sekcji kopii zapasowej w pulpicie nawigacyjnym kafelka zadania tworzenia kopii zapasowej pokazuje liczbę zadań:

* w toku
* Nie można w ciągu ostatnich 24 godzin.

Aby zarządzać zadaniami kopii zapasowej, kliknij przycisk **zadania tworzenia kopii zapasowej** kafelka, która otwiera menu zadania tworzenia kopii zapasowej.

![Elementy kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/backup-jobs.png)

Modyfikowanie informacji dostępnych w menu zadania tworzenia kopii zapasowej z **wybierz kolumny** u góry strony.

Użyj **filtru** przycisk, aby wybrać między plików i folderów z kopii zapasowej maszyny wirtualnej platformy Azure.

Kopii zapasowej plików i folderów nie jest widoczny, kliknij przycisk **filtru** u góry strony i wybierz **pliki i foldery** typu elementu menu.

> [!NOTE]
> Z **ustawienia** menu Zarządzanie zadania tworzenia kopii zapasowej przez zaznaczenie **monitorowanie i Raporty > zadania > zadań tworzenia kopii zapasowej** , a następnie wybierając **folderów plików** z listy rozwijanej menu.
>
>

## <a name="monitor-backup-usage"></a>Monitorowanie użycia kopii zapasowej
W sekcji kopii zapasowej w pulpicie nawigacyjnym kafelka użycie kopii zapasowej zawiera magazynu używane na platformie Azure. Użycie magazynu jest dostępne:

* Użycie magazynu LRS skojarzonego z magazynem w chmurze
* Użycie magazynu grs w warstwie skojarzonego z magazynem w chmurze

## <a name="manage-your-production-servers"></a>Zarządzanie serwerami produkcji
Aby zarządzać serwerów produkcyjnych, kliknij przycisk **ustawienia**.

W obszarze Zarządzanie kliknij **infrastruktura kopii zapasowej > serwerów produkcyjnych**.

Wyświetla menu serwerów produkcyjnych serwerów produkcyjnych. Kliknij serwer, na liście, aby otworzyć szczegóły serwera.

![Elementy chronione](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Otwórz agenta usługi Kopia zapasowa Azure
Otwórz **agenta usługi Kopia zapasowa Microsoft Azure** (można go znaleźć, wyszukaj komputer dla *kopia zapasowa Microsoft Azure*).

![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/snap-in-search.png)

Z **akcje** dostępne po prawej stronie konsoli agenta kopii zapasowej, należy wykonać następujące zadania zarządzania:

* Zarejestruj serwer
* Harmonogram tworzenia kopii zapasowych
* Wykonaj kopię zapasową teraz
* Zmień właściwości

![Akcje konsoli agenta usługi Kopia zapasowa Microsoft Azure](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> Aby **odzyskać dane**, zobacz [przywrócić pliki do systemu Windows server lub komputer kliencki z systemem Windows](backup-azure-restore-windows-server.md).
>
>

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="modify-the-backup-schedule"></a>Zmodyfikuj harmonogram kopii zapasowych
1. W agencie kopia zapasowa Microsoft Azure kliknij **harmonogram tworzenia kopii zapasowych**.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. W **Kreatora kopii zapasowych harmonogram** pozostaw **wprowadzanie zmian do elementów kopii zapasowych lub razy** zaznaczono opcję i kliknij przycisk **dalej**.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. Jeśli chcesz dodać lub zmienić elementy, na **Wybieranie elementów do wykonania kopii zapasowej** ekranu kliknij **Dodaj elementy**.

    Można również ustawić **ustawienia wykluczania** na tej stronie kreatora. Jeśli chcesz wykluczyć pliki lub typów plików zapoznać się z procedurą dodawania [ustawienia wykluczania](#manage-exclusion-settings).
4. Wybierz pliki i foldery, które chcesz utworzyć kopię zapasową, a następnie kliknij przycisk **OK**.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. Określ **harmonogram tworzenia kopii zapasowych** i kliknij przycisk **dalej**.

    Można zaplanować codziennie (maksymalnie 3 razy dziennie) lub cotygodniowe kopie zapasowe.

    ![Elementy związane z tworzeniem kopii zapasowej systemu Windows Server](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Określanie harmonogramu tworzenia kopii zapasowych omówiono szczegółowo w tym [artykułu](backup-azure-backup-cloud-as-tape.md).
   >

6. Wybierz **zasady przechowywania** kopii zapasowej i kliknij **dalej**.

    ![Elementy związane z tworzeniem kopii zapasowej systemu Windows Server](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. Na **potwierdzenie** ekranu Przejrzyj informacje i kliknij przycisk **Zakończ**.
8. Po zakończeniu pracy Kreatora tworzenia **harmonogram tworzenia kopii zapasowych**, kliknij przycisk **Zamknij**.

    Po zmodyfikowaniu ochrony, można potwierdzić poprawnie wyzwalają kopii zapasowych, przechodząc do **zadania** kartę i potwierdzenie, że zmiany zostaną odzwierciedlone w zadania tworzenia kopii zapasowej.

## <a name="enable-network-throttling"></a>Włącz ograniczanie przepustowości sieci

Agent usługi Kopia zapasowa Azure udostępnia na karcie ograniczenia przepustowości, dzięki czemu można kontrolować sposób używania przepustowości sieci podczas transferu danych. Ten formant może być przydatne, jeśli chcesz utworzyć kopię zapasową danych podczas godziny pracy, ale nie chcesz procesu tworzenia kopii zapasowej z innych ruch internetowy. Ograniczanie danych transfer ma zastosowanie do kopii zapasowej i przywracania działań.  

Aby włączyć ograniczenie przepustowości:

1. W **agent usługi Kopia zapasowa**, kliknij przycisk **Zmień właściwości**.
2. Na ** ograniczania kartę, zaznacz **włączyć użycia przepustowości połączenia internetowego do operacji tworzenia kopii zapasowej**.

    ![Ograniczanie przepustowości sieci](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    Po włączeniu ograniczenia przepustowości określić dozwolonych przepustowością transferu danych kopii zapasowej podczas **godzin pracy** i **godziny wolne**.

    Wartości przepustowości rozpoczynały 512 kilobajtach na sekundę (KB/s) i można przejść do 1023 MB na sekundę (MB/s). Można również wyznaczyć rozpoczęcia i zakończenia **godzin pracy**, i dni tygodnia, są traktowane jako pracy dni. Czas poza godzinami pracy wyznaczonych jest uważany za godzin wolnych.
3. Kliknij przycisk **OK**.

## <a name="manage-exclusion-settings"></a>Zarządzanie ustawieniami wykluczeń
1. Otwórz **agenta usługi Kopia zapasowa Microsoft Azure** (można go znaleźć, wyszukaj na maszynie łańcuch *kopia zapasowa Microsoft Azure*).

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. W agencie kopia zapasowa Microsoft Azure kliknij **harmonogram tworzenia kopii zapasowych**.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. W pozostaw Kreatora kopii zapasowych harmonogram **wprowadzanie zmian do elementów kopii zapasowych lub razy** zaznaczono opcję i kliknij przycisk **dalej**.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. Kliknij przycisk **ustawienia wykluczenia**.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. Kliknij przycisk **Dodaj wykluczenia**.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. Wybierz lokalizację, a następnie kliknij **OK**.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. Dodaj rozszerzenie pliku w **typ pliku** pola.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Dodawanie rozszerzenia MP3

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Aby dodać inne rozszerzenie, kliknij przycisk **Dodawanie wykluczenia** i wprowadź inne rozszerzenie typu pliku (Dodawanie rozszerzenia .jpeg).

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. Po dodaniu wszystkich rozszerzeń, kliknij przycisk **OK**.
9. Kontynuuj pracę Kreatora harmonogramu kopii zapasowej, klikając **dalej** do momentu **stronę potwierdzenia**, następnie kliknij przycisk **Zakończ**.

    ![Planowanie kopii zapasowych serwera systemu Windows](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**Q1. Stan zadania tworzenia kopii zapasowej jest wyświetlana jako zakończone Azure agenta kopii zapasowej, dlaczego nie go pobrać natychmiast odzwierciedlone w portalu?**

Odpowiedź 1. Jest w Maksymalne opóźnienie w ciągu 15 minut między stan zadania tworzenia kopii zapasowej zostaną uwzględnione w agenta kopii zapasowej systemu Azure i portalu Azure.

**Q.2, gdy zadanie tworzenia kopii zapasowej nie powiedzie się, jak długo trwa Zgłoś alert?**

A.2 alert jest uruchamiany w ciągu 20 minutach niepowodzenia wykonywania kopii zapasowej Azure.

**Q3. Jest przypadek, w którym nie będzie wysyłana wiadomość e-mail, jeśli skonfigurowano powiadomienia?**

Odpowiedź 3. Poniżej są przypadkach, gdy nie będą wysyłane powiadomienia w celu zmniejszenia szumu alertu:

* Jeśli powiadomienia są skonfigurowane co godzinę, a alert jest uruchamiany rozwiązane w ciągu godziny
* Zadanie zostało anulowane.
* Drugi zadanie tworzenia kopii zapasowej nie powiodło się, ponieważ oryginalne zadanie tworzenia kopii zapasowej jest w toku.

## <a name="troubleshooting-monitoring-issues"></a>Rozwiązywanie problemów monitorowania
**Problem:** zadania i/lub alerty z usługi Azure Backup agent nie są wyświetlane w portalu.

**Kroki rozwiązywania problemów:** procesu ```OBRecoveryServicesManagementAgent```, wysyła dane zadanie i alert do usługi Azure Backup. Czasami ten proces może zostać wstrzymana lub zamknięcie.

1. Aby sprawdzić, proces nie działa, otwórz **Menedżera zadań** i sprawdź, czy ```OBRecoveryServicesManagementAgent``` proces jest uruchomiony.
2. Przy założeniu, że proces nie działa, otwórz **Panelu sterowania** , a następnie przejdź na liście usług. Uruchom lub uruchom ponownie **agenta usług odzyskiwania Microsoft Azure w zarządzania**.

    Aby uzyskać więcej informacji Przejdź dzienniki na:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Na przykład:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Kolejne kroki
* [Przywracanie systemu Windows Server lub klienta systemu Windows z platformy Azure](backup-azure-restore-windows-server.md)
* Aby dowiedzieć się więcej na temat tworzenia kopii zapasowej Azure, zobacz [Omówienie programu Kopia zapasowa Azure](backup-introduction-to-azure-backup.md)
* Odwiedź stronę [Forum kopii zapasowej platformy Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)
