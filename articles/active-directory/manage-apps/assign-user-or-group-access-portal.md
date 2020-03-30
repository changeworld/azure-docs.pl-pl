---
title: Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure AD
description: Jak wybrać aplikację przedsiębiorstwa, aby przypisać do niej użytkownika lub grupę w usłudze Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409196"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory

W tym artykule pokazano, jak przypisać użytkowników lub grupy do aplikacji korporacyjnych w usłudze Azure Active Directory (Azure AD), z poziomu witryny Azure portal lub przy użyciu programu PowerShell. Po przypisaniu użytkownika do aplikacji aplikacja jest wyświetlana w [panelu dostępu Moje aplikacje](https://myapps.microsoft.com/) użytkownika w celu ułatwienia dostępu. Jeśli aplikacja udostępnia role, można również przypisać określoną rolę do użytkownika.

Aby uzyskać większą kontrolę, niektóre typy aplikacji korporacyjnych można skonfigurować tak, aby [wymagały przypisania użytkownika](#configure-an-application-to-require-user-assignment). 

Aby [przypisać użytkownika lub grupę do aplikacji przedsiębiorstwa,](#assign-users-or-groups-to-an-app-via-the-azure-portal)musisz zalogować się jako administrator globalny, administrator aplikacji, administrator aplikacji w chmurze lub przypisany właściciel aplikacji przedsiębiorstwa.

> [!NOTE]
> Przypisanie oparte na grupach wymaga usługi Azure Active Directory Premium P1 lub P2 edition. Przypisanie oparte na grupach jest obsługiwane tylko dla grup zabezpieczeń. Członkostwo w grupach zagnieżdżonych i grupy usługi Office 365 nie są obecnie obsługiwane. Aby uzyskać więcej wymagań licencyjnych dotyczących funkcji omówionych w tym artykule, zobacz [stronę cennika usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurowanie aplikacji w celu wymagania przypisania przez użytkownika

W przypadku następujących typów aplikacji można wymagać od użytkowników przypisania ich do aplikacji, zanim będą mogli uzyskać do niej dostęp:

- Aplikacje skonfigurowane do rejestracji sfederowanej (Logowanie jednokrotne) z uwierzytelnianiem opartym na SAML
- Aplikacje proxy aplikacji korzystające z uwierzytelniania wstępnego usługi Azure Active Directory
- Aplikacje utworzone na platformie aplikacji usługi Azure AD, które używają uwierzytelniania OAuth 2.0 / OpenID Connect po zgodę użytkownika lub administratora na tę aplikację.

Gdy przypisanie użytkownika jest wymagane, tylko ci użytkownicy, których jawnie przypisujesz do aplikacji, będą mogli się zalogować. Mogą uzyskać dostęp do aplikacji na stronie Moje aplikacje lub za pomocą bezpośredniego linku. 

Jeśli przypisanie nie jest *wymagane*, ponieważ ta opcja została ustawiona na **Nie** lub ponieważ aplikacja używa innego trybu jedno i samodzielnego, każdy użytkownik będzie mógł uzyskać dostęp do aplikacji, jeśli ma bezpośrednie łącze do aplikacji lub **adres URL dostępu użytkownika** na stronie **Właściwości** aplikacji. 

To ustawienie nie ma wpływu na to, czy aplikacja jest wyświetlana w panelu Dostęp do aplikacji Moje aplikacje. Aplikacje są wyświetlane w panelach dostępu Moje aplikacje użytkowników po przypisaniu użytkownika lub grupy do aplikacji. Aby uzyskać informacje na temat tła, zobacz [Zarządzanie dostępem do aplikacji](what-is-access-management.md).


Aby wymagać przypisania użytkownika dla aplikacji:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta administratora lub jako właściciel aplikacji.

2. Wybierz pozycję **Azure Active Directory**. W menu nawigacji po lewej stronie wybierz pozycję **Aplikacje przedsiębiorstwa**.

3. Wybierz aplikację z listy. Jeśli nie widzisz aplikacji, zacznij wpisywać jej nazwę w polu wyszukiwania. Możesz też użyć kontrolek filtru, aby wybrać typ aplikacji, stan lub widoczność, a następnie wybrać **pozycję Zastosuj**.

4. W menu nawigacji po lewej stronie wybierz polecenie **Właściwości**.

5. Upewnij się, że wymagane **przypisanie użytkownika?** przełącznik jest ustawiony na **Tak**.

   > [!NOTE]
   > Jeśli **przełącznik Przypisanie użytkownika jest niedostępne,** można użyć programu PowerShell, aby ustawić właściwość appRoleAssignmentRequired na rzeczoznawcy usługi.

6. Wybierz przycisk **Zapisz** u góry ekranu.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Przypisywanie użytkowników lub grup do aplikacji za pośrednictwem witryny Azure portal

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta administratora globalnego, administratora aplikacji lub administratora aplikacji w chmurze lub jako przypisany właściciel aplikacji przedsiębiorstwa.
2. Wybierz pozycję **Azure Active Directory**. W menu nawigacji po lewej stronie wybierz pozycję **Aplikacje przedsiębiorstwa**.
3. Wybierz aplikację z listy. Jeśli nie widzisz aplikacji, zacznij wpisywać jej nazwę w polu wyszukiwania. Możesz też użyć kontrolek filtru, aby wybrać typ aplikacji, stan lub widoczność, a następnie wybrać **pozycję Zastosuj**.
4. W menu nawigacji po lewej stronie wybierz pozycję **Użytkownicy i grupy**.
   > [!NOTE]
   > Jeśli chcesz przypisać użytkowników do aplikacji Microsoft Applications, takich jak aplikacje office 365, niektóre z tych aplikacji używają programu PowerShell. 
5. Wybierz przycisk **Dodaj użytkownika.**
6. W okienku **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.
7. Wybierz użytkownika lub grupę, którą chcesz przypisać do aplikacji, lub zacznij wpisywać nazwę użytkownika lub grupy w polu wyszukiwania. Można wybrać wielu użytkowników i grupy, a wybrane opcje pojawią się w obszarze **Wybrane elementy**.
8. Po zakończeniu kliknij przycisk **Wybierz**.

   ![Przypisywanie użytkownika lub grupy do aplikacji](./media/assign-user-or-group-access-portal/assign-users.png)

9. W okienku **Użytkownicy i grupy** wybierz jednego lub więcej użytkowników lub grup z listy, a następnie wybierz przycisk **Wybierz** u dołu okienka.
10. Jeśli aplikacja obsługuje go, można przypisać rolę do użytkownika lub grupy. W okienku **Dodawanie przydziału** wybierz pozycję **Wybierz rolę**. Następnie w okienku **Wybierz rolę** wybierz rolę, która ma być stosowana do wybranych użytkowników lub grup, a następnie wybierz **przycisk OK** u dołu okienka. 

    > [!NOTE]
    > Jeśli aplikacja nie obsługuje wyboru roli, przypisana jest domyślna rola dostępu. W takim przypadku aplikacja zarządza poziomem dostępu użytkowników.

2. W okienku **Dodawanie przydziału** wybierz przycisk **Przypisz** u dołu okienka.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Przypisywanie użytkowników lub grup do aplikacji za pośrednictwem programu PowerShell

1. Otwórz wiersz polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień.

   > [!NOTE]
   > Należy zainstalować moduł AzureAD (użyj `Install-Module -Name AzureAD`polecenia ). Jeśli zostanie wyświetlony monit o zainstalowanie modułu NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory V2, wpisz Y i naciśnij klawisz ENTER.

1. Uruchamianie `Connect-AzureAD` konta użytkownika administratora globalnego i logowanie się do tego konta administratora globalnego.
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

Aby uzyskać więcej informacji na temat przypisywania użytkownika do roli aplikacji, odwiedź dokumentację [nowego podpisu AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Aby przypisać grupę do aplikacji dla `Get-AzureADUser` przedsiębiorstwa, należy zastąpić `Get-AzureADGroup`go .

### <a name="example"></a>Przykład

W tym przykładzie przypisuje użytkownika Britta Simon do aplikacji [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) za pomocą programu PowerShell.

1. W programie PowerShell przypisz odpowiednie wartości do zmiennych $username, $app_name i $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. W tym przykładzie nie wiemy, jaka jest dokładna nazwa roli aplikacji, którą chcemy przypisać do Britta Simon. Uruchom następujące polecenia, aby uzyskać użytkownika ($user) i jednostki usługi ($sp) przy użyciu nazwy UPN użytkownika i nazwy wyświetlane głównej usługi.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Uruchom polecenie, `$sp.AppRoles` aby wyświetlić role dostępne dla aplikacji Workplace Analytics. W tym przykładzie chcemy przypisać Britta Simon analityka (ograniczony dostęp) rola.

   ![Pokazuje role dostępne dla użytkownika przy użyciu roli analizy w miejscu pracy](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Przypisz nazwę roli `$app_role_name` do zmiennej.

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

## <a name="related-articles"></a>Pokrewne artykuły:

- [Dowiedz się więcej o dostępie użytkowników końcowych do aplikacji](end-user-experiences.md)
- [Planowanie wdrożenia panelu dostępu usługi Azure AD](access-panel-deployment-plan.md)
- [Zarządzanie dostępem do aplikacji](what-is-access-management.md)
 
## <a name="next-steps"></a>Następne kroki

- [Zobacz wszystkie moje grupy](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
- [Wyłączanie logów użytkowników dla aplikacji dla przedsiębiorstwa](disable-user-sign-in-portal.md)
- [Zmienianie nazwy lub logo aplikacji przedsiębiorstwa](change-name-or-logo-portal.md)
