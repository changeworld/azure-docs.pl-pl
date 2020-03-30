---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub za pomocą języka Python | Dokumenty firmy Microsoft
description: Jak przekazać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzenia Usługi Azure IoT dla języka Python. Przekazane pliki są przechowywane w kontenerze obiektów blob magazynu platformy Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110747"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym artykule pokazano, jak użyć [możliwości przekazywania plików usługi IoT Hub](iot-hub-devguide-file-upload.md) do przekazywania pliku do [magazynu obiektów blob platformy Azure](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

* Bezpiecznie podaj kontener magazynu do przekazywania pliku.

* Użyj klienta języka Python, aby przekazać plik za pośrednictwem centrum IoT Hub.

Funkcja [Wyślij dane telemetryczne z urządzenia do szybkiego startu centrum IoT](quickstart-send-telemetry-python.md) hub pokazuje podstawowe funkcje obsługi wiadomości między urządzeniami w chmurze usługi IoT Hub. Jednak w niektórych scenariuszach nie można łatwo mapować danych wysyłanych przez urządzenia do komunikatów stosunkowo małych urządzeń do chmury akceptowanych przez usługę IoT Hub. Gdy potrzebujesz plików wyżynnych z urządzenia, nadal możesz korzystać z zabezpieczeń i niezawodności usługi IoT Hub.

> [!NOTE]
> Zestaw IoT Hub Python SDK obsługuje obecnie tylko przesyłanie plików opartych na znakach, takich jak pliki **txt.**

Na końcu tego samouczka uruchomisz aplikację konsoli Python:

* **FileUpload.py**, który przesyła plik do magazynu przy użyciu SDK urządzenia Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> W tym przewodniku użyto przestarzałego SDK języka Python w języku V1, ponieważ funkcja przekazywania plików nie została jeszcze zaimplementowana w nowym SDK V2.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji utworzysz aplikację urządzenia, aby przekazać plik do centrum IoT Hub.

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **azure-iothub-device-client:**

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Za pomocą edytora tekstu utwórz plik testowy, który zostanie przekazany do magazynu obiektów blob.

    > [!NOTE]
    > Zestaw IoT Hub Python SDK obsługuje obecnie tylko przesyłanie plików opartych na znakach, takich jak pliki **txt.**

3. Za pomocą edytora tekstu utwórz plik **FileUpload.py** w folderze roboczym.

4. Dodaj następujące `import` instrukcje i zmienne na początku pliku **FileUpload.py.** 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. W pliku zastąp `[Device Connection String]` ciągiem połączenia urządzenia centrum IoT. Zamień `[Full path to file]` ścieżkę do utworzonego pliku testowego lub dowolnego pliku na urządzeniu, który chcesz przekazać. Zamień `[File name for storage]` nazwę, którą chcesz nadać plikowi po jego przekazaniu do magazynu obiektów blob. 

6. Utwórz wywołanie zwrotne dla funkcji **upload_blob:**

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Dodaj następujący kod, aby połączyć klienta i przekazać plik. Obejmują również `main` rutynowe:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Zapisz i zamknij plik **UploadFile.py.**

## <a name="run-the-application"></a>Uruchamianie aplikacji

Teraz możesz przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w folderze roboczym uruchom następujące polecenie:

    ```cmd/sh
    python FileUpload.py
    ```

2. Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **FileUpload:**

    ![Dane wyjściowe z aplikacji symulowanego urządzenia](./media/iot-hub-python-python-file-upload/1.png)

3. Za pomocą portalu można wyświetlić przekazany plik w skonfigurowanym kontenerze magazynu:

    ![Przesłany plik](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak korzystać z możliwości przekazywania plików usługi IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz kontynuować eksplorowanie funkcji i scenariuszy centrum IoT za pomocą następujących artykułów:

* [Programowo tworzenie centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do C SDK](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)
