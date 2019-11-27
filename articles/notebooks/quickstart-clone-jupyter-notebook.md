---
title: Klonowanie notesu Jupyter z usługi GitHub za pomocą notesów usługi Azure
description: Szybkie klonowanie notesu programu Jupyter, z repozytorium GitHub i uruchom go na swoim koncie notesów usługi Azure.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 500bd3f85409bb2f5e7b73be0478694695cc9c00
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277529"
---
# <a name="quickstart-clone-a-notebook"></a>Szybki Start: Klonowanie notesu

Wielu analityków danych i deweloperów przechowują swoje notesy w [repozytoriach usługi GitHub](https://github.com), bezpłatnej usłudze, która zapewnia magazyn i kontrolę wersji dla wielu różnych typów projektów. GitHub jest często używana jako sposób nad notesów programu Jupyter, które są uruchamiane lokalnie. W takich przypadkach każdy Współautor, który przechowuje kopię lokalną repozytorium i uruchamia notesów z tej kopii.

Klonowanie tworzy kopię notesu usługi GitHub w ramach Twojego konta platformy Azure, notesy zamiast tego. Klonuj ten jest niezależna od oryginalnego repozytorium; zmiany są przechowywane w ramach Twojego konta platformy Azure, notesy i nie mają wpływu na oryginalny. Ponieważ sklonowanym znajduje się w chmurze, możesz udostępniać projektu innych współpracowników, którzy muszą wprowadzić wszystkie kopie lokalne lub nie nawet mieć zainstalowany na swoich komputerach Jupyter. Można również sklonować notesu po prostu jako punktu wyjścia dla projektu samodzielnie lub uzyskać pliki danych.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonowanie notesów usługi Azure Cognitive Services

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

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
> [Samouczek: Tworzenie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
