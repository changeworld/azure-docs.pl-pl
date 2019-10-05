---
title: Klonowanie notesu Jupyter z usługi GitHub za pomocą Azure Notebooks
description: Szybko Sklonuj Notes Jupyter z repozytorium GitHub i uruchom go na koncie Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: d7122b78-6daa-4bea-883b-ff832cfecef3
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: ed239c470a49fc045bd436668f83dff32eac3a66
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970132"
---
# <a name="quickstart-clone-a-notebook"></a>Szybki Start: klonowanie notesu

Wielu analityków danych i deweloperów przechowują swoje notesy w [repozytoriach usługi GitHub](https://github.com), bezpłatnej usłudze, która zapewnia magazyn i kontrolę wersji dla wielu różnych typów projektów. GitHub jest często używany jako sposób współpracy z notesami Jupyter, które są uruchamiane lokalnie. W takich przypadkach każdy współpracownik zachowuje lokalną kopię repozytorium i uruchamia notesy z tej kopii.

Klonowanie spowoduje utworzenie kopii notesu w usłudze GitHub na koncie usługi Azure Notebooks. Ten klon jest niezależny od oryginalnego repozytorium; zmiany są przechowywane tylko na koncie Azure Notebooks i nie mają wpływu na oryginalny. Ponieważ klon znajduje się w chmurze, można udostępnić projekt innym współpracownikom, którzy nie muszą tworzyć żadnych kopii lokalnych, lub nawet instalować Jupyter na swoich komputerach. Można również sklonować Notes po prostu jako punkt wyjścia dla projektu własnych lub do uzyskiwania plików danych.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonowanie notesów Cognitive Services platformy Azure

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Link Moje projekty w górnej części okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz przycisk strzałki w górę (skrót klawiaturowy: U; przycisk pojawia się jako **Przekaż repozytorium GitHub** , gdy okno przeglądarki jest dostatecznie szerokie):

    ![Prześlij polecenie GitHub repozytorium na stronie Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. W wyświetlonym **repozytorium GitHub** wpisz lub ustaw następujące szczegóły, a następnie wybierz pozycję **Importuj**:

   - **Repozytorium GitHub**: Microsoft/poznawcze-Services-notesy (Ta nazwa służy do klonowania notesów Jupyter dla platformy Cognitive Services Azure w [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Klonuj cyklicznie**: (wyczyszczone)
   - **Nazwa projektu**: klon Cognitive Services
   - **Identyfikator projektu**: poznawcze — usługi — klonowanie
   - **Publiczny**: (wyczyszczone)

     ![Przekazanie okna podręcznego repozytorium GitHub w celu zebrania informacji o repozytorium](media/quickstarts/upload-github-repo-popup.png)

1. Poczekaj, aż zakończy się proces; klonowanie repozytorium może potrwać kilka minut.

1. Po zakończeniu klonowania Azure Notebooks przenosi do nowego projektu, w którym można zobaczyć kopie wszystkich plików.

    [![](media/quickstarts/completed-clone.png#lightbox)(media/quickstarts/completed-clone.png "Widok ukończonego klonu")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Udostępnianie notesu

1. Aby udostępnić kopię sklonowanego projektu, użyj kontrolki **Udostępnij** lub Uzyskaj link, uzyskaj kod HTML lub promocji, który zawiera link, lub Utwórz wiadomość e-mail z linkiem:

    ![Project Share — polecenie](media/quickstarts/share-project-command.png)

1. Ponieważ opcja **publiczna** została wyczyszczona podczas klonowania projektu, klon jest prywatny. Aby utworzyć kopię publiczną, wybierz pozycję **Ustawienia projektu**, ustaw opcję **projekt publiczny** w menu podręcznym, a następnie wybierz pozycję **Zapisz**.

1. Wybierz Notes w projekcie, aby go uruchomić. Każdy Notes w repozytorium Cognitive Services platformy Azure, na przykład, jest własnym przewodnikiem Szybki Start. Poniższy obraz przedstawia wynik użycia notesu BingImageSearchAPI, po dodaniu klucza subskrypcji interfejsu API Cognitive Services i zmianie okresu wyszukiwania "Puppies" na "Bunnies":

    ![Uruchamianie notesu Jupyter sklonowanego z usługi GitHub](media/quickstarts/clone-notebook-result.png)

1. Po zakończeniu działania notesu wybierz pozycję **plik** > **Zamknij i zatrzymany** , aby zamknąć Notes i okno przeglądarki.

1. Aby udostępnić poszczególne notesy w projekcie, kliknij prawym przyciskiem myszy Notes i wybierz polecenie **Kopiuj link** (skrót klawiaturowy: t):

    ![Polecenie menu kontekstowego umożliwiające skopiowanie linku do pojedynczego notesu](media/quickstarts/copy-link-to-individual-notebook.png)

1. Aby edytować pliki inne niż notesy, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Edytuj plik** (skrót klawiaturowy: i). Akcja domyślna, **Uruchom** (skrót klawiaturowy: r), wyświetla tylko zawartość pliku i nie zezwala na edytowanie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
