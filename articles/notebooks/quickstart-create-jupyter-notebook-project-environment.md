---
title: Tworzenie projektu Azure Notebooks w wersji zapoznawczej przy użyciu środowiska niestandardowego
description: Utwórz nowy projekt w Azure Notebooks wersji zapoznawczej, który jest skonfigurowany z określonym zestawem zainstalowanych pakietów i skryptów uruchamiania.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196845"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Szybki Start: Tworzenie projektu za pomocą środowiska niestandardowego w programie Azure Notebooks Preview

Projektu w notesach Azure to zbiór plików, takich jak notesów, pliki danych, dokumentacji, obrazy i tak dalej, oraz środowisko, które można skonfigurować za pomocą poleceń określonej konfiguracji. Definiując środowiska z projektem, każdy, kto klony projektu do własnego konta Azure, notesy zawiera wszystkie informacje, które są im potrzebne do odtworzenia wymagane operacje konfigurowania środowiska.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Tworzenie projektu

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję **+ Nowy projekt** (skrót klawiaturowy: n). przycisk może być wyświetlany tylko jako **+** , jeśli okno przeglądarki jest wąskie:

    ![Nowe polecenie projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym oknie podręcznym **Utwórz nowy projekt** wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Utwórz**:

    - **Nazwa projektu**: projekt ze środowiskiem niestandardowym
    - **Identyfikator projektu**: projekt — środowisko niestandardowe
    - **Projekt publiczny**: (wyczyszczone)
    - **Utwórz element Readme.MD**: (wyczyszczony)

1. Po kilku chwilach notesy platformy Azure przechodzi należy do nowego projektu. Dodaj Notes do projektu, wybierając listę rozwijaną **+ Nowy** (która może być wyświetlana tylko jako **+** ), a następnie wybierając pozycję **Notes**.

1. Nadaj notesowi nazwę, np *. Custom Environment. ipynb*, wybierz opcję **Python 3,6** dla języka i wybierz pozycję **New (nowy**).

## <a name="add-a-custom-setup-step"></a>Dodawanie kroku instalacji niestandardowej

1. Na stronie projekt wybierz pozycję **Ustawienia projektu**.

    ![Polecenie Ustawienia projektu](media/quickstarts/project-settings-command.png)

1. W oknie podręcznym **Ustawienia projektu** wybierz kartę **środowisko** , a następnie w obszarze **kroki konfiguracji środowiska**wybierz pozycję **+ Dodaj**:

    ![Polecenie, aby dodać nowy krok konfiguracji środowiska](media/quickstarts/environment-add-command.png)

1. Polecenie **+ Add** tworzy krok zdefiniowany przez operację i plik docelowy, który jest wybierany z plików w projekcie. Obsługiwane są następujące operacje:

   | Operacja | Opis |
   | --- | --- |
   | Plik Requirements.txt | Projekty Python definiowanie ich zależności w pliku requirements.txt. Po wybraniu tej opcji wybierz odpowiedni plik z listy plików projektu, a także wybierz wersję języka Python w dodatkowej listy rozwijanej wyświetlonej. W razie potrzeby wybierz pozycję **Anuluj** , aby powrócić do projektu, Przekaż lub Utwórz plik, a następnie wróć do karty **Ustawienia projektu** > **środowisko** i Utwórz nowy krok. W tym kroku uruchomienie notesu w projekcie powoduje automatyczne uruchomienie `pip install -r <file>` |
   | Skrypt powłoki | Użyj, aby wskazać skrypt powłoki bash (zazwyczaj plik z rozszerzeniem *. sh* ) zawierający wszystkie polecenia, które chcesz uruchomić w celu zainicjowania środowiska. |
   | Environment.yml | Projekt języka Python, który używa Conda do zarządzania środowiskiem, używa pliku Environments *. yml* do opisywania zależności. Po wybraniu tej opcji wybierz odpowiedni plik z listy plików projektu. |

   > [!WARNING]
   > Ponieważ jest to usługa w wersji zapoznawczej, jest obecnie znany problem, w którym ustawienie `Environment.yml` nie zostanie zastosowane do projektu zgodnie z oczekiwaniami. Projekty i notesy Jupyter w programie nie ładują teraz określonego pliku środowiska.

1. Aby usunąć dowolny krok instalacji, wybierz **znak X** z prawej strony kroku.

1. Po zakończeniu wszystkich kroków instalacji wybierz pozycję **Zapisz**. (Wybierz pozycję **Anuluj** , aby odrzucić zmiany).

1. Aby przetestować środowisko, Utwórz i uruchom nowy Notes, a następnie utwórz komórkę kodu z instrukcjami, które są zależne od pakietu w środowisku, na przykład przy użyciu instrukcji `import` języka Python. Jeśli instrukcja zakończy się powodzeniem, potrzebny pakiet został pomyślnie zainstalowany w środowisku.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie projektami i konfigurowanie ich w Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
