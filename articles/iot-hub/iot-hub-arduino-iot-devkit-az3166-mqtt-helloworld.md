---
title: Wysyłanie komunikatów do serwera MQTT przy użyciu biblioteki klienta usługi Azure MQTT | Dokumentacja firmy Microsoft
description: Użyj Mxchip jako klienta do wysyłania komunikatów do serwera MQTT
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 60520f5a72fd7e27d4ea64ac76511a00a727426e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369348"
---
# <a name="send-messages-to-an-mqtt-server"></a>Wysyłanie komunikatów do serwera MQTT

Często systemów Internetu rzeczy (IoT) dotyczy sporadycznie, niska jakość lub wolne połączenia przez internet. MQTT jest machine-to-machine (M2M) łączności protokołu, który został utworzony przy użyciu tych wyzwań, należy pamiętać. 

MQTT biblioteki klienckiej używana w tym miejscu jest częścią [Eclipse Paho](https://www.eclipse.org/paho/) projektu, który udostępnia interfejsy API dotyczące korzystania z protokołu MQTT za pośrednictwem wielu środków transportu.

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym projekcie dowiesz się:
- Jak wysyłać komunikaty do brokera protokołu MQTT za pomocą biblioteki klienta protokołu MQTT.
- Jak skonfigurować Twojego zestawu deweloperskiego Iot Mxchip jako klient MQTT.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [przewodnik wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje Mxchip nawiązano połączenie z sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

## <a name="open-the-project-folder"></a>Otwórz folder projektu

1. Jeśli już Mxchip łączenie się z komputerem, odłączeniem go.

2. Uruchom program VS Code.

3. Podłącz Mxchip do komputera.

## <a name="open-the-mqttclient-sample"></a>Otwórz przykładową MQTTClient

Po lewej stronie rozwiń **przykłady ARDUINO** sekcji, przejdź do **przykłady zestawu DEWELOPERSKIEGO az3166 usługi > MQTT**i wybierz **MQTTClient**. Nowe okno programu VS Code zostanie otwarty z folderu projektu w nim.

> [!NOTE]
> Przykład można również otworzyć paletę poleceń. Użyj `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: Przykłady**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Tworzenie i przekazywanie szkic Arduino do Mxchip

Typ `Ctrl+P` (z systemem macOS: `Cmd+P`) do uruchamiania `task device-upload`. Po zakończeniu przekazywania Mxchip powoduje ponowne uruchomienie i uruchamia szkicu.

![przekazywanie urządzeń](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Może pojawić się "Błąd: AZ3166: Nieznany pakiet"komunikat o błędzie. Ten błąd występuje, gdy indeksu pakietów tablicy nie jest odświeżany poprawnie. Aby rozwiązać ten problem, zapoznaj się [sekcji Projektowanie Mxchip IoT często zadawanych pytań](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Projekt testowy

W programie VS Code należy wykonać tę procedurę, aby otworzyć i skonfigurować Serial Monitor:

1. Kliknij przycisk `COM[X]` word na pasku stanu, aby ustawiono właściwy port COM za pomocą `STMicroelectronics`: ![set-com port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kliknij ikonę wtyczki zasilania, na pasku stanu, aby otworzyć Monitor szeregowego: ![seryjny monitora](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na pasku stanu kliknij numer, który reprezentuje szybkość transmisji i ustaw go na `115200`: ![szybkości zestawu](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Serial Monitor Wyświetla wszystkie komunikaty wysyłane przez szkic próbki. Szkicu łączy Mxchip sieć Wi-Fi. Po pomyślnym połączenia Wi-Fi szkicu wysyła komunikat do brokera MQTT. Po tym próbki regularnie wysyła dwa komunikaty "iot.eclipse.org" przy użyciu ustawień QoS 0 i QoS 1, odpowiednio.

![dane wyjściowe seryjnych](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się [często zadawane pytania IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub połączyć się przy użyciu następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Zobacz także

* [Połącz DevKit az3166 usługi IoT dla usługi Azure IoT Hub w chmurze](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Potrząśnij, wstrząsnąć dla Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak skonfigurować Twojego zestawu deweloperskiego Iot Mxchip jako klient MQTT i wysyłać komunikaty do brokera protokołu MQTT za pomocą biblioteki klienta protokołu MQTT, Oto zalecane kolejne kroki:

* [Omówienie usługi Azure akcelerator rozwiązań IoT zdalnego monitorowania](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
