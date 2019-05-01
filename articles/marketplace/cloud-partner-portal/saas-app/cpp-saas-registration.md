---
title: Rejestrowanie aplikacji SaaS | Portal Azure Marketplace
description: Wyjaśnia, jak zarejestrować aplikacji SaaS przy użyciu witryny Azure portal.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pabutler
ms.openlocfilehash: 1edaf89c056918f640a905b99d01775273b2c133
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941925"
---
# <a name="register-a-saas-application"></a>Rejestrowanie aplikacji SaaS

W tym artykule wyjaśniono, jak zarejestrować aplikację SaaS, korzystanie z programu Microsoft [witryny Azure portal](https://portal.azure.com/).  Po pomyślnej rejestracji zostanie wyświetlony token zabezpieczeń usługi Azure Active Directory (Azure AD), który umożliwia dostęp do interfejsów API realizacji SaaS.  Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [co to jest uwierzytelnianie?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Przepływ uwierzytelniania do usługi

Na poniższym diagramie przedstawiono przepływ subskrypcji nowych klientów i stosowania tych interfejsów API:

![SaaS oferują usługi flow interfejsu API](./media/saas-offer-publish-api-flow-v1.png)

Azure nie nakłada żadnych ograniczeń dotyczących uwierzytelniania, uwidocznionego przez usługi SaaS dla swoich użytkowników końcowych. Jednak za pomocą interfejsów API realizacji SaaS uwierzytelniania przy użyciu tokenu zabezpieczeń usługi Azure AD, zazwyczaj uzyskiwane przez zarejestrowanie aplikacji SaaS za pośrednictwem witryny Azure portal. 


## <a name="register-an-azure-ad-secured-app"></a>Rejestrowanie aplikacji korzystającej z usługi AD platformy Azure

Każda aplikacja, która ma korzystać z funkcji usługi Azure AD, musi najpierw zostać zarejestrowana w dzierżawie usługi Azure AD. Ten proces rejestracji obejmuje, zapewniając usługi Azure AD szczegółów dotyczących aplikacji, na przykład adres URL, gdzie znajduje się, adres URL, aby wysyłać odpowiedzi, po uwierzytelnieniu użytkownika identyfikatora URI, który identyfikuje aplikację i tak dalej.  Aby zarejestrować nową aplikację w witrynie Azure portal, wykonaj następujące czynności:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, kliknij konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
3.  W okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** usługi, kliknij przycisk **rejestracje aplikacji**i kliknij przycisk **rejestrowanie nowej aplikacji**.

    ![Rejestracje aplikacji AD SaaS](./media/saas-offer-app-registration-v1.png)

4.  Na stronie tworzenia wprowadź aplikację\'informacje rejestracyjne s:
    -   **Nazwa**: wprowadź opisową nazwę aplikacji
    -   **Typ aplikacji**: 
        - Wybierz opcję **Natywna** dla [aplikacji klienckich ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application), które są zainstalowane lokalnie na urządzeniu. To ustawienie jest używane w przypadku [klientów natywnych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) publicznego protokołu OAuth.
        - Wybierz **aplikacji sieci Web / interfejs API** dla [aplikacje klienckie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) i [aplikacji interfejsu API i zasobów](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) są instalowane na zabezpieczonym serwerze. To ustawienie jest używane do uwierzytelniania OAuth poufne [sieci web klientów](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) i publicznych [klientów z systemem agenta użytkownika](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Ta sama aplikacja może ujawniać zarówno klienta, jak i interfejs API lub zasób.
    -   **Adres URL logowania**: W przypadku aplikacji interfejsu API i aplikacji sieci Web Podaj podstawowy adres URL aplikacji. Na przykład **http://localhost:31544** może być adres URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy następnie użyje tego adresu URL do logowania do aplikacji klienta sieci web.
    -   **Identyfikator URI przekierowania**: W przypadku aplikacji natywnych Podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla twojej aplikacji, na przykład **http://MyFirstAADApp**.

        ![Rejestracje aplikacji AD SaaS](./media/saas-offer-app-registration-v1-2.png)

        Konkretne przykłady dla aplikacji sieci web lub aplikacji natywnych, zapoznaj się z tego przewodnika Szybki Start z przewodnikiem konfiguracje, które są dostępne w *wprowadzenie* części [przewodnik dla deweloperów usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Po zakończeniu kliknij pozycję **Gotowe**. Usługa Azure AD przypisuje unikatowy *identyfikator aplikacji* do aplikacji\'ponowne przekierowanie do aplikacji\'strony głównej rejestracji s. W zależności od tego, czy aplikacja jest internetowa czy natywna, dostępne są różne opcje umożliwiające dodawanie dodatkowych funkcji do aplikacji.

>[!Note]
>Domyślnie nowo zarejestrowana aplikacja jest skonfigurowane i umożliwiają tylko użytkownicy z tej samej dzierżawie, aby zalogować się do aplikacji.


## <a name="using-the-azure-ad-security-token"></a>Przy użyciu tokenu zabezpieczeń usługi Azure AD

Po zarejestrowaniu aplikacji mogą programowo żądać tokenu zabezpieczeń usługi Azure AD.  Używanie tego tokenu i zgłosić wniosek o rozwiązać ten problem powinien wydawcy.  Przy użyciu różnych interfejsów realizacji, token jest parametr zapytania w adresie URL po użytkownik jest przekierowywany do witryny sieci Web SaaS na platformie Azure.  Ten token jest prawidłowy tylko przez jedną godzinę.  Ponadto należy adres URL Dekoduj wartość tokenu za pomocą przeglądarki przed jego użyciem.

Aby uzyskać więcej informacji na temat tych tokenów, zobacz [tokenów dostępu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Pobierz token oparty na aplikacji usługi Azure AD

Metoda HTTP

`GET`

*Adres URL żądania*

**https://login.microsoftonline.com/*{Identyfikatordzierżawy}*/oauth2/token**

*Parametr identyfikatora URI*

|  **Nazwa parametru**  | **Wymagane**  | **Opis**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | Identyfikator dzierżawy zarejestrowana aplikacja usługi AAD   |
|  |  |  |


*Nagłówek żądania*

|  **Nazwa nagłówka**  | **Wymagane** |  **Opis**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Typ zawartości skojarzonej z żądaniem. Wartość domyślna to `application/x-www-form-urlencoded`.  |
|  |  |  |


*Treść żądania*

| **Nazwa właściwości**   | **Wymagane** |  **Opis**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Typ udzielenia uprawnień. Wartość domyślna to `client_credentials`.                    |
|  Client_id          | True         |  Identyfikator klienta/aplikacji skojarzone z aplikacją usługi Azure AD.                  |
|  client_secret      | True         |  Hasło skojarzone z aplikacją usługi Azure AD.                               |
|  Resource           | True         |  Zasób docelowy, dla którego żądany jest token. Wartość domyślna to `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Odpowiedź*

|  **Nazwa**  | **Typ**       |  **Opis**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Żądanie zakończone powodzeniem   |
|  |  |  |

*TokenResponse*

Przykładowe tokenu odpowiedzi:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Kolejne kroki

Aplikacji zabezpieczonej przez usługi AD platformy Azure mogą teraz używać [SaaS realizacji interfejsu API w wersji 2](./cpp-saas-fulfillment-api-v2.md).
