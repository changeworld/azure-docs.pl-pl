---
title: Udzielanie dostępu do utworzenia subskrypcji Azure Enterprise | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udzielić użytkownikowi lub jednostki usługi, możliwość programowego tworzenia subskrypcji Azure Enterprise.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 3577edff19788ed9f0925876e3de737eb749b90e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490927"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udzielanie dostępu do utworzenia subskrypcji Azure Enterprise (wersja zapoznawcza)

Jako klient korzystający z platformy Azure na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), można zezwolić innego użytkownika lub usługę podmiotu zabezpieczeń można tworzyć subskrypcje rozliczane na koncie. W tym artykule dowiesz się, jak używać [kontroli dostępu opartej na rolach (RBAC)](../active-directory/role-based-access-control-configure.md) udostępnianie możliwość tworzenia subskrypcji i inspekcja operacje tworzenia subskrypcji. Musi mieć rolę właściciela konta, które chcesz udostępnić.

Aby utworzyć subskrypcję, zobacz [programowe tworzenie subskrypcji Azure Enterprise (wersja zapoznawcza)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegowanie dostępu do konta rejestracji przy użyciu RBAC

Aby zapewnić możliwość tworzenia subskrypcji do określonego konta innego użytkownika lub nazwa główna usługi [ciągowych roli właściciela RBAC w zakresie konta rejestracji](../active-directory/role-based-access-control-manage-access-rest.md). Poniższy przykład daje użytkownikowi w dzierżawie o `principalId` z `<userObjectId>` (dla SignUpEngineering@contoso.com) Rola właściciela konta rejestracji. Aby znaleźć rejestracji identyfikator konta i identyfikator podmiotu zabezpieczeń, zobacz [programowe tworzenie subskrypcji Azure Enterprise (wersja zapoznawcza)](programmatically-create-subscription.md).

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

Pomyślnie przypisano rolę właściciela w zakresie konta rejestracji, Azure odpowie informacje przypisania roli:

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

Użyj [New AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) oferowanie innemu użytkownikowi dostęp właściciela konta rejestracji.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj [utworzenia przypisania roli az](../active-directory/role-based-access-control-manage-access-azure-cli.md) oferowanie innemu użytkownikowi dostęp właściciela konta rejestracji.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Po użytkownik staje się właścicielem RBAC dla konta usługi rejestracji, można programowo utworzyć subskrypcje w ramach go. Subskrypcji utworzonych przez delegowany użytkownik nadal ma pierwotnego właściciela konta, jak administrator usługi, ale ma również delegowanego użytkownika jako właściciela domyślnie. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Inspekcja, która utworzyła subskrypcji przy użyciu dzienników aktywności

Aby śledzić subskrypcje utworzone za pomocą tego interfejsu API, należy użyć [interfejs API dziennika aktywności dzierżawcy](/rest/api/monitor/tenantactivitylogs). Obecnie nie jest możliwe użycie programu PowerShell, interfejsu wiersza polecenia lub witryny Azure portal do śledzenia tworzenia subskrypcji.

1. Jako administrator dzierżawy usługi Azure Active Directory [podnieś poziom dostępu](../active-directory/role-based-access-control-tenant-admin-access.md), a następnie przypisz rolę Czytelnika dla zakresu `/providers/microsoft.insights/eventtypes/management` do użytkownika wykonującego inspekcję.
1. Jako użytkownik inspekcji, należy wywołać [interfejs API dziennika aktywności dzierżawcy](/rest/api/monitor/tenantactivitylogs) wyświetlone działania tworzenia subskrypcji. Przykład:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Aby wygodnie wywołać ten interfejs API z poziomu wiersza polecenia, wypróbuj narzędzie [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Kolejne kroki

* Teraz, użytkownika lub jednostka usługi ma uprawnienia do tworzenia subskrypcji, można użyć tej tożsamości do [programowe tworzenie subskrypcji Azure Enterprise](programmatically-create-subscription.md).
* Na przykład na temat tworzenia subskrypcji przy użyciu platformy .NET, zobacz [przykładowego kodu w serwisie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager i jej interfejsów API, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](management-groups-overview.md)
* Aby wyświetlić kompleksowe najlepszych rozwiązań dla dużych organizacji na Zarządzanie subskrypcją, zobacz [Azure enterprise scaffold - narzucony nadzór subskrypcji](/azure/architecture/cloud-adoption-guide/subscription-governance)
