---
title: Tworzenie i udostępnianie notesu programu Jupyter na platformie Azure
description: Szybko Utwórz uruchamiania notesu programu Jupyter na notesy platformy Azure, a następnie udostępniać innym użytkownikom tego notesu.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b1618e2ac997445606ce98fc72a1ec35ca1280be
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273672"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Szybki start: Tworzenie i udostępnianie notesu

1. Przejdź do [notesów usługi Azure](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać więcej informacji, zobacz [Szybki Start — Zaloguj się do platformy Azure, notesy](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie profilu publicznego, wybierz **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na **Moje projekty** wybierz opcję **+ nowy projekt** (skrót klawiaturowy: n); przycisku może występować tylko jako **+** Jeśli okno przeglądarki jest wąskie:

    ![Nowe polecenie projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W **Utwórz nowy projekt** okna podręcznego, który pojawia się, wprowadź lub ustaw następujące szczegóły, a następnie wybierz **Utwórz**:

   - **Nazwa projektu**: Hello World w języku Python
   - **Identyfikator projektu**: hello world python
   - **Projekt publicznych**: (usunięty)
   - **Utwórz plik README.md**: (usunięty)

     ![Nowe popup projektu ze szczegółami wypełnione](media/quickstarts/new-project-popup.png)

1. Po kilku chwilach notesy platformy Azure przechodzi należy do nowego projektu. Dodaj Notes do projektu, wybierając **+ nowy** listy rozwijanej (może wystąpić tylko jako **+**), a następnie wybierając pozycję **notesu**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "Nowy, pusty projekt i dodać polecenie notesu")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. W **Utwórz nowy notes** okna podręcznego, który pojawia się, wprowadź nazwę notesu, takich jak *HelloWorldInPython.ipynb* (*.ipynb* oznacza notesu IronPython (Jupyter) ) i wybierz **środowiska Python 3.6** języka (nazywane także *jądra*):

    ![Okno podręczne Tworzenie nowego notesu](media/quickstarts/new-notebook-popup.png)

1. Wybierz **New** aby zakończyć tworzenie notesu, który pojawi się na liście plików projektu:

    ![Nowy notes pojawiające się na liście plików projektu](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Uruchamianie notesu

1. Wybierz nowy notes, aby uruchomić je w edytorze; jądra zaznaczone (Python 3.6 w tym przykładzie) jest aktywowana automatycznie dla tego notesu:

    ![Wyświetl nowy notes w notesach platformy Azure](media/quickstarts/create-notebook-first-open.png)

1. Domyślnie Notes ma jeden pustej komórce kodu. Aby zmienić typ komórki do **Markdown**, użyj komórki typu listy rozwijanej, aby wybrać **Markdown**:

    ![Zmiana typu komórki w nowego notesu](media/quickstarts/create-notebook-cell-type.png)

1. Wprowadź lub wklej następujący tekst nagłówka komórki:

    ```markdown
    # Hello World in Python
    ```

1. Ponieważ edytowania kodu Markdown, tekst jest wyświetlany jako nagłówek znakiem "#". Aby renderować języku znaczników Markdown w kodzie HTML, wybierz **Uruchom** przycisku. Notesy platformy Azure automatycznie utworzy nową komórkę kodu później:

    ![Przycisk uruchamiania dla komórki i renderowane znaczniki Markdown](media/quickstarts/run-cell-markdown-render.png)

1. W komórce kodu wprowadź poniższy kod języka Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Wybierz **Uruchom** (skrót klawiaturowy: Shift + Enter) do uruchomienia kodu. Poniżej komórki pomyślne dane wyjściowe powinny być podobne do następującego tekstu:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Wybierz opcję Zapisz ikonę, aby zapisać swoją pracę:

    ![Zapisz ikonę na pasku narzędzi notesu programu Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Wybierz **pliku** > **Zamknij i zatrzymanie** polecenie menu, aby zatrzymać serwer, a następnie zamknij okno przeglądarki.

## <a name="share-the-notebook"></a>Udostępnianie notesu

Aby udostępnić notesu, przejdź z powrotem do strony projektu w razie potrzeby, kliknij prawym przyciskiem myszy plik Notes, wybierz **Kopiuj Link** (skrót klawiaturowy: y) i wkleić ten link do odpowiedni komunikat (wiadomość e-mail, wiadomości Błyskawicznych itp.).

Na stronie projektu można również użyć **udziału** kod osadzania, menu, aby uzyskać link, Utwórz wiadomość e-mail z linkiem lub uzyskać HTML i języka Markdown:

![Udostępnij projekt — polecenie](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: tworzenie uruchomienia notesu programu Jupyter w celu regresji liniowej](tutorial-create-run-jupyter-notebook.md)
