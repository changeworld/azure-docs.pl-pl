---
title: Tworzenie kopii zapasowych udziałów plików platformy Azure w Azure Portal
description: Dowiedz się, jak używać Azure Portal do tworzenia kopii zapasowych udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b2e2053857147513a95b3ae72b82d55450ebcffa
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294515"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Tworzenie kopii zapasowych udziałów plików platformy Azure w magazynie Recovery Services

W tym artykule wyjaśniono, jak używać Azure Portal do tworzenia kopii zapasowych [udziałów plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

* Tworzenie magazynu usługi Recovery Services
* Odkryj udziałów plików i skonfiguruj kopie zapasowe
* Uruchomianie zadania tworzenia kopii zapasowej na żądanie w celu tworzenia punktu przywracania

## <a name="prerequisites"></a>Wymagania wstępne

* Zidentyfikuj lub Utwórz [magazyn Recovery Services](#create-a-recovery-services-vault) w tym samym regionie, w którym znajduje się konto magazynu obsługujące udział plików.

* Upewnij się, że udział plików jest obecny w jednym z [obsługiwanych typów kont magazynu](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Ograniczenia dotyczące tworzenia kopii zapasowej udziału plików platformy Azure w okresie korzystania z wersji zapoznawczej

Funkcja tworzenia kopii zapasowych udziałów plików platformy Azure jest dostępna w wersji zapoznawczej. Obsługiwane są udziały plików platformy Azure na kontach magazynu ogólnego przeznaczenia w wersji 1 i 2. Są to ograniczenia dotyczące tworzenia kopii zapasowych udziałów plików platformy Azure:

* Obsługa kopii zapasowych udziałów plików platformy Azure w ramach kont magazynu z replikacją [magazynu Strefowo nadmiarowego](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) jest obecnie ograniczona do [tych regionów](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Azure Backup obecnie obsługuje Konfigurowanie zaplanowanych raz dziennie kopii zapasowych udziałów plików platformy Azure.
* Maksymalna liczba zaplanowanych kopii zapasowych to jedna dziennie.
* Maksymalna liczba kopii zapasowych na żądanie to cztery dziennie.
* Aby zapobiec przypadkowemu usunięciu kopii zapasowych z magazynu usługi Recovery Services, użyj [blokad zasobów](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na koncie magazynu.
* Nie usuwaj migawek utworzonych przy użyciu usługi Azure Backup. Usunięcie migawek może spowodować utratę punktów odzyskiwania i/lub błędy przywracania.
* Nie usuwaj udziałów plików, które są chronione przez usługę Azure Backup. Bieżące rozwiązanie usunie wszystkie migawki wykonane przez Azure Backup po usunięciu udziału plików i w związku z tym wszystkie punkty przywracania zostaną utracone.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie magazyny korzystają z [magazynu geograficznie nadmiarowego (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zalecamy użycie GRS.

* Możesz użyć [magazynu lokalnie nadmiarowego (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) jako opcji niskiego kosztu.

Zmodyfikuj typ replikacji magazynu w następujący sposób:

1. W nowym magazynie kliknij pozycję **Właściwości** w sekcji **Ustawienia** .

2. W obszarze **Właściwości**, w obszarze **Konfiguracja kopii zapasowej**, kliknij przycisk **Aktualizuj**.

3. Wybierz typ replikacji magazynu i kliknij przycisk **Zapisz**.

    ![Aktualizacja konfiguracji kopii zapasowej](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu magazynu i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, należy ponownie utworzyć magazyn.
>

## <a name="discover-file-shares-and-configure-backup"></a>Odnajdź udziały plików i skonfiguruj kopię zapasową

1. W [Azure Portal](https://portal.azure.com/)otwórz magazyn Recovery Services, którego chcesz użyć do utworzenia kopii zapasowej udziału plików.

2. Na pulpicie nawigacyjnym **magazynu Recovery Services** wybierz pozycję **+ kopia zapasowa**.

   ![Magazyn usługi Recovery Services](./media/backup-afs/recovery-services-vault.png)

   a. W obszarze **cel kopii zapasowej**ustaw, **gdzie działa Twoje obciążenie?** na **platformę Azure**.

    ![Wybierz udział plików platformy Azure jako cel kopii zapasowej](./media/backup-afs/backup-goal.png)

    b.    W obszarze **co chcesz utworzyć kopię zapasową**wybierz pozycję **udział plików platformy Azure** z menu rozwijanego.

    d.    Kliknij pozycję **kopia zapasowa** , aby zarejestrować rozszerzenie udziału plików platformy Azure w magazynie.

      ![Kliknij pozycję Kopia zapasowa, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-afs/register-extension.png)

3. Po kliknięciu przycisku **kopia zapasowa**zostanie otwarty blok kopia zapasowa i zostanie wyświetlony komunikat z prośbą o wybranie konta magazynu z listy odnalezionych obsługiwanych kont magazynu. Są one skojarzone z tym magazynem lub znajdują się w tym samym regionie co magazyn, ale nie są jeszcze skojarzone z żadnym magazynem Recovery Services.

   ![Wybieranie konta magazynu](./media/backup-afs/select-storage-account.png)

4. Z listy odnalezionych kont magazynu wybierz konto, a następnie kliknij przycisk **OK**. Na platformie Azure zostanie wykonane wyszukiwanie udziałów plików w ramach magazynu, dla których można utworzyć kopię zapasową. Jeśli niedawno dodano udziały plików i nie są one widoczne na liście, poproś o pewien czas na wyświetlenie udziałów plików.

    ![Odnajdywanie udziałów plików](./media/backup-afs/discovering-file-shares.png)

5. Z listy **udziały plików** wybierz co najmniej jeden udział plików, dla którego chcesz utworzyć kopię zapasową, a następnie kliknij przycisk **OK**.

6. Po wybraniu udziałów plików menu **kopia zapasowa** jest przełączane do **zasad tworzenia kopii zapasowych**. Z tego menu Wybierz istniejące zasady kopii zapasowej lub Utwórz nowe, a następnie kliknij przycisk **Włącz kopię zapasową**.

    ![Wybieranie pozycji Zasady kopii zapasowych](./media/backup-afs/select-backup-policy.png)

Po ustanowieniu zasad tworzenia kopii zapasowych w zaplanowanym czasie zostanie utworzona migawka udziałów plików, a punkt odzyskiwania zostanie zachowany przez wybrany okres.

## <a name="create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie

Czasami może być konieczne wygenerowanie migawki kopii zapasowej lub punktu odzyskiwania poza godzinami zaplanowanymi w ramach zasad tworzenia kopii zapasowych. Typowym powodem generowania kopii zapasowej na żądanie jest prawidłowe po skonfigurowaniu zasad tworzenia kopii zapasowych. Na podstawie harmonogramu w ramach zasad tworzenia kopii zapasowych może upłynąć wiele godzin lub dni zanim migawka zostanie utworzona. Aby chronić dane przed rozpoczęciem obowiązywania zasad tworzenia kopii zapasowych, zainicjuj tworzenie kopii zapasowej na żądanie. Tworzenie kopii zapasowej na żądanie jest często wymagane przed wprowadzeniem planowanych zmian w udziałach plików.

### <a name="to-create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie

1. Otwórz magazyn Recovery Services użyty do utworzenia kopii zapasowej udziału plików, a następnie kliknij pozycję **elementy kopii zapasowej** w sekcji **chronione elementy** w bloku **Przegląd** .

   ![Kliknij pozycję elementy kopii zapasowej](./media/backup-afs/backup-items.png)

2. Po kliknięciu **pozycji kopie zapasowe**, nowy blok zawierający listę wszystkich **typów zarządzania kopiami zapasowymi** będzie wyświetlany obok bloku **Przegląd** w następujący sposób:

   ![Lista typów zarządzania kopiami zapasowymi](./media/backup-afs/backup-management-types.png)

3. Z listy **typów zarządzania kopiami zapasowymi**wybierz pozycję **Azure Storage (Azure Files)** . Zostanie wyświetlona lista wszystkich udziałów plików i odpowiednich kont magazynu, których kopię zapasową utworzono przy użyciu tego magazynu.

   ![Elementy kopii zapasowej usługi Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

4. Z listy udziałów plików platformy Azure wybierz żądany udział plików. Pojawią się szczegóły **elementu kopia zapasowa** . W menu **elementu kopia zapasowa** kliknij pozycję **Utwórz kopię zapasową teraz**. Ponieważ jest to zadanie tworzenia kopii zapasowej na żądanie, nie istnieją żadne zasady przechowywania skojarzone z punktem odzyskiwania.

   ![Kliknij pozycję Utwórz kopię zapasową teraz](./media/backup-afs/backup-now.png)

5. Zostanie otwarty blok **kopia zapasowa teraz** . Określ ostatni dzień zachowywania punktu odzyskiwania. Możesz mieć maksymalnie 10 lat na tworzenie kopii zapasowej na żądanie.

   ![Wybierz datę przechowywania](./media/backup-afs/retention-date.png)

6. Kliknij przycisk **OK** , aby potwierdzić uruchomienie zadania tworzenia kopii zapasowej na żądanie.

7. Monitoruj powiadomienia portalu, aby śledzić Kończenie wykonywania zadania tworzenia kopii zapasowej. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > **w toku**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić udziały plików platformy Azure](restore-afs.md)

* Dowiedz się, jak [zarządzać kopiami zapasowymi udziałów plików platformy Azure](manage-afs-backup.md)
