---
title: Włączanie tworzenia kopii zapasowej przy tworzeniu maszyny wirtualnej platformy Azure
description: Opisuje sposób włączania tworzenia kopii zapasowej podczas tworzenia maszyny wirtualnej platformy Azure przy użyciu Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f34c5dd8cfdc94775b9bd9a896b4cfbe4154ecf8
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172359"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Włączanie tworzenia kopii zapasowej przy tworzeniu maszyny wirtualnej platformy Azure

Użyj usługi Azure Backup, aby utworzyć kopię zapasową maszyn wirtualnych platformy Azure. Kopie zapasowe maszyn wirtualnych są wykonywane zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowych, a punkty odzyskiwania są tworzone na podstawie kopii zapasowych. Punkty odzyskiwania są przechowywane w magazynach Recovery Services.

W tym artykule opisano sposób włączania tworzenia kopii zapasowej podczas tworzenia maszyny wirtualnej w Azure Portal.  

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Sprawdź](backup-support-matrix-iaas.md#supported-backup-actions) , które systemy operacyjne są obsługiwane w przypadku włączenia tworzenia kopii zapasowej podczas tworzenia maszyny wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Jeśli jeszcze nie zalogowano się na koncie, zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Tworzenie maszyny wirtualnej z skonfigurowaną kopią zapasową

1. W Azure Portal kliknij pozycję **Utwórz zasób**.

2. W portalu Azure Marketplace kliknij pozycję **obliczenia**, a następnie wybierz obraz maszyny wirtualnej.

3. Skonfiguruj maszynę wirtualną zgodnie z instrukcjami dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) .

4. Na karcie **Zarządzanie** w obszarze **Włączanie kopii zapasowej**kliknij **pozycję włączone.**
5. Azure Backup kopie zapasowe w magazynie Recovery Services. Kliknij przycisk **Utwórz nowy** , jeśli nie masz istniejącego magazynu.
6. Zaakceptuj sugerowaną nazwę magazynu lub określ swoje własne.
7. Określ lub Utwórz grupę zasobów, w której będzie znajdować się magazyn. Magazyn grupy zasobów może się różnić od grupy zasobów maszyny wirtualnej.

    ![Włączanie kopii zapasowej maszyny wirtualnej](./media/backup-during-vm-creation/enable-backup.png)

8. Zaakceptuj domyślne zasady tworzenia kopii zapasowych lub zmodyfikuj ustawienia.
    - Zasady tworzenia kopii zapasowych określają, jak często mają być wykonywane migawki kopii zapasowej maszyny wirtualnej oraz jak długo mają być przechowywane kopie zapasowe.
    - Zasady domyślne tworzą kopię zapasową maszyny wirtualnej raz dziennie.
    - Możesz dostosować własne zasady tworzenia kopii zapasowej dla maszyny wirtualnej platformy Azure, aby tworzyć kopie zapasowe codziennie lub co tydzień.
    - [Dowiedz się więcej](backup-azure-vms-introduction.md#backup-and-restore-considerations) o zagadnieniach dotyczących kopii zapasowych dla maszyn wirtualnych platformy Azure
    - [Dowiedz się więcej](backup-instant-restore-capability.md) o funkcji natychmiastowego przywracania.

      ![Domyślne zasady kopii zapasowych](./media/backup-during-vm-creation/daily-policy.png)

> [!NOTE]
> Usługa Azure Backup tworzy oddzielną grupę zasobów (inną niż grupa zasobów maszyny wirtualnej) do przechowywania migawki przy użyciu formatu nazewnictwa **AzureBackupRG_geography_number** (przykład: AzureBackupRG_northeurope_1). Dane w tej grupie zasobów będą przechowywane przez czas trwania w dniach, jak określono w sekcji *zachowywanie migawki odzyskiwania natychmiastowego* zasad tworzenia kopii zapasowej maszyny wirtualnej platformy Azure.  Zastosowanie blokady do tej grupy zasobów może spowodować błędy kopii zapasowych. <br> Ta grupa zasobów powinna być również wykluczona z dowolnych ograniczeń nazw/tagów, ponieważ zasady ograniczeń blokują tworzenie kolekcji punktów zasobów w tym momencie, powodując błędy kopii zapasowych.

## <a name="start-a-backup-after-creating-the-vm"></a>Rozpocznij tworzenie kopii zapasowej po utworzeniu maszyny wirtualnej

Kopia zapasowa maszyny wirtualnej będzie uruchamiana zgodnie z zasadami tworzenia kopii zapasowych. Zaleca się jednak uruchomienie początkowej kopii zapasowej.

Po utworzeniu maszyny wirtualnej wykonaj następujące czynności:

1. We właściwościach maszyny wirtualnej kliknij pozycję **kopia zapasowa**. Stan maszyny wirtualnej to początkowa kopia zapasowa oczekująca na uruchomienie początkowej kopii zapasowej
2. Kliknij pozycję **Wykonaj kopię zapasową teraz** , aby uruchomić kopię zapasową na żądanie.

    ![Uruchamianie kopii zapasowej na żądanie](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Wdrażanie chronionej maszyny wirtualnej przy użyciu szablonu Menedżer zasobów

W poprzednich krokach opisano, jak za pomocą Azure Portal utworzyć maszynę wirtualną i chronić ją w magazynie Recovery Services. Aby szybko wdrożyć co najmniej jedną maszynę wirtualną i chronić ją w magazynie Recovery Services, zapoznaj się z szablonem [Wdrażanie maszyny wirtualnej z systemem Windows i Włączanie kopii zapasowej](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz chronioną maszynę wirtualną, Dowiedz się, jak zarządzać nimi i je przywracać.

- [Zarządzanie maszynami wirtualnymi i monitorowanie ich](backup-azure-manage-vms.md)
- [Przywracanie maszyny wirtualnej](backup-azure-arm-restore-vms.md)

Jeśli napotkasz jakiekolwiek problemy, [zapoznaj](backup-azure-vms-troubleshoot.md) się z przewodnikiem rozwiązywania problemów.
