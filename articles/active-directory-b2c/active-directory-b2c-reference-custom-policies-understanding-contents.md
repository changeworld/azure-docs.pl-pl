---
title: Omówienie zasad niestandardowych modułu uruchamiającego pakietu w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat w zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b52f1a4cb6837dd779dcf4edac140bb13e06eacb
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509557"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Omówienie zasad niestandardowych systemu pakiet startowy usługi Azure AD B2C niestandardowe zasady

Ta sekcja zawiera listę wszystkich elementów podstawowych zasad B2C_1A_base, który jest dostarczany z **pakiet startowy** i który jest używany na potrzeby tworzenia własnych zasad za pomocą dziedziczenia obiektu *B2C_1A_base_extensions zasad* .

Jako takie w szczególności koncentruje się ona na typy oświadczeń już zdefiniowanych, przekształcenia oświadczeń, definicji zawartości, dostawców oświadczeń za pomocą ich profile techniczne i podstawowe podróży użytkownika.

> [!IMPORTANT]
> Microsoft nie udziela żadnych gwarancji, jawnych ani dorozumianych, w związku z informacjami przedstawionymi poniżej. Zmiany mogą być wprowadzane w dowolnym momencie przed chwilą, w czasie GA lub po.

Własne zasady i zasad B2C_1A_base_extensions można zastąpić te definicje i rozszerzać te zasady nadrzędne, podając te dodatkowe, zgodnie z potrzebami.

Elementy podstawowe *zasad B2C_1A_base* są typy oświadczeń, przekształcenia oświadczeń i definicji zawartości. Te elementy mogą się odwoływać się do własne zasady, a także jak w *zasad B2C_1A_base_extensions*.

## <a name="claims-schemas"></a>Schematy oświadczeń

To oświadczeń schematów jest podzielony na trzy sekcje:

1.  Pierwsza sekcja wyświetla minimalne oświadczenia, które są wymagane dla podróży użytkownika zapewnić prawidłowe działanie.
2.  Druga sekcja, która zawiera listę oświadczeń wymagane parametry ciągu zapytania i inne parametry specjalne do przekazania do innych dostawców oświadczeń, szczególnie login.microsoftonline.com na potrzeby uwierzytelniania. **Nie Modyfikuj tych oświadczeń**.
3.  I po pewnym czasie trzecia sekcja, która wyświetla wszelkie dodatkowe, opcjonalne oświadczenia, które mogą być zbierane od użytkownika, przechowywane w katalogu i wysłane w tokenach podczas logowania. W tej sekcji można dodać nowy typ oświadczenia do zebranych od użytkownika i/lub wysyłane w tokenie.

> [!IMPORTANT]
> Schemat oświadczeń zawiera ograniczeń dotyczących określonych oświadczeń, takie jak hasła i nazwy użytkowników. Zasady zaufania Framework (TF) traktuje usługi Azure AD, jak inni dostawcy oświadczeń i wszystkie jej ograniczenia są sporządzony według wzoru w zasadach niestandardowych. Aby dodać więcej ograniczeń, lub użyj innego dostawcy oświadczeń dla magazynu poświadczeń, które będą miały własne ograniczenia, może zostać zmodyfikowany zasad.

Poniżej przedstawiono typy oświadczeń dostępne.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Oświadczenia, które są wymagane dla podróży użytkownika

Poniższe oświadczenia są wymagane dla podróży użytkownika zapewnić prawidłowe działanie:

| Typ oświadczenia | Opis |
|-------------|-------------|
| *UserId* | Nazwa użytkownika |
| *signInName* | Zaloguj się w nazwie |
| *Identyfikator dzierżawy* | Identyfikator dzierżawy (identyfikator) obiektu użytkownika w usłudze Azure AD B2C |
| *objectId* | Obiekt identyfikator obiektu użytkownika w usłudze Azure AD B2C |
| *Hasło* | Hasło |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Zasady haseł używana przez usługę Azure AD B2C do określenia siły hasła, wygaśnięcia itp. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Numer telefonu użytkownika |
| *Verified.strongAuthenticationPhoneNumber* | |
| *Adres e-mail* | Adres e-mail, który może służyć do kontaktowania się z użytkownika |
| *signInNamesInfo.emailAddress* | Adres e-mail, który użytkownik może użyć do logowania |
| *otherMails* | Adresy e-mail, które mogą służyć do kontaktowania się z użytkownika |
| *userPrincipalName* | Nazwa użytkownika, ponieważ przechowywane w usłudze Azure AD B2C |
| *upnUserName* | Nazwa użytkownika do tworzenia głównej nazwy użytkownika |
| *mailNickName* | Nazwa nick poczty użytkownika przechowywane w usłudze Azure AD B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Oświadczenia, który określa, czy atrybuty były zbierane od użytkownika |
| *executed-PhoneFactor-Input* | Oświadczenia, który określa, czy nowy numer telefonu został zebrany od użytkownika |
| *authenticationSource* | Określa, czy użytkownik został uwierzytelniony w dostawcy tożsamości społecznościowych, login.microsoftonline.com lub konto lokalne |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Oświadczenia wymagane parametry ciągu zapytania i inne parametry specjalne

Poniższe oświadczenia są wymagane do przekazania w specjalne parametry (w tym niektóre parametry ciągu zapytania) w odniesieniu do innych dostawców oświadczeń:

| Typ oświadczenia | Opis |
|-------------|-------------|
| *nux* | Specjalne parametr został przekazany do uwierzytelniania lokalnego konta do login.microsoftonline.com |
| *Asystent NCA* | Specjalne parametr został przekazany do uwierzytelniania lokalnego konta do login.microsoftonline.com |
| *wiersz* | Specjalne parametr został przekazany do uwierzytelniania lokalnego konta do login.microsoftonline.com |
| *mkt* | Specjalne parametr został przekazany do uwierzytelniania lokalnego konta do login.microsoftonline.com |
| *lc* | Specjalne parametr został przekazany do uwierzytelniania lokalnego konta do login.microsoftonline.com |
| *grant_type* | Specjalne parametr został przekazany do uwierzytelniania lokalnego konta do login.microsoftonline.com |
| *Zakres* | Specjalne parametr został przekazany do uwierzytelniania lokalnego konta do login.microsoftonline.com |
| *client_id* | Specjalne parametr został przekazany do uwierzytelniania lokalnego konta do login.microsoftonline.com |
| *objectIdFromSession* | Parametr domyślny dostawca zarządzania sesji do wskazania pobrania Identyfikatora obiektu w sesji logowania jednokrotnego |
| *isActiveMFASession* | Parametr Zarządzanie sesjami MFA, aby wskazać, czy użytkownik ma aktywnej sesji usługi MFA |

### <a name="additional-optional-claims-that-can-be-collected"></a>Dodatkowe oświadczenia (opcjonalnie), które mogą być zbierane

Poniższe oświadczenia są dodatkowe oświadczenia, które mogą być zbierane od użytkowników, przechowywane w katalogu i wysyłane w tokenie. Opisany przed dodatkowe oświadczenia można dodać do tej listy.

| Typ oświadczenia | Opis |
|-------------|-------------|
| *givenName* | Imię użytkownika (nazywanego także imię) |
| *surname* | Nazwisko użytkownika (znany także jako nazwa rodziny lub nazwisko) |
| *Extension_picture* | Obraz użytkownika z ubezpieczenia |

## <a name="claim-transformations"></a>Przekształcenia oświadczeń

Przekształcenia oświadczeń dostępne są wymienione poniżej.

| Przekształcania oświadczeń | Opis |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definicji zawartości

W tej sekcji opisano definicji zawartości już zadeklarowana w *B2C_1A_base* zasad. Te definicje zawartości są podatne na odwołania, zastąpione lub rozszerzone zgodnie z potrzebami w własne zasady, a także jak w *B2C_1A_base_extensions* zasad.

| Dostawcy oświadczeń | Opis |
|-----------------|-------------|
| *Facebook* | |
| *Logowanie za pomocą konta lokalnego* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Samodzielna potwierdzony* | |
| *Konto lokalne* | |
| *Zarządzanie sesjami* | |
| *Trustframework Policy Engine* | |
| *TechnicalProfiles* | |
| *Wystawca tokenu* | |

## <a name="technical-profiles"></a>Profile techniczne

W tej sekcji przedstawiono profile techniczne już zadeklarowany na dostawcy oświadczeń w *B2C_1A_base* zasad. Te profile techniczne są podatne na można dodatkowo odwołania, zastąpione i/lub rozszerzone zgodnie z potrzebami w własne zasady, a także jak w *B2C_1A_base_extensions* zasad.

### <a name="technical-profiles-for-facebook"></a>Profile techniczne za pomocą konta Facebook

| Profil techniczny | Opis |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Profile techniczne dla logowanie za pomocą konta lokalnego

| Profil techniczny | Opis |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Profile techniczne dla aplikacji Phone Factor

| Profil techniczny | Opis |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Profile techniczne usługi Azure Active Directory

| Profil techniczny | Opis |
|-------------------|-------------|
| *AAD-Common* | Profil techniczny dołączane przez inne usługi AAD-xxx profile techniczne |
| *AAD-UserWriteUsingAlternativeSecurityId* | Profil techniczny dla logowania społecznościowego |
| *AAD-UserReadUsingAlternativeSecurityId* | Profil techniczny dla logowania społecznościowego |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Profil techniczny dla logowania społecznościowego |
| *AAD-UserWritePasswordUsingLogonEmail* | Profil techniczny dla kont lokalnych |
| *AAD-UserReadUsingEmailAddress* | Profil techniczny dla kont lokalnych |
| *AAD-UserWriteProfileUsingObjectId* | Profil techniczny aktualizowania rekordu użytkownika przy użyciu obiektu |
| *AAD-UserWritePhoneNumberUsingObjectId* | Profil techniczny aktualizowania rekordu użytkownika przy użyciu obiektu |
| *AAD-UserWritePasswordUsingObjectId* | Profil techniczny aktualizowania rekordu użytkownika przy użyciu obiektu |
| *AAD-UserReadUsingObjectId* | Profil techniczny służy do odczytywania danych po użytkownik jest uwierzytelniany |

### <a name="technical-profiles-for-self-asserted"></a>Profile techniczne dla potwierdzone samoobsługowego

| Profil techniczny | Opis |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Profile techniczne dla lokalnego konta

| Profil techniczny | Opis |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Profile techniczne dla sesji zarządzania

| Profil techniczny | Opis |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Nazwa profilu jest używana do odróżniania sesji usługi AAD między logowania się i zaloguj się |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Profile techniczne dla aparatu zasad framework zaufania

Obecnie nie profile techniczne są zdefiniowane dla **profili Technicalprofile aparatu zasad Trustframework** dostawcy oświadczeń.

### <a name="technical-profiles-for-token-issuer"></a>Profile techniczne dla wystawcy tokenów

| Profil techniczny | Opis |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Podróże użytkownika

W tej sekcji przedstawiono podróży użytkownika już zadeklarowana w *B2C_1A_base* zasad. Te podróży użytkownika są podatne na można dodatkowo odwołania, zastąpione i/lub rozszerzone zgodnie z potrzebami w własne zasady, a także jak w *B2C_1A_base_extensions* zasad.

| Podróż użytkownika | Opis |
|--------------|-------------|
| *Rejestracja* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
