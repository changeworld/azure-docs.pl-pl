---
title: Przywracanie udziałów plików platformy Azure
description: Dowiedz się, jak przywrócić cały udział plików lub określone pliki z punktu przywracania utworzonego przez usługę Azure Backup za pomocą portalu Azure.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586955"
---
# <a name="restore-azure-file-shares"></a>Przywracanie udziałów plików platformy Azure

W tym artykule wyjaśniono, jak za pomocą portalu Azure do przywracania całego udziału plików lub określonych plików z punktu przywracania utworzonego przez [usługę Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

W tym artykule dowiesz się, jak:

* Przywracanie pełnego udziału plików platformy Azure.
* Przywracanie pojedynczych plików lub folderów.
* Śledzenie stanu operacji przywracania.

## <a name="steps-to-perform-a-restore-operation"></a>Kroki wykonywania operacji przywracania

Aby wykonać operację przywracania, wykonaj następujące kroki.

### <a name="select-the-file-share-to-restore"></a>Wybierz udział plików do przywrócenia

1. W [witrynie Azure portal](https://portal.azure.com/)otwórz magazyn usług odzyskiwania używany do konfigurowania kopii zapasowej dla udziału plików.

1. W okienku przeglądu wybierz pozycję **Zapas elementów** w sekcji **Chronione elementy.**

    ![Wybieranie elementów kopii zapasowej](./media/restore-afs/backup-items.png)

1. Po **wybraniu pozycji kopii zapasowej**obok okienka przeglądu zostanie otwarte nowe okienko z listą wszystkich typów zarządzania kopiami zapasowymi.

    ![Typy zarządzania kopiami zapasowymi](./media/restore-afs/backup-management.png)

1. W okienku **Elementy kopii zapasowej** w obszarze Typ **zarządzania kopiami zapasowymi**wybierz pozycję **Usługa Azure Storage (pliki Azure)**. Zobaczysz listę wszystkich udziałów plików i ich odpowiednich kont magazynu kopii zapasowej przy użyciu tego magazynu.

    ![Lista wszystkich udziałów plików](./media/restore-afs/file-shares.png)

1. Z listy udziałów plików platformy Azure wybierz udział plików, dla którego chcesz wykonać operację przywracania.

### <a name="full-share-recovery"></a>Pełne odzyskiwanie akcji

Za pomocą tej opcji przywracania można przywrócić pełny udział plików w oryginalnej lokalizacji lub w lokalizacji alternatywnej.

1. Wybierz opcję **Przywróć udział** w okienku **Zapas zapasu zapasu,** który pojawia się po wybraniu udziału plików do przywrócenia w kroku 5 sekcji [Wybierz udział plików do przywrócenia.](#select-the-file-share-to-restore)

   ![Wybierz pozycję Przywróć udział](./media/restore-afs/restore-share.png)

1. Po wybraniu opcji **Przywróć udział**zostanie otwarte okienko **Przywracanie** z menu **Punkt przywracania,** w których zostanie wyświetlona lista punktów przywracania dostępnych dla wybranego udziału plików.

1. Wybierz punkt przywracania, którego chcesz użyć do wykonania operacji przywracania, a następnie wybierz przycisk **OK**.

    ![Wybierz punkt przywracania](./media/restore-afs/restore-point.png)

1. Po wybraniu przycisku **OK**menu **Okienko Przywracanie** przełącza się na **Pozycję Przywróć**. W **obszarze Przywróć lokalizację**określ, gdzie i jak przywrócić dane. Wybierz jedną z następujących dwóch opcji:

    * **Oryginalna lokalizacja:** Przywróć pełny udział plików w tej samej lokalizacji, co oryginalne źródło.
    * **Lokalizacja alternatywna:** Przywróć pełny udział plików w lokalizacji alternatywnej i zachowaj oryginalny udział plików w stanie, w jakim jest.

#### <a name="restore-to-the-original-location"></a>Przywracanie do pierwotnej lokalizacji

1. Wybierz **pozycję Lokalizacja oryginalna** jako **miejsce docelowe odzyskiwania**i wybierz, czy pominąć, czy zastąpić, jeśli występują konflikty. Po dokonaniu odpowiedniego wyboru wybierz przycisk **OK**.

    ![Wybierz oryginalną lokalizację](./media/restore-afs/original-location.png)

1. Wybierz **przycisk Przywróć,** aby rozpocząć operację przywracania.

    ![Wybierz pozycję Przywróć, aby rozpocząć](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

1. Wybierz **pozycję Lokalizacja alternatywna** jako **miejsce docelowe odzyskiwania**.
1. Wybierz docelowe konto magazynu, na którym chcesz przywrócić kopię zapasową zawartości z listy rozwijanej **Konto magazynu.**
1. Lista rozwijana **Wybierz udział plików** wyświetla udziały plików obecne na koncie magazynu wybranym w kroku 2. Wybierz udział plików, w którym chcesz przywrócić zawartość kopii zapasowej.
1. W polu **Nazwa folderu** określ nazwę folderu, którą chcesz utworzyć w docelowym udziale plików z przywróconą zawartością.
1. Wybierz, czy pominąć lub zastąpić, jeśli występują konflikty.
1. Po wprowadzeniu odpowiednich wartości we wszystkich polach wybierz przycisk **OK**.

    ![Wybierz pozycję Alternatywna lokalizacja](./media/restore-afs/alternate-location.png)

1. Wybierz **przycisk Przywróć,** aby rozpocząć operację przywracania.

    ![Wybierz pozycję Przywróć, aby rozpocząć](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Odzyskiwanie na poziomie elementu

Za pomocą tej opcji przywracania można przywrócić poszczególne pliki lub foldery w oryginalnej lokalizacji lub w lokalizacji alternatywnej.

1. Wybierz opcję **Odzyskiwanie plików** w okienku **Zapas zapasu zapasowego,** który pojawia się po wybraniu udziału plików do przywrócenia w kroku 5 sekcji [Wybierz udział plików do przywrócenia.](#select-the-file-share-to-restore)

    ![Wybierz odzyskiwanie plików](./media/restore-afs/file-recovery.png)

1. Po **wybraniu opcji Odzyskiwanie plików**zostanie otwarte okienko **Przywracanie** z menu **Punkt przywracania,** w których zostanie wyświetlona lista punktów przywracania dostępnych dla wybranego udziału plików.

1. Wybierz punkt przywracania, którego chcesz użyć do wykonania operacji przywracania, a następnie wybierz przycisk **OK**.

    ![Wybierz punkt przywracania](./media/restore-afs/restore-point.png)

1. Po wybraniu **przycisku OK**menu okienka przywracania zostanie przełączne do **pozycji Przywróć lokalizację**. W **obszarze Przywróć lokalizację**określ, gdzie i jak przywrócić dane. Wybierz jedną z następujących dwóch opcji:

    * **Oryginalna lokalizacja**: Przywracanie wybranych plików lub folderów do tego samego udziału plików, co oryginalne źródło.
    * **Lokalizacja alternatywna**: Przywróć wybrane pliki lub foldery do alternatywnej lokalizacji i zachowaj oryginalną zawartość udziału plików w stanie, w jakim jest.

#### <a name="restore-to-the-original-location"></a>Przywracanie do pierwotnej lokalizacji

1. Wybierz **pozycję Lokalizacja oryginalna** jako **miejsce docelowe odzyskiwania**i wybierz, czy pominąć, czy zastąpić, jeśli występują konflikty.

    ![Oryginalna lokalizacja do odzyskiwania na poziomie towaru](./media/restore-afs/original-location-item-level.png)

1. Wybierz **pozycję Wybierz plik,** aby wybrać pliki lub foldery, które chcesz przywrócić.

    ![Wybierz pozycję Wybierz plik](./media/restore-afs/select-file.png)

1. Po **wybraniu**opcji Wybierz plik w okienku udziału plików zostanie wyświetlona zawartość punktu odzyskiwania udziału plików wybranego do przywrócenia.

1. Zaznacz pole wyboru odpowiadające plikowi lub folderowi, który chcesz przywrócić, a następnie wybierz pozycję **Wybierz**.

    ![Wybierz plik lub folder](./media/restore-afs/select-file-folder.png)

1. Powtórz kroki od 2 do 4, aby wybrać wiele plików lub folderów do przywrócenia.
1. Po wybraniu wszystkich elementów, które chcesz przywrócić, wybierz przycisk **OK**.

    ![Po wybraniu wszystkich elementów do przywrócenia wybierz przycisk OK](./media/restore-afs/after-selecting-items.png)

1. Wybierz **przycisk Przywróć,** aby rozpocząć operację przywracania.

    ![Wybierz pozycję Przywróć, aby rozpocząć](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

1. Wybierz **pozycję Lokalizacja alternatywna** jako **miejsce docelowe odzyskiwania**.
1. Wybierz docelowe konto magazynu, na którym chcesz przywrócić kopię zapasową zawartości z listy rozwijanej **Konto magazynu.**
1. Lista rozwijana **Wybierz udział plików** wyświetla udziały plików obecne na koncie magazynu wybranym w kroku 2. Wybierz udział plików, w którym chcesz przywrócić zawartość kopii zapasowej.
1. W polu **Nazwa folderu** określ nazwę folderu, którą chcesz utworzyć w docelowym udziale plików z przywróconą zawartością.
1. Wybierz, czy pominąć lub zastąpić, jeśli występują konflikty.
1. Wybierz **pozycję Wybierz plik,** aby wybrać pliki lub foldery, które chcesz przywrócić.

    ![Zaznaczanie elementów do przywrócenia do lokalizacji alternatywnej](./media/restore-afs/restore-to-alternate-location.png)

1. Po **wybraniu opcji Wybierz plik**w okienku udziału plików zostanie wyświetlona zawartość punktu odzyskiwania udziału plików wybranego do przywrócenia.
1. Zaznacz pole wyboru odpowiadające plikowi lub folderowi, który chcesz przywrócić, a następnie wybierz pozycję **Wybierz**.

    ![Wybierz miejsce docelowe odzyskiwania](./media/restore-afs/recovery-destination.png)

1. Powtórz kroki od 6 do 8, aby wybrać wiele plików lub folderów do przywrócenia.
1. Po wybraniu wszystkich elementów, które chcesz przywrócić, wybierz przycisk **OK**.

    ![Wybierz przycisk OK po wybraniu wszystkich plików](./media/restore-afs/after-selecting-all-items.png)

1. Wybierz **przycisk Przywróć,** aby rozpocząć operację przywracania.

## <a name="track-a-restore-operation"></a>Śledzenie operacji przywracania

Po wyzwoleniu operacji przywracania usługa tworzenia kopii zapasowych tworzy zadanie śledzenia. Usługa Azure Backup wyświetla powiadomienia o zadaniu w portalu. Aby wyświetlić operacje dla zadania, wybierz hiperłącze powiadomień.

![Wybieranie hiperłącza powiadomień](./media/restore-afs/notifications-link.png)

Można również monitorować postęp przywracania z magazynu usług odzyskiwania:

1. Otwórz magazyn usług odzyskiwania z miejsca, w którym wyzwoliłeś operację przywracania.
1. W okienku przeglądu wybierz pozycję **Zadania kopii zapasowych** w sekcji **Monitorowanie,** aby wyświetlić stan operacji uruchomionych względem różnych obciążeń.

    ![Wybierz zadania tworzenia kopii zapasowych](./media/restore-afs/backup-jobs.png)

1. Wybierz nazwę obciążenia odpowiadającą udziałowi plików, aby wyświetlić więcej szczegółów dotyczących operacji przywracania, takich jak **Przesyłane dane** i **Liczba przywróconych plików**.

    ![Zobacz przywrócone szczegóły](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zarządzać kopiami zapasowymi udziału plików platformy Azure](manage-afs-backup.md).
