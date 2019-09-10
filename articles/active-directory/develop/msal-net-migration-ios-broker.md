---
title: Migrowanie aplikacji platformy Xamarin iOS przy użyciu Microsoft Authenticator z ADAL.NET do MSAL.NET | Azure
description: Dowiedz się, jak przeprowadzić migrację aplikacji platformy Xamarin iOS przy użyciu Microsoft Authenticator z biblioteki Azure AD Authentication Library for .NET (ADAL.NET) do biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET)
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875655"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrowanie aplikacji systemu iOS przy użyciu Microsoft Authenticator z ADAL.NET do MSAL.NET

Korzystasz z usługi ADAL.NET i brokera systemu iOS, a następnie przeprowadzisz migrację do [biblioteki Microsoft Authentication Library](msal-overview.md), która obsługuje brokera w systemie iOS z wersji 4,3 lub nowszej. 

Od czego rozpocząć? Ten artykuł pomoże przeprowadzić migrację aplikacji platformy Xamarin dla systemu iOS z biblioteki ADAL do MSAL.

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz już aplikację platformy Xamarin dla systemu iOS zintegrowaną z brokerem systemu iOS. Jeśli tego nie zrobisz, najlepiej przenieść się bezpośrednio do MSAL.NET i rozpocząć implementację brokera. Zapoznaj się z [tą dokumentacją](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications) , aby uzyskać szczegółowe informacje na temat wywoływania brokera systemu iOS w programie MSAL.NET z nową aplikacją.

## <a name="background"></a>Tło

### <a name="what-are-brokers"></a>Co to są brokerzy?

Brokerzy są aplikacjami dostarczanymi przez firmę Microsoft w systemach Android i iOS ([Microsoft Authenticator](https://www.microsoft.com/account/authenticator) w systemach iOS i Android Intune — portal firmy w systemie Android). 

Umożliwiają one:

- Logowanie jednokrotne,
- Identyfikacja urządzenia, która jest wymagana przez niektóre [zasady dostępu warunkowego](../conditional-access/overview.md) (zobacz [Zarządzanie urządzeniami](../conditional-access/conditions.md#device-platforms))
- Weryfikacja identyfikacji aplikacji, wymagana również w niektórych scenariuszach dla przedsiębiorstw (Zobacz na przykład [Zarządzanie aplikacjami mobilnymi w usłudze Intune lub mam](https://docs.microsoft.com/intune/mam-faq))

## <a name="migrate-from-adal-to-msal"></a>Migrowanie z biblioteki ADAL do MSAL

### <a name="step-1-enable-the-broker"></a>Krok 1: Włączanie brokera

<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
W programie ADAL.NET obsługa brokera została włączona na podstawie kontekstu uwierzytelniania. jest ona domyślnie wyłączona. Należy ustawić `useBroker` flagę na wartość true `PlatformParameters` w konstruktorze, aby wywoływać brokera:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Ponadto w kodzie specyficznym dla platformy, w tym przykładzie, w module renderowania stron dla systemu iOS Ustaw`useBroker` 
Oflaguj na wartość true:
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Następnie Uwzględnij parametry w wywołaniu tokenu pozyskiwania:
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
W programie MSAL.NET obsługa brokera jest włączana dla poszczególnych publicznych aplikacji klienckich. To ustawienie jest domyślnie wyłączone. Aby je włączyć, użyj: 

`WithBroker()`wartość parametru (domyślnie ustawiona na true) w celu wywołania brokera:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
W wywołaniu tokenu pozyskiwania:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Krok 2: Ustaw UIViewController ()
W programie ADAL.NET przeszedł element UIViewController w ramach PlatformParameters (Zobacz przykład w kroku 1). Jednak w programie MSAL.NET, aby zapewnić deweloperom większą elastyczność, używane jest okno obiektu, ale nie jest to wymagane w przypadku zwykłego użycia systemu iOS. Aby jednak móc korzystać z brokera, należy ustawić okno obiektu, aby wysyłać i odbierać odpowiedzi z brokera. 
<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
UIViewController jest przenoszona do PlatformParamters na platformie dla systemu iOS.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
W programie MSAL.NET należy wykonać dwie czynności, aby ustawić okno obiektu dla systemu iOS:

1) W `AppDelegate.cs`programie `App.RootViewController` ustaw wartość na nowy. `UIViewController()` To przypisanie zapewni, że istnieje UIViewController z wywołaniem do brokera. Jeśli nie jest ustawiona poprawnie, może wystąpić następujący błąd:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) W wywołaniu AcquireTokenInteractive Użyj`.WithParentActivityOrWindow(App.RootViewController)`
i przekaż odwołanie do okna obiektu you'will użycie.

**Na przykład:**

W pliku `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
W pliku `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
W wywołaniu tokenu pozyskiwania:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3: Aktualizowanie AppDelegate w celu obsługi wywołania zwrotnego
Zarówno ADAL, jak i MSAL będą wywoływać brokera, a Broker będzie z kolei wywoływać z powrotem do aplikacji `OpenUrl` za pomocą metody `AppDelegate` klasy. Więcej informacji dostępnych [tutaj](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback)

: heavy_check_mark:**nie ma żadnych zmian między ADAL.NET i MSAL.NET**

### <a name="step-4-register-a-url-scheme"></a>Krok 4: Zarejestruj schemat adresu URL
ADAL.NET i MSAL.NET używają adresów URL do wywołania brokera i zwrócenia odpowiedzi brokera z powrotem do aplikacji. Zarejestruj schemat adresu URL w `Info.plist` pliku dla aplikacji w następujący sposób:

<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
Schemat adresu URL jest unikatowy dla Twojej aplikacji.
</td><td>
Dla zasobu 

`CFBundleURLSchemes`Nazwa musi zawierać 

`msauth.`

jako prefiks, po którym następuje`CFBundleURLName`

Na przykład: `$"msauth.(BundleId")`

```CSharp
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

> [!NOTE]
>  Ten schemat adresu URL staje się częścią RedirectUri używany do unikatowego identyfikowania aplikacji podczas otrzymywania odpowiedzi z brokera

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>Krok 5. LSApplicationQueriesSchemes

ADAL.NET i MSAL.NET służą `-canOpenURL:` do sprawdzania, czy na urządzeniu zainstalowano brokera. Dodaj prawidłowy identyfikator brokera systemu iOS do sekcji LSApplicationQueriesSchemes w pliku info. plist w następujący sposób: 
<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
Użycie 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Użycie 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>Krok 6: Zarejestruj się w portalu RedirectUri

ADAL.NET i MSAL.NET dodają dodatkowe wymaganie na redirectUri podczas określania elementu docelowego dla brokera. Zarejestruj identyfikator URI przekierowania w aplikacji w portalu.
<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`przyklad`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

przykład:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Aby uzyskać więcej informacji na temat rejestrowania redirectUri w portalu, zobacz temat Korzystanie [z brokera w aplikacjach platformy Xamarin. iOS](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app) w celu uzyskania szczegółowych informacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zagadnieniach specyficznych dla platformy Xamarin dla systemu iOS z MSAL.NET](msal-net-xamarin-ios-considerations.md). 
