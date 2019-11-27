---
title: Tworzenie i udostępnianie notesu programu Jupyter na platformie Azure
description: Szybko Utwórz uruchamiania notesu programu Jupyter na notesy platformy Azure, a następnie udostępniać innym użytkownikom tego notesu.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 71220fa5aa0367d1cb1694582b4f96459a3016e7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277499"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Szybki Start: Tworzenie i udostępnianie notesu

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję **+ Nowy projekt** (skrót klawiaturowy: n). przycisk może być wyświetlany tylko jako **+** , jeśli okno przeglądarki jest wąskie:

    ![Nowe polecenie projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym oknie podręcznym **Utwórz nowy projekt** wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Utwórz**:

   - **Nazwa projektu**: Hello World w języku Python
   - **Identyfikator projektu**: Hello-World-Python
   - **Projekt publiczny**: (wyczyszczone)
   - **Utwórz element Readme.MD**: (wyczyszczony)

     ![Nowe popup projektu ze szczegółami wypełnione](media/quickstarts/new-project-popup.png)

1. Po kilku chwilach notesy platformy Azure przechodzi należy do nowego projektu. Dodaj Notes do projektu, wybierając listę rozwijaną **+ Nowy** (która może być wyświetlana tylko jako **+** ), a następnie wybierając opcję **Notes**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. W wyświetlonym oknie podręcznym **Utwórz nowy Notes** wprowadź nazwę pliku dla notesu, na przykład *HelloWorldInPython. ipynb* ( *. ipynb* oznacza Notes IronPython (Jupyter)), a następnie wybierz opcję **Python 3,6** dla języka (nazywanego również *jądrem*):

    ![Okno podręczne Tworzenie nowego notesu](media/quickstarts/new-notebook-popup.png)

1. Wybierz pozycję **Nowy** , aby zakończyć tworzenie notesu, który zostanie wyświetlony na liście plików projektu:

    ![Nowy notes pojawiające się na liście plików projektu](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Uruchamianie notesu

1. Wybierz nowy notes, aby uruchomić je w edytorze; jądra zaznaczone (Python 3.6 w tym przykładzie) jest aktywowana automatycznie dla tego notesu:

    ![Wyświetl nowy notes w notesach platformy Azure](media/quickstarts/create-notebook-first-open.png)

1. Domyślnie Notes ma jeden pustej komórce kodu. **Aby zmienić**typ komórki na rozbiciu, Użyj listy rozwijanej Typ komórki, **Aby wybrać opcję**przestawka:

    ![Zmiana typu komórki w nowego notesu](media/quickstarts/create-notebook-cell-type.png)

1. Wprowadź lub wklej następujący tekst nagłówka komórki:

    ```markdown
    # Hello World in Python
    ```

1. Ponieważ edytowania kodu Markdown, tekst jest wyświetlany jako nagłówek znakiem "#". Aby renderować tę wartość w formacie HTML, wybierz przycisk **Uruchom** . Notesy platformy Azure automatycznie utworzy nową komórkę kodu później:

    ![Przycisk uruchamiania dla komórki i renderowane znaczniki Markdown](media/quickstarts/run-cell-markdown-render.png)

1. W komórce kodu wprowadź poniższy kod języka Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Wybierz polecenie **Uruchom** (skrót klawiaturowy: SHIFT + ENTER), aby uruchomić kod. Poniżej komórki pomyślne dane wyjściowe powinny być podobne do następującego tekstu:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Wybierz opcję Zapisz ikonę, aby zapisać swoją pracę:

    ![Zapisz ikonę na pasku narzędzi notesu programu Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Wybierz polecenie menu **plik** > **Zamknij i** Zatrzymaj, aby zatrzymać serwer i zamknąć okno przeglądarki.

## <a name="share-the-notebook"></a>Udostępnianie notesu

Aby udostępnić Notes, w razie potrzeby przełącz się z powrotem do strony projektu, kliknij prawym przyciskiem myszy plik notesu, wybierz polecenie **Kopiuj link** (skrót klawiaturowy: t), a następnie wklej ten link do odpowiedniego komunikatu (wiadomości e-mail, wiadomości błyskawicznych itp.).

Na stronie projekt można również użyć menu **Udostępnij** , aby uzyskać link, utworzyć wiadomość e-mail z linkiem lub uzyskać kod osadzania HTML i promocji:

![Udostępnij projekt — polecenie](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
