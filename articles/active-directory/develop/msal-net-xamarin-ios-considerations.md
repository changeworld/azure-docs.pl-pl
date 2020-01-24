---
title: Uwagi dotyczące platformy Xamarin iOS (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informacje o określonych kwestiach dotyczących korzystania z platformy Xamarin iOS z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 599b8a3fdbad5747b0b303c71aeef084d04db6df
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694924"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Uwagi dotyczące platformy Xamarin dla systemu iOS z usługą MSAL.NET
W oprogramowaniu Xamarin iOS istnieje kilka kwestii, które należy wziąć pod uwagę podczas korzystania z usługi MSAL.NET

- [Znane problemy z systemem iOS 12 i uwierzytelnianiem](#known-issues-with-ios-12-and-authentication)
- [Przesłoń i zaimplementuj funkcję `OpenUrl` w `AppDelegate`](#implement-openurl)
- [Włącz grupy pęku kluczy](#enable-keychain-access)
- [Włącz udostępnianie pamięci podręcznej tokenu](#enable-token-cache-sharing-across-ios-applications)
- [Włącz dostęp do łańcucha kluczy](#enable-keychain-access)

## <a name="implement-openurl"></a>Implementuj OpenUrl

Najpierw należy zastąpić metodę `OpenUrl` klasy pochodnej `FormsApplicationDelegate` i `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`wywoływania.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Musisz również zdefiniować schemat adresu URL, wymagać uprawnień aplikacji do wywoływania innej aplikacji, mieć określony formularz dla adresu URL przekierowania i zarejestrować ten adres URL przekierowania w [Azure Portal](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Włącz dostęp do łańcucha kluczy

Aby włączyć dostęp do łańcucha kluczy, aplikacja musi mieć grupę dostępu łańcucha kluczy.
Grupę dostępu łańcucha kluczy można skonfigurować przy użyciu interfejsu API `WithIosKeychainSecurityGroup()` podczas tworzenia aplikacji, jak pokazano poniżej:

Aby korzystać z pamięci podręcznej i logowania jednokrotnego, należy ustawić grupę dostępu łańcucha kluczy na taką samą wartość we wszystkich aplikacjach.

Przykładem tego użycia jest MSAL v4. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Ta zmiana stanowi *uzupełnienie* dostępu do łańcucha kluczy w pliku `Entitlements.plist` przy użyciu poniższej grupy dostępu lub własnego:

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

W przypadku korzystania z interfejsu API `WithIosKeychainSecurityGroup()` program MSAL automatycznie dołącza grupę zabezpieczeń na końcu *identyfikatora zespołu* aplikacji (AppIdentifierPrefix), ponieważ w przypadku kompilowania aplikacji przy użyciu Xcode zostanie ona taka sama. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą uprawnień systemu iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). Dlatego, że uprawnienia muszą zawierać `$(AppIdentifierPrefix)` przed grupą dostępu łańcucha kluczy w `Entitlements.plist`.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Włączanie udostępniania pamięci podręcznej tokenów w aplikacjach dla systemu iOS

Z MSAL 2. x można określić grupę dostępu łańcucha kluczy, która ma być używana do utrwalania pamięci podręcznej tokenów w wielu aplikacjach. To ustawienie umożliwia współużytkowanie pamięci podręcznej tokenów między kilkoma aplikacjami mającymi tę samą grupę dostępu pęku kluczy, łącznie z tymi, które opracowano za pomocą aplikacji [ADAL.NET](https://aka.ms/adal-net), MSAL.NET Xamarin. iOS i natywnych aplikacji systemu iOS opracowanych przy użyciu [biblioteki ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) lub [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Udostępnianie pamięci podręcznej tokenów umożliwia logowanie jednokrotne między wszystkimi aplikacjami, które korzystają z tej samej grupy dostępu pęku kluczy.

Aby włączyć udostępnianie pamięci podręcznej, należy ustawić metodę Użyj metody "WithIosKeychainSecurityGroup ()", aby ustawić grupę dostępu łańcucha kluczy na taką samą wartość we wszystkich aplikacjach korzystających z tej samej pamięci podręcznej, jak pokazano w powyższym przykładzie.

Wcześniej stwierdzono, że MSAL dodaliśmy $ (AppIdentifierPrefix) przy każdym użyciu interfejsu API `WithIosKeychainSecurityGroup()`. Wynika to z faktu, że AppIdentifierPrefix lub "Identyfikator zespołu" służy do zapewnienia, że tylko aplikacje wykonane przez tego samego wydawcę mogą udostępniać dostęp do łańcucha kluczy.

> [!NOTE]
> **Właściwość `KeychainSecurityGroup` jest przestarzała.**
> 
> Wcześniej, od MSAL 2. x, deweloperzy byli zmuszeni do uwzględnienia prefiksu użytkownika przy użyciu właściwości `KeychainSecurityGroup`.
>
>  W MSAL 2.7. x przy użyciu nowej właściwości `iOSKeychainSecurityGroup` MSAL rozpozna prefiks użytkownika w czasie wykonywania. W przypadku używania tej właściwości wartość nie powinna zawierać prefiksu użytkownika.
>  Użyj nowej właściwości `iOSKeychainSecurityGroup`, która nie wymaga podania użytkownika, ponieważ Poprzednia Właściwość `KeychainSecurityGroup` jest już przestarzała.

### <a name="use-microsoft-authenticator"></a>Użyj Microsoft Authenticator

Aplikacja może używać Microsoft Authenticator (Broker) do włączania:

- Logowanie jednokrotne (SSO). Użytkownicy nie muszą logować się do poszczególnych aplikacji.
- Identyfikacja urządzenia. Przez uzyskanie dostępu do certyfikatu urządzenia, który został utworzony na urządzeniu, gdy było dołączone do miejsca pracy. Aplikacja będzie gotowa, jeśli Administratorzy dzierżawy będą mogli korzystać z dostępu warunkowego do urządzeń.
- Weryfikacja identyfikacji aplikacji. Gdy aplikacja wywołuje brokera, przekazuje jego adres URL przekierowania i weryfikuje go.

Aby uzyskać szczegółowe informacje na temat włączania brokera, zobacz [używanie Microsoft Authenticator lub Microsoft Intune portal firmy w aplikacjach Xamarin iOS i Android](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Przykład ilustrujący specyficzne właściwości platformy Xamarin dla systemu iOS

Więcej szczegółowych informacji znajduje się w ustępie [uwagi dotyczące określonych zagadnień dotyczących systemu iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) w następującym przykładowym pliku Readme.MD:

Przykład | Platforma | Opis
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, platformy UWP | Prosta aplikacja interfejsu Xamarin Forms, która przedstawia, jak używać MSAL do uwierzytelniania kont MSA i Azure AD za pośrednictwem punktu końcowego usługi Azure AD V 2.0, a następnie uzyskiwać dostęp do Microsoft Graph przy użyciu tokenu z wynikiem.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->

## <a name="known-issues-with-ios-12-and-authentication"></a>Znane problemy z systemem iOS 12 i uwierzytelnianiem
Firma Microsoft udostępniła poradnik dotyczący [zabezpieczeń](https://github.com/aspnet/AspNetCore/issues/4647) , który zawiera informacje o niezgodności między iOS12 i niektórych rodzajów uwierzytelniania. Niezgodność powoduje przerwanie logowania społecznościowego, WSFed i OIDC. Ten poradnik zawiera również wskazówki dotyczące tego, co deweloperzy mogą zrobić, aby usunąć bieżące ograniczenia zabezpieczeń dodane przez ASP.NET do aplikacji, aby były zgodne z iOS12.  

Podczas tworzenia aplikacji MSAL.NET w systemie Xamarin iOS może wystąpić nieskończona pętla podczas próby zalogowania się do witryn sieci Web z systemu iOS 12 (podobnie jak w przypadku tego [problemu ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)). 

Możesz również zobaczyć przerwy w ASP.NET Core OIDC uwierzytelniania z systemem iOS 12 Safari, zgodnie z opisem w tym [problemie WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).
