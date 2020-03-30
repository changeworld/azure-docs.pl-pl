---
title: Wizualizacja danych frm Azure IoT Hub w czasie rzeczywistym — usługa Power BI
description: Użyj usługi Power BI do wizualizacji danych temperatury i wilgotności, które są zbierane z czujnika i wysyłane do centrum Usługi Azure IoT hub.
author: robinsh
keywords: wizualizacja danych w czasie rzeczywistym, wizualizacja danych na żywo, wizualizacja danych czujników
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954626"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Wizualizuj dane czujników w czasie rzeczywistym z usługi Azure IoT Hub przy użyciu usługi Power BI

![Diagram end-to-end](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak wizualizować dane czujników w czasie rzeczywistym, które centrum Azure IoT otrzymuje za pomocą usługi Power BI. Jeśli chcesz spróbować wizualizować dane w centrum IoT hub za pomocą aplikacji sieci web, zobacz [Wizualizacja danych czujników usługi Azure IoT Hub za pomocą aplikacji sieci Web do wizualizacji danych czujników w czasie rzeczywistym z usługi Azure IoT Hub.](iot-hub-live-data-visualization-in-web-apps.md)

## <a name="what-you-do"></a>Co robisz

* Przygotuj centrum IoT do dostępu do danych, dodając grupę odbiorców.

* Utwórz, skonfiguruj i uruchom zadanie usługi Stream Analytics do przesyłania danych z centrum IoT hub na konto usługi Power BI.

* Tworzenie i publikowanie raportu usługi Power BI w celu wizualizacji danych.

## <a name="what-you-need"></a>Co jest potrzebne

* Ukończ samouczek [symulatora online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jeden z samouczków urządzenia; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Artykuły te obejmują następujące wymagania:
  
  * Aktywna subskrypcja platformy Azure.
  * Centrum Usługi Azure IoT w ramach subskrypcji.
  * Aplikacja kliencka, która wysyła wiadomości do centrum Usługi Azure IoT Hub.

* Konto usługi Power BI. ([Wypróbuj power bi za darmo)](https://powerbi.microsoft.com/)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, konfigurowanie i uruchamianie zadania usługi Stream Analytics

Zacznijmy od utworzenia zadania usługi Stream Analytics. Po utworzeniu zadania należy zdefiniować dane wejściowe, wyjściowe i kwerendę używaną do pobierania danych.

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Utwórz zadanie** > usługi**Stream Analytics**w zakresie**internetu rzeczy** > .

2. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów:** Użyj tej samej grupy zasobów, której używa centrum IoT Hub.

   **Lokalizacja:** Użyj tej samej lokalizacji co grupa zasobów.

   ![Tworzenie zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Wybierz **pozycję Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie usługi Stream Analytics.

2. W obszarze **Topologia zadań**wybierz pozycję **Wejścia**.

3. W okienku **Wejścia** wybierz pozycję **Dodaj dane wejściowe strumienia**, a następnie wybierz pozycję **Centrum IoT** z listy rozwijanej. W nowym okienku wprowadzania wprowadź następujące informacje:

   **Alias wejściowy**: Wprowadź unikatowy alias dla danych wejściowych.

   **Podaj Centrum IoT Hub z subskrypcji:** wybierz ten przycisk opcji.

   **Subskrypcja:** Wybierz subskrypcję platformy Azure, której używasz w tym samouczku.

   **Centrum IoT Hub:** Wybierz Centrum IoT, którego używasz w tym samouczku.

   **Punkt końcowy**: wybierz pozycję **Obsługa komunikatów**.

   **Nazwa zasad dostępu współdzielonego:** wybierz nazwę zasady dostępu współdzielonego, której chcesz użyć zadania Usługi Stream Analytics dla centrum IoT Hub. W tym samouczku można wybrać *usługę*. Zasady *usługi* są tworzone domyślnie w nowych centrach IoT i udziela uprawnień do wysyłania i odbierania w punktach końcowych po stronie chmury udostępniane przez centrum IoT hub. Aby dowiedzieć się więcej, zobacz [Kontrola dostępu i uprawnienia](iot-hub-devguide-security.md#access-control-and-permissions).

   **Klucz zasad dostępu współdzielonego:** to pole jest wypełniane automatycznie na podstawie wybranej nazwy zasady dostępu współdzielonego.

   **Grupa odbiorców:** Wybierz grupę odbiorców utworzoną wcześniej.

   Pozostaw wszystkie inne pola przy ich wartościach domyślnych.

   ![Dodawanie danych wejściowych do zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Wybierz **pozycję Zapisz**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadań**wybierz pozycję **Wyjścia**.

2. W okienku **Wyjścia** wybierz pozycję **Dodaj** i **Power BI**.

3. W okienku **Power BI — Nowe dane wyjściowe** wybierz pozycję **Autoryzuj** i postępuj zgodnie z monitami, aby zalogować się do konta usługi Power BI.

4. Po zalogowaniu się do usługi Power BI wprowadź następujące informacje:

   **Alias wyjściowy:** Unikatowy alias dla danych wyjściowych.

   **Obszar roboczy grupy:** wybierz obszar roboczy grupy docelowej.

   **Nazwa zestawu danych**: Wprowadź nazwę zestawu danych.

   **Nazwa tabeli**: Wprowadź nazwę tabeli.

   ![Dodawanie danych wyjściowych do zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Wybierz **pozycję Zapisz**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** wybierz pozycję **Zapytanie**.

2. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.

3. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.

   ![Dodawanie kwerendy do zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Wybierz **pozycję Zapisz**.

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu Usługi Stream Analytics wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Rozpocznij** > **teraz** > **.** Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchamianie zadania usługi Stream Analytics na platformie Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Tworzenie i publikowanie raportu usługi Power BI w celu wizualizacji danych

1. Upewnij się, że przykładowa aplikacja jest uruchomiona na urządzeniu. Jeśli nie, możesz zapoznać się z samouczkami w obszarze [Konfiguracja urządzenia](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Zaloguj się do swojego konta usługi [Power BI](https://powerbi.microsoft.com/en-us/).

3. Wybierz używany obszar **roboczy Mój obszar roboczy**.

4. Wybierz **zestawy danych**.

   Powinien zostać wyświetlony zestaw danych określony podczas tworzenia danych wyjściowych dla zadania usługi Stream Analytics.

5. Dla utworzonego zestawu danych wybierz pozycję **Dodaj raport** (pierwszą ikonę po prawej stronie nazwy zestawu danych).

   ![Tworzenie raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.

   1. W okienku **Wizualizacje** strony tworzenia raportu wybierz ikonę wykresu liniowego, aby dodać wykres liniowy.

   2. W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics.

   3. Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.

   4. Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

      Zostanie utworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

      ![Dodawanie wykresu liniowego dla temperatury do raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby to zrobić, wykonaj te same kroki powyżej i umieść **EventEnqueuedUtcTime** na osi x i **wilgotności** na osi y.

   ![Dodawanie wykresu liniowego dla wilgotności do raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Wybierz **pozycję Zapisz,** aby zapisać raport.

9. Wybierz **pozycję Raporty** w lewym okienku, a następnie wybierz utworzony właśnie raport.

10. Wybierz pozycję**Publikowanie** **plików** > w sieci Web .

11. Wybierz **pozycję Utwórz kod osadzania**, a następnie wybierz pozycję **Publikuj**.

Podana jest łącze do raportu, które możesz udostępnić każdemu w celu uzyskania dostępu do raportu, oraz fragment kodu, którego można użyć do zintegrowania raportu z blogiem lub witryną sieci Web.

![Publikowanie raportu usługi Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Firma Microsoft oferuje również [aplikacje mobilne usługi Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) do przeglądania pulpitów nawigacyjnych i raportów usługi Power BI oraz interakcji z nimi na urządzeniu przenośnym.

## <a name="next-steps"></a>Następne kroki

Pomyślnie wykorzystano usługę Power BI do wizualizacji danych czujników w czasie rzeczywistym z centrum Usługi Azure IoT hub.

Aby uzyskać inny sposób wizualizacji danych z usługi Azure IoT Hub, zobacz [Wizualizacja danych czujników w czasie rzeczywistym z usługi Azure IoT Hub za pomocą aplikacji sieci Web.](iot-hub-live-data-visualization-in-web-apps.md)

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
