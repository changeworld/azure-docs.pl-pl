---
title: Klonowanie notesu Jupyter z usługi GitHub za pomocą notesów usługi Azure
description: Szybkie klonowanie notesu programu Jupyter, z repozytorium GitHub i uruchom go na swoim koncie notesów usługi Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: d7122b78-6daa-4bea-883b-ff832cfecef3
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 30625423553b71e848d27d047d4b7bc3add6eaff
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265155"
---
# <a name="quickstart-clone-a-notebook"></a>Szybki start: Klonowanie notesu

Wielu programistów i analityków danych przechowywania notesy w [repozytoriów GitHub](https://github.com), bezpłatna usługa, która oferuje magazyn i kontroli wersji dla wielu różnych typach projektów. GitHub jest często używana jako sposób nad notesów programu Jupyter, które są uruchamiane lokalnie. W takich przypadkach każdy Współautor, który przechowuje kopię lokalną repozytorium i uruchamia notesów z tej kopii.

Klonowanie tworzy kopię notesu usługi GitHub w ramach Twojego konta platformy Azure, notesy zamiast tego. Klonuj ten jest niezależna od oryginalnego repozytorium; zmiany są przechowywane w ramach Twojego konta platformy Azure, notesy i nie mają wpływu na oryginalny. Ponieważ sklonowanym znajduje się w chmurze, możesz udostępniać projektu innych współpracowników, którzy muszą wprowadzić wszystkie kopie lokalne lub nie nawet mieć zainstalowany na swoich komputerach Jupyter. Można również sklonować notesu po prostu jako punktu wyjścia dla projektu samodzielnie lub uzyskać pliki danych.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klonowanie notesów usługi Azure Cognitive Services

1. Przejdź do [notesów usługi Azure](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać więcej informacji, zobacz [Szybki Start — Zaloguj się do platformy Azure, notesy](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie profilu publicznego, wybierz **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na **Moje projekty** wybierz przycisk strzałki w górę (skrót klawiaturowy: U; Ten przycisk, który jest wyświetlany jako **repozytorium GitHub, aby przekazać** gdy okno przeglądarki jest dostatecznie szeroka):

    ![Przekaż polecenia repozytorium GitHub na stronie Moje projekty](media/quickstarts/upload-github-repo-command.png)

1. W **przekazywanie repozytorium GitHub** wyświetlany, wprowadź lub ustaw następujące szczegóły, a następnie wybierz **importu**:

   - **Repozytorium GitHub**: Microsoft/cognitive-services notesów (Ta nazwa klony notesów programu Jupyter dla usług Azure Cognitive Services na [ https://github.com/Microsoft/cognitive-services-notebooks ](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Rekursywnie Klonuj**: (usunięty)
   - **Nazwa projektu**: Klonuj usług cognitive Services
   - **Identyfikator projektu**: klonowania cognitive services
   - **Publiczne**: (usunięty)

     ![Przekaż popup repozytorium GitHub, aby zebrać informacje o repozytorium](media/quickstarts/upload-github-repo-popup.png)

1. Cierpliwość, podczas gdy proces zostanie zakończony; klonowanie repozytorium może potrwać kilka minut.

1. Po ukończeniu klonowania notesy platformy Azure spowoduje przejście do nowego projektu tam, gdzie zobaczysz kopie wszystkich plików.

    [![](media/quickstarts/completed-clone.png "Wyświetl klonowania ukończone")](media/quickstarts/completed-clone.png#lightbox)

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: tworzenie uruchomienia notesu programu Jupyter w celu regresji liniowej](tutorial-create-run-jupyter-notebook.md)
