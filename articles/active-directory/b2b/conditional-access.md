---
title: Dostęp warunkowy dla użytkowników współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Współpracy w usłudze Azure Active Directory B2B obsługuje uwierzytelnianie wieloskładnikowe (MFA) na selektywny dostęp do aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5234443e234d232a9711274bea2f73427266f6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60355815"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Dostęp warunkowy dla użytkowników współpracy B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Uwierzytelnianie wieloskładnikowe dla użytkowników B2B
Przy użyciu funkcji współpracy B2B usługi Azure AD organizacje mogą wymuszać zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkowników B2B. Te zasady można wymusić poziomie dzierżawy, aplikacji lub użytkownika, taki sam sposób, że są włączone dla pełnoetatowych pracowników i członków organizacji. Zasady MFA są wymuszane w organizacji zasobów.

Przykład:
1. Administratora lub informacje pracownika z firmy, A zaprasza użytkowników firmy B do aplikacji *Foo* w firmie A.
2. Aplikacja *Foo* w firmie A jest skonfigurowany do uwierzytelniania MFA można wymagać dostępu.
3. Po użytkownik firmy B próbuje uzyskać dostęp do aplikacji *Foo* firmy dzierżawy, użytkownicy zostaną poproszeni o ukończenie żądania uwierzytelniania MFA.
4. Użytkownika można skonfigurować ich MFA za pomocą firmy A i wybierze opcję ich usługi MFA.
5. W tym scenariuszu działa w przypadku dowolnej tożsamości (Azure AD lub zarządzanych kont usług, na przykład, jeśli użytkownicy w firmie B uwierzytelniania przy użyciu Identyfikatora społecznościowych)
6. Firma A musi mieć wystarczającą liczbę licencji Premium usługi Azure AD, które obsługują usługi MFA. Użytkownik z firmy B korzysta z tej licencji od firmy A.

Zapraszający dzierżawy jest odpowiedzialny za uwierzytelnianie wieloskładnikowe dla użytkowników z organizacji partnerskiej, zawsze, nawet wtedy, gdy organizacja partnera ma możliwości usługi MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Konfigurowanie uwierzytelniania Wieloskładnikowego dla użytkowników współpracy B2B
Aby dowiedzieć się, jak łatwo jest skonfigurować usługę MFA dla użytkowników współpracy B2B, zobacz temat jak w poniższym klipie wideo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Użytkowników B2B korzystanie z usługi MFA dla oferty realizacji
Zapoznaj się z następujących animacji, aby zobaczyć wykupu środowiska:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Resetuj dla użytkowników współpracy B2B usługi MFA
Obecnie administrator może wymagać użytkowników we współpracy B2B do weryfikacji się ponownie tylko za pomocą następujących poleceń cmdlet programu PowerShell:

1. Łączenie z usługą Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Pobieranie wszystkich użytkowników z dowodem metod

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Oto przykład:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Resetuj metody MFA dla określonego użytkownika wymagać od użytkowników współpracy B2B ponownie ustawić Weryfikacja metody. Przykład:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Dlaczego firma Microsoft wykonać uwierzytelnianie wieloskładnikowe u dzierżawców zasobów?

W bieżącej wersji usługi MFA jest zawsze w do przyczyn przewidywalność dzierżawy zasobów. Na przykład załóżmy, że użytkownik firmy Contoso (Zosi) zostało zaproszone do firmy Fabrikam i Fabrikam ma włączoną usługę MFA dla użytkowników B2B.

Firma Contoso ma włączone zasady MFA na serwerze App1, ale nie App2, następnie jeśli spojrzymy na oświadczenia usługi MFA firmy Contoso w tokenie, firma Microsoft może zostać wyświetlony następujący problem:

* Dzień 1: Użytkownik usługi MFA w firmie Contoso i uzyskuje dostęp do komputera App1, a następnie nie dodatkowego uwierzytelniania Wieloskładnikowego monit jest wyświetlany w firmie Fabrikam.

* Dzień 2: Użytkownik uzyskał dostęp do aplikacji 2 w firmie Contoso, więc teraz podczas uzyskiwania dostępu do firmy Fabrikam, muszą zarejestrować usługi MFA.

Ten proces może być mylące i może prowadzić do upuść w uzupełnienia logowania.

Ponadto nawet wtedy, gdy firma Contoso ma możliwość uwierzytelniania Wieloskładnikowego, nie zawsze jest przypadek Fabrikam będzie zaufanie zasady MFA firmy Contoso.

Na koniec zasobu dzierżawy usługi MFA działa również dla kont MSA i identyfikatorami społecznościowymi i organizacje partnerów, które nie mają skonfigurować uwierzytelnianie wieloskładnikowe.

W związku z tym zalecenie dla usługi MFA dla użytkowników B2B jest zawsze należy wymagać uwierzytelniania Wieloskładnikowego w zapraszający dzierżawca. Ten wymóg może spowodować podwójne MFA w niektórych przypadkach, ale zawsze wtedy, gdy dostęp do zapraszający dzierżawca, środowisko użytkowników końcowych jest przewidywalne: Zosi musi zarejestrować na potrzeby usługi MFA za pomocą zapraszający dzierżawca.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Dostępu warunkowego opartego na urządzeniach, na podstawie lokalizacji i oparty na ryzyku dla użytkowników B2B

Contoso włącza zasady dostępu warunkowego opartego na urządzeniach dla danych firmowych, dostęp jest uniemożliwił urządzeń, które nie są zarządzane przez firmę Contoso i nie są zgodne z zasadami urządzeń firmy Contoso.

Jeśli urządzenie użytkownika B2B nie są zarządzane przez firmę Contoso, dostępu użytkowników B2B z organizacji partnerskich jest zablokowany w kontekście, niezależnie od te zasady są wymuszane. Jednak firmy Contoso, można utworzyć listy wykluczeń zawierających użytkowników partnera, aby wykluczyć je z zasad dostępu warunkowego opartego na urządzeniach.

#### <a name="location-based-conditional-access-for-b2b"></a>Dostęp warunkowy na podstawie lokalizacji, dla modelu B2B

Mogły zostać wymuszone zasady dostępu warunkowego na podstawie lokalizacji dla użytkowników B2B, jeśli organizacji zapraszającej jest w stanie utworzyć zaufanych zakres adresów IP, który definiuje organizacji partnera.

#### <a name="risk-based-conditional-access-for-b2b"></a>Dostęp warunkowy oparty na ryzyku dla modelu B2B

Obecnie zasady na podstawie ryzyka logowania nie można zastosować do użytkowników B2B, ponieważ oceny ryzyka odbywa się w organizacji macierzystej użytkownika B2B.

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Licencjonowanie funkcji współpracy B2B w usłudze Azure AD](licensing-guidance.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
