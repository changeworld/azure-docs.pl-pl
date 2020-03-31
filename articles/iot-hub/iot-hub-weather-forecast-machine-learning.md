---
title: Prognoza pogody przy użyciu usługi Azure Machine Learning z danymi usługi IoT Hub
description: Usługa Azure Machine Learning umożliwia przewidywanie prawdopodobieństwo wystąpienia deszczu na podstawie danych dotyczących temperatury i wilgotności, które centrum IoT zbiera z czujnika.
author: robinsh
manager: philmea
keywords: prognoza pogody uczenia maszynowego
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122218"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Prognoza pogody przy użyciu danych z czujników z centrum IoT w usłudze Azure Machine Learning

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Uczenie maszynowe to technika nauki o danych, która pomaga komputerom uczyć się na podstawie istniejących danych w celu prognozowania przyszłych zachowań, wyników i trendów. Usługa Azure Machine Learning to oparta na chmurze usługa analizy predykcyjnej, która pozwala na szybkie tworzenie i wdrażanie modeli predykcyjnych jako rozwiązań analitycznych.

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak używać usługi Azure Machine Learning do prognozowania pogody (prawdopodobieństwo wystąpienia deszczu) przy użyciu danych o temperaturze i wilgotności z centrum Azure IoT hub. Szansa na deszcz to wyjście przygotowanego modelu prognozowania pogody. Model opiera się na danych historycznych, aby prognozować prawdopodobieństwo deszczu na podstawie temperatury i wilgotności.

## <a name="what-you-do"></a>Co robisz

- Wdrażanie modelu prognozowania pogody jako usługi sieci web.
- Przygotuj centrum IoT do dostępu do danych, dodając grupę odbiorców.
- Utwórz zadanie usługi Stream Analytics i skonfiguruj zadanie w taki sposób:
  - Odczytuj dane dotyczące temperatury i wilgotności z koncentratora IoT Hub.
  - Zadzwoń do serwisu internetowego, aby uzyskać szansę na deszcz.
  - Zapisz wynik w magazynie obiektów blob platformy Azure.
- Użyj Eksploratora magazynu platformy Microsoft Azure, aby wyświetlić prognozę pogody.

## <a name="what-you-need"></a>Co jest potrzebne

- Ukończ samouczek [symulatora online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jeden z samouczków urządzenia; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Centrum Usługi Azure IoT w ramach subskrypcji.
  - Aplikacja kliencka, która wysyła wiadomości do centrum Usługi Azure IoT Hub.
- Konto [usługi Azure Machine Learning Studio (klasyczne).](https://studio.azureml.net/)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Wdrażanie modelu prognozowania pogody jako usługi sieci web

W tej sekcji można uzyskać model prognozowania pogody z biblioteki sztucznej inteligencji platformy Azure. Następnie należy dodać moduł skryptu R do modelu, aby wyczyścić dane temperatury i wilgotności. Na koniec można wdrożyć model jako predykcyjnej usługi sieci web.

### <a name="get-the-weather-prediction-model"></a>Pobierz model prognozowania pogody

W tej sekcji można uzyskać model prognozowania pogody z galerii sztucznej inteligencji platformy Azure i otworzyć go w usłudze Azure Machine Learning Studio (klasyczny).

1. Przejdź do [strony modelu prognozowania pogody](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Otwórz stronę modelu prognozowania pogody w galerii sztucznej inteligencji platformy Azure](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Kliknij **przycisk Otwórz w Studio (klasyczny),** aby otworzyć model w programie Microsoft Azure Machine Learning Studio (klasyczny).

   ![Otwórz model prognozowania pogody w usłudze Azure Machine Learning Studio (klasyczny)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Dodawanie modułu skryptu R do czystych danych temperatury i wilgotności

Aby model zachowywał się poprawnie, dane temperatury i wilgotności muszą być konwertowane na dane liczbowe. W tej sekcji należy dodać moduł skryptu R do modelu prognozowania pogody, który usuwa wszystkie wiersze, które mają wartości danych dla temperatury lub wilgotności, których nie można przekonwertować na wartości liczbowe.

1. Po lewej stronie okna usługi Azure Machine Learning Studio kliknij strzałkę, aby rozwinąć panel narzędzi. Wpisz "Wykonaj" w polu wyszukiwania. Wybierz moduł **Wykonaj skrypt R.**

   ![Wybierz moduł Wykonaj skrypt R](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Przeciągnij moduł **Wykonaj skrypt R** w pobliżu modułu Clean Missing **Data** i istniejącego modułu **Wykonaj skrypt R** na diagramie. Usuń połączenie między **modułami Clean Missing Data** i Execute R **Script,** a następnie połącz wejścia i wyjścia nowego modułu, jak pokazano na rysunku.

   ![Dodaj moduł skryptu R Execute](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Wybierz nowy moduł **Wykonaj skrypt R,** aby otworzyć jego okno właściwości. Skopiuj i wklej następujący kod do pola **Skrypt języka R.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Po zakończeniu okno właściwości powinno wyglądać podobnie do następującego:

   ![Dodaj kod do modułu Wykonywanie skryptu R](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Wdrażanie predykcyjnej usługi sieci web

W tej sekcji należy sprawdzić poprawność modelu, skonfigurować predykcyjną usługę sieci web na podstawie modelu, a następnie wdrożyć usługę sieci web.

1. Kliknij **przycisk Uruchom,** aby sprawdzić poprawność kroków w modelu. Ten krok może potrwać kilka minut.

   ![Uruchom eksperyment, aby sprawdzić poprawność kroków](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Kliknij **pozycję KONFIGUROWANIE usługi** > sieci**WEB Predictive Web Service**. Zostanie otwarty diagram eksperymentu predykcyjnego.

   ![Wdrażanie modelu prognozowania pogody w usłudze Azure Machine Learning Studio (klasyczny)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Na diagramie eksperymentu predykcyjnego usuń połączenie między modułem **wejściowym usługi sieci Web** a **zestawem danych pogody** u góry. Następnie przeciągnij moduł **wejściowy usługi sieci Web** w pobliżu modułu **Score Model** i połącz go w sposób pokazany na rysunku:

   ![Połącz dwa moduły w usłudze Azure Machine Learning Studio (klasyczny)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Kliknij **przycisk URUCHOM,** aby sprawdzić poprawność kroków w modelu.

1. Kliknij **pozycję DEPLOY WEB SERVICE,** aby wdrożyć model jako usługę sieci web.

1. Na pulpicie nawigacyjnym modelu pobierz **skoroszyt programu Excel 2010 lub wcześniejszy** dla **żądania/odpowiedzi**.

   > [!Note]
   > Upewnij się, że pobierasz **skoroszyt programu Excel 2010 lub wcześniejszy,** nawet jeśli na komputerze jest uruchomiona nowsza wersja programu Excel.

   ![Pobieranie programu Excel dla punktu końcowego ODPOWIEDZI NA ŻĄDANIE](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Otwórz skoroszyt programu Excel, zanotuj **adres URL usługi sieci WEB** i klucz **dostępu**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, konfigurowanie i uruchamianie zadania usługi Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij kolejno pozycje **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.
1. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów:** Użyj tej samej grupy zasobów, której używa centrum IoT Hub.

   **Lokalizacja:** Użyj tej samej lokalizacji co grupa zasobów.

   **Przypnij do pulpitu nawigacyjnego**: zaznacz tę opcję, aby mieć łatwy dostęp do centrum IoT Hub z pulpitu nawigacyjnego.

   ![Tworzenie zadania usługi Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Kliknij przycisk **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie usługi Stream Analytics.
1. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.
1. W okienku **Dane wejściowe** kliknij pozycję **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wejściowy**: Unikatowy alias danych wejściowych.

   **Źródło**: Wybierz **centrum IoT**.

   **Grupa odbiorców:** Wybierz utworzoną grupę odbiorców.

   ![Dodawanie danych wejściowych do zadania usługi Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Kliknij przycisk **Utwórz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**.
1. W okienku **Dane wyjściowe** kliknij pozycję **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wyjściowy**: unikatowy alias danych wyjściowych.

   **Ujście**: Wybierz **magazyn obiektów blob**.

   **Konto magazynu:** konto magazynu dla magazynu obiektów blob. Można utworzyć konto magazynu lub użyć istniejącego.

   **Kontener:** Kontener, w którym jest zapisywany obiekt blob. Można utworzyć kontener lub użyć istniejącego.

   **Format serializacji zdarzeń**: Wybierz **CSV**.

   ![Dodawanie danych wyjściowych do zadania usługi Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Kliknij przycisk **Utwórz**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Dodawanie funkcji do zadania Usługi Stream Analytics w celu wywołania wdrożonej usługi sieci web

1. W obszarze **Topologia zadań**kliknij pozycję**Dodawanie** **funkcji** > .
1. Wprowadź następujące informacje:

   **Alias funkcji** `machinelearning`: Wprowadź .

   **Typ funkcji:** Wybierz **pozycję Azure ML**.

   **Opcja importu**: Wybierz **pozycję Importuj z innej subskrypcji**.

   **ADRES URL**: Wprowadź adres URL usługi sieci WEB odnotowany w skoroszycie programu Excel.

   **Klucz:** Wprowadź klucz dostępu, który został odnotowany w skoroszycie programu Excel.

   ![Dodawanie funkcji do zadania usługi Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Kliknij przycisk **Utwórz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Zapytanie**.
1. Zastąp istniejący kod następującym kodem:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.

   Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.

1. Kliknij przycisk **Zapisz**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu Usługi Stream Analytics kliknij przycisk **Rozpocznij** > **teraz** > **start**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania usługi Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Wyświetlanie prognozy pogody za pomocą Eksploratora magazynu platformy Microsoft Azure

Uruchom aplikację kliencką, aby rozpocząć zbieranie i wysyłanie danych temperatury i wilgotności do centrum IoT hub. Dla każdej wiadomości odbieranej przez centrum IoT zadanie usługi Stream Analytics wywołuje usługę sieci web prognozy pogody, aby uzyskać ryzyko wystąpienia deszczu. Wynik jest następnie zapisywany w magazynie obiektów blob platformy Azure. Eksplorator usługi Azure Storage to narzędzie, którego można użyć do wyświetlenia wyniku.

1. [Pobierz i zainstaluj Eksploratora usługi Microsoft Azure Storage .](https://storageexplorer.com/)
1. Otwórz Eksploratora usługi Azure Storage.
1. Zaloguj się do swojego konta platformy Azure.
1. Wybierz subskrypcję.
1. Kliknij konto > **magazynu** > subskrypcji > konto magazynu > **kontenery obiektów blob** > kontenerze.
1. Pobierz plik csv, aby zobaczyć wynik. Ostatnia kolumna rejestruje prawdopodobieństwo wystąpienia opadów deszczu.

   ![Uzyskaj wyniki prognozy pogody dzięki usłudze Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Podsumowanie

Usługa Azure Machine Learning została pomyślnie wykorzystana do uzyskania prawdopodobieńs okazji do wystąpienia deszczu na podstawie danych dotyczących temperatury i wilgotności, które otrzymuje centrum IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]