---
title: Wdrażanie szablonu usługi Azure Resource Manager w element runbook usługi Automatyzacja Azure
description: Wdrażanie szablonu usługi Azure Resource Manager przechowywane w usłudze Azure Storage z poziomu elementu runbook
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
keywords: środowiska PowerShell, elementu runbook, json, usługi Automatyzacja azure
ms.openlocfilehash: b6571b87e5753adcd03d7916218ed5a085fb6f82
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Wdrażanie szablonu usługi Azure Resource Manager w elemencie runbook programu PowerShell usługi Azure Automation

Można napisać [runbook automatyzacji Azure w programie PowerShell](automation-first-runbook-textual-powershell.md) która wdraża zasobów platformy Azure przy użyciu [szablonu usługi Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

W ten sposób można zautomatyzować wdrożenie zasobów platformy Azure. Możesz zachować szablonów usługi Resource Manager w lokalizacji centralnej, bezpieczne, takie jak magazyn Azure.

W tym temacie, utworzymy runbook programu PowerShell, która używa szablonu usługi Resource Manager przechowywane w [usługi Azure Storage](../storage/common/storage-introduction.md) do wdrożenia nowego konta magazynu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub <a href="/pricing/free-account/" target="_blank">[utworzyć bezpłatne konto](https://azure.microsoft.com/free/).
* [Konto usługi Automation](automation-sec-configure-azure-runas-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* [Konto usługi Azure Storage](../storage/common/storage-create-storage-account.md) do przechowywania szablonu usługi Resource Manager
* Program Azure Powershell jest zainstalowane na komputerze lokalnym. Zobacz [Instalowanie i konfigurowanie programu Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) informacji o sposobie pobrania programu Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

W tym przykładzie używamy szablonu usługi Resource Manager, która wdraża nowe konto magazynu Azure.

W edytorze tekstów skopiuj następujący tekst:

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
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
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

Zapisz plik lokalnie jako `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Zapisywanie szablonu usługi Resource Manager w usłudze Azure Storage

Teraz używamy środowiska PowerShell utworzyć udziału plików magazynu Azure i przekazać `TemplateTest.json` pliku.
Aby uzyskać instrukcje dotyczące sposobu tworzenia pliku udziału i przekazywanie pliku w portalu Azure, zobacz [Rozpoczynanie pracy z magazynem plików Azure w systemie Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Uruchom program PowerShell na komputerze lokalnym, a następnie uruchom następujące polecenia, aby utworzyć udział plików i przekazać szablonu usługi Resource Manager do tego udziału plików.

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

## <a name="create-the-powershell-runbook-script"></a>Utwórz skrypt programu PowerShell elementu runbook

Teraz utworzymy skrypt programu PowerShell, który pobiera `TemplateTest.json` pliku z magazynu Azure i wdraża szablon, aby utworzyć nowe konto magazynu Azure.

W edytorze tekstów wklej następujący tekst:

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

Zapisz plik lokalnie jako `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importowanie i opublikować elementu runbook do Twojego konta usługi Automatyzacja Azure

Teraz możemy zaimportuj element runbook do Twojego konta usługi Automatyzacja Azure przy użyciu programu PowerShell, a następnie opublikować elementu runbook.
Aby uzyskać informacje o sposobie importowania i opublikować element runbook w portalu Azure, zobacz [Tworzenie lub importowanie elementu runbook automatyzacji Azure](automation-creating-importing-runbook.md).

Aby zaimportować `DeployTemplate.ps1` do Twojego konta automatyzacji jako elementu runbook programu PowerShell, uruchom następujące polecenia programu PowerShell:

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

Teraz możemy uruchamiania elementu runbook przez wywołanie metody [Start AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0) polecenia cmdlet.

Aby uzyskać informacje o sposobie uruchamiania elementu runbook w portalu Azure, zobacz [uruchamianie elementu runbook automatyzacji Azure](automation-starting-a-runbook.md).

W konsoli programu PowerShell, uruchom następujące polecenia:

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

Element runbook zostanie uruchomiony, i sprawdź jej stan, uruchamiając `$job.Status`.

Element runbook pobiera szablonu usługi Resource Manager i używa go do wdrożenia nowego konta magazynu Azure.
Możesz sprawdzić, czy nowe konto magazynu został utworzony za pomocą następującego polecenia:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Podsumowanie

Gotowe. Teraz można użyć szablonów usługi Automatyzacja Azure i usługi Azure Storage, a Menedżer zasobów do wdrożenia wszystkich zasobów na platformie Azure.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat szablonów Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* Aby rozpocząć pracę z usługą Azure Storage, zobacz [wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md).
* Aby znaleźć inne przydatne elementy runbook automatyzacji Azure, zobacz [galerie elementów Runbook i modułów w automatyzacji Azure](automation-runbook-gallery.md).
* Aby znaleźć inne przydatne szablony Menedżera zasobów, zobacz [szablonów Szybki Start Azure](https://azure.microsoft.com/resources/templates/)

