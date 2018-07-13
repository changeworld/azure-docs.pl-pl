## <a name="configure-the-nodejs-simulated-device"></a>Konfigurowanie symulowanego urządzenia środowiska Node.js
1. Na pulpicie nawigacyjnym monitorowania zdalnego kliknij **+ Dodaj urządzenie** , a następnie dodaj *urządzeń niestandardowych*. Zanotuj usługi IoT Hub nazwę hosta, identyfikator urządzenia i klucz urządzenia. Będą one potrzebne w dalszej części tego samouczka podczas przygotowywania aplikacji klienckiej remote_monitoring.js urządzenia.
2. Upewnij się, że środowisko Node.js w wersji 0.12.x lub nowszy jest zainstalowany na komputerze deweloperskim. Uruchom `node --version` polecenie w wierszu polecenia lub w powłoce, aby sprawdzić wersję. Aby dowiedzieć się, jak przy użyciu Menedżera pakietów dotyczące instalowania programu Node.js w systemie Linux, zobacz [instalowanie środowiska Node.js przy użyciu Menedżera pakietów][node-linux].
3. Po zainstalowaniu środowiska Node.js, klonowanie najnowszą wersję [węzeł platformy azure-iot-sdk] [ lnk-github-repo] repozytorium na komputerze deweloperskim. Zawsze używaj **wzorca** gałąź dla najnowszej wersji biblioteki i przykłady.
4. Z lokalną kopię [węzeł platformy azure-iot-sdk] [ lnk-github-repo] repozytorium, należy skopiować następujące dwa pliki z folderu node/urządzenie/samples do pustego folderu na komputerze deweloperskim:
   
   * Packages.JSON
   * remote_monitoring.js
5. Otwórz plik remote_monitoring.js i poszukaj następujących definicji zmiennej:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Zastąp **[parametry połączenia urządzenia usługi IoT Hub]** parametrami połączenia urządzenia. Użyj wartości dla nazwy hosta Centrum IoT, identyfikator urządzenia i klucz urządzenia, które należy zanotować w kroku 1. Parametry połączenia urządzenia ma następujący format:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Jeśli nazwa hosta usługi IoT Hub jest **contoso** i swojej identyfikator_urządzenia **mydevice**, parametrów połączenia wygląda jak poniższy fragment kodu:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Zapisz plik. Uruchom następujące polecenia powłoki lub wiersza polecenia w folderze, który zawiera te pliki, aby zainstalować wymagane pakiety, a następnie uruchom przykładową aplikację:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Obserwuj telemetrii dynamicznej w działaniu
Na pulpicie nawigacyjnym prezentowana temperatury i wilgotności dane telemetryczne z istniejących symulowane urządzenia:

![Domyślny pulpit nawigacyjny][image1]

Jeśli wybierzesz Node.js symulowanego urządzenia, które uruchomiono w poprzedniej sekcji, zostanie wyświetlony temperaturę, wilgotność i danych telemetrycznych dotyczących temperatury zewnętrznej:

![Dodaj temperatury zewnętrznej do pulpitu nawigacyjnego][image2]

Rozwiązania do monitorowania zdalnego automatycznie wykrywa typ telemetrii dodatkowe temperatury zewnętrznej i dodaje go do wykresów na pulpicie nawigacyjnym.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png