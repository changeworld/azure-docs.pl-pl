---
title: Pogody prognozy przy użyciu danych z Centrum IoT przy użyciu usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Machine Learning przewidzieć ryzyko ustaniu oparte na danych temperatury i wilgotności, które z czujnika zbiera dane Centrum IoT.
author: rangv
manager: ''
keywords: uczenie maszynowe prognozie pogody
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: a331f8a8a69ffe41a368c1b36f1680890aaac8bf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637671"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Pogody prognozy przy użyciu danych czujnika z Centrum IoT w usłudze Azure Machine Learning

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning to technika analizy danych, która pomaga komputerom z istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Usługa Azure Machine Learning to oparta na chmurze usługa analizy predykcyjnej, która pozwala na szybkie tworzenie i wdrażanie modeli predykcyjnych jako rozwiązań analitycznych.

## <a name="what-you-learn"></a>Omawiane zagadnienia

Sposób na potrzeby usługi Azure Machine Learning pogodowe prognozy (szansy ustaniu) przy użyciu danych temperatury i wilgotności z Centrum Azure IoT. Ryzyko ustaniu jest dane wyjściowe przygotowane pogody modelu prognozy. Model jest oparty na danych historycznych Prognozowanie szansy ustaniu na podstawie temperatury i wilgotności.

## <a name="what-you-do"></a>Co zrobić

- Wdróż model prognozowania pogody jako usługę sieci web.
- Przygotuj się Centrum IoT na dostęp do danych przez dodanie grupy odbiorców.
- Utwórz zadanie usługi analiza strumienia i skonfigurować zadanie, aby:
  - Odczytywanie danych temperatury i wilgotności z Centrum IoT.
  - Wywołanie usługi sieci web można pobrać ustaniu szansy.
  - Zapisz wynik do magazynu obiektów blob platformy Azure.
- Umożliwia wyświetlanie prognozie pogody Eksploratora magazynu Microsoft Azure.

## <a name="what-you-need"></a>Co jest potrzebne

- Samouczek [skonfigurować Twoje urządzenie](iot-hub-raspberry-pi-kit-node-get-started.md) ukończone, która obejmuje następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Centrum Azure IoT w ramach Twojej subskrypcji.
  - Aplikacja klienta, która wysyła komunikaty do Centrum Azure IoT.
- Konto usługi Azure Machine Learning Studio. ([Bezpłatnie spróbuj Machine Learning Studio](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Wdróż model prognozowania pogody jako usługę sieci web

1. Przejdź do [stronę modelu prognozy pogody](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Kliknij przycisk **Otwórz w Studio** w usłudze Microsoft Azure Machine Learning Studio.
   ![Otwórz stronę modelu prognozy pogody w Cortana Intelligence Gallery](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Kliknij przycisk **Uruchom** do sprawdzania poprawności kroki w modelu. Ten krok może potrwać 2 minut.
   ![Otwórz modelu prognozy pogody w usłudze Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Kliknij przycisk **Konfigurowanie usługi sieci WEB** > **usługi sieci Web predykcyjnej**.
   ![Wdróż model prognozowania pogody w usłudze Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Na diagramie, przeciągnij **sieci Web dane wejściowe usługi** modułu gdzieś w pobliżu **Score Model** modułu.
1. Połącz **sieci Web dane wejściowe usługi** moduł **Score Model** modułu.
   ![Połącz dwa moduły w usłudze Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Kliknij przycisk **Uruchom** do sprawdzania poprawności kroki w modelu.
1. Kliknij przycisk **wdrażanie usługi sieci WEB** Aby wdrożyć model jako usługę sieci web.
1. Na pulpicie nawigacyjnym modelu pobrać **programu Excel 2010 lub starszych skoroszytu** dla **ŻĄDANIA/odpowiedzi**.

   > [!Note]
   > Upewnij się, że możesz pobrać **programu Excel 2010 lub starszych skoroszytu** nawet wtedy, gdy używasz nowszej wersji programu Excel na tym komputerze.

   ![Pobieranie programu Excel dla punktu końcowego odpowiedzi na żądanie](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Otwórz skoroszyt programu Excel, zanotuj **adres URL usługi sieci WEB** i **klucz dostępu**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, konfigurowanie i uruchamianie zadania usługi analiza strumienia

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij kolejno pozycje **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.
1. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów**: Użyj tej samej grupie zasobów, która używa Centrum IoT.

   **Lokalizacja**: Użyj tej samej lokalizacji co grupie zasobów.

   **Przypnij do pulpitu nawigacyjnego**: zaznacz tę opcję, aby mieć łatwy dostęp do centrum IoT Hub z pulpitu nawigacyjnego.

   ![Utwórz zadanie usługi Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Kliknij przycisk **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie usługi Stream Analytics.
1. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.
1. W **dane wejściowe** okienku, kliknij przycisk **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wejściowy**: unikatowego aliasu dla danych wejściowych.

   **Źródło**: Wybierz **Centrum IoT**.

   **Grupy odbiorców**: wybierz utworzoną grupę odbiorców.

   ![Dodawanie danych wejściowych do zadania usługi analiza strumienia na platformie Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Kliknij przycisk **Utwórz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**.
1. W **dane wyjściowe** okienku, kliknij przycisk **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wyjściowy**: unikatowy alias danych wyjściowych.

   **Obiekt sink**: Wybierz **magazynu obiektów Blob**.

   **Konto magazynu**: Konto magazynu dla usługi magazynu obiektów blob. Można utworzyć konta magazynu lub użyć istniejącego.

   **Kontener**: kontener, w której jest zapisywany obiektu blob. Możesz utworzyć kontener lub użyć istniejącego.

   **Format serializacji zdarzeń**: Wybierz **CSV**.

   ![Dodawanie danych wyjściowych do zadania usługi analiza strumienia na platformie Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Kliknij przycisk **Utwórz**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Dodawanie funkcji do zadania usługi analiza strumienia do wywoływania usługi sieci web, na których wdrożono

1. W obszarze **topologii zadania**, kliknij przycisk **funkcje** > **Dodaj**.
1. Wprowadź następujące informacje:

   **Funkcja Alias**: wprowadź `machinelearning`.

   **Typ funkcji**: Wybierz **Azure ML**.

   **Opcji importowania**: Wybierz **importu z innej subskrypcji**.

   **Adres URL**: Wprowadź adres URL usługi sieci WEB, zanotowaną w dół w skoroszycie programu Excel.

   **Klucz**: Wprowadź klucz dostępu zanotowaną w dół ze skoroszytu programu Excel.

   ![Dodawanie funkcji do zadania usługi analiza strumienia na platformie Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Kliknij przycisk **Utwórz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Zapytanie**.
1. Zastąp istniejący kod następujący kod:

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

1. Kliknij pozycję **Zapisz**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu usługi Stream Analytics kliknij kolejno pozycje **Uruchom** > **Teraz** > **Uruchom**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania usługi Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Umożliwia wyświetlanie prognozie pogody Eksploratora magazynu Microsoft Azure

Uruchom aplikację klienta do rozpoczęcia zbierania i wysyłania danych temperatury i wilgotności do Centrum IoT. Dla każdego komunikatu, który odbiera Centrum IoT zadanie usługi Stream Analytics wywołuje usługę sieci web prognozie pogody wygenerowało ryzyko ustaniu. Wynik jest zapisywane do usługi magazynu obiektów blob platformy Azure. Eksplorator usługi Storage platformy Azure to narzędzie, które służy do wyświetlenia wyników.

1. [Pobieranie i instalowanie Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com/).
1. Otwórz Eksploratora usługi Storage platformy Azure.
1. Zaloguj się do konta platformy Azure.
1. Wybierz subskrypcję.
1. Kliknij subskrypcję > **kont magazynu** > Twoje konto magazynu > **kontenerów obiektów Blob** > z kontenera.
1. Otwórz plik CSV, aby zobaczyć wynik. Ostatnia kolumna rejestruje ryzyko ustaniu.

   ![Uzyskanie wyniku prognozie pogody przy użyciu usługi Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Podsumowanie

Uczenie maszynowe Azure zostało pomyślnie umożliwia tworzenie ryzyko ustaniu na podstawie danych temperatury i wilgotności, który odbiera Centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]