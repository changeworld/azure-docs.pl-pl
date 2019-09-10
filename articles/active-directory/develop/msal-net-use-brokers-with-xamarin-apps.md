---
title: Używanie Microsoft Authenticator lub Microsoft Intune portal firmy w aplikacjach Xamarin iOS i Android | Azure
description: Dowiedz się, jak migrować aplikacje Xamarin iOS mogą używać Microsoft Authenticator z biblioteki uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) w bibliotece uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET)
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
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875642"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Używanie Microsoft Authenticator lub Microsoft Intune portal firmy w aplikacjach platformy Xamarin

W systemach Android i iOS brokerzy, takie jak Microsoft Authenticator lub Microsoft Intune, są włączone (tylko system Android):

- Logowanie jednokrotne (SSO). Użytkownicy nie muszą logować się do poszczególnych aplikacji.
- Identyfikacja urządzenia. Przez uzyskanie dostępu do certyfikatu urządzenia, który został utworzony na urządzeniu, gdy było dołączone do miejsca pracy.
- Weryfikacja identyfikacji aplikacji. Gdy aplikacja wywołuje brokera, przekazuje jego adres URL przekierowania i weryfikuje go.

Aby włączyć jedną z tych funkcji, deweloperzy aplikacji muszą używać `WithBroker()` parametru podczas `PublicClientApplicationBuilder.CreateApplication` wywoływania metody. `.WithBroker()`Domyślnie ma wartość true. Deweloperzy będą również musieli wykonać poniższe kroki w przypadku aplikacji dla [systemu iOS](#brokered-authentication-for-ios) lub [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu iOS

Wykonaj poniższe czynności, aby umożliwić aplikacji platformy Xamarin. iOS komunikację z aplikacją [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Krok 1: Włącz obsługę brokera
Obsługa brokera jest włączana dla poszczególnych PublicClientApplication. Jest on domyślnie wyłączony. `WithBroker()` Użyj parametru (domyślnie ustawione na true) podczas tworzenia PublicClientApplication za pomocą PublicClientApplicationBuilder.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: Aktualizowanie AppDelegate w celu obsługi wywołania zwrotnego
Gdy MSAL.NET wywołuje brokera, Broker będzie z kolei wywoływać z powrotem do aplikacji za pomocą `OpenUrl` metody `AppDelegate` klasy. Ponieważ MSAL będzie oczekiwać na odpowiedź z brokera, aplikacja musi współpracować, aby wywoływać MSAL.NET z powrotem. Aby włączyć tę współpracę, zaktualizuj `AppDelegate.cs` plik, aby zastąpić poniższą metodę.

```CSharp
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

Ta metoda jest wywoływana za każdym razem, gdy aplikacja jest uruchomiona i jest używana jako szansa do przetwarzania odpowiedzi z brokera i ukończenia procesu uwierzytelniania zainicjowane przez MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3: Ustaw UIViewController ()
Nadal w `AppDelegate.cs`programie należy ustawić okno obiektu. Zwykle w przypadku platformy Xamarin iOS nie trzeba ustawiać okna obiektu, ale w celu wysyłania i odbierania odpowiedzi z brokera potrzebne jest okno obiektu. 

Aby to zrobić, musisz wykonać dwie czynności. 
1) W `AppDelegate.cs`programie `App.RootViewController` ustaw wartość na nowy. `UIViewController()` To przypisanie pozwoli upewnić się, że istnieje UIViewController z wywołaniem do brokera. Jeśli nie jest ustawiona poprawnie, może wystąpić następujący błąd:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) W wywołaniu AcquireTokenInteractive Użyj `.WithParentActivityOrWindow(App.RootViewController)` i przekaż odwołanie do okna obiektu, którego będziesz używać.

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

### <a name="step-4-register-a-url-scheme"></a>Krok 4: Zarejestruj schemat adresu URL
MSAL.NET używa adresów URL do wywołania brokera, a następnie zwrócenia odpowiedzi brokera z powrotem do aplikacji. Aby zakończyć rundy, należy zarejestrować schemat adresu URL dla aplikacji w `Info.plist` pliku.

Nazwa musi zawierać `msauth.` jako`CFBundleURLName`prefiks, a po niej. `CFBundleURLSchemes`

`$"msauth.(BundleId)"`

**Na przykład:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Stanie się to częścią RedirectUri używanym do unikatowego identyfikowania aplikacji podczas otrzymywania odpowiedzi z brokera.

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

### <a name="step-5-lsapplicationqueriesschemes"></a>Krok 5. LSApplicationQueriesSchemes
MSAL używa `–canOpenURL:` do sprawdzenia, czy na urządzeniu zainstalowano brokera. W systemie iOS 9 Firma Apple zablokowała schematy, dla których aplikacja może wykonać zapytanie. 

**Dodaj** **`msauthv2`** do`LSApplicationQueriesSchemes` sekcji pliku`Info.plist` .

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>Krok 6: Rejestrowanie RedirectUri w portalu aplikacji
Użycie brokera dodaje dodatkowe wymagania dotyczące redirectUri. RedirectUri _**musi**_ mieć następujący format:
```CSharp
$"msauth.{BundleId}://auth"
```
**Na przykład:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**Zauważ, że RedirectUri pasuje do `CFBundleURLSchemes` nazwy zawartej `Info.plist` w pliku.**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 7. Upewnij się, że identyfikator URI przekierowania jest zarejestrowany w aplikacji

Ten identyfikator URI przekierowania musi być zarejestrowany w portalu rejestracji aplikacji (https://portal.azure.com) jako prawidłowy identyfikator URI przekierowania dla aplikacji). 

Portal udostępnia nowy portal rejestracji aplikacji w celu ułatwienia obliczenia identyfikatora URI odpowiedzi obsługiwanej przez brokera z identyfikatora pakietu:

1. W obszarze Rejestracja aplikacji wybierz pozycję **uwierzytelnianie** i wybierz opcję **Wypróbuj nowe środowisko**.
   ![Wypróbuj nowe środowisko rejestracji aplikacji](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Wybierz pozycję **Dodaj platformę**.
   ![Dodaj platformę](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Jeśli lista platform jest obsługiwana, wybierz pozycję **iOS**.
   ![Konfigurowanie systemu iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Wprowadź identyfikator pakietu jako żądany, a następnie naciśnij pozycję **zarejestruj**.
   ![Wprowadź identyfikator pakietu](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. Identyfikator URI przekierowania jest obliczany dla Ciebie.
   ![Kopiuj identyfikator URI przekierowania](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu Android

Obsługa brokera nie jest dostępna dla systemu Android

## <a name="next-steps"></a>Następne kroki

[Zagadnienia dotyczące platforma uniwersalna systemu Windows z MSAL.NET](msal-net-uwp-considerations.md)