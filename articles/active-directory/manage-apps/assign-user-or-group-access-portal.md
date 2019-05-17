---
title: Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak wybrać aplikację przedsiębiorstwa, aby przypisać użytkownika lub grupy do niej w usłudze Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 702eb5008c60764cd84e2787a8e2c2818d3f9268
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780933"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory
Aby przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa, musi mieć odpowiednie uprawnienia do zarządzania aplikacji przedsiębiorstwa, a musi być administratorem globalnym katalogu.

> [!NOTE]
> Wymagania dotyczące funkcji omówionych w tym artykule z licencjonowaniem znajdują [cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> For Applications firmy Microsoft (takich jak aplikacje usługi Office 365) należy użyć programu PowerShell do przypisywania użytkowników do aplikacji przedsiębiorstwa.


## <a name="assign-a-user-to-an-app---portal"></a>Przypisz użytkownika do aplikacji — portal
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz **wszystkich usług**wprowadź usługi Azure Active Directory w polu tekstowym, a następnie wybierz pozycję **Enter**.
1. Wybierz **aplikacje dla przedsiębiorstw**.
1. Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** okienku można wyświetlić listę aplikacje, którymi można zarządzać. Wybierz aplikację.
1. Na ***appname*** okienku (czyli nazwę wybranej aplikacji w tytule), wybierz **użytkownikami i grupami**.
1. Na ***appname*** **— grupy użytkowników i** okienku wybierz **Dodaj użytkownika**.
1. Na **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Przypisywanie użytkownika lub grupy do aplikacji](./media/assign-user-or-group-access-portal/assign-users.png)
1. Na **użytkowników i grup** okienku, wybierz z listy jeden lub więcej użytkowników lub grup, a następnie wybierz **wybierz** znajdujący się u dołu okienka.
1. Na **Dodaj przydziału** okienku wybierz **roli**. Następnie na **wybierz rolę** okienku, wybierz rolę do zastosowania do wybranych użytkowników lub grup, następnie wybierz pozycję **OK** w dolnej części okienka.
1. Na **Dodaj przydziału** okienku wybierz **przypisać** znajdujący się u dołu okienka. Przypisani użytkownicy lub grupy mają uprawnienia zdefiniowane przez wybraną rolę dla tej aplikacji przedsiębiorstwa.

## <a name="allow-all-users-to-access-an-app---portal"></a>Zezwala wszystkim użytkownikom na dostęp do aplikacji — portal
Aby umożliwić wszystkim użytkownikom dostępu do aplikacji:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz **wszystkich usług**wprowadź usługi Azure Active Directory w polu tekstowym, a następnie wybierz pozycję **Enter**.
1. Wybierz **aplikacje dla przedsiębiorstw**.
1. Na **aplikacje dla przedsiębiorstw** okienku wybierz **wszystkie aplikacje**. Ta lista zawiera aplikacje, którymi można zarządzać.
1. Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** okienku, wybierz aplikację.
1. Na ***appname*** okienku wybierz **właściwości**.
1. Na  ***appname* — właściwości** Ustaw okienku **wymagane przypisanie użytkownika?** ustawienie **nie**. 

**Wymagane przypisanie użytkownika?** opcji:

- Nie ma wpływu na, czy aplikacja jest wyświetlana na panelu dostępu do aplikacji. Aby wyświetlić aplikację w panelu dostępu, należy przypisać do użytkowników lub grupy do aplikacji.
- Tylko funkcje z aplikacjami w chmurze, które są skonfigurowane dla protokołu SAML logowania jednokrotnego, a aplikacje skonfigurowane za pomocą serwera Proxy aplikacji lokalnych. Zobacz [logowanie jednokrotne dla aplikacji](what-is-single-sign-on.md).
- Wymaga się, że użytkownicy wyrazić zgodę na aplikację. Administrator można udzielić zgody dla wszystkich użytkowników.  Zobacz [Konfiguruj sposób użytkownicy końcowi zgoda na aplikację](configure-user-consent.md).


## <a name="assign-a-user-to-an-app---powershell"></a>Przypisz użytkownika do aplikacji — PowerShell

1. Otwórz wiersz polecenia programu Windows PowerShell.

    >[!NOTE] 
    > Musisz zainstalować moduł usługi Azure AD (Użyj polecenia `Install-Module -Name AzureAD`). Jeśli zostanie wyświetlony monit, aby zainstalować moduł NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory w wersji 2, wpisz Y, a następnie naciśnij klawisz ENTER.

1. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta użytkownika administratora globalnego.
1. Aby przypisać użytkownika i roli aplikacji, użyj następującego skryptu:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

Aby uzyskać więcej informacji o tym, jak przypisać użytkownika do roli aplikacji można znaleźć w dokumentacji dotyczącej [New AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Aby przypisać grupę do aplikacji przedsiębiorstwa, musisz zastąpić `Get-AzureADUser` z `Get-AzureADGroup`.

### <a name="example"></a>Przykład

Ten przykład umożliwia przypisanie użytkownika Britta Simon do [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) aplikacji przy użyciu programu PowerShell.

1. W programie PowerShell należy przypisać odpowiednie wartości zmiennych $username $nazwa_aplikacji oraz $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. W tym przykładzie firma Microsoft nie wiadomo, co to jest dokładną nazwę roli aplikacji, którą chcesz przypisać do Britta Simon. Uruchom następujące polecenia, aby pobrać użytkownika ($user) i nazwa główna usługi ($sp) przy użyciu nazwy UPN użytkownika i jednostki usługi są wyświetlane nazwy.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
1. Uruchom polecenie `$sp.AppRoles` do wyświetlenia dostępnych dla danej aplikacji Workplace Analytics ról. W tym przykładzie chcemy przypisać Britta Simon rola analityka (ograniczony dostęp).
    
    ![Obszar roboczy analizy roli](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Przypisz nazwę roli aby `$app_role_name` zmiennej.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Uruchom następujące polecenie, aby przypisać użytkownika do roli aplikacji:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Kolejne kroki
* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa](disable-user-sign-in-portal.md)
* [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)
