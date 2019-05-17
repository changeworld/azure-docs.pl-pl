---
title: Ustaw niestandardową stronę główną dla opublikowanych aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące łączników serwera Proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fa5c5638da390f4416afc9f4bd9c5d58c34cea8
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825571"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ustaw niestandardową stronę główną dla opublikowanych aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób skonfigurować aplikację tak, aby Poleć użytkownikowi, aby niestandardową stronę główną, które mogą się różnić w zależności od tego, czy są one wewnętrzne lub zewnętrzne. Podczas publikowania aplikacji za pomocą serwera Proxy aplikacji, ustaw wewnętrzny adres URL, ale czasami nie jest strona którą użytkownik powinien zobaczyć najpierw. Ustaw niestandardową stronę główną, dlatego, że użytkownik otrzyma odpowiednią stronę w przypadku uzyskiwania dostępu do aplikacji. Użytkownik zobaczy niestandardową stronę główną, należy ustawić, niezależnie od tego, czy mogą uzyskać dostęp do aplikacji z usługi Azure Active Directory panelu dostępu lub uruchamianie aplikacji usługi Office 365.

Gdy użytkownik uruchamia aplikację, są one skierowane domyślnie adres URL domeny katalogu głównego dla opublikowanej aplikacji. Strona docelowa jest zwykle ustawiana jako adres URL strony głównej. Aby zdefiniować niestandardową stronę główną adres URL, jeśli chcesz, aby użytkownik aplikacji z zawarciem na określonej stronie w aplikacji, należy użyć modułu Azure AD PowerShell.

Oto jeden scenariusz tłumaczy to, dlaczego firma ustawiał niestandardową stronę główną i dlaczego będzie się różnić w zależności od rodzaju użytkownika:

- Ponieważ inne zasoby (na przykład obrazy), które serwer Proxy aplikacji musi mieć dostęp na najwyższym poziomie w strukturze folderów, publikowanie aplikacji przy użyciu `https://ExpenseApp` jako wewnętrzny adres URL.
- Jednak w sieci firmowej, użytkownik przechodzi do `https://ExpenseApp/login/login.aspx` logować się i uzyskać dostęp do Twojej aplikacji.
- Domyślny adres URL zewnętrznego `https://ExpenseApp-contoso.msappproxy.net`, która nie przyjmuje użytkownika zewnętrznego do strony logowania.
- Jeśli chcesz ustawić `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` jako zewnętrzny adres URL zamiast tego należy więc zewnętrznych użytkownik zobaczy strony logowania najpierw.

>[!NOTE]
>Po zapewnieniu użytkownikom dostępu do opublikowanych aplikacji, aplikacje są wyświetlane w [panelu dostępu usługi Azure AD](../user-help/my-apps-portal-end-user-access.md) i [uruchamianie aplikacji usługi Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed ustawieniem adres URL strony głównej, należy przestrzegać następujących wymagań:

- Należy określić ścieżkę musi być ścieżką poddomenę adresu URL domeny katalogu głównego.

  Na przykład, jeśli adres URL domeny katalogu głównego jest `https://apps.contoso.com/app1/`, musi rozpoczynać się adres URL strony głównej, które konfigurujesz `https://apps.contoso.com/app1/`.

- Jeśli wprowadzisz zmiany w opublikowanej aplikacji, zmiana może zresetować wartość adres URL strony głównej. Po zaktualizowaniu aplikacji w przyszłości, należy przeprowadzić ponowne sprawdzenie i, jeśli to konieczne, zaktualizuj adres URL strony głównej.

Można zmienić stronę główną zewnętrznego lub wewnętrznego w witrynie Azure portal lub przy użyciu programu PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Zmień stronę główną w witrynie Azure portal

Aby zmienić stronę główną wewnętrznych i zewnętrznych aplikacji za pośrednictwem portalu usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/). Zostanie wyświetlony pulpit nawigacyjny, Centrum administracyjnego usługi Azure Active Directory.
2. Na pasku bocznym wybierz **usługi Azure Active Directory**. Zostanie wyświetlona strona Omówienie usługi Azure AD.
3. Na pasku bocznym Przegląd wybierz **rejestracje aplikacji**. Zostanie wyświetlona lista zarejestrowanych aplikacji.
4. Wybierz aplikację z listy. Zostanie wyświetlona strona przedstawiający szczegółowe informacje o zarejestrowanych aplikacji.
5. Wybierz link w obszarze **identyfikatory URI przekierowań**, który wyświetla liczbę identyfikatory URI przekierowania dla sieci web i typów publicznych klienta. Zostanie wyświetlona strona uwierzytelniania dla zarejestrowanych aplikacji.
6. W ostatnim wierszu **identyfikatory URI przekierowań** tabeli, należy ustawić **typu** kolumny **klientem publicznym (mobilnych i klasycznych)**, a następnie w **identyfikator URI PRZEKIEROWANIA**kolumny typu wewnętrznego adresu URL, którego chcesz użyć. Poniżej wiersza, który właśnie został zmodyfikowany, pojawi się nowy pusty wiersz.
7. W nowym wierszu, ustaw **typu** kolumny **sieci Web**, a następnie w **identyfikator URI PRZEKIEROWANIA** kolumny, typ zewnętrzny adres URL, którego chcesz użyć.
8. Aby usunąć wszystkie istniejące wiersze identyfikatora URI przekierowania, należy zaznaczyć **Usuń** ikonę (Kosza) obok każdego wiersza niepożądane.
9. Wybierz pozycję **Zapisz**.

## <a name="change-the-home-page-with-powershell"></a>Zmień stronę główną przy użyciu programu PowerShell

Aby skonfigurować stronę główną aplikację przy użyciu programu PowerShell, należy:

1. Instalowanie modułu Azure AD PowerShell.
2. Znajdź wartość identyfikator obiektu aplikacji.
3. Zaktualizuj adres URL strony głównej aplikacji za pomocą poleceń programu PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instalowanie modułu Azure AD PowerShell

Przed zdefiniowaniem adres URL niestandardowej strony głównej przy użyciu programu PowerShell, należy zainstalować moduł programu PowerShell usługi Azure AD. Możesz pobrać pakiet ze [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), który używa punktu końcowego interfejsu API programu Graph.

Aby zainstalować pakiet, wykonaj następujące kroki:

1. Otwórz okno programu PowerShell standardowe, a następnie uruchom następujące polecenie:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Jeśli używasz polecenia jako użytkownik inny niż administrator, należy użyć `-scope currentuser` opcji.

2. Podczas instalacji należy wybrać **Y** zainstalować dwa pakiety z witryny Nuget.org. Wymagane są oba pakiety.

### <a name="find-the-objectid-of-the-app"></a>Znajdź identyfikator obiektu aplikacji

Otrzymasz identyfikator obiektu aplikacji, wyszukując aplikacji przez jego nazwę wyświetlaną lub strona główna.

1. W tym samym oknie programu PowerShell Zaimportuj moduł programu Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

2. Zaloguj się do modułu usługi Azure AD jako administrator dzierżawy.

   ```powershell
   Connect-AzureAD
   ```

3. Znajdź aplikację. W tym przykładzie użyto programu PowerShell, aby znaleźć identyfikator obiektu, wyszukując aplikacji z nazwą wyświetlaną `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Powinna pojawić się wynik podobny do pokazanego poniżej. Skopiuj identyfikator GUID obiektu, do użycia w następnej sekcji.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternatywnie można po prostu pobierania listy wszystkich aplikacji, listy aplikacji przy użyciu nazwy wyświetlanej określonych lub strona główna wyszukiwania i skopiować identyfikator obiektu aplikacji, po znalezieniu aplikacji.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Zaktualizuj adres URL strony głównej

Utwórz adres URL strony głównej i aktualizacji aplikacji przy użyciu tej wartości. Kontynuuj korzystanie z tego samego okna programu PowerShell lub jeśli używasz nowe okno programu PowerShell Zaloguj się ponownie przy użyciu modułu Azure AD `Connect-AzureAD`. Następnie wykonaj następujące kroki:

1. Utwórz zmienną zawierającą wartość ObjectId skopiowaną w poprzedniej sekcji. (Zastąp wartość identyfikatora ObjectId w tym przykładzie programu SharePoint z wartością identyfikatora ObjectId aplikacji).

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

2. Czy na pewno masz poprawną aplikacją, uruchamiając następujące polecenie. Dane wyjściowe powinny być identyczne z danych wyjściowych widoczny w poprzedniej sekcji ([znaleźć identyfikator obiektu aplikacji](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

3. Utwórz obiekt pusta aplikacja do przechowywania, które chcesz wprowadzić zmiany.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

4. Ustaw adres URL strony głównej na żądaną wartość. Wartość musi być ścieżką poddomeny opublikowanej aplikacji. Na przykład, jeśli zmienisz adres URL strony głównej z `https://sharepoint-iddemo.msappproxy.net/` do `https://sharepoint-iddemo.msappproxy.net/hybrid/`, użytkownicy aplikacji przejść bezpośrednio do niestandardowej strony głównej.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

5. Należy je na stronie głównej.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

6. Aby sprawdzić, czy zmiana zakończyła się pomyślnie, ponownie uruchom następujące polecenie z kroku 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   W naszym przykładzie danych wyjściowych teraz powinien wyglądać w następujący sposób:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

7. Uruchom ponownie aplikację, aby sprawdzić, czy strona główna ma być wyświetlany jako pierwszy ekran, zgodnie z oczekiwaniami.

>[!NOTE]
>Wszelkie zmiany wprowadzone do aplikacji może zresetować adres URL strony głównej. Jeśli adres URL strony głównej resetuje, powtórz kroki opisane w tej sekcji, aby ustawić go ponownie.

## <a name="next-steps"></a>Kolejne kroki

- [Zdalny dostęp do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Samouczek: Dodawanie aplikacji w środowisku lokalnym dostępu zdalnego za pośrednictwem serwera Proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md)