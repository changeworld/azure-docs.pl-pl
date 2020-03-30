---
title: Weryfikowanie oświadczeń za pomocą kontrolek wyświetlania
titleSuffix: Azure AD B2C
description: Dowiedz się, jak używać formantów wyświetlania usługi Azure AD B2C do weryfikacji oświadczeń w procesach pozyskiwania użytkowników dostarczanych przez zasady niestandardowe.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188787"
---
# <a name="verification-display-control"></a>Kontrola wyświetlania weryfikacyjnego

Użyj [kontroli wyświetlania](display-controls.md) weryfikacji, aby zweryfikować oświadczenie, na przykład adres e-mail lub numer telefonu, z kodem weryfikacyjnym wysłanym do użytkownika.

## <a name="verificationcontrol-actions"></a>VerificationControl akcje

Kontrola wyświetlania weryfikacyjnego składa się z dwóch etapów (akcji):

1. Poproś użytkownika o miejsce docelowe, takie jak adres e-mail lub numer telefonu, na który należy wysłać kod weryfikacyjny. Gdy użytkownik wybierze przycisk **Wyślij kod,** wykonywana jest **akcja SendCode** formantu wyświetlania weryfikacji. **Akcja SendCode** generuje kod, konstruuje zawartość do wysłania i wysyła ją do użytkownika. Wartość adresu może być wstępnie wypełniona i służyć jako uwierzytelnianie drugiego czynnika.

    ![Przykładowa strona akcji wyślij kod](media/display-control-verification/display-control-verification-email-action-01.png)

1. Po wysłaniu kodu użytkownik odczytuje wiadomość, wprowadza kod weryfikacyjny do formantu dostarczonego przez kontrolę wyświetlania i wybiera **opcję Weryfikuj kod**. Wybierając **opcję Weryfikuj kod,** **akcja VerifyCode** jest wykonywana w celu zweryfikowania kodu skojarzonego z adresem. Jeśli użytkownik wybierze **wyślij nowy kod,** pierwsza akcja zostanie wykonana ponownie.

    ![Przykładowa strona weryfikacji akcji kodu](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>Wymagane elementy verificationControl

**VerificationControl** musi zawierać następujące elementy:

- Typ `DisplayControl` jest `VerificationControl`.
- `DisplayClaims`
  - **Wyślij do** — co najmniej jedno oświadczenia określające, do czego należy wysłać kod weryfikacyjny. Na przykład *adres e-mail* lub numer *telefonu* *kraju* .
  - **Kod weryfikacyjny** — oświadczenie kodu weryfikacyjnego, które użytkownik podaje po wysłaniu kodu. To oświadczenie musi być ustawione zgodnie `ControlClaimType` z wymaganiami, a musi być ustawiona na `VerificationCode`.
- Oświadczenie wyjściowe (opcjonalnie) do zwróconego do strony z własnym potwierdzeniem po zakończeniu procesu weryfikacji przez użytkownika. Na przykład *adres e-mail* lub numer *telefonu* *kraju* . Samodzielnie potwierdzony profil techniczny używa oświadczeń do utrwalenia danych lub bańki w górę oświadczeń danych wyjściowych do następnego kroku aranżacji.
- Dwa `Action`s z następującymi nazwami:
  - **SendCode** — wysyła kod do użytkownika. Ta akcja zwykle zawiera dwa sprawdzanie poprawności profilu technicznego, aby wygenerować kod i wysłać go.
  - **VerifyCode** — weryfikuje kod. Ta akcja zwykle zawiera jeden profil techniczny sprawdzania poprawności.

W poniższym przykładzie na stronie jest wyświetlany pole tekstowe **wiadomości e-mail.** Gdy użytkownik wprowadzi swój adres e-mail i wybierze **SendCode,** **sendcode** akcji jest wyzwalany w usłudze Azure AD B2C zaplecza.

Następnie użytkownik wprowadza **verificationCode** i wybiera **VerifyCode,** aby **wyzwolić verifycode** akcji w zapleczu. Jeśli wszystkie weryfikacje przebiegają, **VerificationControl** jest uważany za kompletny i użytkownik może przejść do następnego kroku.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
