---
title: Zarządzanie kopiami zapasowymi udziałów plików platformy Azure
description: W tym artykule opisano typowe zadania dotyczące zarządzania i monitorowania udziałów plików platformy Azure, które są archiwizowane przez usługę Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247659"
---
# <a name="manage-azure-file-share-backups"></a>Zarządzanie kopiami zapasowymi udziałów plików platformy Azure

W tym artykule opisano typowe zadania związane z zarządzaniem i monitorowaniem udziałów plików platformy Azure, które są archiwizowane przez [usługę Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Dowiesz się, jak wykonywać zadania zarządzania w magazynie usług odzyskiwania.

## <a name="monitor-jobs"></a>Monitorowanie zadań

Po wyzwoleniu operacji tworzenia kopii zapasowej lub przywracania usługa tworzenia kopii zapasowych tworzy zadanie śledzenia. Postęp wszystkich zadań możesz monitorować na stronie **Zadania tworzenia kopii zapasowej**.

Aby otworzyć stronę **Zadania tworzenia kopii zapasowej**:

1. Otwórz magazyn usług odzyskiwania używany do konfigurowania kopii zapasowej dla udziałów plików. W **okienku Przegląd** wybierz pozycję **Zadania kopii zapasowej** w sekcji **Monitorowanie.**

   ![Zadania tworzenia kopii zapasowych w sekcji Monitorowanie](./media/manage-afs-backup/backup-jobs.png)

1. Po wybraniu **przycisku OK**w okienku **Zadania kopii zapasowej** zostanie wyświetlenie stanu wszystkich zadań. Wybierz nazwę obciążenia odpowiadającą udziałowi plików, który chcesz monitorować.

   ![Nazwa obciążenia](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Tworzenie nowych zasad

Można utworzyć nową zasadę tworzenia kopii zapasowych udziałów plików platformy Azure z sekcji **Zasady kopii zapasowej** w przechowalni usług odzyskiwania. Wszystkie zasady utworzone podczas konfigurowania kopii zapasowej dla udziałów plików są wyświetlane przy **typie zasad** jako **udziale plików platformy Azure**.

Aby wyświetlić istniejące zasady tworzenia kopii zapasowych:

1. Otwórz magazyn usług odzyskiwania używany do konfigurowania kopii zapasowej dla udziału plików. W menu magazynu usług odzyskiwania wybierz polecenie **Zasady tworzenia kopii zapasowych** w sekcji **Zarządzanie.** Pojawią się wszystkie zasady tworzenia kopii zapasowych skonfigurowane w przechowalni.

   ![Wszystkie zasady tworzenia kopii zapasowych](./media/manage-afs-backup/all-backup-policies.png)

1. Aby wyświetlić zasady specyficzne dla **udziału plików platformy Azure,** wybierz **udział plików platformy Azure** z listy rozwijanej w prawym górnym rogu.

   ![Wybieranie udziału plików platformy Azure](./media/manage-afs-backup/azure-file-share.png)

Aby utworzyć nową zasadę tworzenia kopii zapasowych:

1. W okienku **Zasady tworzenia kopii zapasowych** wybierz pozycję **+ Dodaj**.

   ![Nowe zasady tworzenia kopii zapasowych](./media/manage-afs-backup/new-backup-policy.png)

1. W okienku **Dodawanie** wybierz pozycję **Udział plików platformy Azure** jako typ **zasad**. Zostanie otwarte okienko **zasad kopii zapasowej** dla udziału plików platformy **Azure.** Określ nazwę zasad, częstotliwość tworzenia kopii zapasowych i zakres przechowywania dla punktów odzyskiwania. Po zdefiniowaniu zasady wybierz przycisk **OK**.

   ![Definiowanie zasad tworzenia kopii zapasowych](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modyfikowanie zasad

Można zmodyfikować zasady tworzenia kopii zapasowych, aby zmienić częstotliwość tworzenia kopii zapasowej lub zakres przechowywania.

Aby zmodyfikować zasady:

1. Otwórz magazyn usług odzyskiwania używany do konfigurowania kopii zapasowej dla udziału plików. W menu magazynu usług odzyskiwania wybierz polecenie **Zasady tworzenia kopii zapasowych** w sekcji **Zarządzanie.** Pojawią się wszystkie zasady tworzenia kopii zapasowych skonfigurowane w przechowalni.

   ![Wszystkie zasady tworzenia kopii zapasowych w magazynie](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Aby wyświetlić zasady specyficzne dla udziału plików platformy Azure, wybierz **udział plików platformy Azure** z listy rozwijanej w prawym górnym rogu. Wybierz zasady tworzenia kopii zapasowych, które chcesz zmodyfikować.

   ![Udział plików platformy Azure do zmodyfikowania](./media/manage-afs-backup/azure-file-share-modify.png)

1. Zostanie otwarte okienko **Harmonogram.** Edytuj **harmonogram tworzenia kopii zapasowych** i **zakres przechowywania** zgodnie z wymaganiami i wybierz pozycję **Zapisz**. W okienku zostanie wyświetlony komunikat "Aktualizuj w toku". Po pomyślnym zaktualizowaniu zmian zasad zostanie wyświetlony komunikat "Pomyślnie zaktualizowano zasady tworzenia kopii zapasowych".

   ![Zapisywanie zmodyfikowanych zasad](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Zatrzymywanie ochrony udziału plików

Istnieją dwa sposoby na zatrzymanie ochrony udziałów plików platformy Azure:

* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowych i *usuń wszystkie punkty odzyskiwania*.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowych, ale *pozostaw punkty odzyskiwania*.

Może istnieć koszt związany z opuszczeniem punktów odzyskiwania w magazynie, ponieważ podstawowe migawki utworzone przez usługę Azure Backup zostaną zachowane. Zaletą pozostawienia punktów odzyskiwania jest to, że można przywrócić udział plików później. Aby uzyskać informacje na temat kosztów opuszczenia punktów odzyskiwania, zobacz [szczegóły cen](https://azure.microsoft.com/pricing/details/backup/). Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie możesz przywrócić udziału plików.

Aby zatrzymać ochronę udziału plików platformy Azure:

1. Otwórz magazyn usług odzyskiwania zawierający punkty odzyskiwania udziału plików. Wybierz **pozycję Zapasy zapasowe** w sekcji **Elementy chronione.** Zostanie wyświetlona lista typów elementów kopii zapasowej.

   ![Elementy kopii zapasowej](./media/manage-afs-backup/backup-items.png)

1. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)**. Pojawi się lista **elementów kopii zapasowej (usługi Azure Storage (Azure Files)).**

   ![Wybierz usługę Azure Storage (usługi Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na liście **Elementy kopii zapasowej (Usługi Azure Storage (Azure Files))** wybierz element kopii zapasowej, dla którego chcesz zatrzymać ochronę.

1. Wybierz opcję **Zatrzymaj tworzenie kopii zapasowej.**

   ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/manage-afs-backup/stop-backup.png)

1. W okienku **Zatrzymaj kopię zapasową** wybierz pozycję **Zachowaj dane kopii zapasowej** lub **Usuń dane kopii zapasowej**. Następnie wybierz **pozycję Zatrzymaj tworzenie kopii zapasowej**.

    ![Wybierz pozycję Zachowaj dane kopii zapasowej lub Usuń dane kopii zapasowej](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Wznawianie ochrony udziału plików

Jeśli opcja **Zachowaj dane kopii zapasowej** została wybrana, gdy ochrona udziału plików została zatrzymana, można wznowić ochronę. Jeśli wybrano opcję **Usuń dane kopii zapasowej,** ochrona udziału plików nie może zostać wznowiona.

Aby wznowić ochronę udziału plików platformy Azure:

1. Otwórz magazyn usług odzyskiwania zawierający punkty odzyskiwania udziału plików. Wybierz **pozycję Zapasy zapasowe** w sekcji **Elementy chronione.** Zostanie wyświetlona lista typów elementów kopii zapasowej.

   ![Elementy kopii zapasowej do wznowienia](./media/manage-afs-backup/backup-items-resume.png)

1. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)**. Pojawi się lista **elementów kopii zapasowej (usługi Azure Storage (Azure Files)).**

   ![Lista usługi Azure Storage (usługi Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na liście **Elementy kopii zapasowej (Usługi Azure Storage (Azure Files))** wybierz element kopii zapasowej, dla którego chcesz wznowić ochronę.

1. Wybierz opcję **Wznów kopię zapasową.**

   ![Wybierz pozycję Wznów kopię zapasową](./media/manage-afs-backup/resume-backup.png)

1. Zostanie otwarte **okienko Zasady tworzenia kopii zapasowych.** Wybierz wybraną zasadę, aby wznowić tworzenie kopii zapasowych.

1. Po wybraniu zasady tworzenia kopii zapasowych wybierz pozycję **Zapisz**. W portalu zostanie wyświetlony komunikat "Aktualizuj w toku". Po pomyślnym wznowieniu tworzenia kopii zapasowej zostanie wyświetlony komunikat "Pomyślnie zaktualizowano zasady tworzenia kopii zapasowych dla chronionego udziału plików platformy Azure".

   ![Pomyślnie zaktualizowano zasady tworzenia kopii zapasowych](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Kopię zapasową udziału plików można usunąć podczas zadania **Zatrzymaj tworzenie kopii zapasowej** lub w dowolnym momencie po zatrzymaniu ochrony. Może to być korzystne czekać dni lub nawet tygodnie przed usunięciem punktów odzyskiwania. Po usunięciu danych kopii zapasowej nie można wybrać określonych punktów odzyskiwania do usunięcia. Jeśli zdecydujesz się usunąć dane kopii zapasowej, usuniesz wszystkie punkty odzyskiwania skojarzone z udziałem plików.

Poniższa procedura zakłada, że ochrona została zatrzymana dla udziału plików.

Aby usunąć dane kopii zapasowej dla udziału plików platformy Azure:

1. Po zatrzymaniu zadania tworzenia kopii zapasowej opcje **Wznów kopię zapasową** i **Usuń dane kopii zapasowej** są dostępne na **pulpicie** nawigacyjnym elementu kopii zapasowej. Wybierz opcję **Usuń dane kopii zapasowej.**

   ![Usuwanie danych kopii zapasowej](./media/manage-afs-backup/delete-backup-data.png)

1. Zostanie otwarte okienko **Usuń dane kopii zapasowej.** Wprowadź nazwę udziału plików, aby potwierdzić usunięcie. Opcjonalnie podaj więcej informacji w **polach Przyczyna** lub **Komentarze.** Po upewnieniu się o usunięciu danych kopii zapasowej wybierz pozycję **Usuń**.

   ![Potwierdź usuwanie danych](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Wyrejestrowanie konta magazynu

Aby chronić udziały plików na określonym koncie magazynu przy użyciu innego magazynu usług odzyskiwania, najpierw [zatrzymaj ochronę wszystkich udziałów plików na](#stop-protection-on-a-file-share) tym koncie magazynu. Następnie wyrejestruj konto z bieżącego magazynu usług odzyskiwania używanego do ochrony.

Poniższa procedura zakłada, że ochrona została zatrzymana dla wszystkich udziałów plików na koncie magazynu, które chcesz wyrejestrować.

Aby wyrejestrować konto magazynu:

1. Otwórz magazyn usług odzyskiwania, w którym jest zarejestrowane twoje konto magazynu.
1. W okienku **Przegląd** wybierz opcję **Infrastruktura kopii zapasowych** w sekcji **Zarządzanie.**

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/manage-afs-backup/backup-infrastructure.png)

1. Zostanie otwarte **okienko Infrastruktura kopii zapasowych.** Wybierz **konta magazynu** w sekcji Konta usługi Azure **Storage.**

   ![Wybierz konta magazynu](./media/manage-afs-backup/storage-accounts.png)

1. Po **wybraniu opcji Konta magazynu**zostanie wyświetlona lista kont magazynu zarejestrowanych w przechowalni.
1. Kliknij prawym przyciskiem myszy konto magazynu, które chcesz wyrejestrować, a następnie wybierz polecenie **Wyrejestruj**.

   ![Wybierz pozycję Wyrejestruj](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z tworzeniem kopii zapasowej udziałów plików platformy Azure](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files).
