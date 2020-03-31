---
title: Przetwarzanie strumieni danych IoT w czasie rzeczywistym za pomocą usługi Azure Stream Analytics
description: Używanie tagów czujników IoT i strumieni danych z analizą strumieni i przetwarzaniem danych w czasie rzeczywistym
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 0755131f7d8071e37eadc1339ebc5e122725fa71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426240"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Przetwarzanie strumieni danych IoT w czasie rzeczywistym za pomocą usługi Azure Stream Analytics

W tym artykule dowiesz się, jak utworzyć logikę przetwarzania strumienia do zbierania danych z urządzeń Internetu rzeczy (IoT). Użyj rzeczywistego przypadku użycia Internetu rzeczy (IoT), aby zademonstrować, jak szybko i ekonomicznie utworzyć rozwiązanie.

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz bezpłatną [subskrypcję platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Pobierz przykładowe pliki zapytań i danych z [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Scenariusz

Contoso to firma z branży automatyki przemysłowej, która w pełni zautomatyzowała swój proces produkcji. Maszyny w tym zakładzie mają czujniki mogące emitować strumienie danych w czasie rzeczywistym. W tym scenariuszu menedżer warsztatu produkcyjnego chce mieć wgląd w czasie rzeczywistym w dane czujników w celu szukania wzorców i podejmowania na ich podstawie odpowiednich działań. Za pomocą języka SAQL (Stream Analytics Query Language) można znaleźć interesujące wzorce z przychodzącego strumienia danych.

W tym przykładzie dane są generowane z urządzenia tagu czujnika Texas Instruments. Ładunek danych jest w formacie JSON i może wyglądać następująco:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

W rzeczywistym scenariuszu mogą istnieć setki takich czujników generujących zdarzenia jako strumień. W idealnym przypadku urządzenie bramy będzie uruchamiać kod służący do wypychania tych zdarzeń do usługi [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) lub [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Zadanie usługi Stream Analytics pozyska te zdarzenia z usługi Event Hubs i uruchomi zapytania analityki w czasie rzeczywistym względem strumieni. Następnie będzie można wysłać wyniki do jednego z [obsługiwanych wyjść](stream-analytics-define-outputs.md).

W celu ułatwienia pracy w tym przewodniku z wprowadzeniem udostępniono przykładowy plik danych przechwycony z rzeczywistych urządzeń Sensor Tag. Możesz uruchamiać zapytania dotyczące przykładowych danych i przeglądać ich wyniki. W kolejnych samouczkach dowiesz się, jak łączyć zadania z wejściami i wyjściami, a następnie wdrażać je w usłudze platformy Azure.

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [witrynie Azure portal](https://portal.azure.com)wybierz **pozycję + Utwórz zasób** z lewego menu nawigacji. Następnie wybierz **zadanie usługi Stream Analytics** z **Analytics**.
   
    ![Tworzenie nowego zadania usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Wprowadź unikatową nazwę zadania i sprawdź, czy subskrypcja jest poprawna dla zadania. Utwórz nową grupę zasobów lub wybierz istniejącą z subskrypcji.

1. Wybierz lokalizację zadania. Użyj tej samej lokalizacji dla grupy zasobów i wszystkich zasobów, aby zwiększyć szybkość przetwarzania i zmniejszyć koszty. Po dokonaniu konfiguracji wybierz pozycję **Utwórz**.
   
    ![Tworzenie szczegółów nowego zadania usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Tworzenie zapytania usługi Azure Stream Analytics
Następnym krokiem po utworzeniu zadania jest napisanie kwerendy. Można przetestować zapytania względem przykładowych danych bez podłączania danych wejściowych lub wyjściowych do zadania.

Pobierz [HelloWorldASA-InputStream.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) z GitHub. Następnie przejdź do zadania usługi Azure Stream Analytics w witrynie Azure portal.

Z lewego menu **wybierz pozycję Zapytanie** w obszarze **Topologia zadań.** Następnie wybierz **pozycję Przekaż przykładowe dane wejściowe**. Prześlij `HelloWorldASA-InputStream.json` plik i wybierz **ok**.

![Kafelek kwerendy pulpitu nawigacyjnego usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Należy zauważyć, że podgląd danych jest automatycznie wypełniany w tabeli **Podgląd danych wejściowych.**

![Podgląd przykładowych danych wejściowych](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Zapytanie: archiwizowanie danych pierwotnych

Najprostszą postacią zapytania jest przekazywanie, które powoduje archiwizowanie wszystkich danych wejściowych w określonej lokalizacji wyjściowej. Ta kwerenda jest kwerendą domyślną wypełnioną w nowym zadaniu usługi Azure Stream Analytics.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Wybierz **opcję Testuj kwerendę** i wyświetl wyniki w tabeli **Wyniki testu.**

![Wyniki testów dla kwerendy usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Zapytanie: filtrowanie danych na podstawie warunku

Spróbujmy filtrować wyniki na podstawie warunku. Chcielibyśmy pokazać wyniki tylko dla tych zdarzeń, które pochodzą z "sensorA".

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Wklej kwerendę w edytorze i wybierz **opcję Testuj kwerendę,** aby przejrzeć wyniki.

![Filtrowanie strumienia danych](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Zapytanie: wysyłanie alertów w celu wyzwolenia biznesowego przepływu pracy

Zwiększmy szczegółowość naszego zapytania. Dla każdego typu czujnika chcemy monitorować średnią temperaturę co 30 sekund i wyświetlać wyniki tylko wtedy, gdy przekracza ona 100 stopni.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![Zapytanie z filtrowaniem co 30 sekund](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Powinny być widoczne wyniki, które zawierają tylko 245 wierszy i nazwy czujników, gdzie średnia umiarkowany jest większa niż 100. W tym zapytaniu strumień zdarzeń został pogrupowany według wartości **dspl**, czyli nazwy czujnika, i wartości **Okno wirowania** równej 30 sekund. Zapytania czasowe muszą określać, jak chcesz czas na postęp. Za pomocą **klauzuli TIMESTAMP BY,** określono **OUTPUTTIME** kolumny skojarzyć czasy ze wszystkimi obliczeniami czasowymi. Aby uzyskać szczegółowe informacje, przeczytaj informacje o [funkcjach zarządzania czasem](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) i [oknami](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Zapytanie: wykrywanie braku zdarzeń

Jak napisać zapytanie w celu określenia braku zdarzeń wejściowych? Znajdźmy ostatni raz, kiedy czujnik wysłał dane, a następnie nie wysyłał zdarzeń przez następne 5 sekund.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Wykrywanie braku zdarzeń](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

W tym przypadku zostanie użyte sprzężenie **LEFT OUTER** względem tego samego strumienia danych (samosprzężenie). W przypadku sprzężenia **INNER** wynik jest zwracany tylko wtedy, gdy zostanie znalezione dopasowanie.  Natomiast w przypadku sprzężenia **LEFT OUTER**, jeśli zdarzenie z lewej strony sprzężenia jest niedopasowane, dla wszystkich kolumn po prawej stronie wiersza jest zwracany wiersz z wartością NULL. Ta technika jest bardzo przydatna do wyszukiwania braku zdarzeń. Aby uzyskać więcej informacji, zobacz [DOŁĄCZ](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Podsumowanie

Celem tego artykułu jest pokazanie, jak napisać różne zapytania języka zapytań usługi Stream Analytics i zobaczyć wyniki w przeglądarce. Jednak, to tylko po to, aby zacząć. Usługa Stream Analytics obsługuje różne dane wejściowe oraz wyjściowe i może nawet korzystać z funkcji w usłudze Azure Machine Learning, co czyni ją doskonałym narzędziem do analizowania strumieni danych. Aby uzyskać więcej informacji na temat sposobu pisania zapytań, przeczytaj artykuł dotyczący [typowych wzorców zapytań](stream-analytics-stream-analytics-query-patterns.md).

