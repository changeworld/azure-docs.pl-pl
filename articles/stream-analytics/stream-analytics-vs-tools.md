---
title: Wyświetl zadania usługi Azure Stream Analytics w programie Visual Studio
description: W tym artykule opisano sposób wyświetlania zadań usługi Stream Analytics w programie Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 1c7133801eb0d95616cacf501162e6cee3da7c80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477908"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Wyświetl zadania usługi Azure Stream Analytics przy użyciu programu Visual Studio

Narzędzia platformy Azure Stream Analytics dla programu Visual Studio ułatwia deweloperom zarządzanie ich zadań usługi Stream Analytics bezpośrednio z poziomu środowiska IDE. Za pomocą narzędzi Azure Stream Analytics możesz wykonywać następujące czynności:
- [Utwórz nowe zadania](stream-analytics-quick-create-vs.md)
- Uruchom, Zatrzymaj, a [monitorowanie zadań](stream-analytics-monitor-jobs-use-vs.md)
- Sprawdź wyniki zadania
- Istniejące zadania eksportu do projektu
- Testuj połączenia wejściowe i wyjściowe
- [Lokalnie uruchamiać zapytania](stream-analytics-vs-tools-local-run.md)

Dowiedz się, jak [Instalowanie narzędzi Azure Stream Analytics dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Zapoznaj się z widoku zadania

Widok zadania można użyć do interakcji z zadań usługi Azure Stream Analytics z programu Visual Studio.

### <a name="open-the-job-view"></a>Powoduje ono otwarcie widoku zadania

1. W **Eksploratora serwera**, wybierz opcję **zadania usługi Stream Analytics** , a następnie wybierz **Odśwież**. Zadania powinna zostać wyświetlona w obszarze **zadania usługi Stream Analytics**.

    ![Stream Analytics server explorer listy](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. Rozwiń węzeł zadania i kliknij dwukrotnie **widok zadania** węzeł, aby otworzyć widok zadania.
    
   ![Zadanie rozwinięty węzeł](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Uruchamianie i zatrzymywanie zadań

Zadania usługi Azure Stream Analytics może być w pełni zarządzane w widoku zadania w programie Visual Studio. Użyj opcji, aby uruchomić, zatrzymać lub usunąć zadania.
    
   ![Stream Analytics zadania formantów](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>Sprawdź wyniki zadania

Narzędzia Stream Analytics tools for Visual Studio obsługuje obecnie danych wyjściowych w wersji zapoznawczej dla usługi Azure Data Lake Storage i blob storage. Aby wyświetlić wynik, po prostu kliknij dwukrotnie węzeł danych wyjściowych na diagramie zadania w **widok zadania** i wprowadź odpowiednie poświadczenia.

   ![Stream Analytics zadanie obiektów blob w danych wyjściowych](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>Eksportuj zadania do projektu

Istnieją dwa sposoby, możesz wyeksportować istniejące zadanie do projektu.

1. W **Eksploratora serwera**, w węźle zadania usługi Stream Analytics, kliknij prawym przyciskiem myszy węzeł zadania. Wybierz **Eksportuj do nowego projektu analizy Stream**.
    
   ![Eksportuj zadanie do projektu](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Wygenerowany projekt, który jest wyświetlany w **Eksploratora rozwiązań**.
    
   ![Eksplorator rozwiązań](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. W widoku zadania, wybierz **Generowanie projektu**.
    
   ![Generowanie projektu w widoku zadania](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Testuj połączenia

Połączenia wejściowe i wyjściowe mogą być testowane z **widok zadania** , wybierając opcję z **Testuj połączenie** listy rozwijanej.

   ![Test połączenia z listy rozwijanej](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

**Testuj połączenie** wyniki są wyświetlane w **dane wyjściowe** okna.

   ![Połączenia wyników testu](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Kolejne kroki

* [Monitorowanie i zarządzanie nimi zadań usługi Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Szybki start: Tworzenie zadania usługi Stream Analytics, za pomocą programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Samouczek: Wdrażanie zadania usługi Azure Stream Analytics przy użyciu ciągłej integracji/ciągłego wdrażania za pomocą potoków usługi Azure](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Ciągła integracja i ciągłe opracowywanie za pomocą narzędzi usługi Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
