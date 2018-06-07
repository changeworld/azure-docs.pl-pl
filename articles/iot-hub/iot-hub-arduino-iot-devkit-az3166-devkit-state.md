---
title: Przy użyciu urządzenia Azure twins kontrolować LED użytkownika zestaw deweloperski IoT MXChip | Dokumentacja firmy Microsoft
description: W tym samouczku informacje o sposobie monitorowania zestaw deweloperski stanów i kontroli użytkownika LED twins urządzenia Azure IoT Hub.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 2845b545484f4eef1e6999c1c54aaba5de14b832
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633268"
---
# <a name="mxchip-iot-devkit"></a>Zestaw deweloperski IoT MXChip

W tym przykładzie służy do monitorowania sieci Wi-Fi MXChip IoT zestaw deweloperski informacji i czujnik stan oraz do sterowania kolor LED użytkownika przy użyciu twins urządzenia Azure IoT Hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

- Jak monitorować czujnik zestaw deweloperski IoT MXChip Stany.
- Jak używać twins Azure urządzenia do kontrolowania kolor LED RGB zestaw deweloperski.

## <a name="what-you-need"></a>Co jest potrzebne

- Konfigurowanie środowiska deweloperskiego, wykonując [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
- Twoje okno terminalu GitBash (lub inny interfejs wiersza polecenia Git) wpisz następujące polecenia:
    - `git clone https://github.com/DevKitExamples/DevKitState.git`
    - `cd DevKitState`
    - `code .`

## <a name="provision-azure-services"></a>Udostępnianie usług platformy Azure

1. Kliknij przycisk **zadania** menu rozwijane w programie Visual Studio Code i wybierz **Uruchom zadanie...**   -  **chmurze provision**.
2. Postęp jest wyświetlany w obszarze **terminali** karcie **powitalnej** panelu.
3. Po wyświetleniu monitu z komunikatem *subskrypcję, który chcesz wybrać*, wybierz subskrypcję.
4. Wybierz lub grupy zasobów. 
 
    > [!NOTE]
    > Jeśli masz już bezpłatne Centrum IoT, ten krok zostanie pominięty.

5. Po wyświetleniu monitu z komunikatem *Centrum IoT, które chcesz wybrać*wybierz lub Utwórz Centrum IoT.
6. Coś podobnego do *funkcji aplikacji: Nazwa aplikacji funkcji: xxx*, jest wyświetlana. Zanotuj nazwę aplikacji funkcji; będzie używany w kolejnym kroku.
7. Oczekiwania dla usługi Azure Resource Manager szablonu wdrożenia, aby zakończyć, który jest wskazane, kiedy wiadomość *wdrożenie szablonu usługi Resource Manager: gotowe* jest wyświetlany.

## <a name="deploy-function-app"></a>Wdrażanie aplikacji — funkcja

1. Kliknij przycisk **zadania** menu rozwijane w programie Visual Studio Code i wybierz **Uruchom zadanie...**   -  **wdrażania chmury**.
2. Poczekaj na zakończenie; procesu przekazywania kod aplikacji funkcji komunikat *wdraża aplikację funkcji: gotowe* jest wyświetlany.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurowanie parametrów połączenia urządzenia Centrum IoT w zestaw deweloperski

1. Zestaw deweloperski programu MXChip IoT łączenie się z komputerem.
2. Kliknij przycisk **zadania** menu rozwijane w programie Visual Studio Code i wybierz **Uruchom zadanie...**   -  **konfiguracji urządzenia połączenia**
3. Na zestaw deweloperski IoT MXChip, naciśnij i przytrzymaj przycisk **A**, naciśnij klawisz **zresetować** przycisk, a następnie przycisk **A** dokonanie DekKit trybu konfiguracji.
4. Poczekaj, aż proces konfiguracji ciąg połączenia zostanie ukończona.

## <a name="upload-arduino-code-to-devkit"></a>Przekazywanie Arduino kodu do zestaw deweloperski

Z Twojego zestaw deweloperski IoT MXChip podłączone do komputera:
1. Kliknij przycisk **zadania** menu rozwijane w programie Visual Studio Code i wybierz **Uruchom zadanie kompilacji...** Rysunek Arduino kompilacji i przekazane do zestaw deweloperski.
2. Jeśli schemat został przekazany pomyślnie, *kompilacji & Przekaż szkic: Sukces* zostanie wyświetlony komunikat.

## <a name="monitor-devkit-state-in-browser"></a>Monitor stanu zestaw deweloperski w przeglądarce

1. Otwórz w przeglądarce sieci Web `DevKitState\web\index.html` pliku —, który został utworzony podczas [potrzebnych](#whatyouneed) kroku.
2. Jest wyświetlana następująca strona sieci Web:![Określ nazwę aplikacji funkcji.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)
1. Wprowadź nazwę aplikacji funkcji, które zapisanej wcześniej.
2. Kliknij przycisk **Connect** przycisku
3. W ciągu kilku sekund strona zostanie odświeżona i wyświetla stan połączenia sieci Wi-Fi zestaw deweloperski i stan każdego z czujników dołączyć.

## <a name="control-the-devkits-user-led"></a>Kontrolowanie użytkowników zestaw deweloperski LED

1. Kliknij grafikę LED użytkownika na ilustracji strony sieci Web.
2. W ciągu kilku sekund ekranu odświeża i wyświetla bieżący stan kolor LED użytkownika.
3. Spróbuj zmienić wartości kolorów RGB LED, klikając w różnych miejscach na RGB formanty suwaka.

## <a name="example-operation"></a>Przykład działania

![Przykład testu procedury](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Można wyświetlić dane pierwotne dwie urządzenia w portalu Azure: Centrum IoT -\> urządzenia IoT —\> *\<urządzenia\>*  - \> dwie urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Wiesz już, jak:
- Podłącz urządzenie z systemem zestaw deweloperski IoT MXChip do akcelerator rozwiązań sieci Azure IoT zdalnego monitorowania.
- Funkcja Azure IoT urządzenia twins znaczeniu i sterowanie kolor LED RGB zestaw deweloperski.

Poniżej znajdują się Sugerowane następne kroki:

* [Omówienie usługi Azure akceleratora rozwiązania IoT monitorowania zdalnego](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestaw deweloperski IoT MXChip do aplikacji Azure IoT centralnej](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
