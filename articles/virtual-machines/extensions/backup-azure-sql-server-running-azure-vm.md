---
title: Usługa Azure Backup dla serwera SQL uruchomionego na maszynie Wirtualnej platformy Azure
description: Sposób rejestrowania usługi Azure Backup programu SQL Server uruchomiony na maszynie Wirtualnej platformy Azure
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607613"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Usługa Azure Backup dla serwera SQL uruchomionego na maszynie Wirtualnej platformy Azure

Usługa Azure Backup, między innymi ofertami zapewnia obsługę tworzenia kopii zapasowych obciążeń, takich jak SQL Server uruchamianych na maszynach wirtualnych platformy Azure. Ponieważ aplikacja SQL jest uruchomiona w Maszynie wirtualnej platformy Azure, usługa backup wymaga uprawnień dostępu do aplikacji i pobrać wymagane szczegóły.
Aby to zrobić, usługi Azure Backup instaluje **AzureBackupWindowsWorkload** rozszerzenia na maszynie Wirtualnej, w którym działa program SQL Server, podczas procesu rejestracji wyzwalane przez użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

Lista obsługiwanych scenariuszach można znaleźć [macierz obsługi](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) obsługiwane przez usługę Azure Backup.

## <a name="network-connectivity"></a>Połączenie sieciowe

Usługa Azure Backup obsługuje tagi sieciowej grupy zabezpieczeń, wdrażania serwera proxy lub na liście zakresów adresów IP; Szczegóły dotyczące każdej z metod, można znaleźć to [artykułu](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Schemat rozszerzenia

Rozszerzenia schematu i wartości właściwości są wartościami konfiguracji (ustawienia środowiska uruchomieniowego), które usługa przechodzi do interfejsu API CRP. Te wartości konfiguracji są używane podczas rejestracji i uaktualniania. **AzureBackupWindowsWorkload** rozszerzenie również używają tego schematu. Schemat jest wstępnie ustawiony. w polu objectStr można dodać nowego parametru

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

Następujący kod JSON zawiera schemat dla rozszerzenia WorkloadBackup.  

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

Name (Nazwa) | Przykład/wartości | Typ danych
 --- | --- | ---
Ustawienia regionalne | en-us  |  ciąg
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | ciąg
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ == " | ciąg
commandStartTimeUTCTicks | "636967192566036845"  | ciąg
vmType  | "microsoft.compute/virtualmachines"  | ciąg
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ == " | ciąg
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | ciąg
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | ciąg


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Zaleca się dodawania AzureBackupWindowsWorkload rozszerzenia do maszyny wirtualnej, należy włączyć kopia zapasowa programu SQL Server na maszynie wirtualnej. Można to osiągnąć poprzez [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) przeznaczona do automatyzacji kopii zapasowej na maszynę Wirtualną programu SQL Server.


## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell

Należy zarejestrować maszyny Wirtualnej platformy Azure, który zawiera aplikację SQL przy użyciu magazynu usługi Recovery services. Podczas rejestracji rozszerzenie AzureBackupWindowsWorkload pobiera zainstalowane na maszynie Wirtualnej. Użyj [AzRecoveryServicesBackupContainerPS rejestru](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) polecenia cmdlet można zarejestrować maszyny Wirtualnej.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
Polecenie zwróci **kontener kopii zapasowych** tego zasobu i stan będą **zarejestrowany**.


## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) dotyczące funkcji Kopia zapasowa maszyny Wirtualnej serwera SQL Azure wskazówki dotyczące rozwiązywania problemów
- [Często zadawane pytania dotyczące](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) o tworzeniu kopii zapasowych baz danych programu SQL Server, uruchomioną na maszynach wirtualnych Azure (maszyny wirtualne), które korzystają z usługi Azure Backup.
