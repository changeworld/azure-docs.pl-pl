---
title: Samouczek — Tworzenie kopii zapasowych Azure Files udziałów plików
description: W tym samouczku dowiesz się, jak skonfigurować magazyn Recovery Services i utworzyć kopię zapasową udziałów plików platformy Azure przy użyciu Azure Portal.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293933"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Tworzenie kopii zapasowych udziałów plików platformy Azure w Azure Portal

W tym samouczku wyjaśniono, jak używać Azure Portal do tworzenia kopii zapasowych [udziałów plików platformy Azure](../storage/files/storage-files-introduction.md).

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Konfigurowanie magazynu usługi Recovery Services na potrzeby tworzenia kopii zapasowej w usłudze Azure Files
> * Uruchomianie zadania tworzenia kopii zapasowej na żądanie w celu tworzenia punktu przywracania

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem kopii zapasowej udziału plików platformy Azure sprawdź, czy znajduje się on na [koncie magazynu jednego z obsługiwanych typów](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Jeśli tak, oznacza to, że możesz chronić swoje udziały plików.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Ograniczenia dotyczące tworzenia kopii zapasowych udziałów plików platformy Azure w wersji zapoznawczej

Funkcja tworzenia kopii zapasowych udziałów plików platformy Azure jest dostępna w wersji zapoznawczej. Obsługiwane są udziały plików platformy Azure na kontach magazynu ogólnego przeznaczenia w wersji 1 i 2. Następujące scenariusze tworzenia kopii zapasowej nie są obsługiwane w przypadku udziałów plików platformy Azure:

* Nie ma dostępnego interfejsu wiersza polecenia do ochrony usługi Azure Files z poziomu usługi Azure Backup.
* Maksymalna liczba zaplanowanych kopii zapasowych to jedna dziennie.
* Maksymalna liczba kopii zapasowych na żądanie to cztery dziennie.
* Aby zapobiec przypadkowemu usunięciu kopii zapasowych z magazynu usługi Recovery Services, użyj [blokad zasobów](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na koncie magazynu.
* Nie usuwaj migawek utworzonych przy użyciu usługi Azure Backup. Usunięcie migawek może spowodować utratę punktów odzyskiwania i/lub błędy przywracania.
* Nie usuwaj udziałów plików, które są chronione przez usługę Azure Backup. Bieżące rozwiązanie usunie wszystkie migawki utworzone przez usługę Azure Backup, gdy udział plików zostanie usunięty, i tym samym utraci wszystkie punkty przywracania.

Tworzenie kopii zapasowych udziałów plików platformy Azure w ramach kont magazynu z replikacją [magazynu Strefowo nadmiarowego](../storage/common/storage-redundancy-zrs.md) (ZRS) jest obecnie dostępna tylko w regionach Środkowe stany USA (CUS), Wschodnie stany USA (EUS), Wschodnie stany USA 2 (EUS2), Europa Północna (ne), Azja Południowo-Wschodnia (Sea), Europa Zachodnia, USA 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurowanie kopii zapasowej udziału plików platformy Azure

W tym samouczku przyjęto założenie, że ustanowiono już udział plików platformy Azure. Aby utworzyć kopię zapasową udziału plików platformy Azure:

1. Utwórz magazyn usługi Recovery Services w tym samym regionie, co udziału plików. Jeśli magazyn już istnieje, otwórz stronę z jego przeglądem, a następnie kliknij pozycję **Kopia zapasowa**.

    ![Kliknij pozycję Kopia zapasowa na stronie przeglądu magazynu](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. W menu **cel kopii zapasowej** , z **czego chcesz utworzyć kopię zapasową?** wybierz pozycję udział plików platformy Azure.

    ![Wybieranie udział plików platformy Azure jako celu kopii zapasowej](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. Kliknij pozycję **Utwórz kopię zapasową**, aby skonfigurować udział plików platformy Azure na potrzeby magazynu usługi Recovery Services.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/tutorial-backup-azure-files/set-backup-goal.png)

    Po skojarzeniu magazynu z udziałem plików platformy Azure zostanie otwarte menu kopia zapasowa i zostanie wyświetlony komunikat z prośbą o wybranie konta magazynu. W menu zostaną wyświetlone wszystkie obsługiwane konta magazynu w regionie, w którym znajduje się magazyn, który nie jest już skojarzony z magazynem Recovery Services.

   ![Wybierz konto magazynu](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. Na liście kont magazynu wybierz konto, a następnie kliknij przycisk **OK**. Na platformie Azure zostanie wykonane wyszukiwanie udziałów plików w ramach magazynu, dla których można utworzyć kopię zapasową. Jeśli niedawno dodano udziały plików i nie ma ich na liście, poczekaj chwilę na ich pojawienie się.

   ![Trwa odnajdywanie udziałów plików](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. Z listy **udziały plików** wybierz co najmniej jeden udział plików, dla którego chcesz utworzyć kopię zapasową, a następnie kliknij przycisk **OK**.

6. Po wybraniu udziałów plików menu Kopia zapasowa zostanie przełączone na menu **Zasady tworzenie kopii zapasowych**. Z poziomu tego menu wybierz istniejące zasady tworzenia kopii zapasowych lub utwórz nowe, a następnie kliknij pozycję **Włącz kopię zapasową**.

   ![Wybierz zasady tworzenia kopii zapasowej lub Utwórz nowe](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    Po ustanowieniu zasad tworzenia kopii zapasowych w zaplanowanym czasie zostanie utworzona migawka udziałów plików, a punkt odzyskiwania zostanie zachowany przez wybrany okres.

## <a name="create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie

Po skonfigurowaniu zasad tworzenia kopii zapasowych należy utworzyć kopię zapasową na żądanie, aby upewnić się, że dane będą chronione do momentu następnej zaplanowanej kopii zapasowej.

### <a name="to-create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie

1. Otwórz magazyn usługi Recovery Services zawierający punkty odzyskiwania udziałów plików, a następnie kliknij pozycję **Elementy kopii zapasowej**. Zostanie wyświetlona lista typów elementów kopii zapasowych.

   ![Lista elementów kopii zapasowej](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. Z listy wybierz pozycję **Azure Storage (Azure Files)** . Zostanie wyświetlona lista udziałów plików platformy Azure.

   ![Lista udziałów plików platformy Azure](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. Z listy udziałów plików platformy Azure wybierz żądany udział plików. Zostanie otwarte menu elementu kopii zapasowej dla wybranego udziału plików.

   ![Menu elementu kopii zapasowej dla wybranego udziału plików](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. W menu Element kopii zapasowej kliknij pozycję **Utwórz kopię zapasową teraz**. Ponieważ jest to zadanie tworzenia kopii zapasowej na żądanie, nie istnieją żadne zasady przechowywania skojarzone z punktem odzyskiwania. Zostanie otwarte okno dialogowe **Utwórz kopię zapasową teraz**. Określ ostatni dzień zachowywania punktu odzyskiwania.

   ![Wybierz datę przechowywania punktu odzyskiwania](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem wykonano następujące czynności przy użyciu witryny Azure Portal:

> [!div class="checklist"]
>
> * Konfigurowanie magazynu usługi Recovery Services na potrzeby tworzenia kopii zapasowej w usłudze Azure Files
> * Uruchomianie zadania tworzenia kopii zapasowej na żądanie w celu tworzenia punktu przywracania

Przejdź do następnego artykułu, aby przywrócić kopię zapasową udziału plików platformy Azure.

> [!div class="nextstepaction"]
> [Przywracanie z kopii zapasowej udziałów plików platformy Azure](restore-afs.md)
