---
title: Samouczek — tworzenie przepływów użytkownika — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć przepływy użytkownika dla aplikacji w usłudze Azure Active Directory B2C w witrynie Azure portal.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9c08b5b5278045341ed86d987557d03cb46754e6
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601585"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Samouczek: Tworzenie przepływów użytkownika w usłudze Azure Active Directory B2C

W aplikacji, konieczne może być [przepływy użytkownika](active-directory-b2c-reference-policies.md) umożliwiające użytkownikom subskrypcji, zaloguj się lub zarządzać swój profil. Można utworzyć wiele przepływów użytkownika w różnych typów w dzierżawie usługi Azure Active Directory (Azure AD) B2C i używać ich w aplikacjach, zgodnie z potrzebami. Przepływy użytkownika mogą być ponownie używane w aplikacjach.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie przepływu rejestracji i logowania użytkowników
> * Tworzenie przepływu użytkownika dotyczącego edytowania profilu
> * Tworzenie przepływu użytkownika dotyczącego resetowania haseł

W tym samouczku dowiesz się, jak utworzyć niektóre przepływy użytkownika zalecane przy użyciu witryny Azure portal. Jeśli szukasz informacji na temat sposobu konfigurowania poświadczenia hasła właściciela zasobu (ROPC) przepływu w aplikacji, zobacz [skonfiguruj poświadczenia hasła właściciela zasobu przepływ w usłudze Azure AD B2C](configure-ropc.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[Rejestrowanie aplikacji](tutorial-register-applications.md) będących częścią przepływy użytkownika, w której chcesz utworzyć. 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Tworzenie przepływu rejestracji i logowania użytkowników

Przepływ rejestracji i logowania użytkownika obsługuje środowiska rejestracji i logowania za pomocą jednej konfiguracji. Użytkownicy twojej aplikacji są prowadzone w dół prawidłową ścieżkę, w zależności od kontekstu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.

    ![Przejdź do katalogu subskrypcji](./media/tutorial-create-user-flows/switch-directories.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. W menu po lewej stronie wybierz **przepływy użytkownika**, a następnie wybierz pozycję **nowy przepływ użytkownika**.

    ![Wybierz nowy przepływ użytkownika](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. Wybierz **rejestracji i logowania** przepływ użytkownika na karcie zalecane.

    ![Wybierz przepływ, rejestracji i logowania użytkowników](./media/tutorial-create-user-flows/signup-signin-type.png)

6. Wprowadź **nazwę** przepływu użytkownika. Na przykład *signupsignin1*.
7. Aby uzyskać **dostawców tożsamości**, wybierz opcję **rejestracja E-mail**.

    ![Ustaw właściwości przepływu](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. Aby uzyskać **atrybutach i oświadczeniach użytkowników**, wybierz oświadczenia i atrybuty, które chcesz zbierać i wysyłać od użytkownika podczas rejestracji. Na przykład wybierz **Pokaż więcej**, a następnie wybierz **Kraj/Region**, **nazwę wyświetlaną**, i **kod pocztowy**. Kliknij przycisk **OK**.

    ![Wybierz atrybuty i oświadczenia](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. Kliknij przycisk **Utwórz** dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Na stronie Przegląd przepływ użytkownika, który został utworzony, wybierz **uruchomić przepływ użytkownika**.
2. Aby uzyskać **aplikacji**, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. **Adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
3. Kliknij przycisk **uruchomić przepływ użytkownika**, a następnie wybierz pozycję **Zarejestruj się teraz**.

    ![Uruchamianie przepływu użytkownika](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. Wprowadź prawidłowy adres e-mail, kliknij przycisk **Wyślij kod weryfikacyjny**, a następnie wprowadź kod weryfikacyjny, z którą zostanie wyświetlony.
5. Wprowadź nowe hasło i Potwierdź hasło.
6. Wprowadź nazwę, która ma zostać wyświetlona, wybierz region i kraj, wprowadź kod pocztowy, a następnie kliknij **Utwórz**. Token jest zwracany do `https://jwt.ms` i powinna być wyświetlana użytkownikowi.
7. Teraz możesz uruchamiać przepływ użytkownika ponownie i powinno być możliwe zalogować się przy użyciu konta, który został utworzony. Zwrócony token zawiera oświadczenia, które wybrane nazwy, kraj/region i kod pocztowy.

## <a name="create-a-profile-editing-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego edytowania profilu

Jeśli chcesz użytkownicy mogli edytować swój profil w aplikacji, możesz użyć edytowania przepływu użytkownika profilu.

1. W menu po lewej stronie wybierz **przepływy użytkownika**, a następnie wybierz pozycję **nowy przepływ użytkownika**.
2. Wybierz **edytowania profilu** przepływ użytkownika na karcie zalecane.
3. Wprowadź **nazwę** przepływu użytkownika. Na przykład *profileediting1*.
4. Aby uzyskać **dostawców tożsamości**, wybierz opcję **logowanie za pomocą konta lokalnego**.
5. Aby uzyskać **atrybutów użytkownika**, wybierz atrybuty, które chcesz, aby klient, aby można było edytować w swoim profilu. Na przykład wybierz **Pokaż więcej**, a następnie wybierz **nazwę wyświetlaną** i **stanowisko**. Kliknij przycisk **OK**.
6. Kliknij przycisk **Utwórz** dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Na stronie Przegląd przepływ użytkownika, który został utworzony, wybierz **uruchomić przepływ użytkownika**.
2. Aby uzyskać **aplikacji**, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. **Adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
3. Kliknij przycisk **uruchomić przepływ użytkownika**, a następnie zaloguj się przy użyciu konta, która została wcześniej utworzona.
4. Masz teraz możliwość zmienić wyświetlaną nazwę i stanowisko użytkownika. Kliknij pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinna być wyświetlana użytkownikowi.

## <a name="create-a-password-reset-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Istnieje możliwość włączenia użytkownika aplikacji do zresetowania swojego hasła, jeśli to konieczne. Aby włączyć resetowania hasła, należy użyć przepływ użytkownika resetowania hasła.

1. W menu po lewej stronie wybierz **przepływy użytkownika**, a następnie wybierz pozycję **nowy przepływ użytkownika**.
2. Wybierz **resetowania hasła** przepływ użytkownika na karcie zalecane.
3. Wprowadź **nazwę** przepływu użytkownika. Na przykład *passwordreset1*.
4. Aby uzyskać **dostawców tożsamości**, Włącz **Resetuj hasło przy użyciu adresu e-mail**.
5. W obszarze oświadczeń aplikacji kliknij **Pokaż więcej** i wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji. Na przykład wybierz pozycję **Identyfikator obiektu użytkownika**.
6. Kliknij przycisk **OK**.
7. Kliknij przycisk **Utwórz** dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Na stronie Przegląd przepływ użytkownika, który został utworzony, wybierz **uruchomić przepływ użytkownika**.
2. Aby uzyskać **aplikacji**, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. **Adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
3. Kliknij przycisk **uruchomić przepływ użytkownika**, a następnie zaloguj się przy użyciu konta, która została wcześniej utworzona.
4. Masz teraz możliwość zmiany hasła dla użytkownika. Kliknij pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinna być wyświetlana użytkownikowi.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie przepływu rejestracji i logowania użytkowników
> * Tworzenie przepływu użytkownika dotyczącego edytowania profilu
> * Tworzenie przepływu użytkownika dotyczącego resetowania haseł

> [!div class="nextstepaction"]
> [Dodawanie dostawcy tożsamości do aplikacji w usłudze Azure Active Directory B2C](tutorial-add-identity-providers.md)