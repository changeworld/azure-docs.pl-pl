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
ms.openlocfilehash: b21f82dc0a1eb8edf571da13e0d34fecae5f401b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249731"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Przechowywanie danych tożsamości na potrzeby klientów w Europie w usłudze Azure Active Directory
Azure Active Directory (Azure AD) pomaga zarządzać tożsamościami użytkowników i tworzenia zasad dostępu opartych na analizie, których zabezpieczania zasobów organizacji. Dane tożsamości są przechowywane w lokalizacji opartej na adresie podanym przez Twoją organizację podczas dokonywania subskrypcji usługi. Na przykład subskrybowania usługi Office 365 lub platformy Azure. Aby uzyskać szczegółowe informacje na temat przechowywania danych tożsamości, zobacz sekcję [Gdzie przechowywane są Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) w Centrum zaufania Microsoft.

Chociaż większość Azure danych związanych z usługi AD Europejskiego tożsamości pozostaje w europejskich centrach danych, istnieją pewne dane operacyjne, specyficzne dla usługi, które są wymagane do normalnego działania usługi Azure AD, są przechowywane w Stanach Zjednoczonych, która nie zawiera żadnych danych osobowych.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Dane należące do klientów z Europy przechowywane poza europejskimi centrami danych

W przypadku organizacji korzystających z europejskich adresów większość danych tożsamości dotyczących usługi Azure AD pozostaje w europejskich centrach danych. Azure AD danych przechowywane w europejskich centrach danych, również replikowane do centrów danych w Stanach Zjednoczonych, obejmuje:

- **Usługa Microsoft Azure Multi-Factor Authentication (MFA) i samoobsługowe resetowanie haseł w usłudze Azure AD (SSPR)**
    
    Usługa MFA przechowuje wszystkie dane użytkownika w europejskich centrach danych. Jednak niektóre dane specyficzne dla usługi MFA są przechowywane w Stanach Zjednoczonych, w tym:
    
    - Dane uwierzytelniania dwuskładnikowego i powiązane z nim dane osobowe mogą być przechowywane w Stanach Zjednoczonych w przypadku korzystania z uwierzytelniania wieloskładnikowego lub samoobsługowego resetowania hasła.

        - Wszystkie procedury uwierzytelniania dwuskładnikowego korzystające z połączeń telefonicznych lub wiadomości tekstowych mogą być wykonywane przez operatorów w Stanach Zjednoczonych.
    
        - Powiadomienia push używające aplikacji Microsoft Authenticator wymagają zastosowania powiadomień pochodzących z usługi powiadomień producenta (firmy Apple lub Google), która może znajdować się poza Europą.
    
        - Kody OATH są zawsze weryfikowane w Stanach Zjednoczonych. 
    
    - Niektóre dzienniki uwierzytelniania wieloskładnikowego i samoobsługowego resetowania hasła są przechowywane w Stanach Zjednoczonych przez 30 dni, niezależnie od typu zastosowanego uwierzytelniania.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Usługa Azure AD B2C przechowuje wszystkie dane użytkownika w europejskich centrach danych. Jednak dzienniki operacyjne (bez danych osobowych) pozostają w lokalizacji, z której użytkownik uzyskuje dostęp do usług. Na przykład jeśli użytkownik uzyskuje dostęp do usługi B2C w Stanach Zjednoczonych, dzienniki operacyjne pozostają w Stanach Zjednoczonych. Ponadto wszystkie dane konfiguracji zasad, które nie zawierają danych osobowych, są przechowywane wyłącznie na terenie Stanów Zjednoczonych. Aby uzyskać więcej informacji na temat konfiguracji zasad, zobacz [usługi Azure Active Directory B2C: Wbudowane zasady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) artykułu.

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Usługa Azure AD B2B przechowuje wszystkie dane użytkownika w europejskich centrach danych. Jednak metadane wszelkie inne niż osobiste są przez nią przechowywane w tabelach w centrach danych na terenie Stanów Zjednoczonych. Poniższa tabela zawiera pola, takie jak redeemUrl, invitationTicket, resource tenant Id, InviteRedirectUrl i InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Usługa Azure AD DS przechowuje dane użytkowników w tej samej lokalizacji, co wybrana przez klienta sieć wirtualna Azure. Dlatego jeśli sieć znajduje się poza Europą, dane są replikowane i przechowywane poza kontynentem europejskim.

- **Usługi i aplikacje zintegrowane z usługą Azure AD**

    Wszystkie usługi i aplikacje, które integrują się z usługą Azure AD, mają dostęp do danych tożsamości. Dokonaj oceny poszczególnych usług i aplikacji, aby określić, w jaki sposób dane tożsamości są przez nie przetwarzane, oraz sprawdzić, czy spełniają wymagania dotyczące magazynu danych Twojej firmy.

    Aby uzyskać więcej informacji na temat przechowywania danych usług firmy Microsoft, zobacz sekcję [Gdzie przechowywane są Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) w Centrum zaufania Microsoft.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o tych funkcji i możliwości opisanych powyżej zobacz następujące artykuły:
- [Co to jest uwierzytelnianie wieloskładnikowe?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Samoobsługowe resetowanie haseł w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Co to jest usługa Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Czym jest współpraca B2B w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
