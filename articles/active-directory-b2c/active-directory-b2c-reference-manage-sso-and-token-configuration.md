---
title: Zarządzanie logowania jednokrotnego i dostosowywanie tokenu za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zarządzaniu logowania jednokrotnego i dostosowywanie tokenu za pomocą zasad niestandardowych.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 811fb8b2de59c9d324ab4acb8b0f51b4cec80aee
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441801"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Usługa Azure Active Directory B2C: Zarządzanie logowania jednokrotnego i dostosowywanie tokenu za pomocą zasad niestandardowych
Za pomocą zasad niestandardowych zapewnia tych samych kontroli nad tokenu, sesji i jednej konfiguracji logowania jednokrotnego (SSO) jako za pomocą wbudowanych zasad.  Aby dowiedzieć się, jakie poszczególnych ustawień zobacz dokumentację [tutaj](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Konfiguracja tokenu okresy istnienia i oświadczenia
Aby zmienić ustawienia na Twoje okresów istnienia tokenu, musisz dodać `<ClaimsProviders>` elementu w pliku strony jednostki uzależnionej zasady, które chcesz mieć wpływ na.  `<ClaimsProviders>` Element jest elementem podrzędnym `<TrustFrameworkPolicy>`.  Wewnątrz należy umieścić informacje, które ma wpływ na Twoje okresów istnienia tokenu.  Plik XML wygląda następująco:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

**Dostęp do okresów istnienia tokenu** — czas życia tokenu dostępu, można zmienić, zmieniając wartość wewnątrz `<Item>` przy użyciu klucza = "token_lifetime_secs" w ciągu kilku sekund.  Wartość domyślna w wbudowane to 3600 sekund (60 minut).

**Czas życia tokenu Identyfikatora** -okres istnienia tokenu identyfikator można zmienić, zmieniając wartość wewnątrz `<Item>` przy użyciu klucza = "id_token_lifetime_secs" w ciągu kilku sekund.  Wartość domyślna w wbudowane to 3600 sekund (60 minut).

**Czas życia tokenu odświeżania** — czas życia tokenu odświeżania można zmienić, zmieniając wartość wewnątrz `<Item>` przy użyciu klucza = "refresh_token_lifetime_secs" w ciągu kilku sekund.  Wartość domyślna w wbudowane to 1209600 sekund (14 dni).

**Czas życia okna przewijania tokenu odświeżania** — Jeśli chcesz Ustaw okres istnienia okna przewijania tokenu odświeżania, zmodyfikuj wartość wewnątrz `<Item>` przy użyciu klucza = "rolling_refresh_token_lifetime_secs" w ciągu kilku sekund.  Wartość domyślna w wbudowane to 7776000 (90 dni).  Jeśli nie chcesz wymusić okres istnienia okna przewijania, należy zastąpić ten wiersz z:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Oświadczenie wystawcy (iss)** — Jeśli chcesz zmienić Oświadczenie wystawcy (iss), zmodyfikuj wartość wewnątrz `<Item>` przy użyciu klucza = "IssuanceClaimPattern".  Odpowiednie wartości są `AuthorityAndTenantGuid` i `AuthorityWithTfp`.

**Ustawienie oświadczenie reprezentujące identyfikator zasad** — opcje ustawienie tej wartości są TFP (zasady zaufania framework) i ACR (odwołanie w kontekście uwierzytelniania).  
Firma Microsoft zaleca ustawienie TFP, aby to zrobić, upewnij się, `<Item>` przy użyciu klucza = "AuthenticationContextReferenceClaimPattern" istnieje, a wartość to `None`.
W swojej `<OutputClaims>` , należy dodać ten element:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
W przypadku rejestru Azure container Registry, usunąć `<Item>` przy użyciu klucza = "AuthenticationContextReferenceClaimPattern".

**Oświadczenia podmiotu (pod)** — ta opcja jest ustawiana domyślnie ObjectID, jeśli chcesz przełączyć się na `Not Supported`, wykonaj następujące czynności:

Zamień ten wiersz 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
w tym:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Zachowanie sesji i logowania jednokrotnego

Aby zmienić zachowanie sesji i konfiguracji logowania jednokrotnego, musisz dodać `<UserJourneyBehaviors>` wewnątrz elementu `<RelyingParty>` elementu.  `<UserJourneyBehaviors>` Element należy natychmiast wykonać `<DefaultUserJourney>`.  Wewnątrz swojej `<UserJourneyBehavors>` element powinien wyglądać następująco:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Jednokrotne logowanie jednokrotne (SSO) konfiguracji** — Aby zmienić jednej konfiguracji logowania jednokrotnego, musisz zmodyfikować wartość `<SingleSignOn>`.  Odpowiednie wartości są `Tenant`, `Application`, `Policy` i `Disabled`. 

**Aplikacja sieci Web okres istnienia sesji (w minutach)** — Aby zmienić okres istnienia sesji aplikacji sieci web należy zmodyfikować wartość `<SessionExpiryInSeconds>` elementu.  Wartość domyślna w wbudowane zasady to 86400 sekund (1440 minut).

**Limit czasu sesji aplikacji sieci Web** — Aby zmienić limit czasu sesji aplikacji sieci web, należy zmodyfikować wartość `<SessionExpiryType>`.  Odpowiednie wartości są `Absolute` i `Rolling`.
