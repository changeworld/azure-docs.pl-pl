---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183690"
---
Jeśli wystąpią jakiekolwiek problemy z urządzeniem, można utworzyć pakiet pomocy technicznej z dzienników systemowych. Pomoc techniczna firmy Microsoft używa tego pakietu do rozwiązywania problemów. Wykonaj następujące kroki, aby utworzyć pakiet pomocy technicznej:

1. [Połącz się z interfejsem programu PowerShell urządzenia](#connect-to-the-powershell-interface).
2. Użyj `Get-HcsNodeSupportPackage` polecenia, aby utworzyć pakiet pomocy technicznej. Użycie polecenia cmdlet jest następujące:

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

    Polecenie cmdlet zbiera dzienniki z urządzenia i kopiuje te dzienniki do określonej sieci lub udziału lokalnego.

    Zastosowane parametry są następujące:

    - `-Path`- Określ sieć lub ścieżkę lokalną, do którą chcesz skopiować pakiet pomocy technicznej. (wymagane)
    - `-Credential`- Określ poświadczenia, aby uzyskać dostęp do chronionej ścieżki.
    - `-Zip`- Określ, aby wygenerować plik zip.
    - `-Include`- Określ, aby uwzględnić składniki, które mają być zawarte w pakiecie pomocy technicznej. Jeśli nie `Default` zostanie określony, zakłada się.
    - `-IncludeArchived`- Określ, aby uwzględnić zarchiwizowane dzienniki w pakiecie pomocy technicznej.
    - `-IncludePeriodicStats`- Określ, aby uwzględnić okresowe dzienniki statystyk w pakiecie pomocy technicznej.

    
