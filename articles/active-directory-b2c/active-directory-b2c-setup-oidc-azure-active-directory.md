---
title: Konfigurowanie logowania do Azure Active Directory organizacji — Azure Active Directory B2C
description: Skonfiguruj logowanie do konkretnej organizacji Azure Active Directory w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 58c6d1b032f5b492c5641ff51da80426124069b1
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716783"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurowanie logowania dla konkretnej organizacji Azure Active Directory w programie Azure Active Directory B2C

>[!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie należy używać tej funkcji w środowiskach produkcyjnych.

Aby użyć Azure Active Directory (Azure AD) jako [dostawcy tożsamości](active-directory-b2c-reference-oauth-code.md) w Azure AD B2C, należy utworzyć aplikację, która go reprezentuje. W tym artykule opisano sposób włączania logowania dla użytkowników z określonej organizacji usługi Azure AD przy użyciu przepływu użytkownika w Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Tworzenie aplikacji usługi Azure AD

Aby włączyć Logowanie użytkowników z określonej organizacji usługi Azure AD, musisz zarejestrować aplikację w ramach organizacji dzierżawy usługi Azure AD, która nie jest taka sama jak dzierżawy Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD. Wybierz pozycję **katalog i filtr subskrypcji** w górnym menu i wybierz katalog, który zawiera dzierżawę usługi Azure AD. To nie jest ta sama dzierżawa, co dzierżawa Azure AD B2C.
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
9. Wybierz pozycję **certyfikaty &** wpisy tajne w menu aplikacji, a następnie wybierz pozycję **nowy klucz tajny klienta**.
10. Wprowadź nazwę wpisu tajnego klienta. Na przykład `Azure AD B2C App Secret`.
11. Wybierz okres ważności. W przypadku tej aplikacji zaakceptuj wybór **w ciągu 1 roku**.
12. Wybierz pozycję **Dodaj** i skopiuj wartość nowego klucza tajnego klienta, który zostanie wyświetlony później.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości

1. Upewnij się, że używasz katalogu, który zawiera Azure AD B2C dzierżawcy. Wybierz pozycję **katalog i filtr subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
2. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
3. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
4. Wprowadź **nazwę**. Na przykład wprowadź wartość `Contoso Azure AD`.
5. Wybierz **Typ dostawcy tożsamości**, wybierz pozycję **OpenID Connect Connect (wersja zapoznawcza)** , a następnie kliknij przycisk **OK**.
6. Wybierz opcję **Skonfiguruj tego dostawcę tożsamości**
7. W polu **adres URL metadanych**wprowadź następujący adres URL `your-AD-tenant-domain` zastępujący nazwą domeny dzierżawy usługi Azure AD. Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. W polu **Identyfikator klienta**wprowadź identyfikator aplikacji, który został wcześniej zarejestrowany, a dla **wpisu tajnego klienta**wprowadź wcześniej zarejestrowany klucz tajny klienta.
9. Opcjonalnie wprowadź wartość dla **Domain_hint**. Na przykład `ContosoAD`. Jest to wartość, która będzie używana podczas odwoływania się do tego dostawcy tożsamości przy użyciu *domain_hint* w żądaniu.
10. Kliknij przycisk **OK**.
11. Wybierz pozycję **Mapuj oświadczenia tego dostawcy tożsamości** i ustaw następujące oświadczenia:

    - W obszarze **Identyfikator użytkownika**wprowadź `oid`.
    - W obszarze **Nazwa wyświetlana**wprowadź `name`.
    - Dla **danej nazwy**wprowadź `given_name`.
    - W obszarze **nazwisko**wprowadź `family_name`.
    - W obszarze **poczta e-mail**wprowadź `unique_name`polecenie.

12. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** , aby zapisać konfigurację.
