---
title: Korzystanie z brokerów z systemem Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować aplikacje Xamarin iOS, które mogą używać Microsoft Authenticator i biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET). Dowiedz się również, jak przeprowadzić migrację z biblioteki uwierzytelniania usługi Azure AD dla programu .NET (ADAL.NET) do biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
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
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132635"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Używanie Microsoft Authenticator lub Intune — Portal firmy w aplikacjach platformy Xamarin

W systemach Android i iOS brokerzy, takie jak Microsoft Authenticator i Portal firmy Microsoft Intune specyficzne dla systemu Android, umożliwiają:

- Logowanie jednokrotne **(SSO)** : użytkownicy nie muszą logować się do poszczególnych aplikacji.
- **Identyfikacja urządzenia**: Broker uzyskuje dostęp do certyfikatu urządzenia. Ten certyfikat jest tworzony na urządzeniu, gdy jest on przyłączony do miejsca pracy.
- **Weryfikacja identyfikacji aplikacji**: gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania. Broker weryfikuje adres URL.

Aby włączyć jedną z tych funkcji, użyj `WithBroker()` parametru podczas wywoływania metody `PublicClientApplicationBuilder.CreateApplication`. Parametr `.WithBroker()` jest domyślnie ustawiony na wartość true. 

Należy również użyć instrukcji w poniższych sekcjach, aby skonfigurować uwierzytelnianie obsługiwane przez brokera dla aplikacji [systemu iOS](#brokered-authentication-for-ios) lub aplikacji [systemu Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu iOS

Wykonaj następujące kroki, aby umożliwić aplikacji platformy Xamarin. iOS komunikację z aplikacją [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Krok 1. Włączanie obsługi brokera
Należy włączyć obsługę brokera dla poszczególnych wystąpień `PublicClientApplication`. Obsługa jest domyślnie wyłączona. Gdy tworzysz `PublicClientApplication` za pomocą `PublicClientApplicationBuilder`, użyj `WithBroker()` parametru, jak pokazano w poniższym przykładzie. Parametr `WithBroker()` jest domyślnie ustawiony na wartość true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Krok 2. Włączanie dostępu do łańcucha kluczy

Aby włączyć dostęp do łańcucha kluczy, musisz mieć grupę dostępu łańcucha kluczy dla swojej aplikacji. Przy użyciu interfejsu API `WithIosKeychainSecurityGroup()` można ustawić grupę dostępu łańcucha kluczy podczas tworzenia aplikacji:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Aby uzyskać więcej informacji, zobacz [Włączanie dostępu do łańcucha kluczy](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3. Aktualizacja AppDelegate do obsługi wywołania zwrotnego
Gdy biblioteka uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) wywołuje brokera, Broker wywołuje z powrotem do aplikacji za pomocą metody `OpenUrl` klasy `AppDelegate`. Ponieważ MSAL czeka na odpowiedź brokera, aplikacja musi współpracować w celu wywołania MSAL.NET z powrotem. Aby włączyć tę współpracę, zaktualizuj plik `AppDelegate.cs`, aby zastąpić poniższą metodę.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, 
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }
    
    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {               
         return false;                
    }
    
    return true;     
}           
```

Ta metoda jest wywoływana za każdym razem, gdy aplikacja jest uruchomiona. Jest on używany jako okazja do przetworzenia odpowiedzi z brokera i ukończenia procesu uwierzytelniania, który MSAL.NET uruchomiony.

### <a name="step-4-set-uiviewcontroller"></a>Krok 4. Ustawianie UIViewController ()
Nadal w pliku `AppDelegate.cs` należy ustawić okno obiektu. Zwykle w przypadku platformy Xamarin iOS nie trzeba ustawiać okna obiektu. Ale potrzebujesz okna obiektu, aby wysyłać i odbierać odpowiedzi z brokera. 

Aby skonfigurować okno obiektu: 
1. W pliku `AppDelegate.cs` Ustaw `App.RootViewController` na nowy `UIViewController()`. To przypisanie zapewnia, że wywołanie brokera zawiera `UIViewController`. Jeśli to ustawienie jest przypisane nieprawidłowo, może wystąpić następujący błąd:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Na `AcquireTokenInteractive` wywołaniu użyj `.WithParentActivityOrWindow(App.RootViewController)`, a następnie Przekaż odwołanie do okna obiektu, którego będziesz używać.

    W pliku `App.cs`:
    
    ```csharp
       public static object RootViewController { get; set; }
    ```
    
    W pliku `AppDelegate.cs`:
    
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    
    W `AcquireToken` Wywołaj:
    
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Krok 5. rejestrowanie schematu adresu URL
MSAL.NET używa adresów URL do wywołania brokera, a następnie zwrócenia odpowiedzi brokera do aplikacji. Aby zakończyć rundy, zarejestruj schemat adresu URL aplikacji w pliku `Info.plist`.

Nazwa `CFBundleURLSchemes` musi zawierać `msauth.` jako prefiks. Postępuj zgodnie z prefiksem `CFBundleURLName`. 

W schemacie URL `BundleId` unikatowo identyfikuje aplikację: `$"msauth.(BundleId)"`. Dlatego jeśli `BundleId` jest `com.yourcompany.xforms`, schemat adresu URL jest `msauth.com.yourcompany.xforms`.

> [!NOTE]
> Ten schemat adresu URL staje się częścią identyfikatora URI przekierowania, który jednoznacznie identyfikuje aplikację po odebraniu odpowiedzi z brokera.

```XML
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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 6. Dodawanie identyfikatora brokera do sekcji LSApplicationQueriesSchemes
MSAL używa `–canOpenURL:`, aby sprawdzić, czy na urządzeniu zainstalowano brokera. W systemie iOS 9 program Apple zablokował schematy, dla których aplikacja może wykonać zapytanie. 

Dodaj `msauthv2` do sekcji `LSApplicationQueriesSchemes` pliku `Info.plist`, jak w poniższym przykładzie:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Krok 7. Rejestrowanie identyfikatora URI przekierowania w portalu aplikacji
W przypadku korzystania z brokera identyfikator URI przekierowania ma dodatkowy wymóg. Identyfikator URI przekierowania _musi_ mieć następujący format:
```csharp
$"msauth.{BundleId}://auth"
```

Oto przykład: 

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Zwróć uwagę, że identyfikator URI przekierowania jest zgodny z nazwą `CFBundleURLSchemes`, która została uwzględniona w pliku `Info.plist`.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 8. Upewnij się, że identyfikator URI przekierowania jest zarejestrowany w aplikacji

Identyfikator URI przekierowania musi być zarejestrowany w [portalu rejestracji aplikacji](https://portal.azure.com) jako prawidłowy identyfikator URI przekierowania dla aplikacji. 

Portal rejestracji aplikacji zawiera nowe środowisko ułatwiające Obliczanie identyfikatora URI odpowiedzi obsługiwanej przez brokera z identyfikatora pakietu. 

Aby obliczyć identyfikator URI przekierowania:

1. W portalu rejestracji aplikacji wybierz pozycję **uwierzytelnianie** , > **Wypróbuj nowe środowisko**.

   ![Wypróbuj nowe środowisko rejestracji aplikacji](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Wybierz pozycję **Dodaj platformę**.

   ![Dodaj platformę](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Jeśli lista platform jest obsługiwana, wybierz pozycję **iOS**.

   ![Konfigurowanie systemu iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Wprowadź żądany identyfikator pakietu, a następnie wybierz pozycję **Konfiguruj**.

   ![Wprowadź identyfikator pakietu](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Po zakończeniu kroków zostanie obliczony identyfikator URI przekierowania.

![Kopiuj identyfikator URI przekierowania](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu Android

MSAL.NET obsługuje tylko platformę Xamarin. iOS. Nie obsługuje jeszcze brokerów dla platformy Xamarin. Android.

Biblioteka MSAL systemu Android Native obsługuje już uwierzytelnianie obsługiwane przez brokera. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie obsługiwane przez brokera w systemie Android](brokered-auth.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat zagadnień dotyczących używania platforma uniwersalna systemu Windows z MSAL.NET](msal-net-uwp-considerations.md).
