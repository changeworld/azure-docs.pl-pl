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
ms.openlocfilehash: add2c72535b5be0edcbc00c077dfe20a6deaa3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434178"
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

* **Zatrzymaj ochronę i Zachowaj dane kopii zapasowej**. Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej z ochronę maszyny Wirtualnej; Jednak usługa Azure Backup zostaną zachowane punktów odzyskiwania, które zostały utworzone kopie zapasowe.  Musisz zapłacić Zachowaj punkty odzyskiwania w magazynie (zobacz [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/) Aby uzyskać szczegółowe informacje). Będzie można przywrócić maszynę Wirtualną, jeśli to konieczne. Jeśli zdecydujesz wznowić ochronę maszyny Wirtualnej, a następnie można użyć *Wznów tworzenie kopii zapasowej* opcji.
* **Zatrzymanie ochrony i usunięcie danych kopii zapasowej**. Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej z ochronę maszyny Wirtualnej i usunięcie wszystkich punktów odzyskiwania. Nie można przywrócić maszynę Wirtualną ani używać *Wznów tworzenie kopii zapasowej* opcji.

>[!NOTE]
>Jeśli usuniesz źródła danych bez konieczności zatrzymywania tworzenia kopii zapasowych nowych kopii zapasowych zakończy się niepowodzeniem. Wygaśnie stare punkty odzyskiwania zgodnie z zasadami, ale jeden z ostatniego punktu odzyskiwania zawsze zostaną zachowane, dopóki Zatrzymaj tworzenie kopii zapasowych i usunąć dane.
>

### <a name="stop-protection-and-retain-backup-data"></a>Zatrzymaj ochronę i Zachowaj dane kopii zapasowej

Zatrzymaj ochronę i przechowywanie danych maszyny Wirtualnej:

1. Na [elementu w pulpicie nawigacyjnym magazynu](#view-vms-on-the-dashboard), wybierz opcję **Zatrzymaj kopię zapasową**.
2. Wybierz **Zachowaj dane kopii zapasowej**i potwierdzić wybór, zgodnie z potrzebami. Jeśli chcesz, Dodaj komentarz. Jeśli nie masz pewności nazwy elementu, umieść kursor nad wykrzyknik, aby wyświetlić nazwę.

    ![Zachowaj dane kopii zapasowej](./media/backup-azure-manage-vms/retain-backup-data.png)

Powiadomienia informuje o tym, że zadania tworzenia kopii zapasowej została zatrzymana.

### <a name="stop-protection-and-delete-backup-data"></a>Zatrzymanie ochrony i usunięcie danych kopii zapasowej

Zatrzymaj ochronę, a następnie usunąć danych maszyny Wirtualnej:

1. Na [elementu w pulpicie nawigacyjnym magazynu](#view-vms-on-the-dashboard), wybierz opcję **Zatrzymaj kopię zapasową**.
2. Wybierz **Usuń dane kopii zapasowej**i potwierdzić wybór, zgodnie z potrzebami. Wprowadź nazwę elementu kopii zapasowej i w razie potrzeby dodać komentarz.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Wznawianie ochrony maszyny Wirtualnej

Jeżeli wybrano [zatrzymanie ochrony i Zachowaj dane kopii zapasowej](#stop-protection-and-retain-backup-data) opcji podczas Zatrzymaj ochronę maszyny Wirtualnej, a następnie można użyć **Wznów tworzenie kopii zapasowej**. Ta opcja jest niedostępna w przypadku wybrania [zatrzymanie ochrony i usunięcie danych kopii zapasowej](#stop-protection-and-delete-backup-data) opcji lub [Usuń dane kopii zapasowej](#delete-backup-data).

Aby wznowić ochronę dla maszyny Wirtualnej:

1. Na [elementu w pulpicie nawigacyjnym magazynu](#view-vms-on-the-dashboard), wybierz opcję **Wznów tworzenie kopii zapasowej**.

2. Postępuj zgodnie z instrukcjami w [Zarządzanie zasadami kopii zapasowych](#manage-backup-policy-for-a-vm) Aby przypisać zasady dla maszyny Wirtualnej. Nie ma potrzeby wybierz zasady ochrony początkowej maszyny Wirtualnej.
3. Po zastosowaniu zasad tworzenia kopii zapasowej maszyny Wirtualnej zostanie wyświetlony następujący komunikat:

    ![Komunikat informujący o pomyślnym chronionej maszyny Wirtualnej](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Istnieją dwa sposoby Usuń dane kopii zapasowej maszyny Wirtualnej:

- Na pulpicie nawigacyjnym elementu magazynu, wybierz Zatrzymaj kopię zapasową, a następnie postępuj zgodnie z instrukcjami dotyczącymi [zatrzymanie ochrony i usunięcie danych kopii zapasowej](#stop-protection-and-delete-backup-data) opcji.

  ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-manage-vms/stop-backup-buttom.png)

- Na pulpicie nawigacyjnym elementu magazynu wybierz pozycję Usuń dane kopii zapasowej. Ta opcja jest włączona, jeśli użytkownik wybierze [zatrzymanie ochrony i Zachowaj dane kopii zapasowej](#stop-protection-and-retain-backup-data) opcji podczas Zatrzymaj ochronę maszyny Wirtualnej

  ![Wybieranie kopii zapasowej Delete](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  - Na [pulpitu nawigacyjnego magazynu elementu](#view-vms-on-the-dashboard), wybierz opcję **Usuń dane kopii zapasowej**.
  - Wpisz nazwę elementu kopii zapasowej, aby upewnić się, że chcesz usunąć punkty odzyskiwania.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-manage-vms/delete-backup-data1.png)

  - Aby usunąć dane kopii zapasowej dla elementu, wybierz **Usuń**. Komunikat z powiadomieniem poinformuje Cię o tym, że dane kopii zapasowej został usunięty.

  > [!NOTE]
  > Jeśli usuniesz dane kopii zapasowej możesz usunąć wszystkie punkty odzyskiwania skojarzone. Nie można wybrać punktów odzyskiwania do usunięcia.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [tworzenie kopii zapasowych maszyn wirtualnych platformy Azure z ustawień maszyny Wirtualnej](backup-azure-vms-first-look-arm.md).
- Dowiedz się, jak [przywracanie maszyn wirtualnych](backup-azure-arm-restore-vms.md).
- Dowiedz się, jak [monitorować kopie zapasowe maszyn wirtualnych platformy Azure](backup-azure-monitor-vms.md).
