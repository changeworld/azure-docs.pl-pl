---
title: Przechowywanie danych tożsamości na potrzeby klientów w Europie — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, o której przechowuje dane dotyczące tożsamości usługi Azure Active Directory dla swoich klientów w Europie.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e82a78953c4385f7688705d4ab3f697be9c3ddbd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235163"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Przechowywanie danych tożsamości na potrzeby klientów w Europie w usłudze Azure Active Directory
Dane tożsamości jest przechowywane przez usługę Azure AD w lokalizacji geograficznej, na podstawie adresu udostępnioną przez Twoją organizację, podczas subskrybowania usługi Online firmy Microsoft, takich jak Office 365 i Azure. W przypadku informacji na temat przechowywania danych tożsamości, można użyć [gdzie są Twoje dane znajdujące się?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) sekcji Microsoft Trust Center.

W przypadku klientów, którzy podany adres w Europie usługi Azure AD przechowuje większość danych tożsamości w europejskich centrach danych. Ten dokument zawiera informacje dotyczące wszelkie dane, które są przechowywane poza Europa przy użyciu usługi Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor authentication (MFA)
    
- Wszystkie procedury uwierzytelniania dwuskładnikowego przy użyciu połączenia telefoniczne lub wiadomości SMS pochodzą z centrów danych w Stanach Zjednoczonych i również są kierowane przez dostawców globalnego.
- Wypychać powiadomienia przy użyciu aplikacji pochodzących od nas centrów danych w programie Microsoft Authenticator. Ponadto określonych usług dostawcy urządzeń mogą należą do odtwarzania i tych usług może być poza Europa.
- Kody OATH są zawsze weryfikowane w Stanach Zjednoczonych.

Aby uzyskać więcej informacji na temat zbieranych informacji użytkownika przez serwer usługi Azure Multi-Factor Authentication (MFA Server) i oparte na chmurze usługi Azure MFA, zobacz [zbierania danych użytkowników usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Usługi Azure AD B2C zasady konfiguracji danych i kontenery kluczy są przechowywane w Stanach Zjednoczonych w centrach danych. Te nie zawierają żadnych danych osobowych użytkownika. Aby uzyskać więcej informacji na temat konfiguracji zasad, zobacz [usługi Azure Active Directory B2C: Wbudowane zasady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) artykułu.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B magazynów zaproszeń z realizacji link i przekierowywanie adresu URL informacji w centrach danych w Stanach Zjednoczonych. Ponadto adres e-mail użytkowników, którzy zrezygnować z otrzymywania zaproszeń B2B są także przechowywane w Stanach Zjednoczonych w centrach danych.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Usługa Azure AD DS przechowuje dane użytkowników w tej samej lokalizacji, co wybrana przez klienta sieć wirtualna Azure. Dlatego jeśli sieć znajduje się poza Europą, dane są replikowane i przechowywane poza kontynentem europejskim.

## <a name="other-considerations"></a>Inne zagadnienia

Usługi i aplikacje, które integrują się z usługą Azure AD mają dostęp do danych tożsamości. Oceń wszystkie usługi i aplikacje, które można określić, jak dane tożsamości są przetwarzane przez ten określonej usługi i aplikacji oraz tego, czy spełniają one wymagania dotyczące magazynu danych firmy.

Aby uzyskać więcej informacji na temat przechowywania danych usług firmy Microsoft, zobacz sekcję [Gdzie przechowywane są Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) w Centrum zaufania Microsoft.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o tych funkcji i możliwości opisanych powyżej zobacz następujące artykuły:
- [Co to jest uwierzytelnianie wieloskładnikowe?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Samoobsługowe resetowanie haseł w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Co to jest usługa Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Czym jest współpraca B2B w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
