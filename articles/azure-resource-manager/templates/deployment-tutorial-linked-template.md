---
title: Samouczek — wdrażanie połączonego szablonu
description: Dowiedz się, jak wdrożyć szablon połączony
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 70a09315b0947f41e7602e630460cb3e674a7bf8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082244"
---
# <a name="tutorial-deploy-a-linked-template"></a>Samouczek: Wdrażanie połączonego szablonu

W [poprzednich samouczkach](./deployment-tutorial-local-template.md)opisano, jak wdrożyć szablon przechowywany na komputerze lokalnym. Aby wdrożyć złożone rozwiązania, można podzielić szablon na wiele szablonów i wdrożyć te szablony za pomocą szablonu głównego. W tym samouczku dowiesz się, jak wdrożyć szablon główny, który zawiera odwołanie do szablonu połączonego. Po wdrożeniu szablonu głównego wyzwala wdrożenie połączonego szablonu. Dowiesz się również, jak przechowywać i zabezpieczać połączony szablon przy użyciu tokenu sygnatury dostępu Współdzielonego. Trwa około **12 minut.**

## <a name="prerequisites"></a>Wymagania wstępne

Zaleca się ukończenie poprzedniego samouczka, ale nie jest wymagane.

## <a name="review-template"></a>Szablon recenzji

W poprzednich samouczkach można wdrożyć szablon, który tworzy konto magazynu, plan usługi App Service i aplikacji sieci web. Użyty szablon to:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Tworzenie szablonu połączonego

Zasób konta magazynu można rozdzielić na połączony szablon:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Poniższy szablon jest szablonem głównym.  Wyróżniony obiekt **Microsoft.Resources/deployments** pokazuje, jak wywołać szablon połączony. Połączony szablon nie może być przechowywany jako plik lokalny lub plik, który jest dostępny tylko w sieci lokalnej. Można podać tylko wartość URI, która zawiera *http* lub *https*. Menedżer zasobów musi mieć dostęp do szablonu. Jedną z opcji jest umieszczenie połączonego szablonu na koncie magazynu i użycie identyfikatora URI dla tego elementu. Identyfikator URI jest przekazywany do szablonu przy użyciu parametru. Zobacz definicję podświetlonych parametrów.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Zapisz kopię szablonu głównego na komputerze lokalnym z rozszerzeniem .json, na przykład azuredeploy.json. Nie trzeba zapisywać kopii połączonego szablonu.  Połączony szablon zostanie skopiowany z repozytorium GitHub na konto magazynu.

## <a name="store-the-linked-template"></a>Przechowywanie połączonego szablonu

Poniższy skrypt programu PowerShell tworzy konto magazynu, tworzy kontener i kopiuje połączony szablon z repozytorium GitHub do kontenera. Kopia połączonego szablonu jest przechowywana w [usłudze GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Wybierz **pozycję Wypróbuj,** aby otworzyć powłokę chmury, wybierz **pozycję Kopiuj,** aby skopiować skrypt programu PowerShell, a następnie kliknij prawym przyciskiem myszy okienko powłoki, aby wkleić skrypt:

> [!IMPORTANT]
> Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i używać tylko cyfr i mniejszych liter. Nazwa musi być unikatowa. W szablonie nazwa konta magazynu jest nazwą projektu z dołączenym "magazynem", a nazwa projektu musi zawierać od 3 do 11 znaków. Dlatego nazwa projektu musi spełniać wymagania dotyczące nazwy konta magazynu i ma mniej niż 11 znaków.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Wdrażanie szablonu

Aby wdrożyć szablon prywatny na koncie magazynu, wygeneruj token sygnatury dostępu Współdzielonego i dołącz go do identyfikatora URI dla szablonu. Ustaw czas wygaśnięcia, aby dać wystarczająco dużo czasu na ukończenie wdrożenia. Obiekt blob zawierający szablon jest dostępny tylko dla właściciela konta. Jednak podczas tworzenia tokenu sygnatury dostępu Współdzielonego dla obiektu blob obiekt jest dostępny dla każdego, kto ma ten identyfikator URI. Jeśli inny użytkownik przechwytuje identyfikator URI, ten użytkownik może uzyskać dostęp do szablonu. Token Sygnatury dostępu Współdzielonego jest dobrym sposobem ograniczenia dostępu do szablonów, ale nie należy dołączać poufnych danych, takich jak hasła bezpośrednio w szablonie.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](./deployment-tutorial-local-template.md#create-resource-group).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wyczyść zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Dowiedzialiście się, jak wdrożyć połączony szablon. W następnym samouczku dowiesz się, jak utworzyć potok DevOp, aby wdrożyć szablon.

> [!div class="nextstepaction"]
> [Tworzenie potoku](./deployment-tutorial-pipeline.md)
