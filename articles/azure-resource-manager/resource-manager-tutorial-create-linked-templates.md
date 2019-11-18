---
title: Tworzenie połączonych szablonów
description: Dowiedz się, jak tworzyć połączone szablony usługi Azure Resource Manager na potrzeby tworzenia maszyny wirtualnej
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7caae8f749cf89832740f9b5f71f6a8931a835c9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149320"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Samouczek: tworzenie połączonych szablonów usługi Azure Resource Manager

Dowiedz się, jak tworzyć połączone szablony usługi Azure Resource Manager. Użycie połączonych szablonów umożliwia wywołanie szablonu przez inny szablon. Jest to idealne narzędzie do podziału szablonów na moduły. W tym samouczku użyjesz tego samego szablonu, który jest używany w [samouczku: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md), które tworzą maszynę wirtualną, sieć wirtualną i inne zależne zasoby, w tym konto magazynu. Tworzenie zasobu konta magazynu jest realizowane przez połączony szablon.

Wywołanie połączonego szablonu jest podobne do tworzenia wywołania funkcji.  Dowiesz się również, jak przekazać wartości parametrów do połączonego szablonu i jak uzyskać "wartości zwracane" z połączonego szablonu.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Tworzenie połączonego szablonu
> * Przekazywanie połączonego szablonu
> * Łączenie z połączonym szablonem
> * Konfigurowanie zależności
> * Wdrożenie szablonu
> * Dodatkowe rozwiązania

Aby uzyskać więcej informacji, zobacz [Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure](./resource-group-linked-templates.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem [Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Ten sam szablon jest używany w [samouczku: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Zapisz dwie kopie tego samego szablonu, który ma być używany w następujących celach:

* **Główny szablon**: tworzenie wszystkich zasobów, z wyjątkiem konta magazynu.
* **Połączony szablon**: tworzenie konta magazynu.

1. W programie Visual Studio Code wybierz pozycję **File (Plik)** >**Open File (Otwórz plik)** .
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)** , aby otworzyć plik.
4. Istnieje pięć zasobów definiowanych przez szablon:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     Warto zapoznać się z podstawową wiedzą na temat schematu szablonu przed przystąpieniem do dostosowywania szablonu.
5. Wybierz pozycję **Plik**>**Zapisz jako**, aby zapisać kopię pliku o nazwie **azuredeploy.json** na komputerze lokalnym.
6. Wybierz pozycję **Plik**>**Zapisz jako**, aby utworzyć kolejną kopię pliku o nazwie **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Tworzenie połączonego szablonu

Połączony szablon tworzy konto magazynu. Połączony szablon może służyć jako szablon autonomiczny do tworzenia konta magazynu. W tym samouczku połączony szablon przyjmuje dwa parametry i przekazuje wartość z powrotem do głównego szablonu. Ta wartość "return" jest definiowana w elemencie `outputs`.

1. Otwórz **linkedTemplate. JSON** w Visual Studio Code, jeśli plik nie jest otwarty.
2. Wprowadź następujące zmiany:

    * Usuń wszystkie parametry inne niż **Lokalizacja**.
    * Dodaj parametr o nazwie **storageAccountName**.
        ```json
        "storageAccountName":{
          "type": "string",
          "metadata": {
              "description": "Azure Storage account name."
          }
        },
        ```
        Nazwa i lokalizacja konta magazynu są przesyłane z szablonu głównego do połączonego szablonu jako parametry.

    * Usuń element **variables** i wszystkie definicje zmiennych.
    * Usuń wszystkie zasoby inne niż konto magazynu. Łącznie należy usunąć cztery zasoby.
    * Zaktualizuj wartość elementu **name** zasobu konta magazynu na następującą wartość:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Zaktualizuj element **outputs**, aby wyglądał następująco:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```
       Element **storageUri** jest wymagany przez definicję zasobu maszyny wirtualnej w głównym szablonie.  Wartość jest przekazywana z powrotem do głównego szablonu jako wartość wyjściowa.

        Gdy wszystko będzie gotowe, szablon powinien wyglądać następująco:

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
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
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```
3. Zapisz zmiany.

## <a name="upload-the-linked-template"></a>Przekazywanie połączonego szablonu

Szablon główny i połączony muszą być dostępne z miejsca, gdzie jest uruchamiane wdrożenie. W tym samouczku użyto metody wdrażania usługi Cloud Shell, która została użyta w [samouczku: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Główny szablon (azuredeploy.json) jest przekazywany do powłoki. Połączony szablon (linkedTemplate.json) musi być gdzieś bezpiecznie udostępniony. Poniższy skrypt programu PowerShell tworzy konto usługi Azure Storage, przekazuje szablon do tego konta magazynu, a następnie generuje token SAS w celu udzielenia ograniczonego dostępu do pliku szablonu. Aby uprościć samouczek, skrypt pobiera ukończony połączony szablon z repozytorium GitHub. Jeśli chcesz skorzystać z połączonego szablonu, który został utworzony, możesz użyć usługi [Cloud Shell](https://shell.azure.com) w celu przekazania połączonego szablonu, a następnie zmodyfikować skrypt na potrzeby korzystania z własnego połączonego szablonu.

> [!NOTE]
> Skrypt ogranicza czas użycia tokenu SAS do ośmiu godzin. Jeśli potrzebujesz więcej czasu na ukończenie tego samouczka, zwiększ wartość czasu wygaśnięcia.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
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
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. Wybierz zielony przycisk **Wypróbuj**, aby otworzyć okienko usługi Azure Cloud Shell.
2. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy wewnątrz okienka powłoki (z granatowym tłem), a następnie wybierz polecenie **Wklej**.
4. Zanotuj dwie wartości (nazwę grupy zasobów i identyfikator URI połączonego szablonu) znajdujące się w dolnej części okienka powłoki. Będą one potrzebne w dalszej części tego samouczka.
5. Wybierz pozycję **Wyjdź z trybu koncentracji uwagi**, aby zamknąć okienko powłoki.

W praktyce token SAS jest generowany podczas wdrażania głównego szablonu. Dla tego tokenu jest też ustawiany mniejszy okres czasu wygaśnięcia, aby był on bardziej bezpieczny. Aby uzyskać więcej informacji, zobacz [Udostępnianie tokenu SAS podczas wdrażania](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Wywoływanie połączonego szablonu

Główny szablonu ma nazwę azuredeploy.json.

1. Otwórz plik **azuredeploy. JSON** w Visual Studio Code, jeśli nie jest otwarty.
2. Usuń z szablonu definicję zasobu konta magazynu:

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. Dodaj poniższy fragment kodu json w miejscu, gdzie była definicja konta magazynu:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Zwróć uwagę na następujące szczegóły:

    * Zasób `Microsoft.Resources/deployments` w głównym szablonie służy do łączenia się z innym szablonem.
    * Zasób `deployments` ma nazwę `linkedTemplate`. Ta nazwa jest używana do [konfigurowania zależności](#configure-dependency).
    * W przypadku wywoływania połączonych szablonów możesz używać tylko trybu wdrożenia [Incremental](./deployment-modes.md).
    * Parametr `templateLink/uri` zawiera identyfikator URI połączonego szablonu. Zaktualizuj tę wartość za pomocą identyfikatora URI uzyskanego podczas przekazywania połączonego szablonu (identyfikatora towarzyszącego tokenowi SAS).
    * Za pomocą właściwości `parameters` możesz przekazywać wartości z głównego szablonu do połączonego szablonu.
4. Upewnij się, że zaktualizowano wartość elementu `uri` za pomocą wartości uzyskanej podczas przekazywania połączonego szablonu (wartości towarzyszącej tokenowi SAS). W praktyce identyfikator URI jest przekazywany przy użyciu parametru.
5. Zapisz poprawiony szablon

## <a name="configure-dependency"></a>Konfigurowanie zależności

Odwołaj z [samouczka: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md), zasób maszyny wirtualnej zależy od konta magazynu:

![Diagram zależności szablonów usługi Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ponieważ konto magazynu jest teraz zdefiniowane w połączonym szablonie, musisz zaktualizować następujące dwa elementy zasobu `Microsoft.Compute/virtualMachines`.

* Skonfiguruj ponownie element `dependsOn`. Definicja konta magazynu została przeniesiona do połączonego szablonu.
* Skonfiguruj ponownie element `properties/diagnosticsProfile/bootDiagnostics/storageUri`. W sekcji [Tworzenie połączonego szablonu](#create-the-linked-template) została dodana wartość wyjściowa:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Ta wartość jest wymagana przez główny szablon.

1. Otwórz plik azuredeploy.json w programie Visual Studio Code, jeśli nie jest otwarty.
2. Rozwiń definicję zasobu maszyny wirtualnej i zaktualizuj element **dependsOn** w sposób pokazany na poniższym zrzucie ekranu:

    ![Konfigurowanie zależności połączonych szablonów usługi Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* to nazwa zasobu wdrożeń.
3. Zaktualizuj wartość **properties/diagnosticsProfile/bootDiagnostics/storageUri** w sposób pokazany na poprzednim zrzucie ekranu.
4. Zapisz poprawiony szablon.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Zapoznaj się sekcją [Wdrażanie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), gdzie znajduje się procedura wdrażania. Użyj tej samej nazwy grupy zasobów, której użyto dla konta magazynu, na potrzeby przechowywania połączonego szablonu. Ułatwi to wyczyszczenie zasobów w następnej sekcji. Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Zobacz [Wymagania wstępne](#prerequisites).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="additional-practice"></a>Dodatkowa wskazówka

Aby poprawić projekt, po jego ukończeniu wprowadź w nim następujące dodatkowe zmiany:

1. Zmodyfikuj szablon główny (azuredeploy.json), aby pobierał wartość identyfikatora URI połączonego szablonu za pośrednictwem parametru.
2. Zamiast generować token SAS podczas przekazywania połączonego szablonu, wygeneruj ten token podczas wdrażania szablonu głównego. Aby uzyskać więcej informacji, zobacz [Udostępnianie tokenu SAS podczas wdrażania](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wydzielono z szablonu dwa moduły: szablon główny i szablon połączony. Aby dowiedzieć się, jak wykonać zadania po wdrożeniu przy użyciu rozszerzenia maszyny wirtualnej, zobacz:

> [!div class="nextstepaction"]
> [Wdrażanie rozszerzeń maszyny wirtualnej](./resource-manager-tutorial-deploy-vm-extensions.md)
