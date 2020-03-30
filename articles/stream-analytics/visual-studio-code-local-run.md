---
title: Testowanie zadania usługi Azure Stream Analytics lokalnie przy użyciu przykładowych danych przy użyciu kodu programu Visual Studio
description: W tym artykule opisano sposób testowania zapytań lokalnie przy użyciu przykładowych danych przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486471"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Testowanie zapytań usługi Stream Analytics lokalnie z przykładowymi danymi przy użyciu kodu programu Visual Studio

Za pomocą narzędzi Usługi Azure Stream Analytics dla programu Visual Studio Code można lokalnie przetestować zadania usługi Stream Analytics przy użyciu przykładowych danych. Wyniki kwerendy można znaleźć w plikach JSON w folderze **LocalRunOutputs** projektu.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [podstawowy pakiet SDK platformy .NET](https://dotnet.microsoft.com/download) i uruchom ponownie program Visual Studio Code.

* Skorzystaj z tego [przewodnika Szybki start,](quick-create-vs-code.md) aby dowiedzieć się, jak utworzyć zadanie usługi Stream Analytics przy użyciu programu Visual Studio Code.

## <a name="prepare-sample-data"></a>Przygotowywanie przykładowych danych

Najpierw należy przygotować przykładowe pliki danych wejściowych. Jeśli masz już kilka przykładowych plików danych na komputerze, możesz pominąć ten krok i przejść do następnego.

1. Kliknij **pozycję Podgląd danych** w pliku konfiguracji wejściowej z górnego wiersza. Niektóre dane wejściowe zostaną pobrane z usługi IoT Hub i wyświetlone w oknie podglądu. Należy pamiętać, że może to trochę potrwać.

2. Po wyświetleniu danych kliknij przycisk **Zapisz, aby** zapisać dane w pliku lokalnym.

 ![Podgląd danych wejściowych na żywo](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definiowanie danych wejściowych lokalnych

1. Kliknij **plik input.json** w obszarze Wejścia w folderze Usługi Stream Analytics. Następnie wybierz **pozycję Dodaj lokalne dane wejściowe** z górnego wiersza.

    ![Dodawanie lokalnych danych wejściowych z projektu](./media/quick-create-vs-code/add-input-from-project.png)

    Można również użyć **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń i wprowadzić **ASA: Dodaj dane wejściowe**.

   ![Dodawanie danych wejściowych usługi Stream Analytics w programie VS Code](./media/quick-create-vs-code/add-input.png)

2. Wybierz **pozycję Wejście lokalne**.

    ![Dodawanie danych wejściowych asa w kodzie programu Visual Studio](./media/vscode-local-run/add-local-input.png)

3. Wybierz **+ Nowe wejście lokalne**.

    ![Dodawanie nowego lokalnego wejścia ASA w kodzie programu Visual Studio](./media/vscode-local-run/add-new-local-input.png)

4. Wprowadź ten sam alias wejściowy, który był używany w zapytaniu.

    ![Dodawanie nowego lokalnego aliasu wejściowego ASA](./media/vscode-local-run/new-local-input-alias.png)

5. W nowo wygenerowanym pliku **LocalInput_Input.json** wprowadź ścieżkę pliku, w której znajduje się lokalny plik danych.

    ![Wprowadzanie lokalnej ścieżki pliku w programie Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Wybierz **opcję Podgląd danych,** aby wyświetlić podgląd danych wejściowych. Typ serializacji danych jest automatycznie wykrywany, jeśli jest to jego JSON lub CSV. Użyj selektora, aby wyświetlić dane w formacie **Tabela** lub **Raw.** Poniższa tabela jest przykładem danych w **formacie tabeli:**

     ![Podgląd danych lokalnych w formacie tabeli](./media/vscode-local-run/local-file-preview-table.png)

    Poniższa tabela jest przykładem danych w **formacie Raw:**

    ![Podgląd danych lokalnych w formacie raw](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Uruchamianie zapytań lokalnie

Wróć do edytora zapytań i wybierz pozycję **Uruchom lokalnie**. Następnie wybierz **pozycję Użyj lokalnego wprowadzania danych** z listy rozwijanej.

![Wybierz uruchamianie lokalnie w edytorze zapytań](./media/vscode-local-run/run-locally.png)

![Użyj danych wejściowych lokalnych](./media/vscode-local-run/run-locally-use-local-input.png)

Wynik jest wyświetlany w prawym oknie. Możesz kliknąć przycisk **Uruchom,** aby ponownie przetestować. Można również wybrać **otwórz w folderze,** aby wyświetlić pliki wyników w Eksploratorze plików i dalej otwierać je za pomocą innych narzędzi. Należy zauważyć, że pliki wyników są dostępne tylko w formacie JSON.

![Wyświetlanie lokalnego wyniku biegu](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Następne kroki

* [Testowanie zadań usługi Azure Stream Analytics lokalnie przy użyciu danych wejściowych na żywo przy użyciu kodu programu Visual Studio](visual-studio-code-local-run-live-input.md)

* [Poznaj zadania usługi Azure Stream Analytics za pomocą kodu programu Visual Studio (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)
