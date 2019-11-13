---
title: Połącz Raspberry Pi Web symulator z platformą Azure IoT Hub (Node. js)
description: Połącz Raspberry Pi Web symulator z platformą Azure IoT Hub Raspberry Pi, aby wysyłać dane do chmury platformy Azure.
author: wesmc7777
manager: philmea
keywords: Raspberry Pi symulatora, Azure IoT Raspberry Pi, Raspberry Pi IoT Hub, Raspberry Pi wysyła dane do chmury, Raspberry Pi do chmury
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954527"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Połącz symulator online Raspberry Pi z platformą Azure IoT Hub (Node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku rozpocznie się zapoznanie się z podstawą pracy z symulatorem online Raspberry Pi. Następnie dowiesz się, jak bezproblemowo połączyć symulatora pi z chmurą przy użyciu [usługi Azure IoT Hub](about-iot-hub.md).

Jeśli masz urządzenia fizyczne, odwiedź stronę [Connect Raspberry Pi do platformy IoT Hub Azure](iot-hub-raspberry-pi-kit-node-get-started.md) , aby rozpocząć pracę.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>Co robisz

* Poznaj podstawy symulatora online Raspberry Pi.

* Utwórz centrum IoT.

* Zarejestruj urządzenie dla platformy Pi w centrum IoT.

* Uruchom przykładową aplikację w języku Pi, aby wysyłać symulowane dane czujników do centrum IoT Hub.

Połącz symulowane Raspberry Pi z tworzonym Centrum IoT. Następnie uruchamiasz przykładową aplikację z symulatorem, aby generować dane czujnika. Na koniec wysyłasz dane czujnika do centrum IoT Hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć usługę Azure IoT Hub i uzyskać nowe parametry połączenia z urządzeniem. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu zaledwie kilku minut.

* Jak korzystać z symulatora online Raspberry Pi.

* Jak wysyłać dane czujnika do centrum IoT Hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Omówienie symulatora sieci Web Raspberry Pi

Kliknij przycisk, aby uruchomić symulator online Raspberry Pi.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Uruchom symulator Raspberry Pi</a>

W symulatorze sieci Web znajdują się trzy obszary.

1. Obszar zestawu — domyślnym obwodem jest to, że pi nawiązuje połączenie z czujnikiem BME280 i diodą LED. Obszar jest zablokowany w wersji zapoznawczej, dlatego nie można wykonać dostosowywania.

2. Obszar kodowania — Edytor kodu online służący do kodu z Raspberry Pi. Domyślna aplikacja Przykładowa pomaga zbierać dane czujników z czujnika BME280 i wysyłać je do IoT Hub platformy Azure. Aplikacja jest w pełni zgodna z rzeczywistymi urządzeniami pi. 

3. Zintegrowane okno konsoli — pokazuje dane wyjściowe kodu. W górnej części tego okna istnieją trzy przyciski.

   * **Uruchom** aplikację w obszarze Kodowanie.

   * **Zresetuj** — Zresetuj obszar kodowania do domyślnej aplikacji przykładowej.

   * **Zagnij/rozwiń** — po prawej stronie jest dostępny przycisk umożliwiający złożenie/rozwinięcie okna konsoli.

> [!NOTE]
> Symulator sieci Web Raspberry Pi jest teraz dostępny w wersji zapoznawczej. Chcemy słyszeć Twój głos w [warunkom chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Kod źródłowy jest publiczny w serwisie [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Omówienie symulatora online](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Uruchamianie przykładowej aplikacji na symulatorze sieci Web PI

1. Upewnij się, że pracujesz na domyślnej aplikacji przykładowej w obszarze kodowania. Zastąp symbol zastępczy w wierszu 15 ciągiem połączenia urządzenia usługi Azure IoT Hub.
1. 
   ![Zastąp parametry połączenia urządzenia](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Wybierz pozycję **Uruchom** lub wpisz `npm start`, aby uruchomić aplikację.

Powinny pojawić się następujące dane wyjściowe pokazujące dane czujnika i komunikaty wysyłane do centrum IoT Hub ![dane czujnika danych wyjściowych wysyłane z Raspberry Pi do centrum IoT Hub](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Odczytywanie komunikatów odebranych przez centrum

Jednym ze sposobów monitorowania komunikatów odebranych przez Centrum IoT Hub z symulowanego urządzenia jest użycie narzędzi Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Korzystanie z narzędzi Azure IoT Tools for Visual Studio Code w celu wysyłania i odbierania komunikatów między urządzeniem i IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Aby uzyskać więcej sposobów przetwarzania danych wysyłanych przez urządzenie, przejdź do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

Uruchomiono przykładową aplikację, która umożliwia zbieranie danych z czujnika i wysyłanie ich do centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
