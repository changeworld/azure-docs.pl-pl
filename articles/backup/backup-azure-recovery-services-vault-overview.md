---
title: Omówienie magazynów usługi Recovery Services
description: Przegląd i porównanie magazynów usługi Recovery Services i magazyny kopii zapasowych Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/10/2018
ms.author: raynew
ms.openlocfilehash: 924b36701ecf21f6bd84938aeefbf25e47fcbaa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699254"
---
# <a name="recovery-services-vaults-overview"></a>Omówienie magazynów usług odzyskiwania

W tym artykule opisano funkcje magazynu usług Recovery Services. Magazyn usługi Recovery Services jest jednostką magazynu na platformie Azure, która przechowuje dane. Dane są zwykle kopii danych lub informacje o konfiguracji dla maszyn wirtualnych (VM), obciążenia, serwerach lub stacjach roboczych. Magazyny usługi Recovery Services służy do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takie jak maszyny wirtualne IaaS (Linux lub Windows) i bazy danych Azure SQL. System pomocy technicznej Magazyny usług odzyskiwania Centrum programu DPM, Windows Server, usługi Azure Backup Server i więcej. Magazyny usługi Recovery Services ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zmniejszeniu nakładów pracy związanych z zarządzaniem.

W ramach subskrypcji platformy Azure możesz utworzyć maksymalnie 500 magazynów usługi Recovery Services na subskrypcję na region.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Magazyny usług odzyskiwania porównywanie i magazynami kopii zapasowych

Jeśli nadal masz magazynów kopii zapasowych, są one automatycznie uaktualnione do magazynów usługi Recovery Services. Wszystkie magazyny kopii zapasowych przez listopada 2017 r., zostały uaktualnione do magazynów usługi Recovery Services.

Magazyny usługi Recovery Services są oparte na modelu usługi Azure Resource Manager platformy Azure, magazyny usługi Backup zostały oparte na modelu usługi Azure Service Manager. Po uaktualnieniu magazynu usługi Backup do magazynu usługi Recovery Services dane kopii zapasowej pozostaje bez zmian, podczas i po zakończeniu procesu uaktualniania. Magazyny usługi Recovery Services zapewniają funkcje nie są dostępne dla magazynów kopii zapasowych, takie jak:

- **Rozszerzone możliwości, aby ułatwić zabezpieczanie danych kopii zapasowej**: Magazyny usługi Recovery Services usługi Azure Backup zapewnia funkcje zabezpieczeń, aby chronić kopie zapasowe w chmurze. Funkcje zabezpieczeń można zabezpieczyć kopie zapasowe i bezpieczne odzyskiwanie danych, upewnij się nawet wtedy, gdy serwery produkcyjne i kopii zapasowej są uszkodzone. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Centralne monitorowanie hybrydowe środowiska IT**: Magazyny usługi Recovery Services, umożliwia monitorowanie nie tylko usługi [maszyn wirtualnych IaaS platformy Azure](backup-azure-manage-vms.md) , ale także usługi [zasobach lokalnych](backup-azure-manage-windows-server.md#manage-backup-items) centralnej portalu. [Dowiedz się więcej](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Kontrola dostępu oparta na rolach (RBAC)**: RBAC zapewnia szczegółowej kontroli dostępu do zarządzania na platformie Azure. [System Azure oferuje różne role wbudowane](../role-based-access-control/built-in-roles.md), a usługa Azure Backup ma trzy [role wbudowane, umożliwiające zarządzanie punktami odzyskiwania](backup-rbac-rs-vault.md). Magazyny usługi Recovery Services są zgodne z RBAC, który ogranicza możliwość użycia kopia zapasowa i przywrócenia dostępu do określonych ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

- **Chroń wszystkie konfiguracje maszyn wirtualnych platformy Azure**: Magazyny usług odzyskiwania chronią maszyn wirtualnych opartych na usłudze Resource Manager, m.in. dyski Premium Managed Disks i szyfrowanych maszyn wirtualnych. Uaktualnianie magazynu usługi Backup do magazynu usługi Recovery Services daje możliwość uaktualnienia maszyny wirtualne z opartych na programie Service Manager do maszyn wirtualnych opartych na programie Resource Manager. Podczas uaktualniania magazynu, można zachować punkty odzyskiwania maszyny Wirtualnej opartej na programie Service Manager i skonfigurować ochronę uaktualnionych maszyn wirtualnych (korzystających z Menedżera zasobów). [Dowiedz się więcej](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Natychmiastowe przywracanie dla maszyn wirtualnych IaaS**: Przy użyciu magazynów usługi Recovery Services, można przywrócić pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej, która umożliwia skraca czas ich przywracania. Natychmiastowe przywracanie dla maszyn wirtualnych IaaS jest dostępna dla maszyn wirtualnych systemu Linux i Windows. [Dowiedz się więcej](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Magazyny usługi Recovery Services w portalu zarządzania
Tworzenie i Zarządzanie magazynami usługi Recovery Services w witrynie Azure portal jest proste, ponieważ usługa Backup integruje się z innymi usługami platformy Azure. Ta integracja oznacza, że można tworzyć ani nimi zarządzać w magazynie usługi Recovery Services *w kontekście usługi docelowej*. Na przykład, aby wyświetlić punkty odzyskiwania dla maszyny Wirtualnej, wybierz maszynę Wirtualną i kliknij przycisk **kopii zapasowej** w menu operacji.

![Szczegóły magazynu usług odzyskiwania maszyny Wirtualnej](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Jeśli maszyna wirtualna nie ma skonfigurowano kopię zapasową, a następnie zostanie wyświetlony monit, aby skonfigurować tworzenie kopii zapasowej. Jeśli kopia zapasowa została skonfigurowana, zobaczysz kopii zapasowej informacji o maszynie Wirtualnej, w tym listę dostępnych punktów przywracania.  

![Szczegóły magazynu usług odzyskiwania maszyny Wirtualnej](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

W poprzednim przykładzie **ContosoVM** jest nazwa maszyny wirtualnej. **ContosoVM-demovault** jest nazwą magazynu usługi Recovery Services. Nie trzeba pamiętać nazwy magazynu usługi Recovery Services, która przechowuje punkty odzyskiwania, dostęp do tych informacji z maszyny wirtualnej.  

Jeśli jeden magazyn usługi Recovery Services chroni wielu serwerów, może być bardziej logiczne, aby przyjrzeć się w magazynie usługi Recovery Services. Wyszukaj wszystkie magazyny usługi Recovery Services w ramach subskrypcji i wybierz ją z listy.

Poniższe sekcje zawierają linki do artykułów, które wyjaśniają jak używać magazynu usługi Recovery Services w każdym typie działania.

> [!NOTE]
> Nie można utworzyć magazyn usługi Recovery Services o takiej samej nazwie, jeśli został on usunięty w ciągu 24 godzin. Użyj nazwy innego zasobu lub wybierz inną grupę zasobów lub ponów próbę po upływie 24 godzin.

### <a name="back-up-data"></a>Tworzenie kopii zapasowej danych
- [Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure](backup-azure-vms-first-look-arm.md)
- [Tworzenie kopii zapasowych systemu Windows Server lub stacji roboczej Windows](backup-try-azure-backup-in-10-mins.md)
- [Tworzenie kopii zapasowych obciążeń programu DPM na platformie Azure](backup-azure-dpm-introduction.md)
- [Przygotowanie do tworzenia kopii zapasowych obciążeń przy użyciu usługi Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Zarządzanie punktami odzyskiwania
- [Zarządzanie kopiami zapasowymi maszyny Wirtualnej platformy Azure](backup-azure-manage-vms.md)
- [Zarządzanie plikami i folderami](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Przywracanie danych z magazynu
- [Odzyskanie poszczególnych plików na Maszynie wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md)
- [Przywracanie maszyny Wirtualnej platformy Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Zabezpieczenia magazynu
- [Zabezpieczanie danych kopii zapasowej w chmurze w magazynach usługi Recovery Services](backup-azure-security-feature.md)



## <a name="next-steps"></a>Następne kroki
Skorzystaj z następujących artykułów, aby:</br>
[Tworzenie kopii zapasowych maszyn wirtualnych IaaS](backup-azure-arm-vms-prepare.md)</br>
[Tworzenie kopii zapasowych usługi Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Tworzenie kopii zapasowej systemu Windows Server](backup-configure-vault.md)
