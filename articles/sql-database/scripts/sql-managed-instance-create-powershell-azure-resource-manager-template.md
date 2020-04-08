---
title: Przykład szablonu — tworzenie wystąpienia zarządzanego w bazie danych SQL usługi Azure
description: Ten przykładowy skrypt programu Azure PowerShell służy do tworzenia wystąpienia zarządzanego w bazie danych SQL usługi Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: e5be8c9441be5ca441a5c0f7c4444c2edbdc7a95
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811217"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Tworzenie wystąpienia zarządzanego w bazie danych SQL usługi Azure database za pomocą dodatku PowerShell z szablonem usługi Azure Resource Manager

Wystąpienie zarządzane usługi Azure SQL Database można utworzyć przy użyciu biblioteki programu Azure PowerShell i szablonów usługi Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten samouczek wymaga programu AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Za pomocą poleceń programu Azure PowerShell można rozpocząć wdrożenie przy użyciu wstępnie zdefiniowanego szablonu usługi Azure Resource Manager. W szablonie można określić następujące właściwości:

- Nazwa wystąpienia
- Nazwa użytkownika i hasło administratora programu SQL
- Rozmiar wystąpienia (liczba rdzeni i maksymalny rozmiar magazynu)
- Sieć wirtualna i podsieć, w których zostaną umieszczone wystąpienia
- Sortowanie wystąpienia na poziomie serwera (wersja zapoznawcza)

Nazwy wystąpienia, nazwy użytkownika administratora SQL, sieci wirtualnej/podsieci i sortowania nie można zmienić później. Inne właściwości wystąpienia można zmienić.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie założono, że [utworzono prawidłowe środowisko sieciowe](../sql-database-managed-instance-create-vnet-subnet.md) lub [zmodyfikowano istniejącą sieć wirtualną](../sql-database-managed-instance-configure-vnet-subnet.md) dla wystąpienia zarządzanego. W przykładzie użyto poleceń cmdlet [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) i [Get-AzVirtualNetwork,](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) więc upewnij się, że zainstalowano następujące moduły programu PowerShell:

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
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
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

Zakłada się, że sieć wirtualna platformy Azure z prawidłowo skonfigurowaną podsiecią już istnieje. Jeśli nie masz poprawnie skonfigurowaną podsieci, przygotuj środowisko sieciowe przy użyciu oddzielnego [szablonu zarządzanego zasobem platformy Azure,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) który może być wykonywany niezależnie lub uwzględniony w tym szablonie.

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
