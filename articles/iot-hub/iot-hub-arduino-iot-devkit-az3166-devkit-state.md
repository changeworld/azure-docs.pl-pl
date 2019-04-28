---
title: Używanie platformy Azure bliźniaczych reprezentacji urządzeń do kontrolowania LED użytkownika zestawu deweloperskiego IoT Mxchip | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak monitorować stany Mxchip i kontrolować użytkownika LED bliźniaków urządzeń w usłudze Azure IoT Hub.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: e955d21132dda6caa137ad3b5de9d00ccf7ed1b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369852"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

W tym przykładzie służy do monitorowania stanów informacji i czujnik zestawu deweloperskiego IoT Mxchip sieci Wi-Fi i kontrolować kolor LED użytkownika przy użyciu usługi Azure IoT Hub bliźniaczych reprezentacji urządzeń.

## <a name="what-you-learn"></a>Omawiane zagadnienia

- Jak monitorować stany zestawu deweloperskiego IoT Mxchip czujnika.

- Jak kontrolować kolor diodą LED RGB DevKit za pomocą bliźniaczych reprezentacji urządzeń na platformie Azure.

## <a name="what-you-need"></a>Co jest potrzebne

- Konfigurowanie środowiska projektowego, postępując zgodnie z [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- W oknie terminalu GitBash (lub innego interfejsu wiersza polecenia usługi Git) wpisz następujące polecenia:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Aprowizacja usług platformy Azure

1. Kliknij przycisk **zadania** menu rozwijane w programie Visual Studio Code i wybierz pozycję **Uruchom zadanie...**   -  **aprowizowanie chmury**.

2. Postęp jest wyświetlana w obszarze **terminalu** karcie **powitalnej** panelu.

3. Po wyświetleniu monitu z komunikatem *subskrypcję, który chcesz wybrać*, wybierz subskrypcję.

4. Zaznacz lub wybierz grupę zasobów. 
 
   > [!NOTE]
   > Jeśli masz już bezpłatna usługi IoT Hub, możesz pominąć ten krok.

5. Po wyświetleniu monitu z komunikatem *Centrum IoT, które chcesz wybrać*wybierz lub Utwórz Centrum IoT Hub.

6. Podobnie jak *aplikacji funkcji: Nazwa aplikacji funkcji: xxx*, jest wyświetlany. Zanotuj nazwę aplikacji funkcji; będzie używany w kolejnym kroku.

7. Poczekaj, aż usługi Azure Resource Manager szablon Zakończenie wdrożenia, który jest wskazane, gdy komunikat *wdrożenia szablonu usługi Resource Manager: Gotowe* jest wyświetlana.

## <a name="deploy-function-app"></a>Wdrażanie aplikacji — funkcja

1. Kliknij przycisk **zadania** menu rozwijane w programie Visual Studio Code i wybierz pozycję **Uruchom zadanie...**   -  **— wdrażanie w chmurze**.

2. Poczekaj, aż proces, aby zakończyć; przekazywania kodu aplikacji — funkcja komunikat *wdraża aplikację funkcji: Gotowe* jest wyświetlana.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurowanie parametrów połączenia urządzenia usługi IoT Hub w Mxchip

1. Podłącz swoje zestawu deweloperskiego IoT Mxchip do komputera.

2. Kliknij przycisk **zadania** menu rozwijane w programie Visual Studio Code i wybierz pozycję **Uruchom zadanie...**   -  **config-— połączenie z urządzeniem**

3. Na zestawu deweloperskiego IoT Mxchip, naciśnij i przytrzymaj klawisz przycisku **A**, naciśnij klawisz **resetowania** przycisk, a następnie przycisk **A** się DekKit tryb konfiguracji.

4. Poczekaj, aż proces konfiguracji ciągu połączenia, należy wykonać.

## <a name="upload-arduino-code-to-devkit"></a>Przekaż kod Arduino do Mxchip

Za pomocą Twojego zestawu deweloperskiego IoT Mxchip podłączone do komputera:

1. Kliknij przycisk **zadania** menu rozwijane w programie Visual Studio Code i wybierz pozycję **uruchomienia zadania kompilacji...** Szkic Arduino kompilacji i przekazywane do Mxchip.

2. Gdy szkicu został przekazany pomyślnie, *tworzenie i przekazywanie szkic: Sukces* zostanie wyświetlony komunikat.

## <a name="monitor-devkit-state-in-browser"></a>Monitor stanu Mxchip w przeglądarce

1. Otwórz w przeglądarce sieci Web `DevKitState\web\index.html` pliku--, który został utworzony podczas co należy kroku.

2. Zostanie wyświetlona następująca strona sieci Web:![Określ nazwę aplikacji funkcji.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Wprowadź nazwę aplikacji funkcji, którą napisany w dół wcześniej.

4. Kliknij przycisk **Connect** przycisku

5. W ciągu kilku sekund strona zostanie odświeżona i wyświetla stan połączenia sieci Wi-Fi Mxchip i stan każdego z czujników dołączania.

## <a name="control-the-devkits-user-led"></a>Kontrolowanie użytkowników Mxchip LED

1. Kliknij grafikę LED użytkownika na ilustracji strony sieci Web.

2. W ciągu kilku sekund na ekranie odświeża i wskazuje bieżący stan kolor LED użytkownika.

3. Spróbuj zmienić wartość koloru diody LED RGB, klikając przycisk w różnych miejscach na RGB kontrolki suwaka.

## <a name="example-operation"></a>Przykład operacji

![Przykład testu procedury](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Możesz wyświetlić nieprzetworzone dane bliźniaczej reprezentacji urządzenia w witrynie Azure portal: Usługa IoT Hub —\> urządzenia IoT —\> *\<urządzenia\>*  - \> bliźniaczej reprezentacji urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Wiesz już, jak:
- Podłącz urządzenie z systemem zestawu deweloperskiego IoT Mxchip, aby Twój akcelerator rozwiązań Azure IoT zdalnego monitorowania.
- Funkcja bliźniaczych reprezentacji urządzeń Azure IoT umożliwia sens i kontrolować kolor Mxchip diodą LED RGB.

Oto zalecane kolejne kroki:

* [Omówienie usługi Azure akcelerator rozwiązań IoT zdalnego monitorowania](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
