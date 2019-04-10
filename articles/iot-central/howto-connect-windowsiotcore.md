---
title: Podłącz urządzenie Windows IoT Core z aplikacją usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako deweloper urządzenia Dowiedz się, jak połączyć urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0312e322aea74b3ce9867d09cebc7543da40de5f
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426243"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Podłącz urządzenie Windows IoT Core z aplikacją usługi Azure IoT Central

W tym artykule opisano jak Deweloper urządzenia do łączenia z urządzeniami Windows IoT Core do aplikacji Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja usługi Azure IoT Central, utworzone na podstawie **Devkits przykładowe** szablon aplikacji. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
2. Urządzenie z systemem operacyjnym Windows 10 IoT Core. W tym przewodniku użyjemy Raspberry Pi.


## <a name="sample-devkits-application"></a>**Przykładowy Devkits** aplikacji

Aplikacja utworzone na podstawie **Devkits przykładowe** szablon aplikacji zawiera **Windows IoT Core** szablon urządzenia o następującej charakterystyce: 

- Dane telemetryczne, zawierającą pomiarów urządzenia **wilgotności**, **temperatury** i **wykorzystanie**. 
- Wyświetlanie ustawień **szybkość wentylator**.
- Właściwości zawierający właściwości urządzenia **zdechną numer** i **lokalizacji** właściwość w chmurze.


Można znaleźć szczegółowe informacje o konfiguracji szablonu urządzenia [Windows IoT Core Device Szczegóły szablonu](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details)

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central, Dodaj prawdziwe urządzenie z **Windows IoT Core** szablon urządzenia i zanotować parametry połączenia urządzenia. Aby uzyskać więcej informacji, zobacz [dodać rzeczywistego urządzenia do aplikacji usługi Azure IoT Central](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Przygotuj urządzenie Windows IoT Core

Aby skonfigurować urządzenie Windows IoT Core wykonaj przewodnik krok po kroku w [Konfigurowanie urządzenia Windows IoT Core](https://github.com/Azure/iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central, Dodaj prawdziwe urządzenie z **Windows IoT Core** szablon urządzenia i zanotować szczegółów połączenia urządzeń (**klucza podstawowego identyfikator zakresu, identyfikator urządzenia**). Wykonaj te instrukcje, aby [Generowanie parametrów połączenia urządzenia](howto-generate-connection-string.md) przy użyciu **identyfikator zakresu**, **identyfikator urządzenia**, i **klucz podstawowy** wprowadzono należy pamiętać o wcześniej.

## <a name="prepare-the-windows-10-iot-core-device"></a>Przygotuj urządzenie Windows 10 IoT Core

### <a name="what-youll-need"></a>Czego potrzebujesz

Aby skonfigurować rzeczywistego urządzenia systemu Windows 10 IoT Core, musisz najpierw mieć na urządzenie z systemem Windows 10 IoT Core. Dowiedz się, jak skonfigurować urządzenie Windows 10 IoT Core [tutaj](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

Należy również aplikację kliencką, która może komunikować się z usługi Azure IoT Central. Możesz utworzyć własną aplikację niestandardową przy użyciu zestawu SDK platformy Azure i wdrożyć ją na urządzenie przy użyciu programu Visual Studio, można również pobrać [wstępnie utworzonych przykładowych](https://developer.microsoft.com/windows/iot/samples) po prostu Wdróż i uruchom go na urządzeniu. 

### <a name="deploying-the-sample-client-application"></a>Wdrażanie przykładowej aplikacji klienta

Wdrażanie aplikacji klienckiej z poprzedniego kroku do Twojego urządzenia systemu Windows 10 IoT w celu przygotowania go:

**Upewnij się, że parametry połączenia są przechowywane na urządzeniu na potrzeby aplikacji klienckiej do użycia**
* Na komputerze stacjonarnym należy zapisać parametry połączenia w pliku tekstowym o nazwie connection.string.iothub.
* Skopiuj plik tekstowy do folderu dokumentów urządzenia:
`[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Po wykonaniu tego, musisz otworzyć [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) , wpisując http://[device-IP-address]:8080 w dowolnej przeglądarce.

Z jednej i, jak pokazano w poniższych w przypadku należy:
1. Rozwiń **aplikacje** węzła po lewej stronie.
2. Wybierz **szybkiego uruchamiania przykładów**.
3. Wybierz **klienta usługi Azure IoT Hub**.
4. Wybierz **wdrażanie i uruchamianie**.

![Obraz GIF przedstawiający klienta usługi Azure IoT Hub na Windows Device Portal](./media/howto-connect-windowsiotcore/iothubapp.gif)

Jeśli operacja się powiedzie, aplikacja będzie uruchamianie na urządzeniu i wyglądać następująco:

![Zrzut ekranu przedstawiający usługa Azure IoT Hub aplikację kliencką](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

W usłudze Azure IoT Central można zobaczyć, jak kod uruchomiony na urządzenia Raspberry Pi współdziała z aplikacją:

* Na **pomiarów** strony dla Twojego rzeczywistego urządzenia można wyświetlić dane telemetryczne.
* Na **właściwości** stronie widać wartości zgłaszanych właściwości zdechną numer.
* Na **ustawienia** strony, można zmienić różnych ustawień na urządzenia Raspberry Pi, takie jak szybkość napięcia i wychodzącą.

## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Jeśli chcesz eksplorować i modyfikować kod źródłowy w aplikacji klienckiej, możesz ją pobrać z witryny GitHub [tutaj](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Jeśli zamierzasz zmodyfikować kod, należy wykonać te instrukcje w pliku readme [tutaj](https://github.com/Microsoft/Windows-iotcore-samples) pulpitu w systemie operacyjnym.

> [!NOTE]
> Jeśli **git** nie jest zainstalowany w środowisku deweloperskim, możesz ją pobrać z [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="windows-iot-core-device-template-details"></a>Szczegóły szablonu Windows IoT Core urządzenia

Aplikacja utworzone na podstawie **Devkits przykładowe** szablon aplikacji zawiera **Windows IoT Core** szablon urządzenia o następującej charakterystyce:

### <a name="telemetry-measurements"></a>Pomiary dotyczące prawdziwych danych telemetrycznych

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| Temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Ustawienia

Ustawienia liczbowe

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>Właściwości

| Type            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Zdechną liczb   | dieNumber  | numer    |
| Tekst            | Lokalizacja     | location   | ND       |
