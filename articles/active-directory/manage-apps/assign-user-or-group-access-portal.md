---
title: Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak wybrać aplikację przedsiębiorstwa, aby przypisać użytkownika lub grupy do niej w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: ee0b14123e193f219e403d2608368c27f953013d
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037978"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory
Aby przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa, musi mieć odpowiednie uprawnienia do zarządzania aplikacji przedsiębiorstwa, a musi być administratorem globalnym katalogu.

> [!NOTE]
> Wymagania dotyczące funkcji omówionych w tym artykule z licencjonowaniem znajdują [cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> For Applications firmy Microsoft (takich jak aplikacje usługi Office 365) należy użyć programu PowerShell do przypisywania użytkowników do aplikacji przedsiębiorstwa.


## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Jak przypisywać użytkownikom dostęp do aplikacji przedsiębiorstwa w witrynie Azure portal?
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **wszystkich usług**wprowadź usługi Azure Active Directory w polu tekstowym, a następnie wybierz pozycję **Enter**.
3. Wybierz **aplikacje dla przedsiębiorstw**.

    ![Otwieranie aplikacji dla przedsiębiorstw](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. Na **aplikacje dla przedsiębiorstw** bloku wybierz **wszystkie aplikacje**. Ta lista zawiera aplikacje, którymi można zarządzać.
5. Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** bloku, wybierz aplikację.
6. Na ***appname*** bloku (czyli bloku o nazwie wybranej aplikacji w tytule), wybierz **użytkownikami i grupami**.

    ![Wybierając polecenie wszystkie aplikacje](./media/assign-user-or-group-access-portal/select-app-users.png)
7. Na ***appname*** **— przypisanie do grupy & użytkownika** bloku wybierz **Dodaj** polecenia.
8. Na **Dodaj przydziału** bloku wybierz **użytkowników i grup**.

    ![Przypisywanie użytkownika lub grupy do aplikacji](./media/assign-user-or-group-access-portal/assign-users.png)
9. Na **użytkowników i grup** bloku, wybierz z listy jeden lub więcej użytkowników lub grup, a następnie wybierz pozycję **wybierz** znajdujący się u dołu bloku.
10. Na **Dodaj przydziału** bloku wybierz **roli**. Następnie na **wybierz rolę** bloku, wybierz rolę do zastosowania do wybranych użytkowników lub grup, a następnie wybierz **OK** znajdujący się u dołu bloku.
11. Na **Dodaj przydziału** bloku wybierz **przypisać** znajdujący się u dołu bloku. Przypisani użytkownicy lub grupy mają uprawnienia zdefiniowane przez wybraną rolę dla tej aplikacji przedsiębiorstwa.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Jak przypisać użytkownika do aplikacji przedsiębiorstwa przy użyciu programu PowerShell?

1. Otwórz wiersz polecenia programu Windows PowerShell.

    >[!NOTE] 
    > Musisz zainstalować moduł usługi Azure AD (Użyj polecenia `Install-Module -Name AzureAD`). Jeśli zostanie wyświetlony monit, aby zainstalować moduł NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory w wersji 2, wpisz Y, a następnie naciśnij klawisz ENTER.

2. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta użytkownika administratora globalnego.
3. Aby przypisać użytkownika i roli aplikacji, użyj następującego skryptu:

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

2. W tym przykładzie firma Microsoft nie wiadomo, co to jest dokładną nazwę roli aplikacji, którą chcesz przypisać do Britta Simon. Uruchom następujące polecenia, aby pobrać użytkownika ($user) i nazwa główna usługi ($sp) przy użyciu nazwy UPN użytkownika i jednostki usługi są wyświetlane nazwy.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Uruchom polecenie `$sp.AppRoles` do wyświetlenia dostępnych dla danej aplikacji Workplace Analytics ról. W tym przykładzie chcemy przypisać Britta Simon rola analityka (ograniczony dostęp).
    
    ![Obszar roboczy analizy roli](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. Przypisz nazwę roli aby `$app_role_name` zmiennej.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. Uruchom następujące polecenie, aby przypisać użytkownika do roli aplikacji:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Kolejne kroki
* [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa](disable-user-sign-in-portal.md)
* [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)
