---
title: Zagadnienia dotyczące systemu iOS platformy Xamarin (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o zagadnieniach dotyczących korzystania z systemu Xamarin iOS z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262713"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Zagadnienia dotyczące korzystania z systemu Xamarin iOS z MSAL.NET
Korzystając z biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) w systemie Xamarin iOS, należy: 

- Zastądnik `OpenUrl` i `AppDelegate`zaimplementuj funkcję w pliku .
- Włącz grupy pęku kluczy.
- Włącz udostępnianie pamięci podręcznej tokenów.
- Włącz dostęp do pęku kluczy.
- Poznaj znane problemy z systemem iOS 12 i uwierzytelnianiem.

## <a name="implement-openurl"></a>Implementowanie OpenUrl

Zastąpić `OpenUrl` metodę `FormsApplicationDelegate` klasy pochodnej `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`i wywołać . Oto przykład:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Wykonaj również następujące zadania: 
* Zdefiniuj schemat adresu URL.
* Wymagaj uprawnień do wywoływania innej aplikacji przez aplikację.
* Mieć określony formularz dla adresu URL przekierowania.
* Zarejestruj adres URL przekierowania w [witrynie Azure portal](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Włączanie dostępu do pęku kluczy

Aby włączyć dostęp do pęku kluczy, upewnij się, że aplikacja ma grupę dostępu do pęku kluczy. Grupę dostępu pęku kluczy można ustawić podczas `WithIosKeychainSecurityGroup()` tworzenia aplikacji przy użyciu interfejsu API.

Aby korzystać z pamięci podręcznej i logowania jednokrotnego (SSO), należy ustawić grupę dostępu pęku kluczy do tej samej wartości we wszystkich aplikacjach.

W tym przykładzie instalatora używa msal 4.x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Włącz również dostęp do `Entitlements.plist` pęku kluczy w pliku. Użyj następującej grupy dostępu lub własnej grupy dostępu.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Podczas korzystania `WithIosKeychainSecurityGroup()` z interfejsu API usługa MSAL automatycznie dołącza grupę zabezpieczeń do końca`AppIdentifierPrefix` *identyfikatora zespołu* aplikacji ( ). MSAL dodaje grupy zabezpieczeń, ponieważ podczas tworzenia aplikacji w xcode, zrobi to samo. Dlatego uprawnienia w `Entitlements.plist` pliku muszą zawierać `$(AppIdentifierPrefix)` się przed grupą dostępu pęku kluczy.

Aby uzyskać więcej informacji, zobacz [dokumentację uprawnień systemu iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Włączanie udostępniania pamięci podręcznej tokenów w aplikacjach systemu iOS

Począwszy od msal 2.x, można określić grupę dostępu pęku kluczy do utrwalenia pamięci podręcznej tokenu w wielu aplikacjach. To ustawienie umożliwia współużytkowanie pamięci podręcznej tokenu między kilkoma aplikacjami, które mają tę samą grupę dostępu do pęku kluczy. Środki pieniężne tokenu można udostępniać [aplikacjom ADAL.NET,](https://aka.ms/adal-net) MSAL.NET aplikacji Xamarin.iOS i natywnych aplikacjach systemu iOS opracowanych w [pliku ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) lub [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Udostępniając pamięć podręczną tokenów, zezwalasz na logowanie jednokrotne (Logowanie jednokrotne) między wszystkimi aplikacjami, które używają tej samej grupy dostępu do pęku kluczy.

Aby włączyć udostępnianie tej `WithIosKeychainSecurityGroup()` pamięci podręcznej, użyj metody, aby ustawić grupę dostępu pęku kluczy do tej samej wartości we wszystkich aplikacjach, które współużytkują tę samą pamięć podręczną. Pierwszy przykład kodu w tym artykule pokazuje, jak używać metody.

Wcześniej w tym artykule dowiedziałeś `$(AppIdentifierPrefix)` się, że `WithIosKeychainSecurityGroup()` msal dodaje za każdym razem, gdy używasz interfejsu API. MSAL dodaje ten element, `AppIdentifierPrefix` ponieważ identyfikator zespołu zapewnia, że tylko aplikacje, które są przez tego samego wydawcę mogą współużytkować dostęp do pęku kluczy.

> [!NOTE]
> Właściwość `KeychainSecurityGroup` jest przestarzała.
> 
> Począwszy od MSAL 2.x deweloperzy `TeamId` zostali zmuszeni do `KeychainSecurityGroup` uwzględnienia prefiksu podczas używanego właściwości. Ale począwszy od MSAL 2.7.x, `iOSKeychainSecurityGroup` podczas korzystania z `TeamId` nowej właściwości, MSAL rozpoznaje prefiks podczas wykonywania. Korzystając z tej właściwości, nie `TeamId` należy dołączać prefiksu w wartości. Prefiks nie jest wymagany.
>
> Ponieważ `KeychainSecurityGroup` właściwość jest przestarzała, należy użyć `iOSKeychainSecurityGroup` właściwości.

### <a name="use-microsoft-authenticator"></a>Korzystanie z programu Microsoft Authenticator

Aplikacja może używać programu Microsoft Authenticator jako brokera, aby włączyć:

- **Logowanie sytowe:** Po włączeniu logowania sytego użytkownicy nie muszą logować się do każdej aplikacji.
- **Identyfikacja urządzenia**: Uwierzytelnianie za pomocą funkcji identyfikacji urządzenia umożliwia dostęp do certyfikatu urządzenia. Ten certyfikat jest tworzony na urządzeniu, gdy jest połączony z miejscem pracy. Aplikacja będzie gotowa, jeśli administratorzy dzierżawy włączyć dostęp warunkowy związany z urządzeniami.
- **Weryfikacja identyfikacji aplikacji:** Gdy aplikacja wywołuje brokera, przekazuje adres URL przekierowania. Broker weryfikuje adres URL przekierowania.

Aby uzyskać szczegółowe informacje dotyczące włączania brokera, zobacz [Korzystanie z usługi Microsoft Authenticator lub Microsoft Intune Company Portal w aplikacjach xamarin iOS i Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Znane problemy z systemem iOS 12 i uwierzytelnianiem
Firma Microsoft wydała [poradnik zabezpieczeń](https://github.com/aspnet/AspNetCore/issues/4647) dotyczący niezgodności między systemem iOS 12 a niektórymi typami uwierzytelniania. Niezgodność przerywa logowania społeczne, WSFed i OIDC. Poradnik zabezpieczeń pomaga deweloperom zrozumieć, jak usunąć ograniczenia zabezpieczeń ASP.NET z ich aplikacji, aby były zgodne z systemem iOS 12.  

Podczas tworzenia aplikacji MSAL.NET w układzie Xamarin iOS, może pojawić się nieskończona pętla podczas próby zalogowania się do witryn sieci Web z systemu iOS 12. To zachowanie jest podobne do tego [problemu ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Może również wystąpić przerwa w ASP.NET core oidc uwierzytelniania z iOS 12 Safari. Aby uzyskać więcej informacji, zobacz ten [problem z webkitem](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat właściwości systemu Xamarin iOS, zobacz akapit [zagadnień specyficznych dla systemu iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) w pliku README.md następującego przykładu:

Sample | Platforma | Opis
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Uniwersalna platforma systemu Windows (UWP) | Prosta aplikacja Xamarin Forms, która pokazuje, jak używać usługi MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pośrednictwem punktu końcowego usługi Azure AD 2.0. Aplikacja pokazuje również, jak używać tokenu wynikowego, aby uzyskać dostęp do programu Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->