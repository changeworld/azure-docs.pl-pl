---
title: Zintegrować rozwiązanie monitorowania zdalnego z usługą Data Lake Store — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować rozwiązanie monitorowania zdalnego z usługą Azure Data Lake Store, za pomocą zadania usługi Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 021f18f588613817110539d408f9260fb9247895
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61449502"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integracja rozwiązania do zdalnego monitorowania za pomocą usługi Azure Data Lake Store

Może być zaawansowanym wymagania dotyczące Analityki poza co to jest oferowana w rozwiązaniu do zdalnego monitorowania. Azure Data Lake Store jest idealnym rozwiązaniem dla tej aplikacji, ponieważ go można przechowywać danych z masowych i różnych zestawów danych oraz integracja z usługą Azure Data Lake Analytics w celu zapewnienia analizy na żądanie.

W tym instruktażu użyjesz zadania usługi Azure Stream Analytics przesyłanie strumieniowe danych z usługi IoT hub w rozwiązaniu monitorowania zdalnego, do usługi Azure Data Lake Store.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć Instruktaż, potrzebne następujące elementy:

* [Wdrażanie akceleratora rozwiązania monitorowania zdalnego](quickstart-remote-monitoring-deploy.md).
  * Rozwiązania do zdalnego monitorowania wdroży Centrum IoT i zadania usługi Azure Stream Analytics, używane w tym artykule do subskrypcji platformy Azure.
* [Wdrażanie usługi Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Usługi Data Lake Store powinny zostać wdrożone na tym samym regionie jako rozwiązania do zdalnego monitorowania.
  * [Utwórz folder](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) o nazwie "przesyłanie strumieniowe" w ramach Twojego konta.

## <a name="create-a-consumer-group"></a>Utwórz grupę odbiorców

Utwórz grupę odbiorców dedykowanej w usłudze IoT hub rozwiązania do monitorowania zdalnego. To posłuży za zadanie usługi Stream Analytics dla danych przesyłanych strumieniowo do usługi Data Lake Store.

> [!NOTE]
> Grupy konsumentów są używane przez aplikacje do pobierania danych z usługi Azure IoT Hub. Co pięć dane wyjściowe w konsumentach napisanych należy utworzyć nową grupę odbiorców. Można utworzyć maksymalnie 32 grup odbiorców.

1. Zaloguj się do Portalu Azure.

1. W witrynie Azure portal kliknij pozycję **Cloud Shell** przycisku.

    ![Ikona uruchamiania portalu](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Wykonaj to polecenie, aby utworzyć nową grupę odbiorców:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Użyj grupy zasobów i nazwy Centrum IoT z rozwiązania do zdalnego monitorowania.

## <a name="create-stream-analytics-job"></a>Utwórz zadanie usługi Stream Analytics

Utwórz zadanie usługi Azure Stream Analytics, przesłać strumień danych z usługi IoT hub do usługi Azure Data Lake store.

1. Kliknij przycisk **Utwórz zasób**wybierz Internetu rzeczy z witryny Marketplace, a kliknij **zadania usługi Stream Analytics**.

    ![Nowe zadanie usługi Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Wprowadź nazwę zadania, a następnie wybierz odpowiednią subskrypcję i grupę zasobów.

1. Wybierz lokalizację, w prawie lub w tym samym regionie, co usługi Data Lake Store. Tutaj używamy wschodnie stany USA.

1. Upewnij się, aby pozostawić środowiska hostingu jako domyślny **chmury**.

1. Kliknij pozycję **Utwórz**.

    ![Utwórz zadanie usługi Stream Analytics](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurowanie zadania usługi Stream Analytics

1. Przejdź do **zadania usługi Stream Analytics** w grupie zasobów rozwiązanie monitorowania zdalnego.

1. Na stronie Przegląd kliknij **dane wejściowe**.

    ![Strona przeglądu](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Kliknij przycisk **Dodaj wejście strumienia** i wybierz **usługi IoT Hub** z listy rozwijanej.

    ![Dodaj dane wejściowe](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na nowej karcie danych wejściowych, wprowadź alias danych wejściowych **IoTHub**.

1. Od konsumenta grupy listę rozwijaną wybierz grupy odbiorców, która została utworzona wcześniej. Tutaj używamy **streamanalyticsjob**.

    ![Wybierz pozycję dane wejściowe](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Kliknij pozycję **Zapisz**.

1. Na stronie Przegląd kliknij **dane wyjściowe**.

    ![Dodaj Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Kliknij przycisk **Dodaj** i wybierz **Data Lake Store** z listy rozwijanej.

    ![Dodaj dane wyjściowe](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na nowej karcie danych wyjściowych, wprowadź alias danych wyjściowych **DataLakeStore**.

1. Wybierz konto usługi Data Lake Store, do którego został utworzony w poprzednich krokach, a następnie podaj strukturę folderów, przesyłanie strumieniowe danych do magazynu.

1. Wprowadź w polu format daty **/streaming/ {date} / {time}** . Pozostaw domyślny format daty RRRR/MM/DD i format godziny gg.

    ![Podaj strukturę folderów](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Kliknij przycisk **autoryzować**.

    Należy przeprowadzić autoryzację w usłudze Data Lake Store oferowanie Stream analytics zadanie zapisu w systemie plików.

    ![Autoryzowanie usługi Stream Analytics do usług Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Zobaczysz okno podręczne i po zamknięciu okno podręczne przycisk Autoryzuj będą wyszarzone po zakończeniu autoryzacji.

    > [!NOTE]
    > Jeśli zostanie wyświetlony błąd w oknie podręcznym, Otwórz nowe okno przeglądarki w trybie Incognito i spróbuj ponownie.

1. Kliknij pozycję **Zapisz**.

## <a name="edit-the-stream-analytics-query"></a>Edytuj zapytania usługi Stream Analytics

Usługa Azure Stream Analytics używa język zapytań przypominający SQL, aby określić źródło danych wejściowych przesyłające dane strumieniowo, przekształcić te dane jako odpowiednie i dane wyjściowe do różnych miejsc docelowych przechowywania lub przetwarzania.

1. Na karcie Przegląd kliknij **Edytuj zapytanie**.

    ![Edytuj zapytanie](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. W edytorze zapytań, Zastąp [YourOutputAlias] i [YourInputAlias] zastępcze wartości zdefiniowane wcześniej.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics Query](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Kliknij pozycję **Zapisz**.
1. Kliknij przycisk **tak** aby zatwierdzić zmiany.

## <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Na karcie Przegląd kliknij **Start**.

    ![Uruchom zadanie usługi Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na karcie zadania rozpoczęcia kliknij **niestandardowe**.

1. Ustaw niestandardowy czas Wróć za kilka godzin do pobrania danych z urządzenia ma rozpoczęcia przesyłania strumieniowego.

1. Kliknij przycisk **Uruchom**.

    ![Wybierz niestandardowe daty](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Zaczekaj, aż zadanie przechodzi do stanu, działania, zostaną wyświetlone błędy, może to być zapytania, upewnij się sprawdzić, czy składnia jest poprawna.

    ![Zadanie uruchomione](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Zadanie przesyłania strumieniowego rozpocznie się odczytuje dane z usługi IoT Hub i przechowywanie danych w usługi Data Lake Store. Może upłynąć kilka minut, zanim dane zacząć są wyświetlane w usługi Data Lake Store.

## <a name="explore-the-streaming-data"></a>Zapoznaj się z danych przesyłanych strumieniowo

1. Przejdź do usługi Data Lake Store.

1. Na karcie Przegląd kliknij **Eksplorator danych**.

1. W Eksploratorze danych Drąż w dół do **/ przesyłania strumieniowego** folderu. Zostaną wyświetlone utworzone przy użyciu formatu RRRR/MM/DD/HH folderów.

    ![Eksplorowanie danych przesyłania strumieniowego](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Zostaną wyświetlone pliki w formacie json za pomocą jednego pliku na godzinę.

    ![Eksplorowanie danych przesyłania strumieniowego](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Następne kroki

Usługa Azure Data Lake Analytics może służyć do wykonywania analizy danych big data na zestawy danych usługi Data Lake Store. Dowiedz się więcej o [dokumentacja usługi Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics).
