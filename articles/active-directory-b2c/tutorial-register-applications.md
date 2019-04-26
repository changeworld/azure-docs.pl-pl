---
title: Samouczek — Rejestracja aplikacji — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarejestrować aplikacji sieci web w usłudze Azure Active Directory B2C w witrynie Azure portal.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/05/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 849bcfe2b5ee177d06b8e4cf62fd29459d2e59ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359742"
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
6. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. Na przykład można ustawić go do lokalnego nasłuchiwania na `https://localhost:44316` Jeśli jeszcze nie znasz numeru portu, możesz podać wartość zastępczą i go zmienić później. Do celów testowych możesz ustawić ją na `https://jwt.ms`, która wyświetla zawartość tokenu w celu przeprowadzenia inspekcji. W tym samouczku ustaw ją na `https://jwt.ms`. 

    Odpowiedź, adres URL musi rozpoczynać się od systemu `https`, a wszystkie odpowiedzi wartości adresu URL muszą współużytkować jedną domenę DNS. Na przykład, jeśli aplikacja ma adres URL odpowiedzi `https://login.contoso.com`, można dodać do niego, np. ten adres URL `https://login.contoso.com/new`. Lub możesz zapoznać się z poddomena DNS `login.contoso.com`, takich jak `https://new.login.contoso.com`. Jeśli chcesz korzystać z aplikacji, za pomocą `login-east.contoso.com` i `login-west.contoso.com` jako adresy URL odpowiedzi, należy dodać te adresy URL odpowiedzi w następującej kolejności: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Dwa ostatnie adresy można dodać, ponieważ są poddomenami pierwszego adresu URL odpowiedzi `contoso.com`.

7. Kliknij pozycję **Utwórz**.

## <a name="create-a-client-secret"></a>Utwórz klucz tajny klienta

Jeśli aplikacja wymienia kodu dla tokenu, musisz utworzyć wpis tajny aplikacji.

1. Wybierz **klucze** a następnie kliknij przycisk **Wygeneruj klucz**.
2. Wybierz **Zapisz** Aby wyświetlić klucz. Zanotuj wartość pola **Klucz aplikacji**. Użyj tej wartości jako wpisu tajnego aplikacji w kodzie Twojej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Utwórz klucz tajny klienta

> [!div class="nextstepaction"]
> [Tworzenie przepływów użytkownika w usłudze Azure Active Directory B2C](tutorial-create-user-flows.md)
