---
title: Konfigurowanie logowania do organizacji usługi Azure AD
titleSuffix: Azure AD B2C
description: Konfigurowanie logowania dla określonej organizacji usługi Azure Active Directory w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 35fc4e1d64fa7df392fa878db14c0464da7dccf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188311"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurowanie logowania dla określonej organizacji usługi Azure Active Directory w usłudze Azure Active Directory B2C

Aby użyć usługi Azure Active Directory (Azure AD) jako [dostawcy tożsamości](authorization-code-flow.md) w usłudze Azure AD B2C, należy utworzyć aplikację, która go reprezentuje. W tym artykule pokazano, jak włączyć logowanie dla użytkowników z określonej organizacji usługi Azure AD przy użyciu przepływu użytkownika w usłudze Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Tworzenie aplikacji usługi Azure AD

Aby włączyć logowanie dla użytkowników z określonej organizacji usługi Azure AD, należy zarejestrować aplikację w dzierżawie usługi Azure AD, która nie jest taka sama jak dzierżawa usługi Azure AD B2C.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD. Wybierz filtr **subskrypcja Katalogu +** w górnym menu i wybierz katalog zawierający dzierżawę usługi Azure AD. To nie jest ta sama dzierżawa co dzierżawa usługi Azure AD B2C.
3. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
4. Wybierz **pozycję Nowa rejestracja**.
5. Wprowadź nazwę aplikacji. Na przykład `Azure AD B2C App`.
6. Zaakceptuj wybór **kont w tym katalogu organizacyjnym tylko** dla tej aplikacji.
7. W przypadku **identyfikatora URI przekierowania**zaakceptuj wartość **sieci Web**i wprowadź `your-B2C-tenant-name` następujący adres URL we wszystkich małych literach, gdzie jest zastępowany nazwą dzierżawy usługi Azure AD B2C. Na przykład: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Wszystkie adresy URL powinny teraz używać [b2clogin.com](b2clogin.md).

8. Kliknij **pozycję Zarejestruj**. Skopiuj **identyfikator aplikacji (klienta),** który ma być używany później.
9. Wybierz **pozycję Certyfikaty & wpisów tajnych** w menu aplikacji, a następnie wybierz pozycję **Nowy klucz tajny klienta**.
10. Wprowadź nazwę klucza tajnego klienta. Na przykład `Azure AD B2C App Secret`.
11. Wybierz okres wygaśnięcia. Dla tego wniosku, zaakceptuj wybór **W 1 roku**.
12. Wybierz **dodaj** i skopiuj wartość nowego klucza tajnego klienta, który jest wyświetlany do użycia później.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **subskrypcja katalogu +** w górnym menu i wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję Nowy dostawca **OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź *contoso azure ad*.
1. W przypadku **adresu URL metadanych**wprowadź następujący adres URL zastępujący `your-AD-tenant-domain` nazwą domeny dzierżawy usługi Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Nie** należy na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`używać punktu końcowego metadanych usługi Azure AD w wersji 2.0. Powoduje to błąd podobny `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` do podczas próby zalogowania się.

1. W przypadku **identyfikatora klienta**wprowadź identyfikator aplikacji, który został wcześniej zarejestrowany.
1. W przypadku **klucza tajnego klienta**wprowadź klucz tajny klienta, który został wcześniej zarejestrowany.
1. Pozostaw wartości domyślne dla **zakresu,** **typu odpowiedzi**i **trybu odpowiedzi**.
1. (Opcjonalnie) Wprowadź wartość **dla Domain_hint**. Na przykład *ContosoAD*. Jest to wartość do użycia podczas odwoływania się do tego dostawcy tożsamości przy użyciu *domain_hint* w żądaniu.
1. W obszarze **Mapowanie oświadczeń dostawcy tożsamości**wprowadź następujące wartości mapowania oświadczeń:

    * **Identyfikator użytkownika**: *oid*
    * **Nazwa wyświetlana**: *nazwa*
    * **Imię i nazwisko**: *given_name*
    * **Nazwisko**: *family_name*
    * **E-mail**: *unique_name*

1. Wybierz **pozycję Zapisz**.
