---
title: Omówienie magazynów usługi Recovery Services
description: Omówienie i porównanie między magazynami usług odzyskiwania a magazynami kopii zapasowych platformy Azure.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: e6a359287533c9ffdd688b5285b24b9c70fa7b7f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436960"
---
# <a name="recovery-services-vaults-overview"></a>Przegląd magazynów usługi Recovery Services

W tym artykule opisano funkcje magazynu usług odzyskiwania. Magazyn usług odzyskiwania jest jednostką magazynu na platformie Azure, która mieści dane. Dane są zazwyczaj kopiami danych lub informacjami o konfiguracji maszyn wirtualnych(VM), obciążeń, serwerów lub stacji roboczych. Magazyny usług odzyskiwania można używać do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takich jak maszyny wirtualne IaaS (Linux lub Windows) i bazy danych SQL platformy Azure. Magazyny usług odzyskiwania obsługują program System Center DPM, Windows Server, Serwer kopii zapasowej platformy Azure i inne. Magazyny usługi Recovery Services ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zmniejszeniu nakładów pracy związanych z zarządzaniem.

W ramach subskrypcji platformy Azure można utworzyć maksymalnie 500 magazynów usług odzyskiwania na subskrypcję na region.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Porównywanie magazynów usług odzyskiwania i magazynów kopii zapasowych

Jeśli nadal masz magazyny kopii zapasowych, są one automatycznie uaktualniane do magazynów usług odzyskiwania. Do listopada 2017 r. wszystkie magazyny kopii zapasowych zostały uaktualnione do magazynów usług odzyskiwania.

Magazyny usług odzyskiwania są oparte na modelu usługi Azure Resource Manager platformy Azure, podczas gdy magazyny kopii zapasowych były oparte na modelu usługi Azure Service Manager. Po uaktualnieniu magazynu kopii zapasowej do magazynu usług odzyskiwania dane kopii zapasowej pozostają nienaruszone podczas procesu uaktualniania i po jego zakończeniu. Magazyny usług odzyskiwania zapewniają funkcje niedostępne dla magazynów kopii zapasowych, takie jak:

- **Rozszerzone funkcje ułatwiające bezpieczne dane kopii zapasowych:** dzięki magazynom usług odzyskiwania usługa Azure Backup zapewnia funkcje zabezpieczeń w celu ochrony kopii zapasowych w chmurze. Funkcje zabezpieczeń zapewniają zabezpieczenie kopii zapasowych i bezpieczne odzyskiwanie danych, nawet jeśli serwery produkcyjne i zapasowe zostaną naruszone. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Centralne monitorowanie hybrydowego środowiska IT:** dzięki magazynom usług odzyskiwania można monitorować nie tylko maszyny [wirtualne usługi Azure IaaS,](backup-azure-manage-vms.md) ale także [zasoby lokalne](backup-azure-manage-windows-server.md#manage-backup-items) z centralnego portalu. [Dowiedz się więcej](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Kontrola dostępu oparta na rolach (RBAC)**: RBAC zapewnia szczegółową kontrolę zarządzania dostępem na platformie Azure. [Platforma Azure udostępnia różne wbudowane role,](../role-based-access-control/built-in-roles.md)a usługa Azure Backup ma trzy [wbudowane role do zarządzania punktami odzyskiwania.](backup-rbac-rs-vault.md) Magazyny usług odzyskiwania są zgodne z funkcją RBAC, która ogranicza dostęp do kopii zapasowych i przywracanie do zdefiniowanego zestawu ról użytkowników. [Dowiedz się więcej](backup-rbac-rs-vault.md)

- **Ochrona wszystkich konfiguracji maszyn wirtualnych platformy Azure:** magazyny usług odzyskiwania chronią maszyny wirtualne oparte na Menedżerze zasobów, w tym dyski premium, dyski zarządzane i zaszyfrowane maszyny wirtualne. Uaktualnienie magazynu kopii zapasowych do magazynu usług odzyskiwania umożliwia uaktualnienie maszyn wirtualnych opartych na programie Service Manager do maszyn wirtualnych opartych na Menedżerze zasobów. Podczas uaktualniania magazynu można zachować punkty odzyskiwania maszyn wirtualnych oparte na usłudze Service Manager i skonfigurować ochronę dla uaktualnionych maszyn wirtualnych (z włączoną obsługą Menedżera zasobów). [Dowiedz się więcej](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Natychmiastowe przywracanie maszyn wirtualnych IaaS:** korzystając z magazynów usług odzyskiwania, można przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny Wirtualnej, co umożliwia szybsze czasy przywracania. Natychmiastowe przywracanie maszyn wirtualnych IaaS jest dostępne zarówno dla maszyn wirtualnych z systemem Windows, jak i z systemem Linux. [Dowiedz się więcej](backup-instant-restore-capability.md)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Ustawienia magazynu w przechowalni usług odzyskiwania

Magazyn usług odzyskiwania to jednostka, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn usług odzyskiwania zawiera również zasady tworzenia kopii zapasowych skojarzone z chronionymi maszynami wirtualnymi.

Usługa Azure Backup automatycznie obsługuje magazyn dla magazynu. Zobacz, jak [można zmienić ustawienia pamięci masowej](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy).

Aby dowiedzieć się więcej na temat nadmiarowości magazynu, zobacz te artykuły dotyczące nadmiarowości [geograficznej](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) i [lokalnej.](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Zarządzanie magazynami usług odzyskiwania w portalu

Tworzenie magazynów usług odzyskiwania i zarządzanie nimi w witrynie Azure portal jest łatwe, ponieważ usługa kopia zapasowa integruje się z innymi usługami platformy Azure. Ta integracja oznacza, że można utworzyć magazyn usług odzyskiwania lub zarządzać nim *w kontekście usługi docelowej.* Na przykład, aby wyświetlić punkty odzyskiwania maszyny Wirtualnej, wybierz maszynę wirtualną i kliknij polecenie **Kopia zapasowa** w menu Operacje.

![Szczegóły magazynu usług odzyskiwania](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Jeśli maszyna wirtualna nie ma skonfigurowany kopii zapasowej, a następnie wyświetli monit o skonfigurowanie kopii zapasowej. Jeśli kopia zapasowa została skonfigurowana, zobaczysz informacje o kopii zapasowej maszyny Wirtualnej, w tym listę punktów przywracania.  

![Szczegóły magazynu usług odzyskiwania](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

W poprzednim przykładzie **ContosoVM** jest nazwą maszyny wirtualnej. **ContosoVM-demovault** to nazwa magazynu usług odzyskiwania. Nie trzeba zapamiętywać nazwy magazynu usług odzyskiwania, który przechowuje punkty odzyskiwania, można uzyskać dostęp do tych informacji z maszyny wirtualnej.  

Jeśli jeden magazyn usług odzyskiwania chroni wiele serwerów, bardziej logiczne może być spojrzenie na magazyn usług odzyskiwania. Można wyszukać wszystkie magazyny usług odzyskiwania w ramach subskrypcji i wybrać jeden z listy.

Poniższe sekcje zawierają łącza do artykułów, które wyjaśniają, jak korzystać z magazynu usług odzyskiwania w każdym typie działania.

> [!NOTE]
> Nie można utworzyć magazynu usług odzyskiwania o tej samej nazwie, jeśli został usunięty w ciągu 24 godzin. Użyj innej nazwy zasobu lub wybierz inną grupę zasobów lub spróbuj ponownie po 24 godzinach.

### <a name="back-up-data"></a>Odzyskiwanie kopii zapasowych danych

- [19 ust.](backup-azure-vms-first-look-arm.md)
- [Wykonanie kopii zapasowej systemu Windows Server lub stacji roboczej systemu Windows](backup-try-azure-backup-in-10-mins.md)
- [19.00.](backup-azure-dpm-introduction.md)
- [Przygotowanie do tworzenia kopii zapasowych obciążeń przy użyciu serwera kopii zapasowych platformy Azure](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Zarządzanie punktami odzyskiwania

- [Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure](backup-azure-manage-vms.md)
- [Zarządzanie plikami i folderami](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Przywracanie danych z przechowalni

- [Odzyskiwanie pojedynczych plików z maszyny Wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md)
- [Przywracanie maszyny Wirtualnej platformy Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Zabezpiecz skarbiec

- [Zabezpieczanie danych kopii zapasowej w chmurze w magazynach usług odzyskiwania](backup-azure-security-feature.md)

## <a name="next-steps"></a>Następne kroki

Użyj następujących artykułów, aby:</br>
[Dzm ów zduchodem iaaS](backup-azure-arm-vms-prepare.md)</br>
[Tworzenie kopii zapasowej serwera kopii zapasowych platformy Azure](backup-azure-microsoft-azure-backup.md)</br>
[1.](backup-windows-with-mars-agent.md)
