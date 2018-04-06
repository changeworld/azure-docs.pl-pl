---
title: Wysyłanie komunikatów do serwera MQTT za pomocą biblioteki klienta usługi Azure MQTT | Dokumentacja firmy Microsoft.
description: Wysyłać do serwera MQTT za pomocą zestaw deweloperski jako klienta.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 121bd8a21e74a836fcca74af97fc20cae297d9a2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="send-messages-to-an-mqtt-server"></a>Wysyłanie komunikatów do serwera MQTT

Systemy Internetu rzeczy (IoT) często biznesowych w radzeniu sobie z tymczasowymi, słabą jakość lub powolne połączenia z Internetem. MQTT to protokół łączności komputera (M2M), który został utworzony przy użyciu takich trudności w uwadze. 

MQTT biblioteki klienckiej używana w tym miejscu jest częścią [Eclipse Paho](http://www.eclipse.org/paho/) projektu, który udostępnia interfejsy API dla przy użyciu MQTT przez wiele środków transportu.

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym projekcie dowiesz się:
- Jak używać biblioteki klienta MQTT do wysyłania komunikatów do MQTT brokera.
- Jak skonfigurować zestaw deweloperski programu MXChip Iot jako klient MQTT.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) do:

* Twoje zestaw deweloperski ma podłączony do sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

## <a name="open-the-project-folder"></a>Otwórz folder projektu

1. Odłącz zestaw deweloperski z komputera, jeśli jest już połączony.

2. Uruchom kod programu VS.

3. Zestaw deweloperski można połączyć się z komputerem.
    Kod VS automatycznie wykryje Twoje zestaw deweloperski i otwiera następujące strony:
    * Strona wprowadzenia zestaw deweloperski.
    * Przykłady Arduino: Praktyczne przykłady ułatwiające rozpoczęcie pracy z Twojej zestaw deweloperski.

    ![Mini solution vscode](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/vscode-start.png)

## <a name="open-the-mqttclient-sample"></a>Otwórz przykładowe MQTTClient

Po lewej stronie rozwiń **przykłady ARDUINO** przejdź do **przykłady MXCHIP AZ3166 > MQTT**i wybierz **MQTTClient**. Otwiera nowe okno programu VS kodu z folderu projektu w nim.

![Przykłady folderu](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/examples.png)

> [!NOTE]
> Jeśli wystąpi aby zamknąć okienko, zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) do otwarcia palety polecenia, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Tworzenie i przekazywanie szkicu Arduino do zestaw deweloperski

Typ `Ctrl+P` (macOS: `Cmd+P`) do uruchamiania `task device-upload`. Po zakończeniu przekazywania zestaw deweloperski ponownego uruchomienia i uruchamia szkicu.

![przekazywanie urządzeń](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Może pojawić się "Błąd: AZ3166: Nieznany pakiet" komunikat o błędzie. Ten błąd występuje, gdy indeks tablicy pakietu nie jest odświeżany poprawnie. Aby rozwiązać ten problem, zapoznaj się to [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Projekt testowy

W kodzie VS wykonaj poniższą procedurę, aby otworzyć i skonfigurować Serial Monitor:

1. Kliknij przycisk `COM[X]` słowa na pasku stanu, aby ustawić prawy port COM z `STMicroelectronics`: ![set-com port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kliknij ikonę plug zasilania na pasku stanu, aby otworzyć Serial Monitor: ![seryjny monitora](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na pasku stanu, kliknij przycisk liczba, która reprezentuje szybkość transmisji i ustaw ją `115200`: ![szybkości zestawu](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Serial Monitor Wyświetla wszystkie wiadomości wysyłane przez schemat próbki. Szkicu łączy zestaw deweloperski sieci Wi-Fi. Po pomyślnym połączenia Wi-Fi szkicu wysyła komunikat do brokera MQTT. Po wykonaniu tej próbki wielokrotnie wysyła dwa komunikaty "iot.eclipse.org" odpowiednio przy użyciu ustawień QoS 0 i QoS 1.

![dane wyjściowe seryjny](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub łączyć się przy użyciu następujących kanałów:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Zobacz także

* [AZ3166 zestaw deweloperski IoT nawiązać połączenia z Centrum IoT Azure w chmurze]({{"/docs/getting-started/" | absolute_url }})
* [Potrząsanie, potrząsanie dla Tweet]({{"/docs/projects/shake-shake/" | absolute_url }})

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób konfigurowania programu zestaw deweloperski Iot MXChip jako klient MQTT i wysyłać do brokera MQTT za pomocą biblioteki klienta MQTT, Oto Sugerowane następne kroki:

* [Omówienie pakiet IoT Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestaw deweloperski IoT MXChip do aplikacji Microsoft IoT centralnego](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
