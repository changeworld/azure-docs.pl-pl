---
title: Symulowane Pi malina do chmury (Node.js) - symulatora sieci web Pi malina Połącz z Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Symulator web Pi malina nawiązać połączenia z Centrum IoT Azure pi malina do wysyłania danych do chmury platformy Azure.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timtl
tags: ''
keywords: Symulator malinowe pi, pi azure iot malinowe, Centrum iot malinowe pi, pi malinowe wysyłania danych do chmury, malinowe pi do chmury
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: fe953b56e0a4dbbf7aaaa81d5b8ac6e31bb6bc4d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Symulator online Pi malina nawiązać połączenia z Centrum IoT Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku Rozpocznij od uczenia podstawy pracy z Pi malina symulatora online. Następnie Dowiedz się jak bezproblemowo połączyć symulatora Pi do chmury przy użyciu [Centrum IoT Azure](iot-hub-what-is-iot-hub.md). 

Jeśli masz urządzenia fizyczne, odwiedź stronę [połączyć Pi malina z Centrum IoT Azure](iot-hub-raspberry-pi-kit-node-get-started.md) rozpocząć pracę. 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3_banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6_button_default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5_button_click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6_button_default.png';">
</div>

## <a name="what-you-do"></a>Co zrobić

* Poznaj podstawy Pi malina symulatora online.
* Tworzenie Centrum IoT.
* Zarejestruj urządzenie pi w Centrum IoT.
* Uruchom przykładową aplikację na Pi, aby wysłać dane czujników symulowane do Centrum IoT.

Symulowane Pi malina nawiązać połączenia z Centrum IoT utworzony. Następnie uruchom przykładową aplikację z symulatorem do generowania danych czujnika. Ponadto użytkownik wysyła dane czujników do Centrum IoT.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Sposób tworzenia Centrum Azure IoT i uzyskać nowy ciąg połączenia urządzenia. Jeśli nie masz konta platformy Azure, [utworzyć bezpłatne konto próbne Azure](https://azure.microsoft.com/free/) za kilka minut.
* Jak pracować z Pi malina symulatora online.
* Jak wysyłać dane czujników do Centrum IoT.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Omówienie symulatora web Pi malina

Kliknij przycisk, aby uruchomić symulatora online malina Pi.

> [!div class="button"]
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Uruchomić symulatora malinowe Pi</a>

Istnieją trzy obszary w symulatorze sieci web.
1. Obszar zestawu — obwodu domyślny jest czy Pi nawiązanie połączenia z czujnika BME280 i LED. Obszar jest zablokowana w wersji zapoznawczej tak aktualnie nie może wykonać dostosowania.
2. Kodowanie obszar — edytora online kodu dla Ciebie do kodu z malina Pi. Domyślne przykładowej aplikacji umożliwia zbieranie danych czujnika z czujnika BME280 i wysyła do Centrum IoT Azure. Aplikacja jest w pełni zgodne z rzeczywistym Pi. 
3. Okno konsoli zintegrowane - zawiera dane wyjściowe w kodzie. U góry tego okna istnieją trzy przyciski.
   * **Uruchom** — uruchamianie aplikacji w obszarze kodowania.
   * **Resetuj** — Resetuj kodowania obszaru do aplikacji przykładowej domyślne.
   * **Składanie/rozszerzanie** — po prawej stronie jest przycisk umożliwiające składanie/Rozwiń okno konsoli.

> [!NOTE] 
Symulator web Pi malina jest teraz dostępna w wersji zapoznawczej. Chcielibyśmy usłyszeć głosu w [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Kod źródłowy jest publiczny na [Github](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Omówienie Pi symulatora online](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Uruchom przykładową aplikację w symulatorze web Pi

1. W kodowania obszaru, upewnij się, że pracujesz nad domyślne przykładowej aplikacji. Zastąp symbol zastępczy w wierszu 15 ciąg połączenia urządzenia Centrum Azure IoT.
   ![Zastąp ciąg połączenia urządzenia](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Kliknij przycisk **Uruchom** lub typ `npm start` do uruchomienia aplikacji.


Powinny pojawić się następujące dane wyjściowe, który zawiera dane czujników i komunikaty, które są wysyłane do Centrum IoT ![dane wyjściowe — dane czujników wysłanych z Pi malina Centrum IoT](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>Kolejne kroki

Uruchomiono przykładowej aplikacji do zbierania danych czujników i wysyłania go do Centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
