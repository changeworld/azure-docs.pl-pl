---
title: Prognozowanie pogody przy użyciu danych z usługi IoT Hub przy użyciu usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Machine Learning, aby przewidzieć prawdopodobieństwo deszczu na podstawie danych usługi IoT hub, który zbiera dane z czujnika temperatury i wilgotności.
author: robinsh
manager: philmea
keywords: uczenie maszynowe prognozę pogody
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: ffc2e5fb588ce6861f5df6cefdf810c1a015c043
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441088"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Prognozowanie pogody przy użyciu danych czujników z Centrum IoT hub w usłudze Azure Machine Learning

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Uczenie maszynowe to technika przetwarzania danych, która ułatwia komputerom uczenie się na podstawie istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów. Usługa Azure Machine Learning to oparta na chmurze usługa analizy predykcyjnej, która pozwala na szybkie tworzenie i wdrażanie modeli predykcyjnych jako rozwiązań analitycznych.

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak używać usługi Azure Machine Learning na warunki pogodowe prognozy (prawdopodobieństwo deszczu) przy użyciu danych temperatury i wilgotności z usługi Azure IoT hub. Prawdopodobieństwo deszczu znajdują się dane wyjściowe modelu prognozy pogody przygotowany. Model jest utworzonych na podstawie danych historycznych w celu prognozowania prawdopodobieństwo deszczu na podstawie temperatury i wilgotności.

## <a name="what-you-do"></a>Co należy zrobić

- Wdrażanie modeli prognozy pogody jako usługę sieci web.
- Przygotuj usługi IoT hub na dostęp do danych, dodając grupy odbiorców.
- Utwórz zadanie usługi Stream Analytics i skonfiguruj zadanie, aby:
  - Odczytywać dane temperatury i wilgotności z usługi IoT hub.
  - Wywołanie usługi sieci web, aby uzyskać prawdopodobieństwo deszczu.
  - Zapisz wynik do usługi Azure blob storage.
- Microsoft Azure Storage Explorer umożliwia wyświetlanie prognozę pogody.

## <a name="what-you-need"></a>Co jest potrzebne

- Wykonaj [symulatora w trybie online urządzenia Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) samouczka lub jednym z samouczków urządzenia; na przykład [Raspberry Pi w środowisku node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Usługi Azure IoT hub w ramach Twojej subskrypcji.
  - Aplikacja kliencka, która wysyła komunikaty do usługi Azure IoT hub.
- Konto usługi Azure Machine Learning Studio. ([Wypróbuj bezpłatnie usługę Machine Learning Studio](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Wdrażanie modeli prognozy pogody jako usługę sieci web

1. Przejdź do [stronie modelu prognozy pogody](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Kliknij przycisk **Otwórz w programie Studio** w usłudze Microsoft Azure Machine Learning Studio.
   ![Otwórz stronę modelu prognozy pogody w galerii Cortana Intelligence](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Kliknij przycisk **Uruchom** do sprawdzania poprawności kroki w modelu. W tym kroku może potrwać 2 minut.
   ![Otwieranie modelu prognozy pogody w usłudze Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Kliknij przycisk **konfiguracji usługi sieci WEB** > **predykcyjna usługa internetowa**.
   ![Wdrażanie modelu prognozy pogody w usłudze Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Na diagramie, przeciągnij **sieci Web dane wejściowe usługi** modułu gdzieś w pobliżu **Score Model** modułu.
1. Połącz **sieci Web dane wejściowe usługi** moduł **Score Model** modułu.
   ![Połącz dwa moduły w usłudze Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Kliknij przycisk **Uruchom** do sprawdzania poprawności kroki w modelu.
1. Kliknij przycisk **wdrażanie usługi sieci WEB** do wdrażania modelu w postaci usługi sieci web.
1. Na pulpicie nawigacyjnym modelu, Pobierz **Excel 2010 lub starszej skoroszytu** dla **ŻĄDAŃ/odpowiedzi**.

   > [!Note]
   > Upewnij się, że możesz pobrać **Excel 2010 lub starszej skoroszytu** nawet wtedy, gdy używasz nowszej wersji programu Excel na tym komputerze.

   ![Pobieranie programu Excel dla punktu końcowego odpowiedzi na żądanie](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Otwórz skoroszyt programu Excel, zwróć uwagę na **adres URL usługi sieci WEB** i **klucz dostępu**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, konfigurowanie i uruchamianie zadania usługi Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij kolejno pozycje **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.
1. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: Nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów**: Użyj tej samej grupie zasobów, która korzysta z usługi IoT hub.

   **Lokalizacja**: Użyj tej samej lokalizacji co grupa zasobów.

   **Przypnij do pulpitu nawigacyjnego**: Sprawdź tę opcję, aby łatwo uzyskiwać dostęp do usługi IoT hub z pulpitu nawigacyjnego.

   ![Tworzenie zadania usługi Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie usługi Stream Analytics.
1. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.
1. W **dane wejściowe** okienku kliknij **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias danych wejściowych**: Unikatowego aliasu dla danych wejściowych.

   **Źródło**: Wybierz **usługi IoT hub**.

   **Grupy użytkowników**: Wybierz utworzoną grupę odbiorców.

   ![Dodaj dane wejściowe do zadania usługi Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**.
1. W **dane wyjściowe** okienku kliknij **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias danych wyjściowych**: Unikatowy alias danych wyjściowych.

   **Obiekt sink**: Wybierz **magazynu obiektów Blob**.

   **Konto magazynu**: Konto magazynu usługi blob Storage. Można utworzyć konto magazynu lub użyj istniejącej.

   **kontener**: Kontener, w którym jest zapisany obiekt blob. Można utworzyć kontenera lub użyj istniejącej.

   **Format serializacji zdarzeń**: Wybierz **CSV**.

   ![Dodaj dane wyjściowe zadania usługi Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Kliknij pozycję **Utwórz**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Dodawanie funkcji do wywoływania usługi sieci web, wdrożone zadania usługi Stream Analytics

1. W obszarze **topologia zadań**, kliknij przycisk **funkcje** > **Dodaj**.
1. Wprowadź następujące informacje:

   **Alias funkcji**: Wprowadź polecenie `machinelearning`.

   **Typ funkcji**: Wybierz **uczenie Maszynowe systemu Azure**.

   **Opcja importu**: Wybierz **Importuj z innej subskrypcji**.

   **ADRES URL**: Wprowadź adres URL usługi sieci WEB zanotowanym w dół ze skoroszytu programu Excel.

   **Klucz**: Wprowadź klucz dostępu, zanotowaną w dół ze skoroszytu programu Excel.

   ![Dodawanie funkcji do zadania usługi Stream Analytics na platformie Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

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

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Użyj Eksploratora usługi Microsoft Azure Storage, aby wyświetlić prognozę pogody

Uruchom aplikację klienta, aby rozpocząć zbieranie i wysyłanie temperatury i wilgotności danych do usługi IoT hub. Dla każdego komunikatu usługi IoT hub odbierze zadanie usługi Stream Analytics wywołuje usługę sieci web prognozę pogody, aby wygenerować prawdopodobieństwo deszczu. Wynik są następnie zapisywane do magazynu obiektów blob platformy Azure. Eksplorator usługi Azure Storage jest narzędziem, które można użyć w celu wyświetlenia wyniku.

1. [Pobieranie i instalowanie Eksploratora usługi Microsoft Azure Storage](https://storageexplorer.com/).
1. Otwórz Eksplorator usługi Azure Storage.
1. Zaloguj się do swojego konta platformy Azure.
1. Wybierz subskrypcję.
1. Kliknij subskrypcję > **kont magazynu** > konta magazynu > **kontenery obiektów Blob** > kontenera.
1. Otwórz plik CSV, aby wyświetlić wynik. Ostatnia kolumna rejestruje prawdopodobieństwo deszczu.

   ![Pobierz wyniki prognozy pogody przy użyciu usługi Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Podsumowanie

Usługi Azure Machine Learning zostały pomyślnie użyta do wyprodukowania prawdopodobieństwo deszczu na podstawie danych temperatury i wilgotności usługi IoT hub odbierze.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]