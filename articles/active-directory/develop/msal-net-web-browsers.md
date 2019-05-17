---
title: Przeglądarek Biblioteka Microsoft Authentication Library dla platformy .NET | Azure
description: Dowiedz się więcej o tu konkretne uwagi przy użyciu platformy Xamarin Android Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f7f91e6ab1fb12132068b839e66fafd3ab1bc73
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65543961"
---
# <a name="using-web-browsers-in-msalnet"></a>Przy użyciu przeglądarki sieci web w platformy MSAL.NET
Przeglądarki sieci Web są wymagane, aby przeprowadzić uwierzytelnianie interakcyjne. Domyślnie obsługuje platformy MSAL.NET [przeglądarki sieci web systemu](#system-web-browser-on-xamarinios-and-xamarinandroid) na Xamarin.iOS i [Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser). Ale [można również włączyć przeglądarki sieci Web osadzone](#enable-embedded-webviews) w zależności od wymagań (UX potrzeby logowania jednokrotnego (SSO) zabezpieczeń) w [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) i [platformy Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) aplikacje. A może nawet [wybierz dynamicznie](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) używanej przeglądarki sieci web do użycia na podstawie obecności Chrome lub przeglądarki, Obsługa niestandardowych kart dla programu Chrome w systemie Android.

## <a name="web-browsers-in-msalnet"></a>Przeglądarki sieci Web w platformy MSAL.NET

Jest ważne dowiedzieć się, że podczas uzyskiwania tokenu interaktywnie, zawartość okna dialogowego nie zostanie podany przez bibliotekę, ale przez usługę STS (usługę tokenu zabezpieczającego). Punkt końcowy uwierzytelniania wysyła z powrotem niektóre HTML i JavaScript, który kontroluje interakcji, która jest wyświetlana w przeglądarce sieci web lub kontrolka sieci web. Dzięki czemu usługa STS do obsługi interakcji HTML ma wiele zalet:

- Hasło (jeśli jest to jeden został wpisany) nigdy nie jest przechowywany przez aplikację, ani biblioteki uwierzytelniania.
- Umożliwia przekierowywanie do innych dostawców tożsamości, (na przykład logowanie w za pomocą konto służbowe lub osobiste konta przy użyciu biblioteki MSAL lub za pomocą konta społecznościowego za pomocą usługi Azure AD B2C).
- Umożliwia usługi STS, kontrolować dostęp warunkowy, na przykład przez użytkownika wielu uwierzytelniania wieloskładnikowego (MFA) w fazie uwierzytelniania (numerem pin Windows Hello lub wywoływana na telefon lub w aplikacji uwierzytelniania na telefon). W przypadkach, gdzie uwierzytelniania współczynnik multi wymagane nie ustawiono go jeszcze użytkownika można ustawić go dokładnie na czas, w tym samym oknie dialogowym.  Użytkownik wprowadza numeru telefonu komórkowego i kieruje do zainstalowania aplikacji uwierzytelniania i skanowania, tagu QR, aby dodać swoje konto. Ten serwer opartych na interakcję to doskonałe środowisko!
- Zezwala użytkownikowi na zmiany hasła w tym samym oknie dialogowym, gdy hasło wygasło (zapewnia dodatkowe pola stare hasło i nowe hasło).
- Włącza znakowanie dzierżawy lub aplikacji (obrazy) i kontrolowane przez administratora dzierżawy usługi Azure AD / właściciela aplikacji.
- Umożliwia użytkownikom na wyrażanie zgody, aby umożliwić aplikacji dostęp do zasobów / zakresów w swojej nazwie zaraz po uwierzytelniania.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Przeglądarki sieci web systemu na Xamarin.iOS i Xamarin.Android

Domyślnie platformy MSAL.NET obsługuje przeglądarki sieci web systemu Xamarin.iOS i Xamarin.Android. Dla wszystkich platform, które udostępniają interfejs użytkownika (czyli nie .NET Core) okno dialogowe jest zapewniana przez bibliotekę osadzania formant przeglądarki sieci Web. Platformy MSAL.NET również używa widoku sieci web osadzone klasycznych na platformie .NET i Książka adresowa systemu Windows dla platformy UWP. Jednak korzysta domyślnie **przeglądarki sieci web systemu** dla aplikacji platformy Xamarin Android i platformy Xamarin iOS. W systemach iOS, nawet wybiera widoku sieci web do użycia w zależności od wersji systemu operacyjnego (iOS12, system IOS 11 i starszych).

Za pomocą przeglądarki system ma znaczącą korzyścią udostępniania stanu logowania jednokrotnego z innych aplikacji i aplikacji sieci web bez konieczności brokera (portalu firmy / Authenticator). System użytej przeglądarki, domyślnie w platformy MSAL.NET dla platform Xamarin dla systemu Android i platformy Xamarin iOS ponieważ na tych platformach przeglądarki sieci web systemu zajmuje cały ekran i lepiej jest środowisko użytkownika. Widok sieci web systemu nie jest rozróżnialnych z okna dialogowego. W systemach iOS jednak użytkownik może być konieczne wyrazić zgodę dla przeglądarki do wywołania zwrotnego aplikację, która może być irytujące.

### <a name="uwp-does-not-use-the-system-webview"></a>System Webview nie korzysta z platformy uniwersalnej systemu Windows

Dla aplikacji klasycznych, uruchamiając System Webview prowadzi do niepoprawne komfortu jako użytkownik zobaczy przeglądarce, gdzie mogą już mieć innych kart, otwarty. I w przypadku uwierzytelniania się nie stało, użytkownicy pobiera strony z monitem o zamknąć to okno. Jeśli użytkownik nie należy zwrócić uwagę, ich zamknąć cały proces (w tym innych kart, które są związane z uwierzytelnianiem). Korzystanie z przeglądarki systemu na pulpicie również wymagałoby otwierania portów lokalnych i nasłuchuje na nich, może to wymagać uprawnień zaawansowanych aplikacji. Jako deweloper, użytkownik lub administrator, może być zapewniony dotyczących tego wymagania.

## <a name="enable-embedded-webviews"></a>Włącz osadzonych elementów Webview 
Można również włączyć osadzonych elementów Webview w aplikacji platformy Xamarin.iOS i Xamarin.Android. Począwszy od platformy MSAL.NET 2.0.0-preview platformy MSAL.NET obsługuje również przy użyciu **osadzone** opcji widoku sieci Web. Dla ADAL.NET embedded webview jest jedyną opcją, które są obsługiwane.

Jako deweloper przy użyciu platformy MSAL.NET przeznaczonych dla platformy Xamarin można używać osadzonych elementów Webview lub przeglądarki systemu. Jest to wybór w zależności od użytkownika środowisko i zabezpieczeniami. problemy, które chcesz przeanalizować.

Obecnie platformy MSAL.NET nie obsługuje jeszcze brokerów systemów Android i iOS. W związku z tym jeśli potrzebujesz zapewnić logowanie jednokrotne (SSO), w przeglądarce system nadal może być lepszym rozwiązaniem. Obsługa brokerów z przeglądarką internetową osadzone znajduje się na zaległości platformy MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Różnice między webview osadzone i przeglądarki systemu 
Istnieją pewne visual różnice między osadzone webview a przeglądarką system w platformy MSAL.NET.

**Interaktywne Zaloguj się przy użyciu platformy MSAL.NET przy użyciu osadzonych widoku sieci Web:**

![embedded](media/msal-net-web-browsers/embedded-webview.png)

**Interaktywne Zaloguj się przy użyciu platformy MSAL.NET za pomocą przeglądarki systemu:**

![Przeglądarka systemu](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opcje dewelopera

Jako deweloper przy użyciu platformy MSAL.NET masz kilka opcji umożliwiających wyświetlanie interaktywnego okna dialogowego z STS:

- **Przeglądarka systemu.** Przeglądarka systemu jest ustawiona domyślnie w bibliotece. Jeśli używasz systemu Android, przeczytaj [przeglądarki systemu](msal-net-system-browser-android-considerations.md) Aby uzyskać szczegółowe informacje o tym, jakie przeglądarki są obsługiwane dla uwierzytelniania. Korzystając z przeglądarki systemu w systemie Android, firma Microsoft zaleca, czy urządzenie ma przeglądarki, która obsługuje niestandardowe karty przeglądarki Chrome.  W przeciwnym razie uwierzytelnianie może zakończyć się niepowodzeniem.
- **Osadzonego elementu webview.** Aby użyć tylko osadzone webview w platformy MSAL.NET, `AcquireTokenInteractively` zawiera parametry konstruktora `WithUseEmbeddedWebView()` metody.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Wybieranie między przeglądarki sieci web osadzone lub przeglądarki systemu na platformy Xamarin.iOS

W aplikacji systemu iOS w `AppDelegate.cs` można zainicjować `ParentWindow` do `null`. Nie jest używany w systemie iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Wybieranie między przeglądarki sieci web osadzone lub przeglądarki systemu na platformy Xamarin.Android

W aplikacji systemu Android w `MainActivity.cs` działania nadrzędnego można ustawić tak, aby wyniki uwierzytelniania otrzymuje się do niego:

```csharp
 App.ParentWindow = this;
```

Następnie w `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Wykrywanie obecności niestandardowych kart na platformy Xamarin.Android

Jeśli chcesz włączyć logowanie Jednokrotne za pomocą aplikacji w przeglądarce przy użyciu przeglądarki sieci web systemu, ale są niepokoju czynności użytkownika dla urządzeń z systemem Android nie ma przeglądarki z obsługą niestandardową kartę, użytkownik może zdecydować, wywołując `IsSystemWebViewAvailable()` method in Class metoda < c 2 > `IPublicClientApplication` . Ta metoda zwraca `true` Jeśli PackageManager wykryje niestandardowe karty i `false` Jeśli nie są wykrywane na urządzeniu.

Na podstawie wartości zwracane przez tę metodę i swoje wymagania, możesz wykonać decyzji:

- Niestandardowy komunikat o błędzie możesz wrócić do użytkownika. Na przykład: "Zainstaluj program Chrome, aby kontynuować uwierzytelnianie" - lub-
- Można wrócić do opcji/webview osadzone i uruchomić interfejs użytkownika jako osadzonego webview.

Poniższy kod opcji embedded webview:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

## <a name="net-core-does-not-support-interactive-authentication-out-of-the-box"></a>.NET core nie obsługuje uwierzytelnianie interakcyjne gotowe

Dla platformy .NET Core nabycia tokenów interaktywnie nie jest dostępna. W rzeczywistości platformy .NET Core nie dostarcza jeszcze interfejsu użytkownika. Jeśli chcesz udostępnić interakcyjnego logowania dla aplikacji platformy .NET Core, można pozwolić aplikacji prezentowanej użytkownikowi kod i adres URL, aby przejść do logowania interakcyjnego (zobacz [przepływu kodu urządzenia](msal-authentication-flows.md#device-code)).

Alternatywnie można zaimplementować [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) interfejs i dostarczyć własne przeglądarki