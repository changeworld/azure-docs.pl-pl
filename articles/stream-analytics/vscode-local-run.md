---
title: Testowanie Azure Stream Analytics zapytań z Visual Studio Code
description: W tym artykule opisano sposób testowania zapytań lokalnie za pomocą Azure Stream Analytics narzędzi dla Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924972"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Testowanie Stream Analytics zapytań lokalnie za pomocą Visual Studio Code

Za pomocą Azure Stream Analytics narzędzi do Visual Studio Code można testować Stream Analytics zadania lokalnie z przykładowymi danymi.

Skorzystaj z tego [przewodnika Szybki Start](quick-create-vs-code.md) , aby dowiedzieć się, jak utworzyć zadanie Stream Analytics przy użyciu Visual Studio Code.

## <a name="prerequisites"></a>Wymagania wstępne
* Zainstaluj [zestaw SDK platformy .NET Core](https://dotnet.microsoft.com/download).
* Uruchom ponownie Visual Studio Code.
 
## <a name="run-queries-locally"></a>Uruchom zapytania lokalnie

Możesz użyć rozszerzenia Azure Stream Analytics, aby Visual Studio Code do testowania Stream Analyticsych zadań lokalnie z przykładowymi danymi.

1. Po utworzeniu zadania Stream Analytics Użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń. Następnie wpisz i wybierz **ASA: Dodaj dane wejściowe**.

    ![Dodawanie danych wejściowych ASA w programie Visual Studio Code](./media/vscode-local-run/add-input.png)

2. Wybierz pozycję **lokalne dane wejściowe**.

    ![Dodawanie lokalnych danych wejściowych ASA w programie Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Wybierz pozycję **+ nowe lokalne dane wejściowe**.

    ![Dodawanie nowych lokalnych danych wejściowych ASA w programie Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Wprowadź ten sam alias wejściowy, który był używany w zapytaniu.

    ![Dodawanie nowego lokalnego aliasu wejściowego ASA](./media/vscode-local-run/new-local-input-alias.png)

5. W pliku **LocalInput_DefaultLocalStream. JSON** wprowadź ścieżkę pliku, w którym znajduje się plik danych lokalnych.

    ![Wprowadź ścieżkę do lokalnego pliku w programie Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Wróć do edytora zapytań i wybierz pozycję **Uruchom lokalnie**.

    ![Wybierz pozycję Uruchom lokalnie w edytorze zapytań](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie Azure Stream Analytics zadania w chmurze w Visual Studio Code (wersja zapoznawcza)](quick-create-vs-code.md)

* [Eksplorowanie Azure Stream Analytics zadań z Visual Studio Code (wersja zapoznawcza)](vscode-explore-jobs.md)
