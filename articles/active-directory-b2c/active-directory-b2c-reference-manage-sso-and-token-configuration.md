---
title: Zarządzanie logowania jednokrotnego i dostosowywanie tokenu za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zarządzaniu logowania jednokrotnego i dostosowywanie tokenu za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2033d37a4a847380003fb95243138082df804bbf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703381"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Zarządzanie logowania jednokrotnego i dostosowywanie tokenu za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

Ten artykuł zawiera informacje o jak można zarządzać z tokenów, sesji i konfiguracji z logowania jednokrotnego (SSO) przy użyciu [zasady niestandardowe](active-directory-b2c-overview-custom.md) w usłudze Azure Active Directory (Azure AD) B2C.

## <a name="token-lifetimes-and-claims-configuration"></a>Konfiguracja tokenu okresy istnienia i oświadczenia

Aby zmienić ustawienia na Twoje okresów istnienia tokenu, można dodać [ClaimsProviders](claimsproviders.md) elementu w pliku strony jednostki uzależnionej zasady, które chcesz mieć wpływ na.  **ClaimsProviders** element jest elementem podrzędnym [elementu TrustFrameworkPolicy](trustframeworkpolicy.md) elementu. 

Wstaw element ClaimsProviders między elementem BasePolicy i element RelyingParty pliku strony jednostki uzależnionej.

Wewnątrz należy umieścić informacje, które ma wpływ na Twoje okresów istnienia tokenu. Plik XML wygląda następująco:

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

Następujące wartości są ustawione w poprzednim przykładzie:

- **Dostęp do okresów istnienia tokenu** — czas życia tokenu dostępu wartość została ustawiona za pomocą **token_lifetime_secs** elementu metadanych. Wartość domyślna to 3600 sekund (60 minut).
- **Czas życia tokenu Identyfikatora** — czas życia tokenu Identyfikatora wartość została ustawiona za pomocą **id_token_lifetime_secs** elementu metadanych. Wartość domyślna to 3600 sekund (60 minut).
- **Czas życia tokenu odświeżania** — czas życia tokenu odświeżania wartość została ustawiona za pomocą **refresh_token_lifetime_secs** elementu metadanych. Wartość domyślna to 1209600 sekund (14 dni).
- **Czas życia okna przewijania tokenu odświeżania** — Jeśli chcesz Ustaw okres istnienia okna przewijania tokenu odświeżania, ustaw wartość **rolling_refresh_token_lifetime_secs** elementu metadanych. Wartość domyślna to 7776000 (90 dni). Jeśli nie chcesz wymusić okres istnienia okna przewijania, należy zastąpić element o `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Oświadczenie wystawcy (iss)** -Oświadczenie wystawcy (iss) została ustawiona za pomocą **IssuanceClaimPattern** elementu metadanych. Odpowiednie wartości są `AuthorityAndTenantGuid` i `AuthorityWithTfp`.
- **Ustawienie oświadczenie reprezentujące identyfikator zasad** — opcje ustawienie tej wartości są `TFP` (framework zasady zaufania) i `ACR` (odwołanie w kontekście uwierzytelniania). `TFP` jest to zalecana wartość. Ustaw **AuthenticationContextReferenceClaimPattern** z wartością `None`. 

    W **ClaimsSchema** elementu Dodawanie tego elementu: 
    
    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```
    
    W swojej **OutputClaims** elementu Dodawanie tego elementu:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    W przypadku rejestru Azure container Registry, usunąć **AuthenticationContextReferenceClaimPattern** elementu.

- **Oświadczenia podmiotu (pod)** — tę opcję, wartość domyślna to identyfikator obiektu, jeśli chcesz to ustawienie, aby przełączyć `Not Supported`, Zastąp ten wiersz: 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    w tym:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Zachowanie sesji i logowania jednokrotnego

Aby zmienić zachowanie sesji i logowania jednokrotnego konfiguracji, należy dodać **UserJourneyBehaviors** wewnątrz elementu [RelyingParty](relyingparty.md) elementu.  **UserJourneyBehaviors** element należy natychmiast wykonać **DefaultUserJourney**. Wewnątrz swojej **UserJourneyBehavors** element powinien wyglądać następująco:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Następujące wartości są konfigurowane w poprzednim przykładzie:

- **Jednokrotne logowanie jednokrotne (SSO)** — logowanie jednokrotne jest konfigurowana **SingleSignOn**. Odpowiednie wartości są `Tenant`, `Application`, `Policy`, i `Suppressed`. 
- **Aplikacja sieci Web okres istnienia sesji (w minutach)** — sesji aplikacji sieci web okres istnienia została ustawiona za pomocą **SessionExpiryInSeconds** elementu. Wartość domyślna to 86400 sekund (1440 minut).
- **Limit czasu sesji aplikacji sieci Web** — limit czasu można ustawić za pomocą sesji aplikacji sieci web **wartość SessionExpiryType** elementu. Odpowiednie wartości są `Absolute` i `Rolling`.
