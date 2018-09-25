---
title: Usługa Azure AD platformy Xamarin wprowadzenie | Dokumentacja firmy Microsoft
description: Twórz aplikacje platformy Xamarin, które Integracja z usługą Azure AD, logowania i wywoływać chroniony przez usługi AD interfejsy API usługi Azure przy użyciu protokołu OAuth.
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
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8af6846da78d12460b7866297c9802c5dab20a69
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967527"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Szybki Start: Tworzenie aplikacji platformy Xamarin, która integruje się logowania firmy Microsoft

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Za pomocą platformy Xamarin można napisać aplikacji mobilnych w języku C#, który można uruchomić na systemów iOS, Android i Windows (urządzeń przenośnych i komputerów). Jeśli tworzysz aplikację przy użyciu platformy Xamarin usługi Azure Active Directory (Azure AD) ułatwia uwierzytelnianie użytkowników za pomocą swoich kont usługi Azure AD. Aplikacji mogą wykorzystywać jednocześnie możliwość bezpiecznego wszelkie internetowy interfejs API jest chroniony przez usługę Azure AD, takich jak interfejsy API usługi Office 365 lub interfejsu API platformy Azure.

W przypadku aplikacji platformy Xamarin, które muszą uzyskać dostęp do chronionych zasobów usługi Azure AD zapewnia Active Directory Authentication Library (ADAL). Wyłącznie do celów ADAL jest ułatwiają aplikacjom uzyskiwanie tokenów dostępu. Aby zademonstrować, jak łatwo jest, w tym artykule pokazano, jak tworzyć aplikacje DirectorySearcher który:

* Uruchom w systemach iOS, Android, Windows Desktop, Windows Phone i Windows Store.
* Z jednej przenośnej biblioteki klas (PCL) umożliwia uwierzytelnianie użytkowników i uzyskiwanie tokenów dla interfejsu API programu Graph usługi Azure AD.
* Wyszukaj katalog dla użytkowników za pomocą podanej nazwy UPN.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz [szkielet projektu](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), lub pobrać [ukończone przykładowe](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Każdego pobrania to rozwiązanie programu Visual Studio 2013.
* Należy również dzierżawy usługi Azure AD w taki sposób, w której chcesz utworzyć użytkowników i rejestrowania aplikacji. Jeśli nie masz jeszcze dzierżawy, [Dowiedz się, jak je](quickstart-create-new-tenant.md).

Gdy wszystko będzie gotowe, należy wykonać procedury opisane w następnych czterech sekcjach.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Krok 1: Konfigurowanie środowiska deweloperskiego platformy Xamarin

Ten samouczek zawiera projekty dla systemów iOS, Android i Windows, potrzebujesz programu Visual Studio i Xamarin. Aby utworzyć środowisko to konieczne, zakończyć ten proces w [Ustaw Konfigurowanie i instalowanie programu Visual Studio i Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) w witrynie MSDN. Instrukcje zawierają materiału, który można sprawdzić na poszerzenie wiedzy na temat platformy Xamarin w trakcie oczekiwania w przypadku instalacji należy wykonać.

Po zakończeniu instalacji, otwórz rozwiązanie w programie Visual Studio. Znajdziesz sześć projektów: pięć projektów specyficznych dla platformy i PCL jeden, DirectorySearcher.cs, które będą udostępniane na wszystkich platformach.

## <a name="step-2-register-the-directorysearcher-app"></a>Krok 2: Rejestrowanie aplikacji DirectorySearcher

Aby włączyć aplikację, aby uzyskać tokenów, należy najpierw zarejestrować ją w dzierżawie usługi Azure AD i przyznać jej uprawnienia dostępu do interfejsu API programu Graph usługi Azure AD. Oto kroki tej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku kliknij swoje konto. Następnie w obszarze **katalogu** wybierz dzierżawę usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Kliknij przycisk **wszystkich usług** w okienku po lewej stronie, a następnie wybierz **usługi Azure Active Directory**.
4. Kliknij przycisk **rejestracje aplikacji**, a następnie wybierz pozycję **Dodaj**.
5. Aby utworzyć nowy **natywną aplikację kliencką**, postępuj zgodnie z monitami.
  * **Nazwa** opisuje aplikacji dla użytkowników.
  * **Identyfikator URI przekierowania** jest połączeniem schemat i parametry, które usługa Azure AD używa do zwracania odpowiedzi tokenu. Wprowadź wartość (na przykład http://DirectorySearcher).
6. Po zakończeniu rejestracji usługi Azure AD przypisze aplikacji Unikatowy identyfikator aplikacji. Skopiuj wartości z **aplikacji** karcie, ponieważ będzie on potrzebny później.
7. Na **ustawienia** wybierz opcję **wymagane uprawnienia**, a następnie wybierz pozycję **Dodaj**.
8. Wybierz **programu Microsoft Graph** jako interfejs API. W obszarze **delegowane uprawnienia**, Dodaj **Czytaj dane katalogu** uprawnień. Ta akcja umożliwia aplikacji do wykonywania zapytań interfejsu API programu Graph dla użytkowników.

## <a name="step-3-install-and-configure-adal"></a>Krok 3. Instalowanie i konfigurowanie biblioteki ADAL

Teraz, gdy masz aplikację w usłudze Azure AD, można zainstalować biblioteki ADAL i pisanie kodu związanych z tożsamościami. Aby włączyć biblioteki ADAL w celu komunikowania się z usługą Azure AD, nadaj pewne informacje o rejestracji aplikacji.

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

    Należy pamiętać, że dwa odwołania do biblioteki zostały dodane do każdego projektu: PCL część biblioteki ADAL i część specyficzne dla platformy.
2. W projekcie DirectorySearcherLib Otwórz DirectorySearcher.cs.
3. Zastąp wartości elementów członkowskich klasy z wartościami, które zostały wprowadzone w witrynie Azure portal. Twój kod odwołuje się do tych wartości, zawsze wtedy, gdy używa biblioteki ADAL.

  * *Dzierżawy* jest domena dzierżawy usługi Azure AD (np. contoso.onmicrosoft.com).
  * *ClientId* jest identyfikator klienta aplikacji, który został skopiowany z portalu.
  * *ReturnUri* jest przekierowania URI, które wprowadziłeś w portalu (na przykład http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Krok 4: Użyj biblioteki ADAL w celu uzyskania tokenów z usługi Azure AD

Prawie wszystkie logiki uwierzytelniania aplikacji znajduje się w `DirectorySearcher.SearchByAlias(...)`. Wszystko, co jest konieczne w projektach specyficzne dla platformy służy do przekazywania parametru kontekstowego `DirectorySearcher` PCL.

1. Otwórz DirectorySearcher.cs, a następnie dodaj nowy parametr w celu `SearchByAlias(...)` metody. `IPlatformParameters` jest kontekstowe parametr, który hermetyzuje obiekty specyficzne dla platformy, których potrzebuje biblioteki ADAL w celu przeprowadzenia uwierzytelniania.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inicjowanie `AuthenticationContext`, czyli klasy podstawowej biblioteki adal. Ta akcja przekazuje ADAL współrzędne potrzebuje do komunikacji z usługą Azure AD.
3. Wywołaj `AcquireTokenAsync(...)`, który akceptuje `IPlatformParameters` obiektu, a następnie wywołuje przepływ uwierzytelniania, które są niezbędne do zwracania tokenu do aplikacji.

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

    `AcquireTokenAsync(...)` najpierw próbuje zwrócił tokenu dla żądanego zasobu (interfejsu API programu Graph w tym przypadku) bez monitowania użytkowników o wprowadzenie poświadczeń (przy użyciu pamięci podręcznej lub odświeżanie tokenów starej). Zgodnie z potrzebami diagram przedstawia użytkowników na stronę logowania usługi Azure AD przed uzyskanie żądanego tokenu.
4. Dołącz token dostępu do żądania interfejsu API programu Graph w **autoryzacji** nagłówka:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

To wszystko dla `DirectorySearcher` PCL i aplikacji firmy dotyczącymi tożsamości kodu. Pozostaje tylko do wywołania `SearchByAlias(...)` metody w widokach każdej platformy i w razie potrzeby dodać kod poprawnie obsługi cyklu życia interfejsu użytkownika.

### <a name="android"></a>Android
1. W MainActivity.cs, dodaj wywołanie `SearchByAlias(...)` przycisku kliknij program obsługi:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Zastąp `OnActivityResult` cyklu życia metodę, aby przekazywać dowolne uwierzytelnianie wykonuje przekierowanie do odpowiedniej metody. Biblioteka ADAL zapewnia metody pomocnika dla tego w systemie Android:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows Desktop

W MainWindow.xaml.cs, wywoływania `SearchByAlias(...)` , przekazując `WindowInteropHelper` na pulpicie `PlatformParameters` obiektu:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
W DirSearchClient_iOSViewController.cs, ustawień systemu iOS `PlatformParameters` obiektu przyjmuje odwołanie do kontrolera widoku:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Aplikacje uniwersalne systemu Windows
W Windows Universal Otwórz MainPage.xaml.cs, a następnie wdrożyć `Search` metody. Ta metoda używa metody pomocnika w udostępnionym projekcie można zaktualizować interfejsu użytkownika, zgodnie z potrzebami.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Masz teraz działającą aplikację Xamarin, która może uwierzytelniać użytkowników i bezpiecznie wywoływać interfejsy API sieci web przy użyciu protokołu OAuth 2.0 na pięć różnych platformach.

## <a name="step-5-populate-your-tenant"></a>Krok 5: Wypełnij dzierżawy 

Jeśli nie została już wypełniona dzierżawy z użytkownikami, nadszedł czas, aby to zrobić.

1. Uruchom aplikację DirectorySearcher, a następnie zaloguj się przy użyciu jednego z użytkowników.
2. Wyszukiwać innych użytkowników, w oparciu o ich nazwy UPN.

## <a name="next-steps"></a>Kolejne kroki

Biblioteki ADAL można łatwo zintegrować popularne funkcje tożsamości aplikacji. Ta odpowiada za całą pracę dirty, takich jak zarządzanie pamięcią podręczną, obsługa protokołu OAuth, prezentowanie użytkownika z logowaniem użytkownika i odświeżanie wygasła tokenów. Musisz znać tylko jednego wywołania interfejsu API, `authContext.AcquireToken*(…)`.

* Pobierz [ukończone przykładowe](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (bez wartości konfiguracji).
* Dowiedz się, jak [zabezpieczanie .NET interfejsu Web API z usługą Azure AD](quickstart-v1-dotnet-webapi.md).
