---
title: Konfigurowanie tożsamości zarządzanych za pomocą konfiguracji aplikacji platformy Azure
description: Dowiedz się, jak działają tożsamości zarządzane w konfiguracji aplikacji platformy Azure i jak skonfigurować tożsamość zarządzaną
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623029"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Jak używać tożsamości zarządzanych w konfiguracji aplikacji platformy Azure

W tym temacie pokazano, jak utworzyć tożsamość zarządzaną dla konfiguracji aplikacji platformy Azure. Tożsamość zarządzana z usługi Azure Active Directory (AAD) umożliwia konfiguracji aplikacji platformy Azure łatwy dostęp do innych zasobów chronionych przez usługę AAD, takich jak usługa Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure. Nie wymaga inicjowania obsługi ani obracania żadnych wpisów tajnych. Aby uzyskać więcej informacji o tożsamościach zarządzanych w usłudze AAD, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

Wniosek może otrzymać dwa typy tożsamości:

- **Tożsamość przypisana przez system** jest powiązana z magazynem konfiguracji. Jest usuwany, jeśli magazyn konfiguracji zostanie usunięty. Magazyn konfiguracji może mieć tylko jedną tożsamość przypisaną do systemu.
- **Tożsamość przypisana przez użytkownika** to autonomiczny zasób platformy Azure, który można przypisać do magazynu konfiguracji. Magazyn konfiguracji może mieć wiele tożsamości przypisanych przez użytkownika.

## <a name="adding-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu

Tworzenie magazynu konfiguracji aplikacji z tożsamością przypisaną do systemu wymaga ustawienia dodatkowej właściwości w magazynie.

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenia [przypisywania tożsamości az appconfig] względem istniejącego magazynu konfiguracji. W tej sekcji dostępne są trzy opcje uruchamiania przykładów:

- Użyj [usługi Azure Cloud Shell](../cloud-shell/overview.md) z witryny Azure portal.
- Użyj osadzonej usługi Azure Cloud Shell za pomocą przycisku "Wypróbuj", znajdującego się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.1 lub nowszego), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia.

Poniższe kroki przebiegają przez tworzenie sklepu konfiguracji aplikacji i przypisywanie mu tożsamości przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login]. Użyj konta skojarzonego z subskrypcją platformy Azure:

    ```azurecli-interactive
    az login
    ```

1. Utwórz sklep konfiguracji aplikacji przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów używania interfejsu wiersza polecenia z konfiguracją aplikacji platformy Azure, zobacz [przykłady interfejsu wiersza polecenia konfiguracji aplikacji:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Uruchom polecenie [przypisywania tożsamości az appconfig,] aby utworzyć tożsamość przypisaną systemowi dla tego magazynu konfiguracji:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Dodawanie tożsamości przypisanej przez użytkownika

Utworzenie magazynu konfiguracji aplikacji z tożsamością przypisaną przez użytkownika wymaga utworzenia tożsamości, a następnie przypisania jej identyfikatora zasobów do magazynu.

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenia [przypisywania tożsamości az appconfig] względem istniejącego magazynu konfiguracji. W tej sekcji dostępne są trzy opcje uruchamiania przykładów:

- Użyj [usługi Azure Cloud Shell](../cloud-shell/overview.md) z witryny Azure portal.
- Użyj osadzonej usługi Azure Cloud Shell za pomocą przycisku "Wypróbuj", znajdującego się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 lub nowszego), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia.

Poniższe kroki przebiegają przez tworzenie tożsamości przypisanej przez użytkownika i magazynu konfiguracji aplikacji, a następnie przypisywanie tożsamości do magazynu przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login]. Użyj konta skojarzonego z subskrypcją platformy Azure:

    ```azurecli-interactive
    az login
    ```

1. Utwórz sklep konfiguracji aplikacji przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów używania interfejsu wiersza polecenia z konfiguracją aplikacji platformy Azure, zobacz [przykłady interfejsu wiersza polecenia konfiguracji aplikacji:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Utwórz tożsamość przypisaną `myUserAssignedIdentity` przez użytkownika o nazwie przy użyciu interfejsu wiersza polecenia.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    W danych wyjściowych tego polecenia zanotuj wartość `id` właściwości.

1. Uruchom polecenie [przypisywania tożsamości az appconfig,] aby przypisać nową tożsamość przypisaną przez użytkownika do tego magazynu konfiguracji. Użyj wartości właściwości, `id` która została odnotowana w poprzednim kroku.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Usuwanie tożsamości

Tożsamości przypisane do systemu można usunąć, wyłączając funkcję przy użyciu [az appconfig tożsamości usunąć](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) polecenie w usłudze Azure CLI. Tożsamości przypisane przez użytkownika można usunąć indywidualnie. Usunięcie tożsamości przypisanej przez system w ten sposób spowoduje również usunięcie jej z usługi AAD. Tożsamości przypisane do systemu są również automatycznie usuwane z aad po usunięciu zasobu aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji platformy ASP.NET Core używającej usługi Azure App Configuration](quickstart-aspnet-core-app.md)

[az appconfig przypisanie tożsamości]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
