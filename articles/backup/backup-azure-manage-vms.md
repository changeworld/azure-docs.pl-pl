---
title: Zarządzanie kopii zapasowych maszyn wirtualnych wdrożonych przez Menedżera zasobów
description: Dowiedz się, jak zarządzanie nimi oraz monitorowanie kopii zapasowych maszyn wirtualnych wdrożonych przez Menedżera zasobów
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/28/2016
ms.author: trinadhk
ms.openlocfilehash: 4d45db6ba6354f85c3ed67561751720b6f6f4b77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606345"
---
# <a name="manage-azure-virtual-machine-backups"></a>Zarządzanie kopiami zapasowymi maszyny wirtualnej platformy Azure

Ten artykuł zawiera wskazówki dotyczące zarządzania kopii zapasowych maszyn wirtualnych i wyjaśniono, alerty kopii zapasowej informacji dostępnych na pulpicie nawigacyjnym portalu. Wskazówki zawarte w tym artykule dotyczą przy użyciu maszyn wirtualnych z Magazyny usług odzyskiwania. W tym artykule opisano tworzenie maszyn wirtualnych nie jest opisano sposób ochrony maszyn wirtualnych. Aby Elementarz o ochronie usługi Azure Resource Manager wdrożone maszyny wirtualne na platformie Azure w magazynie usług odzyskiwania, zobacz [Pierwsze spojrzenie: Utwórz kopię zapasową maszyn wirtualnych do magazynu usług odzyskiwania](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Zarządzanie magazynami i chronione maszyny wirtualne
W portalu Azure na pulpicie nawigacyjnym magazynu usług odzyskiwania zapewnia dostęp do informacji o tym magazynu:

* najnowsze migawki kopii zapasowej jest również najnowszy punkt przywracania
* zasady tworzenia kopii zapasowej
* Całkowity rozmiar wszystkich migawek kopii zapasowych
* Liczba maszyn wirtualnych, które są chronione przy użyciu magazynu

Wiele zadań zarządzania z kopii zapasowej maszyny wirtualnej zaczynać Otwieranie magazynu w pulpicie nawigacyjnym. Jednak ponieważ magazynów może służyć do ochrony wielu elementów (lub wiele maszyn wirtualnych), aby wyświetlić szczegóły dotyczące określonej maszyny Wirtualnej, Otwórz pulpit nawigacyjny elementu magazynu. Poniższa procedura przedstawia sposób otwierania *pulpitem nawigacyjnym magazynu* , a następnie przejdź do *pulpitem nawigacyjnym magazynu elementu*. W obu procedurach, które wskazują, jak dodać magazyn do magazynu element do pulpitu nawigacyjnego platformy Azure przy użyciu Przypnij do pulpitu nawigacyjnego polecenia są "porady". Przypnij do pulpitu nawigacyjnego jest sposób tworzenia skrótów do magazynu lub elementu. Można również wykonywać typowe polecenia ze skrótu.

> [!TIP]
> Jeśli masz wiele pulpitów nawigacyjnych i otworzyć bloków, przesuń Azure pulpitu nawigacyjnego i z powrotem za pomocą suwaka dark blue w dolnej części okna.
>
>

![Pełny widok z suwaka](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Otwórz magazyn usług odzyskiwania na pulpicie nawigacyjnym:
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu Centrum kliknij opcję **Przeglądaj** i na liście zasobów wpisz ciąg **Usługi odzyskiwania**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Kliknij opcję **Magazyn Usług odzyskiwania**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    Zostanie wyświetlona lista magazynów Usług odzyskiwania.

    ![Lista usług odzyskiwania magazynów ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Jeśli przypniesz magazynu Azure, odwiedź pulpit nawigacyjny tego magazynu jest natychmiast dostępna po otwarciu portalu Azure. Aby przypiąć do pulpitu nawigacyjnego, magazynu na liście magazynu, kliknij prawym przyciskiem myszy magazyn, a następnie wybierz **Przypnij do pulpitu nawigacyjnego**.
   >
   >
3. Z listy magazynów wybierz magazyn, aby otworzyć jego pulpitu nawigacyjnego. Po wybraniu magazynu, na pulpicie nawigacyjnym magazynu i **ustawienia** Otwórz blok. Na poniższej ilustracji **magazynu Contoso** zostanie wyróżniona pulpitu nawigacyjnego.

    ![Otwórz pulpit nawigacyjny magazynu i blok ustawień](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Otwórz pulpit nawigacyjny elementu magazynu
W poprzedniej procedurze należy otworzyć pulpitem nawigacyjnym magazynu. Aby otworzyć pulpit nawigacyjny elementu magazynu:

1. Na pulpicie nawigacyjnym magazynu na **kopii zapasowej elementów** Kafelek, kliknij przycisk **maszyny wirtualne Azure**.

    ![Kafelek otwarte elementy kopii zapasowej](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    **Elementów kopii zapasowych** ostatnie zadanie tworzenia kopii zapasowej dla każdego elementu listy bloku. W tym przykładzie Brak jednej maszyny wirtualnej, demovm-markgal chronione przez ten magazyn.  

    ![Kafelek elementów kopii zapasowych](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > W celu ułatwienia dostępu można przypiąć element magazynu do pulpitu nawigacyjnego platformy Azure. Aby przypiąć element magazynu na liście element magazynu, kliknij element prawym przyciskiem myszy i wybierz **Przypnij do pulpitu nawigacyjnego**.
   >
   >
2. W **kopii zapasowej elementów** bloku, kliknij element, aby otworzyć pulpit nawigacyjny elementu magazynu.

    ![Kafelek elementów kopii zapasowych](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Pulpit nawigacyjny elementu Magazyn i jego **ustawienia** Otwórz blok.

    ![Pulpit nawigacyjny elementów kopii zapasowych z bloku ustawień](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    Z poziomu pulpitu nawigacyjnego elementu magazynu można wykonywać wiele zadań zarządzania kluczami, takich jak:

   * Zmiana zasad lub utworzyć nowe zasady tworzenia kopii zapasowej
   * Wyświetl punkty przywracania i wyświetlić ich stan spójności
   * Na żądanie kopii zapasowej maszyny wirtualnej
   * Zatrzymaj ochronę maszyn wirtualnych
   * Wznów ochronę maszyny wirtualnej
   * Usuwanie danych kopii zapasowej (lub punktu odzyskiwania)
   * [Przywracanie kopii zapasowych dysków](backup-azure-arm-restore-vms.md#restore-backed-up-disks)

Dla poniższych procedur punktem początkowym jest pulpit nawigacyjny elementu magazynu.

## <a name="manage-backup-policies"></a>Zarządzanie zasadami tworzenia kopii zapasowych
1. Na [pulpitem nawigacyjnym magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **wszystkie ustawienia** otworzyć **ustawienia** bloku.

    ![Blok zasady tworzenia kopii zapasowej](./media/backup-azure-manage-vms/all-settings-button.png)
2. Na **ustawienia** bloku, kliknij przycisk **kopii zapasowej zasad** do otwarcia tego bloku.

    W bloku są wyświetlane szczegóły kopii zapasowej zakres częstotliwości i przechowywania.

    ![Blok zasady tworzenia kopii zapasowej](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. Z **wybierz zasady tworzenia kopii zapasowej** menu:

   * Aby zmienić zasady, wybierz inne zasady, a następnie kliknij przycisk **zapisać**. Nowe zasady zostaną natychmiast zastosowane do magazynu.
   * Aby utworzyć zasady, wybierz **Utwórz nowy**.

     ![Kopia zapasowa maszyny wirtualnej](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Aby uzyskać instrukcje na temat tworzenia zasad tworzenia kopii zapasowej, zobacz [Definiowanie zasad tworzenia kopii zapasowej](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Zasady tworzenia kopii zapasowej i zarządzania nimi, upewnij się, że należy wykonać [najlepsze rozwiązania](backup-azure-vms-introduction.md#best-practices) do uzyskania optymalnej wydajności tworzenia kopii zapasowej
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>Na żądanie kopii zapasowej maszyny wirtualnej
Skonfigurowane dla ochrony na żądanie można wykonać kopii zapasowej maszyny wirtualnej. Jeśli oczekuje tworzenia początkowej kopii zapasowej na żądanie kopii zapasowej tworzy pełną kopię maszyny wirtualnej w magazynie usług odzyskiwania. Jeśli początkowa kopia zapasowa została wykonana, na żądanie kopii zapasowej wysyła zmiany z poprzednią migawkę w magazynie usług odzyskiwania. Oznacza to, że kolejne kopie zapasowe są zawsze przyrostowe.

> [!NOTE]
> Zakres przechowywania kopii zapasowej na żądanie jest wartość przechowywania określona dla codziennego punktu kopii zapasowej w zasadach. Jeśli wybrano opcję nie codziennego punktu kopii zapasowej, tygodniowego punktu kopii zapasowej jest używany.
>
>

Wyzwalanie tworzenia kopii zapasowej na żądanie maszyny wirtualnej:

* Na [pulpitem nawigacyjnym magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **wykonaj kopię zapasową teraz**.

    ![Kopię zapasową teraz przycisku.](./media/backup-azure-manage-vms/backup-now-button.png)

    Portalu upewnia się, że chcesz uruchomić zadanie tworzenia kopii zapasowej na żądanie. Kliknij przycisk **tak** można uruchomić zadanie tworzenia kopii zapasowej.

    ![Kopię zapasową teraz przycisku.](./media/backup-azure-manage-vms/backup-now-check.png)

    Zadanie tworzenia kopii zapasowej tworzy punkt odzyskiwania. Zakres przechowywania punktu odzyskiwania jest taka sama jak zakres przechowywania określonym w zasadach skojarzoną z maszyną wirtualną. Aby śledzić postęp zadania, na pulpicie nawigacyjnym magazynu, kliknij przycisk **zadania tworzenia kopii zapasowej** kafelka.  

## <a name="stop-protecting-virtual-machines"></a>Zatrzymaj ochronę maszyn wirtualnych
Jeśli wybierzesz zatrzymanie ochrony maszyny wirtualnej, zostanie wyświetlona prośba Jeśli chcesz zachować punktów odzyskiwania. Istnieją dwa sposoby zatrzymanie ochrony maszyn wirtualnych:

* zatrzymanie wszystkich przyszłych zadań kopii zapasowej i usunięcie wszystkich punktów odzyskiwania lub
* zatrzymanie wszystkich przyszłych zadań kopii zapasowej, ale pozostawić punktów odzyskiwania

Brak kosztów związanych z pozostawienie punktów odzyskiwania w magazynie. Jednak zaletą pozostawienie punktów odzyskiwania jest maszynę wirtualną można przywrócić później, w razie potrzeby. Uzyskać informacji o koszt opuszczania punktów odzyskiwania, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/backup/). Jeśli wybierzesz opcję usunięcia wszystkich punktów odzyskiwania, nie można przywrócić maszyny wirtualnej.

Aby zatrzymać ochronę dla maszyny wirtualnej:

1. Na [pulpitem nawigacyjnym magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **kopii zapasowej Zatrzymaj**.

    ![Zatrzymaj przycisku tworzenia kopii zapasowej](./media/backup-azure-manage-vms/stop-backup-button.png)

    Zostanie otwarty blok zatrzymać kopii zapasowej.

    ![Zatrzymywanie tworzenia kopii zapasowej bloku](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. Na **zatrzymać kopii zapasowej** bloku, wybierz, czy chcesz zachować lub usunąć danych kopii zapasowej. Pole informacje szczegółowe informacje na temat wybór.

    ![Zatrzymaj ochronę](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Jeśli użytkownik wybrał opcję zachowania danych kopii zapasowej, przejdź do kroku 4. Jeśli chcesz usunąć dane kopii zapasowej, upewnij się, czy chcesz zatrzymać zadania tworzenia kopii zapasowej i usunięcie punktów odzyskiwania — wpisz nazwę elementu.

    ![Zatrzymaj weryfikacji](./media/backup-azure-manage-vms/item-verification-box.png)

    Jeśli nie masz pewności co nazwa elementu, umieść kursor nad wykrzyknik, aby wyświetlić nazwę. Ponadto nazwa elementu podlega **zatrzymać kopii zapasowej** w górnej części bloku.
4. Opcjonalnie wprowadź **Przyczyna** lub **komentarz**.
5. Aby zatrzymać zadanie tworzenia kopii zapasowej dla bieżącego elementu, kliknij przycisk ![Zatrzymaj przycisku tworzenia kopii zapasowej](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Komunikat z powiadomieniem informuje o tym, że zadania tworzenia kopii zapasowej zostały zatrzymane.

    ![Upewnij się, Zatrzymaj ochronę](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>Wznów ochronę maszyny wirtualnej
Jeśli **Zachowaj dane kopii zapasowej** została wybrana opcja podczas ochrony dla maszyny wirtualnej została zatrzymana, wówczas jest to możliwe wznowić ochronę. Jeśli **usunąć dane z kopii zapasowej** została wybrana opcja, a następnie nie można wznowić ochronę dla maszyny wirtualnej.

Aby wznowić ochronę dla maszyny wirtualnej

1. Na [pulpitem nawigacyjnym magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **Wznów wykonywanie kopii zapasowej**.

    ![Wznów ochronę](./media/backup-azure-manage-vms/resume-backup-button.png)

    Zostanie otwarty blok zasad tworzenia kopii zapasowej.

   > [!NOTE]
   > Jeśli ponownie ochronę maszyny wirtualnej, można określić różnych zasad niż zasady, z którą maszyny wirtualnej został początkowo chronić.
   >
   >
2. Postępuj zgodnie z instrukcjami [Zarządzanie zasadami tworzenia kopii zapasowej](backup-azure-manage-vms.md#manage-backup-policies) można przypisać zasady dla maszyny wirtualnej.

    Po zastosowaniu zasad tworzenia kopii zapasowej do maszyny wirtualnej, zostanie wyświetlony następujący komunikat.

    ![Pomyślnie chronionej maszyny Wirtualnej](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej
Można usunąć danych kopii zapasowej skojarzonego z maszyną wirtualną podczas **kopii zapasowej Zatrzymaj** zadania, lub w dowolnym momencie po zakończeniu tworzenia kopii zapasowej zadania zakończone. Wstrzymanie się z usunięciem punktów odzyskiwania przez określoną liczbę dni lub tygodni może być nawet korzystne. W odróżnieniu od przywracania punktów odzyskiwania podczas usuwania danych kopii zapasowej nie można wybrać punktów odzyskiwania do usunięcia. Jeśli zdecydujesz się usunąć dane kopii zapasowych, wszystkie skojarzone z tym elementem punkty odzyskiwania zostaną usunięte.

W poniższej procedurze przyjęto zadanie tworzenia kopii zapasowej maszyny wirtualnej została zatrzymana lub wyłączona. Gdy zadanie tworzenia kopii zapasowej jest wyłączone, **Wznów wykonywanie kopii zapasowej** i **kopii zapasowej Delete** opcje są dostępne na pulpicie nawigacyjnym elementu magazynu.

![Wznów i usuń przyciski](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Aby usunąć dane kopii zapasowej na maszynie wirtualnej z *kopii zapasowej wyłączone*:

1. Na [pulpitem nawigacyjnym magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **usunięcia kopii zapasowej**.

    ![Typ maszyny wirtualnej](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    **Usunąć dane z kopii zapasowej** zostanie otwarty blok.

    ![Typ maszyny wirtualnej](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Wpisz nazwę elementu, aby potwierdzić, że chcesz usunąć punktów odzyskiwania.

    ![Zatrzymaj weryfikacji](./media/backup-azure-manage-vms/item-verification-box.png)

    Jeśli nie masz pewności co nazwa elementu, umieść kursor nad wykrzyknik, aby wyświetlić nazwę. Ponadto nazwa elementu podlega **usunąć dane z kopii zapasowej** w górnej części bloku.
3. Opcjonalnie wprowadź **Przyczyna** lub **komentarz**.
4. Aby usunąć dane kopii zapasowej dla bieżącego elementu, kliknij przycisk ![Zatrzymaj przycisku tworzenia kopii zapasowej](./media/backup-azure-manage-vms/delete-button.png)

    Komunikat z powiadomieniem informuje dane kopii zapasowej została usunięta.

## <a name="next-steps"></a>Kolejne kroki
Informacje dotyczące ponownego tworzenia maszyny wirtualnej z punktu odzyskiwania, zapoznaj się z [przywracanie maszyn wirtualnych Azure](backup-azure-arm-restore-vms.md). Jeśli potrzebujesz informacji na temat ochrony maszyn wirtualnych, zobacz [Pierwsze spojrzenie: Utwórz kopię zapasową maszyn wirtualnych do magazynu usług odzyskiwania](backup-azure-vms-first-look-arm.md). Aby uzyskać informacje dotyczące monitorowania zdarzeń, zobacz [monitorowania alertów dla kopii zapasowych maszyn wirtualnych Azure](backup-azure-monitor-vms.md).
