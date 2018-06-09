---
title: Udziel dostępu do utworzenia subskrypcji Azure Enterprise | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zapewnić użytkownika lub nazwy głównej usługi programowe tworzenie subskrypcji Azure Enterprise.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: jlian
ms.openlocfilehash: 4c5d505f431ef684b73adc04629464883d336a5b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238273"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Przyznaj uprawnienia do tworzenia subskrypcji Azure Enterprise (wersja zapoznawcza)

Azure klientów na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), można zezwolić innego użytkownika lub usługę podmiotu zabezpieczeń można utworzyć subskrypcji rozliczane do Twojego konta. W tym artykule dowiesz się jak używać [kontroli dostępu opartej na rolach (RBAC)](../active-directory/role-based-access-control-configure.md) udostępnianie możliwość tworzenia subskrypcji i inspekcja podczas tworzenia subskrypcji.

Aby utworzyć subskrypcję, zobacz [programowe tworzenie subskrypcji Azure Enterprise (wersja zapoznawcza)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegowanie dostępu do konta rejestracji przy użyciu funkcji RBAC

Aby zapewnić możliwość tworzenia subskrypcji do określonego konta innego użytkownika lub nazwy głównej usługi [nadanie im rolę właściciela RBAC w zakresie konta rejestracji](../active-directory/role-based-access-control-manage-access-rest.md). Poniższy przykład umożliwia w dzierżawie z `principalId` z `<userObjectId>` (dla SignUpEngineering@contoso.com) rolę właściciela konta rejestracji. Aby znaleźć konto rejestracji identyfikator i identyfikator podmiotu zabezpieczeń, zobacz [programowe tworzenie subskrypcji Azure Enterprise (wersja zapoznawcza)](programmatically-create-subscription.md).

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
Rola właściciela został pomyślnie przypisany w zakresie rejestracji konta, Azure odpowie informacji przypisania roli:

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj [AzureRmRoleAssignment nowy](../active-directory/role-based-access-control-manage-access-powershell.md) umożliwiają innego użytkownika właściciela dostęp do Twojego konta rejestracji.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj [utworzenia przypisania roli az](../active-directory/role-based-access-control-manage-access-azure-cli.md) umożliwiają innego użytkownika właściciela dostęp do Twojego konta rejestracji.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Gdy użytkownik staje się właścicielem RBAC dla Twojego konta rejestracji, można programowo utworzyć subskrypcje w nim. Utworzone przez użytkownika delegowanego subskrypcji nadal ma pierwotnego właściciela konta administratora usługi, ale również ma delegowane użytkownika jako właściciela domyślnie. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Inspekcji, który utworzył subskrypcji przy użyciu dzienników działania

Aby śledzić subskrypcjami za pośrednictwem tego interfejsu API, należy użyć [interfejs API dzierżawcy działania dziennika](/rest/api/monitor/tenantactivitylogs). Obecnie nie jest możliwe użycie programu PowerShell, interfejsu wiersza polecenia lub portalu Azure do śledzenia tworzenia subskrypcji.

1. Jako Administrator dzierżawy dzierżawy usługi Azure AD [podniesienie uprawnień dostępu](../active-directory/role-based-access-control-tenant-admin-access.md) następnie przypisać rolę czytelnika użytkownikowi inspekcji w zakresie `/providers/microsoft.insights/eventtypes/management`.
1. Jako użytkownik inspekcji, wywołaj [interfejs API dzierżawcy działania dziennika](/rest/api/monitor/tenantactivitylogs) wyświetlać działania tworzenia subskrypcji. Przykład:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Wygodny sposób wywołania tego interfejsu API z poziomu wiersza polecenia, spróbuj [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Kolejne kroki

* Teraz, użytkownika lub nazwy głównej usługi ma uprawnienia do tworzenia subskrypcji, można użyć tej tożsamości do [programowe tworzenie subskrypcji Azure Enterprise](programmatically-create-subscription.md).
* Na przykład dotyczące tworzenia subskrypcji przy użyciu platformy .NET, zobacz [przykładowy kod w serwisie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager i jej interfejsów API, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [organizowania zasobów z grupami zarządzania Azure](management-groups-overview.md)
* Aby wyświetlić kompleksowe najlepszych rozwiązań dla dużych organizacji na Zarządzanie subskrypcją, zobacz [szkieletu Azure enterprise — ładu przetestowanego subskrypcji](/azure/architecture/cloud-adoption-guide/subscription-governance)
