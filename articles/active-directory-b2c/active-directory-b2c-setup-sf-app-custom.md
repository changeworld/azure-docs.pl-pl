---
title: Dodawanie dostawcy Salesforce SAML za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak tworzyć i zarządzać nimi w zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1307fc455cacde81cb25ad58c5e99df21f126568
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448258"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Usługa Azure Active Directory B2C: Zaloguj się przy użyciu konta usługi Salesforce za pośrednictwem protokołu SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule dowiesz się, jak używać [zasady niestandardowe](active-directory-b2c-overview-custom.md) do skonfigurowania logowania dla użytkowników z określonych organizacji usługi Salesforce.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-ad-b2c-setup"></a>Konfiguracja usługi Azure AD B2C

Upewnij się, że zostały wykonane wszystkie kroki, które pokazują, jak do [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) w usłudze Azure Active Directory B2C (Azure AD B2C).

Należą do nich:

* Tworzenie dzierżawy usługi Azure AD B2C.
* Jak utworzyć aplikację usługi Azure AD B2C.
* Zarejestruj dwie aplikacje aparatu zasad.
* Konfigurowanie kluczy.
* Skonfiguruj pakiet startowy.

### <a name="salesforce-setup"></a>Konfiguracja usługi SalesForce

W tym artykule przyjęto założenie, że już wykonano następujące czynności:

* Zarejestrowali się do konta usługi Salesforce. Możesz zarejestrować się w celu [bezpłatne konto Developer Edition](https://developer.salesforce.com/signup).
* [Konfigurowanie Moja domena](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) dla Twojej organizacji w usłudze Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Konfigurowanie usługi Salesforce, dzięki czemu użytkownicy mogą tworzyć federacje

Aby komunikować się z usług Salesforce usługi Azure AD B2C, musisz uzyskać adres URL metadanych usługi Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurowanie usługi Salesforce jako dostawcy tożsamości

> [!NOTE]
> W tym artykule przyjęto założenie, którego używasz [środowisko pod kątem obsługi usługi Salesforce](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Zaloguj się do usługi Salesforce](https://login.salesforce.com/). 
2. W menu po lewej stronie w obszarze **ustawienia**, rozwiń węzeł **tożsamości**, a następnie kliknij przycisk **dostawcy tożsamości**.
3. Kliknij przycisk **umożliwiają dostawcy tożsamości**.
4. W obszarze **wybierz certyfikat**, wybierz certyfikat, który ma Salesforce można używać do komunikowania się z usługą Azure AD B2C. (Użyj domyślnego certyfikatu). Kliknij pozycję **Zapisz**. 

### <a name="create-a-connected-app-in-salesforce"></a>Tworzenie połączonych aplikacji w usłudze Salesforce

1. Na **dostawcy tożsamości** strony, przejdź do **usługodawców**.
2. Kliknij przycisk **dostawców usług są teraz tworzone za pomocą aplikacji połączonej z usługą. Kliknij tutaj.**
3. W obszarze **podstawowe informacje**, wprowadź wymagane wartości dla połączonych aplikacji.
4. W obszarze **ustawień aplikacji sieci Web**, wybierz opcję **Włącz SAML** pole wyboru.
5. W **identyfikator jednostki** wprowadź następujący adres URL. Upewnij się, zastąp wartość `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. W **adres URL usługi ACS** wprowadź następujący adres URL. Upewnij się, zastąp wartość `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Pozostaw wartości domyślne dla wszystkich innych ustawień.
8. Przewiń w dół listy, a następnie kliknij przycisk **Zapisz**.

### <a name="get-the-metadata-url"></a>Pobierz adres URL metadanych

1. Na stronie Przegląd połączonych aplikacji, kliknij przycisk **Zarządzaj**.
2. Skopiuj wartość dla **punkt końcowy odnajdowania metadanych**, a następnie zapisz go. Zostanie on użyty w dalszej części tego artykułu.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurowanie użytkowników usług Salesforce w celu sfederowania

1. Na **Zarządzaj** strony z połączonych aplikacji, przejdź do **profile**.
2. Kliknij przycisk **Zarządzanie profilami**.
3. Wybierz profile (lub grupom użytkowników), którą chcesz utworzyć Federację z usługą Azure AD B2C. Administrator systemu, wybierz **Administrator systemu** pole wyboru, dzięki czemu może tworzyć federacje przy użyciu konta usługi Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Generuj certyfikat podpisywania dla usługi Azure AD B2C

Żądania wysyłane do usługi Salesforce, musisz zalogować się za pomocą usługi Azure AD B2C. Aby wygenerować certyfikat podpisywania, Otwórz program Azure PowerShell, a następnie uruchom następujące polecenia.

> [!NOTE]
> Pamiętaj o zaktualizowaniu nazwy dzierżawy i hasło w dwóch pierwszych wierszy.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Dodawanie certyfikatu podpisywania protokołu SAML do usługi Azure AD B2C

Przekaż certyfikat podpisywania z dzierżawą usługi Azure AD B2C: 

1. Przejdź do dzierżawy usługi Azure AD B2C. Kliknij przycisk **ustawienia** > **struktura środowiska tożsamości** > **klucze zasad**.
2. Kliknij przycisk **+ Dodaj**, a następnie:
    1. Kliknij przycisk **opcje** > **przekazywanie**.
    2. Wprowadź **nazwa** (na przykład SAMLSigningCert). Prefiks *B2C_1A_* jest automatycznie dodawany do nazwy klucza.
    3. Aby wybrać certyfikat, wybierz **przekazywanie pliku sterowania**. 
    4. Wprowadź hasło do certyfikatu, który zostało ustawiony w skrypcie programu PowerShell.
3. Kliknij przycisk **Utwórz**.
4. Sprawdź, czy utworzono klucza (na przykład B2C_1A_SAMLSigningCert). Zwróć uwagę, pełna nazwa (włącznie z *B2C_1A_*). Będzie odnosił się do tego klucza w dalszej części zasad.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Tworzenie usługi Salesforce SAML dostawcy oświadczeń w zasadach podstawowych

Należy zdefiniować Salesforce jako dostawcy oświadczeń, dzięki czemu użytkownicy mogą logować się za pomocą usługi Salesforce. Innymi słowy należy określić punkt końcowy, który usługa Azure AD B2C będą komunikować się z. Punkt końcowy zostanie *zapewniają* zbiór *oświadczeń* używającej usługi Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony. Aby to zrobić, Dodaj `<ClaimsProvider>` usługi Salesforce w pliku rozszerzenie zasad:

1. W katalogu roboczym otwórz plik rozszerzenia (TrustFrameworkExtensions.xml).
2. Znajdź `<ClaimsProviders>` sekcji. Jeśli nie istnieje, należy go utworzyć, w węźle głównym.
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

1. Zmień wartość `<Domain>` na unikatową wartość, która odróżnia `<ClaimsProvider>` od innych dostawców tożsamości.
2. Zaktualizuj wartość `<DisplayName>` na nazwę wyświetlaną dla dostawcy oświadczeń. Ta wartość nie jest obecnie używana.

### <a name="update-the-technical-profile"></a>Aktualizuj profil techniczny

Aby uzyskać token SAML z usług Salesforce, zdefiniuj protokołów używających usługi Azure AD B2C do komunikowania się z usługą Azure Active Directory (Azure AD). To zrobić w `<TechnicalProfile>` elementu `<ClaimsProvider>`:

1. Zaktualizuj identyfikator `<TechnicalProfile>` węzła. Ten identyfikator służy do odwoływania się do tego profilu technicznego z innymi częściami zasad.
2. Zaktualizuj wartość `<DisplayName>`. Ta wartość jest wyświetlana na przycisku logowania na stronie logowania.
3. Zaktualizuj wartość `<Description>`.
4. SalesForce korzysta z protokołu SAML 2.0. Upewnij się, że wartość `<Protocol>` jest **SAML2**.

Aktualizacja `<Metadata>` sekcji poprzedni kod XML w celu odzwierciedlenia ustawień dla określonego konta usługi Salesforce. W pliku XML zaktualizuj wartości metadanych:

1. Zaktualizuj wartość `<Item Key="PartnerEntity">` za pomocą adresu URL metadanych usługi Salesforce, które wcześniej zostały skopiowane. Ma następujący format: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. W `<CryptographicKeys>` sekcji, zaktualizuj wartość dla obu wystąpień `StorageReferenceId` nazwę klucza podpisywania certyfikatu (na przykład B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia o weryfikację

Twoje zasady został skonfigurowany tak, aby usługa Azure AD B2C wie, jak nawiązać połączenia z usługą Salesforce. Spróbuj przekazać plik rozszerzenia zasad, aby sprawdzić, czy nie ma żadnych problemów do tej pory. Aby przekazać plik rozszerzenia zasad:

1. W swojej dzierżawie usługi Azure AD B2C, przejdź do **wszystkie zasady** bloku.
2. Wybierz **Zastąp zasady Jeśli istnieje** pole wyboru.
3. Przekaż plik rozszerzenia (TrustFrameworkExtensions.xml). Upewnij się, że nie wystąpi niepowodzenie weryfikacji.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Zarejestruj dostawcę oświadczeń SAML usług Salesforce do podróży użytkownika

Następnie dodaj dostawcy tożsamości SAML usług Salesforce do jednego z Twojej podróży użytkownika. W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest dostępne na wszystkich stronach rejestracji lub logowania użytkownika. Aby dodać dostawcę tożsamości do strony logowania, najpierw utwórz duplikatem istniejącej podróży użytkownika szablonu. Następnie należy zmodyfikować szablon, aby miała ona również dostawcy tożsamości usługi Azure AD.

1. Otwórz plik podstawowy zasady (na przykład TrustFrameworkBase.xml).
2. Znajdź `<UserJourneys>` elementu, a następnie skopiuj cały `<UserJourney>` wartość, łącznie z Id = "SignUpOrSignIn".
3. Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml). Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, utwórz ją.
4. Wklej całą skopiowane `<UserJourney>` jako element podrzędny elementu `<UserJourneys>` elementu.
5. Zmień nazwę identyfikator nowej `<UserJourney>` (na przykład SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Wyświetlić przycisk dostawcy tożsamości

`<ClaimsProviderSelection>` Element jest odpowiednikiem przycisk dostawcy tożsamości, na stronie tworzenia konta lub logowania. Dodając `<ClaimsProviderSelection>` element usługi Salesforce, nowy przycisk jest wyświetlany, gdy użytkownik przechodzi do tej strony. Aby wyświetlić przycisk dostawcy tożsamości:

1. W `<UserJourney>` utworzony, Znajdź `<OrchestrationStep>` z `Order="1"`.
2. Dodaj następujący kod XML:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Ustaw `TargetClaimsExchangeId` na wartość logiczną. Jednak zalecamy zastosowanie tej samej Konwencji co inne osoby (na przykład  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Połącz przycisk dostawcy tożsamości z akcji

Teraz, gdy przycisk dostawcy tożsamości w miejscu, należy go połączyć akcji. W tym przypadku akcja jest dla usługi Azure AD B2C do komunikowania się z usług Salesforce do odbierania SAML token. Można to zrobić, łącząc profilu technicznego dla usługi Salesforce SAML dostawcy oświadczeń:

1. W `<UserJourney>` węzła, Znajdź `<OrchestrationStep>` z `Order="2"`.
2. Dodaj następujący kod XML:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Aktualizacja `Id` taką samą wartość, która została użyta wcześniej dla `TargetClaimsExchangeId`.
4. Aktualizacja `TechnicalProfileReferenceId` do `Id` techniczne profilu utworzonego wcześniej (na przykład ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Przekaż zaktualizowany rozszerzenie pliku

Po zakończeniu modyfikowania pliku rozszerzenia. Zapisz, a następnie przekaż ten plik. Upewnij się, że wszystkie sprawdzenia powiodło się.

### <a name="update-the-relying-party-file"></a>Aktualizowanie pliku strony jednostki uzależnionej

Następnie zaktualizuj plik innych firm (RP) jednostki uzależnionej, który inicjuje podróży użytkownika, który został utworzony:

1. Utwórz kopię SignUpOrSignIn.xml w katalogu roboczym. Następnie zmień jego nazwę (na przykład SignUpOrSignInWithAAD.xml).
2. Otwórz nowy plik i zaktualizuj `PolicyId` atrybutu dla `<TrustFrameworkPolicy>` przy użyciu unikatowej wartości. To jest nazwa zasady (na przykład SignUpOrSignInWithAAD).
3. Modyfikowanie `ReferenceId` atrybutu w `<DefaultUserJourney>` do dopasowania `Id` nowe podróży użytkownika, który został utworzony (na przykład SignUpOrSignUsingContoso).
4. Zapisz zmiany, a następnie przekazać plik.

## <a name="test-and-troubleshoot"></a>Testowanie i rozwiązywanie problemów

Aby przetestować zasad niestandardowych, który właśnie został przekazany, w witrynie Azure portal, przejdź do bloku zasady, a następnie kliknij **Uruchom teraz**. Jeśli nie powiedzie się, zobacz [Rozwiązywanie problemów dotyczących zasad niestandardowych](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Kolejne kroki

Prześlij opinię do [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
