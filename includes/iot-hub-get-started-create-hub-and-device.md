## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Teraz, po utworzeniu Centrum IoT, zlokalizuj ważne informacje, które są używane do łączenia urządzeń i aplikacji do Centrum IoT. 

1. Po utworzeniu centrum IoT Hub kliknij je na pulpicie nawigacyjnym. Zanotuj wartość z pola **Nazwa hosta**, a następnie kliknij polecenie **Zasady dostępu współużytkowanego**.

   ![Pobieranie nazwy hosta centrum IoT Hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. W okienku **Zasady dostępu współużytkowanego** kliknij zasady **iothubowner**, a następnie skopiuj i zanotuj **Parametry połączenia** centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu do centrum IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

> [!NOTE] 
W przypadku tego samouczka konfiguracji te parametry połączenia iothubowner nie będą potrzebne. Niemniej mogą być potrzebne w ramach innych samouczków dotyczących innych scenariuszy IoT po zakończeniu tej konfiguracji.

   ![Pobieranie parametrów połączenia centrum IoT Hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Rejestracja urządzenia w centrum IoT Hub dla Twojego urządzenia

1. W witrynie [Azure Portal](https://portal.azure.com/) otwórz swoje centrum IoT Hub.

2. Kliknij przycisk **urządzenia IoT**.
3. W okienku urządzenia IoT kliknij **Dodaj** do dodania urządzenia do Centrum IoT. Następnie wykonaj poniższe czynności:

   **Identyfikator urządzenia**: wprowadź identyfikator nowego urządzenia. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

   **Typ uwierzytelniania**: wybierz pozycję **Klucz symetryczny**.

   **Automatyczne generowanie kluczy**: zaznacz to pole wyboru.

   **Połącz urządzenie z IoT Hub**: kliknij pozycję **Włącz**.

   ![Dodaj urządzenie urządzenia IoT z Centrum IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-iot-devices-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Kliknij pozycję **Zapisz**.
5. Po utworzeniu urządzenia, należy otworzyć urządzenia w **urządzenia IoT** okienka.

   ![Lista urządzeń IoT w Centrum IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_device-list-in-iot-devices-portal.png)

6. Zanotuj klucz podstawowy parametrów połączenia.

   ![Pobieranie parametrów połączenia urządzenia](../articles/iot-hub/media/iot-hub-create-hub-and-device/8_get-device-connection-string-in-iot-devices-portal.png)
