---
title: Migrowanie lokalnego notesu Jupyter do Azure Notebooks
description: Szybko Przenieś Notes Jupyter do Azure Notebooks z komputera lokalnego lub adresu URL sieci Web, a następnie Udostępnij go do współpracy.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2151d5b04b0fe5d47e8e2ddfb750ac279b6f74fa
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970183"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Szybki Start: Migrowanie lokalnego notesu Jupyter

Notesy Jupyter tworzone lokalnie na własnym komputerze są dostępne tylko dla Ciebie. Pliki można udostępniać za pomocą różnych metod, ale odbiorcy mają własną lokalną kopię notesu i trudno jest wprowadzić wszelkie zmiany, które mogą wprowadzić. Możesz również przechowywać notesy w udostępnionym repozytorium online, takim jak GitHub, ale nadal wymaga to, aby każdy współpracownik miał własną lokalną instalację Jupyter z taką samą konfiguracją jak dla Ciebie.

Migrowanie notesów lokalnych lub opartych na repozytorium do Azure Notebooks, przechowujesz je w chmurze, z których możesz natychmiast udostępnić je współpracownikom. Ci współpracownicy potrzebują tylko przeglądarki w celu wyświetlenia i uruchomienia notesu, a jeśli [zalogują](quickstart-sign-in-azure-notebooks.md) się do Azure Notebooks mogą także wprowadzać zmiany.

Ten przewodnik Szybki Start przedstawia proces migrowania notesu z komputera lokalnego lub innego dostępnego adresu URL pliku. Aby przeprowadzić migrację notesów z repozytorium GitHub, zobacz [Szybki Start: klonowanie notesu](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Utwórz projekt na Azure Notebooks

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję **+ Nowy projekt** (skrót klawiaturowy: n). przycisk może być wyświetlany tylko jako **+** , jeśli okno przeglądarki jest wąskie:

    ![Polecenie nowego projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym oknie podręcznym **Utwórz nowy projekt** wprowadź odpowiednie wartości dla notesu migrowanego w polach **Nazwa projektu** i **Identyfikator projektu** , usuń zaznaczenie opcji **projekt publiczny** i **Utwórz Readme.MD**, a następnie Wybierz pozycję **Utwórz**.

## <a name="upload-the-local-notebook"></a>Przekazywanie lokalnego notesu

1. Na stronie projekt wybierz pozycję **Przekaż** (która może pojawić się jako strzałka w górę tylko wtedy, gdy okno przeglądarki jest małe), a następnie wybierz pozycję 1. W wyświetlonym oknie podręcznym wybierz pozycję **z komputera** , jeśli Notes znajduje się w lokalnym systemie plików lub **z adresu URL** , jeśli Notes znajduje się w trybie online:

    ![Polecenie przekazania notesu z adresu URL lub komputera lokalnego](media/quickstarts/upload-from-computer-url-command.png)

   (Jeśli Notes znajduje się w repozytorium GitHub, wykonaj kroki opisane w sekcji [Szybki Start: klonowanie notesu](quickstart-clone-jupyter-notebook.md) ).

   - Jeśli używasz **z komputera**, przeciągnij i upuść pliki *. ipynb* w menu podręcznym lub wybierz pozycję **Wybierz pliki**, a następnie wyszukaj i wybierz pliki, które chcesz zaimportować. Następnie wybierz pozycję **Przekaż**. Przekazane pliki mają taką samą nazwę jak pliki lokalne. (Nie musisz przekazywać zawartości żadnych folderów *. ipynb_checkpoints* ).

     ![Okno podręczne przekazywania z komputera](media/quickstarts/upload-from-computer-popup.png)

   - W przypadku używania **z adresu URL**wprowadź adres źródłowy w polu **adres URL pliku** i nazwę pliku do przypisania do notesu w projekcie w polu **Nazwa pliku** . Następnie wybierz pozycję **Przekaż**. Jeśli masz wiele plików z oddzielnymi adresami URL, użyj polecenia **+ Dodaj plik** do sprawdzenia pierwszego WPROWADZONEGO adresu URL, po którym okno podręczne zawiera nowe pola dla innego pliku.

     ![Okno podręczne przekazywania z adresu URL](media/quickstarts/upload-from-url-popup.png)

1. Otwórz i Uruchom nowo przekazany Notes, aby sprawdzić jego zawartość i działanie. Gdy skończysz, wybierz pozycję **plik** > **zatrzymać i Zamknij** , aby zamknąć Notes.

1. Aby udostępnić link do przekazanego notesu, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Kopiuj link** (skrót klawiaturowy: y), a następnie wklej ten link do odpowiedniego komunikatu. Alternatywnie można udostępnić projekt jako całość przy użyciu kontrolki **Udostępnij** na stronie projektu.

1. Aby edytować pliki inne niż notesy, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Edytuj plik** (skrót klawiaturowy: i). Akcja domyślna, **Uruchom** (skrót klawiaturowy: r), wyświetla tylko zawartość pliku i nie zezwala na edytowanie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
