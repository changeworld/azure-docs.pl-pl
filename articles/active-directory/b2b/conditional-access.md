---
title: Dostęp warunkowy dla użytkowników współpracy B2B — usługa Azure AD
description: Współpraca usługi Azure Active Directory B2B obsługuje uwierzytelnianie wieloskładnikowe (MFA) w celu selektywnego dostępu do aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0b6ceba4c3c9202e2024b5c163c0e98bb6cbf55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272995"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Dostęp warunkowy dla użytkowników współpracy B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Uwierzytelnianie wieloskładnikowe dla użytkowników B2B
Dzięki współpracy usługi Azure AD B2B organizacje mogą wymuszać zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkowników B2B. Te zasady mogą być wymuszane na poziomie dzierżawy, aplikacji lub poszczególnych użytkowników, w taki sam sposób, w jaki są włączone dla pracowników pełnoetatowych i członków instytucji. Zasady usługi MFA są wymuszane w organizacji zasobów.

Przykład:
1. Administrator lub pracownik informacyjny w firmie A zaprasza użytkownika z firmy B do aplikacji *Foo* w firmie A.
2. *Foo* aplikacji w firmie A jest skonfigurowany do wymagania mfa na dostęp.
3. Gdy użytkownik z firmy B próbuje uzyskać dostęp do aplikacji *Foo* w dzierżawie firmy A, jest proszony o wykonanie wyzwania usługi MFA.
4. Użytkownik może skonfigurować swoje usługi MFA w firmie A i wybiera opcję usługi MFA.
5. Ten scenariusz działa dla dowolnej tożsamości (usługi Azure AD lub MSA, na przykład, jeśli użytkownicy w firmie B uwierzytelniają się przy użyciu identyfikatora społecznościowego)
6. Firma A musi mieć wystarczające licencje usługi Azure AD w usłudze Premium, które obsługują usługę MFA. Użytkownik z firmy B zużywa tę licencję od firmy A.

Zapraszanie najmu jest zawsze odpowiedzialny za usługi MFA dla użytkowników z organizacji partnerskiej, nawet jeśli organizacja partnerska ma możliwości usługi MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Konfigurowanie usługi MFA dla użytkowników współpracujących z B2B
Aby dowiedzieć się, jak łatwo jest skonfigurować usługi MFA dla użytkowników współpracujących z B2B, zobacz, jak w poniższym klipie wideo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B użytkowników MFA doświadczenie do realizacji oferty
Zapoznaj się z następującą animacją, aby zobaczyć doświadczenie realizacji:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Resetowanie usługi MFA dla użytkowników współpracy B2B
Obecnie administrator może wymagać od użytkowników współpracy B2B ponownego sprawdzania się tylko przy użyciu następujących poleceń cmdlet programu PowerShell:

1. Łączenie z usługą Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Uzyskaj wszystkim użytkownikom metody weryfikacji

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Oto przykład:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Zresetuj metodę usługi MFA dla określonego użytkownika, aby wymagać od użytkownika współpracy B2B ponownego ustawienia metod sprawdzania. Przykład:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Dlaczego wykonujemy usługi MFA w dzierżawie zasobów?

W bieżącej wersji usługi MFA jest zawsze w dzierżawie zasobów, ze względu na przewidywalność. Załóżmy na przykład, że użytkownik Contoso (Sally) jest zaproszony do firmy Fabrikam, a firma Fabrikam włączyła usługi MFA dla użytkowników B2B.

Jeśli contoso ma zasady usługi MFA włączone dla aplikacji1, ale nie App2, a następnie jeśli przyjrzymy się contoso mfa oświadczenia w tokenie, możemy zobaczyć następujący problem:

* Dzień 1: Użytkownik ma usługi MFA w programie Contoso i uzyskuje dostęp do aplikacji 1, a następnie nie ma dodatkowego monitu usługi MFA jest wyświetlany w programie Fabrikam.

* Dzień 2: Użytkownik ma dostęp do aplikacji 2 w Contoso, więc teraz podczas uzyskiwania dostępu do usługi Fabrikam, muszą zarejestrować się w tym uwierzytelniania Wieloskładnikowego.

Ten proces może być mylące i może prowadzić do spadku w zakończeniu logowania.

Ponadto nawet jeśli Contoso ma możliwości usługi MFA, nie zawsze jest tak, że firma Fabrikam ufa zasadom usługi Contoso MFA.

Na koniec usługa MFA dzierżawy zasobów działa również dla msa i identyfikatorów społecznościowych oraz dla organizacji partnerów, które nie mają skonfigurowania usługi MFA.

W związku z tym zalecenie dla usługi MFA dla użytkowników B2B jest zawsze wymagać usługi MFA w zapraszając dzierżawy. To wymaganie może prowadzić do podwójnego usługi MFA w niektórych przypadkach, ale gdy dostęp do zapraszając dzierżawy, środowisko użytkowników końcowych jest przewidywalne: Sally musi zarejestrować się dla usługi MFA z zapraszając dzierżawy.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Dostęp warunkowy oparty na urządzeniach, lokalizacji i oparty na ryzyku dla użytkowników B2B

Gdy contoso włącza zasady dostępu warunkowego oparte na urządzeniach dla swoich danych firmowych, dostęp jest uniemożliwiony z urządzeń, które nie są zarządzane przez firmę Contoso i nie są zgodne z zasadami urządzeń Contoso.

Jeśli urządzenie użytkownika B2B nie jest zarządzane przez firmę Contoso, dostęp użytkowników B2B z organizacji partnerskich jest zablokowany w dowolnym kontekście, w jakim te zasady są wymuszane. Jednak Contoso można utworzyć listy wykluczeń zawierające określonych użytkowników partnerskich, aby wykluczyć je z zasad dostępu warunkowego opartego na urządzeniu.

#### <a name="mobile-application-management-policies-for-b2b"></a>Zasady zarządzania aplikacjami mobilnymi dla B2B

Zasady ochrony aplikacji dostępu warunkowego nie mogą być stosowane do użytkowników B2B, ponieważ organizacja zapraszająca nie ma wglądu w organizację domową użytkownika B2B.

#### <a name="location-based-conditional-access-for-b2b"></a>Dostęp warunkowy oparty na lokalizacji dla B2B

Zasady dostępu warunkowego oparte na lokalizacji mogą być wymuszane dla użytkowników B2B, jeśli zapraszająca organizacja może utworzyć zaufany zakres adresów IP definiujący ich organizacje partnerskie.

#### <a name="risk-based-conditional-access-for-b2b"></a>Dostęp warunkowy oparty na ryzyku dla B2B

Obecnie zasady logowania oparte na ryzyku nie mogą być stosowane do użytkowników B2B, ponieważ ocena ryzyka jest wykonywana w organizacji domowej użytkownika B2B.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły dotyczące współpracy usługi Azure AD B2B:

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Licencjonowanie funkcji współpracy B2B w usłudze Azure AD](licensing-guidance.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
