---
title: ClaimsProviders — Usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Określ element ClaimsProvider zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dfb34085181e0b759d1d77485ff21b5bc59e0de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189773"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dostawca oświadczeń zawiera zestaw [profili technicznych](technicalprofiles.md). Każdy dostawca oświadczeń musi mieć jeden lub więcej profilów technicznych, które określają punkty końcowe i protokoły potrzebne do komunikowania się z dostawcą oświadczeń. Dostawca oświadczeń może mieć wiele profilów technicznych. Na przykład można zdefiniować wiele profilów technicznych, ponieważ dostawca oświadczeń obsługuje wiele protokołów, różnych punktów końcowych z różnymi możliwościami lub zwalnia różne oświadczenia na różnych poziomach pewności. Może być dopuszczalne, aby zwolnić poufnych oświadczeń w jednej podróży użytkownika, ale nie w innym.

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

Element **ClaimsProviders** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | Akredytowany dostawca oświadczeń, który może być wykorzystany w różnych procesach użytkownika. |

## <a name="claimsprovider"></a>ClaimsProvider

Element **ClaimsProvider** zawiera następujące elementy podrzędne:

| Element | Wystąpień | Opis |
| ------- | ---------- | ----------- |
| Domain | 0:1 | Ciąg zawierający nazwę domeny dostawcy oświadczeń. Jeśli na przykład dostawca oświadczeń zawiera profil techniczny facebooka, nazwa domeny jest Facebook.com. Ta nazwa domeny jest używana dla wszystkich profilów technicznych zdefiniowanych w dostawcy oświadczeń, chyba że zostanie zastąpiona przez profil techniczny. Do nazwy domeny można również odwoływać się w **domain_hint**. Aby uzyskać więcej informacji, zobacz **sekcję Przekieruj logowanie do dostawcy społecznościowego** [w obszarze Konfigurowanie bezpośredniego logowania przy użyciu usługi Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Ciąg zawierający nazwę dostawcy oświadczeń. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Zestaw profili technicznych obsługiwanych przez dostawcę oświadczeń |

**ClaimsProvider** organizuje, jak profile techniczne odnoszą się do dostawcy oświadczeń. W poniższym przykładzie pokazano dostawcę oświadczeń usługi Azure Active Directory z profilami technicznymi usługi Azure Active Directory:

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

W poniższym przykładzie przedstawiono dostawcę oświadczeń facebooka z profilem technicznym **Facebook-OAUTH.**

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
