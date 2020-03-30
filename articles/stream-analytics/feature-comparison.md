---
title: Porównanie funkcji usługi Azure Stream Analytics
description: W tym artykule porównano funkcje obsługiwane dla zadań chmury usługi Azure Stream Analytics i usługi IoT Edge w witrynie Azure portal, visual studio i kodze programu Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: cdae6a3b7319aefa9d4f19b5d613d1afb8b6804a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235308"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porównanie funkcji usługi Azure Stream Analytics

Za pomocą usługi Azure Stream Analytics można tworzyć rozwiązania do przesyłania strumieniowego w chmurze i w przeglądarce IoT Edge za pomocą [portalu Azure,](stream-analytics-quick-create-portal.md) [programu Visual Studio](stream-analytics-quick-create-vs.md)i kodu programu Visual [Studio.](quick-create-vs-code.md) Tabele w tym artykule pokazują, które funkcje są obsługiwane przez każdą platformę dla obu typów zadań.

## <a name="cloud-job-features"></a>Funkcje zadań w chmurze


|Funkcja  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Platforma krzyżowa     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Tworzenie skryptów     |Tak         |Tak         |Tak         |
|Skrypt Intellisense     |Wyróżnianie składni         |Wyróżnianie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |Wyróżnianie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |
|Definiowanie wszystkich typów wejść, wyjść i konfiguracji zadań     |Tak         |Tak         |Tak         |
|Kontrola źródła     |Nie         |Tak         |Tak         |
|Obsługa ciągłej integracji/ciągłej integracji/cd     |Częściowo         |Tak         |Tak         |
|Udostępnianie danych wejściowych i wyjściowych w wielu kwerendach     |Nie         |Tak         |Tak         |
|Testowanie kwerend przy za pomocą przykładowego pliku     |Tak         |Tak        |Tak         |
|Lokalne testy danych na żywo     |Nie         |Tak       |Tak      |
|Wyświetlanie zadań i wyświetlanie encji zadań     |Tak         |Tak        |Tak         |
|Eksportowanie zadania do projektu lokalnego     |Nie         |Tak         |Tak         |
|Prześlij, rozpocznij i zatrzymaj zadania     |Tak         |Tak         |Tak         |
|Wyświetlanie metryk zadań i diagramu     |Tak         |Tak         |Otwórz w portalu         |
|Wyświetlanie błędów środowiska uruchomieniowego zadania     |Tak         |Tak         |Nie         |
|Dzienniki diagnostyczne     |Tak         |Nie         |Nie         |
|Niestandardowe właściwości wiadomości     |Tak         |Tak         |Nie       |
|C# niestandardowa funkcja kodu i Deserializer|Tryb tylko do odczytu|Tak|Nie|
|JavaScript UDF i UDA     |Tak         |Tak         |Tylko Windows         |
|Machine Learning Service     |Tak        |Tak         |Nie         |
|Machine Learning Studio     |Tak, ale kwerendy nie można przetestować        |Tak |Nie         |
|Poziom zgodności     |1.0</br>1.1</br>1.2 (domyślnie)         |1.0</br>1.1</br>1.2 (domyślnie)           |1.0</br>1.1</br>1.2 (domyślnie)           |
|Wbudowane funkcje wykrywania anomalii oparte na technologii ML     |Tak         |Tak         |Tak         |
|Wbudowane funkcje geoprzestrzenne     |Tak         |Tak         |Tak         |



## <a name="iot-edge-job-features"></a>Funkcje zadań IoT Edge

|Funkcja  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Tworzenie zadań     |Tak         |Tak         |Nie         |
|Kontrola źródła     |Nie         |Tak         |Nie         |
|Eksportowanie zadania do projektu lokalnego     |Nie         |Tak         |Nie         |
|Testowanie kwerend przy za pomocą przykładowego pliku     |Tak         |Tak         |Nie         |
|Udostępnianie danych wejściowych i wyjściowych w wielu kwerendach     |Nie         |Tak         |Nie         |
|C# UDF     |Nie         |Tak         |Nie         |
|Przesyłanie ofert pracy     |Tak         |Tak         |Nie         |
|Wyświetlanie zadań i wyświetlanie encji zadań     |Tak         |Tak         |Nie         |
|Wyświetlanie metryk zadań i diagramu     |Tak         |Częściowo         |Nie         |
|Wyświetlanie błędów środowiska uruchomieniowego zadania     |Tak         |Częściowo         |Nie         |
|Obsługa ciągłej integracji/ciągłej integracji/cd     |Nie         |Nie         |Nie         |


## <a name="next-steps"></a>Następne kroki

* [Azure Stream Analytics na urządzeniach IoT Edge](stream-analytics-edge.md)
* [Samouczek: Napisz funkcję zdefiniowaną przez użytkownika w języku C# dla zadania usługi Azure Stream Analytics IoT Edge (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Tworzenie zadań usługi Stream Analytics IoT Edge przy użyciu narzędzi programu Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Eksploruj usługę Azure Stream Analytics za pomocą kodu programu Visual Studio (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)


