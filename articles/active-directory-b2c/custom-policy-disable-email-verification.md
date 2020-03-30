---
title: Wyłączanie weryfikacji poczty e-mail podczas rejestracji klienta za pomocą zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak wyłączyć weryfikację poczty e-mail podczas rejestracji klienta w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136146"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Wyłączanie weryfikacji poczty e-mail podczas rejestracji klienta przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md). Musisz mieć działające zasady niestandardowe dotyczące rejestracji i logowania się za pomocą kont społecznościowych i lokalnych.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Dodawanie metadanych do samodzielnie potwierdzonego profilu technicznego

**LocalAccountSignUpWithLogonEmail** profil techniczny jest [samodzielnie potwierdzone](self-asserted-technical-profile.md), który jest wywoływany podczas przepływu rejestracji. Aby wyłączyć weryfikację wiadomości `EnforceEmailVerification` e-mail, ustaw metadane na false. Zastąp profile techniczne LocalAccountSignUpWithLogonEmail w pliku rozszerzenia. 

1. Otwórz plik rozszerzeń zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.
1. Znajdź `ClaimsProviders` element. Jeśli element nie istnieje, dodaj go.
1. Dodaj do elementu następującego dostawcę `ClaimsProviders` oświadczeń:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **subskrypcja Katalog +** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
3. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
4. Wybierz **platformę środowiska tożsamości**.
5. Wybierz **pozycję Przekaż zasady niestandardowe**, a następnie przekaż dwa zmienione pliki zasad.
2. Wybierz przekazane zasady rejestracji lub logowania, a następnie kliknij przycisk **Uruchom teraz.**
3. Powinieneś być w stanie zarejestrować się przy użyciu adresu e-mail bez sprawdzania poprawności.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [samodzielnie potwierdzonym profilu technicznym](self-asserted-technical-profile.md) w odwołaniu do IEF.
