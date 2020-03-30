---
title: Migrowanie lokalnego notesu Jupyter do usługi Azure Notebooks Preview
description: Szybko przenieś notes Jupyter do usługi Azure Notebooks Preview z komputera lokalnego lub adresu URL sieci Web, a następnie udostępnij go do współpracy.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064329"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Szybki start: migrowanie lokalnego notesu Jupyter w usłudze Azure Notebooks Preview

W tym przewodniku Szybki start można przeprowadzić migrację notesu Jupyter z komputera lokalnego lub innego adresu URL pliku z ułatwieniami dostępu do notesów platformy Azure. 

Notebooki Jupyter na własnym komputerze są dostępne tylko dla Ciebie. Pliki można udostępniać, ale odbiorcy mają wtedy własne lokalne kopie notesu i trudno jest uwzględnić ich zmiany. Nawet jeśli przechowujesz notesy w udostępnionym repozytorium online, takim jak GitHub, każdy współpracownik musi mieć skonfigurowaną lokalną instalację Jupytera tak jak Twoja.

Migrując notesy lokalne lub oparte na repozytorium do notesów platformy Azure, można natychmiast udostępnić je współpracownikom, którzy potrzebują tylko przeglądarki do wyświetlania i uruchamiania notesów. Jeśli zalogują się do notesów platformy Azure, mogą również wprowadzać zmiany.

## <a name="prerequisites"></a>Wymagania wstępne

- [Notes Jupyter](https://jupyter-notebook.readthedocs.io) na komputerze lokalnym lub pod innym adresem URL pliku z dostępem. 

## <a name="create-a-project-on-azure-notebooks"></a>Tworzenie projektu w notesach platformy Azure

Ten przewodnik Szybki start pokazuje migrację notesu z komputera lokalnego lub innego dostępnego adresu URL pliku. Aby przeprowadzić migrację notesów z repozytorium usługi GitHub, zobacz [Szybki start: Klonowanie notesu](quickstart-clone-jupyter-notebook.md).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Przejdź do [usługi Azure Notess](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki start — logowanie się do notesów platformy Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na stronie profilu publicznego wybierz **pozycję Moje projekty** u góry strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję **Nowy projekt** (skrót klawiaturowy: n). Przycisk może pojawić **+** się tak, jakby okno przeglądarki było wąskie:

    ![Polecenie Nowy projekt na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym okienku **Utwórz nowy projekt** wprowadź odpowiednie wartości dla migrującego notesu w polach **Nazwa projektu** i **Identyfikator projektu,** wyczyść opcje **projektu publicznego** i **utwórz README.md**, a następnie wybierz pozycję **Utwórz**.

## <a name="upload-the-local-notebook"></a>Przekazywanie notesu lokalnego

1. Na stronie projektu wybierz **pozycję Przekaż** (która może być wyświetlana jako strzałka w górę tylko wtedy, gdy okno przeglądarki jest małe), a następnie wybierz 1. W wyświetlonym wyskakującym okienku wybierz pozycję **Z komputera,** jeśli notes znajduje się w lokalnym systemie plików, lub **Z adresu URL,** jeśli notes znajduje się w trybie online:

    ![Polecenie przekazywania notesu z adresu URL lub komputera lokalnego](media/quickstarts/upload-from-computer-url-command.png)

   Ponownie, jeśli notes znajduje się w repozytorium Usługi GitHub, wykonaj kroki opisane w przewodniku Szybki start: Zamiast tego [klonuj notes.](quickstart-clone-jupyter-notebook.md)

   - Jeśli **używasz**opcji Z komputera, przeciągnij i upuść pliki *.ipynb* do okna podręcznego lub wybierz pozycję **Wybierz pliki**, a następnie przejdź do i wybierz pliki, które chcesz zaimportować. Następnie wybierz przycisk **Upload** (Przekaż). Przekazane pliki mają taką samą nazwę jak pliki lokalne. Nie musisz przekazywać zawartości żadnych folderów *ipynb_checkpoints.*

     ![Prześlij z okna podręcznego komputera](media/quickstarts/upload-from-computer-popup.png)

   - Jeśli **używasz adresu URL z**, wprowadź adres źródłowy w polu **Adres URL pliku** i nazwę pliku, aby przypisać go do notesu w projekcie w polu **Nazwa pliku.** Następnie wybierz przycisk **Upload** (Przekaż). Jeśli masz wiele plików z oddzielnymi adresami URL, użyj polecenia **Dodaj plik,** aby sprawdzić pierwszy wprowadzony adres URL, po czym okno podręczne zawiera nowe pola dla innego pliku.

     ![Prześlij z wyskakujących okienków adresu URL](media/quickstarts/upload-from-url-popup.png)

1. Otwórz i uruchom nowo przesłany notes, aby zweryfikować jego zawartość i działanie. Po zakończeniu wybierz pozycję Zatrzymanie **pliku** > **i zamknij,** aby zamknąć notes.

1. Aby udostępnić łącze do przekazanego notesu, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Kopiuj łącze** (skrót klawiaturowy: y), a następnie wklej to łącze do odpowiedniego komunikatu. Alternatywnie można udostępnić projekt jako całość za pomocą **Share** kontroli na stronie projektu.

1. Aby edytować pliki inne niż notesy, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Edytuj plik** (skrót klawiaturowy: i). Domyślna akcja **Uruchom** (skrót klawiaturowy: r) pokazuje tylko zawartość pliku i nie zezwala na edycję.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i uruchamianie notebooka Jupyter do regresji liniowej](tutorial-create-run-jupyter-notebook.md)
