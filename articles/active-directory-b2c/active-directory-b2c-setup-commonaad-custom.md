---
title: Dodawanie dostawcy tożsamości wieloma dzierżawami usługi Azure AD za pomocą niestandardowych zasad — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dodawanie dostawcy tożsamości wieloma dzierżawami usługi Azure AD za pomocą niestandardowych zasad — Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: cff5c1eed374683ad3e2c1f1a69f6f172f36c536
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Zezwalaj użytkownikom na logowanie się do dostawcy tożsamości wieloma dzierżawami usługi Azure AD za pomocą niestandardowych zasad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono sposób włączenia logowania dla użytkowników za pomocą wspólnego punktu końcowego usługi Azure Active Directory (Azure AD) za pośrednictwem [niestandardowych zasad](active-directory-b2c-overview-custom.md).

>[!NOTE]
> Używamy "contoso.com" dla organizacji dzierżawy usługi Azure AD i "fabrikamb2c.onmicrosoft.com" jako dzierżawy usługi Azure AD B2C w poniższych instrukcjach.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

Kroki te obejmują:
     
1. Tworzenie usługi Azure Active Directory B2C dzierżawy (Azure AD B2C).
2. Tworzenie aplikacji usługi Azure AD B2C.    
3. Rejestrowanie dwie aplikacje aparat zasad.  
4. Konfigurowanie kluczy. 
5. Ustawianie początkowego pakietu.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Krok 1. Tworzenie aplikacji dzierżawy usługi Azure AD

Aby włączyć logowanie użytkowników przy użyciu wielu dzierżawców punktu końcowego usługi Azure AD, należy aplikacja wielodostępne zarejestrowana w żadnym dzierżawcy usługi Azure AD. W tym artykule firma Microsoft opisano, jak utworzyć aplikację wieloma dzierżawami usługi Azure AD w dzierżawie usługi Azure AD B2C. Włącz logowanie użytkowników przy użyciu tego wielodostępnych aplikacji usługi Azure AD.

>[!NOTE]
> Jeśli chcesz użytkowników usługi Azure AD **i użytkownicy z kontami Microsoft** do logowania, Pomiń tę sekcję i zamiast tego należy zarejestrować aplikację w [portalu dla deweloperów firmy Microsoft](https://apps.dev.microsoft.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz konto. Z **katalogu** wybierz dzierżawy usługi Azure AD B2C, aby zarejestrować aplikację usługi Azure AD (fabrikamb2c.onmicrosoft.com).
2. Wybierz **więcej usług** w okienku po lewej stronie, a następnie wyszukaj "Rejestracji aplikacji".
3. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
4. Wprowadź nazwę dla aplikacji (na przykład `Azure AD B2C App`).
5. Jako typ aplikacji wybierz pozycję **Interfejs API/aplikacja sieci Web**.
6. Aby uzyskać **adres URL logowania**, wprowadź następujący adres URL, gdzie `yourtenant` zastępuje nazwę dzierżawy usługi Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Wartość "yourtenant" musi być małych liter w **adres URL logowania**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Zapisz identyfikator aplikacji.
1. Wybierz nowo utworzonej aplikacji.
1. W obszarze **ustawienia** bloku, wybierz opcję **właściwości**.
1. Ustaw **dzierżawcza Multi** do **tak**.
1. W obszarze **ustawienia** bloku, wybierz opcję **klucze**.
1. Utwórz nowy klucz i zapisz go. Zostanie użyty w krokach w następnej sekcji.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Krok 2. Dodawanie klucza usługi Azure AD do usługi Azure AD B2C

Należy zarejestrować klucz aplikacji w ustawieniach usługi Azure AD B2C. W tym celu:

1. Przejdź do menu Ustawienia dla usługi Azure AD B2C
1. Polecenie **Framework obsługi tożsamości** > **klucze zasad**.
1. Wybierz **+ Dodaj**.
1. Wybierz lub wprowadź następujące opcje:
   * Wybierz **ręcznego**.
   * Aby uzyskać **nazwa**, wybierz nazwę, która odpowiada nazwa dzierżawy usługi Azure AD (na przykład `AADAppSecret`).  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
   * Wklej swój klucz aplikacji w **klucz tajny** pole.
   * Wybierz **podpisu**.
5. Wybierz pozycję **Utwórz**.
6. Upewnij się, że utworzono klucz `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Krok 3. Dodawanie dostawcy oświadczeń w zasadach podstawowej

Użytkownikom na logowanie się przy użyciu usługi Azure AD, należy zdefiniować usługi Azure AD jako dostawcy oświadczeń. Innymi słowy należy określić punkt końcowy usługi Azure AD B2C będzie komunikować się z. Punkt końcowy udostępni zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony. 

Można zdefiniować usługi Azure AD jako dostawcy oświadczeń, przez dodanie usługi Azure AD do `<ClaimsProvider>` węzeł w pliku rozszerzenie zasad:

1. Otwórz plik rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego.
1. Znajdź `<ClaimsProviders>` sekcji. Jeśli nie istnieje, dodaj ją w obszarze węzła głównego.
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
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. If you would like only specific tenants to be able to sign in, uncomment the line below and update the GUIDs. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item> -->

        <!-- The commented key below specifies that users from any tenant can sign-in. Comment or remove the line below if using the line above. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
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

1. W obszarze `<ClaimsProvider>` węzła, zaktualizuj wartość `<Domain>` do unikatową wartość, która może służyć do odróżnienia go od innych dostawców tożsamości.
1. W obszarze `<TechnicalProfile>` węzła, zaktualizuj wartość `<DisplayName>`. Ta wartość będzie wyświetlana na przycisk logowania na ekranie logowania.
1. Zaktualizuj wartość `<Description>`.
1. Ustaw `<Item Key="client_id">` do Identyfikatora aplikacji z rejestracji aplikacji mulity dzierżawy usługi Azure AD.

### <a name="step-31-optional-restrict-access-to-specific-list-of-azure-ad-tenants"></a>Krok 3.1 [opcjonalnie] ograniczanie dostępu do określonej listy dzierżaw usługi Azure AD
Można zaktualizować listy prawidłowy token wystawców i ograniczanie dostępu do określonej listy dzierżaw usługi Azure AD, użytkownicy mogą logowania. Aby uzyskać wartości, należy przyjrzeć się metadanych dla każdego z konkretnym dzierżaw usługi Azure AD, które chcesz przypisać użytkowników zalogować z. Format danych wygląda podobnie do następującej: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, gdzie `yourAzureADtenant` jest nazwa dzierżawy usługi Azure AD (contoso.com lub innych dzierżawy usługi Azure AD).
1. Otwórz przeglądarkę i przejdź do adresu URL metadanych.
1. W przeglądarce wyszukaj obiekt "Wystawca" i skopiuj jej wartość. Powinien wyglądać podobnie do następującej: `https://sts.windows.net/{tenantId}/`.
1. Wklej wartość `ValidTokenIssuerPrefixes` klucza. Można dodawać wiele, rozdzielając je za pomocą przecinka. Na przykład jest ujęta w przykładzie powyżej XML.

> [!NOTE]
> Przy użyciu `https://sts.windows.net` wszystkich użytkowników usługi Azure AD zalogować się do aplikacji dopuszcza wartość prefiksu.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Krok 4. Zarejestruj dostawcę oświadczeń konta usługi Azure AD

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1 kopię przebieg użytkownika

Teraz należy dodać dostawcy tożsamości usługi Azure AD do jednego z podróże użytkownika. W tym momencie Konfigurowanie dostawcy tożsamości, ale nie jest dostępna w żadnym ekrany konta-konta/logowania.

Aby było to możliwe, firma Microsoft będzie utworzyć kopię istniejącej przebieg użytkownika szablonu, a następnie zmodyfikować go, aby miała ona również dostawcy tożsamości usługi Azure AD:

1. Otwórz plik bazowy tej zasady (na przykład TrustFrameworkBase.xml).
1. Znajdź `<UserJourneys>` element i skopiuj cały `<UserJourney>` węzła, który zawiera `Id="SignUpOrSignIn"`.
1. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodaj je.
1. Wklej całą `<UserJourney>` węzła, który został skopiowany jako element podrzędny `<UserJourneys>` elementu.
1. Zmień nazwę Identyfikatora nowy przebieg użytkownika (na przykład zmienić nazwę jako `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Wyświetl krok 4.2 "button"

`<ClaimsProviderSelection>` Element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie konta-konta/logowania. Jeśli dodasz `<ClaimsProviderSelection>` elementu dla usługi Azure AD, nowy przycisk zostaną wyświetlone po wyładowuje użytkownika na stronie. Aby dodać ten element:

1. Znajdź `<OrchestrationStep>` węzła, który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
1. Dodaj następujące informacje:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Ustaw `TargetClaimsExchangeId` odpowiednią wartość. Zaleca się następujące tej samej Konwencji, co inne osoby:  *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3 łącza na przycisku akcji

Teraz, gdy masz przycisku w miejscu, należy połączyć je z akcją. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z usługą Azure AD otrzymujących token. Połączyć przycisku akcji przez łączenie techniczne profilu dla dostawcy oświadczeń usługi Azure AD:

1. Znajdź `<OrchestrationStep>` zawierającą `Order="2"` w `<UserJourney>` węzła.
1. Dodaj następujące informacje:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Aktualizacja `Id` taką samą wartość jak `TargetClaimsExchangeId` w poprzedniej sekcji.
1. Aktualizacja `TechnicalProfileReferenceId` dla identyfikatora profilu techniczne utworzonego wcześniej (typowe-AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Krok 5: Tworzenie nowych zasad planu odzyskiwania

Należy teraz zaktualizować jednostki uzależnionej pliku strony (RP), który inicjuje przebieg użytkownika, który został właśnie utworzony:
 
1. Utwórz kopię SignUpOrSignIn.xml w katalogu roboczym i zmień jego nazwę (na przykład, zmień jego nazwę na SignUpOrSignInWithAAD.xml).  
1. Otwórz nowy plik i aktualizacji `PolicyId` atrybutu dla `<TrustFrameworkPolicy>` unikatowe wartości (na przykład SignUpOrSignInWithAAD). Są to nazwa zasady (na przykład B2C\_1A\_SignUpOrSignInWithAAD). 
1. Modyfikowanie `ReferenceId` atrybutu w `<DefaultUserJourney>` odpowiadające identyfikator nowego użytkownika podróży utworzony (SignUpOrSignUsingAzureAD). 
1. Zapisz zmiany i przekazać plik. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Przekaż zasady dla Twojej dzierżawy

1. W [portalu Azure](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz **usługi Azure AD B2C**.
2. Wybierz **Framework obsługi tożsamości**.
3. Wybierz **wszystkich zasad**.
4. Wybierz **przekazywać zasady**.
5. Wybierz **zastąpić zasady, jeśli istnieje** pole wyboru.
6. Przekaż `TrustFrameworkExtensions.xml` plików i plików RP (np. `SignUpOrSignInWithAAD.xml`) i upewnij się, ich przeszedł pomyślnie weryfikacji.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Testowanie zasad niestandardowych za pomocą Uruchom teraz

1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie wybierz **Framework obsługi tożsamości**.
    > [!NOTE]
    > Uruchom teraz wymaga co najmniej jednej aplikacji można preregistered dla dzierżawcy. Aby dowiedzieć się, jak zarejestrować aplikacji, zapoznaj się z usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [Rejestracja aplikacji](active-directory-b2c-app-registration.md) artykułu.

1. Otwórz jednostki uzależnionej zasady niestandardowe strony (RP), który został przekazany (*B2C\_1A\_SignUpOrSignInWithAAD*), a następnie wybierz **Uruchom teraz**.
1. Teraz można się zalogować przy użyciu konta usługi Azure AD.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Opcjonalnie) Krok 8: Rejestrowanie dostawcy oświadczeń konta usługi Azure AD do podróży edycji profilu użytkownika

Można także dodać dostawcy tożsamości konta usługi Azure AD do Twojej `ProfileEdit` podróży użytkownika. Aby udostępnić przebieg użytkownika, powtórz kroki od 4 do 6. Teraz, wybierz opcję `<UserJourney>` węzła, który zawiera `Id="ProfileEdit"`. Zapisz, przekazywanie i przetestowania zasad.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Do diagnozowania problemów, przeczytaj o [Rozwiązywanie problemów z](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Kolejne kroki

Przekazywania informacji pozwalających na [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
