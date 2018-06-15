---
title: Tworzenie kopii zapasowej Azure stosu | Dokumentacja firmy Microsoft
description: Wykonaj kopię zapasową na żądanie w stosie Azure przy użyciu kopii zapasowej w miejscu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075191"
---
# <a name="back-up-azure-stack"></a>Tworzenie kopii zapasowej Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Wykonaj kopię zapasową na żądanie w stosie Azure przy użyciu kopii zapasowej w miejscu. Jeśli musisz włączyć usługę kopia zapasowa infrastruktury, zobacz [włączenia kopii zapasowej Azure stosu z portalu administracyjnego](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Aby uzyskać instrukcje na temat konfigurowania środowiska PowerShell, zobacz [Zainstaluj program PowerShell Azure stosu ](azure-stack-powershell-install.md).

## <a name="start-azure-stack-backup"></a>Kopia zapasowa Azure stosu

Otwórz program Windows PowerShell z podwyższonym poziomem uprawnień monitem w środowisku zarządzania operatora i uruchom następujące polecenia:

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Potwierdź kopii zapasowej zostało ukończone w portalu administracyjnym

1. Otwieranie portalu administracyjnego platformy Azure stosu w [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Wybierz **więcej usług** > **infrastruktura kopii zapasowej**. Wybierz **konfiguracji** w **infrastruktura kopii zapasowej** bloku.
3. Znajdź **nazwa** i **Data ukończenia** kopii zapasowej w **dostępnych kopii zapasowych** listy.
4. Sprawdź **stanu** jest **zakończyło się pomyślnie**.

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat przepływu pracy do odzyskiwania od zdarzenia utraty danych. Zobacz [odzyskać przed utratą danych w wyniku katastrofy](azure-stack-backup-recover-data.md).
