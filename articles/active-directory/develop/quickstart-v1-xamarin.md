---
title: Usługa Azure AD i platforma Xamarin — wprowadzenie | Microsoft Docs
description: Twórz aplikacje platformy Xamarin, które integrują się z usługą Azure AD na potrzeby logowania i wywołują chronione przez usługę Azure AD interfejsy API przy użyciu protokołu OAuth.
services: active-directory
documentationcenter: xamarin
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdb3de88fde2fbc7ec2aaffdb7d82433845db34b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545944"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Szybki start: Tworzenie aplikacji Xamarin integrującej logowanie firmy Microsoft

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Za pomocą platformy Xamarin możesz pisać aplikacje mobilne w języku C#, które mogą być uruchamiane w systemach iOS, Android i Windows (na urządzeniach przenośnych i komputerach). Jeśli tworzysz aplikację przy użyciu platformy Xamarin, usługa Azure Active Directory (Azure AD) ułatwia uwierzytelnianie użytkowników za pomocą ich kont usługi Azure AD. Aplikacja może bezpiecznie wykorzystywać dowolny internetowy interfejs API chroniony przez usługę Azure AD, na przykład interfejsy API usługi Office 365 lub interfejs API platformy Azure.

Dla aplikacji platformy Xamarin potrzebujących dostępu do chronionych zasobów usługa Azure Active Directory (Azure AD) udostępnia bibliotekę Active Directory Authentication Library (ADAL). Ta biblioteka służy wyłącznie do tego, aby ułatwić aplikacjom uzyskanie tokenów dostępu. Aby zademonstrować, jakie to łatwe, w tym artykule pokazano sposób tworzenia aplikacji DirectorySearcher które:

* są uruchamiane w systemach iOS, Android, Windows Desktop i Windows Phone oraz w Sklepie Windows;
* używają jednej biblioteki klas przenośnych (PCL) do uwierzytelniania użytkowników i uzyskiwania tokenów dla interfejsu API programu Graph usługi Azure AD;
* wyszukują w katalogu użytkowników o określonej nazwie UPN.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobranie [szkieletu projektu](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) lub [pełnego przykładu](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Każdy plik do pobrania to rozwiązanie programu Visual Studio 2013.
* Potrzebna jest także dzierżawa usługi Azure AD, w której można utworzyć użytkowników i zarejestrować aplikację. Jeśli nie masz jeszcze dzierżawy, [dowiedz się, jak ją uzyskać](quickstart-create-new-tenant.md).

Gdy wszystko będzie gotowe, wykonaj procedury opisane w następnych czterech sekcjach.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Krok 1: Konfigurowanie środowiska projektowego platformy Xamarin

Ponieważ ten samouczek zawiera projekty dla systemów iOS, Android i Windows, potrzebujesz programu Visual Studio i platformy Xamarin. Aby utworzyć niezbędne środowisko, wykonaj proces opisany na stronie [Set up and install Visual Studio and Xamarin (Konfigurowanie i instalowanie programów Visual Studio i Xamarin)](https://msdn.microsoft.com/library/mt613162.aspx) w witrynie MSDN. Instrukcje obejmują materiały, które można przejrzeć, aby dowiedzieć się więcej o platformie Xamarin w trakcie oczekiwania na ukończenie instalacji.

Po zakończeniu instalacji otwórz rozwiązanie w programie Visual Studio. Znajdziesz tutaj sześć projektów: pięć projektów specyficznych dla platformy i jedną bibliotekę PCL, DirectorySearcher.cs, która będzie współużytkowana przez wszystkie platformy.

## <a name="step-2-register-the-directorysearcher-app"></a>Krok 2: Rejestrowanie aplikacji DirectorySearcher

Aby umożliwić aplikacji uzyskiwanie tokenów, musisz najpierw zarejestrować ją w dzierżawie usługi Azure AD i przyznać jej uprawnienia dostępu do interfejsu API programu Graph usługi Azure AD. Oto kroki tej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku kliknij swoje konto. Następnie z listy **Katalog** wybierz dzierżawę usługi Active Directory, w której chcesz zarejestrować aplikację.
3. Kliknij pozycję **Wszystkie usługi** w okienku po lewej stronie, a następnie wybierz pozycję **Azure Active Directory**.
4. Kliknij przycisk **Rejestracje aplikacji**, a następnie wybierz pozycję **Dodaj**.
5. Aby utworzyć nową **natywną** aplikację kliencką, postępuj zgodnie z monitami.
   * **Nazwa** opisuje aplikację użytkownikom.
   * **Identyfikator URI przekierowania** jest połączeniem schematu i ciągu, przy użyciu którego usługa Azure AD zwraca odpowiedzi tokenów. Wprowadź wartość (na przykład `http://DirectorySearcher`).
6. Gdy zakończysz rejestrację, usługa Azure AD przypisze aplikacji unikatowy identyfikator. Skopiuj wartość z karty **Aplikacje**, ponieważ będzie ona potrzebna później.
7. Na stronie **Ustawienia** wybierz pozycję **Wymagane uprawnienia**, a następnie wybierz pozycję **Dodaj**.
8. Wybierz **Microsoft Graph** jako interfejs API. W obszarze **Uprawnienia delegowane** dodaj uprawnienie **Odczytuj dane katalogu**. 
   Ta akcja umożliwia aplikacji wykonywanie zapytań względem interfejsu API programu Graph dotyczących użytkowników.

## <a name="step-3-install-and-configure-adal"></a>Krok 3: Instalowanie i konfigurowanie biblioteki ADAL

Gdy masz już aplikację w usłudze Azure AD, możesz zainstalować bibliotekę ADAL i napisać kod dotyczący tożsamości. Aby biblioteka ADAL mogła komunikować się z usługą Azure AD, podaj niektóre informacje dotyczące rejestracji aplikacji.

1. Dodaj bibliotekę ADAL do projektu DirectorySearcher przy użyciu konsoli menedżera pakietów.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    ```

    Zwróć uwagę, że do każdego projektu zostały dodane dwa odwołania do bibliotek: część PCL biblioteki ADAL i część specyficzna dla platformy.
2. W projekcie DirectorySearcherLib otwórz plik DirectorySearcher.cs.
3. Zastąp wartości elementów członkowskich klasy wartościami, które zostały wprowadzone w witrynie Azure Portal. Twój kod odwołuje się do tych wartości podczas każdego użycia biblioteki ADAL.

   * *tenant* to domena Twojej dzierżawy usługi Azure AD (na przykład contoso.onmicrosoft.com).
   * *clientId* to identyfikator klienta aplikacji skopiowany z portalu.
   * *ReturnUri* jest przekierowania URI, które wprowadziłeś w portalu (na przykład `http://DirectorySearcher`).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Krok 4: Uzyskiwanie tokenów z usługi Azure AD przy użyciu biblioteki ADAL

Prawie cała logika uwierzytelniania aplikacji znajduje się w elemencie `DirectorySearcher.SearchByAlias(...)`. Wszystko, co trzeba zrobić w projektach specyficznych dla platformy, to przekazać parametr kontekstowy do biblioteki PCL `DirectorySearcher`.

1. Otwórz plik DirectorySearcher.cs, a następnie dodaj nowy parametr do metody `SearchByAlias(...)`. `IPlatformParameters` jest kontekstowym parametrem, który hermetyzuje obiekty specyficzne dla platformy potrzebne bibliotece ADAL do przeprowadzenia uwierzytelniania.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Zainicjuj klasę `AuthenticationContext`, czyli klasę podstawową biblioteki ADAL. Ta akcja powoduje przekazanie bibliotece ADAL wszystkich współrzędnych, których potrzebuje ona do komunikacji z usługą Azure AD.
3. Wywołaj metodę `AcquireTokenAsync(...)`, która akceptuje obiekt `IPlatformParameters`, a następnie wywołuje przepływ uwierzytelniania, niezbędny w celu zwrócenia tokenu do aplikacji.

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

    Metoda `AcquireTokenAsync(...)` najpierw próbuje zwrócić token dla żądanego zasobu (w tym przypadku interfejsu API programu Graph) bez monitowania użytkowników o wprowadzenie poświadczeń (przy użyciu pamięci podręcznej lub odświeżania starych tokenów). W razie potrzeby wyświetla użytkownikom stronę logowania usługi Azure AD przed uzyskaniem żądanego tokenu.
4. Dołącz token dostępu do żądania interfejsu API programu Graph w nagłówku **autoryzacji**:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

To wszystko, jeśli chodzi o PCL `DirectorySearcher` i kod dotyczący tożsamości aplikacji. Pozostaje tylko wywołać metodę `SearchByAlias(...)` w widokach każdej platformy i w razie potrzeby dodać kod w celu poprawnej obsługi cyklu życia interfejsu użytkownika.

### <a name="android"></a>Android
1. W pliku MainActivity.cs dodaj wywołanie metody `SearchByAlias(...)` w procedurze obsługi kliknięcia przycisku:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Zastąp metodę cyklu życia `OnActivityResult`, aby przekazywać dowolne przekierowania uwierzytelniania z powrotem do odpowiedniej metody. Biblioteka ADAL udostępnia w tym celu metodę pomocnika w systemie Android:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Pulpit systemu Windows

W pliku MainWindow.xaml.cs wykonaj wywołanie metody `SearchByAlias(...)`, przekazując element `WindowInteropHelper` w obiekcie `PlatformParameters` pulpitu:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
W pliku DirSearchClient_iOSViewController.cs obiekt `PlatformParameters` systemu iOS przyjmuje odwołanie do kontrolera widoku:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Platforma uniwersalna systemu Windows
W aplikacjach uniwersalnych systemu Windows otwórz plik MainPage.xaml.cs, a następnie wdróż metodę `Search`. Ta metoda używa metody pomocnika w udostępnionym projekcie, aby w razie potrzeby zaktualizować interfejs użytkownika.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Masz teraz działającą aplikację platformy Xamarin, która może uwierzytelniać użytkowników i bezpiecznie wywoływać internetowe interfejsy API przy użyciu protokołu OAuth 2.0 na pięciu różnych platformach.

## <a name="step-5-populate-your-tenant"></a>Krok 5. Wypełnianie dzierżawy 

Jeśli dzierżawa nie została jeszcze wypełniona użytkownikami, nadszedł czas, aby to zrobić.

1. Uruchom aplikację DirectorySearcher, a następnie zaloguj się przy użyciu jednego z użytkowników.
2. Wyszukaj innych użytkowników na podstawie ich nazw UPN.

## <a name="next-steps"></a>Kolejne kroki

Biblioteka ADAL ułatwia dodanie typowych funkcji tożsamości do aplikacji. Zajmuje się ona wszystkimi żmudnymi zadaniami, takimi jak zarządzanie pamięcią podręczną, obsługa protokołu OAuth, prezentowanie użytkownikom interfejsu użytkownika logowania i odświeżanie wygasłych tokenów. Musisz znać tylko jedno wywołanie interfejsu API, `authContext.AcquireToken*(…)`.

* Pobierz [gotowy przykład](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (bez wartości konfiguracyjnych).
* Dowiedz się, jak [zabezpieczyć internetowy interfejs API platformy .NET za pomocą usługi Azure AD](quickstart-v1-dotnet-webapi.md).
