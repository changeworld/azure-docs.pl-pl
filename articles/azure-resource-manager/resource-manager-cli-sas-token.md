---
title: Wdrażanie szablonu platformy Azure z tokenu sygnatury dostępu Współdzielonego i interfejsu wiersza polecenia Azure | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i interfejsu wiersza polecenia Azure, aby wdrożyć zasobów na platformie Azure z szablonu, który jest chroniony za pomocą tokenu sygnatury dostępu Współdzielonego.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: b97fdea36eab914926ff91e1f27bdab0fb8a3930
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Wdrażanie prywatnej szablonu usługi Resource Manager z tokenu sygnatury dostępu Współdzielonego i wiersza polecenia platformy Azure

Gdy w szablonie znajduje się na koncie magazynu, można ograniczyć dostęp do szablonu, a także dostarczają token sygnatury dostępu Współdzielonego dostępu współdzielonego podczas wdrażania. W tym temacie wyjaśniono, jak przy użyciu programu Azure PowerShell z szablonami usługi Resource Manager zapewnienie tokenu sygnatury dostępu Współdzielonego podczas wdrażania. 

## <a name="add-private-template-to-storage-account"></a>Dodaj szablon prywatnych do konta magazynu

Szablony można dodać do konta magazynu i link do ich podczas wdrażania z tokenem sygnatury dostępu Współdzielonego.

> [!IMPORTANT]
> Wykonując poniższe kroki, obiektu blob zawierającego szablon jest dostępny tylko dla właściciela konta. Podczas tworzenia tokenu SAS obiektu blob obiektu blob jest jednak dostępne dla wszystkich użytkowników z tym identyfikatorem URI. Jeśli inny użytkownik przechwytuje identyfikator URI, ten użytkownik jest w stanie uzyskać dostęp do szablonu. Za pomocą tokenu sygnatury dostępu Współdzielonego jest dobrym sposobem ograniczenia dostępu do szablonów, ale nie może zawierać dane poufne, takie jak hasła, bezpośrednio w szablonie.
> 
> 

Poniższy przykład ustawia kontener konta magazynu prywatnych i przekazuje szablonu:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Podaj token sygnatury dostępu Współdzielonego podczas wdrażania
Aby wdrożyć szablon prywatnych na koncie magazynu, wygenerowania tokenu sygnatury dostępu Współdzielonego i dołączyć go w identyfikatorze URI dla szablonu. Ustawianie czasu wygaśnięcia poczekać na ukończenie wdrożenia.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Na przykład za pomocą tokenu sygnatury dostępu Współdzielonego przy użyciu szablonów połączonych, zobacz [za pomocą szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do wdrażania szablonów, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](resource-group-template-deploy-cli.md).
* Zakończenie przykładowego skryptu, który wdraża szablonu, zobacz [skryptu szablonu wdrażania Menedżera zasobów](resource-manager-samples-cli-deploy.md)
* Aby określić parametry w szablonie, zobacz [tworzenia szablonów](resource-group-authoring-templates.md#parameters).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
