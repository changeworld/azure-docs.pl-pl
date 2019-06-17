---
title: Wizualizacja danych w czasie rzeczywistym danych z czujnika z usługi Azure IoT Hub — usługa Power BI | Dokumentacja firmy Microsoft
description: Usługa Power BI umożliwia wizualizowanie temperatury i wilgotności danych zebranych z czujników i wysyłane do usługi Azure IoT hub.
author: robinsh
keywords: Wizualizacja danych czasu rzeczywistego, Wizualizacja danych na żywo, Wizualizacja danych czujników
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: 7deb1b501d30c8af0cb190f4722d46435afa9b8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065931"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Wizualizowanie danych z czujników w czasie rzeczywistym z usługi Azure IoT Hub przy użyciu usługi Power BI

![Diagram end-to-end](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak wizualizować dane z czujników w czasie rzeczywistym, które usługi Azure IoT hub odbiera przy użyciu usługi Power BI. Jeśli chcesz wypróbować wizualizowanie danych w usłudze IoT hub za pomocą aplikacji sieci web, zobacz [wizualizowanie danych czujników w czasie rzeczywistym z usługi Azure IoT Hub za pomocą aplikacji sieci web](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co należy zrobić

* Przygotuj usługi IoT hub na dostęp do danych, dodając grupy odbiorców.

* Tworzenie, konfigurowanie i uruchamianie zadania usługi Stream Analytics za transfer danych z usługi IoT hub na koncie usługi Power BI.

* Tworzenie i publikowanie raportu usługi Power BI w celu wizualizacji danych.

## <a name="what-you-need"></a>Co jest potrzebne

* Wykonaj [symulatora w trybie online urządzenia Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) samouczka lub jednym z samouczków urządzenia; na przykład [Raspberry Pi w środowisku node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Artykuły te obejmują następujące wymagania:
  
  * Aktywna subskrypcja platformy Azure.
  * Usługi Azure IoT hub w ramach Twojej subskrypcji.
  * Aplikacja kliencka, która wysyła komunikaty do usługi Azure IoT hub.

* A Power BI account. ([Bezpłatnego wypróbowania usługi Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, konfigurowanie i uruchamianie zadania usługi Stream Analytics

Zacznijmy od utworzenia zadania usługi Stream Analytics. Po utworzeniu zadania, należy zdefiniować wejść, wyjść i zapytania używane do pobierania danych.

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Utwórz zasób** > **Internet of Things** > **zadania usługi Stream Analytics**.

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: Nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów**: Użyj tej samej grupie zasobów, która korzysta z usługi IoT hub.

   **Lokalizacja**: Użyj tej samej lokalizacji co grupa zasobów.

   ![Tworzenie zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Wybierz pozycję **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie usługi Stream Analytics.

2. W obszarze **topologia zadań**, wybierz opcję **dane wejściowe**.

3. W **dane wejściowe** okienku wybierz **Dodaj wejście strumienia**, a następnie wybierz **usługi IoT Hub** z listy rozwijanej. Na nowe okienko danych wejściowych wprowadź następujące informacje:

   **Alias danych wejściowych**: Wprowadź unikatowego aliasu dla danych wejściowych.

   **Podaj Centrum IoT Hub z subskrypcji**: Wybierz ten przycisk radiowy.

   **Subskrypcja**: Wybierz subskrypcję platformy Azure, używane na potrzeby tego samouczka.

   **IoT Hub**: Wybierz Centrum IoT, używane na potrzeby tego samouczka.

   **Punkt końcowy**: Wybierz pozycję **Obsługa komunikatów**.

   **Nazwa zasad dostępu współdzielonego**: Wybierz nazwę zasad dostępu współdzielonego podczas wykonywania zadania usługi Stream Analytics na potrzeby Centrum IoT hub. Na potrzeby tego samouczka możesz wybrać *usługi*. *Usługi* zasad jest tworzony domyślnie na nowe centra IoT i przyznaje uprawnienia do wysyłania i odbierania w punktach końcowych po stronie chmury udostępnianych przez usługę IoT hub. Aby dowiedzieć się więcej, zobacz [kontrola dostępu i uprawnień](iot-hub-devguide-security.md#access-control-and-permissions).

   **Klucz zasad dostępu współdzielonego**: To pole jest wypełniane automatycznie na podstawie wybranych dla nazwy zasady dostępu współdzielonego.

   **Grupy użytkowników**: Wybierz grupy odbiorców, która została utworzona wcześniej.

   Pozostaw inne pola na ich wartości domyślne.

   ![Dodaj dane wejściowe do zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Wybierz pozycję **Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **topologia zadań**, wybierz opcję **dane wyjściowe**.

2. W **dane wyjściowe** okienku wybierz **Dodaj** i **usługi Power BI**.

3. Na **usługi Power BI — nowe dane wyjściowe** okienku wybierz **Autoryzuj** i postępuj zgodnie z monitami, aby zalogować się do konta usługi Power BI.

4. Po zalogowaniu się do usługi Power BI, wprowadź następujące informacje:

   **Alias danych wyjściowych**: Unikatowego aliasu dla danych wyjściowych.

   **Obszar roboczy grupy**: Wybierz docelowy obszar roboczy grupy.

   **Nazwa zestawu danych**: Wprowadź nazwę zestawu danych.

   **Nazwa tabeli**: Wprowadź nazwę tabeli.

   ![Dodaj dane wyjściowe do zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Wybierz pozycję **Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.

   ![Dodaj zapytanie do zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Wybierz pozycję **Zapisz**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W ramach zadania usługi Stream Analytics wybierz **Przegląd**, a następnie wybierz **Start** > **teraz** > **Start**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Tworzenie i publikowanie raportu usługi Power BI w celu wizualizacji danych

1. Upewnij się, że przykładowa aplikacja jest uruchomiona na urządzeniu. Jeśli nie, możesz zapoznać się z samouczkami, w obszarze [skonfigurować na twoim urządzeniu](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/en-us/).

3. Wybierz obszar roboczy, możesz użyć **Mój obszar roboczy**.

4. Wybierz **zestawów danych**.

   Powinien zostać wyświetlony zestaw danych, które zostały określone podczas tworzenia danych wyjściowych dla zadania usługi Stream Analytics.

5. Utworzony zestaw danych, można wybrać **Dodaj raport** (pierwsza ikona z prawej strony nazwy zestawu danych).

   ![Tworzenie raportu w usłudze Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   1. Na **wizualizacje** okienku strony tworzenia raportu wybierz ikonę wykresu liniowego, aby dodać wykres liniowy.

   2. W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics.

   3. Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   4. Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

      Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

      ![Dodawanie wykresu liniowego dla temperatury do raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby to zrobić, wykonaj powyższe kroki tej samej i umieść **EventEnqueuedUtcTime** na osi x i **wilgotności** na osi y.

   ![Dodawanie wykresu liniowego wilgotności do raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Wybierz **Zapisz** Aby zapisać raport.

9. Wybierz **raporty** w okienku po lewej stronie, a następnie wybierz raport został właśnie utworzony.

10. Wybierz **pliku** > **Publikuj w sieci web**.

11. Wybierz **Utwórz kod osadzania**, a następnie wybierz pozycję **Publikuj**.

Otrzymasz link do raportu, który możesz udostępnić innym osobom, aby uzyskać dostęp do raportów i fragmentu kodu, które można użyć do integracji raportu w blogu lub witrynie sieci Web.

![Publikowanie raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Firma Microsoft oferuje także [aplikacjach mobilnych Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) umożliwiające wyświetlanie i wchodzenie w interakcje z pulpitów nawigacyjnych usługi Power BI i raporty na swoim urządzeniu przenośnym.

## <a name="next-steps"></a>Kolejne kroki

Usługa Power BI została pomyślnie użyta, wizualizowanie danych z czujników w czasie rzeczywistym z usługi Azure IoT hub.

Innym sposobem wizualizacji danych z usługi Azure IoT Hub, zobacz [wizualizowanie danych czujników w czasie rzeczywistym z usługi Azure IoT Hub za pomocą aplikacji sieci web](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
