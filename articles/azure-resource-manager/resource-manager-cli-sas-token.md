---
title: Wdrażanie szablonu platformy Azure przy użyciu tokenu sygnatury dostępu Współdzielonego i wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure do wdrażania zasobów platformy Azure z tego szablonu, który jest chroniony za pomocą tokenu sygnatury dostępu Współdzielonego.
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
ms.openlocfilehash: c869b76a0d1ba10bc27aefa60cbe4ed5b8d8201a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061362"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Wdrażanie prywatnego szablonu usługi Resource Manager przy użyciu tokenu sygnatury dostępu Współdzielonego i wiersza polecenia platformy Azure

Gdy szablon znajduje się na koncie magazynu, można ograniczyć dostęp do szablonu, a także dostarczają token sygnatury (SAS) dostępu współdzielonego podczas wdrażania. W tym temacie wyjaśniono, jak użyć programu Azure PowerShell przy użyciu szablonów usługi Resource Manager, aby zapewnić tokenu sygnatury dostępu Współdzielonego podczas wdrażania. 

## <a name="add-private-template-to-storage-account"></a>Dodaj szablon prywatnych do konta magazynu

Szablony można dodać do konta magazynu i link do ich podczas wdrażania przy użyciu tokenu sygnatury dostępu Współdzielonego.

> [!IMPORTANT]
> Wykonując poniższe kroki, obiektu blob zawierającego szablon jest dostępny tylko właściciel konta. Jednak podczas tworzenia tokenu sygnatury dostępu Współdzielonego dla obiektu blob, obiekt blob jest dostępna dla wszystkich odbiorców za pomocą tego identyfikatora URI. Jeśli inny użytkownik przechwytuje identyfikatora URI, ten użytkownik będzie mógł uzyskiwać dostęp do szablonu. Przy użyciu tokenu sygnatury dostępu Współdzielonego jest dobrym sposobem ograniczenia dostępu do szablonów, ale nie może zawierać dane poufne, takie jak hasła, bezpośrednio w szablonie.
> 
> 

Poniższy przykład ustawia kontenera konta magazynu prywatnego i przekazuje szablonu:
   
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
Aby wdrożyć szablon prywatne w ramach konta magazynu, wygeneruj token sygnatury dostępu Współdzielonego, a następnie dołącz ją w identyfikatorze URI dla szablonu. Ustaw czas wygaśnięcia, aby umożliwić wystarczająco dużo czasu na ukończenie wdrożenia.
   
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

Przykład za pomocą tokenu sygnatury dostępu Współdzielonego z połączonymi szablonami, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do wdrażania szablonów, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](resource-group-template-deploy-cli.md).
* Aby uzyskać kompletny przykładowy skrypt, który służy do wdrażania szablonu, zobacz [skryptu szablonu wdrażania usługi Resource Manager](resource-manager-samples-cli-deploy.md)
* Aby zdefiniować parametry w szablonie, zobacz [Tworzenie szablonów](resource-group-authoring-templates.md#parameters).
