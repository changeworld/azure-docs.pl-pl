---
title: Wizualizacja danych w czasie rzeczywistym danych czujnika z Centrum IoT Azure — usługi Power BI | Dokumentacja firmy Microsoft
description: Wizualizuj dane temperatury i wilgotności, który został zebrany z czujnika i wysyłany do Centrum Azure IoT przy użyciu usługi Power BI.
author: rangv
manager: ''
keywords: wizualizację danych czasu rzeczywistego, wizualizacji danych na żywo czujnik wizualizacji danych
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: a3c54fe635fe0f8988c321684a815e9896922587
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634349"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Wizualizacja danych czujnika w czasie rzeczywistym z Centrum IoT Azure przy użyciu usługi Power BI

![Diagram end-to-end](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Omawiane zagadnienia

Sposób do wizualizacji danych czujnika w czasie rzeczywistym, który odbiera Centrum Azure IoT przez usługę Power BI. Jeśli chcesz spróbować wizualizacji danych w Centrum IoT z aplikacjami sieci Web, zapoznaj się z artykułem [użyj aplikacji sieci Web platformy Azure do wizualizacji danych czujnika w czasie rzeczywistym z Centrum IoT Azure](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co zrobić

- Przygotuj się Centrum IoT na dostęp do danych przez dodanie grupy odbiorców.
- Tworzenie, konfigurowanie i uruchamianie zadania usługi analiza strumienia do transferu danych z Centrum IoT na koncie usługi Power BI.
- Tworzenie i publikowanie raportu usługi Power BI do wizualizacji danych.

## <a name="what-you-need"></a>Co jest potrzebne

- Samouczek [skonfigurować Twoje urządzenie](iot-hub-raspberry-pi-kit-node-get-started.md) ukończone, która obejmuje następujące wymagania:
  - Aktywna subskrypcja platformy Azure.
  - Centrum Azure IoT w ramach Twojej subskrypcji.
  - Aplikacja klienta, która wysyła komunikaty do Centrum Azure IoT.
- A Power BI account. ([Spróbuj bezpłatnie usługę Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Tworzenie, konfigurowanie i uruchamianie zadania usługi analiza strumienia

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij kolejno pozycje **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.
1. Wprowadź poniższe informacje dotyczące zadania.

   **Nazwa zadania**: nazwa zadania. Nazwa musi być unikatowa w skali globalnej.

   **Grupa zasobów**: Użyj tej samej grupie zasobów, która używa Centrum IoT.

   **Lokalizacja**: Użyj tej samej lokalizacji co grupie zasobów.

   **Przypnij do pulpitu nawigacyjnego**: zaznacz tę opcję, aby mieć łatwy dostęp do centrum IoT Hub z pulpitu nawigacyjnego.

   ![Utwórz zadanie usługi Stream Analytics na platformie Azure](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Kliknij przycisk **Utwórz**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Dodawanie danych wejściowych do zadania usługi Stream Analytics

1. Otwórz zadanie usługi Stream Analytics.
1. W obszarze **Topologia zadania** kliknij pozycję **Dane wejściowe**.
1. W **dane wejściowe** okienku, kliknij przycisk **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wejściowy**: unikatowego aliasu dla danych wejściowych.

   **Źródło**: Wybierz **Centrum IoT**.

   **Grupy odbiorców**: zaznacz właśnie utworzoną grupę odbiorców.
1. Kliknij przycisk **Utwórz**.

   ![Dodawanie danych wejściowych do zadania usługi analiza strumienia na platformie Azure](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Dodawanie danych wyjściowych do zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Dane wyjściowe**.
1. W **dane wyjściowe** okienku, kliknij przycisk **Dodaj**, a następnie wprowadź następujące informacje:

   **Alias wyjściowy**: unikatowy alias danych wyjściowych.

   **Obiekt sink**: Wybierz **Power BI**.
1. Kliknij przycisk **autoryzacji**, a następnie zaloguj się na koncie usługi Power BI.
1. Autoryzowany, wprowadź następujące informacje:

   **Obszar roboczy grupy**: Wybierz docelowy obszar roboczy grupy.

   **Nazwa zestawu danych**: Wprowadź nazwę zestawu danych.

   **Nazwa tabeli**: Wprowadź nazwę tabeli.
1. Kliknij przycisk **Utwórz**.

   ![Dodawanie danych wyjściowych do zadania usługi analiza strumienia na platformie Azure](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurowanie zapytania zadania usługi Stream Analytics

1. W obszarze **Topologia zadania** kliknij pozycję **Zapytanie**.
1. Zastąp element `[YourInputAlias]` aliasem wejściowym zadania.
1. Zastąp element `[YourOutputAlias]` aliasem wyjściowym zadania.
1. Kliknij pozycję **Zapisz**.

   ![Dodaj zapytanie do zadania usługi analiza strumienia na platformie Azure](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

W zadaniu usługi Stream Analytics kliknij kolejno pozycje **Uruchom** > **Teraz** > **Uruchom**. Po pomyślnym uruchomieniu zadania jego stan zmieni się z **Zatrzymano** na **Uruchomiono**.

![Uruchom zadanie usługi Stream Analytics na platformie Azure](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Tworzenie i publikowanie raportu usługi Power BI do wizualizacji danych

1. Upewnij się, że przykładowa aplikacja jest uruchomiona na twoim urządzeniu. Jeśli nie mogą odwoływać się do samouczków w obszarze [skonfigurować na twoim urządzeniu](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).
1. Zaloguj się do Twojego [usługi Power BI](https://powerbi.microsoft.com/en-us/) konta.
1. Przejdź do obszaru roboczego grupy, która ustawione podczas tworzenia dane wyjściowe zadania usługi analiza strumienia.
1. Kliknij przycisk **przesyłania strumieniowego zestawów danych**.

   Powinien pojawić się wymieniony na liście zestaw danych, który został wybrany podczas tworzenia danych wyjściowych zadania usługi Stream Analytics.
1. W obszarze **AKCJE** kliknij pierwszą ikonę, aby utworzyć raport.

   ![Tworzenie raportu usługi Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Utwórz wykres liniowy, aby pokazać zachodzące w miarę upływu czasu zmiany temperatury w czasie rzeczywistym.
   1. Na stronie tworzenia raportu należy dodać wykres liniowy.
   1. W okienku **Pola** rozwiń tabelę, która została wybrana podczas tworzenia danych wyjściowych zadania usługi Stream Analytics.
   1. Przeciągnij element **EventEnqueuedUtcTime** do obszaru **Oś** w okienku **Wizualizacje**.
   1. Przeciągnij element **temperature** (Temperatura) do obszaru **Wartości**.

      Teraz jest tworzony wykres liniowy. Na osi X jest wyświetlana data i godzina w strefie czasowej UTC. Na osi Y jest wyświetlana temperatura z czujnika.

      ![Dodaj wykres liniowy dla temperatury raport Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Utwórz inny wykres liniowy, aby przedstawić zachodzące w miarę upływu czasu zmiany wilgotności w czasie rzeczywistym. Aby to zrobić, wykonaj te same czynności powyżej i umieścić **EventEnqueuedUtcTime** na osi x i **wilgotności** na osi y.

   ![Dodaj wykres wiersza wilgotność raport Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Kliknij przycisk **Zapisz**, aby zapisać raport.
1. Kliknij przycisk **pliku** > **publikowanie w sieci web**.
1. Kliknij przycisk **kod osadzania, Utwórz**, a następnie kliknij przycisk **publikowania**.

Wprowadzono łącze do raportu udostępnienie osobom dostęp do raportów i fragment kodu do integracji raportu w blogu lub witrynie sieci Web.

![Publikowanie raportu Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Firma Microsoft oferuje także [aplikacji mobilnych usługi Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) do przeglądania i interakcji z raportów i pulpitów nawigacyjnych usługi Power BI na swoim urządzeniu przenośnym.

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie zastosowano usługi Power BI do wizualizacji danych czujnika w czasie rzeczywistym z Centrum Azure IoT.
Istnieje inny sposób wizualizuj dane z Centrum IoT Azure. Zobacz [użyj aplikacji sieci Web platformy Azure do wizualizacji danych czujnika w czasie rzeczywistym z Centrum IoT Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
