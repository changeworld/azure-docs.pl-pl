---
title: 'Samouczek: Rejestrowanie aplikacji'
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zarejestrować aplikację sieci web w usłudze Azure Active Directory B2C przy użyciu witryny Azure portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a688f5e75f7513d0ea4308b751f87f75a2c9510a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183095"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Samouczek: Rejestrowanie aplikacji w usłudze Azure Active Directory B2C

Aby [aplikacje](application-types.md) mogły wchodzić w interakcje z usługą Azure Active Directory B2C (Azure AD B2C), muszą być zarejestrowane w dzierżawie, którą zarządzasz. W tym samouczku pokazano, jak zarejestrować aplikację sieci web przy użyciu witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Tworzenie klucza tajnego klienta

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze własnej [dzierżawy usługi Azure AD B2C,](tutorial-create-tenant.md)utwórz go teraz. Można użyć istniejącej dzierżawy usługi Azure AD B2C.

## <a name="register-a-web-application"></a>Rejestrowanie aplikacji internetowej

Aby zarejestrować aplikację w dzierżawie usługi Azure AD B2C, można użyć **bieżącego środowiska aplikacji** lub naszego nowego środowiska rejestracji aplikacji ujednoliconej **(w wersji zapoznawczej).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W witrynie Azure portal wyszukaj i wybierz **pozycję Azure AD B2C**.
1. Wybierz **pozycję Aplikacje**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *webapp1*.
1. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
1. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. Na przykład można ustawić go do `https://localhost:44316`słuchania lokalnie w . Jeśli numer portu nie jest jeszcze numerem portu, możesz wprowadzić wartość zastępczą i zmienić ją później.

    Do celów testowych, takich jak `https://jwt.ms` ten samouczek można ustawić go do którego wyświetla zawartość tokenu do inspekcji. W tym samouczku ustaw `https://jwt.ms`adres URL **odpowiedzi** na .

    Do odpowiedzi na adresy URL obowiązują następujące ograniczenia:

    * Adres URL odpowiedzi musi `https`zaczynać się od schematu .
    * Adres URL odpowiedzi jest rozróżniana wielkość liter. Jego przypadek musi być zgodny ze ścieżką adresu URL uruchomionej aplikacji. Na przykład jeśli aplikacja zawiera jako `.../abc/response-oidc`część swojej `.../ABC/response-oidc` ścieżki, nie należy określać w adresie URL odpowiedzi. Ponieważ przeglądarka internetowa traktuje ścieżki jako rozróżniane `.../abc/response-oidc` wielkość liter, pliki cookie skojarzone z `.../ABC/response-oidc` mogą zostać wykluczone, jeśli zostaną przekierowane do adresu URL niezgodnego z wielkością sprawy.

1. Wybierz **pozycję Utwórz,** aby zakończyć rejestrację aplikacji.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W witrynie Azure portal wyszukaj i wybierz **pozycję Azure AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *webapp1*.
1. Wybierz **konta w dowolnym katalogu organizacyjnym lub dowolnym dostawcy tożsamości**.
1. W obszarze **Przekierowanie identyfikatora URI**wybierz pozycję **Web**, a następnie wprowadź `https://jwt.ms` pole tekstowe adresu URL.

    Identyfikator URI przekierowania jest punktem końcowym, do którego użytkownik jest wysyłany przez serwer autoryzacji (usługa Azure AD B2C, w tym przypadku) po zakończeniu interakcji z użytkownikiem i do którego token dostępu lub kod autoryzacji jest wysyłany po pomyślnej autoryzacji. W aplikacji produkcyjnej jest to zazwyczaj publicznie dostępny punkt końcowy, w `https://contoso.com/auth-response`którym aplikacja jest uruchomiona, na przykład. Do celów testowych, takich jak ten `https://jwt.ms`samouczek, można ustawić go do , należącej do firmy Microsoft aplikacji sieci web, która wyświetla zdekodowany zawartość tokenu (zawartość tokenu nigdy nie opuścić przeglądarki). Podczas tworzenia aplikacji można dodać punkt końcowy, w którym `https://localhost:5000`aplikacja nasłuchuje lokalnie, na przykład . Identyfikatory URI przekierowania można w dowolnym momencie dodawać i modyfikować w zarejestrowanych aplikacjach.

    Następujące ograniczenia mają zastosowanie do przekierowania identyfikatorów URI:

    * Adres URL odpowiedzi musi `https`zaczynać się od schematu .
    * Adres URL odpowiedzi jest rozróżniana wielkość liter. Jego przypadek musi być zgodny ze ścieżką adresu URL uruchomionej aplikacji. Na przykład jeśli aplikacja zawiera jako `.../abc/response-oidc`część swojej `.../ABC/response-oidc` ścieżki, nie należy określać w adresie URL odpowiedzi. Ponieważ przeglądarka internetowa traktuje ścieżki jako rozróżniane `.../abc/response-oidc` wielkość liter, pliki cookie skojarzone z `.../ABC/response-oidc` mogą zostać wykluczone, jeśli zostaną przekierowane do adresu URL niezgodnego z wielkością sprawy.

1. W obszarze **Uprawnienia**zaznacz pole wyboru *Udzielaj zgody administratora na otwieranie i offline_access uprawnienia.*
1. Wybierz pozycję **Zarejestruj**.

Po zakończeniu rejestracji aplikacji włącz niejawny przepływ dotacji:

1. W obszarze **Zarządzanie**wybierz pozycję **Uwierzytelnianie**.
1. Wybierz **wypróbuj nowe środowisko** (jeśli jest to pokazane).
1. W obszarze **Niejawna dotacja**wybierz pola wyboru **Tokeny dostępu** i **Tokeny Identyfikatorów.**
1. Wybierz **pozycję Zapisz**.

* * *

## <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta

Jeśli aplikacja wymienia kod autoryzacji dla tokenu dostępu, należy utworzyć klucz tajny aplikacji.

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Na stronie **Azure AD B2C — Aplikacje** wybierz utworzoną aplikację, na przykład *webapp1*.
1. Wybierz **pozycję Klawisze,** a następnie wybierz pozycję **Generuj klawisz**.
1. Wybierz **pozycję Zapisz,** aby wyświetlić klucz. Zanotuj wartość pola **Klucz aplikacji**. Tej wartości używasz jako klucza tajnego aplikacji w kodzie aplikacji.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Na stronie **Azure AD B2C — Rejestracje aplikacji (Wersja zapoznawcza)** wybierz utworzoną aplikację, na przykład *webapp1*.
1. W obszarze **Zarządzanie**wybierz pozycję **Certyfikaty & wpisy tajne**.
1. Wybierz pozycję **Nowy wpis tajny klienta**.
1. Wprowadź opis klucza tajnego klienta w polu **Opis.** Na przykład *clientsecret1*.
1. W obszarze **Wygasa**wybierz czas trwania, dla którego klucz tajny jest prawidłowy, a następnie wybierz pozycję **Dodaj**.
1. Zapisz wartość klucza **tajnego**. Tej wartości używasz jako klucza tajnego aplikacji w kodzie aplikacji.

* * *

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Tworzenie klucza tajnego klienta

Następnie dowiedz się, jak utworzyć przepływy użytkowników, aby umożliwić użytkownikom rejestrację, logowanie się i zarządzanie ich profilami.

> [!div class="nextstepaction"]
> [Tworzenie przepływów użytkowników w usłudze Azure Active Directory B2C >](tutorial-create-user-flows.md)
