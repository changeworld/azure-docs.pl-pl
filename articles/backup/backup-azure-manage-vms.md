---
title: Zarządzanie kopiami zapasowymi maszyn wirtualnych wdrożonych przez usługę Resource Manager
description: Informacje o sposobie zarządzania i monitorowania kopii zapasowych maszyn wirtualnych wdrożonych przez usługę Resource Manager
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 11/28/2016
ms.author: sogup
ms.openlocfilehash: d0fac3a075923b000c453480edbf18599f5fed3d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "55994901"
---
# <a name="manage-azure-virtual-machine-backups"></a>Zarządzanie kopiami zapasowymi maszyny wirtualnej platformy Azure

Ten artykuł zawiera wskazówki dotyczące zarządzania kopiami zapasowymi maszyny Wirtualnej i wyjaśnia alerty kopii zapasowej informacje zawarte na pulpicie nawigacyjnym portalu. Wskazówki zawarte w tym artykule dotyczy przy użyciu maszyn wirtualnych przy użyciu magazynów usługi Recovery Services. W tym artykule opisano tworzenie maszyn wirtualnych nie jest on wyjaśniają, jak chronić maszyny wirtualne. Aby uzyskać podstawowe informacje na temat ochrony maszyn wirtualnych wdrożonych przez usługi Azure Resource Manager na platformie Azure przy użyciu magazynu usługi Recovery Services, zobacz [Pierwsze spojrzenie: Tworzenie kopii zapasowych maszyn wirtualnych w magazynie usługi Recovery Services](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Zarządzanie magazynami i chronionych maszyn wirtualnych
W witrynie Azure portal na pulpicie nawigacyjnym magazynu usług Recovery Services zapewnia dostęp do informacji na temat magazynu, w tym:

* najbardziej aktualną migawki kopii zapasowej jest również najnowszy punkt przywracania
* zasady kopii zapasowych
* Całkowity rozmiar wszystkich migawek kopii zapasowych
* Liczba maszyn wirtualnych, które są chronione przy użyciu magazynu

Wiele zadań zarządzania za pomocą kopii zapasowej maszyny wirtualnej rozpoczynają się od otwierania magazynu na pulpicie nawigacyjnym. Jednak ponieważ magazynów może służyć do ochrony wielu elementów (lub wielu maszyn wirtualnych), aby wyświetlić szczegóły dotyczące określonej maszyny Wirtualnej, należy otworzyć pulpit nawigacyjny elementu magazynu. Poniższa procedura pokazuje sposób otwierania *pulpitu nawigacyjnego magazynu* i przejdź do *pulpitu nawigacyjnego magazynu elementu*. W obu tych procedur, które zwracają uwagę na sposób dodawania magazynu i Magazyn element do pulpitu nawigacyjnego platformy Azure przy użyciu Przypnij do pulpitu nawigacyjnego polecenia są "porady". Przypnij do pulpitu nawigacyjnego jest sposobem tworzenia skrótów do magazynu lub elementu. Typowe polecenia można również wykonać za pomocą skrótu.

> [!TIP]
> Jeśli masz wiele pulpitów nawigacyjnych i zostaną otwarte bloki, za pomocą Ciemnoniebieski suwaka w dolnej części okna slajd pulpicie nawigacyjnym platformy Azure i z powrotem.
>
>

![Pełen przegląd za pomocą suwaka](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Otwórz magazyn usługi Recovery Services na pulpicie nawigacyjnym:
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu Centrum kliknij opcję **Przeglądaj** i na liście zasobów wpisz ciąg **Usługi odzyskiwania**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Kliknij opcję **Magazyn Usług odzyskiwania**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    Zostanie wyświetlona lista magazynów Usług odzyskiwania.

    ![Magazyny listę usług Recovery Services ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Jeśli przypniesz magazynu do pulpitu nawigacyjnego platformy Azure tego magazynu jest natychmiast dostępna, po otwarciu witryny Azure portal. Aby przypiąć magazynu do pulpitu nawigacyjnego, na liście magazynu, kliknij prawym przyciskiem myszy magazyn, a następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.
   >
   >
3. Z listy magazynów wybierz magazyn, aby otworzyć jego pulpit nawigacyjny. Po wybraniu magazynu, na pulpicie nawigacyjnym magazynu i **ustawienia** otwarcie. Na poniższej ilustracji **magazynu firmy Contoso** pulpit nawigacyjny zostanie wyróżniona.

    ![Otwórz pulpit nawigacyjny i blok ustawień](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Otwórz pulpit nawigacyjny elementu magazynu
W poprzedniej procedurze został otwarty na pulpicie nawigacyjnym magazynu. Aby otworzyć pulpit nawigacyjny elementu magazynu:

1. Na pulpicie nawigacyjnym magazynu na **elementy kopii zapasowej** kafelków, kliknij przycisk **maszyn wirtualnych platformy Azure**.

    ![Otwarte elementy kopii zapasowej kafelka](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    **Elementów kopii zapasowych** bloku Wyświetla ostatnie zadanie tworzenia kopii zapasowej dla każdego elementu. W tym przykładzie istnieje jedna maszyna wirtualna, demovm markgal, są chronione przez ten magazyn.  

    ![Kafelek elementów kopii zapasowych](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > W celu ułatwienia dostępu możesz przypiąć element magazynu do pulpitu nawigacyjnego platformy Azure. Aby przypiąć element magazynu na liście element magazynu, kliknij prawym przyciskiem myszy element i wybierz **Przypnij do pulpitu nawigacyjnego**.
   >
   >
2. W **elementy kopii zapasowej** bloku kliknij element, aby otworzyć pulpit nawigacyjny elementu magazynu.

    ![Kafelek elementów kopii zapasowych](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Pulpit nawigacyjny elementu magazynu i jego **ustawienia** otwarcie.

    ![Pulpit nawigacyjny elementów kopii zapasowych, z bloku ustawień](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    Na pulpicie nawigacyjnym magazynu elementu można wykonywać wiele zadań kluczy, takie jak:

   * Zmiana zasad lub utworzyć nowe zasady kopii zapasowych
   * Wyświetl punkty przywracania oraz wyświetlić ich stan spójności
   * na żądanie kopii zapasowej maszyny wirtualnej
   * Zatrzymaj ochronę maszyn wirtualnych
   * wznawianie ochrony maszyny wirtualnej
   * Usuń dane kopii zapasowej (lub punktu odzyskiwania)
   * [Przywracanie kopii zapasowych dysków](backup-azure-arm-restore-vms.md#create-new-restore-disks)

Dla poniższych procedur punkt początkowy jest pulpit nawigacyjny elementu magazynu.

## <a name="manage-backup-policies"></a>Zarządzanie zasadami tworzenia kopii zapasowych
1. Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **wszystkie ustawienia** otworzyć **ustawienia** bloku.

    ![Blok zasady tworzenia kopii zapasowej](./media/backup-azure-manage-vms/all-settings-button.png)
2. Na **ustawienia** bloku kliknij **zasady tworzenia kopii zapasowej** do otwierania tego bloku.

    W bloku są wyświetlane szczegóły kopii zapasowej zakres częstotliwości i przechowywania.

    ![Blok zasady tworzenia kopii zapasowej](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. Z **wybierz zasady tworzenia kopii zapasowej** menu:

   * Aby zmienić zasady, wybierz inne zasady, a następnie kliknij przycisk **Zapisz**. Nowe zasady zostaną natychmiast zastosowane do magazynu.
   * Aby utworzyć zasady, wybierz pozycję **Utwórz nowy**.

     ![Kopia zapasowa maszyny wirtualnej](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Aby uzyskać instrukcje dotyczące tworzenia zasad kopii zapasowych, zobacz [Definiowanie zasad tworzenia kopii zapasowej](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Zarządzanie zasadami tworzenia kopii zapasowych, upewnij się, że należy wykonać [najlepsze praktyki](backup-azure-vms-introduction.md#best-practices) pod kątem optymalnej wydajności kopii zapasowych
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>na żądanie kopii zapasowej maszyny wirtualnej
Na żądanie można wykonać kopii zapasowej maszyny wirtualnej, po został skonfigurowany do ochrony. Jeśli tworzenie początkowej kopii zapasowej jest w stanie oczekiwania, kopii zapasowej na żądanie tworzy pełną kopię maszyny wirtualnej w magazynie usługi Recovery Services. Jeśli tworzenie początkowej kopii zapasowej zostało ukończone, kopii zapasowej na żądanie wysyła zmian od poprzedniej migawki w magazynie usługi Recovery Services. Oznacza to, że kolejne kopie zapasowe są zawsze przyrostowe.

> [!NOTE]
> Zakres przechowywania dla kopii zapasowej na żądanie jest wartość przechowywania określona dla codziennego punktu kopii zapasowej w zasadach. Jeśli wybrano opcję nie codziennego punktu kopii zapasowej, tygodniowego punktu kopii zapasowej jest używany.
>
>

Aby wyzwolić kopii zapasowej na żądanie maszyny wirtualnej:

* Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **Utwórz teraz kopię zapasową**.

    ![Kopia zapasowa teraz przycisk.](./media/backup-azure-manage-vms/backup-now-button.png)

    Portal sprawia, że na pewno chcesz uruchomić zadanie tworzenia kopii zapasowej na żądanie. Kliknij przycisk **tak** można uruchomić zadania tworzenia kopii zapasowej.

    ![Kopia zapasowa teraz przycisk.](./media/backup-azure-manage-vms/backup-now-check.png)

    Zadanie tworzenia kopii zapasowej tworzy punkt odzyskiwania. Zakres przechowywania punktu odzyskiwania jest taka sama jak zakres przechowywania określonym w zasadach skojarzonego z maszyną wirtualną. Aby śledzić postępy zadania, na pulpicie nawigacyjnym magazynu kliknij **zadania tworzenia kopii zapasowej** kafelka.  

## <a name="stop-protecting-virtual-machines"></a>Zatrzymaj ochronę maszyn wirtualnych
Jeśli zdecydujesz się na zatrzymanie ochrony maszyny wirtualnej, zostanie wyświetlony monit, jeśli chcesz zachować punkty odzyskiwania. Istnieją dwa sposoby na zatrzymanie ochrony maszyn wirtualnych:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania lub
* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych, ale pozostawienie punktów odzyskiwania

Istnieje koszt związany z pozostawienia punktów odzyskiwania w magazynie. Jednak zaletą pozostawienia punktów odzyskiwania jest później, można przywrócić maszynę wirtualną w razie potrzeby. Aby uzyskać informacje o koszcie pozostawienia punktów odzyskiwania, zobacz [cennik](https://azure.microsoft.com/pricing/details/backup/). Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie można przywrócić maszyny wirtualnej.

Punkt odzyskiwania zostaną zachowane nieskończoność, dopóki elementu kopii zapasowej jest przełączona do trybu zasady przechowywania lub StopProtection usuwanie danych. W przypadku ponownego włączania ochrony nowe zasady, które skojarzono wprowadzenie określi przechowywania punktów odzyskiwania. Podobnie po usunięciu źródła danych bez przeprowadzenia Zatrzymaj tworzenie kopii zapasowej, nowych kopii zapasowych będzie kończyć się niepowodzeniem i wygaśnie stare punkty odzyskiwania zgodnie z zasadami przechowywania, ale jeden z ostatniego punktu odzyskiwania zawsze będą przechowywane do momentu wykonania Zatrzymaj tworzenie kopii zapasowej za pomocą usunięcie danych.

Aby zatrzymać ochronę dla maszyny wirtualnej:

1. Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **Zatrzymaj kopię zapasową**.

    ![Przycisk tworzenia kopii zapasowej Zatrzymaj](./media/backup-azure-manage-vms/stop-backup-button.png)

    Zostanie otwarty blok Zatrzymaj tworzenie kopii zapasowej.

    ![Zatrzymywanie tworzenia kopii zapasowej bloku](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. Na **Zatrzymaj tworzenie kopii zapasowej** bloku, wybierz, czy zachować, czy usuwanie danych kopii zapasowej. Pole informacyjne zawiera szczegółowe informacje o wybranym.

    ![Zatrzymaj ochronę](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Jeśli wybrano opcję Zachowaj dane kopii zapasowej, przejdź do kroku 4. Jeśli została wybrana opcja Usuń dane kopii zapasowej, upewnij się, że chcesz zatrzymać zadania tworzenia kopii zapasowej i usunąć punkty odzyskiwania — wpisz nazwę elementu.

    ![Zatrzymaj weryfikacji](./media/backup-azure-manage-vms/item-verification-box.png)

    Jeśli nie masz pewności nazwy elementu, umieść kursor nad wykrzyknik, aby wyświetlić nazwę. Ponadto nazwa elementu podlega **Zatrzymaj tworzenie kopii zapasowej** w górnej części bloku.
4. Opcjonalnie podaj **Przyczyna** lub **komentarz**.
5. Aby zatrzymać zadanie tworzenia kopii zapasowej dla bieżącego elementu, kliknij ![kopii zapasowej przycisk Zatrzymaj](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Komunikat powiadomienia informuje o tym, że zadania tworzenia kopii zapasowej zostały zatrzymane.

    ![Upewnij się, Zatrzymaj ochronę](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>wznawianie ochrony maszyny wirtualnej
Jeśli **Zachowaj dane kopii zapasowej** wybrano opcję podczas ochrony dla maszyny wirtualnej została zatrzymana, jest możliwe wznowić ochronę. Jeśli **Usuń dane kopii zapasowej** wybrano opcję, a następnie nie można wznowić ochronę maszyny wirtualnej.

Aby wznowić ochronę dla maszyny wirtualnej

1. Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **Wznów tworzenie kopii zapasowej**.

    ![Wznawianie ochrony](./media/backup-azure-manage-vms/resume-backup-button.png)

    Zostanie otwarty blok zasad tworzenia kopii zapasowej.

   > [!NOTE]
   > Gdy ponownie ochronę maszyny wirtualnej, można wybrać inne zasady niż zasady za pomocą której maszyna wirtualna była chroniona początkowo.
   >
   >
2. Postępuj zgodnie z instrukcjami w [Zarządzanie zasadami kopii zapasowych](backup-azure-manage-vms.md#manage-backup-policies) Aby przypisać zasady dla maszyny wirtualnej.

    Po zastosowaniu zasad tworzenia kopii zapasowej maszyny wirtualnej, zostanie wyświetlony następujący komunikat.

    ![Pomyślnie chronionej maszyny Wirtualnej](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej
Można usunąć danych kopii zapasowej skojarzonego z maszyną wirtualną w trakcie **Zatrzymaj kopię zapasową** zadania, lub w dowolnym momencie po zakończeniu tworzenia kopii zapasowej zadania zakończone. Wstrzymanie się z usunięciem punktów odzyskiwania przez określoną liczbę dni lub tygodni może być nawet korzystne. W odróżnieniu od przywracania punktów odzyskiwania podczas usuwania danych kopii zapasowej nie można wybrać punktów odzyskiwania do usunięcia. Jeśli zdecydujesz się usunąć dane kopii zapasowych, wszystkie skojarzone z tym elementem punkty odzyskiwania zostaną usunięte.

W poniższej procedurze przyjęto, zadanie tworzenia kopii zapasowej dla maszyny wirtualnej została zatrzymana lub wyłączona. Gdy zadanie tworzenia kopii zapasowej jest wyłączona, **Wznów tworzenie kopii zapasowej** i **usuwania kopii zapasowej** opcje są dostępne na pulpicie nawigacyjnym elementu magazynu.

![Wznów i usuń przyciski](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Można usunąć danych kopii zapasowej na maszynie wirtualnej za pomocą *kopii zapasowej wyłączone*:

1. Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **usuwania kopii zapasowej**.

    ![Typ maszyny wirtualnej](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    **Usuń dane kopii zapasowej** zostanie otwarty blok.

    ![Typ maszyny wirtualnej](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Wpisz nazwę elementu, aby potwierdzić, że chcesz usunąć punktów odzyskiwania.

    ![Zatrzymaj weryfikacji](./media/backup-azure-manage-vms/item-verification-box.png)

    Jeśli nie masz pewności nazwy elementu, umieść kursor nad wykrzyknik, aby wyświetlić nazwę. Ponadto nazwa elementu podlega **Usuń dane kopii zapasowej** w górnej części bloku.
3. Opcjonalnie podaj **Przyczyna** lub **komentarz**.
4. Aby usunąć dane kopii zapasowej dla bieżącego elementu, kliknij przycisk ![kopii zapasowej przycisk Zatrzymaj](./media/backup-azure-manage-vms/delete-button.png)

    Komunikat powiadomienia informuje o tym, czy dane kopii zapasowej został usunięty.

## <a name="next-steps"></a>Kolejne kroki
Informacje na temat ponownego tworzenia maszyny wirtualnej z punktu odzyskiwania, zapoznaj się z [przywracania maszyn wirtualnych platformy Azure](backup-azure-arm-restore-vms.md). Aby uzyskać informacje na temat ochrony maszyn wirtualnych, zobacz [Pierwsze spojrzenie: Tworzenie kopii zapasowych maszyn wirtualnych w magazynie usługi Recovery Services](backup-azure-vms-first-look-arm.md). Aby uzyskać informacje na temat monitorowania zdarzeń, zobacz [monitorowanie alertów związanych z kopiami zapasowymi maszyny wirtualnej platformy Azure](backup-azure-monitor-vms.md).
