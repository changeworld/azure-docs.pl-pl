---
title: Zachowaj zalogowanie się w usłudze Azure Active Directory B2C
description: Dowiedz się, jak skonfigurować usługę Keep Me Signed In (KMSI) w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 041fb8d881307b52fb170a11618f930debc522a4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803164"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Włącz opcję Zachowaj zalogowanie się (KMSI) w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Funkcję keep me signed in (KMSI) można włączyć dla użytkowników aplikacji sieci web i aplikacji natywnych, którzy mają konta lokalne w katalogu usługi Azure Active Directory B2C (Azure AD B2C). Ta funkcja udziela dostępu użytkownikom powracającym do aplikacji bez monitowania ich o ponowne wniesienie nazwy użytkownika i hasła. Ten dostęp jest odwoływany, gdy użytkownik się wyloguje.

Użytkownicy nie powinni włączać tej opcji na komputerach publicznych.

![Przykładowa strona logowania do rejestracji z polem wyboru Keep me signed in](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawa usługi Azure AD B2C skonfigurowana tak, aby zezwalała na logowanie do konta lokalnego. Usługa KMSI nie jest chy <2> pod względem liczby kont zewnętrznych dostawców tożsamości.
- Wykonaj kroki opisane w [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Konfigurowanie identyfikatora strony

Aby włączyć funkcję KMSI, `DataUri` ustaw element definicji zawartości na [identyfikator](contentdefinitions.md#datauri) `unifiedssp` strony i stronę w wersji *1.1.0* lub [nowszej.](page-layout.md)

1. Otwórz plik rozszerzenia zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>. Ten plik rozszerzenia jest jednym z plików zasad zawartych w pakiecie startowym zasad niestandardowych, które powinny być uzyskane w wymagania wstępne, [Wprowadzenie do zasad niestandardowych](custom-policy-get-started.md).
1. Wyszukaj **element BuildingBlocks.** Jeśli element nie istnieje, dodaj go.
1. Dodaj **ContentDefinitions** element do **BuildingBlocks** element zasad.

    Zasady niestandardowe powinny wyglądać następująco:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Dodawanie metadanych do samodzielnie potwierdzonego profilu technicznego

Aby dodać pole wyboru KMSI do strony rejestracji i logowania, ustaw metadane na `setting.enableRememberMe` true. Zastąp profile techniczne SelfAsserted-LocalAcCountSignin-Email w pliku rozszerzenia.

1. Znajdź ClaimsProviders element. Jeśli element nie istnieje, dodaj go.
1. Dodaj następującego dostawcę oświadczeń do elementu ClaimsProviders:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Zapisz plik rozszerzeń.

## <a name="configure-a-relying-party-file"></a>Konfigurowanie pliku jednostki uzależniającej

Zaktualizuj plik jednostki uzależniającej (RP), który inicjuje proces użytkownika, który został utworzony.

1. Otwórz niestandardowy plik zasad. Na przykład *signuporsignin.xml*.
1. Jeśli jeszcze nie istnieje, dodaj `<UserJourneyBehaviors>` węzeł podrzędny do węzła. `<RelyingParty>` Musi znajdować się `<DefaultUserJourney ReferenceId="User journey Id" />`bezpośrednio po `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`, na przykład: .
1. Dodaj następujący węzeł jako element `<UserJourneyBehaviors>` podrzędny elementu.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Wskazuje, jak sesja jest rozszerzana `SessionExpiryInSeconds` `KeepAliveInDays`o czas określony w i . Wartość `Rolling` (domyślna) wskazuje, że sesja jest rozszerzana za każdym razem, gdy użytkownik wykonuje uwierzytelnianie. Wartość `Absolute` wskazuje, że użytkownik jest zmuszony do ponownego uwierzytelnienia po określonym czasie.

    - **SessionExpiryInSeconds** - Okres istnienia plików cookie sesji podczas *utrzymywanie mnie zalogować* nie jest włączona, lub jeśli użytkownik nie wybierz *zachowaj mnie zalogowany*. Sesja wygasa po `SessionExpiryInSeconds` minięciu lub przeglądarka jest zamknięta.

    - **KeepAliveInDays** - Okres istnienia plików cookie sesji, gdy *mnie zalogować* jest włączona, a użytkownik wybiera *zachować mnie zalogować*.  Wartość `KeepAliveInDays` ma pierwszeństwo przed `SessionExpiryInSeconds` wartością i dyktuje czas wygaśnięcia sesji. Jeśli użytkownik zamknie przeglądarkę i ponownie ją ponownie otworzy później, nadal może po cichu zalogować się tak długo, jak jest w okresie keepaliveInDays.

    Aby uzyskać więcej informacji, zobacz [zachowania podróży użytkownika](relyingparty.md#userjourneybehaviors).

Zaleca się ustawienie wartości SessionExpiryInSeconds jako krótkiego okresu (1200 sekund), podczas gdy wartość KeepAliveInDays można ustawić na stosunkowo długi okres (30 dni), jak pokazano w poniższym przykładzie:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>Testowanie zasad

1. Zapisz zmiany, a następnie przekaż plik.
1. Aby przetestować przekazane zasady niestandardowe, przejdź do strony zasad w portalu Azure, a następnie wybierz pozycję **Uruchom teraz**.
1. Wpisz **swoją nazwę użytkownika** i **hasło**, wybierz **pozycję Niech mnie zalogowano,** a następnie kliknij przycisk **Zaloguj**się .
1. Wróć do witryny Azure Portal. Przejdź do strony zasad, a następnie wybierz pozycję **Kopiuj,** aby skopiować adres URL logowania.
1. Na pasku adresu przeglądarki `&prompt=login` usuń parametr ciągu zapytania, który zmusza użytkownika do wprowadzenia poświadczeń w tym żądaniu.
1. W przeglądarce kliknij przycisk **Przejdź**. Teraz usługa Azure AD B2C wystawi token dostępu bez monitowania o ponowne zalogowanie się. 

## <a name="next-steps"></a>Następne kroki

Znajdź przykładowe zasady [tutaj](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
