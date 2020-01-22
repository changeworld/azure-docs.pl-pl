---
title: Zarządzanie kopiami zapasowymi udziałów plików platformy Azure
description: W tym artykule opisano typowe zadania związane z zarządzaniem i monitorowaniem udziałów plików platformy Azure, których kopia zapasowa została utworzona przez usługę Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294554"
---
# <a name="manage-azure-file-share-backups"></a>Zarządzanie kopiami zapasowymi udziałów plików platformy Azure

W tym artykule opisano typowe zadania związane z zarządzaniem i monitorowaniem udziałów plików platformy Azure, których kopia zapasowa została utworzona przez usługę [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) . Dowiesz się, jak wykonywać następujące zadania zarządzania w magazynie Recovery Services:

* [Monitorowanie zadań](#monitor-jobs)
* [Tworzenie nowych zasad](#create-a-new-policy)
* [Modyfikuj zasady](#modify-policy)
* [Zatrzymywanie ochrony udziału plików](#stop-protection-on-a-file-share)
* [Wznawianie ochrony udziału plików](#resume-protection-on-a-file-share)
* [Usuwanie danych kopii zapasowej](#delete-backup-data)
* [Wyrejestruj konto magazynu](#unregister-storage-account)

## <a name="monitor-jobs"></a>Monitorowanie zadań

Gdy Wyzwalasz operację tworzenia kopii zapasowej lub przywracania, usługa Backup tworzy zadanie śledzenia. Postęp wszystkich zadań możesz monitorować na stronie **Zadania tworzenia kopii zapasowej**.

Aby otworzyć stronę **Zadania tworzenia kopii zapasowej**:

1. Otwórz magazyn Recovery Services, który został użyty do skonfigurowania kopii zapasowej dla udziałów plików. W bloku **Przegląd** kliknij pozycję **zadania tworzenia kopii zapasowej** w sekcji **monitorowanie** .

   ![Zadania tworzenia kopii zapasowej w sekcji monitorowanie](./media/manage-afs-backup/backup-jobs.png)

2. Po kliknięciu przycisku OK zostanie wyświetlony blok **zadania tworzenia kopii zapasowej** zawierający listę stanów wszystkich zadań. Możesz kliknąć nazwę obciążenia odpowiadającą udziałowi plików, który chcesz monitorować.

   ![Nazwa obciążenia](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Tworzenie nowych zasad

Można utworzyć nowe zasady tworzenia kopii zapasowych udziałów plików platformy Azure z sekcji **zasady tworzenia kopii zapasowych** w magazynie Recovery Services. Wszystkie zasady utworzone podczas konfigurowania kopii zapasowej dla udziałów plików są wyświetlane z typem zasad jako udział plików platformy Azure.

Aby wyświetlić istniejące zasady tworzenia kopii zapasowych:

1. Otwórz magazyn Recovery Services, który został użyty do skonfigurowania kopii zapasowej udziału plików, a następnie w menu magazyn Recovery Services kliknij pozycję **zasady tworzenia kopii zapasowych** w obszarze Zarządzaj. Zostaną wyświetlone wszystkie zasady tworzenia kopii zapasowej skonfigurowane w magazynie.

   ![Wszystkie zasady tworzenia kopii zapasowych](./media/manage-afs-backup/all-backup-policies.png)

2. Aby wyświetlić zasady dotyczące udziału plików platformy Azure, wybierz pozycję **udział plików platformy Azure** z listy rozwijanej w prawym górnym rogu.

   ![Wybierz udział plików platformy Azure](./media/manage-afs-backup/azure-file-share.png)

Aby utworzyć nowe zasady tworzenia kopii zapasowych:

1. Kliknij pozycję **+ Dodaj** w bloku zasady kopii zapasowych.

   ![Nowe zasady tworzenia kopii zapasowych](./media/manage-afs-backup/new-backup-policy.png)

2. Wybierz pozycję **udział plików platformy Azure** jako **Typ zasad** w bloku **Dodaj** . Zostanie otwarty blok zasady tworzenia kopii zapasowych dla udziału plików platformy Azure. Określ nazwę zasad, częstotliwość tworzenia kopii zapasowych i zakres przechowywania dla punktów odzyskiwania. Po zdefiniowaniu zasad kliknij przycisk **OK** .

   ![Definiowanie zasad tworzenia kopii zapasowych](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modyfikowanie zasad

Możesz zmodyfikować zasady tworzenia kopii zapasowej, aby zmienić częstotliwość tworzenia kopii zapasowych lub zakres przechowywania.

Aby zmodyfikować zasady:

1. Otwórz magazyn Recovery Services, który został użyty do skonfigurowania kopii zapasowej udziału plików, a następnie w menu magazyn Recovery Services kliknij pozycję **zasady tworzenia kopii zapasowych** w sekcji Zarządzanie. Zostaną wyświetlone wszystkie zasady tworzenia kopii zapasowej skonfigurowane w magazynie.

   ![Wszystkie zasady tworzenia kopii zapasowych w magazynie](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Aby wyświetlić zasady dotyczące udziału plików platformy Azure, wybierz pozycję **udział plików platformy Azure** z listy rozwijanej w prawym górnym rogu. Kliknij zasady tworzenia kopii zapasowej, które chcesz zmodyfikować.

   ![Udział plików platformy Azure do zmodyfikowania](./media/manage-afs-backup/azure-file-share-modify.png)

3. Zostanie otwarty blok **harmonogram** . Edytuj zakres harmonogramu/przechowywania kopii zapasowej zgodnie z wymaganiami, a następnie kliknij przycisk **Zapisz**. W bloku zostanie wyświetlony komunikat "Aktualizacja w toku", a po pomyślnym zaktualizowaniu zasad zostaną wyświetlone komunikat "pomyślnie Zaktualizowano zasady tworzenia kopii zapasowej".

   ![Zapisz zmodyfikowane zasady](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Zatrzymywanie ochrony udziału plików

Istnieją dwa sposoby na zatrzymanie ochrony udziałów plików platformy Azure:

* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i *Usuń wszystkie punkty odzyskiwania*
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej *, ale pozostaw punkty odzyskiwania*

W ramach magazynu mogą występować koszty związane z opuszczeniem punktów odzyskiwania, ponieważ źródłowe migawki utworzone za pomocą Azure Backup zostaną zachowane. Zaletą pozostawienia punktów odzyskiwania jest natomiast możliwość późniejszego przywrócenia udziału plików, jeśli zaistnieje taka potrzeba. Aby uzyskać informacje o kosztu opuszczenia punktów odzyskiwania, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/backup/). Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie możesz przywrócić udziału plików.

Aby zatrzymać ochronę udziału plików platformy Azure:

1. Otwórz magazyn Recovery Services zawierający punkty odzyskiwania udziału plików, a następnie kliknij pozycję **elementy kopii zapasowej** w obszarze chronione elementy. Zostanie wyświetlona lista typów elementów kopii zapasowych.

   ![Elementy kopii zapasowej](./media/manage-afs-backup/backup-items.png)

2. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)** . Zostanie wyświetlona lista **elementów kopii zapasowych dla usługi (Azure Storage (Azure Files))** .

   ![Wybierz usługę Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Na liście **elementów kopii zapasowej (usługa Azure Storage (Azure Files))** wybierz element kopii zapasowej, dla którego chcesz zatrzymać ochronę.

4. Wybierz opcję **Zatrzymaj tworzenie kopii zapasowej** z menu bloku **elementu kopii zapasowej** .

   ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/manage-afs-backup/stop-backup.png)

5. W bloku **Zatrzymaj kopię zapasową** wybierz opcję **zachowania danych kopii zapasowej** lub **Usuń dane kopii zapasowej** , a następnie kliknij przycisk **Zatrzymaj kopię zapasową**

    ![Wybierz opcję Zachowaj lub Usuń dane kopii zapasowej](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Wznawianie ochrony udziału plików

Jeśli opcja **Zachowaj dane kopii zapasowej** została wybrana, gdy ochrona udziału plików została zatrzymana, można wznowić ochronę. Jeśli wybrano opcję **Usuń dane kopii zapasowej**, wznowienie ochrony udziału plików nie jest możliwe.

Aby wznowić ochronę udziału plików platformy Azure:

1. Otwórz magazyn Recovery Services zawierający punkty odzyskiwania udziału plików, a następnie kliknij pozycję **elementy kopii zapasowej** w obszarze chronione elementy. Zostanie wyświetlona lista typów elementów kopii zapasowych.

   ![Elementy kopii zapasowej do wznowienia](./media/manage-afs-backup/backup-items-resume.png)

2. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)** . Zostanie wyświetlona lista **elementów kopii zapasowych dla usługi (Azure Storage (Azure Files))** .

   ![Lista usługi Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Na liście **elementów kopii zapasowej (usługa Azure Storage (Azure Files))** wybierz element kopii zapasowej, dla którego chcesz wznowić ochronę.

4. Wybierz opcję **Wznów tworzenie kopii zapasowej** z menu bloku **elementu kopii zapasowej** .

   ![Wybierz pozycję Wznów wykonywanie kopii zapasowej](./media/manage-afs-backup/resume-backup.png)

5. Zostanie otwarty blok **zasady tworzenia kopii zapasowych** i można wybrać wybrane zasady w celu wznowienia tworzenia kopii zapasowej.

6. Po wybraniu żądanych **zasad kopii zapasowych**kliknij pozycję **Zapisz** . Zostanie wyświetlony komunikat "Aktualizacja w toku" w portalu. po pomyślnym wznowieniu tworzenia kopii zapasowej zostanie wyświetlony komunikat "pomyślnie Zaktualizowano zasady tworzenia kopii zapasowych dla chronionego udziału plików platformy Azure".

   ![Pomyślnie Zaktualizowano zasady tworzenia kopii zapasowych](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Kopię zapasową udziału plików można usunąć podczas zadania **zatrzymania tworzenia kopii zapasowej** lub w dowolnym momencie po zatrzymaniu ochrony. Wstrzymanie się z usunięciem punktów odzyskiwania przez określoną liczbę dni lub tygodni może być nawet korzystne. Podczas usuwania danych kopii zapasowej nie można wybrać określonych punktów odzyskiwania do usunięcia. Jeśli zdecydujesz się usunąć dane kopii zapasowej, wszystkie punkty odzyskiwania skojarzone z udziałem plików są usuwane.

W poniższej procedurze przyjęto założenie, że ochrona udziału plików została zatrzymana.

Aby usunąć dane kopii zapasowej dla udziału plików platformy Azure:

1. Gdy zadanie tworzenia kopii zapasowej zostanie zatrzymane, opcje **Wznów wykonywanie kopii zapasowej** i **Usuń kopię zapasową** są dostępne na pulpicie nawigacyjnym **elementu kopii zapasowej** . Kliknij opcję **Usuń dane kopii zapasowej** w menu bloku **elementu kopii zapasowej** .

   ![Usuwanie danych kopii zapasowej](./media/manage-afs-backup/delete-backup-data.png)

2. Zostanie otwarty blok **usuwanie danych kopii zapasowej** . Wpisz nazwę udziału plików, aby potwierdzić usunięcie. Opcjonalnie możesz podać **przyczynę** usunięcia lub **komentarza**. Kliknij przycisk **Usuń** po upewnieniu się, że usuwasz dane kopii zapasowej.

   ![Potwierdź usunięcie danych](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>Wyrejestruj konto magazynu

Jeśli chcesz chronić udziały plików na określonym koncie magazynu przy użyciu innego magazynu usług Recovery Services, najpierw [Zatrzymaj ochronę wszystkich udziałów plików](#stop-protection-on-a-file-share) na tym koncie magazynu. Następnie Wyrejestruj konto z bieżącego magazynu usług Recovery Services używanego do ochrony.

W poniższej procedurze przyjęto założenie, że ochrona została zatrzymana dla wszystkich udziałów plików na koncie magazynu, które chcesz wyrejestrować.

Aby wyrejestrować konto magazynu:

1. Otwórz magazyn usługi Recovery Services, w którym zarejestrowano konto magazynu.
2. Kliknij opcję **infrastruktura kopii zapasowych** w sekcji **Zarządzanie** w bloku **Przegląd** .

   ![Kliknij pozycję Infrastruktura kopii zapasowych](./media/manage-afs-backup/backup-infrastructure.png)

3. Zostanie otwarty blok **infrastruktura kopii zapasowych** . Kliknij pozycję **konta magazynu** w sekcji **konta usługi Azure Storage** w tym bloku.

   ![Kliknij pozycję konta magazynu](./media/manage-afs-backup/storage-accounts.png)

4. Po kliknięciu **konta magazynu**zostanie wyświetlona lista kont magazynu zarejestrowanych w magazynie.
5. Kliknij prawym przyciskiem myszy konto magazynu, które chcesz wyrejestrować, a następnie wybierz polecenie **Wyrejestruj**.

   ![Wybierz pozycję Wyrejestruj](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów dotyczących tworzenia kopii zapasowej/przywracania dla udziałów plików platformy Azure](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)
