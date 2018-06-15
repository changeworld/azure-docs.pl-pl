---
title: Jak odinstalować narzędzia zadania elastycznej bazy danych
description: Dowiedz się, jak odinstalować składniki zadania elastycznej bazy danych przy użyciu portalu Azure PowerShell.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: e1089db848b30945e5e61765c762262f5478450e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645892"
---
# <a name="uninstall-elastic-database-jobs-components"></a>Odinstaluj składniki zadania elastycznej bazy danych
**Zadania elastyczne bazy danych** składniki można odinstalować za pomocą portalu Azure lub programu PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Odinstaluj składniki zadania elastycznej bazy danych przy użyciu portalu Azure
1. Otwórz [portal Azure](https://portal.azure.com/).
2. Przejdź do subskrypcji, która zawiera **zadania elastycznej bazy danych** składniki, to znaczy subskrypcji, w których elastycznej bazy danych zostały zainstalowane składniki zadania.
3. Kliknij przycisk **Przeglądaj** i kliknij przycisk **grup zasobów**.
4. Wybierz grupę zasobów o nazwie "__ElasticDatabaseJob".
5. Usuń grupę zasobów.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Odinstaluj składniki zadania elastycznej bazy danych przy użyciu programu PowerShell
1. Uruchom okno poleceń programu PowerShell usługi Microsoft Azure i przejdź do podkatalogu narzędzia w folderze Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: typ **narzędzia cd**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > Narzędzia dysku cd
2. Wykonanie.\UninstallElasticDatabaseJobs.ps1 skrypt programu PowerShell.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools > C:.\UninstallElasticDatabaseJobs.ps1 PS odblokowanie pliku\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools >. \ UninstallElasticDatabaseJobs.ps1

Lub po prostu, uruchom następujący skrypt, przy założeniu, domyślne wartości, jeśli w instalacji składników:

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
Aby ponownie zainstalować zadania elastycznej bazy danych, zobacz [instalowania usługi zadania elastycznej bazy danych](sql-database-elastic-jobs-service-installation.md)

Omówienie zadania elastycznej bazy danych, zobacz [omówienie zadania elastycznej bazy danych](sql-database-elastic-jobs-overview.md).

<!--Image references-->


