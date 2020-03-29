---
title: Dziele zapasowe udziałów plików platformy Azure w witrynie Azure portal
description: Dowiedz się, jak korzystać z witryny Azure Portal do generacji kopii zapasowych udziałów plików platformy Azure w magazynie usług odzyskiwania
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938154"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Generdyzuje zapas udziałów plików platformy Azure w magazynie usług odzyskiwania

W tym artykule wyjaśniono, jak używać witryny Azure portal do 300 [000 000 000 000 000 000](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)000 000

W tym artykule dowiesz się, jak:

* Utwórz magazyn usługi Recovery Services.
* Odnajduj udziały plików i konfiguruj kopie zapasowe.
* Uruchom zadanie kopii zapasowej na żądanie, aby utworzyć punkt przywracania.

## <a name="prerequisites"></a>Wymagania wstępne

* Identyfikowanie lub tworzenie [magazynu usług odzyskiwania](#create-a-recovery-services-vault) w tym samym regionie co konto magazynu, na którym znajduje się udział plików.
* Upewnij się, że udział plików jest obecny w jednym z [obsługiwanych typów kont magazynu](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Ograniczenia dotyczące tworzenia kopii zapasowej udziału plików platformy Azure w okresie korzystania z wersji zapoznawczej

Funkcja tworzenia kopii zapasowych udziałów plików platformy Azure jest dostępna w wersji zapoznawczej. Obsługiwane są udziały plików platformy Azure na kontach magazynu ogólnego przeznaczenia w wersji 1 i 2. Oto ograniczenia dotyczące tworzenia kopii zapasowych udziałów plików platformy Azure:

* Obsługa tworzenia kopii zapasowych udziałów plików platformy Azure na kontach magazynu z replikacją [magazynu nadmiarowego strefy](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) jest obecnie ograniczona do [tych regionów.](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)
* Usługa Azure Backup obsługuje obecnie konfigurowanie zaplanowanych kopii zapasowych raz dziennie udziałów plików platformy Azure.
* Maksymalna liczba zaplanowanych kopii zapasowych to jedna dziennie.
* Maksymalna liczba kopii zapasowych na żądanie to cztery dziennie.
* Użyj [blokad zasobów](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na koncie magazynu, aby zapobiec przypadkowemu usunięciu kopii zapasowych w magazynie usług odzyskiwania.
* Nie usuwaj migawek utworzonych przez usługę Azure Backup. Usuwanie migawek może spowodować utratę punktów odzyskiwania lub przywracanie błędów.
* Nie usuwaj udziałów plików chronionych przez usługę Azure Backup. Bieżące rozwiązanie usuwa wszystkie migawki wykonane przez usługę Azure Backup po usunięciu udziału plików, więc wszystkie punkty przywracania zostaną utracone.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie przechowalnia używają [magazynu geograficznie nadmiarowego (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zaleca się użycie GRS.
* Można użyć [lokalnie nadmiarowego magazynu (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) jako opcji taniej.

Aby zmodyfikować typ replikacji magazynu:

1. W nowym przechowalni wybierz **pozycję Właściwości** w sekcji **Ustawienia.**

1. Na stronie **Właściwości** w obszarze **Konfiguracja kopii zapasowej**wybierz pozycję **Aktualizuj**.

1. Wybierz typ replikacji magazynu i wybierz pozycję **Zapisz**.

    ![Aktualizowanie konfiguracji kopii zapasowej](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu przechowalni i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, musisz ponownie utworzyć przechowalnię.
>

## <a name="discover-file-shares-and-configure-backup"></a>Odnajduj udziały plików i konfiguruj kopię zapasową

1. W [witrynie Azure portal](https://portal.azure.com/)otwórz magazyn usług odzyskiwania, którego chcesz użyć do utworzenia kopii zapasowej udziału plików.

1. Na **pulpicie** nawigacyjnym magazynu usług odzyskiwania wybierz pozycję **+Kopia zapasowa**.

   ![Magazyn usługi Recovery Services](./media/backup-afs/recovery-services-vault.png)

    a. W **celu tworzenia kopii zapasowej,** **Azure** **ustawić, gdzie jest uruchomione obciążenie?**

    ![Wybieranie udziału plików platformy Azure jako celu tworzenia kopii zapasowej](./media/backup-afs/backup-goal.png)

    b.  W **obszarze Co chcesz wykonać kopii zapasowej?**, wybierz pozycję Udział plików platformy **Azure** z listy rozwijanej.

    d.  Wybierz **pozycję Kopia zapasowa,** aby zarejestrować rozszerzenie udziału plików platformy Azure w przechowalni.

    ![Wybierz pozycję Kopia zapasowa, aby skojarzyć udział plików platformy Azure z przechowalnią](./media/backup-afs/register-extension.png)

1. Po **wybraniu opcji Kopia zapasowa**zostanie otwarte **okienko Kopia zapasowa** i zostanie wyświetlone monit o wybranie konta magazynu z listy odnalezionych obsługiwanych kont magazynu. Są one skojarzone z tym magazynem lub obecne w tym samym regionie co magazyn, ale nie są jeszcze skojarzone z żadnym magazynem usług odzyskiwania.

   ![Wybieranie konta magazynu](./media/backup-afs/select-storage-account.png)

1. Z listy odnalezionych kont magazynu wybierz konto i wybierz przycisk **OK**. Platforma Azure przeszukuje konto magazynu w poszukiwaniu udziałów plików, których można wyw. Jeśli ostatnio dodano udziały plików i nie widzisz ich na liście, pozostaw trochę czasu na wyświetlenie udziałów plików.

    ![Odnajdowanie udziałów plików](./media/backup-afs/discovering-file-shares.png)

1. Z listy **Udziały plików** wybierz jeden lub więcej udziałów plików, których chcesz udostępnić. Kliknij przycisk **OK**.

1. Po wybraniu udziałów plików menu **Kopia zapasowa** przełączy się na **zasady kopii zapasowej**. Z tego menu wybierz istniejącą zasadę tworzenia kopii zapasowych lub utwórz nową. Następnie wybierz pozycję **Włącz kopię zapasową**.

    ![Wybieranie pozycji Zasady kopii zapasowych](./media/backup-afs/select-backup-policy.png)

Po ustawieniu zasad tworzenia kopii zapasowych migawka udziałów plików jest pobierana w zaplanowanym czasie. Punkt odzyskiwania jest również zachowywany dla wybranego okresu.

## <a name="create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie

Od czasu do czasu można wygenerować migawkę kopii zapasowej lub punkt odzyskiwania poza godzinami zaplanowanych w zasadach tworzenia kopii zapasowych. Częstym powodem generowania kopii zapasowej na żądanie jest zaraz po skonfigurowaniu zasad tworzenia kopii zapasowych. Na podstawie harmonogramu w zasadach tworzenia kopii zapasowych może to być godziny lub dni, aż zostanie pobrana migawka. Aby chronić dane przed rozpoczęciem obowiązywania zasad tworzenia kopii zapasowych, zainicjuj tworzenie kopii zapasowej na żądanie. Tworzenie kopii zapasowej na żądanie jest często wymagane przed wprowadzeniem planowanych zmian w udziałach plików.

### <a name="create-a-backup-job-on-demand"></a>Tworzenie zadania tworzenia kopii zapasowej na żądanie

1. Otwórz magazyn usług odzyskiwania używany do utworzenia kopii zapasowej udziału plików. W okienku **Przegląd** wybierz pozycję **Zapas elementów** w sekcji **Chronione elementy.**

   ![Wybieranie elementów kopii zapasowej](./media/backup-afs/backup-items.png)

1. Po **wybraniu pozycji kopii zapasowej**obok okienka Przegląd pojawi się nowe okienko z listą wszystkich **typów zarządzania kopiami zapasowymi.** **Overview**

   ![Lista typów zarządzania kopiami zapasowymi](./media/backup-afs/backup-management-types.png)

1. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Usługa Azure Storage (Usługi Azure Files).** Zobaczysz listę wszystkich udziałów plików i odpowiadające im konta magazynu, którego kopię zapasową jest przy użyciu tego magazynu.

   ![Elementy kopii zapasowej usługi Azure Storage (Usługi Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Z listy udziałów plików platformy Azure wybierz odpowiedni udział plików. Zostanie wyświetlone szczegóły **elementu kopii zapasowej.** W menu **Element kopii zapasowej** wybierz polecenie Kopia **zapasowa teraz**. Ponieważ to zadanie tworzenia kopii zapasowej jest na żądanie, nie ma żadnych zasad przechowywania skojarzonych z punktem odzyskiwania.

   ![Wybierz kopię zapasową teraz](./media/backup-afs/backup-now.png)

1. Zostanie otwarte okienko **Kopia zapasowa teraz.** Określ ostatni dzień zachowywania punktu odzyskiwania. Maksymalna rezerwa może wynosić 10 lat w przypadku kopii zapasowej na żądanie.

   ![Wybierz datę przechowywania](./media/backup-afs/retention-date.png)

1. Wybierz **przycisk OK,** aby potwierdzić zadanie tworzenia kopii zapasowej na żądanie, które jest uruchamiane.

1. Monitorowanie powiadomień portalu w celu śledzenia zakończenia wykonywania zadania tworzenia kopii zapasowej. Postęp zadania można monitorować na pulpicie nawigacyjnym przechowalni. Wybierz **pozycję Zadania** > tworzenia kopii zapasowych w**toku**.

## <a name="next-steps"></a>Następne kroki

Instrukcje:
* [Przywracanie udziałów plików platformy Azure](restore-afs.md)
* [Zarządzanie kopiami zapasowymi udziału plików platformy Azure](manage-afs-backup.md)
