---
title: Ustaw niestandardową stronę główną dla opublikowanych aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące łączników serwera Proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0880ad2ab02fad574f5204741b0fa03e4ef0338
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648067"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ustaw niestandardową stronę główną dla opublikowanych aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób konfigurowania aplikacji, aby przekierować użytkowników do niestandardowej strony głównej. Podczas publikowania aplikacji za pomocą serwera Proxy aplikacji, możesz ustawić wewnętrzny adres URL jednak czasami nie będący żądanej strony użytkowników powinien zostać wyświetlony najpierw. Ustaw niestandardową stronę główną, aby użytkownicy, przejdź do prawej strony podczas uzyskiwania dostępu do aplikacji. Użytkownicy zobaczą niestandardową stronę główną, który został ustawiony, czy uzyskania dostępu do aplikacji z usługi Azure Active Directory panelu dostępu lub uruchamianie aplikacji usługi Office 365.

Podczas uruchamiania aplikacji przez użytkowników, są one skierowane domyślnie adres URL domeny katalogu głównego dla opublikowanej aplikacji. Strona docelowa jest zwykle ustawiana jako adres URL strony głównej. Użyj modułu Azure AD PowerShell do definiowania adresy URL niestandardową stronę główną, jeśli chcesz, aby użytkownikom aplikacji przejście na określonej stronie w aplikacji. 

Oto przykład, dlaczego firma ustawiał niestandardowa strona główna:
- W sieci firmowej użytkownicy przejść do `https://ExpenseApp/login/login.aspx` logować się i uzyskać dostęp do Twojej aplikacji.
- Ponieważ inne zasoby, takie jak obrazy, które serwer Proxy aplikacji musi mieć dostęp na najwyższym poziomie w strukturze folderów, publikowanie aplikacji przy użyciu `https://ExpenseApp` jako wewnętrzny adres URL.
- Domyślny adres URL zewnętrznego `https://ExpenseApp-contoso.msappproxy.net`, która nie przyjmuje użytkowników na stronę logowania.  
- Ustaw `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` jako adres URL strony głównej. 

>[!NOTE]
>Po zapewnieniu użytkownikom dostępu do opublikowanych aplikacji, aplikacje są wyświetlane w [panelu dostępu usługi Azure AD](../user-help/active-directory-saas-access-panel-introduction.md) i [uruchamianie aplikacji usługi Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed ustawieniem adres URL strony głównej, należy przestrzegać następujących wymagań:

* Upewnij się, że ścieżka określona ścieżka adresu URL domeny katalogu głównego poddomeny.

  W przypadku adresu URL domeny katalogu głównego, na przykład https://apps.contoso.com/app1/, musi rozpoczynać się adres URL strony głównej, które konfigurujesz https://apps.contoso.com/app1/.

* Jeśli wprowadzisz zmiany w opublikowanej aplikacji, zmiana może zresetować wartość adres URL strony głównej. Po zaktualizowaniu aplikacji w przyszłości, należy przeprowadzić ponowne sprawdzenie i, jeśli to konieczne, zaktualizuj adres URL strony głównej.

## <a name="change-the-home-page-in-the-azure-portal"></a>Zmień stronę główną w witrynie Azure portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **rejestracje aplikacji** i wybierz aplikację z listy. 
3. Wybierz **właściwości** przy użyciu ustawień.
4. Aktualizacja **adres URL strony głównej** pole Twojej nowej ścieżki. 

   ![Podaj nowy adres URL strony głównej](./media/application-proxy-configure-custom-home-page/homepage.png)

5. Wybierz **Zapisz**

## <a name="change-the-home-page-with-powershell"></a>Zmień stronę główną przy użyciu programu PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Instalowanie modułu Azure AD PowerShell

Przed zdefiniowaniem adres URL niestandardowej strony głównej przy użyciu programu PowerShell, należy zainstalować moduł programu PowerShell usługi Azure AD. Możesz pobrać pakiet ze [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), który używa punktu końcowego interfejsu API programu Graph. 

Aby zainstalować pakiet, wykonaj następujące kroki:

1. Otwórz okno programu PowerShell standardowe, a następnie uruchom następujące polecenie:

    ```powershell
     Install-Module -Name AzureAD
    ```

    Jeśli używasz polecenia jako użytkownik inny niż administrator, należy użyć `-scope currentuser` opcji.
2. Podczas instalacji należy wybrać **Y** zainstalować dwa pakiety z witryny Nuget.org. Wymagane są oba pakiety. 

### <a name="find-the-objectid-of-the-app"></a>Znajdź identyfikator obiektu aplikacji

Uzyskaj identyfikator obiektu aplikacji, a następnie wyszukaj aplikację przez jego strony głównej.

1. W tym samym oknie programu PowerShell Zaimportuj moduł programu Azure AD.

    ```powershell
    Import-Module AzureAD
    ```

2. Zaloguj się do modułu usługi Azure AD jako administrator dzierżawy.

    ```powershell
    Connect-AzureAD
    ```

3. Znajdź aplikację, w oparciu o jego adres URL strony głównej. Adres URL można znaleźć w portalu, przechodząc do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**. W tym przykładzie użyto *sharepoint iddemo*.

    ```powershell
    Get-AzureADApplication | Where-Object { $_.Homepage -like "sharepoint-iddemo" } | Format-List DisplayName, Homepage, ObjectID
    ```

4. Powinna pojawić się wynik podobny do pokazanego poniżej. Skopiuj identyfikator GUID obiektu, do użycia w następnej sekcji.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Zaktualizuj adres URL strony głównej

Utwórz adres URL strony głównej i aktualizowanie aplikacji przy użyciu tej wartości. Kontynuuj korzystanie w tym samym oknie programu PowerShell do uruchamiania tych poleceń. Lub, jeśli używasz nowe okno programu PowerShell Zaloguj się ponownie przy użyciu modułu Azure AD `Connect-AzureAD`. 

1. Upewnij się, ma poprawną aplikacją i Zastąp *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* z identyfikator obiektu, który został skopiowany w poprzedniej sekcji.

    ```powershell
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

   Teraz, gdy zostało potwierdzone aplikacji, możesz przystąpić do aktualizacji strony głównej, w następujący sposób.

2. Utwórz obiekt pusta aplikacja do przechowywania, które chcesz wprowadzić zmiany. Ta zmienna przechowuje wartości, które chcesz zaktualizować. Nic nie jest tworzone w tym kroku.

    ```powershell
    $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
    ```

3. Ustaw adres URL strony głównej na żądaną wartość. Wartość musi być ścieżką poddomeny opublikowanej aplikacji. Na przykład, jeśli zmienisz adres URL strony głównej z `https://sharepoint-iddemo.msappproxy.net/` do `https://sharepoint-iddemo.msappproxy.net/hybrid/`, użytkownicy aplikacji przejść bezpośrednio do niestandardowej strony głównej.

    ```powershell
    $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
    ```

4. Tworzenie aktualizacji przy użyciu GUID (identyfikator obiektu), który został skopiowany w "krok 1: Znajdź identyfikator obiektu aplikacji."

    ```powershell
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```

5. Aby upewnić się, że zmiana zakończyła się pomyślnie, należy ponownie uruchomić aplikację.

    ```powershell
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Wszelkie zmiany wprowadzone do aplikacji może zresetować adres URL strony głównej. Jeśli adres URL strony głównej resetuje, powtórz kroki opisane w tej sekcji, aby ustawić go ponownie.

## <a name="next-steps"></a>Kolejne kroki

- [Zdalny dostęp do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Włącz serwer Proxy aplikacji w witrynie Azure portal](application-proxy-add-on-premises-application.md)