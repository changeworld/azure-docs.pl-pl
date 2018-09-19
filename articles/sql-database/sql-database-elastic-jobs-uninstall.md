---
title: Jak odinstalować narzędzie zadań elastycznej bazy danych
description: Dowiedz się, jak odinstalować składniki zadań elastycznych baz danych przy użyciu witryny Azure portal, programu PowerShell.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: sstein
ms.openlocfilehash: 395bbf50373d3a6e3848fba9fd3db0d6989023f4
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "35649495"
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
1. Uruchom okno poleceń programu Microsoft Azure PowerShell i przejdź do narzędzia podkatalogu w folderze Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: typ **ciągłego wdrażania narzędzia**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > Narzędzia ciągłego wdrażania
2. Wykonaj.\UninstallElasticDatabaseJobs.ps1 skrypt programu PowerShell.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > odblokowanie pliku.\UninstallElasticDatabaseJobs.ps1 PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >. \ UninstallElasticDatabaseJobs.ps1

Lub po prostu, uruchom następujący skrypt, zakładając, że domyślne wartości w przypadku instalacji składników:

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
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>Kolejne kroki
Aby ponownie zainstalować zadania elastycznych baz danych, zobacz [Instalowanie usługi zadania elastycznych baz danych](sql-database-elastic-jobs-service-installation.md)

Aby uzyskać przegląd zadań elastycznej bazy danych, zobacz [Przegląd zadań Elastic Database](sql-database-elastic-jobs-overview.md).

<!--Image references-->


