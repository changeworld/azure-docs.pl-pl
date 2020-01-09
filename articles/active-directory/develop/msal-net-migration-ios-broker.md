---
title: Migrowanie aplikacji platformy Xamarin do biblioteki MSAL.NET przy użyciu brokerów
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przeprowadzić migrację aplikacji platformy Xamarin dla systemu iOS, które używają Microsoft Authenticator z programu ADAL.NET do MSAL.NET.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c830b7f6d13d9b85eae34b6193ad2a10e7bfb410
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424209"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrowanie aplikacji systemu iOS, które używają Microsoft Authenticator z programu ADAL.NET do MSAL.NET

Korzystasz Azure Active Directory z biblioteki ADAL.NET Authentication Library dla platformy .NET i brokera systemu iOS. Teraz czas na migrację do [biblioteki uwierzytelniania firmy Microsoft](msal-overview.md) dla programu .net (MSAL.NET), która obsługuje brokera w systemie iOS z wersji 4,3. 

Gdzie należy zacząć? Ten artykuł ułatwia Migrowanie aplikacji platformy Xamarin dla systemu iOS z biblioteki ADAL do MSAL.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz już aplikację platformy Xamarin dla systemu iOS zintegrowaną z brokerem systemu iOS. Jeśli nie, przejdź bezpośrednio do MSAL.NET i Rozpocznij implementację brokera. Aby uzyskać informacje na temat sposobu wywoływania brokera systemu iOS w MSAL.NET z nową aplikacją, zapoznaj się z [tą dokumentacją](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Tło

### <a name="what-are-brokers"></a>Co to są brokerzy?

Brokerzy są aplikacjami udostępnianymi przez firmę Microsoft w systemach Android i iOS. (Zobacz aplikację [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) w systemach iOS i Android oraz aplikację Intune — portal firmy w systemie Android). 

Umożliwiają one:

- Logowanie jednokrotne.
- Identyfikacja urządzenia, która jest wymagana przez niektóre [zasady dostępu warunkowego](../conditional-access/overview.md). Aby uzyskać więcej informacji, zobacz [Zarządzanie urządzeniami](../conditional-access/conditions.md#device-platforms).
- Weryfikacja identyfikacji aplikacji, która jest również wymagana w niektórych scenariuszach dla przedsiębiorstw. Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacjami mobilnymi w usłudze Intune (mam)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrowanie z biblioteki ADAL do MSAL

### <a name="step-1-enable-the-broker"></a>Krok 1. Włączanie brokera

<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
W programie ADAL.NET obsługa brokera została włączona na podstawie kontekstu uwierzytelniania. Jest on domyślnie wyłączony. Należy ustawić 

Flaga `useBroker` w konstruktorze `PlatformParameters` do wywołania brokera:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Ponadto w przypadku kodu specyficznego dla platformy w tym przykładzie w module renderowania stron dla systemu iOS Ustaw `useBroker` 
Oflaguj na wartość true:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Następnie Uwzględnij parametry w wywołaniu tokenu pozyskiwania:
```csharp
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
W programie MSAL.NET obsługa brokera jest włączana dla poszczególnych PublicClientApplication. Jest on domyślnie wyłączony. Aby ją włączyć, użyj 

`WithBroker()` parametru (domyślnie ustawiona na true) w celu wywołania brokera:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
W wywołaniu tokenu pozyskiwania:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Krok 2. Ustawianie elementu UIViewController ()
W programie ADAL.NET przeszedł element UIViewController w ramach `PlatformParameters`. (Zobacz przykład w kroku 1). W programie MSAL.NET, aby zapewnić deweloperom większą elastyczność, używane jest okno obiektu, ale nie jest to wymagane w przypadku zwykłego użycia systemu iOS. Aby użyć brokera, należy ustawić okno obiektu, aby wysyłać i odbierać odpowiedzi z brokera. 
<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
UIViewController jest przenoszona do 

`PlatformParameters` na platformie dla systemu iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
W programie MSAL.NET należy wykonać dwie czynności, aby ustawić okno obiektu dla systemu iOS:

1. W `AppDelegate.cs`Ustaw `App.RootViewController` na nową `UIViewController()`. To przypisanie gwarantuje, że istnieje UIViewController z wywołaniem do brokera. Jeśli nie jest ustawiona poprawnie, może zostać wyświetlony następujący błąd: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. W wywołaniu AcquireTokenInteractive Użyj `.WithParentActivityOrWindow(App.RootViewController)`i przekaż odwołanie do okna obiektu, którego będziesz używać.

**Na przykład:**

W pliku `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
W pliku `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
W wywołaniu tokenu pozyskiwania:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3. Aktualizacja AppDelegate do obsługi wywołania zwrotnego
Zarówno ADAL, jak i MSAL wywołują brokera, a Broker z kolei wywołuje z powrotem do aplikacji za pomocą metody `OpenUrl` klasy `AppDelegate`. Aby uzyskać więcej informacji, zobacz [tę dokumentację](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Między ADAL.NET i MSAL.NET nie wprowadzono żadnych zmian.

### <a name="step-4-register-a-url-scheme"></a>Krok 4. rejestrowanie schematu adresu URL
ADAL.NET i MSAL.NET używają adresów URL do wywołania brokera i zwrócenia odpowiedzi brokera z powrotem do aplikacji. Zarejestruj schemat adresu URL w pliku `Info.plist` dla aplikacji w następujący sposób:

<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
Schemat adresu URL jest unikatowy dla Twojej aplikacji.
</td><td>
Nie można usunąć zasobu typu 

Nazwa `CFBundleURLSchemes` musi zawierać 

`msauth.`

jako prefiks, po którym następuje `CFBundleURLName`

Na przykład: `$"msauth.(BundleId")`

```csharp
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
> Ten schemat adresu URL staje się częścią identyfikatora URI przekierowania, który jest używany do jednoznacznego identyfikowania aplikacji po odebraniu odpowiedzi z brokera.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 5. Dodawanie identyfikatora brokera do sekcji LSApplicationQueriesSchemes

ADAL.NET i MSAL.NET oba używają `-canOpenURL:`, aby sprawdzić, czy na urządzeniu zainstalowano brokera. Dodaj prawidłowy identyfikator brokera systemu iOS do sekcji LSApplicationQueriesSchemes w pliku info. plist w następujący sposób:

<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
Użycie 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Użycie 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Krok 6. Rejestrowanie identyfikatora URI przekierowania w portalu

ADAL.NET i MSAL.NET jednocześnie dodają dodatkowe wymagania dotyczące identyfikatora URI przekierowania, gdy jest on przeznaczony dla brokera. Zarejestruj identyfikator URI przekierowania w aplikacji w portalu.
<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Przykład: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Przykład:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Aby uzyskać więcej informacji na temat rejestrowania identyfikatora URI przekierowania w portalu, zobacz Korzystanie [z brokera w aplikacjach platformy Xamarin. iOS](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zagadnieniach specyficznych dla platformy Xamarin dla systemu iOS z MSAL.NET](msal-net-xamarin-ios-considerations.md). 
