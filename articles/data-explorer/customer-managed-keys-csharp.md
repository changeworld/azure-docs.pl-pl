---
title: Konfigurowanie kluczy zarządzanych przez klienta przy użyciuC#
description: W tym artykule opisano sposób konfigurowania szyfrowania kluczy zarządzanych przez klienta na danych w usłudze Azure Eksplorator danych.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: ac37c0e1f1f7d769a881c5965744b29d40468486
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276682"
---
# <a name="configure-customer-managed-keys-using-c"></a>Konfigurowanie kluczy zarządzanych przez klienta przy użyciuC#

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Szablon usługi Azure Resource Manager](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

W tej sekcji pokazano, jak skonfigurować szyfrowanie kluczy zarządzanych przez klienta przy użyciu klienta Eksplorator danych C# platformy Azure. 

### <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

### <a name="install-c-nuget"></a>Zainstaluj C# pakiet NuGet

* Zainstaluj [pakiet NuGet platformy Azure Eksplorator danych (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Zainstaluj [pakiet NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) na potrzeby uwierzytelniania.

### <a name="authentication"></a>Authentication

Aby uruchomić przykłady z tego artykułu, należy [utworzyć aplikację usługi Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) i nazwę główną usługi, która może uzyskiwać dostęp do zasobów. Możesz dodać przypisanie roli do zakresu subskrypcji i uzyskać wymagane `Directory (tenant) ID`, `Application ID`i `Client Secret`.

### <a name="configure-cluster"></a>Konfigurowanie klastra

Domyślnie usługa Azure Eksplorator danych Encryption używa kluczy zarządzanych przez firmę Microsoft. Skonfiguruj klaster Eksplorator danych platformy Azure pod kątem używania kluczy zarządzanych przez klienta i określ klucz, który ma zostać skojarzony z klastrem.

1. Zaktualizuj klaster przy użyciu następującego kodu:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Uruchom następujące polecenie, aby sprawdzić, czy klaster został pomyślnie zaktualizowany:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Jeśli wynik zawiera `ProvisioningState` z wartością `Succeeded`, klaster został pomyślnie zaktualizowany.

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować klaster tak, aby korzystał z nowej wersji. Najpierw Wywołaj `Get-AzKeyVaultKey`, aby uzyskać najnowszą wersję klucza. Następnie zaktualizuj właściwości magazynu kluczy klastra, aby użyć nowej wersji klucza, jak pokazano w [Konfiguruj klaster](#configure-cluster).

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie klastrów usługi Azure Eksplorator danych na platformie Azure](security.md)
* [Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Eksplorator danych](managed-identities.md)
* [Zabezpiecz swój klaster na platformie Azure Eksplorator danych — Azure Portal](manage-cluster-security.md) przez włączenie szyfrowania w stanie spoczynku.
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu Azure Resource Manager](customer-managed-keys-resource-manager.md)


