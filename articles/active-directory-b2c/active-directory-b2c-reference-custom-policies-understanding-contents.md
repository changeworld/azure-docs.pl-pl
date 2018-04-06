---
title: 'Usługa Azure Active Directory B2C: Opis zasady niestandardowe pakietu starter | Dokumentacja firmy Microsoft'
description: Temat dotyczący zasad niestandardowych usługi Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.openlocfilehash: 12f63bc42f8450f086ed9f0e8d598c9c91a0c3d4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Opis zasad niestandardowych początkowego pakietu Azure AD B2C niestandardowych zasad

Ta sekcja zawiera listę wszystkich elementów podstawowych zasad B2C_1A_base, który jest dostarczany z **pakiet początkowy** i która jest wykorzystywana do tworzenia własnych zasad za pomocą dziedziczenia obiektu *B2C_1A_base_extensions zasad* .

Tak on bardziej szczegółowo skupia się na typy oświadczeń już zdefiniowane, przekształcenia oświadczeń, definicje zawartości, dostawców oświadczeń z ich profile technicznych i podróże użytkownika core.

> [!IMPORTANT]
> Microsoft nie udziela żadnych gwarancji, wprost względem z informacji podanych poniżej lub. Zmiany mogą być wprowadzane w dowolnym momencie przed upływem terminu GA w czasie GA lub po.

Zarówno własnych zasad, jak i zasady B2C_1A_base_extensions można zastąpić te definicje i rozszerzyć te zasady nadrzędnego zgodnie z potrzebami, podając te dodatkowe.

Elementy podstawowe *zasad B2C_1A_base* są typy oświadczeń, przekształcenia oświadczeń i definicje zawartości. Te elementy można wrażliwych odwoływać się do własnych zasad, a także jako w *zasad B2C_1A_base_extensions*.

## <a name="claims-schemas"></a>Schematy oświadczeń

Oświadczenia to schematów jest podzielone na trzy części:

1.  Pierwsza sekcja wymieniono minimalne oświadczenia, które są wymagane dla podróże użytkownika do poprawnego działania.
2.  Drugiej sekcji lista oświadczeń wymagany w przypadku parametrów ciągu zapytania i inne specjalne parametry do przekazania do innych dostawców oświadczeń, szczególnie login.microsoftonline.com do uwierzytelniania. **Nie Modyfikuj tych oświadczeń**.
3.  I po pewnym czasie trzeci sekcja, która wyświetla wszelkie dodatkowe, opcjonalne oświadczenia, które mogą być zbierane od użytkownika, przechowywane w katalogu i wysłane w tokenach podczas logowania. W tej sekcji można dodać nowy typ oświadczenia zbierane od użytkownika i/lub w tokenie.

> [!IMPORTANT]
> Schemat oświadczeń zawiera ograniczenia dotyczące określonych oświadczeń, takich jak nazwy użytkowników i hasła. Zasady zaufania Framework (TF) traktuje usługi Azure AD jako innego dostawcy oświadczeń i wszystkie jego ograniczenia są modelowany w zasady niestandardowe. Aby dodać więcej ograniczeń, lub użyj innego dostawcy oświadczeń dla magazynu poświadczeń, który ma własną ograniczenia mogły zostać zmodyfikowane zasady.

Poniżej przedstawiono typy oświadczeń dostępne.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Oświadczenia, które są wymagane w przypadku podróży użytkownika

Następujące oświadczenia są wymagane dla użytkownika podróże do poprawnego działania:

| Typ oświadczenia | Opis |
|-------------|-------------|
| *Nazwa użytkownika* | Nazwa użytkownika |
| *signInName* | Zaloguj się w nazwie |
| *Dla identyfikatora dzierżawcy* | Identyfikator dzierżawy (ID) obiektu użytkownika w usłudze Azure AD B2C |
| *objectId* | Identyfikator obiektu (ID) obiektu użytkownika w usłudze Azure AD B2C |
| *Hasło* | Hasło |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Zasady haseł używany przez usługę Azure AD B2C w celu określenia siły hasła, wygaśnięcia itp. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Numer telefonu użytkownika |
| *Verified.strongAuthenticationPhoneNumber* | |
| *Adres e-mail* | Adres e-mail, który może służyć do kontaktowania się z użytkownika |
| *signInNamesInfo.emailAddress* | Adres e-mail, który użytkownik może użyć do logowania |
| *otherMails* | Adresy e-mail, które mogą być używane do kontaktu użytkownik |
| *userPrincipalName* | Nazwa użytkownika, jak przechowywane w usłudze Azure AD B2C |
| *upnUserName* | Nazwa użytkownika do tworzenia głównej nazwy użytkownika |
| *mailNickName* | Nazwa użytkownika poczty nick przechowywanej w usłudze Azure AD B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Oświadczenie, które określa, czy atrybuty zostały zebrane przez użytkownika |
| *executed-PhoneFactor-Input* | Oświadczenie, które określa, czy nowy numer telefonu został zebrany przez użytkownika |
| *authenticationSource* | Określa, czy użytkownik został uwierzytelniony w społecznościowych dostawcy tożsamości, login.microsoftonline.com lub lokalnego konta |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Oświadczenia wymagane w celu parametrów ciągu zapytania i inne parametry specjalne

Następujące oświadczenia są wymagane do przekazania na specjalne parametry (w tym niektórych parametrów ciągu zapytania) do innych dostawców oświadczeń:

| Typ oświadczenia | Opis |
|-------------|-------------|
| *nux* | Specjalne parametr przekazany do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *nca* | Specjalne parametr przekazany do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *wiersz* | Specjalne parametr przekazany do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *mkt* | Specjalne parametr przekazany do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *lc* | Specjalne parametr przekazany do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *grant_type* | Specjalne parametr przekazany do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *Zakres* | Specjalne parametr przekazany do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *client_id* | Specjalne parametr przekazany do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *objectIdFromSession* | Parametr udostępniane przez dostawcę zarządzania sesji domyślnej, aby wskazać, że identyfikator obiektu zostały pobrane z sesji rejestracji Jednokrotnej |
| *isActiveMFASession* | Udostępniony parametr przez Zarządzanie sesjami MFA wskazująca, czy użytkownik ma aktywnej sesji usługi MFA |

### <a name="additional-optional-claims-that-can-be-collected"></a>Dodatkowe oświadczenia (opcjonalnie), które mogą być zbierane

Następujące oświadczenia są dodatkowe oświadczenia, które mogą być zebrane od użytkowników, przechowywane w katalogu i wysłane w tokenie. Zgodnie z opisem przed, dodatkowe oświadczeń można dodać do tej listy.

| Typ oświadczenia | Opis |
|-------------|-------------|
| *givenName* | Imię użytkownika (znanej także jako nazwa pierwszej) |
| *surname* | Nazwisko użytkownika (znanej także jako nazwa rodziny lub nazwisko) |
| *Extension_picture* | Obraz użytkownika z społecznego |

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

## <a name="content-definitions"></a>Definicje zawartości

W tej sekcji opisano zawartości definicje już zadeklarowany w *B2C_1A_base* zasad. Te definicje zawartości są podatne na odwołuje się do, przesłonięcia i rozszerzony w własnych zasad, a także jako w miarę potrzeb *B2C_1A_base_extensions* zasad.

| Dostawcy oświadczeń | Opis |
|-----------------|-------------|
| *Facebook* | |
| *Logowanie konta lokalnego* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Samodzielnie potwierdzony* | |
| *Konto lokalne* | |
| *Zarządzanie sesjami* | |
| *Trustframework Policy Engine* | |
| *TechnicalProfiles* | |
| *Wystawca tokenu* | |

## <a name="technical-profiles"></a>Profile techniczne

W tej sekcji przedstawiono techniczne profile już zadeklarowana dla dostawcy oświadczeń w *B2C_1A_base* zasad. Te profile techniczne są podatne na dalsze odwołuje się do, zastąpiona, i/lub być rozszerzony w własnych zasad, a także jako w miarę potrzeb *B2C_1A_base_extensions* zasad.

### <a name="technical-profiles-for-facebook"></a>Profile techniczne dla usługi Facebook

| Profil techniczne | Opis |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Profile techniczne dla lokalnego konta logowanie

| Profil techniczne | Opis |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Profile techniczne dla aplikacji Phone Factor

| Profil techniczne | Opis |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Profile techniczne dotyczące usługi Azure Active Directory

| Profil techniczne | Opis |
|-------------------|-------------|
| *AAD-Common* | Techniczne dołączonego przez innych profilów techniczne AAD xxx profilu |
| *AAD-UserWriteUsingAlternativeSecurityId* | Profil techniczne dla logowania społecznościowych |
| *AAD-UserReadUsingAlternativeSecurityId* | Profil techniczne dla logowania społecznościowych |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Profil techniczne dla logowania społecznościowych |
| *AAD-UserWritePasswordUsingLogonEmail* | Profil techniczne dla kont lokalnych |
| *AAD-UserReadUsingEmailAddress* | Profil techniczne dla kont lokalnych |
| *AAD-UserWriteProfileUsingObjectId* | Profil techniczne aktualizowania rekordu użytkownika przy użyciu objectId |
| *AAD-UserWritePhoneNumberUsingObjectId* | Profil techniczne aktualizowania rekordu użytkownika przy użyciu objectId |
| *AAD-UserWritePasswordUsingObjectId* | Profil techniczne aktualizowania rekordu użytkownika przy użyciu objectId |
| *AAD-UserReadUsingObjectId* | Techniczne profil jest używany do odczytywania danych po uwierzytelnia użytkownika |

### <a name="technical-profiles-for-self-asserted"></a>Profile techniczne dla potwierdzone samoobsługowego

| Profil techniczne | Opis |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Profile techniczne dla lokalnego konta

| Profil techniczne | Opis |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Profile techniczne dla sesji zarządzania

| Profil techniczne | Opis |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM SocialSignup* | Nazwa profilu jest używana do odróżniania sesji AAD między logowania się i zaloguj się |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-trustframework-policy-engine-technicalprofiles"></a>Profile techniczne dla TechnicalProfiles aparatu zasad Trustframework

Obecnie brak techniczne profilów są definiowane dla **TechnicalProfiles aparatu zasad Trustframework** dostawcy oświadczeń.

### <a name="technical-profiles-for-token-issuer"></a>Profile techniczne dla wystawcy tokenów

| Profil techniczne | Opis |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Podróże użytkownika

W tej sekcji przedstawiono podróże użytkownik już zadeklarowany w *B2C_1A_base* zasad. Te podróże użytkownika są podatne na dalsze odwołuje się do, zastąpiona, i/lub być rozszerzony w własnych zasad, a także jako w miarę potrzeb *B2C_1A_base_extensions* zasad.

| Przebieg użytkownika | Opis |
|--------------|-------------|
| *Rejestracja* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
