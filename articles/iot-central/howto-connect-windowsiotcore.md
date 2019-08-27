---
title: Połącz urządzenie z systemem Windows IoT Core z aplikacją usługi Azure IoT Central | Microsoft Docs
description: Jako deweloper urządzenia dowiesz się, jak połączyć urządzenie zestawu deweloperskiego IoT DevKit z aplikacją IoT Central platformy Azure.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b14d6f70f4c4163f16c8275f4e071da6a9e0bc78
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019821"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Podłączanie urządzenia Windows IoT Core do aplikacji IoT Central platformy Azure

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano sposób, w jaki deweloper urządzenia nawiązuje połączenie z urządzeniem Windows IoT Core z aplikacją IoT Central Microsoft Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

- Aplikacja IoT Central platformy Azure utworzona na podstawie przykładowego szablonu aplikacji **Devkits** . Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).

- Urządzenie z systemem operacyjnym Windows 10 IoT Core. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Komputer deweloperski z zainstalowanym środowiskiem [Node. js](https://nodejs.org/) w wersji 8.0.0 lub nowszej. Aby sprawdzić swoją `node --version` wersję, można uruchomić polecenie w wierszu polecenia. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="the-sample-devkits-application"></a>Przykładowa aplikacja Devkits

Aplikacja utworzona na podstawie przykładowego szablonu aplikacji **Devkits** zawiera szablon urządzenia **Windows IoT Core** o następujących cechach:

- Pomiary telemetrii dla urządzenia: **Wilgotność**, **temperatura**i **ciśnienie**.
- Ustawienie sterujące **szybkością wentylatora**.
- **Numer** pozycji właściwości urządzenia i **Lokalizacja**właściwości chmury.

Aby uzyskać szczegółowe informacje na temat konfiguracji szablonu urządzenia, zobacz [szczegóły szablonu urządzenia Windows IoT Core](#device-template-details).

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji IoT Central platformy Azure Użyj strony **Device Explorer** , aby dodać rzeczywiste urządzenie z szablonu urządzenia **Windows 10 IoT Core** . Zanotuj szczegóły połączenia urządzenia (**Identyfikator zakresu**, **Identyfikator urządzenia**i **klucz podstawowy**). Aby uzyskać więcej informacji, zobacz [Uzyskiwanie informacji o połączeniu](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Przygotowywanie urządzenia

Aby urządzenie łączyło się z IoT Central, potrzebuje parametrów połączenia.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Aby kod urządzenia mógł uzyskać dostęp do parametrów połączenia, Zapisz go w pliku o nazwie **Connection. String. iothub** w folderze `C:\Data\Users\DefaultAccount\Documents\` na urządzeniu z systemem Windows 10 IoT Core.

Aby skopiować plik **Connection. iothub** z komputera stacjonarnego do `C:\Data\Users\DefaultAccount\Documents\` folderu na urządzeniu, możesz użyć [portalu urządzeń z systemem Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Użyj przeglądarki sieci Web, aby przejść do portalu urządzeń z systemem Windows na urządzeniu.
1. Aby przeglądać pliki na urządzeniu, wybierz pozycję **aplikacje > Eksploratora plików**.
1. Przejdź do **Folders\Documents użytkownika**. Następnie Przekaż plik **Connection. String. iothub** :

    ![Przekaż parametry połączenia](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Wdrażanie i uruchamianie

Aby wdrożyć i uruchomić przykładową aplikację na urządzeniu, możesz użyć [portalu urządzeń z systemem Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Użyj przeglądarki sieci Web, aby przejść do portalu urządzeń z systemem Windows na urządzeniu.
1. Aby wdrożyć i uruchomić aplikację **kliencką IoT Hub platformy Azure** , wybierz pozycję **aplikacje > Szybkie uruchamianie przykładów**. Następnie wybierz pozycję **Azure IoT Hub Client**.
1. Następnie wybierz pozycję **Wdróż i uruchom**.

    ![Wdrażanie i uruchamianie](media/howto-connect-windowsiotcore/quick-run.png)

Po kilku minutach można wyświetlić dane telemetryczne z urządzenia w aplikacji IoT Central.

[Portal urządzeń z systemem Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) zawiera narzędzia, których można użyć do rozwiązywania problemów z urządzeniem:

- Strona **Menedżer aplikacji** pozwala kontrolować aplikacje działające na urządzeniu.
- Jeśli nie masz monitora połączonego z urządzeniem, możesz użyć strony **ustawień urządzenia** , aby przechwycić zrzuty ekranu z urządzenia. Przykład:

    ![Zrzut ekranu aplikacji](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Jeśli chcesz eksplorować i modyfikować kod źródłowy aplikacji klienckiej, możesz pobrać ją z repozytorium w witrynie [GitHub systemu Windows-iotcore-Samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Szczegóły szablonu urządzenia

Aplikacja utworzona na podstawie przykładowego szablonu aplikacji **Devkits** zawiera szablon urządzenia **Windows IoT Core** o następujących cechach:

### <a name="telemetry-measurements"></a>Pomiary telemetrii

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Ustawienia

Ustawienia liczbowe

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Szybkość wentylatorów    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Właściwości

| Type            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwość urządzenia | Numer struktury   | dieNumber  | numer    |
| Text            | Location     | location   | ND       |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak podłączyć urządzenie Windows IoT Core do aplikacji IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Konfigurowanie niestandardowego szablonu urządzenia](howto-set-up-template.md) dla własnego urządzenia IoT.
