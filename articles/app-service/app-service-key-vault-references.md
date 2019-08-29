---
title: Odwołania Key Vault-Azure App Service | Microsoft Docs
description: Dokumentacja koncepcyjna i przewodnik konfigurowania dla Azure Key Vault odwołań w Azure App Service i Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 30bd7c68ae1c88aba288b515d0ec32581f90b868
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088184"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Użyj Key Vault odwołań dla App Service i Azure Functions (wersja zapoznawcza)

> [!NOTE] 
> Odwołania Key Vault są obecnie dostępne w wersji zapoznawczej.

W tym temacie przedstawiono sposób pracy z wpisami tajnymi Azure Key Vault w App Service lub Azure Functions aplikacji bez konieczności wprowadzania jakichkolwiek zmian w kodzie. [Azure Key Vault](../key-vault/key-vault-overview.md) to usługa zapewniająca scentralizowane zarządzanie kluczami tajnymi z pełną kontrolą nad zasadami dostępu i historią inspekcji.

## <a name="granting-your-app-access-to-key-vault"></a>Udzielanie aplikacji dostępu do Key Vault

Aby można było odczytać wpisy tajne z Key Vault, należy utworzyć magazyn i nadać aplikacji uprawnienia dostępu do niej.

1. Utwórz magazyn kluczy, postępując zgodnie z [przewodnikiem Szybki start Key Vault](../key-vault/quick-create-cli.md).

1. Utwórz skojarzoną z [systemem tożsamość zarządzaną](overview-managed-identity.md) dla aplikacji.

   > [!NOTE] 
   > Odwołania Key Vault obecnie obsługują tylko tożsamości zarządzane przypisane do systemu. Nie można używać tożsamości przypisanych do użytkownika.

1. Utwórz [zasady dostępu w Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) dla utworzonej wcześniej tożsamości aplikacji. Włącz uprawnienie "Pobieranie" klucza tajnego dla tych zasad. Nie należy konfigurować "autoryzowanej aplikacji" ani `applicationId` ustawień, ponieważ nie są one zgodne z zarządzaną tożsamością.

    Przyznanie dostępu do tożsamości aplikacji w magazynie kluczy jest operacją jednorazowej, która pozostanie taka sama dla wszystkich subskrypcji platformy Azure. Można go użyć do wdrożenia dowolnej liczby certyfikatów. 

## <a name="reference-syntax"></a>Składnia odwołania

Odwołanie Key Vault ma postać `@Microsoft.KeyVault({referenceString})`, gdzie `{referenceString}` jest zastępowana jedną z następujących opcji:

> [!div class="mx-tdBreakAll"]
> | Ciąg odwołania                                                            | Opis                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** powinien być pełnym identyfikatorem URI płaszczyzny danych klucza tajnego w Key Vault, w tym wersji, np. https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | Nazwa **magazynu** powinna być nazwą zasobu Key Vault. **Wpis tajny** musi być nazwą docelowego wpisu tajnego. **Wersjawpisutajnego** powinna być wersją klucza tajnego do użycia. |

> [!NOTE] 
> W bieżącej wersji zapoznawczej wymagania są wymagane. W przypadku rotacji kluczy tajnych należy zaktualizować wersję w konfiguracji aplikacji.

Na przykład kompletne odwołanie będzie wyglądać następująco:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Alternatywnie:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Ustawienia aplikacji źródłowej z Key Vault

Odwołania Key Vault mogą być używane jako wartości [ustawień aplikacji](configure-common.md#configure-app-settings), co pozwala zachować wpisy tajne w Key Vault zamiast konfiguracji lokacji. Ustawienia aplikacji są bezpiecznie szyfrowane w stanie spoczynku, ale jeśli potrzebujesz funkcji tajnego zarządzania, należy przejść do Key Vault.

Aby użyć odwołania Key Vault dla ustawienia aplikacji, należy ustawić odwołanie jako wartość ustawienia. Twoja aplikacja może odwoływać się do klucza tajnego za pomocą jego klucza jako normalnego. Nie są wymagane żadne zmiany w kodzie.

> [!TIP]
> Większość ustawień aplikacji używających odwołań Key Vault powinna być oznaczona jako ustawienia miejsca, ponieważ dla każdego środowiska należy mieć oddzielne magazyny.

### <a name="azure-resource-manager-deployment"></a>Wdrożenie usługi Azure Resource Manager

W przypadku automatyzowania wdrożeń zasobów za pomocą szablonów Azure Resource Manager może być konieczne sekwencjonowanie zależności w określonej kolejności, aby ta funkcja działała. Pamiętaj, że musisz zdefiniować ustawienia aplikacji jako własny zasób zamiast używać `siteConfig` właściwości w definicji lokacji. Wynika to z faktu, że lokacja musi być zdefiniowana jako pierwsza, aby można było utworzyć tożsamość przypisaną do systemu i użyć jej w zasadach dostępu.

Przykład psuedo-Template dla aplikacji funkcji może wyglądać następująco:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> W tym przykładzie wdrożenie kontroli źródła zależy od ustawień aplikacji. Jest to zwykle niebezpieczne zachowanie, ponieważ aktualizacja ustawienia aplikacji zachowuje się asynchronicznie. Jednak ze względu na `WEBSITE_ENABLE_SYNC_UPDATE_SITE` to, że zostało dołączone ustawienie aplikacji, aktualizacja jest synchroniczna. Oznacza to, że wdrożenie kontroli źródła rozpocznie się dopiero po całkowitym zaktualizowaniu ustawień aplikacji.
