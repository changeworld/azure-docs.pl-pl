---
title: Usługi Azure AD v1 ASP.NET serwera sieci Web wprowadzenie | Dokumentacja firmy Microsoft
description: Implementowanie logowania firmy Microsoft dla rozwiązania ASP.NET z aplikacji opartych na przeglądarce sieci web tradycyjnych przy użyciu standardowego protokołu OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: andret
ms.openlocfilehash: 00fef27529dd1d69c13ceaf22e16d2b7cd47801f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Dodaj logowanie z firmy Microsoft do aplikacji sieci web platformy ASP.NET

W tym przewodniku pokazano, jak do implementowania logowania z firmą Microsoft za pomocą rozwiązania ASP.NET MVC z tradycyjnego aplikacji sieci web opartej na przeglądarce przy użyciu protokołu OpenID Connect. 

Na końcu tego przewodnika, aplikacja będzie akceptować logowania kont służbowych z organizacji, które zostały zintegrowane z usługą Azure Active Directory.

> [!NOTE]
> Ten przewodnik instalacji umożliwia włączenie logowania z kont służbowych w aplikacji ASP.NET. Jeśli interesuje Cię do włączenia logowania dla kont osobistych oprócz kont służbowych, możesz użyć [punktu końcowego v2](../active-directory-v2-compare.md). Zobacz [tej aplikacji platformy ASP.NET instrukcje konfiguracji dla punktu końcowego v2](./active-directory-aspnetwebapp.md) oraz [tego dokumentu](../active-directory-v2-limitations.md) wyjaśniający bieżące ograniczenia v2 punktu końcowego.
<br/><br/>

<!--separator-->

> W tym przewodniku wymaga programu Visual Studio 2015 Update 3 lub Visual Studio 2017 r.  Nie masz?  [Bezpłatnie pobrać Visual Studio 2017 r.](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Jak działa w tym przewodniku

![Jak działa w tym przewodniku](media/active-directory-aspnetwebapp-v1/aspnet-intro.png)

W tym przewodniku opiera się na scenariusz, w których przeglądarką uzyskuje dostęp do witryny sieci web ASP.NET, żądanie użytkownika do uwierzytelniania za pośrednictwem przycisku Zaloguj. W tym scenariuszu większość pracy do renderowania strony sieci web występuje po stronie serwera.

> [!NOTE]
> Ten przewodnik instalacji pokazuje, jak zarejestrować użytkowników na aplikację sieci Web platformy ASP.NET, zaczynając od pustego szablonu i zawierać czynności, takie jak dodanie znaku w przycisk i każdego kontrolera i metody, podczas również wyjaśniający niektóre pojęcia. Alternatywnie można również utworzyć projektu do zarejestrowania się w usłudze Azure Active Directory użytkowników (kont służbowych) za pomocą [szablonu sieci web programu Visual Studio](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) i wybierając *konta organizacyjne* , a następnie jeden możliwości chmury — ta opcja używa szablonem bardziej zaawansowane funkcje, dodatkowe kontrolery, metod i widoków.

## <a name="libraries"></a>Biblioteki

W tym przewodniku używa następujących pakietów:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Oprogramowanie pośredniczące, które włącza aplikację na potrzeby uwierzytelniania OpenIdConnect|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Oprogramowanie pośredniczące, który umożliwia aplikacjom obsługę plików cookie sesji użytkownika|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Umożliwia aplikacji OWIN do uruchomienia na serwerze IIS za pomocą potoku żądania programu ASP.NET|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>Konfigurowanie projektu

W tej sekcji przedstawiono kroki, aby zainstalować i skonfigurować potoku uwierzytelniania za pomocą oprogramowania pośredniczącego OWIN w projekcie ASP.NET przy użyciu protokołu OpenID Connect. 

> Preferowane jest zamiast tego Pobierz ten przykładowy projekt programu Visual Studio? [Pobieranie projektu](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) i przejść [kroku konfiguracji](#configure-your-webconfig-and-register-an-application) skonfigurować przykładowy kod przed wykonaniem.

## <a name="create-your-aspnet-project"></a>Tworzenie projektu platformy ASP.NET
1. W programie Visual Studio: `File` > `New` > `Project`<br/>
2. W obszarze *Visual C# \Web*, wybierz pozycję `ASP.NET Web Application (.NET Framework)`.
3. Nazwa aplikacji, a następnie kliknij przycisk *OK*
4. Wybierz `Empty` , a następnie zaznacz pole wyboru, aby dodać `MVC` odwołań

## <a name="add-authentication-components"></a>Dodaj składniki uwierzytelniania

1. W programie Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Dodaj *pakietów NuGet oprogramowanie pośredniczące OWIN* wpisując następujące polecenie w oknie konsoli Menedżera pakietów:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Informacje te pakiety
>Powyżej biblioteki Włącz logowanie jednokrotne (SSO) przy użyciu protokołu OpenID Connect przy użyciu uwierzytelniania opartego na pliku cookie. Po zakończeniu uwierzytelniania i token reprezentujący użytkownika jest wysyłany do aplikacji, oprogramowanie pośredniczące OWIN tworzy plik cookie sesji. Dlatego użytkownik nie musi ponownie uwierzytelniać i nie jest wymagane nie dodatkowej weryfikacji przeglądarki następnie używa tego pliku cookie dla kolejnych żądań.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurowanie procesu uwierzytelniania
Poniższe kroki są używane do tworzenia oprogramowania pośredniczącego OWIN *Klasa początkowa* można skonfigurować uwierzytelniania OpenID Connect. Ta klasa jest wykonywany automatycznie.

> [!TIP]
> Jeśli projekt nie ma `Startup.cs` pliku w folderze głównym:<br/>
> 1. Kliknij prawym przyciskiem myszy w folderze głównym projektu: >    `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Nadaj jej nazwę `Startup.cs`<br/>
>
>> Upewnij się, że jest wybrana klasa, klasę początkową OWIN i nie C# klasa standardowa. To potwierdzić, sprawdzając występowanie `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` powyżej przestrzeni nazw.


1. Dodaj *OWIN* i *Microsoft.IdentityModel* obszarów nazw do `Startup.cs`:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Klasa startowa. Zastąp następujący kod:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> Parametry podane *OpenIDConnectAuthenticationOptions* służyć jako współrzędnych dla aplikacji do komunikowania się z usługą Azure AD. Ponieważ oprogramowania pośredniczącego protokołu OpenID Connect używa plików cookie, należy skonfigurować plik cookie uwierzytelniania, jak w poprzednim kodzie. *ValidateIssuer* wartość informuje OpenIdConnect nie ograniczyć dostęp do jednej z określonych organizacji.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Dodawanie kontrolera do obsługi żądań logowania i wylogowywania

W tym kroku przedstawiono sposób tworzenia nowego kontrolera do udostępnienia metody logowania i wylogowywania.

1.  Kliknij prawym przyciskiem myszy `Controllers` i wybierz polecenie `Add` > `Controller`
2.  Wybierz pozycję `MVC (.NET version) Controller – Empty`.
3.  Kliknij przycisk *Dodaj*
4.  Nadaj mu nazwę `HomeController` i kliknij przycisk *Dodaj*
5.  Dodaj *OWIN* przestrzeni nazw do klasy:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Dodaj następujące metody umożliwiające obsługę logowania się i wylogowania do kontrolera inicjując żądania uwierzytelnienia za pomocą kodu:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Tworzenie strony głównej aplikacji do logowania użytkowników przy użyciu przycisku logowania

W programie Visual Studio Utwórz nowy widok, aby dodać przycisk Zarejestruj i wyświetlić informacje o użytkowniku po uwierzytelnieniu:

1.  Kliknij prawym przyciskiem myszy `Views\Home` i wybierz polecenie `Add View`
2.  Nadaj jej nazwę `Index`.
3.  Dodaj poniższy kod HTML, który zawiera przycisk Zarejestruj, do pliku:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> Ta strona dodaje przycisk Zarejestruj się w formacie SVG z czarnym tle:<br/>![Logowania z firmą Microsoft](media/active-directory-aspnetwebapp-v1/aspnetsigninbuttonsample.png)<br/> W przypadku więcej logowania przycisków, przejdź do [tej strony](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "znakowanie wytyczne").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Wyświetl oświadczeń użytkownika przez dodawanie kontrolera
Ten kontroler pokazuje zastosowań `[Authorize]` atrybut do ochrony kontrolera. Ten atrybut ogranicza dostęp do kontrolera, zezwalając tylko użytkownicy uwierzytelnieni. Poniższy kod umożliwia użycie atrybutu do wyświetlenia w ramach logowania w oświadczeń użytkowników, które zostały pobrane.

1.  Kliknij prawym przyciskiem myszy `Controllers` folderu: `Add` > `Controller`
2.  Wybierz pozycję `MVC {version} Controller – Empty`.
3.  Kliknij przycisk *Dodaj*
4.  Nadaj jej nazwę `ClaimsController`
5.  Zastąp kod klasy kontrolera następującym kodem — spowoduje to dodanie `[Authorize]` do klasy atrybutu:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Z powodu użycia `[Authorize]` atrybutu, wszystkie metody tego kontrolera można wykonać tylko, jeśli użytkownik jest uwierzytelniony. Jeśli użytkownik nie jest uwierzytelniony i próbuje uzyskać dostęp z kontrolerem, OWIN inicjuje żądanie uwierzytelniania i zmusza użytkownika do uwierzytelniania. Powyższy kod analizuje kolekcję oświadczeń użytkownika określone atrybuty uwzględnione w tokenie użytkownika. Te atrybuty obejmują pełną nazwę użytkownika i nazwę użytkownika, a także temat identyfikator użytkownika globalne. Zawiera także *identyfikator dzierżawcy*, który reprezentuje identyfikator organizacji użytkownika. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Tworzenie widoku do wyświetlenia oświadczenia użytkownika

W programie Visual Studio Utwórz nowy widok, aby wyświetlić oświadczeń użytkownika na stronie sieci web:

1.  Kliknij prawym przyciskiem myszy `Views\Claims` folderu oraz: `Add View`
2.  Nadaj jej nazwę `Index`.
3.  Dodaj poniższy kod HTML do pliku:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Konfigurowanie sieci *web.config* i rejestrowania aplikacji

1. W programie Visual Studio, Dodaj następujący kod do `web.config` (znajdujący się w folderze głównym) w sekcji `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. W Eksploratorze rozwiązań wybierz projekt i przyjrzyj się <i>właściwości</i> okna (Jeśli nie widzisz okna właściwości, naciśnij klawisz F4)
3. Zmiany, aby włączyć protokół SSL <code>True</code>
4. Skopiuj adres URL protokołu SSL projektu do Schowka:<br/><br/>![Właściwości projektu](media/active-directory-aspnetwebapp-v1/visual-studio-project-properties.png)<br />
5. W <code>web.config</code>, Zastąp <code>Enter_the_Redirect_URL_here</code> z adresem URL protokołu SSL projektu 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Zarejestrować aplikację w portalu Azure, a następnie dodaj informacje do *pliku web.config*

1. Przejdź do [portalu Microsoft Azure - rejestracji aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) do rejestrowania aplikacji
2. Wybierz pozycję `New application registration`
3. Wprowadź nazwę aplikacji
4. Wklej projekt programu Visual Studio *adres URL protokołu SSL* w `Sign-on URL` (adres URL jest także dodawane automatycznie do listy adresów URL odpowiedzi dla aplikacji, w przypadku rejestracji)
5. Kliknij przycisk `Create` zarejestrować aplikację. Ta akcja powoduje przejście do listy aplikacji
6. Teraz wyszukiwania i/lub wybierz właśnie utworzony aby otworzyć jego właściwości aplikacji
7. Skopiuj identyfikator guid w obszarze `Application ID` do Schowka
8. Wróć do programu Visual Studio, a w `web.config`, Zastąp `Enter_the_Application_Id_here` wraz z Identyfikatorem aplikacji z aplikacji został zarejestrowany

> [!TIP]
> Jeśli konto zostało skonfigurowane, aby uzyskać dostęp do wielu katalogów, upewnij się, że wybrano prawo katalogu dla organizacji ma aplikacji, które mają być zarejestrowane, klikając nazwę konta w górnym prawym przyciskiem myszy w portalu Azure, a następnie sprawdź Wybrany katalog określonych:<br/>![Wybieranie katalogu prawo](media/active-directory-aspnetwebapp-v1/tenantselector.png)

## <a name="configure-sign-in-options"></a>Skonfiguruj opcje logowania

Można skonfigurować aplikacji umożliwia tylko w przypadku użytkowników, którzy należą do jednej z organizacji wystąpienia usługi Azure Active Directory do logowania, lub zaakceptuj logowania z użytkowników, którzy należą do każdej organizacji. Wykonaj instrukcje z jedną z następujących opcji:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Skonfigurować aplikację tak, aby umożliwić logowania kont służbowych z firmy lub organizacji (wielodostępnej)

Wykonaj następujące kroki, aby zaakceptować logowania kont służbowych z firmy lub organizacji, która ma być zintegrowane z usługą Azure Active Directory. To jest typowym scenariuszem dla *aplikacji SaaS*:

1. Wróć do [portalu Microsoft Azure - rejestracji aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) i zlokalizować aplikacji został zarejestrowany
2. W obszarze `All Settings` wybierz `Properties`
3. Zmień `Multi-tenanted` właściwości `Yes` i kliknij przycisk `Save`

Aby uzyskać więcej informacji na temat tego ustawienia i pojęcia aplikacje wielodostępne, zobacz [w tym artykule](../active-directory-devhowto-multi-tenant-overview.md "omówienie wielodostępnej").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Ogranicz użytkowników z wystąpienia usługi Active Directory tylko jednej z organizacji logować się do aplikacji (pojedynczej dzierżawy)

Ta opcja jest typowym scenariuszem dla *aplikacji biznesowych*: Jeśli chcesz zaakceptować logowania tylko z kont należących do określonego wystąpienia usługi Azure Active Directory (w tym *konta gościa*tego wystąpienia), Zastąp `Tenant` parametru w *web.config* z `Common` o nazwie dzierżawy organizacji — przykład *contoso.onmicrosoft.com*. Po wykonaniu tej zmiany `ValidateIssuer` argumentu w Twojej [ *klasy początkowej OWIN* ](#configure-the-authentication-pipeline) do `true`.

Aby zezwolić użytkownikom tylko listę określonym organizacjom, `ValidateIssuer` na true i użyj `ValidIssuers` parametr, aby określić listę organizacji.

Inną możliwością jest wykonanie implementować niestandardowe metodę do sprawdzania poprawności wystawcy przy użyciu *IssuerValidator* parametru. Aby uzyskać więcej informacji na temat `TokenValidationParameters`, można znaleźć pod adresem [ten artykuł w witrynie MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "artykuł w witrynie MSDN TokenValidationParameters").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](media/active-directory-aspnetwebapp-v1/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->
## <a name="test-your-code"></a>Testowanie kodu

Naciśnij klawisz `F5` do uruchomienia projektu w programie Visual Studio. W przeglądarce zostanie otwarty i kieruje użytkownika do *http://localhost:{port}* której występuje *Zaloguj się przy użyciu Microsoft* przycisku. Przejdź dalej i kliknij go, aby zarejestrować.

Gdy wszystko jest gotowe do testowania, zaloguj się za pomocą konta służbowego (Azure Active Directory). 

![Zaloguj się przy użyciu okna przeglądarki Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin.png)

![Zaloguj się przy użyciu okna przeglądarki Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Oczekiwanych rezultatów
Po zalogowaniu zostanie przekierowany użytkownik do strony głównej witryny sieci web, czyli adres URL HTTPS określony w informacji o rejestracji aplikacji w portalu rejestracji aplikacji firmy Microsoft. Ta strona zawiera obecnie *Hello {użytkownika}* i łącze, aby się wylogować, a łącze, aby wyświetlić oświadczeń użytkownika — czyli łącze do kontrolera autoryzacji utworzony wcześniej.

### <a name="see-users-claims"></a>Zobacz oświadczenia użytkownika
Wybierz, aby zobaczyć oświadczenia użytkownika. Ta akcja prowadzi do kontrolera i widoku, który jest dostępny tylko dla użytkowników, którzy zostali uwierzytelnieni.

#### <a name="expected-results"></a>Oczekiwanych rezultatów
 Powinny pojawić się tabeli zawierającej podstawowe właściwości zalogowanego użytkownika:

| Właściwość | Wartość | Opis|
|---|---|---|
| Name (Nazwa) | {Pełna nazwa użytkownika} | Użytkownik miał na imię i nazwisko
|Nazwa użytkownika | <span>user@domain.com</span>| Nazwa użytkownika używana do identyfikacji zalogowanego użytkownika
| Temat| {Subject}|Ciąg do unikatowego identyfikowania logowania użytkownika sieci web|
| Identyfikator dzierżawy| {Guid}| A *guid* jednoznacznie reprezentujący użytkownika usługi Azure Active Directory organizacji.|

Ponadto zobaczysz tabeli, w tym wszystkie oświadczenia użytkownika dołączana do uwierzytelniania żądań. Lista wszystkich oświadczeń w Token Identyfikatora i ich wyjaśnienia, zobacz [artykułu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "listy oświadczenia w tokenie identyfikator").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Test podczas uzyskiwania dostępu do metody, która ma *[Authorize]* atrybutu (opcjonalnie)
W tym kroku należy przetestować uzyskiwanie dostępu do kontrolera oświadczeń jako użytkownik anonimowy:<br/>
Wybierz łącze do wylogowania użytkownika i ukończyć proces wylogowywania.<br/>
Teraz w przeglądarce, wpisz http://localhost:{port}/claims kontrolerze, który jest chroniony za pomocą dostępu do `[Authorize]` atrybutu

#### <a name="expected-results"></a>Oczekiwanych rezultatów
Powinien zostać wyświetlony monit wymaga uwierzytelniania, aby wyświetlić widok.

## <a name="additional-information"></a>Dodatkowe informacje

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Ochrona całej witryny sieci web
Aby chronić całej witryny sieci web, Dodaj `AuthorizeAttribute` do `GlobalFilters` w `Global.asax` `Application_Start` metody:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->