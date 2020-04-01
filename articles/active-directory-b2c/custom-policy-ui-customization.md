---
title: Dostosowywanie interfejsu użytkownika aplikacji za pomocą zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o dostosowywaniu interfejsu użytkownika przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e50d6d0623e87dfa68a7cc9744c3f595ff0179c6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396380"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wykonując czynności opisane w tym artykule, tworzysz niestandardowe zasady rejestracji i logowania z marką i wyglądem. Dzięki usłudze Azure Active Directory B2C (Azure AD B2C) uzyskasz prawie pełną kontrolę nad zawartością HTML i CSS, która jest prezentowana użytkownikom. Korzystając z zasad niestandardowych, można skonfigurować dostosowywanie interfejsu użytkownika w języku XML zamiast używać formantów w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md). Aby zarejestrować się i zalogować za pomocą kont lokalnych, powinny być dostępne robocze zasady niestandardowe.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Modyfikowanie pliku rozszerzeń

Aby skonfigurować dostosowywanie interfejsu użytkownika, skopiuj **element ContentDefinition** i jego elementy podrzędne z pliku podstawowego do pliku rozszerzeń.

1. Otwórz plik podstawowy zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>. Ten plik podstawowy jest jednym z plików zasad zawartych w pakiecie startowym zasad niestandardowych, który powinien zostać uzyskany w wymagania wstępne, [Wprowadzenie do zasad niestandardowych](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Wyszukaj i skopiuj całą zawartość elementu **ContentDefinitions.**
1. Otwórz plik rozszerzenia. Na przykład *TrustFrameworkExtensions.xml*. Wyszukaj **element BuildingBlocks.** Jeśli element nie istnieje, dodaj go.
1. Wklej całą zawartość **ContentDefinitions** element, który został skopiowany jako element podrzędny **BuildingBlocks** elementu.
1. Wyszukaj element **ContentDefinition,** który zawiera `Id="api.signuporsignin"` skopiowany kod XML.
1. Zmień wartość **LoadUri** na adres URL pliku HTML, który został przekazany do magazynu. Na przykład `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Zasady niestandardowe powinny wyglądać następująco:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Zapisz plik rozszerzeń.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Przesyłaj i testuj zaktualizowane zasady niestandardowe

#### <a name="51-upload-the-custom-policy"></a>5.1 Przesyłanie zasad niestandardowych

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wyszukaj i wybierz **pozycję Azure AD B2C**.
1. W obszarze **Zasady**wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Przekaż zasady niestandardowe**.
1. Przekaż plik rozszerzeń, który został wcześniej zmieniony.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Przetestuj zasady niestandardowe za pomocą **funkcji Uruchom teraz**

1. Wybierz przekazane zasady, a następnie wybierz pozycję **Uruchom teraz**.
1. Powinieneś być w stanie zarejestrować się przy użyciu adresu e-mail.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurowanie identyfikatora URI zawartości strony niestandardowej dynamicznej

Za pomocą zasad niestandardowych usługi Azure AD B2C, można wysłać parametr w ścieżce adresu URL lub ciąg kwerendy. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Parametrem może być dowolny [program rozpoznawania oświadczeń,](claim-resolver-overview.md)taki jak identyfikator aplikacji, identyfikator języka `campaignId`lub niestandardowy parametr ciągu zapytania, taki jak .

### <a name="sending-query-string-parameters"></a>Wysyłanie parametrów ciągu kwerendy

Aby wysłać parametry ciągu zapytania, w [zasadach jednostki uzależnianej](relyingparty.md) `ContentDefinitionParameters` dodaj element, jak pokazano poniżej.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

W definicji zawartości zmień `LoadUri` wartość `https://<app_name>.azurewebsites.net/home/unified`na . Zasady `ContentDefinition` niestandardowe powinny wyglądać następująco:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Gdy usługa Azure AD B2C ładuje stronę, wywołuje punkt końcowy serwera sieci web:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Identyfikator URI zawartości strony dynamicznej

Zawartość można pobierać z różnych miejsc na podstawie użytych parametrów. W punkcie końcowym obsługującym mechanizm CORS skonfiguruj strukturę folderów do hosta zawartości. Na przykład można zorganizować zawartość w następującej strukturze. *Folder/folder główny na język/pliki html*. Na przykład identyfikator URI strony niestandardowej może wyglądać następująco:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Usługa Azure AD B2C wysyła dwuliterowy `fr` kod ISO dla języka, dla języka francuskiego:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat elementów interfejsu użytkownika, które można dostosować, zobacz [przewodnik do dostosowywania interfejsu użytkownika dla przepływów użytkownika](customize-ui-overview.md).
