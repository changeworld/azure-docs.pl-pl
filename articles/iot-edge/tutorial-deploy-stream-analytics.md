---
title: 'Samouczek: wdrażanie zadania usługi Azure Stream Analytics na urządzeniu — Azure IoT Edge | Microsoft Docs'
description: W tym samouczku usługa Azure Stream Analytics zostanie wdrożona jako moduł na urządzeniu usługi IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 31330c3d2b10e6245db775da4039fd1948539df4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096287"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Samouczek: Wdrażanie usługi Azure Stream Analytics jako modułu usługi IoT Edge

W wielu rozwiązaniach IoT używane są usługi analizy, które pozwalają uzyskiwać szczegółowe informacje na temat danych w miarę przesyłania ich do chmury z urządzeń IoT. Dzięki usłudze Azure IoT Edge możesz zastosować logikę usługi [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), wdrażając ją bezpośrednio na urządzeniu. Dzięki przetwarzaniu potoków danych telemetrycznych na urządzeniu brzegowym możesz zmniejszyć ilość przekazywanych danych i skrócić czas reakcji na szczegółowe informacje umożliwiające podejmowanie działań.

Dzięki integracji usług Azure IoT Edge i Azure Stream Analytics możesz utworzyć zadanie usługi Azure Stream Analytics w witrynie Azure Portal, a następnie wdrożyć je jako moduł usługi IoT Edge bez użycia dodatkowego kodu.  

Usługa Azure Stream Analytics oferuje zaawansowaną składnię zapytań strukturalnych do analizy danych zarówno w chmurze, jak i na urządzeniach usługi IoT Edge. Aby uzyskać więcej informacji na temat usługi Azure Stream Analytics na urządzeniach brzegowych IoT Edge, zobacz [dokumentację usługi Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Moduł usługi Stream Analytics w tym samouczku oblicza średnią temperaturę w ruchomym przedziale 30 sekund. Jeśli średnia wyniesie 70 stopni, moduł wyśle dla urządzenia alert o konieczności podjęcia działania. W tym przypadku tym działaniem jest zresetowanie symulowanego czujnika temperatury. W środowisku produkcyjnym możesz użyć tej funkcji na przykład do wyłączenia maszyny lub podjęcia działań zapobiegawczych, gdy temperatura osiągnie niebezpieczną wartość. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zadania usługi Azure Stream Analytics w celu przetwarzania danych na urządzeniu brzegowym.
> * Łączenie nowego zadania usługi Azure Stream Analytics z innymi modułami usługi IoT Edge.
> * Wdrażanie zadania usługi Azure Stream Analytics na urządzeniu usługi IoT Edge z poziomu witryny Azure Portal.

<center>

![Diagram — samouczek architektury, testowanie i wdrażanie zadanie ASA](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Jako urządzenia brzegowego możesz użyć maszyny deweloperskiej albo maszyny wirtualnej, postępując zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń z systemem [Linux](quickstart-linux.md) lub [Windows](quickstart.md).

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Tworzenie zadania usługi Azure Stream Analytics

W tej sekcji utworzysz zadanie usługi Azure Stream Analytics obejmujące pobieranie danych z centrum IoT Hub, wykonywanie zapytań o dane telemetryczne przesłane z urządzenia i przekazywanie wyników do kontenera usługi Azure Blob Storage. 

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W przypadku tworzenia zadania usługi Azure Stream Analytics do uruchamiania na urządzeniu usługi IoT Edge musi być ono przechowywane w sposób umożliwiający wywoływanie go z urządzenia. Możesz użyć istniejącego konta usługi Azure Storage lub utworzyć teraz nowe konto. 

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Storage** > **Konto usługi Storage — Blob, File, Table, Queue**. 

1. Podaj następujące wartości, aby utworzyć konto magazynu:

   | Pole | Wartość |
   | ----- | ----- |
   | Name (Nazwa) | Wprowadź unikatową nazwę konta magazynu. | 
   | Lokalizacja | Wybierz bliską lokalizację. |
   | Subskrypcja | Wybierz tę samą subskrypcję co używana dla centrum IoT Hub. |
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |

1. Zachowaj wartości domyślne dla pozostałych pól i wybierz pozycję **Utwórz**. 

### <a name="create-a-new-job"></a>Tworzenie nowego zadania

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.

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

   ![Dodawanie danych wejściowych usługi Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. Wybierz pozycję **Edge Hub** z listy rozwijanej.

1. W okienku **Nowe dane wejściowe** wpisz **temperature** jako alias danych wejściowych. 

1. Zachowaj wartości domyślne dla pozostałych pól i wybierz pozycję **Zapisz**.

1. W obszarze **Topologia zadania** otwórz pozycję **Dane wyjściowe**, a następnie wybierz pozycję **Dodaj**.

   ![Dodawanie danych wyjściowych usługi Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)

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

1. W obszarze **Konfigurowanie** wybierz opcję **Ustawienia konta magazynu**.

1. Wybierz polecenie **Dodaj konto magazynu**. 

1. Wybierz **konto magazynu** z menu rozwijanego.

1. W polu **Kontener** wybierz pozycję **Utwórz nowy** i wpisz nazwę kontenera magazynu. 

1. Wybierz pozycję **Zapisz**. 

## <a name="deploy-the-job"></a>Wdrażanie zadania

Możesz teraz wdrożyć zadanie usługi Azure Stream Analytics na urządzeniu usługi IoT Edge. 

W tej sekcji użyjesz kreatora **Utwórz moduły** w witrynie Azure Portal, aby utworzyć *manifest wdrożenia*. Manifest wdrożenia to plik JSON opisujący wszystkie moduły, które zostaną wdrożone na urządzeniu, rejestry kontenerów, w których są przechowywane obrazy modułów, sposób, w jaki powinny być zarządzane moduły oraz sposób, w jaki moduły mogą komunikować się między sobą. Urządzenie usługi IoT Edge pobiera manifest wdrożenia z usługi IoT Hub, a następnie używa zawartych w nim informacji, aby wdrożyć i skonfigurować wszystkie przypisane moduły. 

W tym samouczku wdrażane są dwa moduły. Pierwszy to moduł **tempSensor**, który symuluje czujnik temperatury i wilgotności. Drugi to zadanie usługi Stream Analytics. Moduł czujnika dostarcza strumień danych, który będzie analizowany przez zapytanie zadania. 

1. W witrynie Azure Portal w używanym centrum IoT Hub przejdź do sekcji **IoT Edge**, a następnie otwórz stronę szczegółów urządzenia usługi IoT Edge.

1. Wybierz opcję **Ustaw moduły**.  

1. Jeśli moduł tempSensor został już wcześniej wdrożony na tym urządzeniu, może on zostać automatycznie wypełniony. W przeciwnym razie dodaj moduł, wykonując następujące kroki:

   1. Kliknij pozycję **Dodaj** i wybierz pozycję **Moduł usługi IoT Edge**.
   1. W polu Nazwa wpisz **tempSensor**.
   1. W polu Identyfikator URI obrazu wpisz **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.

1. Dodaj zadanie usługi Azure Stream Analytics dla usługi IoT Edge, wykonując następujące kroki:

   1. Kliknij przycisk **Dodaj** i wybierz pozycję **Moduł usługi Azure Stream Analytics**.
   1. Wybierz subskrypcję i utworzone wcześniej zadanie usługi Azure Stream Analytics dla usługi IoT Edge. 
   1. Wybierz pozycję **Zapisz**.

1. Po opublikowaniu zadania usługi Stream Analytics w utworzonym kontenerze magazynu kliknij nazwę modułu, aby zobaczyć strukturę modułu usługi Stream Analytics. 

   Identyfikator URI obrazu wskazuje standardowy obraz usługi Azure Stream Analytics. Jest to ten sam obraz, które jest używany dla każdego zadania wdrażanego na urządzeniu usługi IoT Edge. 

   Bliźniacza reprezentacja modułu jest skonfigurowana z żądaną właściwością o nazwie **ASAJobInfo**. Wartość tej właściwości wskazuje definicję zadania w kontenerze magazynu. Ta właściwość określa, jak obraz usługi Stream Analytics jest skonfigurowany z informacjami o konkretnym zadaniu. 

1. Zamknij stronę modułu.

1. Zanotuj nazwę modułu usługi Stream Analytics, ponieważ będzie ona potrzebna w następnym kroku, a następnie wybierz przycisk **Dalej**, aby kontynuować.

1. Zastąp wartość domyślną w polu **Trasy** następującym kodem. Zastąp wszystkie trzy wystąpienia elementu _{moduleName}_ nazwą modułu usługi Azure Stream Analytics. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   Trasy zadeklarowane w tym miejscu definiują przepływ danych przez urządzenie usługi IoT Edge. Dane telemetryczne z modułu tempSensor są wysyłane do usługi IoT Hub i do danych wejściowych **temperatury**, które zostały skonfigurowane w zadaniu usługi Stream Analytics. Komunikaty wyjściowe **alertu** są wysyłane do usługi IoT Hub i do modułu tempSensor w celu wyzwolenia polecenia reset. 

1. Wybierz opcję **Dalej**.

1. W kroku **Przegląd wdrożenia** wybierz pozycję **Prześlij**.

1. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**.  

    Powinien zostać wyświetlony nowy moduł usługi Stream Analytics, uruchomiony wraz z modułem agenta usługi IoT Edge i centrum usługi IoT Edge.

    ![Moduły tempSensor i ASA zgłoszone przez urządzenie](./media/tutorial-deploy-stream-analytics/module_output2.png)

## <a name="view-data"></a>Wyświetlanie danych

Teraz możesz przejść do urządzenia usługi IoT Edge, aby sprawdzić interakcje pomiędzy modułem usługi Azure Stream Analytics a modułem tempSensor.

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

Powinny zostać wyświetlone stopniowo rosnące wartości temperatury maszyny, do momentu osiągnięcia wartości 70 stopni przez 30 sekund. Następnie moduł usługi Stream Analytics wywoła reset, a temperatura maszyny spadnie do 21 stopni. 

   ![Resetowanie danych wyjściowych polecenia do dzienników modułu](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Kolejne kroki

Podczas pracy z tym samouczkiem skonfigurowano zadanie usługi Azure Stream Analytics służące do analizy danych z urządzenia usługi IoT Edge. Następnie załadowano moduł usługi Azure Stream Analytics na urządzeniu usługi IoT Edge w celu lokalnego przetwarzania danych dotyczących temperatury i reagowania na wzrost temperatury, a także przesyłania zagregowanego strumienia danych do chmury. Aby zobaczyć, jak w usłudze Azure IoT Edge utworzyć więcej rozwiązań dla swojej firmy, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"] 
> [Wdrażanie modelu usługi Azure Machine Learning jako modułu](tutorial-deploy-machine-learning.md)
