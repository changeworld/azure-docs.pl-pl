## <a name="create-a-device-identity"></a>Tworzenie tożsamości urządzenia

W tej sekcji użyjesz narzędzia środowiska Node.js, o nazwie [narzędzia iothub-explorer] [ iot-hub-explorer] do tworzenie tożsamości urządzenia w ramach tego samouczka. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

1. W środowisku wiersza polecenia, uruchom następujące polecenie:

    `npm install -g iothub-explorer@latest`

1. Następnie uruchom następujące polecenie, aby zalogować się do Centrum. Zastąp `{iot hub connection string}` skopiowany wcześniej parametrami połączenia Centrum IoT:

    `iothub-explorer login "{iot hub connection string}"`

1. Na koniec Utwórz nową tożsamość urządzenia o nazwie `myDeviceId` przy użyciu polecenia:

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Zanotuj parametry połączenia urządzenia z wyników. Te parametry połączenia urządzenia jest używany przez aplikację urządzenia połączyć się z Centrum IoT Hub jako urządzenie.

![][img-identity]

Zapoznaj się [wprowadzenie do usługi IoT Hub] [ lnk-getstarted] programowo utworzyć tożsamości urządzeń.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/quickstart-send-telemetry-dotnet.md
