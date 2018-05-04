---
title: Omówienie usług odzyskiwania magazyny | Dokumentacja firmy Microsoft
description: Zawiera omówienie i porównanie Magazyny usług odzyskiwania i magazyny kopii zapasowych platformy Azure.
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.assetid: 38d4078b-ebc8-41ff-9bc8-47acf256dc80
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/15/2017
ms.author: markgal;arunak;sogup
ms.openlocfilehash: 12ecc7ab33dba2f4f9842a4fbd6491400024a4e5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="recovery-services-vaults-overview"></a>Omówienie Magazyny usług odzyskiwania

W tym artykule opisano funkcje magazynu usług odzyskiwania. Magazyn usług odzyskiwania jest jednostką magazynu na platformie Azure, która przechowuje dane. Dane są zwykle kopii danych lub informacje o konfiguracji dla maszyn wirtualnych (VM), obciążenia, serwerach lub stacjach roboczych. Magazyny usług odzyskiwania służy do przechowywania danych kopii zapasowej dla różnych usług Azure, takich jak maszyny wirtualne IaaS (Linux lub Windows) oraz baz danych Azure SQL. System obsługi magazynów usług odzyskiwania Centrum programu DPM, Windows Server, serwer kopii zapasowej Azure i więcej. Magazyny usługi Recovery Services ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zmniejszeniu nakładów pracy związanych z zarządzaniem. 

W ramach subskrypcji platformy Azure możesz utworzyć maksymalnie 500 Magazyny usług odzyskiwania na subskrypcję dla regionu.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Magazyny usług odzyskiwania porównaniem i magazynami kopii zapasowych

Jeśli nadal masz magazyny kopii zapasowych, są one automatycznie zaktualizowane do magazynów usług odzyskiwania. Przez 2017 listopada wszystkie magazyny kopii zapasowych zostały uaktualnione do magazynów usług odzyskiwania. 

Magazyny usług odzyskiwania są oparte na modelu usługi Azure Resource Manager platformy Azure, magazyny kopii zapasowych są oparte na modelu Azure Service Manager. Po uaktualnieniu magazynu kopii zapasowych do magazynu usług odzyskiwania dane kopii zapasowej pozostanie niezmieniona podczas i po zakończeniu procesu uaktualniania. Magazyny usług odzyskiwania zapewniają funkcje nie są dostępne dla magazyny kopii zapasowych, takich jak:

- **Ulepszonych funkcji ułatwia zabezpieczanie danych kopii zapasowej**: magazynów z usług odzyskiwania, kopia zapasowa Azure udostępnia funkcje zabezpieczeń w celu ochrony kopii zapasowych w chmurze. Funkcje zabezpieczeń upewnij się secure kopii zapasowych i bezpieczne odzyskiwanie danych, nawet jeśli serwerów produkcyjnych i tworzenia kopii zapasowej są uszkodzone. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Centralne monitorowanie sieci hybrydowe środowiska IT**: magazynów z usług odzyskiwania, można monitorować nie tylko z [maszyn wirtualnych IaaS platformy Azure](backup-azure-manage-vms.md) , ale także z [zasoby lokalne](backup-azure-manage-windows-server.md#manage-backup-items) z centralnej portalu. [Dowiedz się więcej](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Kontrola dostępu oparta na rolach (RBAC)**: RBAC zapewnia szczegółowej zarządzania kontroli dostępu na platformie Azure. [Platforma Azure oferuje różne role wbudowane](../role-based-access-control/built-in-roles.md), a kopia zapasowa Azure ma trzy [wbudowane role, aby zarządzać punktami odzyskiwania](backup-rbac-rs-vault.md). Magazyny usług odzyskiwania są zgodne z RBAC, który ogranicza możliwość użycia kopii zapasowej i przywracania dostępu do zdefiniowanego zestawu ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

- **Chroń wszystkie konfiguracje maszyn wirtualnych Azure**: Magazyny usług odzyskiwania chronić maszyny wirtualne oparte na Menedżera zasobów, w tym dyski Premium, dysków zarządzanych i szyfrowane maszyn wirtualnych. Uaktualnianie magazynu kopii zapasowych do magazynu usług odzyskiwania daje możliwość uaktualnienia programu Service Manager na podstawie maszyn wirtualnych do maszyn wirtualnych w Menedżerze zasobów. Podczas uaktualniania magazynu, można zachować punktów odzyskiwania maszyny Wirtualnej opartej na programu Service Manager i skonfiguruj ochronę uaktualnionego maszyn wirtualnych (włączone Resource Manager). [Dowiedz się więcej](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Natychmiastowego przywracania dla maszyn wirtualnych IaaS**: Magazyny usług odzyskiwania przy użyciu, można przywrócić pliki i foldery z maszyn wirtualnych IaaS bez przywracania całą maszynę Wirtualną, która umożliwia szybsze przywracania. Natychmiastowego przywracania dla maszyn wirtualnych IaaS jest dostępna dla systemów Windows i maszyn wirtualnych systemu Linux. [Dowiedz się więcej](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Twoje Magazyny usług odzyskiwania w portalu zarządzania
Tworzenie i zarządzanie Magazyny usług odzyskiwania w portalu Azure jest łatwe, ponieważ usługa kopii zapasowej jest zintegrowana z menu ustawień platformy Azure. Integracja ta oznacza, że można tworzyć ani nimi zarządzać Magazyn usług odzyskiwania *w kontekście usługi docelowej*. Na przykład, aby wyświetlić punkty odzyskiwania dla maszyny Wirtualnej, zaznacz go i kliknij przycisk **kopii zapasowej** w menu Ustawienia. Zostanie wyświetlone kopii zapasowej informacje specyficzne dla tej maszyny Wirtualnej. W poniższym przykładzie **ContosoVM** jest nazwa maszyny wirtualnej. **ContosoVM demovault** to nazwa magazynu usług odzyskiwania. Nie trzeba pamiętać nazwy magazynu usług odzyskiwania, która przechowuje punkty odzyskiwania, dostęp do tych informacji z maszyny wirtualnej.  

![Szczegóły magazynu usług odzyskiwania maszyny Wirtualnej](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context.png)

Wiele serwerów są chronione przy użyciu tego samego magazynu usług odzyskiwania, może być bardziej logiczne, aby przyjrzeć się magazyn usług odzyskiwania. Wyszukaj wszystkie magazyny usług odzyskiwania w ramach subskrypcji i wybierz z listy.

Poniższe sekcje zawierają łącza do artykułów, które wyjaśniono, jak używać magazynu usług odzyskiwania w przypadku każdego typu działania.

### <a name="back-up-data"></a>Tworzenie kopii zapasowej danych
- [Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure](backup-azure-vms-first-look-arm.md)
- [Tworzenie kopii zapasowej systemu Windows Server lub stacji roboczej systemu Windows](backup-try-azure-backup-in-10-mins.md)
- [Tworzenie kopii zapasowej obciążeń programu DPM na platformie Azure](backup-azure-dpm-introduction.md)
- [Przygotowanie do tworzenia kopii zapasowych obciążeń przy użyciu serwera usługi Kopia zapasowa Azure](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Zarządzanie punktami odzyskiwania
- [Zarządzanie kopiami zapasowymi maszyny Wirtualnej Azure](backup-azure-manage-vms.md)
- [Zarządzanie plikami i folderami](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Przywróć dane z magazynu
- [Odzyskanie poszczególnych plików maszyny Wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md)
- [Przywracanie maszyny Wirtualnej platformy Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Bezpiecznego magazynu
- [Zabezpieczanie danych kopii zapasowej w chmurze w Magazyny usług odzyskiwania](backup-azure-security-feature.md)



## <a name="next-steps"></a>Następne kroki
Skorzystaj z poniższych artykułów, aby:</br>
[Tworzenie kopii zapasowej maszyn wirtualnych IaaS](backup-azure-arm-vms-prepare.md)</br>
[Utwórz kopię zapasową serwera kopia zapasowa Azure](backup-azure-microsoft-azure-backup.md)</br>
[Tworzenie kopii zapasowej systemu Windows Server](backup-configure-vault.md)
