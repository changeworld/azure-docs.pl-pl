---
title: Klonowanie notesu jupyter z usługi GitHub za pomocą usługi Azure Notebooks Preview
description: Szybko sklonuj notes Jupyter z repozytorium Usługi GitHub i uruchom go na koncie notesów platformy Azure.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064601"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Szybki start: klonowanie notesu w usłudze Azure Notebooks Preview

W tym przewodniku Szybki start można skopiować notes Jupyter przechowywane w usłudze GitHub do konta notesów platformy Azure. 

Repozytoria GitHub zapewniają kontrolę pamięci masowej i wersji dla notebooków Jupyter. Współpracownicy przechowują lokalne kopie repozytoriów i uruchamiają notesy z tych kopii. Klonowanie notesu Jupyter z usługi GitHub na koncie notesów platformy Azure tworzy niezależną kopię notesu. Zmiany są przechowywane tylko na koncie notesów platformy Azure i nie mają wpływu na oryginalne repozytorium Usługi GitHub. 

Ponieważ klon notesów platformy Azure znajduje się w chmurze, można udostępnić go współpracownikom, którzy nie muszą tworzyć żadnych kopii lokalnych lub mieć jupyter zainstalowany na swoich komputerach. Można również sklonować notes po prostu jako punkt wyjścia dla projektu własnego lub do uzyskania plików danych. 

## <a name="prerequisites"></a>Wymagania wstępne
Brak.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonowanie notesów usług Azure Cognitive Services

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Przejdź do [usługi Azure Notess](https://notebooks.azure.com) i zaloguj się. Aby uzyskać szczegółowe informacje, zobacz [Szybki start — logowanie się do notesów platformy Azure](quickstart-sign-in-azure-notebooks.md).

1. Na stronie profilu publicznego wybierz **pozycję Moje projekty** u góry strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz przycisk strzałki w górę (skrót klawiaturowy: U; przycisk jest wyświetlany jako **Przekaż repozytorium GitHub,** gdy okno przeglądarki jest wystarczająco szerokie):

    ![Przekaż polecenie Repozytorium GitHub na stronie Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. W wyświetlonym **repozytorium Przekazywania GitHub** wprowadź lub ustaw następujące szczegóły, a następnie wybierz pozycję **Importuj:**

   - **Repozytorium GitHub**: Microsoft/cognitive-services-notebooks (ta nazwa klonuje notesy Jupyter dla usług Azure Cognitive Services pod adresem [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Klonowanie cyklicznie**: (wyczyszczone)
   - **Nazwa projektu**: Cognitive Services Clone
   - **Identyfikator projektu**: cognitive-services-clone
   - **Publiczne**: (wyczyszczone)

     ![Prześlij wyskakujące okienko Repozytorium GitHub w celu zbierania informacji o repozytorium](media/quickstarts/upload-github-repo-popup.png)

1. Bądź cierpliwy, gdy proces się zakończy; klonowanie repozytorium może potrwać kilka minut.

1. Po zakończeniu klonowania notesy platformy Azure przeniesie Cię do nowego projektu, w którym można zobaczyć kopie wszystkich plików.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Udostępnianie notesu

1. Aby udostępnić kopię sklonowanego projektu, użyj formantu **Udostępnij** lub uzyskaj łącze, uzyskaj kod HTML lub Markdown zawierający łącze lub utwórz wiadomość e-mail z łączem:

    ![Polecenie Udział projektu](media/quickstarts/share-project-command.png)

1. Ponieważ wyczyszczono **opcję Publiczne** podczas klonowania projektu, klon jest prywatny. Aby udostępnić kopię publicznie, wybierz **pozycję Ustawienia projektu**, ustaw opcję Projekt **publiczny** w wyskakującym okienku, a następnie wybierz pozycję **Zapisz**.

1. Wybierz notes w projekcie, aby go uruchomić. Każdy notes w repozytorium usług Azure Cognitive Services, na przykład, jest jego własny samodzielny Przewodnik Szybki start. Poniższy obraz ekspozycyjny przedstawia wynik korzystania z notesu BingImageSearchAPI, po dodaniu klucza subskrypcji interfejsu API usług Cognitive Services i zmianie wyszukiwanego terminu "szczenięta" na "króliczki":

    ![Uruchamianie notebooka Jupyter sklonowane z GitHub](media/quickstarts/clone-notebook-result.png)

1. Po zakończeniu uruchamiania notesu wybierz pozycję **Zamknij plik** > **i zatrzymaj,** aby zamknąć notes i jego okno przeglądarki.

1. Aby udostępnić pojedynczy notes w projekcie, kliknij prawym przyciskiem myszy notes i wybierz polecenie **Kopiuj łącze** (skrót klawiaturowy: y):

    ![Polecenie menu kontekstowego służące do kopiowania łącza do pojedynczego notesu](media/quickstarts/copy-link-to-individual-notebook.png)

1. Aby edytować pliki inne niż notesy, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Edytuj plik** (skrót klawiaturowy: i). Domyślna akcja **Uruchom** (skrót klawiaturowy: r) pokazuje tylko zawartość pliku i nie zezwala na edycję.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i uruchamianie notebooka Jupyter do regresji liniowej](tutorial-create-run-jupyter-notebook.md)
