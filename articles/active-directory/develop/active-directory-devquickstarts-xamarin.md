---
title: Azure AD platformy Xamarin wprowadzenie | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji platformy Xamarin integracji z usługą Azure AD, logowania i wywoływanie Azure API chronione przez usługi AD w trybie OAuth.
services: active-directory
documentationcenter: xamarin
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1ac04cddc00bf76bb366a249a5a2ec4c56d5212c
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-xamarin-getting-started"></a>Azure AD platformy Xamarin wprowadzenie
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Za pomocą platformy Xamarin można napisać aplikacji mobilnych w języku C#, który można uruchomić w systemach iOS, Android i Windows (urządzeń przenośnych i komputerów). Jeśli tworzysz aplikację za pomocą platformy Xamarin usługi Azure Active Directory (Azure AD) ułatwia uwierzytelnianie użytkowników przy użyciu ich kont usługi Azure AD. Aplikacja również bezpiecznie może używać dowolnego interfejsu API sieci web chronionej przez usługę Azure AD, takie jak interfejsami API usługi Office 365 lub interfejsu API usługi Azure.

W przypadku aplikacji platformy Xamarin, które wymagają dostępu do chronionych zasobów usługi Azure AD zapewnia Active Directory Authentication Library (ADAL). Wyłącznie do celów ADAL jest ułatwiają aplikacji uzyskać tokeny dostępu. Aby zademonstrować, jak łatwo, w tym artykule pokazano, jak tworzyć aplikacje DirectorySearcher który:

* Uruchom na systemu iOS, Android, Windows Desktop, Windows Phone i Sklep Windows.
* Użyj pojedynczego przenośnej biblioteki klas (PCL) do uwierzytelniania użytkowników i uzyskać tokenów dla interfejsu API programu Azure AD Graph.
* Wyszukaj katalog dla użytkowników z danym głównej nazwy użytkownika.

## <a name="before-you-get-started"></a>Przed rozpoczęciem
* Pobierz [szkielet projektu](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), lub pobrać [ukończone próbki](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Każdego pobrania to rozwiązanie Visual Studio 2013.
* Należy również dzierżawa usługi Azure AD, w którym do tworzenia użytkowników i rejestrowania aplikacji. Jeśli nie masz już dzierżawę, [Dowiedz się, jak kupić](active-directory-howto-tenant.md).

Gdy wszystko będzie gotowe, wykonaj procedury opisane w obok cztery sekcje.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Krok 1: Konfigurowanie środowiska deweloperskiego Xamarin
Ten samouczek zawiera projektów dla systemu iOS, Android i Windows, należy Xamarin i Visual Studio. Aby utworzyć środowisko konieczne, zakończyć proces w [ustawić Konfigurowanie i instalowanie programu Visual Studio i Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) w witrynie MSDN. Instrukcje zawierają materiału, który można przejrzeć szczegółowe informacje dotyczące Xamarin podczas oczekiwania dla instalacji należy wykonać.

Po zakończeniu instalacji, otwórz rozwiązanie w programie Visual Studio. Istnieje, można znaleźć projektów sześciu: pięć projektów specyficzne dla platformy i jeden PCL, DirectorySearcher.cs, które będą udostępniane na wszystkich platformach.

## <a name="step-2-register-the-directorysearcher-app"></a>Krok 2: Rejestrowanie aplikacji DirectorySearcher
Aby umożliwić aplikacji można uzyskać tokeny, należy najpierw zarejestrować ją w dzierżawie usługi Azure AD i udzielić jej uprawnienia dostępu do interfejsu API programu Azure AD Graph. Oto kroki tej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku kliknij konto. Następnie w obszarze **katalogu** wybierz dzierżawy usługi Active Directory, które chcesz zarejestrować aplikację.
3. Kliknij przycisk **wszystkie usługi** w okienku po lewej stronie, a następnie wybierz **usługi Azure Active Directory**.
4. Kliknij przycisk **rejestracji aplikacji**, a następnie wybierz **Dodaj**.
5. Aby utworzyć nową **natywną aplikację kliencką**, postępuj zgodnie z monitami.
  * **Nazwa** opisuje aplikacji dla użytkowników.
  * **Identyfikator URI przekierowania** jest kombinacją schemat i ciąg, korzystającą z usługi Azure AD w celu zwracać odpowiedzi tokenu. Wprowadź wartość (na przykład http://DirectorySearcher).
6. Po zakończeniu rejestracji usługi Azure AD przypisuje aplikacji identyfikatora aplikacji Skopiuj wartości z **aplikacji** karcie, ponieważ będzie on potrzebny później.
7. Na **ustawienia** wybierz pozycję **wymagane uprawnienia**, a następnie wybierz **Dodaj**.
8. Wybierz **Microsoft Graph** jako interfejsu API. W obszarze **delegowane uprawnienia**, Dodaj **Czytaj dane katalogu** uprawnienia. Ta akcja umożliwia aplikacji zapytania interfejsu API programu Graph dla użytkowników.

## <a name="step-3-install-and-configure-adal"></a>Krok 3: Instalowanie i konfigurowanie biblioteki ADAL
Teraz, gdy masz aplikację w usłudze Azure AD, można zainstalować biblioteki ADAL i wpisz swój kod dotyczące tożsamości. Aby włączyć biblioteki ADAL do komunikowania się z usługą Azure AD, nadaj mu pewnych informacji o rejestracji aplikacji.

1. Dodawanie biblioteki ADAL do projektu DirectorySearcher przy użyciu konsoli Menedżera pakietów.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Należy pamiętać, że dwa odwołania biblioteki są dodawane do każdego projektu: część PCL ADAL i części specyficzne dla platformy.
2. W projekcie DirectorySearcherLib Otwórz DirectorySearcher.cs.
3. Zastąp wartości elementu członkowskiego klasy wartości, które zostały wprowadzone w portalu Azure. Kod odwołuje się do tych wartości, przy każdym użyciu biblioteki ADAL.

  * *Dzierżawy* jest domeny dzierżawy usługi Azure AD (np. contoso.onmicrosoft.com).
  * *ClientId* jest identyfikator klienta aplikacji, który został skopiowany z portalu.
  * *ReturnUri* jest przekierowanie URI, który został wprowadzony w portalu (na przykład http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Krok 4: Użyj biblioteki ADAL w celu uzyskania tokenów z usługi Azure AD
Prawie wszystkie logika uwierzytelniania aplikacji znajduje się `DirectorySearcher.SearchByAlias(...)`. Wszystko, co jest niezbędne w projektach specyficzne dla platformy jest przekazanie parametr kontekstowy `DirectorySearcher` PCL.

1. Otwórz DirectorySearcher.cs, a następnie dodaj nowy parametr `SearchByAlias(...)` metody. `IPlatformParameters` to kontekstowych parametr, który hermetyzuje obiekty specyficzne dla platformy, które musi wykonać uwierzytelnianie ADAL.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inicjowanie `AuthenticationContext`, która jest klasy podstawowej biblioteki adal. Ta akcja przekazuje ADAL współrzędne potrzebuje do komunikacji z usługą Azure AD.
3. Wywołanie `AcquireTokenAsync(...)`, który akceptuje `IPlatformParameters` obiektu i wywołuje przepływ uwierzytelniania, które są niezbędne zwrócić token do aplikacji.

    ```csharp
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` najpierw próbuje zwrócić token dla żądanego zasobu (interfejsu API programu Graph w tym przypadku) bez monitowania użytkowników o wprowadzenie poświadczeń (za pośrednictwem buforowanie lub odświeżanie starego tokeny). W razie potrzeby pokazywane są użytkownicy strony logowania usługi Azure AD przed Uzyskiwanie żądanego tokenu.
4. Dołącz token dostępu do żądania interfejsu API programu Graph w **autoryzacji** nagłówka:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

To wszystko dla `DirectorySearcher` PCL i aplikacji na powiązanych tożsamości kodu. Wszystkie opcje, które pozostaje jest wywołanie `SearchByAlias(...)` metody w widokach każdej platformy i w razie potrzeby dodać kod poprawnie obsługi cyklu życia interfejsu użytkownika.

### <a name="android"></a>Android
1. W MainActivity.cs, dodaj wywołanie do `SearchByAlias(...)` przycisku kliknij program obsługi:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Zastąpienie `OnActivityResult` metody cyklu życia do przekazywania uwierzytelniania przekierowuje do odpowiedniej metody. Biblioteka ADAL udostępnia metodę pomocnika dla tego w systemie Android:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>System Windows Desktop
W MainWindow.xaml.cs, wywoływania `SearchByAlias(...)` przez przekazanie `WindowInteropHelper` na pulpicie `PlatformParameters` obiektu:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
W DirSearchClient_iOSViewController.cs, iOS `PlatformParameters` obiektu przyjmuje odwołanie do kontrolera widoku:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Aplikacje uniwersalne systemu Windows
W uniwersalnych systemu Windows otwórz MainPage.xaml.cs, a następnie wdrożyć to rozwiązanie `Search` metody. Ta metoda używa metody pomocnika w projekcie udostępnionym można zaktualizować interfejsu użytkownika w razie potrzeby.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>Co dalej
Masz teraz pracy aplikacji platformy Xamarin, który może uwierzytelniać użytkowników i bezpiecznie wywoływać interfejsy API sieci web przy użyciu protokołu OAuth 2.0 na pięciu różnych platformach.

Jeśli nie zostało już wypełnione dzierżawy z użytkownikami, należy teraz Aby to zrobić.

1. Uruchom aplikację DirectorySearcher, a następnie zaloguj się przy użyciu jednego z użytkowników.
2. Wyszukiwać innych użytkowników, w oparciu o ich nazwy UPN.

ADAL można łatwo zastosować wspólne funkcje tożsamości w aplikacji. Go odpowiedzialna za całą pracę dirty, takich jak zarządzanie pamięci podręcznej, obsługa protokołu OAuth, przedstawiający użytkownika za pomocą interfejsu użytkownika, nazwy logowania i odświeżanie wygasła tokenów. Należy znać tylko jednego wywołania interfejsu API, `authContext.AcquireToken*(…)`.

Odwołania, Pobierz [ukończone próbki](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (bez wartości konfiguracji).

Możesz teraz przejść do tożsamości dodatkowe scenariusze. Na przykład, spróbuj [zabezpieczyć interfejs API sieci Web .NET z usługą Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
