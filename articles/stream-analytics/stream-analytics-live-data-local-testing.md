---
title: Testowanie danych na żywo za pomocą usługi Azure Stream Analytics dla programu Visual Studio
description: Dowiedz się, jak lokalnie przetestować zadanie usługi Azure Stream Analytics przy użyciu danych przesyłania strumieniowego na żywo.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: f2876ea32bdcd900a454ae6b7ac58c11b8ec67c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840489"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Testowanie danych na żywo lokalnie przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio (Wersja zapoznawcza)

Narzędzia usługi Azure Stream Analytics dla programu Visual Studio umożliwiają testowanie zadań lokalnie z IDE przy użyciu strumieni zdarzeń na żywo z usługi Azure Event Hub, Usługi IoT Hub i magazynu obiektów Blob. Lokalne testy danych na żywo nie mogą zastąpić [testów wydajności i skalowalności,](stream-analytics-streaming-unit-consumption.md) które można wykonać w chmurze, ale można zaoszczędzić czas podczas testowania funkcjonalnego, nie muszą przesyłać do chmury za każdym razem, gdy chcesz przetestować zadanie usługi Stream Analytics. Ta funkcja jest w wersji zapoznawczej i nie powinny być używane dla obciążeń produkcyjnych.

## <a name="testing-options"></a>Opcje testowania

Obsługiwane są następujące opcje testowania lokalnego:

|**Dane wejściowe**  |**Wyjście**  |**Typ zadania**  |
|---------|---------|---------|
|Lokalne dane statyczne   |  Lokalne dane statyczne   |   Chmura/krawędź |
|Dane wejściowe na żywo   |  Lokalne dane statyczne   |   Chmura |
|Dane wejściowe na żywo   |  Dane wyjściowe na żywo   |   Chmura |

## <a name="local-testing-with-live-data"></a>Lokalne testy z danymi na żywo

1. Po utworzeniu [projektu chmury usługi Azure Stream Analytics w programie Visual Studio](stream-analytics-quick-create-vs.md)otwórz plik **script.asaql**. Lokalne testowanie domyślnie używa danych wejściowych lokalnych i danych wyjściowych lokalnych.

   ![Lokalne dane wejściowe usługi Azure Stream Analytics Visual Studio i dane wyjściowe lokalne](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Aby przetestować dane na żywo, wybierz **pozycję Użyj danych wejściowych w chmurze** z listy rozwijanej.

   ![Dane wejściowe w chmurze usługi Azure Stream Analytics Visual Studio na żywo](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Ustaw **godzinę rozpoczęcia,** aby zdefiniować, kiedy zadanie rozpocznie przetwarzanie danych wejściowych. Zadanie może być konieczne do odczytu danych wejściowych z wyprzedzeniem, aby zapewnić dokładne wyniki. Domyślny czas jest ustawiony na 30 minut przed bieżącym czasem.

   ![Czas rozpoczęcia danych usługi Azure Stream Analytics Visual Studio na żywo](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Kliknij pozycję **Uruchom lokalnie**. Pojawi się okno konsoli z uruchomionym postępem i metrykami zadań. Jeśli chcesz zatrzymać proces, możesz to zrobić ręcznie. 

   ![Okno przetwarzania danych na żywo usługi Azure Stream Analytics Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Wyniki danych wyjściowych są odświeżane co trzy sekundy z pierwszych 500 wierszy danych wyjściowych w oknie wyników przebiegu lokalnego, a pliki wyjściowe są umieszczane w folderze **ASALocalRun** ścieżki projektu. Można również otworzyć pliki wyjściowe, klikając przycisk **Otwórz folder wyników** w oknie wyników przebiegu lokalnego.

   ![Folder wyników otwartych danych na żywo usługi Azure Stream Analytics Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Jeśli chcesz wyprowadzić wyniki do pochłaniania danych wyjściowych w chmurze, wybierz **wyjście do chmury** z drugiego pola rozwijanego. Usługi Power BI i usługi Azure Data Lake Storage nie są obsługiwane jako pochłaniacze danych wyjściowych.

   ![Dane danych na żywo usługi Azure Stream Analytics Visual Studio do chmury](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Ograniczenia

* Usługi Power BI i usługi Azure Data Lake Storage nie są obsługiwane pochłaniacze danych wyjściowych z powodu ograniczeń modelu uwierzytelniania.

* Tylko opcje wprowadzania w chmurze mają obsługę [zasad czasu,](stream-analytics-out-of-order-and-late-events.md) podczas gdy lokalne opcje wprowadzania nie.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zadania usługi Stream Analytics przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie za pomocą programu Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
