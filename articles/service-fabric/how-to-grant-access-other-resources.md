---
title: Udzielanie dostępu aplikacji do innych zasobów platformy Azure
description: W tym artykule wyjaśniono, jak udzielić aplikacji sieci szkieletowej usługi z obsługą zarządzanych tożsamości dostępu do innych zasobów platformy Azure obsługujących uwierzytelnianie oparte na usłudze Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614797"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Udzielanie aplikacji sieci szkieletowej usługi zarządzanego dostępu do tożsamości zasobów platformy Azure (wersja zapoznawcza)

Zanim aplikacja może używać swojej tożsamości zarządzanej, aby uzyskać dostęp do innych zasobów, uprawnienia muszą być przyznane tej tożsamości na chronionym zasobie platformy Azure, do którego uzyskuje się dostęp. Udzielanie uprawnień jest zazwyczaj akcji zarządzania na "płaszczyzny kontroli" usługi platformy Azure, która jest właścicielem chronionego zasobu kierowane za pośrednictwem usługi Azure Resource Manager, który wymusi wszelkie odpowiednie sprawdzanie dostępu oparte na rolach.

Dokładna sekwencja kroków będzie następnie zależeć od typu zasobu platformy Azure, do którego uzyskuje się dostęp, a także od języka/klienta używanego do udzielania uprawnień. Dalsza część artykułu zakłada, że tożsamość przypisana przez użytkownika jest przypisana do aplikacji i zawiera kilka typowych przykładów dla Twojej wygody, ale nie jest to w żaden sposób wyczerpujące odniesienie do tego tematu; zapoznaj się z dokumentacją odpowiednich usług platformy Azure, aby uzyskać aktualne instrukcje dotyczące udzielania uprawnień.  

## <a name="granting-access-to-azure-storage"></a>Udzielanie dostępu do usługi Azure Storage
Można użyć tożsamości zarządzanej aplikacji sieci szkieletowej usługi (przypisane przez użytkownika w tym przypadku) do pobierania danych z obiektu blob magazynu platformy Azure. Udziel tożsamości wymaganych uprawnień w witrynie Azure portal, wykonując następujące kroki:

1. Przejdź do konta magazynu
2. Kliknij link Kontrola dostępu (IAM) w panelu po lewej stronie.
3. (opcjonalnie) Sprawdź istniejący dostęp: wybierz tożsamość zarządzaną przypisaną do systemu lub przez użytkownika w formancie "Znajdź"; wybierz odpowiednią tożsamość z listy wyników
4. Kliknij przycisk + Dodaj przypisanie roli u góry strony, aby dodać nowe przypisanie roli dla tożsamości aplikacji.
W obszarze Rola z listy rozwijanej wybierz pozycję Czytnik danych obiektów blob magazynu.
5. W następnej pozycji rozwijanej w `User assigned managed identity`obszarze Przypisywanie dostępu wybierz pozycję .
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej Subskrypcja, po czym ustaw opcję Grupa zasobów na wartość Wszystkie grupy zasobów.
7. W obszarze Wybierz wybierz pozycję UAI odpowiadającą aplikacji sieci szkieletowej usług, a następnie kliknij przycisk Zapisz.

Obsługa tożsamości zarządzanych sieci szkieletowej usługi przypisanych do systemu nie obejmuje integracji w witrynie Azure portal; jeśli aplikacja używa tożsamości przypisanej do systemu, należy najpierw znaleźć identyfikator klienta tożsamości aplikacji, a następnie powtórzyć powyższe `Azure AD user, group, or service principal` kroki, ale wybierając opcję w formancie Znajdź.

## <a name="granting-access-to-azure-key-vault"></a>Udzielanie dostępu do usługi Azure Key Vault
Podobnie z dostępem do magazynu, można wykorzystać tożsamość zarządzaną aplikacji sieci szkieletowej usług, aby uzyskać dostęp do magazynu kluczy platformy Azure. Kroki przyznawania dostępu w witrynie Azure portal są podobne do tych wymienionych powyżej i nie będą powtarzane w tym miejscu. Różnice można znaleźć na poniższej ilustracji.

![Zasady dostępu do usługi Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Poniższy przykład ilustruje udzielanie dostępu do magazynu za pośrednictwem wdrożenia szablonu; dodać fragmenty kodu poniżej jako inny wpis `resources` pod elementem szablonu. Przykład pokazuje przyznanie dostępu dla typów tożsamości przypisanych przez użytkownika i przypisanych do systemu, odpowiednio — wybierz odpowiedni.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
A w przypadku tożsamości zarządzanych przypisanych do systemu:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Aby uzyskać więcej informacji, zobacz [Vaults - Update Access Policy](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)