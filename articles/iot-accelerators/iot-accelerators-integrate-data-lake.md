---
title: Integracja rozwiązania monitorowania zdalnego z usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować rozwiązanie monitorowania zdalnego z usługi Azure Data Lake Store przy użyciu zadania usługi analiza strumienia Azure.
+services: ''
+suite: iot-suite
+author: philmea
+manager: timlt
+ms.author: philmea
+ms.date: 04/029/2018
+ms.topic: article
+ms.service: iot-suite
ms.openlocfilehash: 426ded8079ba5d6de4c186a8d18dd284082b0c1c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integracja rozwiązania monitorowania zdalnego z usługi Azure Data Lake Store

Może mieć zaawansowane wymagań analytics poza co to jest oferowany rozwiązanie monitorowania zdalnego. Azure Data Lake Store jest odpowiedni dla tej aplikacji, ponieważ on można przechowywać dane, które z ogromną i różnych zestawów danych jak również zintegrować z usługi Azure Data Lake Analytics, aby zapewnić na żądanie analizy.

W tym instrukcje użyjesz zadanie usługi analiza strumienia Azure do transmisji danych z Centrum IoT w rozwiązaniu monitorowania zdalnego do usługi Azure Data Lake Store.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten jak to zrobić, potrzebne następujące elementy:

* [Wdrażanie zdalne wstępnie skonfigurowane rozwiązanie monitorowania](iot-accelerators-remote-monitoring-deploy.md).
  * To rozwiązanie monitorowanie zdalnego zostaną wdrożone w Centrum IoT i zadania usługi analiza strumienia Azure używane w tym artykule w Twojej subskrypcji platformy Azure.
* [Wdrażanie usługi Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Usługi Data Lake Store powinny zostać wdrożone na tym samym regionie co rozwiązanie monitorowania zdalnego.
  * [Utwórz folder](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) o nazwie "przesyłania strumieniowego" na Twoim koncie.

## <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Utwórz dedykowanej grupy klientów w Centrum IoT rozwiązania monitorowania zdalnego. To umożliwi przez zadanie usługi Stream Analytics dane przesyłane strumieniowo do usługi Data Lake Store.

> [!NOTE]
> Grupy konsumentów są używane przez aplikacje w celu pobierania danych z Centrum IoT Azure. Należy utworzyć nową grupę odbiorców dla konsumentów co pięć danych wyjściowych. Można utworzyć grupy konsumentów do 32.

1. Zaloguj się do Portalu Azure.

1. W portalu Azure kliknij **powłoki chmury** przycisku.

    ![Ikona uruchamiania portalu](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Wykonanie tego polecenia, aby utworzyć nową grupę odbiorców:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Użyj nazwy Centrum IoT z rozwiązania do monitorowania zdalnego i grupy zasobów.

## <a name="create-stream-analytics-job"></a>Tworzenie zadania usługi analiza strumienia

Utwórz zadanie usługi analiza strumienia Azure do strumienia danych z Centrum IoT do usługi Azure Data Lake store.

1. Kliknij przycisk **Utwórz zasób**, wybierz Internetu rzeczy z witryny Marketplace i kliknij przycisk **zadanie usługi Stream Analytics**.

    ![Nowe zadanie analizy strumienia](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Wprowadź nazwę zadania i wybierz odpowiednią grupę subskrypcji i zasobu.

1. Wybierz lokalizację w najbliższej lub w tym samym regionie co usługi Data Lake Store. W tym miejscu użyto wschodnie stany USA.

1. Upewnij się, aby pozostawić środowisko macierzyste domyślnie **chmury**.

1. Kliknij przycisk **Utwórz**.

    ![Tworzenie zadania usługi analiza strumienia](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Skonfiguruj zadania usługi analiza strumienia

1. Przejdź do **zadanie usługi Stream Analytics** w grupie zasobów rozwiązanie monitorowania zdalnego.

1. Na stronie Przegląd kliknij **dane wejściowe**.

    ![Strona przeglądu](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Kliknij przycisk **dodać strumienia danych wejściowych** i wybierz **Centrum IoT** z listy rozwijanej.

    ![Dodawanie danych wejściowych](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na nowej karcie wejściowych, wprowadź alias wejściowy **Centrum IoTHub**.

1. Z odbiorców grupy listy rozwijanej wybierz utworzony wcześniej grupy odbiorców. W tym miejscu używamy **streamanalyticsjob**.

    ![Wybierz dane wejściowe](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Kliknij pozycję **Zapisz**.

1. Na stronie Przegląd kliknij **dane wyjściowe**.

    ![Dodawanie usługi Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Kliknij przycisk **Dodaj** i wybierz **usługi Data Lake Store** z listy rozwijanej.

    ![Dodawanie danych wyjściowych](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na nowej karcie dane wyjściowe, wprowadź alias wyjściowy **DataLakeStore**.

1. Wybierz utworzone w poprzednich krokach konto usługi Data Lake Store i podaj struktury folderów do transmisji danych w magazynie.

1. Wprowadź w polu format daty **/streaming/ {date} / {time}**. Pozostaw domyślny format RRRR/MM/dd. daty i czasu formacie gg.

    ![Podaj struktury folderów](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Kliknij przycisk **autoryzować**.

    Konieczne będzie autoryzowanie z usługą Data Lake Store umożliwiają Stream analytics zadanie zapisu w systemie plików.

    ![Autoryzowanie usługi Stream Analytics do usługi Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Zobaczysz menu podręcznego i po zamknięciu menu podręcznego przycisk Autoryzuj będzie wyszarzona po zakończeniu autoryzacji.

    > [!NOTE]
    > Jeśli zostanie wyświetlony błąd w oknie podręcznym, Otwórz nowe okno przeglądarki w trybie Incognito i spróbuj ponownie.

1. Kliknij pozycję **Zapisz**.

## <a name="edit-the-stream-analytics-query"></a>Edytuj zapytanie usługi analiza strumienia

Usługa Azure Stream Analytics używa języka zapytania przypominającego SQL, aby określić źródło danych wejściowych, że strumienie danych, przekształcania danych jako odpowiednie i dane wyjściowe do różnych magazynów lub przetwarzania miejsc docelowych.

1. Na karcie Ogólne kliknij **edycji zapytania**.

    ![Edytuj zapytanie](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. W edytorze zapytań zastąp [YourOutputAlias] i symboli zastępczych [YourInputAlias] z wartości zdefiniowanych w wcześniej.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Zapytanie usługi analiza strumienia](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Kliknij pozycję **Zapisz**.
1. Kliknij przycisk **tak** aby zatwierdzić zmiany.

## <a name="start-the-stream-analytics-job"></a>Uruchom zadanie usługi analiza strumienia

1. Na karcie Ogólne kliknij **Start**.

    ![Uruchom zadania usługi analiza strumienia](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na karcie zadania Start kliknij **niestandardowy**.

1. Ustawianie czasu niestandardowych można wrócić do poprzedniej strony kilku godzin do pobrania danych z urządzenia ma rozpoczęcia przesyłania strumieniowego.

1. Kliknij przycisk **Start**.

    ![Wybierz datę niestandardowych](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Poczekaj na zakończenie zadania przechodzi do stanu, działania, jeśli występuje błąd, może to być z zapytania, upewnij się sprawdzić, czy składnia jest poprawna.

    ![Uruchomione zadanie](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Zadanie przesyłania strumieniowego rozpocznie się do odczytywania danych z Centrum IoT i przechowywania danych w usługi Data Lake Store. Może upłynąć kilka minut, aż danych rozpocząć pojawią się w usługi Data Lake Store.

## <a name="explore-the-streaming-data"></a>Eksploruj dane przesyłane strumieniowo

1. Przejdź do usługi Data Lake Store.

1. Na karcie Ogólne kliknij **Eksploratora danych**.

1. W Eksploratorze danych Drąż w dół do **przesyłania strumieniowego audio /** folderu. Wyświetlona zostanie utworzony w formacie RRRR/MM/DD/HH folderów.

    ![Eksploruj dane przesyłane strumieniowo](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Zostanie wyświetlone pliki z jednego pliku na godzinę w formacie json.

    ![Eksploruj dane przesyłane strumieniowo](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Następne kroki

Azure Data Lake Analytics może służyć do wykonywania analizy danych big data na zestawy danych z usługi Data Lake Store. Dowiedz się więcej na [Data Lake Analytics dokumentacji](https://docs.microsoft.com/en-us/azure/data-lake-analytics).
