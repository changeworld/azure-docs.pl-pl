---
title: Migrowanie lokalnego notesu programu Jupyter do notesów usługi Azure
description: Szybko przenieść notesu programu Jupyter do notesów usługi Azure z komputera lokalnego lub adres URL sieci web, a następnie udostępnić go do współpracy.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 7df64c3fb70bdf3e7689787ec558bfe0e4942352
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754011"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Szybki start: Migrowanie z lokalnego notesu Jupyter

Notesy Jupyter notebook tworzące lokalnie na własnym komputerze są dostępne tylko dla Ciebie. Można udostępniać swoje pliki przy użyciu różnych oznacza, ale następnie adresaci mają własne lokalną kopię Notes i trudno uwzględniać wszelkie zmiany, których mogą wybierać. Można również przechowywać notesów w współdzielonego repozytorium online, takich jak GitHub, ale nadal wymaga czy każdy Współautor, który ma swoje własne lokalnej instalacji programu Jupyter, z taką samą konfigurację jak należy do Ciebie.

Przy użyciu funkcji migracji notesy lokalnych lub opartych na repozytorium do notesów usługi Azure, przechowywane w chmurze, z którego można natychmiast udostępnisz je innym swoje współpracowników. Tylko przeglądarka do wyświetlania i uruchamiania notesu, niezbędna tych współpracowników i, jeśli ich [Zaloguj](quickstart-sign-in-azure-notebooks.md) do notesów usługi Azure może również wprowadzić zmiany.

Ten przewodnik Szybki Start procesu migracji notesu z komputera lokalnego lub innego pliku dostępnego adresu URL. Aby przeprowadzić migrację notesów z repozytorium GitHub, zobacz [Szybki Start: Klonowanie notesu](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Utwórz projekt w notesach platformy Azure

1. Przejdź do [notesów usługi Azure](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać więcej informacji, zobacz [Szybki Start — Zaloguj się do platformy Azure, notesy](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie profilu publicznego, wybierz **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na **Moje projekty** wybierz opcję **+ nowy projekt** (skrót klawiaturowy: n); przycisku może występować tylko jako **+** Jeśli okno przeglądarki jest wąskie:

    ![Nowe polecenie projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W **Utwórz nowy projekt** okna podręcznego, który pojawia się, wprowadź odpowiednie wartości dla notesu migracja jest przeprowadzana w **Nazwa projektu** i **identyfikator projektu** pól, usuń zaznaczenie Opcje dla **publicznego projektu** i **Utwórz plik README.md**, a następnie wybierz **Utwórz**.

## <a name="upload-the-local-notebook"></a>Przekazywanie lokalnego notesu

1. Na stronie projektu wybierz **przekazywanie** (które mogą pojawić się jako się strzałka tylko wtedy, gdy okno przeglądarki jest mała), następnie wybierz opcję 1. W wyświetlonym oknie podręcznym, zaznacz **z komputera** Jeśli notes jest umieszczony w lokalnym systemie plików, lub **z adresu URL** Jeśli notes jest znajduje się w trybie online:

    ![Polecenie, aby przekazać Notes z adresem URL lub na komputerze lokalnym](media/quickstarts/upload-from-computer-url-command.png)

   (Ponownie, jeśli notesu znajduje się w repozytorium GitHub, postępuj zgodnie z instrukcjami [Szybki Start: Klonowanie notesu](quickstart-clone-jupyter-notebook.md) zamiast.)

   - Jeśli przy użyciu **z komputera**, przeciągnij i upuść swoje *.ipynb* pliki do okna podręcznego, lub wybierz **Wybieranie plików**, a następnie wyszukaj i wybierz pliki, które chcesz zaimportować. Następnie wybierz pozycję **przekazywanie**. Przekazane pliki znajdują się taką samą nazwę jak pliki lokalne. (Nie trzeba przekazać zawartość dowolnego *.ipynb_checkpoints* folderów.)

     ![Przekaż z komputera okna podręcznego](media/quickstarts/upload-from-computer-popup.png)

   - Jeśli przy użyciu **z adresu URL**, wprowadź adres źródła w **adres URL pliku** pola i nazwę pliku, aby przypisać do Notes w projekcie w **nazwy pliku** pola. Następnie wybierz pozycję **przekazywanie**. Jeśli masz wiele plików przy użyciu osobnych adresów URL, użyj **+ Dodaj plik** polecenie, aby sprawdzić pierwszy adres URL wprowadzony, po upływie którego okno podręczne zawiera nowe pola do innego pliku.

     ![Przekaż z adresu URL okna podręcznego](media/quickstarts/upload-from-url-popup.png)

1. Otwórz i uruchom notesu nowo przesłanym, aby sprawdzić jej zawartość i operacji. Gdy wszystko będzie gotowe, wybierz pozycję **pliku** > **zatrzymania, a następnie Zamknij** zamknięcie notesu.

1. Aby udostępnić łącze do notesu przekazany, kliknij prawym przyciskiem myszy plik w projekcie i wybierz **Kopiuj Link** (skrót klawiaturowy: y), następnie wkleić ten link do odpowiedni komunikat. Alternatywnie można udostępnić projektu jako całość przy użyciu **udostępnianie** kontrolki na stronie projektu.

1. Aby edytować pliki inne niż notesów, kliknij prawym przyciskiem myszy plik w projekcie i wybierz **Edytuj plik** (skrót klawiaturowy: czy mogę). Domyślna akcja **Uruchom** (skrót klawiaturowy: r), tylko pokazuje zawartość pliku i nie zezwala na edycję.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: tworzenie uruchomienia notesu programu Jupyter w celu regresji liniowej](tutorial-create-run-jupyter-notebook.md)
