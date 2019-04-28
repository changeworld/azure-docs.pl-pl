---
title: Korzystanie ze strumieni danych IoT w czasie rzeczywistym za pomocą usługi Azure Stream Analytics
description: Używanie tagów czujników IoT i strumieni danych z analizą strumieni i przetwarzaniem danych w czasie rzeczywistym
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: b883dcf941683d6065c9b6ee5075d2a358f3452a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481499"
---
# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Wprowadzenie do usługi Azure Stream Analytics służącej do przetwarzania danych z urządzeń IoT
Korzystając z tego samouczka, nauczysz się tworzyć logikę przetwarzania strumieni w celu zbierania danych z urządzeń Internetu rzeczy (IoT). Użyjemy w nim rzeczywistych przypadków użycia Internetu rzeczy (IoT) w celu zademonstrowania szybkiego i ekonomicznego kompilowania rozwiązań.

## <a name="prerequisites"></a>Wymagania wstępne
* [Subskrypcja platformy Azure](https://azure.microsoft.com/pricing/free-trial/)
* Przykładowe pliki zapytań i danych do pobrania z serwisu [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Scenariusz
Contoso to firma z branży automatyki przemysłowej, która w pełni zautomatyzowała swój proces produkcji. Maszyny w tym zakładzie mają czujniki mogące emitować strumienie danych w czasie rzeczywistym. W tym scenariuszu menedżer warsztatu produkcyjnego chce mieć wgląd w czasie rzeczywistym w dane czujników w celu szukania wzorców i podejmowania na ich podstawie odpowiednich działań. Dla danych czujników będzie używany język zapytań usługi Stream Analytics (SAQL) w celu znalezienia interesujących wzorców w przychodzącym strumieniu danych.

W tym przykładzie dane są generowane z urządzenia Texas Instruments Sensor Tag. Ładunek danych jest w formacie JSON i może wyglądać następująco:

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
1. W witrynie [Azure Portal](https://portal.azure.com) kliknij znak plus, a następnie wpisz **STREAM ANALYTICS** w oknie tekstowym z prawej strony. Następnie na liście wyników wybierz pozycję **Zadanie Stream Analytics**.
   
    ![Tworzenie nowego zadania usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)
2. Wprowadź unikatową nazwę zadania i sprawdź, czy subskrypcja jest poprawna dla zadania. Następnie utwórz nową grupę zasobów lub wybierz istniejącą w subskrypcji.
3. Następnie wybierz lokalizację dla zadania. W celu przyspieszenia i redukcji kosztu transferu danych zalecany jest wybór tej samej lokalizacji, w której znajduje się grupa zasobów i docelowe konto magazynu.
   
    ![Tworzenie szczegółów nowego zadania usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)
   
   > [!NOTE]
   > W każdym regionie utwórz to konto magazynu tylko raz. Ten magazyn zostanie udostępniony wszystkim zadaniom usługi Stream Analytics tworzonym w danym regionie.
   > 
   > 
4. Zaznacz pole wyboru, aby umieścić zadanie na pulpicie nawigacyjnym, a następnie kliknij pozycję **UTWÓRZ**.
   
    ![Trwa tworzenie zadania usługi Stream analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)
5. W prawym górnym rogu okna przeglądarki powinien być widoczny komunikat „Wdrażanie rozpoczęte...”. Wkrótce zostanie on zmieniony na okna ukończenia przedstawione poniżej.
   
    ![Stream analytics wdrażanie zakończyło się pomyślnie](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

## <a name="create-an-azure-stream-analytics-query"></a>Tworzenie zapytania usługi Azure Stream Analytics
Po utworzeniu zadania należy je otworzyć i utworzyć zapytanie. Dostęp do zadania można łatwo uzyskać, klikając w tym celu kafelek.

![Stream Analytics Kafelek zadania w witrynie Azure portal](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

W okienku **Topologia zadań** kliknij pozycję **ZAPYTANIE**, aby przejść do edytora zapytań. Edytor **ZAPYTANIE** umożliwia wprowadzenie zapytania T-SQL, które wykonuje przekształcenie danych przychodzących zdarzenia.

![Stream Analytics pulpit nawigacyjny zapytania kafelka](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Zapytanie: Archiwizowanie danych pierwotnych
Najprostszą postacią zapytania jest przekazywanie, które powoduje archiwizowanie wszystkich danych wejściowych w określonej lokalizacji wyjściowej. Pobierz przykładowy plik danych z usługi [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) do lokalizacji na komputerze. 

1. Wklej zapytanie z pliku PassThrough.txt. 
   
    ![Wklej zapytanie w edytorze zapytań usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)
2. Kliknij przycisk z wielokropkiem obok wprowadzanych danych i zaznacz pole **Przekaż dane przykładowe z pliku**.
   
    ![Wybierz pozycję Przekaż przykładowe dane z pliku](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)
3. W rezultacie zostanie otwarte okienko po prawej stronie. W tym okienku wybierz plik danych HelloWorldASA-InputStream.json z lokalizacji pobierania i kliknij przycisk **OK** u dołu okienka.
   
    ![Przekaż plik danych przykładowych json](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)
4. Następnie kliknij koło zębate **Test** w lewym górnym rogu okna i przetwórz zapytanie testowe względem zestawu danych przykładowych. Po zakończeniu przetwarzania pod zapytaniem zostanie otwarte okno wyników.
   
    ![Wyniki testu dla zapytania usługi Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Zapytanie: Filtrowanie danych na podstawie warunku
Poniżej przedstawiono filtrowanie wyników na podstawie warunku. Chcielibyśmy wyświetlić wyniki tylko dla tych zdarzeń, które pochodzą z czujnika "sensorA". Zapytanie znajduje się w pliku Filtering.txt.

![Filtrowanie strumienia danych](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Zauważ, że zapytanie uwzględniające wielkość liter porównuje wartość ciągu. Kliknij ponownie koło zębate **Test**, aby wykonać zapytanie. Zapytanie powinno zwrócić 389 wierszy z 1860 zdarzeń.

![Drugi zestaw danych wyjściowych z testu zapytania](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Zapytanie: Wysyłanie alertów w celu wyzwolenia biznesowego przepływu pracy
Zwiększmy szczegółowość naszego zapytania. Dla każdego typu czujnika chcemy monitorować średnią temperaturę co 30 sekund i wyświetlać wyniki tylko wtedy, gdy przekracza ona 100 stopni. W tym celu napiszemy poniższe zapytanie, a następnie w celu wyświetlenia wyników klikniemy pozycję **Test**. Zapytanie znajduje się w pliku ThresholdAlerting.txt.

![Zapytanie z filtrowaniem co 30 sekund](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Wyniki powinny teraz zawierać tylko 245 wierszy i nazwy czujników, w przypadku których średnia temperatura przekracza 100 stopni. W tym zapytaniu strumień zdarzeń został pogrupowany według wartości **dspl**, czyli nazwy czujnika, i wartości **Okno wirowania** równej 30 sekund. W zapytaniu dotyczącym danych czasowych należy określić sposób przyrastania czasu. Użyliśmy klauzuli **TIMESTAMP BY** i wybraliśmy kolumnę **OUTPUTTIME**, aby skojarzyć czasy ze wszystkimi obliczeniami danych czasowych. Aby uzyskać szczegółowe informacje, przeczytaj w witrynie MSDN artykuły dotyczące [zarządzania czasem](https://msdn.microsoft.com/library/azure/mt582045.aspx) i [funkcji obsługi okien](https://msdn.microsoft.com/library/azure/dn835019.aspx).

### <a name="query-detect-absence-of-events"></a>Zapytanie: Wykrywanie braku zdarzeń
Jak napisać zapytanie w celu określenia braku zdarzeń wejściowych? Spróbujmy określić, kiedy ostatnio czujnik wysłał dane, a następnie przez kolejnych 5 sekund nie wysłał zdarzeń. Zapytanie znajduje się w pliku AbsenceOfEvent.txt.

![Wykrywanie braku zdarzeń](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

W tym przypadku zostanie użyte sprzężenie **LEFT OUTER** względem tego samego strumienia danych (samosprzężenie). W przypadku sprzężenia **INNER** wynik jest zwracany tylko wtedy, gdy zostanie znalezione dopasowanie.  Natomiast w przypadku sprzężenia **LEFT OUTER**, jeśli zdarzenie z lewej strony sprzężenia jest niedopasowane, dla wszystkich kolumn po prawej stronie wiersza jest zwracany wiersz z wartością NULL. Ta technika jest bardzo przydatna do wyszukiwania braku zdarzeń. Aby uzyskać więcej informacji na temat sprzężenia [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx), zobacz dokumentację w witrynie MSDN.

## <a name="conclusion"></a>Podsumowanie
Celem tego samouczka jest zademonstrowanie sposobu pisania różnych zapytań języka zapytań usługi Stream Analytics i wyświetlania wyników w przeglądarce. Jest to jednak tylko początek. Za pomocą usługi Stream Analytics można zrobić dużo więcej. Usługa Stream Analytics obsługuje różne dane wejściowe oraz wyjściowe i może nawet korzystać z funkcji w usłudze Azure Machine Learning, co czyni ją doskonałym narzędziem do analizowania strumieni danych. Jeśli chcesz dowiedzieć się więcej na temat usługi Stream Analytics, skorzystaj z naszej [mapy uczenia](https://docs.microsoft.com/azure/stream-analytics/). Aby uzyskać więcej informacji na temat sposobu pisania zapytań, przeczytaj artykuł dotyczący [typowych wzorców zapytań](stream-analytics-stream-analytics-query-patterns.md).

