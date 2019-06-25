---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183690"
---
Jeśli wystąpią problemy z dowolnego urządzenia, można utworzyć pakietu dla pomocy technicznej z dzienników systemu. Microsoft Support używa tego pakietu do rozwiązywania problemów. Wykonaj następujące kroki, aby utworzyć pakiet dla pomocy technicznej:

1. [Nawiązać połączenie z interfejsu programu PowerShell urządzenia](#connect-to-the-powershell-interface).
2. Użyj `Get-HcsNodeSupportPackage` polecenie, aby utworzyć pakiet dla pomocy technicznej. Użycie polecenia cmdlet jest następująca:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Polecenie cmdlet zbiera dzienniki z urządzenia i kopiuje tych dzienników w określonej sieci lub w udziale lokalnym.

    Parametry używane są następujące:

    - `-Path` -Określ sieci lub ścieżkę lokalną do skopiowania do pakietu dla pomocy technicznej. (wymagane)
    - `-Credential` -Określ poświadczenia, które mają dostęp do ścieżki chronionej.
    - `-Zip` -Podaj można wygenerować pliku zip.
    - `-Include` -Określono jako uwzględniane składniki, które mają zostać uwzględnione w pakietu dla pomocy technicznej. Jeśli nie zostanie określony, `Default` zakłada, że.
    - `-IncludeArchived` — Określ, które mają zostać objęte zarchiwizowanych dzienników pakietu dla pomocy technicznej.
    - `-IncludePeriodicStats` — Określ, które mają zostać objęte okresowe stat — dzienniki pakietu dla pomocy technicznej.

    
