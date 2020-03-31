---
title: Użyj bliźniaczych urządzeń platformy Azure do sterowania diodami LED użytkownika MXChip IoT DevKit | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak monitorować stany DevKit i kontrolować diodę LED użytkownika za pomocą bliźniaczych urządzeń usługi Azure IoT Hub.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: deb1ea8c7b41ad48bddebfbed1b15c667ee0071a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73483944"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

W tym przykładzie można użyć do monitorowania MXChip IoT DevKit WiFi informacji i stanów czujników i kontrolować kolor diody LED użytkownika przy użyciu bliźniaczych urządzeń Usługi Azure IoT Hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

- Jak monitorować stany czujnika MXChip IoT DevKit.

- Jak używać bliźniaczych urządzeń platformy Azure do kontrolowania koloru diody LED RGB devkit.

## <a name="what-you-need"></a>Co jest potrzebne

- Skonfiguruj środowisko programistyczne, postępował zgodnie z [Przewodnikiem Wprowadzenie](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- W oknie terminala GitBash (lub innym interfejsie wiersza polecenia Git) wpisz następujące polecenia:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Aprowizuj usługi platformy Azure

1. Kliknij menu rozwijane **Zadania** w programie Visual Studio Code i wybierz pozycję **Uruchom zadanie...**  -  **cloud-provision**.

2. Twoje postępy są wyświetlane w zakładce **TERMINAL** w panelu **Powitanie.**

3. Po wyświetleniu monitu z komunikatem *Jaka subskrypcja chcesz wybrać*, wybierz subskrypcję.

4. Wybierz lub wybierz grupę zasobów. 
 
   > [!NOTE]
   > Jeśli masz już bezpłatne Centrum IoT, możesz pominąć ten krok.

5. Po wyświetleniu monitu z komunikatem *Co centrum IoT chcesz wybrać*, wybierz lub utwórz Centrum IoT Hub.

6. Coś podobnego do *funkcji aplikacji: nazwa aplikacji funkcji: xxx*, jest wyświetlany. Zapisz nazwę aplikacji funkcji; zostanie użyty w późniejszym kroku.

7. Poczekaj na zakończenie wdrażania szablonu usługi Azure Resource Manager, co jest wskazywane podczas wyświetlania komunikatu *Wdrożenia szablonu Menedżera zasobów: Gotowe.*

## <a name="deploy-function-app"></a>Wdrażanie aplikacji funkcji

1. Kliknij menu rozwijane **Zadania** w programie Visual Studio Code i wybierz pozycję **Uruchom zadanie...**  -  **cloud-deploy**.

2. Poczekaj na zakończenie procesu przekazywania kodu aplikacji funkcji; zostanie *wdrożona aplikacja funkcji wiadomości: Gotowe* jest wyświetlane.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurowanie ciągu połączenia urządzenia usługi IoT hub w zestawie DevKit

1. Podłącz urządzenie MXChip IoT DevKit do komputera.

2. Kliknij menu rozwijane **Zadania** w programie Visual Studio Code i wybierz pozycję **Uruchom zadanie...**  -  **połączenie z urządzeniem konfiguracowym**

3. Na MXChip IoT DevKit naciśnij i przytrzymaj przycisk **A**, naciśnij przycisk **Reset,** a następnie zwolnij przycisk **A,** aby DekKit wszedł w tryb konfiguracji.

4. Poczekaj na zakończenie procesu konfiguracji ciągu połączenia.

## <a name="upload-arduino-code-to-devkit"></a>Prześlij kod Arduino do DevKit

Po podłączeniu MXChip IoT DevKit do komputera:

1. Kliknij menu rozwijane **Zadania** w programie Visual Studio Code i wybierz polecenie **Uruchom zadanie kompilacji...** Szkic Arduino jest kompilowany i przesyłany do DevKit.

2. Po pomyślnym przesłaniu szkicu wyświetlany jest komunikat *o sukcesie & prześlij szkic.*

## <a name="monitor-devkit-state-in-browser"></a>Monitorowanie stanu DevKit w przeglądarce

1. W przeglądarce sieci `DevKitState\web\index.html` Web otwórz plik — który został utworzony podczas kroku Co jest potrzebne.

2. Zostanie wyświetlona następująca strona sieci Web:![Określ nazwę aplikacji funkcji.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Wprowadź nazwę aplikacji funkcji, którą wcześniej zapisałeś.

4. Kliknij przycisk **Połącz**

5. W ciągu kilku sekund strona odświeża i wyświetla stan połączenia Wi-Fi devkit i stan każdego z wbudowanych czujników.

## <a name="control-the-devkits-user-led"></a>Sterowanie diodą LED użytkownika devkit

1. Kliknij grafikę LED użytkownika na ilustracji strony sieci Web.

2. W ciągu kilku sekund ekran odświeża się i pokazuje aktualny stan koloru diody LED użytkownika.

3. Spróbuj zmienić wartość kolorów diody LED RGB, klikając w różnych lokalizacjach na suwaku RGB.

## <a name="example-operation"></a>Przykładowa operacja

![Przykładowa procedura badania](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Możesz zobaczyć surowe dane bliźniaczej reprezentacji urządzenia\> w witrynie Azure portal: Usługa IoT Hub — urządzenia IoT —\> * \<urządzenie\> *  - \> bliźniacze.

## <a name="next-steps"></a>Następne kroki

Nauczyłeś się, jak:
- Połącz urządzenie MXChip IoT DevKit z akceleratorem rozwiązań zdalnego monitorowania usługi Azure IoT.
- Użyj funkcji bliźniaczych urządzeń Azure IoT, aby wyczuć i kontrolować kolor diody LED RGB devkit.

Oto sugerowane następne kroki:

* [Omówienie akceleratora rozwiązań do zdalnego monitorowania usługi Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Łączenie urządzenia MXChip IoT DevKit z aplikacją Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)
