---
title: Migrowanie aplikacji platformy Xamarin do biblioteki MSAL.NET przy użyciu brokerów
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przeprowadzić migrację aplikacji platformy Xamarin na iOS, które używają programu Microsoft Authenticator z ADAL.NET do MSAL.NET.
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
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185826"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrowanie aplikacji systemu iOS korzystających z usługi Microsoft Authenticator z ADAL.NET do MSAL.NET

Używasz biblioteki uwierzytelniania usługi Azure Active Directory dla platformy .NET (ADAL.NET) i brokera systemu iOS. Teraz nadszedł czas, aby przeprowadzić migrację do [biblioteki uwierzytelniania firmy Microsoft](msal-overview.md) dla platformy .NET (MSAL.NET), która obsługuje brokera w systemie iOS od wersji 4.3. 

Od czego zacząć? Ten artykuł ułatwia migrację aplikacji xamarin na iOS z usługi ADAL do usługi MSAL.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz już aplikację xamarin na iOS, która jest zintegrowana z brokerem systemu iOS. Jeśli tego nie zrobisz, przejdź bezpośrednio do MSAL.NET i rozpocznij tam implementację brokera. Aby uzyskać informacje na temat wywoływania brokera systemu iOS w MSAL.NET z nową aplikacją, zobacz [tę dokumentację](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Tło

### <a name="what-are-brokers"></a>Co to są brokerzy?

Brokerzy to aplikacje dostarczane przez firmę Microsoft na android i iOS. (Zobacz aplikację [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) w systemach iOS i Android oraz aplikację Portal firmy usługi Intune w systemie Android). 

Umożliwiają one:

- Logowanie jednokrotne.
- Identyfikacja urządzenia, która jest wymagana przez niektóre [zasady dostępu warunkowego](../conditional-access/overview.md). Aby uzyskać więcej informacji, zobacz [Zarządzanie urządzeniami](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Weryfikacja identyfikacji aplikacji, która jest również wymagana w niektórych scenariuszach przedsiębiorstwa. Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacjami mobilnymi usługi Intune (MAM)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrowanie z usługi ADAL do usługi MSAL

### <a name="step-1-enable-the-broker"></a>Krok 1: Włącz brokera

<table>
<tr><td>Aktualny kod ADAL:</td><td>Odpowiednik MSAL:</td></tr>
<tr><td>
W ADAL.NET obsługa brokera została włączona na podstawie kontekstu uwierzytelniania. Jest domyślnie wyłączona. Trzeba było ustawić 

`useBroker`flaga true `PlatformParameters` w konstruktorze, aby wywołać brokera:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Ponadto w kodzie specyficznym dla platformy w tym przykładzie w renderecie stron dla systemu iOS`useBroker` 
flaga do true:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Następnie należy uwzględnić parametry w wywołaniu tokenu nabycia:
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
W MSAL.NET obsługa brokera jest włączona na podstawie aplikacji na klienta. Jest domyślnie wyłączona. Aby ją włączyć, użyj 

`WithBroker()`parametr (domyślnie ustawiony na true) w celu wywołania brokera:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
W wywołaniu tokenu nabycia:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Krok 2: Ustawianie kontrolera UIViewController()
W ADAL.NET, przeszedłeś w UIViewController jako `PlatformParameters`część . (Zobacz przykład w kroku 1.) W MSAL.NET, aby zapewnić deweloperom większą elastyczność, używane jest okno obiektu, ale nie jest wymagane w zwykłym użyciu systemu iOS. Aby użyć brokera, ustaw okno obiektu, aby wysyłać i odbierać odpowiedzi od brokera. 
<table>
<tr><td>Aktualny kod ADAL:</td><td>Odpowiednik MSAL:</td></tr>
<tr><td>
Kontroler UIViewController jest przekazywany do 

`PlatformParameters`na platformie specyficznej dla systemu iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
W MSAL.NET wykonaj dwie czynności, aby ustawić okno obiektu dla systemu iOS:

1. W `AppDelegate.cs`, `App.RootViewController` ustawiono `UIViewController()`nowy . To przypisanie zapewnia, że istnieje UIViewController z wywołaniem brokera. Jeśli nie jest poprawnie ustawiona, może pojawić się ten błąd:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. W acquireTokenInteractive wywołania, użyj `.WithParentActivityOrWindow(App.RootViewController)`i przekazać w odwołaniu do okna obiektu, który będzie używany.

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
W wywołaniu tokenu nabycia:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3: Aktualizacja AppDelegate do obsługi wywołania zwrotnego
Zarówno ADAL i MSAL wywołać brokera, a broker z `OpenUrl` kolei `AppDelegate` wywołuje z powrotem do aplikacji za pomocą metody klasy. Aby uzyskać więcej informacji, zobacz [tę dokumentację](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Nie ma tu żadnych zmian między ADAL.NET a MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Krok 4: Zarejestruj schemat adresu URL
ADAL.NET i MSAL.NET używać adresów URL do wywoływania brokera i zwracania odpowiedzi brokera z powrotem do aplikacji. Zarejestruj schemat adresu `Info.plist` URL w pliku aplikacji w następujący sposób:

<table>
<tr><td>Aktualny kod ADAL:</td><td>Odpowiednik MSAL:</td></tr>
<tr><td>
Schemat adresu URL jest unikatowy dla twojej aplikacji.
</td><td>
Dla zasobu 

`CFBundleURLSchemes`nazwa musi zawierać 

`msauth.`

jako przedrostek, po którym następuje`CFBundleURLName`

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
> Ten schemat adresu URL staje się częścią identyfikatora URI przekierowania, który jest używany do jednoznacznej identyfikacji aplikacji po otrzymaniu odpowiedzi od brokera.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 5: Dodaj identyfikator brokera do sekcji LSApplicationQueriesSchemes

ADAL.NET i MSAL.NET używają `-canOpenURL:` zarówno do sprawdzenia, czy broker jest zainstalowany na urządzeniu. Dodaj poprawny identyfikator dla brokera systemu iOS do sekcji LSApplicationQueriesSchemes pliku info.plist w następujący sposób:

<table>
<tr><td>Aktualny kod ADAL:</td><td>Odpowiednik MSAL:</td></tr>
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

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Krok 6: Zarejestruj identyfikator URI przekierowania w portalu

ADAL.NET i MSAL.NET zarówno dodać dodatkowe wymagania na identyfikatorze URI przekierowania, gdy jest on skierowany do brokera. Zarejestruj identyfikator URI przekierowania w aplikacji w portalu.
<table>
<tr><td>Aktualny kod ADAL:</td><td>Odpowiednik MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Przykład: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Przykład:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Aby uzyskać więcej informacji na temat rejestrowania identyfikatora URI przekierowania w portalu, zobacz [Dźwignia brokera w aplikacjach Xamarin.iOS](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zagadnieniach specyficznych dla systemu Xamarin iOS, MSAL.NET](msal-net-xamarin-ios-considerations.md). 
