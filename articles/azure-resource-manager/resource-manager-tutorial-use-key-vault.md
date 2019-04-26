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
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 127b411f4de0bceff98f1c81f6a4ca8c1fc4fb3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391030"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Samouczek: Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager

Dowiedz się, jak pobrać wpisy tajne z usługi Azure Key Vault i przekazać te wpisy jako parametry podczas wdrażania usługi Resource Manager. Wartość nigdy nie jest uwidoczniana, ponieważ używane jest tylko odwołanie do jej identyfikatora magazynu kluczy. Aby uzyskać więcej informacji, zobacz [Use Azure Key Vault to pass secure parameter value during deployment (Bezpieczne przekazywanie wartości parametru za pomocą usługi Azure Key Vault podczas wdrażania)](./resource-manager-keyvault-parameter.md)

W samouczku [Ustawianie kolejności wdrażania zasobów](./resource-manager-tutorial-create-templates-with-dependent-resources.md) utworzono maszynę wirtualną, sieć wirtualną i kilka innych zasobów zależnych. W tym samouczku dostosujesz szablon, aby pobrać hasło administratora maszyny wirtualnej z magazynu kluczy.

![Diagram integracji usługi Key Vault szablonu usługi Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowanie magazynu kluczy
> * Otwieranie szablonu szybkiego startu
> * Edytowanie pliku parametrów
> * Wdrożenie szablonu
> * Weryfikowanie wdrożenia
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem [Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Sprawdź, czy wygenerowane hasło spełnia wymagania maszyny wirtualnej. Każda usługa platformy Azure ma określone wymagania dotyczące hasła. Aby sprawdzić wymagania maszyn wirtualnych dotyczące haseł, zobacz [What are the password requirements when creating a VM? (Jakie są wymagania dotyczące hasła podczas tworzenia maszyny wirtualnej?)](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Przygotowanie magazynu kluczy

W tej sekcji użyjesz szablonu usługi Resource Manager do utworzenia magazynu kluczy oraz wpisu tajnego. Ten szablon umożliwia wykonanie następujących czynności:

* Utworzenie magazynu kluczy z włączoną właściwością `enabledForTemplateDeployment`. Ta właściwość musi mieć wartość „true”, aby w procesie wdrażania szablonu można było uzyskać dostęp do wpisów tajnych zdefiniowanych w tym magazynie kluczy.
* Dodawanie wpisu tajnego do magazynu kluczy.  Wpis tajny zawiera hasło administratora maszyny wirtualnej.

Jeśli użytkownik wdrażający szablon maszyny wirtualnej nie jest właścicielem ani współautorem magazynu kluczy, właściciel lub współautor magazynu kluczy musi przyznać mu uprawnienie Microsoft.KeyVault/vaults/deploy/action do tego magazynu kluczy. Aby uzyskać więcej informacji, zobacz [Use Azure Key Vault to pass secure parameter value during deployment (Bezpieczne przekazywanie wartości parametru za pomocą usługi Azure Key Vault podczas wdrażania)](./resource-manager-keyvault-parameter.md)

Szablon wymaga Twojego identyfikatora obiektu użytkownika z usługi Azure Active Directory w celu skonfigurowania uprawnień. Poniższa procedura pozwala pobrać identyfikator obiektu (GUID).

1. Uruchom następujące polecenie programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.  

    # <a name="clitabcli"></a>[Interfejs wiersza polecenia](#tab/CLI)
    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    ```   
    # <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/PowerShell)
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
    (Get-AzADUser -UserPrincipalName $upn).Id
    ```
    lub
    ```azurepowershell-interactive
    $displayName = Read-Host -Prompt "Enter your user display name (i.e. John Dole, see the upper right corner of the Azure portal)"
    (Get-AzADUser -DisplayName $displayName).Id
    ```
    ---
2. Zanotuj identyfikator obiektu. Będzie on potrzebny w dalszej części tego samouczka.

Aby utworzyć magazyn kluczy:

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Wybierz lub wprowadź następujące wartości.  Nie wybieraj przycisku **Kup** po wprowadzeniu wartości.

    ![Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager w portalu](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: przypisz unikatową nazwę. Zanotuj tę nazwę, ponieważ ta sama grupa zasobów będzie używana do wdrożenia maszyny wirtualnej w kolejnej sesji. Umieszczenie magazynu kluczy oraz maszyny wirtualnej w tej samej grupie zasobów ułatwia oczyszczanie zasobów po zakończeniu pracy z samouczkiem.
    * **Lokalizacja**: wybierz lokalizację.  Domyślna lokalizacja to **Środkowe stany USA**.
    * **Nazwa usługi Key Vault**: przypisz unikatową nazwę. 
    * **Identyfikator dzierżawy**: funkcja szablonu automatycznie pobiera identyfikator dzierżawy.  Nie zmieniaj wartości domyślnej
    * **Identyfikator użytkownika usługi AD**: wprowadź identyfikator obiektu użytkownika w usłudze Azure AD, pobrany w poprzedniej procedurze.
    * **Nazwa wpisu tajnego**: Nazwa domyślna to **vmAdminPassword**. Jeśli zmienisz nazwę wpisu tajnego w tym miejscu, będzie konieczne zaktualizowanie nazwy wpisu tajnego podczas wdrażania maszyny wirtualnej.
    * **Wartość wpisu tajnego**: Wprowadź wpis tajny.  Wpis tajny to hasło używane do logowania na maszynie wirtualnej. Zaleca się używanie hasła wygenerowanego w poprzedniej procedurze.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: Zaznacz.
3. Wybierz pozycję **Edytuj parametry** w górnej części, aby zapoznać się z szablonem.
4. Przejdź do wiersza 28 pliku szablonu w formacie JSON. Jest to definicja zasobu magazynu kluczy.
5. Przejdź do wiersza 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` to właściwość usługi Key Vault. Ta właściwość musi mieć wartość „true”, aby podczas wdrażania było możliwe pobieranie wpisów tajnych z tego magazynu kluczy.
6. Przejdź do wiersza 89. Jest to definicja wpisu tajnego usługi Key Vault.
7. Wybierz pozycję **Odrzuć** w dolnej części strony. Nie wprowadzono żadnych zmian.
8. Sprawdź, czy wszystkie wartości zostały podane tak, jak na powyższym zrzucie ekranu, a następnie kliknij przycisk **Kup** w dolnej części strony.
9. Wybierz ikonę dzwonka (powiadomienia) w górnej części strony, aby otworzyć okienko **Powiadomienia**. Zaczekaj na pomyślne wdrożenie zasobu.
10. W okienku **Powiadomienia** wybierz pozycję **Przejdź do grupy zasobów**. 
11. Wybierz nazwę magazynu kluczy, aby go otworzyć.
12. Wybierz pozycję **Wpisy tajne** w okienku po lewej stronie. Powinien być tam wymieniony wpis **vmAdminPassword**.
13. W okienku po lewej stronie wybierz pozycję **Zasady dostępu**. Powinna być tam widoczna Twoja nazwa (z usługi Active Directory) — jeśli jej nie ma, nie masz uprawnień dostępu do tego magazynu kluczy.
14. Wybierz pozycję **Kliknij, aby wyświetlić zaawansowane zasady dostępu**. Zwróć uwagę, że pole **Włącz dostęp do usługi Azure Resource Manager na potrzeby wdrożenia szablonu** jest zaznaczone. To ustawienie to kolejny warunek, który musi być spełniony, aby umożliwić integrację z usługą Key Vault.

    ![Zasady dostępu na potrzeby integracji szablonu usługi Resource Manager z usługą Key Vault](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
15. Wybierz pozycję **Właściwości** w okienku po lewej stronie.
16. Skopiuj **Identyfikator zasobu**. Ten identyfikator będzie potrzebny podczas wdrażania maszyny wirtualnej.  Format identyfikatora zasobu jest następujący:

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

    Zastąp wartość **id** identyfikatorem zasobu magazynu kluczy utworzonym w poprzedniej procedurze.  

    ![Integracja usługi Key Vault podczas wdrażania maszyny wirtualnej z szablonu usługi Resource Manager — plik parametrów](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Podaj następujące wartości:

    * **adminUsername**: nazwa konta administratora maszyny wirtualnej.
    * **dnsLabelPrefix**: nazwa dnsLabelPrefix.
4. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z instrukcjami przedstawionymi w sekcji [Wdrożenie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), aby wdrożyć szablon. Przekaż pliki **azuredeploy.json** i **azuredeploy.parameters.json** do usługi Cloud Shell, a następnie wdróż szablon za pomocą następującego skryptu programu PowerShell:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Podczas wdrażania szablonu należy użyć tej samej grupy zasobów, która została użyta na potrzeby magazynu kluczy. Ułatwia to oczyszczanie zasobów. Wystarczy wówczas usunąć jedną grupę zasobów, a nie dwie.

## <a name="valid-the-deployment"></a>Weryfikacja wdrożenia

Po pomyślnym wdrożeniu maszyny wirtualnej przetestuj logowanie przy użyciu hasła przechowywanego w magazynie kluczy.

1. Otwórz [portal Azure](https://portal.azure.com).
2. Wybierz pozycję **Grupy zasobów**/**Nazwa_grupy_zasobów>**/**simpleWinVM**
3. Wybierz pozycję **Połącz** na górze strony.
4. Wybierz pozycję **Pobierz plik RDP**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na maszynie wirtualnej za pomocą hasła przechowywanego w magazynie kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Kolejne kroki

Podczas pracy z tym samouczkiem pobrano wpis tajny z usługi Azure Key Vault i użyto go podczas wdrażania szablonu.  Aby dowiedzieć się, jak tworzyć połączone szablony, zobacz:

> [!div class="nextstepaction"]
> [Create linked templates (Tworzenie połączonych szablonów)](./resource-manager-tutorial-create-linked-templates.md)
