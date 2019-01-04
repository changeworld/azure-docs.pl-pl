---
title: Przekaż token dostępu za pomocą zasad niestandardowych dla aplikacji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przekazać tokenu dostępu dla dostawców tożsamości OAuth 2.0 jako oświadczenia za pomocą niestandardowych zasad do aplikacji w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7a40ded3ff6af1c519400c755d1ee6d9bd73ba9e
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602294"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Przekaż token dostępu za pomocą zasad niestandardowych dla aplikacji w usłudze Azure Active Directory B2C

> [!NOTE]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.

> [!Important]
> Ta funkcja publicznej wersji zapoznawczej jest tymczasowo niedostępna.

A [zasad niestandardowych](active-directory-b2c-get-started-custom.md) w usłudze Azure Active Directory (Azure AD) B2C umożliwia użytkownikom aplikacji Zarejestruj się lub zaloguj się przy użyciu dostawcy tożsamości. W takiej sytuacji usługa Azure AD B2C odbiera [token dostępu](active-directory-b2c-reference-tokens.md) od dostawcy tożsamości. Usługa Azure AD B2C używa tego tokenu, można pobrać informacji o użytkowniku. Możesz dodać typ oświadczenia i dane wyjściowe oświadczenia do niestandardowych zasad do przekazania tokenu za pośrednictwem aplikacji, które należy zarejestrować w usłudze Azure AD B2C. 

Usługa Azure AD B2C aktualnie obsługuje tylko przekazywanie tokenu dostępu [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) dostawców tożsamości, które obejmują usługi Facebook i [Google](active-directory-b2c-custom-setup-goog-idp.md). W przypadku innych dostawców tożsamości oświadczenie zwracany jest pusty.

## <a name="prerequisites"></a>Wymagania wstępne

- Niestandardowe zasady jest skonfigurowany z dostawcą tożsamości OAuth 2.0.

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
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
3. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Azure AD B2C**.
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





