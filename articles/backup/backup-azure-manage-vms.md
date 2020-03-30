---
title: Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure i monitorowanie ich
description: Dowiedz się, jak zarządzać kopiami zapasowymi maszyn wirtualnych platformy Azure i monitorować ich przy użyciu usługi Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248153"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup

W tym artykule opisano sposób zarządzania maszynami wirtualnymi platformy Azure, które są archiwizowane przy użyciu [usługi Azure Backup.](backup-overview.md) W tym artykule podsumowano również informacje o kopii zapasowej, które można znaleźć na pulpicie nawigacyjnym magazynu.

W witrynie Azure portal pulpit nawigacyjny magazynu usług odzyskiwania zapewnia dostęp do informacji o przechowalni, w tym:

* Najnowsza kopia zapasowa, która jest również najnowszym punktem przywracania.
* Zasady tworzenia kopii zapasowych.
* Całkowity rozmiar wszystkich migawek kopii zapasowej.
* Liczba maszyn wirtualnych, które są włączone dla kopii zapasowych.

Tworzenie kopii zapasowych można zarządzać za pomocą pulpitu nawigacyjnego i przechodzenia do poszczególnych maszyn wirtualnych. Aby rozpocząć tworzenie kopii zapasowych komputera, otwórz magazyn na pulpicie nawigacyjnym.

![Pełny widok pulpitu nawigacyjnego z suwakiem](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Wyświetlanie maszyn wirtualnych na pulpicie nawigacyjnym

Aby wyświetlić maszyny wirtualne na pulpicie nawigacyjnym przechowalni:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. W menu Centrum wybierz polecenie **Przeglądaj**. Na liście zasobów wpisz **Usługi odzyskiwania**. Podczas pisania lista jest filtrowana na podstawie danych wejściowych. Wybierz **przechowalnie usług odzyskiwania**.

    ![Tworzenie magazynu usługi Recovery Services](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Aby ułatwić obsługę, kliknij prawym przyciskiem myszy przechowalnię i wybierz **pozycję Przypnij do pulpitu nawigacyjnego**.
4. Otwórz pulpit nawigacyjny przechowalni.

    ![Otwieranie pulpitu nawigacyjnego przechowalni i okienka Ustawienia](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Na kafelku **Elementy kopii zapasowej** wybierz pozycję Maszyny **wirtualne platformy Azure**.

    ![Otwieranie kafelka Zapasy kopii zapasowej](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. W okienku **Elementy kopii zapasowej** można wyświetlić listę chronionych maszyn wirtualnych. W tym przykładzie przechowalnia chroni jedną maszynę wirtualną: demobackup.  

    ![Wyświetlanie okienka Zapasy kopii zapasowej](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Na pulpicie nawigacyjnym elementu magazynu modyfikuj zasady tworzenia kopii zapasowych, uruchamiaj kopię zapasową na żądanie, zatrzymuj lub wznawiaj ochronę maszyn wirtualnych, usuwaj dane kopii zapasowej, wyświetlaj punkty przywracania i uruchamiaj przywracanie.

    ![Pulpit nawigacyjny Zapasów kopii zapasowych i okienko Ustawienia](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Zarządzanie zasadami tworzenia kopii zapasowych dla maszyny Wirtualnej

Aby zarządzać zasadami tworzenia kopii zapasowych:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/). Otwórz pulpit nawigacyjny przechowalni.
2. Na kafelku **Elementy kopii zapasowej** wybierz pozycję Maszyny **wirtualne platformy Azure**.

    ![Otwieranie kafelka Zapasy kopii zapasowej](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. W okienku **Elementy kopii zapasowej** można wyświetlić listę chronionych maszyn wirtualnych i stan ostatniej kopii zapasowej z czasem najnowszych punktów przywracania.

    ![Wyświetlanie okienka Zapasy kopii zapasowej](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Na pulpicie nawigacyjnym elementu przechowalni można wybrać zasady tworzenia kopii zapasowych.

   * Aby przełączyć zasady, wybierz inną zasadę, a następnie wybierz pozycję **Zapisz**. Nowe zasady zostaną natychmiast zastosowane do magazynu.

     ![Wybieranie zasad kopii zapasowych](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

Po skonfigurowaniu jej ochrony można uruchomić kopię zapasową maszyny wirtualnej na żądanie. Należy pamiętać o tych szczegółach:

* Jeśli początkowa kopia zapasowa jest oczekująca, kopia zapasowa na żądanie tworzy pełną kopię maszyny Wirtualnej w magazynie usług odzyskiwania.
* Jeśli początkowa kopia zapasowa zostanie ukończona, kopia zapasowa na żądanie będzie wysyłać zmiany z poprzedniej migawki tylko do magazynu usług odzyskiwania. Oznacza to, że późniejsze kopie zapasowe są zawsze przyrostowe.
* Zakres przechowywania kopii zapasowej na żądanie jest wartością przechowywania określoną podczas wyzwalania kopii zapasowej.

Aby wyzwolić kopię zapasową na żądanie:

1. Na [pulpicie nawigacyjnym elementu przechowalni](#view-vms-on-the-dashboard)w obszarze **Element chroniony**wybierz pozycję **Zapas zapasowy**.

    ![Opcja Kopia zapasowa teraz](./media/backup-azure-manage-vms/backup-now-button.png)

2. Z **typu zarządzania kopiami zapasowymi**wybierz pozycję **Maszyna wirtualna platformy Azure**. Zostanie wyświetlene okienko **Element kopii zapasowej (Maszyna wirtualna platformy Azure).**
3. Wybierz maszynę wirtualną i wybierz **pozycję Utwórz teraz kopię zapasową,** aby utworzyć kopię zapasową na żądanie. Pojawi się okienko **Kopia zapasowa teraz.**
4. W polu **Zachowaj do czasu tworzenia kopii zapasowej** określ datę zachowania kopii zapasowej.

    ![Kalendarz Kopia zapasowa teraz](./media/backup-azure-manage-vms/backup-now-check.png)

5. Wybierz **przycisk OK,** aby uruchomić zadanie tworzenia kopii zapasowej.

Aby śledzić postęp zadania, na pulpicie nawigacyjnym przechowalni wybierz kafelek **Zadania kopii zapasowej.**

## <a name="stop-protecting-a-vm"></a>Zatrzymywać ochronę maszyny wirtualnej

Istnieją dwa sposoby, aby zatrzymać ochronę maszyny Wirtualnej:

* **Zatrzymaj ochronę i zachowaj dane kopii zapasowej**. Ta opcja uniemożliwi ochronę wszystkich przyszłych zadań tworzenia kopii zapasowych maszyny Wirtualnej; jednak usługa Azure Backup zachowa punkty odzyskiwania, które zostały utworzone.  Musisz zapłacić, aby zachować punkty odzyskiwania w magazynie (zobacz [cennik usługi Azure Backup,](https://azure.microsoft.com/pricing/details/backup/) aby uzyskać szczegółowe informacje). W razie potrzeby będzie można przywrócić maszynę wirtualną. Jeśli zdecydujesz się wznowić ochronę maszyny Wirtualnej, możesz użyć opcji *Wznów kopię zapasową.*
* **Zatrzymaj ochronę i usuń dane kopii zapasowej**. Ta opcja uniemożliwi ochronę wszystkich przyszłych zadań tworzenia kopii zapasowych i usunie wszystkie punkty odzyskiwania. Nie będzie można przywrócić maszyny Wirtualnej ani użyć opcji *wznawiania kopii zapasowej.*

>[!NOTE]
>Jeśli usuniesz źródło danych bez zatrzymywania kopii zapasowych, nowe kopie zapasowe nie powiodą się. Stare punkty odzyskiwania wygasną zgodnie z zasadami, ale jeden ostatni punkt odzyskiwania będzie zawsze przechowywany, dopóki nie zatrzymasz kopii zapasowych i nie usuniesz danych.
>

### <a name="stop-protection-and-retain-backup-data"></a>Zatrzymaj ochronę i zachowaj dane kopii zapasowej

Aby zatrzymać ochronę i zachować dane maszyny Wirtualnej:

1. Na [pulpicie nawigacyjnym elementu przechowalni](#view-vms-on-the-dashboard)wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.
2. Wybierz **pozycję Zachowaj dane kopii zapasowej**i potwierdź wybór zgodnie z potrzebami. Jeśli chcesz, dodaj komentarz. Jeśli nie masz pewności co do nazwy elementu, umieść wskaźnik myszy na wykrzykniku, aby wyświetlić jego nazwę.

    ![Zachowaj dane kopii zapasowej](./media/backup-azure-manage-vms/retain-backup-data.png)

Powiadomienie informuje, że zadania tworzenia kopii zapasowej zostały zatrzymane.

### <a name="stop-protection-and-delete-backup-data"></a>Zatrzymaj ochronę i usuń dane kopii zapasowej

Aby zatrzymać ochronę i usunąć dane maszyny Wirtualnej:

1. Na [pulpicie nawigacyjnym elementu przechowalni](#view-vms-on-the-dashboard)wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.
2. Wybierz **pozycję Usuń dane kopii zapasowej**i potwierdź wybór zgodnie z potrzebami. Wprowadź nazwę elementu kopii zapasowej i dodaj komentarz, jeśli chcesz.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Wznawianie ochrony maszyny Wirtualnej

Jeśli podczas zatrzymania ochrony maszyny [Wirtualnej wybrano opcję Zatrzymaj ochronę i zachowaj dane kopii zapasowej,](#stop-protection-and-retain-backup-data) możesz użyć **funkcji Wznówania kopii zapasowej**. Ta opcja nie jest dostępna, jeśli wybierzesz opcję [Zatrzymaj ochronę i usuń dane kopii zapasowej](#stop-protection-and-delete-backup-data) lub Usuń dane kopii [zapasowej](#delete-backup-data).

Aby wznowić ochronę maszyny Wirtualnej:

1. Na [pulpicie nawigacyjnym elementu przechowalni](#view-vms-on-the-dashboard)wybierz pozycję **Wznów kopię zapasową**.

2. Wykonaj kroki opisane w [obszarze Zarządzanie zasadami tworzenia kopii zapasowych,](#manage-backup-policy-for-a-vm) aby przypisać zasady dla maszyny Wirtualnej. Nie musisz wybierać początkowych zasad ochrony maszyny Wirtualnej.
3. Po zastosowaniu zasad tworzenia kopii zapasowych do maszyny Wirtualnej zostanie wyświetlony następujący komunikat:

    ![Komunikat informujący o pomyślnie chronionej maszynie wirtualnej](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Istnieją dwa sposoby usuwania danych kopii zapasowej maszyny Wirtualnej:

* Na pulpicie nawigacyjnym elementu przechowalni wybierz pozycję Zatrzymaj tworzenie kopii zapasowych i postępuj zgodnie z instrukcjami [dotyczącymi opcji Zatrzymaj ochronę i usuń dane kopii zapasowej.](#stop-protection-and-delete-backup-data)

  ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* Na pulpicie nawigacyjnym elementu przechowalni wybierz pozycję Usuń dane kopii zapasowej. Ta opcja jest włączona, jeśli wybrano opcję [Zatrzymaj ochronę i zachowaj dane kopii zapasowej](#stop-protection-and-retain-backup-data) podczas zatrzymania ochrony maszyny Wirtualnej

  ![Wybierz pozycję Usuń kopię zapasową](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * Na [pulpicie nawigacyjnym elementu przechowalni](#view-vms-on-the-dashboard)wybierz pozycję **Usuń dane kopii zapasowej**.
  * Wpisz nazwę elementu kopii zapasowej, aby potwierdzić, że chcesz usunąć punkty odzyskiwania.

    ![Usuwanie danych kopii zapasowej](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Aby usunąć dane kopii zapasowej elementu, wybierz pozycję **Usuń**. Komunikat z powiadomieniem informuje, że dane kopii zapasowej zostały usunięte.

Aby chronić dane, usługa Azure Backup zawiera funkcję usuwania nietrwałego. Po usunięciu nietrwałego, nawet po usunięciu kopii zapasowej (wszystkie punkty odzyskiwania) maszyny wirtualnej, dane kopii zapasowej są przechowywane przez 14 dodatkowych dni. Aby uzyskać więcej informacji, zobacz [dokumentację usuwania nietrwałego](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

  > [!NOTE]
  > Po usunięciu danych kopii zapasowej usuwasz wszystkie skojarzone punkty odzyskiwania. Nie można wybrać określonych punktów odzyskiwania do usunięcia.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Element kopii zapasowej, w którym podstawowe źródło danych już nie istnieje

* Jeśli maszyny wirtualne platformy Azure skonfigurowane do tworzenia kopii zapasowych platformy Azure są usuwane lub przenoszone bez zatrzymywania ochrony, zarówno zaplanowane zadania tworzenia kopii zapasowej, jak i zadania tworzenia kopii zapasowych na żądanie (ad hoc) nie powiodą się z powodu błędu UserErrorVmNotFoundV2. Sprawdzanie wstępne kopii zapasowej będzie wyświetlane jako krytyczne tylko dla nieudanych zadań tworzenia kopii zapasowych na żądanie (zaplanowane zadania nie powiodły się).
* Te elementy kopii zapasowej pozostają aktywne w systemie, stosując się do zasad tworzenia kopii zapasowych i przechowywania ustawionych przez użytkownika. Kopie zapasowe danych dla tych maszyn wirtualnych platformy Azure zostaną zachowane zgodnie z zasadami przechowywania. Wygasłe punkty odzyskiwania (z wyjątkiem ostatniego punktu odzyskiwania) są czyszczone zgodnie z zakresem przechowywania ustawionym w zasadach tworzenia kopii zapasowych.
* Użytkownikom zaleca się usunięcie elementów kopii zapasowej, w których podstawowe źródło danych już nie istnieje, aby uniknąć dodatkowych kosztów, jeśli element kopii zapasowej/dane dla zasobów usuwania nie są już wymagane, ponieważ ostatni punkt odzyskiwania jest zachowywany na zawsze, a użytkownik jest obciążany zgodnie z odpowiednią ceną kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [wyw.](backup-azure-vms-first-look-arm.md)
* Dowiedz się, jak [przywrócić maszyny wirtualne](backup-azure-arm-restore-vms.md).
* Dowiedz się, jak [monitorować kopie zapasowe maszyn wirtualnych platformy Azure.](backup-azure-monitor-vms.md)
