---
title: Przekaż token dostępu za pomocą zasad niestandardowych dla aplikacji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przekazać tokenu dostępu dla dostawców tożsamości OAuth 2.0 jako oświadczenia za pomocą niestandardowych zasad do aplikacji w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 784387b119bff6445015419adfd3bc0e52eee43f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397201"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Przekaż token dostępu za pomocą zasad niestandardowych dla aplikacji w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A [zasad niestandardowych](active-directory-b2c-get-started-custom.md) w usłudze Azure Active Directory (Azure AD) B2C umożliwia użytkownikom aplikacji Zarejestruj się lub zaloguj się przy użyciu dostawcy tożsamości. W takiej sytuacji usługa Azure AD B2C odbiera [token dostępu](active-directory-b2c-reference-tokens.md) od dostawcy tożsamości. Usługa Azure AD B2C używa tego tokenu, można pobrać informacji o użytkowniku. Możesz dodać typ oświadczenia i dane wyjściowe oświadczenia do niestandardowych zasad do przekazania tokenu za pośrednictwem aplikacji, które należy zarejestrować w usłudze Azure AD B2C. 

Usługa Azure AD B2C obsługuje przekazywanie tokenu dostępu [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) i [OpenID Connect](active-directory-b2c-reference-oidc.md) dostawców tożsamości. W przypadku innych dostawców tożsamości oświadczenie zwracany jest pusty.

## <a name="prerequisites"></a>Wymagania wstępne

- Niestandardowe zasady jest skonfigurowany za pomocą protokołu OAuth 2.0 lub OpenID Connect dostawcy tożsamości.

## <a name="add-the-claim-elements"></a>Dodawanie elementów oświadczeń 

1. Otwórz swoje *TrustframeworkExtensions.xml* pliku i Dodaj następujący kod **oświadczenia** elementu z identyfikatorem `identityProviderAccessToken` do **ClaimsSchema** elementu:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Dodaj **oświadczenie OutputClaim** elementu **profilu technicznego** elementu dla każdego dostawcy tożsamości OAuth 2.0, które chcieliby token dostępu. Poniższy przykład pokazuje element dodawane do profilu technicznego Facebook:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Zapisz *TrustframeworkExtensions.xml* pliku.
4. Otwórz pliku jednostki uzależnionej zasad firmy, takich jak *SignUpOrSignIn.xml*i Dodaj **oświadczenie OutputClaim** elementu **profilu technicznego**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Zapisz plik zasad.

## <a name="test-your-policy"></a>Testowanie zasad

Podczas testowania aplikacji w usłudze Azure AD B2C, może być przydatne do ma tokenu usługi Azure AD B2C, powrót do `https://jwt.ms` aby mieć możliwość przejrzenia oświadczeń w nim.

### <a name="upload-the-files"></a>Przekaż pliki

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **struktura środowiska tożsamości**.
5. Na stronie zasad niestandardowych kliknij **zasady przekazywania**.
6. Wybierz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustframeworkExtensions.xml* pliku.
7. Kliknij pozycję **Przekaż**.
8. Powtórz kroki od 5 do 7 dla jednostki uzależnionej pliku innych firm, takich jak *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Uruchom zasady

1. Otwórz zasady, który został zmodyfikowany. Na przykład *B2C_1A_signup_signin*.
2. Aby uzyskać **aplikacji**, wybierz swoją aplikację, która została wcześniej zarejestrowana. Aby wyświetlić token w poniższym przykładzie **adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
3. Kliknij pozycję **Uruchom teraz**.

    Powinien zostać wyświetlony podobny do poniższego przykładu:

    ![Token zdekodowany](./media/idp-pass-through-custom/idp-pass-through-custom-token.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o tokenów w [odwołania do tokenu usługi Azure Active Directory](active-directory-b2c-reference-tokens.md).





