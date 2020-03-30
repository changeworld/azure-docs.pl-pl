---
title: Wybierz odpowiedni protokół federacyjny dla aplikacji wielodostępnej
description: Wskazówki dotyczące integracji z usługą Azure Active Directory dla niezależnych dostawców oprogramowania
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443371"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Wybierz odpowiedni protokół federacyjny dla aplikacji wielodostępnej

Podczas tworzenia oprogramowania jako usługi (SaaS) należy wybrać protokół federacyjny, który najlepiej odpowiada twoim i twoim potrzebom klientów. Ta decyzja jest oparta na platformie deweloperskiej i chęci integracji z danymi dostępnymi w ekosystemie usługi Office 365 i usługi Azure AD klientów.

Zobacz pełną listę [protokołów dostępnych dla integracji z protokołem SSO](what-is-single-sign-on.md) z usługą Azure Active Directory.
W poniższej tabeli porównano 
* Otwórz uwierzytelnianie 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* Język znaczników oświadczeń zabezpieczeń (SAML)
* Federacja usług sieci Web (WSFed)

| Możliwości| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Logowanie jednokrotne oparte na sieci Web| √| √ |
| Wylogowywanie jednokrotne oparte na sieci Web| √| √ |
| Logowanie jednokrotne oparte na urządzeniach mobilnych| √| √* |
| Wylogowywanie jednokrotne na urządzeniach mobilnych| √| √* |
| Zasady dostępu warunkowego dla aplikacji mobilnych| √| X |
| Bezproblemowe środowisko usługi MFA dla aplikacji mobilnych| √| X |
| Dostęp do programu Microsoft Graph| √| X |

* Możliwe, ale firma Microsoft nie udostępnia przykładów ani wskazówek.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 i Open ID Connect

OAuth 2.0 jest [standardowym](https://oauth.net/2/) protokołem autoryzacji. OIDC (OpenID Connect) to [standardowa](https://openid.net/connect/) warstwa uwierzytelniania tożsamości oparta na protokole OAuth 2.0.

### <a name="benefits"></a>Korzyści

Firma Microsoft zaleca używanie OIDC/OAuth 2.0, ponieważ mają one wbudowane uwierzytelnianie i autoryzację do protokołów. Z SAML, należy dodatkowo zaimplementować autoryzacji.

Autoryzacja związana z tymi protokołami umożliwia aplikacji dostęp do bogatych danych użytkownika i organizacji oraz ich integrację za pośrednictwem interfejsu API programu Microsoft Graph.

Korzystanie z OAuth 2.0 i OIDC upraszcza środowisko użytkownika końcowego klientów podczas przyjmowania swoidłu sytego dla aplikacji. Można łatwo zdefiniować niezbędne zestawy uprawnień, które są następnie automatycznie reprezentowane do administratora lub użytkownika końcowego zgody.

Ponadto za pomocą tych protokołów umożliwia klientom za pomocą funkcji dostępu warunkowego i zasad usługi MFA do kontrolowania dostępu do aplikacji. Firma Microsoft udostępnia biblioteki i [przykłady kodu na wielu platformach technologicznych,](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) aby ułatwić tworzenie programów.  

### <a name="implementation"></a>Wdrażanie

Rejestracja aplikacji w firmie Microsoft Identity, która jest dostawcą OAuth 2.0. Następnie można również zarejestrować aplikację opartą na OAuth 2.0 z dowolnym innym dostawcą tożsamości, z którym chcesz zintegrować. 

Aby uzyskać informacje dotyczące rejestrowania aplikacji i implementowania tych protokołów logowania typu SSO do aplikacji sieci Web, zobacz [Autoryzowanie dostępu do aplikacji sieci Web przy użyciu openid connect i usługi Azure Active Directory](../develop/sample-v2-code.md).  Aby uzyskać informacje na temat implementacji tych protokołów logowania typu SSO w aplikacjach mobilnych, zobacz następujące informacje: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Platforma uniwersalna systemu Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 i WSFed

Język znaczników oświadczeń zabezpieczeń (SAML) jest zwykle używany w aplikacjach sieci web. Zobacz, [jak platforma Azure używa protokołu SAML w](../develop/active-directory-saml-protocol-reference.md) celu zapoznania się z omówieniem. 

Federacja usług sieci Web (WSFed) jest [standardem branżowym](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) używanym zazwyczaj dla aplikacji sieci web opracowanych przy użyciu platformy .Net.

### <a name="benefits"></a>Korzyści

SAML 2.0 jest dojrzałym standardem, a większość platform technologicznych obsługuje biblioteki open source dla SAML 2.0. Można udostępnić klientom interfejs administracyjny do konfigurowania identyfikatora SAML. Mogą skonfigurować sytą usługę SAML dla usługi Microsoft Azure AD i dowolnego innego dostawcy tożsamości obsługującego saml 2

### <a name="trade-offs"></a>Kompromisy

W przypadku korzystania z protokołów SAML 2.0 lub WSFed dla aplikacji mobilnych niektóre zasady dostępu warunkowego, w tym uwierzytelnianie wieloskładnikowe (MFA), będą miały obniżone środowisko. Ponadto jeśli chcesz uzyskać dostęp do programu Microsoft Graph, należy zaimplementować autoryzację za pośrednictwem OAuth 2.0 do generowania niezbędnych tokenów. 

### <a name="implementation"></a>Wdrażanie

Firma Microsoft nie udostępnia bibliotek dla implementacji SAML ani nie zaleca określonych bibliotek. Dostępnych jest wiele bibliotek typu open source.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>Interfejs syt lub za pomocą interfejsu API odpoczynku wykresu firmy Microsoft 

Microsoft Graph to sieć szkieletowa danych we wszystkich usługach Microsoft 365, w tym w usłudze Office 365, Windows 10 i Enterprise Mobility and Security oraz w dodatkowych produktach, takich jak Dynamics 365. Obejmuje to podstawowe schematy jednostek, takich jak Użytkownicy, Grupy, Kalendarz, Poczta, Pliki i inne, które zwiększają produktywność użytkowników. Program Microsoft Graph oferuje trzy interfejsy dla deweloperów interfejsu API opartego na rest, połączenia danych programu Microsoft Graph i łączników, które umożliwiają deweloperom dodawanie własnych danych do programu Microsoft Graph.  

Korzystanie z dowolnego z powyższych protokołów dla aplikacji SSO umożliwia aplikacji dostęp do danych rozszerzonych dostępnych za pośrednictwem interfejsu API REST programu Microsoft Graph. Dzięki temu klienci mogą uzyskać większą wartość z inwestycji w usługę Microsoft 365. Na przykład aplikacja może wywołać interfejs API programu Microsoft Graph w celu zintegrowania z wystąpieniem usługi Office 365 klientów oraz elementami pakietu Microsoft Office i programu SharePoint użytkowników tabletu Surface w aplikacji. 

Jeśli używasz Open ID Connect do uwierzytelniania, środowisko programistyczne jest bezproblemowe, ponieważ będzie używany OAuth2, podstawa Open ID Connect, do uzyskiwania tokenów może służyć do wywoływania interfejsów API programu Microsoft Graph. Jeśli aplikacja używa SAML lub WSFed, należy dodać dodatkowy kod w aplikacji, aby uzyskać te OAuth2 do uzyskania tokenów wymaganych do wywoływania interfejsów API programu Microsoft Graph. 

## <a name="next-steps"></a>Następne kroki

[Włączanie logowania jednokrotnego dla aplikacji wielodostępnej](isv-sso-content.md)

[Tworzenie dokumentacji dla aplikacji wielodostępnych](isv-create-sso-documentation.md)
