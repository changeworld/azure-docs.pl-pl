---
title: Zarządzanie i monitorowanie kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup
description: Dowiedz się, jak zarządzać i monitorować kopie zapasowe maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: aa953440f03137f3359276bc9e06cb0c73f0ab4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61219299"
---
# <a name="manage-azure-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure

W tym artykule opisano sposób zarządzania maszyn wirtualnych (VM), które tworzy kopię zapasową przy użyciu [usługi Azure Backup](backup-overview.md). Artykuł zawiera również podsumowanie informacji o kopii zapasowej można znaleźć na pulpicie nawigacyjnym magazynu.


W witrynie Azure portal na pulpicie nawigacyjnym magazynu usług Recovery Services zapewnia dostęp do magazynu informacji, w tym:

* Najnowszej kopii zapasowej, która jest również najnowszy punkt przywracania.
* Zasady tworzenia kopii zapasowej.
* Całkowity rozmiar wszystkich migawek kopii zapasowych.
* Liczba maszyn wirtualnych, które są włączone dla kopii zapasowych.

Tworzenie kopii zapasowych można zarządzać przy użyciu pulpitu nawigacyjnego oraz przy przechodzeniu do szczegółów poszczególnych maszyn wirtualnych. Aby rozpocząć tworzenie kopii zapasowych maszyny, otwórz magazynu na pulpicie nawigacyjnym.

![Wyświetl pełny pulpit nawigacyjny za pomocą suwaka](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>View VMS maszyn wirtualnych na pulpicie nawigacyjnym

Aby wyświetlić maszyny wirtualne na pulpicie nawigacyjnym magazynu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu Centrum wybierz **Przeglądaj**. Na liście zasobów wpisz **Usługi odzyskiwania**. Podczas wpisywania lista jest filtrowana w oparciu o wpisywane dane. Wybierz **Magazyny usługi Recovery Services**.

    ![Tworzenie magazynu usługi Recovery Services](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. W celu ułatwienia, kliknij prawym przyciskiem myszy magazyn, a następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.
4. Otwórz pulpit nawigacyjny magazynu.

    ![Otwórz pulpit nawigacyjny i blok ustawień](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Na **elementy kopii zapasowej** kafelka, wybierz opcję **maszyn wirtualnych platformy Azure**.

    ![Otwórz Kafelek elementy kopii zapasowej](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Na **elementy kopii zapasowej** bloku można wyświetlić listę chronionych maszyn wirtualnych. W tym przykładzie magazynu zapewnia ochronę maszyn wirtualnych: demobackup.  

    ![Widok bloku elementy kopii zapasowej](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Z poziomu pulpitu nawigacyjnego elementu magazynu zmodyfikuj zasady tworzenia kopii zapasowych, uruchamianie, zatrzymywanie tworzenia kopii zapasowej, na żądanie lub wznowić ochronę maszyn wirtualnych, Usuń dane kopii zapasowej, wyświetlanie punktów przywracania i uruchomić operację przywracania.

    ![Elementy kopii zapasowej pulpit nawigacyjny i blok ustawienia](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Zarządzanie zasadami tworzenia kopii zapasowej dla maszyny Wirtualnej

Aby zarządzać zasad tworzenia kopii zapasowej:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Otwórz pulpit nawigacyjny magazynu.
2. Na **elementy kopii zapasowej** kafelka, wybierz opcję **maszyn wirtualnych platformy Azure**.

    ![Otwórz Kafelek elementy kopii zapasowej](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Na **elementów kopii zapasowych** bloku można wyświetlić listę chronionych maszyn wirtualnych i stan ostatniej kopii zapasowej za pomocą Najpóźniejsza godzina punktów przywracania.

    ![Widok bloku elementy kopii zapasowej](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Z poziomu pulpitu nawigacyjnego elementu magazynu możesz wybrać zasady tworzenia kopii zapasowych.

   * Przełącz zasady, wybierz inne zasady, a następnie wybierz pozycję **Zapisz**. Nowe zasady zostaną natychmiast zastosowane do magazynu.

     ![Wybierz zasady tworzenia kopii zapasowej](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Uruchomienie w kopii zapasowej na żądanie
Po skonfigurowaniu ochrony, możesz uruchomić na żądanie kopii zapasowej maszyny Wirtualnej. Te szczegóły należy pamiętać:

- Jeśli tworzenie początkowej kopii zapasowej jest w stanie oczekiwania, kopii zapasowej na żądanie tworzy pełną kopię maszyny Wirtualnej w magazynie usługi Recovery Services.
- Jeśli tworzenie początkowej kopii zapasowej zostało zakończone, kopii zapasowej na żądanie wysyła zmian od poprzedniej migawki w magazynie usługi Recovery Services. Oznacza to nowsze kopie zapasowe są zawsze przyrostowe.
- Zakres przechowywania dla kopii zapasowej na żądanie jest wartość przechowywania Określ, kiedy wyzwolić tworzenie kopii zapasowej.

Aby wyzwolić kopii zapasowej na żądanie:

1. Na [pulpitu nawigacyjnego magazynu elementu](#view-vms-on-the-dashboard)w obszarze **chronionego elementu**, wybierz opcję **elementu kopii zapasowej**.

    ![Opcja kopia zapasowa teraz](./media/backup-azure-manage-vms/backup-now-button.png)

2. Z **typ zarządzania kopiami zapasowymi**, wybierz opcję **maszyny wirtualnej platformy Azure**. **Elementu kopii zapasowej (maszynie wirtualnej platformy Azure)** zostanie wyświetlony blok.
3. Wybierz maszynę Wirtualną, a następnie wybierz pozycję **Utwórz kopię zapasową teraz** do utworzenia kopii zapasowej na żądanie. **Utwórz teraz kopię zapasową** zostanie wyświetlony blok.
4. W **Przechowuj kopię zapasową do** Określ datę dla narzędzia Kopia zapasowa ma zostać zachowana.

    ![Utwórz teraz kopię zapasową kalendarza](./media/backup-azure-manage-vms/backup-now-check.png)

5. Wybierz **OK** do uruchomienia zadania tworzenia kopii zapasowej.

Aby śledzić postęp zadania, na pulpicie nawigacyjnym magazynu, zaznacz **zadania tworzenia kopii zapasowej** kafelka.

## <a name="stop-protecting-a-vm"></a>Zatrzymaj ochronę maszyny Wirtualnej

Istnieją dwa sposoby na zatrzymanie ochrony maszyn wirtualnych:

- Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania. W tym przypadku nie będzie mógł przywrócić maszynę Wirtualną.
- Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i Zachowaj punkty odzyskiwania. Mimo że będzie trzeba płacić za Zachowaj punkty odzyskiwania w magazynie, będzie można przywrócić maszynę Wirtualną, jeśli to konieczne. Aby uzyskać więcej informacji, zobacz [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Jeśli usuniesz źródła danych bez konieczności zatrzymywania tworzenia kopii zapasowych nowych kopii zapasowych zakończy się niepowodzeniem. Wygaśnie stare punkty odzyskiwania zgodnie z zasadami, ale jeden z ostatniego punktu odzyskiwania zawsze zostaną zachowane, dopóki Zatrzymaj tworzenie kopii zapasowych i usunąć dane.
>

Aby zatrzymać ochronę dla maszyny Wirtualnej:

1. Na [elementu w pulpicie nawigacyjnym magazynu](#view-vms-on-the-dashboard), wybierz opcję **Zatrzymaj kopię zapasową**.
2. Wybierz, czy zachować, czy usuwanie danych kopii zapasowej i Potwierdź wybór, zgodnie z potrzebami. Jeśli chcesz, Dodaj komentarz. Jeśli nie masz pewności nazwy elementu, umieść kursor nad wykrzyknik, aby wyświetlić nazwę.

    ![Zatrzymaj ochronę](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     Powiadomienia informuje o tym, że zadania tworzenia kopii zapasowej została zatrzymana.

## <a name="resume-protection-of-a-vm"></a>Wznawianie ochrony maszyny Wirtualnej

Jeśli dane kopii zapasowych są przechowywane po zatrzymaniu maszyny Wirtualnej, później można wznowić ochronę. Jeśli usuniesz dane kopii zapasowej, nie można wznowić ochronę.

Aby wznowić ochronę dla maszyny Wirtualnej:

1. Na [elementu w pulpicie nawigacyjnym magazynu](#view-vms-on-the-dashboard), wybierz opcję **Wznów tworzenie kopii zapasowej**.

2. Postępuj zgodnie z instrukcjami w [Zarządzanie zasadami kopii zapasowych](#manage-backup-policy-for-a-vm) Aby przypisać zasady dla maszyny Wirtualnej. Nie ma potrzeby wybierz zasady ochrony początkowej maszyny Wirtualnej.
3. Po zastosowaniu zasad tworzenia kopii zapasowej maszyny Wirtualnej zostanie wyświetlony następujący komunikat:

    ![Komunikat informujący o pomyślnym chronionej maszyny Wirtualnej](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Można usunąć danych kopii zapasowej maszyny Wirtualnej podczas **Zatrzymaj kopię zapasową** zadania lub po zakończeniu zadania tworzenia kopii zapasowej. Przed usunięciem danych kopii zapasowej należy pamiętać o te informacje:

- Może to być dobry pomysł, aby dni lub tygodni przed usunięciem punktów odzyskiwania.
- W przeciwieństwie do procesu przywracania punktów odzyskiwania, gdy usuniesz dane kopii zapasowej, nie możesz wybrać punktów odzyskiwania do usunięcia. Jeśli usuniesz dane kopii zapasowej, należy usunąć wszystkie punkty odzyskiwania skojarzone.

Po Zatrzymaj lub Wyłącz zadanie tworzenia kopii zapasowej maszyny Wirtualnej, można usunąć danych kopii zapasowej:


1. Na [pulpitu nawigacyjnego magazynu elementu](#view-vms-on-the-dashboard), wybierz opcję **Usuń dane kopii zapasowej**.

    ![Wybieranie kopii zapasowej Delete](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. Wpisz nazwę elementu kopii zapasowej, aby upewnić się, że chcesz usunąć punkty odzyskiwania.

    ![Upewnij się, że chcesz usunąć punkty odzyskiwania](./media/backup-azure-manage-vms/item-verification-box.png)

1. Aby usunąć dane kopii zapasowej dla elementu, wybierz **Usuń**. Komunikat z powiadomieniem poinformuje Cię o tym, że dane kopii zapasowej został usunięty.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z ustawień maszyny Wirtualnej](backup-azure-vms-first-look-arm.md).
- Dowiedz się, jak [przywracanie maszyn wirtualnych](backup-azure-arm-restore-vms.md).
- Dowiedz się, jak [monitorować kopie zapasowe maszyn wirtualnych platformy Azure](backup-azure-monitor-vms.md).
