---
title: Ustaw niestandardową stronę główną dla opublikowanych aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące serwera Proxy aplikacji usługi Azure AD łączników
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 3f2dd8dd860ecc1da476fcbccbe16f9c21e3c836
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ustaw niestandardową stronę główną dla opublikowanych aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób konfigurowania aplikacji, aby kierować użytkowników do niestandardowej strony głównej. Podczas publikowania aplikacji przy użyciu serwera Proxy aplikacji, należy ustawić wewnętrzny adres URL jednak czasami nie będący strony, które należy najpierw widzą użytkownicy. Ustaw niestandardową stronę główną, dzięki czemu użytkownicy przejdź do prawej strony, przy uzyskiwaniu dostępu do aplikacji. Użytkownicy zobaczą niestandardową stronę główną, która zostanie ustawiona, czy uzyskują oni dostęp do aplikacji z usługi Azure Active Directory panelu dostępu lub uruchamiania aplikacji usługi Office 365.

Gdy użytkownicy uruchomią aplikację, są one skierowane domyślnie do adresu URL domeny katalogu głównego dla opublikowanych aplikacji. Strona docelowa jest zwykle ustawiana jako adres URL strony głównej. Użyj modułu Azure AD PowerShell do definiowania adresy URL niestandardową stronę główną, gdy chcesz użytkownikom aplikacji przejście na określonej strony w aplikacji. 

Oto przykład, dlaczego firmy ustawiał niestandardową stronę główną:
- W sieci firmowej użytkownicy przejść do *https://ExpenseApp/login/login.aspx* logować się i uzyskać dostępu do aplikacji.
- Ponieważ inne zasoby, takie jak obrazy, które serwer Proxy aplikacji musi mieć dostęp na najwyższym poziomie struktury folderów, Opublikuj aplikację z *https://ExpenseApp* jako wewnętrzny adres URL.
- Domyślny adres URL zewnętrznego *https://ExpenseApp-contoso.msappproxy.net*, która nie przyjmuje użytkowników do strony logowania.  
- Ustaw *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* jako adres URL strony głównej. 

>[!NOTE]
>Gdy można udzielać użytkownikom dostępu do opublikowanych aplikacji, aplikacje są wyświetlane w [Panel dostępu usługi Azure AD](active-directory-saas-access-panel-introduction.md) i [uruchamiania aplikacji usługi Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby ustawić adres URL strony głównej, należy pamiętać, następujące wymagania:

* Upewnij się, że ścieżkę, którą określisz jest ścieżką poddomeny domeny głównego adresu URL.

  W przypadku adresu URL domeny głównej, na przykład https://apps.contoso.com/app1/, adres URL strony głównej, które można skonfigurować musi rozpoczynać się od https://apps.contoso.com/app1/.

* Jeśli wprowadzisz zmiany w opublikowanej aplikacji, zmiana może zresetować wartość adres URL strony głównej. Podczas aktualizacji aplikacji w przyszłości, należy ponownie sprawdzić i, w razie potrzeby zaktualizuj adres URL strony głównej.

## <a name="change-the-home-page-in-the-azure-portal"></a>Zmienianie strony głównej w portalu Azure

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **rejestracji aplikacji** i wybierz aplikację z listy. 
3. Wybierz **właściwości** z ustawień.
4. Aktualizacja **adres URL strony głównej** pole z nowej ścieżki. 

   ![Podaj adres URL nowej strony głównej](./media/application-proxy-office365-app-launcher/homepage.png)

5. Wybierz **Zapisz**

## <a name="change-the-home-page-with-powershell"></a>Zmienianie strony głównej przy użyciu programu PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Zainstaluj moduł programu PowerShell usługi Azure AD

Przed zdefiniowaniem adres URL niestandardowej strony głównej przy użyciu programu PowerShell, należy zainstalować moduł Azure AD PowerShell. Możesz pobrać pakiet ze [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), która wykorzystuje punkt końcowy interfejsu API programu Graph. 

Aby zainstalować pakiet, wykonaj następujące kroki:

1. Otwórz okno programu PowerShell standard, a następnie uruchom następujące polecenie:

    ```
     Install-Module -Name AzureAD
    ```
    Jeśli używasz polecenia jako bez uprawnień administratora, użyj `-scope currentuser` opcji.
2. Podczas instalacji wybierz **Y** zainstalować dwóch pakietów z Nuget.org. Oba pakiety są wymagane. 

### <a name="find-the-objectid-of-the-app"></a>Znajdź identyfikator obiektu aplikacji

Uzyskaj identyfikator obiektu aplikacji, a następnie wyszukaj aplikację przez jego strony głównej.

1. W tym samym oknie programu PowerShell Zaimportuj moduł usługi Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Zaloguj się do modułu Azure AD jako administrator dzierżawy.

    ```
    Connect-AzureAD
    ```
3. Znajdź na podstawie jego adresu URL strony głównej. Adres URL można znaleźć w portalu, przechodząc do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**. W tym przykładzie użyto *sharepoint iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. Należy uzyskać wynik, który jest podobny do pokazanego poniżej. Skopiuj identyfikator GUID ObjectID do użycia w następnej sekcji.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Aktualizacja adresu URL strony głównej

Utwórz adres URL strony głównej i aktualizacji aplikacji przy użyciu tej wartości. Nadal używać w tym samym oknie programu PowerShell do uruchamiania tych poleceń. Lub, jeśli używasz nowe okno programu PowerShell, zaloguj się ponownie przy użyciu modułu Azure AD `Connect-AzureAD`. 

1. Potwierdź prawidłowe aplikacji i Zastąp *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* z ObjectID, które zostały skopiowane z poprzedniej sekcji.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Po potwierdzeniu aplikacji możesz zaktualizować strony głównej w następujący sposób.

2. Utwórz obiekt pusta aplikacja do zmiany, które mają być przechowywane. Ta zmienna przechowuje wartości, które chcesz zaktualizować. Nic nie jest tworzony w tym kroku.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Ustaw adres URL strony głównej na żądaną wartość. Wartość musi być ścieżką poddomeny opublikowanych aplikacji. Na przykład zmień adres URL strony głównej z *https://sharepoint-iddemo.msappproxy.net/* do *https://sharepoint-iddemo.msappproxy.net/hybrid/*, użytkownicy aplikacji przejść bezpośrednio do strony głównej niestandardowego.

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Tworzenie aktualizacji przy użyciu identyfikatora GUID (identyfikator obiektu) skopiowany w "krok 1: znajdowanie ObjectID aplikacji."

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Aby upewnić się, że zmiana zakończyła się pomyślnie, uruchom ponownie aplikację.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Wszelkie zmiany wprowadzone do aplikacji może zresetować adres URL strony głównej. Jeśli adres URL strony głównej resetuje, powtórz kroki opisane w tej sekcji, aby ustawić ją ponownie.

## <a name="next-steps"></a>Kolejne kroki

- [Włączenie dostępu zdalnego do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-enable-remote-access-sharepoint.md)
- [Włącz serwer Proxy aplikacji w portalu Azure](manage-apps/application-proxy-enable.md)
