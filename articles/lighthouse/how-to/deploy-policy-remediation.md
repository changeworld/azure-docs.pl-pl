---
title: Wdrażanie zasad, które można skorygować
description: Dowiedz się, jak przywdzielić klienta do zarządzania zasobami delegowanymi platformy Azure, umożliwiając dostęp do jego zasobów i zarządzanie nimi za pośrednictwem własnej dzierżawy.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: b625e9e3c96866cfbc655a55b770c9ac07a626bd
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985171"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej

[Latarnia morska platformy Azure](../overview.md) umożliwia dostawcom usług tworzenie i edytowanie definicji zasad w ramach subskrypcji delegowanej. Jednak aby wdrożyć zasady, które używają [zadania korygowania](../../governance/policy/how-to/remediate-resources.md) (czyli zasad z [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) lub [zmodyfikować](../../governance/policy/concepts/effects.md#modify) efekt), należy utworzyć [tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md) w dzierżawie klienta. Ta tożsamość zarządzana może służyć przez usługę Azure Policy do wdrożenia szablonu w ramach zasad. Istnieją kroki wymagane, aby włączyć ten scenariusz, zarówno podczas dołączania klienta do zarządzania zasobami delegowanymi platformy Azure, jak i podczas wdrażania samej zasady.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Tworzenie użytkownika, który może przypisywać role do tożsamości zarządzanej w dzierżawie klienta

Po poleceniu klienta do zarządzania zasobami delegowanymi platformy Azure, należy użyć [szablonu usługi Azure Resource Manager](onboard-customer.md#create-an-azure-resource-manager-template) wraz z plikiem parametrów, który definiuje użytkowników, grup użytkowników i podmiotów usługi w dzierżawie zarządzającej, które będą mogły uzyskać dostęp do delegowanych zasobów w dzierżawie klienta. W pliku parametrów każdemu z tych użytkowników **(principalId)** jest przypisywana [wbudowana rola](../../role-based-access-control/built-in-roles.md) **(roleDefinitionId**), która definiuje poziom dostępu.

Aby zezwolić **identyfikatorowi głównemu** na utworzenie tożsamości zarządzanej w dzierżawie klienta, należy ustawić jego **funkcjęDekonfinitionId** na **Administratora dostępu użytkownika.** Chociaż ta rola nie jest ogólnie obsługiwane, może służyć w tym konkretnym scenariuszu, dzięki czemu użytkownicy z tym uprawnieniem przypisać jeden lub więcej określonych ról wbudowanych do tożsamości zarządzanych. Te role są **zdefiniowane we właściwisce deledRoleDefinitionIds.** W tym miejscu można uwzględnić dowolną wbudowaną rolę, z wyjątkiem administratora dostępu użytkownika lub właściciela.

Po klient jest dołączany, **principalId** utworzony w tej autoryzacji będzie można przypisać te wbudowane role do zarządzanych tożsamości w dzierżawie klienta. Nie będą jednak miały żadnych innych uprawnień normalnie skojarzonych z rolą Administrator dostępu użytkownika.

W poniższym przykładzie pokazano **principalId,** który będzie miał rolę Administratora dostępu użytkownika. Ten użytkownik będzie mógł przypisać dwie wbudowane role do zarządzanych tożsamości w dzierżawie klienta: współautor i współautor analizy dzienników.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Wdrażanie zasad, które można korygować

Po utworzeniu użytkownika z niezbędnymi uprawnieniami, jak opisano powyżej, ten użytkownik może wdrożyć zasady w dzierżawie klienta, które używają zadań korygowania.

Załóżmy na przykład, że chcesz włączyć diagnostykę zasobów usługi Azure Key Vault w dzierżawie klienta, jak pokazano w tym [przykładzie.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) Użytkownik w dzierżawie zarządzającej z odpowiednimi uprawnieniami (jak opisano powyżej) wdroży [szablon usługi Azure Resource Manager,](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) aby włączyć ten scenariusz.

Należy zauważyć, że tworzenie przypisania zasad do użycia z delegowaną subskrypcją musi być obecnie wykonywane za pośrednictwem interfejsów API, a nie w witrynie Azure portal. W ten sposób **apiVersion** musi być ustawiona na **2019-04-01-preview**, który zawiera nową **właściwośćlegedManagedIdentityResourceId.** Ta właściwość umożliwia dołączenie tożsamości zarządzanej, która znajduje się w dzierżawie klienta (w grupie subskrypcji lub zasobów, która została przyłączona do zarządzania zasobami delegowanymi platformy Azure).

W poniższym przykładzie pokazano przypisanie roli z **identyfikatorem delegowanymManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> [Podobny przykład](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) jest dostępny, aby zademonstrować sposób wdrażania zasad, które dodaje lub usuwa tag (przy użyciu efektu modyfikowania) do subskrypcji delegowanej.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zasadach platformy Azure](../../governance/policy/index.yml).
- Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).
