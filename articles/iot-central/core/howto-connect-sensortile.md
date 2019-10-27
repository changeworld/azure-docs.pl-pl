---
title: Połącz urządzenie SensorTile. Box z aplikacją IoT Central platformy Azure | Microsoft Docs
description: Jako deweloper urządzenia dowiesz się, jak połączyć urządzenie SensorTile. Box z aplikacją IoT Central platformy Azure.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 0969ee6dbc035ffa105dd54d34f3b4711d4915cf
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951213"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Połącz urządzenie SensorTile. Box z aplikacją IoT Central platformy Azure

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano, jak deweloper urządzenia łączy urządzenie SensorTile. Box z aplikacją IoT Central Microsoft Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

* Urządzenie SensorTile. Box. Aby uzyskać więcej informacji, zobacz [SensorTile. Box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* Aplikacja czujnika usługi ST beli zainstalowana na urządzeniu z systemem Android można [pobrać z tego miejsca](https://play.google.com/store/apps/details?id=com.st.bluems). Aby uzyskać więcej informacji, odwiedź [czujnik beli: St](https://www.st.com/stblesensor)
* Aplikacja IoT Central platformy Azure utworzona na podstawie szablonu aplikacji **DevKits** . Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Dodaj szablon urządzenia **SensorTile. Box** do aplikacji IoT Central, odwiedzając stronę **Szablony urządzeń** , klikając pozycję **+ Nowy**i wybierając szablon **SensorTile. Box** .

### <a name="get-your-device-connection-details"></a>Pobierz szczegóły połączenia z urządzeniem

W aplikacji IoT Central platformy Azure Dodaj rzeczywiste urządzenie z szablonu urządzenia **SensorTile. Box** i zanotuj szczegóły połączenia urządzenia: **Identyfikator zakresu**, **Identyfikator urządzenia**i **klucz podstawowy**:

1. Dodaj urządzenie z urządzeń. Wybierz pozycję **+ nowy > rzeczywista** , aby dodać rzeczywiste urządzenie.

    * Wprowadź **Identyfikator urządzenia**małymi literami lub użyj sugerowanego **identyfikatora urządzenia**.
    * Wprowadź **nazwę urządzenia**lub użyj sugerowanej nazwy

    ![Dodaj urządzenie](media/howto-connect-sensortile/real-device.png)

1. Aby uzyskać szczegóły połączenia z urządzeniem, **Identyfikator zakresu**, **Identyfikator urządzenia**i **klucz podstawowy**, wybierz pozycję **Połącz** na stronie urządzenia.

    ![Szczegóły połączenia](media/howto-connect-sensortile/connect-device.png)

1. Zanotuj szczegóły połączenia. Po przygotowaniu urządzenia DevKit w następnym kroku nastąpi tymczasowe odłączenie od Internetu.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Skonfiguruj SensorTile. Box przy użyciu aplikacji mobilnej

W tej sekcji dowiesz się, jak wypchnąć oprogramowanie układowe aplikacji na urządzenie. Następnie wysyłasz dane urządzenia do IoT Central za pośrednictwem aplikacji mobilnej czujnika usługi ST

1. Otwórz aplikację czujnika beli i naciśnij przycisk **Utwórz nową aplikację** .

    ![Tworzenie aplikacji](media/howto-connect-sensortile/create-app.png)

1. Wybierz aplikację **BAROMETER** .
1. Naciśnij przycisk Przekaż.

    ![BAROMETER przekazywanie](media/howto-connect-sensortile/barometer-upload.png)

1. Naciśnij przycisk Odtwórz skojarzony z Twoim SensorTile. Box.
1. Po zakończeniu procesu, SensorTile. Box strumieniuje temperaturę, ciśnienie i wilgotność w czasie.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Połącz SensorTile. Box z chmurą

W tej sekcji dowiesz się, jak połączyć SensorTile. Box z aplikacją mobilną i połączyć aplikację mobilną z chmurą.

1. W okienku po lewej stronie wybierz przycisk **Rejestrowanie w chmurze** .

    ![Rejestrowanie w chmurze](media/howto-connect-sensortile/cloud-logging.png)

1. Wybierz pozycję **Azure IoT Central** jako dostawcę chmury.
1. Wstaw identyfikator urządzenia i identyfikator zakresu, które zostały wcześniej odnotowane.

    ![Poświadczenia](media/howto-connect-sensortile/credentials.png)

1. Wybierz przycisk radiowy **klucz aplikacji** .
1. Kliknij przycisk **Połącz** i wybierz dane telemetryczne, które chcesz przekazać.
1. Po kilku sekundach dane są wyświetlane na pulpicie nawigacyjnym aplikacji IoT Central.

## <a name="sensortilebox-device-template-details"></a>Szczegóły szablonu urządzenia SensorTile. Box

Aplikacja utworzona na podstawie szablonu urządzenia SensorTile. Box o następujących cechach:

### <a name="telemetry"></a>Telemetria

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 30       | 90     | 1              |
| temp           | OC     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| przyspieszeniomierz | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | dokumenty   | -3276   | 3276    | 1              |
| gyroscopeY     | dokumenty   | -3276   | 3276    | 1              |
| gyroscopeZ     | dokumenty   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak połączyć SensorTile. Box z aplikacją IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z [tematem Konfigurowanie niestandardowego szablonu urządzenia](howto-set-up-template.md) dla własnego urządzenia IoT.
