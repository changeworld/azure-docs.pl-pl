---
title: Wdrażanie szablonu platformy Azure z tokenu sygnatury dostępu Współdzielonego i programu PowerShell | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i programu Azure PowerShell, aby wdrożyć zasobów na platformie Azure z szablonu, który jest chroniony za pomocą tokenu sygnatury dostępu Współdzielonego.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 2dbf7f9ac5a735ec0c70f4daefa721509212a84b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Wdrażanie prywatnej szablonu usługi Resource Manager z tokenu sygnatury dostępu Współdzielonego i programu Azure PowerShell

Gdy w szablonie znajduje się na koncie magazynu, można ograniczyć dostęp do szablonu, a także dostarczają token sygnatury dostępu Współdzielonego dostępu współdzielonego podczas wdrażania. W tym temacie wyjaśniono, jak przy użyciu programu Azure PowerShell z szablonami usługi Resource Manager zapewnienie tokenu sygnatury dostępu Współdzielonego podczas wdrażania. 

## <a name="add-private-template-to-storage-account"></a>Dodaj szablon prywatnych do konta magazynu

Szablony można dodać do konta magazynu i link do ich podczas wdrażania z tokenem sygnatury dostępu Współdzielonego.

> [!IMPORTANT]
> Wykonując poniższe kroki, obiektu blob zawierającego szablon jest dostępny tylko dla właściciela konta. Podczas tworzenia tokenu SAS obiektu blob obiektu blob jest jednak dostępne dla wszystkich użytkowników z tym identyfikatorem URI. Jeśli inny użytkownik przechwytuje identyfikator URI, ten użytkownik jest w stanie uzyskać dostęp do szablonu. Za pomocą tokenu sygnatury dostępu Współdzielonego jest dobrym sposobem ograniczenia dostępu do szablonów, ale nie może zawierać dane poufne, takie jak hasła, bezpośrednio w szablonie.
> 
> 

Poniższy przykład ustawia kontener konta magazynu prywatnych i przekazuje szablonu:
   
```powershell
# create a storage account for templates
New-AzureRmResourceGroup -Name ManageGroup -Location "South Central US"
New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Podaj token sygnatury dostępu Współdzielonego podczas wdrażania
Aby wdrożyć szablon prywatnych na koncie magazynu, wygenerowania tokenu sygnatury dostępu Współdzielonego i dołączyć go w identyfikatorze URI dla szablonu. Ustawianie czasu wygaśnięcia poczekać na ukończenie wdrożenia.
   
```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Na przykład za pomocą tokenu sygnatury dostępu Współdzielonego przy użyciu szablonów połączonych, zobacz [za pomocą szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do wdrażania szablonów, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](resource-group-template-deploy.md).
* Zakończenie przykładowego skryptu, który wdraża szablonu, zobacz [skryptu szablonu wdrażania Menedżera zasobów](resource-manager-samples-powershell-deploy.md)
* Aby określić parametry w szablonie, zobacz [tworzenia szablonów](resource-group-authoring-templates.md#parameters).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

