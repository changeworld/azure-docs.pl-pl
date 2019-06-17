---
title: Wdrażanie szablonu usługi Azure Resource Manager w elemencie runbook usługi Azure Automation
description: Wdrażanie szablonu usługi Azure Resource Manager przechowywane w usłudze Azure Storage z poziomu elementu runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: Program PowerShell, elementów runbook, json, usługa azure automation
ms.openlocfilehash: 2008ba697665baa0e8cf73564ec31d6267425404
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61073750"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Wdrażanie szablonu usługi Azure Resource Manager w elemencie runbook programu PowerShell usługi Azure Automation

Można napisać [elementu runbook usługi Azure Automation PowerShell](automation-first-runbook-textual-powershell.md) , służy do wdrażania zasobów platformy Azure przy użyciu [szablonu usługi Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Dzięki temu można zautomatyzować wdrażanie zasobów platformy Azure. Można zachować szablonów usługi Resource Manager w lokalizacji centralnej i bezpieczną takich jak Azure Storage.

W tym artykule, możemy utworzyć element runbook programu PowerShell, który używa szablonu usługi Resource Manager, przechowywane w [usługi Azure Storage](../storage/common/storage-introduction.md) do wdrożenia nowego konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze, możesz to zrobić [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub [Załóż bezpłatne konto](https://azure.microsoft.com/free/).
* [Konto usługi Automation](automation-sec-configure-azure-runas-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* [Konto usługi Azure Storage](../storage/common/storage-create-storage-account.md) w którym będzie przechowywany szablon usługi Resource Manager
* Program Azure Powershell zainstalowane na komputerze lokalnym. Zobacz [Instalowanie i konfigurowanie programu Azure Powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) uzyskać informacji na temat sposobu uzyskania programu Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

W tym przykładzie używamy szablonu usługi Resource Manager, która wdraża nowe konto usługi Azure Storage.

W edytorze tekstów Skopiuj poniższy tekst:

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

Zapisz plik lokalnie jako `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Zapisz szablon usługi Resource Manager w usłudze Azure Storage

Teraz możemy przy użyciu programu PowerShell Utwórz udział plików usługi Azure Storage i przekaż `TemplateTest.json` pliku.
Aby uzyskać instrukcje dotyczące sposobu tworzenia pliku udostępnianie i przekazać plik w witrynie Azure portal, zobacz [Rozpoczynanie pracy z usługą Azure File storage w Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Uruchom program PowerShell na komputerze lokalnym, a następnie uruchom następujące polecenia, aby utworzyć udział plików, a następnie Przekaż szablon usługi Resource Manager do tego udziału plików.

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

## <a name="create-the-powershell-runbook-script"></a>Utwórz skrypt elementu runbook programu PowerShell

Teraz możemy utworzyć skrypt programu PowerShell, który pobiera `TemplateTest.json` pliku z usługi Azure Storage i służy do wdrażania szablonu Aby utworzyć nowe konto usługi Azure Storage.

W edytorze tekstu Wklej następujący tekst:

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

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importowanie i publikowanie elementu runbook na Twoim koncie usługi Azure Automation

Teraz możemy zaimportować elementu runbook do konta usługi Azure Automation za pomocą programu PowerShell, a następnie opublikować element runbook.
Aby uzyskać informacje o tym, jak zaimportować i opublikować element runbook w witrynie Azure portal, zobacz [Zarządzanie elementami runbook w usłudze Azure Automation](manage-runbooks.md).

Aby zaimportować `DeployTemplate.ps1` do konta usługi Automation, jako elementu runbook programu PowerShell, uruchom następujące polecenia programu PowerShell:

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

Teraz możemy uruchomić element runbook, wywołując [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) polecenia cmdlet.

Aby uzyskać informacji o sposobie uruchamiania elementu runbook w witrynie Azure portal, zobacz [uruchamianie elementu runbook w usłudze Azure Automation](automation-starting-a-runbook.md).

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

Element runbook działa, a jej stan można sprawdzić, uruchamiając `$job.Status`.

Element runbook pobiera szablon usługi Resource Manager i używa go do wdrożenia nowego konta usługi Azure Storage.
Możesz zobaczyć, że utworzono nowe konto magazynu, uruchamiając następujące polecenie:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Podsumowanie

To wszystko! Teraz można użyć szablonów usługi Azure Automation i Azure Storage i usługi Resource Manager do wdrażania wszystkich zasobów platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat szablonów usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* Aby rozpocząć pracę z usługą Azure Storage, zobacz [wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md).
* Aby znaleźć inne przydatne elementy runbook usługi Azure Automation, zobacz [elementów Runbook i modułów galeriach, aby usługa Azure Automation](automation-runbook-gallery.md).
* Aby znaleźć inne przydatne szablonów usługi Resource Manager, zobacz [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/)


