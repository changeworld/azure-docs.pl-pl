---
title: Dostęp warunkowy dla usługi Azure Active Directory B2B współpracy użytkowników | Dokumentacja firmy Microsoft
description: Azure współpracy B2B usługi Active Directory obsługuje uwierzytelnianie wieloskładnikowe (MFA) dla selektywny dostęp do aplikacji firmowych
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 09/11/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2fed28bb7f7b61556a36b11b325ace9773f89991
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Dostęp warunkowy dla użytkowników współpracy B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Uwierzytelnianie wieloskładnikowe dla użytkowników B2B
Współpracy B2B usługi Azure AD organizacji można wymusić uwierzytelnianie wieloskładnikowe (MFA) zasad dla użytkowników B2B. Te zasady można wymusić na poziomie indywidualnego użytkownika dzierżawcy, aplikacji lub ten sam sposób, że są włączone dla pełnoetatowych pracowników i członków organizacji. Zasady MFA są wymuszane w organizacji zasobów.

Przykład:
1. Administratora lub informacje pracownika z firmy, A zaprasza użytkownikowi aplikacji firmy B *Foo* w firmie A.
2. Aplikacja *Foo* w firmie A jest skonfigurowany do uwierzytelniania MFA można wymagać dostępu.
3. Próba dostępu aplikacji użytkownika z firmy B *Foo* firmy dzierżawcy, proszeni są o wykonać żądanie uwierzytelniania MFA.
4. Użytkownika można skonfigurować ich MFA z firmy A i wybierze opcję ich MFA.
5. W tym scenariuszu działa w przypadku tożsamości (Azure AD lub zarządzanych kont usług, na przykład, jeśli użytkownicy w firmie B uwierzytelniać za pomocą Identyfikatora społecznościowych)
6. Firmy, A musi mieć wystarczającą liczbę licencji usługi Azure AD Premium, które obsługują usługę MFA. Ta licencja firmy A. zużywa użytkownika z firmy B

Zaproszenia dzierżawy jest odpowiedzialny za uwierzytelnianie wieloskładnikowe dla użytkowników w organizacji partnera zawsze, nawet w przypadku organizacji partnerskiej funkcję MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Konfigurowanie usługi MFA dla użytkowników współpracy B2B
Aby dowiedzieć się, jak łatwo jest skonfigurować uwierzytelnianie wieloskładnikowe dla użytkowników współpracy B2B, zobacz temat jak w poniższym klipie wideo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Użytkownicy B2B środowisko MFA oferują realizacji
Zapoznaj się z następujących animacji, aby zobaczyć realizacji środowisko:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Resetuj dla użytkowników współpracy B2B usługi MFA
Obecnie administrator może wymagać współpracy B2B użytkownikom dowód się ponownie tylko za pomocą następujących poleceń cmdlet programu PowerShell:

1. Łączenie z usługą Azure AD

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Pobierz wszyscy użytkownicy dowód metod

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Oto przykład:

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Reset — metoda uwierzytelniania Wieloskładnikowego dla określonego użytkownika wymagać od użytkownika współpracy B2B ponownie ustawić metody potwierdzenia w górę. Przykład:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Dlaczego możemy użyć uwierzytelniania MFA w dzierżawy zasobów?

W bieżącej wersji usługi MFA jest zawsze w dzierżawy zasobów, ze względu na przewidywalności. Na przykład załóżmy, że użytkownik Contoso (Zosi) zachęca się do firmy Fabrikam ma włączone uwierzytelnianie wieloskładnikowe dla użytkowników B2B.

Jeżeli Contoso ma włączone zasady MFA na serwerze App1, ale nie App2, jeśli przyjrzymy się Contoso MFA oświadczenia w tokenie, firma Microsoft może zobacz następujący problem:

* Dzień 1: Użytkownik ma MFA w firmie Contoso i uzyskuje dostęp do komputera App1, a następnie nie dodatkowe uwierzytelnianie wieloskładnikowe monit jest wyświetlany w firmie Fabrikam.

* Dzień 2: Użytkownik uzyskiwał dostęp do 2 aplikacji w firmie Contoso, więc podczas uzyskiwania dostępu do firmy Fabrikam, muszą zarejestrować dla usługi MFA istnieje.

Ten proces może być trudne i może prowadzić do drop w zakończeń logowania.

Ponadto nawet jeśli firma Contoso ma możliwość uwierzytelniania MFA, nie zawsze jest sprawy firma Fabrikam może zaufanie zasady MFA firmy Contoso.

Na koniec zasobów dzierżawy MFA działa również MSA i identyfikatory społecznościowych oraz organizacjami partnera, bez konfigurowania uwierzytelniania Wieloskładnikowego.

W związku z tym dla usługi MFA dla użytkowników B2B zaleca się zawsze wymagają usługi MFA w dzierżawie zaproszenia. Ten wymóg może prowadzić do podwójne MFA w niektórych przypadkach, ale zawsze, gdy dostęp do dzierżawy zaproszenia, środowisko użytkownika końcowego jest atrybutem wartości prognozowanych: Zosi musi zarejestrować w usłudze MFA z dzierżawcą zaproszenia.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Oparta na urządzeniach, na podstawie lokalizacji i ryzyka dostępu warunkowego dla użytkowników B2B

Gdy Contoso włącza zasady dostępu warunkowego opartego na urządzenia do danych firmowych, dostępu nie urządzeń, które nie są zarządzane przez firmę Contoso i nie są zgodne z zasadami urządzenia firmy Contoso.

Jeśli urządzenie użytkownika B2B nie jest zarządzana przez firmy Contoso, dostęp użytkowników B2B z organizacji partnera jest zablokowany w dowolnym kontekście te zasady są wymuszane. Jednak Contoso można utworzyć listy wykluczeń zawierających użytkowników partnera, aby wykluczyć je z zasad dostępu warunkowego opartego na urządzeniu.

#### <a name="location-based-conditional-access-for-b2b"></a>Na podstawie lokalizacji dostępu warunkowego dla B2B

Jeśli zaproszenia organizacji jest możliwość tworzenia zaufany zakres adresów IP, który definiuje organizacji partnera można wymuszać zasady dostępu warunkowego na podstawie lokalizacji B2B użytkowników.

#### <a name="risk-based-conditional-access-for-b2b"></a>Dostęp warunkowy dla B2B ryzyka

Obecnie zasady oparte na ryzyko logowania nie można zastosować użytkownikom B2B oceny ryzyka jest wykonywana w organizacji macierzystej użytkownika B2B.

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

* [Czym jest współpraca B2B w usłudze Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure licencjonowania współpracy B2B usługi AD](active-directory-b2b-licensing.md)
* [Azure współpracy B2B usługi Active Directory — często zadawane pytania (FAQ)](active-directory-b2b-faq.md)
