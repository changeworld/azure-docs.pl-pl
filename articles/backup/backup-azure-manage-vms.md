---
title: Zarządzanie i monitorowanie kopii zapasowych maszyn wirtualnych platformy Azure, przy użyciu usługi Azure Backup
description: Informacje o sposobie zarządzania i monitorowania kopii zapasowych maszyn wirtualnych platformy Azure, za pomocą usługi Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: da686e53f711877642a33806719a0d71050e721b
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669384"
---
# <a name="manage-azure-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure

W tym artykule opisano sposób zarządzania maszynami wirtualnymi Azure kopii zapasowej za pomocą [usługę Azure Backup](backup-overview.md) kopie zapasowe, a także podsumowano alerty kopii zapasowej informacji dostępnych w pulpicie nawigacyjnym portalu.


W witrynie Azure portal na pulpicie nawigacyjnym magazynu usług Recovery Services zapewnia dostęp do informacji na temat magazynu, w tym:

* Najnowszej kopii zapasowej, która jest również najnowszy punkt przywracania.
* zasady kopii zapasowych
* Całkowity rozmiar wszystkich migawek kopii zapasowych
* Liczba maszyn wirtualnych, włączone do utworzenia kopii zapasowej

Możesz zarządzać kopiami zapasowymi przy użyciu pulpitu nawigacyjnego, a przy przechodzeniu do szczegółów poszczególnych maszyn wirtualnych. Kopia zapasowa achine rozpoczynają się od otwierania magazynu na pulpicie nawigacyjnym. 

![Pełen przegląd za pomocą suwaka](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>Wyświetlanie maszyn wirtualnych w pulpicie nawigacyjnym

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu Centrum kliknij opcję **Przeglądaj** i na liście zasobów wpisz ciąg **Usługi odzyskiwania**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Kliknij opcję **Magazyn Usług odzyskiwania**. 

    ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. W celu ułatwienia, kliknij prawym przyciskiem myszy magazyn na liście magazynów > **Przypnij do pulpitu nawigacyjnego**.
4. Otwórz pulpit nawigacyjny magazynu. 
    ![Otwórz pulpit nawigacyjny i blok ustawień](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. Na **elementy kopii zapasowej** kafelków, kliknij przycisk **maszyn wirtualnych platformy Azure**.

    ![Otwarte elementy kopii zapasowej kafelka](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. W **elementów kopii zapasowych** bloku, możesz zobaczyć ostatnie zadanie tworzenia kopii zapasowej dla każdego elementu. W tym przykładzie istnieje jedna maszyna wirtualna, demovm markgal, są chronione przez ten magazyn.  

    ![Kafelek elementów kopii zapasowych](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. Na pulpicie nawigacyjnym elementu magazynu można utworzyć lub zmodyfikować zasady tworzenia kopii zapasowych, Wyświetl punkty przywracania, uruchom zatrzymywanie tworzenia kopii zapasowej, na żądanie i wznowić ochronę maszyn wirtualnych, usunąć punkty odzyskiwania i uruchom Przywracanie.

    ![Pulpit nawigacyjny elementów kopii zapasowych, z bloku ustawień](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>Zarządzanie zasadami tworzenia kopii zapasowych
1. Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **wszystkie ustawienia** .

    ![Blok zasady tworzenia kopii zapasowej](./media/backup-azure-manage-vms/all-settings-button.png)
2. W **ustawienia**, kliknij przycisk**zasady tworzenia kopii zapasowej**e.
3. Z **wybierz zasady tworzenia kopii zapasowej** menu:

   * Aby zmienić zasady, wybierz inne zasady, a następnie kliknij przycisk **Zapisz**. Nowe zasady zostaną natychmiast zastosowane do magazynu.
   * Aby utworzyć zasady, wybierz pozycję **Utwórz nowy**. [Dowiedz się więcej](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![Kopia zapasowa maszyny wirtualnej](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Uruchomienie w kopii zapasowej na żądanie
Na żądanie można wykonać kopii zapasowej maszyny wirtualnej, po został skonfigurowany do ochrony.
- Jeśli tworzenie początkowej kopii zapasowej jest w stanie oczekiwania, kopii zapasowej na żądanie tworzy pełną kopię maszyny wirtualnej w magazynie usługi Recovery Services.
- Jeśli tworzenie początkowej kopii zapasowej zostało ukończone, kopii zapasowej na żądanie wysyła zmian od poprzedniej migawki w magazynie usługi Recovery Services. Oznacza to, że kolejne kopie zapasowe są zawsze przyrostowe.
- Zakres przechowywania dla kopii zapasowej na żądanie jest wartość przechowywania określona dla codziennego punktu kopii zapasowej w zasadach. Jeśli wybrano opcję nie codziennego punktu kopii zapasowej, tygodniowego punktu kopii zapasowej jest używany.


Aby wyzwolić kopii zapasowej na żądanie:

1. Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **Utwórz teraz kopię zapasową**.

    ![Kopia zapasowa teraz przycisk.](./media/backup-azure-manage-vms/backup-now-button.png)

 2. Kliknij przycisk **tak** można uruchomić zadania tworzenia kopii zapasowej.

    ![Kopia zapasowa teraz przycisk.](./media/backup-azure-manage-vms/backup-now-check.png)

 
 Zadanie tworzenia kopii zapasowej tworzy punkt odzyskiwania. Zakres przechowywania punktu odzyskiwania jest taka sama jak zakres przechowywania określonym w zasadach skojarzonego z maszyną wirtualną. Aby śledzić postępy zadania, na pulpicie nawigacyjnym magazynu kliknij **zadania tworzenia kopii zapasowej** kafelka.  

## <a name="stop-protecting-a-vm"></a>Zatrzymaj ochronę maszyny Wirtualnej

Istnieją dwa sposoby na zatrzymanie ochrony maszyn wirtualnych:

- Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania. Nie można przywrócić maszynę Wirtualną, w tym przypadku.
- Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej, ale pozostawienie punktów odzyskiwania. Istnieje koszt związany z pozostawienia punktów odzyskiwania w magazynie. Zaletą pozostawienia punktów odzyskiwania jest jednak, że można przywrócić maszynę Wirtualną, jeśli to konieczne. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/backup/) o szczegółach cennika.

Należy pamiętać, że:

- Pozostawienie punktów odzyskiwania, punkty zostaną wyczyszczone zgodnie z zasadami tworzenia kopii zapasowej. Zostaną naliczone opłaty za chronione wystąpienie i użytego miejsca do magazynowania, dopóki wszystkie punkty odzyskiwania są czyszczone. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/backup/) o cenach.
- Gdy punkty odzyskiwania pozostanie bez zmian, mimo że wygasają zgodnie z zasadami przechowywania, kopia zapasowa Azure zawsze przechowuje jeden ostatniego punktu odzyskiwania aż jawnie usuniesz dane kopii zapasowej.
- Jeśli usuniesz źródła danych bez zatrzymywania tworzenia kopii zapasowych, nowych kopii zapasowych będzie kończyć się niepowodzeniem. Ponownie wygaśnie stare punkty odzyskiwania zgodnie z zasadami, ale jeden z ostatniego punktu odzyskiwania zawsze zostaną zachowane, dopóki Zatrzymaj kopię zapasową i Usuń dane.



Aby zatrzymać ochronę dla maszyny wirtualnej:

1. Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **Zatrzymaj kopię zapasową**.
2. Wybierz, czy zachować, czy usuwanie danych kopii zapasowej i upewnij się, zgodnie z potrzebami. Upewnij się, zgodnie z wymaganiami i opcjonalnie podaj komentarz. Jeśli nie masz pewności nazwy elementu, umieść kursor nad wykrzyknik, aby wyświetlić nazwę.

    ![Zatrzymaj ochronę](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 Komunikat powiadomienia informuje o tym, że zadania tworzenia kopii zapasowej zostały zatrzymane.


## <a name="resume-protection-of-a-vm"></a>Wznawianie ochrony maszyny Wirtualnej

Dane kopii zapasowej jest zachowywana, gdy maszyna wirtualna została zatrzymana, po wznowieniu ochrony. Jeśli usunięte dane kopii zapasowej, a następnie nie można wznowić.

Usuń

1. Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **Wznów tworzenie kopii zapasowej**.

2. Postępuj zgodnie z instrukcjami w [Zarządzanie zasadami kopii zapasowych](backup-azure-manage-vms.md#manage-backup-policies) Aby przypisać zasady dla maszyny wirtualnej. Możesz wybrać inne zasady niż zasady za pomocą której maszyna wirtualna była chroniona początkowo.
3. Po zastosowaniu zasad tworzenia kopii zapasowej maszyny wirtualnej zostanie wyświetlony następujący komunikat.

    ![Pomyślnie chronionej maszyny Wirtualnej](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Można usunąć danych kopii zapasowej skojarzony z maszyną Wirtualną w trakcie **Zatrzymaj kopię zapasową** zadania, lub w dowolnym momencie po zakończeniu tworzenia kopii zapasowej zadania zakończone.

- Wstrzymanie się z usunięciem punktów odzyskiwania przez określoną liczbę dni lub tygodni może być nawet korzystne.
- W odróżnieniu od przywracania punktów odzyskiwania podczas usuwania danych kopii zapasowej nie można wybrać punktów odzyskiwania do usunięcia. Jeśli zdecydujesz się usunąć dane kopii zapasowych, wszystkie skojarzone z tym elementem punkty odzyskiwania zostaną usunięte.

Ta procedura zakłada, zadanie tworzenia kopii zapasowej dla maszyny Wirtualnej została zatrzymana lub wyłączona.


1. Na [pulpitu nawigacyjnego magazynu elementu](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kliknij przycisk **usuwania kopii zapasowej**.

    ![Typ maszyny wirtualnej](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. Wpisz nazwę elementu, aby potwierdzić, że chcesz usunąć punktów odzyskiwania.

    ![Zatrzymaj weryfikacji](./media/backup-azure-manage-vms/item-verification-box.png)

4. Aby usunąć dane kopii zapasowej dla bieżącego elementu, kliknij przycisk ![kopii zapasowej przycisk Zatrzymaj](./media/backup-azure-manage-vms/delete-button.png)

    Komunikat powiadomienia informuje o tym, czy dane kopii zapasowej został usunięty.

## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się więcej o](backup-azure-vms-first-look-arm.md) zapisywania kopii zapasowych maszyn wirtualnych platformy Azure z ustawieniami maszyny Wirtualnej.
- [Dowiedz się więcej o](backup-azure-arm-restore-vms.md) przywracania maszyn wirtualnych. 
- [Dowiedz się więcej o](backup-azure-monitor-vms.md) monitorowania kopii zapasowych maszyn wirtualnych platformy Azure.
 
