---
title: 'Tutorial: Stream Analytics at the edge - Azure IoT Edge'
description: In this tutorial, you deploy Azure Stream Analytics as a module to an IoT Edge device
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7fbbe32efcedd4fa2635db1cc21f7ce98557515b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452534"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: Deploy Azure Stream Analytics as an IoT Edge module

Many IoT solutions use analytics services to gain insight about data as it arrives in the cloud from IoT devices. Dzięki usłudze Azure IoT Edge możesz zastosować logikę usługi [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), wdrażając ją bezpośrednio na urządzeniu. Dzięki przetwarzaniu potoków danych telemetrycznych na urządzeniu brzegowym możesz zmniejszyć ilość przekazywanych danych i skrócić czas reakcji na szczegółowe informacje umożliwiające podejmowanie działań.

Dzięki integracji usług Azure IoT Edge i Azure Stream Analytics możesz utworzyć zadanie usługi Azure Stream Analytics w witrynie Azure Portal, a następnie wdrożyć je jako moduł usługi IoT Edge bez użycia dodatkowego kodu.  

Azure Stream Analytics provides a richly structured query syntax for data analysis, both in the cloud and on IoT Edge devices. For more information, see [Azure Stream Analytics documentation](../stream-analytics/stream-analytics-edge.md).

Moduł usługi Stream Analytics w tym samouczku oblicza średnią temperaturę w ruchomym przedziale 30 sekund. Jeśli średnia wyniesie 70 stopni, moduł wyśle dla urządzenia alert o konieczności podjęcia działania. W tym przypadku tym działaniem jest zresetowanie symulowanego czujnika temperatury. W środowisku produkcyjnym możesz użyć tej funkcji na przykład do wyłączenia maszyny lub podjęcia działań zapobiegawczych, gdy temperatura osiągnie niebezpieczną wartość. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zadania usługi Azure Stream Analytics w celu przetwarzania danych na urządzeniu brzegowym.
> * Łączenie nowego zadania usługi Azure Stream Analytics z innymi modułami usługi IoT Edge.
> * Wdrażanie zadania usługi Azure Stream Analytics na urządzeniu usługi IoT Edge z poziomu witryny Azure Portal.

<center>

![Diagram - Tutorial architecture, stage and deploy ASA job](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* You can use an Azure virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md) or [Windows devices](quickstart.md).

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Tworzenie zadania usługi Azure Stream Analytics

In this section, you create an Azure Stream Analytics job that will do the following steps:
* Receive data from your IoT Edge device.
* Query the telemetry data for values outside a set range.
* Take action on the IoT Edge device based on the query results. 

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W przypadku tworzenia zadania usługi Azure Stream Analytics do uruchamiania na urządzeniu usługi IoT Edge musi być ono przechowywane w sposób umożliwiający wywoływanie go z urządzenia. Możesz użyć istniejącego konta usługi Azure Storage lub utworzyć teraz nowe konto. 

1. In the Azure portal, go to **Create a resource** > **Storage** > **Storage account**. 

1. Podaj następujące wartości, aby utworzyć konto magazynu:

   | Pole | Wartość |
   | ----- | ----- |
   | Subskrypcja | Wybierz tę samą subskrypcję co używana dla centrum IoT Hub. |
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |
   | Nazwa | Wprowadź unikatową nazwę konta magazynu. | 
   | Lokalizacja | Wybierz bliską lokalizację. |


1. Keep the default values for the other fields and select **Review + Create**.

1. Review your settings then select **Create**.

### <a name="create-a-new-job"></a>Tworzenie nowego zadania

1. In the Azure portal, go to **Create a resource** > **Internet of Things** > **Stream Analytics job**.

1. Podaj następujące wartości, aby utworzyć zadanie:

   | Pole | Wartość |
   | ----- | ----- |
   | Nazwa zadania | Podaj nazwę zadania. Na przykład **IoTEdgeJob** | 
   | Subskrypcja | Wybierz tę samą subskrypcję co używana dla centrum IoT Hub. |
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |
   | Lokalizacja | Wybierz bliską lokalizację. | 
   | Środowisko hostingu | Wybierz pozycję **Edge**. |
 
1. Wybierz pozycję **Utwórz**.

### <a name="configure-your-job"></a>Konfigurowanie zadania

Po utworzeniu zadania usługi Stream Analytics w witrynie Azure Portal można je skonfigurować za pomocą danych wejściowych, danych wyjściowych i zapytania w celu uruchamiania na danych, które przez nie przechodzą. 

Przy użyciu trzech elementów — danych wejściowych, danych wyjściowych i zapytania — w tej sekcji zostanie utworzone zadanie, które odbiera dane dotyczące temperatury z urządzenia usługi IoT Edge. Analizuje ono te dane w kroczącym 30-sekundowym przedziale czasu. Jeśli średnia temperatura w tym przedziale czasu przekracza 70 stopni, wysyłany jest alert do urządzenia usługi IoT Edge. W następnej sekcji, podczas wdrażania zadania, określisz dokładnie, skąd dane pochodzą i dokąd są przesyłane.  

1. Przejdź do zadania usługi Stream Analytics w witrynie Azure Portal. 

1. W obszarze **Topologia zadania** wybierz pozycję **Dane wejściowe**, a następnie pozycję **Dodaj wejście strumienia**.

   ![Azure Stream Analytics - add input](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Wybierz pozycję **Edge Hub** z listy rozwijanej.

1. W okienku **Nowe dane wejściowe** wpisz **temperature** jako alias danych wejściowych. 

1. Zachowaj wartości domyślne dla pozostałych pól i wybierz pozycję **Zapisz**.

1. W obszarze **Topologia zadania** otwórz pozycję **Dane wyjściowe**, a następnie wybierz pozycję **Dodaj**.

   ![Azure Stream Analytics - add output](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Wybierz pozycję **Edge Hub** z listy rozwijanej.

1. W okienku **Nowe dane wyjściowe** wpisz **alert** jako alias danych wyjściowych. 

1. Zachowaj wartości domyślne dla pozostałych pól i wybierz pozycję **Zapisz**.

1. W obszarze **Topologia zadania** wybierz pozycję **Zapytanie**.

1. Zastąp tekst domyślny poniższym zapytaniem. Kod SQL wysyła polecenie reset do danych wyjściowych alertu, jeśli średnia temperatura maszyny w 30-sekundowym przedziale czasu osiągnie 70 stopni. Polecenie reset zostało wstępnie zaprogramowane w czujniku jako akcja, która może zostać wykonana. 

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

1. Wybierz pozycję **Zapisz**.

### <a name="configure-iot-edge-settings"></a>Konfigurowanie ustawień usługi IoT Edge

Aby przygotować zadanie sługi Stream Analytics do wdrożenia na urządzeniu usługi IoT Edge, musisz skojarzyć zadanie z kontenerem na koncie magazynu. Po przejściu do wdrażania zadania definicja zadania zostanie wyeksportowana do kontenera magazynu. 

1. Under **Configure**, select **Storage account settings** then select **Add storage account**. 

   ![Azure Stream Analytics - add storage account](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Select the **Storage account** that you created at the beginning of this tutorial from the drop-down menu.

1. W polu **Kontener** wybierz pozycję **Utwórz nowy** i wpisz nazwę kontenera magazynu. 

1. Wybierz pozycję **Zapisz**. 

## <a name="deploy-the-job"></a>Wdrażanie zadania

Możesz teraz wdrożyć zadanie usługi Azure Stream Analytics na urządzeniu usługi IoT Edge. 

W tej sekcji użyjesz kreatora **Utwórz moduły** w witrynie Azure Portal, aby utworzyć *manifest wdrożenia*. Manifest wdrożenia to plik JSON opisujący wszystkie moduły, które zostaną wdrożone na urządzeniu, rejestry kontenerów, w których są przechowywane obrazy modułów, sposób, w jaki powinny być zarządzane moduły oraz sposób, w jaki moduły mogą komunikować się między sobą. Urządzenie usługi IoT Edge pobiera manifest wdrożenia z usługi IoT Hub, a następnie używa zawartych w nim informacji, aby wdrożyć i skonfigurować wszystkie przypisane moduły. 

W tym samouczku wdrażane są dwa moduły. The first is **SimulatedTemperatureSensor**, which is a module that simulates a temperature and humidity sensor. Drugi to zadanie usługi Stream Analytics. Moduł czujnika dostarcza strumień danych, który będzie analizowany przez zapytanie zadania. 

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

1. Go to **IoT Edge**, and then open the details page for your IoT Edge device.

1. Wybierz opcję **Ustaw moduły**.  

1. If you previously deployed the SimulatedTemperatureSensor module on this device, it might autopopulate. W przeciwnym razie dodaj moduł, wykonując następujące kroki:

   1. Kliknij pozycję **Dodaj** i wybierz pozycję **Moduł usługi IoT Edge**.
   1. For the name, type **SimulatedTemperatureSensor**.
   1. W polu Identyfikator URI obrazu wpisz **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.

1. Dodaj zadanie usługi Azure Stream Analytics dla usługi IoT Edge, wykonując następujące kroki:

   1. Kliknij przycisk **Dodaj** i wybierz pozycję **Moduł usługi Azure Stream Analytics**.
   1. Wybierz subskrypcję i utworzone wcześniej zadanie usługi Azure Stream Analytics dla usługi IoT Edge. 
   1. Wybierz pozycję **Zapisz**.

   Once you save your changes, the details of your Stream Analytics job are published to the storage container that you created. 

1. When the Stream Analytics module is added to the list of modules, select **Configure** to see how it's structured. 

   Identyfikator URI obrazu wskazuje standardowy obraz usługi Azure Stream Analytics. This one image is used for every Stream Analytics module that gets deployed to an IoT Edge device. 

   Bliźniacza reprezentacja modułu jest skonfigurowana z żądaną właściwością o nazwie **ASAJobInfo**. Wartość tej właściwości wskazuje definicję zadania w kontenerze magazynu. This property is how the Stream Analytics image is configured with your specific job details. 

   By default, the Stream Analytics module takes the same name as the job it's based on. You can change the module name on this page if you like, but it's not necessary. 

1. Close the module configuration page.

1. Zanotuj nazwę modułu usługi Stream Analytics, ponieważ będzie ona potrzebna w następnym kroku, a następnie wybierz przycisk **Dalej**, aby kontynuować.

1. Zastąp wartość domyślną w polu **Trasy** następującym kodem. Zastąp wszystkie trzy wystąpienia elementu _{moduleName}_ nazwą modułu usługi Azure Stream Analytics. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/SimulatedTemperatureSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   Trasy zadeklarowane w tym miejscu definiują przepływ danych przez urządzenie usługi IoT Edge. The telemetry data from SimulatedTemperatureSensor are sent to IoT Hub and to the **temperature** input that was configured in the Stream Analytics job. The **alert** output messages are sent to IoT Hub and to the SimulatedTemperatureSensor module to trigger the reset command. 

1. Wybierz opcję **Dalej**.

1. In the **Review Deployment** step, you can see how the information you provided in the wizard is converted into a JSON deployment manifest. When you're done reviewing the manifest, select **Submit**.

1. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**.  

    You should see the new Stream Analytics module running, along with the IoT Edge agent and IoT Edge hub modules. It may take a few minutes for the information to reach your IoT Edge device, and then for the new modules to start. If you don't see the modules running right away, continue refreshing the page.

    ![SimulatedTemperatureSensor and ASA module reported by device](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Wyświetlanie danych

Now you can go to your IoT Edge device to check out the interaction between the Azure Stream Analytics module and the SimulatedTemperatureSensor module.

1. Sprawdź, czy wszystkie moduły działają w aplikacji Docker:

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. Wyświetl wszystkie dzienniki systemu i dane metryk. Użyj nazwy modułu usługi Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. View the reset command affect the SimulatedTemperatureSensor by viewing the sensor logs:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   You can watch the machine's temperature gradually rise until it reaches 70 degrees for 30 seconds. Następnie moduł usługi Stream Analytics wywoła reset, a temperatura maszyny spadnie do 21 stopni. 

   ![Resetowanie danych wyjściowych polecenia do dzienników modułu](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem skonfigurowano zadanie usługi Azure Stream Analytics służące do analizy danych z urządzenia usługi IoT Edge. Następnie załadowano moduł usługi Azure Stream Analytics na urządzeniu usługi IoT Edge w celu lokalnego przetwarzania danych dotyczących temperatury i reagowania na wzrost temperatury, a także przesyłania zagregowanego strumienia danych do chmury. Aby zobaczyć, jak w usłudze Azure IoT Edge utworzyć więcej rozwiązań dla swojej firmy, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"] 
> [Wdrażanie modelu usługi Azure Machine Learning jako modułu](tutorial-deploy-machine-learning.md)
