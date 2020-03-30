---
title: Udzielanie dostępu do tworzenia subskrypcji platformy Azure Enterprise
description: Dowiedz się, jak dać użytkownikowi lub podmiotowi usługi możliwość programowego tworzenia subskrypcji platformy Azure Enterprise.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478879"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udziel dostępu do tworzenia subskrypcji platformy Azure Enterprise (wersja zapoznawcza)

Jako klient platformy Azure w [ramach umowy Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)możesz udzielić innemu użytkownikowi lub głównemu zleceniodawcy usługi uprawnień do tworzenia subskrypcji rozliczanych na koncie. W tym artykule dowiesz się, jak używać [kontroli dostępu opartej na rolach (RBAC)](../../active-directory/role-based-access-control-configure.md) do udostępniania możliwości tworzenia subskrypcji i sposobu inspekcji tworzenia subskrypcji. Musisz mieć rolę Właściciel na koncie, które chcesz udostępnić.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Udzielanie dostępu

Aby [utworzyć subskrypcje w ramach konta rejestracji,](programmatically-create-subscription.md)użytkownicy muszą mieć [rolę właściciela RBAC](../../role-based-access-control/built-in-roles.md#owner) na tym koncie. Możesz przyznać użytkownikowi lub grupie użytkowników rolę właściciela RBAC na koncie rejestracji, wykonując następujące kroki:

1. Pobierz identyfikator obiektu konta rejestracji, do którego chcesz udzielić dostępu

    Aby przyznać innym rolę właściciela RBAC na koncie rejestracji, musisz być właścicielem konta lub właścicielem RBAC konta.

    # <a name="rest"></a>[Reszta](#tab/rest)

    Poproś o wyświetlenie listy wszystkich kont rejestracji, do których masz dostęp:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Platforma Azure odpowiada za pomocą listy wszystkich kont rejestracji, do których masz dostęp:

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

    Użyj `principalName` właściwości, aby zidentyfikować konto, do którego chcesz udzielić dostępu właścicielowi RBAC. Skopiuj `name` to konto. Na przykład, jeśli chcesz udzielić właścicielowi SignUpEngineering@contoso.com RBAC dostępu do ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```konta rejestracji, należy skopiować program . Jest to identyfikator obiektu konta rejestracji. Wklej tę wartość gdzieś, aby można było `enrollmentAccountObjectId`jej użyć w następnym kroku jako .

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

    Polecenie cmdlet [Get-AzEnrollmentAccount służy](/powershell/module/az.billing/get-azenrollmentaccount) do listy wszystkich kont rejestracji, do których masz dostęp. Wybierz pozycję **Spróbuj,** aby otworzyć [usługę Azure Cloud Shell](https://shell.azure.com/). Aby wkleić kod, kliknij prawym przyciskiem myszy okna powłoki, a następnie wybierz pozycję **Wklej**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Platforma Azure odpowiada za pomocą listy kont rejestracji, do których masz dostęp:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Użyj `principalName` właściwości, aby zidentyfikować konto, do którego chcesz udzielić dostępu właścicielowi RBAC. Skopiuj `ObjectId` to konto. Na przykład, jeśli chcesz udzielić właścicielowi SignUpEngineering@contoso.com RBAC dostępu do ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```konta rejestracji, należy skopiować program . Wklej ten identyfikator obiektu gdzieś, aby można było go `enrollmentAccountObjectId`użyć w następnym kroku jako .

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    Użyj polecenia [listy kont rejestracji rozliczeń az,](https://aka.ms/EASubCreationPublicPreviewCLI) aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp. Wybierz pozycję **Spróbuj,** aby otworzyć [usługę Azure Cloud Shell](https://shell.azure.com/). Aby wkleić kod, kliknij prawym przyciskiem myszy okna powłoki, a następnie wybierz pozycję **Wklej**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Platforma Azure odpowiada za pomocą listy kont rejestracji, do których masz dostęp:

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

    ---

    Użyj `principalName` właściwości, aby zidentyfikować konto, do którego chcesz udzielić dostępu właścicielowi RBAC. Skopiuj `name` to konto. Na przykład, jeśli chcesz udzielić właścicielowi SignUpEngineering@contoso.com RBAC dostępu do ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```konta rejestracji, należy skopiować program . Jest to identyfikator obiektu konta rejestracji. Wklej tę wartość gdzieś, aby można było `enrollmentAccountObjectId`jej użyć w następnym kroku jako .

1. <a id="userObjectId"></a>Pobierz identyfikator obiektu użytkownika lub grupy, której chcesz nadać roli właściciela RBAC

    1. W witrynie Azure portal wyszukaj w **usłudze Azure Active Directory**.
    1. Jeśli chcesz udzielić użytkownikowi dostępu, kliknij **na Użytkownicy** w menu po lewej stronie. Jeśli chcesz udzielić dostępu do grupy, kliknij przycisk **Grupy**.
    1. Wybierz użytkownika lub grupę, której chcesz nadać roli właściciela RBAC.
    1. Jeśli wybrano użytkownika, identyfikator obiektu zostanie znaleziony na stronie Profil. Jeśli wybrano grupę, identyfikator obiektu będzie na stronie Przegląd. Skopiuj **identyfikator obiektu,** klikając ikonę po prawej stronie pola tekstowego. Wklej to gdzieś, aby można było go `userObjectId`użyć w następnym kroku jako .

1. Nadaj użytkownikowi lub grupie rolę właściciela RBAC na koncie rejestracji

    Korzystając z wartości zebranych w pierwszych dwóch krokach, przyznaj użytkownikowi lub grupie rolę właściciela RBAC na koncie rejestracji.

    # <a name="rest"></a>[Reszta](#tab/rest-2)

    Uruchom następujące polecenie, ```<enrollmentAccountObjectId>``` zastępując `name` skopiowanym w pierwszym```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```kroku ( ). Zamień ```<userObjectId>``` identyfikator obiektu skopiowany z drugiego kroku.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Gdy rola Właściciel zostanie pomyślnie przypisana w zakresie konta rejestracji, platforma Azure odpowiada informacjami o przypisaniu roli:

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

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell-2)

    Uruchom następujące polecenie [New-AzRoleAssignment,](../../active-directory/role-based-access-control-manage-access-powershell.md) ```<enrollmentAccountObjectId>``` `ObjectId` zastępując zebrane w```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```pierwszym kroku ( ). Zamień ```<userObjectId>``` identyfikator obiektu zebrany w drugim kroku.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli-2)

    Uruchom następujące polecenie [tworzenia przypisania roli az,](../../active-directory/role-based-access-control-manage-access-azure-cli.md) zastępując ```<enrollmentAccountObjectId>``` polecenie `name` skopiowanym w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zamień ```<userObjectId>``` identyfikator obiektu zebrany w drugim kroku.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Gdy użytkownik staje się właścicielem RBAC dla konta rejestracji, mogą [programowo tworzyć subskrypcje](programmatically-create-subscription.md) pod nim. Subskrypcja utworzona przez delegowanego użytkownika nadal ma oryginalnego właściciela konta jako administratora usługi, ale domyślnie ma również delegowanego użytkownika jako właściciela RBAC.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Inspekcja osób, które utworzyły subskrypcje przy użyciu dzienników aktywności

Aby śledzić subskrypcje utworzone za pośrednictwem tego interfejsu API, użyj [interfejsu API dziennika aktywności dzierżawy](/rest/api/monitor/tenantactivitylogs). Obecnie nie jest możliwe użycie programu PowerShell, interfejsu wiersza polecenia lub witryny Azure portal do śledzenia tworzenia subskrypcji.

1. Jako administrator dzierżawy usługi Azure Active Directory [podnieś poziom dostępu](../../active-directory/role-based-access-control-tenant-admin-access.md), a następnie przypisz rolę Czytelnika dla zakresu `/providers/microsoft.insights/eventtypes/management` do użytkownika wykonującego inspekcję.
1. Jako użytkownik inspekcji, wywołać [interfejs API dziennika aktywności dzierżawy,](/rest/api/monitor/tenantactivitylogs) aby wyświetlić działania tworzenia subskrypcji. Przykład:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Aby wygodnie wywołać ten interfejs API z poziomu wiersza polecenia, wypróbuj narzędzie [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Następne kroki

* Teraz, gdy użytkownik lub podmiot usługi ma uprawnienia do tworzenia subskrypcji, można użyć tej tożsamości do [programowego tworzenia subskrypcji platformy Azure Enterprise.](programmatically-create-subscription.md)
* Na przykład podczas tworzenia subskrypcji przy użyciu platformy .NET można znaleźć [przykładowy kod w usłudze GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Aby dowiedzieć się więcej o usłudze Azure Resource Manager i jego interfejsach API, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
* Aby dowiedzieć się więcej o zarządzaniu dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [Organizowanie zasobów za pomocą grup zarządzania platformy Azure](../../governance/management-groups/overview.md)
* Aby zapoznać się z kompleksowymi wskazówkami dotyczącymi najlepszych rozwiązań dla dużych organizacji w zakresie nadzoru nad subskrypcjami, zobacz [szkielet przedsiębiorstwa platformy Azure — nakazowe zarządzanie subskrypcjami](/azure/architecture/cloud-adoption-guide/subscription-governance)
