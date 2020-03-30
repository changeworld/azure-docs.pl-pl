---
title: Bezpieczne wdrażanie szablonu za pomocą tokenu sygnatury dostępu Współdzielonego
description: Wdrażanie zasobów na platformie Azure za pomocą szablonu usługi Azure Resource Manager, który jest chroniony przez token sygnatury dostępu Współdzielonego. Pokazuje platformę Azure PowerShell i platformę Azure CLI.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156399"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Wdrażanie prywatnego szablonu ARM za pomocą tokenu Sygnatury dostępu Współdzielonego

Gdy szablon usługi Azure Resource Manager (ARM) znajduje się na koncie magazynu, można ograniczyć dostęp do szablonu, aby uniknąć uwidaczniania go publicznie. Dostęp do zabezpieczonego szablonu można uzyskać, tworząc token podpisu dostępu współdzielonego (SAS) dla szablonu i udostępniając go tokenu podczas wdrażania. W tym artykule wyjaśniono, jak używać narzędzia Azure PowerShell lub interfejsu wiersza polecenia platformy Azure do wdrażania szablonu z tokenem sygnatury dostępu Współdzielonego.

## <a name="create-storage-account-with-secured-container"></a>Tworzenie konta magazynu z zabezpieczonym kontenerem

Poniższy skrypt tworzy konto magazynu i kontener z wyłączonym dostępem publicznym.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Przekaż szablon do konta magazynu

Teraz możesz przesłać szablon na konto magazynu. Podaj ścieżkę do szablonu, którego chcesz użyć.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Podaj token sygnatury dostępu Współdzielonego podczas wdrażania

Aby wdrożyć szablon prywatny na koncie magazynu, wygeneruj token sygnatury dostępu Współdzielonego i dołącz go do identyfikatora URI dla szablonu. Ustaw czas wygaśnięcia, aby dać wystarczająco dużo czasu na ukończenie wdrożenia.

> [!IMPORTANT]
> Obiekt blob zawierający szablon jest dostępny tylko dla właściciela konta. Jednak podczas tworzenia tokenu sygnatury dostępu Współdzielonego dla obiektu blob obiekt jest dostępny dla każdego, kto ma ten identyfikator URI. Jeśli inny użytkownik przechwytuje identyfikator URI, ten użytkownik może uzyskać dostęp do szablonu. Token Sygnatury dostępu Współdzielonego jest dobrym sposobem ograniczenia dostępu do szablonów, ale nie należy dołączać poufnych danych, takich jak hasła bezpośrednio w szablonie.
>

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Na przykład przy użyciu tokenu sygnatury dostępu Współdzielonego z szablonami połączonymi zobacz [Używanie połączonych szablonów z usługą Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Następne kroki
* Aby zapoznać się z wprowadzeniem do wdrażania szablonów, zobacz [Wdrażanie zasobów za pomocą szablonów ARM i programu Azure PowerShell](deploy-powershell.md).
* Aby zdefiniować parametry w szablonie, zobacz [Szablony tworzenia](template-syntax.md#parameters).
