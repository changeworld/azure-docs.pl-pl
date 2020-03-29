---
title: Zarządzanie znakiem sytym i dostosowywaniem tokenów przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o zarządzaniu sytuacyjnym i dostosowywaniem tokenów przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff9f57af92c50c0df6f628113bd9490ca83e1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189297"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Zarządzanie sytuacyjnym i dostosowywaniem tokenów przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

Ten artykuł zawiera informacje dotyczące sposobu zarządzania konfiguracjami tokenu, sesji i logowania jednokrotnego przy użyciu [zasad niestandardowych](custom-policy-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C).

## <a name="token-lifetimes-and-claims-configuration"></a>Okresy istnienia tokenu i konfiguracja oświadczeń

Aby zmienić ustawienia w okresach istnienia tokenu, należy dodać [ClaimsProviders](claimsproviders.md) element w pliku jednostki uzależnianej zasad, które mają mieć wpływ.  Element **ClaimsProviders** jest elementem podrzędnym [elementu TrustFrameworkPolicy.](trustframeworkpolicy.md)

Wstaw claimsproviders element między BasePolicy element i RelyingParty element pliku jednostki uzależnionej.

Wewnątrz należy umieścić informacje, które mają wpływ na okres istnienia tokenu. Kod XML wygląda następująco w tym przykładzie:

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

W poprzednim przykładzie są ustawione następujące wartości:

- **Okres istnienia tokenu dostępu** — wartość okresu istnienia tokenu dostępu jest ustawiona z **token_lifetime_secs** elementem metadanych. Wartość domyślna to 3600 sekund (60 minut).
- **Okres istnienia tokenu identyfikatora** — wartość okresu istnienia tokenu identyfikatora jest ustawiana z elementem **metadanych id_token_lifetime_secs.** Wartość domyślna to 3600 sekund (60 minut).
- **Okres istnienia tokenu odświeżania** — wartość okresu istnienia tokenu odświeżania jest ustawiana z elementem **metadanych refresh_token_lifetime_secs.** Wartość domyślna to 1209600 sekund (14 dni).
- **Okres istnienia okna przesuwnego tokenu odświeżania** — jeśli chcesz ustawić okres istnienia okna przesuwnego na token odświeżania, ustaw wartość **rolling_refresh_token_lifetime_secs** elementu metadanych. Wartość domyślna to 7776000 (90 dni). Jeśli nie chcesz wymuszać okresu istnienia okna przesuwnego, zastąp element `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Oświadczenie emitenta (iss)** - Oświadczenie Emitenta (iss) jest ustawiane z elementem metadanych **IssuanceClaimPattern.** Stosowane wartości `AuthorityAndTenantGuid` są `AuthorityWithTfp`i .
- **Ustawianie oświadczenia reprezentującego identyfikator zasad** — opcje `TFP` ustawiania tej `ACR` wartości to (zasady struktury zaufania) i (odwołanie do kontekstu uwierzytelniania). `TFP`jest zalecaną wartością. Ustaw **AuthenticationContextReferenceClaimPattern** z `None`wartością .

    W **claimsschema** elementu, dodaj ten element:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    W **outputClaims** element, dodaj ten element:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    W przypadku usługi ACR usuń element **AuthenticationContextReferenceClaimPattern.**

- **Oświadczenie tematu (sub)** — ta opcja domyślnie ma wartość ObjectID, jeśli chcesz przełączyć to ustawienie na `Not Supported`, zastąp ten wiersz:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    z tą linią:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Zachowanie sesji i funkcja SSO

Aby zmienić zachowanie sesji i konfiguracje SSO, należy dodać **UserJourneyBehaviors** element wewnątrz [RelyingParty](relyingparty.md) elementu.  **UserJourneyBehaviors** element musi natychmiast wykonać **DefaultUserJourney**. Wnętrze **elementu UserJourneyBehavors** powinno wyglądać następująco w tym przykładzie:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

W poprzednim przykładzie skonfigurowano następujące wartości:

- **Logowanie jednokrotne (Logowanie jednokrotne)** — logowanie jednokrotne jest skonfigurowane za pomocą **programu SingleSignOn**. Obowiązującymi wartościami `Application` `Policy`są `Tenant` `Suppressed`, , i .
- **Limit czasu sesji aplikacji sieci Web** — limit czasu sesji aplikacji sieci web jest ustawiany za pomocą elementu **SessionExpiryType.** Stosowane wartości `Absolute` są `Rolling`i .
- **Okres istnienia sesji aplikacji sieci Web** — okres istnienia sesji aplikacji sieci web jest ustawiany za pomocą elementu **SessionExpiryInSeconds.** Wartość domyślna to 86400 sekund (1440 minut).
