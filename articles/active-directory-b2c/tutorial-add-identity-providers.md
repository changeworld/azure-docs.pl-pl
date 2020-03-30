---
title: 'Samouczek: Dodawanie dostawców tożsamości do aplikacji'
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dodać dostawców tożsamości do aplikacji w usłudze Azure Active Directory B2C przy użyciu witryny Azure portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183520"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Samouczek: Dodawanie dostawców tożsamości do aplikacji w usłudze Azure Active Directory B2C

W swoich aplikacjach możesz umożliwić użytkownikom logowanie się za pomocą różnych dostawców tożsamości. *Dostawca tożsamości* tworzy, i przechowuje informacje dotyczące tożsamości oraz zarządza nimi, zapewniając jednocześnie aplikacjom usługi uwierzytelniania. Można dodać dostawców tożsamości, które są obsługiwane przez usługę Azure Active Directory B2C (Azure AD B2C) do [przepływów użytkowników](user-flow-overview.md) przy użyciu witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie aplikacji dostawcy tożsamości
> * Dodawanie dostawców tożsamości do dzierżawy
> * Dodawanie dostawców tożsamości do przepływu użytkownika

Zazwyczaj używasz tylko jednego dostawcy tożsamości w aplikacjach, ale masz możliwość dodania więcej. W tym samouczku pokazano, jak dodać dostawcę tożsamości usługi Azure AD i dostawcę tożsamości Facebooka do aplikacji. Dodawanie obu tych dostawców tożsamości do aplikacji jest opcjonalne. Możesz również dodać innych dostawców tożsamości, takich jak [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md)lub [Twitter](identity-provider-twitter.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika,](tutorial-create-user-flows.md) aby umożliwić użytkownikom rejestrację i zalogowanie się do aplikacji.

## <a name="create-applications"></a>Tworzenie aplikacji

Aplikacje dostawcy tożsamości zapewniają identyfikator i klucz, aby umożliwić komunikację z dzierżawą usługi Azure AD B2C. W tej sekcji samouczka utworzysz aplikację usługi Azure AD i aplikację Facebook, z której otrzymujesz identyfikatory i klucze, aby dodać dostawców tożsamości do dzierżawy. Jeśli dodajesz tylko jednego z dostawców tożsamości, wystarczy utworzyć aplikację dla tego dostawcy.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

Aby włączyć logowanie dla użytkowników z usługi Azure AD, należy zarejestrować aplikację w dzierżawie usługi Azure AD. Dzierżawa usługi Azure AD nie jest taka sama jak dzierżawa usługi Azure AD B2C.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **subskrypcja Katalog +** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
1. Wybierz **pozycję Nowa rejestracja**.
1. Wprowadź nazwę aplikacji. Na przykład `Azure AD B2C App`.
1. Zaakceptuj wybór **kont w tym katalogu organizacyjnym tylko** dla tej aplikacji.
1. W przypadku **identyfikatora URI przekierowania**zaakceptuj wartość **sieci Web** i wprowadź `your-B2C-tenant-name` następujący adres URL we wszystkich małych literach, zastępując nazwą dzierżawy usługi Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Wszystkie adresy URL powinny teraz używać [b2clogin.com](b2clogin.md).

1. Wybierz **pozycję Zarejestruj**, a następnie zarejestruj identyfikator aplikacji **(klienta),** którego używasz w późniejszym kroku.
1. W obszarze **Zarządzaj** w menu aplikacji wybierz polecenie **Certyfikaty & wpisy tajne**, a następnie wybierz pozycję **Nowy klucz tajny klienta**.
1. Wprowadź **opis** klucza tajnego klienta. Na przykład `Azure AD B2C App Secret`.
1. Wybierz okres wygaśnięcia. Dla tego wniosku, zaakceptuj wybór **W 1 roku**.
1. Wybierz **dodaj**, a następnie zapisz wartość nowego klucza tajnego klienta, którego używasz w późniejszym kroku.

### <a name="create-a-facebook-application"></a>Tworzenie aplikacji na Facebooku

Aby użyć konta na Facebooku jako dostawcy tożsamości w usłudze Azure AD B2C, musisz utworzyć aplikację na Facebooku. Jeśli nie masz jeszcze konta na Facebooku, możesz [https://www.facebook.com/](https://www.facebook.com/)je uzyskać w punkcie .

1. Zaloguj się na [Facebooku dla programistów za](https://developers.facebook.com/) pomocą poświadczeń konta na Facebooku.
1. Jeśli jeszcze tego nie zrobiłeś, musisz zarejestrować się jako programista facebooka. Aby to zrobić, wybierz **pozycję Wprowadzenie** w prawym górnym rogu strony, zaakceptuj zasady Facebooka i wykonaj kroki rejestracji.
1. Wybierz **pozycję Moje aplikacje,** a następnie **utwórz aplikację**.
1. Wprowadź **nazwę wyświetlaną** i prawidłowy **adres e-mail kontaktu**.
1. Kliknij **pozycję Utwórz identyfikator aplikacji**. Może to wymagać zaakceptowania zasad platformy Facebooka i zakończenia kontroli bezpieczeństwa online.
1. Wybierz **pozycję Ustawienia** > **podstawowe**.
1. Wybierz **kategorię**, `Business and Pages`na przykład . Ta wartość jest wymagana przez Facebooka, ale nie jest używana przez usługę Azure AD B2C.
1. U dołu strony wybierz pozycję **Dodaj platformę**, a następnie wybierz pozycję **Witryna sieci Web**.
1. W **adresie URL witryny**wprowadź `https://your-tenant-name.b2clogin.com/` zastąpienie `your-tenant-name` nazwą dzierżawy.
1. Wprowadź adres URL adresu URL polityki `http://www.contoso.com/` **prywatności**, na przykład . Adres URL polityki prywatności to strona, którą przechowujesz, aby podać informacje o prywatności dla aplikacji.
1. Wybierz pozycję **Zapisz zmiany**.
1. W górnej części strony zapisz wartość identyfikatora **aplikacji**.
1. Obok pozycji **Klucz tajny aplikacji**wybierz pozycję **Pokaż** i zapisz jego wartość. Identyfikator aplikacji i klucz tajny aplikacji są używane do konfigurowania Facebooka jako dostawcy tożsamości w dzierżawie. **Klucz tajny aplikacji** to ważne poświadczenia zabezpieczeń, które należy bezpiecznie przechowywać.
1. Wybierz znak plus obok **pozycji PRODUKTY**, a następnie w obszarze **Logowanie na Facebooku**wybierz pozycję **Skonfiguruj**.
1. W obszarze **Logowanie do Facebooka** w menu po lewej stronie wybierz pozycję **Ustawienia**.
1. W **polu Prawidłowe identyfikatory URI przekierowania OAuth**wprowadź . `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` Zamień `your-tenant-name` na nazwę dzierżawy. Wybierz **pozycję Zapisz zmiany** u dołu strony.
1. Aby udostępnić aplikację Facebook na platformie Azure AD B2C, kliknij selektor **stanu** w prawym górnym rogu strony i włącz **ją,** aby udostępnić aplikację, a następnie kliknij przycisk **Potwierdź**. W tym momencie stan powinien zmienić się z **Rozwoju** na **Żywo**.

## <a name="add-the-identity-providers"></a>Dodawanie dostawców tożsamości

Po utworzeniu aplikacji dla dostawcy tożsamości, który chcesz dodać, należy dodać dostawcę tożsamości do dzierżawy.

### <a name="add-the-azure-active-directory-identity-provider"></a>Dodawanie dostawcy tożsamości usługi Azure Active Directory

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **subskrypcja katalogu +** w górnym menu i wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję Nowy dostawca **OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź *contoso azure ad*.
1. W przypadku **adresu URL metadanych**wprowadź następujący adres URL zastępujący `your-AD-tenant-domain` nazwą domeny dzierżawy usługi Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. W przypadku **identyfikatora klienta**wprowadź identyfikator aplikacji, który został wcześniej zarejestrowany.
1. W przypadku **klucza tajnego klienta**wprowadź klucz tajny klienta, który został wcześniej zarejestrowany.
1. Pozostaw wartości domyślne dla **zakresu,** **typu odpowiedzi**i **trybu odpowiedzi**.
1. (Opcjonalnie) Wprowadź wartość **dla Domain_hint**. Na przykład *ContosoAD*. [Wskazówki dotyczące domeny](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) są dyrektywami, które są zawarte w żądaniu uwierzytelnienia z aplikacji. Mogą one służyć do przyspieszenia użytkownika do ich federacyjnej strony logowania IdP. Lub mogą być używane przez aplikację z wieloma dzierżawami, aby przyspieszyć użytkownika bezpośrednio do strony logowania usługi Azure AD dla ich dzierżawy.
1. W obszarze **Mapowanie oświadczeń dostawcy tożsamości**wprowadź następujące wartości mapowania oświadczeń:

    * **Identyfikator użytkownika**: *oid*
    * **Nazwa wyświetlana**: *nazwa*
    * **Imię i nazwisko**: *given_name*
    * **Nazwisko**: *family_name*
    * **E-mail**: *unique_name*

1. Wybierz **pozycję Zapisz**.

### <a name="add-the-facebook-identity-provider"></a>Dodawanie dostawcy tożsamości facebooka

1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję **Facebook**.
1. Wprowadź **nazwę**. Na przykład *Facebook*.
1. W przypadku identyfikatora **klienta**wprowadź identyfikator aplikacji Facebooka, który został utworzony wcześniej.
1. W przypadku **klucza tajnego klienta**wprowadź zarejestrowany klucz tajny aplikacji.
1. Wybierz **pozycję Zapisz**.

## <a name="update-the-user-flow"></a>Aktualizowanie przepływu użytkownika

W samouczku, który został ukończony w ramach wymagań wstępnych, utworzono przepływ użytkownika do rejestracji i logowania o nazwie *B2C_1_signupsignin1*. W tej sekcji należy dodać dostawców tożsamości do przepływu użytkownika *B2C_1_signupsignin1.*

1. Wybierz **przepływy użytkownika (zasady),** a następnie wybierz *B2C_1_signupsignin1* przepływ użytkownika.
2. Wybierz **dostawców tożsamości**, wybierz dostawców tożsamości **Facebook** i **Contoso Azure AD,** które zostały dodane.
3. Wybierz **pozycję Zapisz**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Na stronie Przegląd utworzonego przepływu użytkownika wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację internetową o nazwie *webapp1.* **Adres URL** odpowiedzi `https://jwt.ms`powinien być pokazywalny .
1. Wybierz **pozycję Uruchom przepływ użytkownika**, a następnie zaloguj się za pomocą dostawcy tożsamości, który został wcześniej dodany.
1. Powtórz kroki od 1 do 3 dla innych dostawców tożsamości, które zostały dodane.

Jeśli operacja logowania zakończy się pomyślnie, zostanie `https://jwt.ms` przekierowana do której jest wyświetlana zdekodowana token, podobnie jak:

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
> * Dodawanie dostawców tożsamości do dzierżawy
> * Dodawanie dostawców tożsamości do przepływu użytkownika

Następnie dowiedz się, jak dostosować interfejs użytkownika stron wyświetlanych użytkownikom w ramach ich tożsamości w aplikacjach:

> [!div class="nextstepaction"]
> [Dostosowywanie interfejsu użytkownika aplikacji w usłudze Azure Active Directory B2C](tutorial-customize-ui.md)
