---
title: Tworzenie połączonych Azure Resource Manager szablonów | Microsoft Docs
description: Dowiedz się, jak utworzyć połączone szablony Azure Resource Manager na potrzeby tworzenia maszyny wirtualnej
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 869e59aea9b78c44b1a920e58ecefab5e0ca4920
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169408"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Samouczek: Tworzenie połączonych Azure Resource Manager szablonów

Dowiedz się, jak tworzyć połączone Azure Resource Manager szablony. Przy użyciu połączonych szablonów można wywołać jeden szablon innego szablonu. Doskonale nadaje się do szablonów modularizing. W tym samouczku użyjesz tego samego szablonu, który jest używany w [samouczku: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md), które tworzą maszynę wirtualną, sieć wirtualną i inne zależne zasoby, w tym konto magazynu. Tworzenie zasobu konta magazynu można rozdzielić na połączony szablon.

Wywołanie połączonego szablonu jest podobne do tworzenia wywołania funkcji.  Dowiesz się również, jak przekazać wartości parametrów do połączonego szablonu i jak uzyskać "wartości zwracane" z połączonego szablonu.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwórz szablon szybkiego startu
> * Utwórz połączony szablon
> * Przekaż połączony szablon
> * Połącz z połączonym szablonem
> * Konfiguruj zależność
> * Wdróż szablon
> * Dodatkowe praktyki

Aby uzyskać więcej informacji, zobacz [Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure](./resource-group-linked-templates.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tego artykułu są potrzebne następujące czynności:

* [Visual Studio Code](https://code.visualstudio.com/) z [rozszerzeniem Menedżer zasobów Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, należy użyć wygenerowanego hasła dla konta administratora maszyny wirtualnej. Oto przykład generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault jest przeznaczony do ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integrowanie Azure Key Vault w Menedżer zasobów Template Deployment](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="open-a-quickstart-template"></a>Otwórz szablon szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium szablonów Menedżer zasobów. Zamiast tworzyć szablon od podstaw, można znaleźć przykładowy szablon i dostosować go. Szablon używany w tym samouczku jest nazywany [wdrożeniem prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Ten sam szablon jest używany w [samouczku: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Zapisujesz dwie kopie tego samego szablonu do użycia jako:

* **Główny szablon**: Utwórz wszystkie zasoby z wyjątkiem konta magazynu.
* **Połączony szablon**: Utwórz konto magazynu.

1. W obszarze Visual Studio Code wybierz pozycję **plik**>**Otwórz plik**.
2. W polu **Nazwa pliku**wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wybierz pozycję **Otwórz** , aby otworzyć plik.
4. Istnieje pięć zasobów zdefiniowanych przez szablon:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     Warto zapoznać się z podstawową wiedzą na temat schematu szablonu przed przystąpieniem do dostosowywania szablonu.
5. Wybierz pozycję **plik**>**Zapisz jako,** aby zapisać kopię pliku na komputerze lokalnym przy użyciu nazwy **azuredeploy. JSON**.
6. Wybierz pozycję **plik**>**Zapisz jako,** aby utworzyć kolejną kopię pliku o nazwie **linkedTemplate. JSON**.

## <a name="create-the-linked-template"></a>Utwórz połączony szablon

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

    * Usuń element Variables i wszystkie **definicje zmiennych.**
    * Usuń wszystkie zasoby inne niż konto magazynu. Łączna liczba czterech zasobów zostanie usunięta.
    * Zaktualizuj wartość elementu **name** zasobu konta magazynu, aby:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Zaktualizuj element Outputs, aby wyglądał następująco:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```
       **storageUri** jest wymagana przez definicję zasobu maszyny wirtualnej w szablonie głównym.  Wartość jest przekazywany z powrotem do głównego szablonu jako wartość wyjściowa.

        Gdy skończysz, szablon będzie wyglądać następująco:

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

## <a name="upload-the-linked-template"></a>Przekaż połączony szablon

Główny szablon i połączony szablon muszą być dostępne z lokalizacji, w której uruchomiono wdrożenie. W tym samouczku użyto metody wdrażania usługi Cloud Shell, która została użyta w [samouczku: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Główny szablon (azuredeploy. JSON) jest przekazywany do powłoki. Połączony szablon (linkedTemplate. JSON) musi być udostępniany w bezpiecznym miejscu. Poniższy skrypt programu PowerShell tworzy konto usługi Azure Storage, przekazuje szablon do konta magazynu, a następnie generuje token sygnatury dostępu współdzielonego w celu przyznania ograniczonego dostęp do pliku szablonu. Aby uprościć samouczek, skrypt pobiera ukończony połączony szablon z repozytorium GitHub. Jeśli chcesz użyć utworzonego szablonu połączonego, możesz użyć usługi [Cloud Shell](https://shell.azure.com) do przekazania połączonego szablonu, a następnie zmodyfikować skrypt tak, aby korzystał z własnego połączonego szablonu.

> [!NOTE]
> Skrypt ogranicza token sygnatury dostępu współdzielonego, który ma być używany w ciągu ośmiu godzin. Jeśli potrzebujesz więcej czasu na ukończenie tego samouczka, Zwiększ czas wygaśnięcia.

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

1. Wybierz przycisk **Wypróbuj** zielony, aby otworzyć okienko Azure Cloud Shell.
2. Wybierz pozycję **Kopiuj** , aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy w dowolnym miejscu wewnątrz okienka powłoki (granatowy, Niebieska część), a następnie wybierz **Wklej**.
4. Zanotuj dwie wartości (nazwa grupy zasobów i identyfikator URI połączonego szablonu) na końcu okienka powłoki. Potrzebujesz wartości w dalszej części samouczka.
5. Wybierz pozycję **Wyjdź z trybu koncentracji uwagi** , aby zamknąć okienko powłoki.

W ramach tej metody generujesz token sygnatury dostępu współdzielonego podczas wdrażania szablonu głównego i przyznaj token sygnatury dostępu współdzielonego, aby zwiększyć jego bezpieczeństwo. Aby uzyskać więcej informacji, zobacz [udostępnianie tokenu SAS podczas wdrażania](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Wywoływanie połączonego szablonu

Główny szablon ma nazwę azuredeploy. JSON.

1. Otwórz plik **azuredeploy. JSON** w Visual Studio Code, jeśli nie jest otwarty.
2. Usuń definicję zasobu konta magazynu z szablonu:

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
3. Dodaj następujący fragment kodu JSON do miejsca, w którym masz definicję konta magazynu:

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

    Zwróć uwagę na te szczegóły:

    * Zasób `Microsoft.Resources/deployments` w szablonie głównym jest używany do łączenia z innym szablonem.
    * Zasób `deployments` ma nazwę o nazwie `linkedTemplate`. Ta nazwa jest używana do [konfigurowania zależności](#configure-dependency).
    * Trybu wdrażania [przyrostowego](./deployment-modes.md) można używać tylko podczas wywoływania połączonych szablonów.
    * `templateLink/uri` zawiera identyfikator URI połączonego szablonu. Zaktualizuj wartość identyfikatora URI, który otrzymujesz podczas przekazywania połączonego szablonu (z tokenem SAS).
    * Użyj `parameters`, aby przekazać wartości z szablonu głównego do połączonego szablonu.
4. Upewnij się, że Zaktualizowano wartość `uri` elementu do wartości wprowadzonej podczas przekazywania połączonego szablonu (z tokenem SAS). W tym celu należy podać identyfikator URI przy użyciu parametru.
5. Zapisz zmieniony szablon

## <a name="configure-dependency"></a>Konfiguruj zależność

Odwołaj z [samouczka: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md), zasób maszyny wirtualnej zależy od konta magazynu:

![Diagram zależności szablonów Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ponieważ konto magazynu jest teraz zdefiniowane w połączonym szablonie, należy zaktualizować następujące dwa elementy zasobu `Microsoft.Compute/virtualMachines`.

* Skonfiguruj ponownie element `dependsOn`. Definicja konta magazynu jest przenoszona do połączonego szablonu.
* Skonfiguruj ponownie element `properties/diagnosticsProfile/bootDiagnostics/storageUri`. W obszarze [Utwórz połączony szablon](#create-the-linked-template)dodano wartość wyjściową:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Ta wartość jest wymagana przez szablon główny.

1. Otwórz plik azuredeploy. JSON w Visual Studio Code, jeśli nie jest otwarty.
2. Rozwiń definicję zasobu maszyny wirtualnej, zaktualizuj **dependsOn** , jak pokazano na poniższym zrzucie ekranu:

    ![Zależność konfiguracji połączonych szablonów Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* to nazwa zasobu wdrożenia.
3. Zaktualizuj **Właściwości/diagnosticsProfile/bootDiagnostics/storageUri** , jak pokazano na poprzednim zrzucie ekranu.
4. Zapisz zmodyfikowany szablon.

## <a name="deploy-the-template"></a>Wdróż szablon

Zapoznaj się z sekcją [Wdrażanie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) , aby zapoznać się z procedurą wdrażania. Użyj tej samej nazwy grupy zasobów co konto magazynu do przechowywania połączonego szablonu. Ułatwia to czyszczenie zasobów w następnej sekcji. Aby zwiększyć bezpieczeństwo, należy użyć wygenerowanego hasła dla konta administratora maszyny wirtualnej. Zobacz [wymagania wstępne](#prerequisites).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy** .
3. Wybierz nazwę grupy zasobów.  Zobaczysz łącznie sześć zasobów w grupie zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="additional-practice"></a>Dodatkowe rozwiązanie

Aby ulepszyć projekt, wprowadź następujące dodatkowe zmiany w zakończonym projekcie:

1. Zmodyfikuj szablon główny (azuredeploy. JSON) tak, aby pobierał wartość identyfikatora URI połączonego szablonu za pośrednictwem parametru.
2. Zamiast generowania tokenu sygnatury dostępu współdzielonego podczas przekazywania połączonego szablonu, wygeneruj token podczas wdrażania szablonu głównego. Aby uzyskać więcej informacji, zobacz [udostępnianie tokenu SAS podczas wdrażania](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Następne kroki

W tym samouczku szablon zostanie zamodularny do szablonu głównego i połączonego szablonu. Aby dowiedzieć się, jak używać rozszerzeń maszyn wirtualnych do wykonywania zadań po wdrożeniu, zobacz:

> [!div class="nextstepaction"]
> [Wdróż rozszerzenia maszyny wirtualnej](./resource-manager-tutorial-deploy-vm-extensions.md)
