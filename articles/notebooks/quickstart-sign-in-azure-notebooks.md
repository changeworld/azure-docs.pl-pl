---
title: Logowanie się do usługi Azure Notebooks Preview
description: Szybko zaloguj się w usłudze Azure Notebooks Preview i ustaw identyfikator użytkownika, który umożliwia dostęp do zapisanych projektów i udostępnianie notesów innym osobom.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: b6572a7c0b965b2b72916db577b47eff4f1921c0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75647020"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Szybki start: zaloguj się i ustaw identyfikator użytkownika w wersji Zapoznawczej notesów platformy Azure

Chociaż zawsze możesz wyświetlać notesy platformy Azure bez logowania się, musisz zalogować się, aby uruchamiać notesy, uzyskiwać dostęp do zapisanych projektów i notesów oraz udostępniać notesy innym osobom.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="sign-in"></a>Logowanie

1. Wybierz **pozycję Zaloguj** się w prawym górnym rogu [notebooks.azure.com](https://notebooks.azure.com/).

    ![Lokalizacja polecenia logowania w notesach platformy Azure](media/accounts/sign-in-command.png)

1. Po wyświetleniu monitu wprowadź adres e-mail konta Microsoft lub konta służbowego i wybierz pozycję **Dalej**. Typy kont są opisane na [koncie użytkownika notesów platformy Azure](azure-notebooks-user-account.md). Jeśli nie masz konta Microsoft lub chcesz je utworzyć do użytku specjalnie z notesami platformy Azure, wybierz pozycję **Utwórz jedno:**

    ![Tworzenie nowego polecenia logowania konta Microsoft](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Jeśli spróbujesz utworzyć nowe konto z adresem e-mail, który ma już skojarzone konto, możesz zobaczyć komunikat "Nie możesz zarejestrować się tutaj przy roboczym lub szkolnym adresie e-mail. Użyj osobistej poczty e-mail, takiej jak Gmail lub Yahoo!, lub uzyskaj nową wiadomość e-mail programu Outlook." W takim przypadku spróbuj zalogować się przy za pomocą służbowego adresu e-mail bez tworzenia nowego konta.

1. Po wyświetleniu monitu wprowadź hasło.

1. Jeśli logujesz się po raz pierwszy, notesy platformy Azure proszą o pozwolenie na dostęp do konta. Wybierz **opcję Tak,** aby kontynuować:

    ![Monit o uprawnienia do konta](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Ustawianie identyfikatora użytkownika

1. Po pierwszym zalogowaniu, masz przypisany tymczasowy identyfikator użytkownika, takich jak "anon-idrca3". Za każdym razem, gdy masz identyfikator użytkownika, który zaczyna się od "anon-", Notesy platformy Azure monituje o utworzenie własnego identyfikatora. Identyfikator użytkownika jest używany w dowolnym adresie URL, który można uzyskać do udostępniania projektów i notesów, więc wybierz coś, co jest unikalne i znaczące dla Ciebie.

    ![Monitowanie o wprowadzenie identyfikatora użytkownika notesów platformy Azure](media/accounts/create-user-id.png)

    Jeśli wybierzesz **opcję Nie dla podziękowań,** notesy platformy Azure będą monitować o identyfikator użytkownika przy każdym loguchieniu. Identyfikator użytkownika można również ustawić w dowolnym momencie w [profilu użytkownika.](azure-notebooks-user-profile.md)

1. Po pomyślnym zalogowaniu się notesy platformy Azure przechodzą do publicznej strony profilu, na której można wybrać **pozycję Edytuj informacje o profilu,** aby wypełnić pozostałe informacje (aby uzyskać więcej informacji, zobacz Twój profil i identyfikator [użytkownika):](azure-notebooks-user-profile.md)

    ![Początkowy widok strony profilu notesów platformy Azure](media/accounts/profile-page-new.png)

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat "Nazwa użytkownika jest już używana", spróbuj użyć innego identyfikatora. Identyfikatory użytkowników są unikatowe na wszystkich kontach notesów platformy Azure, a notesy platformy Azure rezerwują również niektóre identyfikatory użytkowników, takie jak nazwy marek firmy Microsoft.

## <a name="sign-out"></a>Wyloguj

Aby się wylogować, wybierz swoją nazwę użytkownika w prawym górnym rogu strony, a następnie wybierz pozycję **Wyloguj:**

![Lokalizacja polecenia wylogowywania w notesach platformy Azure](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki start: tworzenie notesu i udostępnianie go](quickstart-create-share-jupyter-notebook.md)
