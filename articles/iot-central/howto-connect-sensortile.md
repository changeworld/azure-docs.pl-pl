---
title: Podłącz urządzenie SensorTile.box do aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako deweloper urządzenia Dowiedz się, jak połączyć urządzenie SensorTile.box aplikację usługi Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472170"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Podłącz urządzenie SensorTile.box do aplikacji usługi Azure IoT Central

W tym artykule opisano jak Deweloper urządzenia podłączenia urządzenia SensorTile.box do aplikacji Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

* Urządzenie SensorTile.box. Aby uzyskać więcej informacji, zobacz [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* Czujnik cz ST aplikacji zainstalowane na urządzeniu z systemem Android, możesz [Pobierz go stąd](https://play.google.com/store/apps/details?id=com.st.bluems). Więcej informacji można znaleźć pod adresem: [Czujnik cz ST](https://www.st.com/stblesensor)
* Aplikacja usługi Azure IoT Central, utworzone na podstawie **DevKits** szablon aplikacji. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Dodaj **SensorTile.box** szablon urządzenia w aplikacji IoT Central, odwiedzając **szablonów urządzeń** strony, klikając **+ nowy**i wybierając polecenie **SensorTile.box** szablonu.

### <a name="get-your-device-connection-details"></a>Uczyń swoje urządzenie szczegóły połączenia

W aplikacji usługi Azure IoT Central, Dodaj prawdziwe urządzenie z **SensorTile.box** szablon urządzenia i zanotować szczegóły połączenia urządzenia: **Identyfikator zakresu**, **identyfikator urządzenia**, i **klucz podstawowy**:

1. Dodaj urządzenia z Device Explorer. Wybierz **+ nowy > rzeczywistych** dodać rzeczywistego urządzenia.

    * Wprowadzić małymi literami **identyfikator urządzenia**, lub użyj sugerowanego **identyfikator urządzenia**.
    * Wprowadź **nazwy urządzenia**, lub użyć sugerowanej nazwy

    ![Dodaj urządzenie](media/howto-connect-sensortile/real-device.png)

1. Aby uzyskać szczegóły połączenia urządzenia **identyfikator zakresu**, **identyfikator urządzenia**, i **klucza podstawowego**, wybierz opcję **Connect** na stronie urządzenia.

    ![Szczegóły połączenia](media/howto-connect-sensortile/connect-device.png)

1. Zanotuj szczegóły połączenia. Teraz możesz tymczasowo odłączony od Internetu, podczas przygotowywania urządzenia Mxchip w następnym kroku.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Konfigurowanie SensorTile.box z aplikacją mobilną

W tej sekcji dowiesz się, jak wypychać oprogramowanie układowe aplikacji na urządzeniu. Możesz następnie jak wysyłać dane urządzenia IoT Central za pośrednictwem aplikacji mobilnej ST cz czujników przy użyciu połączenia Bluetooth niski energii (Włącz).

1. Otwórz aplikację czujnik cz ST i naciśnij klawisz **Utwórz nową aplikację** przycisku.

    ![Tworzenie aplikacji](media/howto-connect-sensortile/create-app.png)

1. Wybierz **Barometer** aplikacji.
1. Naciśnij przycisk Prześlij.

    ![Przekazywanie barometer](media/howto-connect-sensortile/barometer-upload.png)

1. Naciśnij przycisk Odtwórz, skojarzone z Twojej SensorTile.box.
1. Po zakończeniu procesu SensorTile.box strumieni za pośrednictwem cz temperatury, wykorzystanie i wilgotności.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Łączenie SensorTile.box z chmurą

W tej sekcji dowiesz się, jak połączyć SensorTile.box dla aplikacji mobilnej i połączyć z aplikacjami mobilnymi do chmury.

1. Korzystając z menu po lewej stronie wybierz **rejestrowanie chmury** przycisku.

    ![Rejestrowanie chmury](media/howto-connect-sensortile/cloud-logging.png)

1. Wybierz **usługi Azure IoT Central** jako dostawcy chmury.
1. Wstaw identyfikator urządzenia i identyfikator zakresu, które wcześniej zostały podane.

    ![Poświadczenia](media/howto-connect-sensortile/credentials.png)

1. Wybierz **klucz aplikacji** przycisku radiowego.
1. Kliknij przycisk **Connect** i wybierz dane telemetryczne, który chcesz przekazać.
1. Po kilku sekundach dane są wyświetlane na pulpicie nawigacyjnym aplikacji IoT Central.

## <a name="sensortilebox-device-template-details"></a>Szczegóły szablonu urządzenia SensorTile.box

Aplikacja utworzone na podstawie szablonu urządzenia SensorTile.box o następującej charakterystyce:

### <a name="telemetry"></a>Telemetria

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 30       | 90     | 1              |
| Temp           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | dps   | -3276   | 3276    | 1              |
| gyroscopeY     | dps   | -3276   | 3276    | 1              |
| gyroscopeZ     | dps   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Kolejne kroki

Teraz, wyjaśniono, jak połączyć SensorTile.box do aplikacji usługi Azure IoT Central, sugerowane następnym krokiem jest Dowiedz się, [sposób konfigurowania szablonu niestandardowego urządzenia](howto-set-up-template.md) dla urządzenia IoT.
