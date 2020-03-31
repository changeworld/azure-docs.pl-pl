---
title: Usługa Azure Backup dla programu SQL Server uruchomiona na maszynie Wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak zarejestrować usługę Azure Backup w programie SQL Server uruchomionym na maszynie wirtualnej platformy Azure.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247388"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Usługa Azure Backup dla programu SQL Server uruchomiona na maszynie Wirtualnej platformy Azure

Usługa Azure Backup, między innymi, zapewnia obsługę tworzenia kopii zapasowych obciążeń, takich jak SQL Server uruchomiony na maszynach wirtualnych platformy Azure. Ponieważ aplikacja SQL jest uruchomiona w ramach maszyny Wirtualnej platformy Azure, usługa tworzenia kopii zapasowych wymaga uprawnień dostępu do aplikacji i pobrania niezbędnych szczegółów.
Aby to zrobić, usługa Azure Backup instaluje rozszerzenie **AzureBackupWindowsWorkload** na maszynie wirtualnej, na której jest uruchomiony program SQL Server, podczas procesu rejestracji wyzwalane przez użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać listę obsługiwanych scenariuszy, zobacz [macierz obsługi](../../backup/sql-support-matrix.md#scenario-support) obsługiwane przez usługę Azure Backup.

## <a name="network-connectivity"></a>Łączność sieciowa

Usługa Azure Backup obsługuje tagi sieciowej grupy danych i sieci płciowych, wdrażając serwer proxy lub wymienione zakresy adresów IP; szczegółowe informacje na temat każdej z metod można znaleźć w tym [artykule](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Schemat rozszerzenia

Wartości schematu rozszerzenia i właściwości są wartościami konfiguracji (ustawieniami środowiska wykonawczego), które usługa przekazuje do interfejsu API CRP. Te wartości konfiguracji są używane podczas rejestracji i uaktualniania. **Rozszerzenie AzureBackupWindowsWorkload** również używa tego schematu. Schemat jest wstępnie ustawiony; nowy parametr można dodać w polu objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Poniżej przedstawiono schemat rozszerzenia WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Wartości właściwości

Nazwa | Wartość/przykład | Typ danych
 --- | --- | ---
locale | pl-pl  |  ciąg
identyfikator zadania | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | ciąg
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29ud GFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIMDU5NWIwOGYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIwiU3Vic2YAXB0aW9uSWQiOiJkNGEzOTliNy1iYJAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMu29tIn1dfQQ ==" | ciąg
commandStartTimeUTCTicks | "636967192566036845"  | ciąg
typ maszyny wirtualnej  | "microsoft.compute/virtualmachines"  | ciąg
objectStr <br/> (chronioneStawy) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29ud GFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIMDU5NWIwOGYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIwiU3Vic2YAXB0aW9uSWQiOiJkNGEzOTliNy1iYJAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMu29tIn1dfQQ ==" | ciąg
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | ciąg
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | ciąg

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Zaleca się dodanie rozszerzenia AzureBackupWindowsWorkload do maszyny wirtualnej polega na włączeniu kopii zapasowej programu SQL Server na maszynie wirtualnej. Można to osiągnąć za pomocą [szablonu Menedżera zasobów przeznaczonego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) do automatyzacji tworzenia kopii zapasowych na maszynie wirtualnej programu SQL Server.

## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

Musisz "zarejestrować" maszynę wirtualną platformy Azure, która zawiera aplikację SQL z magazynem usług odzyskiwania. Podczas rejestracji rozszerzenie AzureBackupWindowsWorkload zostanie zainstalowane na maszynie wirtualnej. Użyj polecenia cmdlet [Register-AzRecoveryServicesBackupContainerPS,](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) aby zarejestrować maszynę wirtualną.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Polecenie zwróci **kontener kopii zapasowej** tego zasobu, a stan zostanie **zarejestrowany**.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) o wskazówki dotyczące rozwiązywania problemów z tworzeniem kopii zapasowych usługi Azure SQL Server
- [Typowe pytania](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) dotyczące tworzenia kopii zapasowych baz danych programu SQL Server, które są uruchamiane na maszynach wirtualnych platformy Azure (VM) i które korzystają z usługi Azure Backup.
