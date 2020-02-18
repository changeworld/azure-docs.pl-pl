---
title: Jak skonfigurować tożsamości zarządzane dla klastra usługi Azure Eksplorator danych
description: Dowiedz się, jak skonfigurować tożsamości zarządzane dla klastra Eksplorator danych platformy Azure.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e76ae2e072bb780ac9788902e9157db871e4f09d
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373374"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Eksplorator danych

[Zarządzana tożsamość z Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) umożliwia klastrowi łatwe uzyskiwanie dostępu do innych zasobów chronionych przez usługi AAD, takich jak Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowizacji ani rotacji żadnych wpisów tajnych. W tym artykule pokazano, jak utworzyć zarządzaną tożsamość dla klastrów Eksplorator danych platformy Azure. Konfiguracja tożsamości zarządzanej jest obecnie obsługiwana tylko w celu [włączenia kluczy zarządzanych przez klienta w klastrze](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Tożsamość zarządzana dla usługi Azure Eksplorator danych nie będzie działać zgodnie z oczekiwaniami, jeśli Twoja aplikacja jest migrowana między subskrypcjami lub dzierżawcami. Aplikacja będzie musiała uzyskać nową tożsamość, którą można wykonać, wyłączając i ponownie włączając funkcję przy użyciu polecenia [Usuń tożsamość](#remove-an-identity). Zasady dostępu do zasobów podrzędnych również muszą zostać zaktualizowane, aby można było korzystać z nowej tożsamości.

## <a name="add-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu

Do klastra można przypisać **tożsamość przypisaną do systemu** , która jest powiązana z klastrem, i jest usuwana, Jeśli klaster został usunięty. Klaster może mieć tylko jedną tożsamość przypisaną do systemu. Utworzenie klastra z tożsamością przypisaną do systemu wymaga ustawienia dodatkowej właściwości w klastrze.

### <a name="add-a-system-assigned-identity-using-c"></a>Dodawanie tożsamości przypisanej do systemu przy użyciuC#

Aby skonfigurować tożsamość zarządzaną za pomocą klienta Eksplorator danych C# platformy Azure, wykonaj następujące czynności:

* Zainstaluj [pakiet NuGet platformy Azure Eksplorator danych (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [pakiet NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) na potrzeby uwierzytelniania.
* Aby uruchomić Poniższy przykład, [Utwórz aplikację usługi Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) i nazwę główną usługi, która może uzyskiwać dostęp do zasobów. Możesz dodać przypisanie roli do zakresu subskrypcji i uzyskać wymagane `Directory (tenant) ID`, `Application ID`i `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Utwórz lub zaktualizuj klaster

1. Utwórz lub zaktualizuj klaster przy użyciu właściwości `Identity`:

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
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Uruchom następujące polecenie, aby sprawdzić, czy klaster został pomyślnie utworzony lub zaktualizowany przy użyciu tożsamości:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Jeśli wynik zawiera `ProvisioningState` z wartością `Succeeded`, klaster został utworzony lub zaktualizowany i powinien mieć następujące właściwości:
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` i `TenantId` są zastępowane identyfikatorami GUID. Właściwość `TenantId` identyfikuje dzierżawcę usługi AAD, do której należy tożsamość. `PrincipalId` jest unikatowym identyfikatorem nowej tożsamości klastra. W usłudze AAD nazwa główna usługi ma taką samą nazwę, która została nadana App Service lub Azure Functions wystąpieniem.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Dodawanie tożsamości przypisanej do systemu przy użyciu szablonu Azure Resource Manager

Szablon Azure Resource Manager może służyć do automatyzowania wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej o wdrażaniu do usługi Azure Eksplorator danych, zobacz [Tworzenie klastra Eksplorator danych platformy Azure i bazy danych przy użyciu szablonu Azure Resource Manager](create-cluster-database-resource-manager.md).

Dodanie typu przypisanego do systemu informuje platformę Azure, aby utworzył tożsamość klastra i zarządzać nią. Każdy zasób typu `Microsoft.Kusto/clusters` można utworzyć za pomocą tożsamości, dołączając następującą właściwość w definicji zasobu: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Na przykład:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

Po utworzeniu klastra ma następujące dodatkowe właściwości:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` i `<PRINCIPALID>` są zastępowane identyfikatorami GUID. Właściwość `TenantId` identyfikuje dzierżawcę usługi AAD, do której należy tożsamość. `PrincipalId` jest unikatowym identyfikatorem nowej tożsamości klastra. W usłudze AAD nazwa główna usługi ma taką samą nazwę, która została nadana App Service lub Azure Functions wystąpieniem.

## <a name="remove-an-identity"></a>Usuwanie tożsamości

Usunięcie tożsamości przypisanej do systemu spowoduje również usunięcie jej z usługi AAD. Tożsamości przypisane do systemu są również automatycznie usuwane z usługi AAD po usunięciu zasobu klastra. Tożsamość przypisana przez system można usunąć, wyłączając funkcję:

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie klastrów usługi Azure Eksplorator danych na platformie Azure](security.md)
* [Zabezpiecz swój klaster na platformie Azure Eksplorator danych — Azure Portal](manage-cluster-security.md) przez włączenie szyfrowania w stanie spoczynku.
 * [Konfigurowanie kluczy zarządzanych przez klienta przy użyciuC#](customer-managed-keys-csharp.md)
 * [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu Azure Resource Manager](customer-managed-keys-resource-manager.md)
