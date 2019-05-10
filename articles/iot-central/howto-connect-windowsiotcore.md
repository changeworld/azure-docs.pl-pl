---
title: Podłącz urządzenie Windows IoT Core z aplikacją usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako deweloper urządzenia Dowiedz się, jak połączyć urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e8d4ab46c598580a3a87f4344202f2700926bf5c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510321"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Podłącz urządzenie Windows IoT Core z aplikacją usługi Azure IoT Central

W tym artykule opisano jak Deweloper urządzenia do łączenia z urządzeniami Windows IoT Core do aplikacji Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

- Aplikacja usługi Azure IoT Central, utworzone na podstawie **Devkits przykładowe** szablon aplikacji. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).

- Urządzenie z systemem operacyjnym Windows 10 IoT Core. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Jest maszyna deweloperska z [Node.js](https://nodejs.org/) wersji 8.0.0 lub nowszej. Możesz uruchomić `node --version` w wierszu polecenia, aby sprawdzić swoją wersję. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="the-sample-devkits-application"></a>Devkits przykładowej aplikacji

Aplikacja utworzone na podstawie **Devkits przykładowe** szablon aplikacji zawiera **Windows IoT Core** szablon urządzenia o następującej charakterystyce:

- Pomiary dane telemetryczne dla tego urządzenia: **Wilgotność**, **temperatury**, i **wykorzystanie**.
- Ustawienia, aby określić **szybkość wentylator**.
- Właściwości urządzenia **zdechną numer** i właściwości chmury **lokalizacji**.

Aby uzyskać szczegółowe informacje o konfiguracji szablonu urządzenia, zobacz [Szczegóły szablonu Windows IoT Core Device](#device-template-details).

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central, użyj **Device Explorer** stronę, aby dodać prawdziwe urządzenie z **systemu Windows 10 IoT Core** szablon urządzenia. Zwróć uwagę na urządzeniu szczegóły połączenia (**identyfikator zakresu**, **identyfikator urządzenia**, i **klucz podstawowy**). Aby uzyskać więcej informacji, zobacz [pobieranie informacji o połączeniu](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Przygotuj urządzenie

Dla urządzenia, połączyć się z IoT Central musi ona parametry połączenia.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Aby kod urządzenia dostęp do parametrów połączenia, zapisz go w pliku o nazwie **connection.string.iothub** w folderze `C:\Data\Users\DefaultAccount\Documents\` na urządzeniu z systemem Windows 10 IoT Core.

Aby skopiować **connection.string.iothub** plik z komputera stacjonarnego, aby `C:\Data\Users\DefaultAccount\Documents\` folderu na swoim urządzeniu, można użyć [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Użyj przeglądarki sieci web, aby przejść do Windows Device Portal na urządzeniu.
1. Aby przeglądać plików na twoim urządzeniu, wybierz **aplikacji > Eksploratora plików**.
1. Przejdź do **Folders\Documents użytkownika**. Następnie przekaż **connection.string.iothub** pliku:

    ![Przekazywanie parametrów połączenia](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Wdrażanie i uruchamianie

Wdrażanie i uruchamianie przykładowej aplikacji na urządzeniu, można użyć [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Użyj przeglądarki sieci web, aby przejść do Windows Device Portal na urządzeniu.
1. Aby wdrożyć i uruchomić **Azure IoT Hub Client** aplikacji, wybierz **aplikacji > szybkiego uruchamiania przykładów**. Następnie wybierz **Azure IoT Hub Client**.
1. Następnie wybierz **wdrażanie i uruchamianie**.

    ![Wdrażanie i uruchamianie](media/howto-connect-windowsiotcore/quick-run.png)

Za kilka minut można wyświetlić danych telemetrycznych z urządzenia w aplikacji IoT Central.

[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) zawiera narzędzia, które umożliwiają rozwiązywanie problemów z urządzeniem:

- **Menedżera aplikacji** strona pozwala kontrolować aplikacje uruchomione na urządzeniu.
- Jeśli nie masz monitor podłączone do urządzenia, możesz użyć **ustawienia urządzenia** strony na Przechwytywanie zrzutów ekranu na urządzeniu. Na przykład:

    ![Zrzut ekranu aplikacji](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Jeśli chcesz eksplorować i modyfikować kod źródłowy w aplikacji klienckiej, możesz ją pobrać z [repozytorium GitHub samples-Windows-iotcore](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Szczegóły szablonu urządzenia

Aplikacja utworzone na podstawie **Devkits przykładowe** szablon aplikacji zawiera **Windows IoT Core** szablon urządzenia o następującej charakterystyce:

### <a name="telemetry-measurements"></a>Pomiary dotyczące prawdziwych danych telemetrycznych

| Nazwa pola     | Jednostki  | Minimum | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| Temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Ustawienia

Ustawienia liczbowe

| `Display name` | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimum | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Właściwości

| Type            | `Display name` | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Zdechną liczb   | dieNumber  | numer    |
| Text            | Lokalizacja     | lokalizacja   | ND       |

## <a name="next-steps"></a>Kolejne kroki

Teraz, wyjaśniono, jak połączyć urządzenie Windows IoT Core z aplikacją usługi Azure IoT Central, sugerowane następnym krokiem jest Dowiedz się, jak [Konfigurowanie szablonu niestandardowego urządzenia](howto-set-up-template.md) dla urządzenia IoT.
