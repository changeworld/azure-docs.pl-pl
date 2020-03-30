---
title: Tworzenie projektu usługi Azure Notebooks Preview w środowisku niestandardowym
description: Utwórz nowy projekt w usłudze Azure Notebooks Preview, który jest skonfigurowany z określonym zestawem zainstalowanych pakietów i skryptów startowych.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78196845"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Szybki start: tworzenie projektu w środowisku niestandardowym w usłudze Azure Notebooks Preview

Projekt w notesach platformy Azure to zbiór plików, takich jak notesy, pliki danych, dokumentacja, obrazy itd., wraz ze środowiskiem, które można skonfigurować za pomocą określonych poleceń konfiguracji. Definiując środowisko z projektem, każdy, kto klonuje projekt do własnego konta notesów platformy Azure ma wszystkie informacje potrzebne do odtworzenia niezbędnego środowiska.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Tworzenie projektu

1. Przejdź do [usługi Azure Notess](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki start — logowanie się do notesów platformy Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na stronie profilu publicznego wybierz **pozycję Moje projekty** u góry strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję + **Nowy projekt** (skrót klawiaturowy: n); przycisk może pojawić **+** się tak, jakby okno przeglądarki było wąskie:

    ![Polecenie Nowy projekt na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym **wyskakującym** okienku Utwórz nowy projekt wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Utwórz:**

    - **Nazwa projektu**: Projekt ze środowiskiem niestandardowym
    - **Identyfikator projektu:** projekt-niestandardowe środowisko
    - **Projekt publiczny**: (wyczyszczone)
    - **Tworzenie README.md**: (wyczyszczone)

1. Po kilku chwilach notesy platformy Azure przechodzi do nowego projektu. Dodaj notes do projektu, wybierając pozycję rozwijaną **+ Nowy** (który może być wyświetlany tylko **+** jako ), a następnie wybierając pozycję **Notes**.

1. Nadaj notesowi nazwę, taką jak *Niestandardowe środowisko.ipynb*, wybierz **python 3.6** dla języka i wybierz pozycję **Nowy**.

## <a name="add-a-custom-setup-step"></a>Dodawanie niestandardowego kroku konfiguracji

1. Na stronie projektu wybierz pozycję **Ustawienia projektu**.

    ![Polecenie Ustawienia projektu](media/quickstarts/project-settings-command.png)

1. W oknie **podręcznym Ustawienia projektu** wybierz kartę **Środowisko,** a następnie w obszarze **Kroki konfiguracji środowiska**wybierz pozycję + **Dodaj**:

    ![Polecenie, aby dodać nowy krok konfiguracji środowiska](media/quickstarts/environment-add-command.png)

1. Polecenie **+ Dodaj** tworzy krok zdefiniowany przez operację i plik docelowy wybrany z plików w projekcie. Obsługiwane są następujące operacje:

   | Operacja | Opis |
   | --- | --- |
   | Requirements.txt | Projekty języka Python definiują ich zależności w pliku requirements.txt. Za pomocą tej opcji wybierz odpowiedni plik z listy plików projektu, a także wybierz wersję języka Python w dodatkowym liście rozwijanej, która się pojawi. W razie potrzeby wybierz **pozycję Anuluj,** aby powrócić do projektu, przekaż lub utwórz plik, a następnie wróć do karty**Środowisko** **ustawień** > projektu i utwórz nowy krok. Po tym kroku uruchomienie notesu w projekcie jest automatycznie uruchamiane`pip install -r <file>` |
   | Skrypt powłoki | Służy do wskazania skryptu powłoki bash (zazwyczaj pliku z rozszerzeniem *.sh),* który zawiera wszystkie polecenia, które chcesz uruchomić w celu zainicjowania środowiska. |
   | Środowisko.yml | Projekt języka Python, który używa conda do zarządzania środowiskiem używa pliku *environments.yml* do opisywania zależności. Za pomocą tej opcji wybierz odpowiedni plik z listy plików projektu. |

   > [!WARNING]
   > Ponieważ jest to usługa w wersji zapoznawczej w `Environment.yml` fazie rozwoju, obecnie istnieje znany problem, w którym ustawienie nie zostanie zastosowane do projektu zgodnie z oczekiwaniami. Projekt i notesy Jupyter wewnątrz nie ładują pliku określonego środowiska w chwili obecnej.

1. Aby usunąć dowolny krok konfiguracji, wybierz **x** po prawej stronie kroku.

1. Gdy wszystkie kroki konfiguracji są na swoim miejscu, wybierz pozycję **Zapisz**. (Wybierz **anuluj,** aby odrzucić zmiany).

1. Aby przetestować środowisko, utwórz i uruchom nowy notes, a następnie utwórz komórkę kodu `import` z instrukcjami, które zależą od pakietu w środowisku, na przykład przy użyciu instrukcji Języka Python. Jeśli instrukcja powiedzie się, niezbędny pakiet został pomyślnie zainstalowany w środowisku.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie projektami i konfigurowanie ich w notesach platformy Azure](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Samouczek: tworzenie uruchamiania notesu Jupyter do regresji liniowej](tutorial-create-run-jupyter-notebook.md)
