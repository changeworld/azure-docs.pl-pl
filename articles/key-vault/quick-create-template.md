---
title: Przewodnik Szybki Start platformy Azure — tworzenie magazynu kluczy platformy Azure i wpisu tajnego przy użyciu szablonu Azure Resource Manager | Microsoft Docs
description: Przewodnik Szybki Start przedstawiający sposób tworzenia magazynów kluczy Azure i dodawania wpisów tajnych do magazynów przy użyciu szablonu Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/22/2019
ms.author: jgao
ms.openlocfilehash: b27caa3d91d67ad63bfbf5e7c549d690980cdd7b
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934434"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Szybki start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu szablonu Menedżer zasobów

[Azure Key Vault](./key-vault-overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn dla wpisów tajnych, takich jak klucze, hasła, certyfikaty i inne wpisy tajne. Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Menedżer zasobów, aby utworzyć magazyn kluczy i klucz tajny. Aby uzyskać więcej informacji na temat opracowywania szablonów Menedżer zasobów, zobacz [dokumentację Menedżer zasobów i dokumentacja](/azure/azure-resource-manager/) [szablonu](/azure/templates/microsoft.keyvault/allversions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Szablon wymaga Twojego identyfikatora obiektu użytkownika z usługi Azure Active Directory w celu skonfigurowania uprawnień. Poniższa procedura pozwala pobrać identyfikator obiektu (GUID).

    1. Uruchom następujące Azure PowerShell lub polecenie interfejsu wiersza polecenia platformy Azure, wybierając pozycję **Wypróbuj**, a następnie wklej skrypt do okienka powłoki. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Zanotuj identyfikator obiektu. Będzie ona potrzebna w następnej sekcji tego przewodnika Szybki Start.

## <a name="create-a-vault-and-a-secret"></a>Tworzenie magazynu i wpisu tajnego

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Więcej przykładów szablonów Azure Key Vault można znaleźć [tutaj](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Wybierz lub wprowadź następujące wartości.  

    ![Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager w portalu](./media/quick-create-template/create-key-vault-using-template-portal.png)

    O ile nie jest określona, użyj wartości domyślnej, aby utworzyć magazyn kluczy i klucz tajny.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**. 
    * **Lokalizacja**: wybierz lokalizację.  Na przykład **Środkowe stany USA**.
    * **Nazwa Key Vault**: Wprowadź nazwę magazynu kluczy, która musi być globalnie unikatowa w przestrzeni nazw. Vault.Azure.NET.  
    * **Identyfikator dzierżawy**: funkcja szablonu automatycznie pobiera identyfikator dzierżawy.  Nie zmieniaj wartości domyślnej.
    * **Identyfikator użytkownika usługi AD**: Wprowadź identyfikator obiektu użytkownika usługi Azure AD, który został pobrany z [wymagań wstępnych](#prerequisites).
    * **Nazwa wpisu tajnego**: Wprowadź nazwę wpisu tajnego, który jest przechowywany w magazynie kluczy.  Na przykład klucz **AdminPassword**.
    * **Wartość wpisu tajnego**: wprowadź wartość klucza tajnego.  W przypadku przechowywania hasła zaleca się użycie wygenerowanego hasła utworzonego w sekcji wymagania wstępne.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: Wybierz.
3. Wybierz pozycję **Kup**.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Możesz użyć Azure Portal, aby sprawdzić Magazyn kluczy i klucz tajny, lub użyć następującego interfejsu wiersza polecenia platformy Azure lub skryptu Azure PowerShell, aby wyświetlić utworzony wpis tajny.

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName
```

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```
```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Następne kroki

* [Strona główna usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentacja usługi Azure Key Vault](index.yml)
* [Azure SDK For Node (Zestaw Azure SDK dla platformy Node)](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Dokumentacja interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/keyvault/)
