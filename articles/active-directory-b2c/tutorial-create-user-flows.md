---
title: Samouczek — tworzenie przepływów użytkowników — usługa Azure Active Directory B2C
description: Dowiedz się, jak utworzyć przepływy użytkowników w witrynie Azure portal, aby umożliwić rejestrację, logowanie się i edycję profilu użytkownika dla aplikacji w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264247"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Samouczek: Tworzenie przepływów użytkowników w usłudze Azure Active Directory B2C

W aplikacjach mogą być [przepływy użytkowników,](user-flow-overview.md) które umożliwiają użytkownikom rejestrację, logowanie się lub zarządzanie ich profilem. Można utworzyć wiele przepływów użytkowników różnych typów w dzierżawie usługi Azure Active Directory B2C (Azure AD B2C) i używać ich w aplikacjach w razie potrzeby. Przepływy użytkownika mogą być ponownie zażywane w aplikacjach.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie przepływu użytkowników rejestracji i logowania
> * Tworzenie przepływu użytkownika dotyczącego edytowania profilu
> * Tworzenie przepływu użytkownika dotyczącego resetowania haseł

W tym samouczku pokazano, jak utworzyć niektóre zalecane przepływy użytkownika przy użyciu witryny Azure portal. Jeśli szukasz informacji na temat konfigurowania przepływu poświadczeń haseł właściciela zasobu (ROPC) w aplikacji, zobacz [Konfigurowanie przepływu poświadczeń hasła właściciela zasobu w usłudze Azure AD B2C](configure-ropc.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

[Zarejestruj aplikacje,](tutorial-register-applications.md) które są częścią przepływów użytkownika, które chcesz utworzyć.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Tworzenie przepływu użytkowników rejestracji i logowania

Przepływ użytkownika rejestracji i logowania obsługuje zarówno środowisko rejestracji, jak i logowania w jednej konfiguracji. Użytkownicy aplikacji są prowadzone w dół właściwą ścieżkę w zależności od kontekstu.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.

    ![Okienko dzierżawy, katalogu i subskrypcji B2C, witryna Azure portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. W witrynie Azure portal wyszukaj i wybierz **pozycję Azure AD B2C**.
1. W obszarze **Zasady**wybierz **pozycję Przepływy użytkownika (zasady),** a następnie wybierz pozycję **Nowy przepływ użytkownika**.

    ![Strona Przepływy użytkownika w portalu z wyróżnionym przyciskiem Nowy przepływ użytkownika](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na karcie **Zalecane** wybierz pozycję **Zarejestruj się i zaloguj się przepływem** użytkownika.

    ![Wybierz stronę przepływu użytkownika z wyróżnioną pozycją Zarejestruj się i zaloguj się](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *signupsignin1*.
1. W przypadku **dostawców tożsamości**wybierz **pozycję Rejestracja za pośrednictwem poczty e-mail**.

    ![Tworzenie strony przepływu użytkowników w witrynie Azure portal z wyróżnionymi właściwościami](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. W przypadku **atrybutów i oświadczeń użytkownika**wybierz oświadczenia i atrybuty, które chcesz zebrać i wysłać od użytkownika podczas rejestracji. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz atrybuty i oświadczenia dla **kraju/regionu,** **nazwy wyświetlanej**i **kodu pocztowego**. Kliknij przycisk **OK**.

    ![Strona wyboru atrybuty i oświadczenia z zaznaczonymi trzema roszczeniami](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kliknij **przycisk Utwórz,** aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację internetową o nazwie *webapp1.* **Adres URL** odpowiedzi `https://jwt.ms`powinien być pokazywalny .
1. Kliknij **pozycję Uruchom przepływ użytkownika**, a następnie wybierz pozycję Zarejestruj się **teraz**.

    ![Uruchom stronę przepływu użytkownika w portalu z wyróżnionym przyciskiem Uruchom przepływ użytkownika](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Wprowadź prawidłowy adres e-mail, kliknij przycisk **Wyślij kod weryfikacyjny**, wprowadź otrzymany kod weryfikacyjny, a następnie wybierz **pozycję Zweryfikuj kod**.
1. Wprowadź nowe hasło i potwierdź hasło.
1. Wybierz swój kraj i region, wprowadź nazwę, którą chcesz wyświetlić, wprowadź kod pocztowy, a następnie kliknij przycisk **Utwórz**. Token jest zwracany i `https://jwt.ms` powinien być wyświetlany.
1. Teraz można ponownie uruchomić przepływ użytkownika i powinieneś mieć możliwość zalogowania się przy pomocą utworzonego konta. Zwrócony token zawiera oświadczenia wybrane przez kraj/region, nazwę i kod pocztowy.

## <a name="create-a-profile-editing-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego edytowania profilu

Jeśli chcesz umożliwić użytkownikom edytowanie ich profilu w aplikacji, użyj przepływu użytkownika edycji profilu.

1. W menu strony przeglądu dzierżawy usługi Azure AD B2C wybierz **pozycję Przepływy użytkownika (zasady),** a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Wybierz przepływ użytkownika **edycji profilu** na karcie **Zalecane.**
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *profileediting1*.
1. W przypadku **dostawców tożsamości**wybierz pozycję **Logowanie do konta lokalnego**.
1. W przypadku **atrybutów użytkownika**wybierz atrybuty, które odbiorca ma mieć możliwość edytowania w swoim profilu. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz oba atrybuty i oświadczenia dla **nazwy wyświetlanej** i **tytułu zadania**. Kliknij przycisk **OK**.
1. Kliknij **przycisk Utwórz,** aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację internetową o nazwie *webapp1.* **Adres URL** odpowiedzi `https://jwt.ms`powinien być pokazywalny .
1. Kliknij **pozycję Uruchom przepływ użytkownika**, a następnie zaloguj się przy poprzednim utworzonym koncie.
1. Teraz masz możliwość zmiany nazwy wyświetlanej i stanowiska dla użytkownika. Kliknij przycisk **Kontynuuj**. Token jest zwracany i `https://jwt.ms` powinien być wyświetlany.

## <a name="create-a-password-reset-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Aby umożliwić użytkownikom aplikacji zresetowanie hasła, należy użyć przepływu użytkownika resetowania hasła.

1. W menu przeglądowa dzierżawy usługi Azure AD B2C wybierz polecenie **Przepływy użytkownika (zasady),** a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Wybierz przepływ użytkownika **resetowania hasła** na karcie **Zalecane.**
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *hasłoreset1*.
1. W przypadku **dostawców tożsamości**włącz **opcję Resetuj hasło przy użyciu adresu e-mail**.
1. W obszarze Oświadczenia aplikacji kliknij pozycję **Pokaż więcej** i wybierz oświadczenia, które mają zostać zwrócone w tokenach autoryzacji wysłanych z powrotem do aplikacji. Na przykład wybierz pozycję **Identyfikator obiektu użytkownika**.
1. Kliknij przycisk **OK**.
1. Kliknij **przycisk Utwórz,** aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację internetową o nazwie *webapp1.* **Adres URL** odpowiedzi `https://jwt.ms`powinien być pokazywalny .
1. Kliknij **pozycję Uruchom przepływ użytkownika**, sprawdź adres e-mail utworzonego wcześniej konta i wybierz pozycję **Kontynuuj**.
1. Teraz masz możliwość zmiany hasła dla użytkownika. Zmień hasło i wybierz przycisk **Kontynuuj**. Token jest zwracany i `https://jwt.ms` powinien być wyświetlany.

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie przepływu użytkowników rejestracji i logowania
> * Tworzenie przepływu użytkownika dotyczącego edytowania profilu
> * Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Następnie dowiedz się więcej o dodawaniu dostawców tożsamości do aplikacji, aby umożliwić logowanie użytkowników za pomocą dostawców, takich jak Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft lub Twitter.

> [!div class="nextstepaction"]
> [Dodawanie dostawców tożsamości do aplikacji >](tutorial-add-identity-providers.md)