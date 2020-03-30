---
title: Włączanie tworzenia kopii zapasowej przy tworzeniu maszyny wirtualnej platformy Azure
description: W tym artykule opisano, jak włączyć tworzenie kopii zapasowej podczas tworzenia maszyny Wirtualnej platformy Azure za pomocą usługi Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 0cfea6579791c4fd23c1b7acdfe722d57b5ec2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247854"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Włączanie tworzenia kopii zapasowej przy tworzeniu maszyny wirtualnej platformy Azure

Użyj usługi Azure Backup do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure (maszyn wirtualnych). Kopie zapasowe maszyn wirtualnych są archiwizowane zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowych, a punkty odzyskiwania są tworzone na podstawie kopii zapasowych. Punkty odzyskiwania są przechowywane w magazynach usług odzyskiwania.

W tym artykule opisano, jak włączyć tworzenie kopii zapasowej podczas tworzenia maszyny wirtualnej (VM) w witrynie Azure portal.  

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Sprawdź,](backup-support-matrix-iaas.md#supported-backup-actions) które systemy operacyjne są obsługiwane, jeśli kopia zapasowa zostanie włączona podczas tworzenia maszyny Wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Jeśli nie zalogowano się jeszcze na swoje konto, zaloguj się do [witryny Azure portal](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Tworzenie maszyny wirtualnej ze skonfigurowaną pomocą kopii zapasowej

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób**.

2. W portalu Azure Marketplace kliknij pozycję **Oblicz**, a następnie wybierz obraz maszyny Wirtualnej.

3. Skonfiguruj maszynę wirtualną zgodnie z instrukcjami [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) lub [Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)

4. Na karcie **Zarządzanie** w obszarze **Włączanie tworzenia kopii zapasowych**kliknij pozycję **Włącz**.
5. Kopie zapasowe usługi Azure Backup do magazynu usług odzyskiwania. Kliknij **przycisk Utwórz nowy,** jeśli nie masz istniejącego przechowalni.
6. Zaakceptuj sugerowaną nazwę przechowalni lub określ własną.
7. Określ lub utwórz grupę zasobów, w której będzie znajdować się przechowalnia. Magazyn grupy zasobów może się różnić od grupy zasobów maszyny Wirtualnej.

    ![Włączanie tworzenia kopii zapasowych dla maszyny Wirtualnej](./media/backup-during-vm-creation/enable-backup.png)

8. Zaakceptuj domyślną zasadę tworzenia kopii zapasowych lub zmodyfikuj ustawienia.
    - Zasady tworzenia kopii zapasowych określają, jak często należy robić migawki kopii zapasowej maszyny Wirtualnej i jak długo należy przechowywać te kopie zapasowe.
    - Domyślna zasada kopii zapasowej maszyny Wirtualnej raz dziennie.
    - Możesz dostosować własne zasady tworzenia kopii zapasowych dla maszyny Wirtualnej platformy Azure, aby wykonać kopie zapasowe codziennie lub co tydzień.
    - [Dowiedz się więcej](backup-azure-vms-introduction.md#backup-and-restore-considerations) o zagadnieniach dotyczących tworzenia kopii zapasowych dla maszyn wirtualnych platformy Azure.
    - [Dowiedz się więcej](backup-instant-restore-capability.md) o funkcji natychmiastowego przywracania.

      ![Domyślne zasady tworzenia kopii zapasowych](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Grupa zasobów usługi Azure Backup dla maszyn wirtualnych

Usługa kopia zapasowa tworzy oddzielną grupę zasobów (RG), inną niż grupa zasobów maszyny Wirtualnej do przechowywania kolekcji punktów przywracania (RPC). RPC zawiera punkty natychmiastowego odzyskiwania zarządzanych maszyn wirtualnych. Domyślnym formatem nazewnictwa grupy zasobów utworzonej `AzureBackupRG_<Geo>_<number>`przez usługę Kopia zapasowa jest: . Na przykład: *AzureBackupRG_northeurope_1*. Teraz można dostosować nazwę grupy zasobów utworzone przez usługę Azure Backup.

Uwagi do uwag:

1. Można użyć domyślnej nazwy RG lub edytować ją zgodnie z wymaganiami firmy.
2. Wzorzec nazw RG należy podać jako dane wejściowe podczas tworzenia zasad tworzenia kopii zapasowych maszyny Wirtualnej. Nazwa RG powinna mieć następujący `<alpha-numeric string>* n <alpha-numeric string>`format: . 'n' zastępuje się całkowitej liczby (począwszy od 1) i jest używany do skalowania w poziomie, jeśli pierwszy RG jest pełny. Jeden RG może mieć max 600 RPC dzisiaj.
              ![Wybieranie nazwy podczas tworzenia zasad](./media/backup-during-vm-creation/create-policy.png)
3. Wzorzec powinien być zgodny z poniższymi regułami nazewnictwa RG, a całkowita długość nie powinna przekraczać maksymalnej dozwolonej długości nazwy RG.
    1. Nazwy grup zasobów zezwalają tylko na znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Nie mogą one zakończyć się w okresie.
    2. Nazwy grup zasobów mogą zawierać maksymalnie 74 znaki, w tym nazwę RG i sufiks.
4. Pierwszy `<alpha-numeric-string>` jest obowiązkowy, a drugi po 'n' jest opcjonalny. Ma to zastosowanie tylko wtedy, gdy podasz niestandardową nazwę. Jeśli nie wprowadzisz niczego w żadnym z pól tekstowych, zostanie użyta nazwa domyślna.
5. Nazwę RG można edytować, modyfikując zasady, jeśli i kiedy jest to wymagane. Jeśli wzorzec nazwy zostanie zmieniony, nowe serwery RP zostaną utworzone w nowym RG. Jednak stare punkty RR nadal będą znajdować się w starym RG i nie zostaną przeniesione, ponieważ kolekcja RP nie obsługuje przenoszenia zasobów. Ostatecznie dostawcy usług internetowych otrzymają śmieci zbierane w miarę wygasania punktów.
![Zmienianie nazwy podczas modyfikowania zasad](./media/backup-during-vm-creation/modify-policy.png)
6. Zaleca się, aby nie blokować grupy zasobów utworzonej do użytku przez usługę Kopia zapasowa.

## <a name="start-a-backup-after-creating-the-vm"></a>Rozpoczynanie tworzenia kopii zapasowej po utworzeniu maszyny Wirtualnej

Kopia zapasowa maszyny Wirtualnej będzie działać zgodnie z zasadami tworzenia kopii zapasowych. Zaleca się jednak uruchomienie początkowej kopii zapasowej.

Po utworzeniu maszyny Wirtualnej wykonaj następujące czynności:

1. We właściwościach maszyny Wirtualnej kliknij pozycję **Kopia zapasowa**. Stan maszyny Wirtualnej to Oczekiwanie na początkową kopię zapasową do momentu uruchomieniu początkowej kopii zapasowej
2. Kliknij **przycisk Utwórz kopię zapasową teraz,** aby uruchomić kopię zapasową na żądanie.

    ![Uruchamianie kopii zapasowej na żądanie](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Wdrażanie chronionej maszyny wirtualnej za pomocą szablonu Menedżera zasobów

W poprzednich krokach wyjaśniono, jak używać witryny Azure portal do tworzenia maszyny wirtualnej i ochrony jej w magazynie usług odzyskiwania. Aby szybko wdrożyć jedną lub więcej maszyn wirtualnych i chronić je w przechowalni usług odzyskiwania, zobacz szablon [Wdrażanie maszyny Wirtualnej systemu Windows i włączanie kopii zapasowej](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz chronione maszyny Wirtualnej, dowiedz się, jak zarządzać i przywrócić je.

- [Monitorowanie maszyn wirtualnych i zarządzanie nimi](backup-azure-manage-vms.md)
- [Przywracanie maszyny wirtualnej](backup-azure-arm-restore-vms.md)

Jeśli wystąpią jakiekolwiek problemy, [zapoznaj](backup-azure-vms-troubleshoot.md) się z przewodnikiem rozwiązywania problemów.
