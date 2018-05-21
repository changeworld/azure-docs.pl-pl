---
title: Wdrażanie usługi Azure Stream Analytics Azure IoT krawędzi | Dokumentacja firmy Microsoft
description: Wdrażanie usługi Azure Stream Analytics jako moduł do urządzenia
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5d4c2b3bc55b94b08287a06125e15ac61013834a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Wdrażanie usługi Azure Stream Analytics jako moduł krawędzi IoT — w wersji preview

Urządzenia IoT może spowodować duże ilości danych. Aby zmniejszyć ilość danych przekazanego lub wyeliminowanie obustronnych opóźnień możliwością szczegółowe informacje o danych czasami musi przeanalizowane lub przetworzone przed osiągnie chmury.

Azure IoT krawędzi korzysta z wbudowanych usługa Azure IoT krawędzi modułów do szybkiego wdrożenia. [Usługa Azure Stream Analytics] [ azure-stream] jest takich modułów. Można utworzyć zadania usługi analiza strumienia Azure z portalu, a następnie przejdź do portalu Centrum IoT Azure, aby wdrożyć go jako moduł IoT krawędzi. 

Usługa Azure Stream Analytics zawiera Bogato strukturalnych składnię analizy danych, zarówno w chmurze, jak i na krawędzi IoT urządzeń. Aby uzyskać więcej informacji na temat usługi Azure Stream Analytics na krawędzi IoT, zobacz [dokumentacji usługi Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

W tym samouczku przedstawiono sposób tworzenia zadania usługi analiza strumienia Azure i wdrażania go na urządzeniu IoT krawędzi. Umożliwia to przetworzyć strumienia lokalnego telemetrii bezpośrednio na urządzeniu i generowania alertów, którzy sterują natychmiastowego działania na urządzeniu. 

Samouczek przedstawia dwa moduły: 
* Symulowane temperatury czujnik modułu (tempSensor) generujący danych temperatury 20 stopni 120, zwiększana o 1 co 5 sekund. 
* Moduł usługi analiza strumienia, który resetuje tempSensor, gdy średni 30 sekund osiągnie 70. W środowisku produkcyjnym wyłączone maszynę lub gdy temperatura osiągnie niebezpieczne poziomy podjęcia środków zapobiegawczych może użyć tej funkcji. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz zadanie usługi analiza strumienia Azure do przetwarzania danych na krawędzi.
> * Nowe zadanie usługi analiza strumienia Azure Uzyskuj dostęp do innych modułów IoT krawędzi.
> * Wdróż zadania usługi analiza strumienia Azure IoT urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Centrum IoT. 
* Urządzenia, który został utworzony i skonfigurowany z opcją szybkiego startu lub w artykułach dotyczących wdrażania usługi Azure IoT krawędzi na symulowane urządzenie w [Windows] [ lnk-tutorial1-win] lub [Linux] [ lnk-tutorial1-lin]. Należy znać klucza połączenia urządzenia i identyfikator urządzenia. 
* Docker uruchomione na urządzeniu IoT krawędzi.
    * [Zainstaluj Docker w systemie Windows][lnk-docker-windows].
    * [Zainstaluj Docker w systemie Linux][lnk-docker-linux].
* Python 2.7.x na urządzeniu IoT krawędzi.
    * [Instalowanie języka Python 2.7 w systemie Windows][lnk-python].
    * Większość dystrybucje systemu Linux, łącznie z Ubuntu, już Python 2.7 zainstalowane. Aby upewnić się, że zainstalowano narzędzia pip, użyj następującego polecenia: `sudo apt-get install python-pip`.

## <a name="create-an-azure-stream-analytics-job"></a>Utwórz zadanie usługi analiza strumienia Azure

W tej sekcji utworzysz zadanie usługi analiza strumienia Azure do pobierają dane z Centrum IoT, zapytania na danych telemetrycznych wysłanych z urządzenia, a następnie przekazać wyniki do kontenera magazynu obiektów Blob platformy Azure. Aby uzyskać więcej informacji, zobacz sekcję "Overview" [dokumentacji analityka strumienia][azure-stream]. 

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto usługi Azure Storage są wymagane do świadczenia punkt końcowy do użycia jako dane wyjściowe z zadania usługi analiza strumienia Azure. W przykładzie w tej sekcji użyto typu magazynu obiektów Blob. Aby uzyskać więcej informacji, zobacz sekcję "Blob" [dokumentację usługi Azure Storage][azure-storage].

1. W portalu Azure, przejdź do **Utwórz zasób**, wprowadź **konta magazynu** w polu wyszukiwania, a następnie wybierz **konta magazynu — obiekt blob, plików, tabeli, kolejki**.

2. W **utworzyć konto magazynu** okienku, wprowadź nazwę konta magazynu, wybierz lokalizację, w którym przechowywana jest Centrum IoT, wybierz grupę zasobów jako centrum IoT, a następnie wybierz **Utwórz**. Zanotuj nazwę do późniejszego użycia.

    ![Tworzenie konta magazynu][1]

3. Przejdź do konta magazynu, który został właśnie utworzony, a następnie wybierz **Przeglądaj obiekty BLOB**. 

4. Utwórz nowy kontener dla modułu usługi analiza strumienia Azure do przechowywania danych, Ustaw dostęp poziom **kontenera**, a następnie wybierz **OK**.

    ![Ustawienia magazynu][10]

### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W portalu Azure, przejdź do **Utwórz zasób** > **Internetu rzeczy**, a następnie wybierz **zadania usługi analiza strumienia**.

2. W **nowego zadania usługi analiza strumienia** okienko, wykonaj następujące czynności:

    a. W **Nazwa zadania** wpisz nazwę zadania.
    
    b. W obszarze **środowisko macierzyste**, wybierz pozycję **krawędzi**.
    
    c. W pozostałych polach należy użyć wartości domyślnych.

    > [!NOTE]
    > Obecnie zadania usługi analiza strumienia Azure IoT krawędzi nie są obsługiwane w regionu zachodnie stany USA 2. 

3. Wybierz pozycję **Utwórz**.

4. W zadaniu utworzony w obszarze **topologii zadania**, otwórz **dane wejściowe**.

   ![Azure Stream Analytics danych wejściowych.](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Wybierz **dodać strumienia danych wejściowych**, a następnie wybierz pozycję **krawędzi Centrum**.

5. W **wprowadzania nowych** okienku, wprowadź **temperatury** jako alias wejściowy. 

6. Wybierz pozycję **Zapisz**.

7. W obszarze **topologii zadania**, otwórz **dane wyjściowe**.

   ![Azure Stream Analytics danych wyjściowych.](./media/tutorial-deploy-stream-analytics/asa_output.png)

8. Wybierz **Dodaj**, a następnie wybierz pozycję **krawędzi Centrum**.

8. W **nowe dane wyjściowe** okienku, wprowadź **alert** jako alias wyjściowy. 

9. Wybierz pozycję **Utwórz**.

9. W obszarze **topologii zadania**, wybierz pozycję **zapytania**, a następnie zastąpić domyślny tekst następujące zapytanie:

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

10. Wybierz pozycję **Zapisz**.

## <a name="deploy-the-job"></a>Zadanie wdrażania

Teraz można przystąpić do wdrażania zadania usługi analiza strumienia Azure na urządzeniu IoT krawędzi.

1. W portalu Azure, w Centrum IoT, przejdź do **IoT krawędzi (wersja zapoznawcza)**, a następnie otwórz strona szczegółów urządzenia IoT krawędzi.

2. Wybierz opcję **Ustaw moduły**.  
    Jeśli wdrożono modułu tempSensor na tym urządzeniu może automatycznego wypełniania. Jeśli nie, Dodaj moduł w następujący sposób:

   a. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.

   b. Na stronie Nazwa wpisz **tempSensor**.
    
   c. Dla identyfikatora URI obrazu, wprowadź **microsoft/azureiotedge symulowane temperatury-czujnik: 1.0-preview**. 

   d. Pozostaw innych ustawień bez zmian.
   
   e. Wybierz pozycję **Zapisz**.

3. Aby dodać zadanie Azure Stream Analytics krawędzi, zaznacz **Import Azure Stream Analytics IoT krawędzi Module**.

4. Wybierz subskrypcję i zadania usługi Azure Stream Analytics krawędzi, utworzony. 

5. Wybierz subskrypcję i konto magazynu, który zostanie utworzony, a następnie wybierz **zapisać**.

    ![Moduł zestawu][6]

6. Skopiuj nazwę modułu Azure Stream Analytics. 

    ![Moduł temperatury][11]

7. Można skonfigurować tras, wybierz **dalej**.

8. Skopiuj następujący kod do **tras**. Zastąp _{moduleName}_ ze skopiowanego Nazwa modułu:

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

9. Wybierz opcję **Dalej**.

10. W kroku **Przegląd szablonu** wybierz opcję **Prześlij**.

11. Wróć do strony szczegółów urządzenia, a następnie wybierz **Odśwież**.  
    Moduł usługi Stream Analytics uruchomiona wraz z modułem agenta krawędzi IoT i Centrum IoT krawędzi powinna zostać wyświetlona.

    ![dane wyjściowe z modułu][7]

## <a name="view-data"></a>Wyświetlanie danych

Teraz można przejść do Twojego urządzenia IoT krawędzi wyewidencjonować interakcji między modułu tempSensor i moduł Azure Stream Analytics.

1. Sprawdź, czy w rozwiązaniu Docker są uruchomione wszystkie moduły:

   ```cmd/sh
   docker ps  
   ```

   ![dane wyjściowe docker][8]

2. Wyświetl wszystkie dane dzienników i metryki systemu. Użyj nazwy modułu usługi Stream Analytics:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Należy się patrzeć na komputerze temperatury stopniowo rosną, dopóki nie osiągnie 70 stopni 30 sekund. Następnie moduł Stream Analytics wyzwala Resetowanie i temperatury maszyny spadnie wstecz do 21. 

   ![Dziennik docker][9]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku należy skonfigurować kontener magazynu Azure i zadanie analizy przesyłania strumieniowego do analizowania danych z urządzenia IoT krawędzi. Następnie ładowany niestandardowego modułu Azure Stream Analytics do przeniesienia danych z urządzenia, za pomocą strumienia do obiektu blob do pobrania. Aby zobaczyć, jak utworzyć więcej rozwiązania dla biznesu Azure IoT krawędzi, przejść do innych samouczków.

> [!div class="nextstepaction"] 
> [Wdróż model uczenia maszynowego Azure jako moduł][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
