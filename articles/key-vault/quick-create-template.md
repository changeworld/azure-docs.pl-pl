---
title: Szybki Start Azure — Tworzenie usługi Azure key vault i klucz tajny przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Przewodnik Szybki Start przedstawiający sposób tworzenia magazyny kluczy platformy Azure i dodawanie kluczy tajnych do magazynów przy użyciu szablonu usługi Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/04/2019
ms.author: jgao
ms.openlocfilehash: 4b774e5b0a5c64a9af9a5a54ba264c6226558a24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777749"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Szybki start: Ustawianie i pobieranie wpisu tajnego z usługi Azure Key Vault przy użyciu szablonu usługi Resource Manager

[Usługa Azure Key Vault](./key-vault-overview.md) to usługa w chmurze, który zapewnia bezpiecznego magazynu wpisów tajnych, takich jak klucze, hasła, certyfikaty i inne wpisy tajne. Ten przewodnik Szybki Start koncentruje się na proces wdrażania szablonu usługi Resource Manager, aby utworzyć magazyn kluczy i klucz tajny. Aby uzyskać więcej informacji na temat tworzenia szablonów usługi Resource Manager, zobacz [dokumentacji usługi Resource Manager](/azure/azure-resource-manager/) i [odwołanie do szablonu](/azure/templates/microsoft.keyvault/allversions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Szablon wymaga Twojego identyfikatora obiektu użytkownika z usługi Azure Active Directory w celu skonfigurowania uprawnień. Poniższa procedura pozwala pobrać identyfikator obiektu (GUID).

    1. Uruchom następujące polecenie programu Azure PowerShell lub wiersza polecenia platformy Azure, wybierz **wypróbuj**, a następnie wklej skrypt do okienka powłoki. Wklej skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz **Wklej**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Zanotuj identyfikator obiektu. Będą potrzebne w następnej sekcji tego przewodnika Szybki Start.

## <a name="create-a-vault-and-a-secret"></a>Tworzenie magazynu i klucz tajny

Szablon używany w tym przewodniku Szybki Start jest z [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Więcej przykładów szablonów usługi Azure Key Vault można znaleźć [tutaj](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Wybierz lub wprowadź następujące wartości.  

    ![Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager w portalu](./media/quick-create-template/create-key-vault-using-template-portal.png)

    O ile nie zostanie określony, użyj wartości domyślnej, aby utworzyć magazyn kluczy oraz klucz tajny.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: Wybierz **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**. 
    * **Lokalizacja**: wybierz lokalizację.  Na przykład **Środkowe stany USA**.
    * **Nazwa usługi Key Vault**: wprowadź unikatową nazwę dla usługi key vault.  
    * **Identyfikator dzierżawy**: funkcja szablonu automatycznie pobiera identyfikator dzierżawy.  Nie zmieniaj wartości domyślnej
    * **Identyfikator użytkownika usługi AD**: Wprowadź identyfikator obiektu użytkownika usługi Azure AD, możesz pobrać z [wymagania wstępne](#prerequisites).
    * **Nazwa wpisu tajnego**: Wprowadź nazwę klucza tajnego, który jest przechowywany w magazynie kluczy.  Na przykład **adminpassword**
    * **Wartość wpisu tajnego**: wprowadź wartość wpisu tajnego.  Jeśli przechowujesz hasła, zaleca się używać wygenerowane hasło utworzone w sekcji wymagania wstępne.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: Zaznacz.
3. Wybierz pozycję **Kup**.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Możesz sprawdzić magazyn kluczy oraz klucz tajny za pomocą witryny Azure portal lub użyj następującego skryptu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby wyświetlić utworzony klucz tajny.

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
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pomocą wiersza polecenia platformy Azure lub programu Azure Powershell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```
```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Kolejne kroki

* [Strona główna usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentacja usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK For Node (Zestaw Azure SDK dla platformy Node)](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Dokumentacja interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/keyvault/)
