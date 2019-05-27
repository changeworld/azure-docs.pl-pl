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
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239239"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Samouczek: Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager

Dowiedz się, jak pobrać wpisy tajne z usługi Azure Key Vault i przekazać te wpisy jako parametry podczas wdrażania usługi Resource Manager. Wartość nigdy nie jest uwidoczniana, ponieważ używane jest tylko odwołanie do jej identyfikatora magazynu kluczy. Aby uzyskać więcej informacji, zobacz [Use Azure Key Vault to pass secure parameter value during deployment (Bezpieczne przekazywanie wartości parametru za pomocą usługi Azure Key Vault podczas wdrażania)](./resource-manager-keyvault-parameter.md)

W [Ustaw kolejność wdrażania zasobów](./resource-manager-tutorial-create-templates-with-dependent-resources.md) samouczka, utwórz maszynę wirtualną. Należy podać nazwę użytkownika administratora maszyny wirtualnej i hasło. Zamiast podawać hasła, możesz wstępnie przechowywania haseł w usłudze Azure Key Vault, a następnie Dostosuj szablon można pobrać hasło z usługi key vault podczas wdrażania.

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

W tej sekcji służy do tworzenia magazynu kluczy i dodać wpis tajny do magazynu kluczy, więc, że można pobrać klucza tajnego, podczas wdrażania szablonu. Istnieje wiele sposobów, aby utworzyć magazyn kluczy. W tym samouczku użyjesz programu Azure PowerShell, aby wdrożyć [szablonu usługi Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Ten szablon umożliwia wykonanie następujących czynności:

* Utworzenie magazynu kluczy z włączoną właściwością `enabledForTemplateDeployment`. Ta właściwość musi mieć wartość „true”, aby w procesie wdrażania szablonu można było uzyskać dostęp do wpisów tajnych zdefiniowanych w tym magazynie kluczy.
* Dodawanie wpisu tajnego do magazynu kluczy.  Wpis tajny zawiera hasło administratora maszyny wirtualnej.

> [!NOTE]
> Jeśli użytkownik wdrażający szablon maszyny wirtualnej nie jest właścicielem ani współautorem magazynu kluczy, właściciel lub współautor magazynu kluczy musi przyznać mu uprawnienie Microsoft.KeyVault/vaults/deploy/action do tego magazynu kluczy. Aby uzyskać więcej informacji, zobacz [Use Azure Key Vault to pass secure parameter value during deployment (Bezpieczne przekazywanie wartości parametru za pomocą usługi Azure Key Vault podczas wdrażania)](./resource-manager-keyvault-parameter.md)

Aby uruchomić poniższy skrypt programu PowerShell, zaznacz **wypróbuj** do Otwórz usługę Cloud shell. Wklej skrypt, kliknij prawym przyciskiem myszy w okienku shell, a następnie wybierz **Wklej**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

Kilka rodzajów ważne informacje:

* Nazwa grupy zasobów jest nazwą projektu **rg** dołączane. Aby łatwiej [usuwaj zasobów utworzonych w tym samouczku](#clean-up-resources), użyj nazwy tej samej nazwie i zasobów grupy projektów, gdy użytkownik [wdrażanie kolejny szablon](#deploy-the-template).
* Domyślna nazwa nazwa wpisu tajnego to **vmAdminPassword**. Jest trwale zakodowana w szablonie.
* Aby można było dla szablonu, który można pobrać klucza tajnego, należy włączyć zasady dostępu o nazwie **włączyć dostęp do usługi Azure Resource Manager dla wdrożenia szablonu** dla usługi key vault. Ta zasada jest włączona w szablonie. Aby uzyskać więcej informacji na temat tych zasad dostępu, zobacz [wdrażanie magazynów kluczy i wpisów tajnych](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Szablon ma jedną wartość danych wyjściowych o nazwie **keyVaultId**. Zanotuj wartość. Ten identyfikator będzie potrzebny podczas wdrażania maszyny wirtualnej. Format identyfikatora zasobu jest następujący:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Podczas kopiowania i wklej identyfikator identyfikator może być podzielony na wiele wierszy. Należy scalić wiersze i trim dodatkowego miejsca do magazynowania.

Aby zweryfikować wdrożenie, uruchom następujące polecenie programu PowerShell w okienko powłoki można pobrać klucza tajnego w postaci zwykłego tekstu. Polecenie działa tylko w tej samej sesji powłoki ponieważ używa ona zmienną $keyVaultName zdefiniowane w poprzednim skrypcie programu PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Teraz przygotowaniu magazynu kluczy i klucz tajny, sekcje dowiesz się, jak dostosować istniejący szablon można pobrać klucza tajnego podczas wdrażania.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **File (Plik)** >**Open File (Otwórz plik)** .
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Wybierz pozycję **Open (Otwórz)** , aby otworzyć plik. To ten sam scenariusz, który był używany w samouczku [Tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
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

    > [!IMPORTANT]
    > Zastąp wartość **identyfikator** o identyfikatorze zasobu usługi key Vault, utworzony w poprzedniej procedurze.

    ![Integracja usługi Key Vault podczas wdrażania maszyny wirtualnej z szablonu usługi Resource Manager — plik parametrów](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Podaj następujące wartości:

    * **adminUsername**: nazwa konta administratora maszyny wirtualnej.
    * **dnsLabelPrefix**: nazwa dnsLabelPrefix.

    Zobacz przykład w poprzednim zrzucie ekranu.

4. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z instrukcjami przedstawionymi w sekcji [Wdrożenie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), aby wdrożyć szablon. Należy przekazać zarówno **azuredeploy.json** i **azuredeploy.parameters.json** do usługi Cloud shell, a następnie użyj poniższy skrypt programu PowerShell do wdrożenia szablonu:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Podczas wdrażania szablonu należy użyć tej samej grupy zasobów, która została użyta na potrzeby magazynu kluczy. Ułatwia to oczyszczanie zasobów. Wystarczy wówczas usunąć jedną grupę zasobów, a nie dwie.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Po pomyślnym wdrożeniu maszyny wirtualnej przetestuj logowanie przy użyciu hasła przechowywanego w magazynie kluczy.

1. Otwórz [portal Azure](https://portal.azure.com).
2. Wybierz pozycję **Grupy zasobów**/**Nazwa_grupy_zasobów>** /**simpleWinVM**
3. Wybierz pozycję **Połącz** na górze strony.
4. Wybierz pozycję **Pobierz plik RDP**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na maszynie wirtualnej za pomocą hasła przechowywanego w magazynie kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Kolejne kroki

Podczas pracy z tym samouczkiem pobrano wpis tajny z usługi Azure Key Vault i użyto go podczas wdrażania szablonu.  Aby dowiedzieć się, jak tworzyć połączone szablony, zobacz:

> [!div class="nextstepaction"]
> [Create linked templates (Tworzenie połączonych szablonów)](./resource-manager-tutorial-create-linked-templates.md)
