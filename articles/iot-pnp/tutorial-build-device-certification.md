---
title: Utwórz urządzenie usługi IoT Plug and Play w wersji zapoznawczej, które jest gotowe do certyfikacji | Microsoft Docs
description: Jako deweloper urządzenia zapoznaj się z informacjami na temat tworzenia urządzenia usługi IoT Plug and Play w wersji zapoznawczej, które jest gotowe do certyfikacji.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 43fc928b1274159839dc0df395e86d065f84b4c7
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550270"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Utwórz urządzenie usługi IoT Plug and Play w wersji zapoznawczej, które jest gotowe do certyfikacji

W tym samouczku opisano, jak jako deweloper urządzenia można utworzyć urządzenie usługi IoT Plug and Play w wersji zapoznawczej, które jest gotowe do certyfikacji.

Testy certyfikacji sprawdzają, czy:

- Twój kod urządzenia IoT Plug and Play jest zainstalowany na urządzeniu.
- Kod urządzenia IoT Plug and Play jest tworzony przy użyciu zestawu Azure IoT SDK.
- Kod urządzenia obsługuje [IoT Hub Device Provisioning Service platformy Azure](../iot-dps/about-iot-dps.md).
- Kod urządzenia implementuje interfejs informacji o urządzeniu.
- Model możliwości i kod urządzenia pracują z IoT Central.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Narzędzia Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack

Potrzebne jest również urządzenie IoT Plug and Play utworzone w ramach [szybkiego startu: Użyj modelu możliwości urządzenia, aby utworzyć urządzenie](quickstart-create-pnp-device-windows.md).

## <a name="store-a-capability-model-and-interfaces"></a>Przechowywanie modelu możliwości i interfejsów

W przypadku urządzeń z Plug and Play IoT należy utworzyć model możliwości i interfejsy, które definiują możliwości urządzenia jako pliki JSON.

Te pliki JSON można przechowywać w trzech różnych lokalizacjach:

- Repozytorium modelu publicznego.
- Repozytorium modelu firmy.
- Na urządzeniu.

Obecnie w celu certyfikowania urządzenia pliki muszą być przechowywane w repozytorium modelu firmy lub w repozytorium modelu publicznego.

## <a name="include-the-required-interfaces"></a>Dołącz wymagane interfejsy

Aby przekazać proces certyfikacji, należy uwzględnić i zaimplementować interfejs **informacji o urządzeniu** w modelu możliwości. Ten interfejs ma następujące identyfikatory:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Jeśli ukończono [Przewodnik Szybki Start: korzystanie z modelu możliwości urządzenia do utworzenia urządzenia](quickstart-create-pnp-device-windows.md), interfejs **informacji o urządzeniu** został już uwzględniony w modelu.

Aby dołączyć Interfejs **informacji o urządzeniu** do modelu urządzenia, Dodaj identyfikator interfejsu do właściwości `implements` modelu możliwości:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Aby wyświetlić interfejs **informacji o urządzeniu** w vs Code:

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play** a następnie wybierz polecenie **Otwórz repozytorium Plug and Play "IoT model** ". Wybierz pozycję **Otwórz repozytorium modelu publicznego**. Repozytorium modelu publicznego zostanie otwarte w VS Code.

1. W repozytorium modelu publicznego wybierz kartę **interfejsy** , wybierz ikonę filtru, a następnie wprowadź **Informacje o urządzeniu** w polu Filtr.

1. Aby utworzyć lokalną kopię interfejsu **informacji o urządzeniu** , zaznacz ją na liście filtrowane, a następnie wybierz pozycję **Pobierz**. VS Code Wyświetla plik interfejsu.

Aby wyświetlić interfejs **informacji o urządzeniu** przy użyciu interfejsu wiersza polecenia platformy Azure:

1. [Zainstaluj rozszerzenie interfejsu wiersza polecenia usługi Azure IoT](howto-install-pnp-cli.md).

1. Poniższe polecenie interfejsu wiersza polecenia platformy Azure umożliwia wyświetlenie interfejsu z IDENTYFIKATORem interfejsu informacji o urządzeniu:

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Aby uzyskać więcej informacji, zobacz [Instalowanie i używanie rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Aktualizowanie kodu urządzenia

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Włączanie aprowizacji urządzeń za pomocą usługi Azure IoT Device Provisioning (DPS)

Aby przeprowadzić certyfikację urządzenia, należy włączyć obsługę administracyjną w [usłudze Azure IoT Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Aby dodać możliwość korzystania z funkcji DPS, można wygenerować procedurę tworzenia kodu w języku C w programie VS Code. Wykonaj następujące kroki:

1. Otwórz folder z plikiem DCM w VS Code, użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz opcję **Generuj procedurę tworzenia kodu urządzenia**.

1. Wybierz plik DCM, którego chcesz użyć do wygenerowania szczątkowego kodu urządzenia.

1. Wprowadź nazwę projektu, czyli nazwę aplikacji urządzenia.

1. Wybierz **ANSI C** jako język.

1. Wybierz pozycję **za pośrednictwem usługi DPS (usługa Device Provisioning) klucz symetryczny** jako metodę połączenia.

1. Wybierz **projekt CMAKE w systemie Windows** lub **CMAKE projekt w systemie Linux** jako szablon projektu w zależności od systemu operacyjnego urządzenia.

1. VS Code otwiera nowe okno z wygenerowanymi plikami zastępczymi kodu urządzenia.

1. Po skompilowaniu kodu wprowadź poświadczenia usługi DPS (**zakres identyfikatorów**usługi DPS, **klucz symetryczny usługi DPS**, **Identyfikator urządzenia**) jako parametry aplikacji. Aby uzyskać poświadczenia z portalu certyfikacji, zobacz [łączenie i testowanie urządzenia Plug and Play IoT](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd/sh
    .\your_pnp_app.exe [DPS ID Scope] [DPS symmetric key] [device ID]
    ```

### <a name="implement-standard-interfaces"></a>Implementuj interfejsy standardowe

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementowanie informacji o modelu i interfejsów informacji zestawu SDK

Zestaw SDK urządzenia usługi Azure IoT implementuje informacje o modelu i interfejsy informacji zestawu SDK. Jeśli używasz funkcji generowania kodu w VS Code, kod urządzenia używa zestawu SDK Plug and Play urządzenia IoT.

Jeśli zdecydujesz się nie używać zestawu SDK urządzeń Azure IoT, możesz użyć kodu źródłowego zestawu SDK jako odniesienia do własnej implementacji.

#### <a name="implement-the-device-information-interface"></a>Implementowanie interfejsu informacji o urządzeniu

Zaimplementuj Interfejs **informacji o urządzeniu** na urządzeniu i podaj informacje dotyczące urządzenia z urządzenia w czasie wykonywania.

Możesz użyć przykładowej implementacji interfejsu **informacji o urządzeniu** dla systemu [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) jako odwołania.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Zaimplementuj wszystkie możliwości zdefiniowane w modelu

Podczas certyfikacji urządzenie jest testowane programowo, aby upewnić się, że implementuje możliwości zdefiniowane w interfejsach. Użyj kodu stanu HTTP 501, aby odpowiedzieć na żądania właściwości odczytu i zapisu, jeśli urządzenie nie implementuje ich.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy utworzono urządzenie Plug and Play IoT na potrzeby certyfikacji, sugerowanym następnym krokiem jest:

> [!div class="nextstepaction"]
> [Dowiedz się, jak certyfikowanie urządzenia](tutorial-certification-test.md)
