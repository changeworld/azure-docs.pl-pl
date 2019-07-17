---
title: Tworzenie kopii zapasowej udziałów plików platformy Azure
description: W tym artykule szczegółowo przedstawiono sposób tworzenia kopii zapasowej udziałów plików platformy Azure i ich przywracania. Opisano w nim także zadania związane z zarządzaniem.
services: backup
author: rayne-wiselman
ms.author: raynew
ms.date: 01/31/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 252dc48fd35318f9cd8407007187b81a8674fab0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296913"
---
# <a name="back-up-azure-file-shares"></a>Tworzenie kopii zapasowej udziałów plików platformy Azure
W tym artykule opisano sposób tworzenia kopii zapasowej i przywracania [udziałów plików platformy Azure](../storage/files/storage-files-introduction.md) przy użyciu witryny Azure Portal.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie magazynu usługi Recovery Services na potrzeby tworzenia kopii zapasowej w usłudze Azure Files
> * Uruchomianie zadania tworzenia kopii zapasowej na żądanie w celu tworzenia punktu przywracania
> * Przywracanie plików z punktu przywracania
> * Zarządzanie zadaniami tworzenia kopii zapasowych
> * Zatrzymywanie ochrony usługi Azure Files
> * Usuwanie danych kopii zapasowych

## <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem kopii zapasowej udziału plików platformy Azure sprawdź, czy znajduje się on na [koncie magazynu jednego z obsługiwanych typów](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Jeśli tak, oznacza to, że możesz chronić swoje udziały plików.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Ograniczenia dotyczące tworzenia kopii zapasowej udziału plików platformy Azure w okresie korzystania z wersji zapoznawczej
Funkcja tworzenia kopii zapasowych udziałów plików platformy Azure jest dostępna w wersji zapoznawczej. Obsługiwane są udziały plików platformy Azure na kontach magazynu ogólnego przeznaczenia w wersji 1 i 2. Następujące scenariusze tworzenia kopii zapasowej nie są obsługiwane w przypadku udziałów plików platformy Azure:
- Obsługa kopii zapasowych udziałów plików platformy Azure w ramach kont magazynu z replikacją [magazynu strefowo](../storage/common/storage-redundancy-zrs.md) nadmiarowego (ZRS) jest obecnie ograniczona do [tych regionów](backup-azure-files-faq.md#in-which-geos-can-i-back-up-azure-file-shares-).
- Nie można chronić udziałów plików platformy Azure w ramach kont magazynu, które mają włączone sieci wirtualne lub zaporę.
- Nie ma dostępnego interfejsu wiersza polecenia do ochrony usługi Azure Files z poziomu usługi Azure Backup.
- Maksymalna liczba zaplanowanych kopii zapasowych to jedna dziennie.
- Maksymalna liczba kopii zapasowych na żądanie to cztery dziennie.
- Aby zapobiec przypadkowemu usunięciu kopii zapasowych z magazynu usługi Recovery Services, użyj [blokad zasobów](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na koncie magazynu.
- Nie usuwaj migawek utworzonych przy użyciu usługi Azure Backup. Usunięcie migawek może spowodować utratę punktów odzyskiwania i/lub błędy przywracania.
- Nie usuwaj udziałów plików, które są chronione przez usługę Azure Backup. Bieżące rozwiązanie usunie wszystkie migawki wykonane przez Azure Backup po usunięciu udziału plików, a tym samym utraci wszystkie punkty przywracania.



## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurowanie kopii zapasowej udziału plików platformy Azure
W tym samouczku przyjęto założenie, że ustanowiono już udział plików platformy Azure. Aby utworzyć kopię zapasową udziału plików platformy Azure:

1. Utwórz magazyn usługi Recovery Services w tym samym regionie, co udziału plików. Jeśli magazyn już istnieje, otwórz stronę z jego przeglądem, a następnie kliknij pozycję **Kopia zapasowa**.

    ![Wybieranie udział plików platformy Azure jako celu kopii zapasowej](./media/backup-file-shares/overview-backup-page.png)

2. W menu **Cel kopii zapasowej** w obszarze **Dla jakich elementów chcesz utworzyć kopię zapasową?** wybierz pozycję Udział plików platformy Azure.

    ![Wybieranie udział plików platformy Azure jako celu kopii zapasowej](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Kliknij pozycję **Utwórz kopię zapasową**, aby skonfigurować udział plików platformy Azure na potrzeby magazynu usługi Recovery Services.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/set-backup-goal.png)

    Po skojarzeniu magazynu z udziałem plików platformy Azure zostanie otwarte menu Kopia zapasowa oraz zostanie wyświetlony monit dotyczący wyboru konta magazynu. W menu wyświetlane są wszystkie obsługiwane konta magazynu znajdujące się w tym samym regionie co magazyn, które nie są jeszcze skojarzone z magazynem usługi Recovery Services.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/list-of-storage-accounts.png)

4. Na liście kont magazynu wybierz konto, a następnie kliknij przycisk **OK**. Na platformie Azure zostanie wykonane wyszukiwanie udziałów plików w ramach magazynu, dla których można utworzyć kopię zapasową. Jeśli niedawno dodano udziały plików i nie ma ich na liście, poczekaj chwilę na ich pojawienie się.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/discover-file-shares.png)

5. Z listy **Udziały plików** wybierz co najmniej jeden udział plików, którego kopię zapasową chcesz utworzyć, a następnie kliknij przycisk **OK**.

6. Po wybraniu udziałów plików menu Kopia zapasowa zostanie przełączone na menu **Zasady tworzenie kopii zapasowych**. Z poziomu tego menu wybierz istniejące zasady tworzenia kopii zapasowych lub utwórz nowe, a następnie kliknij pozycję **Włącz kopię zapasową**.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/apply-backup-policy.png)

    Po ustanowieniu zasad tworzenia kopii zapasowych w zaplanowanym czasie zostanie utworzona migawka udziałów plików, a punkt odzyskiwania zostanie zachowany przez wybrany okres.

## <a name="create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie
W pewnych okolicznościach może zaistnieć potrzeba wygenerowania migawki kopii zapasowych lub punktu odzyskiwania w godzinach innych niż zaplanowane w ramach zasad tworzenia kopii zapasowych. Typowy moment generowania kopii zapasowej na żądanie przypada zaraz po skonfigurowaniu zasad tworzenia kopii zapasowych. Na podstawie harmonogramu w ramach zasad tworzenia kopii zapasowych może upłynąć wiele godzin lub dni zanim migawka zostanie utworzona. Aby chronić dane przed rozpoczęciem obowiązywania zasad tworzenia kopii zapasowych, zainicjuj tworzenie kopii zapasowej na żądanie. Tworzenie kopii zapasowej na żądanie jest często wymagane przed wprowadzeniem planowanych zmian w udziałach plików.

### <a name="to-create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie

1. Otwórz magazyn usługi Recovery Services zawierający punkty odzyskiwania udziałów plików, a następnie kliknij pozycję **Elementy kopii zapasowej**. Zostanie wyświetlona lista typów elementów kopii zapasowych.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/list-of-backup-items.png)

2. Z listy wybierz pozycję **Azure Storage (Azure Files)** . Zostanie wyświetlona lista udziałów plików platformy Azure.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Z listy udziałów plików platformy Azure wybierz żądany udział plików. Zostanie otwarte menu elementu kopii zapasowej dla wybranego udziału plików.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/backup-item-menu.png)

4. W menu Element kopii zapasowej kliknij pozycję **Utwórz kopię zapasową teraz**. Ponieważ jest to zadanie tworzenia kopii zapasowej na żądanie, nie istnieją żadne zasady przechowywania skojarzone z punktem odzyskiwania. Zostanie otwarte okno dialogowe **Utwórz kopię zapasową teraz**. Określ ostatni dzień zachowywania punktu odzyskiwania.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Przywracanie z kopii zapasowej udziału plików platformy Azure
Jeśli chcesz przywrócić cały udział plików bądź pojedyncze pliki lub foldery z punktu przywracania, przejdź do elementu kopii zapasowej zgodnie z opisem w poprzedniej sekcji. Wybierz pozycję **Przywróć udział plików**, aby przywrócić cały udział plików z żądanego punktu w czasie. Z wyświetlonej listy Punkty przywracania wybierz punkt przywracania, aby możliwe było zastąpienie bieżącego udziału plików lub przywrócenie go do alternatywnego udziału plików w tym samym regionie.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Przywracanie pojedynczych plików lub folderów z kopii zapasowej udziału plików platformy Azure
Usługa Azure Backup zapewnia możliwość przeglądania punktu przywracania w witrynie Azure Portal. Aby przywrócić wybrany plik lub folder, kliknij pozycję Odzyskiwanie plików na stronie Element kopii zapasowej, a następnie dokonaj wyboru z listy dostępnych punktów przywracania. Wybierz lokalizację docelową odzyskiwania, a następnie kliknij pozycję **Wybierz plik**, aby przejrzeć punkt przywracania. Wybierz wybrany plik lub folder, a następnie wybierz pozycję **Przywróć**.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Zarządzanie kopiami zapasowymi udziałów plików platformy Azure

Na stronie **Zadania tworzenia kopii zapasowych** możliwe jest wykonanie kilku zadań dotyczących zarządzania względem kopii zapasowych udziałów plików, w tym:
- [Monitorowanie zadań](backup-azure-files.md#monitor-jobs)
- [Tworzenie nowych zasad](backup-azure-files.md#create-a-new-policy)
- [Zatrzymywanie ochrony udziału plików](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [Wznawianie ochrony udziału plików](backup-azure-files.md#resume-protection-for-azure-file-share)
- [Usuwanie danych kopii zapasowej](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Monitorowanie zadań

Postęp wszystkich zadań możesz monitorować na stronie **Zadania tworzenia kopii zapasowej**.

Aby otworzyć stronę **Zadania tworzenia kopii zapasowej**:

- Otwórz magazyn usługi Recovery Services, który chcesz monitorować, w menu magazynu usługi Recovery Services kliknij pozycję **Zadania**, a następnie kliknij pozycję **Zadania tworzenia kopii zapasowej**.

   ![Wybieranie zadania do monitorowania](./media/backup-file-shares/open-backup-jobs.png)

    Zostanie wyświetlona lista zadań tworzenia kopii zapasowej i ich stan.

    ![Wybieranie zadania do monitorowania](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Tworzenie nowych zasad

Nowe zasady w celu tworzenia kopii zapasowej udziałów plików platformy Azure możesz utworzyć z poziomu obszaru **Zasady tworzenia kopii zapasowych** magazynu usługi Recovery Services. Wszystkie zasady utworzone podczas konfigurowania kopii zapasowej na potrzeby udziałów plików są wyświetlane z udziałem plików platformy Azure jako typem zasad.

Aby wyświetlić istniejące zasady tworzenia kopii zapasowych:

- Otwórz wybrany magazyn usługi Recovery Services, a następnie z poziomu menu magazynu usługi Recovery Services kliknij pozycję **Zasady tworzenia kopii zapasowych**. Wyświetlone są wszystkie zasady tworzenia kopii zapasowych.

   ![Wybieranie zadania do monitorowania](./media/backup-file-shares/list-of-backup-policies.png)

Aby utworzyć nowe zasady tworzenia kopii zapasowych:

1. W menu magazynu usługi Recovery Services kliknij pozycję **Zasady tworzenia kopii zapasowych**.
2. Na liście zasad tworzenia kopii zapasowych kliknij pozycję **Dodaj**.

   ![Wybieranie zadania do monitorowania](./media/backup-file-shares/new-backup-policy.png)

3. Z poziomu menu **Dodawanie** wybierz pozycję **Udział plików platformy Azure**. Zostanie otwarte menu Zasady tworzenia kopii zapasowych dla udziału plików platformy Azure. Podaj nazwę zasad, częstotliwość tworzenia kopii zapasowych oraz zakres przechowywania dla punktów odzyskiwania. Kliknij przycisk OK po zdefiniowaniu zasad.

   ![Wybieranie zadania do monitorowania](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Zatrzymywanie ochrony udziału plików platformy Azure

Jeśli zdecydujesz się na zatrzymanie ochrony udziału plików platformy Azure, zostanie wyświetlony monit dotyczący zachowania punktów odzyskiwania. Istnieją dwa sposoby na zatrzymanie ochrony udziałów plików platformy Azure:

- Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania lub
- Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych, ale pozostawienie punktów odzyskiwania

Pozostawienie punktów odzyskiwania w magazynie może spowodować naliczenie opłaty, ponieważ odpowiednie migawki utworzone przez usługę Azure Backup zostaną zachowane. Zaletą pozostawienia punktów odzyskiwania jest natomiast możliwość późniejszego przywrócenia udziału plików, jeśli zaistnieje taka potrzeba. Aby uzyskać informacje o koszcie pozostawienia punktów odzyskiwania, zobacz szczegóły cennika. Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie możesz przywrócić udziału plików.

Aby zatrzymać ochronę udziału plików platformy Azure:

1. Otwórz magazyn usługi Recovery Services zawierający punkty odzyskiwania udziałów plików, a następnie kliknij pozycję **Elementy kopii zapasowej**. Zostanie wyświetlona lista typów elementów kopii zapasowych.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-file-shares/list-of-backup-items.png)

2. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)** . Zostanie wyświetlona lista elementów kopii zapasowych dla typu Azure Storage (Azure Files).

   ![kliknij element, aby otworzyć dodatkowe menu](./media/backup-file-shares/azure-file-share-backup-items.png)

3. Na liście elementów kopii zapasowych dla typu Azure Storage (Azure Files) wybierz element kopii zapasowej, który chcesz zatrzymać.

4. W obszarze elementów udziałów plików platformy Azure kliknij menu **Więcej**, a następnie wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

   ![kliknij element, aby otworzyć dodatkowe menu](./media/backup-file-shares/stop-backup.png)

5. W menu Zatrzymaj kopię zapasową wybierz pozycję **Zachowaj dane kopii zapasowej** lub **Usuń dane kopii zapasowej**, a następnie kliknij pozycję **Zatrzymaj tworzenie kopii zapasowej**.

   ![kliknij element, aby otworzyć dodatkowe menu](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Wznawianie ochrony udziału plików platformy Azure

Jeśli opcja Zachowaj dane kopii zapasowej została wybrana gdy ochrona udziału plików była zatrzymana, możliwe jest wznowienie ochrony. Jeśli wybrano opcję **Usuń dane kopii zapasowej**, wznowienie ochrony udziału plików nie jest możliwe.

Aby wznowić ochronę udziału plików, przejdź do elementu kopii zapasowej, a następnie kliknij pozycję Wznów tworzenie kopii zapasowej. Zostanie otwarty obszar Zasady tworzenia kopii zapasowych, w którym można wybrać żądane zasady w celu wznowienia tworzenia kopii zapasowych.

   ![Wybieranie zadania do monitorowania](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Możliwe jest usunięcie kopii zapasowej lub udziału plików podczas zadania zatrzymywania tworzenia kopii zapasowych lub w dowolnym momencie po zatrzymaniu ochrony. Wstrzymanie się z usunięciem punktów odzyskiwania przez określoną liczbę dni lub tygodni może być nawet korzystne. W odróżnieniu od przywracania punktów odzyskiwania podczas usuwania danych kopii zapasowej nie można wybrać konkretnych punktów odzyskiwania do usunięcia. Jeśli zdecydujesz się usunąć dane kopii zapasowych, wszystkie skojarzone z tym elementem punkty odzyskiwania zostaną usunięte.

Na potrzeby poniższej procedury przyjęto założenie, że zadanie tworzenia kopii zapasowej dla maszyny wirtualnej zostało zatrzymane. Po zatrzymaniu zadania tworzenia kopii zapasowej opcje Wznów tworzenie kopii zapasowej i Usuń dane kopii zapasowej są dostępne na pulpicie nawigacyjnym elementu kopii zapasowej. Kliknij pozycję Usuń dane kopii zapasowej, a następnie wpisz nazwę udziału plików, aby potwierdzić usunięcie. Opcjonalnie podaj przyczynę usunięcia lub dodaj komentarz.

## <a name="see-also"></a>Zobacz też
Aby uzyskać dodatkowe informacje na temat udziałów plików platformy Azure, zobacz
- [Tworzenie kopii zapasowej udziału plików platformy Azure — często zadawane pytania](backup-azure-files-faq.md)
- [Rozwiązywanie problemów dotyczących tworzenia kopii zapasowej udziału plików platformy Azure](troubleshoot-azure-files.md)
