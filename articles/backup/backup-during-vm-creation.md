---
title: Włącz kopię zapasową maszyny Wirtualnej platformy Azure podczas tworzenia
description: Jak włączyć kopie zapasowe maszyn wirtualnych platformy Azure w trakcie procesu tworzenia.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780447"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Włącz wykonywanie kopii zapasowej po utworzeniu maszyny wirtualnej platformy Azure

Tworzenie kopii zapasowych maszyn wirtualnych (VM) przy użyciu usługi Azure Backup. Maszyny wirtualne kopie zapasowe są tworzone zgodnie z harmonogramem określonym w zasadach tworzenia kopii zapasowych i punktów odzyskiwania są tworzone na podstawie kopii zapasowych. Punkty odzyskiwania są przechowywane w magazynach usługi Recovery Services.

Ten artykuł szczegółowo opisuje sposób włączania kopii zapasowej, gdy tworzysz maszynę wirtualną (VM) w witrynie Azure portal.  

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Jeśli nie są już zalogowano do swojego konta, zaloguj się do [witryny Azure portal](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Tworzenie maszyny Wirtualnej przy użyciu kopii zapasowej skonfigurowane 

1. W lewym górnym rogu witryny Azure portal wybierz **New**.

1. Wybierz **obliczenia**, a następnie wybierz obraz maszyny Wirtualnej.

1. Wprowadź informacje dotyczące maszyny Wirtualnej. Nazwa użytkownika i hasło podane będzie służyć do logowania się do maszyny Wirtualnej. Gdy skończysz, wybierz pozycję **OK**. 

1. Wybierz rozmiar maszyny wirtualnej.  

1. W obszarze **ustawienia** > **kopii zapasowej**, wybierz opcję **włączone** aby otworzyć ustawienia konfiguracji kopii zapasowej.

   - Aby zaakceptować wartości domyślne, wybrać **OK** na **ustawienia** strony. Następnie nastąpi przejście do **Podsumowanie** strony, aby utworzyć maszynę Wirtualną. Pomiń kroki 6 – 8.
   - Aby zmienić wartości konfiguracji kopii zapasowej, wykonaj kolejne kroki.  

1. Utwórz lub wybierz magazyn usługi Recovery Services do przechowywania kopii zapasowych maszyny wirtualnej. Jeśli tworzysz magazyn usługi Recovery Services można wybrać grupę zasobów magazynu.  

    ![Ustawienia konfiguracji kopii zapasowej na stronie tworzenia maszyny Wirtualnej](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Grupa zasobów dla magazynu usługi Recovery Services może być inna niż grupa zasobów dla maszyny Wirtualnej.  

1. Domyślnie zasady tworzenia kopii zapasowych jest wybrana do ochrony maszyny Wirtualnej. Zasady tworzenia kopii zapasowych określa, jak często migawek kopii zapasowych i jak długo przechowywać te kopie zapasowe. 

   - Możesz zaakceptować domyślne zasady lub można utworzyć lub wybrać inne zasady kopii zapasowych. 
   - Aby edytować zasady kopii zapasowych, wybierz **zasady tworzenia kopii zapasowej** i zmień wartości.  

1. Po zakończeniu ustawiania wartości konfiguracji kopii zapasowej wybierz **OK** na **ustawienia** strony.  

1. Na **Podsumowanie** po upływie weryfikacji stronie wybierz **Utwórz** do utworzenia maszyny Wirtualnej, który używa skonfigurowanych ustawień kopii zapasowej. 

## <a name="start-a-backup-after-creating-the-vm"></a>Rozpocznij tworzenie kopii zapasowej po utworzeniu maszyny Wirtualnej 

Mimo, że skonfigurowano zasady kopii zapasowej dla maszyny Wirtualnej, jest dobrym rozwiązaniem, aby utworzyć początkową kopię zapasową. 

Po zakończeniu tworzenia szablonu maszyny Wirtualnej, przejdź do **operacji** w menu po lewej stronie i wybierz pozycję **kopii zapasowej** Aby wyświetlić szczegóły kopii zapasowej dla maszyny wirtualnej. Można użyć tej strony, aby:

- Wyzwalanie kopii zapasowej na żądanie.
- Przywracanie pełnej maszyny Wirtualnej lub jej dysków.
- Przywróć pliki z kopii zapasowej maszyny Wirtualnej.
- Zmień zasady tworzenia kopii zapasowej skojarzony z maszyną Wirtualną.  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Wdrażanie chronionej maszyny Wirtualnej przy użyciu szablonu usługi Resource Manager

Poprzednie kroki wyjaśniają jak używać witryny Azure portal do utworzenia maszyny wirtualnej i chronić go w magazynie usługi Recovery Services. Aby szybko wdrożyć co najmniej jednej maszyny wirtualnej i chronić je w magazynie usługi Recovery Services, zobacz szablonu [wdrażanie maszyny Wirtualnej z systemem Windows i włącz wykonywanie kopii zapasowej](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Często zadawane pytania 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>Obrazy maszyn wirtualnych, które obsługują konfigurację tworzenia kopii zapasowych podczas tworzenia maszyny Wirtualnej?

Na poniższych ilustracjach core opublikowane przez firmę Microsoft są obsługiwane w przypadku włączania kopii zapasowych podczas tworzenia maszyny Wirtualnej. Dla innych maszyn wirtualnych aby umożliwić kopii zapasowej po utworzeniu maszyny Wirtualnej. Aby dowiedzieć się więcej, zobacz [Włącz kopię zapasową, po utworzeniu maszyny Wirtualnej](quick-backup-vm-portal.md).

- **Windows** -Windows Server 2016 Datacenter, Windows Server 2016 Core Datacenter, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** — Ubuntu Server 17.10, Ubuntu Server 17.04, Ubuntu Server 16.04 (LTS), serwer Ubuntu 14.04 (LTS) 
- **Red Hat** -RHEL 6.7, 6.8 6,9, 7.2, 7.3, wersji 7.4 
- **SUSE** — SUSE Linux Enterprise Server 11 z dodatkiem SP4, 12 z dodatkiem SP2, 12 z dodatkiem SP3 
- **Debian** — Debian 8, Debian 9 
- **CentOS** -CentOS 6,9, CentOS 7.3 
- **Oracle Linux** — Oracle Linux 6.7, 6.8, 6,9, 7.2, 7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Jest koszt kopii zapasowej wliczane w koszt maszyny Wirtualnej? 

Nie. Koszty tworzenia kopii zapasowej są niezależne od koszty maszyn wirtualnych. Aby uzyskać więcej informacji o cenach kopii zapasowych, zobacz [cennik usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Jakie uprawnienia są wymagane do włączenia kopii zapasowej na maszynie Wirtualnej? 

Jeśli jesteś współautorem maszyny Wirtualnej, aby umożliwić kopii zapasowej na maszynie Wirtualnej. Jeśli używasz roli niestandardowej potrzebne są następujące uprawnienia w celu włączenia kopii zapasowej na maszynie Wirtualnej: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Jeśli maszyn wirtualnych i magazynu usługi Recovery Services różnych grupach zasobów, upewnij się, że masz uprawnienia do zapisu w grupie zasobów dla magazynu usługi Recovery Services.  

## <a name="next-steps"></a>Kolejne kroki 

Skoro już chronione maszyny Wirtualnej, zobacz następujące artykuły, aby dowiedzieć się, jak zarządzać i przywracanie maszyn wirtualnych:

- [Monitorowanie maszyn wirtualnych i zarządzanie nimi](backup-azure-manage-vms.md) 
- [Przywracanie maszyn wirtualnych](backup-azure-arm-restore-vms.md) 
