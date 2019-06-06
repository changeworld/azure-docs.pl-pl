---
title: Definiowanie profilu technicznego sprawdzania poprawności w przypadku zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W przypadku zasad niestandardowych w usłudze Azure Active Directory B2C, należy zdefiniować profil techniczny usługi Azure Active Directory.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f20c3c6d6821b5a8bbdb74101095431f6f7f18f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511910"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego sprawdzania poprawności w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny sprawdzania poprawności jest zwykłe profilu technicznego z każdego protokołu, takich jak [usługi Azure Active Directory](active-directory-technical-profile.md) lub [interfejsu API REST](restful-technical-profile.md). Profil techniczny weryfikacji zwraca oświadczeń danych wyjściowych lub zwraca komunikat o błędzie HTTP 409 (kod stanu odpowiedzi konflikt,) przy użyciu następujących danych:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Oświadczenia, które są zwracane z profilu technicznego sprawdzania poprawności są dodawane do zbioru oświadczeń. W następnym profile techniczne sprawdzania poprawności, można użyć tych oświadczeń.

Profile techniczne sprawdzanie poprawności są wykonywane w kolejności, w jakiej występują w **ValidationTechnicalProfiles** elementu. Można skonfigurować w profilu technicznym weryfikacji, czy wykonywanie żadnych kolejnych weryfikacji profile techniczne powinno być kontynuowane, jeśli profil techniczny weryfikacji zgłasza błąd lub zakończy się pomyślnie.

Profilu technicznego mogą być wykonywane warunkowo weryfikacji oparte na zdefiniowanych w warunkach wstępnych **ValidationTechnicalProfile** elementu. Na przykład można sprawdzić, czy oświadczenia właściwe dla istnieje, lub czy roszczenie jest równy lub nie z podaną wartością.

Samodzielnie profilu technicznego mogą definiować profilu technicznego sprawdzania poprawności, służący do weryfikowania niektórych lub wszystkich jego oświadczeń danych wyjściowych. Wszystkie oświadczeń wejściowych, do którego istnieje odwołanie profilu technicznego musi znajdować się w profilu technicznego odwołujący się sprawdzanie poprawności oświadczeń danych wyjściowych.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Profil techniczny służący do weryfikowania niektórych lub wszystkich oświadczeń danych wyjściowych odwołujący się profilu technicznego. |

**ValidationTechnicalProfile** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego już zdefiniowane w zasadach lub zasady nadrzędne. |
|ContinueOnError|Nie| Wskazująca, czy sprawdzanie poprawności wszystkie profile techniczne kolejnych weryfikacji powinno być kontynuowane, zgłasza błąd, jeśli ten profil techniczny sprawdzania poprawności. Możliwe wartości: `true` lub `false` (ustawienie domyślne, zostanie zatrzymane przetwarzanie dalszych weryfikacji profile i komunikat o błędzie). |
|ContinueOnSuccess | Nie | Wskazująca, czy Weryfikacja profile żadnych kolejnych weryfikacji powinno być kontynuowane, jeśli ten profil techniczny sprawdzania poprawności zakończy się pomyślnie. Możliwe wartości: `true` lub `false`. Wartość domyślna to `true`, co oznacza, że przetwarzanie dalszych profilów sprawdzania poprawności będzie kontynuowane. |

**ValidationTechnicalProfile** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Warunki wstępne | 0:1 | Lista warunków wstępnych, które muszą zostać spełnione dla profilu technicznego sprawdzania poprawności do wykonania. |

**Wstępnym** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| `Type` | Yes | Typ wyboru lub zapytanie w celu przeprowadzenia warunek wstępny. Albo `ClaimsExist` jest określony, aby upewnić się, że akcje, należy wykonać, jeśli określone oświadczenia istnieje w bieżącym zestawie oświadczeń użytkownika lub `ClaimEquals` określono, że akcje można wykonać, jeśli istnieje w określonym oświadczenia, a jego wartość jest równa Określona wartość. |
| `ExecuteActionsIf` | Tak | Wskazuje, czy akcje warunek wstępny należy przeprowadzić, gdy wynikiem testu jest wartość PRAWDA lub FAŁSZ. |

**Wstępnym** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Wartość | 1: n | Dane, które jest używane przez wyboru. Jeśli typ to sprawdzanie jest `ClaimsExist`, to pole określa ClaimTypeReferenceId, aby wykonać zapytanie. Jeśli typ wyboru `ClaimEquals`, to pole określa ClaimTypeReferenceId, aby wykonać zapytanie. Podczas gdy inny element wartość zawiera wartość do sprawdzenia.|
| Akcja | 1:1 | Akcja, która należy podjąć, jeśli sprawdzanie warunku wstępnego w ramach kroku aranżacji ma wartość true. Wartość **akcji** ustawiono `SkipThisValidationTechnicalProfile`. Określa profil techniczny skojarzone sprawdzania poprawności nie powinien być wykonywany. |

### <a name="example"></a>Przykład

Poniższy przykład używa tych profilów pomoc sprawdzania poprawności:

1. Pierwszy profil techniczny weryfikacji sprawdza poświadczenia użytkownika i nie Kontynuuj, jeśli błąd wystąpi, takie jak nieprawidłowa nazwa użytkownika lub nieprawidłowe hasło.
2. Dalej weryfikacji profilu technicznego, nie jest wykonywany czy oświadczenia userType nie istnieje, czy wartością userType jest `Partner`. Profil techniczny sprawdzania poprawności podejmie próbę odczytuj profil użytkownika z bazy danych klientów wewnętrznych i kontynuuje, jeśli wystąpi błąd, takich jak Usługa interfejsu API REST jest niedostępna lub dowolnego błędu wewnętrznego.
3. Ostatnie sprawdzanie poprawności profilu technicznego, nie jest wykonywany czy oświadczenia userType nie istniało, czy wartością userType jest `Customer`. Profil techniczny weryfikacji próbuje odczytuj profil użytkownika z bazy danych partnera wewnętrzny i kontynuuje, jeśli wystąpi błąd, takich jak Usługa interfejsu API REST jest niedostępna lub dowolnego błędu wewnętrznego.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
