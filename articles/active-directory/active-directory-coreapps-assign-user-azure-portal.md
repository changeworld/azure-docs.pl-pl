---
title: Przypisanie użytkownika lub grupę do aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak wybrać aplikację przedsiębiorstwa przypisać użytkownika lub grupę do niego w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: markvi
ms.reviewer: luleon
ms.openlocfilehash: 50e122ba3912d295adfc9207c7efda685f2d4625
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Przypisanie użytkownika lub grupę do aplikacji przedsiębiorstwa w usłudze Azure Active Directory
Aby przypisać użytkownika lub grupę do aplikacji w przedsiębiorstwie, musi mieć odpowiednie uprawnienia do zarządzania aplikacjami przedsiębiorstwa, a musi być administratorem globalnym katalogu.
> [!NOTE]
> For Applications firmy Microsoft (takich jak aplikacje pakietu Office 365) Przypisz użytkowników do aplikacji w organizacji za pomocą programu PowerShell.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Jak przypisać dostępu użytkownika do aplikacji przedsiębiorstwa w portalu Azure?
1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **wszystkie usługi**wprowadź Azure Active Directory w polu tekstowym, a następnie wybierz **Enter**.
3. Na **usługi Azure Active Directory - *directoryname***  bloku (to znaczy usługi Azure AD bloku katalogu zarządzasz), wybierz **aplikacje dla przedsiębiorstw**.

    ![Otwieranie aplikacji przedsiębiorstwa](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. Na **aplikacje dla przedsiębiorstw** bloku, wybierz opcję **wszystkie aplikacje**. Ta lista zawiera aplikacje, którymi można zarządzać.
5. Na **aplikacje przedsiębiorstwa — wszystkie aplikacje** bloku, wybierz aplikację.
6. Na ***appname*** bloku (to znaczy bloku o nazwie wybranej aplikacji w tytule), wybierz **użytkownicy i grupy**.

    ![Polecenie wszystkie aplikacje](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. Na ***appname*** **— przypisanie do grupy & użytkownika** bloku, wybierz opcję **Dodaj** polecenia.
8. Na **Dodaj przydziału** bloku, wybierz opcję **użytkowników i grup**.

    ![Przypisanie użytkownika lub grupę do aplikacji](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. Na **użytkowników i grup** bloku, wybierz jeden lub więcej użytkowników lub grup z listy, a następnie wybierz **wybierz** przycisk w dolnej części bloku.
10. Na **Dodaj przydziału** bloku, wybierz opcję **roli**. Następnie na **wybierz rolę** bloku, wybierz rolę do zastosowania do wybranych użytkowników lub grup, a następnie wybierz **OK** przycisk w dolnej części bloku.
11. Na **Dodaj przydziału** bloku, wybierz opcję **przypisać** przycisk w dolnej części bloku. Przypisanych użytkowników lub grupy mają uprawnienia określone przez wybraną rolę dla tej aplikacji przedsiębiorstwa.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Jak przypisać użytkownika do aplikacji przedsiębiorstwa za pomocą programu PowerShell?

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień programu Windows PowerShell.

    >[!NOTE] 
    > Musisz zainstalować moduł AzureAD (za pomocą polecenia `Install-Module -Name AzureAD`). Jeśli zostanie wyświetlony monit, aby zainstalować moduł NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory w wersji 2, typ T, a następnie naciśnij klawisz ENTER.

2. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta administratora globalnego.
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

Aby uzyskać więcej informacji na temat sposobu przypisywania użytkowników do roli aplikacji można znaleźć w dokumentacji [AzureADUserAppRoleAssignment nowy](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Aby przypisać grupę do aplikacji w organizacji, należy zastąpić `Get-AzureADUser` z `Get-AzureADGroup`.

### <a name="example"></a>Przykład

W tym przykładzie przypisuje użytkownika Simona Britta do [Microsoft Analytics pracy](https://products.office.com/en-us/business/workplace-analytics) aplikacji przy użyciu programu PowerShell.

1. W programie PowerShell przypisać odpowiednie wartości zmiennych $username, nazwa_aplikacji $ i $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. W tym przykładzie firma Microsoft nie wiadomo, co to jest dokładną nazwę roli aplikacji, którą chcemy, aby przypisać do Simona Britta. Uruchom następujące polecenia, aby pobrać użytkownika ($user) i nazwy głównej usługi ($sp) przy użyciu nazwy UPN użytkownika i nazwę główną usługi nazw wyświetlanych.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Uruchom polecenie `$sp.AppRoles` do wyświetlenia dostępnych dla danej aplikacji miejsca pracy Analytics ról. W tym przykładzie chcemy przypisać Simona Britta rola analityka (ograniczony dostęp).
    
    ![Rola analityka miejsca pracy](media/active-directory-coreapps-assign-user-azure-portal/workplace-analytics-role.png)

4. Nazwa roli, aby przypisać `$app_role_name` zmiennej.
        
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
* [Wyświetl wszystkie moje grupy](active-directory-groups-view-azure-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacjami](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Wyłącz logowania użytkowników dla aplikacji przedsiębiorstwa](active-directory-coreapps-disable-app-azure-portal.md)
* [Zmiana nazwy lub logo aplikacji przedsiębiorstwa](active-directory-coreapps-change-app-logo-user-azure-portal.md)
