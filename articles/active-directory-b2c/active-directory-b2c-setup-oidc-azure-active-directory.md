---
title: Konfigurowanie logowania do Azure Active Directory organizacji — Azure Active Directory B2C
description: Skonfiguruj logowanie do konkretnej organizacji Azure Active Directory w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0c2e368b9c12d8ab673e5b8808632501de448b9a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755766"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurowanie logowania dla konkretnej organizacji Azure Active Directory w programie Azure Active Directory B2C

Aby użyć Azure Active Directory (Azure AD) jako [dostawcy tożsamości](active-directory-b2c-reference-oauth-code.md) w Azure AD B2C, należy utworzyć aplikację, która go reprezentuje. W tym artykule opisano sposób włączania logowania dla użytkowników z określonej organizacji usługi Azure AD przy użyciu przepływu użytkownika w Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Tworzenie aplikacji usługi Azure AD

Aby włączyć Logowanie użytkowników z określonej organizacji usługi Azure AD, musisz zarejestrować aplikację w ramach organizacji dzierżawy usługi Azure AD, która nie jest taka sama jak dzierżawy Azure AD B2C.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę usługi Azure AD. To nie jest ta sama dzierżawa, co dzierżawa Azure AD B2C.
3. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
4. Wybierz pozycję **Nowa rejestracja**.
5. Wprowadź nazwę aplikacji. Na przykład `Azure AD B2C App`.
6. Zaakceptuj wybór **kont w tym katalogu organizacyjnym tylko** dla tej aplikacji.
7. Dla **identyfikatora URI przekierowania**Zaakceptuj wartość **sieci Web**i wprowadź następujący adres URL w postaci małych liter, gdzie `your-B2C-tenant-name` jest zastępowana nazwą dzierżawy Azure AD B2C. Na przykład `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Wszystkie adresy URL powinny teraz używać [b2clogin.com](b2clogin.md).

8. Kliknij pozycję **zarejestruj**. Skopiuj **Identyfikator aplikacji (klienta)** do użycia później.
9. Wybierz pozycję **certyfikaty & wpisy tajne** w menu aplikacji, a następnie wybierz pozycję **nowy klucz tajny klienta**.
10. Wprowadź nazwę wpisu tajnego klienta. Na przykład `Azure AD B2C App Secret`.
11. Wybierz okres ważności. W przypadku tej aplikacji zaakceptuj wybór **w ciągu 1 roku**.
12. Wybierz pozycję **Dodaj** i skopiuj wartość nowego klucza tajnego klienta, który zostanie wyświetlony później.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości

1. Upewnij się, że używasz katalogu, który zawiera Azure AD B2C dzierżawcy. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Nowy dostawca połączenia OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź nazwę *contoso Azure AD*.
1. W polu **adres URL metadanych**wprowadź następujący adres URL, zastępując `your-AD-tenant-domain` nazwą domeny dzierżawy usługi Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Nie** należy używać punktu końcowego metadanych usługi Azure AD v 2.0, na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Wykonanie tej operacji spowoduje wystąpienie błędu podobnego do `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` podczas próby zalogowania się.

1. W polu **Identyfikator klienta**wprowadź wcześniej zarejestrowany identyfikator aplikacji.
1. W polu **klucz tajny klienta**wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. Pozostaw wartości domyślne dla **zakresu**, **typu odpowiedzi**i **trybu odpowiedzi**.
1. Obowiązkowe Wprowadź wartość dla **Domain_hint**. Na przykład *ContosoAD*. Jest to wartość, która będzie używana podczas odwoływania się do tego dostawcy tożsamości przy użyciu *domain_hint* w żądaniu.
1. W obszarze **Mapowanie oświadczeń dostawcy tożsamości**wprowadź następujące wartości mapowania oświadczeń:

    * **Identyfikator użytkownika**: *OID*
    * **Nazwa wyświetlana**: *Nazwa*
    * **Imię:** *given_name*
    * **Nazwisko**: *family_name*
    * **Adres e-mail**: *unique_name*

1. Wybierz pozycję **Zapisz**.
