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
ms.date: 08/27/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7509ed46ba07cd8250f82f8eb258d18e3f4a1ee6
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107109"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Samouczek: tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi

Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager, aby wdrożyć wiele zasobów.  Po utworzeniu szablonu możesz wdrożyć go przy użyciu usługi Cloud Shell w witrynie Azure Portal.

Niektórych zasobów nie można wdrożyć, dopóki nie istnieje inny zasób. Przykładowo nie można utworzyć maszyny wirtualnej, jeżeli nie istnieje konto magazynu i interfejs sieciowy. Relację tę definiuje się, ustawiając jeden zasób jako zależny od innych zasobów. Usługa Resource Manager ocenia zależności pomiędzy zasobami i wdraża je w kolejności opartej na zależności. Gdy zasoby nie zależą od siebie nawzajem, usługa Resource Manager wdraża je równolegle. Aby uzyskać więcej informacji, zobacz [Definiowanie kolejności wdrażania zasobów w szablonach usługi Azure Resource Manager](./resource-group-define-dependencies.md).

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Eksplorowanie szablonu
> * Wdrożenie szablonu
> * Oczyszczanie zasobów

Instrukcje w tym samouczku pozwalają utworzyć maszynę wirtualną, sieć wirtualną oraz niektóre zasoby zależne. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* [Program Visual Studio Code](https://code.visualstudio.com/)
* Rozszerzenie Narzędzia usługi Resource Manager.  Zobacz [Instalowanie rozszerzenia ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

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
2. Rozwiń pierwszy zasób. Jest to konto magazynu. Definicja powinna być identyczna, jak ta użyta na początku poprzedniego samouczka.

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

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Cloud Shell** w prawym górnym rogu, jak pokazano na poniższym obrazie:

    ![Usługa Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. Wybierz opcję **PowerShell** z lewego górnego rogu okna usługi Cloud Shell.  W tym samouczku użyty zostanie program PowerShell.
4. Wybierz opcję **Uruchom ponownie**.
5. Wybierz opcję **Przekaż plik** w usłudze Cloud Shell:

    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. Wybierz plik, który został zapisany wcześniej w ramach tego samouczka. Nazwa domyślna to **azuredeploy.json**.  Jeżeli masz plik o tej samej nazwie, starszy plik zostanie zastąpiony bez żadnego powiadomienia.
7. W usłudze Cloud Shell uruchom następujące polecenie, aby zweryfikować, czy plik został pomyślnie przekazany. 

    ```shell
    ls
    ```

    ![Wyświetlanie listy plików w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Nazwa pliku przedstawiona na zrzucie ekranu to azuredeploy.json.

8. W usłudze Cloud Shell uruchom następujące polecenie, aby zweryfikować zawartość pliku JSON:

    ```shell
    cat azuredeploy.json
    ```
9. W usłudze Cloud Shell uruchom następujące polecenia programu PowerShell:

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    Poniżej przedstawiono zrzut ekranu przedstawiający przykładowe wdrożenie:

    ![Wdrażanie szablonu w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    Na zrzucie ekranu zostały użyte następujące wartości:

    * **$resourceGroupName**: myresourcegroup0710. 
    * **$location**: eastus2
    * **&lt;DeployName>**: mydeployment0710
    * **&lt;TemplateFile>**: azuredeploy.json
    * **Parametry szablonu**:

        * **adminUsername**: JohnDole
        * **adminPassword**: Pass@word123
        * **dnsLabelPrefix**: myvm0710

10. Uruchom następujące polecenie programu PowerShell, aby wyświetlić nowo utworzoną maszynę wirtualną:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    Nazwa maszyny wirtualnej jest zakodowana jako **SimpleWinVM** wewnątrz szablonu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku tworzy się i wdraża szablon, aby utworzyć maszynę wirtualną, sieć wirtualną i zasoby zależne. Aby uzyskać więcej informacji o szablonach, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](./resource-group-authoring-templates.md).