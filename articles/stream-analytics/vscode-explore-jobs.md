---
title: Zapoznaj się z zadań usługi Azure Stream Analytics w programie Visual Studio Code (wersja zapoznawcza)
description: W tym artykule pokazano, jak wyeksportować zadania usługi Azure Stream Analytics do lokalnego projektu, listy zadań i widoku zadań jednostek.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827803"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Eksplorowanie usługi Azure Stream Analytics za pomocą programu Visual Studio Code (wersja zapoznawcza)

Usługi Azure Stream Analytics, rozszerzenia programu Visual Studio Code daje deweloperom uproszczone środowisko do zarządzania ich zadań usługi Stream Analytics. Może służyć w Windows, Mac i Linux. Rozszerzenie usługi Azure Stream Analytics możesz wykonywać następujące czynności:

- [Utwórz](quick-create-vs-code.md), uruchomić i zatrzymać zadania
- Istniejące zadania eksportu do lokalnego projektu
- Wyświetlić zadania oraz jednostki zadania

## <a name="export-a-job-to-a-local-project"></a>Eksportuj zadania do lokalnego projektu

Aby wyeksportować zadania do lokalnego projektu, odszukaj zadanie, o których chcesz eksportować w **Stream Analytics Eksplorator** w programie Visual Studio Code. Następnie wybierz folder dla projektu. Projektu są eksportowane do folderu, którą wybierzesz i można kontynuować zarządzanie zadanie z programu Visual Studio Code. Aby uzyskać więcej informacji na temat korzystania z programu Visual Studio Code do zarządzania zadania usługi Stream Analytics, zobacz Visual Studio Code [Szybki Start](quick-create-vs-code.md).

![Eksportuj zadanie ASA w programie Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Wyświetlić zadania oraz jednostki zadania

Widok zadania można użyć do interakcji z zadań usługi Azure Stream Analytics z programu Visual Studio.


1. Kliknij przycisk **Azure** ikonę na pasku działań programu Visual Studio kodu i rozwiń **węzła usługi Stream Analytics**. Zadania powinny być wyświetlane w obszarze subskrypcji.

   ![Open Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Rozwiń węzeł swoje zadania, możesz otworzyć i wyświetlić zapytanie dotyczące zadań, konfiguracji, danych wejściowych, danych wyjściowych i funkcji. 

3. Kliknij prawym przyciskiem myszy węzeł zadania, a następnie wybierz **Otwórz widok zadania w portalu** węzeł, aby otworzyć widok zadań w witrynie Azure portal.

   ![Widok otwartego zadania w portalu](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie zadania usługi Azure Stream Analytics w chmurze w programie Visual Studio Code (wersja zapoznawcza)](quick-create-vs-code.md)
