---
title: Przywracanie udziałów plików platformy Azure
description: Dowiedz się, w jaki sposób używać Azure Portal do przywracania całego udziału plików lub określonych plików z punktu przywracania utworzonego przez usługę Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294398"
---
# <a name="restore-azure-file-shares"></a>Przywracanie udziałów plików platformy Azure

W tym artykule wyjaśniono, jak używać Azure Portal do przywracania całego udziału plików lub określonych plików z punktu przywracania utworzonego przez usługę [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) .

W tym przewodniku dowiesz się, jak:

* Przywróć pełny udział plików platformy Azure
* Przywracanie pojedynczych plików lub folderów
* Śledzenie stanu operacji przywracania

## <a name="steps-to-perform-restore"></a>Kroki do wykonania przywracania

### <a name="select-the-file-share-to-restore"></a>Wybierz udział plików do przywrócenia

1. W [Azure Portal](https://portal.azure.com/)otwórz magazyn Recovery Services, który został użyty do skonfigurowania kopii zapasowej udziału plików.

2. Kliknij pozycję **elementy kopii zapasowej** w sekcji **chronione elementy** w bloku **Przegląd** .

    ![Kliknij pozycję elementy kopii zapasowej](./media/restore-afs/backup-items.png)

3. Po kliknięciu **pozycji kopie zapasowe**w bloku **Omówienie** zostanie wyświetlony nowy blok zawierający listę wszystkich typów zarządzania kopiami zapasowymi, jak pokazano poniżej:

    ![Typy zarządzania kopiami zapasowymi](./media/restore-afs/backup-management.png)

4. W obszarze **elementy kopii zapasowej**w obszarze **Typ zarządzania kopiami zapasowymi**wybierz pozycję **Azure Storage (Azure Files)** . Zostanie wyświetlona lista wszystkich udziałów plików i odpowiednich kont magazynu, których kopię zapasową utworzono przy użyciu tego magazynu.

    ![Lista wszystkich udziałów plików](./media/restore-afs/file-shares.png)

5. Z listy udziałów plików platformy Azure wybierz żądany udział plików, dla którego chcesz wykonać operację przywracania.

### <a name="full-share-recovery"></a>Odzyskiwanie pełnego udziału

Można użyć tej opcji przywracania, aby przywrócić pełny udział plików w lokalizacji oryginalnej lub alternatywnej.

1. Wybierz opcję **Przywróć udział** z bloku **elementu kopia zapasowa** , która jest wyświetlana po wybraniu żądanego udziału plików do przywrócenia w kroku 5 sekcji [Wybierz udział plików do przywrócenia](#select-the-file-share-to-restore) .

   ![Wybierz pozycję Przywróć udział](./media/restore-afs/restore-share.png)

2. Po kliknięciu przycisku **Przywróć udział**zostanie otwarty blok **Przywróć** z menu **punkt przywracania** zawierającym listę punktów przywracania dostępnych dla wybranego udziału plików.

3. Wybierz punkt przywracania, którego chcesz użyć do wykonania operacji przywracania, a następnie kliknij przycisk **OK**.

    ![Wybierz punkt przywracania](./media/restore-afs/restore-point.png)

4. Po kliknięciu przycisku OK menu Przywróć blok przełącza się do **lokalizacji przywracania**. W obszarze **przywracanie lokalizacji**Określ, gdzie (lub jak) przywrócić dane. Można wybrać jedną z następujących dwóch opcji:

    * **Oryginalna lokalizacja**: Przywróć pełny udział plików w tej samej lokalizacji, w której znajduje się oryginalne źródło.
    * **Lokalizacja alternatywna**: Przywróć pełny udział plików w alternatywnej lokalizacji i Zachowaj oryginalny udział plików **w formacie**.

#### <a name="restore-to-original-location"></a>Przywróć do oryginalnej lokalizacji

1. Wybierz opcję **Oryginalna lokalizacja** jako **miejsce docelowe odzyskiwania** i wybierz, czy pominąć lub zastąpić, jeśli występują konflikty. Po wybraniu odpowiedniego wyboru kliknij przycisk **OK** .

    ![Wybierz oryginalną lokalizację](./media/restore-afs/original-location.png)

2. Kliknij przycisk **Przywróć** , aby rozpocząć operację przywracania.

    ![Kliknij przycisk Przywróć, aby rozpocząć](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

1. Wybierz **alternatywną lokalizację** jako lokalizację docelową odzyskiwania.
2. Wybierz docelowe konto magazynu, w którym chcesz przywrócić kopię zapasową zawartości z menu rozwijanego pola **konto magazynu** .
3. W oparciu o konto magazynu wybrane w kroku 2, w menu rozwijanym **Wybierz udział plików** zostanie wyświetlona lista udziałów plików znajdujących się na wybranym koncie magazynu. Wybierz udział plików, do którego ma zostać przywrócona zawartość kopii zapasowej.
4. W polu **Nazwa folderu** Określ nazwę folderu, który ma zostać utworzony w docelowym udziale plików z przywróconą zawartością.
5. Wybierz, czy pominąć, czy zastąpić, jeśli występują konflikty.
6. Kliknij przycisk **OK** po wprowadzeniu odpowiednich wartości we wszystkich polach.

    ![Wybierz lokalizację alternatywną](./media/restore-afs/alternate-location.png)

7. Kliknij przycisk Przywróć, aby rozpocząć operację przywracania.

    ![Kliknij przycisk Przywróć, aby rozpocząć](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Odzyskiwanie na poziomie elementu

Ta opcja przywracania służy do przywracania pojedynczych plików lub folderów w lokalizacji oryginalnej lub alternatywnej.

1. Wybierz opcję **odzyskiwanie plików** z bloku **element kopii zapasowej** , który jest wyświetlany po wybraniu żądanego udziału plików do przywrócenia w kroku 5 sekcji [Wybierz udział plików do przywrócenia](#select-the-file-share-to-restore) .

    ![Wybierz odzyskiwanie plików](./media/restore-afs/file-recovery.png)

2. Po kliknięciu przycisku **odzyskiwanie plików**zostanie otwarty blok **Przywróć** z menu **punkt przywracania** zawierającym listę punktów przywracania dostępnych dla wybranego udziału plików.

3. Wybierz punkt przywracania, którego chcesz użyć do wykonania operacji przywracania, a następnie kliknij przycisk **OK**.

    ![Wybierz punkt przywracania](./media/restore-afs/restore-point.png)

4. Po kliknięciu przycisku OK menu Przywróć blok przełącza się do **lokalizacji przywracania**. W obszarze **przywracanie lokalizacji**Określ, gdzie (lub jak) przywrócić dane. Można wybrać jedną z następujących dwóch opcji:

    * **Oryginalna lokalizacja**: Przywróć wybrane pliki/foldery do tego samego udziału plików, co oryginalne źródło.
    * **Lokalizacja alternatywna**: Przywróć wybrane pliki/foldery do lokalizacji alternatywnej i Zachowaj oryginalną **zawartość udziału**plików.

#### <a name="restore-to-original-location"></a>Przywracanie do oryginalnej lokalizacji

1. Wybierz opcję **Oryginalna lokalizacja** jako **miejsce docelowe odzyskiwania** i wybierz, czy pominąć lub zastąpić, jeśli występują konflikty.

    ![Oryginalna lokalizacja odzyskiwania na poziomie elementu](./media/restore-afs/original-location-item-level.png)

2. Kliknij pozycję **Wybierz plik** , aby wybrać pliki/foldery, które chcesz przywrócić.

    ![Kliknij pozycję Wybierz plik](./media/restore-afs/select-file.png)

3. Po kliknięciu przycisku **Wybierz plik**zostanie wyświetlony blok udział plików zawierający zawartość punktu odzyskiwania udziału plików, który został wybrany do przywracania.

4. Zaznacz pole wyboru odpowiadające plikowi/folderowi, który chcesz przywrócić, a następnie kliknij przycisk **Wybierz**.

    ![Wybierz plik lub folder](./media/restore-afs/select-file-folder.png)

5. Powtórz kroki 2-4, aby wybrać wiele plików/folderów do przywrócenia.
6. Po wybraniu wszystkich elementów, które chcesz przywrócić, kliknij przycisk **OK**.

    ![Po wybraniu wszystkich elementów do przywrócenia kliknij przycisk OK.](./media/restore-afs/after-selecting-items.png)

7. Kliknij przycisk Przywróć, aby rozpocząć operację przywracania.

    ![Kliknij przycisk Przywróć, aby rozpocząć](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

1. Wybierz **alternatywną lokalizację** jako lokalizację docelową odzyskiwania.
2. Wybierz docelowe konto magazynu, w którym chcesz przywrócić kopię zapasową zawartości z menu rozwijanego pola **konto magazynu** .
3. W oparciu o konto magazynu wybrane w kroku 2, w menu rozwijanym **Wybierz udział plików** zostanie wyświetlona lista udziałów plików znajdujących się na wybranym koncie magazynu. Wybierz udział plików, do którego ma zostać przywrócona zawartość kopii zapasowej.
4. W polu **Nazwa folderu** Określ nazwę folderu, który ma zostać utworzony w docelowym udziale plików z przywróconą zawartością.
5. Wybierz, czy pominąć, czy zastąpić, jeśli występują konflikty.
6. Kliknij pozycję **Wybierz plik** , aby wybrać pliki/foldery, które chcesz przywrócić.

    ![Wybierz elementy, które mają zostać przywrócone do lokalizacji alternatywnej](./media/restore-afs/restore-to-alternate-location.png)

7. Po kliknięciu przycisku **Wybierz plik**zostanie wyświetlony blok udział plików zawierający zawartość punktu odzyskiwania udziału plików, który został wybrany do przywracania.
8. Zaznacz pole wyboru odpowiadające plikowi/folderowi, który chcesz przywrócić, a następnie kliknij przycisk **Wybierz**.

    ![Wybierz miejsce docelowe odzyskiwania](./media/restore-afs/recovery-destination.png)

9. Powtórz kroki 6-8, aby wybrać wiele plików/folderów do przywrócenia.
10. Po wybraniu wszystkich elementów, które chcesz przywrócić, kliknij przycisk **OK**.

    [Kliknij przycisk OK po wybraniu wszystkich plików](./media/restore-afs/after-selecting-all-items.png)

11. Kliknij przycisk **Przywróć** , aby rozpocząć operację przywracania.

## <a name="track-restore-operation"></a>Śledź operację przywracania

Po zainicjowaniu operacji przywracania usługa Backup tworzy zadanie śledzenia. Azure Backup wyświetla powiadomienia dotyczące zadania w portalu. Aby wyświetlić operacje dla zadania, kliknij hiperlink powiadomienia.

![Kliknij hiperlink powiadomienia](./media/restore-afs/notifications-link.png)

Możesz również monitorować postęp przywracania z magazynu usługi Recovery Services. Poniżej przedstawiono kroki umożliwiające sprawdzenie stanu operacji przywracania:

1. Otwórz magazyn Recovery Services z którego została wyzwolona operacja przywracania.
2. Kliknij pozycję **zadania tworzenia kopii zapasowej** w **sekcji monitorowanie** w bloku **Przegląd** , aby zobaczyć stan operacji uruchomionych dla różnych obciążeń.

    ![Kliknij pozycję zadania tworzenia kopii zapasowej](./media/restore-afs/backup-jobs.png)

3. Kliknij nazwę obciążenia odpowiadającego udziałowi plików, aby wyświetlić więcej szczegółów na temat operacji przywracania, takich jak transferowane dane, liczba przywróconych plików itp.

    ![Zobacz przywrócone szczegóły](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zarządzać kopiami zapasowymi udziałów plików platformy Azure](manage-afs-backup.md)
