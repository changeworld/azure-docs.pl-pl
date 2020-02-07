---
title: Klonowanie notesu Jupyter z usługi GitHub przy użyciu wersji zapoznawczej Azure Notebooks
description: Szybkie klonowanie notesu programu Jupyter, z repozytorium GitHub i uruchom go na swoim koncie notesów usługi Azure.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064601"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Szybki Start: klonowanie notesu w programie Azure Notebooks Preview

Ten przewodnik Szybki Start umożliwia skopiowanie notesu Jupyter przechowywanego w usłudze GitHub na konto Azure Notebooks. 

Repozytoria GitHub zapewniają magazyn i kontrolę wersji dla notesów Jupyter. Współpracownicy utrzymują lokalne kopie repozytoriów i uruchamiają notesy z tych kopii. Klonowanie notesu Jupyter z usługi GitHub do konta Azure Notebooks tworzy niezależną kopię notesu. Zmiany są przechowywane tylko na koncie Azure Notebooks i nie mają wpływu na oryginalne repozytorium GitHub. 

Ponieważ klon Azure Notebooks znajduje się w chmurze, możesz udostępnić go współpracownikom, którzy nie muszą tworzyć żadnych kopii lokalnych ani instalować Jupyter na swoich komputerach. Można również sklonować Notes po prostu jako punkt wyjścia dla projektu własnych lub do uzyskiwania plików danych. 

## <a name="prerequisites"></a>Wymagania wstępne
Brak.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonowanie notesów usługi Azure Cognitive Services

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz przycisk strzałki w górę (skrót klawiaturowy: U; przycisk pojawia się jako **Przekaż repozytorium GitHub** , gdy okno przeglądarki jest dostatecznie szerokie):

    ![Prześlij polecenie GitHub repozytorium na stronie Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. W wyświetlonym **repozytorium GitHub** wpisz lub ustaw następujące szczegóły, a następnie wybierz pozycję **Importuj**:

   - **Repozytorium GitHub**: Microsoft/poznawcze-Services-notesy (Ta nazwa służy do klonowania notesów Jupyter dla platformy Azure Cognitive Services w [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Klonuj cyklicznie**: (wyczyszczone)
   - **Nazwa projektu**: klon Cognitive Services
   - **Identyfikator projektu**: poznawcze — usługi — klonowanie
   - **Publiczny**: (wyczyszczone)

     ![Przekazanie okna podręcznego repozytorium GitHub w celu zebrania informacji o repozytorium](media/quickstarts/upload-github-repo-popup.png)

1. Cierpliwość, podczas gdy proces zostanie zakończony; klonowanie repozytorium może potrwać kilka minut.

1. Po ukończeniu klonowania notesy platformy Azure spowoduje przejście do nowego projektu tam, gdzie zobaczysz kopie wszystkich plików.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Udostępnianie notesu

1. Aby udostępnić kopię sklonowanego projektu, użyj kontrolki **Udostępnij** lub Uzyskaj link, uzyskaj kod HTML lub promocji, który zawiera link, lub Utwórz wiadomość e-mail z linkiem:

    ![Udostępnij projekt — polecenie](media/quickstarts/share-project-command.png)

1. Ponieważ opcja **publiczna** została wyczyszczona podczas klonowania projektu, klon jest prywatny. Aby utworzyć kopię publiczną, wybierz pozycję **Ustawienia projektu**, ustaw opcję **projekt publiczny** w menu podręcznym, a następnie wybierz pozycję **Zapisz**.

1. Wybierz notes w projekcie, aby go uruchomić. Każdy Notes w repozytorium usług Azure Cognitive Services jest na przykład swój własny niezależna Szybki Start. Na poniższym obrazie przedstawiono korzystania z notesu BingImageSearchAPI, po dodaniu interfejsu API usług Cognitive Services klucz subskrypcji i zmiana wyszukiwany termin "Szczeniaki" na "bunnies" wynik:

    ![Uruchamianie notesu programu Jupyter sklonowanym z usługi GitHub](media/quickstarts/clone-notebook-result.png)

1. Po zakończeniu pracy z notesem wybierz pozycję **plik** > **Zamknij i zatrzymany** , aby zamknąć Notes i jego okno przeglądarki.

1. Aby udostępnić poszczególne notesy w projekcie, kliknij prawym przyciskiem myszy Notes i wybierz polecenie **Kopiuj link** (skrót klawiaturowy: t):

    ![Polecenia menu kontekstowego, aby skopiować łącze do poszczególnych notesu](media/quickstarts/copy-link-to-individual-notebook.png)

1. Aby edytować pliki inne niż notesy, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Edytuj plik** (skrót klawiaturowy: i). Akcja domyślna, **Uruchom** (skrót klawiaturowy: r), wyświetla tylko zawartość pliku i nie zezwala na edytowanie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i uruchamianie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
