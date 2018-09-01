---
title: Dodawanie usług AD FS jako dostawcy tożsamości SAML, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Instrukcje artykuł na temat konfigurowania usług AD FS 2016 przy użyciu protokołu SAML i zasady niestandardowe
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4380d21eded3f97e3b3107e78c9544a09d1b0bb2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338534"
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Usługa Azure Active Directory B2C: Dodawanie usług AD FS jako dostawcy tożsamości SAML, za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule dowiesz się, jak włączyć logowanie dla użytkowników z usług AD FS konta za pośrednictwem [zasady niestandardowe](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

Kroki te obejmują:

1.  Tworzenie usług AD FS jednostki uzależnionej relacja zaufania.
2.  Dodawanie certyfikatu usługi AD FS zaufania jednostki uzależnionej do usługi Azure AD B2C.
3.  Dodawanie dostawcy oświadczeń z zasadami.
4.  Konto, rejestrowania usług AD FS oświadczenia dostawcy podróży użytkownika.
5.  Przekazywanie zasad do usługi Azure AD B2C dzierżawy i przetestuj ją.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Aby utworzyć oświadczenia pamiętać zaufania jednostki uzależnionej

Aby korzystać z usług AD FS jako dostawcy tożsamości w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć usług AD FS zaufania jednostki uzależnionej i dostarczyć odpowiednie parametry.

Aby dodać nowe zaufanie jednostki uzależnionej przy użyciu przystawki zarządzania usługami AD FS i ręcznie skonfigurować ustawienia, należy wykonać poniższą procedurę na serwerze federacyjnym.

Członkostwo w grupie **Administratorzy**, lub równoważnej na komputerze lokalnym jest minimalnym warunkiem potrzebnym do ukończenia tej procedury. Przejrzyj szczegóły dotyczące stosowania odpowiednich kont i członkostwa w grupach [lokalne i domenowe grupy domyślne](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  W Menedżerze serwera kliknij **narzędzia**, a następnie wybierz pozycję **zarządzania usług AD FS**.

2.  Kliknij pozycję **Dodaj zaufanie jednostki uzależnionej**.
    ![Dodaj zaufanie jednostki uzależnionej](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  Na **powitalnej** wybierz **oświadczenia pamiętać** i kliknij przycisk **Start**.
    ![Na stronie powitalnej wybierz oświadczenia pamiętać](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  Na **wybierz źródło danych** kliknij **ręcznie wprowadź dane jednostki uzależnionej**, a następnie kliknij przycisk **dalej**.
    ![Wprowadzanie danych dotyczących jednostki uzależnionej](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  Na **Określ nazwę wyświetlaną** strony, wpisz nazwę w **nazwę wyświetlaną**w obszarze **uwagi** wpisz opis tego zaufania jednostki uzależnionej, a następnie kliknij przycisk **dalej** .
    ![Określ nazwę wyświetlaną i uwagi](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Opcjonalne. Jeśli certyfikat szyfrowania tokenu opcjonalne, następnie na **konfigurowania certyfikatu** kliknij **Przeglądaj** zlokalizuj plik certyfikatu, a następnie kliknij przycisk **dalej**.
    ![Konfigurowanie certyfikatu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  Na **Konfiguruj adres URL** wybierz opcję **włączyć obsługę protokołu SAML 2.0 websso** pole wyboru. W obszarze **URL usługi rejestracji Jednokrotnej 2.0 SAML strony Relying**, wpisz adres URL punktu końcowego usługi zabezpieczeń Assertion Markup Language (SAML) dla tego zaufania jednostki uzależnionej, a następnie kliknij przycisk **dalej**.  Aby uzyskać **URL usługi rejestracji Jednokrotnej 2.0 SAML strony Relying**, Wklej `https://(tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policy}`. Zamień na nazwę Twojej dzierżawy (na przykład contosob2c) {dzierżawa} i {zasady} Zamień na nazwę zasad rozszerzenia (na przykład B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >Nazwa zasad jest tą, która dziedziczy zasady signup_or_signin, w tym przypadku jest: `B2C_1A_TrustFrameworkExtensions`.
    >Na przykład może być adres URL: https://**contosob2c**.b2clogin.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Jednostki uzależnionej adres URL usługi rejestracji Jednokrotnej 2.0 SAML strony](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. Na **skonfiguruj identyfikatory** strony, należy określić ten sam adres URL co w poprzednim kroku, kliknij przycisk **Dodaj** dodać je do listy, a następnie kliknij przycisk **dalej**.
    ![Jednostki uzależnionej strony zaufania identyfikatorów](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  Na **wybierz zasad kontroli dostępu** wybierz zasadę, a następnie kliknij przycisk **dalej**.
    ![Wybierz zasad kontroli dostępu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  Na **gotowy do dodawania zaufania** strony, przejrzyj ustawienia, a następnie kliknij przycisk **dalej** można zapisać swoje jednostki uzależnionej informacje zaufania.
    ![Zapisz dane zaufania jednostki uzależnionej ze stron](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  Na **Zakończ** kliknij **Zamknij**, ta akcja automatycznie wyświetla **Edycja reguł oświadczeń** okno dialogowe.
    ![Edytuj reguły oświadczeń](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Kliknij przycisk **Dodaj regułę**.  
      ![Dodaj nową regułę](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  W **szablonu reguły w oświadczenie**, wybierz opcję **Wyślij atrybuty LDAP jako oświadczenia**.
    ![Wybierz opcję Wyślij atrybuty LDAP jako oświadczenia reguły szablonu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Podaj **Nazwa reguły oświadczeń**. Dla **magazyn atrybutów** wybierz **usługi Active Directory wybierz** Dodaj poniższe oświadczenia, a następnie kliknij przycisk **Zakończ** i **OK**.
    ![Ustawianie właściwości reguły](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  W Menedżerze serwera wybierz **zaufania jednostek uzależnionych** , a następnie wybierz jednostki uzależnionej strony zaufania, został utworzony i kliknij przycisk **właściwości**.
    ![Jednostki uzależnionej strona Edytuj właściwości](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  Jeden, kliknij w oknie dialogowym właściwości jednostki uzależnionej zaufania (B2C pokaz) innych firm **podpisu** kartę, a następnie kliknij przycisk **Dodaj**.  
    ![Ustaw podpis](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Dodaj certyfikat podpisu (bez klucza prywatnego .cert pliku).  
    ![Dodaj certyfikat podpisu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  W oknie dialogowym właściwości jednostki uzależnionej zaufania (B2C pokaz) innych firm kliknij **zaawansowane** kartę i zmień **skrótu Secure hash algorithm** do **SHA-1**, kliknij przycisk **Ok**.  
    ![Wartość skrótu secure hash algorithm SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Dodaj klucz aplikacji konto usług AD FS do usługi Azure AD B2C
Federacja z konta usług AD FS wymaga klucz tajny klienta konta usług AD FS do relacji zaufania usługi Azure AD B2C w imieniu aplikacji. Musisz zapisać certyfikat usług AD FS w swojej dzierżawie usługi Azure AD B2C. 

1.  Przejdź do dzierżawy usługi Azure AD B2C i wybierz **ustawieniami B2C** > **struktura środowiska tożsamości**
2.  Wybierz **klucze zasad** Aby wyświetlić klucze, które są dostępne w Twojej dzierżawie.
3.  Kliknij przycisk **+ Dodaj**.
4.  Aby uzyskać **opcje**, użyj **przekazywanie**.
5.  Aby uzyskać **nazwa**, użyj `ADFSSamlCert`.  
    Prefiks `B2C_1A_` mogą być dodawane automatycznie.
6.  W przypadku przekazywania pliku ** wybierz swój plik PFX certyfikatu z kluczem prywatnym. Uwaga: ten certyfikat (z kluczem prywatnym) powinna być taka sama, wystawiony i używane dla jednostki uzależnionej usług AD FS.
![Przekaż klucz zasad](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Kliknij przycisk **Utwórz**
8.  Upewnij się, że utworzono klucz `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Dodawanie dostawcy oświadczeń w zasadach rozszerzenia
Użytkownikom na logowanie przy użyciu konta usług AD FS, należy zdefiniować konto usług AD FS jako dostawcy oświadczeń. Innymi słowy należy określić punkt końcowy, który komunikuje się usługi Azure AD B2C. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj usług AD FS jako dostawcy oświadczeń, dodając `<ClaimsProvider>` węzła w pliku zasad rozszerzenia:

1. Otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego. Jeśli potrzebujesz edytora XML [spróbuj programu Visual Studio Code](https://code.visualstudio.com/download), lekki edytor dla wielu platform.
2. Znajdź `<ClaimsProviders>` sekcji
3. Dodaj następujący fragment kodu XML w obszarze `ClaimsProviders` i Zastąp ciąg `identityProvider` za pomocą usługi DNS (dowolną wartością, którą wskazuje domenę) i Zapisz plik. 

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
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Zarejestruj dostawcę oświadczeń konto usług AD FS do logowania się lub zaloguj się w podróży użytkownika
W tym momencie dostawcy tożsamości została prawidłowo skonfigurowana.  Jednak nie jest dostępna we wszystkich ekranów konta-dokonywania/logowania. Teraz należy dodać dostawcę tożsamości konta usługi AD FS do użytkownika `SignUpOrSignIn` podróży użytkownika. Aby udostępnić ją, możemy utworzyć duplikatem istniejącej podróży użytkownika szablonu.  Następnie zmodyfikujemy go uwzględniając dostawcy usług AD FS tożsamości:

>[!NOTE]
>Jeśli został wcześniej skopiowany `<UserJourneys>` elementu z pliku podstawowego zasad do pliku rozszerzenie (TrustFrameworkExtensions.xml), możesz pominąć tę sekcję.

1.  Otwórz plik podstawowy zasady (na przykład TrustFrameworkBase.xml).
2.  Znajdź `<UserJourneys>` elementu i skopiować całą zawartość `<UserJourneys>` węzła.
3.  Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodać.
4.  Wklej całą zawartość `<UserJournesy>` węzeł, który został skopiowany jako element podrzędny elementu `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Wyświetlanie przycisku
`<ClaimsProviderSelections>` Element definiuje listę opcji do wyboru dostawcy oświadczeń i ich kolejność.  `<ClaimsProviderSelection>` element jest odpowiednikiem przycisk dostawcy tożsamości, na stronie konta-dokonywania/logowania. Jeśli dodasz `<ClaimsProviderSelection>` elementu dla konta usług AD FS, nowy przycisk pojawia się po użytkownik wyładowuje na stronie. Aby dodać ten element:

1.  Znajdź `<UserJourney>` węzeł, który zawiera `Id="SignUpOrSignIn"` w podróży użytkownika, który został skopiowany.
2.  Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"`
3.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się przy użyciu konta usług AD FS, aby odebrać token. Łącze przycisku do akcji, łącząc profilu technicznego dla dostawcy oświadczeń konto usług AD FS:

1.  Znajdź `<OrchestrationStep>` zawierającej `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Upewnij się, `Id` ma taką samą wartość jak w przypadku `TargetClaimsExchangeId` w poprzedniej sekcji.
> * Upewnij się, `TechnicalProfileReferenceId` jest ustawiony do profilu technicznego utworzonego wcześniej (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Przekazywanie zasad dla Twojej dzierżawy
1.  W [witryny Azure portal](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2.  Wybierz **struktura środowiska tożsamości**.
3.  Otwórz **wszystkie zasady** bloku.
4.  Wybierz **przekazywać zasady**.
5.  Sprawdź **Zastąp zasady Jeśli istnieje** pole.
6.  **Przekaż** TrustFrameworkExtensions.xml i upewnij się, że nie wystąpi niepowodzenie weryfikacji

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz
1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości**.
2.  Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strona (RP) zasad niestandardowych, który został przekazany. Wybierz **Uruchom teraz**.
3.  Powinien móc logować się za pomocą konta usług AD FS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Opcjonalnie] Zarejestruj dostawcę oświadczeń konto usług AD FS do edytowania profilu podróży użytkownika
Warto również dodać dostawcę tożsamości konta usługi AD FS do użytkownika `ProfileEdit` podróży użytkownika. Aby udostępnić ją, firma Microsoft Powtórz ostatnie dwa kroki:

### <a name="display-the-button"></a>Wyświetlanie przycisku
1.  Otwórz plik rozszerzenia zasad (na przykład TrustFrameworkExtensions.xml).
2.  Znajdź `<UserJourney>` węzeł, który zawiera `Id="ProfileEdit"` w podróży użytkownika, który został skopiowany.
3.  Znajdź `<OrchestrationStep>` węzeł, który zawiera `Order="1"`
4.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji
1.  Znajdź `<OrchestrationStep>` zawierającej `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testowanie niestandardowe zasady edytowania profilu za pomocą polecenia Uruchom teraz
1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości**.
2.  Otwórz **B2C_1A_ProfileEdit**, jednostki uzależnionej strona (RP) zasad niestandardowych, który został przekazany. Wybierz **Uruchom teraz**.
3.  Powinien móc logować się za pomocą konta usług AD FS.

## <a name="download-the-complete-policy-files"></a>Pobierz pliki pełną zasad
Opcjonalnie: Zalecamy tworzenie scenariusza za pomocą własne zasady niestandardowe, które omówimy pliki po ukończeniu, wprowadzenie do zasad niestandardowych. [Pliki przykładowe zasady tylko do celów referencyjnych](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
