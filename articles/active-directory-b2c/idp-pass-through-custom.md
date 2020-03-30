---
title: Przekazywanie tokenu dostępu za pośrednictwem zasad niestandardowych do aplikacji
titleSuffix: Azure AD B2C
description: Dowiedz się, jak przekazać token dostępu dla dostawców tożsamości OAuth 2.0 jako oświadczenie za pośrednictwem zasad niestandardowych do aplikacji w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5ef8f742914129d868152814d84d2112267c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187799"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Przekazywanie tokenu dostępu za pośrednictwem zasad niestandardowych do aplikacji w usłudze Azure Active Directory B2C

[Zasady niestandardowe](custom-policy-get-started.md) w usłudze Azure Active Directory B2C (Azure AD B2C) zapewniają użytkownikom aplikacji możliwość zarejestrowania się lub zalogowania się u dostawcy tożsamości. W takim przypadku usługa Azure AD B2C odbiera [token dostępu](tokens-overview.md) od dostawcy tożsamości. Usługa Azure AD B2C używa tego tokenu do pobierania informacji o użytkowniku. Dodaj typ oświadczenia i oświadczenie wyjściowe do zasad niestandardowych, aby przekazać token do aplikacji, które można zarejestrować w usłudze Azure AD B2C.

Usługa Azure AD B2C obsługuje przekazywanie tokenu dostępu dostawców tożsamości [OAuth 2.0](authorization-code-flow.md) i [OpenID Connect.](openid-connect.md) Dla wszystkich innych dostawców tożsamości oświadczenie jest zwracane puste.

## <a name="prerequisites"></a>Wymagania wstępne

* Zasady niestandardowe są konfigurowane za pomocą dostawcy tożsamości OAuth 2.0 lub OpenID Connect.

## <a name="add-the-claim-elements"></a>Dodawanie elementów oświadczenia

1. Otwórz plik *TrustframeworkExtensions.xml* i dodaj następujący element **ClaimType** z identyfikatorem `identityProviderAccessToken` elementu **ClaimsSchema:**

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

2. Dodaj **OutputClaim** element do **TechnicalProfile** elementu dla każdego dostawcy tożsamości OAuth 2.0, dla którego chcesz token dostępu. W poniższym przykładzie pokazano element dodany do profilu technicznego Facebooka:

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

3. Zapisz plik *TrustframeworkExtensions.xml.*
4. Otwórz plik zasad jednostki uzależnionej, taki jak *SignUpOrSignIn.xml,* i dodaj element **OutputClaim** do **profilu technicznego:**

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

Podczas testowania aplikacji w usłudze Azure AD B2C może być przydatne, `https://jwt.ms` aby token usługi Azure AD B2C został zwrócony, aby móc przeglądać oświadczenia w nim.

### <a name="upload-the-files"></a>Przekazywanie plików

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, klikając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **platformę środowiska tożsamości**.
5. Na stronie Zasady niestandardowe kliknij pozycję **Przekaż zasady**.
6. Wybierz **opcję Zastąp zasadę, jeśli istnieje,** a następnie wyszukaj i wybierz plik *TrustframeworkExtensions.xml.*
7. Wybierz **pozycję Przekaż**.
8. Powtórz kroki od 5 do 7 dla pliku jednostki uzależnionej, na przykład *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Uruchamianie zasad

1. Otwórz zmienione zasady. Na przykład *B2C_1A_signup_signin*.
2. W **przypadku aplikacji**wybierz aplikację, która została wcześniej zarejestrowana. Aby wyświetlić token w poniższym przykładzie, powinien zostać wyświetlony `https://jwt.ms`adres URL **odpowiedzi** .
3. Wybierz **pozycję Uruchom teraz**.

    Powinieneś zobaczyć coś podobnego do następującego przykładu:

    ![Zdekodowany token w jwt.ms z wyróżnionym blokiem idp_access_token](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tokenach w [odwołaniu do tokenu B2C usługi Azure Active Directory.](tokens-overview.md)
