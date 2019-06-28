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
ms.openlocfilehash: b91a3488750795e8ee9df6602bb2b3df8a9b08ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436573"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Samouczek: Integrowanie usługi Azure Key Vault w danym wdrożeniu szablonu usługi Resource Manager

Dowiedz się, jak pobrać Wpisy tajne z usługi Azure key vault i przekazać wpisy tajne jako parametry podczas wdrażania usługi Azure Resource Manager. Wartość tego parametru nie są udostępniane, ponieważ odwołują się tylko identyfikator magazynu kluczy. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure Key Vault do przekazywania wartości parametru secure podczas wdrażania](./resource-manager-keyvault-parameter.md).

W [Ustaw kolejność wdrażania zasobów](./resource-manager-tutorial-create-templates-with-dependent-resources.md) samouczka, utworzysz maszynę wirtualną (VM). Należy podać nazwę użytkownika administratora maszyny Wirtualnej i hasło. Zamiast podawać hasła, możesz wstępnie przechowywania haseł w usłudze Azure key vault, a następnie Dostosuj szablon można pobrać hasło z usługi key vault podczas wdrażania.

![Diagram przedstawiający integrację szablon usługi Resource Manager z usługą key vault](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

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

* [Visual Studio Code](https://code.visualstudio.com/) z [rozszerzenie narzędzia usługi Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, należy użyć wygenerowane hasło dla konta administratora maszyny Wirtualnej. Poniżej przedstawiono przykład generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Sprawdź, czy wygenerowane hasło spełnia wymagania dotyczące hasła maszyny Wirtualnej. Każda usługa platformy Azure ma określone wymagania dotyczące hasła. Aby uzyskać wymagania dotyczące hasła maszyny Wirtualnej, zobacz [jakie są wymagania dotyczące hasła, podczas tworzenia maszyny Wirtualnej?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Przygotowanie magazynu kluczy

W tej sekcji służy do tworzenia magazynu kluczy i wpisu tajnego do niej dodać, tak, aby podczas wdrażania szablonu, można pobrać klucza tajnego. Istnieje wiele sposobów, aby utworzyć magazyn kluczy. W tym samouczku użyjesz programu Azure PowerShell, aby wdrożyć [szablonu usługi Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Ten szablon wykonuje następujące czynności:

* Tworzy magazyn kluczy przy użyciu `enabledForTemplateDeployment` właściwości włączone. Ta właściwość musi być *true* przed przystąpieniem do wdrożenia szablonu proces może uzyskać dostępu do kluczy tajnych, które są zdefiniowane w usłudze key vault.
* Dodaje wpis tajny do magazynu kluczy. Klucz tajny przechowuje hasło administratora maszyny Wirtualnej.

> [!NOTE]
> Jako użytkownik, który jest wdrożenie szablonu maszyny wirtualnej, jeśli nie jesteś właścicielem lub współautorem do magazynu kluczy, właściciela lub współautora musi udzielić dostępu do *Microsoft.KeyVault/vaults/deploy/action* uprawnień dla magazyn kluczy. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure Key Vault do przekazywania wartości parametru secure podczas wdrażania](./resource-manager-keyvault-parameter.md).

Aby uruchomić poniższy skrypt programu Azure PowerShell, zaznacz **wypróbuj** można otworzyć usługi Azure Cloud Shell. Wklej skrypt, kliknij prawym przyciskiem myszy w okienku shell, a następnie wybierz **Wklej**.

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

> [!IMPORTANT]
> * Nazwa grupy zasobów jest nazwa projektu, ale z **rg** dołączone do niego. Aby łatwiej [usuwaj zasobów utworzonych w tym samouczku](#clean-up-resources), użyj nazwy tej samej nazwie i zasobów grupy projektów, kiedy należy [wdrażanie kolejny szablon](#deploy-the-template).
> * Domyślna nazwa wpisu tajnego to **vmAdminPassword**. Jego zapisane na stałe w szablonie.
> * Aby włączyć szablon można pobrać klucza tajnego, należy włączyć zasady dostępu o nazwie "Włącz dostęp do usługi Azure Resource Manager dla szablonu wdrożenia" dla usługi key vault. Ta zasada jest włączona w szablonie. Aby uzyskać więcej informacji na temat zasad dostępu, zobacz [wdrażanie magazynów kluczy i wpisów tajnych](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Szablon ma jedną wartość danych wyjściowych, o nazwie *keyVaultId*. Zanotuj wartość Identyfikatora do późniejszego użycia w przypadku wdrażania maszyny wirtualnej. Format Identyfikatora zasobów jest:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Podczas kopiowania i wklej identyfikator może można podzielić na wiele wierszy. Scalenie wierszy i usunąć dodatkowych spacji.

Aby zweryfikować wdrożenie, uruchom następujące polecenie programu PowerShell w okienko powłoki można pobrać klucza tajnego w postaci zwykłego tekstu. Polecenie działa tylko w tej samej sesji powłoki, ponieważ jest używana zmienna *$keyVaultName*, który jest zdefiniowany w poprzednim skrypcie programu PowerShell.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Po przygotowaniu magazynu kluczy i klucz tajny. Poniższe sekcje pokazują, jak dostosować istniejący szablon można pobrać klucza tajnego podczas wdrażania.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Nosi nazwę szablonu, który jest używany w tym samouczku [wdrożyć prostą maszynę Wirtualną Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **Plik** > **Otwórz plik**.

1. W **nazwy pliku** pole, wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Wybierz pozycję **Open (Otwórz)** , aby otworzyć plik. Scenariusz jest taka sama jak ta, która jest używana w [samouczka: Tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
   Szablon definiuje pięć zasobów:

   * `Microsoft.Storage/storageAccounts`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Dobrze jest mieć podstawowych informacji na temat szablonu, aby dostosować go.

1. Wybierz **pliku** > **Zapisz jako**, a następnie zapisz kopię pliku na komputer lokalny o nazwie *azuredeploy.json*.

1. Powtórz kroki 1 – 3, aby otworzyć następującego adresu URL, a następnie zapisz plik jako *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Edytowanie pliku parametrów

Nie musisz wprowadzać żadnych zmian w pliku szablonu.

1. W programie Visual Studio Code Otwórz *azuredeploy.parameters.json* Jeśli nie jest już otwarty.
1. Aktualizacja `adminPassword` parametru, aby:

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
    > Zastąp wartość **identyfikator** o identyfikatorze zasobu magazynu kluczy, który został utworzony w poprzedniej procedurze.

    ![Integracja magazynu kluczy i pliku parametrów wdrożenia maszyny wirtualnej szablonu usługi Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Zaktualizuj następujące wartości:

    * **AdminUsername**: Nazwa konta administratora maszyny wirtualnej.
    * **dnsLabelPrefix**: Nazwa wartości dnsLabelPrefix.

    Aby uzyskać przykłady nazw Zobacz powyższej ilustracji.

1. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z instrukcjami w [wdrożenia szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Przekaż zarówno *azuredeploy.json* i *azuredeploy.parameters.json* do usługi Cloud Shell, a następnie użyj następującego skryptu programu PowerShell, aby wdrożyć szablon:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Podczas wdrażania szablonu, należy użyć tej samej grupie zasobów, które było używane w magazynie kluczy. Takie podejście ułatwia dla Ciebie wyczyścić zasoby, ponieważ musisz usunąć tylko jedna grupa zasobów zamiast dwóch.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Po pomyślnym wdrożeniu maszyny wirtualnej, należy przetestować poświadczeń logowania przy użyciu hasła, które są przechowywane w magazynie kluczy.

1. Otwórz [portal Azure](https://portal.azure.com).

1. Wybierz **grup zasobów** >  **\<*YourResourceGroupName*>**  > **simpleWinVM** .
1. Wybierz **połączyć** u góry.
1. Wybierz **Pobierz plik RDP**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do maszyny wirtualnej przy użyciu hasła, które są przechowywane w magazynie kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów platformy Azure, należy wyczyścić zasoby, które wdrożone przez usunięcie grupy zasobów.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wpisu tajnego jest pobierane z usługi Azure key vault. Klucz tajny jest następnie używany we wdrożeniu szablonu. Aby dowiedzieć się, jak tworzyć połączone szablony, zobacz:

> [!div class="nextstepaction"]
> [Create linked templates (Tworzenie połączonych szablonów)](./resource-manager-tutorial-create-linked-templates.md)
