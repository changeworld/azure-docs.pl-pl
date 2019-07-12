---
title: Odwołania do usługi Key Vault — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Koncepcyjny Przewodnik instalacji i odwołania dla odwołania do usługi Azure Key Vault w usłudze Azure App Service i Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e7a049c8def0a5014aeb8a0e7a16aaa8def28009
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705695"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Użyj odwołania do usługi Key Vault dla usługi App Service i Azure Functions (wersja zapoznawcza)

> [!NOTE] 
> Odwołania do usługi Key Vault są obecnie dostępne w wersji zapoznawczej.

W tym temacie dowiesz się, jak pracować z wpisy tajne z usługi Azure Key Vault w aplikacji usługi App Service lub Azure Functions bez żadnych zmian w kodzie. [Usługa Azure Key Vault](../key-vault/key-vault-overview.md) to usługa, która umożliwia zarządzanie scentralizowane wpisów tajnych, z pełną kontrolą nad Historia zasad i inspekcja dostępu.

## <a name="granting-your-app-access-to-key-vault"></a>Udzielanie dostępu do usługi Key Vault

Aby przeczytać wpisy tajne z magazynu Key Vault, musisz mieć utworzony magazyn i zezwolić aplikacji na dostęp do niego.

1. Tworzenie magazynu kluczy, postępując zgodnie z [szybkiego startu usługi Key Vault](../key-vault/quick-create-cli.md).

1. Tworzenie [przypisany systemowo tożsamości zarządzanej](overview-managed-identity.md) dla aplikacji.

   > [!NOTE] 
   > Usługa Key Vault odwołuje się do aktualnie tylko przypisana przez system obsługi tożsamości zarządzanej. Nie można używać tożsamości przypisanych przez użytkownika.

1. Tworzenie [zasady w usłudze Key Vault dostępu](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) tożsamości aplikacji została utworzona wcześniej. Włącz uprawnienie wpisu tajnego "Get" te zasady. Nie należy konfigurować "uprawnienia aplikacji" lub `applicationId` ustawienia, ponieważ nie jest zgodny z tożsamości zarządzanej.

    Udzielanie dostępu do aplikacji tożsamości w usłudze key vault jest to jednorazowa operacja i pozostaną takie same dla wszystkich subskrypcji platformy Azure. Służy on do wdrożenia dowolną liczbę certyfikatów, jak chcesz. 

## <a name="reference-syntax"></a>Składnia odwołania

Dokumentacja usługi Key Vault ma postać `@Microsoft.KeyVault({referenceString})`, gdzie `{referenceString}` jest zastępowany przez jedną z następujących opcji:

> [!div class="mx-tdBreakAll"]
> | Ciąg odwołania                                                            | Opis                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** powinien być identyfikator URI danych płaszczyzna klucz tajny w usłudze Key Vault, łącznie z wersją, np. https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** powinien nazwę zasobu usługi Key Vault. **SecretName** powinien zawierać nazwę docelowego wpisu tajnego. **SecretVersion** powinna mieć taką wersję wpisu tajnego do użycia. |

> [!NOTE] 
> W bieżącej wersji zapoznawczej wersje są wymagane. W przypadku rotacji kluczy tajnych, należy zaktualizować wersję w konfiguracji aplikacji.

Na przykład pełną dokumentację powinien wyglądać następująco:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Alternatywnie:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Ustawienia aplikacji źródłowej z usługi Key Vault

Odwołania do usługi Key Vault może służyć jako wartości [ustawienia aplikacji](configure-common.md#configure-app-settings), dzięki czemu można zachować wpisy tajne w usłudze Key Vault, zamiast konfiguracji witryny. Ustawienia aplikacji są bezpiecznie szyfrowane w stanie spoczynku, ale jeśli potrzebujesz możliwości zarządzania wpisami tajnymi, należy przejść do usługi Key Vault.

Aby użyć odwołanie do usługi Key Vault dla ustawienia aplikacji, ustaw odwołanie jako wartość ustawienia. Aplikacja odwoływać się do klucza tajnego za pomocą jego klucza, jak zwykle. Wymagane są bez zmian w kodzie.

> [!TIP]
> Większość ustawień aplikacji za pomocą usługi Key Vault odwołania powinien być oznaczony jako ustawienia gniazda, jak powinny mieć osobne magazynów dla każdego środowiska.

### <a name="azure-resource-manager-deployment"></a>Wdrożenie usługi Azure Resource Manager

Podczas automatyzacji wdrożenia zasobów za pomocą szablonów usługi Azure Resource Manager, może być konieczne sekwencji zależności w określonej kolejności, aby ta funkcja mogła działać. Uwaga: musisz ustawienia aplikacji jest definiowana jako ich własnych zasobów zamiast używania `siteConfig` właściwości w definicji witryny. Jest to spowodowane lokacji musi być definiowana jako pierwsza, tak aby tożsamości przypisanych przez system jest tworzony z nim i mogą być używane w zasadach dostępu.

Przykład psuedo szablonów dla aplikacji funkcji może wyglądać następująco:

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
> W tym przykładzie wdrożenia kontroli źródła zależy od ustawień aplikacji. Jest to zwykle niebezpieczne zachowania, ponieważ aktualizacja ustawień aplikacji zachowuje się asynchronicznie. Jednakże ponieważ wprowadzono `WEBSITE_ENABLE_SYNC_UPDATE_SITE` aplikacji ustawienia aktualizacji jest synchroniczne. Oznacza to, że wdrożenia kontroli źródła tylko rozpocznie się po w pełni zaktualizowano ustawienia aplikacji.
