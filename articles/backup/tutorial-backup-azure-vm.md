---
title: Tworzenie kopii zapasowych maszyn wirtualnych na platformie Azure na dużą skalę | Microsoft Docs
description: Ten samouczek zawiera szczegółowe instrukcje tworzenia kopii zapasowych wielu maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: kopia zapasowa maszyny wirtualnej; tworzenie kopii zapasowej maszyny wirtualnej; kopia zapasowa i odzyskiwanie po awarii
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 62cc623dc3130119c5ec803933012c5545d703e5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Tworzenie kopii zapasowych maszyn wirtualnych na platformie Azure na dużą skalę

Ten samouczek zawiera szczegółowe instrukcje tworzenia kopii zapasowych maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services. Większość działań związanych z tworzeniem kopii zapasowych maszyn wirtualnych to działania przygotowawcze. Zanim będzie można utworzyć kopię zapasową maszyny wirtualnej (lub zabezpieczyć ją), należy spełnić [wymagania wstępne](backup-azure-arm-vms-prepare.md) w celu przygotowania środowiska na potrzeby ochrony maszyn wirtualnych. 

> [!IMPORTANT]
> W tym samouczku przyjęto założenie, że utworzono już grupę zasobów i maszynę wirtualną platformy Azure.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

[Magazyn usługi Recovery Services](backup-azure-recovery-services-vault-overview.md) to kontener przechowujący punkty odzyskiwania elementów, dla których tworzona jest kopia zapasowa. Magazyn usługi Recovery Services jest zasobem platformy Azure, który można wdrażać i którym można zarządzać w ramach grupy zasobów platformy Azure. W tym samouczku utworzysz magazyn usługi Recovery Services w tej samej grupie zasobów, w której znajduje się chroniona maszyna wirtualna.


Przy pierwszym użyciu usługi Azure Backup należy zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji. Jeśli dostawca został już zarejestrowany w ramach subskrypcji, przejdź do następnego kroku.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Utwórz magazyn usługi Recovery Services za pomocą polecenia **New-AzureRmRecoveryServicesVault**. Pamiętaj, aby określić nazwę i lokalizację grupy zasobów używane podczas konfigurowania maszyny wirtualnej, której kopia zapasowa będzie tworzona. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej. Następnie należy użyć polecenia **Set-AzureRmRecoveryServicesBackupProperties**, aby ustawić opcję **-BackupStorageRedundancy** na wartość [Magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Zanim będzie można uruchomić proces tworzenia początkowej kopii zapasowej, należy ustawić kontekst magazynu. Kontekst magazynu to typ danych chronionych w magazynie. Podczas tworzenia magazynu usługi Recovery Services jest on dostarczany z domyślnymi zasadami ochrony i przechowywania. Domyślne zasady ochrony wyzwalają zadanie tworzenia kopii zapasowej każdego dnia o określonej godzinie. Domyślne zasady przechowywania zachowują codzienny punkt odzyskiwania przez 30 dni. Dla celów tego samouczka zaakceptuj te domyślne zasady. 

Użyj polecenia **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**, aby ustawić kontekst magazynu. Po ustawieniu kontekst magazynu ma zastosowanie do wszystkich kolejnych poleceń cmdlet. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Użyj polecenia **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)**, aby wyzwolić zadanie tworzenia kopii zapasowej. Zadanie tworzenia kopii zapasowej tworzy punkt odzyskiwania. Jeśli tworzona jest początkowa kopia zapasowa, punkt odzyskiwania jest pełną kopią zapasową. Kolejne operacje tworzenia kopii zapasowych tworzą kopie przyrostowe.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Usuwanie magazynu usługi Recovery Services

Aby usunąć magazyn usługi Recovery Services, należy najpierw usunąć z niego wszystkie punkty odzyskiwania, a następnie wyrejestrować magazyn. Następujące polecenia obrazują szczegółowo wykonywane kroki. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami
Jeśli podczas tworzenia kopii zapasowej maszyny wirtualnej wystąpią błędy, pomocne mogą się okazać wskazówki z artykułu dotyczącego [rozwiązywania problemów z tworzeniem kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-troubleshoot.md).

## <a name="next-steps"></a>Następne kroki
Teraz, po zabezpieczeniu maszyn wirtualnych, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o zadaniach zarządzania i sposobie przywracania maszyn wirtualnych z punktu odzyskiwania.

* Aby zmodyfikować zasady tworzenia kopii zapasowych, zobacz sekcję dotyczącą [użycia poleceń cmdlet AzureRM.RecoveryServices.Backup do tworzenia kopii zapasowych maszyn wirtualnych](backup-azure-vms-automation.md#create-a-protection-policy).
* [Monitorowanie maszyn wirtualnych i zarządzanie nimi](backup-azure-manage-vms.md)
* [Przywracanie maszyn wirtualnych](backup-azure-arm-restore-vms.md)
