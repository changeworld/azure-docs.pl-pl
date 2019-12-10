---
title: Składniki początkowego pakietu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Omówienie zasad w ramach pakietu Azure Active Directory B2C zasad niestandardowych.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7d49bd9af80b1bb9bd86466269b14ba0a47181e0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948185"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Informacje o zasadach niestandardowych Azure AD B2C

W tej sekcji wymieniono wszystkie podstawowe elementy zasad B2C_1A_base, które są dostarczane z **pakietem startowym** i które są wykorzystywane do tworzenia własnych zasad przez dziedziczenie zasad dotyczących *B2C_1A_base_extensions*.

W związku z tym, bardziej szczegółowo koncentruje się na już zdefiniowanych typach oświadczeń, przekształceniach oświadczeń, definicjach zawartości, dostawcach oświadczeń wraz z profilami technicznymi oraz w podróży użytkowników podstawowych.

> [!IMPORTANT]
> Firma Microsoft nie udziela żadnych gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji, które znajdują się w dalszej części. Zmiany mogą być wprowadzane w dowolnym momencie, przed upływem czasu, w tym czasie lub później.

Zarówno własne zasady, jak i zasady B2C_1A_base_extensions mogą przesłonić te definicje i rozłożyć te zasady nadrzędne, dostarczając dodatkowe dane w razie potrzeb.

Podstawowe elementy *zasad B2C_1A_base* to typy oświadczeń, przekształcenia oświadczeń i definicje zawartości. Te elementy mogą być podatne na odwołania do własnych zasad, a także *zasad B2C_1A_base_extensions*.

## <a name="claims-schemas"></a>Schematy oświadczeń

Te schematy oświadczeń są podzielone na trzy sekcje:

1.  Pierwsza sekcja, która zawiera listę minimalnych oświadczeń, które są wymagane do prawidłowego działania podróży użytkownika.
2.  Druga sekcja zawierająca listę oświadczeń wymaganych dla parametrów ciągu zapytania i innych parametrów specjalnych, które mają być przekazywane do innych dostawców oświadczeń, szczególnie login.microsoftonline.com na potrzeby uwierzytelniania. **Nie należy modyfikować tych oświadczeń**.
3.  I ostatecznie trzecią sekcję, która zawiera listę dodatkowych, opcjonalnych oświadczeń, które mogą być zbierane od użytkownika, przechowywane w katalogu i wysyłane w tokenach podczas logowania. Nowy typ oświadczeń do zebrania z użytkownika i/lub wysłanego w tokenie można dodać do tej sekcji.

> [!IMPORTANT]
> Schemat oświadczeń zawiera ograniczenia dotyczące niektórych oświadczeń, takich jak hasła i nazwy użytkowników. Zasady zaufania struktury (TF) traktują usługę Azure AD jako jakikolwiek inny dostawca oświadczeń i wszystkie jej ograniczenia są modelowane w niestandardowych zasadach. Zasady mogą być modyfikowane w celu dodania większej liczby ograniczeń lub użycia innego dostawcy oświadczeń do przechowywania poświadczeń, które będą miały własne ograniczenia.

Dostępne typy zgłoszeń są wymienione poniżej.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Oświadczenia, które są wymagane do podróży użytkownika

Następujące oświadczenia są wymagane do poprawnego działania podróży użytkowników:

| Typ oświadczeń | Opis |
|-------------|-------------|
| *UserId* | Nazwa użytkownika |
| *signInName* | Nazwa logowania |
| *tenantId* | Identyfikator dzierżawy obiektu użytkownika w Azure AD B2C |
| *objectId* | Identyfikator obiektu użytkownika w Azure AD B2C |
| *Hasło* | Hasło |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Zasady haseł używane przez Azure AD B2C do określania siły hasła, wygaśnięcia itp. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Numer telefonu użytkownika |
| *Verified.strongAuthenticationPhoneNumber* | |
| *Adres e-mail* | Adres e-mail, za pomocą którego można skontaktować się z użytkownikiem |
| *signInNamesInfo.emailAddress* | Adres e-mail, za pomocą którego użytkownik może się zalogować |
| *otherMails* | Adresy e-mail, które mogą być używane do kontaktowania się z użytkownikiem |
| *userPrincipalName* | Nazwa użytkownika jako przechowywana w Azure AD B2C |
| *upnUserName* | Nazwa użytkownika do tworzenia głównej nazwy użytkownika |
| *mailNickName* | Nazwa nick poczty użytkownika w postaci zapisanej w Azure AD B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Wartość określająca, czy atrybuty zostały zebrane przez użytkownika |
| *executed-PhoneFactor-Input* | Wniosek określający, czy nowy numer telefonu został zebrany od użytkownika |
| *authenticationSource* | Określa, czy użytkownik został uwierzytelniony przy użyciu dostawcy tożsamości społecznościowej, login.microsoftonline.com czy konta lokalnego |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Oświadczenia wymagane dla parametrów ciągu zapytania i innych parametrów specjalnych

Następujące oświadczenia są wymagane do przekazywania specjalnych parametrów (w tym parametrów ciągu zapytania) do innych dostawców oświadczeń:

| Typ oświadczeń | Opis |
|-------------|-------------|
| *nux* | Szczególny parametr przeszedł do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *Asystent* | Szczególny parametr przeszedł do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *pytać* | Szczególny parametr przeszedł do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *mkt* | Szczególny parametr przeszedł do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *lc* | Szczególny parametr przeszedł do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *grant_type* | Szczególny parametr przeszedł do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *Scope* | Szczególny parametr przeszedł do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *client_id* | Szczególny parametr przeszedł do uwierzytelniania konta lokalnego do login.microsoftonline.com |
| *objectIdFromSession* | Parametr dostarczony przez domyślnego dostawcę zarządzania sesją w celu wskazania, że identyfikator obiektu został pobrany z sesji logowania jednokrotnego |
| *isActiveMFASession* | Parametr udostępniany przez zarządzanie sesjami usługi MFA w celu wskazania, że użytkownik ma aktywną sesję usługi MFA |

### <a name="additional-optional-claims-that-can-be-collected"></a>Dodatkowe (opcjonalne) oświadczenia, które mogą być zbierane

Następujące oświadczenia są dodatkowymi oświadczeniami, które mogą być zbierane od użytkowników, przechowywane w katalogu i wysyłane w tokenie. Jak wskazano wcześniej, dodatkowe oświadczenia można dodać do tej listy.

| Typ oświadczeń | Opis |
|-------------|-------------|
| *givenName* | Imię i nazwisko użytkownika (znane także jako imię) |
| *surname* | Nazwisko użytkownika (znane także jako nazwa rodziny lub nazwisko) |
| *Extension_picture* | Obraz użytkownika z społeczności społecznej |

## <a name="claim-transformations"></a>Przekształcenia roszczeń

Poniżej wymieniono dostępne przekształcenia roszczeń.

| Przekształcanie roszczeń | Opis |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definicje zawartości

W tej sekcji opisano definicje zawartości zadeklarowane już w zasadach *B2C_1A_base* . Te definicje zawartości są podatne na odwołania, przesłonięte i/lub rozszerzone w zależności od potrzeb we własnych zasadach, a także zasady *B2C_1A_base_extensions* .

| Dostawca oświadczeń | Opis |
|-----------------|-------------|
| *Facebook* | |
| *Logowanie do konta lokalnego* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Samodzielnie potwierdzone* | |
| *Konto lokalne* | |
| *Zarządzanie sesjami* | |
| *Trustframework Policy Engine* | |
| *TechnicalProfiles* | |
| *Wystawca tokenu* | |

## <a name="technical-profiles"></a>Profile techniczne

Ta sekcja przedstawia profile techniczne już zadeklarowane dla każdego dostawcy oświadczenia w zasadach *B2C_1A_base* . Te profile techniczne są podatne na dalsze odwołania, przesłonięte i/lub rozszerzone w zależności od potrzeb we własnych zasadach, a także zasady *B2C_1A_base_extensions* .

### <a name="technical-profiles-for-facebook"></a>Profile techniczne usługi Facebook

| Profil techniczny | Opis |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Profile techniczne dla logowania do konta lokalnego

| Profil techniczny | Opis |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Profile techniczne dla współczynnika telefonu

| Profil techniczny | Opis |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Profile techniczne dla Azure Active Directory

| Profil techniczny | Opis |
|-------------------|-------------|
| *AAD-Common* | Profil techniczny obejmujący inne profile techniczne usługi AAD-XXX |
| *AAD-UserWriteUsingAlternativeSecurityId* | Profil techniczny dla logowań społecznościowych |
| *AAD-UserReadUsingAlternativeSecurityId* | Profil techniczny dla logowań społecznościowych |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Profil techniczny dla logowań społecznościowych |
| *AAD-UserWritePasswordUsingLogonEmail* | Profil techniczny dla kont lokalnych |
| *AAD-UserReadUsingEmailAddress* | Profil techniczny dla kont lokalnych |
| *AAD-UserWriteProfileUsingObjectId* | Profil techniczny do aktualizowania rekordu użytkownika przy użyciu identyfikatora obiektu |
| *AAD-UserWritePhoneNumberUsingObjectId* | Profil techniczny do aktualizowania rekordu użytkownika przy użyciu identyfikatora obiektu |
| *AAD-UserWritePasswordUsingObjectId* | Profil techniczny do aktualizowania rekordu użytkownika przy użyciu identyfikatora obiektu |
| *AAD-UserReadUsingObjectId* | Profil techniczny służy do odczytywania danych po uwierzytelnieniu użytkownika |

### <a name="technical-profiles-for-self-asserted"></a>Profile techniczne dla samodzielnie potwierdzonych

| Profil techniczny | Opis |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Profile techniczne dla konta lokalnego

| Profil techniczny | Opis |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Profile techniczne zarządzania sesją

| Profil techniczny | Opis |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Nazwa profilu jest używana w celu odróżnienia sesji usługi AAD między rejestracją i logowaniem |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Profile techniczne aparatu zasad struktury zaufania

Obecnie nie są zdefiniowane profile techniczne dla dostawcy oświadczeń **TechnicalProfiles aparatu zasad Trustframework** .

### <a name="technical-profiles-for-token-issuer"></a>Profile techniczne dla wystawcy tokenów

| Profil techniczny | Opis |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Podróże użytkownika

W tej sekcji przedstawiono przedziały użytkownika zadeklarowane już w zasadach *B2C_1A_base* . Te podróże użytkowników są podatne na dalsze odwołania, przesłonięte i/lub rozszerzone w zależności od potrzeb we własnych zasadach, a także zasady *B2C_1A_base_extensions* .

| Podróż użytkownika | Opis |
|--------------|-------------|
| *Zapisania* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
