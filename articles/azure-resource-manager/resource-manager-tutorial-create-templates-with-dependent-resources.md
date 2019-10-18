---
title: Szablon Azure Resource Manager z zasobami zależnymi
description: Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager z wieloma zasobami, a także jak wdrożyć go przy użyciu witryny Azure Portal
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0eb4b29ffb8ae0269dbc7efd7e9ef6b720188bce
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533506"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Samouczek: tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi

Dowiedz się, jak utworzyć szablon Azure Resource Manager, aby wdrożyć wiele zasobów i skonfigurować kolejność wdrażania. Po utworzeniu szablonu możesz wdrożyć go przy użyciu usługi Cloud Shell w witrynie Azure Portal.

Instrukcje w tym samouczku pozwalają utworzyć konto magazynu, maszynę wirtualną, sieć wirtualną oraz niektóre inne zasoby zależne. Niektórych zasobów nie można wdrożyć, dopóki nie istnieje inny zasób. Przykładowo nie można utworzyć maszyny wirtualnej, jeżeli nie istnieje konto magazynu i interfejs sieciowy. Relację tę definiuje się, ustawiając jeden zasób jako zależny od innych zasobów. Usługa Resource Manager ocenia zależności pomiędzy zasobami i wdraża je w kolejności opartej na zależności. Gdy zasoby nie zależą od siebie nawzajem, usługa Resource Manager wdraża je równolegle. Aby uzyskać więcej informacji, zobacz [Definiowanie kolejności wdrażania zasobów w szablonach usługi Azure Resource Manager](./resource-group-define-dependencies.md).

![Diagram kolejności wdrażania zasobów zależnych szablonu Menedżera zasobów](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Eksplorowanie szablonu
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem Resource Manager Tools.  Zobacz [Instalowanie rozszerzenia](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **File (Plik)** >**Open File (Otwórz plik)** .
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)** , aby otworzyć plik.
4. Wybierz pozycję **Plik**>**Zapisz jako**, aby zapisać kopię pliku o nazwie **azuredeploy.json** na komputerze lokalnym.

## <a name="explore-the-template"></a>Eksplorowanie szablonu

Podczas eksplorowania szablonu w tej sekcji spróbuj odpowiedzieć na następujące pytania:

* Jak wiele zasobów platformy Azure zostało zdefiniowanych w tym szablonie?
* Jednym z tych zasobów jest konto usługi Azure Storage.  Czy definicja przypomina tę użytą w poprzednim samouczku?
* Czy możesz znaleźć dokumentację szablonów dla zasobów zdefiniowanych w tym szablonie?
* Czy możesz znaleźć zależności zasobów?

1. W programie Visual Studio Code zwiń elementy, aby wyświetlić tylko elementy pierwszego poziomu oraz elementy drugiego poziomu wewnątrz **zasobów**:

    ![Szablony usługi Azure Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Istnieje pięć zasobów definiowanych przez szablon:

   * `Microsoft.Storage/storageAccounts`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.

2. Rozwiń pierwszy zasób. Jest to konto magazynu. Porównaj definicję zasobu z [dokumentacją szablonu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).

    ![Definicja konta magazynu w szablonach usługi Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Rozwiń drugi zasób. Typ zasobu to `Microsoft.Network/publicIPAddresses`. Porównaj definicję zasobu z [dokumentacją szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).

    ![Definicja publicznego adresu IP w szablonach usługi Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Rozwiń czwarty zasób. Typ zasobu to `Microsoft.Network/networkInterfaces`:  

    ![Element dependsOn szablonów usługi Azure Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Element dependsOn umożliwia zdefiniowanie jednego zasobu jako zasobu zależnego od jednego lub większej liczby zasobów. Zasób zależy od dwóch innych zasobów:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. Rozwiń piąty zasób. Ten zasób to maszyna wirtualna. Zależy on od dwóch innych zasobów:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Na poniższym diagramie przedstawiono zasoby i informacje o zależności dla tego szablonu:

![Diagram zależności szablonu usługi Azure Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Poprzez określenie zależności usługa Resource Manager efektywnie wdraża rozwiązanie. Usługa wdraża równolegle konto magazynu, publiczny adres IP i sieć wirtualną, ponieważ nie mają zależności. Po wdrożeniu adresu IP i sieci wirtualnej zostanie utworzony interfejs sieciowy. Po wdrożeniu wszystkich innych zasobów usługa Resource Manager wdroży maszynę wirtualną.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Istnieje wiele metod wdrażania szablonów.  W tym samouczku zostanie użyta usługa Cloud Shell z poziomu witryny Azure Portal.

1. Zaloguj się do usługi [Cloud Shell](https://shell.azure.com). 
2. Wybierz pozycję **PowerShell** z lewego górnego rogu okna usługi Cloud Shell, a następnie wybierz pozycję **Potwierdź**.  W tym samouczku użyty zostanie program PowerShell.
3. Wybierz opcję **Przekaż plik** w usłudze Cloud Shell:

    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Wybierz szablon, który został zapisany wcześniej w ramach tego samouczka. Nazwa domyślna to **azuredeploy.json**.  Jeżeli masz plik o tej samej nazwie, starszy plik zostanie zastąpiony bez żadnego powiadomienia.

    Opcjonalnie możesz skorzystać z poleceń **ls $HOME** i **cat $HOME/azuredeploy.json**, aby sprawdzić, czy pliki zostały przekazane pomyślnie. 

5. W usłudze Cloud Shell uruchom poniższe polecenia programu PowerShell. Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Zobacz [Wymagania wstępne](#prerequisites).

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"
    ```

8. Uruchom następujące polecenie programu PowerShell, aby wyświetlić nowo utworzoną maszynę wirtualną:

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Nazwa maszyny wirtualnej jest zakodowana jako **SimpleWinVM** wewnątrz szablonu.

9. Nawiąż połączenie RDP z maszyną wirtualną, aby sprawdzić, czy została pomyślnie utworzona.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono i wdrożono szablon, aby utworzyć maszynę wirtualną, sieć wirtualną i zasoby zależne. Aby dowiedzieć się, jak wdrażać zasoby platformy Azure na podstawie warunków, zobacz:

> [!div class="nextstepaction"]
> [Używanie warunków](./resource-manager-tutorial-use-conditions.md)
