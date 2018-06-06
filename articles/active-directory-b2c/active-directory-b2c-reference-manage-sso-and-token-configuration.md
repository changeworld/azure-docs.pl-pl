---
title: Zarządzanie logowania jednokrotnego i dostosowywania tokenu z niestandardowych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Informacje o zarządzaniu logowania jednokrotnego i dostosowywania token z zasadami niestandardowymi.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 43e392979c50d340a10575898edb25b119e1410b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712236"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Usługa Azure Active Directory B2C: Zarządzanie logowania jednokrotnego i tokenu możliwości dostosowania za pomocą zasad niestandardowych
Za pomocą niestandardowych zasad zapewnia kontrolę tego samego tokenu, sesjami i jednej konfiguracji logowania jednokrotnego (SSO) jako za pomocą wbudowanych zasad.  Aby dowiedzieć się, jakie każdego ustawienia zobacz dokumentację [tutaj](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Token konfiguracji okresy istnienia i oświadczenia
Aby zmienić ustawienia na Twojego tokenu okresy istnienia, musisz dodać `<ClaimsProviders>` elementu w pliku strony jednostki uzależnionej zasady będą miały wpływ.  `<ClaimsProviders>` Element jest elementem podrzędnym `<TrustFrameworkPolicy>`.  Wewnątrz należy umieścić informacje, które ma wpływ na Twoje istnienia tokenu.  Plik XML wygląda następująco:

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

**Okresy istnienia tokenu dostępu** -okres istnienia tokenu dostępu można zmienić, modyfikując wartość wewnątrz `<Item>` klucz = "token_lifetime_secs" w sekundach.  Wartość domyślna w wbudowane to 3600 sekund (60 minut).

**Okres istnienia tokenu identyfikator** — identyfikator okres istnienia tokenu można zmienić, modyfikując wartość wewnątrz `<Item>` klucz = "id_token_lifetime_secs" w sekundach.  Wartość domyślna w wbudowane to 3600 sekund (60 minut).

**Okres istnienia tokenu odświeżania** -okres istnienia tokenu odświeżania można zmienić, modyfikując wartość wewnątrz `<Item>` klucz = "refresh_token_lifetime_secs" w sekundach.  Wartość domyślna w wbudowane to 1209600 sekund (14 dni).

**Odśwież okres istnienia tokenu przesuwanego okna** — Jeśli chcesz ustawić zmienną okres istnienia okna token odświeżania, zmodyfikuj wartość wewnątrz `<Item>` klucz = "rolling_refresh_token_lifetime_secs" w sekundach.  Wartość domyślna w wbudowane to 7776000 (90 dni).  Jeśli nie chcesz wymusić przesuwanego okres istnienia okna, należy zastąpić ten wiersz:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Oświadczenia wystawcy (iss)** — Jeśli chcesz zmienić oświadczenia wystawcy (iss), zmodyfikuj wartość wewnątrz `<Item>` klucz = "IssuanceClaimPattern".  Są stosowane wartości `AuthorityAndTenantGuid` i `AuthorityWithTfp`.

**Ustawienie oświadczeń reprezentujący identyfikator zasad** — opcje ustawienie tej wartości są TFP (zasady zaufania framework) i ACR (odwołanie w kontekście uwierzytelniania).  
Zalecamy skonfigurowanie w tym TFP, aby to zrobić, upewnij się, `<Item>` z Key = "AuthenticationContextReferenceClaimPattern" istnieje, a wartość to `None`.
W Twojej `<OutputClaims>` elementu, należy dodać tego elementu:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
W przypadku ACR, usunąć `<Item>` klucz = "AuthenticationContextReferenceClaimPattern".

**Oświadczenia podmiotu (sub)** — ta opcja jest ustawiana domyślnie ObjectID, jeśli chcesz przełączyć się na `Not Supported`, wykonaj następujące czynności:

Zastąp ten wiersz 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
w tym:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Zachowanie sesji i logowania jednokrotnego

Aby zmienić zachowanie sesji i konfiguracje logowania jednokrotnego, musisz dodać `<UserJourneyBehaviors>` wewnątrz elementu `<RelyingParty>` elementu.  `<UserJourneyBehaviors>` Elementu musi występować zaraz po `<DefaultUserJourney>`.  Wewnątrz sieci `<UserJourneyBehavors>` element powinien wyglądać następująco:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Rejestracja jednokrotna w konfiguracji (rejestracji jednokrotnej SSO)** — Aby zmienić jednej konfiguracji logowania jednokrotnego, należy zmodyfikować wartość `<SingleSignOn>`.  Są stosowane wartości `Tenant`, `Application`, `Policy` i `Disabled`. 

**Aplikacja sieci Web okres istnienia sesji (w minutach)** — Aby zmienić aplikacji sieci web okres istnienia sesji, należy zmodyfikować wartość `<SessionExpiryInSeconds>` elementu.  Wartość domyślna w ramach zasad wbudowany to 86400 sekund (1440 minut).

**Limit czasu sesji aplikacji sieci Web** — Aby zmienić limit czasu sesji aplikacji sieci web, należy zmodyfikować wartość `<SessionExpiryType>`.  Są stosowane wartości `Absolute` i `Rolling`.
