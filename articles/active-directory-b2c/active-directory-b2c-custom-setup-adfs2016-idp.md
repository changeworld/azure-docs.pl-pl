---
title: Dodawanie usług AD FS jako dostawcy tożsamości SAML, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Konfigurowanie usług AD FS 2016 przy użyciu protokołu SAML i zasad niestandardowych w usłudze Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669230"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie usług AD FS jako dostawcy tożsamości SAML, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie dla konta użytkownika usługi AD FS za pomocą [zasady niestandardowe](active-directory-b2c-overview-custom.md) w usłudze Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Dodaj klucz aplikacji konto usług AD FS do usługi Azure AD B2C

Federacja z konta usług AD FS wymaga klucz tajny klienta konta relacji zaufania usługi Azure AD B2C w imieniu aplikacji. Musisz zapisać certyfikat usług AD FS w swojej dzierżawie usługi Azure AD B2C. 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz **Przełącz katalog**, a następnie wybierz katalog, który zawiera dzierżawy został utworzony. W tym samouczku *contoso* używany katalog zawiera w dzierżawie o nazwie *contoso0522Tenant.onmicrosoft.com*.

    ![Przełączanie katalogów](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**. Teraz powinien być używany dzierżawy.
4. Na stronie Przegląd wybierz **struktura środowiska tożsamości**.
5. Wybierz **klucze zasad** wyświetlić klucze, które są dostępne w Twojej dzierżawie, a następnie kliknij przycisk **Dodaj**.
6. Wybierz **przekazywanie** jako opcję.
7. Wprowadź `ADFSSamlCert` dla nazwy. Prefiks `B2C_1A_` mogą być dodawane automatycznie.
8. Przejdź do, a następnie wybierz swój plik PFX certyfikatu z kluczem prywatnym. Ten certyfikat z kluczem prywatnym powinna być taka sama, który został wystawiony i używane dla jednostki uzależnionej usług AD FS.
9. Kliknij przycisk **Utwórz** i upewnij się, że utworzono `B2C_1A_ADFSSamlCert` klucza.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Dodawanie dostawcy oświadczeń w zasadach rozszerzenia

Użytkownikom na logowanie przy użyciu konta z usług AD FS, należy zdefiniować konto jako dostawcy oświadczeń. Można to zrobić, określając punkt końcowy, który komunikuje się usługi Azure AD B2C. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj usług AD FS jako dostawcy oświadczeń, dodając **ClaimsProvider** elementu w pliku zasad rozszerzenia.

1. Otwórz *TrustFrameworkExtensions.xml* zasad plików w katalogu roboczym. Jeśli potrzebujesz edytora XML [spróbuj programu Visual Studio Code](https://code.visualstudio.com/download), który jest uproszczony edytor dla wielu platform.
2. Dodaj następujący kod XML w obszarze **ClaimsProviders** i Zastąp ciąg **Twojej domeny usługi AD FS** z Twoją domeną usługi AD FS nazwę, a następnie zastąp wartość **identityProvider** dane wyjściowe oświadczenia za pomocą usługi DNS (dowolną wartością, którą wskazuje domenę) i Zapisz plik. 

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
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

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>Zarejestruj dostawcę oświadczeń dla rejestracji i logowania

Aby udostępnić na stronach rejestracji i logowania dostawcy tożsamości konta usługi AD FS, należy dodać go do Twojego **SignUpOrSignIn** podróży użytkownika. 

Utwórz kopię istniejących podróży użytkownika szablonu, a następnie zmodyfikować go, aby obejmowała dostawcy usług AD FS tożsamości:

>[!NOTE]
>Jeśli został wcześniej skopiowany **podróży użytkowników** elementu z pliku podstawowego zasad do pliku rozszerzenie (*TrustFrameworkExtensions.xml*) można pominąć tę sekcję.

1. Otwórz plik podstawowy zasad. Na przykład *TrustFrameworkBase.xml*.
2. Kopiuj całą zawartość **podróży użytkowników** elementu.
3. Otwórz plik rozszerzenia (*TrustFrameworkExtensions.xml*) i Wklej całą zawartość **podróży użytkowników** element, który został skopiowany w pliku rozszerzenia.

### <a name="display-the-button"></a>Wyświetlanie przycisku

**ClaimsProviderSelections** element definiuje listę opcji dostawcy oświadczeń i ich kolejność.  **Elemencie ClaimsProviderSelection** element jest odpowiednikiem przycisk dostawcy tożsamości, na stronie rejestracji i logowania. Jeśli dodasz **elemencie ClaimsProviderSelection** element przycisku nowego konta usług AD FS jest wyświetlane, gdy użytkownik zobaczy strony. Aby dodać ten element:

1. W **UserJourney** elementu z identyfikatorem `SignUpOrSignIn` w podróży użytkownika, które zostały skopiowane, zlokalizuj **OrchestrationStep** elementu `Order="1"`.
2. Dodaj następujące **elemencie ClaimsProviderSelection** pod **ClaimsProviderSelections** elementu:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się przy użyciu konta usług AD FS, aby odebrać token. Łącze przycisku do akcji, łącząc profilu technicznego dla dostawcy oświadczeń konto usług AD FS:

1. Znajdź **OrchestrationStep** z `Order="2"` w obszarze **UserJourney** elementu.
2. Dodaj następujące **elementu ClaimsExchange** pod **ClaimsExchanges** elementu:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * Upewnij się, że `Id` ma taką samą wartość jak `TargetClaimsExchangeId` w poprzedniej sekcji.
> * Upewnij się, że `TechnicalProfileReferenceId` jest ustawiony do profilu technicznego utworzonego wcześniej (Contoso-SAML2).


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[Opcjonalnie] Zarejestruj dostawcę oświadczeń do edycji profilu

Można również dodać dostawcę tożsamości konta usługi AD FS do swoją podróż po użytkownik edycji profilu.

### <a name="display-the-button"></a>Wyświetlanie przycisku

1. Otwórz plik rozszerzenia zasad. Na przykład *TrustFrameworkExtensions.xml*.
2. W **UserJourney** elementu o identyfikatorze odpowiadającym `ProfileEdit` w podróży użytkownika, które zostały skopiowane, zlokalizuj **OrchestrationStep** elementu `Order="1"`.
3. Dodaj następujące **elemencie ClaimsProviderSelection** pod **ClaimsProviderSelections** elementu:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

1. Znajdź **OrchestrationStep** z `Order="2"` w obszarze **UserJourney** elementu.
2. Dodaj następujące **elementu ClaimsExchange** pod **ClaimsExchanges** elementu:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>Przekazywanie zasad dla Twojej dzierżawy

1. W witrynie Azure portal wybierz **wszystkie zasady**.
2. Wybierz **przekazywać zasady**.
3. Włącz **Zastąp zasady Jeśli istnieje**.
4. Wyszukaj i wybierz swoje *TrustFrameworkExtensions.xml* pliku zasad, a następnie wybierz **przekazywanie**. Upewnij się, że weryfikacja zakończyła się powodzeniem.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurowanie usług AD FS jednostki uzależnionej relacja zaufania

Aby korzystać z usług AD FS jako dostawcy tożsamości w usłudze Azure AD B2C, musisz utworzyć usług AD FS zaufania jednostki uzależnionej przy użyciu metadanych SAML usługi Azure AD B2C. Poniższy przykład przedstawia adresu URL metadanych SAML profilu technicznego usługi Azure AD B2C:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Zastąp następujące wartości:

- **Twojej dzierżawy** nazwą dzierżawy, takich jak usługi tenant.onmicrosoft.com.
- **Usługi zasad** nazwą zasady. Użyj zasad, w którym można skonfigurować profil techniczny SAML dostawcy lub zasadę, która dziedziczy tych zasad.
- **Twój profil techniczny** o nazwie tha profilu technicznego dostawcy tożsamości SAML.
 
Otwórz przeglądarkę i przejdź do adresu URL. Upewnij się, wpisz poprawny adres URL i czy masz dostęp do pliku metadanych XML.

Aby dodać nowe zaufanie jednostki uzależnionej przy użyciu przystawki Zarządzanie usługi AD FS i ręcznie skonfigurować ustawienia, należy wykonać poniższą procedurę na serwerze federacyjnym. Członkostwo w grupie **Administratorzy** lub równoważnej na komputerze lokalnym jest minimalnym warunkiem potrzebnym do ukończenia tej procedury. Przejrzyj szczegóły dotyczące stosowania odpowiednich kont i członkostwa w grupach [lokalne i domenowe grupy domyślne](http://go.microsoft.com/fwlink/?LinkId=83477).

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

    ![Ustawianie właściwości reguły](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  Oparte na typu certyfikatu, konieczne może być ustawiona algorytmu wyznaczania wartości skrótu. Na jednostki uzależnionej strony zaufania (B2C pokaz) oknie właściwości wybierz **zaawansowane** kartę i zmień **skrótu Secure hash algorithm** do `SHA-1` lub `SHA-256`i kliknij przycisk **Ok**.  

### <a name="update-the-relying-party-metadata"></a>Zaktualizuj metadane jednostki uzależnionej ze stron

Profil techniczny SAML zmiana wymaga aktualizacji usług AD FS za pomocą wersji zaktualizowanymi metadanymi. Nie trzeba zaktualizować metadanych podczas tworzenia aplikacji jednostki uzależnionej, ale po wprowadzeniu zmiany, zaktualizuj metadane w usługach AD FS.

1. W Menedżerze serwera wybierz **narzędzia**, a następnie wybierz pozycję **zarządzania usług AD FS**.
2. Wybierz zaufanie jednostki uzależnionej został utworzony, wybierz pozycję **aktualizacji z metadanych Federacji**, a następnie kliknij przycisk **aktualizacji**. 

### <a name="test-the-policy-by-using-run-now"></a>Testowanie zasad za pomocą polecenia Uruchom teraz

1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości**.
2.  Otwórz **B2C_1A_ProfileEdit**, jednostki uzależnionej strona (RP) zasad niestandardowych, który został przekazany. Wybierz **Uruchom teraz**. Powinien móc logować się za pomocą konta usług AD FS.

## <a name="download-the-complete-policy-files"></a>Pobierz pliki pełną zasad

Opcjonalnie: Możesz tworzyć scenariusz przy użyciu plików zasad niestandardowych po wykonaniu tych kroków w [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md). Na przykład Zobacz pliki, [pliki przykładowe zasady tylko do celów referencyjnych](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app).
