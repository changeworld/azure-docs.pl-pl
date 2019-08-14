---
title: Przechowywanie danych tożsamości na potrzeby klientów w Europie — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, o której przechowuje dane dotyczące tożsamości usługi Azure Active Directory dla swoich klientów w Europie.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 178f81cf42e5c57be4a0b69ada6560d46951a3a3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942845"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Przechowywanie danych tożsamości na potrzeby klientów w Europie w usłudze Azure Active Directory
Dane tożsamości są przechowywane w usłudze Azure AD w lokalizacji geograficznej na podstawie adresu dostarczonego przez organizację w przypadku subskrybowania usługi online firmy Microsoft, takiej jak Office 365 i Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane tożsamości, możesz użyć sekcji [gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Centrum zaufania Microsoft.

W przypadku klientów, którzy podały adres w Europie, usługa Azure AD przechowuje większość danych tożsamości w obrębie europejskich centrów Datacenter. Ten dokument zawiera informacje dotyczące wszelkich danych przechowywanych poza Europą przez usługi Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure uwierzytelnianie wieloskładnikowe (MFA)
    
- Wszystkie uwierzytelnianie dwuskładnikowe korzystające z połączeń telefonicznych i wiadomości SMS pochodzą z centrów danych USA i są również kierowane przez dostawców globalnych.
- Powiadomienia wypychane przy użyciu aplikacji Microsoft Authenticator pochodzą z centrów danych US. Ponadto usługi specyficzne dla dostawcy urządzeń mogą również być odtwarzane, a te usługi są dostępne poza Europą.
- Kody OATH są zawsze weryfikowane w Stanach Zjednoczonych. 

Aby uzyskać więcej informacji o tym, jakie informacje o użytkowniku są zbierane przez serwer usługi Azure MFA (serwer MFA) i opartą na chmurze chmurę Azure, zobacz [zbieranie danych użytkownika w usłudze Azure Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Dane konfiguracji zasad Azure AD B2C i kontenery kluczy są przechowywane w stanowych centrach danych. Nie zawierają one żadnych danych osobowych użytkownika. Aby uzyskać więcej informacji na temat konfiguracji zasad, [Zobacz Azure Active Directory B2C: Artykuł zasady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) wbudowane.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Usługa Azure AD B2B zapisuje zaproszenia przy użyciu linku Zrealizuj i przekierowuje informacje o adresie URL w centrach danych US. Ponadto adresy e-mail użytkowników, którzy nie subskrybują otrzymywania zaproszeń B2B, również są przechowywane w stanach USA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory usługi domenowe (Azure AD DS)

Usługa Azure AD DS przechowuje dane użytkowników w tej samej lokalizacji, co wybrana przez klienta sieć wirtualna Azure. Dlatego jeśli sieć znajduje się poza Europą, dane są replikowane i przechowywane poza kontynentem europejskim.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federacja w programie Microsoft Exchange Server 2013
    
- Identyfikator aplikacji (AppID) — unikatowy numer wygenerowany przez system uwierzytelniania Azure Active Directory w celu identyfikowania organizacji programu Exchange.
- Lista zatwierdzonych domen federacyjnych dla aplikacji
- Klucz publiczny podpisywania tokenu aplikacji 

Aby uzyskać więcej informacji na temat Federacji w programie Microsoft Exchange Server [, zapoznaj się z Federacją: Artykuł pomocy](https://docs.microsoft.com/exchange/federation-exchange-2013-help) programu Exchange 2013.


## <a name="other-considerations"></a>Inne zagadnienia

Usługi i aplikacje, które integrują się z usługą Azure AD, mają dostęp do danych tożsamości. Oceń poszczególne usługi i aplikacje, których używasz, aby określić, jak dane tożsamości są przetwarzane przez daną usługę i aplikację oraz czy spełniają wymagania dotyczące magazynu danych w firmie.

Aby uzyskać więcej informacji na temat przechowywania danych usług firmy Microsoft, zobacz sekcję [Gdzie przechowywane są Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) w Centrum zaufania Microsoft.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat funkcji i funkcji opisanych powyżej, zobacz następujące artykuły:
- [Co to jest uwierzytelnianie wieloskładnikowe?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Samoobsługowe resetowanie haseł w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Co to jest usługa Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Czym jest współpraca B2B w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
