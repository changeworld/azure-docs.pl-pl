---
title: Zaloguj się do wersji zapoznawczej Azure Notebooks
description: Szybko zaloguj się do Azure Notebooks wersja zapoznawcza i ustaw identyfikator użytkownika, który umożliwia dostęp do zapisanych projektów i udostępnianie notesów innym osobom.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: b6572a7c0b965b2b72916db577b47eff4f1921c0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647020"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Szybki Start: Logowanie i Ustawianie identyfikatora użytkownika na potrzeby wersji zapoznawczej Azure Notebooks

Mimo że można zawsze wyświetlić notesy platformy Azure, bez logowania, musisz zarejestrować się uruchamianie notesów, dostęp do zapisanych projektów i notesy i udostępniać innym użytkownikom notesów programu.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="sign-in"></a>Zaloguj

1. Wybierz **Zaloguj** u góry po prawej stronie [notebooks.azure.com](https://notebooks.azure.com/).

    ![Lokalizacja polecenia Zaloguj się na notesy platformy Azure](media/accounts/sign-in-command.png)

1. Po wyświetleniu monitu wprowadź adres e-mail Account Microsoft pracy lub służbowe konto i wybierz **dalej**. Typy kont zostały opisane na [Twoje konto użytkownika dla usługi Azure notesów](azure-notebooks-user-account.md). Jeśli nie masz Account Microsoft lub chcesz wprowadzić jeden do użycia z notesów usługi Azure, wybierz opcję **utworzyć**:

    ![Utwórz nowe polecenie konta Microsoft w monit logowania](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Jeśli spróbujesz utworzyć nowe konto przy użyciu adresu e-mail, z którym jest już skojarzone konto, może zostać wyświetlony komunikat "nie można utworzyć konta w tym miejscu przy użyciu służbowego adresu e-mail. Użyj osobistej poczty e-mail, takiej jak Gmail lub Yahoo!, lub Utwórz nową wiadomość e-mail w programie Outlook. W takim przypadku spróbuj zalogować się przy użyciu służbowego adresu e-mail bez tworzenia nowego konta.

1. Wprowadź hasło po wyświetleniu monitu.

1. Jeśli logujesz się po raz pierwszy, notesów usługi Azure z pytaniem o zezwolenie na dostęp do tego konta. Wybierz **tak** aby kontynuować:

    ![Wiersz uprawnień konta](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Ustaw identyfikator użytkownika

1. Po pierwszym zalogowaniu masz przypisaną Identyfikatora użytkownika tymczasowych, takich jak "anon-idrca3". Zawsze, gdy masz identyfikator użytkownika, który rozpoczyna się od "anon-", notesy platformy Azure wyświetli monit o utworzenie własnego Identyfikatora. Identyfikator użytkownika jest używany dowolny adres URL uzyskany na udostępnianie projektów i notebooki, więc wybierz element, który jest unikatowy i zrozumiała dla Ciebie.

    ![Monit o podanie Identyfikatora użytkownika dla notesów usługi Azure](media/accounts/create-user-id.png)

    Jeśli wybierzesz **nie, dziękuję**, notesy platformy Azure w dalszym ciągu wyświetlenie monitu o nazwę użytkownika każdym zalogowaniu. W dowolnym momencie można również ustawić identyfikator użytkownika usługi [profilu użytkownika](azure-notebooks-user-profile.md).

1. Po pomyślnym zalogowaniu notesy platformy Azure przechodzi do strony swojego profilu publicznego, w którym można wybrać **edytować informacje o profilu** Wypełnij pozostałe informacje (Aby uzyskać więcej informacji, zobacz [swój profil i identyfikator użytkownika](azure-notebooks-user-profile.md)):

    ![Widok początkowy strony profilu notesy platformy Azure](media/accounts/profile-page-new.png)

> [!NOTE]
> Jeśli zobaczysz komunikat "identyfikator użytkownika jest już używany", spróbuj użyć innego identyfikatora. Identyfikatory użytkowników są unikatowe dla wszystkich kont Azure Notebooks, a Azure Notebooks również rezerwują pewne identyfikatory użytkowników, takie jak nazwy marki firmy Microsoft.

## <a name="sign-out"></a>Wyloguj

Aby się wylogować, wybierz swoją nazwę użytkownika w prawym górnym rogu strony, a następnie wybierz **Wyloguj**:

![Lokalizacja polecenia wylogowania w notesy platformy Azure](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie i udostępnianie notesu](quickstart-create-share-jupyter-notebook.md)
