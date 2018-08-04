---
title: Usługa Azure AD w wersji 1 ASP.NET serwera sieci Web wprowadzenie | Dokumentacja firmy Microsoft
description: Implementowanie logowania firmy Microsoft na rozwiązanie platformy ASP.NET z aplikacji opartych na przeglądarce sieci web tradycyjnych przy użyciu standardowego protokołu OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2018
ms.author: andret
ms.openlocfilehash: 2fd41d08df5a8f425a1d59e8cd1172043286d8b7
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39496167"
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Dodawanie logowania z firmą Microsoft do aplikacji sieci web platformy ASP.NET

Ten przewodnik demonstruje sposób implementacji logowania z firmą Microsoft przy użyciu rozwiązania platformy ASP.NET MVC przy użyciu tradycyjnych aplikacji sieci web opartej na przeglądarce przy użyciu protokołu OpenID Connect. 

Na końcu tego przewodnika, aplikacja będzie akceptować logowania oraz do kont służbowych z organizacji, które zostały zintegrowane z usługą Azure Active Directory.

> [!NOTE]
> Ta instalacja z przewodnikiem pomoże Ci włączyć logowania z kont służbowych w aplikacji programu ASP.NET. Jeśli interesuje Cię do włączenia logowania dla osobistych kont oprócz oraz do kont służbowych, możesz użyć [punktu końcowego v2](active-directory-v2-compare.md). Zobacz [tego środowiska ASP.NET instrukcje konfiguracji dla punktu końcowego v2](tutorial-v2-asp-webapp.md) także [w tym dokumencie](active-directory-v2-limitations.md) wyjaśniających bieżących ograniczeń punktu końcowego v2.
<br/><br/>

<!--separator-->

> Ten przewodnik wymaga programu Visual Studio 2015 Update 3 lub Visual Studio 2017.  Nie masz go?  [Pobierz bezpłatnie program Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Jak działa ten przewodnik

![Jak działa ten przewodnik](./media/quickstart-v1-aspnet-webapp/aspnet-intro.png)

Ten przewodnik opiera się na scenariuszu, gdzie przeglądarki uzyskuje dostęp do witryny sieci web ASP.NET, żądanie na uwierzytelnianie za pomocą przycisku logowania. W tym scenariuszu większość pracy do renderowania strony sieci web występuje po stronie serwera.

> [!NOTE]
> Ta instalacja z przewodnikiem pokazuje, jak logować użytkowników na aplikację sieci Web platformy ASP.NET, zaczynając od pustego szablonu i obejmują czynności, takie jak dodanie znaku w przycisku i każdego kontrolera i metody, objaśniając równocześnie, również niektóre pojęcia. Alternatywnie również utworzenie projektu służącego do logowania w usłudze Azure Active Directory użytkowników (kont służbowych) za pomocą [szablonu sieci web programu Visual Studio](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) i wybierając polecenie *kont organizacyjnych* a następnie jednej opcji chmury — ta opcja używa bardziej rozbudowane szablonu, za pomocą metod, widoków i kontrolerów dodatkowe.

## <a name="libraries"></a>Biblioteki

W tym przewodniku używane są następujące pakiety:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Oprogramowanie pośredniczące, który umożliwia aplikacji do uwierzytelniania OpenIdConnect|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Oprogramowania pośredniczącego, które umożliwia aplikacji zachować sesję użytkownika przy użyciu plików cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Umożliwia aplikacji OWIN do uruchamiania w usługach IIS przy użyciu potoku żądania programu ASP.NET|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>Konfigurowanie projektu

W tej sekcji przedstawiono kroki, aby zainstalować i skonfigurować potoku uwierzytelniania za pomocą oprogramowania pośredniczącego OWIN w projekcie ASP.NET przy użyciu protokołu OpenID Connect. 

> Chcesz zamiast tego Pobierz ten przykładowy projekt programu Visual Studio? [Pobieranie projektu](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) , a następnie przejdź do [kroku konfiguracji](#configure-your-webconfig-and-register-an-application) do skonfigurowania przykładowego kodu przed wykonaniem.

## <a name="create-your-aspnet-project"></a>Utwórz projekt platformy ASP.NET
1. W programie Visual Studio: `File` > `New` > `Project`<br/>
2. W obszarze *Visual C# \Web*, wybierz opcję `ASP.NET Web Application (.NET Framework)`.
3. Nadaj nazwę aplikacji, a następnie kliknij przycisk *OK*
4. Wybierz `Empty` a następnie zaznacz pole wyboru, aby dodać `MVC` odwołania

## <a name="add-authentication-components"></a>Dodaj składniki uwierzytelniania

1. W programie Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Dodaj *pakiety NuGet oprogramowania pośredniczącego OWIN* wpisując następujące polecenie w oknie Konsola Menedżera pakietów:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>O tych paloetach
>Powyższe biblioteki Włącz logowanie jednokrotne (SSO) przy użyciu protokołu OpenID Connect za pośrednictwem uwierzytelniania na podstawie plików cookie. Po zakończeniu uwierzytelniania i token reprezentujący użytkownika są wysyłane do aplikacji, oprogramowanie pośredniczące OWIN tworzy plik cookie sesji. Przeglądarki następnie używa tego pliku cookie dla kolejnych żądań, dzięki czemu użytkownik nie musiał ponownie uwierzytelniać i nie dodatkowa weryfikacja jest wymagana.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurowanie potoku uwierzytelniania
Następujące kroki służą do tworzenia pośredniczące OWIN *Klasa początkowa* konfigurowania uwierzytelniania OpenID Connect. Ta klasa jest wykonywana automatycznie.

> [!TIP]
> Jeśli projekt nie ma `Startup.cs` pliku w folderze głównym:<br/>
> 1. Kliknij prawym przyciskiem myszy w folderze głównym projektu: >    `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Nadaj mu nazwę `Startup.cs`<br/>
>
>> Upewnij się, że jest wybrana klasa, klasę początkową OWIN, a nie standardowy klasy C#. To potwierdzić, sprawdzając występowanie `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` powyżej przestrzeni nazw.


1. Dodaj *OWIN* i *Microsoft.IdentityModel* przestrzeni nazw na `Startup.cs`:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Klasa początkowa Zastąp następujący kod:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> Parametry podane *OpenIDConnectAuthenticationOptions* służyć jako współrzędnych dla aplikacji do komunikowania się z usługą Azure AD. Ponieważ oprogramowania pośredniczącego OpenID Connect używa plików cookie, należy skonfigurować uwierzytelniania plików cookie, jak w poprzednim kodzie. *ValidateIssuer* wartość informuje OpenIdConnect nie ograniczyć dostęp do jednej konkretnej organizacji.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Dodaj kontroler do obsługi żądań logowania i wylogowania

W tym kroku przedstawiono sposób tworzenia nowego kontrolera do udostępnienia metody logowania i wylogowywania.

1.  Kliknij prawym przyciskiem myszy `Controllers` i wybierz polecenie `Add` > `Controller`
2.  Wybierz pozycję `MVC (.NET version) Controller – Empty`.
3.  Kliknij przycisk *Dodaj*
4.  Nadaj mu nazwę `HomeController` i kliknij przycisk *Dodaj*
5.  Dodaj *OWIN* przestrzenie nazw do klasy:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Dodaj następujące metody do obsługi logowania i wylogowania do kontrolera, inicjując wezwanie do uwierzytelnienia za pomocą kodu:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Tworzenie strony głównej aplikacji do logowania użytkowników za pomocą przycisku logowania

W programie Visual Studio Utwórz nowy widok, aby dodać przycisk Zarejestruj i wyświetlić informacje o użytkowniku, po uwierzytelnieniu:

1.  Kliknij prawym przyciskiem myszy `Views\Home` i wybierz polecenie `Add View`
2.  Nadaj jej nazwę `Index`.
3.  Dodaj poniższy kod HTML, która zawiera przycisk Zarejestruj, do pliku:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> Ta strona dodaje przycisk logowania w formacie SVG z czarnym tle:<br/>![Zaloguj się przy użyciu programu Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetsigninbuttonsample.png)<br/> Aby uzyskać więcej logowania przyciski, przejdź do [na tej stronie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "wytyczne dotyczące oznaczania marką").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Wyświetlanie oświadczeń użytkownika przez dodawanie kontrolera
Ten kontroler pokazuje zastosowań `[Authorize]` atrybutu, aby chronić kontroler. Ten atrybut ogranicza dostęp do kontrolera, zezwalając tylko uwierzytelnionym użytkownikom. Poniższy kod powoduje, że korzystanie z atrybutu w celu wyświetlenia oświadczeń użytkowników, które zostały pobrane w ramach logowania.

1.  Kliknij prawym przyciskiem myszy `Controllers` folderu: `Add` > `Controller`
2.  Wybierz pozycję `MVC {version} Controller – Empty`.
3.  Kliknij przycisk *Dodaj*
4.  Nadaj mu nazwę `ClaimsController`
5.  Zastąp kod klasy kontrolera następującym kodem — spowoduje to dodanie `[Authorize]` do klasy atrybutu:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Z powodu użycia `[Authorize]` atrybutu, wszystkie metody ten kontroler można wykonać tylko, jeśli użytkownik jest uwierzytelniony. Jeśli użytkownik nie jest uwierzytelniony, próbuje uzyskać dostęp z kontrolerem OWIN inicjuje żądanie uwierzytelniania i zmusza użytkownika do uwierzytelniania. Powyższy kod wygląda w kolekcji oświadczeń użytkownika, pod kątem określonych atrybutów zawarte w tokenie użytkownika. Te atrybuty zawierają pełną nazwę użytkownika i nazwę użytkownika, a także temat identyfikator użytkownika globalnego. Zawiera ona także *identyfikator dzierżawy*, który reprezentuje identyfikator organizacji użytkownika. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Utwórz widok na wyświetlanie oświadczeń użytkownika

W programie Visual Studio Utwórz nowy widok na wyświetlanie oświadczeń użytkownika na stronie sieci web:

1.  Kliknij prawym przyciskiem myszy `Views\Claims` folderu oraz: `Add View`
2.  Nadaj jej nazwę `Index`.
3.  Dodaj poniższy kod HTML do pliku:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Konfigurowanie usługi *web.config* i zarejestrowanie aplikacji

1. W programie Visual Studio, należy dodać następujące polecenie, aby `web.config` (znajdujący się w folderze głównym) w sekcji `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. W Eksploratorze rozwiązań wybierz projekt i przyjrzyj się <i>właściwości</i> okna (Jeśli nie widzisz okna właściwości, naciśnij klawisz F4)
3. Zmiana włączony na protokół SSL <code>True</code>
4. Skopiuj adres URL SSL projektu do Schowka:<br/><br/>![Właściwości projektu](./media/quickstart-v1-aspnet-webapp/visual-studio-project-properties.png)<br />
5. W <code>web.config</code>, Zastąp <code>Enter_the_Redirect_URL_here</code> za pomocą adresu URL SSL projektu 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Zarejestruj swoją aplikację w witrynie Azure Portal, a następnie dodaj informacje do *pliku web.config*

1. Przejdź do [portalu Microsoft Azure — rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) do rejestrowania aplikacji
2. Wybierz pozycję `New application registration`
3. Wprowadź nazwę aplikacji
4. Wklej projektu programu Visual Studio *adresu URL protokołu SSL* w `Sign-on URL` (ten adres URL jest także dodawane automatycznie do listy adresów URL odpowiedzi dla aplikacji, w przypadku rejestracji)
5. Kliknij przycisk `Create` można zarejestrować aplikacji. Ta akcja powoduje przejście do listy aplikacji
6. Teraz wyszukiwanie i/lub wybierz aplikację utworzoną otworzyć jego właściwości
7. Skopiuj identyfikator guid w obszarze `Application ID` do Schowka
8. Wróć do programu Visual Studio, a w `web.config`, Zastąp `Enter_the_Application_Id_here` identyfikatorem aplikacji z poziomu aplikacji, które właśnie zostało zarejestrowane

> [!TIP]
> Jeśli Twoje konto jest skonfigurowane w celu uzyskania dostępu do wielu katalogów, upewnij się, że wybrano odpowiednie katalogu dla organizacji ma aplikacji do zarejestrowania, klikając nazwę konta w prawym górnym prawym przyciskiem myszy w witrynie Azure Portal, a następnie sprawdź Wybrany katalog jak wskazano:<br/>![Zaznaczanie w prawidłowym katalogu](./media/quickstart-v1-aspnet-webapp/tenantselector.png)

## <a name="configure-sign-in-options"></a>Skonfiguruj opcje logowania

Można skonfigurować aplikację, aby zezwolić tylko użytkownicy, którzy należą do jednej z organizacji wystąpienia usługi Azure Active Directory do logowania, lub zaakceptuj logowania użytkowników, które należą do każdej organizacji. Wykonaj poniższe instrukcje jednego z następujących opcji:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Skonfigurować aplikację tak, aby umożliwić logowania oraz do kont służbowych z firmy lub organizacji (z wieloma dzierżawami)

Wykonaj następujące kroki, aby akceptować logowania oraz do kont służbowych z firmy lub organizacji, która została zintegrowana z usługą Azure Active Directory. Jest to typowy scenariusz dla *aplikacji SaaS*:

1. Wróć do [portalu Microsoft Azure — rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) i lokalizowania aplikacji, które właśnie zostało zarejestrowane
2. W obszarze `All Settings` wybierz `Properties`
3. Zmiana `Multi-tenanted` właściwość `Yes` i kliknij przycisk `Save`

Aby uzyskać więcej informacji na temat tego ustawienia i koncepcji wielodostępnych aplikacji, zobacz [w tym artykule](active-directory-devhowto-multi-tenant-overview.md "omówienie wielodostępnych").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Ogranicz użytkowników z wystąpienia usługi Active Directory tylko jednej z organizacji, aby zalogować się do aplikacji (pojedyncza dzierżawa)

Ta opcja jest typowym scenariuszem dla *aplikacje LOB*: Jeśli chcesz, aby Twoja aplikacja będzie akceptować logowania tylko z kont, które należą do konkretnego wystąpienia usługi Azure Active Directory (w tym *konta gościa*danego wystąpienia), Zastąp `Tenant` parametru w *web.config* z `Common` nazwą dzierżawy organizacji — przykład *contoso.onmicrosoft.com*. Następnie należy zmienić `ValidateIssuer` argumentu w swojej [ *Klasa początkowa OWIN* ](#configure-the-authentication-pipeline) do `true`.

Aby umożliwić użytkownikom tylko listę określonym organizacjom, należy ustawić `ValidateIssuer` true i używać `ValidIssuers` parametru, aby określić listę organizacji.

Innym rozwiązaniem jest zaimplementowanie niestandardową metodę do sprawdzania poprawności wystawcy przy użyciu *IssuerValidator* parametru. Aby uzyskać więcej informacji na temat `TokenValidationParameters`, zobacz [w tym artykule MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "artykuł w witrynie MSDN TokenValidationParameters").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](./media/quickstart-v1-aspnet-webapp/vsprojectproperties.png)<br />
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

Naciśnij klawisz `F5` Aby uruchomić projekt w programie Visual Studio. Przeglądarka zostanie otwarta i kieruje użytkownika do *http://localhost:{port}* tam, gdzie zobaczysz *Zaloguj się przy użyciu Microsoft* przycisku. Przejdź dalej i kliknij go, aby zalogować się.

Gdy możesz przystąpić do testowania, zaloguj się za pomocą konta służbowego (Azure Active Directory). 

![Zaloguj się przy użyciu okna przeglądarki Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin.png)

![Zaloguj się przy użyciu okna przeglądarki Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Oczekiwane wyniki
Po zalogowaniu użytkownik jest przekierowany do strony głównej witryny sieci web, czyli adres URL HTTPS, określone w aplikacji informacje rejestracyjne w portalu rejestracji aplikacji firmy Microsoft. Ta strona zawiera obecnie *Witaj {User}* i łącze, aby się wylogować, a łącze, aby wyświetlić oświadczenia użytkownika — czyli łącze do kontrolera Autoryzuj utworzone wcześniej.

### <a name="see-users-claims"></a>Zobacz oświadczeń użytkownika
Wybierz hiperlink, aby zobaczyć oświadczenia użytkownika. Ta akcja prowadzi do kontrolera i widoku, który jest dostępny tylko dla użytkowników, którzy są uwierzytelnieni.

#### <a name="expected-results"></a>Oczekiwane wyniki
 Powinny zostać wyświetlone tabeli zawierającej podstawowe właściwości zalogowanego użytkownika:

| Właściwość | Wartość | Opis|
|---|---|---|
| Name (Nazwa) | {Pełna nazwa użytkownika} | Użytkownik na imię i nazwisko
|Nazwa użytkownika | <span>user@domain.com</span>| Nazwa użytkownika służąca do identyfikowania zalogowanego użytkownika
| Temat| {Subject}|Ciąg do unikatowego identyfikowania logowania użytkownika w sieci Web|
| Identyfikator dzierżawy| {Guid}| A *guid* jednoznacznie reprezentujący użytkownika usługi Azure Active Directory organizacji.|

Ponadto zobaczysz tabelę, w tym wszystkie oświadczenia użytkownika dołączana do żądań uwierzytelnienia. Aby uzyskać listę wszystkich oświadczeń w ich wyjaśnienie i tokenu Identyfikatora, zobacz ten [artykułu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "listy oświadczenia w tokenie identyfikator").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Test, uzyskiwanie dostępu do metody, która ma *[Authorize]* atrybutu (opcjonalnie)
W tym kroku testu, uzyskiwanie dostępu do kontrolera oświadczeń jako użytkownik anonimowy:<br/>
Wybierz link umożliwiający wylogowanie użytkownika i ukończyć proces wylogowywania.<br/>
Teraz w przeglądarce, należy wpisać http://localhost:{port}/claims do dostępu do sieci kontroler, który jest chroniony za pomocą `[Authorize]` atrybutu

#### <a name="expected-results"></a>Oczekiwane wyniki
Powinien zostać wyświetlony monit, wymagając od użytkownika do uwierzytelniania, aby wyświetlić widok.

## <a name="additional-information"></a>Dodatkowe informacje

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Ochrona całej witryny sieci web
Aby chronić całej witryny sieci web, należy dodać `AuthorizeAttribute` do `GlobalFilters` w `Global.asax` `Application_Start` metody:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->