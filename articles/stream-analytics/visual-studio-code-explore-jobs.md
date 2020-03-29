---
title: Poznaj zadania usługi Azure Stream Analytics w programie Visual Studio Code
description: W tym artykule pokazano, jak wyeksportować zadanie usługi Azure Stream Analytics do lokalnego projektu, listy zadań i wyświetlania jednostek zadań.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479243"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Eksploruj usługę Azure Stream Analytics za pomocą kodu programu Visual Studio (wersja zapoznawcza)

Rozszerzenie usługi Azure Stream Analytics for Visual Studio code zapewnia deweloperom uproszczone środowisko zarządzania zadaniami usługi Stream Analytics. Może być używany w systemach Windows, Mac i Linux. Dzięki rozszerzeniu usługi Azure Stream Analytics możesz:

- [Tworzenie,](quick-create-vs-code.md)uruchamianie i zatrzymywania zadań
- Eksportowanie istniejących zadań do projektu lokalnego
- Wyświetlanie zadań i wyświetlanie encji zadań

## <a name="export-a-job-to-a-local-project"></a>Eksportowanie zadania do projektu lokalnego

Aby wyeksportować zadanie do projektu lokalnego, znajdź zadanie, które chcesz wyeksportować w **Eksploratorze analizy strumienia** w kodzie programu Visual Studio. Następnie wybierz folder dla projektu. Projekt jest eksportowany do wybranego folderu i można kontynuować zarządzanie zadaniem z programu Visual Studio Code. Aby uzyskać więcej informacji na temat używania programu Visual Studio Code do zarządzania zadaniami usługi Stream Analytics, zobacz [Szybki start](quick-create-vs-code.md)kodu programu Visual Studio .

![Eksportowanie zadania ASA w programie Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Lista zadań i wyświetlanie encji zadań

Widoku zadań można używać do interakcji z zadaniami usługi Azure Stream Analytics z programu Visual Studio.


1. Kliknij ikonę **platformy Azure** na pasku aktywności kodu programu Visual Studio, a następnie rozwiń węzeł Usługi **Stream Analytics**. Zadania powinny być wyświetlane w ramach subskrypcji.

   ![Otwórz Eksploratora analizy strumienia](./media/vscode-explore-jobs/open-explorer.png)

2. Rozwiń węzeł zadania, można otworzyć i wyświetlić kwerendę zadania, konfigurację, dane wejściowe, wyjścia i funkcje. 

3. Kliknij prawym przyciskiem myszy węzeł zadania i wybierz **widok otwórz zadanie w węźle Portal,** aby otworzyć widok zadania w witrynie Azure portal.

   ![Otwórz widok zadania w portalu](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zadania chmury usługi Azure Stream Analytics w programie Visual Studio Code (Wersja zapoznawcza)](quick-create-vs-code.md)
