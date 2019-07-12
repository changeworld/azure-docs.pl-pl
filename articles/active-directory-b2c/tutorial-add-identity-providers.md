---
title: Samouczek — Dodawanie dostawcy tożsamości do aplikacji — Azure Active Directory B2C
description: Dowiedz się, jak dodać dostawców tożsamości do aplikacji w usłudze Azure Active Directory B2C w witrynie Azure portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33f595dd36ac9448cc1276647f9943326b0b74c1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655222"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Samouczek: Dodawanie dostawcy tożsamości do aplikacji w usłudze Azure Active Directory B2C

W swoich aplikacjach możesz umożliwić użytkownikom logowanie się za pomocą różnych dostawców tożsamości. *Dostawca tożsamości* tworzy, i przechowuje informacje dotyczące tożsamości oraz zarządza nimi, zapewniając jednocześnie aplikacjom usługi uwierzytelniania. Możesz dodać dostawców tożsamości, które są obsługiwane przez usługi Azure Active Directory (Azure AD) B2C, aby Twoje [przepływy użytkownika](active-directory-b2c-reference-policies.md) przy użyciu witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie aplikacji dostawcy tożsamości
> * Dodaj dostawców tożsamości dla Twojej dzierżawy
> * Dodawanie dostawcy tożsamości do przepływu użytkownika

Zazwyczaj używa się tylko jednego dostawcę tożsamości w swoich aplikacjach, ale istnieje możliwość, aby dodać więcej. W tym samouczku przedstawiono sposób dodawania do aplikacji dostawcy tożsamości usługi Azure AD i dostawcy tożsamości usługi Facebook. Dodanie obu tych dostawców tożsamości do aplikacji jest opcjonalne. Możesz również dodać innych dostawców tożsamości, takich jak [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [firmyMicrosoft](active-directory-b2c-setup-msa-app.md), lub [Twitter](active-directory-b2c-setup-twitter-app.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika](tutorial-create-user-flows.md) aby użytkownicy mogli zarejestrować się i zaloguj się do aplikacji.

## <a name="create-applications"></a>Tworzenie aplikacji

Aplikacje dostawcy tożsamości, podaj identyfikator i klucz w celu umożliwienia komunikacji z dzierżawy usługi Azure AD B2C. W tej części samouczka utworzysz aplikację usługi Azure AD i aplikacji usługi Facebook, z których możesz korzystać z identyfikatory i klucze, aby dodać dostawców tożsamości do dzierżawy. W przypadku dodawania tylko jednego z dostawców tożsamości, wystarczy utworzyć aplikację dla tego dostawcy.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

Aby włączyć logowania dla użytkowników z usługi Azure AD, musisz zarejestrować aplikację w dzierżawie usługi Azure AD. Dzierżawy usługi Azure AD nie jest taka sama jak dzierżawy usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera dzierżawy usługi Azure AD.
1. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **rejestracje aplikacji**.
1. Wybierz **nowej rejestracji**.
1. Wprowadź nazwę aplikacji. Na przykład `Azure AD B2C App`.
1. Zaakceptuj wybór **kont w tym katalogu organizacji tylko** dla tej aplikacji.
1. Aby uzyskać **identyfikator URI przekierowania**, zaakceptuj wartość **sieci Web** i wprowadź następujący adres URL małymi literami, zastępując `your-B2C-tenant-name` nazwą dzierżawy usługi Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Wszystkie adresy URL powinny teraz być za pomocą [z usługi b2clogin.com](b2clogin.md).

1. Wybierz **zarejestrować**, następnie rekord **identyfikator aplikacji (klienta)** używane w kolejnym kroku.
1. W obszarze **Zarządzaj** w menu aplikacji wybierz **certyfikaty i klucze tajne**, a następnie wybierz **nowy wpis tajny klienta**.
1. Wprowadź **opis** jako klucz tajny klienta. Na przykład `Azure AD B2C App Secret`.
1. Wybierz okres ważności. Dla tej aplikacji, Zaakceptuj wybór **w 1 rok**.
1. Wybierz **Dodaj**, następnie zapisz wartość nowy wpis tajny klienta, którego można użyć w późniejszym kroku.

### <a name="create-a-facebook-application"></a>Tworzenie aplikacji usługi Facebook

Aby użyć konta w serwisie Facebook jako dostawcy tożsamości w usłudze Azure AD B2C, musisz utworzyć aplikację w usłudze Facebook. Jeśli nie masz jeszcze konta w serwisie Facebook, możesz pobrać na stronie [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Zaloguj się do [serwisu Facebook dla deweloperów](https://developers.facebook.com/) przy użyciu poświadczeń konta serwisu Facebook.
1. Jeśli jeszcze tego nie zrobiono, należy zarejestrować się jako deweloper usługi Facebook. Aby to zrobić, wybierz **wprowadzenie** w prawym górnym rogu strony, Zaakceptuj zasady w serwisie Facebook, a kroki rejestracji.
1. Wybierz **Moje aplikacje** i następnie **tworzenie aplikacji**.
1. Wprowadź **nazwę wyświetlaną** i prawidłową **E-mail kontaktowy**.
1. Kliknij przycisk **utworzyć identyfikator aplikacji**. Może to wymagać zaakceptować zasady platformy usługi Facebook i ukończyć sprawdzania zabezpieczeń w trybie online.
1. Wybierz **ustawienia** > **podstawowe**.
1. Wybierz **kategorii**, na przykład `Business and Pages`. Ta wartość jest wymagana przez usługi Facebook, ale nie jest używany przez usługę Azure AD B2C.
1. W dolnej części strony wybierz **Dodaj platformy**, a następnie wybierz pozycję **witryny sieci Web**.
1. W **adres URL witryny**, wprowadź `https://your-tenant-name.b2clogin.com/` zastępowanie `your-tenant-name` nazwą Twojej dzierżawy.
1. Wprowadź adres URL dla **adres URL zasad zachowania poufności**, na przykład `http://www.contoso.com/`. Adres URL zasad ochrony prywatności to strona, których obsługa zapewniające informacje o ochronie prywatności dla aplikacji.
1. Wybierz pozycję **Zapisz zmiany**.
1. W górnej części strony, należy rejestrować wartości **Identyfikatora aplikacji**.
1. Obok pozycji **klucz tajny aplikacji**, wybierz opcję **Pokaż** i zapisz jego wartość. Aby skonfigurować usługi Facebook jako dostawcy tożsamości w dzierżawie używasz Identyfikatora aplikacji i klucz tajny aplikacji. **Klucz tajny aplikacji** jest ważnym poświadczeniem zabezpieczeń, które należy przechowywać bezpiecznie.
1. Wybierz znak plus obok pozycji **produktów**, a następnie w obszarze **logowania do usługi Facebook**, wybierz opcję **Konfigurowanie**.
1. W obszarze **logowania do usługi Facebook** w menu po lewej stronie wybierz **ustawienia**.
1. W **identyfikatory URI przekierowania OAuth prawidłowe**, wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Zastąp `your-tenant-name` nazwą Twojej dzierżawy. Wybierz **Zapisz zmiany** w dolnej części strony.
1. Aby udostępnić aplikację usługi Facebook do usługi Azure AD B2C, kliknij przycisk **stan** selektora w prawym górnym rogu strony i przekształcać je **na** upublicznić aplikacji, a następnie kliknij przycisk **Potwierdź** . W tym momencie stan powinien zmienić z **rozwoju** do **Live**.

## <a name="add-the-identity-providers"></a>Dodawanie dostawcy tożsamości

Po utworzeniu aplikacji dostawcy tożsamości, który chcesz dodać, możesz dodać dostawcy tożsamości do dzierżawy.

### <a name="add-the-azure-active-directory-identity-provider"></a>Dostawca tożsamości usługi Azure Active Directory

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera dzierżawy usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź **nazwa**. Na przykład, wprowadź *firmy Contoso w usłudze Azure AD*.
1. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Open ID Connect (wersja zapoznawcza)** , a następnie kliknij przycisk **OK**.
1. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości**
1. Aby uzyskać **adres url metadanych**, wprowadź następujący adres URL, zastępując `your-AD-tenant-domain` z nazwą domeny dzierżawy usługi Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Aby uzyskać **identyfikator klienta**, wprowadź *identyfikator aplikacji (klienta)* wcześniej zapisane.
1. Aby uzyskać **klucz tajny klienta**, wprowadź *klucz tajny klienta* wartość, która wcześniej zapisaną.
1. Opcjonalnie wprowadź wartość dla **Domain_hint**. Na przykład `ContosoAD`. [Wskazówki dotyczące domeny](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) są dyrektyw, które są częścią żądania uwierzytelniania z aplikacji. Mogą one używane, aby przyspieszyć użytkownika do ich federacyjnych dostawca tożsamości strony logowania. Lub mogą być używane przez aplikację wielodostępną aby przyspieszyć użytkownika bezpośrednio na marki usługi Azure AD strony logowania dla swojej dzierżawy.
1. Kliknij przycisk **OK**.
1. Wybierz **Mapowanie oświadczeń tego dostawcy tożsamości** i ustaw następujące oświadczeń:

    - Aby uzyskać **identyfikator użytkownika**, wprowadź `oid`.
    - Aby uzyskać **nazwę wyświetlaną**, wprowadź `name`.
    - Aby uzyskać **imię**, wprowadź `given_name`.
    - Aby uzyskać **nazwisko**, wprowadź `family_name`.
    - Aby uzyskać **E-mail**, wprowadź `unique_name`.

1. Wybierz **OK**, a następnie wybierz **Utwórz** Aby zapisać konfigurację.

### <a name="add-the-facebook-identity-provider"></a>Dostawca tożsamości aplikacji usługi Facebook

1. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź **nazwa**. Na przykład, wprowadź *Facebook*.
1. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Facebook**, a następnie wybierz **OK**.
1. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź *Identyfikatora aplikacji* wcześniej zarejestrowany jako **identyfikator klienta**.
1. Wprowadź *klucz tajny aplikacji* zapisane jako **klucz tajny klienta**.
1. Wybierz **OK** , a następnie wybierz **Utwórz** Aby zapisać konfigurację usługi Facebook.

## <a name="update-the-user-flow"></a>Aktualizacja przepływu użytkownika

W tym samouczku ukończono jako część wymagań wstępnych utworzono przepływ użytkownika dla rejestracji i logowania o nazwie *B2C_1_signupsignin1*. W tej sekcji dodasz dostawców tożsamości do *B2C_1_signupsignin1* przepływu użytkownika.

1. Wybierz **przepływy użytkownika (zasady)** , a następnie wybierz pozycję *B2C_1_signupsignin1* przepływu użytkownika.
2. Wybierz **dostawców tożsamości**, wybierz opcję **Facebook** i **firmy Contoso w usłudze Azure AD** dostawców tożsamości, które zostały dodane.
3. Wybierz pozycję **Zapisz**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Na stronie Przegląd przepływ użytkownika, który został utworzony, wybierz **uruchomić przepływ użytkownika**.
1. Aby uzyskać **aplikacji**, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. **Adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
1. Wybierz **uruchomić przepływ użytkownika**, a następnie zaloguj się przy użyciu dostawcy tożsamości, które zostały wcześniej dodane.
1. Powtórz kroki od 1 do 3 dla innych dostawców tożsamości, które zostały dodane.

Jeśli logowanie operacja się powiedzie, użytkownik jest przekierowany do `https://jwt.ms` powoduje wyświetlenie zdekodowany tokenu, podobnie do:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji dostawcy tożsamości
> * Dodaj dostawców tożsamości dla Twojej dzierżawy
> * Dodawanie dostawcy tożsamości do przepływu użytkownika

Dowiedz się, jak dostosować interfejsu użytkownika strony widocznym dla użytkowników w ramach środowiska obsługi tożsamości w swoich aplikacjach:

> [!div class="nextstepaction"]
> [Dostosowywanie interfejsu użytkownika aplikacji w usłudze Azure Active Directory B2C](tutorial-customize-ui.md)
