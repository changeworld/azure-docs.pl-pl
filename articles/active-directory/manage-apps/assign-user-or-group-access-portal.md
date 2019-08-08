---
title: Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw w Azure Active Directory | Microsoft Docs
description: Jak wybrać aplikację dla przedsiębiorstw, aby przypisać do niej użytkownika lub grupę w Azure Active Directory
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
ms.openlocfilehash: 15f7e830079c224e9e15dd45d14c1741376f8762
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851695"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw w Azure Active Directory

Aby przypisać użytkownika lub grupę do aplikacji dla przedsiębiorstw, musisz mieć odpowiednie uprawnienia do zarządzania aplikacją dla przedsiębiorstw, a użytkownik musi być administratorem globalnym katalogu. W przypadku aplikacji firmy Microsoft (takich jak aplikacje pakietu Office 365) należy używać programu PowerShell do przypisywania użytkowników do aplikacji dla przedsiębiorstw.

> [!NOTE]
> Wymagania dotyczące licencjonowania funkcji omówionych w tym artykule znajdują się na [stronie cennika Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Przypisywanie użytkownika do portalu aplikacji

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz pozycję **wszystkie usługi**, wpisz Azure Active Directory w polu tekstowym, a następnie wybierz klawisz **Enter**.
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**.
1. W okienku **aplikacje dla przedsiębiorstw — wszystkie aplikacje** zostanie wyświetlona lista aplikacji, którymi można zarządzać. Wybierz aplikację.
1. W okienku ***nazwa_aplikacji*** (czyli okienku z nazwą wybranej aplikacji w tytule) wybierz pozycję **Użytkownicy & grupy**.
1. W okienku ***nazwa_aplikacji*** **— Użytkownicy i grupy** wybierz pozycję **Dodaj użytkownika**.
1. W okienku **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy**.

   ![Przypisywanie użytkownika lub grupy do aplikacji](./media/assign-user-or-group-access-portal/assign-users.png)

1. W okienku **Użytkownicy i grupy** wybierz co najmniej jednego użytkownika lub grupę z listy, a następnie wybierz przycisk **Wybierz** w dolnej części okienka.
1. W okienku **Dodaj przypisanie** wybierz pozycję **rola**. Następnie w okienku **Wybierz rolę** wybierz rolę, która ma zostać zastosowana do wybranych użytkowników lub grup, a następnie wybierz pozycję **OK** w dolnej części okienka.
1. W okienku **Dodaj przypisanie** wybierz przycisk **Przypisz** w dolnej części okienka. Przypisani użytkownicy lub grupy mają uprawnienia zdefiniowane przez wybraną rolę dla tej aplikacji dla przedsiębiorstw.

## <a name="allow-all-users-to-access-an-app---portal"></a>Zezwalaj wszystkim użytkownikom na dostęp do portalu aplikacji

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz pozycję **wszystkie usługi**, wpisz Azure Active Directory w polu tekstowym, a następnie wybierz klawisz **Enter**.
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**.
1. W okienku **aplikacje dla przedsiębiorstw** wybierz pozycję **wszystkie aplikacje**. Ta lista zawiera aplikacje, którymi można zarządzać.
1. W okienku **aplikacje dla przedsiębiorstw — wszystkie aplikacje** wybierz aplikację.
1. W okienku ***nazwa_aplikacji*** wybierz pozycję **Właściwości**.
1. W okienku  ***nazwa_aplikacji* -właściwości** Ustaw **wymagane przypisanie użytkownika?** wartość **nie**.

**Wymagane przypisanie użytkownika?** opcja:

- Jeśli ta opcja jest ustawiona na tak, użytkownicy muszą najpierw zostać przypisani do tej aplikacji, zanim będą mogli uzyskać do niej dostęp.
- Jeśli ta opcja jest ustawiona na wartość nie, wszyscy użytkownicy, którzy przejdą do adresu URL bezpośredniego linku do aplikacji lub adresu URL aplikacji, będą mieli dostęp
- Nie ma wpływu na to, czy aplikacja jest wyświetlana w panelu dostępu do aplikacji. Aby wyświetlić aplikację w panelu dostępu, należy przypisać do aplikacji odpowiedniego użytkownika lub grupę.
- Działa tylko z aplikacjami w chmurze skonfigurowanymi do logowania jednokrotnego w protokole SAML, aplikacji serwera proxy aplikacji, które używają Azure Active Directory wstępnego uwierzytelniania lub aplikacji utworzonych bezpośrednio na platformie aplikacji usługi Azure AD, która używa protokołu OAuth 2,0/ OpenID Connect połączenie z uwierzytelnianiem, gdy użytkownik lub administrator wyraził zgodę na tę aplikację. Zobacz [Logowanie jednokrotne dla aplikacji](what-is-single-sign-on.md). Zobacz [Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikację](configure-user-consent.md).
- Ta opcja nie obowiązuje, gdy aplikacja jest skonfigurowana dla dowolnego z trybów logowania jednokrotnego.

## <a name="assign-a-user-to-an-app---powershell"></a>Przypisywanie użytkownika do aplikacji — PowerShell

1. Otwórz wiersz polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień.

   > [!NOTE]
   > Należy zainstalować moduł AzureAD (Użyj polecenia `Install-Module -Name AzureAD`). Jeśli zostanie wyświetlony monit o zainstalowanie modułu NuGet lub nowego modułu Azure Active Directory v2 PowerShell, wpisz Y i naciśnij klawisz ENTER.

1. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta administratora globalnego.
1. Użyj następującego skryptu, aby przypisać użytkownika i rolę do aplikacji:

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

Więcej informacji o sposobie przypisywania użytkownika do roli aplikacji można znaleźć w dokumentacji usługi [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Aby przypisać grupę do aplikacji dla przedsiębiorstw, należy zamienić `Get-AzureADUser` `Get-AzureADGroup`ją na.

### <a name="example"></a>Przykład

Ten przykład przypisuje użytkownikowi Britta Simon do aplikacji [analizy w miejscu pracy firmy Microsoft](https://products.office.com/business/workplace-analytics) przy użyciu programu PowerShell.

1. W programie PowerShell Przypisz odpowiednie wartości do zmiennych $username, $app _name i $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. W tym przykładzie nie wiemy, co to jest dokładna nazwa roli aplikacji, którą chcemy przypisać do Britta Simon. Uruchom następujące polecenia, aby uzyskać użytkownika ($user) i nazwę główną usługi ($sp) przy użyciu nazwy UPN użytkownika i nazw wyświetlanych jednostki usługi.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Uruchom polecenie `$sp.AppRoles` , aby wyświetlić role dostępne dla aplikacji do analizy w miejscu pracy. W tym przykładzie chcemy przypisać rolę analityka (ograniczony dostęp) Britta Simon.

   ![Pokazuje role dostępne dla użytkownika przy użyciu roli analiza w miejscu pracy](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Przypisz nazwę roli do `$app_role_name` zmiennej.

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

## <a name="next-steps"></a>Następne kroki

- [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Usuwanie przypisania użytkownika lub grupy z aplikacji dla przedsiębiorstw](remove-user-or-group-access-portal.md)
- [Wyłączanie logowania użytkownika dla aplikacji dla przedsiębiorstw](disable-user-sign-in-portal.md)
- [Zmiana nazwy lub logo aplikacji dla przedsiębiorstw](change-name-or-logo-portal.md)
