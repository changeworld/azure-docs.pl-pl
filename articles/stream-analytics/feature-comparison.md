---
title: Porównanie funkcji Azure Stream Analytics
description: W tym artykule porównano funkcje obsługiwane w przypadku zadań w chmurze i IoT Edge Azure Stream Analytics w Azure Portal, Visual Studio i Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8cdba27ac949584e1fa96e3f7b0874f4fc0d4212
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443654"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porównanie funkcji Azure Stream Analytics

Za pomocą Azure Stream Analytics można tworzyć rozwiązania przesyłania strumieniowego w chmurze i IoT Edge przy użyciu [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)i [Visual Studio Code](quick-create-vs-code.md). W tabelach w tym artykule przedstawiono, które funkcje są obsługiwane przez każdą platformę dla obu typów zadań.

## <a name="cloud-job-features"></a>Funkcje zadania w chmurze


|Funkcja  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Wiele platform     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Tworzenie skryptu     |Tak         |Tak         |Tak         |
|Skrypt IntelliSense     |Wyróżnianie składni         |Wyróżnianie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |Wyróżnianie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |
|Zdefiniuj wszystkie typy danych wejściowych, wyjść i konfiguracji zadań     |Tak         |Tak         |Tak         |
|Kontrola źródła     |Nie         |Tak         |Tak         |
|Obsługa ciągłej integracji/ciągłego wdrażania     |Częściowo         |Tak         |Tak         |
|Udostępnianie danych wejściowych i wyjściowych w wielu zapytaniach     |Nie         |Tak         |Tak         |
|Testowanie zapytań przy użyciu pliku przykładowego     |Tak         |Tak        |Tak         |
|Lokalne testowanie danych na żywo     |Nie         |Tak       |Tak      |
|Wyświetlanie listy zadań i przeglądanie jednostek zadań     |Tak         |Tak        |Tak         |
|Eksportowanie zadania do projektu lokalnego     |Nie         |Tak         |Tak         |
|Przesyłanie, uruchamianie i zatrzymywanie zadań     |Tak         |Tak         |Tak         |
|Wyświetlanie metryk i diagramów zadań     |Tak         |Tak         |Otwórz w portalu         |
|Wyświetl błędy środowiska uruchomieniowego zadania     |Tak         |Tak         |Nie         |
|Dzienniki diagnostyczne     |Tak         |Nie         |Nie         |
|Niestandardowe właściwości komunikatów     |Tak         |Tak         |Nie       |
|C#Funkcja kodu niestandardowego i deserializacja|Tryb tylko do odczytu|Tak|Nie|
|Java UDF i UDA     |Tak         |Tak         |Tylko Windows         |
|Usługa Machine Learning     |Tak, ale nie można przetestować zapytania        |Tak         |Nie         |
|Machine Learning Studio     |Tak, ale nie można przetestować zapytania        |Tak |Nie         |
|Poziom zgodności     |1.0</br>1.1</br>1,2 (wartość domyślna)         |1.0</br>1.1</br>1,2 (wartość domyślna)           |1.0</br>1.1</br>1,2 (wartość domyślna)           |
|Wbudowane funkcje wykrywania anomalii opartej na ML     |Tak         |Tak         |Tak         |
|Wbudowane funkcje geograficzne     |Tak         |Tak         |Tak         |



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

* [Azure Stream Analytics w usłudze IoT Edge](stream-analytics-edge.md)
* [Samouczek: pisanie funkcji C# zdefiniowanej przez użytkownika dla zadania Azure Stream Analytics IoT Edge (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Opracowywanie zadań IoT Edge Stream Analytics przy użyciu narzędzi Visual Studio Tools](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Eksplorowanie Azure Stream Analytics z Visual Studio Code (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)


