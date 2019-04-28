---
title: Jak odinstalować narzędzie zadań elastycznej bazy danych
description: Dowiedz się, jak odinstalować składniki zadań elastycznych baz danych przy użyciu witryny Azure portal, programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 12f923724616378a6ea6c83a947bd5362840a697
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435415"
---
# <a name="uninstall-elastic-database-jobs-components"></a>Odinstaluj składniki zadań elastycznej bazy danych


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Zadania elastic Database** składniki można odinstalować za pomocą witryny Azure portal lub programu PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Odinstaluj składniki zadań elastycznych baz danych przy użyciu witryny Azure portal
1. Otwórz [portal Azure](https://portal.azure.com/).
2. Przejdź do subskrypcji, która zawiera **zadania Elastic Database** składników, a mianowicie subskrypcji, w której elastycznej bazy danych zostały zainstalowane składniki zadania.
3. Kliknij przycisk **Przeglądaj** i kliknij przycisk **grup zasobów**.
4. Wybierz grupę zasobów o nazwie "__ElasticDatabaseJob".
5. Usuń grupę zasobów.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Odinstaluj składniki zadań elastycznych baz danych przy użyciu programu PowerShell
1. Uruchom okno poleceń programu Microsoft Azure PowerShell i przejdź do narzędzia podkatalogu w folderze Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: Typ **ciągłego wdrażania narzędzia**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > Narzędzia ciągłego wdrażania
2. Wykonaj.\UninstallElasticDatabaseJobs.ps1 skrypt programu PowerShell.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > odblokowanie pliku.\UninstallElasticDatabaseJobs.ps1 PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >. \ UninstallElasticDatabaseJobs.ps1

Lub po prostu, uruchom następujący skrypt, zakładając, że domyślne wartości w przypadku instalacji składników:

```powershell
$ResourceGroupName = "__ElasticDatabaseJob"
Switch-AzureMode AzureResourceManager

$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
if(!$resourceGroup)
{
     Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
     return
}

Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
Remove-AzureResourceGroup -Name $ResourceGroupName -Force
Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job components are now uninstalled."
```

## <a name="next-steps"></a>Kolejne kroki
Aby ponownie zainstalować zadania elastycznych baz danych, zobacz [Instalowanie usługi zadania elastycznych baz danych](sql-database-elastic-jobs-service-installation.md)

Aby uzyskać przegląd zadań elastycznej bazy danych, zobacz [Przegląd zadań Elastic Database](sql-database-elastic-jobs-overview.md).
