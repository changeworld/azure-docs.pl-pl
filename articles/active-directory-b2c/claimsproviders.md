---
title: ClaimsProviders — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Określ element ClaimsProvider zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2d862d07a65c3fb28b49a82692ea575f787b9750
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313855"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dostawcy oświadczeń zawiera zbiór [profile techniczne](technicalprofiles.md). Każdego dostawcy oświadczeń musi mieć co najmniej jeden profil techniczne określające punktów końcowych i protokoły wymagane do komunikowania się z dostawcą oświadczeń. Dostawcy oświadczeń mogą mieć wiele profilów Technical Preview. Na przykład wiele profilów Technical Preview może być zdefiniowana, ponieważ obsługuje wiele protokołów, różne punkty końcowe z różnych możliwości dostawcy oświadczeń lub zwalnia różne oświadczenia na zapewnienie różnych poziomach. Może to być akceptowane oświadczenia poufnych w podróży jednego użytkownika, ale nie w innej wersji.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

**ClaimsProviders** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | Dostawca akredytowanych oświadczenia, który można wykorzystać w różnych podróży użytkownika. |

## <a name="claimsprovider"></a>ClaimsProvider

**ClaimsProvider** element zawiera następujących elementów podrzędnych:

| Element | Wystąpienia | Opis |
| ------- | ---------- | ----------- |
| Domain | 0:1 | Ciąg, który zawiera nazwę domeny dla dostawcy oświadczeń. Na przykład jeśli Twój dostawca oświadczeń zawiera profilu technicznego usługi Facebook, nazwa domeny jest Facebook.com. Ta nazwa domeny jest używana we wszystkich profilach techniczne zdefiniowane w dostawcy oświadczeń, chyba że zostaną zastąpione profilu technicznego. Nazwa domeny może także odwoływać się **domain_hint**. Aby uzyskać więcej informacji, zobacz **przekierowania Zaloguj się do dostawcy społecznościowych** części [skonfigurować bezpośrednie logowanie przy użyciu usługi Azure Active Directory B2C](direct-signin.md). |
| Nazwa wyświetlana | 0:1 | Ciąg, który zawiera nazwę dostawcy oświadczeń, które mogą być wyświetlane użytkownikom. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Zbiór profilów Technical Preview obsługiwana przez dostawcę oświadczeń |

**ClaimsProvider** organizuje, jak Twoje profile techniczne odnoszą się do dostawcy oświadczeń. Poniższy przykład pokazuje usługi Azure Active Directory dostawcy oświadczeń przy użyciu profilów techniczne usługi Azure Active Directory:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Poniższy przykład pokazuje usługi Facebook dostawcy oświadczeń przy użyciu **uwierzytelniania Facebook OAUTH** profilu technicznego.

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
