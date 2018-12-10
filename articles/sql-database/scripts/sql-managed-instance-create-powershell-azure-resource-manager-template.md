---
title: Przykład programu PowerShell — tworzenie wystąpienia zarządzanego usługi Azure SQL Database | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell umożliwiający utworzenie wystąpienia zarządzanego usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: e2075f8a8e54a091dbb82f9ed6d1c8ddaa9da4d0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869847"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-an-azure-sql-database-managed-instance"></a>Używając programu PowerShell z szablonem usługi Azure Resource Manager, można utworzyć wystąpienie zarządzane usługi Azure SQL Database

Wystąpienie zarządzane usługi Azure SQL Database można utworzyć przy użyciu biblioteki programu Azure PowerShell i szablonów usługi Azure Resource Manager. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

Za pomocą poleceń programu Azure PowerShell można rozpocząć wdrożenie przy użyciu wstępnie zdefiniowanego szablonu usługi Azure Resource Manager. W szablonie można określić następujące właściwości:
- Nazwa wystąpienia
- Nazwa użytkownika i hasło administratora programu SQL 
- Rozmiar wystąpienia (liczba rdzeni i maksymalny rozmiar magazynu)
- Sieć wirtualna i podsieć, w których zostaną umieszczone wystąpienia
- Sortowanie wystąpienia na poziomie serwera (wersja zapoznawcza)

Nazwy wystąpienia, nazwy użytkownika administratora SQL, sieci wirtualnej/podsieci i sortowania nie można zmienić później. Inne właściwości wystąpienia można zmienić.

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Następującą zawartość należy umieścić w pliku reprezentującym szablon, który zostanie użyty do utworzenia wystąpienia:
```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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
Zakłada się, że sieć wirtualna platformy Azure z prawidłowo skonfigurowaną podsiecią już istnieje. Jeśli tak nie jest, należy przygotować środowisko sieciowe przy użyciu oddzielnego [szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment), który można wykonać oddzielnie lub dołączyć do tego szablonu.

Zapisz zawartość tego pliku w formacie JSON, umieść ścieżkę pliku w następującym skrypcie programu PowerShell i zmień nazwy obiektów w skrypcie: 

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzureRmSubscription -SubscriptionId $subscriptionId

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
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzureRmResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```
Po pomyślnym wykonaniu tego skryptu można uzyskać dostęp do bazy danych SQL z poziomu wszystkich usług platformy Azure przy użyciu skonfigurowanego adresu IP. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).



