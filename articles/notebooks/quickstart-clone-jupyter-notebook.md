---
title: Klonowanie notesu Jupyter z usługi GitHub przy użyciu wersji zapoznawczej Azure Notebooks
description: Szybkie klonowanie notesu programu Jupyter, z repozytorium GitHub i uruchom go na swoim koncie notesów usługi Azure.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 8aa88008ece170a5eed7ab491e3318aed5168923
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647105"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Szybki Start: klonowanie notesu w programie Azure Notebooks Preview

Wielu programistów i analityków danych przechowywania notesy w [repozytoriów GitHub](https://github.com), bezpłatna usługa, która oferuje magazyn i kontroli wersji dla wielu różnych typach projektów. GitHub jest często używana jako sposób nad notesów programu Jupyter, które są uruchamiane lokalnie. W takich przypadkach każdy Współautor, który przechowuje kopię lokalną repozytorium i uruchamia notesów z tej kopii.

Klonowanie tworzy kopię notesu usługi GitHub w ramach Twojego konta platformy Azure, notesy zamiast tego. Klonuj ten jest niezależna od oryginalnego repozytorium; zmiany są przechowywane w ramach Twojego konta platformy Azure, notesy i nie mają wpływu na oryginalny. Ponieważ sklonowanym znajduje się w chmurze, możesz udostępniać projektu innych współpracowników, którzy muszą wprowadzić wszystkie kopie lokalne lub nie nawet mieć zainstalowany na swoich komputerach Jupyter. Można również sklonować notesu po prostu jako punktu wyjścia dla projektu samodzielnie lub uzyskać pliki danych.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonowanie notesów usługi Azure Cognitive Services

1. Przejdź do [notesów usługi Azure](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać więcej informacji, zobacz [Szybki Start — Zaloguj się do platformy Azure, notesy](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie profilu publicznego, wybierz **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz przycisk strzałki w górę (skrót klawiaturowy: U; przycisk pojawia się jako **Przekaż repozytorium GitHub** , gdy okno przeglądarki jest dostatecznie szerokie):

    ![Prześlij polecenie GitHub repozytorium na stronie Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. W wyświetlonym **repozytorium GitHub** wpisz lub ustaw następujące szczegóły, a następnie wybierz pozycję **Importuj**:

   - **Repozytorium GitHub**: Microsoft/poznawcze-Services-notesy (Ta nazwa służy do klonowania notesów Jupyter dla platformy Azure Cognitive Services w [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Rekursywnie Klonuj**: (usunięty)
   - **Nazwa projektu**: klonowania usługi Cognitive Services
   - **Identyfikator projektu**: klonowania cognitive services
   - **Publiczne**: (usunięty)

     ![Przekazanie okna podręcznego repozytorium GitHub w celu zebrania informacji o repozytorium](media/quickstarts/upload-github-repo-popup.png)

1. Cierpliwość, podczas gdy proces zostanie zakończony; klonowanie repozytorium może potrwać kilka minut.

1. Po ukończeniu klonowania notesy platformy Azure spowoduje przejście do nowego projektu tam, gdzie zobaczysz kopie wszystkich plików.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Udostępnianie notesu

1. Aby udostępnić swoją kopię sklonowany projektu, należy użyć **udostępnianie** kontrolować lub uzyskać link, Uzyskaj kod HTML lub języka znaczników Markdown, która zawiera łącze lub utworzenie wiadomości e-mail z linkiem:

    ![Udostępnij projekt — polecenie](media/quickstarts/share-project-command.png)

1. Ponieważ została wyczyszczona **publicznych** podczas klonowania projektu klonu jest prywatny. Aby upublicznić kopii, wybierz pozycję **ustawienia projektu**ustaw **publicznego projektu** opcji w okienku wyskakującym, a następnie wybierz **Zapisz**.

1. Wybierz notes w projekcie, aby go uruchomić. Każdy Notes w repozytorium usług Azure Cognitive Services jest na przykład swój własny niezależna Szybki Start. Na poniższym obrazie przedstawiono korzystania z notesu BingImageSearchAPI, po dodaniu interfejsu API usług Cognitive Services klucz subskrypcji i zmiana wyszukiwany termin "Szczeniaki" na "bunnies" wynik:

    ![Uruchamianie notesu programu Jupyter sklonowanym z usługi GitHub](media/quickstarts/clone-notebook-result.png)

1. Po zakończeniu uruchamiania notesu, wybierz **pliku** > **Zamknij i zatrzymanie** na zamknięcie notesu i okna przeglądarki.

1. Aby udostępnić poszczególnych Notes w projekcie, kliknij prawym przyciskiem myszy w notesie, a następnie wybierz **Kopiuj link** (skrót klawiaturowy: y):

    ![Polecenia menu kontekstowego, aby skopiować łącze do poszczególnych notesu](media/quickstarts/copy-link-to-individual-notebook.png)

1. Aby edytować pliki inne niż notesów, kliknij prawym przyciskiem myszy plik w projekcie i wybierz **Edytuj plik** (skrót klawiaturowy: czy mogę). Domyślna akcja **Uruchom** (skrót klawiaturowy: r), tylko pokazuje zawartość pliku i nie zezwala na edycję.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: tworzenie uruchomienia notesu programu Jupyter w celu regresji liniowej](tutorial-create-run-jupyter-notebook.md)
