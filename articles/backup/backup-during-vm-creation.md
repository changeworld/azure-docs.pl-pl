---
title: Włącz wykonywanie kopii zapasowej po utworzeniu maszyny Wirtualnej platformy Azure za pomocą usługi Azure Backup
description: W tym artykule opisano sposób włączania kopii zapasowej po utworzeniu maszyny Wirtualnej platformy Azure za pomocą usługi Azure Backup.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: raynew
ms.openlocfilehash: a19653f7ae3900fd7999f347ef4d3ef710be1430
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436337"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Włączanie tworzenia kopii zapasowej przy tworzeniu maszyny wirtualnej platformy Azure

Tworzenie kopii zapasowych maszyn wirtualnych (VM) przy użyciu usługi Azure Backup. Maszyny wirtualne kopie zapasowe są tworzone zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowych i punktów odzyskiwania są tworzone na podstawie kopii zapasowych. Punkty odzyskiwania są przechowywane w magazynach usługi Recovery Services.

Ten artykuł szczegółowo opisuje sposób włączania kopii zapasowej po utworzeniu maszyny wirtualnej (VM) w witrynie Azure portal.  

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Sprawdź](backup-support-matrix-iaas.md#supported-backup-actions) które systemy operacyjne są obsługiwane w przypadku włączenia kopii zapasowej po utworzeniu maszyny Wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Jeśli nie są już zalogowano do swojego konta, zaloguj się do [witryny Azure portal](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Tworzenie maszyny Wirtualnej przy użyciu kopii zapasowej skonfigurowane

1. W witrynie Azure portal kliknij **Utwórz zasób**.

2. W portalu Azure Marketplace kliknij **obliczenia**, a następnie wybierz obraz maszyny Wirtualnej.

3. Konfigurowanie maszyny Wirtualnej zgodnie z [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) instrukcje.

4. Na **zarządzania** na karcie **Włącz kopię zapasową**, kliknij przycisk **na**.
5. Azure kopii zapasowej kopii zapasowych w magazynie usługi Recovery Services. Kliknij przycisk **Utwórz nowy** Jeśli nie masz istniejącego magazynu.
6. Zaakceptuj nazwę sugerowaną magazynu lub określić własny.
7. Określ lub Utwórz grupę zasobów, w którym zostaną umieszczone magazynu. Vault grupy zasobów może być inna niż grupa zasobów maszyny Wirtualnej.

    ![Włącz wykonywanie kopii zapasowej dla maszyny Wirtualnej](./media/backup-during-vm-creation/enable-backup.png)

8. Zaakceptuj domyślne zasady kopii zapasowych lub zmodyfikuj ustawienia.
    - Zasady tworzenia kopii zapasowych określa, jak często migawek kopii zapasowych maszyn wirtualnych i jak długo przechowywać te kopie zapasowe.
    - Domyślna zasada wykonuje kopię zapasową maszyny Wirtualnej raz dziennie.
    - Można dostosować zasady tworzenia kopii zapasowej maszyny wirtualnej platformy Azure wykonać kopie zapasowe codziennie lub co tydzień.
    - [Dowiedz się więcej](backup-azure-vms-introduction.md#backup-and-restore-considerations) dotyczących tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.
    - [Dowiedz się więcej](backup-instant-restore-capability.md) o chwili przywrócenia jej funkcjonalności.

      ![Domyślne zasady kopii zapasowych](./media/backup-during-vm-creation/daily-policy.png)


> [!NOTE]
> Usługa Azure Backup tworzy oddzielnej grupy zasobów (innej niż grupa zasobów maszyny Wirtualnej) do przechowywania migawek z formatem nazewnictwa **AzureBackupRG_geography_number** (przykład: AzureBackupRG_northeurope_1). Dane w tej grupie zasobów, które będą przechowywane przez czas trwania w dniach, jak to określono w *Zachowaj natychmiastowe odzyskiwanie migawki* części zasad kopii zapasowej maszyny wirtualnej platformy Azure.  Stosowanie blokady do tej grupy zasobów może powodować błędy kopii zapasowych.<br>
Ta grupa zasobów również powinny być wykluczone z jakieś ograniczenia dotyczące nazw/tag, zgodnie z zasadami ograniczeń mogłyby spowodować zablokowanie tworzenia kolekcji punktów zasobów w niej ponownie, powodując błędy kopii zapasowych.


## <a name="start-a-backup-after-creating-the-vm"></a>Rozpocznij tworzenie kopii zapasowej po utworzeniu maszyny Wirtualnej

Kopia zapasowa maszyny Wirtualnej zostanie uruchomione zgodnie z zasadami tworzenia kopii zapasowej. Jednak zaleca się, że uruchomieniu początkowej kopii zapasowej.

Po utworzeniu maszyny Wirtualnej, wykonaj następujące czynności:

1. We właściwościach maszyny Wirtualnej, kliknij przycisk **kopii zapasowej**. Stan maszyny Wirtualnej oczekuje początkowej kopii zapasowej do momentu uruchamia tworzenie początkowej kopii zapasowej
2. Kliknij przycisk **wykonaj kopię zapasową teraz** do uruchamiania kopii zapasowej na żądanie.

    ![Uruchomienie w kopii zapasowej na żądanie](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Wdrażanie chronionej maszyny Wirtualnej przy użyciu szablonu usługi Resource Manager

Poprzednie kroki wyjaśniają jak używać witryny Azure portal do utworzenia maszyny wirtualnej i chronić go w magazynie usługi Recovery Services. Aby szybko wdrożyć co najmniej jedną maszynę wirtualną i chronić je w magazynie usługi Recovery Services, zobacz szablonu [wdrażanie maszyny Wirtualnej z systemem Windows i włącz wykonywanie kopii zapasowej](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>Kolejne kroki

Skoro już chronione maszyny Wirtualnej, informacje o sposobie zarządzania i ich przywracania.

- [Monitorowanie maszyn wirtualnych i zarządzanie nimi](backup-azure-manage-vms.md)
- [Przywracanie maszyny wirtualnej](backup-azure-arm-restore-vms.md)

Jeśli napotkasz jakiekolwiek problemy [Przejrzyj](backup-azure-vms-troubleshoot.md) przewodnik rozwiązywania problemów.
