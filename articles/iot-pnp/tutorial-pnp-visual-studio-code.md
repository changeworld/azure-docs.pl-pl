---
title: Tworzenie i testowanie urządzenia IoT Plug and Play Preview | Dokumenty firmy Microsoft
description: Jako deweloper urządzeń dowiedz się, jak używać programu VS Code do tworzenia i testowania nowego modelu możliwości urządzenia dla urządzenia IoT Plug and Play Preview.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719725"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Samouczek: Tworzenie i testowanie modelu możliwości urządzenia przy użyciu kodu programu Visual Studio

W tym samouczku pokazano, jak jako deweloper urządzeń używać programu Visual Studio Code do tworzenia _modelu możliwości urządzenia._ Model służy do generowania kodu szkieletu do uruchomienia na urządzeniu, które łączy się z wystąpieniem usługi Azure IoT Hub w chmurze.

W sekcji w tym samouczku, który opisuje, jak skompilować wygenerowany kod szkieletu zakłada, że używasz systemu Windows.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie modelu możliwości urządzenia
> * Generowanie kodu urządzenia szkieletowego z modelu
> * Implementowanie wycinków w wygenerowanym kodzie
> * Uruchom kod, aby przetestować interakcje z centrum IoT

## <a name="prerequisites"></a>Wymagania wstępne

Aby pracować z modelem możliwości urządzenia w tym samouczku, potrzebujesz:

* [Kod programu Visual Studio:](https://code.visualstudio.com/download)Kod VS jest dostępny dla wielu platform
* Pakiet rozszerzeń [usługi Azure IoT Tools for VS Code.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Aby zainstalować pakiet rozszerzeń w programie VS Code, należy wykonać następujące czynności:

    1. W programie VS Code wybierz kartę **Rozszerzenia.**
    1. Wyszukaj **narzędzia IoT platformy Azure**.
    1. Wybierz pozycję **Zainstaluj**.

Aby utworzyć wygenerowany kod C w systemie Windows w tym samouczku, potrzebujesz:

* [Tworzenie narzędzi dla programu Visual Studio za](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) pomocą narzędzi **kompilacji języka C++** i obciążeń **składników menedżera pakietów NuGet.** Lub jeśli masz już [visual studio (społeczność, professional lub przedsiębiorstwo)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 lub 2015 z tymi samymi obciążeniami zainstalowanymi.
* [Git](https://git-scm.com/download)
* [Cmake](https://cmake.org/download/)

Aby przetestować kod urządzenia w tym samouczku, potrzebujesz:

* [Eksplorator IoT platformy Azure](https://github.com/Azure/azure-iot-explorer/releases).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modelowanie urządzenia

Użyj _języka cyfrowej definicji bliźniaczej reprezentacji,_ aby utworzyć model możliwości urządzenia. Model zazwyczaj składa się z wielu plików definicji _interfejsu_ i pliku pojedynczego modelu. **Narzędzia Usługi Azure IoT dla programu VS Code** zawierają narzędzia ułatwiające tworzenie i edytowanie tych plików JSON.

### <a name="create-the-interface-file"></a>Tworzenie pliku interfejsu

Aby utworzyć plik interfejsu definiujący możliwości urządzenia IoT w programie VS Code:

1. Utwórz folder o nazwie **devicemodel**.

1. Uruchom program VS Code i otwórz paletę poleceń za pomocą **klawiszy Ctrl+Shift+P.**

1. Wprowadź **pozycję Plug and Play,** a następnie wybierz polecenie IoT Plug & Play: Create Interface ( polecenie **IoT Plug & Play: Create Interface).**

1. Przejdź do utworzonego folderu **devicemodel** i wybierz go.

1. Następnie wprowadź **EnvironmentalSensor** jako nazwę interfejsu i naciśnij **klawisz Enter**. Program VS Code tworzy przykładowy plik interfejsu o nazwie **EnvironmentalSensor.interface.json**.

1. Zastąp zawartość tego pliku następującym `{your name}` JSON i zastąp `@id` w polu unikatową wartością. Używaj tylko znaków a-z, A-Z, 0-9 i podkreślenia. Aby uzyskać więcej informacji, zobacz [Format identyfikatora Digital Twin](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). Identyfikator interfejsu musi być unikatowy, aby zapisać interfejs w repozytorium:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Ten interfejs definiuje właściwości urządzenia, takie jak **Nazwa klienta,** typy telemetrii, takie jak **Temperatura,** i polecenia, takie jak **turnon**.

1. Dodaj funkcję polecenia o nazwie **blink** na końcu tego pliku interfejsu. Przed dodaniem polecenia należy dodać przecinek. Spróbuj wpisać definicję, aby zobaczyć, w jaki sposób intellisense, autouzupełnianie i sprawdzanie poprawności może pomóc w edycji definicji interfejsu:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Zapisz plik.

### <a name="create-the-model-file"></a>Tworzenie pliku modelu

Plik modelu określa interfejsy implementujące urządzenie IoT Plug and Play. Zazwyczaj istnieją co najmniej dwa interfejsy w modelu — jeden lub więcej, które definiują określone możliwości urządzenia i standardowy interfejs, który muszą implementować wszystkie urządzenia Typu Plug and Play IoT.

Aby utworzyć plik modelu określający interfejsy, które urządzenie IoT Plug and Play implementuje w programie VS Code:

1. Użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń.

1. Wprowadź **pozycję Plug and Play,** a następnie wybierz polecenie **IoT Plug & Play: Create Capability Model** ( Umożliwia utworzenie modelu możliwości). Następnie wpisz **SensorboxModel** jako nazwę modelu. VS Code tworzy przykładowy plik interfejsu o nazwie **SensorboxModel.capabilitymodel.json**.

1. Zastąp zawartość tego pliku następującym `{your name}` JSON i zastąp w `@id` polu i w `EnvironmentalSensor` interfejsie taką samą wartość, jaka została użyta w pliku **EnvironmentalSensor.interface.json.** Identyfikator interfejsu musi być unikatowy, aby zapisać interfejs w repozytorium:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Model definiuje urządzenie, które implementuje interfejs **EnvironmentalSensor** i standardowy interfejs **DeviceInformation.**

1. Zapisz plik.

### <a name="download-the-deviceinformation-interface"></a>Pobierz interfejs DeviceInformation

Przed wygenerowaniem kodu szkieletu z modelu należy utworzyć lokalną kopię **DeviceInformation** z *repozytorium modelu publicznego*. Repozytorium modelu publicznego zawiera już interfejs **DeviceInformation.**

Aby pobrać interfejs **DeviceInformation** z repozytorium modelu publicznego przy użyciu kodu VS:

1. Użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń.

1. Wprowadź **polecenie Plug and Play**, wybierz polecenie Otwórz **repozytorium modelu,** a następnie wybierz pozycję **Otwórz publiczne repozytorium modeli**.

1. Wybierz **pozycję Interfejsy**, a następnie `urn:azureiot:DeviceManagement:DeviceInformation:1`wybierz interfejs informacji o urządzeniu z identyfikatorem, a następnie wybierz pozycję **Pobierz**.

Masz teraz trzy pliki, które tworzą model możliwości urządzenia:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* ŚrodowiskoSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>Publikowanie modelu

Aby narzędzie do odczytywania możliwości urządzenia w programie Azure IoT zostało odczytane, należy opublikować go w repozytorium firmy. Aby opublikować z programu VS Code, potrzebny jest ciąg połączenia dla repozytorium firmy:

1. Przejdź do [portalu Certyfikat platformy Azure dla IoT](https://aka.ms/ACFI).

1. Zaloguj się do portalu za pomocą _konta służbowego_ Microsoft.

1. Wybierz **repozytorium firmowe,** a następnie **parametry połączenia**.

1. Skopiuj parametry połączenia.

Aby otworzyć repozytorium firmy w programie VS Code:

1. Użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń.

1. Wprowadź **pozycję Plug and Play,** a następnie wybierz polecenie **IoT Plug & Play: Open Model Repozytorium.**

1. Wybierz **otwórz repozytorium modeli organizacji** i wklej w ciągu połączenia.

1. Naciśnij **klawisz Enter,** aby otworzyć repozytorium firmowe.

Aby opublikować model możliwości urządzenia i interfejsy w repozytorium firmowym:

1. Użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń.

1. Wprowadź **pozycję Plug and Play,** a następnie wybierz polecenie **IoT Plug & Play: Submit files to Model Repozytorium.**

1. Wybierz pliki **EnvironmentalSensor.interface.json** i **SensorboxModel.capabilitymodel.json** i wybierz **OK**.

Pliki są teraz przechowywane w repozytorium firmowym.

## <a name="generate-code"></a>Generowanie kodu

Za pomocą **narzędzia Azure IoT Tools for VS Code** można wygenerować szkieletowy kod C z modelu. Aby wygenerować kod szkieletu w programie VS Code:

1. Użyj **klawiszy Ctrl+Shift+P,** aby otworzyć paletę poleceń.

1. Wprowadź **pozycję Plug and Play,** a następnie wybierz polecenie **IoT Plug & Play: Generuj** skrót kodu urządzenia.

1. Wybierz plik modelu **możliwości SensorboxModel.capabilitymodel.json.**

1. Wprowadź **sensorbox_app** jako nazwę projektu.

1. Wybierz **ANSI C** jako język.

1. Jako sposób nawiązania połączenia wybierz jako sposób połączenia **opcję Via IoT Hub.**

1. Wybierz **pozycję CMake Project w systemie Windows** jako szablon projektu.

1. Wybierz **via Vcpkg** jako sposób na włączenie sdk urządzenia.

Vs Code generuje szkieletowy kod C i zapisuje pliki w folderze **sensorbox_app** w folderze **modelcode.** Vs Code otwiera nowe okno, które zawiera wygenerowane pliki kodu.

## <a name="update-the-generated-code"></a>Aktualizowanie wygenerowanego kodu

Przed utworzeniem i uruchomieniem kodu należy zaimplementować właściwości, dane telemetryczne i polecenia.

Aby zapewnić implementacje dla kodu wycinka w programie VS Code:

1. Otwórz plik **SensorboxModel_impl.c.**

1. Dodaj kod, aby zaimplementować funkcje ściętego.

1. Zapisz zmiany.

## <a name="build-the-code"></a>Kompilowanie kod

Przed uruchomieniem kodu, aby przetestować urządzenie Typu IoT Plug and Play z centrum Azure IoT hub, należy skompilować kod.

Postępuj zgodnie z instrukcjami zawartymi w pliku **Readme.md** w folderze **sensorbox_app,** aby utworzyć i uruchomić kod w systemie Windows. Poniższa sekcja zawiera instrukcje dotyczące pobierania ciągu połączenia urządzenia do użycia po uruchomieniu kodu urządzenia.

## <a name="test-the-code"></a>Przetestuj kod

Po uruchomieniu kodu łączy się z Centrum IoT i rozpoczyna wysyłanie przykładowych wartości telemetrycznych i właściwości. Urządzenie odpowiada również na polecenia wysyłane z usługi IoT Hub. Aby zweryfikować to zachowanie:

1. Aby utworzyć centrum IoT:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Dodaj urządzenie do centrum IoT hub i pobierz jego parametry połączenia:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Zanotuj parametry połączenia.

1. W wierszu polecenia przejdź do folderu **azure-iot-sdk-c,** w którym został utworzony zestaw SDK i przykłady. Następnie przejdź do folderu **cmake\\sensorbox_app\\Release.**

1. Uruchom następujące polecenie:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Użyj narzędzia Eksploratora IoT platformy Azure do interakcji z urządzeniem Typu Plug and Play IoT połączonym z centrum IoT hub. Aby uzyskać więcej informacji, zobacz [Instalowanie i używanie eksploratora Usługi Azure IoT](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Następne kroki

Po zbudowaniu technologii IoT Plug and Play gotowej do certyfikacji dowiedz się, jak:

> [!div class="nextstepaction"]
> [Tworzenie urządzenia gotowego do certyfikacji](tutorial-build-device-certification.md)
