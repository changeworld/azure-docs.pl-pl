---
title: Samouczek — kopie zapasowe udziałów plików usługi Azure Files
description: W tym samouczku dowiesz się, jak skonfigurować magazyn usług Recovery Services i udostępnić zapasy udziałów plików platformy Azure za pomocą portalu Azure.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293933"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Dziele zapasowe udziałów plików platformy Azure w witrynie Azure portal

W tym samouczku wyjaśniono, jak używać witryny Azure portal do 300 [000 000 000](../storage/files/storage-files-introduction.md)000 000 000 000 000

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Konfigurowanie magazynu usługi Recovery Services na potrzeby tworzenia kopii zapasowej w usłudze Azure Files
> * Uruchomianie zadania tworzenia kopii zapasowej na żądanie w celu tworzenia punktu przywracania

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem kopii zapasowej udziału plików platformy Azure sprawdź, czy znajduje się on na [koncie magazynu jednego z obsługiwanych typów](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Jeśli tak, oznacza to, że możesz chronić swoje udziały plików.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Ograniczenia dotyczące tworzenia kopii zapasowej udziału plików platformy Azure w okresie korzystania z wersji zapoznawczej

Funkcja tworzenia kopii zapasowych udziałów plików platformy Azure jest dostępna w wersji zapoznawczej. Obsługiwane są udziały plików platformy Azure na kontach magazynu ogólnego przeznaczenia w wersji 1 i 2. Następujące scenariusze tworzenia kopii zapasowej nie są obsługiwane w przypadku udziałów plików platformy Azure:

* Nie ma dostępnego interfejsu wiersza polecenia do ochrony usługi Azure Files z poziomu usługi Azure Backup.
* Maksymalna liczba zaplanowanych kopii zapasowych to jedna dziennie.
* Maksymalna liczba kopii zapasowych na żądanie to cztery dziennie.
* Użyj [blokad zasobów](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na koncie magazynu, aby zapobiec przypadkowemu usunięciu kopii zapasowych w magazynie usług odzyskiwania.
* Nie usuwaj migawek utworzonych przy użyciu usługi Azure Backup. Usunięcie migawek może spowodować utratę punktów odzyskiwania i/lub błędy przywracania.
* Nie usuwaj udziałów plików, które są chronione przez usługę Azure Backup. Bieżące rozwiązanie usunie wszystkie migawki utworzone przez usługę Azure Backup, gdy udział plików zostanie usunięty, i tym samym utraci wszystkie punkty przywracania.

Zapasowy dla udziałów plików platformy Azure na kontach magazynu z replikacją [magazynu strefowego](../storage/common/storage-redundancy-zrs.md) (ZRS) jest obecnie dostępny tylko w centrali USA (CUS), wschodnich stanach USA (EUS), wschodnich stanach USA 2 (EUS2), Europie Północnej (NE), Azji Południowo-Południowej (SEA), Europie Zachodniej (WE) i zachodnie stany USA 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurowanie kopii zapasowej udziału plików platformy Azure

W tym samouczku przyjęto założenie, że ustanowiono już udział plików platformy Azure. Aby utworzyć kopię zapasową udziału plików platformy Azure:

1. Utwórz magazyn usługi Recovery Services w tym samym regionie, co udziału plików. Jeśli magazyn już istnieje, otwórz stronę z jego przeglądem, a następnie kliknij pozycję **Kopia zapasowa**.

    ![Kliknij pozycję Kopia zapasowa na stronie Przegląd przechowalni](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. W menu **Cel kopii zapasowej** z menu Co chcesz wykonać kopię **zapasową?**

    ![Wybieranie udział plików platformy Azure jako celu kopii zapasowej](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. Kliknij **przycisk Kopia zapasowa,** aby skonfigurować udział plików platformy Azure w magazynie usług odzyskiwania.

   ![Klikanie pozycji Utwórz kopię zapasową, aby skojarzyć udział plików platformy Azure z magazynem](./media/tutorial-backup-azure-files/set-backup-goal.png)

    Po skojarzeniu przechowalni z udziałem plików platformy Azure zostanie otwarte menu Kopia zapasowa i zostanie wyświetlone monit o wybranie konta magazynu. W menu są wyświetlane wszystkie obsługiwane konta magazynu w regionie, w którym istnieje przechowalnia, które nie są jeszcze skojarzone z magazynem usług odzyskiwania.

   ![Wybierz swoje konto magazynu](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. Na liście kont magazynu wybierz konto, a następnie kliknij przycisk **OK**. Na platformie Azure zostanie wykonane wyszukiwanie udziałów plików w ramach magazynu, dla których można utworzyć kopię zapasową. Jeśli niedawno dodano udziały plików i nie ma ich na liście, poczekaj chwilę na ich pojawienie się.

   ![Wykryto udziały plików](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. Z listy **Udziały plików** wybierz jeden lub więcej udziałów plików, których chcesz udostępnić, a następnie kliknij przycisk **OK**.

6. Po wybraniu udziałów plików menu Kopia zapasowa zostanie przełączone na menu **Zasady tworzenie kopii zapasowych**. Z poziomu tego menu wybierz istniejące zasady tworzenia kopii zapasowych lub utwórz nowe, a następnie kliknij pozycję **Włącz kopię zapasową**.

   ![Wybieranie zasad tworzenia kopii zapasowych lub tworzenie nowej](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    Po ustanowieniu zasad tworzenia kopii zapasowych w zaplanowanym czasie zostanie utworzona migawka udziałów plików, a punkt odzyskiwania zostanie zachowany przez wybrany okres.

## <a name="create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie

Po skonfigurowaniu zasad tworzenia kopii zapasowych należy utworzyć kopię zapasową na żądanie, aby upewnić się, że dane są chronione do następnej zaplanowanej kopii zapasowej.

### <a name="to-create-an-on-demand-backup"></a>Tworzenie kopii zapasowej na żądanie

1. Otwórz magazyn usługi Recovery Services zawierający punkty odzyskiwania udziałów plików, a następnie kliknij pozycję **Elementy kopii zapasowej**. Zostanie wyświetlona lista typów elementów kopii zapasowych.

   ![Lista elementów kopii zapasowej](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. Z listy wybierz pozycję **Azure Storage (Azure Files)**. Zostanie wyświetlona lista udziałów plików platformy Azure.

   ![Lista udziałów plików platformy Azure](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. Z listy udziałów plików platformy Azure wybierz żądany udział plików. Zostanie otwarte menu elementu kopii zapasowej dla wybranego udziału plików.

   ![Menu Zapas zapasowy dla wybranego udziału plików](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. W menu Element kopii zapasowej kliknij pozycję **Utwórz kopię zapasową teraz**. Ponieważ jest to zadanie tworzenia kopii zapasowej na żądanie, nie istnieją żadne zasady przechowywania skojarzone z punktem odzyskiwania. Zostanie otwarte okno dialogowe **Utwórz kopię zapasową teraz**. Określ ostatni dzień zachowywania punktu odzyskiwania.

   ![Wybierz datę przechowywania punktów odzyskiwania](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem wykonano następujące czynności przy użyciu witryny Azure Portal:

> [!div class="checklist"]
>
> * Konfigurowanie magazynu usługi Recovery Services na potrzeby tworzenia kopii zapasowej w usłudze Azure Files
> * Uruchomianie zadania tworzenia kopii zapasowej na żądanie w celu tworzenia punktu przywracania

Przejdź do następnego artykułu, aby przywrócić z kopii zapasowej udziału plików platformy Azure.

> [!div class="nextstepaction"]
> [Przywracanie z kopii zapasowej udziałów plików platformy Azure](restore-afs.md)
