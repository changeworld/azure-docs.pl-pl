---
title: Tworzenie i udostępnianie notesu Jupyter na platformie Azure
description: Szybko Twórz i uruchamiaj Notes Jupyter na Azure Notebooks, a następnie Udostępnij go innym osobom.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 19e8217bf88427c353fdd4c90c9f421445b7c81a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970172"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Szybki Start: Tworzenie i udostępnianie notesu

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję **+ Nowy projekt** (skrót klawiaturowy: n). przycisk może być wyświetlany tylko jako **+** , jeśli okno przeglądarki jest wąskie:

    ![Polecenie nowego projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym oknie podręcznym **Utwórz nowy projekt** wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Utwórz**:

   - **Nazwa projektu**: Hello World w języku Python
   - **Identyfikator projektu**: Hello-World-Python
   - **Projekt publiczny**: (wyczyszczone)
   - **Utwórz element Readme.MD**: (wyczyszczony)

     ![Okno podręczne nowego projektu z wypełnionymi szczegółami](media/quickstarts/new-project-popup.png)

1. Po kilku chwilach Azure Notebooks przechodzi do nowego projektu. Dodaj Notes do projektu, wybierając listę rozwijaną **+ Nowy** (która może być wyświetlana tylko jako **+** ), a następnie wybierając opcję **Notes**:

    [![](media/quickstarts/empty-project-new-notebook-button.png#lightbox)(media/quickstarts/empty-project-new-notebook-button.png "Nowy, pusty projekt i Dodawanie notesu — polecenie")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. W wyświetlonym oknie podręcznym **Utwórz nowy Notes** wprowadź nazwę pliku dla notesu, na przykład *HelloWorldInPython. ipynb* ( *. ipynb* oznacza Notes IronPython (Jupyter)), a następnie wybierz opcję **Python 3,6** dla języka (nazywanego również *jądro*):

    ![Okno podręczne Tworzenie nowego notesu](media/quickstarts/new-notebook-popup.png)

1. Wybierz pozycję **Nowy** , aby zakończyć tworzenie notesu, który zostanie wyświetlony na liście plików projektu:

    ![Nowy Notes pojawiający się na liście plików projektu](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Uruchamianie notesu

1. Wybierz nowy Notes, który ma być uruchamiany w edytorze; wybrany jądro (Python 3,6 w tym przykładzie) jest automatycznie aktywowany dla tego notesu:

    ![Widok nowego notesu w Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Domyślnie Notes ma jedną pustą komórkę kodu. **Aby zmienić**typ komórki na rozbiciu, Użyj listy rozwijanej Typ komórki, **Aby wybrać opcję**przestawka:

    ![Zmiana typu komórki w nowym notesie](media/quickstarts/create-notebook-cell-type.png)

1. Wprowadź lub wklej następujący tekst nagłówka do komórki:

    ```markdown
    # Hello World in Python
    ```

1. Ponieważ edytujesz opcję promocji, tekst jest wyświetlany jako nagłówek z "#". Aby renderować tę wartość w formacie HTML, wybierz przycisk **Uruchom** . Azure Notebooks następnie automatycznie tworzy nową komórkę kodu:

    ![Przycisk Run dla komórki i renderowanej promocji](media/quickstarts/run-cell-markdown-render.png)

1. W komórce kod wprowadź następujący kod w języku Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Wybierz polecenie **Uruchom** (skrót klawiaturowy: SHIFT + ENTER), aby uruchomić kod. Poniżej komórki powinny być widoczne pomyślne dane wyjściowe podobne do następującego tekstu:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Wybierz ikonę Zapisz, aby zapisać swoją służbę:

    ![Ikona zapisywania na pasku narzędzi notesu Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Wybierz polecenie **menu @no__t-** 1**Zamknij i** Zatrzymaj, aby zatrzymać serwer i zamknąć okno przeglądarki.

## <a name="share-the-notebook"></a>Udostępnianie notesu

Aby udostępnić Notes, w razie potrzeby przełącz się z powrotem do strony projektu, kliknij prawym przyciskiem myszy plik notesu, wybierz polecenie **Kopiuj link** (skrót klawiaturowy: t), a następnie wklej ten link do odpowiedniego komunikatu (wiadomości e-mail, wiadomości błyskawicznych itp.).

Na stronie projekt można również użyć menu **Udostępnij** , aby uzyskać link, utworzyć wiadomość e-mail z linkiem lub uzyskać kod osadzania HTML i promocji:

![Project Share — polecenie](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
