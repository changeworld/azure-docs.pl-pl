---
title: Samouczek — wdrażanie zadań usługi ASA na urządzeniach usługi Azure IoT Edge | Microsoft Docs
description: Wdrażanie usługi Azure Stream Analytics jako modułu na urządzeniu usługi IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0790f504c978b4302812cffc9b655e817c156da3
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081391"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Samouczek: wdrażanie usługi Azure Stream Analytics jako modułu usługi IoT Edge — wersja zapoznawcza

W wielu rozwiązaniach IoT używane są usługi analizy, które pozwalają uzyskiwać szczegółowe informacje na temat danych w miarę przesyłania ich do chmury z urządzeń IoT. Dzięki usłudze Azure IoT Edge możesz zastosować logikę usługi [Azure Stream Analytics][azure-stream], wdrażając ją bezpośrednio na urządzeniu. Dzięki przetwarzaniu potoków danych telemetrycznych na urządzeniu brzegowym możesz zmniejszyć ilość przekazywanych danych i skrócić czas reakcji na szczegółowe informacje umożliwiające podejmowanie działań.

Dzięki integracji usług Azure IoT Edge i Azure Stream Analytics możesz utworzyć zadanie usługi Azure Stream Analytics w witrynie Azure Portal, a następnie wdrożyć je jako moduł usługi IoT Edge bez użycia dodatkowego kodu.  

Usługa Azure Stream Analytics oferuje zaawansowaną składnię zapytań strukturalnych do analizy danych zarówno w chmurze, jak i na urządzeniach usługi IoT Edge. Aby uzyskać więcej informacji na temat usługi Azure Stream Analytics na urządzeniach brzegowych IoT Edge, zobacz [dokumentację usługi Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Moduł usługi Stream Analytics w tym samouczku oblicza średnią temperaturę w ruchomym przedziale 30 sekund. Jeśli średnia wyniesie 70 stopni, moduł wyśle dla urządzenia alert o konieczności podjęcia działania. W tym przypadku tym działaniem jest zresetowanie symulowanego czujnika temperatury. W środowisku produkcyjnym możesz użyć tej funkcji na przykład do wyłączenia maszyny lub podjęcia działań zapobiegawczych, gdy temperatura osiągnie niebezpieczną wartość. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zadania usługi Azure Stream Analytics w celu przetwarzania danych na urządzeniu brzegowym.
> * Łączenie nowego zadania usługi Azure Stream Analytics z innymi modułami usługi IoT Edge.
> * Wdrażanie zadania usługi Azure Stream Analytics na urządzeniu usługi IoT Edge z poziomu witryny Azure Portal.

>[!NOTE]
>Moduły usługi Azure Stream Analytics dla usługi IoT Edge są obecnie dostępne w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

* Centrum IoT Hub.
* Urządzenie usługi IoT Edge utworzone i skonfigurowane podczas pracy z przewodnikiem Szybki start dla systemu [Windows][lnk-quickstart-win] lub [Linux][lnk-quickstart-lin]. 

## <a name="create-an-azure-stream-analytics-job"></a>Tworzenie zadania usługi Azure Stream Analytics

W tej sekcji utworzysz zadanie usługi Azure Stream Analytics obejmujące pobieranie danych z centrum IoT Hub, wykonywanie zapytań o dane telemetryczne przesłane z urządzenia i przekazywanie wyników do kontenera usługi Azure Blob Storage. Aby uzyskać więcej informacji, zobacz sekcję „Omówienie” w [dokumentacji usługi Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Jako punktu końcowego dla danych wyjściowych zadania usługi Azure Stream Analytics należy użyć konta usługi Azure Storage. W przykładzie w tej sekcji użyto magazynu obiektów blob. Aby uzyskać więcej informacji, zobacz sekcję „Obiekty blob” w [dokumentacji usługi Azure Storage][azure-storage].

1. W witrynie Azure Portal wybierz polecenie **Utwórz zasób**, wpisz **konto magazynu** w polu wyszukiwania, a następnie wybierz pozycję **Konto magazynu — obiekt blob, plik, tabela, kolejka**.

2. W okienku **Tworzenie konta magazynu** wprowadź nazwę konta magazynu, wybierz tę samą lokalizację i tę samą grupę zasobów, w których znajduje się centrum IoT Hub, a następnie wybierz przycisk **Utwórz**. Zapisz tę nazwę do późniejszego użycia.

    ![Tworzenie konta magazynu][1]


### <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Internet rzeczy**, a następnie wybierz pozycję **Zadanie usługi Stream Analytics**.

2. W okienku **Nowe zadanie usługi Stream Analytics** wykonaj następujące kroki:

   1. W polu **Nazwa zadania** wpisz nazwę zadania.
   
   2. Użyj tej samej **grupy zasobów** i **lokalizacji**, w których znajduje się centrum IoT Hub. 

      > [!NOTE]
      > Obecnie zadania usługi Azure Stream Analytics na urządzeniach brzegowych IoT Edge nie są obsługiwane w regionie Zachodnie stany USA 2. 

   3. W obszarze **Środowisko hostingu** wybierz pozycję **Edge**.
    
3. Wybierz pozycję **Utwórz**.

4. W utworzonym zadaniu w sekcji **Topologia zadań** wybierz pozycję **Dane wejściowe**.

   ![Dane wejściowe usługi Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Wybierz pozycję **Dodaj wejście strumienia**, a następnie pozycję **Edge Hub**.

6. W okienku **Nowe dane wejściowe** wpisz **temperature** jako alias danych wejściowych. 

7. Wybierz pozycję **Zapisz**.

8. W obszarze **Topologia zadań** kliknij pozycję **Dane wyjściowe**.

   ![Dane wyjściowe usługi Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)

9. Wybierz pozycję **Dodaj**, a następnie pozycję **Edge Hub**.

10. W okienku **Nowe dane wyjściowe** wpisz **alert** jako alias danych wyjściowych. 

11. Wybierz pozycję **Zapisz**.

12. W obszarze **Topologia zadań** wybierz pozycję **Zapytanie**, a następnie zastąp tekst domyślny następującym zapytaniem, służącym do generowania alertu, gdy średnia temperatura maszyny w przedziale 30 sekund osiągnie 70 stopni:

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

13. Wybierz pozycję **Zapisz**.

14. W obszarze **Konfigurowanie** wybierz pozycję **Ustawienia usługi IoT Edge**.

15. Wybierz **konto magazynu** z menu rozwijanego.

16. W polu **Kontener** wybierz pozycję **Utwórz nowy** i wpisz nazwę kontenera magazynu. 

17. Wybierz pozycję **Zapisz**. 


## <a name="deploy-the-job"></a>Wdrażanie zadania

Możesz teraz wdrożyć zadanie usługi Azure Stream Analytics na urządzeniu usługi IoT Edge.

1. W witrynie Azure Portal w używanym centrum IoT Hub przejdź do sekcji **IoT Edge**, a następnie otwórz stronę szczegółów urządzenia usługi IoT Edge.

2. Wybierz opcję **Ustaw moduły**.  

   Jeśli moduł tempSensor został już wcześniej wdrożony na tym urządzeniu, może on zostać automatycznie wypełniony. W przeciwnym razie dodaj moduł, wykonując następujące kroki:

   1. Kliknij pozycję **Dodaj** i wybierz pozycję **Moduł usługi IoT Edge**.
   2. W polu Nazwa wpisz **tempSensor**.
   3. W polu Identyfikator URI obrazu wpisz **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   4. Pozostaw inne ustawienia bez zmian.
   5. Wybierz pozycję **Zapisz**.

3. Dodaj zadanie usługi Azure Stream Analytics dla usługi IoT Edge, wykonując następujące kroki:

   1. Kliknij przycisk **Dodaj** i wybierz pozycję **Moduł usługi Azure Stream Analytics**.
   2. Wybierz subskrypcję i utworzone wcześniej zadanie usługi Azure Stream Analytics dla usługi IoT Edge. 
   3. Wybierz pozycję **Zapisz**.

4. Wybierz opcję **Dalej**.

5. Zastąp wartość domyślną w polu **Trasy** następującym kodem. Zastąp wartość _{moduleName}_ nazwą modułu usługi Azure Stream Analytics. Moduł powinien mieć taką samą nazwę jak zadanie, na podstawie którego został utworzony. 

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

6. Wybierz opcję **Dalej**.

7. W kroku **Przegląd wdrożenia** wybierz pozycję **Prześlij**.

8. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**.  

    Powinien zostać wyświetlony nowy moduł usługi Stream Analytics, uruchomiony wraz z modułem agenta usługi IoT Edge i centrum usługi IoT Edge.

    ![Dane wyjściowe modułu][7]

## <a name="view-data"></a>Wyświetlanie danych

Teraz możesz przejść do urządzenia usługi IoT Edge, aby sprawdzić interakcje pomiędzy modułem usługi Azure Stream Analytics a modułem tempSensor.

1. Sprawdź, czy wszystkie moduły działają w aplikacji Docker:

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
2. Wyświetl wszystkie dzienniki systemu i dane metryk. Użyj nazwy modułu usługi Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Powinny zostać wyświetlone stopniowo rosnące wartości temperatury maszyny, do momentu osiągnięcia wartości 70 stopni przez 30 sekund. Następnie moduł usługi Stream Analytics wywoła reset, a temperatura maszyny spadnie do 21 stopni. 

   ![Dziennik aplikacji Docker][9]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować już utworzone zasoby oraz konfiguracje i użyć ich ponownie.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie zasobów i grupy zasobów platformy Azure jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć tylko centrum IoT Hub, uruchom następujące polecenie, używając nazwy centrum i nazwy grupy zasobów:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Aby usunąć całą grupę zasobów na podstawie nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

2. W polu tekstowym **Filtruj według nazwy** wpisz nazwę grupy zasobów zawierającej usługę IoT Hub. 

3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem skonfigurowano zadanie usługi Azure Stream Analytics służące do analizy danych z urządzenia usługi IoT Edge. Następnie załadowano moduł usługi Azure Stream Analytics na urządzeniu usługi IoT Edge w celu lokalnego przetwarzania danych dotyczących temperatury i reagowania na wzrost temperatury, a także przesyłania zagregowanego strumienia danych do chmury. Aby zobaczyć, jak w usłudze Azure IoT Edge utworzyć więcej rozwiązań dla swojej firmy, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"] 
> [Wdrażanie modelu usługi Azure Machine Learning jako modułu][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
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
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

