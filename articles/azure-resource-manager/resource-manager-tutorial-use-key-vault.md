---
title: Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager | Microsoft Docs
description: Dowiedz się, jak używać usługi Azure Key Vault do bezpiecznego przekazywania wartości parametrów podczas wdrażania szablonu usługi Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 3a84f9ed35bac7f56d4a6aa2af94d1c28e335b74
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093203"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Samouczek: Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager

Dowiedz się, jak pobrać wartości wpisu tajnego z usługi Azure Key Vault i przekazać te wartości jako parametry podczas wdrażania usługi Resource Manager. Wartość nigdy nie jest uwidoczniana, ponieważ używane jest tylko odwołanie do jej identyfikatora magazynu Key Vault. Aby uzyskać więcej informacji, zobacz [Use Azure Key Vault to pass secure parameter value during deployment (Bezpieczne przekazywanie wartości parametru za pomocą usługi Azure Key Vault podczas wdrażania)](./resource-manager-keyvault-parameter.md)

W samouczku [Ustawianie kolejności wdrażania zasobów](./resource-manager-tutorial-create-templates-with-dependent-resources.md) utworzono maszynę wirtualną, sieć wirtualną i kilka innych zasobów zależnych. W tym samouczku dostosujesz szablon, aby pobrać hasło administratora maszyny wirtualnej z usługi Azure Key Vault.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowanie magazynu Key Vault
> * Otwieranie szablonu szybkiego startu
> * Edytowanie pliku parametrów
> * Wdrożenie szablonu
> * Weryfikowanie wdrożenia
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem [Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-the-key-vault"></a>Przygotowanie magazynu Key Vault

W tej sekcji użyjesz szablonu usługi Resource Manager do utworzenia magazynu Key Vault oraz wpisu tajnego. Ten szablon umożliwia wykonanie następujących czynności:

* Utworzenie magazynu Key Vault z włączoną właściwością `enabledForTemplateDeployment`. Ta właściwość musi mieć wartość „true”, aby w procesie wdrażania szablonu można było uzyskać dostęp do wpisów tajnych zdefiniowanych w tym magazynie Key Vault.
* Dodaj wpis tajny do magazynu Key Vault.  Wpis tajny zawiera hasło administratora maszyny wirtualnej.

Jeśli użytkownik wdrażający szablon maszyny wirtualnej nie jest właścicielem ani współautorem magazynu Key Vault, właściciel lub współautor magazynu Key Vault musi przyznać mu uprawnienie Microsoft.KeyVault/vaults/deploy/action do tego magazynu kluczy. Aby uzyskać więcej informacji, zobacz [Use Azure Key Vault to pass secure parameter value during deployment (Bezpieczne przekazywanie wartości parametru za pomocą usługi Azure Key Vault podczas wdrażania)](./resource-manager-keyvault-parameter.md)

Szablon wymaga Twojego identyfikatora obiektu użytkownika z usługi Azure Active Directory w celu skonfigurowania uprawnień. Poniższa procedura pobiera identyfikator obiektu (identyfikator GUID) i generuje hasło administratora. W celu zapewnienia ochrony przed atakiem rozproszonym na hasła zalecamy korzystanie z haseł generowanych.

1. Uruchom następujące polecenie programu Azure PowerShell lub wiersza polecenia platformy Azure.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    openssl rand -base64 32
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzureADUser -ObjectId $upn).ObjectId
    openssl rand -base64 32
    ```
2. Zanotuj identyfikator obiektu oraz wygenerowane hasło. Będą one potrzebne później.
3. Sprawdź, czy wygenerowane hasło spełnia wymagania maszyny wirtualnej. Każda usługa platformy Azure ma określone wymagania dotyczące hasła. Aby sprawdzić wymagania maszyn wirtualnych dotyczące haseł, zobacz [What are the password requirements when creating a VM? (Jakie są wymagania dotyczące hasła podczas tworzenia maszyny wirtualnej?)](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Aby utworzyć magazyn Key Vault:

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu Key Vault oraz wpisu tajnego.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Wybierz lub wprowadź następujące wartości.  Nie wybieraj przycisku **Kup** po wprowadzeniu wartości.

    ![Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager w portalu](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: przypisz unikatową nazwę. Zanotuj tę nazwę, ponieważ ta sama grupa zasobów będzie używana do wdrożenia maszyny wirtualnej w kolejnej sesji. Umieszczenie magazynu Key Vault oraz maszyny wirtualnej w tej samej grupie zasobów ułatwia oczyszczanie zasobów po zakończeniu pracy z samouczkiem.
    * **Lokalizacja**: wybierz lokalizację.  Domyślna lokalizacja to **Środkowe stany USA**.
    * **Nazwa usługi Key Vault**: przypisz unikatową nazwę. 
    * **Identyfikator dzierżawy**: funkcja szablonu automatycznie pobiera identyfikator dzierżawy.  Nie zmieniaj wartości domyślnej
    * **Identyfikator użytkownika usługi AD**: wprowadź identyfikator obiektu użytkownika w usłudze Azure AD, pobrany w poprzedniej procedurze.
    * **Nazwa wpisu tajnego**: Nazwa domyślna to **vmAdminPassword**. Jeśli zmienisz nazwę wpisu tajnego w tym miejscu, będzie konieczne zaktualizowanie nazwy wpisu tajnego podczas wdrażania maszyny wirtualnej.
    * **Wartość wpisu tajnego**: Wprowadź wpis tajny.  Wpis tajny to hasło używane do logowania na maszynie wirtualnej. Zaleca się używanie hasła wygenerowanego w poprzedniej procedurze.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: Zaznacz.
3. Wybierz pozycję **Edytuj parametry** w górnej części, aby zapoznać się z szablonem.
4. Przejdź do wiersza 28 pliku szablonu w formacie JSON. Jest to definicja zasobu usługi Key Vault.
5. Przejdź do wiersza 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` to właściwość usługi Key Vault. Ta właściwość musi mieć wartość „true”, aby podczas wdrażania było możliwe pobieranie wpisów tajnych z tego magazynu Key Vault.
6. Przejdź do wiersza 89. Jest to definicja wpisu tajnego usługi Key Vault.
7. Wybierz pozycję **Odrzuć** w dolnej części strony. Nie wprowadzono żadnych zmian.
8. Sprawdź, czy wszystkie wartości zostały podane tak, jak na powyższym zrzucie ekranu, a następnie kliknij przycisk **Kup** w dolnej części strony.
9. Wybierz ikonę dzwonka (powiadomienia) w górnej części strony, aby otworzyć okienko **Powiadomienia**. Zaczekaj na pomyślne wdrożenie zasobu.
10. W okienku **Powiadomienia** wybierz pozycję **Przejdź do grupy zasobów**. 
11. Wybierz nazwę magazynu Key Vault, aby go otworzyć.
12. W okienku po lewej stronie wybierz pozycję **Zasady dostępu**. Powinna być tam widoczna Twoja nazwa (z usługi Active Directory) — jeśli jej nie ma, nie masz uprawnień dostępu do tego magazynu kluczy.
13. Wybierz pozycję **Kliknij, aby wyświetlić zaawansowane zasady dostępu**. Zwróć uwagę, że pole **Włącz dostęp do usługi Azure Resource Manager na potrzeby wdrożenia szablonu** jest zaznaczone. To kolejny warunek, który musi być spełniony, aby umożliwić integrację z usługą Key Vault.

    ![Zasady dostępu na potrzeby integracji szablonu usługi Resource Manager z usługą Key Vault](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
14. Wybierz pozycję **Właściwości** w okienku po lewej stronie.
15. Skopiuj **Identyfikator zasobu**. Ten identyfikator będzie potrzebny podczas wdrażania maszyny wirtualnej.  Format identyfikatora zasobu jest następujący:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik. To ten sam scenariusz, który był używany w samouczku [Tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Istnieje pięć zasobów definiowanych przez szablon:

    * `Microsoft.Storage/storageAccounts`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.
5. Wybierz pozycję **Plik**>**Zapisz jako**, aby zapisać kopię pliku o nazwie **azuredeploy.json** na komputerze lokalnym.
6. Powtórz kroki 1 – 4, aby otworzyć następujący adres URL, a następnie zapisz plik jako **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Edytowanie pliku parametrów

Nie musisz wprowadzać żadnych zmian w pliku szablonu.

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
    Zastąp wartość **id** identyfikatorem zasobu magazynu Key Vault utworzonego w poprzedniej procedurze.  

    ![Integracja usługi Key Vault podczas wdrażania maszyny wirtualnej z szablonu usługi Resource Manager — plik parametrów](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Podaj następujące wartości:

    * **adminUsername**: nazwa konta administratora maszyny wirtualnej.
    * **dnsLabelPrefix**: nazwa dnsLabelPrefix.
4. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z instrukcjami przedstawionymi w sekcji [Wdrożenie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), aby wdrożyć szablon. Przekaż pliki **azuredeploy.json** i **azuredeploy.parameters.json** do usługi Cloud Shell, a następnie wdróż szablon za pomocą następującego skryptu programu PowerShell:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name of the Key Vault"
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

Podczas wdrażania szablonu należy użyć tej samej grupy zasobów, która została użyta na potrzeby magazynu Key Vault. Ułatwia to oczyszczanie zasobów. Wystarczy wówczas usunąć jedną grupę zasobów, a nie dwie.

## <a name="valid-the-deployment"></a>Weryfikacja wdrożenia

Po pomyślnym wdrożeniu maszyny wirtualnej przetestuj logowanie przy użyciu hasła przechowywanego w usłudze Key Vault.

1. Otwórz [portal Azure](https://portal.azure.com).
2. Wybierz pozycję **Grupy zasobów**/**Nazwa_grupy_zasobów>**/**simpleWinVM**
3. Wybierz pozycję **Połącz** na górze strony.
4. Wybierz pozycję **Pobierz plik RDP**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na maszynie wirtualnej za pomocą hasła przechowywanego w magazynie Key Vault.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem pobrano wpis tajny z usługi Azure Key Vault i użyto go podczas wdrażania szablonu.  Aby dowiedzieć się, jak tworzyć połączone szablony, zobacz:

> [!div class="nextstepaction"]
> [Create linked templates (Tworzenie połączonych szablonów)](./resource-manager-tutorial-create-linked-templates.md)
