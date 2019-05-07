---
title: Eksplorowanie usługi Azure Stream Analytics za pomocą programu Visual Studio Code (wersja zapoznawcza)
description: W tym artykule pokazano, jak wyeksportować zadania usługi Azure Stream Analytics do lokalnego projektu listy zadań i widoku zadań jednostek i skonfiguruj potok ciągłej integracji/ciągłego Dostarczania dla zadania usługi Stream Analytics.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079213"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Eksplorowanie usługi Azure Stream Analytics za pomocą programu Visual Studio Code (wersja zapoznawcza)

Usługi Azure Stream Analytics, rozszerzenia programu Visual Studio Code daje deweloperom uproszczone środowisko do zarządzania ich zadań usługi Stream Analytics. Rozszerzenie usługi Azure Stream Analytics możesz wykonywać następujące czynności:

- [Utwórz](quick-create-vs-code.md), uruchomić i zatrzymać zadania
- Istniejące zadania eksportu do lokalnego projektu
- Lokalnie uruchamiać zapytania
- Skonfiguruj potok ciągłej integracji/ciągłego wdrażania

## <a name="export-a-job-to-a-local-project"></a>Eksportuj zadania do lokalnego projektu

Aby wyeksportować zadania do lokalnego projektu, odszukaj zadanie, o których chcesz eksportować w **Stream Analytics Eksplorator** w programie Visual Studio code. Następnie wybierz folder dla projektu. Projektu są eksportowane do folderu, którą wybierzesz i można kontynuować zarządzanie zadanie z programu Visual Studio Code. Aby uzyskać więcej informacji na temat korzystania z programu Visual Studio Code do zarządzania zadania usługi Stream Analytics, zobacz Visual Studio Code [Szybki Start](quick-create-vs-code.md).

![Eksportuj zadanie ASA w programie Visual Studio Code](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>Lokalnie uruchamiać zapytania

Rozszerzenia programu Visual Studio Code dla usługi Azure Stream Analytics można użyć do testowania zadań usługi Stream Analytics lokalnie z przykładowymi danymi.

1. Po utworzeniu zadania usługi Stream Analytics, użyj **Ctrl + Shift + P** aby otworzyć paletę poleceń. Następnie wpisz i wybierz **ASA: Dodaj dane wejściowe**.

    ![Dodaj dane wejściowe ASA w programie Visual Studio code](./media/vs-code-how-to/add-input.png)

2. Wybierz **lokalnych danych wejściowych**.

    ![Dodawanie lokalnych danych wejściowych ASA w programie Visual Studio code](./media/vs-code-how-to/add-local-input.png)

3. Wybierz **+ nowe dane wejściowe lokalnego**.

    ![Dodaj nowe lokalne ASA danych wejściowych w programie Visual Studio code](./media/vs-code-how-to/add-new-local-input.png)

4. Wprowadź ten sam alias danych wejściowych, które było używane w zapytaniu.

    ![Dodaj nowy alias danych wejściowych lokalnego ASA](./media/vs-code-how-to/new-local-input-alias.png)

5. W **LocalInput_DefaultLocalStream.json** pliku, wprowadź ścieżkę do pliku, w którym znajduje się lokalnego pliku danych.

    ![Wprowadź ścieżkę do pliku lokalnego w programie Visual Studio](./media/vs-code-how-to/local-file-path.png)

6. Wróć do edytora zapytań i wybierz **uruchamiane lokalnie**.

    ![Wybierz polecenie Uruchom lokalnie w edytorze zapytań](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>Skonfiguruj potok ciągłej integracji/ciągłego wdrażania

Możesz włączyć ciągłej integracji i ciągłego wdrażania dla zadań usługi Azure Stream Analytics przy użyciu **narzędzia asa cicd** pakietów Menedżera NPM. Pakiet NPM udostępnia narzędzia do automatycznego wdrażania projektów, Stream Analytics Visual Studio Code. Może służyć w Windows, macOS i Linux bez konieczności instalowania programu Visual Studio Code.

Po utworzeniu [pobrano pakiet](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), użyj następującego polecenia w danych wyjściowych szablonów usługi Azure Resource Manager. Jeśli **outputPath** nie zostanie określony, szablony zostaną umieszczone w **Wdróż** folder w węźle projektu **bin** folderu.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie zadania usługi Azure Stream Analytics w chmurze w programie Visual Studio Code (wersja zapoznawcza)](quick-create-vs-code.md)