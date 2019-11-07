---
title: Porównanie funkcji Azure Stream Analytics
description: W tym artykule porównano funkcje obsługiwane w przypadku zadań w chmurze i IoT Edge Azure Stream Analytics w Azure Portal, Visual Studio i Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4eb19a5b344cc5bda5ecad724daaddf9b0000d7e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580907"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porównanie funkcji Azure Stream Analytics

Za pomocą Azure Stream Analytics można tworzyć rozwiązania przesyłania strumieniowego w chmurze i IoT Edge przy użyciu [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)i [Visual Studio Code](quick-create-vs-code.md). W tabelach w tym artykule przedstawiono, które funkcje są obsługiwane przez każdą platformę dla obu typów zadań.

## <a name="cloud-job-features"></a>Funkcje zadania w chmurze


|Funkcja  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Wiele platform     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Tworzenie skryptu     |Tak         |Tak         |Tak         |
|Skrypt IntelliSense     |Podświetlanie składni         |Podświetlanie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |Podświetlanie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |
|Definiowanie danych wejściowych, wyjściowych i konfiguracji zadań     |Tak         |Tak         |Tak         |
|Partycjonowanie danych wyjściowych obiektu BLOB     |Tak         |Tak         |Tak         |
|Power BI jako dane wyjściowe     |Tak         |Tak         |Nie         |
|Dane referencyjne bazy danych SQL     |Tak         |Tak         |Tak         |
|Niestandardowe właściwości komunikatów     |Tak         |Nie         |Nie         |
|Udostępnianie danych wejściowych i wyjściowych w wielu zapytaniach     |Nie         |Tak         |Tak         |
|Java UDF i UDA     |Tak         |Tak         |Tylko Windows         |
|Wywołania Machine Learning     |Tak, ale nie można przetestować zapytania        |Tak, ale nie można przeprowadzić testowania lokalnego         |Nie         |
|Poziom zgodności     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Wbudowane funkcje wykrywania anomalii opartej na ML     |Tak         |Tak         |Tak         |
|Wbudowane funkcje geograficzne     |Tak         |Tak         |Tak         |
|Testowanie zapytań przy użyciu pliku przykładowego     |Tak         |Tak         |Tak         |
|Lokalne testowanie danych na żywo     |Nie         |Tak         |Nie         |
|Wyświetlanie listy zadań i przeglądanie jednostek zadań     |Tak         |Tak         |Tak         |
|Eksportowanie zadania do projektu lokalnego     |Nie         |Tak         |Tak         |
|Przesyłanie, uruchamianie i zatrzymywanie zadań     |Tak         |Tak         |Tak         |
|Kontrola źródła     |Nie         |Tak         |Tak         |
|Obsługa ciągłej integracji/ciągłego wdrażania     |Częściowo         |Tak         |Tak         |
|Wyświetlanie metryk i diagramów zadań     |Tak         |Tak         |Otwórz w portalu         |
|Wyświetl błędy środowiska uruchomieniowego zadania     |Tak         |Tak         |Nie         |
|Dzienniki diagnostyczne     |Tak         |Nie         |Nie         |


## <a name="iot-edge-job-features"></a>Funkcje zadania IoT Edge

|Funkcja  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Tworzenie zadań     |Tak         |Tak         |Nie         |
|Kontrola źródła     |Nie         |Tak         |Nie         |
|Eksportowanie zadania do projektu lokalnego     |Nie         |Tak         |Nie         |
|Testowanie zapytań przy użyciu pliku przykładowego     |Tak         |Tak         |Nie         |
|Udostępnianie danych wejściowych i wyjściowych w wielu zapytaniach     |Nie         |Tak         |Nie         |
|C#UDF     |Nie         |Tak         |Nie         |
|Prześlij zadania     |Tak         |Tak         |Nie         |
|Wyświetlanie listy zadań i przeglądanie jednostek zadań     |Tak         |Tak         |Nie         |
|Wyświetlanie metryk i diagramów zadań     |Tak         |Częściowo         |Nie         |
|Wyświetl błędy środowiska uruchomieniowego zadania     |Tak         |Częściowo         |Nie         |
|Obsługa ciągłej integracji/ciągłego wdrażania     |Nie         |Nie         |Nie         |


## <a name="next-steps"></a>Następne kroki

* [Azure Stream Analytics na urządzeniach IoT Edge](stream-analytics-edge.md)
* [Samouczek: pisanie funkcji C# zdefiniowanej przez użytkownika dla zadania Azure Stream Analytics IoT Edge (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Opracowywanie zadań IoT Edge Stream Analytics przy użyciu narzędzi Visual Studio Tools](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Eksplorowanie Azure Stream Analytics z Visual Studio Code (wersja zapoznawcza)](vscode-explore-jobs.md)


