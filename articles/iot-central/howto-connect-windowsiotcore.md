---
title: Podłącz urządzenie Windows IoT Core do aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Deweloper urządzenia jak Podłącz urządzenie z systemem zestaw deweloperski IoT MXChip do aplikacji Azure IoT centralnej.
author: miriamb
ms.author: mriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 55c0d9c26cd4e7f8ae9f6ff2359e0f2d2a9970c9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629147"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Podłącz urządzenie Windows IoT Core do aplikacji Azure IoT centralnej

W tym artykule opisano sposób Deweloper urządzenia połączenia z urządzeniem Windows IoT Core do aplikacji Microsoft Azure IoT centralnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja Azure IoT centralnej utworzone na podstawie **Devkits próbki** szablon aplikacji. Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji centralnej Azure IoT](howto-create-application.md).
2. Urządzenie z systemem operacyjnym Windows 10 IoT Core. W ramach tego przewodnika użyjemy Pi malina

Aplikacji utworzone na podstawie **Devkits próbki** szablon aplikacji obejmuje **Windows IoT Core** szablonu urządzenia o następującej charakterystyce:

### <a name="telemetry-measurements"></a>Pomiary telemetrii

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| wilgotność       | %      | 0       | 100     | 0              |
| Temp           | C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Ustawienia

Ustawienia numeryczne

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>Właściwości

| Typ            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Die numer   | dieNumber  | numer    |
| Tekst            | Lokalizacja     | location   | ND       |

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji Azure IoT centralnej, Dodaj rzeczywistego urządzenia z **Windows IoT Core** szablon urządzenia i zanotować ciąg połączenia urządzenia. Aby uzyskać więcej informacji, zobacz [dodawania rzeczywistego urządzenia do aplikacji Azure IoT centralnej](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Przygotuj urządzenie Windows IoT Core

Aby skonfigurować urządzenie Windows IoT Core wykonaj przewodnik krok po kroku w [Konfigurowanie urządzenia Windows IoT Core] (https://github.com/Microsoft/microsoft-iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji Azure IoT centralnej, Dodaj rzeczywistego urządzenia z **Windows IoT Core** szablon urządzenia i zanotować ciąg połączenia urządzenia. Aby uzyskać więcej informacji, zobacz [dodawania rzeczywistego urządzenia do aplikacji Azure IoT centralnej](tutorial-add-device.md).

## <a name="prepare-the-windows-10-iot-core-device"></a>Przygotuj urządzenie z systemem Windows 10 IoT Core

### <a name="what-youll-need"></a>Czego potrzebujesz

Aby skonfigurować urządzenia z systemem Windows 10 IoT Core fizycznego, należy najpierw masz urządzenie z systemem Windows 10 IoT Core. Informacje o sposobie konfigurowania urządzenia z systemem Windows 10 IoT Core [tutaj](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice).

Należy również aplikacji klienta, który może komunikować się z centralną IoT Azure. Możesz tworzenie własnych niestandardowych aplikacji za pomocą zestawu Azure SDK i wdrożyć ją na urządzenie przy użyciu programu Visual Studio, można również pobrać [wbudowanych próbki](https://developer.microsoft.com/en-us/windows/iot/samples) i po prostu Wdróż i uruchom go na urządzeniu. 

### <a name="deploying-the-sample-client-application"></a>Wdrażanie przykładowej aplikacji klienta

Aby wdrożyć aplikację klienta z poprzedniego kroku na urządzeniu IoT z systemem Windows 10 w celu przygotowania go:

**Upewnij się, że parametry połączenia są przechowywane na urządzeniu w przypadku aplikacji klienta**
* Na pulpicie należy zapisać parametry połączenia w pliku tekstowym o nazwie connection.string.iothub.
* Skopiuj plik do folderu dokumentu urządzenia: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Po wykonaniu tego musisz otworzyć [Portal urządzenia z systemem Windows](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal) , wpisując w http://[device-IP-address]:8080 do dowolnej przeglądarki.

Brak i, jak pokazano w przypadku poniżej można:
1. Rozwiń węzeł "Aplikacji" po lewej stronie.
2. Kliknij przycisk "Uruchom szybkie próbek".
3. Kliknij przycisk "Client Centrum IoT Azure".
4. Kliknij polecenie "Wdróż i uruchom".

![GIF klienta Centrum IoT Azure w portalu urządzenia z systemem Windows](./media/howto-connect-windowsiotcore/iothubapp.gif)

Gdy to się powiedzie, aplikacja uruchamianie na urządzeniu i wyglądać następująco:

![Zrzut ekranu przedstawiający Centrum IoT Azure aplikacji klienta](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

W Azure IoT centralnej można wyświetlić, jak uruchomiony Pi malina kod współdziała z aplikacją:

* Na **pomiary** strony dla rzeczywistego urządzenia, można wyświetlić dane telemetryczne.
* Na **właściwości** strony widać wartości właściwości Die numer zgłoszony.
* Na **ustawienia** strony, można zmienić różnych ustawień na Pi malina, takich jak napięcia i wentylator szybkości.

## <a name="download-the-source-code"></a>Pobieranie kodu źródłowego

Jeśli chcesz Eksploruj i zmodyfikować kod źródłowy aplikacji klienckiej, można go pobrać z witryny GitHub [tutaj](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Jeśli zamierzasz zmodyfikować kod, należy wykonać instrukcje zawarte w pliku readme [tutaj](https://github.com/Microsoft/Windows-iotcore-samples) pulpitu systemu operacyjnego.

> [!NOTE]
> Jeśli **git** nie jest zainstalowany w środowisku projektowania, możesz pobrać go z [ https://git-scm.com/download ](https://git-scm.com/download).
