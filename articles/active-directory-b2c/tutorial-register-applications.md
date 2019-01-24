---
title: Samouczek — rejestrowanie aplikacji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarejestrować aplikacji w usłudze Azure Active Directory B2C w witrynie Azure portal.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 99ad1bbaa732b1207ead9da8da36f345d4978241
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856030"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>Samouczek: Rejestrowanie aplikacji w usłudze Azure Active Directory B2C

Zanim Twoje [aplikacje](active-directory-b2c-apps.md) mogą wchodzić w interakcje z usługą Azure Active Directory (Azure AD) B2C, musi być zarejestrowana w dzierżawie, w którym możesz zarządzać. W tym samouczku dowiesz się, jak zarejestrować aplikacji przy użyciu witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Rejestrowanie internetowego interfejsu API
> * Rejestrowanie aplikacji mobilnej lub natywnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie zostało jeszcze utworzone własne [dzierżawy usługi Azure AD B2C](tutorial-create-tenant.md), utwórz ją teraz. Można użyć istniejącej dzierżawy.

## <a name="register-a-web-application"></a>Rejestrowanie aplikacji internetowej

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.

    ![Przejdź do katalogu subskrypcji](./media/tutorial-register-applications/switch-directories.png)

2. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Azure AD B2C**.
3. Wybierz **aplikacje**, a następnie wybierz pozycję **Dodaj**.

    ![Dodawanie aplikacji](./media/tutorial-register-applications/add-application.png)

4. Wprowadź nazwę aplikacji. Na przykład *webapp1*.
5. Aby uzyskać **Uwzględnij aplikację sieci web / interfejs API sieci web** i **Zezwalaj na niejawny przepływ**, wybierz opcję **tak**.
6. Aby uzyskać **adres URL odpowiedzi**, wprowadź punkt końcowy, w którym usługi Azure AD B2C należy zwracać wszystkie tokeny żądań aplikacji. Na przykład można ustawić go do lokalnego nasłuchiwania na `https://localhost:44316` Jeśli jeszcze nie znasz numeru portu, możesz podać wartość zastępczą i go zmienić później. Do celów testowych możesz ustawić ją na `https://jwt.ms`, która wyświetla zawartość tokenu w celu przeprowadzenia inspekcji. W tym samouczku ustaw ją na `https://jwt.ms`. 

    Odpowiedź, adres URL musi rozpoczynać się od systemu `https`, a wszystkie odpowiedzi wartości adresu URL muszą współużytkować jedną domenę DNS. Na przykład, jeśli aplikacja ma adres URL odpowiedzi `https://login.contoso.com`, można dodać do niego, np. ten adres URL `https://login.contoso.com/new`. Lub możesz zapoznać się z poddomena DNS `login.contoso.com`, takich jak `https://new.login.contoso.com`. Jeśli chcesz korzystać z aplikacji, za pomocą `login-east.contoso.com` i `login-west.contoso.com` jako adresy URL odpowiedzi, należy dodać te adresy URL odpowiedzi w następującej kolejności: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Dwa ostatnie adresy można dodać, ponieważ są poddomenami pierwszego adresu URL odpowiedzi `contoso.com`.

7. Kliknij pozycję **Utwórz**.

    ![Ustawianie właściwości aplikacji](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>Utwórz klucz tajny klienta

W przypadku aplikacji wymienia kodu dla tokenu, musisz utworzyć wpis tajny aplikacji.

1. Wybierz **klucze** a następnie kliknij przycisk **Wygeneruj klucz**.

    ![Generuj klucze](./media/tutorial-register-applications/generate-keys.png)

2. Wybierz **Zapisz** Aby wyświetlić klucz. Zanotuj wartość pola **Klucz aplikacji**. Użyj tej wartości jako wpisu tajnego aplikacji w kodzie Twojej aplikacji.

    ![Zapisz klucz](./media/tutorial-register-applications/save-key.png)
    
3. Wybierz **dostęp do interfejsu API**, kliknij przycisk **Dodaj**i wybierz internetowy interfejs API oraz zakresy (uprawnienia).

    ![Konfigurowanie dostępu do interfejsu API](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>Rejestrowanie internetowego interfejsu API

1. Wybierz **aplikacje**, a następnie wybierz pozycję **Dodaj**.
3. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
4. Aby uzyskać **Uwzględnij aplikację sieci web / interfejs API sieci web** i **Zezwalaj na niejawny przepływ**, wybierz opcję **tak**.
5. Aby uzyskać **adres URL odpowiedzi**, wprowadź punkt końcowy, w którym usługi Azure AD B2C należy zwracać wszystkie tokeny żądań aplikacji. Na przykład można ustawić go do lokalnego nasłuchiwania na `https://localhost:44316`. Jeśli jeszcze nie znasz numeru portu, można wprowadzić wartość symbolu zastępczego i go zmienić później.
6. Aby uzyskać **identyfikator URI Identyfikatora aplikacji**, wprowadź identyfikator używany na potrzeby interfejsu API sieci web. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
7. Kliknij pozycję **Utwórz**.
8. Wybierz *webapi1* aplikacji, który został utworzony i następnie wybierz pozycję **opublikowane zakresy** można dodać więcej zakresów, zgodnie z potrzebami. Domyślnie `user_impersonation` jest definiowany zakres. `user_impersonation` Zakresu daje innym aplikacjom możliwość dostępu do tego interfejsu API w imieniu zalogowanego użytkownika. Jeśli chcesz, `user_impersonation` zakres można usunąć.

    ![Ustaw opublikowane zakresy](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>Rejestrowanie aplikacji mobilnej lub natywnej

1. Wybierz **aplikacje**, a następnie wybierz pozycję **Dodaj**.
2. Wprowadź nazwę aplikacji. Na przykład *nativeapp1*.
3. Aby uzyskać **Uwzględnij aplikację sieci web / interfejs API sieci web**, wybierz opcję **nie**.
4. Aby uzyskać **Uwzględnij klienta natywnego**, wybierz opcję **tak**.
5. Aby uzyskać **identyfikator URI przekierowania**, wprowadź prawidłowy identyfikator URI przekierowania ze schematem niestandardowym. Istnieją dwie ważne uwagi podczas wybierania identyfikator URI przekierowania:

    - **Unikatowe** — schemat identyfikatora URI przekierowania powinien być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` jest to schemat. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik może wskazać dostawcę o wybranie aplikacji. Jeśli użytkownik dokona nieprawidłowego wyboru, logowanie nie powiedzie się.
    - **Pełne** — identyfikator URI przekierowania musi mieć schemat i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//contoso/` działa i `//contoso` zakończy się niepowodzeniem. Upewnij się, że identyfikator URI przekierowania nie zawiera znaków specjalnych, takich jak podkreślenia.

6. Kliknij pozycję **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Rejestrowanie internetowego interfejsu API
> * Rejestrowanie aplikacji mobilnej lub natywnej

> [!div class="nextstepaction"]
> [Tworzenie przepływów użytkownika w usłudze Azure Active Directory B2C](tutorial-create-user-flows.md)