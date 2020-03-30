---
title: Przesyłanie strumieniowe danych z monitorowania zdalnego do magazynu usługi Data Lake — Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak zintegrować rozwiązanie do zdalnego monitorowania z usługą Azure Data Lake Store przy użyciu zadania usługi Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889233"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integracja rozwiązania do zdalnego monitorowania z magazynem usługi Azure Data Lake Store

Możesz mieć zaawansowane wymagania analityczne wykraczające poza to, co jest oferowane w rozwiązaniu do zdalnego monitorowania. Usługa Azure Data Lake Store jest idealnym rozwiązaniem dla tej aplikacji, ponieważ może przechowywać dane z ogromnych i zróżnicowanych zestawów danych, a także integrować się z usługą Azure Data Lake Analytics w celu zapewnienia analizy na żądanie.

W tym instrukcje użyjesz zadania usługi Azure Stream Analytics do przesyłania strumieniowego danych z centrum IoT w rozwiązaniu do zdalnego monitorowania do magazynu usługi Azure Data Lake.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten sposób, konieczne będą następujące czynności:

* [Wdrażanie akceleratora rozwiązań do zdalnego monitorowania](quickstart-remote-monitoring-deploy.md).
  * Rozwiązanie do zdalnego monitorowania wdroży zadanie centrum IoT i usługi Azure Stream Analytics używane w tym artykule w ramach subskrypcji platformy Azure.
* [Wdrażanie magazynu usługi Azure Data Lake](../data-lake-store/data-lake-store-get-started-portal.md)
  * Magazyn usługi Data Lake należy wdrożyć w tym samym regionie co rozwiązanie do zdalnego monitorowania.
  * [Utwórz folder](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) o nazwie "przesyłanie strumieniowe" na koncie.

## <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Utwórz dedykowaną grupę odbiorców w centrum IoT rozwiązania do zdalnego monitorowania. Będzie to używane przez zadanie usługi Stream Analytics do przesyłania strumieniowego danych do magazynu Usługi Data Lake.

> [!NOTE]
> Grupy konsumentów są używane przez aplikacje do ściągania danych z usługi Azure IoT Hub. Należy utworzyć nową grupę konsumentów dla każdego pięciu odbiorców wyjściowych. Można utworzyć maksymalnie 32 grupy odbiorców.

1. Zaloguj się do Portalu Azure.

1. W witrynie Azure portal kliknij przycisk **Powłoki chmury.**

    ![Ikona uruchamiania portalu](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Wykonaj to polecenie, aby utworzyć nową grupę odbiorców:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Użyj nazwy grupy zasobów i centrum IoT z rozwiązania zdalnego monitorowania.

## <a name="create-stream-analytics-job"></a>Utwórz zadanie analizy strumienia

Utwórz zadanie usługi Azure Stream Analytics, aby przesyłać strumieniowo dane z centrum IoT hub do magazynu usługi Azure Data Lake.

1. Kliknij **pozycję Utwórz zasób**, wybierz pozycję Internet rzeczy z marketplace, a następnie kliknij pozycję **Zadanie usługi Stream Analytics**.

    ![Nowe zadanie analizy strumienia](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Wprowadź nazwę zadania i wybierz odpowiednią grupę Subskrypcji i Zasobów.

1. Wybierz lokalizację w pobliżu lub w tym samym regionie co magazyn Usługi Data Lake. Tutaj używamy wschodnich stanów USA.

1. Upewnij się, że środowisko hostingu jest domyślne w **chmurze.**

1. Kliknij przycisk **Utwórz**.

    ![Utwórz zadanie analizy strumienia](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurowanie zadania usługi Stream Analytics

1. Przejdź do **zadania usługi Stream Analytics** w grupie zasobów rozwiązania zdalnego monitorowania.

1. Na stronie Przegląd kliknij pozycję **Wejścia**.

    ![Strona przeglądowa](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Kliknij **pozycję Dodaj dane wejściowe strumienia** i wybierz pozycję Centrum **IoT** z listy rozwijanej.

    ![Dodaj dane wejściowe](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na karcie Nowe dane wejściowe wprowadź alias wejściowy **usługi IoTHub**.

1. Z listy rozwijanej Grupa Konsument wybierz wcześniej utworzoną grupę odbiorców. Tutaj używamy **streamanalyticsjob**.

    ![Wybierz wejście](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Kliknij przycisk **Zapisz**.

1. Na stronie Przegląd kliknij pozycję **Dane wyjściowe**.

    ![Dodaj magazyn Data Lake](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Kliknij **pozycję Dodaj** i wybierz pozycję Magazyn jeziora **danych** z listy rozwijanej.

    ![Dodaj dane wyjściowe](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na karcie Nowe dane wyjściowe wprowadź alias wyjściowy **datalakestore**.

1. Wybierz konto Data Lake Store utworzone w poprzednich krokach i podaj strukturę folderów, aby przesyłać strumieniowo dane do magazynu.

1. W polu Format daty wprowadź **/streaming/{date}/{time}**. Pozostaw domyślny format daty YYYY/MM/DD i format czasu HH.

    ![Podaj strukturę folderów](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Kliknij pozycję **Autoryzuj**.

    Musisz autoryzować z Usługi Data Lake Store, aby dać zadanie analizy strumienia dostęp do zapisu do systemu plików.

    ![Autoryzuj analizę strumienia do magazynu usługi Data Lake](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Zobaczysz wyskakujące okienko, a po zamknięciu okna przycisku Autoryzuj zostanie wyszarzony po zakończeniu autoryzacji.

    > [!NOTE]
    > Jeśli w oknie podręcznym zostanie wyświetlony błąd, otwórz nowe okno przeglądarki w trybie incognito i spróbuj ponownie.

1. Kliknij przycisk **Zapisz**.

## <a name="edit-the-stream-analytics-query"></a>Edytowanie kwerendy usługi Stream Analytics

Usługa Azure Stream Analytics używa języka zapytań podobnych do SQL, aby określić źródło wejściowe, które strumieniuje dane, przekształca te dane zgodnie z potrzebami i wyprowadza do różnych miejsc docelowych magazynu lub przetwarzania.

1. Na karcie Przegląd kliknij pozycję **Edytuj kwerendę**.

    ![Edytuj zapytanie](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. W edytorze zapytań zastąp symbole zastępcze [YourOutputAlias] i [YourInputAlias] wartościami zdefiniowanymi wcześniej.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Zapytanie analizy strumienia](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Kliknij przycisk **Zapisz**.
1. Kliknij **przycisk Tak,** aby zaakceptować zmiany.

## <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

1. Na karcie Przegląd kliknij pozycję **Start**.

    ![Uruchom zadanie analizy strumienia](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na karcie Rozpocznij zadanie kliknij pozycję **Niestandardowe**.

1. Ustaw niestandardowy czas, aby cofnąć się o kilka godzin, aby odebrać dane z momentu rozpoczęcia przesyłania strumieniowego przez urządzenie.

1. Kliknij przycisk **Uruchom**.

    ![Wybierz datę niestandardową](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Poczekaj, aż zadanie przejdzie do stanu działania, jeśli widzisz błędy może być z kwerendy, upewnij się, że weryfikuje, że składnia jest poprawna.

    ![Uruchamianie zadania](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Zadanie przesyłania strumieniowego rozpocznie odczytywanie danych z usługi IoT Hub i przechowywanie danych w magazynie usługi Data Lake Store. Może upłynąć kilka minut, aby dane zaczęły pojawiać się w magazynie usługi Data Lake Store.

## <a name="explore-the-streaming-data"></a>Eksplorowanie danych przesyłania strumieniowego

1. Przejdź do magazynu Data Lake Store.

1. Na karcie Przegląd kliknij pozycję **Eksplorator danych**.

1. W Eksploratorze danych przejdź do folderu **/streaming.** Zobaczysz foldery utworzone w formacie YYYY/MM/DD/HH.

    ![Eksploruj dane przesyłania strumieniowego](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Zobaczysz pliki json z jednym plikiem na godzinę.

    ![Eksploruj dane przesyłania strumieniowego](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Następne kroki

Usługa Azure Data Lake Analytics może służyć do przeprowadzania analizy dużych zbiorów danych w zbiorach danych usługi Data Lake Store. Dowiedz się więcej o [dokumentacji analizy usługi Data Lake](https://docs.microsoft.com/azure/data-lake-analytics).
