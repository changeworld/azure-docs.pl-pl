---
title: Symulowane urządzenia Raspberry Pi w chmurze (Node.js) - łączenie urządzenia Raspberry Pi symulator internetowy dla usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Symulator internetowy urządzenia Raspberry Pi nawiązać połączenia usługi Azure IoT Hub dla urządzenia Raspberry Pi w celu wysyłania danych do chmury platformy Azure.
author: wesmc7777
manager: philmea
keywords: Symulator urządzenia raspberry pi, usługa azure iot urządzenia raspberry pi, usługi iot hub dla urządzenia raspberry pi, urządzenia raspberry pi wysyłania danych do chmury, urządzenia raspberry pi w chmurze
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 9af2f0860c415ddb701e24ed8a698fae36d42e1f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838702"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Łączenie urządzenia Raspberry Pi symulatora w trybie online dla usługi Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku rozpoczniesz od nauki podstawy pracy z urządzeniem Raspberry Pi symulatora w trybie online. Następnie dowiesz się, jak połączyć bezproblemowo symulator Pi do chmury przy użyciu [usługi Azure IoT Hub](about-iot-hub.md).

W przypadku urządzeń fizycznych, odwiedź witrynę [łączenie urządzenia Raspberry Pi dla usługi Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) na rozpoczęcie pracy.

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

## <a name="what-you-do"></a>Co należy zrobić

* Poznaj podstawy symulatora urządzenia Raspberry Pi w trybie online.

* Utwórz centrum IoT.

* Zarejestruj urządzenie Pi w usłudze IoT hub.

* Uruchom przykładową aplikację na Pi do wysyłania danych z symulowanych czujników z Centrum IoT.

Połączyć symulowane urządzenia Raspberry Pi do usługi IoT hub, którą tworzysz. Następnie uruchom przykładową aplikację z symulatorem do generowania danych z czujników. Na koniec wysyłania danych czujnika do usługi IoT hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć Centrum Azure IoT hub i uzyskać nowe parametry połączenia urządzenia. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu kilku minut.

* Jak pracować z urządzeniem Raspberry Pi symulatora w trybie online.

* Jak wysyłać danych czujnika do usługi IoT hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Omówienie symulator internetowy urządzenia Raspberry Pi

Kliknij przycisk, który można uruchomić symulatora urządzenia Raspberry Pi w trybie online.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Uruchom symulator urządzenia Raspberry Pi</a>

Istnieją trzy obszary, w symulatorze sieci web.

1. Obszar zestawu — obwód domyślny jest, że Pi łączy się z czujnika BME280 i DIODĘ. Obszar jest zablokowana w wersji zapoznawczej więc obecnie nie można wykonywać dostosowywania.

2. Kodowanie obszar — kod online Edytor kodu, korzystając z urządzeniem Raspberry Pi. Przykładowa aplikacja domyślna ułatwia zbieranie danych z czujników z czujnika BME280 i wysyła do usługi Azure IoT Hub. Aplikacja jest w pełni zgodne z rzeczywistych urządzeń Pi. 

3. Zintegrowana Konsola okna — pokazuje dane wyjściowe kodu. W górnej części tego okna istnieją trzy przyciski.

   * **Uruchom** — Uruchom aplikację w obszarze kodowania.

   * **Resetuj** — resetowanie kodowania obszaru do domyślnej aplikacji przykładowej.

   * **Rozwijania/zwijania** — z prawej strony znajduje się przycisk umożliwia zwijania/Rozwiń okno konsoli.

> [!NOTE]
> Symulator internetowy urządzenia Raspberry Pi jest teraz dostępna w wersji zapoznawczej. Chcielibyśmy poznać swój głos w [pokoju dotyczącym oprogramowania Gitter](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Kod źródłowy jest publicznie w witrynie [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Omówienie Pi symulatora w trybie online](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Uruchamianie przykładowej aplikacji w symulatorze web Pi

1. Kodu obszaru, upewnij się, że pracujesz nad domyślne przykładowej aplikacji. Zastąp symbol zastępczy w wierszu 15 parametrami połączenia usługi Azure IoT hub urządzenia.
1. 
   ![Zastąp parametry połączenia urządzenia](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Wybierz **Uruchom** lub typ `npm start` do uruchomienia aplikacji.

Następujące dane wyjściowe, dane czujników i komunikaty, które są wysyłane do usługi IoT hub powinny być ![dane wyjściowe — dane czujników wysyłanych z urządzenia Raspberry Pi do Centrum IoT hub](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Przeczytaj komunikaty odbierane przez Centrum

Jednym ze sposobów monitorowania komunikaty odbierane przez Centrum IoT hub z symulowanego urządzenia jest konieczne użycie narzędzi IoT platformy Azure dla programu Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Użyj narzędzia IoT platformy Azure dla programu Visual Studio Code do wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Więcej sposobów na przetwarzanie danych przesyłanych przez urządzenia przejdź do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

Po uruchomieniu aplikacji przykładowej, zbieranie danych z czujników i wysyłać je do Centrum IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
