---
title: Zarejestruj aplikację SaaS | Azure Marketplace
description: W tym artykule wyjaśniono, jak zarejestrować aplikację SaaS przy użyciu witryny Azure portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275717"
---
# <a name="register-a-saas-application"></a>Rejestrowanie aplikacji SaaS

W tym artykule wyjaśniono, jak zarejestrować aplikację SaaS za pomocą portalu Microsoft [Azure](https://portal.azure.com/).  Po pomyślnej rejestracji otrzymasz token zabezpieczający usługi Azure Active Directory (Azure AD), którego można użyć do uzyskania dostępu do interfejsów API realizacji usługi SaaS.  Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [Co to jest uwierzytelnianie?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Przepływ uwierzytelniania usługi do usługi

Na poniższym diagramie przedstawiono przepływ subskrypcji nowego klienta i gdy te interfejsy API są używane:

![SaaS oferują przepływ interfejsu API](./media/saas-offer-publish-api-flow-v1.png)

Platforma Azure nie nakłada żadnych ograniczeń na uwierzytelnianie, które usługa SaaS udostępnia swoim użytkownikom końcowym. Jednak uwierzytelnianie za pomocą interfejsów API realizacji usługi SaaS jest wykonywane za pomocą tokenu zabezpieczeń usługi Azure AD, zwykle uzyskiwane przez zarejestrowanie aplikacji SaaS za pośrednictwem witryny Azure portal. 


## <a name="register-an-azure-ad-secured-app"></a>Rejestrowanie aplikacji zabezpieczonej za pomocą usługi Azure AD

Każda aplikacja, która ma korzystać z funkcji usługi Azure AD, musi najpierw zostać zarejestrowana w dzierżawie usługi Azure AD. Ten proces rejestracji obejmuje podanie szczegółów usługi Azure AD dotyczących aplikacji, takich jak adres URL, w którym się znajduje, adres URL do wysyłania odpowiedzi po uwierzytelnianiu użytkownika, identyfikator URI identyfikujący aplikację itd.  Aby zarejestrować nową aplikację przy użyciu witryny Azure portal, wykonaj następujące kroki:

1.  Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2.  Jeśli twoje konto daje dostęp do więcej niż jednego, kliknij swoje konto w prawym górnym rogu i ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
3.  W lewym okienku nawigacji kliknij usługę **Azure Active Directory,** kliknij pozycję **Rejestracje aplikacji**i kliknij pozycję Nowa **rejestracja aplikacji**.

    ![Rejestracje aplikacji usługi AD SaaS](./media/saas-offer-app-registration-v1.png)

4.  Na stronie Tworzenie wprowadź\'informacje rejestracyjne aplikacji:
    -   **Nazwa**: Wprowadź opisową nazwę aplikacji
    -   **Typ aplikacji**: 
        - Wybierz opcję **Natywna** dla [aplikacji klienckich ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application), które są zainstalowane lokalnie na urządzeniu. To ustawienie jest używane w przypadku [klientów natywnych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) publicznego protokołu OAuth.
        - Wybierz **aplikację sieci Web / interfejs API** dla aplikacji [klienckich](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) i [aplikacji zasobów/interfejsu API,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) które są zainstalowane na bezpiecznym serwerze. To ustawienie jest używane dla poufnych [klientów internetowych](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) OAuth i publicznych [klientów opartych na agentach użytkowników](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Ta sama aplikacja może ujawniać zarówno klienta, jak i interfejs API lub zasób.
    -   **Adres URL logowania:** w przypadku aplikacji sieci Web/aplikacji interfejsu API podaj podstawowy adres URL aplikacji. Na przykład **http://localhost:31544** może być adres URL aplikacji sieci web uruchomionej na komputerze lokalnym. Użytkownicy będą następnie używać tego adresu URL, aby zalogować się do aplikacji klienta sieci web.
    -   **Przekierowanie identyfikatora URI:** Dla aplikacji natywnych, podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla **http://MyFirstAADApp**aplikacji, na przykład .

        ![Rejestracje aplikacji usługi AD SaaS](./media/saas-offer-app-registration-v1-2.png)

        Aby zapoznać się z konkretnymi przykładami dla aplikacji sieci web lub aplikacji natywnych, zapoznaj się z ustawieniami przewodników Szybki start, które są dostępne w sekcji *Wprowadzenie* w [Przewodniku dla deweloperów usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Po zakończeniu kliknij pozycję **Gotowe**. Usługa Azure AD przypisuje unikatowy *identyfikator aplikacji* \'do aplikacji i\'ponownie zostanie przesunięta na stronę rejestracji głównej aplikacji. W zależności od tego, czy aplikacja jest internetowa czy natywna, dostępne są różne opcje umożliwiające dodawanie dodatkowych funkcji do aplikacji.

>[!Note]
>Domyślnie nowo zarejestrowana aplikacja jest skonfigurowana tak, aby zezwalała tylko użytkownikom z tej samej dzierżawy na logowanie się do aplikacji.


## <a name="using-the-azure-ad-security-token"></a>Korzystanie z tokenu zabezpieczeń usługi Azure AD

Po zarejestrowaniu aplikacji można programowo zażądać tokenu zabezpieczającego usługi Azure AD.  Oczekuje się, że wydawca użyje tego tokenu i złożyć żądanie, aby go rozwiązać.  Podczas korzystania z różnych interfejsów API realizacji parametr zapytania tokenu znajduje się w adresie URL, gdy użytkownik jest przekierowywane do witryny sieci Web SaaS z platformy Azure.  Ten token jest ważny tylko przez jedną godzinę.  Ponadto należy dekodować adres URL wartość tokenu z przeglądarki przed użyciem go.

Aby uzyskać więcej informacji na temat tych tokenów, zobacz [Tokeny dostępu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Pobierz token oparty na aplikacji usługi Azure AD

Metoda HTTP

`POST`

*Adres URL żądania*

**https://login.microsoftonline.com/*{identyfikator_dzierżawcy}*/oauth2/token**

*Parametr URI*

|  **Nazwa parametru**  | **Wymagane**  | **Opis**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| identyfikator dzierżawy             | True          | Identyfikator dzierżawy zarejestrowanej aplikacji AAD   |
|  |  |  |


*Nagłówek żądania*

|  **Nazwa nagłówka**  | **Wymagane** |  **Opis**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Typ zawartości skojarzony z żądaniem. Wartością domyślną jest `application/x-www-form-urlencoded`.  |
|  |  |  |


*Treść żądania*

| **Nazwa właściwości**   | **Wymagane** |  **Opis**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Typ dotacji. Wartością domyślną jest `client_credentials`.                    |
|  Client_id          | True         |  Identyfikator klienta/aplikacji skojarzony z aplikacją usługi Azure AD.                  |
|  client_secret      | True         |  Hasło skojarzone z aplikacją usługi Azure AD.                               |
|  Zasób           | True         |  Zasób docelowy, dla którego token jest wymagany. Wartością domyślną jest `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Odpowiedzi*

|  **Nazwa**  | **Typ**       |  **Opis**    |
| ---------- | -------------  | ------------------- |
| 200 ok.    | TokenOdpowiedzialność  | Żądanie powiodło się   |
|  |  |  |

*TokenOdpowiedzialność*

Przykładowy token odpowiedzi:

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


## <a name="next-steps"></a>Następne kroki

Aplikacja zabezpieczona usługą Azure AD może teraz korzystać z [interfejsu API realizacji usługi SaaS w wersji 2.](./pc-saas-fulfillment-api-v2.md)
