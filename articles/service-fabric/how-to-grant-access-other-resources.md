---
title: Service Fabric platformy Azure — Udziel Service Fabric aplikacji dostępu do innych zasobów platformy Azure | Microsoft Docs
description: W tym artykule wyjaśniono, jak udzielić zarządzanej tożsamości Service Fabric dostępu do innych zasobów platformy Azure obsługujących uwierzytelnianie oparte na Azure Active Directory.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: 1facd85563a54de2b703bbc8c192976db92c30f7
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624903"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Udzielanie zarządzanej tożsamości aplikacji Service Fabric do zasobów platformy Azure (wersja zapoznawcza)

Aby aplikacja mogła korzystać z tożsamości zarządzanej w celu uzyskania dostępu do innych zasobów, należy przyznać uprawnienia do tej tożsamości w chronionym dostępnym zasobach platformy Azure. Przyznawanie uprawnień jest zazwyczaj akcją zarządzania w ramach "płaszczyzny kontroli" usługi platformy Azure będącej właścicielem chronionego zasobu kierowanego za pośrednictwem Azure Resource Manager, który będzie wymuszać wszelkie stosowne sprawdzanie dostępu oparte na rolach.

Dokładna sekwencja kroków będzie następnie zależała od typu zasobów platformy Azure, do którego uzyskiwany jest dostęp, a także od języka/klienta używanego do udzielania uprawnień. W pozostałej części artykułu przyjęto założenie, że tożsamość przypisana do aplikacji i zawiera kilka typowych przykładów dla wygody użytkownika, ale nie jest to wyczerpujące odwołanie do tego tematu. Zapoznaj się z dokumentacją odpowiednich usług platformy Azure, aby uzyskać aktualne instrukcje dotyczące przyznawania uprawnień.  

## <a name="granting-access-to-azure-storage"></a>Udzielanie dostępu do usługi Azure Storage
Możesz użyć zarządzanej tożsamości aplikacji Service Fabric (w tym przypadku przypisanej do użytkownika), aby pobrać dane z obiektu BLOB usługi Azure Storage. Przyznaj tożsamości wymagane uprawnienia w Azure Portal wykonaj następujące czynności:

1. Przejdź do konta magazynu
2. Kliknij link kontrola dostępu (IAM) w lewym panelu.
3. obowiązkowe Sprawdź istniejący dostęp: Wybierz zarządzaną tożsamość systemową lub przypisaną przez użytkownika w kontrolce "Znajdź"; Wybierz odpowiednią tożsamość z listy wyników wynikowych
4. Kliknij pozycję + Dodaj przypisanie roli w górnej części strony, aby dodać nowe przypisanie roli dla tożsamości aplikacji.
W obszarze Rola, z listy rozwijanej wybierz pozycję czytnik danych magazynu obiektów BLOB.
5. Na następnej liście rozwijanej w obszarze Przypisz dostęp do wybierz `User assigned managed identity`opcję.
6. Następnie upewnij się, że na liście rozwijanej subskrypcja znajduje się odpowiednia subskrypcja, a następnie ustaw grupę zasobów na wszystkie grupy zasobów.
7. W obszarze Wybierz Wybierz UAI odpowiadający aplikacji Service Fabric a następnie kliknij przycisk Zapisz.

Obsługa zarządzanych tożsamości przypisanych przez system Service Fabric nie obejmuje integracji w Azure Portal; Jeśli aplikacja używa tożsamości przypisanej do systemu, należy najpierw znaleźć identyfikator klienta tożsamości aplikacji, a następnie powtórz powyższe kroki, ale wybierając `Azure AD user, group, or service principal` opcję w kontrolce Znajdź.

## <a name="granting-access-to-azure-key-vault"></a>Udzielanie dostępu Azure Key Vault
Podobnie jak w przypadku uzyskiwania dostępu do magazynu, można wykorzystać zarządzaną tożsamość aplikacji Service Fabric, aby uzyskać dostęp do magazynu kluczy platformy Azure. Procedury udzielania dostępu w Azure Portal są podobne do wymienionych powyżej i nie będą powtarzane w tym miejscu. Różnice można znaleźć na poniższej ilustracji.

![Zasady dostępu Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Poniższy przykład ilustruje udzielanie dostępu do magazynu za pośrednictwem wdrożenia szablonu; Dodaj poniższy fragment kodu jako inny wpis pod `resources` elementem szablonu.

```json
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

Aby uzyskać więcej informacji, zobacz temat [magazyny — aktualizacje zasad dostępu](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)

* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)

## <a name="related-articles"></a>Pokrewne artykuły:

* Przejrzyj [obsługę tożsamości zarządzanych](./concepts-managed-identity.md) w usłudze Azure Service Fabric

* [Wdróż nowy](./configure-new-azure-service-fabric-enable-managed-identity.md) Klaster Service Fabric platformy Azure z obsługą tożsamości zarządzanej 

* [Włącz zarządzaną tożsamość](./configure-existing-cluster-enable-managed-identity-token-service.md) w istniejącym klastrze Service Fabric platformy Azure

* Korzystanie [z zarządzanej tożsamości aplikacji Service Fabric z kodu źródłowego](./how-to-managed-identity-service-fabric-app-code.md)

* Zapoznaj się z listą [usług platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md)
