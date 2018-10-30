---
title: Tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi | Microsoft Docs
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
ms.date: 10/19/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5e198310dd18cc8574b5510b9318ff4badaffca3
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646314"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Samouczek: tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi

Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager, aby wdrożyć wiele zasobów.  Po utworzeniu szablonu możesz wdrożyć go przy użyciu usługi Cloud Shell w witrynie Azure Portal.

Instrukcje w tym samouczku pozwalają utworzyć konto magazynu, maszynę wirtualną, sieć wirtualną oraz niektóre inne zasoby zależne. Niektórych zasobów nie można wdrożyć, dopóki nie istnieje inny zasób. Przykładowo nie można utworzyć maszyny wirtualnej, jeżeli nie istnieje konto magazynu i interfejs sieciowy. Relację tę definiuje się, ustawiając jeden zasób jako zależny od innych zasobów. Usługa Resource Manager ocenia zależności pomiędzy zasobami i wdraża je w kolejności opartej na zależności. Gdy zasoby nie zależą od siebie nawzajem, usługa Resource Manager wdraża je równolegle. Aby uzyskać więcej informacji, zobacz [Definiowanie kolejności wdrażania zasobów w szablonach usługi Azure Resource Manager](./resource-group-define-dependencies.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie bezpiecznego środowiska
> * Otwieranie szablonu szybkiego startu
> * Eksplorowanie szablonu
> * Edytowanie pliku parametrów
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem Resource Manager Tools.  Zobacz [Instalowanie rozszerzenia ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* W celu zapewnienia ochrony przed atakiem rozproszonym na hasła należy wygenerować hasło dla konta administratora maszyny wirtualnej. Oto przykład:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz pozycję **Plik**>**Zapisz jako**, aby zapisać kopię pliku o nazwie **azuredeploy.json** na komputerze lokalnym.

## <a name="explore-the-template"></a>Eksplorowanie szablonu

Podczas eksplorowania szablonu w tej sekcji spróbuj odpowiedzieć na następujące pytania:

- Jak wiele zasobów platformy Azure zostało zdefiniowanych w tym szablonie?
- Jednym z tych zasobów jest konto usługi Azure Storage.  Czy definicja przypomina tę użytą w poprzednim samouczku?
- Czy możesz znaleźć dokumentację szablonów dla zasobów zdefiniowanych w tym szablonie?
- Czy możesz znaleźć zależności zasobów?

1. W programie Visual Studio Code zwiń elementy, aby wyświetlić tylko elementy pierwszego poziomu oraz elementy drugiego poziomu wewnątrz **zasobów**:

    ![Szablony usługi Azure Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Istnieje pięć zasobów definiowanych przez szablon.
2. Rozwiń pierwszy zasób. Jest to konto magazynu. Definicja powinna być taka sama jak definicja użyta na początku poprzedniego samouczka.

    ![Definicja konta magazynu w szablonach usługi Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Rozwiń drugi zasób. Typ zasobu to **Microsoft.Network/publicIPAddresses**. Aby odnaleźć dokumentację szablonu, przejdź do [dokumentacji szablonów](https://docs.microsoft.com/azure/templates/) i wpisz **publiczny adres IP** lub **publiczne adresy IP** w polu **Filtruj według tytułu**. Porównaj definicję zasobu z dokumentacją szablonu.

    ![Definicja publicznego adresu IP w szablonach usługi Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Powtórz ostatni krok, aby znaleźć dokumentację szablonów dla innych zasobów zdefiniowanych w tym szablonie.  Porównaj definicje zasobów z dokumentacją.
5. Rozwiń czwarty zasób:

    ![Element dependsOn szablonów usługi Azure Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Element dependsOn umożliwia zdefiniowanie jednego zasobu jako zasobu zależnego od jednego lub większej liczby zasobów. W tym przykładzie zasób to networkInterface.  Zależy on od dwóch innych zasobów:

    * publicIPAddress
    * virtualNetwork

6. Rozwiń piąty zasób. Ten zasób to maszyna wirtualna. Zależy on od dwóch innych zasobów:

    * storageAccount
    * networkInterface

Na poniższym diagramie przedstawiono zasoby i informacje o zależności dla tego szablonu:

![Diagram zależności szablonu usługi Azure Resource Manager w programie Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Poprzez określenie zależności usługa Resource Manager efektywnie wdraża rozwiązanie. Usługa wdraża równolegle konto magazynu, publiczny adres IP i sieć wirtualną, ponieważ nie mają zależności. Po wdrożeniu adresu IP i sieci wirtualnej zostanie utworzony interfejs sieciowy. Po wdrożeniu wszystkich innych zasobów usługa Resource Manager wdroży maszynę wirtualną.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Istnieje wiele metod wdrażania szablonów.  W tym samouczku zostanie użyta usługa Cloud Shell z poziomu witryny Azure Portal.

1. Zaloguj się do usługi [Cloud Shell](https://shell.azure.com). 
2. Wybierz pozycję **PowerShell** z lewego górnego rogu okna usługi Cloud Shell, a następnie wybierz pozycję **Potwierdź**.  W tym samouczku użyty zostanie program PowerShell.
3. Wybierz opcję **Przekaż plik** w usłudze Cloud Shell:

    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Wybierz szablon, który został zapisany wcześniej w ramach tego samouczka. Nazwa domyślna to **azuredeploy.json**.  Jeżeli masz plik o tej samej nazwie, starszy plik zostanie zastąpiony bez żadnego powiadomienia.
5. W usłudze Cloud Shell uruchom następujące polecenie, aby zweryfikować, czy plik został pomyślnie przekazany. 

    ```bash
    ls
    ```

    ![Wyświetlanie listy plików w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Nazwa pliku przedstawiona na zrzucie ekranu to azuredeploy.json.

6. W usłudze Cloud Shell uruchom następujące polecenie, aby zweryfikować zawartość pliku JSON:

    ```bash
    cat azuredeploy.json
    ```
7. W usłudze Cloud Shell uruchom poniższe polecenia programu PowerShell. Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Zobacz [Wymagania wstępne](#prerequisites).

    ```azurepowershell
    $deploymentName = Read-Host -Prompt "Enter the name for this deployment"
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password"
    $dnsLablePrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -adminUsername = $adminUsername `
        -adminPassword = $adminPassword `
        -dnsLabelPrefix = $dnsLabelPrefix `
        -TemplateFile azuredeploy.json 
    ```
8. Uruchom następujące polecenie programu PowerShell, aby wyświetlić nowo utworzoną maszynę wirtualną:

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Nazwa maszyny wirtualnej jest zakodowana jako **SimpleWinVM** wewnątrz szablonu.

9. Zaloguj się do maszyny wirtualnej, aby przetestować poświadczenia administratora. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku tworzy się i wdraża szablon, aby utworzyć maszynę wirtualną, sieć wirtualną i zasoby zależne. Aby dowiedzieć się, jak wdrażać zasoby platformy Azure na podstawie warunków, zobacz:


> [!div class="nextstepaction"]
> [Używanie warunków](./resource-manager-tutorial-use-conditions.md)

