---
title: Uwagi dotyczące platformy Xamarin iOS (Microsoft Authentication Library for .NET) | Azure
description: Informacje o określonych kwestiach dotyczących korzystania z platformy Xamarin iOS z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 906f2fc8cdac31922e6e93526f65577fe76c4b9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532374"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Uwagi dotyczące platformy Xamarin dla systemu iOS z usługą MSAL.NET
W oprogramowaniu Xamarin iOS istnieje kilka kwestii, które należy wziąć pod uwagę podczas korzystania z usługi MSAL.NET

- [Znane problemy z systemem iOS 12 i uwierzytelnianiem](#known-issues-with-ios-12-and-authentication)
- [Przesłoń i zaimplementuj `OpenUrl` funkcję w`AppDelegate`](#implement-openurl)
- [Włącz grupy pęku kluczy](#enable-keychain-access)
- [Włącz udostępnianie pamięci podręcznej tokenu](#enable-token-cache-sharing-across-ios-applications)
- [Włącz dostęp do łańcucha kluczy](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Znane problemy z systemem iOS 12 i uwierzytelnianiem
Firma Microsoft udostępniła poradnik dotyczący [zabezpieczeń](https://github.com/aspnet/AspNetCore/issues/4647) , który zawiera informacje o niezgodności między iOS12 i niektórych rodzajów uwierzytelniania. Niezgodność powoduje przerwanie logowania społecznościowego, WSFed i OIDC. Ten poradnik zawiera również wskazówki dotyczące tego, co deweloperzy mogą zrobić, aby usunąć bieżące ograniczenia zabezpieczeń dodane przez ASP.NET do aplikacji, aby były zgodne z iOS12.  

Podczas tworzenia aplikacji MSAL.NET w systemie Xamarin iOS może wystąpić nieskończona pętla podczas próby zalogowania się do witryn sieci Web z systemu iOS 12 (podobnie jak w przypadku tego [problemu biblioteki ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)). 

Możesz również zobaczyć przerwy w ASP.NET Core OIDC uwierzytelniania z systemem iOS 12 Safari, zgodnie z opisem w tym [problemie WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementuj OpenUrl

Najpierw należy zastąpić `OpenUrl` metodę `FormsApplicationDelegate` klasy pochodnej i wywołania `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Musisz również zdefiniować schemat adresu URL, wymagać uprawnień aplikacji do wywoływania innej aplikacji, mieć określony formularz dla adresu URL przekierowania i zarejestrować ten adres URL przekierowania w [Azure Portal](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Włącz dostęp do łańcucha kluczy

Aby włączyć dostęp do łańcucha kluczy, aplikacja musi mieć grupę dostępu łańcucha kluczy.
Grupę dostępu łańcucha kluczy można skonfigurować przy użyciu `WithIosKeychainSecurityGroup()` interfejsu API podczas tworzenia aplikacji, jak pokazano poniżej:

Aby włączyć logowanie jednokrotne, należy ustawić `PublicClientApplication.iOSKeychainSecurityGroup` właściwość na taką samą wartość we wszystkich aplikacjach.

Przykładem tego użycia jest MSAL v3. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Uprawnienia. plist należy zaktualizować tak, aby wyglądały jak w poniższym fragmencie kodu XML:

Ta zmiana stanowi *uzupełnienie* dostępu do łańcucha kluczy w `Entitlements.plist` pliku przy użyciu poniższej grupy dostępu lub własnego:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

Przykładem tego użycia jest MSAL v4. x:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

Gdy korzystasz `WithIosKeychainSecurityGroup()` z interfejsu API, MSAL automatycznie dołączy grupę zabezpieczeń na końcu "Identyfikator zespołu" (AppIdentifierPrefix) aplikacji, ponieważ podczas kompilowania aplikacji przy użyciu Xcode zostanie ona taka sama. [Więcej informacji można znaleźć w dokumentacji dotyczącej uprawnień systemu iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). Dlatego należy zaktualizować uprawnienia w celu uwzględnienia $ (AppIdentifierPrefix) przed grupą dostępu pęku kluczy w uprawnieniach. plist.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Włączanie udostępniania pamięci podręcznej tokenów w aplikacjach dla systemu iOS

Z MSAL 2. x można określić grupę dostępu łańcucha kluczy, która ma być używana do utrwalania pamięci podręcznej tokenów w wielu aplikacjach. To ustawienie umożliwia udostępnienie pamięci podręcznej tokenom między kilkoma aplikacjami mającymi tę samą grupę dostępu pęku kluczy, łącznie z tymi, które opracowano za pomocą aplikacji [ADAL.NET](https://aka.ms/adal-net), MSAL.NET Xamarin. iOS oraz natywnych aplikacji systemu iOS utworzonych przy użyciu [ ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) lub [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Udostępnianie pamięci podręcznej tokenów umożliwia logowanie jednokrotne między wszystkimi aplikacjami, które korzystają z tej samej grupy dostępu pęku kluczy.

Aby włączyć udostępnianie pamięci podręcznej, należy ustawić metodę Użyj metody "WithIosKeychainSecurityGroup ()", aby ustawić grupę dostępu łańcucha kluczy na taką samą wartość we wszystkich aplikacjach korzystających z tej samej pamięci podręcznej, jak pokazano w powyższym przykładzie.

Wcześniej stwierdzono, że MSAL dodaliśmy $ (AppIdentifierPrefix) za każdym razem, gdy korzystasz z `WithIosKeychainSecurityGroup()` interfejsu API. Wynika to z faktu, że AppIdentifierPrefix lub "Identyfikator zespołu" służy do zapewnienia, że tylko aplikacje wykonane przez tego samego wydawcę mogą udostępniać dostęp do łańcucha kluczy.

#### <a name="note-keychainsecuritygroup-property-deprecated"></a>Uwaga: Właściwość KeychainSecurityGroup jest przestarzała

Wcześniej, od MSAL 2. x, deweloperzy byli zmuszeni do uwzględnienia prefiksu użytkownika przy użyciu `KeychainSecurityGroup` właściwości

W MSAL 2.7. x, gdy jest używana nowa `iOSKeychainSecurityGroup` właściwość, MSAL rozwiąże prefiks użytkownika w czasie wykonywania. W przypadku używania tej właściwości wartość nie powinna zawierać prefiksu użytkownika.

Użyj nowej `iOSKeychainSecurityGroup` właściwości, która nie wymaga od deweloperów podania użytkownika, ponieważ Poprzednia `KeychainSecurityGroup` właściwość jest już przestarzała.

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Przykład ilustrujący specyficzne właściwości platformy Xamarin dla systemu iOS

Więcej szczegółowych informacji znajduje się w ustępie [uwagi dotyczące określonych zagadnień dotyczących systemu iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) w następującym przykładowym pliku Readme.MD:

Próbka | Platforma | Opis
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, platformy UWP | Prosta aplikacja interfejsu Xamarin Forms, która przedstawia, jak używać MSAL do uwierzytelniania kont MSA i Azure AD za pośrednictwem punktu końcowego usługi Azure AD V 2.0, a następnie uzyskiwać dostęp do Microsoft Graph przy użyciu tokenu z wynikiem.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
