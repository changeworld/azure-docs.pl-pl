---
title: ESP8266 do chmury — łączenie pióra HUZZAH ESP8266 z platformą Azure IoT Hub | Microsoft Docs
description: Dowiedz się, jak skonfigurować i połączyć Adafruit Wtapianie HUZZAH ESP8266 z platformą Azure IoT Hub, aby wysłać dane do platformy Azure w chmurze w tym samouczku.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 8e97a979c37af8ade51b4ff6ca4b2c5b4eec126e
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68232724"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Połącz Adafruit Wtapianie HUZZAH ESP8266 z platformą Azure IoT Hub w chmurze

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Połączenie między DHT22, Wtapianie HUZZAH ESP8266 i IoT Hub](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Co robisz

Połącz Adafruit Wtapianie HUZZAH ESP8266 z tworzonym Centrum IoT Hub. Następnie uruchom przykładową aplikację na ESP8266, aby zebrać dane temperatury i wilgotności z czujnika DHT22. Na koniec wysyłasz dane czujnika do centrum IoT Hub.

> [!NOTE]
> Jeśli używasz innych tablic ESP8266ych, nadal możesz wykonać poniższe kroki, aby połączyć je z Centrum IoT Hub. W zależności od używanej tablicy ESP8266 może być konieczne ponowne skonfigurowanie `LED_PIN`usługi. Na przykład jeśli korzystasz z ESP8266 z usługi AI, możesz zmienić ją z `0` na. `2` Nie masz jeszcze zestawu? Pobierz ją z [witryny sieci Web systemu Azure](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć Centrum IoT Hub i zarejestrować urządzenie na potrzeby wtapiania HUZZAH ESP8266
* Jak połączyć Wtapianie HUZZAH ESP8266 z czujnikiem i komputerem
* Jak zbierać dane czujników przez uruchomienie przykładowej aplikacji na Wtapianie HUZZAH ESP8266
* Jak wysłać dane czujnika do centrum IoT Hub

## <a name="what-you-need"></a>Co jest potrzebne

![Części wymagające samouczka](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Aby ukończyć tę operację, potrzebne są następujące części z programu Wtapianie HUZZAH ESP8266 Start Kit:

* Tablica Wtapianie HUZZAH ESP8266
* MikroUSB do wpisywania kabla USB

Potrzebne są również następujące elementy w środowisku deweloperskim:

* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu zaledwie kilku minut.
* Komputer Mac lub na komputerze z systemem Windows lub Ubuntu.
* [NARZĘDZIA](https://git-scm.com/download)
* [Arduino](https://www.arduino.cc/en/main/software#download)
* [Sterownik Adafruit Huzzah ESP8266 dla USB](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide)
* Sieć bezprzewodowa dla wtapiania HUZZAH ESP8266, z którą można nawiązać połączenie.
* Połączenie internetowe umożliwiające pobranie narzędzia konfiguracji.
* [Visual Studio Code rozszerzenie Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Wersja Arduino IDE używana przez rozszerzenie Visual Studio Code dla Arduino musi mieć wersję 1.6.8 lub nowszą. Wcześniejsze wersje nie współpracują z biblioteką AzureIoT.

Następujące elementy są opcjonalne na wypadek braku czujnika. Dostępna jest również opcja użycia symulowanych danych czujników.

* Adafruit DHT22 temperatury i czujnika wilgotności
* Breadboard
* Przewody skoku m/M

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Połącz Wtapianie HUZZAH ESP8266 z czujnikiem i komputerem

W tej sekcji połączysz czujniki z tablicą. Następnie podłącz urządzenie do komputera w celu dodatkowego użycia.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Łączenie temperatury DHT22 i czujnika wilgotności z wtapianiem HUZZAH ESP8266

Użyj przewodów breadboard i zworki, aby nawiązać połączenie w następujący sposób. Jeśli nie masz czujnika, Pomiń tę sekcję, ponieważ zamiast niej możesz użyć symulowanych danych czujników.

![Informacje o połączeniach](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

W przypadku numerów PIN czujnika Użyj następujących okablowania:

| Uruchom (czujnik)           | End (tablica)            | Kolor kabla   |
| -----------------------  | ---------------------- | ------------  |
| VDD (Przypnij 31F)            | 3V (Przypnij 58H)           | Czerwony kabel     |
| DANE (Przypnij 32F)           | GPIO 2 (Przypinanie 46A)       | Niebieski kabel    |
| GND (Przypnij 34F)            | GND (Przypnij 56I)          | Czarny kabel   |

Aby uzyskać więcej informacji, zobacz [ADAFRUIT DHT22 sensor Setup](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) and [Adafruit Wtapianie HUZZAH Esp8266 Pinouts](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Teraz Twoje Wtapianie Huzzah ESP8266 powinno być połączone z czujnikiem działającym.

![Połącz DHT22 z piórem Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Połącz Wtapianie HUZZAH ESP8266 z komputerem

Jak pokazano poniżej, użyj mikroUSB do wpisania kabla USB, aby połączyć Wtapianie HUZZAH ESP8266 z komputerem.

![Połącz Wtapianie Huzzah z komputerem](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Dodawanie uprawnień portu szeregowego (tylko Ubuntu)

Jeśli używasz Ubuntu, upewnij się, że masz uprawnienia do działania na porcie USB wtapiania HUZZAH ESP8266. Aby dodać uprawnienia portu szeregowego, wykonaj następujące czynności:

1. Uruchom następujące polecenia w terminalu:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Uzyskasz jeden z następujących danych wyjściowych:

   * CRW-RW----1 root UUCP XXXXXXXX
   * CRW-RW----1 root dialout XXXXXXXX

   W danych wyjściowych Zwróć uwagę `uucp` , `dialout` że lub jest nazwą właściciela portu USB.

2. Dodaj użytkownika do grupy, uruchamiając następujące polecenie:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>`jest nazwą właściciela grupy uzyskaną w poprzednim kroku. `<username>`jest nazwą użytkownika Ubuntu.

3. Wyloguj się z usługi Ubuntu, a następnie zaloguj się ponownie, aby zmienić.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Zbieranie danych z czujnika i wysyłanie ich do centrum IoT Hub

W tej sekcji wdrażasz i uruchamiasz przykładową aplikację w programie Wtapianie HUZZAH ESP8266. Aplikacja Przykładowa miga diody LED na Wtapianie HUZZAH ESP8266 i wysyła dane dotyczące temperatury i wilgotności zebrane z czujnika DHT22 do centrum IoT Hub.

### <a name="get-the-sample-application-from-github"></a>Pobieranie przykładowej aplikacji z usługi GitHub

Przykładowa aplikacja jest hostowana w serwisie GitHub. Sklonuj przykładowe repozytorium zawierające przykładową aplikację z usługi GitHub. Aby sklonować przykładowe repozytorium, wykonaj następujące kroki:

1. Otwórz wiersz polecenia lub okno terminalu.

2. Przejdź do folderu, w którym ma być przechowywana Przykładowa aplikacja.

3. Uruchom następujące polecenie:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Następnie zainstaluj pakiet dla wtapiania HUZZAH ESP8266 w Visual Studio Code.

4. Otwórz folder, w którym jest przechowywana Przykładowa aplikacja.

5. Otwórz plik App. oknach w folderze App w Visual Studio Code.

   ![Otwórz przykładową aplikację w Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. W Visual Studio Code wprowadź `F1`wartość.

7. Wpisz **Arduino** i wybierz **Arduino: Menedżer**tablicy.

8. Na karcie **Arduino Board Manager** kliknij przycisk **dodatkowych adresów URL**.

   ![VS Code Arduino Board Manager](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. W oknie **Ustawienia użytkownika** skopiuj i wklej następujące elementy na końcu pliku

   ```json
   "arduino.additionalUrls": "https://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Skonfiguruj adres URL pakietu Arduino w VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Zapisz plik i Zamknij kartę **Ustawienia użytkownika** .

11. Kliknij przycisk **Odśwież indeksy pakietu**. Po zakończeniu odświeżania Wyszukaj pozycję **ESP8266**.

12. Kliknij przycisk **Instaluj** dla ESP8266.

    Menedżer tablic wskazuje, że zainstalowano ESP8266 z wersją 2.2.0 lub nowszą.

    ![Pakiet ESP8266 jest zainstalowany](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Wprowadź `F1`polecenie, a  następnie wpisz Arduino **i wybierz Arduino: Konfiguracja**tablicy.

14. Kliknij pole wyboru dla **wybranej tablicy:** i wpisz **ESP8266**, a następnie wybierz pozycję **Adafruit HUZZAH ESP8266 (ESP8266)** .

    ![Wybierz tablicę ESP8266](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Instalowanie niezbędnych bibliotek

1. W Visual Studio Code wprowadź `F1`wartość, a następnie wpisz **Arduino** i wybierz **Arduino: Menedżer**biblioteki.

2. Wyszukaj następujące nazwy bibliotek po jednej. Dla każdej znalezionej biblioteki kliknij przycisk **Instaluj**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nie masz rzeczywistego czujnika DHT22?

Przykładowa aplikacja może symulować dane temperatury i wilgotności w przypadku, gdy nie masz rzeczywistego czujnika DHT22. Aby skonfigurować przykładową aplikację do korzystania z symulowanych danych, wykonaj następujące kroki:

1. `config.h` Otwórz plik`app` w folderze.

2. Znajdź następujący wiersz kodu i zmień wartość z `false` na: `true`

   ```c
   define SIMULATED_DATA true
   ```

   ![Konfigurowanie przykładowej aplikacji do korzystania z symulowanych danych](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Zapisz plik.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Wdróż przykładową aplikację, aby przewtapianie HUZZAH ESP8266

1. W Visual Studio Code kliknij  **\<pozycję Wybierz port szeregowy >** na pasku stanu, a następnie kliknij port szeregowy dla opcji Wtapianie HUZZAH ESP8266.

2. Wprowadź `F1`polecenie, a  następnie wpisz Arduino **i wybierz Arduino: Przekaż** , aby skompilować i wdrożyć przykładową aplikację do Wtapianie HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Wprowadź swoje poświadczenia

Po pomyślnym ukończeniu przekazywania wykonaj następujące kroki, aby wprowadzić swoje poświadczenia:

1. Otwórz Arduino IDE, kliknij przycisk **Narzędzia** > **monitor szeregowy**.

2. W oknie monitora szeregowego Zwróć uwagę na dwie listy rozwijane w prawym dolnym rogu.

3. Nie zaznaczaj **końca linii** dla lewej listy rozwijanej.

4. Wybierz **115200 bodów** dla prawej listy rozwijanej.

5. W polu wejściowym znajdującym się u góry okna monitora szeregowego wprowadź następujące informacje, jeśli zostanie wyświetlony monit o ich podanie, a następnie kliknij pozycję **Wyślij**.

   * Wi-Fi SSID
   * Hasło Wi-Fi
   * Parametry połączenia urządzenia

> [!Note]
> Informacje o poświadczeniu są przechowywane w pamięci poddanej Wtapianie HUZZAH ESP8266. Jeśli klikniesz przycisk Resetuj na tablicy Wtapianie HUZZAH ESP8266, aplikacja Przykładowa pyta, czy chcesz wymazać informacje. Wprowadź `Y` , aby zostały wymazane informacje. Zostanie wyświetlony monit o podanie informacji po raz drugi.

### <a name="verify-the-sample-application-is-running-successfully"></a>Weryfikowanie, czy aplikacja Przykładowa działa prawidłowo

Jeśli zobaczysz następujące dane wyjściowe z okna monitora szeregowego i miganie diody LED na pasku wtapiania HUZZAH ESP8266, aplikacja Przykładowa zostanie uruchomiona pomyślnie.

![Końcowe dane wyjściowe w Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Odczytywanie komunikatów odebranych przez centrum

Jednym ze sposobów monitorowania komunikatów odebranych przez Centrum IoT Hub z urządzenia jest użycie narzędzi Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Korzystanie z narzędzi Azure IoT Tools for Visual Studio Code w celu wysyłania i odbierania komunikatów między urządzeniem i IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Aby uzyskać więcej sposobów przetwarzania danych wysyłanych przez urządzenie, przejdź do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

Pomyślnie połączono Wtapianie HUZZAH ESP8266 z Centrum IoT Hub i wysłano przechwycone dane czujnika do centrum IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]