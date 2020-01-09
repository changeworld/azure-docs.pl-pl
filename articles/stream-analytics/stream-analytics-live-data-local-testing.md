---
title: Testowanie danych na żywo z usługą Azure Stream Analytics dla programu Visual Studio
description: Dowiedz się, jak przetestować zadanie usługi Azure Stream Analytics lokalnie za pomocą danych transmisji strumieniowej na żywo.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: cda02408f9e90cb9f19a4f7d806a34e5cff9c33c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426035"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Testowanie danych na żywo lokalnie przy użyciu usługi Azure Stream Analytics tools for Visual Studio (wersja zapoznawcza)

Narzędzia platformy Azure Stream Analytics dla programu Visual Studio umożliwia przetestowanie zadań lokalnie z IDE za pomocą strumieni na żywo zdarzeń z usługi Azure Event Hub, IoT Hub i Blob Storage. Dane lokalne testowanie nie można zamienić na żywo [testowanie wydajności i skalowalności](stream-analytics-streaming-unit-consumption.md) można wykonywać w chmurze, ale możesz zaoszczędzić czas podczas testowania funkcjonalnego, ponieważ nie ma potrzeby przesłać do chmury, każdym razem, którą chcesz przetestować usługi Stream Zadanie analizy. Ta funkcja jest dostępna w wersji zapoznawczej i nie powinny być używane dla obciążeń produkcyjnych.

## <a name="testing-options"></a>Opcje testowania

Obsługiwane są następujące opcje testowania lokalnego:

|**Dane wejściowe**  |**Dane wyjściowe**  |**Typ zadania**  |
|---------|---------|---------|
|Lokalnych danych statycznych   |  Lokalnych danych statycznych   |   Chmura/Microsoft Edge |
|Dane wejściowe na żywo   |  Lokalnych danych statycznych   |   Chmura |
|Dane wejściowe na żywo   |  Dane wyjściowe na żywo   |   Chmura |

## <a name="local-testing-with-live-data"></a>Lokalne testowanie przy użyciu bieżących danych

1. Po utworzeniu [projektu w programie Visual Studio w chmurze usługi Azure Stream Analytics](stream-analytics-quick-create-vs.md), otwórz **script.asaql**. Lokalne testowanie używa lokalnych danych wejściowych i wyjściowych lokalnym domyślnie.

   ![Usługa Azure lokalne Stream Analytics, Visual Studio, dane wejściowe i lokalnych danych wyjściowych](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Aby przetestować dane na żywo, wybierz **użycia chmury w danych wejściowych** w polu listy rozwijanej.

   ![Usługa Azure Stream Analytics, Visual Studio chmurze na żywo w danych wejściowych](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Ustaw **czas rozpoczęcia** do zdefiniowania, kiedy zadanie zostanie uruchomione, przetwarzania danych wejściowych. Zadanie może być konieczne odczytanie danych wejściowych z wyprzedzeniem, aby zapewnić dokładne wyniki. Domyślny czas jest ustawiona na 30 minut przed bieżącym czasem.

   ![Czas rozpoczęcia danych na żywo usługi Azure Stream Analytics, Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Kliknij przycisk **uruchamiane lokalnie**. Za pomocą uruchomionej metryk postępu i zadania pojawi się okno konsoli. Jeśli chcesz zatrzymać proces, możesz to zrobić ręcznie. 

   ![Okno procesu na żywo dane w usłudze Azure Stream Analytics, Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Wyniki dane wyjściowe są odświeżane co trzy sekundy z pierwszych wierszy 500 danych wyjściowych w oknie wyniku lokalnego uruchomienia i pliki wyjściowe są umieszczane w ścieżce projektu **ASALocalRun** folderu. Pliki wyjściowe można również otworzyć, klikając **Otwórz Folder wyników** przycisk w oknie wyniku lokalnego uruchomienia.

   ![Danych na żywo usługi Azure Stream Analytics, Visual Studio, otwórz folder wyników](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Aby przekazywać wyniki do ujść danych wyjściowych z chmury, należy wybrać **dane wyjściowe do chmury** w drugim polu listy rozwijanej. Usługa Power BI i usługi Azure Data Lake Storage nie są ujść obsługiwanych danych wyjściowych.

   ![Danych platformy Azure Stream Analytics, Visual Studio na żywo dane wyjściowe do chmury](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Ograniczenia

* Usługa Power BI i usługi Azure Data Lake Storage nie są ujść obsługiwanych danych wyjściowych ze względu na ograniczenia model uwierzytelniania.

* Tylko wejściowych opcje usług w chmurze mają [czasu zasady](stream-analytics-out-of-order-and-late-events.md) obsługują, natomiast nie obsługują opcji lokalnego danych wejściowych.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zadania usługi Stream Analytics przy użyciu narzędzi Azure Stream Analytics dla programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalowanie narzędzi Azure Stream Analytics dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie z programem Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
