---
title: Jak skonfigurować tożsamości zarządzane dla klastra usługi Azure Data Explorer
description: Dowiedz się, jak skonfigurować tożsamości zarządzane dla klastra usługi Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 900bf815917a4b7c9841860d663a2183b1ab71b3
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529672"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Data Explorer

[Tożsamość zarządzana z usługi Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) umożliwia klastrowi łatwy dostęp do innych zasobów chronionych przez usługę AAD, takich jak usługa Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowidizacji ani obracania żadnych wpisów tajnych. W tym artykule pokazano, jak utworzyć tożsamość zarządzaną dla klastrów usługi Azure Data Explorer. Konfiguracja tożsamości zarządzanej jest obecnie obsługiwana tylko w celu [włączenia kluczy zarządzanych przez klienta dla klastra](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Tożsamości zarządzane dla Usługi Azure Data Explorer nie będą zachowywać się zgodnie z oczekiwaniami, jeśli klaster usługi Azure Data Explorer jest migrowany między subskrypcjami lub dzierżawami. Aplikacja będzie musiała uzyskać nową tożsamość, co można zrobić, [wyłączając](#disable-a-system-assigned-identity) i [ponownie włączając](#add-a-system-assigned-identity) tę funkcję. Aby użyć nowej tożsamości, konieczne będzie również zaktualizowanie zasad dostępu do zasobów niższego szczebla.

## <a name="add-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu
                                                                                                    
Przypisz tożsamość przypisaną systemowi, która jest powiązana z klastrem i jest usuwana, jeśli klaster zostanie usunięty. Klaster może mieć tylko jedną tożsamość przypisaną do systemu. Tworzenie klastra z tożsamością przypisaną do systemu wymaga ustawienia dodatkowej właściwości w klastrze. Tożsamość przypisana do systemu jest dodawana przy użyciu szablonów Języka C#, ARM lub witryny Azure portal, jak opisano poniżej.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Dodawanie tożsamości przypisanej do systemu przy użyciu portalu Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Nowy klaster Eksploratora danych platformy Azure

1. [Tworzenie klastra eksploratora danych platformy Azure](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. Na karcie **Zabezpieczenia** > **system przypisany tożsamość**, wybierz **wł**. Aby usunąć przypisaną tożsamość systemu, wybierz opcję **Wył.**
2. Wybierz **przycisk Dalej:Znaczniki>** lub **Przejrzyj + utwórz,** aby utworzyć klaster.

    ![Dodawanie tożsamości przypisanej do systemu do nowego klastra](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Istniejący klaster Eksploratora danych platformy Azure

1. Otwórz istniejący klaster usługi Azure Data Explorer.
1. Wybierz **pozycję Ustawienia** > **tożsamości** w lewym okienku portalu.
1. Na karcie Przypisany > **okienku** **tożsamości:**
   1. Przesuń suwak **Stan** do **wł.**
   1. Wybierz pozycję **Zapisz**.
   1. W wyskakującym oknie wybierz pozycję **Tak**

    ![Dodawanie tożsamości przypisanej do systemu](media/managed-identities/turn-system-assigned-identity-on.png)

1. Po kilku minutach na ekranie pojawi się: 
  * **Identyfikator obiektu** — używany dla kluczy zarządzanych przez klienta 
  * **Przypisania ról** — kliknij łącze, aby przypisać odpowiednie role

    ![Tożsamość przypisaną przez system na](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Dodawanie tożsamości przypisanej do systemu przy użyciu języka C #

#### <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować tożsamość zarządzaną przy użyciu klienta Usługi Azure Data Explorer C#:

* Zainstaluj [pakiet Azure Data Explorer (Kusto) NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [pakiet Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) do uwierzytelniania.
* [Utwórz jednostkę aplikacji](/azure/active-directory/develop/howto-create-service-principal-portal) i usługi Azure AD, która może uzyskiwać dostęp do zasobów. Dodaj przypisanie roli w zakresie subskrypcji `Directory (tenant) ID`i `Application ID`otrzymujesz wymagane , i `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Tworzenie lub aktualizowanie klastra

1. Utwórz lub zaktualizuj klaster przy `Identity` użyciu właściwości:

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
    
2. Uruchom następujące polecenie, aby sprawdzić, czy klaster został pomyślnie utworzony lub zaktualizowany przy łączeniu tożsamości:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Jeśli wynik `ProvisioningState` zawiera `Succeeded` wartość, klaster został utworzony lub zaktualizowany i powinien mieć następujące właściwości:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`i `TenantId` są zastępowane identyfikatorami GUID. Właściwość `TenantId` identyfikuje dzierżawy usługi AAD, do której należy tożsamość. Jest `PrincipalId` unikatowy identyfikator dla nowej tożsamości klastra. W ramach usługi AAD podmiotu zabezpieczeń usługi ma taką samą nazwę, która została nadana do usługi app service lub wystąpienia usługi Azure Functions.

# <a name="arm-template"></a>[Szablon ARM](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Dodawanie tożsamości przypisanej do systemu przy użyciu szablonu usługi Azure Resource Manager

Szablon usługi Azure Resource Manager może służyć do automatyzacji wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej na temat wdrażania w Eksploratorze danych platformy Azure, zobacz [Tworzenie klastra i bazy danych Usługi Azure Data Explorer przy użyciu szablonu Usługi Azure Resource Manager](create-cluster-database-resource-manager.md).

Dodanie typu przypisanego do systemu nakazuje platformie Azure tworzenie tożsamości klastra i zarządzanie nią. Każdy zasób typu `Microsoft.Kusto/clusters` można utworzyć za pomocą tożsamości, dołączając następującą właściwość do definicji zasobu: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Przykład:

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

Po utworzeniu klastra ma następujące właściwości dodatkowe:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`i `<PRINCIPALID>` są zastępowane identyfikatorami GUID. Właściwość `TenantId` identyfikuje dzierżawy usługi AAD, do której należy tożsamość. Jest `PrincipalId` unikatowy identyfikator dla nowej tożsamości klastra. W ramach usługi AAD podmiotu zabezpieczeń usługi ma taką samą nazwę, która została nadana do usługi app service lub wystąpienia usługi Azure Functions.

---

## <a name="disable-a-system-assigned-identity"></a>Wyłączanie tożsamości przypisanej systemowi

Usunięcie tożsamości przypisanej przez system spowoduje również usunięcie jej z usługi AAD. Tożsamości przypisane do systemu są również automatycznie usuwane z usługi AAD po usunięciu zasobu klastra. Tożsamość przypisaną do systemu można usunąć, wyłączając tę funkcję.  Tożsamość przypisana do systemu jest usuwana przy użyciu szablonów języka C#, ARM lub witryny Azure portal, jak opisano poniżej.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Wyłączanie tożsamości przypisanej przez system przy użyciu witryny Azure portal

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Wybierz **pozycję Ustawienia** > **tożsamości** w lewym okienku portalu.
1. Na karcie Przypisany > **okienku** **tożsamości:**
    1. Przesuń suwak **Stan** do **wyłączonego**.
    1. Wybierz pozycję **Zapisz**.
    1. W wyskakującym oknie wybierz pozycję **Tak,** aby wyłączyć tożsamość przypisaną do systemu. **Okienko Tożsamości** powraca do tego samego warunku, co przed dodaniem tożsamości przypisanej przez system.

    ![Tożsamość przypisana do systemu wyłączona](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Usuwanie tożsamości przypisanej przez system przy użyciu języka C #

Uruchom następujące czynności, aby usunąć tożsamość przypisaną do systemu:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[Szablon ARM](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Usuwanie tożsamości przypisanej przez system przy użyciu szablonu usługi Azure Resource Manager

Uruchom następujące czynności, aby usunąć tożsamość przypisaną do systemu:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie klastrów eksploratora danych platformy Azure na platformie Azure](security.md)
* [Zabezpiecz swój klaster w usłudze Azure Data Explorer — witryna Azure portal,](manage-cluster-security.md) włączając szyfrowanie w spoczynku.
 * [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu języka C #](customer-managed-keys-csharp.md)
 * [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu usługi Azure Resource Manager](customer-managed-keys-resource-manager.md)
