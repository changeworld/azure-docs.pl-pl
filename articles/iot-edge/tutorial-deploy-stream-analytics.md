---
title: Stream Analytics na krawędzi Azure IoT Edge | Microsoft Docs
description: W tym samouczku przedstawiono wdrożenie Azure Stream Analytics jako modułu na urządzeniu IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0128574ff0ef9db1c5a4326e3ebce25fbba0c2e7
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845122"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Samouczek: Wdrażanie usługi Azure Stream Analytics jako modułu usługi IoT Edge

Wiele rozwiązań IoT korzysta z usług analitycznych w celu uzyskania wglądu w informacje o danych, które docierają do chmury z urządzeń IoT. Dzięki usłudze Azure IoT Edge możesz zastosować logikę usługi [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), wdrażając ją bezpośrednio na urządzeniu. Dzięki przetwarzaniu potoków danych telemetrycznych na urządzeniu brzegowym możesz zmniejszyć ilość przekazywanych danych i skrócić czas reakcji na szczegółowe informacje umożliwiające podejmowanie działań.

Dzięki integracji usług Azure IoT Edge i Azure Stream Analytics możesz utworzyć zadanie usługi Azure Stream Analytics w witrynie Azure Portal, a następnie wdrożyć je jako moduł usługi IoT Edge bez użycia dodatkowego kodu.  

Azure Stream Analytics zawiera rozbudowaną składnię zapytań na potrzeby analizy danych, zarówno w chmurze, jak i na urządzeniach z IoT Edge. Aby uzyskać więcej informacji, zobacz [dokumentację Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Moduł usługi Stream Analytics w tym samouczku oblicza średnią temperaturę w ruchomym przedziale 30 sekund. Jeśli średnia wyniesie 70 stopni, moduł wyśle dla urządzenia alert o konieczności podjęcia działania. W tym przypadku tym działaniem jest zresetowanie symulowanego czujnika temperatury. W środowisku produkcyjnym możesz użyć tej funkcji na przykład do wyłączenia maszyny lub podjęcia działań zapobiegawczych, gdy temperatura osiągnie niebezpieczną wartość. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zadania usługi Azure Stream Analytics w celu przetwarzania danych na urządzeniu brzegowym.
> * Łączenie nowego zadania usługi Azure Stream Analytics z innymi modułami usługi IoT Edge.
> * Wdrażanie zadania usługi Azure Stream Analytics na urządzeniu usługi IoT Edge z poziomu witryny Azure Portal.

<center>

![Diagram — architektura samouczka, zadanie etapowe i wdrażanie ASA](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Możesz użyć maszyny wirtualnej platformy Azure jako urządzenia IoT Edge, wykonując czynności opisane w przewodniku szybki start dla urządzeń z systemem [Linux](quickstart-linux.md) lub [Windows](quickstart.md).

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Tworzenie zadania usługi Azure Stream Analytics

W tej sekcji utworzysz zadanie Azure Stream Analytics, które wykona następujące czynności:
* Odbieraj dane z urządzenia IoT Edgeowego.
* Zbadaj dane telemetryczne dla wartości spoza zestawu.
* Wykonaj akcję na urządzeniu IoT Edge na podstawie wyników zapytania. 

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W przypadku tworzenia zadania usługi Azure Stream Analytics do uruchamiania na urządzeniu usługi IoT Edge musi być ono przechowywane w sposób umożliwiający wywoływanie go z urządzenia. Możesz użyć istniejącego konta usługi Azure Storage lub utworzyć teraz nowe konto. 

1. W Azure Portal przejdź do pozycji **Tworzenie zasobu magazynu zasobów** > . >  

1. Podaj następujące wartości, aby utworzyć konto magazynu:

   | Pole | Value |
   | ----- | ----- |
   | Subscription | Wybierz tę samą subskrypcję co używana dla centrum IoT Hub. |
   | Resource group | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |
   | Name (Nazwa) | Wprowadź unikatową nazwę konta magazynu. | 
   | Location | Wybierz bliską lokalizację. |


1. Zachowaj wartości domyślne dla innych pól i wybierz kolejno pozycje **Recenzja + Utwórz**.

1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-new-job"></a>Tworzenie nowego zadania

1. W Azure Portal przejdź do pozycji **Tworzenie zasobu** > **Internet rzeczy** > **Stream Analytics zadanie**.

1. Podaj następujące wartości, aby utworzyć zadanie:

   | Pole | Value |
   | ----- | ----- |
   | Nazwa zadania | Podaj nazwę zadania. Na przykład **IoTEdgeJob** | 
   | Subscription | Wybierz tę samą subskrypcję co używana dla centrum IoT Hub. |
   | Resource group | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |
   | Lokalizacja | Wybierz bliską lokalizację. | 
   | Środowisko hostingu | Wybierz pozycję **Edge**. |
 
1. Wybierz pozycję **Utwórz**.

### <a name="configure-your-job"></a>Konfigurowanie zadania

Po utworzeniu zadania usługi Stream Analytics w witrynie Azure Portal można je skonfigurować za pomocą danych wejściowych, danych wyjściowych i zapytania w celu uruchamiania na danych, które przez nie przechodzą. 

Przy użyciu trzech elementów — danych wejściowych, danych wyjściowych i zapytania — w tej sekcji zostanie utworzone zadanie, które odbiera dane dotyczące temperatury z urządzenia usługi IoT Edge. Analizuje ono te dane w kroczącym 30-sekundowym przedziale czasu. Jeśli średnia temperatura w tym przedziale czasu przekracza 70 stopni, wysyłany jest alert do urządzenia usługi IoT Edge. W następnej sekcji, podczas wdrażania zadania, określisz dokładnie, skąd dane pochodzą i dokąd są przesyłane.  

1. Przejdź do zadania usługi Stream Analytics w witrynie Azure Portal. 

1. W obszarze **Topologia zadania** wybierz pozycję **Dane wejściowe**, a następnie pozycję **Dodaj wejście strumienia**.

   ![Azure Stream Analytics — Dodaj dane wejściowe](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Wybierz pozycję **Edge Hub** z listy rozwijanej.

1. W okienku **Nowe dane wejściowe** wpisz **temperature** jako alias danych wejściowych. 

1. Zachowaj wartości domyślne dla pozostałych pól i wybierz pozycję **Zapisz**.

1. W obszarze **Topologia zadania** otwórz pozycję **Dane wyjściowe**, a następnie wybierz pozycję **Dodaj**.

   ![Azure Stream Analytics — Dodawanie danych wyjściowych](./media/tutorial-deploy-stream-analytics/asa-output.png)

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

1. W obszarze **Konfiguracja**wybierz pozycję **Ustawienia konta magazynu** , a następnie wybierz pozycję **Dodaj konto magazynu**. 

   ![Azure Stream Analytics — Dodaj konto magazynu](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Wybierz **konto magazynu** utworzone na początku tego samouczka z menu rozwijanego.

1. W polu **Kontener** wybierz pozycję **Utwórz nowy** i wpisz nazwę kontenera magazynu. 

1. Wybierz pozycję **Zapisz**. 

## <a name="deploy-the-job"></a>Wdrażanie zadania

Możesz teraz wdrożyć zadanie usługi Azure Stream Analytics na urządzeniu usługi IoT Edge. 

W tej sekcji użyjesz kreatora **Utwórz moduły** w witrynie Azure Portal, aby utworzyć *manifest wdrożenia*. Manifest wdrożenia to plik JSON opisujący wszystkie moduły, które zostaną wdrożone na urządzeniu, rejestry kontenerów, w których są przechowywane obrazy modułów, sposób, w jaki powinny być zarządzane moduły oraz sposób, w jaki moduły mogą komunikować się między sobą. Urządzenie usługi IoT Edge pobiera manifest wdrożenia z usługi IoT Hub, a następnie używa zawartych w nim informacji, aby wdrożyć i skonfigurować wszystkie przypisane moduły. 

W tym samouczku wdrażane są dwa moduły. Pierwszy to **SimulatedTemperatureSensor**, który jest modułem, który symuluje temperaturę i czujnik wilgotności. Drugi to zadanie usługi Stream Analytics. Moduł czujnika dostarcza strumień danych, który będzie analizowany przez zapytanie zadania. 

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

1. Przejdź do **IoT Edge**, a następnie otwórz stronę szczegółów urządzenia IoT Edge.

1. Wybierz opcję **Ustaw moduły**.  

1. Jeśli moduł SimulatedTemperatureSensor został wcześniej wdrożony na tym urządzeniu, może zostać automatycznie wypełniony. W przeciwnym razie dodaj moduł, wykonując następujące kroki:

   1. Kliknij pozycję **Dodaj** i wybierz pozycję **Moduł usługi IoT Edge**.
   1. W polu Nazwa wpisz **SimulatedTemperatureSensor**.
   1. W polu Identyfikator URI obrazu wpisz **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.

1. Dodaj zadanie usługi Azure Stream Analytics dla usługi IoT Edge, wykonując następujące kroki:

   1. Kliknij przycisk **Dodaj** i wybierz pozycję **Moduł usługi Azure Stream Analytics**.
   1. Wybierz subskrypcję i utworzone wcześniej zadanie usługi Azure Stream Analytics dla usługi IoT Edge. 
   1. Wybierz pozycję **Zapisz**.

   Po zapisaniu zmian szczegóły zadania Stream Analytics są publikowane w utworzonym kontenerze magazynu. 

1. Gdy moduł Stream Analytics zostanie dodany do listy modułów, wybierz pozycję **Konfiguruj** , aby zobaczyć, jak to jest struktura. 

   Identyfikator URI obrazu wskazuje standardowy obraz usługi Azure Stream Analytics. Ten obraz jest używany dla każdego modułu Stream Analytics, który jest wdrażany na urządzeniu IoT Edge. 

   Bliźniacza reprezentacja modułu jest skonfigurowana z żądaną właściwością o nazwie **ASAJobInfo**. Wartość tej właściwości wskazuje definicję zadania w kontenerze magazynu. Ta właściwość umożliwia skonfigurowanie obrazu Stream Analytics przy użyciu szczegółowych informacji o zadaniu. 

   Domyślnie moduł Stream Analytics ma taką samą nazwę jak zadanie, na podstawie którego bazuje. Możesz zmienić nazwę modułu na tej stronie, jeśli chcesz, ale nie jest to konieczne. 

1. Zamknij stronę Konfiguracja modułu.

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

   Trasy zadeklarowane w tym miejscu definiują przepływ danych przez urządzenie usługi IoT Edge. Dane telemetryczne z SimulatedTemperatureSensor są wysyłane do IoT Hub i do **temperatury** wejściowej, która została skonfigurowana w zadaniu Stream Analytics. Komunikaty wyjściowe alertu są wysyłane do IoT Hub i do modułu SimulatedTemperatureSensor, aby wyzwolić polecenie resetowania. 

1. Wybierz opcję **Dalej**.

1. W kroku **Przegląd wdrożenia** można zobaczyć, jak informacje podane w kreatorze są konwertowane do manifestu wdrożenia JSON. Po zakończeniu przeglądania manifestu wybierz pozycję **Prześlij**.

1. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**.  

    Powinien zostać wyświetlony nowy moduł Stream Analytics z uruchomionym programem oraz agentem IoT Edge i modułem Centrum IoT Edge. Dostęp do IoT Edge urządzenia może potrwać kilka minut, a następnie zostaną uruchomione nowe moduły. Jeśli nie widzisz jeszcze uruchomionych modułów, Kontynuuj odświeżanie strony.

    ![Moduł SimulatedTemperatureSensor i ASA zgłoszony przez urządzenie](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Wyświetlanie danych

Teraz możesz przejść do urządzenia IoT Edge, aby sprawdzić interakcję między modułem Azure Stream Analytics i modułem SimulatedTemperatureSensor.

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

1. Wyświetlenie polecenia Zresetuj ma wpływ na SimulatedTemperatureSensor przez wyświetlenie dzienników czujników:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Można monitorować temperaturę maszyny stopniowo, dopóki nie osiągnie ona 70 stopni przez 30 sekund. Następnie moduł usługi Stream Analytics wywoła reset, a temperatura maszyny spadnie do 21 stopni. 

   ![Resetowanie danych wyjściowych polecenia do dzienników modułu](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć naliczania opłat. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem skonfigurowano zadanie usługi Azure Stream Analytics służące do analizy danych z urządzenia usługi IoT Edge. Następnie załadowano moduł usługi Azure Stream Analytics na urządzeniu usługi IoT Edge w celu lokalnego przetwarzania danych dotyczących temperatury i reagowania na wzrost temperatury, a także przesyłania zagregowanego strumienia danych do chmury. Aby zobaczyć, jak w usłudze Azure IoT Edge utworzyć więcej rozwiązań dla swojej firmy, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"] 
> [Wdrażanie modelu usługi Azure Machine Learning jako modułu](tutorial-deploy-machine-learning.md)
