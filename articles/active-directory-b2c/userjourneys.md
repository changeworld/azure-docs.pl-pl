---
title: Podróży użytkowników | Dokumentacja firmy Microsoft
description: Określ element podróży użytkowników zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d2e6ad9aa9692efa4ea5633dff78b262bb1917be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512025"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Podróży użytkownika określić jawnego ścieżki, za pomocą których zasady umożliwia aplikacji jednostki uzależnionej można uzyskać żądanego oświadczenia dla użytkownika. Użytkownik jest pobierana za pośrednictwem tych ścieżek do pobierania oświadczeń, które są przedstawiane do jednostki uzależnionej. Innymi słowy podróży użytkownika zdefiniowanie logiki biznesowej, z jakiego użytkownik końcowy przechodzi przez jako procesy struktura środowiska tożsamości usługi Azure AD B2C żądania.

Te podróży użytkownika może być traktowany jako szablony można spełnić wymagania podstawowe różnych jednostki uzależnionej stron społeczności zainteresowania. Podróży użytkownika ułatwienia definicji jednostki uzależnionej strona część zasad. Zasady można zdefiniować wiele podróży użytkownika. Każdy podróż użytkownika jest sekwencji kroków aranżacji.

Aby zdefiniować trasach użytkownika obsługiwany przez zasady, **podróży użytkowników** element jest dodawany element najwyższego poziomu w pliku zasad. 

**Podróży użytkowników** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| UserJourney | 1: n | Podróż użytkownika, który definiuje wszystkie niezbędne usługi flow użytkownikowi pełne konstrukcji. | 

**UserJourney** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator podróży użytkownika, którego można odwołać się do niego z innych elementów w zasadach. **DefaultUserJourney** elementu [jednostki uzależnionej zasad firmy](relyingparty.md) wskazuje tego atrybutu. |

**UserJourney** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Sekwencję aranżacji, która musi występować za pośrednictwem transakcja zakończona powodzeniem. Podróż użytkownika, co składa się z uporządkowaną listą kroków aranżacji, które są wykonywane w kolejności. Jeśli którykolwiek z kroków zakończy się niepowodzeniem, transakcja nie powiedzie się. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Podróż użytkownika jest reprezentowany jako sekwencja aranżacji, która musi występować za pośrednictwem transakcja zakończona powodzeniem. Jeśli którykolwiek z kroków zakończy się niepowodzeniem, transakcja nie powiedzie się. Te kroki aranżacji odwoływać się bloki konstrukcyjne i dostawców oświadczeń są dozwolone w pliku zasad. Wszystkie kroki aranżacji, które odpowiada, aby wyświetlić lub renderowania środowisko użytkownika również odwołuje się do odpowiedniego identyfikatora definicji zawartości.

Organizowanie kroki mogą być wykonywane warunkowo, warunki wstępne zdefiniowane w elemencie kroku aranżacji w oparciu. Na przykład można sprawdzić, wykonać krok aranżacji, tylko wtedy, gdy istnieje określonych oświadczeń, lub czy roszczenie jest równy lub nie z podaną wartością. 

Aby określić uporządkowaną listą kroków aranżacji **OrchestrationSteps** element zostanie dodany jako część zasad. Ten element jest wymagany.

**OrchestrationSteps** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1: n | Krok aranżacji uporządkowany. | 

**OrchestrationStep** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| `Order` | Tak | Kolejność etapów aranżacji. | 
| `Type` | Yes | Typ kroku aranżacji. Możliwe wartości: <ul><li>**Elemencie ClaimsProviderSelection** — wskazuje, że kroku aranżacji przedstawia informacje o różnych dostawców oświadczeń użytkownikowi wybrać jeden z nich.</li><li>**CombinedSignInAndSignUp** — wskazuje, że kroku aranżacji przedstawia połączone mediów społecznościowych strona rejestracji dla kont logowania i lokalnych.</li><li>**Elementu ClaimsExchange** — wskazuje, że kroku aranżacji wymienia oświadczenia, za pomocą dostawcy oświadczeń.</li><li>**SendClaims** — wskazuje, że kroku aranżacji wysyła oświadczenia do jednostki uzależnionej przy użyciu tokenu wystawionego przez wystawcę oświadczenia.</li></ul> | 
| ContentDefinitionReferenceId | Nie | Identyfikator [zawartości definicji](contentdefinitions.md) skojarzony z tym krokiem aranżacji. Zazwyczaj identyfikator odwołania definicji zawartości jest zdefiniowany w profilu technicznym samodzielnie. Gdy usługa Azure AD B2C musi wyświetlić coś bez profilu technicznego istnieją jednak przypadki. Istnieją dwa przykłady — Jeśli typ kroku aranżacji jest jednym z następujących czynności: `ClaimsProviderSelection` lub `CombinedSignInAndSignUp`, wymaganych usługi Azure AD B2C, aby wyświetlić wyboru dostawcy tożsamości bez profilu technicznego. | 
| CpimIssuerTechnicalProfileReferenceId | Nie | Typ kroku aranżacji jest `SendClaims`. Ta właściwość określa identyfikator profilu technicznego dotyczącego dostawcy oświadczeń, który wystawia token dla jednostki uzależnionej.  Jeśli brak, jest tworzony token nie jednostki uzależnionej ze stron. |


**OrchestrationStep** element może zawierać następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- | 
| Warunki wstępne | 0: n | Lista warunków wstępnych, które muszą zostać spełnione dla kroku aranżacji do wykonania. | 
| ClaimsProviderSelections | 0: n | Lista wyboru dostawcy oświadczeń dla kroku aranżacji. | 
| ClaimsExchanges | 0: n | Lista wymiany oświadczeń dla kroku aranżacji. | 

### <a name="preconditions"></a>Warunki wstępne

**Warunki wstępne** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- | 
| Warunek wstępny | 0: n | W zależności od profilu technicznego używany albo przekierowuje klienta zgodnie z wyboru dostawcy oświadczeń lub sprawia, że oświadczenia wywołanie serwera programu exchange. | 


#### <a name="precondition"></a>Warunek wstępny

**Wstępnym** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| `Type` | Yes | Typ wyboru lub zapytanie w celu przeprowadzenia tego warunku wstępnego. Wartość może być **ClaimsExist**, która określa, czy działania powinny wykonane, jeśli określone oświadczenia istnieje w bieżącym zestawie oświadczeń użytkownika lub **ClaimEquals**, która określa, że akcje należy wykonać, jeśli istnieje w określonym oświadczenia, a jego wartość jest równa określonej wartości. |
| `ExecuteActionsIf` | Yes | Użyj testu prawda lub FAŁSZ, aby zdecydować, jeśli można wykonać akcje w warunku wstępnym. | 

**Wstępnym** elementy zawierają następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Wartość | 1: n | ClaimTypeReferenceId zostać wykonane zapytanie dla. Inny element wartość zawiera wartość do sprawdzenia.</li></ul>|
| Akcja | 1:1 | Akcja, która powinna być wykonywana, jeśli sprawdzanie warunku wstępnego w ramach kroku aranżacji ma wartość true. Jeśli wartość `Action` ustawiono `SkipThisOrchestrationStep`, skojarzonego `OrchestrationStep` nie powinien być wykonywany. | 

#### <a name="preconditions-examples"></a>Przykłady warunków wstępnych

Następujące warunki wstępne sprawdza, czy istnieje identyfikator obiektu użytkownika. W podróży użytkownika użytkownik wybrał logować się za pomocą konta lokalnego. Jeśli istnieje identyfikator obiektu, Pomiń ten krok aranżacji.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Następujące warunki wstępne sprawdza, czy użytkownik jest zalogowany przy użyciu konta społecznościowego. Aby znaleźć konto użytkownika w katalogu zostanie podjęta próba. Jeśli użytkownik loguje się lub rejestruje się za pomocą konta lokalnego, Pomiń ten krok aranżacji.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Warunki wstępne można sprawdzić wiele warunków wstępnych. Poniższy przykład sprawdza, czy istnieje "objectId" lub "email". Jeśli pierwszy warunek jest spełniony, podróży przejdzie do następnego kroku aranżacji.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>      
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Krok aranżacji typu `ClaimsProviderSelection` lub `CombinedSignInAndSignUp` mogą zawierać listy dostawców oświadczeń, które użytkownik może zarejestrować się za pomocą. Kolejność elementów wewnątrz `ClaimsProviderSelections` elementy określa kolejność dostawców tożsamości, użytkownik widzi.

**Elemencie ClaimsProviderSelection** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 0: n | Zawiera listę dostawców oświadczeń, które można wybrać.|

**Elemencie ClaimsProviderSelection** element zawiera następujące atrybuty: 

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Nie | Identyfikator programu exchange oświadczenia, który jest wykonywany w następnym kroku aranżacji wyboru dostawcy oświadczeń. Ten atrybut lub atrybut ValidationClaimsExchangeId musi być określony, ale nie oba. | 
| ValidationClaimsExchangeId | Nie | Identyfikator programu exchange oświadczenia, który jest wykonywany w bieżącego kroku aranżacji w celu zweryfikowania wyboru dostawcy oświadczeń. Ten atrybut lub atrybut TargetClaimsExchangeId musi być określony, ale nie oba. |

### <a name="claimsproviderselection-example"></a>Przykład elemencie ClaimsProviderSelection

W poniższym kroku aranżacji użytkownika można zalogować się przy użyciu usługi Facebook, LinkedIn, Twitter, Google lub kontem lokalnym. Jeśli użytkownik wybierze jeden z dostawców tożsamości społecznościowych, drugi krok aranżacji wykonuje się przy użyciu programu exchange wybranego oświadczenia określone w `TargetClaimsExchangeId` atrybutu. Drugi etap aranżacji przekierowuje użytkownika do dostawcy tożsamości społecznościowych w celu ukończenia procesu logowania. Jeśli użytkownik zdecyduje się zalogować się przy użyciu konta lokalnego, usługi Azure AD B2C jest realizowany zgodnie z tego samego kroku aranżacji (tej samej stronie tworzenia konta lub stronie logowania), a pomija drugi krok aranżacji.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange" 
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

**ClaimsExchanges** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| ClaimsExchange | 0: n | W zależności od profilu technicznego używany albo przekierowuje klienta zgodnie z elemencie ClaimsProviderSelection, który został wybrany lub sprawia, że oświadczenia wywołanie serwera programu exchange. | 

**Elementu ClaimsExchange** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator kroku exchange oświadczeń. Identyfikator jest używana do odwołania, wymiana oświadczeń spośród dostawcy oświadczeń krok w ramach zasad. | 
| TechnicalProfileReferenceId | Tak | Identyfikator profilu technicznego, który ma zostać wykonana. |
