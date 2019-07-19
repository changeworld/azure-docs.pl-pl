---
title: Przekaż token dostępu za pomocą zasad niestandardowych do aplikacji w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak przekazać token dostępu dla dostawców tożsamości OAuth 2.0 jako rolę w ramach zasad niestandardowych do aplikacji w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2253e9f6331662f0ead0251f9affd7996e02aa31
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846873"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Przekaż token dostępu za pomocą zasad niestandardowych do aplikacji w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

[Zasady niestandardowe](active-directory-b2c-get-started-custom.md) w Azure Active Directory (Azure AD) B2C zapewniają użytkownikom aplikacji możliwość rejestrowania się lub logowania za pomocą dostawcy tożsamości. W takim przypadku Azure AD B2C odbiera [token dostępu](active-directory-b2c-reference-tokens.md) od dostawcy tożsamości. Azure AD B2C używa tego tokenu do pobierania informacji o użytkowniku. Aby przekazać token do aplikacji rejestrowanych w Azure AD B2C, należy dodać do zasad niestandardowych typ i zastrzeżenie wyjściowe.

Azure AD B2C obsługuje przekazywanie tokenu dostępu dla dostawców tożsamości [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) i [OpenID Connect Connect](active-directory-b2c-reference-oidc.md) . W przypadku wszystkich innych dostawców tożsamości, zgłoszenie jest zwracane puste.

## <a name="prerequisites"></a>Wymagania wstępne

- Zasady niestandardowe są konfigurowane za pomocą dostawcy tożsamości protokołu OAuth 2,0 lub OpenID Connect Connect.

## <a name="add-the-claim-elements"></a>Dodaj elementy roszczeń

1. Otwórz plik *TrustframeworkExtensions. XML* i Dodaj następujący element **ClaimType** z identyfikatorem `identityProviderAccessToken` do elementu **ClaimsSchema** :

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

2. Dodaj element **oświadczenie outputclaim** do elementu **profilu technicznym** dla każdego dostawcy tożsamości OAuth 2,0, dla którego chcesz uzyskać token dostępu. Poniższy przykład pokazuje element dodany do profilu technicznego Facebook:

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

3. Zapisz plik *TrustframeworkExtensions. XML* .
4. Otwórz plik zasad jednostki uzależnionej, taki jak *SignUpOrSignIn. XML*, i Dodaj element **oświadczenie outputclaim** do **profilu technicznym**:

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

Podczas testowania aplikacji w Azure AD B2C może być przydatne, aby token `https://jwt.ms` Azure AD B2C był w stanie przejrzeć oświadczenia w nim.

### <a name="upload-the-files"></a>Przekaż pliki

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **platforma obsługi tożsamości**.
5. Na stronie zasady niestandardowe kliknij pozycję **Przekaż zasady**.
6. Wybierz opcję **Zastąp zasady, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustframeworkExtensions. XML* .
7. Kliknij pozycję **Przekaż**.
8. Powtórz kroki od 5 do 7 dla pliku jednostki uzależnionej, np. *SignUpOrSignIn. XML*.

### <a name="run-the-policy"></a>Uruchamianie zasad

1. Otwórz zasady, które zostały zmienione. Na przykład *B2C_1A_signup_signin*.
2. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token w poniższym przykładzie, **adres URL odpowiedzi** powinien być widoczny `https://jwt.ms`.
3. Kliknij pozycję **Uruchom teraz**.

    Powinieneś zobaczyć coś podobnego do poniższego przykładu:

    ![Zdekodowany token w jwt.ms z wyróżnionym blokiem idp_access_token](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tokenach w odniesieniu do [tokenów Azure Active Directory](active-directory-b2c-reference-tokens.md).
