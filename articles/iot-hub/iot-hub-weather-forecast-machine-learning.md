---
title: Prognoza pogody przy użyciu Azure Machine Learning z danymi z IoT Hub | Microsoft Docs
description: Użyj Azure Machine Learning, aby przewidzieć prawdopodobieństwo oddeszczu w oparciu o dane dotyczące temperatury i wilgotności, które Centrum IoT zbiera z czujnika.
author: robinsh
manager: philmea
keywords: Prognoza pogody — Uczenie maszynowe
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: d7b71a6aa17e8eeae55fd6c8c6e9a5aa9e0ce524
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498882"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Prognoza pogody przy użyciu danych czujników z Centrum IoT Hub w Azure Machine Learning

![Diagram kompleksowy](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Uczenie maszynowe to technika analizy danych, która ułatwia komputerom uczenie się na podstawie istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Usługa Azure Machine Learning to oparta na chmurze usługa analizy predykcyjnej, która pozwala na szybkie tworzenie i wdrażanie modeli predykcyjnych jako rozwiązań analitycznych.

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak używać Azure Machine Learning do prognozowania pogody (szansa deszczu) przy użyciu danych temperatury i wilgotności z usługi Azure IoT Hub. Szansa deszczu to wyjście przygotowanego modelu przewidywania pogody. Model jest tworzony na podstawie historycznych danych w celu przewidywania szans oddeszczu na podstawie temperatury i wilgotności.

## <a name="what-you-do"></a>Co robisz

- Wdróż model prognozowania pogody jako usługę sieci Web.
- Przygotuj Centrum IoT Hub do dostępu do danych, dodając grupę odbiorców.
- Utwórz zadanie Stream Analytics i skonfiguruj zadanie w programie:
  - Odczytaj dane temperatury i wilgotności z Centrum IoT Hub.
  - Wywołaj usługę sieci Web, aby uzyskać deszczową szansę.
  - Zapisz wynik w usłudze Azure Blob Storage.
- Użyj Eksplorator usługi Microsoft Azure Storage, aby wyświetlić prognozę pogody.

## <a name="what-you-need"></a>Co jest potrzebne

- Ukończ samouczek [gry online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jedno z samouczków dotyczących urządzeń; na przykład [Raspberry Pi przy użyciu środowiska Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Usługa Azure IoT Hub w ramach Twojej subskrypcji.
  - Aplikacja kliencka, która wysyła komunikaty do usługi Azure IoT Hub.
- Konto [Azure Machine Learning Studio](https://studio.azureml.net/) .

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Wdrażanie modelu przewidywania pogody jako usługi sieci Web

1. Przejdź do [strony modelu przewidywania pogody](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Kliknij pozycję **Otwórz w programie Studio** w Microsoft Azure Machine Learning Studio.
   ![otworzyć stronę modelu przewidywania pogody w Cortana Intelligence Gallery](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Kliknij przycisk **Uruchom** , aby sprawdzić poprawność kroków w modelu. Wykonanie tego kroku może potrwać 2 minuty.
   ![otworzyć model prognozowania pogody w Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Kliknij pozycję **Skonfiguruj usługę sieci web** > **predykcyjną usługę sieci Web**.
   ![wdrożyć model prognozowania pogody w Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Na diagramie przeciągnij moduł **wejściowy usługi sieci Web** w sąsiedztwie modułu **modelu oceny** .
1. Podłącz moduł **danych wejściowych usługi sieci Web** do modułu **modelu oceny** .
   ![połączyć dwa moduły w Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Kliknij przycisk **Uruchom** , aby sprawdzić poprawność kroków w modelu.
1. Kliknij pozycję **Wdróż usługę sieci Web** , aby wdrożyć model jako usługę sieci Web.
1. Na pulpicie nawigacyjnym modelu Pobierz **skoroszyt Excel 2010 lub wcześniejszy** dla **żądania/odpowiedzi**.

   > [!Note]
   > Upewnij się, że pobierasz **skoroszyt Excel 2010 lub wcześniejszy** , nawet jeśli korzystasz z nowszej wersji programu Excel na komputerze.

   ![Pobierz program Excel dla punktu końcowego odpowiedzi na żądanie](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Otwórz skoroszyt programu Excel, zanotuj **adres URL usługi sieci Web** i **klucz dostępu**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, Konfigurowanie i uruchamianie zadania Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij kolejno pozycje **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.
1. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów**: Użyj tej samej grupy zasobów, która jest używana przez Centrum IoT Hub.

   **Lokalizacja**: Użyj tej samej lokalizacji co grupa zasobów.

   **Przypnij do pulpitu nawigacyjnego**: zaznacz tę opcję, aby mieć łatwy dostęp do centrum IoT Hub z pulpitu nawigacyjnego.

   ![Tworzenie zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie Stream Analytics.
1. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.
1. W okienku **dane wejściowe** kliknij pozycję **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wejściowy**: unikatowy alias dla danych wejściowych.

   **Źródło**: wybierz pozycję **IoT Hub**.

   **Grupa konsumentów**: Wybierz utworzoną grupę odbiorców.

   ![Dodawanie danych wejściowych do zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**.
1. W okienku dane **wyjściowe** kliknij pozycję **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wyjściowy**: unikatowy alias danych wyjściowych.

   **Ujścia**: Wybierz **BLOB Storage**.

   **Konto magazynu**: konto magazynu dla magazynu obiektów BLOB. Możesz utworzyć konto magazynu lub użyć istniejącego.

   **Kontener**: kontener, w którym zapisano obiekt BLOB. Można utworzyć kontener lub użyć istniejącego.

   **Format serializacji zdarzenia**: wybierz opcję **CSV**.

   ![Dodawanie danych wyjściowych do zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Dodaj funkcję do zadania Stream Analytics, aby wywołać wdrożoną usługę sieci Web

1. W obszarze **topologia zadania**kliknij pozycję **funkcje** > **Dodaj**.
1. Wprowadź następujące informacje:

   **Alias funkcji**: wprowadź `machinelearning`.

   **Typ funkcji**: wybierz pozycję **Azure ml**.

   **Opcja importowania**: wybierz pozycję **Importuj z innej subskrypcji**.

   **Adres URL**: wprowadź adres URL usługi sieci Web zanotowany w skoroszycie programu Excel.

   **Klucz**: Wprowadź klucz dostępu zanotowany w skoroszycie programu Excel.

   ![Dodawanie funkcji do zadania Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

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

1. Kliknij pozycję **Zapisz**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu usługi Stream Analytics kliknij kolejno pozycje **Uruchom** > **Teraz** > **Uruchom**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania usługi Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Użyj Eksplorator usługi Microsoft Azure Storage, aby wyświetlić prognozę pogody

Uruchom aplikację kliencką, aby rozpocząć zbieranie i wysyłanie danych dotyczących temperatury i wilgotności do centrum IoT. Dla każdego komunikatu otrzymanego przez Centrum IoT Hub zadanie Stream Analytics wywołuje usługę sieci Web Prognozowanie pogody w celu wygenerowania szansy deszczu. Następnie wynik zostanie zapisany w usłudze Azure Blob Storage. Eksplorator usługi Azure Storage to narzędzie, którego można użyć do wyświetlenia wyniku.

1. [Pobierz i zainstaluj Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/).
1. Otwórz Eksplorator usługi Azure Storage.
1. Zaloguj się do swojego konta platformy Azure.
1. Wybierz subskrypcję.
1. Kliknij swoją subskrypcję, > **konta magazynu** > Twoje konto magazynu > **kontenery obiektów BLOB** > kontenera.
1. Otwórz plik CSV, aby zobaczyć wynik. Ostatnia kolumna rejestruje szansę deszczu.

   ![Pobierz wynik prognozy pogody przy użyciu Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Podsumowanie

Pomyślnie użyto Azure Machine Learning do wygenerowania szansy deszczu na podstawie danych temperatury i wilgotności odbieranych przez Centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]