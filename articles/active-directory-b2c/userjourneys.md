---
title: UserJourneys | Dokumenty firmy Microsoft
description: Określ userJourneys element zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d73a1a3ce23817d9d6f742a4a8c730afb58ee0c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226993"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Procesy podróży użytkownika określają jawne ścieżki, za pomocą których zasady umożliwiają aplikacji jednostki uzależniającej uzyskanie żądanych oświadczeń dla użytkownika. Użytkownik jest przyjmowany przez te ścieżki, aby pobrać oświadczenia, które mają być przedstawione do jednostki uzależniającej. Innymi słowy, procesy pozyskiwania użytkowników definiują logikę biznesową tego, przez co przechodzi użytkownik końcowy, gdy platforma Azure AD B2C Identity Experience Framework przetwarza żądanie.

Te podróże użytkowników można uznać za szablony dostępne w celu zaspokojenia podstawowych potrzeb różnych stron uzależnień społeczności będących przedmiotem zainteresowania. Podróże użytkownika ułatwiają definiowanie części zasady jednostki uzależniającej. Zasady można zdefiniować wiele podróży użytkownika. Każda podróż użytkownika jest sekwencją kroków aranżacji.

Aby zdefiniować podróże użytkownika obsługiwane przez zasady, **UserJourneys** element jest dodawany w ramach elementu najwyższego poziomu pliku zasad.

**UserJourneys** Element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| UserJourney (UserJourney) | 1:n | Podróż użytkownika, która definiuje wszystkie konstrukcje niezbędne do pełnego przepływu użytkownika. |

**UserJourney** Element zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator podróży użytkownika, który może służyć do odwoływania się do niego z innych elementów w zasadach. **DefaultUserJourney** element [zasady jednostki uzależniającej](relyingparty.md) wskazuje na ten atrybut. |

**UserJourney** element zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| AranżacjaSteps | 1:n | Sekwencja aranżacji, które muszą być następnie za pośrednictwem pomyślnej transakcji. Każda podróż użytkownika składa się z uporządkowanej listy kroków aranżacji, które są wykonywane w sekwencji. Jeśli dowolny krok zakończy się niepowodzeniem, transakcja zakończy się niepowodzeniem. |

## <a name="orchestrationsteps"></a>AranżacjaSteps

Proces użytkownika jest reprezentowany jako sekwencja aranżacji, która musi być przestrzegana dla pomyślnej transakcji. Jeśli dowolny krok zakończy się niepowodzeniem, transakcja zakończy się niepowodzeniem. Te kroki aranżacji odwołają się zarówno do bloków konstrukcyjnych, jak i dostawców oświadczeń dozwolonych w pliku zasad. Każdy krok aranżacji, który jest odpowiedzialny za pokazanie lub renderowanie środowiska użytkownika ma również odwołanie do odpowiedniego identyfikatora definicji zawartości.

Kroki aranżacji mogą być warunkowo wykonywane na podstawie warunków wstępnych zdefiniowanych w elemencie kroku aranżacji. Na przykład można sprawdzić, aby wykonać krok aranżacji tylko wtedy, gdy istnieją określone oświadczenia lub jeśli oświadczenie jest równe lub nie do określonej wartości.

Aby określić uporządkowaną listę kroków aranżacji, element **OrchestrationSteps** jest dodawany jako część zasad. Ten element jest wymagany.

**AranżacjaSteps** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| AranżacjaStep | 1:n | Uporządkowany krok aranżacji. |

Element **OrchestrationStep** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| `Order` | Tak | Kolejność kroków aranżacji. |
| `Type` | Tak | Typ kroku aranżacji. Możliwe wartości: <ul><li>**ClaimsProviderSelection** — wskazuje, że krok aranżacji przedstawia różnych dostawców oświadczeń do użytkownika, aby wybrać jeden.</li><li>**CombinedSignInAndSignUp** — wskazuje, że krok aranżacji przedstawia połączone logowania dostawcy społecznościowego i lokalnego konta strony rejestracji.</li><li>**ClaimsExchange** — wskazuje, że krok aranżacji wymiany oświadczeń z dostawcą oświadczeń.</li><li>**GetClaims** — wskazuje, że krok aranżacji odczytuje oświadczenia wejściowe.</li><li>**SendClaims** — wskazuje, że krok aranżacji wysyła oświadczenia do jednostki uzależniającej z tokenu wystawionego przez wystawcę oświadczeń.</li></ul> |
| ContentDefinitionReferenceId (ida) | Nie | Identyfikator [definicji zawartości](contentdefinitions.md) skojarzonej z tym krokiem aranżacji. Zazwyczaj identyfikator odwołania do definicji zawartości jest zdefiniowany w samodzielnie potwierdzonym profilu technicznym. Ale istnieją przypadki, gdy usługa Azure AD B2C musi wyświetlić coś bez profilu technicznego. Istnieją dwa przykłady — jeśli typ kroku aranżacji `ClaimsProviderSelection` `CombinedSignInAndSignUp`jest jednym z następujących: lub , Usługa Azure AD B2C musi wyświetlić wybór dostawcy tożsamości bez profilu technicznego. |
| CpimIssuerTechnicalProfileReferenceId | Nie | Typem kroku aranżacji jest `SendClaims`. Ta właściwość definiuje identyfikator profilu technicznego dostawcy oświadczeń, który wystawia token dla jednostki uzależniającej.  W przypadku nieobecności nie jest tworzony token jednostki uzależniającej. |


Element **OrchestrationStep** może zawierać następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Warunki wstępne | 0:n | Lista warunków wstępnych, które muszą być spełnione dla kroku aranżacji do wykonania. |
| ClaimsProviderWybory | 0:n | Lista wyborów dostawcy oświadczeń dla kroku aranżacji. |
| Zmiany w reklamach roszczeń | 0:n | Lista wymian oświadczeń dla kroku aranżacji. |

### <a name="preconditions"></a>Warunki wstępne

Element **Warunki wstępne** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Warunek wstępny | 1:n | W zależności od używanego profilu technicznego przekierowuje klienta zgodnie z wyborem dostawcy oświadczeń lub wywołuje serwer w celu wymiany oświadczeń. |


#### <a name="precondition"></a>Warunek wstępny

Element **Warunek wstępny** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| `Type` | Tak | Typ sprawdzania lub kwerendy do wykonania dla tego warunku wstępnego. Wartością może być **ClaimsExist**, która określa, że akcje powinny być wykonywane, jeśli określone oświadczenia istnieją w bieżącym zestawie oświadczeń użytkownika lub **ClaimEquals**, który określa, że akcje powinny być wykonywane, jeśli określone oświadczenie istnieje, a jego wartość jest równa określonej wartości. |
| `ExecuteActionsIf` | Tak | Użyj testu true lub false, aby zdecydować, czy należy wykonać akcje w warunku wstępnym. |

**Elementy Warunek wstępny** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Wartość | 1:n | A ClaimTypeReferenceId do kwerendy. Inny element wartości zawiera wartość, która ma być sprawdzana.</li></ul>|
| Akcja | 1:1 | Akcja, która powinna być wykonana, jeśli sprawdzanie warunku wstępnego w kroku aranżacji jest true. Jeśli wartość jest `Action` ustawiona `SkipThisOrchestrationStep`na , `OrchestrationStep` skojarzone nie powinny być wykonywane. |

#### <a name="preconditions-examples"></a>Przykłady warunków wstępnych

Następujące warunki wstępne sprawdza, czy identyfikator obiektu użytkownika istnieje. W podróży użytkownika użytkownik wybrał, aby zalogować się przy użyciu konta lokalnego. Jeśli objectId istnieje, pomiń ten krok aranżacji.

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

Następujące warunki wstępne sprawdza, czy użytkownik zalogował się za pomocą konta społecznościowego. Podejmowana jest próba znalezienia konta użytkownika w katalogu. Jeśli użytkownik zaloguje się lub zarejestruje się przy pomocy konta lokalnego, pomiń ten krok aranżacji.

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

Warunki wstępne można sprawdzić wiele warunków wstępnych. Poniższy przykład sprawdza, czy istnieje "objectId" lub "email". Jeśli pierwszy warunek jest spełniony, podróż przechodzi do następnego kroku aranżacji.

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

## <a name="claimsproviderselection"></a>ClaimsProviderSelekcja

Krok aranżacji `ClaimsProviderSelection` `CombinedSignInAndSignUp` typu lub może zawierać listę dostawców oświadczeń, za pomocą których użytkownik może się zalogować. Kolejność elementów wewnątrz `ClaimsProviderSelections` elementów kontroluje kolejność dostawców tożsamości przedstawione użytkownikowi.

Element **ClaimsProviderSelections** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ClaimsProviderSelekcja | 1:n | Zawiera listę dostawców oświadczeń, które można wybrać.|

Element **ClaimsProviderSelections** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Ochopcja wyświetlania| Nie | Steruje zachowaniem sprawy, w której dostępny jest wybór pojedynczego dostawcy oświadczeń. Możliwe wartości: `DoNotShowSingleProvider` (domyślnie) użytkownik jest natychmiast przekierowywane do dostawcy tożsamości federacyjnej. Lub `ShowSingleProvider` usługi Azure AD B2C przedstawia stronę logowania z wyboru dostawcy pojedynczej tożsamości. Aby użyć tego atrybutu, wersja `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` [definicji zawartości](page-layout.md) musi być i powyżej.|

Element **ClaimsProviderSelection** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId (Obiekt docelowy) | Nie | Identyfikator wymiany oświadczeń, który jest wykonywany w następnym kroku aranżacji wyboru dostawcy oświadczeń. Ten atrybut lub ValidationClaimsExchangeId atrybut musi być określony, ale nie oba. |
| ValidationClaimsExchangeId | Nie | Identyfikator wymiany oświadczeń, który jest wykonywany w bieżącym kroku aranżacji w celu sprawdzenia poprawności wyboru dostawcy oświadczeń. Ten atrybut lub Atrybut TargetClaimsExchangeId musi być określony, ale nie oba. |

### <a name="claimsproviderselection-example"></a>Przykład claimsproviderSelection

W poniższym kroku aranżacji użytkownik może zalogować się za pomocą konta Facebook, LinkedIn, Twitter, Google lub konta lokalnego. Jeśli użytkownik wybierze jednego z dostawców tożsamości społecznościowych, drugi krok aranżacji `TargetClaimsExchangeId` jest wykonywany z wybraną wymianą oświadczeń określoną w atrybucie. Drugi krok aranżacji przekierowuje użytkownika do dostawcy tożsamości społecznościowej, aby zakończyć proces logowania. Jeśli użytkownik zdecyduje się zalogować za pomocą konta lokalnego, usługa Azure AD B2C pozostaje w tym samym kroku aranżacji (tej samej strony rejestracji lub strony logowania) i pomija drugi krok aranżacji.

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

## <a name="claimsexchanges"></a>Zmiany w reklamach roszczeń

Element **ClaimsExchanges** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Reklama roszczeń | 1:n | W zależności od używanego profilu technicznego przekierowuje klienta zgodnie z wybranym wyborem ClaimsProviderSelection lub wywołuje serwer w celu wymiany oświadczeń. |

Element **ClaimsExchange** zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator kroku wymiany oświadczeń. Identyfikator jest używany do odwoływania się do wymiany oświadczeń z kroku wyboru dostawcy oświadczeń w zasadach. |
| Techniczny profilproferyd | Tak | Identyfikator profilu technicznego, który ma zostać wykonany. |
