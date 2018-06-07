Jedną z najważniejszych funkcji usługi Azure IoT Edge jest możliwość wdrażania modułów na urządzeniach usługi IoT Edge z poziomu chmury. Moduł usługi IoT Edge to pakiet wykonywalny wdrażany jako kontener. W tej sekcji zapoznasz się ze sposobem wdrażania modułu generującego dane telemetryczne na symulowanym urządzeniu. 

1. W witrynie Azure Portal przejdź do centrum IoT Hub.
1. Przejdź do pozycji **IoT Edge (wersja zapoznawcza)** i wybierz urządzenie usługi IoT Edge.
1. Wybierz pozycję **Ustaw moduły**.
1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
1. W polu **Nazwa** wprowadź wartość `tempSensor`. 
1. W polu **Identyfikator URI obrazu** wprowadź wartość `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
1. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.

   ![Zapisywanie modułu usługi IoT Edge po wprowadzeniu nazwy i identyfikatora URI obrazu](./media/iot-edge-deploy-module/name-image.png)

1. W kroku **Dodawanie modułów** wybierz opcję **Dalej**.
1. W kroku **Określanie tras** wybierz opcję **Dalej**.
1. W kroku **Przegląd szablonu** wybierz opcję **Prześlij**.
1. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Nowy moduł tempSensor powinien zostać uruchomiony wraz ze środowiskiem uruchomieniowym usługi IoT Edge. 

   ![Wyświetlanie modułu tempSensor na liście wdrożonych modułów][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png