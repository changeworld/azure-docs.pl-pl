---
title: Przykład programu PowerShell — Tworzenie wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Przykładowy skrypt Azure PowerShell do tworzenia wystąpienia zarządzanego usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 2d5ecb3035aad657485916a4c472f6f4dc9eb530
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729150"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Użyj programu PowerShell przy użyciu szablonu usługi Azure Resource Manager, aby utworzyć wystąpienie zarządzane usługi Azure SQL Database

Wystąpienie zarządzane usługi Azure SQL Database można utworzyć przy użyciu biblioteki programu Azure PowerShell i szablonów usługi Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z programu PowerShell lokalnie, ten samouczek wymaga programu PowerShell AZ 1.4.0 lub nowszej. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Za pomocą poleceń programu Azure PowerShell można rozpocząć wdrożenie przy użyciu wstępnie zdefiniowanego szablonu usługi Azure Resource Manager. W szablonie można określić następujące właściwości:

- Nazwa wystąpienia
- Nazwa użytkownika i hasło administratora programu SQL
- Rozmiar wystąpienia (liczba rdzeni i maksymalny rozmiar magazynu)
- Sieć wirtualna i podsieć, w których zostaną umieszczone wystąpienia
- Sortowanie wystąpienia na poziomie serwera (wersja zapoznawcza)

Nazwy wystąpienia, nazwy użytkownika administratora SQL, sieci wirtualnej/podsieci i sortowania nie można zmienić później. Inne właściwości wystąpienia można zmienić.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie założono, że [utworzono prawidłowe środowisko sieciowe](../sql-database-managed-instance-create-vnet-subnet.md) lub [zmodyfikowano istniejącą sieć wirtualną](../sql-database-managed-instance-configure-vnet-subnet.md) dla wystąpienia zarządzanego. W przykładzie użyto polecenia cmdlet [New AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) i [Get AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) dlatego upewnij się, że zainstalowano poniższe moduły programu PowerShell:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Następującą zawartość należy umieścić w pliku reprezentującym szablon, który zostanie użyty do utworzenia wystąpienia:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Zakłada się, że sieć wirtualna platformy Azure z prawidłowo skonfigurowaną podsiecią już istnieje. Jeśli nie masz poprawnie skonfigurowany podsieci, należy przygotować środowisko sieciowe, przy użyciu oddzielnych [szablonu usługi Azure Managed zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) , mogą być wykonywane niezależnie lub zawarte w tym szablonie.

Zapisz zawartość tego pliku w formacie JSON, umieść ścieżkę pliku w następującym skrypcie programu PowerShell i zmień nazwy obiektów w skrypcie:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Po pomyślnym wykonaniu tego skryptu można uzyskać dostęp do bazy danych SQL z poziomu wszystkich usług platformy Azure przy użyciu skonfigurowanego adresu IP.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
