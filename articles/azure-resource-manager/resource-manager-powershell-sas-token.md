---
title: Wdrażanie szablonu platformy Azure przy użyciu tokenu sygnatury dostępu Współdzielonego i programu PowerShell | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i programu Azure PowerShell do wdrażania zasobów platformy Azure z tego szablonu, który jest chroniony za pomocą tokenu sygnatury dostępu Współdzielonego.
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
ms.openlocfilehash: 818aa63ced56d7cf382536f10bb6150199ab74e9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66128431"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Wdrażanie prywatnego szablonu usługi Resource Manager przy użyciu tokenu sygnatury dostępu Współdzielonego i programu Azure PowerShell

Gdy szablon znajduje się na koncie magazynu, można ograniczyć dostęp do szablonu, a także dostarczają token sygnatury (SAS) dostępu współdzielonego podczas wdrażania. W tym temacie wyjaśniono, jak użyć programu Azure PowerShell przy użyciu szablonów usługi Resource Manager, aby zapewnić tokenu sygnatury dostępu Współdzielonego podczas wdrażania. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-private-template-to-storage-account"></a>Dodaj szablon prywatnych do konta magazynu

Szablony można dodać do konta magazynu i link do ich podczas wdrażania przy użyciu tokenu sygnatury dostępu Współdzielonego.

> [!IMPORTANT]
> Wykonując poniższe kroki, obiektu blob zawierającego szablon jest dostępny tylko właściciel konta. Jednak podczas tworzenia tokenu sygnatury dostępu Współdzielonego dla obiektu blob, obiekt blob jest dostępna dla wszystkich odbiorców za pomocą tego identyfikatora URI. Jeśli inny użytkownik przechwytuje identyfikatora URI, ten użytkownik będzie mógł uzyskiwać dostęp do szablonu. Przy użyciu tokenu sygnatury dostępu Współdzielonego jest dobrym sposobem ograniczenia dostępu do szablonów, ale nie może zawierać dane poufne, takie jak hasła, bezpośrednio w szablonie.
> 
> 

Poniższy przykład ustawia kontenera konta magazynu prywatnego i przekazuje szablonu:
   
```powershell
# create a storage account for templates
New-AzResourceGroup -Name ManageGroup -Location "South Central US"
New-AzStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzStorageContainer -Name templates -Permission Off
Set-AzStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Podaj token sygnatury dostępu Współdzielonego podczas wdrażania
Aby wdrożyć szablon prywatne w ramach konta magazynu, wygeneruj token sygnatury dostępu Współdzielonego, a następnie dołącz ją w identyfikatorze URI dla szablonu. Ustaw czas wygaśnięcia, aby umożliwić wystarczająco dużo czasu na ukończenie wdrożenia.
   
```powershell
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Przykład za pomocą tokenu sygnatury dostępu Współdzielonego z połączonymi szablonami, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do wdrażania szablonów, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](resource-group-template-deploy.md).
* Aby uzyskać kompletny przykładowy skrypt, który służy do wdrażania szablonu, zobacz [skryptu szablonu wdrażania usługi Resource Manager](resource-manager-samples-powershell-deploy.md)
* Aby zdefiniować parametry w szablonie, zobacz [Tworzenie szablonów](resource-group-authoring-templates.md#parameters).
