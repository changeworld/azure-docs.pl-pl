---
title: Wdrażanie szablonu usługi Azure Resource Manager w ożeniu usługi Azure Automation
description: Jak wdrożyć szablon usługi Azure Resource Manager przechowywany w usłudze Azure Storage z uruchomieniu księgi rozmieszczenia
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: d4adbea42cda54380ad32dce40cfa0d8391ee490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75366638"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Wdrażanie szablonu usługi Azure Resource Manager w elemencie runbook programu PowerShell usługi Azure Automation

Można napisać element [runbook programu Azure Automation PowerShell,](automation-first-runbook-textual-powershell.md) który wdraża zasób platformy Azure przy użyciu [szablonu zarządzania zasobami platformy Azure.](../azure-resource-manager/resource-manager-create-first-template.md)

W ten sposób można zautomatyzować wdrażanie zasobów platformy Azure. Szablony Usługi Resource Manager można zachować w centralnej, bezpiecznej lokalizacji, takiej jak usługa Azure Storage.

W tym artykule tworzymy projekt runbook programu PowerShell, który używa szablonu Usługi Resource Manager przechowywane w [usłudze Azure Storage](../storage/common/storage-introduction.md) do wdrożenia nowego konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli jeszcze go nie masz, możesz [aktywować swoje korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub [założyć bezpłatne konto.](https://azure.microsoft.com/free/)
* [Konto usługi Automation](automation-sec-configure-azure-runas-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* [Konto usługi Azure Storage,](../storage/common/storage-create-storage-account.md) na którym ma być przechowywane szablon Usługi Resource Manager
* Program Azure Powershell zainstalowany na komputerze lokalnym. Zobacz [Instalowanie i konfigurowanie programu Azure Powershell,](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) aby uzyskać informacje dotyczące sposobu uzyskania programu Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

W tym przykładzie używamy szablonu Menedżera zasobów, który wdraża nowe konto usługi Azure Storage.

W edytorze tekstu skopiuj następujący tekst:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Zapisz plik lokalnie `TemplateTest.json`jako .

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Zapisywanie szablonu Usługi Resource Manager w usłudze Azure Storage

Teraz używamy programu PowerShell do utworzenia udziału `TemplateTest.json` plików usługi Azure Storage i przekazania pliku.
Aby uzyskać instrukcje dotyczące tworzenia udziału plików i przekazywania pliku w witrynie Azure portal, zobacz Wprowadzenie do [magazynu plików platformy Azure w systemie Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Uruchom program PowerShell na komputerze lokalnym i uruchom następujące polecenia, aby utworzyć udział plików i przekazać szablon Menedżera zasobów do tego udziału plików.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Tworzenie skryptu śmiętu programu PowerShell

Teraz tworzymy skrypt programu PowerShell, który pobiera `TemplateTest.json` plik z usługi Azure Storage i wdraża szablon, aby utworzyć nowe konto usługi Azure Storage.

W edytorze tekstu wklej następujący tekst:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Zapisz plik lokalnie `DeployTemplate.ps1`jako .

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importowanie i publikowanie podręcznika runbooka na koncie usługi Azure Automation

Teraz używamy programu PowerShell do zaimportowania życiornika do konta usługi Azure Automation, a następnie opublikowania systemu runbook.
Aby uzyskać informacje dotyczące importowania i publikowania likwidu w witrynie Azure Portal, zobacz [Zarządzanie podręcznikami runbook w usłudze Azure Automation](manage-runbooks.md).

Aby `DeployTemplate.ps1` zaimportować do konta automatyzacji jako system runbook programu PowerShell, uruchom następujące polecenia programu PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Teraz uruchamiamy księgę uruchominiczą, wywołując polecenie cmdlet [Start-AzureRmAutomationRunbook.](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)

Aby uzyskać informacje dotyczące uruchamiania uruchomieniu w witrynie Azure portal, zobacz [Uruchamianie uruchomieniu w usłudze Azure Automation.](automation-starting-a-runbook.md)

Uruchom następujące polecenia w konsoli programu PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Runbook działa i można sprawdzić jego `$job.Status`stan, uruchamiając program .

Zestaw runbook pobiera szablon Menedżera zasobów i używa go do wdrożenia nowego konta usługi Azure Storage.
Widać, że nowe konto magazynu zostało utworzone przez uruchomienie następującego polecenia:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Podsumowanie

Gotowe. Teraz możesz użyć usługi Azure Automation i usługi Azure Storage oraz szablonów Usługi Resource Manager, aby wdrożyć wszystkie zasoby platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o szablonach Usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md)
* Aby rozpocząć korzystanie z usługi Azure Storage, zobacz [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md).
* Aby znaleźć inne przydatne elementy runbook usługi Azure Automation, zobacz [Galeria żyli i galerii modułów dla usługi Azure Automation.](automation-runbook-gallery.md)
* Aby znaleźć inne przydatne szablony Menedżera zasobów, zobacz [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/)


