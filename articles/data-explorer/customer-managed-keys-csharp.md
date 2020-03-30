---
title: 'Konfigurowanie kluczy zarządzanych przez klienta przy użyciu języka C #'
description: W tym artykule opisano sposób konfigurowania szyfrowania kluczy zarządzanych przez klienta na danych w Eksploratorze danych platformy Azure.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a00b0876c4a188b932032129ed5a394e94198930
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297945"
---
# <a name="configure-customer-managed-keys-using-c"></a>Konfigurowanie kluczy zarządzanych przez klienta przy użyciu języka C #

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Szablon usługi Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta

W tej sekcji pokazano, jak skonfigurować szyfrowanie kluczy zarządzanych przez klienta przy użyciu klienta Usługi Azure Data Explorer C#. 

### <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać **bezpłatną** [wersję programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niej. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.

### <a name="install-c-nuget"></a>Zainstaluj C# NuGet

* Zainstaluj [pakiet Azure Data Explorer (Kusto) NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Zainstaluj [pakiet Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) do uwierzytelniania.

### <a name="authentication"></a>Uwierzytelnianie

Aby uruchomić przykłady w tym artykule, należy utworzyć aplikację usługi Azure AD i [jednostkę](/azure/active-directory/develop/howto-create-service-principal-portal) usługi, która może uzyskać dostęp do zasobów. Można dodać przypisanie roli w zakresie subskrypcji `Directory (tenant) ID` `Application ID`i `Client Secret`uzyskać wymagane , i .

### <a name="configure-cluster"></a>Konfigurowanie klastra

Domyślnie szyfrowanie usługi Azure Data Explorer używa kluczy zarządzanych przez firmę Microsoft. Skonfiguruj klaster usługi Azure Data Explorer do używania kluczy zarządzanych przez klienta i określ klucz do skojarzenia z klastrem.

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

    Jeśli wynik `ProvisioningState` zawiera `Succeeded` wartość, klaster został pomyślnie zaktualizowany.

## <a name="update-the-key-version"></a>Aktualizowanie wersji klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować klaster, aby użyć nowej wersji. Najpierw zadzwoń, `Get-AzKeyVaultKey` aby uzyskać najnowszą wersję klucza. Następnie zaktualizuj właściwości magazynu kluczy klastra, aby użyć nowej wersji klucza, jak pokazano w [temacie Konfigurowanie klastra](#configure-cluster).

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie klastrów eksploratora danych platformy Azure na platformie Azure](security.md)
* [Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Data Explorer](managed-identities.md)
* [Zabezpiecz swój klaster w usłudze Azure Data Explorer — witryna Azure portal,](manage-cluster-security.md) włączając szyfrowanie w spoczynku.
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu usługi Azure Resource Manager](customer-managed-keys-resource-manager.md)


