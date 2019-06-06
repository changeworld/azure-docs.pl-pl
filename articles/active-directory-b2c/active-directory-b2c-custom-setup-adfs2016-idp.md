---
title: Dodawanie usług AD FS jako dostawcy tożsamości SAML, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Konfigurowanie usług AD FS 2016 przy użyciu protokołu SAML i zasad niestandardowych w usłudze Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4510074619ff513b7284819d88fdb2532e4ce33a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510429"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie usług AD FS jako dostawcy tożsamości SAML, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie dla konta użytkownika usługi AD FS za pomocą [zasady niestandardowe](active-directory-b2c-overview-custom.md) w usłudze Azure Active Directory (Azure AD) B2C. Włącz logowanie, dodając [profil techniczny SAML](saml-technical-profile.md) do zasad niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki [wprowadzenie do zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Upewnij się, że masz dostęp do pliku PFX certyfikatu z kluczem prywatnym. Można wygenerować własny certyfikat z podpisem i przekaż go do usługi Azure AD B2C. Usługa Azure AD B2C używa tego certyfikatu do podpisania żądania języka SAML wysłanego do dostawcy tożsamości SAML.

## <a name="create-a-policy-key"></a>Utwórz klucz zasad

Potrzebujesz przechować certyfikat w dzierżawie usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Wybierz **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twojej dzierżawy.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz **struktura środowiska tożsamości**.
5. Wybierz **klucze zasad** , a następnie wybierz **Dodaj**.
6. Aby uzyskać **opcje**, wybierz `Upload`.
7. Wprowadź **nazwa** klucza zasad. Na przykład `SamlCert`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. Przejdź do, a następnie wybierz swój plik PFX certyfikatu z kluczem prywatnym.
9. Kliknij pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Użytkownikom na logowanie przy użyciu konta usług AD FS, należy zdefiniować konto jako dostawcy oświadczeń, które usługi Azure AD B2C mogą się komunikować za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony. 

Konto usług AD FS można zdefiniować jako dostawcy oświadczeń, przez dodanie jej do **ClaimsProviders** elementu w pliku rozszerzenie zasad.

1. Otwórz *TrustFrameworkExtensions.xml*.
2. Znajdź **ClaimsProviders** elementu. Jeśli nie istnieje, należy dodać go pod elementem głównym.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

4. Zastąp `your-ADFS-domain` z nazwą domeny usług AD FS i zastąp wartość **identityProvider** oświadczeń wychodzących za pomocą usługi DNS (dowolną wartością, którą wskazuje domenę).
5. Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia o weryfikację

W razie skonfigurowano zasady tak, aby usługa Azure AD B2C wie, jak komunikować się z kontem usług AD FS. Spróbuj przekazać plik rozszerzenia swoich zasad, aby potwierdzić, że wszystkie problemy nie ma do tej pory.

1. Na **zasady niestandardowe** strony w swojej dzierżawie usługi Azure AD B2C, wybierz opcję **zasady przekazywania**.
2. Włącz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustFrameworkExtensions.xml* pliku.
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest dostępny w wszystkich ekranów rejestracji lub logowania. Aby udostępnić ją, utworzenie duplikatu istniejącego podróży użytkownika szablonu, a następnie zmodyfikuj go tak, aby w nim również dostawcy tożsamości usługi AD FS.

1. Otwórz *TrustFrameworkBase.xml* plik z pakietu startowego.
2. Znajdź i skopiuj cała zawartość **UserJourney** element, który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *TrustFrameworkExtensions.xml* i Znajdź **podróży użytkowników** elementu. Jeśli element nie istnieje, dodać.
4. Wklej całą zawartość **UserJourney** element, który został skopiowany jako element podrzędny elementu **podróży użytkowników** elementu.
5. Zmień nazwę identyfikator podróży użytkownika. Na przykład `SignUpSignInADFS`.

### <a name="display-the-button"></a>Wyświetlanie przycisku

**Elemencie ClaimsProviderSelection** element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz **elemencie ClaimsProviderSelection** elementu dla konta usług AD FS, nowy przycisk pojawia się po użytkownik wyładowuje na stronie.

1. Znajdź **OrchestrationStep** element, który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelections**, Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** do odpowiedniej wartości, na przykład `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z kontem usług AD FS do odbierania token.

1. Znajdź **OrchestrationStep** zawierającej `Order="2"` w podróży użytkownika.
2. Dodaj następujący kod **elementu ClaimsExchange** upewniając się, użyj tej samej wartości dla Identyfikatora, który był używany przez element **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    Zaktualizuj wartość **TechnicalProfileReferenceId** identyfikator profilu technicznego została utworzona wcześniej. Na przykład `Contoso-SAML2`.

3. Zapisz *TrustFrameworkExtensions.xml* plik i ponownie przekazać go do weryfikacji.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurowanie usług AD FS, zaufanie jednostki uzależnionej

Aby korzystać z usług AD FS jako dostawcy tożsamości w usłudze Azure AD B2C, musisz utworzyć usług AD FS zaufania jednostki uzależnionej przy użyciu metadanych SAML usługi Azure AD B2C. Poniższy przykład przedstawia adresu URL metadanych SAML profilu technicznego usługi Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Zastąp następujące wartości:

- **Twojej dzierżawy** nazwą dzierżawy, takich jak usługi tenant.onmicrosoft.com.
- **Usługi zasad** nazwą zasady. Na przykład B2C_1A_signup_signin_adfs.
- **Twój profil techniczny** o nazwie profilu technicznego dostawcy tożsamości SAML. Na przykład Contoso-SAML2.
 
Otwórz przeglądarkę i przejdź do adresu URL. Upewnij się, wpisz poprawny adres URL i czy masz dostęp do pliku metadanych XML. Aby dodać nowe zaufanie jednostki uzależnionej przy użyciu przystawki Zarządzanie usługi AD FS i ręcznie skonfigurować ustawienia, należy wykonać poniższą procedurę na serwerze federacyjnym. Członkostwo w grupie **Administratorzy** lub równoważnej na komputerze lokalnym jest minimalnym warunkiem potrzebnym do ukończenia tej procedury.

1. W Menedżerze serwera wybierz **narzędzia**, a następnie wybierz pozycję **zarządzania usług AD FS**.
2. Wybierz **Dodaj zaufanie jednostki uzależnionej**.
3. Na **powitalnej** wybierz **oświadczenia pamiętać**, a następnie kliknij przycisk **Start**.
4. Na **wybierz źródło danych** wybierz **importowanie danych dotyczących jednostki uzależnionej publikowania online lub w sieci lokalnej**, podaj adres URL metadanych usługi Azure AD B2C, a następnie kliknij **dalej**.
5. Na **Określ nazwę wyświetlaną** wpisz **nazwę wyświetlaną**w obszarze **uwagi**, wprowadź opis tego zaufania jednostki uzależnionej, a następnie kliknij przycisk **dalej**.
6. Na **wybierz zasad kontroli dostępu** strony, wybierz zasady, a następnie kliknij przycisk **dalej**.
7. Na **gotowy do dodawania zaufania** strony, przejrzyj ustawienia, a następnie kliknij przycisk **dalej** można zapisać swoje jednostki uzależnionej informacje zaufania.
8. Na **Zakończ** kliknij **Zamknij**, ta akcja automatycznie wyświetla **Edycja reguł oświadczeń** okno dialogowe.
9. Wybierz **Dodaj regułę**.  
10. W **szablonu reguły w oświadczenie**, wybierz opcję **Wyślij atrybuty LDAP jako oświadczenia**.
11. Podaj **Nazwa reguły oświadczeń**. Dla **magazyn atrybutów**, wybierz opcję **usługi Active Directory wybierz**, Dodaj następujące oświadczeń, a następnie kliknij przycisk **Zakończ** i **OK**.

    | Atrybut LDAP | Typ oświadczenia wychodzącego |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Nazwisko | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |
    
12.  Oparte na typu certyfikatu, konieczne może być ustawiona algorytmu wyznaczania wartości skrótu. Na jednostki uzależnionej strony zaufania (B2C pokaz) oknie właściwości wybierz **zaawansowane** kartę i zmień **skrótu Secure hash algorithm** do `SHA-256`i kliknij przycisk **Ok**.  
13. W Menedżerze serwera wybierz **narzędzia**, a następnie wybierz pozycję **zarządzania usług AD FS**.
14. Wybierz zaufanie jednostki uzależnionej został utworzony, wybierz pozycję **aktualizacji z metadanych Federacji**, a następnie kliknij przycisk **aktualizacji**. 

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Za pomocą usługi Azure AD B2c odbywa się przy użyciu aplikacji utworzonej w dzierżawie. W tej sekcji przedstawiono kroki opcjonalne, które możesz wykonać, aby utworzyć aplikację testu, jeśli jeszcze tego nie zrobiłeś.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji, na przykład *testapp1*.
6. Dla **aplikacji sieci Web / interfejs API sieci Web**, wybierz opcję `Yes`, a następnie wprowadź `https://jwt.ms` dla **adres URL odpowiedzi**.
7. Kliknij pozycję **Utwórz**.

### <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i przetestować go jednostki uzależnionej ze stron

Należy zaktualizować plik innych firm (RP) jednostki uzależnionej, która inicjuje podróży użytkownika, który został utworzony.

1. Utwórz kopię *SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład, zmień jej nazwę na *SignUpSignInADFS.xml*.
2. Otwórz nowy plik i zaktualizuj wartość **PolicyId** atrybutu dla **elementu TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInADFS`.
3. Zaktualizuj wartość **PublicPolicyUri** o identyfikatorze URI zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Zaktualizuj wartość **ReferenceId** atrybutu w **DefaultUserJourney** aby dopasować identyfikator nowego podróży użytkownika utworzony (SignUpSignInADFS).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że zaznaczone jest aplikacja usługi Azure AD B2C, który został utworzony w **wybierz aplikację** pola, a następnie przetestuj go, klikając **Uruchom teraz**.

