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
ms.openlocfilehash: b08e8ea6a8768510177f1ea664f3036813e1a890
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2019
ms.locfileid: "57009923"
---
# <a name="manage-azure-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure

W tym artykule opisano sposób zarządzania maszynami wirtualnymi Azure kopii zapasowej za pomocą [usługę Azure Backup](backup-overview.md) kopie zapasowe, a także podsumowano alerty kopii zapasowej informacji dostępnych w pulpicie nawigacyjnym portalu.


W witrynie Azure portal na pulpicie nawigacyjnym magazynu usług Recovery Services zapewnia dostęp do informacji na temat magazynu, w tym:

* Najnowszej kopii zapasowej, która jest również najnowszy punkt przywracania.
* zasady kopii zapasowych
* Całkowity rozmiar wszystkich migawek kopii zapasowych
* Liczba maszyn wirtualnych, włączone do utworzenia kopii zapasowej

Możesz zarządzać kopiami zapasowymi przy użyciu pulpitu nawigacyjnego, a przy przechodzeniu do szczegółów poszczególnych maszyn wirtualnych. Wykonywanie kopii zapasowej maszyny zaczynają się od otwierania magazynu na pulpicie nawigacyjnym.

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
1. Na [pulpitu nawigacyjnego magazynu elementu](#view-vms-in-the-dashboard), kliknij przycisk **wszystkie ustawienia** .

    ![Blok zasady tworzenia kopii zapasowej](./media/backup-azure-manage-vms/all-settings-button.png)
2. W **ustawienia**, kliknij przycisk **zasady tworzenia kopii zapasowej**.
3. Z **wybierz zasady tworzenia kopii zapasowej** menu:

   * Aby zmienić zasady, wybierz inne zasady, a następnie kliknij przycisk **Zapisz**. Nowe zasady zostaną natychmiast zastosowane do magazynu.
   * Aby utworzyć zasady, wybierz pozycję **Utwórz nowy**. [Dowiedz się więcej](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![Kopia zapasowa maszyny wirtualnej](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Uruchomienie w kopii zapasowej na żądanie
Na żądanie można wykonać kopii zapasowej maszyny wirtualnej, po został skonfigurowany do ochrony.
- Jeśli tworzenie początkowej kopii zapasowej jest w stanie oczekiwania, kopii zapasowej na żądanie tworzy pełną kopię maszyny wirtualnej w magazynie usługi Recovery Services.
- Jeśli tworzenie początkowej kopii zapasowej zostało ukończone, kopii zapasowej na żądanie wysyła zmian od poprzedniej migawki w magazynie usługi Recovery Services. Oznacza to, że kolejne kopie zapasowe są zawsze przyrostowe.
- Zakres przechowywania dla kopii zapasowej na żądanie jest wartość przechowywania określona w momencie wyzwalanie zadania tworzenia kopii zapasowej.

Aby wyzwolić kopii zapasowej na żądanie:

1. Na [pulpitu nawigacyjnego magazynu elementu](#view-vms-in-the-dashboard), kliknij przycisk **elementu kopii zapasowej** w obszarze **chronionego elementu** sekcji.

    ![Kopia zapasowa teraz przycisk.](./media/backup-azure-manage-vms/backup-now-button.png)

2. Kliknij przycisk **maszyny wirtualnej platformy Azure** z **zarządzania typ kopii zapasowej**. **Elementu kopii zapasowej (maszynie wirtualnej platformy Azure)** zostanie wyświetlony blok.
3. Wybierz maszynę Wirtualną, a następnie kliknij przycisk **Utwórz kopię zapasową teraz** do utworzenia kopii zapasowej na żądanie. **Bloku Utwórz teraz kopię zapasową** pojawia się.
4. W **Przechowuj kopię zapasową do** opcji, należy określić datę dla narzędzia Kopia zapasowa ma zostać zachowana.

    ![Kopia zapasowa teraz przycisk.](./media/backup-azure-manage-vms/backup-now-check.png)

5. Kliknij przycisk **OK**, aby uruchomić zadanie tworzenia kopii zapasowej.

Aby śledzić postępy zadania, na pulpicie nawigacyjnym magazynu kliknij **zadania tworzenia kopii zapasowej** kafelka.

## <a name="stop-protecting-a-vm"></a>Zatrzymaj ochronę maszyny Wirtualnej

Istnieją dwa sposoby na zatrzymanie ochrony maszyn wirtualnych:

- Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania. Nie można przywrócić maszynę Wirtualną, w tym przypadku.
- Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej, ale zachowaj punkty odzyskiwania. Istnieje koszt związany z zachowaniem punktów odzyskiwania w magazynie. Zalet przechowywania punktów odzyskiwania jest jednak, że można przywrócić maszynę Wirtualną, jeśli to konieczne. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/backup/) o szczegółach cennika.

>[!NOTE]
>
* Jeśli użytkownik **Zatrzymaj kopię zapasową** z **Zachowaj dane kopii zapasowej**, nie wygasną punktów odzyskiwania zgodnie z zasadami tworzenia kopii zapasowych. Opłaty są naliczane za chronione wystąpienia i użytego miejsca do magazynowania. Punkty odzyskiwania zostaną tylko wyczyszczone po wznowienia tworzenia kopii zapasowych (ponownie włączysz ochronę) zgodnie z zasadami lub ręcznie usuń dane kopii zapasowej.
* Jeśli usuniesz źródła danych bez zatrzymywania tworzenia kopii zapasowych, nowych kopii zapasowych będzie kończyć się niepowodzeniem. Ponownie wygaśnie stare punkty odzyskiwania zgodnie z zasadami, ale jeden z ostatniego punktu odzyskiwania zawsze zostaną zachowane, dopóki Zatrzymaj kopię zapasową i Usuń dane.

Aby zatrzymać ochronę dla maszyny wirtualnej:

1. Na [pulpitu nawigacyjnego magazynu elementu](#view-vms-in-the-dashboard), kliknij przycisk **Zatrzymaj kopię zapasową**.
2. Wybierz, czy zachować, czy usuwanie danych kopii zapasowej i upewnij się, zgodnie z potrzebami. Upewnij się, zgodnie z wymaganiami i opcjonalnie podaj komentarz. Jeśli nie masz pewności nazwy elementu, umieść kursor nad wykrzyknik, aby wyświetlić nazwę.

    ![Zatrzymaj ochronę](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 Komunikat powiadomienia informuje o tym, że zadania tworzenia kopii zapasowej zostały zatrzymane.


## <a name="resume-protection-of-a-vm"></a>Wznawianie ochrony maszyny Wirtualnej

Dane kopii zapasowej jest zachowywana, gdy maszyna wirtualna została zatrzymana, po wznowieniu ochrony. Jeśli usunięte dane kopii zapasowej, a następnie nie można wznowić.

Aby wznowić ochronę dla maszyny wirtualnej:

1. Na [pulpitu nawigacyjnego magazynu elementu](#view-vms-in-the-dashboard), kliknij przycisk **Wznów tworzenie kopii zapasowej**.

2. Postępuj zgodnie z instrukcjami w [Zarządzanie zasadami kopii zapasowych](#manage-backup-policies) Aby przypisać zasady dla maszyny wirtualnej. Możesz wybrać inne zasady niż zasady za pomocą której maszyna wirtualna była chroniona początkowo.
3. Po zastosowaniu zasad tworzenia kopii zapasowej maszyny wirtualnej zostanie wyświetlony następujący komunikat.

    ![Pomyślnie chronionej maszyny Wirtualnej](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Można usunąć danych kopii zapasowej skojarzony z maszyną Wirtualną w trakcie **Zatrzymaj kopię zapasową** zadania, lub w dowolnym momencie po zakończeniu tworzenia kopii zapasowej zadania zakończone.

- Wstrzymanie się z usunięciem punktów odzyskiwania przez określoną liczbę dni lub tygodni może być nawet korzystne.
- W odróżnieniu od przywracania punktów odzyskiwania podczas usuwania danych kopii zapasowej nie można wybrać punktów odzyskiwania do usunięcia. Jeśli zdecydujesz się usunąć dane kopii zapasowych, wszystkie skojarzone z tym elementem punkty odzyskiwania zostaną usunięte.

Ta procedura zakłada, zadanie tworzenia kopii zapasowej dla maszyny Wirtualnej została zatrzymana lub wyłączona.


1. Na [pulpitu nawigacyjnego magazynu elementu](#view-vms-in-the-dashboard), kliknij przycisk **usuwania kopii zapasowej**.

    ![Typ maszyny wirtualnej](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. Wpisz nazwę elementu, aby potwierdzić, że chcesz usunąć punktów odzyskiwania.

    ![Zatrzymaj weryfikacji](./media/backup-azure-manage-vms/item-verification-box.png)

4. Aby usunąć dane kopii zapasowej dla bieżącego elementu, kliknij przycisk ![kopii zapasowej przycisk Zatrzymaj](./media/backup-azure-manage-vms/delete-button.png)

    Komunikat powiadomienia informuje o tym, czy dane kopii zapasowej został usunięty.

## <a name="next-steps"></a>Kolejne kroki
- [Dowiedz się więcej o](backup-azure-vms-first-look-arm.md) zapisywania kopii zapasowych maszyn wirtualnych platformy Azure z ustawieniami maszyny Wirtualnej.
- [Dowiedz się więcej o](backup-azure-arm-restore-vms.md) przywracania maszyn wirtualnych.
- [Dowiedz się więcej o](backup-azure-monitor-vms.md) monitorowania kopii zapasowych maszyn wirtualnych platformy Azure.
