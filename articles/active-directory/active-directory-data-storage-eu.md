---
title: Której przechowuje dane tożsamości usługi Azure AD dla klientów | Dokumentacja firmy Microsoft
description: Więcej informacji na temat której przechowuje dane dotyczące tożsamości Microsoft Azure Active Directory dla jej klientów.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 2ae60c620db4fb3d88554ad5653729c7c1325e97
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713494"
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Gdzie Microsoft Azure Active Directory (Azure AD) przechowywania danych tożsamości dla klientów
Usługi Azure AD pomaga zarządzać tożsamościami użytkowników i utworzyć zasady dostępu oparte na analizy, które zabezpieczanie zasobów w organizacji. Tożsamość dane są przechowywane w lokalizacji, która ma oparte na adresie organizacji podane podczas subskrybowania usługi. Na przykład jeśli masz subskrypcję usługi Office 365 ani Azure. Aby uzyskać szczegółowe informacje dotyczące przechowywania danych tożsamości, można użyć [danych znajdujących się w przypadku?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) sekcji Center Trust firmy Microsoft.

Chociaż najbardziej Azure danych tożsamości Europejskiego powiązane AD pozostaje w centrach danych Europejskich, istnieją pięciu atrybutów związane z użytkownikiem, które zwykle są przechowywane w centrach danych Stanów Zjednoczonych. Te atrybuty są imię, nazwisko userPrincipalName, domeny i PasswordHash. Atrybut PasswordHash może być wyjątek i nie są przechowywane w Stanach Zjednoczonych, jeśli ktoś korzysta z lokalnej, metody uwierzytelniania federacyjnego zatrzymuje wartość PasswordHash z synchronizacji z usługą Azure AD. Ponadto istnieje niektóre dane operacyjne, specyficzne dla usługi, która jest wymagana do normalnego operacji usługi Azure AD, która jest przechowywana w Stanach Zjednoczonych i nie zawiera żadnych danych osobowych.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Dane przechowywane poza Europejskiego centrów danych dla klientów

Najbardziej Azure danych związanych z AD Europejskiego tożsamości, dla organizacji mających Europejskiej na podstawie adresów pozostaje w centrach danych Europejskich. Danych Azure AD, który nie znajduje się w centrach danych Europejskich, obejmują:

- **Atrybuty dotyczące tożsamości**

    Następujące atrybuty dotyczące tożsamości będą replikowane do Stanów Zjednoczonych:

    - Imię
    - Nazwisko
    - userPrincipalName
    - Domena
    - PasswordHash
    - sourceAnchor
    - AccountEnabled
    - passwordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - IDENTYFIKATOR PUID

- **Microsoft Azure Multi-Factor authentication (MFA) i Azure AD samoobsługowego resetowania hasła (SSPR)**
    
    Magazyny MFA wszystkie dane użytkownika podczas spoczynku w centrach danych Europejskich. Jednak niektóre dane specyficzne dla usługi MFA są przechowywane w Stanach Zjednoczonych, w tym:
    
    - Uwierzytelniania dwuskładnikowego i jej powiązane dane osobowe mogą być przechowywane w Stanach Zjednoczonych, jeśli używane jest uwierzytelnianie wieloskładnikowe lub SSPR.
        - Wszystkie uwierzytelniania dwuskładnikowego przy użyciu połączenia telefoniczne lub wiadomości SMS może zostać wykonane przez nośniki Stanów Zjednoczonych.
        - Powiadomienia wypychane za pomocą aplikacji Microsoft Authenticator wymagają powiadomień z usługi powiadomień producenta (firmy Apple lub Google), która może znajdować się poza Europy.
        - Kody OATH zawsze są weryfikowane w Stanach Zjednoczonych 
    - Niektóre MFA i SSPR dzienniki są przechowywane w Stanach Zjednoczonych przez 30 dni, niezależnie od typu uwierzytelniania.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C magazynów wszystkie dane użytkownika podczas spoczynku w centrach danych Europejskich. Jednak operacyjne dzienniki (z danych osobowych usunięte) pozostają w lokalizacji, z którym osoba uzyskuje dostęp do usług. Na przykład jeśli użytkownik B2C uzyskuje dostęp do usługi w Stanach Zjednoczonych, pozostanie operacyjne dzienniki w USA Ponadto wszystkie dane konfiguracyjne zasady nie zawierające danych osobowych są przechowywane tylko w Stanach Zjednoczonych Aby uzyskać więcej informacji o konfiguracji zasad, zobacz [usługi Azure Active Directory B2C: wbudowane zasady](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-policies) artykułu.

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B magazynów wszystkie dane użytkownika podczas spoczynku w centrach danych Europejskich. Jednak B2B metadane są przechowywane jego-personal w tabelach w centrach danych Stanów Zjednoczonych. Ta tabela zawiera pola, takich jak redeemUrl, invitationTicket zasobów dzierżawy identyfikator, InviteRedirectUrl i InviterAppId.

- **Microsoft Azure Active Directory Domain Services (usługi Azure AD DS)**

    Azure AD DS są przechowywane dane użytkownika w tej samej lokalizacji co sieć wirtualna Azure wybrane przez klienta. Tak Jeśli sieć jest poza Europę, dane są replikowane i przechowywane poza Europy.

- **Usługi i aplikacje są zintegrowane z usługą Azure AD**

    Wszystkie usługi i aplikacje do zintegrowania z usługą Azure AD mają dostęp do danych tożsamości. Należy ocenić wszystkie usługi i aplikacji, aby określić sposób przetwarzania danych tożsamości tej określonej usługi i aplikacji i czy spełniają one wymagania dotyczące magazynu danych firmy.

    Aby uzyskać więcej informacji o podziale danych usług firmy Microsoft, zobacz [danych znajdujących się w przypadku?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) sekcji Center Trust firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o tych funkcji i możliwości opisane powyżej zobacz następujące artykuły.
- [Rozpoczynanie pracy z usługą Azure Active Directory](get-started-azure-ad.md)
- [Co to jest uwierzytelnianie wieloskładnikowe?](https://docs.microsoft.com/en-us/azure/active-directory/authentication/multi-factor-authentication)
- [Azure AD samoobsługowego resetowania hasła](https://docs.microsoft.com/en-us/azure/active-directory/authentication/active-directory-passwords-overview)
- [Co to jest Azure Active Directory B2C?](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview)
- [Czym jest współpraca B2B w usłudze Azure AD?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Usługi domenowe w usłudze Azure Active Directory (AD)](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-overview)
