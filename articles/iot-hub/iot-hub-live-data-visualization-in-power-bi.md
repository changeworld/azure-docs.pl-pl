---
title: Wizualizacja danych w czasie rzeczywistym danych z czujnika z usługi Azure IoT Hub — usługa Power BI | Dokumentacja firmy Microsoft
description: Usługa Power BI umożliwia wizualizowanie temperatury i wilgotności danych zebranych z czujników i wysyłane do usługi Azure IoT hub.
author: robinsh
keywords: Wizualizacja danych czasu rzeczywistego, Wizualizacja danych na żywo, Wizualizacja danych czujników
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: robinsh
ms.openlocfilehash: 5349a8a81c1d1361637b4fc9cf83e1ee83f5276a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265512"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Wizualizowanie danych z czujników w czasie rzeczywistym z usługi Azure IoT Hub przy użyciu usługi Power BI

![Diagram end-to-end](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak wizualizować dane z czujników w czasie rzeczywistym, które usługi Azure IoT hub odbiera przy użyciu usługi Power BI. Jeśli chcesz wypróbować kółko wizualizowanie danych w usłudze IoT hub z usługą Web Apps, zobacz [użycia usługi Azure Web Apps w celu wizualizacji danych z czujników w czasie rzeczywistym z usługi Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co należy zrobić

* Przygotuj usługi IoT hub na dostęp do danych, dodając grupy odbiorców.

* Tworzenie, konfigurowanie i uruchamianie zadania usługi Stream Analytics za transfer danych z usługi IoT hub na koncie usługi Power BI.

* Tworzenie i publikowanie raportu usługi Power BI w celu wizualizacji danych.

## <a name="what-you-need"></a>Co jest potrzebne

* Samouczek [skonfigurować na twoim urządzeniu](iot-hub-raspberry-pi-kit-node-get-started.md) ukończone, która obejmuje następujące wymagania:
  
  * Aktywna subskrypcja platformy Azure.
  * Usługi Azure IoT hub w ramach Twojej subskrypcji.
  * Aplikacja kliencka, która wysyła komunikaty do usługi Azure IoT hub.

* Konto usługi Power BI. ([Bezpłatnego wypróbowania usługi Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, konfigurowanie i uruchamianie zadania usługi Stream Analytics

Zacznijmy od utworzenia zadania usługi Stream Analytics. Po utworzeniu zadania, należy zdefiniować wejść, wyjść i zapytania używane do pobierania danych.

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij kolejno pozycje **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: Nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów**: Użyj tej samej grupie zasobów, która korzysta z usługi IoT hub.

   **Lokalizacja**: Użyj tej samej lokalizacji co grupa zasobów.

   **Przypnij do pulpitu nawigacyjnego**: Sprawdź tę opcję, aby łatwo uzyskiwać dostęp do usługi IoT hub z pulpitu nawigacyjnego.

   ![Tworzenie zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

3. Kliknij pozycję **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie usługi Stream Analytics.

2. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.

3. W **dane wejściowe** okienku kliknij **Dodaj wejście strumienia**, a następnie wprowadź następujące informacje:

   **Alias danych wejściowych**: Unikatowego aliasu dla danych wejściowych i wybierz **ustawienia Centrum IoT Hub zapewniają ręcznie** poniżej.

   **Źródło**: Wybierz **usługi IoT hub**.
   
   **Punkt końcowy**: Kliknij przycisk **komunikatów**.

   **Grupy użytkowników**: Wybierz grupy odbiorców, który został utworzony.

4. Kliknij pozycję **Utwórz**.

   ![Dodaj dane wejściowe do zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**.

2. W **dane wyjściowe** okienku kliknij **Dodaj** i **usługi Power BI**, a następnie wprowadź następujące informacje:

   **Alias danych wyjściowych**: Unikatowy alias danych wyjściowych.

   **Obszar roboczy grupy**: Wybierz docelowy obszar roboczy grupy.

   **Nazwa zestawu danych**: Wprowadź nazwę zestawu danych.

   **Nazwa tabeli**: Wprowadź nazwę tabeli.

3. Kliknij przycisk **Autoryzuj**, a następnie zaloguj się do konta usługi Power BI.

4. Kliknij pozycję **Utwórz**.

   ![Dodaj dane wyjściowe do zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.

4. Kliknij pozycję **Zapisz**.

   ![Dodaj zapytanie do zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu usługi Stream Analytics kliknij kolejno pozycje **Uruchom** > **Teraz** > **Uruchom**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Tworzenie i publikowanie raportu usługi Power BI w celu wizualizacji danych

1. Upewnij się, że przykładowa aplikacja jest uruchomiona na urządzeniu. Jeśli nie, możesz zapoznać się z samouczkami, w obszarze [skonfigurować na twoim urządzeniu](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/en-us/).

3. Kliknij obszar roboczy, możesz użyć **Mój obszar roboczy**.

4. Kliknij pozycję **Zestawy danych**.

   Powinien zostać wyświetlony zestaw danych, które zostały określone podczas tworzenia danych wyjściowych dla zadania usługi Stream Analytics.

5. Utworzony zestaw danych, kliknij **Dodaj raport** (pierwsza ikona z prawej strony nazwy zestawu danych).

   ![Tworzenie raportu w usłudze Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

6. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   1. Na stronie tworzenia raportów dodawania wykresu liniowego.

   2. W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics.
   
   3. Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.
   
   4. Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

      Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

      ![Dodawanie wykresu liniowego dla temperatury do raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

7. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby to zrobić, wykonaj powyższe kroki tej samej i umieść **EventEnqueuedUtcTime** na osi x i **wilgotności** na osi y.

   ![Dodawanie wykresu liniowego wilgotności do raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

8. Kliknij przycisk **Zapisz**, aby zapisać raport.

9. Kliknij przycisk **raporty** w okienku po lewej stronie, a następnie kliknij raport został właśnie utworzony.

10. Kliknij przycisk **pliku** > **Publikuj w sieci web**.

11. Kliknij przycisk **Utwórz kod osadzania**, a następnie kliknij przycisk **Publikuj**.

Otrzymasz link do raportu, możesz udostępnić innym osobom na dostęp do raportów i wstawki kodu programu do integracji raportu w blogu lub witrynie sieci Web.

![Publikowanie raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Firma Microsoft oferuje także [aplikacjach mobilnych Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) umożliwiające wyświetlanie i wchodzenie w interakcje z pulpitów nawigacyjnych usługi Power BI i raporty na swoim urządzeniu przenośnym.

## <a name="next-steps"></a>Kolejne kroki

Usługa Power BI została pomyślnie użyta, wizualizowanie danych z czujników w czasie rzeczywistym z usługi Azure IoT hub.

Istnieje alternatywny sposób wizualizować dane z usługi Azure IoT Hub. Zobacz [użycia usługi Azure Web Apps w celu wizualizacji danych z czujników w czasie rzeczywistym z usługi Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
