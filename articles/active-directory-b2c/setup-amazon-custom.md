---
title: Skonfiguruj polecenie logowania za pomocą konta Amazon, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Konfigurowanie logowania za pomocą konta Amazon w usłudze Azure Active Directory B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f62238543f1c24d05702ee0679610934d308538d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360388"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>Skonfiguruj polecenie logowania za pomocą konta Amazon, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowania dla użytkowników z konta Amazon, używając [zasady niestandardowe](active-directory-b2c-overview-custom.md) w usłudze Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md).
- Jeśli nie masz jeszcze konta Amazon, utworzyć w [ https://www.amazon.com/ ](https://www.amazon.com/).

## <a name="register-the-application"></a>Rejestrowanie aplikacji

Aby włączyć logowania dla użytkowników z konta Amazon, musisz utworzyć aplikację Amazon.

1. Zaloguj się do [Centrum deweloperów Amazon](https://login.amazon.com/) przy użyciu poświadczeń konta Amazon.
2. Jeśli jeszcze tego nie zrobiono, kliknij przycisk **Zarejestruj**, postępuj zgodnie z instrukcjami rejestracji dla deweloperów i zaakceptować zasady.
3. Wybierz **zarejestrować nową aplikację**.
4. Wprowadź **nazwa**, **opis**, i **adres URL zasad zachowania powiadomienie**, a następnie kliknij przycisk **Zapisz**. Powiadomienie dotyczące prywatności jest strona, którą zarządzasz, która zawiera informacje o ochronie prywatności dla użytkowników.
5. W **ustawień sieci Web** sekcji, skopiuj wartości z **identyfikator klienta**. Wybierz **wyświetlić wpis tajny** można pobrać klucza tajnego klienta, a następnie skopiuj go. Należy dysponować je, aby skonfigurować konto usługi Amazon jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
6. W **ustawień sieci Web** zaznacz **Edytuj**, a następnie wprowadź `https://your-tenant-name.b2clogin.com` w **dozwolone źródła JavaScript** i `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **dozwolone Zwraca adresy URL**. Zastąp `your-tenant-name` nazwą Twojej dzierżawy. Podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C, należy używać małych liter.
7. Kliknij pozycję **Zapisz**.

## <a name="create-a-policy-key"></a>Utwórz klucz zasad

Chcesz przechować klucz tajny klienta, który wcześniej zapisaną w dzierżawie usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz **struktura środowiska tożsamości — wersja ZAPOZNAWCZA**.
5. Wybierz **klucze zasad** , a następnie wybierz **Dodaj**.
6. Aby uzyskać **opcje**, wybierz `Manual`.
7. Wprowadź **nazwa** klucza zasad. Na przykład `AmazonSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **klucz tajny**, wprowadź klucz tajny klienta, który wcześniej zarejestrowane.
9. Aby uzyskać **użycie klucza**, wybierz opcję `Signature`.
10. Kliknij pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Użytkownikom na logowanie za pomocą konta Amazon, należy zdefiniować konto jako dostawcy oświadczeń, które usługi Azure AD B2C mogą się komunikować za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik jest uwierzytelniony. 

Konto Amazon można zdefiniować jako dostawcy oświadczeń, przez dodanie jej do **ClaimsProviders** elementu w pliku rozszerzenie zasad.


1. Otwórz *TrustFrameworkExtensions.xml*.
2. Znajdź **ClaimsProviders** elementu. Jeśli nie istnieje, należy dodać go pod elementem głównym.
3. Dodaj nową **ClaimsProvider** w następujący sposób:  

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ustaw **client_id** do Identyfikatora aplikacji z rejestracji aplikacji.
5. Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia o weryfikację

W razie skonfigurowano zasady tak, aby usługa Azure AD B2C wie, jak komunikować się z katalogiem usługi Azure AD. Spróbuj przekazać plik rozszerzenia swoich zasad, aby potwierdzić, że wszystkie problemy nie ma do tej pory.

1. Na **zasady niestandardowe** strony w swojej dzierżawie usługi Azure AD B2C, wybierz opcję **zasady przekazywania**.
2. Włącz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustFrameworkExtensions.xml* pliku.
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie skonfigurowano dostawcy tożsamości, ale nie jest dostępny w wszystkich ekranów konta-dokonywania/logowania. Aby udostępnić ją, utworzenie duplikatu istniejącego podróży użytkownika szablonu, a następnie zmodyfikuj go tak, aby w nim również Amazon dostawcy tożsamości.

1. Otwórz *TrustFrameworkBase.xml* plik z pakietu startowego.
2. Znajdź i skopiuj cała zawartość **UserJourney** element, który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *TrustFrameworkExtensions.xml* i Znajdź **podróży użytkowników** elementu. Jeśli element nie istnieje, dodać.
4. Wklej całą zawartość **UserJourney** element, który został skopiowany jako element podrzędny elementu **podróży użytkowników** elementu.
5. Zmień nazwę identyfikator podróży użytkownika. Na przykład `SignUpSignInAmazon`.

### <a name="display-the-button"></a>Wyświetlanie przycisku

**Elemencie ClaimsProviderSelection** element jest odpowiednikiem przycisk dostawcy tożsamości na ekranie konta-dokonywania/logowania. Jeśli dodasz **elemencie ClaimsProviderSelection** elementu konta Amazon, nowy przycisk pojawia się po użytkownik wyładowuje na stronie.

1. Znajdź **OrchestrationStep** element, który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelects**, Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** do odpowiedniej wartości, na przykład `AmazonExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk akcji

Teraz, gdy przycisk w miejscu, należy połączyć akcji. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się za pomocą konta Amazon do odbierania token.

1. Znajdź **OrchestrationStep** zawierającej `Order="2"` w podróży użytkownika.
2. Dodaj następujący kod **elementu ClaimsExchange** upewniając się, użyj tej samej wartości dla Identyfikatora, który był używany przez element **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```
    
    Zaktualizuj wartość **TechnicalProfileReferenceId** identyfikator profilu technicznego została utworzona wcześniej. Na przykład `Amazon-OAuth`.

3. Zapisz *TrustFrameworkExtensions.xml* plik i ponownie przekazać go do weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Za pomocą usługi Azure AD B2c odbywa się przy użyciu aplikacji utworzonej w dzierżawie. W tej sekcji przedstawiono kroki opcjonalne, które możesz wykonać, aby utworzyć aplikację testu, jeśli jeszcze tego nie zrobiłeś.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji, na przykład *testapp1*.
6. Dla **aplikacji sieci Web / interfejs API sieci Web**, wybierz opcję `Yes`, a następnie wprowadź `https://jwt.ms` dla **adres URL odpowiedzi**.
7. Kliknij pozycję **Utwórz**.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i przetestować go jednostki uzależnionej ze stron

Należy zaktualizować plik innych firm (RP) jednostki uzależnionej, która inicjuje podróży użytkownika, który został utworzony.

1. Utwórz kopię *SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład, zmień jej nazwę na *SignUpSignInAmazon.xml*.
2. Otwórz nowy plik i zaktualizuj wartość **PolicyId** atrybutu dla **elementu TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInAmazon`.
3. Zaktualizuj wartość **PublicPolicyUri** o identyfikatorze URI zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_amazon`
4. Zaktualizuj wartość **ReferenceId** atrybutu w **DefaultUserJourney** aby dopasować identyfikator nowego podróży użytkownika utworzony (SignUpSignAmazon).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że zaznaczone jest aplikacja usługi Azure AD B2C, który został utworzony w **wybierz aplikację** pola, a następnie przetestuj go, klikając **Uruchom teraz**.
