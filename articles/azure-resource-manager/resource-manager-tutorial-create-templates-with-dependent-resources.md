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
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114316"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Samouczek: tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi

Dowiedz się, jak utworzyć szablon usługi Azure Resource Manager, aby wdrożyć wiele zasobów.  Po utworzeniu szablonu możesz wdrożyć go przy użyciu usługi Cloud Shell w witrynie Azure Portal.

Instrukcje w tym samouczku pozwalają utworzyć konto magazynu, maszynę wirtualną, sieć wirtualną oraz niektóre inne zasoby zależne. Niektórych zasobów nie można wdrożyć, dopóki nie istnieje inny zasób. Przykładowo nie można utworzyć maszyny wirtualnej, jeżeli nie istnieje konto magazynu i interfejs sieciowy. Relację tę definiuje się, ustawiając jeden zasób jako zależny od innych zasobów. Usługa Resource Manager ocenia zależności pomiędzy zasobami i wdraża je w kolejności opartej na zależności. Gdy zasoby nie zależą od siebie nawzajem, usługa Resource Manager wdraża je równolegle. Aby uzyskać więcej informacji, zobacz [Definiowanie kolejności wdrażania zasobów w szablonach usługi Azure Resource Manager](./resource-group-define-dependencies.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowanie magazynu Key Vault
> * Otwieranie szablonu szybkiego startu
> * Eksplorowanie szablonu
> * Edytowanie pliku parametrów
> * Wdrożenie szablonu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem Resource Manager Tools.  Zobacz [Instalowanie rozszerzenia ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="prepare-key-vault"></a>Przygotowanie magazynu Key Vault

W celu zapewnienia ochrony przed atakiem rozproszonym na hasła zaleca się korzystanie z automatycznie generowanych haseł dla konta administratora maszyny wirtualnej oraz przechowywanie hasła w magazynie Key Vault. Poniżej przedstawiono procedurę tworzenia magazynu Key Vault i wpisu tajnego umożliwiającego przechowywanie hasła. Pokazano też, jak skonfigurować wymagane uprawnienia wdrożenia szablonu zapewniające dostęp do wpisu tajnego przechowywanego w magazynie Key Vault. Jeśli magazyn Key Vault znajduje się w innej subskrypcji platformy Azure, są wymagane dodatkowe zasady dostępu. Aby uzyskać więcej informacji, zobacz [Use Azure Key Vault to pass secure parameter value during deployment (Bezpieczne przekazywanie wartości parametru za pomocą usługi Azure Key Vault podczas wdrażania)](./resource-manager-keyvault-parameter.md).

1. Zaloguj się do usługi [Azure Cloud Shell](https://shell.azure.com).
2. Przełącz się do ulubionego środowiska (**PowerShell** lub **Bash**) w lewym górnym rogu.
3. Uruchom następujące polecenie programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. Zanotuj wartości danych wyjściowych. Będą potrzebne później podczas korzystania z samouczka

> [!NOTE]
> Każda usługa platformy Azure ma określone wymagania dotyczące hasła. Na przykład wymagania maszyny wirtualnej platformy Azure można znaleźć na stronie What are the password requirements when creating a VM (Jakie są wymagania dotyczące hasła podczas tworzenia maszyny wirtualnej).

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz pozycję **Plik**>**Zapisz jako**, aby zapisać kopię pliku o nazwie **azuredeploy.json** na komputerze lokalnym.
5. Powtórz kroki 1–4, aby otworzyć adres **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json**, a następnie zapisz plik jako **azuredeploy.parameters.json**.

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

## <a name="edit-the-parameters-file"></a>Edytowanie pliku parametrów

Nie musisz wprowadzać żadnych zmian w pliku szablonu. Musisz jednak zmodyfikować plik parametrów, aby pobrać hasło administratora z magazynu Key Vault.

1. Otwórz plik **azuredeploy.parameters.json** w programie Visual Studio Code, jeśli nie został jeszcze otwarty.
2. Zaktualizuj parametr **adminPassword** w następujący sposób:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```
    Zastąp wartość **id** identyfikatorem zasobu magazynu Key Vault utworzonego w poprzedniej procedurze. Jest to jeden z elementów wyjściowych. 

    ![Integracja usługi Key Vault podczas wdrażania maszyny wirtualnej z szablonu usługi Resource Manager — plik parametrów](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Podaj następujące wartości:

    - **adminUsername**: nazwa konta administratora maszyny wirtualnej.
    - **dnsLabelPrefix**: nazwa dnsLablePrefix.
4. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Istnieje wiele metod wdrażania szablonów.  W tym samouczku zostanie użyta usługa Cloud Shell z poziomu witryny Azure Portal.

1. Zaloguj się do usługi [Cloud Shell](https://shell.azure.com). Możesz też zalogować się do witryny [Azure Portal](https://portal.azure.com), a następnie wybrać pozycję **Cloud Shell** w prawym górnym rogu, jak pokazano na poniższej ilustracji:

    ![Usługa Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. Wybierz pozycję **PowerShell** z lewego górnego rogu okna usługi Cloud Shell, a następnie wybierz pozycję **Potwierdź**.  W tym samouczku użyty zostanie program PowerShell.
3. Wybierz opcję **Przekaż plik** w usłudze Cloud Shell:

    ![Przekazywanie pliku w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Wybierz pliki, które zostały zapisane wcześniej w ramach tego samouczka. Domyślna nazwa to **azuredeploy.json** i **azuredeploy.paraemters.json**.  Jeśli masz pliki o tych samych nazwach, stare pliki zostaną zastąpione bez żadnego powiadomienia.
5. W usłudze Cloud Shell uruchom następujące polecenie, aby zweryfikować, czy plik został pomyślnie przekazany. 

    ```bash
    ls
    ```

    ![Wyświetlanie listy plików w usłudze Cloud Shell w witrynie Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Nazwa pliku przedstawiona na zrzucie ekranu to azuredeploy.json.

6. W usłudze Cloud Shell uruchom następujące polecenie, aby zweryfikować zawartość pliku JSON:

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. W usłudze Cloud Shell uruchom poniższe polecenia programu PowerShell. Przykładowy skrypt używa grupy zasobów utworzonej dla magazynu Key Vault. Użycie tej samej grupy zasobów ułatwia czyszczenie zasobów.

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. Uruchom następujące polecenie programu PowerShell, aby wyświetlić nowo utworzoną maszynę wirtualną:

    ```powershell
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

