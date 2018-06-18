---
title: Dodaj usługi AD FS jako dostawca tożsamości SAML za pomocą niestandardowych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Artykule na temat konfigurowania usług AD FS 2016 przy użyciu protokołu SAML i zasad niestandardowych
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 06b70e48398fd720b6c87cc4575b7a7973f5bce5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709193"
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Usługa Azure Active Directory B2C: Dodawanie usług AD FS jako dostawca tożsamości SAML za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono sposób włączenia logowania użytkowników z usług AD FS konta za pośrednictwem [niestandardowych zasad](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

Kroki te obejmują:

1.  Tworzenie usług AD FS jednostki uzależnionej zaufania.
2.  Trwa dodawanie certyfikatu usługi AD FS zaufania jednostki uzależnionej do usługi Azure AD B2C.
3.  Dodawanie dostawcy oświadczeń dla zasad.
4.  Rejestrowanie usług AD FS konta oświadczeń dostawcy przebieg użytkownika.
5.  Przekazywanie zasady do usługi Azure AD B2C dzierżawy i przetestować go.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Aby utworzyć oświadczeń zaufania jednostki uzależnionej

Aby używać usług AD FS jako dostawca tożsamości w usłudze Azure Active Directory (Azure AD) B2C, należy do tworzenia usług AD FS zaufania jednostki uzależnionej dostarczyć prawo parametrów.

Aby dodać nowe zaufanie jednostki uzależnionej przy użyciu przystawki Zarządzanie usługami AD FS i należy ręcznie skonfigurować ustawienia, należy wykonać następującą procedurę na serwerze federacyjnym.

Członkostwo w grupie **Administratorzy**, lub równoważnej na komputerze lokalnym jest minimalnym wymaganiem do wykonania tej procedury. Szczegółowe informacje na temat używania odpowiednich kont i członkostwa w grupach [grupy domyślne w domenie i lokalne](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  W Menedżerze serwera kliknij **narzędzia**, a następnie wybierz **zarządzania usług AD FS**.

2.  Polecenie **Dodawanie zaufania jednostki uzależnionej**.
    ![Dodawanie zaufania jednostki uzależnionej](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  Na **powitalnej** wybierz pozycję **oświadczeń pamiętać** i kliknij przycisk **Start**.
    ![Na stronie powitalnej wybierz pamiętać oświadczeń](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  Na **wybierz źródło danych** kliknij przycisk **ręcznie wprowadź dane jednostki uzależnionej**, a następnie kliknij przycisk **dalej**.
    ![Wprowadź dane jednostki uzależnionej](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  Na **Określanie nazwy wyświetlanej** wpisz nazwę w **Nazwa wyświetlana**w obszarze **uwagi** wpisz opis tego zaufania jednostki uzależnionej, a następnie kliknij przycisk **dalej** .
    ![Określ nazwę wyświetlaną i uwagi](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Opcjonalny. Jeśli certyfikat szyfrowania tokenu opcjonalne następnie na **Konfigurowanie certyfikatu** kliknij przycisk **Przeglądaj** zlokalizuj plik certyfikatu, a następnie kliknij przycisk **dalej**.
    ![Konfigurowanie certyfikatu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  Na **Konfigurowanie adresu URL** wybierz pozycję **Włącz obsługę protokołu SAML 2.0 WebSSO** pole wyboru. W obszarze **URL usługi logowania jednokrotnego SAML 2.0 jednostki uzależnionej w strona**, wpisz adres URL punktu końcowego usługi Security (Assertion Markup Language SAML) dla tego zaufania jednostki uzależnionej, a następnie kliknij przycisk **dalej**.  Aby uzyskać **URL usługi logowania jednokrotnego SAML 2.0 jednostki uzależnionej w strona**, Wklej `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. Zamień na nazwę swojej dzierżawy (na przykład contosob2c.onmicrosoft.com) {dzierżawa} i {zasad} Zamień na nazwę zasady rozszerzenia (na przykład B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >Nazwa zasady jest dziedziczący signup_or_signin zasady, w tym przypadku jest: `B2C_1A_TrustFrameworkExtensions`.
    >Na przykład adres URL może być: https://login.microsoftonline.com/te/ **contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Jednostki uzależnionej adres URL strony logowania jednokrotnego SAML 2.0 usługi](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. Na **skonfiguruj identyfikatory** Określ ten sam adres URL co w poprzednim kroku, kliknij przycisk **Dodaj** dodać je do listy, a następnie kliknij przycisk **dalej**.
    ![Jednostki uzależnionej identyfikatorów zaufania](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  Na **wybierz zasady kontroli dostępu** wybierz zasady, a następnie kliknij przycisk **dalej**.
    ![Wybierz zasady kontroli dostępu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  Na **gotowe do dodania zaufania** , przejrzyj ustawienia, a następnie kliknij przycisk **dalej** zapisać uzależnionej informacje o zaufaniu.
    ![Zapisz dane zaufania jednostki uzależnionej strony](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  Na **Zakończ** kliknij przycisk **Zamknij**, ta czynność spowoduje automatyczne wyświetlenie **Edycja reguł oświadczeń** okno dialogowe.
    ![Edycja reguł oświadczeń](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Kliknij przycisk **Dodaj regułę**.  
      ![Dodaj nową regułę](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  W **szablonu reguły oświadczeń**, wybierz pozycję **Wyślij atrybuty LDAP jako oświadczeń**.
    ![Wybierz Wyślij atrybuty LDAP jako oświadczeń szablonu reguł](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Podaj **nazwy reguły oświadczeń**. Dla **magazynu atrybutów** wybierz **usługi Active Directory wybierz** Dodaj następujące oświadczeń, a następnie kliknij przycisk **Zakończ** i **OK**.
    ![Właściwości zestawu reguł](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  W Menedżerze serwera wybierz **zaufania jednostek uzależnionych** , a następnie wybierz jednostkę uzależnioną zaufania utworzony i kliknij przycisk **właściwości**.
    ![Jednostki uzależnionej strony Edytuj właściwości](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  Okno Właściwości zaufania (pokaz B2C) jednostki uzależnionej strony kliknij jeden **podpisu** i kliknij polecenie **Dodaj**.  
    ![Podpis zestawu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Dodaj certyfikat podpisu (bez klucza prywatnego plik .cert).  
    ![Dodaj certyfikat podpisu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  W oknie Właściwości zaufania (pokaz B2C) jednostki uzależnionej strony kliknij **zaawansowane** karcie i zmień **skrótu Secure hash algorithm** do **SHA-1**, kliknij przycisk **Ok**.  
    ![Wartość skrótu secure hash algorithm SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Dodaj klucz aplikacji konto usług AD FS do usługi Azure AD B2C
Federacja z kontami usług AD FS wymaga klucz tajny klienta konta usług AD FS do relacji zaufania usługi Azure AD B2C w imieniu aplikacji. Należy do przechowywania certyfikatu usług AD FS w dzierżawie usługi Azure AD B2C. 

1.  Przejdź do dzierżawy usługi Azure AD B2C i wybierz **ustawieniami B2C** > **Framework obsługi tożsamości**
2.  Wybierz **zasad kluczy** Aby wyświetlić dostępne w Twojej dzierżawie kluczy.
3.  Kliknij przycisk **+ Dodaj**.
4.  Aby uzyskać **opcje**, użyj **przekazać**.
5.  Aby uzyskać **nazwa**, użyj `ADFSSamlCert`.  
    Prefiks `B2C_1A_` mogą być dodawane automatycznie.
6.  W przypadku przekazywania pliku ** wybierz plik .pfx certyfikatu z kluczem prywatnym. Uwaga: ten certyfikat (z kluczem prywatnym) powinna być taka sama, wydane i używane dla jednostki uzależnionej usług AD FS.
![Przekazywanie klucza zasad](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Kliknij przycisk **Utwórz**
8.  Upewnij się, że utworzono klucz `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Dodawanie dostawcy oświadczeń w zasadach rozszerzenia
Użytkownikom na logowanie się przy użyciu konta usług AD FS, należy zdefiniować konta usług AD FS jako dostawcy oświadczeń. Innymi słowy należy określić punkt końcowy, który komunikuje się usługi Azure AD B2C. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony.

Zdefiniuj usług AD FS jako dostawcy oświadczeń, dodając `<ClaimsProvider>` węzeł rozszerzenia pliku zasad:

1. Otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml) z katalogu roboczego. Jeśli potrzebujesz edytora XML [spróbuj Visual Studio Code](https://code.visualstudio.com/download), lekkie edytora i platform.
2. Znajdź `<ClaimsProviders>` sekcji
3. Dodaj następujący fragment kodu XML w obszarze `ClaimsProviders` elementu i zastępowanie `identityProvider` o systemie DNS (dowolną wartość, która wskazuje domenę) i Zapisz plik. 

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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Zarejestruj dostawcę oświadczeń konta usług AD FS do logowania się lub zaloguj się w podróży użytkownika
W tym momencie dostawcy tożsamości nie został skonfigurowany.  Jednak nie jest dostępna w żadnym ekrany konta-konta/logowania. Teraz należy dodać dostawcy tożsamości konta usług AD FS do użytkownika `SignUpOrSignIn` podróży użytkownika. Aby było to możliwe, utworzymy duplikatem istniejącej przebieg użytkownika szablonu.  Następnie możemy zmodyfikować go, uwzględniając dostawcy tożsamości usługi AD FS:

>[!NOTE]
>Jeśli został wcześniej skopiowany `<UserJourneys>` elementu z pliku podstawowego zasad do pliku rozszerzenie (TrustFrameworkExtensions.xml), możesz pominąć tę sekcję.

1.  Otwórz plik bazowy tej zasady (na przykład TrustFrameworkBase.xml).
2.  Znajdź `<UserJourneys>` element i skopiuj cała zawartość `<UserJourneys>` węzła.
3.  Otwórz plik rozszerzenia (na przykład TrustFrameworkExtensions.xml) i Znajdź `<UserJourneys>` elementu. Jeśli element nie istnieje, dodaj je.
4.  Wklej całą zawartość `<UserJournesy>` węzła, który został skopiowany jako element podrzędny `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Wyświetlany przycisk
`<ClaimsProviderSelections>` Element definiuje listę opcje wyboru dostawcy oświadczeń i ich kolejność.  `<ClaimsProviderSelection>` element jest odpowiednikiem przycisku dostawcy tożsamości na stronie konta-konta/logowania. Jeśli dodasz `<ClaimsProviderSelection>` elementu dla konta usług AD FS, nowy przycisk zostaną wyświetlone po wyładowuje użytkownika na stronie. Aby dodać ten element:

1.  Znajdź `<UserJourney>` węzła, który zawiera `Id="SignUpOrSignIn"` w podróży użytkownika, które zostały skopiowane.
2.  Zlokalizuj `<OrchestrationStep>` węzła, który zawiera `Order="1"`
3.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Połącz przycisku akcji

Teraz, gdy masz przycisku w miejscu, należy połączyć je z akcją. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z konta usług AD FS otrzymujących token. Połączyć przycisku akcji przez łączenie techniczne profilu dla dostawcy oświadczeń konta usług AD FS:

1.  Znajdź `<OrchestrationStep>` zawierającą `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Upewnij się, `Id` ma taką samą wartość jak `TargetClaimsExchangeId` w poprzedniej sekcji.
> * Upewnij się, `TechnicalProfileReferenceId` ustawiono techniczne profilu utworzonego wcześniej (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Przekaż zasady dla Twojej dzierżawy
1.  W [portalu Azure](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2.  Wybierz **Framework obsługi tożsamości**.
3.  Otwórz **wszystkie zasady** bloku.
4.  Wybierz **przekazywać zasady**.
5.  Sprawdź **zastąpić zasady, jeśli istnieje** pole.
6.  **Przekaż** TrustFrameworkExtensions.xml i upewnij się, że nie wystąpi niepowodzenie weryfikacji

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych przy użyciu Uruchom teraz
1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **Framework obsługi tożsamości**.
2.  Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej zasady niestandardowe strony (RP), który został przekazany. Wybierz **Uruchom teraz**.
3.  Można będzie zalogować się przy użyciu konta usług AD FS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Opcjonalnie] Zarejestruj dostawcę oświadczeń konta usług AD FS do edycji profilu użytkownika podróży
Można również dodać dostawcy tożsamości konta usług AD FS do użytkownika `ProfileEdit` podróży użytkownika. Aby było to możliwe, możemy Powtórz ostatnie dwa kroki:

### <a name="display-the-button"></a>Wyświetlany przycisk
1.  Otwórz plik rozszerzenia zasad (na przykład TrustFrameworkExtensions.xml).
2.  Znajdź `<UserJourney>` węzła, który zawiera `Id="ProfileEdit"` w podróży użytkownika, które zostały skopiowane.
3.  Zlokalizuj `<OrchestrationStep>` węzła, który zawiera `Order="1"`
4.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsProviderSelections>` węzła:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Połącz przycisku akcji
1.  Znajdź `<OrchestrationStep>` zawierającą `Order="2"` w `<UserJourney>` węzła.
2.  Dodaj następujący fragment kodu XML w obszarze `<ClaimsExchanges>` węzła:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testowanie zasad niestandardowych edycji profilu przy użyciu Uruchom teraz
1.  Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **Framework obsługi tożsamości**.
2.  Otwórz **B2C_1A_ProfileEdit**, jednostki uzależnionej zasady niestandardowe strony (RP), który został przekazany. Wybierz **Uruchom teraz**.
3.  Można będzie zalogować się przy użyciu konta usług AD FS.

## <a name="download-the-complete-policy-files"></a>Pobierz pliki pełną zasad
Opcjonalnie: Zaleca się tworzenie scenariusz przy użyciu własnych zasad niestandardowych, które przeprowadzenie pliki po zakończeniu wprowadzenie do zasad niestandardowych. [Pliki przykładowe zasady jedynie do celów referencyjnych](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
