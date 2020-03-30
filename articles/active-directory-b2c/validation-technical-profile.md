---
title: Definiowanie profilu technicznego sprawdzania poprawności w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Sprawdzanie poprawności oświadczeń przy użyciu profilu technicznego sprawdzania poprawności w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1eaf159149bb353b1cf0474aad5bc233decddc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481572"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego sprawdzania poprawności w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny sprawdzania poprawności jest zwykłym profilem technicznym z dowolnego protokołu, takiego jak [usługa Azure Active Directory](active-directory-technical-profile.md) lub [interfejs API REST](restful-technical-profile.md). Profil techniczny sprawdzania poprawności zwraca oświadczenia wyjściowe lub zwraca 4xx kod stanu HTTP z następującymi danymi. Aby uzyskać więcej informacji, zobacz [zwracany komunikat o błędzie](restful-technical-profile.md#returning-error-message)

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Zakres oświadczeń danych wyjściowych profilu technicznego sprawdzania poprawności jest ograniczony do [samodzielnie potwierdzonego profilu technicznego,](self-asserted-technical-profile.md) który wywołuje profil techniczny sprawdzania poprawności i jego profile techniczne sprawdzania poprawności. Jeśli chcesz użyć oświadczeń danych wyjściowych w następnym kroku aranżacji, dodaj oświadczenia wyjściowe do samodzielnie potwierdzonego profilu technicznego, który wywołuje profil techniczny sprawdzania poprawności.

Sprawdzanie poprawności profili technicznych są wykonywane w sekwencji, które pojawiają się w **ValidationTechnicalProfiles** elementu. Można skonfigurować w profilu technicznym sprawdzania poprawności, czy wykonywanie kolejnych profilów technicznych sprawdzania poprawności należy kontynuować, jeśli profil techniczny sprawdzania poprawności zgłasza błąd lub zakończy się pomyślnie.

Profil techniczny sprawdzania poprawności może być warunkowo wykonane na podstawie warunków wstępnych zdefiniowanych w **ValidationTechnicalProfile** elementu. Na przykład można sprawdzić, czy istnieje określone oświadczenie lub czy oświadczenie jest równe lub nie do określonej wartości.

Samodzielnie potwierdzony profil techniczny może zdefiniować profil techniczny sprawdzania poprawności, który ma być używany do sprawdzania poprawności niektórych lub wszystkich jego oświadczeń wyjściowych. Wszystkie oświadczenia wejściowe przywołanego profilu technicznego muszą znajdować się w oświadczeń wyjściowych profilu technicznego weryfikacji odwołującej się do tego.

> [!NOTE]
> Tylko samodzielnie potwierdzone profile techniczne mogą używać profilów technicznych sprawdzania poprawności. Jeśli chcesz sprawdzić poprawność oświadczeń danych wyjściowych z nie-samodzielnie potwierdzonych profilów technicznych, należy rozważyć użycie dodatkowego kroku aranżacji w podróży użytkownika, aby pomieścić profil techniczny odpowiedzialny za sprawdzanie poprawności.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles (Profesorowie technologii)

Element **ValidationTechnicalProfiles** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile (Profiltechniczny) | 1:n | Profil techniczny, który ma być używany do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyjściowych odwołującego się do profilu technicznego. |

Element **ValidationTechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego już zdefiniowany w zasadach lub zasadach nadrzędnych. |
|Continueonerror|Nie| Wskazanie, czy sprawdzanie poprawności kolejnych profili technicznych sprawdzania poprawności należy kontynuować, jeśli ten profil techniczny sprawdzania poprawności powoduje błąd. Możliwe `true` wartości: `false` lub (domyślnie, przetwarzanie dalszych profili sprawdzania poprawności zostanie zatrzymane i zwrócony błąd). |
|ContinueOnSukces | Nie | Wskazując, czy sprawdzanie poprawności kolejnych profili sprawdzania poprawności należy kontynuować, jeśli ten profil techniczny sprawdzania poprawności powiedzie się. Możliwe `true` wartości: `false`lub . Wartość domyślna to `true`, co oznacza, że przetwarzanie dalszych profili sprawdzania poprawności będzie kontynuowane. |

Element **ValidationTechnicalProfile** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Warunki wstępne | 0:1 | Lista warunków wstępnych, które muszą być spełnione dla profilu technicznego sprawdzania poprawności do wykonania. |

Element **Warunek wstępny** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| `Type` | Tak | Typ sprawdzania lub kwerendy do wykonania dla warunku wstępnego. Albo `ClaimsExist` jest określony w celu zapewnienia, że akcje powinny być wykonywane, jeśli `ClaimEquals` określone oświadczenia istnieją w bieżącym zestawie oświadczeń użytkownika lub określono, że akcje powinny być wykonywane, jeśli istnieje określone oświadczenie, a jego wartość jest równa określonej wartości. |
| `ExecuteActionsIf` | Tak | Wskazuje, czy akcje w warunku wstępnym powinny być wykonywane, jeśli test jest true lub false. |

**Element Warunek wstępny** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Wartość | 1:n | Dane, które są używane przez czek. Jeśli typem tego `ClaimsExist`wyboru jest to pole, to pole określa ClaimTypeReferenceId do kwerendy. Jeśli typem czeku jest `ClaimEquals`to pole , to pole określa ClaimTypeReferenceId do kwerendy. Podczas gdy inny element wartości zawiera wartość, która ma być zaznaczona.|
| Akcja | 1:1 | Akcja, która powinna zostać podjęta, jeśli sprawdzanie warunku wstępnego w ramach kroku aranżacji jest true. Wartość **akcji** jest ustawiona `SkipThisValidationTechnicalProfile`na . Określa, że skojarzony profil techniczny sprawdzania poprawności nie powinien być wykonywany. |

### <a name="example"></a>Przykład

W poniższym przykładzie użyto tych profilów technicznych sprawdzania poprawności:

1. Pierwszy profil techniczny sprawdzania poprawności sprawdza poświadczenia użytkownika i nie jest kontynuowany, jeśli wystąpi błąd, taki jak nieprawidłowa nazwa użytkownika lub złe hasło.
2. Następny profil techniczny sprawdzania poprawności, nie jest wykonywany, jeśli oświadczenie userType nie istnieje `Partner`lub jeśli wartość userType jest . Profil techniczny sprawdzania poprawności próbuje odczytać profil użytkownika z wewnętrznej bazy danych klienta i kontynuować, jeśli wystąpi błąd, takich jak usługa interfejsu API REST nie jest dostępna lub błąd wewnętrzny.
3. Ostatni profil techniczny sprawdzania poprawności, nie jest wykonywany, jeśli oświadczenie userType nie istnieje `Customer`lub jeśli wartość userType jest . Profil techniczny sprawdzania poprawności próbuje odczytać profil użytkownika z wewnętrznej bazy danych partnera i kontynuuje, jeśli wystąpi błąd, takich jak usługa interfejsu API REST nie jest dostępna lub błąd wewnętrzny.

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
