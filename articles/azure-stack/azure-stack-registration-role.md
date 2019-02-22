---
title: Jak utworzyć rolę rejestracji dla usługi Azure Stack
description: Jak utworzyć rolę niestandardową, aby uniknąć, za pomocą administratora globalnego dla rejestracji.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 4858e33023ff0f94bb9fb879b01846184df2491e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652148"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Utwórz rolę rejestracji dla usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

W przypadku scenariuszy, w których nie chcesz nadać uprawnienia właściciela w subskrypcji platformy Azure można utworzyć rolę niestandardową, aby przypisać uprawnienia do konta użytkownika, aby zarejestrować usługi Azure Stack.

> [!WARNING]
> Nie jest funkcją poziomu zabezpieczeń. Jej używać w scenariuszach, w którym ma ograniczenia, aby zapobiec przypadkowym zmianom do subskrypcji platformy Azure. Gdy użytkownik jest delegowane uprawnienia do tę rolę niestandardową, użytkownik ma uprawnienia do edytowania uprawnień i podniesienie uprawnień. Należy przypisywać tylko użytkowników, którym ufasz do roli niestandardowej.

Podczas rejestrowania w usłudze Azure Stack, konto rejestracji wymaga następujących uprawnień usługi Azure Active Directory i uprawnienia subskrypcji platformy Azure:

* **Uprawnienia rejestracji aplikacji w dzierżawie usługi Azure Active Directory:** Administratorzy mają uprawnienia rejestracji aplikacji. Uprawnienia dla użytkowników jest ustawienie globalne dla wszystkich użytkowników w dzierżawie. Aby wyświetlić lub zmienić ustawienia, zobacz [tworzenia aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

    *Użytkownika mogą rejestrować aplikacje* ustawienie musi być równa **tak** umożliwiające włączanie konta użytkownika można zarejestrować usługi Azure Stack. Jeśli wartością ustawienia rejestracji aplikacji jest równa **nie**, nie można używać konta użytkownika i należy użyć konta administratora globalnego do rejestrowania usługi Azure Stack.

* **Zestaw wystarczających uprawnień do subskrypcji platformy Azure:** Użytkownicy w grupie właścicieli ma wystarczających uprawnień. W przypadku innych kont można przypisać uprawnienia ustawione przez przypisanie roli niestandardowej, zgodnie z opisem w poniższych sekcjach.

## <a name="create-a-custom-role-using-powershell"></a>Utworzyć rolę niestandardową przy użyciu programu PowerShell

Aby utworzyć rolę niestandardową, konieczne jest posiadanie `Microsoft.Authorization/roleDefinitions/write` uprawnienia na wszystkich `AssignableScopes`, takich jak [właściciela](../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator). Użyj następującego szablonu JSON ułatwiają definiowanie roli niestandardowej. Ten szablon tworzy rolę niestandardową, która umożliwia wymagane odczytu i zapisu dla rejestracji w usłudze Azure Stack.

1. Utwórz plik JSON. Na przykład  `C:\CustomRoles\registrationrole.json`
2. Dodaj do pliku następujący kod JSON. Zamień wartość <SubscriptionID> na identyfikator swojej subskrypcji platformy Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. W programie PowerShell łączenie z platformą Azure, aby używać usługi Azure Resource Manager. Po wyświetleniu monitu uwierzytelniania przy użyciu konta z wystarczającymi uprawnieniami takich jak [właściciela](../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Aby dodać rolę do subskrypcji, użyj **New-AzureRmRoleDefinition** Określanie pliku szablonu JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Przypisz użytkownika do roli rejestracji

Po utworzeniu roli niestandardowej rejestracji przydzielić roli rejestracji w usłudze Azure Stack.

1. Zaloguj się przy użyciu konta przy użyciu wystarczających uprawnień w subskrypcji platformy Azure, takich jak delegować prawa - [właściciela](../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) .
2. W **subskrypcje**, wybierz opcję **kontrola dostępu (IAM) > Dodaj przypisanie roli**.
3. W **roli**, wybierz rolę niestandardową, utworzono *roli rejestracji w usłudze Azure Stack*.
4. Wybierz użytkowników, dla których chcesz przypisać do roli.
5. Wybierz **Zapisz** można przypisać wybranych użytkowników do roli.

    ![Wybierz użytkowników, aby przypisać do roli](media/azure-stack-registration-role/assign-role.png)

Aby uzyskać więcej informacji na temat korzystania z ról niestandardowych, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Kolejne kroki

[Rejestrowania usługi Azure Stack na platformie Azure](azure-stack-registration.md)
