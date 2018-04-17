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
ms.openlocfilehash: e751272222c493f3061eb15b8106f1c13eec960d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-into-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Usługa Azure Active Directory B2C: Umożliwiają użytkownikom logowanie się do dzierżawy usługi Azure AD dostawcy tożsamości, za pomocą zasad niestandardowych

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

## <a name="create-a-multi-tenant-azure-ad-app"></a>Tworzenie aplikacji dzierżawy usługi Azure AD

Aby włączyć logowanie użytkowników przy użyciu wielu dzierżawców punktu końcowego usługi Azure AD, musisz mieć aplikację wielodostępne zarejestrowana w żadnym z dzierżawców

W tym artykule firma Microsoft opisano, jak utworzyć aplikację wieloma dzierżawami usługi Azure AD w dzierżawie usługi Azure AD B2C.

>[!NOTE]
> Jeśli chcesz użytkowników usługi Azure AD **i użytkownicy z kontami Microsoft** do logowania, Pomiń tę sekcję i zamiast tego należy zarejestrować aplikację w [portalu dla deweloperów firmy Microsoft](https://apps.dev.microsoft.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz konto. Z **katalogu** wybierz dzierżawy usługi Azure AD B2C, które chcesz zarejestrować aplikację (fabrikamb2c.onmicrosoft.com).
1. Wybierz **więcej usług** w okienku po lewej stronie, a następnie wyszukaj "Rejestracji aplikacji".
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
1. Wprowadź nazwę dla aplikacji (na przykład `Azure AD B2C App`).
1. Jako typ aplikacji wybierz pozycję **Interfejs API/aplikacja sieci Web**.
1. Aby uzyskać **adres URL logowania**, wprowadź następujący adres URL, gdzie `yourtenant` zastępuje nazwę dzierżawy usługi Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

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

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Dodawanie klucza usługi Azure AD do usługi Azure AD B2C

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
6. Upewnij się, że utworzono klucz `B2C_1A_ContosoAppSecret`.

## <a name="add-a-claims-provider-in-your-base-policy"></a>Dodawanie dostawcy oświadczeń w zasadach podstawowej

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
            <!-- Update the client ID below to Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below -->
            <Key Id="client_secret" StorageReferenceId="Reference to Client Secret" />
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
1. W obszarze `<ClaimsProvider>` węzła, zaktualizuj wartość `<DisplayName>` przyjazną nazwę dla dostawcy oświadczeń. Ta wartość nie jest obecnie używana.

### <a name="update-the-technical-profile"></a>Aktualizuj profil techniczne

Aby uzyskać token z punktem końcowym usługi Azure AD, musisz zdefiniować protokołów, które usługi Azure AD B2C należy używać do komunikacji z usługą Azure AD. Jest to realizowane wewnątrz `<TechnicalProfile>` elementu `<ClaimsProvider>`.
1. Aktualizacja Identyfikatora `<TechnicalProfile>` węzła. Ten identyfikator służy do odwoływania się do tego profilu techniczne z innymi częściami zasad.
1. Zaktualizuj wartość `<DisplayName>`. Ta wartość będzie wyświetlana na przycisk logowania na ekranie logowania.
1. Zaktualizuj wartość `<Description>`.
1. Usługi Azure AD używa protokołu OpenID Connect, dlatego upewnij się, że wartość `<Protocol>` jest `"OpenIdConnect"`.
1. Ustaw `<Item Key="client_id">` do Identyfikatora aplikacji z rejestracji aplikacji.
1. Upewnij się, że `<Item Key="UsePolicyInRedirectUri">` ma ustawioną wartość `false`.

Należy zaktualizować listę prawidłowy token wystawców. Aby uzyskać wartości, należy przyjrzeć się metadanych dla każdego z konkretnym dzierżaw usługi Azure AD, które chcesz przypisać użytkowników zalogować z. Format danych wygląda podobnie do następującej: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, gdzie `yourAzureADtenant` jest nazwa dzierżawy usługi Azure AD (contoso.com).
1. Otwórz przeglądarkę i przejdź do adresu URL metadanych.
1. W przeglądarce wyszukaj obiekt "Wystawca" i skopiuj jej wartość. Powinien wyglądać podobnie do następującej: `https://sts.windows.net/{tenantId}/`.
1. Wklej wartość `ValidTokenIssuerPrefixes` klucza. Można dodawać wiele przez rozdzielając je za pomocą przecinka. 

> [!NOTE]
> Przy użyciu `https://sts.windows.net` wszystkich użytkowników usługi Azure AD zalogować się do aplikacji dopuszcza wartość prefiksu.

Musisz także połączyć klucz tajny aplikacji usługi Azure AD, który został zarejestrowany w dzierżawie usługi Azure AD B2C do usługi Azure AD `<ClaimsProvider>` informacji:

* W `<CryptographicKeys>` sekcji w poprzednim pliku XML, zaktualizuj wartość `StorageReferenceId` Identyfikator odwołania klucz tajny, który zdefiniowano (na przykład `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia do weryfikacji

Przez teraz zgodnie z zasadami został skonfigurowany tak, aby usługi Azure AD B2C potrafi do komunikowania się z punktem końcowym typowe dla usługi Azure AD. Spróbuj przekazać plik rozszerzenia zasad tylko w celu potwierdzenia, że wszystkie problemy nie ma do tej pory. W tym celu:

1. Otwórz **wszystkie zasady** bloku w dzierżawie usługi Azure AD B2C.
1. Sprawdź **zastąpić zasady, jeśli istnieje** pole.
1. Przekaż plik rozszerzenia (TrustFrameworkExtensions.xml) i upewnij się, że nie wystąpi niepowodzenie weryfikacji.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Rejestrowanie dostawcy oświadczeń usługi Azure AD do podróży użytkownika

Teraz należy dodać dostawcy tożsamości usługi Azure AD do jednego z podróże użytkownika. W tym momencie Konfigurowanie dostawcy tożsamości, ale nie jest dostępna w żadnym ekrany konta-konta/logowania. Aby było to możliwe, firma Microsoft będzie utworzyć kopię istniejącej przebieg użytkownika szablonu, a następnie zmodyfikować go, aby miała ona również dostawcy tożsamości usługi Azure AD:

1. Otwórz plik bazowy tej zasady (na przykład TrustFrameworkBase.xml).
1. Znajdź `<UserJourneys>` element i skopiuj cały `<UserJourney>` węzła, który zawiera `Id="SignUpOrSignIn"`.
1. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodaj je.
1. Wklej całą `<UserJourney>` węzła, który został skopiowany jako element podrzędny `<UserJourneys>` elementu.
1. Zmień nazwę Identyfikatora nowy przebieg użytkownika (na przykład zmienić nazwę jako `SignUpOrSignUsingAzureAD`).

### <a name="display-the-button"></a>Ekran "button"

`<ClaimsProviderSelection>` Element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie konta-konta/logowania. Jeśli dodasz `<ClaimsProviderSelection>` elementu dla usługi Azure AD, nowy przycisk zostaną wyświetlone po wyładowuje użytkownika na stronie. Aby dodać ten element:

1. Znajdź `<OrchestrationStep>` węzła, który zawiera `Order="1"` w podróży użytkownika, który został właśnie utworzony.
1. Dodaj następujące informacje:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Ustaw `TargetClaimsExchangeId` odpowiednią wartość. Zaleca się następujące tej samej Konwencji, co inne osoby:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Połącz przycisku akcji

Teraz, gdy masz przycisku w miejscu, należy połączyć je z akcją. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z usługą Azure AD otrzymujących token. Połączyć przycisku akcji przez łączenie techniczne profilu dla dostawcy oświadczeń usługi Azure AD:

1. Znajdź `<OrchestrationStep>` zawierającą `Order="2"` w `<UserJourney>` węzła.
1. Dodaj następujące informacje:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Aktualizacja `Id` taką samą wartość jak `TargetClaimsExchangeId` w poprzedniej sekcji.
1. Aktualizacja `TechnicalProfileReferenceId` dla identyfikatora profilu techniczne utworzonego wcześniej (typowe-AAD).

### <a name="upload-the-updated-extension-file"></a>Przekaż plik rozszerzenia zaktualizowane

Po zakończeniu modyfikowania pliku rozszerzenia. Zapisz go. Przekaż plik i upewnij się, że wszystkie operacje sprawdzania poprawności powiodło się.

### <a name="update-the-rp-file"></a>Zaktualizuj plik planu odzyskiwania

Należy teraz zaktualizować jednostki uzależnionej pliku strony (RP), który inicjuje przebieg użytkownika, który został właśnie utworzony:

1. Utwórz kopię SignUpOrSignIn.xml w katalogu roboczym i zmień jego nazwę (na przykład, zmień jego nazwę na SignUpOrSignInWithAAD.xml).
1. Otwórz nowy plik i aktualizacji `PolicyId` atrybutu dla `<TrustFrameworkPolicy>` unikatowe wartości (na przykład SignUpOrSignInWithAAD). <br> Są to nazwa zasady (na przykład B2C\_1A\_SignUpOrSignInWithAAD).
1. Modyfikowanie `ReferenceId` atrybutu w `<DefaultUserJourney>` odpowiadające identyfikator nowego użytkownika podróży utworzony (SignUpOrSignUsingAzureAD).
1. Zapisz zmiany i przekazać plik.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Testowanie zasad niestandardowych, które przekazanym właśnie otwierając jego bloku i klikając pozycję **Uruchom teraz**. Do diagnozowania problemów, przeczytaj o [Rozwiązywanie problemów z](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Kolejne kroki

Przekazywania informacji pozwalających na [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
