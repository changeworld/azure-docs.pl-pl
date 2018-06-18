---
title: Dodawanie dostawcy usług Salesforce SAML za pomocą niestandardowych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu tworzenia i zarządzania nimi w zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/11/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f4399730755c15fe5e171bf7fd5826c2b7ffea0a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709652"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Usługa Azure Active Directory B2C: Zaloguj się przy użyciu konta usług Salesforce za pośrednictwem SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono sposób użycia [zasady niestandardowe](active-directory-b2c-overview-custom.md) można skonfigurować konta logowania dla użytkowników z określonych organizacji Salesforce.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-ad-b2c-setup"></a>Instalacja usługi Azure AD B2C

Upewnij się, że zostały wykonane wszystkie kroki, które opisano, jak do [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) w usłudze Azure Active Directory B2C (Azure AD B2C).

Należą do nich:

* Tworzenie dzierżawy usługi Azure AD B2C.
* Tworzenie aplikacji usługi Azure AD B2C.
* Zarejestruj dwie aplikacje aparatu zasad.
* Konfigurowanie kluczy.
* Skonfiguruj pakiet początkowy.

### <a name="salesforce-setup"></a>Instalator usług SalesForce

W tym artykule przyjęto założenie, że już wykonano następujące czynności:

* Konta konta usług Salesforce. Możesz zarejestrować się w celu [bezpłatne konto Developer Edition](https://developer.salesforce.com/signup).
* [Konfigurowanie domeny Moje](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) organizacji Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Konfigurowanie usług Salesforce, umożliwiające użytkownikom można było wykonać Federację

Aby pomóc w usłudze Azure AD B2C komunikować się z usług Salesforce, należy uzyskać adres URL metadanych usługi Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurowanie usług Salesforce jako dostawca tożsamości

> [!NOTE]
> W tym artykule przyjęto założenie, używane są [Salesforce Lightning środowisko](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Zaloguj się do usługi Salesforce](https://login.salesforce.com/). 
2. W menu po lewej stronie w obszarze **ustawienia**, rozwiń węzeł **tożsamości**, a następnie kliknij przycisk **dostawcy tożsamości**.
3. Kliknij przycisk **włączenia dostawcy tożsamości**.
4. W obszarze **wybierz certyfikat**, wybierz żądany certyfikat usługi Salesforce, aby używać do komunikacji z usługą Azure AD B2C. (Możesz użyć domyślnego certyfikatu). Kliknij pozycję **Zapisz**. 

### <a name="create-a-connected-app-in-salesforce"></a>Tworzenie połączonej aplikacji w usłudze Salesforce

1. Na **dostawcy tożsamości** strony, przejdź do **usługodawców**.
2. Kliknij przycisk **usługodawców zostaną utworzone za pośrednictwem połączenia aplikacji. Kliknij tutaj.**
3. W obszarze **podstawowe informacje**, wprowadź wymagane wartości dla połączonych aplikacji.
4. W obszarze **ustawień aplikacji sieci Web**, wybierz pozycję **Włącz SAML** pole wyboru.
5. W **identyfikator jednostki** wprowadź następujący adres URL. Upewnij się, że zastąpić wartość `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. W **adres URL usługi ACS** wprowadź następujący adres URL. Upewnij się, że zastąpić wartość `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Pozostaw wartości domyślne dla wszystkich innych ustawień.
8. Przewiń w dół listy, a następnie kliknij przycisk **zapisać**.

### <a name="get-the-metadata-url"></a>Pobierz adres URL metadanych

1. Na stronie Przegląd połączonych aplikacji, kliknij przycisk **Zarządzaj**.
2. Skopiuj wartość **punkt końcowy odnajdowania metadanych**, a następnie zapisz je. Będzie on używany w dalszej części tego artykułu.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurowanie użytkowników Salesforce możliwości utworzenia Federacji

1. Na **Zarządzaj** strony z połączonych aplikacji, przejdź do **profile**.
2. Kliknij przycisk **zarządzania profilami**.
3. Wybierz profile (lub grupom użytkowników), który ma zostać wykonana Federacja z usługi Azure AD B2C. Administrator systemu, wybierz **administratorem** Sprawdź okno, tak aby można było wykonać Federację przy użyciu konta usług Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Generuj certyfikat podpisywania dla usługi Azure AD B2C

Żądania wysyłane do usługi Salesforce muszą być podpisane przez usługę Azure AD B2C. Aby wygenerować certyfikat podpisywania, Otwórz program Azure PowerShell, a następnie uruchom następujące polecenia.

> [!NOTE]
> Upewnij się, należy zaktualizować dzierżawy nazwę i hasło w dwóch pierwszych wierszy.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Dodawanie certyfikatu podpisywania SAML do usługi Azure AD B2C

Przekaż certyfikat podpisywania dla Twojej dzierżawy usługi Azure AD B2C: 

1. Przejdź do dzierżawy usługi Azure AD B2C. Kliknij przycisk **ustawienia** > **Framework obsługi tożsamości** > **klucze zasad**.
2. Kliknij przycisk **+ Dodaj**, a następnie:
    1. Kliknij przycisk **opcje** > **przekazać**.
    2. Wprowadź **nazwa** (na przykład SAMLSigningCert). Prefiks *B2C_1A_* jest automatycznie dodawany do nazwy klucza.
    3. Aby wybrać certyfikat, wybierz **przekazać formant pliku**. 
    4. Wprowadź hasło certyfikatu, które można ustawić w skrypcie programu PowerShell.
3. Kliknij przycisk **Utwórz**.
4. Sprawdź, czy utworzono klucz (na przykład B2C_1A_SAMLSigningCert). Zwróć uwagę, jaka jest pełna nazwa (włącznie z *B2C_1A_*). Będzie odnosić się do tego klucza później w zasadach.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Tworzenie dostawcy oświadczeń Salesforce SAML w zasadach podstawowej

Musisz zdefiniować Salesforce jako dostawcy oświadczeń, dlatego użytkownicy mogą zarejestrować się przy użyciu Salesforce. Innymi słowy należy określić punkt końcowy, który usługi Azure AD B2C będzie komunikować się z. Punkt końcowy zostanie *podaj* zestaw *oświadczeń* używającej usługi Azure AD B2C, aby sprawdzić, czy określony użytkownik został uwierzytelniony. Aby to zrobić, należy dodać `<ClaimsProvider>` dla usług Salesforce w pliku rozszerzenie zasad:

1. W katalogu roboczym otwórz plik rozszerzenia (TrustFrameworkExtensions.xml).
2. Znajdź `<ClaimsProviders>` sekcji. Jeśli nie istnieje, utwórz go w obszarze węzła głównego.
3. Dodaj nową `<ClaimsProvider>`:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

W obszarze `<ClaimsProvider>` węzła:

1. Zmień wartość atrybutu `<Domain>` unikatowa wartość odróżniająca `<ClaimsProvider>` od innych dostawców tożsamości.
2. Zaktualizuj wartość `<DisplayName>` na nazwę wyświetlaną dostawcy oświadczeń. Ta wartość nie jest obecnie używana.

### <a name="update-the-technical-profile"></a>Aktualizuj profil techniczne

Aby uzyskać tokenu SAML z usług Salesforce, zdefiniuj protokołów używających usługi Azure AD B2C do komunikowania się z usługą Azure Active Directory (Azure AD). Tym `<TechnicalProfile>` elementu `<ClaimsProvider>`:

1. Aktualizacja Identyfikatora `<TechnicalProfile>` węzła. Ten identyfikator służy do odwoływania się do tego profilu techniczne z innymi częściami zasad.
2. Zaktualizuj wartość `<DisplayName>`. Ta wartość jest wyświetlany na przycisku logowania na stronie logowania.
3. Zaktualizuj wartość `<Description>`.
4. SalesForce korzysta z protokołu SAML 2.0. Upewnij się, że wartość `<Protocol>` jest **SAML2**.

Aktualizacja `<Metadata>` części poprzedniego XML, aby uwzględnić ustawienia dla określonego konta usług Salesforce. W pliku XML zaktualizuj wartości metadanych:

1. Zaktualizuj wartość `<Item Key="PartnerEntity">` z adresem URL metadanych usługi Salesforce, które zostały wcześniej zostały skopiowane. Ma następujący format: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. W `<CryptographicKeys>` sekcji, zaktualizuj tę wartość dla obu wystąpień `StorageReferenceId` na nazwę klucza certyfikatu podpisywania (na przykład B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia do weryfikacji

Twoje zasady został skonfigurowany tak, aby usługi Azure AD B2C potrafi do komunikowania się z usług Salesforce. Spróbuj przekazać plik rozszerzenia zasad, aby sprawdzić, czy nie ma żadnych problemów wykonanej do tej pory. Aby przekazać plik rozszerzenia zasad:

1. W dzierżawie usługi Azure AD B2C, przejdź do **wszystkie zasady** bloku.
2. Wybierz **zastąpić zasady, jeśli istnieje** pole wyboru.
3. Przekaż plik rozszerzenia (TrustFrameworkExtensions.xml). Upewnij się, że nie wystąpi niepowodzenie weryfikacji.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Zarejestruj dostawcę oświadczeń Salesforce SAML na przebieg użytkownika

Następnie dodaj dostawca tożsamości Salesforce SAML do jednego z podróże użytkownika. W tym momencie Konfigurowanie dostawcy tożsamości, ale nie jest dostępny na stronach rejestracji i logowania użytkownika. Aby dodać dostawcę tożsamości do strony logowania, najpierw utwórz duplikatem istniejącej przebieg użytkownika szablonu. Następnie należy zmodyfikować szablon, dzięki czemu ma również dostawcy tożsamości usługi Azure AD.

1. Otwórz plik bazowy tej zasady (na przykład TrustFrameworkBase.xml).
2. Znajdź `<UserJourneys>` elementu, a następnie skopiuj cały `<UserJourney>` wartość, tym Id = "SignUpOrSignIn".
3. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml). Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, utwórz je.
4. Wklej skopiowany cały `<UserJourney>` jako element podrzędny `<UserJourneys>` elementu.
5. Zmień nazwę Identyfikatora nowej `<UserJourney>` (na przykład SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Wyświetlany przycisk dostawcy tożsamości

`<ClaimsProviderSelection>` Element jest odpowiednikiem przycisk dostawcy tożsamości na stronie tworzenia konta lub logowania. Dodając `<ClaimsProviderSelection>` elementu Salesforce, nowy przycisk jest wyświetlany, gdy użytkownik przechodzi do tej strony. Aby wyświetlić przycisk dostawcy tożsamości:

1. W `<UserJourney>` utworzony, Znajdź `<OrchestrationStep>` z `Order="1"`.
2. Dodaj następujący kod XML:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Ustaw `TargetClaimsExchangeId` na wartość logiczną. Zaleca się następujące tej samej Konwencji jako innych (na przykład  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Połącz przycisk dostawcy tożsamości akcji

Teraz, gdy masz przycisk dostawcy tożsamości w miejscu, należy go powiązać akcji. W takim przypadku akcja jest dla usługi Azure AD B2C do komunikowania się z Salesforce odebrać tokenu SAML. Można to zrobić przez łączenie techniczne profilu dla użytkownika SAML Salesforce dostawcy oświadczeń:

1. W `<UserJourney>` węzła, Znajdź `<OrchestrationStep>` z `Order="2"`.
2. Dodaj następujący kod XML:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Aktualizacja `Id` na tę samą wartość, który był wcześniej używany przez `TargetClaimsExchangeId`.
4. Aktualizacja `TechnicalProfileReferenceId` do `Id` techniczne profilu utworzonego wcześniej (na przykład ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Przekaż plik rozszerzenia zaktualizowane

Po zakończeniu modyfikowania pliku rozszerzenia. Zapisz i przekazywanie tego pliku. Upewnij się, że wszystkie operacje sprawdzania poprawności powiodło się.

### <a name="update-the-relying-party-file"></a>Zaktualizuj plik jednostki uzależnionej strony

Następnie zaktualizuj jednostki uzależnionej pliku strony (RP), który inicjuje przebieg użytkownika, który został utworzony:

1. Utwórz kopię SignUpOrSignIn.xml w katalogu roboczym. Następnie należy zmienić jego nazwę (na przykład SignUpOrSignInWithAAD.xml).
2. Otwórz nowy plik i aktualizacji `PolicyId` atrybutu dla `<TrustFrameworkPolicy>` z unikatową wartość. Jest to nazwa zasady (na przykład SignUpOrSignInWithAAD).
3. Modyfikowanie `ReferenceId` atrybutu w `<DefaultUserJourney>` odpowiadające `Id` nowe podróży użytkownika utworzony (na przykład SignUpOrSignUsingContoso).
4. Zapisz zmiany, a następnie przekazać plik.

## <a name="test-and-troubleshoot"></a>Testowanie i rozwiązywanie problemów

Testowanie zasad niestandardowych, które przekazanym właśnie, w portalu Azure, przejdź do bloku zasady, a następnie kliknij przycisk **Uruchom teraz**. Jeśli nie powiedzie się, zobacz [Rozwiązywanie problemów dotyczących zasad niestandardowych](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Kolejne kroki

Przekazywania informacji pozwalających na [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
