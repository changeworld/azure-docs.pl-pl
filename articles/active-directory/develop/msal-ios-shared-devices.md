---
title: Tryb urządzenia udostępnionego dla urządzeń z systemem iOS
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się, jak włączyć tryb urządzenia udostępnionego, aby zezwolić pracownikom pierwszej linii na udostępnianie urządzenia z systemem iOS
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550255"
---
# <a name="shared-device-mode-for-ios-devices"></a>Tryb urządzenia udostępnionego dla urządzeń z systemem iOS

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pracownicy pierwszej linii, tacy jak pracownicy handlu detalicznego, członkowie załogi lotniczej i pracownicy obsługi terenowej, często używają współdzielonego urządzenia mobilnego do wykonywania swojej pracy. Te udostępnione urządzenia mogą stwarzać zagrożenia bezpieczeństwa, jeśli użytkownicy celowo lub nie udostępniają swoich haseł lub kodów PIN w celu uzyskania dostępu do danych klienta i firmy na udostępnionym urządzeniu.

Tryb urządzenia udostępnionego umożliwia skonfigurowanie urządzenia z systemem iOS 13 lub nowszego w celu łatwiejszego i bezpieczniejszego udostępniania przez pracowników. Pracownicy mogą szybko logować się i uzyskiwać dostęp do informacji o klientach. Po zakończeniu zmiany lub zadania mogą wylogować się z urządzenia i jest ono natychmiast gotowe do użycia przez następnego pracownika.

Tryb urządzenia udostępnionego zapewnia również zarządzanie urządzeniem z pomocą tożsamości firmy Microsoft.

Ta funkcja używa [aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) do zarządzania użytkownikami na urządzeniu i rozpowszechniania [wtyczki SSO firmy Microsoft Enterprise dla urządzeń Apple.](apple-sso-plugin.md)

## <a name="create-a-shared-device-mode-app"></a>Tworzenie udostępnionej aplikacji trybu urządzenia

Aby utworzyć aplikację trybu urządzenia udostępnionego, deweloperzy i administratorzy urządzeń w chmurze współpracują ze sobą:

1. **Deweloperzy aplikacji** piszą aplikację z jednym kontem (aplikacje z wieloma kontami nie są obsługiwane w trybie urządzenia udostępnionego) i piszą kod do obsługi rzeczy, takich jak wylogowywanie się z urządzenia udostępnionego.

1. **Administratorzy urządzeń** przygotowują urządzenie do udostępniania przy użyciu dostawcy zarządzania urządzeniami przenośnymi (MDM), takiego jak Microsoft Intune, do zarządzania urządzeniami w organizacji. Narzędzie MDM wypycha aplikację Microsoft Authenticator do urządzeń i włącza "Tryb udostępniony" dla każdego urządzenia za pośrednictwem aktualizacji profilu na urządzeniu. To ustawienie trybu udostępnionego zmienia zachowanie obsługiwanych aplikacji na urządzeniu. Ta konfiguracja dostawcy mdm ustawia tryb urządzenia udostępnionego dla urządzenia i włącza [wtyczkę rejestracji sso firmy Microsoft Enterprise dla urządzeń apple,](apple-sso-plugin.md) która jest wymagana w trybie urządzenia współużytkującego.

1. [**Wymagane tylko podczas podglądu publicznego**] Użytkownik z rolą [Administratora urządzeń w chmurze](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) musi następnie uruchomić [aplikację Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) i dołączyć do urządzenia w organizacji.

    Aby skonfigurować członkostwo ról organizacji w witrynie Azure Portal: Administrator urządzeń w **chmurze usługi Azure Active Directory** > **i administratorzy** > urządzeń**w chmurze administratora urządzeń w chmurze**

Poniższe sekcje ułatwiają aktualizowanie aplikacji w celu obsługi trybu udostępnionego urządzenia.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Włącz tryb urządzenia współdzielonego & rozszerzeniem rejestracji rejestracji rejestracji.

> [!NOTE]
> Poniższy krok jest wymagany tylko podczas publicznej wersji zapoznawczej.

Urządzenie musi być skonfigurowane do obsługi trybu urządzenia współdzielonego. Musi mieć zainstalowany system iOS 13+ i być zarejestrowanym w mdm. Konfiguracja MDM musi również włączyć [wtyczkę SSO firmy Microsoft Enterprise dla urządzeń Apple.](apple-sso-plugin.md) Aby dowiedzieć się więcej o rozszerzeniach SSO, zobacz [wideo firmy Apple](https://developer.apple.com/videos/play/tech-talks/301/).

1. W portalu konfiguracji usługi Intune poinformuj urządzenie, aby włączyć [wtyczkę SSO firmy Microsoft Enterprise dla urządzeń apple](apple-sso-plugin.md) z następującą konfiguracją:

    - **Typ**: Przekierowanie
    - **Identyfikator rozszerzenia:** com.microsoft.azureauthenticator.ssoextension
    - **Identyfikator zespołu**: SGGM6D27TK
    - **Adresy URL**:https://login.microsoftonline.com
    - Dodatkowe dane do skonfigurowania:
      - Klucz: sharedDeviceMode
      - Typ: Boolean
      - Wartość: Prawda

    Aby uzyskać więcej informacji na temat konfigurowania usługi Intune, zobacz [dokumentację konfiguracji usługi Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

1. Następnie skonfiguruj urządzenie MDM, aby wypchnąć aplikację Microsoft Authenticator do urządzenia za pośrednictwem profilu MDM.

    Ustaw następujące opcje konfiguracji, aby włączyć tryb urządzenia udostępnionego:

    - Konfiguracja 1:
      - Klucz: sharedDeviceMode
      - Typ: Boolean
      - Wartość: Prawda

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Modyfikowanie aplikacji systemu iOS w celu obsługi trybu urządzenia udostępnionego

Użytkownicy są zależni od Ciebie, aby upewnić się, że ich dane nie są wyciekane do innego użytkownika. W poniższych sekcjach zawierają przydatne sygnały wskazujące aplikacji, że nastąpiła zmiana i powinny być obsługiwane.

Użytkownik jest odpowiedzialny za sprawdzanie stanu użytkownika na urządzeniu za każdym razem, gdy aplikacja jest używana, a następnie czyszczenie danych poprzedniego użytkownika. Obejmuje to, jeśli jest ponownie załadowany z tła w wielozadaniowości.

W przypadku zmiany użytkownika należy upewnić się, że zarówno dane poprzedniego użytkownika są czyszczone, jak i że wszystkie buforowane dane wyświetlane w aplikacji są usuwane. Zalecamy, aby Ty i Twoja firma przeprowadziliśmy proces weryfikacji zabezpieczeń po zaktualizowaniu aplikacji w celu obsługi trybu udostępnionego urządzenia.

### <a name="detect-shared-device-mode"></a>Wykrywanie trybu współdzielonego urządzenia

Wykrywanie trybu udostępnionego urządzenia jest ważne dla aplikacji. Wiele aplikacji będzie wymagać zmiany w ich środowiska użytkownika (UX), gdy aplikacja jest używana na urządzeniu udostępnionym. Na przykład aplikacja może mieć funkcję "Zarejestruj się", która nie jest odpowiednia dla pracownika pierwszej linii, ponieważ prawdopodobnie mają już konto. Można również dodać dodatkowe zabezpieczenia do obsługi danych aplikacji, jeśli jest w trybie urządzenia udostępnionego.

Użyj `getDeviceInformationWithParameters:completionBlock:` interfejsu API `MSALPublicClientApplication` w celu określenia, czy aplikacja jest uruchomiona na urządzeniu w trybie urządzenia udostępnionego.

Poniższe fragmenty kodu pokazują przykłady `getDeviceInformationWithParameters:completionBlock:` korzystania z interfejsu API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Obiektowy C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Pobierz zalogowanego użytkownika i określ, czy użytkownik zmienił się na urządzeniu

Inną ważną częścią obsługi trybu współużytkowania urządzenia jest określenie stanu użytkownika na urządzeniu i wyczyszczenie danych aplikacji, jeśli użytkownik zmienił lub jeśli w ogóle nie ma użytkownika na urządzeniu. Użytkownik jest odpowiedzialny za zapewnienie, że dane nie są wyciekane do innego użytkownika.

Za pomocą `getCurrentAccountWithParameters:completionBlock:` interfejsu API można zbadać aktualnie zalogowane konto na urządzeniu.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Obiektowy C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Globalnie zaloguj się do użytkownika

Gdy urządzenie jest skonfigurowane jako urządzenie udostępnione, `acquireTokenWithParameters:completionBlock:` aplikacja może wywołać interfejs API, aby zalogować się na koncie. Konto będzie dostępne globalnie dla wszystkich kwalifikujących się aplikacji na urządzeniu po zalogowaniu się pierwszej aplikacji na koncie.

#### <a name="objective-c"></a>Obiektowy C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Globalnie wyloguj użytkownika

Poniższy kod usuwa zalogowane konto i czyści tokeny buforowane nie tylko z aplikacji, ale także z urządzenia, które jest w trybie urządzenia udostępnionego. Nie wyczyszcza jednak *danych* z aplikacji. Należy wyczyścić dane z aplikacji, a także wyczyścić wszystkie buforowane dane, które aplikacja może być wyświetlana użytkownikowi.

#### <a name="clear-browser-state"></a>Wyczyść stan przeglądarki

> [!NOTE]
> Poniższy krok jest wymagany tylko podczas publicznej wersji zapoznawczej.

W tej publicznej wersji zapoznawczej [wtyczka SSO firmy Microsoft Enterprise dla urządzeń Apple](apple-sso-plugin.md) jest czyszczyna tylko dla aplikacji. Nie jest jasne, stan w przeglądarce Safari. Zalecamy ręczne czyszczenie sesji przeglądarki, aby upewnić się, że żadne ślady stanu użytkownika nie są pozostawione. Możesz skorzystać z `signoutFromBrowser` opcjonalnej właściwości pokazanej poniżej, aby wyczyścić wszelkie pliki cookie. Spowoduje to krótkie uruchomienie przeglądarki na urządzeniu.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Obiektowy C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić tryb urządzenia udostępnionego w akcji, poniższy przykład kodu w usłudze GitHub zawiera przykład uruchamiania aplikacji Procesowy linii pierwszej na urządzeniu z systemem iOS w trybie urządzenia udostępnionego:

[Przykładowa interfejs API programu MsAL iOS Swift Microsoft Graph](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
