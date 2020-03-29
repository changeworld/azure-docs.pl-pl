---
title: Wyświetlanie zadań usługi Azure Stream Analytics w programie Visual Studio
description: Dowiedz się, jak wyświetlać, uruchamiać i zatrzymywać, testować połączenia, sprawdzać wyniki i eksportować zadania usługi Azure Stream Analytics za pomocą programu Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: ad35ed342dfd40b98d61919749479ec5612cdd4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369630"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)

Narzędzia usługi Azure Stream Analytics dla programu Visual Studio ułatwiają deweloperom zarządzanie zadaniami usługi Stream Analytics bezpośrednio z ide. Dzięki narzędziom usługi Azure Stream Analytics możesz:
- [Tworzenie nowych miejsc pracy](stream-analytics-quick-create-vs.md)
- Uruchamianie, zatrzymywania i [monitorowanie zadań](stream-analytics-monitor-jobs-use-vs.md)
- Sprawdź wyniki zadań
- Eksportowanie istniejących zadań do projektu
- Testowanie połączeń wejściowych i wyjściowych
- [Uruchamianie zapytań lokalnie](stream-analytics-vs-tools-local-run.md)

Dowiedz się, jak [zainstalować narzędzia usługi Azure Stream Analytics dla programu Visual Studio.](stream-analytics-tools-for-visual-studio-install.md)

## <a name="explore-the-job-view"></a>Eksplorowanie widoku zadania

Widoku zadań można używać do interakcji z zadaniami usługi Azure Stream Analytics z programu Visual Studio.

### <a name="open-the-job-view"></a>Otwieranie widoku zadania

1. W **Eksploratorze serwera**wybierz pozycję **Zadania usługi Stream Analytics,** a następnie wybierz pozycję **Odśwież**. Zadanie powinno być wyświetlane w obszarze **Zadania usługi Stream Analytics**.

    ![Lista eksploratora serwerów usługi Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Rozwiń węzeł zadania i kliknij dwukrotnie węzeł **Widok zadania,** aby otworzyć widok zadania.
    
   ![Rozwinięty węzeł zadania](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Uruchamianie i zatrzymywania zadań

Zadania usługi Azure Stream Analytics można w pełni zarządzać z widoku zadania w programie Visual Studio. Użyj formantów, aby rozpocząć, zatrzymać lub usunąć zadanie.
    
   ![Kontrolki zadań usługi Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Sprawdź wyniki zadań

Narzędzia analizy strumienia dla programu Visual Studio obecnie obsługuje podgląd danych wyjściowych dla usługi Azure Data Lake Storage i magazynu obiektów blob. Aby wyświetlić wynik, wystarczy dwukrotnie kliknąć węzeł wyjściowy diagramu zadania w **widoku zadania** i wprowadzić odpowiednie poświadczenia.

   ![Dane wyjściowe zadania usługi Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Eksportowanie zadań do projektu

Istnieją dwa sposoby eksportowania istniejącego zadania do projektu.

1. W **Eksploratorze serwera**w obszarze Zadania analizy strumienia kliknij prawym przyciskiem myszy węzeł zadania. Wybierz **pozycję Eksportuj do nowego projektu analizy strumienia**.
    
   ![Eksportowanie zadania do projektu](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Wygenerowany projekt pojawi się w **Eksploratorze rozwiązań**.
    
   ![Eksplorator rozwiązań](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. W widoku zadania wybierz pozycję **Generuj projekt**.
    
   ![Generowanie projektu z widoku zadania](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Połączenia testowe

Połączenia wejściowe i wyjściowe można przetestować z **widoku zadania,** wybierając opcję z listy rozwijanej **Test Connection.**

   ![Rozwijanie połączenia testowego](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Wyniki **połączenia testowego** są wyświetlane w oknie **Dane wyjściowe.**

   ![Wyniki testu połączenia](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie zadań usługi Azure Stream Analytics i zarządzanie nimi przy użyciu programu Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Samouczek: wdrażanie zadania usługi Azure Stream Analytics z ciągłą integracją/ciągłym wdrażaniem przy użyciu usługi Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Ciągła integracja i ciągłe opracowywanie za pomocą narzędzi usługi Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
