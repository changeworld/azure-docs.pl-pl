---
title: Wybierz protokół Federacji odpowiednie dla twojej aplikacji wielodostępnej
description: Wskazówki dla niezależnych dostawców oprogramowania na integrację z usługą Azure Active Directory
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
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795193"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Wybierz protokół Federacji odpowiednie dla twojej aplikacji wielodostępnej

Podczas opracowywania Twoja aplikacja oprogramowania jako usługi (SaaS), należy wybrać protokół federacyjnych, które najlepiej odpowiadają potrzebom Twoich i Twoich klientów. Ta decyzja zależy od platformy deweloperskiej i chęć Integracja z usługą danych dostępnych w ekosystemie klientów usługi Office 365 i Azure AD.

Zobacz pełną listę [protokołów dostępnych dla rejestracji Jednokrotnej integracji](what-is-single-sign-on.md) za pomocą usługi Azure Active Directory.
Następujące porównuje tabeli 
* Otwarte uwierzytelnianie (protokołu OAuth 2.0) w wersji 2.0
* Open ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Web Services Federation (WSFed)

| Możliwość| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Oparte na sieci Web logowania jednokrotnego| √| √ |
| Wylogowania jednokrotnego opartego na sieci Web| √| √ |
| Na podstawie mobilnej logowanie jednokrotne| √| √* |
| Na podstawie Mobile wylogowania jednokrotnego| √| √* |
| Zasady dostępu warunkowego dla aplikacji mobilnych| √| X |
| Bezproblemowe uwierzytelnianie wieloskładnikowe dla aplikacji mobilnych| √| X |
| Access Microsoft Graph| √| X |

\* To możliwe, ale Microsoft nie zawiera przykłady i wskazówki.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 i Open ID Connect

OAuth 2.0 to [standardowych](https://oauth.net/2/) Protokół autoryzacji. OIDC (OpenID Connect) jest [standardem branżowym](https://openid.net/connect/) warstwy uwierzytelniania tożsamości, zbudowany na podstawie protokołu Oath 2.0.

### <a name="benefits"></a>Korzyści

Firma Microsoft zaleca używanie OIDC/OAuth 2.0, mają uwierzytelnianie i autoryzacja wbudowanej w protokołów. Przy użyciu protokołu SAML należy również zaimplementować autoryzacji.

W tych protokołów autoryzacji włącza aplikację, aby uzyskać dostęp i przeprowadzić integrację z zaawansowanych użytkowników i danych organizacji za pośrednictwem interfejsu API programu Microsoft Graph.

Przy użyciu protokołu OAuth 2.0 i OIDC upraszcza środowisko użytkownika końcowego klientów, przyjmując logowania jednokrotnego dla aplikacji. Możesz łatwo definiować niezbędne zestawy uprawnień, które następnie są automatycznie reprezentowane administratora lub użytkownika końcowego wyrażanie zgody.

Ponadto przy użyciu tych protokołów umożliwia klientom korzystanie z dostępu warunkowego i zasad uwierzytelniania Wieloskładnikowego do kontrolowania dostępu do aplikacji. Firma Microsoft udostępnia biblioteki i [przykłady kodu na wielu platformach technologii](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) ułatwiające tworzenie oprogramowania.  

### <a name="implementation"></a>Implementacja

Możesz zarejestrować aplikację w usłudze Microsoft Identity, która jest dostawcą uwierzytelniania OAuth 2.0. Następnie można również zarejestrować aplikację na podstawie protokołu OAuth 2.0 przy użyciu innego dostawcy tożsamości, który chcesz zintegrować z. 

Aby uzyskać informacje na temat sposobu Zarejestruj swoją aplikację i zaimplementować te protokoły logowania jednokrotnego do aplikacji sieci web, zobacz [Autoryzowanie dostępu do aplikacji sieci web przy użyciu protokołu OpenID Connect i Azure Active Directory](../develop/sample-v2-code.md).  Aby uzyskać informacje na temat sposobu zaimplementować te protokoły logowania jednokrotnego w aplikacjach mobilnych zobacz następujące tematy: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Platforma uniwersalna systemu Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 i WSFed

Zabezpieczeń Assertion Markup Language (SAML) jest zwykle używany w dla aplikacji sieci web. Zobacz [platformy Azure korzysta z protokołu SAML](../develop/active-directory-saml-protocol-reference.md) omówienie. 

Web Services Federation (WSFed) jest [standardem branżowym](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) zazwyczaj używane w przypadku aplikacji sieci web, które są tworzone przy użyciu platformy .net.

### <a name="benefits"></a>Korzyści

SAML 2.0 to dojrzała standard i większość technologii platformy typu open-source bibliotek obsługi SAML 2.0. Możesz zapewnić klientom interfejs administracji do konfigurowania logowania jednokrotnego SAML. Logowania jednokrotnego SAML można skonfigurować dla usługi Microsoft Azure AD i innego dostawcy tożsamości, który obsługuje SAML 2

### <a name="trade-offs"></a>Wad

W przypadku używania protokołów języka SAML 2.0 lub WSFed dla aplikacji mobilnych, niektóre zasady dostępu warunkowego, w tym usługi Multi-Factor Authentication (MFA) mają ograniczonymi możliwościami. Ponadto ma dostęp do programu Microsoft Graph, należy zaimplementować autoryzacji za pomocą protokołu OAuth 2.0 do generowania tokenów niezbędne. 

### <a name="implementation"></a>Implementacja

Firma Microsoft nie udostępniają biblioteki dla implementacji protokołu SAML ani nie zaleca się określonej biblioteki. Brak dostępnych wiele bibliotek typu open source.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>Usługa rejestracji Jednokrotnej i Rest, programu Microsoft Graph przy użyciu interfejsu API 

Program Microsoft Graph jest sieć szkieletową danych we wszystkich Microsoft 365, w tym usługi Office 365, systemu Windows 10 i pakietu Enterprise Mobility i zabezpieczeń oraz dodatkowych produktów, takich jak Dynamics 365. W tym schematy podstawowej jednostki, takie jak użytkowników, grup, kalendarza, poczty, plików i innych produktywność użytkowników tego dysku. Program Microsoft Graph udostępnia trzy interfejsy, dla deweloperów REST API oparty na, połączenie danych programu Microsoft Graph i łączników, które umożliwiają deweloperom można dodać własne dane do programu Microsoft Graph.  

Przy użyciu dowolnej z powyższych protokołów logowania jednokrotnego umożliwia aplikacji dostęp do zaawansowanych danych dostępne za pośrednictwem interfejsu API REST usługi Microsoft Graph. Umożliwia to klientom lepsze wykorzystanie inwestycji w usłudze Microsoft 365. Na przykład aplikację, można wywołać interfejsu API Microsoft Graph integrację z wystąpienia usługi Office 365 klientów i powierzchni użytkowników programu Microsoft Office i elementów programu SharePoint w aplikacji. 

Jeśli używasz Open ID Connect na potrzeby uwierzytelniania, a następnie tworzenie oprogramowania jest bezproblemowe, ponieważ będzie używać protokołu OAuth2, podstawę Open ID Connect, aby uzyskać tokeny może służyć do wywoływania interfejsów API programu Microsoft Graph. Jeśli aplikacja korzysta z protokołu SAML lub WSFed, należy dodać dodatkowy kod w aplikacji, aby otrzymywać te OAuth2 można uzyskać tokenów, wymaganych do wywoływania interfejsów API programu Microsoft Graph. 

## <a name="next-steps"></a>Następne kroki

[Włączanie logowania jednokrotnego dla aplikacji wielodostępnych](isv-sso-content.md)

[Tworzenie dokumentacji dla twojej aplikacji wielodostępnej](isv-create-sso-documentation.md)
