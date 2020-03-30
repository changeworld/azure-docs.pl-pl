---
title: Tworzenie i udostępnianie notesu jupyter w usłudze Azure Notebooks Preview
description: Szybko utwórz i uruchom notes Jupyter w usłudze Azure Notebooks Preview, a następnie udostępnij go innym osobom.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064448"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Szybki start: tworzenie notesu i udostępnianie go w usłudze Azure Notebooks Preview

W tym przewodniku Szybki start utworzysz i uruchomisz notes Jupyter w notesach platformy Azure, a następnie udostępnisz go innym osobom. Jupyter umożliwia łatwe łączenie tekstu Markdown, kodu wykonywalnego, trwałych danych, grafiki i wizualizacji na jednym kanwie, w tym notesie. Azure Notebooks to bezpłatna usługa hostowana umożliwiająca tworzenie i uruchamianie notesów Jupyter w chmurze bez instalacji.

## <a name="prerequisites"></a>Wymagania wstępne
Brak.

## <a name="create-a-new-project-and-notebook"></a>Tworzenie nowego projektu i notesu

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Przejdź do [witryny notesówhttps://notebooks.azure.com) platformy Azure (](https://notebooks.azure.com) i zaloguj się. Aby uzyskać szczegółowe informacje, zobacz [Szybki start — logowanie się do notesów platformy Azure](quickstart-sign-in-azure-notebooks.md).

1. Na stronie profilu publicznego wybierz **pozycję Moje projekty** u góry strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję + **Nowy projekt** (skrót klawiaturowy: n). Przycisk może pojawić **+** się tak, jakby okno przeglądarki było wąskie:

    ![Polecenie Nowy projekt na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym **wyskakującym** okienku Utwórz nowy projekt wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Utwórz:**

   - **Nazwa projektu**: Hello World in Python
   - **Identyfikator projektu**: hello-world-python
   - **Projekt publiczny**: (wyczyszczone)
   - **Tworzenie README.md**: (wyczyszczone)

     ![Nowe okno podręczne projektu z wypełnionymi szczegółami](media/quickstarts/new-project-popup.png)

1. Po kilku chwilach notesy platformy Azure przechodzi do nowego projektu. Dodaj notes do projektu, wybierając pozycję rozwijaną **+ Nowy** (który może być wyświetlany tylko **+** jako ), a następnie wybierając pozycję **Notes:**

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. W wyświetlonym **wyskakującym** okienku Utwórz nowy notes wprowadź nazwę pliku notesu, takiego jak *HelloWorldInPython.ipynb* (*.ipynb* oznacza Notatnik IronPython (Jupyter) i wybierz **python 3.6** dla języka (określanego również jako *jądro):*

    ![Okno podręczne Tworzenie nowego notesu](media/quickstarts/new-notebook-popup.png)

1. Wybierz **pozycję Nowy,** aby zakończyć tworzenie notesu, który następnie pojawi się na liście plików projektu:

    ![Nowy notes wyświetlany na liście plików projektu](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Uruchamianie notesu

1. Wybierz nowy notes, aby uruchomić go w edytorze; wybrane jądro (Python 3.6 w tym przykładzie) jest automatycznie aktywowane dla tego notesu:

    ![Wyświetlanie nowego notesu w notesach platformy Azure](media/quickstarts/create-notebook-first-open.png)

1. Domyślnie notes ma jedną pustą komórkę kodu. Aby zmienić typ komórki na **Markdown,** użyj listy rozwijanej typu komórki, aby wybrać **opcję Markdown:**

    ![Zmienianie typu komórki w nowym notesie](media/quickstarts/create-notebook-cell-type.png)

1. Wprowadź lub wklej następujący tekst nagłówka do komórki:

    ```markdown
    # Hello World in Python
    ```

1. Ponieważ edytujesz Markdown, tekst jest wyświetlany jako nagłówek z "#". Aby renderować markdown do kodu HTML, wybierz przycisk **Uruchom.** Następnie notesy platformy Azure automatycznie tworzą nową komórkę kodu:

    ![Przycisk uruchamiania komórki i renderowania znacznika](media/quickstarts/run-cell-markdown-render.png)

1. W komórce kodu wprowadź następujący kod języka Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Wybierz **pozycję Uruchom** (skrót klawiaturowy: Shift+Enter), aby uruchomić kod. Pod komórką powinno być widoczne pomyślne dane wyjściowe podobne do następującego tekstu:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Wybierz ikonę zapisz, aby zapisać swoją pracę:

    ![Ikona Zapisywania na pasku narzędzi notesu Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Wybierz polecenie menu **Zamknij** > **plik i Zatrzymaj,** aby zatrzymać serwer i zamknąć okno przeglądarki.

## <a name="share-the-notebook"></a>Udostępnianie notesu

Aby udostępnić notes, w razie potrzeby przełącz się z powrotem do strony projektu, kliknij prawym przyciskiem myszy plik notesu, wybierz polecenie **Kopiuj łącze** (skrót klawiaturowy: y) i wklej to łącze do odpowiedniej wiadomości (wiadomości e-mail, wiadomości błyskawiczne itp.).

Na stronie projektu można również użyć menu **Udostępnij,** aby uzyskać łącze, utworzyć wiadomość e-mail z łączem lub uzyskać kod osadzania HTML i Markdown:

![Polecenie Udział projektu](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i uruchamianie notebooka Jupyter do regresji liniowej](tutorial-create-run-jupyter-notebook.md)
