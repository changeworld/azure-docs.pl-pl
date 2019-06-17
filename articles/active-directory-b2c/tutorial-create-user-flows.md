---
title: Samouczek — tworzenie przepływów użytkownika — Azure Active Directory B2C
description: Dowiedz się, jak tworzyć przepływy użytkownika w portalu Azure w celu włączenia logowania się w górę, zaloguj się i edycji profilu użytkownika dla aplikacji w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 111196388d0e17ecde8a2055959f2f573e43ade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056356"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Samouczek: Tworzenie przepływów użytkownika w usłudze Azure Active Directory B2C

W aplikacji może być [przepływy użytkownika](active-directory-b2c-reference-policies.md) umożliwiające użytkownikom subskrypcji, zaloguj się lub zarządzać swój profil. Można utworzyć wiele przepływów użytkownika w różnych typów w dzierżawie usługi Azure Active Directory (Azure AD) B2C i używać ich w aplikacjach, zgodnie z potrzebami. Przepływy użytkownika mogą być ponownie używane w aplikacjach.

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
1. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.

    ![Przejdź do katalogu subskrypcji](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. W menu po lewej stronie w obszarze **zasady**, wybierz opcję **przepływy użytkownika (zasady)** , a następnie wybierz pozycję **nowy przepływ użytkownika**.

    ![Wybierz nowy przepływ użytkownika](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na **zalecane** zaznacz **Zaloguj się i logowanie** przepływu użytkownika.

    ![Wybierz przepływ, rejestracji i logowania użytkowników](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *signupsignin1*.
1. Aby uzyskać **dostawców tożsamości**, wybierz opcję **rejestracja E-mail**.

    ![Ustaw właściwości przepływu](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Aby uzyskać **atrybutach i oświadczeniach użytkowników**, wybierz oświadczenia i atrybuty, które chcesz zbierać i wysyłać od użytkownika podczas rejestracji. Na przykład wybierz **Pokaż więcej**, a następnie wybierz polecenie atrybutach i oświadczeniach dla **Kraj/Region**, **nazwę wyświetlaną**, i **kod pocztowy**. Kliknij przycisk **OK**.

    ![Wybierz atrybuty i oświadczenia](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kliknij przycisk **Utwórz** dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz przepływ użytkownika został utworzony, otwórz jego stronę przeglądu, a następnie wybierz **uruchomić przepływ użytkownika**.
1. Aby uzyskać **aplikacji**, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. **Adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
1. Kliknij przycisk **uruchomić przepływ użytkownika**, a następnie wybierz pozycję **Zarejestruj się teraz**.

    ![Uruchamianie przepływu użytkownika](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Wprowadź prawidłowy adres e-mail, kliknij przycisk **Wyślij kod weryfikacyjny**, wprowadź kod weryfikacyjny, który zostanie wyświetlony, a następnie wybierz **zweryfikować kodu**.
1. Wprowadź nowe hasło i Potwierdź hasło.
1. Wybierz region i kraj, wprowadź nazwę, która ma zostać wyświetlony, wprowadź kod pocztowy, a następnie kliknij **Utwórz**. Token jest zwracany do `https://jwt.ms` i powinna być wyświetlana użytkownikowi.
1. Teraz możesz uruchamiać przepływ użytkownika ponownie i powinno być możliwe zalogować się przy użyciu konta, który został utworzony. Zwrócony token zawiera oświadczenia, które wybrany kraj/region, nazwę i kod pocztowy.

## <a name="create-a-profile-editing-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego edytowania profilu

Jeśli chcesz użytkownicy mogli edytować swój profil w aplikacji, możesz użyć edytowania przepływu użytkownika profilu.

1. W menu po lewej stronie przeglądu dzierżawy usługi Azure AD B2C wybierz **przepływy użytkownika (zasady)** , a następnie wybierz pozycję **nowy przepływ użytkownika**.
1. Wybierz **edytowania profilu** przepływ użytkownika na karcie zalecane.
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *profileediting1*.
1. Aby uzyskać **dostawców tożsamości**, wybierz opcję **logowanie za pomocą konta lokalnego**.
1. Aby uzyskać **atrybutów użytkownika**, wybierz atrybuty, które chcesz, aby klient, aby można było edytować w swoim profilu. Na przykład wybierz **Pokaż więcej**, a następnie wybierz atrybuty i oświadczenia dotyczące **nazwę wyświetlaną** i **stanowisko**. Kliknij przycisk **OK**.
1. Kliknij przycisk **Utwórz** dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz przepływ użytkownika został utworzony, otwórz jego stronę przeglądu, a następnie wybierz **uruchomić przepływ użytkownika**.
1. Aby uzyskać **aplikacji**, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. **Adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
1. Kliknij przycisk **uruchomić przepływ użytkownika**, a następnie zaloguj się przy użyciu konta, która została wcześniej utworzona.
1. Masz teraz możliwość zmienić wyświetlaną nazwę i stanowisko użytkownika. Kliknij pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinna być wyświetlana użytkownikowi.

## <a name="create-a-password-reset-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Aby umożliwić użytkownikom aplikacji do zresetowania swojego hasła, należy użyć przepływu użytkownika resetowania hasła.

1. W menu po lewej stronie wybierz **przepływy użytkownika (zasady)** , a następnie wybierz pozycję **nowy przepływ użytkownika**.
1. Wybierz **resetowania hasła** przepływ użytkownika na karcie zalecane.
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *passwordreset1*.
1. Aby uzyskać **dostawców tożsamości**, Włącz **Resetuj hasło przy użyciu adresu e-mail**.
1. W obszarze oświadczeń aplikacji kliknij **Pokaż więcej** i wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji. Na przykład wybierz pozycję **Identyfikator obiektu użytkownika**.
1. Kliknij przycisk **OK**.
1. Kliknij przycisk **Utwórz** dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz przepływ użytkownika został utworzony, otwórz jego stronę przeglądu, a następnie wybierz **uruchomić przepływ użytkownika**.
1. Aby uzyskać **aplikacji**, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. **Adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
1. Kliknij przycisk **uruchomić przepływ użytkownika**, sprawdź poprawność adresu e-mail konta, który został wcześniej utworzony i wybierz **Kontynuuj**.
1. Masz teraz możliwość zmiany hasła dla użytkownika. Zmień hasło, a następnie wybierz pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinna być wyświetlana użytkownikowi.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie przepływu rejestracji i logowania użytkowników
> * Tworzenie przepływu użytkownika dotyczącego edytowania profilu
> * Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Następnie informacje o dodawaniu dostawców tożsamości dla tych aplikacji, aby włączyć logowanie użytkownika przy użyciu dostawców, takich jak usługi Azure AD, Amazon, Facebook, serwisu GitHub, LinkedIn, Microsoft lub Twitter.

> [!div class="nextstepaction"]
> [Dodawanie dostawcy tożsamości do aplikacji >](tutorial-add-identity-providers.md)