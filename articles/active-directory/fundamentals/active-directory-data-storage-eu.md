---
title: Magazyn danych tożsamości dla klientów europejskich — usługa Azure AD
description: Dowiedz się, gdzie usługa Azure Active Directory przechowuje dane związane z tożsamościami dla swoich klientów europejskich.
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
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422997"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Przechowywanie danych tożsamości dla klientów europejskich w usłudze Azure Active Directory
Dane dotyczące tożsamości są przechowywane przez usługę Azure AD w lokalizacji geograficznej na podstawie adresu podanego przez organizację podczas subskrybowania usługi Microsoft Online, takiej jak Office 365 i Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane tożsamości, można użyć sekcji [Gdzie znajdują się dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Dla klientów, którzy podali adres w Europie, usługa Azure AD przechowuje większość danych tożsamości w europejskich centrach danych. Ten dokument zawiera informacje o wszelkich danych przechowywanych poza Europą przez usługi Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Uwierzytelnianie wieloskładnikowe platformy Microsoft Azure (MFA)
    
- Wszystkie uwierzytelnianie dwuskładnikowe przy użyciu połączeń telefonicznych lub wiadomości SMS pochodzi z amerykańskich centrów danych i jest również kierowane przez globalnych dostawców.
- Powiadomienia wypychane przy użyciu aplikacji Microsoft Authenticator pochodzą z amerykańskich centrów danych. Ponadto w grę mogą również wchodzić usługi specyficzne dla dostawców urządzeń, a usługi te mogą być poza Europą.
- Kody OATH są zawsze weryfikowane w Stanach Zjednoczonych. 

Aby uzyskać więcej informacji o tym, jakie informacje o użytkowniku są zbierane przez serwer uwierzytelniania wieloskładnikowego platformy Azure (SERWER MFA) i usługę Azure MFA opartą na chmurze, zobacz [Zbieranie danych użytkowników uwierzytelniania wieloskładnikowego platformy Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Dane konfiguracji zasad usługi Azure AD B2C i kontenery kluczy są przechowywane w amerykańskich centrach danych. Nie zawierają one żadnych danych osobowych użytkownika. Aby uzyskać więcej informacji na temat konfiguracji zasad, zobacz artykuł [Azure Active Directory B2C: Built-in policies (Azure Active Directory B2C — wbudowane zasady)](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Usługa Azure AD B2B przechowuje zaproszenia z łączem realizowania i przekierowywaniem informacji o adresie URL w amerykańskich centrach danych. Ponadto adres e-mail użytkowników, którzy wyrzekają się otrzymywania zaproszeń B2B, jest również przechowywany w amerykańskich centrach danych.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Usługa Azure AD DS przechowuje dane użytkowników w tej samej lokalizacji, co wybrana przez klienta sieć wirtualna Azure. Dlatego jeśli sieć znajduje się poza Europą, dane są replikowane i przechowywane poza kontynentem europejskim.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federacja w programie Microsoft Exchange Server 2013
    
- Identyfikator aplikacji (AppID) — unikatowy numer wygenerowany przez system uwierzytelniania usługi Azure Active Directory w celu zidentyfikowania organizacji programu Exchange.
- Zatwierdzona lista domen federatywnych dla aplikacji
- Podpisywanie tokenu aplikacji klucz publiczny 

Aby uzyskać więcej informacji na temat federacji na serwerze Microsoft Exchange, zobacz [Artykuł Pomoc Federacja: Exchange 2013.](https://docs.microsoft.com/exchange/federation-exchange-2013-help)


## <a name="other-considerations"></a>Inne zagadnienia

Usługi i aplikacje integrujące się z usługą Azure AD mają dostęp do danych tożsamości. Oceń każdą usługę i aplikację używaną do określenia sposobu przetwarzania danych tożsamości przez tę konkretną usługę i aplikację oraz czy spełniają one wymagania firmy dotyczące przechowywania danych.

Aby uzyskać więcej informacji na temat przechowywania danych usług firmy Microsoft, zobacz sekcję [Gdzie przechowywane są Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) w Centrum zaufania Microsoft.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat dowolnej funkcji i funkcji opisanych powyżej, zobacz następujące artykuły:
- [Co to jest uwierzytelnianie wieloskładnikowe?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Samoobsługowe resetowanie hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Co to jest usługa Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Czym jest współpraca B2B w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
