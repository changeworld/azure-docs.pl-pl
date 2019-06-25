---
title: Udzielanie dostępu do utworzenia subskrypcji Azure Enterprise | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udzielić użytkownikowi lub jednostki usługi, możliwość programowego tworzenia subskrypcji Azure Enterprise.
services: azure-resource-manager
author: jureid
manager: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: a7ed7dffd27b51c1314c4293820dc33be4d7e8e0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206640"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Udzielanie dostępu do utworzenia subskrypcji Azure Enterprise (wersja zapoznawcza)

Jako klient korzystający z platformy Azure na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), można zezwolić innego użytkownika lub usługę podmiotu zabezpieczeń można tworzyć subskrypcje rozliczane na koncie. W tym artykule dowiesz się, jak używać [kontroli dostępu opartej na rolach (RBAC)](../active-directory/role-based-access-control-configure.md) udostępnianie możliwość tworzenia subskrypcji i inspekcja operacje tworzenia subskrypcji. Musi mieć rolę właściciela konta, które chcesz udostępnić.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Udzielanie dostępu

Aby [tworzyć subskrypcje w ramach konta rejestracji](programmatically-create-subscription.md), użytkownicy muszą mieć [roli właściciela RBAC](../role-based-access-control/built-in-roles.md#owner) dla tego konta. Możesz nadawać użytkownika lub grupy użytkowników rolę właściciela RBAC przy użyciu konta rejestracji, wykonaj następujące czynności:

1. Pobierz identyfikator konta rejestracji, który chcesz udzielić dostępu do obiektu

    Aby udzielić innym roli właściciela RBAC przy użyciu konta rejestracji, albo musi być właścicielem konta lub RBAC konta.

    # <a name="resttabrest"></a>[REST](#tab/rest)

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

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Użyj [Get AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) polecenia cmdlet, aby wyświetlić listę wszystkich kont rejestracji, masz dostęp. Wybierz **wypróbuj** otworzyć [usługi Azure Cloud Shell](https://shell.azure.com/). Wklej kod, kliknij prawym przyciskiem myszy powłokę systemu windows i wybierz **Wklej**.

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

    # <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    Użyj [az rozliczania konta rejestracji listy](https://aka.ms/EASubCreationPublicPreviewCLI) polecenie, aby wyświetlić listę wszystkich kont rejestracji, masz dostęp do. Wybierz **wypróbuj** otworzyć [usługi Azure Cloud Shell](https://shell.azure.com/). Wklej kod, kliknij prawym przyciskiem myszy powłokę systemu windows i wybierz **Wklej**.

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

    ---

    Użyj `principalName` właściwość do identyfikacji konta, na którym chcesz przyznać dostęp właściciela RBAC do. Kopiuj `name` tego konta. Na przykład, jeśli chcesz przyznać dostęp właściciela RBAC do SignUpEngineering@contoso.com konta rejestracji może spowodować skopiowanie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Jest to identyfikator obiektu konta rejestracji. Wklej tę wartość, gdzieś tak, aby służy w następnym kroku jako `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Pobierz identyfikator obiektu użytkownika lub grupy, które chcesz przypisać rolę właściciela RBAC do

    1. W witrynie Azure portal Wyszukaj **usługi Azure Active Directory**.
    1. Jeśli chcesz udzielić użytkownikowi dostępu, kliknij polecenie **użytkowników** w menu po lewej stronie. Jeśli chcesz udzielić dostępu do grupy, kliknij przycisk **grup**.
    1. Wybierz użytkownika lub grupy, które chcesz przypisać rolę właściciela RBAC do.
    1. Jeśli wybrano użytkownika, znajdziesz identyfikator obiektu na stronie profilu. Jeśli zostało wybrane grupy, identyfikator obiektu będzie na stronie Przegląd. Kopiuj **ObjectID** , klikając ikonę z prawej strony pola tekstowego. Wklej ten gdzieś tak, aby służy w następnym kroku jako `userObjectId`.

1. Przyznaj użytkownikowi lub grupie roli właściciela RBAC dla konta rejestracji

    Przy użyciu wartości, które zostały zebrane w dwa pierwsze kroki, Przyznaj użytkownikowi lub grupie roli właściciela RBAC dla konta rejestracji.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

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

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    Uruchom następujące polecenie [New AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) polecenia, zastępując ```<enrollmentAccountObjectId>``` z `ObjectId` zebranych w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zastąp ```<userObjectId>``` zebrano Identyfikatora obiektu w drugim kroku.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli-2)

    Uruchom następujące polecenie [utworzenia przypisania roli az](../active-directory/role-based-access-control-manage-access-azure-cli.md) polecenia, zastępując ```<enrollmentAccountObjectId>``` z `name` kopiowane w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zastąp ```<userObjectId>``` zebrano Identyfikatora obiektu w drugim kroku.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Po użytkownik staje się właścicielem RBAC dla konta usługi rejestracji, także oni mogą [twórz subskrypcje programistycznie](programmatically-create-subscription.md) wczytywania. Subskrypcji utworzonych przez delegowany użytkownik nadal ma pierwotnego właściciela konta, jak administrator usługi, ale ma również delegowanego użytkownika jako właściciela RBAC domyślnie.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Inspekcja, która utworzyła subskrypcji przy użyciu dzienników aktywności

Aby śledzić subskrypcje utworzone za pomocą tego interfejsu API, należy użyć [interfejs API dziennika aktywności dzierżawcy](/rest/api/monitor/tenantactivitylogs). Obecnie nie jest możliwe użycie programu PowerShell, interfejsu wiersza polecenia lub witryny Azure portal do śledzenia tworzenia subskrypcji.

1. Jako administrator dzierżawy usługi Azure Active Directory [podnieś poziom dostępu](../active-directory/role-based-access-control-tenant-admin-access.md), a następnie przypisz rolę Czytelnika dla zakresu `/providers/microsoft.insights/eventtypes/management` do użytkownika wykonującego inspekcję.
1. Jako użytkownik inspekcji, należy wywołać [interfejs API dziennika aktywności dzierżawcy](/rest/api/monitor/tenantactivitylogs) wyświetlone działania tworzenia subskrypcji. Przykład:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Aby wygodnie wywołać ten interfejs API z poziomu wiersza polecenia, wypróbuj narzędzie [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Kolejne kroki

* Teraz, użytkownika lub jednostka usługi ma uprawnienia do tworzenia subskrypcji, można użyć tej tożsamości do [programowe tworzenie subskrypcji Azure Enterprise](programmatically-create-subscription.md).
* Na przykład na temat tworzenia subskrypcji przy użyciu platformy .NET, zobacz [przykładowego kodu w serwisie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager i jej interfejsów API, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](management-groups-overview.md)
* Aby wyświetlić kompleksowe najlepszych rozwiązań dla dużych organizacji na Zarządzanie subskrypcją, zobacz [Azure enterprise scaffold - narzucony nadzór subskrypcji](/azure/architecture/cloud-adoption-guide/subscription-governance)
