---
title: Testowanie zapytań usługi Azure Stream Analytics lokalnie za pomocą programu Visual Studio Code (wersja zapoznawcza)
description: W tym artykule opisano sposób testowania zapytań lokalnie za pomocą usługi Azure Stream Analytics Tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827953"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Testowanie zapytań usługi Stream Analytics lokalnie za pomocą programu Visual Studio Code

Za pomocą usługi Azure Stream Analytics tools for Visual Studio Code do testowania zadań usługi Stream Analytics lokalnie z przykładowymi danymi.

Użyj tego [Szybki Start](quick-create-vs-code.md) dowiesz się, jak utworzyć zadanie usługi Stream Analytics, za pomocą programu Visual Studio Code.

## <a name="run-queries-locally"></a>Lokalnie uruchamiać zapytania

Rozszerzenia programu Visual Studio Code dla usługi Azure Stream Analytics można użyć do testowania zadań usługi Stream Analytics lokalnie z przykładowymi danymi.

1. Po utworzeniu zadania usługi Stream Analytics, użyj **Ctrl + Shift + P** aby otworzyć paletę poleceń. Następnie wpisz i wybierz **ASA: Dodaj dane wejściowe**.

    ![Dodaj dane wejściowe ASA w programie Visual Studio code](./media/vscode-local-run/add-input.png)

2. Wybierz **lokalnych danych wejściowych**.

    ![Dodawanie lokalnych danych wejściowych ASA w programie Visual Studio code](./media/vscode-local-run/add-local-input.png)

3. Wybierz **+ nowe dane wejściowe lokalnego**.

    ![Dodaj nowe lokalne ASA danych wejściowych w programie Visual Studio code](./media/vscode-local-run/add-new-local-input.png)

4. Wprowadź ten sam alias danych wejściowych, które było używane w zapytaniu.

    ![Dodaj nowy alias danych wejściowych lokalnego ASA](./media/vscode-local-run/new-local-input-alias.png)

5. W **LocalInput_DefaultLocalStream.json** pliku, wprowadź ścieżkę do pliku, w którym znajduje się lokalnego pliku danych.

    ![Wprowadź ścieżkę do pliku lokalnego w programie Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Wróć do edytora zapytań i wybierz **uruchamiane lokalnie**.

    ![Wybierz polecenie Uruchom lokalnie w edytorze zapytań](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie zadania usługi Azure Stream Analytics w chmurze w programie Visual Studio Code (wersja zapoznawcza)](quick-create-vs-code.md)

* [Zapoznaj się z zadań usługi Azure Stream Analytics w programie Visual Studio Code (wersja zapoznawcza)](vscode-explore-jobs.md)
