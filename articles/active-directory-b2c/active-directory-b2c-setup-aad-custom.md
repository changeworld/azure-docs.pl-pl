---
title: Dodaj dostawcę usługi Azure AD za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cb70828960220bbc1e37321b77375fe2c42ee1b6
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060716"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Usługa Azure Active Directory B2C: Zaloguj się przy użyciu konta usługi Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule dowiesz się, jak włączyć logowanie dla użytkowników z określonych organizacji usługi Azure Active Directory (Azure AD) za pośrednictwem [zasady niestandardowe](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

Kroki te obejmują:

1. Tworzenie usługi Azure Active Directory B2C (Azure AD B2C) dzierżawy.
2. Tworzenie aplikacji usługi Azure AD B2C.
3. Rejestrowanie dwie aplikacje aparatu zasad.
4. Konfigurowanie kluczy.
5. Konfigurowanie pakietu startowego.

## <a name="create-an-azure-ad-app"></a>Tworzenie aplikacji usługi Azure AD

Aby włączyć logowania dla użytkowników z określonym organizacji usługi Azure AD, musisz zarejestrować aplikację w organizacji dzierżawy usługi Azure AD.

>[!NOTE]
> Używamy "contoso.com" w celu organizacyjne dzierżawy usługi Azure AD i "fabrikamb2c.onmicrosoft.com" jako dzierżawy usługi Azure AD B2C w poniższych instrukcjach.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku wybierz swoje konto. Z **katalogu** wybierz organizacyjne dzierżawy usługi Azure AD, w którym chcesz zarejestrować aplikację (contoso.com).
3. Wybierz **więcej usług** w okienku po lewej stronie i wyszukaj "Rejestracje aplikacji".
4. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
5. Wprowadź nazwę aplikacji (na przykład `Azure AD B2C App`).
6. Jako typ aplikacji wybierz pozycję **Interfejs API/aplikacja internetowa**.
7. Aby uzyskać **adres URL logowania**, wprowadź następujący adres URL, gdzie `yourtenant` jest zastępowana nazwą dzierżawy usługi Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Wartość "yourtenant" musi być tylko małe litery w **adres URL logowania**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

8. Zapisz identyfikator aplikacji.
9. Wybierz nowo utworzoną aplikację.
10. W obszarze **ustawienia** bloku wybierz **klucze**.
11. Wprowadź opis klucza, wybierz czas trwania, a następnie kliknij **Zapisz**. Wartość klucza jest wyświetlana. Skopiuj go, ponieważ użyjesz go w krokach w następnej sekcji.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Dodaj klucz usługi Azure AD do usługi Azure AD B2C

Chcesz przechować contoso.com klucz aplikacji w dzierżawie usługi Azure AD B2C. W tym celu:
1. Przejdź do dzierżawy usługi Azure AD B2C i wybierz **ustawieniami B2C** > **struktura środowiska tożsamości** > **klucze zasad**.
1. Wybierz **+ Dodaj**.
1. Wybierz lub wprowadź następujące opcje:
   * Wybierz **ręczne**.
   * Aby uzyskać **nazwa**, wybierz nazwę, która jest zgodna nazwa dzierżawy usługi Azure AD (na przykład `ContosoAppSecret`).  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
   * Wklej swój klucz aplikacji w **klucz tajny** pole.
   * Wybierz **podpisu**.
1. Wybierz pozycję **Utwórz**.
1. Upewnij się, że utworzono klucz `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Dodawanie dostawcy oświadczeń w zasadach podstawowych

Użytkownikom na logowanie za pomocą usługi Azure AD, należy zdefiniować usługi Azure AD jako dostawcy oświadczeń. Innymi słowy należy określić punkt końcowy usługi Azure AD B2C będą komunikować się z. Punkt końcowy udostępni zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony. 

Można zdefiniować usługi Azure AD jako dostawcy oświadczeń, przez dodanie usługi Azure AD do `<ClaimsProvider>` węzeł w pliku rozszerzenie zasad:

1. Otwórz plik rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego.
1. Znajdź `<ClaimsProviders>` sekcji. Jeśli nie istnieje, należy go dodać w węźle głównym.
1. Dodaj nową `<ClaimsProvider>` węzła w następujący sposób:

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. W obszarze `<ClaimsProvider>` węzła, zaktualizuj wartość `<Domain>` na unikatową wartość, która może służyć do odróżnienia go od innych dostawców tożsamości.
1. W obszarze `<ClaimsProvider>` węzła, zaktualizuj wartość `<DisplayName>` przyjazną nazwę dla dostawcy oświadczeń. Ta wartość nie jest obecnie używana.

### <a name="update-the-technical-profile"></a>Aktualizuj profil techniczny

Aby uzyskać token z punktu końcowego usługi Azure AD, musisz zdefiniować protokołów, które usługi Azure AD B2C należy używać do komunikowania się z usługą Azure AD. Odbywa się wewnątrz `<TechnicalProfile>` elementu `<ClaimsProvider>`.
1. Zaktualizuj identyfikator `<TechnicalProfile>` węzła. Ten identyfikator służy do odwoływania się do tego profilu technicznego z innymi częściami zasad.
1. Zaktualizuj wartość `<DisplayName>`. Ta wartość będzie wyświetlana na przycisku logowania na ekranie logowania.
1. Zaktualizuj wartość `<Description>`.
1. Usługa Azure AD używa protokołu OpenID Connect, dlatego upewnij się, że wartość `<Protocol>` jest `"OpenIdConnect"`.

Należy zaktualizować `<Metadata>` sekcji w pliku XML określonej wcześniej do odzwierciedlają ustawienia konfiguracyjne dla konkretnej dzierżawy usługi Azure AD. W pliku XML zaktualizuj wartości metadanych w następujący sposób:

1. Ustaw `<Item Key="METADATA">` do `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, gdzie `yourAzureADtenant` jest nazwa dzierżawy usługi Azure AD (contoso.com).
1. Otwórz przeglądarkę i przejdź do `METADATA` adresu URL, który właśnie został zaktualizowany.
1. W przeglądarce wyszukaj obiekt "issuer" i skopiuj jego wartość. Powinno to wyglądać podobnie do następującego: `https://sts.windows.net/{tenantId}/`.
1. Wklej wartość `<Item Key="ProviderName">` w pliku XML.
1. Ustaw `<Item Key="client_id">` do Identyfikatora aplikacji z rejestracji aplikacji.
1. Ustaw `<Item Key="IdTokenAudience">` do Identyfikatora aplikacji z rejestracji aplikacji.
1. Upewnij się, że `<Item Key="response_types">` ustawiono `id_token`.
1. Upewnij się, że `<Item Key="UsePolicyInRedirectUri">` ustawiono `false`.

Musisz także utworzyć link klucz tajny usługi Azure AD, która została zarejestrowana w dzierżawie usługi Azure AD B2C do usługi Azure AD `<ClaimsProvider>` informacji:

* W `<CryptographicKeys>` sekcji w poprzednim pliku XML, zaktualizuj wartość `StorageReferenceId` Identyfikator odwołania klucza tajnego, który zdefiniowano (na przykład `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia o weryfikację

W razie skonfigurowano zasady tak, aby usługa Azure AD B2C wie, jak komunikować się z katalogiem usługi Azure AD. Spróbuj przekazać plik rozszerzenia swoich zasad, aby potwierdzić, że wszystkie problemy nie ma do tej pory. W tym celu:

1. Otwórz **wszystkie zasady** bloku w dzierżawie usługi Azure AD B2C.
1. Sprawdź **Zastąp zasady Jeśli istnieje** pole.
1. Przekaż plik rozszerzenia (TrustFrameworkExtensions.xml) i upewnij się, że nie wystąpi niepowodzenie weryfikacji.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Zarejestruj dostawcę usługi Azure AD oświadczeń podróży użytkownika

Teraz należy dodać dostawcę tożsamości usługi Azure AD do jednego z Twojej podróży użytkownika. W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest dostępny w wszystkich ekranów konta-dokonywania/logowania. Aby udostępnić ją, firma Microsoft będzie utworzenie duplikatu istniejącego podróży użytkownika szablonu, a następnie zmodyfikuj tak, aby w nim również dostawcy tożsamości usługi Azure AD:

1. Otwórz plik podstawowy zasady (na przykład TrustFrameworkBase.xml).
1. Znajdź `<UserJourneys>` elementu i skopiować całą `<UserJourney>` węzeł, który zawiera `Id="SignUpOrSignIn"`.
1. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodać.
1. Wklej całą `<UserJourney>` węzeł, który został skopiowany jako element podrzędny elementu `<UserJourneys>` elementu.
1. Zmień nazwę identyfikator nowego podróży użytkownika (na przykład zmień nazwę `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Ekran "button"

`<ClaimsProviderSelection>` Element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie konta-dokonywania/logowania. Jeśli dodasz `<ClaimsProviderSelection>` elementu dla usługi Azure AD, nowy przycisk pojawia się po użytkownik wyładowuje na stronie. Aby dodać ten element:

1. Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
1. Dodaj następujący fragment kodu:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Ustaw `TargetClaimsExchangeId` odpowiednią wartość. Jednak zalecamy zastosowanie tej samej Konwencji co inne osoby:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z usługą Azure AD, aby otrzymać token. Łącze przycisku do akcji, łącząc profilu technicznego dla dostawcy oświadczeń usługi Azure AD:

1. Znajdź `<OrchestrationStep>` zawierającej `Order="2"` w `<UserJourney>` węzła.
1. Dodaj następujący fragment kodu:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Aktualizacja `Id` taką samą wartość jak w przypadku `TargetClaimsExchangeId` w poprzedniej sekcji.
1. Aktualizacja `TechnicalProfileReferenceId` identyfikator profilu technicznego utworzonego wcześniej (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Przekaż zaktualizowany rozszerzenie pliku

Po zakończeniu modyfikowania pliku rozszerzenia. Zapisz go. Następnie przekaż plik i upewnij się, że wszystkie sprawdzenia powiodło się.

### <a name="update-the-rp-file"></a>Zaktualizuj plik RP

Teraz musisz zaktualizować plik innych firm (RP) jednostki uzależnionej, który będzie inicjował podróży użytkownika, który został utworzony:

1. Utwórz kopię SignUpOrSignIn.xml w katalogu roboczym i zmień jego nazwę (na przykład, zmień jego nazwę na SignUpOrSignInWithAAD.xml).
1. Otwórz nowy plik i zaktualizuj `PolicyId` atrybutu dla `<TrustFrameworkPolicy>` przy użyciu unikatowej wartości (na przykład SignUpOrSignInWithAAD). <br> Są to nazwę swoich zasad (na przykład B2C\_1A\_SignUpOrSignInWithAAD).
1. Modyfikowanie `ReferenceId` atrybutu w `<DefaultUserJourney>` aby dopasować identyfikator nowego podróży użytkownika utworzony (SignUpOrSignUsingContoso).
1. Zapisz zmiany i przekazać plik.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Testowanie zasad niestandardowych, który właśnie został przekazany, otworzyć jego blok i klikając **Uruchom teraz**. Diagnozowanie problemów, przeczytaj temat [Rozwiązywanie problemów z](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Kolejne kroki

Prześlij opinię do [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
