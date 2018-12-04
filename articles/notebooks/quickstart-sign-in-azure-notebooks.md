---
title: Zaloguj się do notesy platformy Azure | Dokumentacja firmy Microsoft
description: Szybko zalogować się do platformy Azure, notesy i Ustaw identyfikator użytkownika, co daje możliwość dostępu do zapisanych projektów i udostępniać innym użytkownikom notesy.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 44513a63f3bc13592a5c7de2595149956597251c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856299"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>Zaloguj się do niego Szybki Start: I Ustaw identyfikator użytkownika

Mimo że można zawsze wyświetlić notesy platformy Azure, bez logowania, musisz zarejestrować się uruchamianie notesów, dostęp do zapisanych projektów i notesy i udostępniać innym użytkownikom notesów programu.

## <a name="sign-in"></a>Logowanie

1. Wybierz **Zaloguj** u góry po prawej stronie [notebooks.azure.com](https://notebooks.azure.com/).

    ![Lokalizacja polecenia Zaloguj się na notesy platformy Azure](media/accounts/sign-in-command.png)

1. Po wyświetleniu monitu wprowadź adres e-mail Account Microsoft pracy lub służbowe konto i wybierz **dalej**. Typy kont zostały opisane na [Twoje konto użytkownika dla usługi Azure notesów](azure-notebooks-user-account.md). Jeśli nie masz Account Microsoft lub chcesz wprowadzić jeden do użycia z notesów usługi Azure, wybierz opcję **utworzyć**:

    ![Utwórz nowe polecenie konta Microsoft w monit logowania](media/accounts/create-new-microsoft-account.png)

1. Wprowadź hasło po wyświetleniu monitu.

1. Jeśli logujesz się po raz pierwszy, notesów usługi Azure z pytaniem o zezwolenie na dostęp do tego konta. Wybierz **tak** aby kontynuować:

    ![Wiersz uprawnień konta](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Ustaw identyfikator użytkownika

1. Po pierwszym zalogowaniu masz przypisaną Identyfikatora użytkownika tymczasowych, takich jak "anon-idrca3". Zawsze, gdy masz identyfikator użytkownika, który rozpoczyna się od "anon-", notesy platformy Azure wyświetli monit o utworzenie własnego Identyfikatora. Identyfikator użytkownika jest używany dowolny adres URL uzyskany na udostępnianie projektów i notebooki, więc wybierz element, który jest unikatowy i zrozumiała dla Ciebie.

    ![Monit o podanie Identyfikatora użytkownika dla notesów usługi Azure](media/accounts/create-user-id.png)

    Jeśli wybierzesz **nie, dziękuję**, notesy platformy Azure w dalszym ciągu wyświetlenie monitu o nazwę użytkownika każdym zalogowaniu. W dowolnym momencie można również ustawić identyfikator użytkownika usługi [profilu użytkownika](azure-notebooks-user-profile.md).

1. Po pomyślnym zalogowaniu notesy platformy Azure przechodzi do strony swojego profilu publicznego, w którym można wybrać **edytować informacje o profilu** Wypełnij pozostałe informacje (Aby uzyskać więcej informacji, zobacz [swój profil i identyfikator użytkownika](azure-notebooks-user-profile.md)):

    ![Widok początkowy strony profilu notesy platformy Azure](media/accounts/profile-page-new.png)

## <a name="sign-out"></a>Wyloguj

1. Aby się wylogować, wybierz swoją nazwę użytkownika w prawym górnym rogu strony, a następnie wybierz **Wyloguj**:

    ![Lokalizacja polecenia wylogowania w notesy platformy Azure](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie i udostępnianie notesu](quickstart-create-share-jupyter-notebook.md)
