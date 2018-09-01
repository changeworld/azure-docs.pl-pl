---
title: Dodawanie dostawcy tożsamości wielu dzierżaw usługi Azure AD za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dodawanie dostawcy tożsamości wielu dzierżaw usługi Azure AD za pomocą zasad niestandardowych — Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 68eab85c7f67ad3af18c6066c29e1250e1be3d23
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344410"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Usługa Azure Active Directory B2C: Zezwalaj użytkownikom na logowanie się do dostawcy tożsamości wielu dzierżaw usługi Azure AD za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule dowiesz się, jak włączyć logowanie użytkowników przy użyciu punktu końcowego z wieloma dzierżawami usługi Azure Active Directory (Azure AD) za pośrednictwem [zasady niestandardowe](active-directory-b2c-overview-custom.md). Dzięki temu użytkownicy z wieloma dzierżawcami usługi Azure AD, aby zalogować się do usługi Azure AD B2C, bez konieczności konfigurowania techniczne dostawcy dla każdej dzierżawy. Jednak gościa elementów członkowskich w dowolnym z tych dzierżaw **nie będzie** można logować się. W tym trzeba będzie [skonfigurować oddzielnie każdą dzierżawę](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> Używamy "contoso.com" w celu organizacyjne dzierżawy usługi Azure AD i "fabrikamb2c.onmicrosoft.com" jako dzierżawy usługi Azure AD B2C w poniższych instrukcjach.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

Kroki te obejmują:
     
1. Tworzenie usługi Azure Active Directory B2C (Azure AD B2C) dzierżawy.
1. Tworzenie aplikacji usługi Azure AD B2C.    
1. Rejestrowanie dwie aplikacje aparatu zasad.  
1. Konfigurowanie kluczy. 
1. Konfigurowanie pakietu startowego.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Krok 1. Tworzenie aplikacji z wieloma dzierżawami usługi Azure AD

Aby włączyć logowania dla użytkowników korzystających z wieloma dzierżawami punktu końcowego usługi Azure AD, musisz mieć aplikacji wielodostępnej zarejestrowany w jednym z Twojej dzierżawy usługi Azure AD. W tym artykule pokażemy sposób tworzenia aplikacji z wieloma dzierżawami usługi Azure AD w swojej dzierżawie usługi Azure AD B2C. Następnie należy włączyć logowania dla użytkowników za pomocą tego wielodostępnych aplikacji usługi Azure AD.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz swoje konto. Z **katalogu** listy, wybierz dzierżawę usługi Azure AD B2C, aby zarejestrować aplikację usługi Azure AD (fabrikamb2c.onmicrosoft.com).
1. Wybierz **więcej usług** w okienku po lewej stronie i wyszukaj "Rejestracje aplikacji".
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
1. Wprowadź nazwę aplikacji (na przykład `Azure AD B2C App`).
1. Jako typ aplikacji wybierz pozycję **Interfejs API/aplikacja internetowa**.
1. Aby uzyskać **adres URL logowania**, wprowadź następujący adres URL, gdzie `yourtenant` jest zastępowana nazwą dzierżawy usługi Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Wartość "yourtenant" musi być tylko małe litery w **adres URL logowania**.

    ```
    https://yourtenant.b2clogin.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Zapisz identyfikator aplikacji.
1. Wybierz nowo utworzoną aplikację.
1. W obszarze **ustawienia** bloku wybierz **właściwości**.
1. Ustaw **wielodostępnych** do **tak**.
1. W obszarze **ustawienia** bloku wybierz **klucze**.
1. Utwórz nowy klucz i zapisz go. Użyjesz go w krokach w następnej sekcji.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Krok 2. Dodaj klucz usługi Azure AD do usługi Azure AD B2C

Należy zarejestrować klucz aplikacji w ustawieniach usługi Azure AD B2C. W tym celu:

1. Przejdź do menu Ustawienia dla usługi Azure AD B2C
1. Kliknij pozycję **struktura środowiska tożsamości** > **klucze zasad**.
1. Wybierz **+ Dodaj**.
1. Wybierz lub wprowadź następujące opcje:
   * Wybierz **ręczne**.
   * Aby uzyskać **nazwa**, wybierz nazwę, która jest zgodna nazwa dzierżawy usługi Azure AD (na przykład `AADAppSecret`).  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
   * Wklej swój klucz aplikacji w **klucz tajny** pole.
   * Wybierz **podpisu**.
1. Wybierz pozycję **Utwórz**.
1. Upewnij się, że utworzono klucz `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Krok 3. Dodawanie dostawcy oświadczeń w zasadach podstawowych

Użytkownikom na logowanie za pomocą usługi Azure AD, należy zdefiniować usługi Azure AD jako dostawcy oświadczeń. Innymi słowy należy określić punkt końcowy usługi Azure AD B2C będą komunikować się z. Punkt końcowy udostępni zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony. 

Można zdefiniować usługi Azure AD jako dostawcy oświadczeń, przez dodanie usługi Azure AD do `<ClaimsProvider>` węzeł w pliku rozszerzenie zasad:

1. Otwórz plik rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego.
1. Znajdź `<ClaimsProviders>` sekcji. Jeśli nie istnieje, należy go dodać w węźle głównym.
1. Dodaj nową `<ClaimsProvider>` węzła w następujący sposób:

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. W obszarze `<ClaimsProvider>` węzła, zaktualizuj wartość `<Domain>` na unikatową wartość, która może służyć do odróżnienia go od innych dostawców tożsamości.
1. W obszarze `<TechnicalProfile>` węzła, zaktualizuj wartość `<DisplayName>`. Ta wartość będzie wyświetlana na przycisku logowania na ekranie logowania.
1. Zaktualizuj wartość `<Description>`.
1. Ustaw `<Item Key="client_id">` do Identyfikatora aplikacji z rejestracji aplikacji mulity dzierżawy usługi Azure AD.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Krok 3.1 ograniczanie dostępu do określonej listy dzierżaw usługi Azure AD

> [!NOTE]
> Za pomocą `https://sts.windows.net` jako wartość pozycji **ValidTokenIssuerPrefixes** umożliwi wszystkich użytkowników usługi Azure AD zalogować się do aplikacji.

Należy zaktualizować listę prawidłowi wystawcy tokenów i ograniczanie dostępu do określonej listy dzierżaw usługi Azure AD można logowania użytkownika. Aby uzyskać wartości, należy przyjrzeć metadanych dla każdego określonego dzierżaw usługi Azure AD, które chcesz, aby użytkownicy mogli Zaloguj się za pomocą. Format danych wygląda podobnie do następującej: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, gdzie `yourAzureADtenant` jest nazwa dzierżawy usługi Azure AD (contoso.com lub inne dzierżawy usługi Azure AD).
1. Otwórz przeglądarkę i przejdź do adresu URL metadanych.
1. W przeglądarce wyszukaj obiekt "issuer" i skopiuj jego wartość. Powinno to wyglądać podobnie do następującego: `https://sts.windows.net/{tenantId}/`.
1. Wklej wartość `ValidTokenIssuerPrefixes` klucza. Można dodawać wiele, oddzielając je przecinkami. Na przykład jest ujęty w przykładzie powyżej XML.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Krok 4. Zarejestruj dostawcę oświadczeń konta usługi Azure AD

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1 kopię podróży użytkownika

Teraz należy dodać dostawcę tożsamości usługi Azure AD do jednego z Twojej podróży użytkownika. W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest dostępny w wszystkich ekranów konta-dokonywania/logowania.

Aby udostępnić ją, firma Microsoft będzie utworzenie duplikatu istniejącego podróży użytkownika szablonu, a następnie zmodyfikuj tak, aby w nim również dostawcy tożsamości usługi Azure AD:

1. Otwórz plik podstawowy zasady (na przykład TrustFrameworkBase.xml).
1. Znajdź `<UserJourneys>` elementu i skopiować całą `<UserJourney>` węzeł, który zawiera `Id="SignUpOrSignIn"`.
1. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodać.
1. Wklej całą `<UserJourney>` węzeł, który został skopiowany jako element podrzędny elementu `<UserJourneys>` elementu.
1. Zmień nazwę identyfikator nowego podróży użytkownika (na przykład zmień nazwę `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Wyświetlanie kroku 4.2 "button"

`<ClaimsProviderSelection>` Element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie konta-dokonywania/logowania. Jeśli dodasz `<ClaimsProviderSelection>` elementu dla usługi Azure AD, nowy przycisk pojawia się po użytkownik wyładowuje na stronie. Aby dodać ten element:

1. Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
1. Dodaj następujący fragment kodu:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Ustaw `TargetClaimsExchangeId` odpowiednią wartość. Jednak zalecamy zastosowanie tej samej Konwencji co inne osoby:  *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3 łącze przycisku akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z usługą Azure AD, aby otrzymać token. Łącze przycisku do akcji, łącząc profilu technicznego dla dostawcy oświadczeń usługi Azure AD:

1. Znajdź `<OrchestrationStep>` zawierającej `Order="2"` w `<UserJourney>` węzła.
1. Dodaj następujący fragment kodu:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Aktualizacja `Id` taką samą wartość jak w przypadku `TargetClaimsExchangeId` w poprzedniej sekcji.
1. Aktualizacja `TechnicalProfileReferenceId` identyfikator profilu technicznego utworzonego wcześniej (typowe-AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Krok 5: Tworzenie nowych zasad RP

Teraz musisz zaktualizować plik innych firm (RP) jednostki uzależnionej, który będzie inicjował podróży użytkownika, który został utworzony:
 
1. Utwórz kopię SignUpOrSignIn.xml w katalogu roboczym i zmień jego nazwę (na przykład, zmień jego nazwę na SignUpOrSignInWithAAD.xml).  
1. Otwórz nowy plik i zaktualizuj `PolicyId` atrybutu dla `<TrustFrameworkPolicy>` przy użyciu unikatowej wartości (na przykład SignUpOrSignInWithAAD). Są to nazwę swoich zasad (na przykład B2C\_1A\_SignUpOrSignInWithAAD). 
1. Modyfikowanie `ReferenceId` atrybutu w `<DefaultUserJourney>` aby dopasować identyfikator nowego podróży użytkownika utworzony (SignUpOrSignUsingAzureAD). 
1. Zapisz zmiany i przekazać plik. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Przekaż zasady dla Twojej dzierżawy

1. W [witryny Azure portal](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz pozycję **usługi Azure AD B2C**.
1. Wybierz **struktura środowiska tożsamości**.
1. Wybierz **wszystkie zasady**.
1. Wybierz **przekazywać zasady**.
1. Wybierz **Zastąp zasady Jeśli istnieje** pole wyboru.
1. Przekaż `TrustFrameworkExtensions.xml` plik oraz plik RP (np. `SignUpOrSignInWithAAD.xml`) i upewnij się, przekazują sprawdzania poprawności.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Testowania zasad niestandardowych za pomocą polecenia Uruchom teraz

1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie wybierz pozycję **struktura środowiska tożsamości**.
    > [!NOTE]
    > Uruchom teraz wymaga co najmniej jedną aplikację, aby być jest wstępnie zarejestrowane w ramach dzierżawy. Aby dowiedzieć się, jak zarejestrować aplikacji, zobacz temat usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [rejestracji aplikacji](active-directory-b2c-app-registration.md) artykułu.

1. Otwórz jednostki uzależnionej strona (RP) zasad niestandardowych, który został przekazany (*B2C\_1A\_SignUpOrSignInWithAAD*), a następnie wybierz pozycję **Uruchom teraz**.
1. Teraz można się zalogować przy użyciu konta usługi Azure AD.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Opcjonalnie) Krok 8: Rejestrowanie dostawcy oświadczeń konta usługi Azure AD do edytowania profilu podróży użytkownika

Można także dodać dostawcę tożsamości konta usługi Azure AD do swojej `ProfileEdit` podróży użytkownika. Aby udostępnić podróży użytkownika, powtórz kroki od 4 do 6. Tym razem wybierz pozycję `<UserJourney>` węzeł, który zawiera `Id="ProfileEdit"`. Zapisz, przekazywanie i testowanie zasad.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Diagnozowanie problemów, przeczytaj temat [Rozwiązywanie problemów z](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Kolejne kroki

Prześlij opinię do [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
