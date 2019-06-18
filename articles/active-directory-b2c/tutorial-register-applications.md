---
title: Samouczek — Rejestracja aplikacji — Azure Active Directory B2C
description: Dowiedz się, jak zarejestrować aplikacji sieci web w usłudze Azure Active Directory B2C w witrynie Azure portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5c46d3153bdc5768836bce198af115f82e8469f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056278"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Samouczek: Zarejestrować aplikację w usłudze Azure Active Directory B2C

Zanim Twoje [aplikacje](active-directory-b2c-apps.md) mogą wchodzić w interakcje z usługą Azure Active Directory (Azure AD) B2C, musi być zarejestrowana w dzierżawie, w którym możesz zarządzać. W tym samouczku dowiesz się, jak zarejestrować aplikacji sieci web przy użyciu witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Utwórz klucz tajny klienta

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie zostało jeszcze utworzone własne [dzierżawy usługi Azure AD B2C](tutorial-create-tenant.md), utwórz ją teraz. Można użyć istniejącej dzierżawy usługi Azure AD B2C.

## <a name="register-a-web-application"></a>Rejestrowanie aplikacji internetowej

1. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
2. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
3. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
4. Wprowadź nazwę aplikacji. Na przykład *webapp1*.
5. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
6. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. Na przykład możesz ustawić ją do nasłuchiwania lokalnie na `https://localhost:44316`. Jeśli jeszcze nie znasz numeru portu, można wprowadzić wartość symbolu zastępczego i go zmienić później.

    Podczas testowania, np. w tym samouczku można ustawić go `https://jwt.ms` powoduje wyświetlenie zawartości tokenu w celu przeprowadzenia inspekcji. W tym samouczku ustaw **adres URL odpowiedzi** do `https://jwt.ms`.

    Odpowiedź, adres URL musi rozpoczynać się od systemu `https`, a wszystkie odpowiedzi wartości adresu URL muszą współużytkować jedną domenę DNS. Na przykład, jeśli aplikacja ma adres URL odpowiedzi `https://login.contoso.com`, można dodać do niego, np. ten adres URL `https://login.contoso.com/new`. Lub możesz zapoznać się z poddomena DNS `login.contoso.com`, takich jak `https://new.login.contoso.com`. Jeśli chcesz korzystać z aplikacji, za pomocą `login-east.contoso.com` i `login-west.contoso.com` jako adresy URL odpowiedzi, należy dodać te adresy URL odpowiedzi w następującej kolejności: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Dwa ostatnie adresy można dodać, ponieważ są poddomenami pierwszego adresu URL odpowiedzi `contoso.com`.

7. Kliknij pozycję **Utwórz**.

## <a name="create-a-client-secret"></a>Utwórz klucz tajny klienta

Jeśli aplikacja wymienia kodu dla tokenu, musisz utworzyć wpis tajny aplikacji.

1. W **usługi Azure AD B2C — aplikacje** wybierz utworzoną aplikację, na przykład *webapp1*.
2. Wybierz **klucze** , a następnie wybierz **Wygeneruj klucz**.
3. Wybierz **Zapisz** Aby wyświetlić klucz. Zanotuj wartość pola **Klucz aplikacji**. Będzie ona używana jako klucz tajny aplikacji w kodzie twojej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Utwórz klucz tajny klienta

Następnie Dowiedz się, jak tworzyć przepływy użytkownika, aby umożliwić użytkownikom w taki sposób zarejestrować się, zaloguj się i zarządzają swoimi profilami.

> [!div class="nextstepaction"]
> [Tworzenie przepływów użytkownika w usłudze Azure Active Directory B2C >](tutorial-create-user-flows.md)
