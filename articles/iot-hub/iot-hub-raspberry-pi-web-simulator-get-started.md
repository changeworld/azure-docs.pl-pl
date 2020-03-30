---
title: Podłącz symulator sieci Web Raspberry Pi do usługi Azure IoT Hub (node.js)
description: Połącz symulator sieci Web Raspberry Pi z usługą Azure IoT Hub dla Raspberry Pi, aby wysyłać dane do chmury platformy Azure.
author: wesmc7777
manager: philmea
keywords: symulator raspberry pi, azure iot raspberry pi, raspberry pi iot hub, raspberry pi wysyłają dane do chmury, raspberry pi do chmury
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954527"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Podłącz symulator online Raspberry Pi z usługą Azure IoT Hub (node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku możesz rozpocząć od nauki podstaw pracy z symulatorem online Raspberry Pi. Następnie dowiesz się, jak bezproblemowo połączyć symulator Pi z chmurą przy użyciu [usługi Azure IoT Hub.](about-iot-hub.md)

Jeśli masz urządzenia fizyczne, odwiedź [witrynę Podłącz Raspberry Pi do usługi Azure IoT Hub,](iot-hub-raspberry-pi-kit-node-get-started.md) aby rozpocząć.

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

* Zarejestruj urządzenie dla Pi w centrum IoT hub.

* Uruchom przykładową aplikację na Pi, aby wysłać symulowane dane czujnika do centrum IoT hub.

Połącz symulowane Raspberry Pi z utworzonym centrum IoT Hub. Następnie uruchom przykładową aplikację z symulatorem do generowania danych z czujnika. Na koniec wysyłasz dane czujnika do centrum IoT Hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć centrum Usługi Azure IoT i uzyskać nowy ciąg połączenia urządzenia. Jeśli nie masz konta platformy Azure, [utwórz bezpłatne konto próbne platformy Azure](https://azure.microsoft.com/free/) w ciągu zaledwie kilku minut.

* Jak pracować z symulatorem online Raspberry Pi.

* Jak wysyłać dane z czujników do centrum IoT Hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Przegląd symulatora sieci Web Raspberry Pi

Kliknij przycisk, aby uruchomić symulator online Raspberry Pi.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Uruchom Raspberry Pi Simulator</a>

Istnieją trzy obszary w symulatorze www.

1. Obszar montażu — domyślny obwód to urządzenie Pi połączone z czujnikiem BME280 i diodą LED. Obszar ten jest zablokowany w wersji zapoznawczej więc obecnie nie można go dostosowywać.

2. Obszar kodowania — edytor kodu online umożliwiający kodowanie, korzystając z urządzenia Raspberry Pi. Przykładowa aplikacja domyślna ułatwia zbieranie danych z czujnika BME280 i wysyłanie do Twojej usługi Azure IoT Hub. Aplikacja jest w pełni zgodna z rzeczywistymi urządzeniami Pi. 

3. Zintegrowane okno konsoli — pokazuje dane wyjściowe kodu. W górnej części tego okna znajdują się trzy przyciski.

   * **Uruchom** — uruchamia aplikację w obszarze kodowania.

   * **Resetuj** — resetuje obszar kodowania do domyślnej aplikacji przykładowej.

   * **Zwiń/rozwiń** — z prawej strony znajduje się przycisk umożliwiający zwijanie/rozwijanie okna konsoli.

> [!NOTE]
> Symulator sieci Web Raspberry Pi jest teraz dostępny w wersji zapoznawczej. Chcielibyśmy usłyszeć Twój głos w [ChatroomIe Gitter.](https://gitter.im/Microsoft/raspberry-pi-web-simulator) Kod źródłowy jest publiczny w [usłudze GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Przegląd symulatora online Pi](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Uruchamianie przykładowej aplikacji w internetowym symulatorze urządzenia Pi

1. W obszarze kodowania upewnij się, że pracujesz na domyślnej przykładowej aplikacji. W wierszu 15. zastąp symbol zastępczy parametrami połączenia urządzenia centrum IoT na platformie Azure.
1. 
   ![Zastępowanie parametrów połączenia urządzenia](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Wybierz **uruchom** `npm start` lub wpisz, aby uruchomić aplikację.

Powinieneś zobaczyć następujące dane wyjściowe, które pokazują dane z czujnika ![i komunikaty wysyłane do koncentratora IoT Output — dane czujników wysyłane z Raspberry Pi do centrum IoT hub](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Czytanie wiadomości odebranych przez centrum

Jednym ze sposobów monitorowania wiadomości odebranych przez centrum IoT hub z symulowanego urządzenia jest użycie narzędzi Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Wysyłanie i odbieranie wiadomości między urządzeniem a centrum IoT Hub za pomocą narzędzi Azure IoT Tools for Visual Studio Code.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

Aby uzyskać więcej sposobów przetwarzania danych wysyłanych przez urządzenie, przejdź do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

Uruchomisz przykładową aplikację, aby zebrać dane z czujników i wysłać je do centrum IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
