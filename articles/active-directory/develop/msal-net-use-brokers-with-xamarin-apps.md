---
title: Używanie Microsoft Authenticator lub Microsoft Intune Portal firmy w aplikacjach Xamarin iOS i Android
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przeprowadzić migrację aplikacji platformy Xamarin dla systemu iOS, które mogą używać Microsoft Authenticator z biblioteki Azure AD Authentication Library for .NET (ADAL.NET) w bibliotece uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET)
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
ms.openlocfilehash: f2ae0d79076a0137b3756721675267d89990bf5a
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554040"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Używanie Microsoft Authenticator lub Microsoft Intune Portal firmy w aplikacjach platformy Xamarin

W systemach Android i iOS brokerzy takich jak Microsoft Authenticator lub Microsoft Intune Portal firmy Enable (tylko system Android):

- Logowanie jednokrotne (SSO). Użytkownicy nie muszą logować się do poszczególnych aplikacji.
- Identyfikacja urządzenia. Broker uzyskuje dostęp do certyfikatu urządzenia, który został utworzony na urządzeniu, gdy było dołączone do miejsca pracy.
- Weryfikacja identyfikacji aplikacji. Gdy aplikacja wywołuje brokera, przekazuje jego adres URL przekierowania i weryfikuje go.

Aby włączyć jedną z tych funkcji, deweloperzy aplikacji muszą używać `WithBroker()` parametru podczas wywoływania metody `PublicClientApplicationBuilder.CreateApplication`. `.WithBroker()` jest domyślnie ustawiona na wartość true. Deweloperzy muszą również wykonać kroki opisane tutaj dla aplikacji dla [systemu iOS](#brokered-authentication-for-ios) lub [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu iOS

Wykonaj następujące kroki, aby umożliwić aplikacji platformy Xamarin. iOS komunikację z aplikacją [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Krok 1. Włączanie obsługi brokera
Obsługa brokera jest włączana dla poszczególnych PublicClientApplication. Jest on domyślnie wyłączony. Użyj parametru `WithBroker()` (domyślnie ustawiona na true) podczas tworzenia PublicClientApplication za pomocą PublicClientApplicationBuilder.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2. Aktualizacja AppDelegate do obsługi wywołania zwrotnego
Gdy Biblioteka Microsoft Authentication Library for .NET (MSAL.NET) wywołuje brokera, Broker z kolei wywołuje z powrotem do aplikacji za pomocą metody `OpenUrl` klasy `AppDelegate`. Ponieważ MSAL czeka na odpowiedź z brokera, aplikacja musi współpracować, aby wywoływać MSAL.NET z powrotem. Aby włączyć tę współpracę, zaktualizuj plik `AppDelegate.cs`, aby zastąpić poniższą metodę.

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

Ta metoda jest wywoływana za każdym razem, gdy aplikacja jest uruchamiana. Jest on używany jako okazja do przetworzenia odpowiedzi z brokera i ukończenia procesu uwierzytelniania zainicjowanego przez MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3. Ustawianie elementu UIViewController ()
Nadal w `AppDelegate.cs`należy ustawić okno obiektu. Zwykle w przypadku platformy Xamarin iOS nie trzeba ustawiać okna obiektu. Aby wysyłać i odbierać odpowiedzi z brokera, potrzebujesz okna obiektu. 

W tym celu wykonaj dwie rzeczy. 
1. W `AppDelegate.cs`Ustaw `App.RootViewController` na nowy `UIViewController()`. To przypisanie pozwala upewnić się, że istnieje UIViewController z wywołaniem do brokera. Jeśli nie jest ustawiona poprawnie, może zostać wyświetlony następujący błąd: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. W wywołaniu AcquireTokenInteractive Użyj `.WithParentActivityOrWindow(App.RootViewController)` i przekaż odwołanie do okna obiektu, którego będziesz używać.

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

### <a name="step-4-register-a-url-scheme"></a>Krok 4. rejestrowanie schematu adresu URL
MSAL.NET używa adresów URL do wywołania brokera, a następnie zwrócenia odpowiedzi brokera z powrotem do aplikacji. Aby zakończyć rundy, zarejestruj schemat adresu URL aplikacji w pliku `Info.plist`.

Nazwa `CFBundleURLSchemes` musi zawierać `msauth.` jako prefiks, a następnie `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Na przykład:**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ten schemat adresu URL staje się częścią identyfikatora URI przekierowania, który jest używany do jednoznacznego identyfikowania aplikacji po odebraniu odpowiedzi z brokera.

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

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 5. Dodawanie identyfikatora brokera do sekcji LSApplicationQueriesSchemes
MSAL używa `–canOpenURL:`, aby sprawdzić, czy na urządzeniu zainstalowano brokera. W systemie iOS 9 Firma Apple zablokowała schematy, dla których aplikacja może wykonać zapytanie. 

Dodaj `msauthv2` do sekcji `LSApplicationQueriesSchemes` pliku `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirect-uri-in-the-application-portal"></a>Krok 6. Rejestrowanie identyfikatora URI przekierowania w portalu aplikacji
Użycie brokera dodaje dodatkowe wymagania dotyczące identyfikatora URI przekierowania. Identyfikator URI przekierowania _musi_ mieć następujący format:
```CSharp
$"msauth.{BundleId}://auth"
```
**Na przykład:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Zwróć uwagę, że identyfikator URI przekierowania pasuje do nazwy `CFBundleURLSchemes` zawartej w pliku `Info.plist`.

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 7. Upewnij się, że identyfikator URI przekierowania jest zarejestrowany w aplikacji

Ten identyfikator URI przekierowania musi być zarejestrowany w portalu rejestracji aplikacji (https://portal.azure.com) jako prawidłowy identyfikator URI przekierowania dla aplikacji. 

Portal udostępnia nowy portal rejestracji aplikacji w celu ułatwienia obliczenia identyfikatora URI odpowiedzi obsługiwanej przez brokera z identyfikatora pakietu.

1. W obszarze Rejestracja aplikacji wybierz pozycję **uwierzytelnianie** i wybierz opcję **Wypróbuj nowe środowisko**.

   ![Wypróbuj nowe środowisko rejestracji aplikacji](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Wybierz pozycję **Dodaj platformę**.

   ![Dodaj platformę](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Jeśli lista platform jest obsługiwana, wybierz pozycję **iOS**.

   ![Konfigurowanie systemu iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Wprowadź żądany identyfikator pakietu, a następnie wybierz pozycję **Konfiguruj**.

   ![Wprowadź identyfikator pakietu](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. Identyfikator URI przekierowania jest obliczany dla Ciebie.

   ![Kopiuj identyfikator URI przekierowania](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Uwierzytelnianie obsługiwane przez brokera dla systemu Android

MSAL.NET obsługuje tylko platformę Xamarin. iOS. Nie obsługuje jeszcze brokerów dla platformy Xamarin. Android.

Biblioteka MSAL systemu Android Native jest już obsługiwana. Aby uzyskać szczegółowe informacje, zobacz [uwierzytelnianie obsługiwane przez brokera w systemie Android](brokered-auth.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat zagadnień specyficznych dla platforma uniwersalna systemu Windows w MSAL.NET](msal-net-uwp-considerations.md).