---
title: Udzielanie dostępu do utworzenia subskrypcji Azure Enterprise | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udzielić użytkownikowi lub jednostki usługi, możliwość programowego tworzenia subskrypcji Azure Enterprise.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: jureid
ms.openlocfilehash: 742658e36da956c46bd932b59903e68786c65b93
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794563"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udzielanie dostępu do utworzenia subskrypcji Azure Enterprise (wersja zapoznawcza)

Jako klient korzystający z platformy Azure na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), można zezwolić innego użytkownika lub usługę podmiotu zabezpieczeń można tworzyć subskrypcje rozliczane na koncie. W tym artykule dowiesz się, jak używać [kontroli dostępu opartej na rolach (RBAC)](../active-directory/role-based-access-control-configure.md) udostępnianie możliwość tworzenia subskrypcji i inspekcja operacje tworzenia subskrypcji. Musi mieć rolę właściciela konta, które chcesz udostępnić.

Aby utworzyć subskrypcję, zobacz [programowe tworzenie subskrypcji Azure Enterprise (wersja zapoznawcza)](programmatically-create-subscription.md).

## <a name="grant-subscription-creation-access-to-a-user-or-group"></a>Udzielić dostępu do tworzenia subskrypcji do użytkownika lub grupy

Aby tworzyć subskrypcje w ramach konta rejestracji, użytkownicy muszą mieć [roli właściciela RBAC](../role-based-access-control/built-in-roles.md#owner) dla tego konta. Możesz nadawać użytkownika lub grupy użytkowników rolę właściciela RBAC przy użyciu konta rejestracji, wykonaj następujące czynności:

### <a name="1-get-the-object-id-of-the-enrollment-account-you-want-to-grant-access-to"></a>1. Pobierz identyfikator konta rejestracji, który chcesz udzielić dostępu do obiektu

Aby udzielić innym roli właściciela RBAC przy użyciu konta rejestracji, albo musi być właścicielem konta lub RBAC konta.

### <a name="resttabrest"></a>[REST](#tab/rest)

Żądanie wyświetlenia listy wszystkich kont rejestracji, do których masz dostęp do:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Usługa Azure współpracuje z listą wszystkich kont rejestracji, do których masz dostęp do:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Użyj `principalName` właściwość do identyfikacji konta, na którym chcesz przyznać dostęp właściciela RBAC do. Kopiuj `name` tego konta. Na przykład, jeśli chcesz przyznać dostęp właściciela RBAC do SignUpEngineering@contoso.com konta rejestracji może spowodować skopiowanie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Jest to identyfikator obiektu konta rejestracji. Wklej tę wartość, gdzieś tak, aby służy w następnym kroku jako `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Otwórz [usługi Azure Cloud Shell](https://shell.azure.com/) i wybierz polecenie programu PowerShell.

Użyj [Get AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) polecenia cmdlet, aby wyświetlić listę wszystkich kont rejestracji, masz dostęp.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure odpowiada za pomocą listę kont rejestracji, do których masz dostęp do:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

Użyj `principalName` właściwości, aby zidentyfikować konto, którego chcesz przyznać dostęp właściciela RBAC do. Kopiuj `ObjectId` tego konta. Na przykład, jeśli chcesz przyznać dostęp właściciela RBAC do SignUpEngineering@contoso.com konta rejestracji może spowodować skopiowanie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Wklej ten identyfikator obiektu gdzieś tak, aby służy w następnym kroku jako `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj [az rozliczania konta rejestracji listy](https://aka.ms/EASubCreationPublicPreviewCLI) polecenie, aby wyświetlić listę wszystkich kont rejestracji, masz dostęp do.

```azurecli-interactive 
az billing enrollment-account list
```

Azure odpowiada za pomocą listę kont rejestracji, do których masz dostęp do:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]

```

Użyj `principalName` właściwość do identyfikacji konta, na którym chcesz przyznać dostęp właściciela RBAC do. Kopiuj `name` tego konta. Na przykład, jeśli chcesz przyznać dostęp właściciela RBAC do SignUpEngineering@contoso.com konta rejestracji może spowodować skopiowanie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Jest to identyfikator obiektu konta rejestracji. Wklej tę wartość, gdzieś tak, aby służy w następnym kroku jako `enrollmentAccountObjectId`.

<a id="userObjectId"></a>

### <a name="2-get-object-id-of-the-user-or-group-you-want-to-give-the-rbac-owner-role-to"></a>2. Pobierz identyfikator obiektu użytkownika lub grupy, które chcesz przypisać rolę właściciela RBAC do

1. W witrynie Azure portal Wyszukaj **usługi Azure Active Directory**.
1. Jeśli chcesz udzielić użytkownikowi dostępu, kliknij polecenie **użytkowników** w menu po lewej stronie. Jeśli chcesz udzielić dostępu do grupy, kliknij przycisk **grup**.
1. Wybierz użytkownika lub grupy, które chcesz przypisać rolę właściciela RBAC do.
1. Jeśli wybrano użytkownika, znajdziesz identyfikator obiektu na stronie profilu. Jeśli zostało wybrane grupy, identyfikator obiektu będzie na stronie Przegląd. Kopiuj **ObjectID** , klikając ikonę z prawej strony pola tekstowego. Wklej ten gdzieś tak, aby służy w następnym kroku jako `userObjectId`.

### <a name="3-grant-the-user-or-group-the-rbac-owner-role-on-the-enrollment-account"></a>3. Przyznaj użytkownikowi lub grupie roli właściciela RBAC dla konta rejestracji

Przy użyciu wartości, które zostały zebrane w dwa pierwsze kroki, Przyznaj użytkownikowi lub grupie roli właściciela RBAC dla konta rejestracji.

### <a name="resttabrest-2"></a>[REST](#tab/rest-2)

Uruchom następujące polecenie, zastępując ```<enrollmentAccountObjectId>``` z `name` kopiowane w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zastąp ```<userObjectId>``` o identyfikatorze obiektu został skopiowany w kroku drugim.

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

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

### <a name="powershelltabazure-powershell-2"></a>[Program PowerShell](#tab/azure-powershell-2)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Uruchom następujące polecenie [New AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) polecenia, zastępując ```<enrollmentAccountObjectId>``` z `ObjectId` zebranych w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zastąp ```<userObjectId>``` zebrano Identyfikatora obiektu w drugim kroku.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

### <a name="azure-clitabazure-cli-2"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli-2)

Uruchom następujące polecenie [utworzenia przypisania roli az](../active-directory/role-based-access-control-manage-access-azure-cli.md) polecenia, zastępując ```<enrollmentAccountObjectId>``` z `name` kopiowane w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zastąp ```<userObjectId>``` zebrano Identyfikatora obiektu w drugim kroku.

```azurecli-interactive
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

----

Po użytkownik staje się właścicielem RBAC dla konta usługi rejestracji, także oni mogą [twórz subskrypcje programistycznie](programmatically-create-subscription.md) wczytywania. Subskrypcji utworzonych przez delegowany użytkownik nadal ma pierwotnego właściciela konta, jak administrator usługi, ale ma również delegowanego użytkownika jako właściciela RBAC domyślnie.

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Inspekcja, która utworzyła subskrypcji przy użyciu dzienników aktywności

Aby śledzić subskrypcje utworzone za pomocą tego interfejsu API, należy użyć [interfejs API dziennika aktywności dzierżawcy](/rest/api/monitor/tenantactivitylogs). Obecnie nie jest możliwe użycie programu PowerShell, interfejsu wiersza polecenia lub witryny Azure portal do śledzenia tworzenia subskrypcji.

1. Jako administrator dzierżawy usługi Azure Active Directory [podnieś poziom dostępu](../active-directory/role-based-access-control-tenant-admin-access.md), a następnie przypisz rolę Czytelnika dla zakresu `/providers/microsoft.insights/eventtypes/management` do użytkownika wykonującego inspekcję.
1. Jako użytkownik inspekcji, należy wywołać [interfejs API dziennika aktywności dzierżawcy](/rest/api/monitor/tenantactivitylogs) wyświetlone działania tworzenia subskrypcji. Przykład:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> Aby wygodnie wywołać ten interfejs API z poziomu wiersza polecenia, wypróbuj narzędzie [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Kolejne kroki

* Teraz, użytkownika lub jednostka usługi ma uprawnienia do tworzenia subskrypcji, można użyć tej tożsamości do [programowe tworzenie subskrypcji Azure Enterprise](programmatically-create-subscription.md).
* Na przykład na temat tworzenia subskrypcji przy użyciu platformy .NET, zobacz [przykładowego kodu w serwisie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager i jej interfejsów API, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](management-groups-overview.md)
* Aby wyświetlić kompleksowe najlepszych rozwiązań dla dużych organizacji na Zarządzanie subskrypcją, zobacz [Azure enterprise scaffold - narzucony nadzór subskrypcji](/azure/architecture/cloud-adoption-guide/subscription-governance)
