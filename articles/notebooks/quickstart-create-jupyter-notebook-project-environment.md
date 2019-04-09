---
title: Tworzenie projektu notesy platformy Azure w środowisku niestandardowym
description: Utwórz nowy projekt w notesach platformy Azure, który jest skonfigurowany przy użyciu określonych pakietów zainstalowanych i uruchamiania skryptów.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 368bc0402e56479ad8696ca9ed0702f246a670bd
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262010"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Szybki start: Tworzenie projektu w środowisku niestandardowym

Projektu w notesach Azure to zbiór plików, takich jak notesów, pliki danych, dokumentacji, obrazy i tak dalej, oraz środowisko, które można skonfigurować za pomocą poleceń określonej konfiguracji. Definiując środowiska z projektem, każdy, kto klony projektu do własnego konta Azure, notesy zawiera wszystkie informacje, które są im potrzebne do odtworzenia wymagane operacje konfigurowania środowiska.

## <a name="create-a-project"></a>Tworzenie projektu

1. Przejdź do [notesów usługi Azure](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać więcej informacji, zobacz [Szybki Start — Zaloguj się do platformy Azure, notesy](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie profilu publicznego, wybierz **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na **Moje projekty** wybierz opcję **+ nowy projekt** (skrót klawiaturowy: n); przycisku może występować tylko jako **+** Jeśli okno przeglądarki jest wąskie:

    ![Nowe polecenie projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W **Utwórz nowy projekt** okna podręcznego, który pojawia się, wprowadź lub ustaw następujące szczegóły, a następnie wybierz **Utwórz**:

    - **Nazwa projektu**: Projekt w środowisku niestandardowym
    - **Identyfikator projektu**: projekt niestandardowe środowiska
    - **Projekt publicznych**: (usunięty)
    - **Utwórz plik README.md**: (usunięty)

1. Po kilku chwilach notesy platformy Azure przechodzi należy do nowego projektu. Dodaj Notes do projektu, wybierając **+ nowy** listy rozwijanej (może wystąpić tylko jako **+**), a następnie wybierając pozycję **notesu**.

1. Nazwij Notes takich jak *environment.ipynb niestandardowe*, wybierz opcję **środowiska Python 3.6** dla języka, a następnie wybierz **New**.

## <a name="add-a-custom-setup-step"></a>Dodawanie kroku instalacji niestandardowej

1. Na stronie projektu wybierz **ustawienia projektu**.

    ![Polecenie Ustawienia projektu](media/quickstarts/project-settings-command.png)

1. W **ustawienia projektu** podręcznym wybierz **środowiska** kartę, a następnie w obszarze **kroków konfiguracji środowiska**, wybierz opcję **+ Dodaj**:

    ![Polecenie, aby dodać nowy krok konfiguracji środowiska](media/quickstarts/environment-add-command.png)

1. **+ Dodaj** polecenie tworzy krok, który jest definiowany przez operację i pliku docelowego, który jest wybrana w zaufanym pliki w projekcie. Obsługiwane są następujące operacje:

    | Operacja | Opis |
    | --- | --- |
    | Plik Requirements.txt | Projekty Python definiowanie ich zależności w pliku requirements.txt. Po wybraniu tej opcji wybierz odpowiedni plik z listy plików projektu, a także wybierz wersję języka Python w dodatkowej listy rozwijanej wyświetlonej. W razie potrzeby zaznacz **anulować** aby powrócić do projektu, przekazywania lub utworzyć plik, a następnie wróć do **ustawienia projektu** > **środowiska** kartę i Utwórz nowy krok. Ten krok w miejscu automatycznemu uruchamianiu Notes w projekcie działa `pip install -r <file>` |
    | Skrypt powłoki | Służy do wskazywania skrypt powłoki bash (zazwyczaj plik z *SH* rozszerzenia) zawierający wszystkie polecenia, które chcesz uruchomić, aby zainicjować środowisko. |
    | Environment.yml | Projekt języka Python, który używa conda dla zarządzania środowiskiem używa *environments.yml* pliku, aby opisać zależności. Po wybraniu tej opcji wybierz odpowiedni plik z listy plików projektu. |

1. Aby usunąć dowolny krok konfiguracji, wybierz **X** po prawej stronie kroku.

1. Gdy wszystkie kroki instalacji znajdują się w miejscu, wybierz pozycję **Zapisz**. (Wybierz **anulować** aby odrzucić zmiany).

1. Do Twojego środowiska testowego, Utwórz i uruchom nowy notes, a następnie tworzenie komórkę kodu za pomocą instrukcji, które są zależne od pakietu w środowisku, na przykład przy użyciu języka Python `import` instrukcji. Jeśli instrukcja zakończy się powodzeniem, potrzebny pakiet został pomyślnie zainstalowany w środowisku.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zarządzanie i konfigurowanie projektów w notesach platformy Azure](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Samouczek: tworzenie uruchomienia notesu programu Jupyter w celu regresji liniowej](tutorial-create-run-jupyter-notebook.md)
