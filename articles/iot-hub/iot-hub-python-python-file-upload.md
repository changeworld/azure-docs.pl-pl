---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub za pomocą języka Python | Dokumentacja firmy Microsoft
description: Sposób przekazywania plików z urządzenia do chmury przy użyciu zestawu SDK urządzeń Azure IoT dla języka Python. Przekazane pliki są przechowywane w kontenerze obiektów blob usługi Azure storage.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: kgremban
ms.openlocfilehash: 23b0a2ac8e0264ddc1592479759cc8398d9ef5f8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621262"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym artykule pokazano, jak [pliku przekazywania możliwościami usługi IoT Hub](iot-hub-devguide-file-upload.md) można przekazać pliku do [usługi Azure blob storage](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

* Kontener magazynu można bezpiecznie przekazać do przekazywania pliku.

* Użyj klienta języka Python, aby przekazać plik za pomocą usługi IoT hub.

[Wysyłanie danych telemetrycznych z urządzenia do usługi IoT hub](quickstart-send-telemetry-python.md) Przewodnik Szybki Start przedstawia podstawowe funkcje obsługi komunikatów urządzenia do chmury usługi IoT Hub. Jednak w niektórych scenariuszach nie pozwala na łatwe mapowanie danych wysyłanych przez urządzenia do stosunkowo mały wiadomości urządzenia do chmury, które akceptuje usługi IoT Hub. Gdy zachodzi potrzeba wyżynne plików z urządzenia, można nadal używać zabezpieczeń i niezawodności usługi IoT Hub.

> [!NOTE]
> IoT Hub Python SDK aktualnie obsługuje tylko przekazywania plików opartego na znakach, takich jak **.txt** plików.

Na końcu tego samouczka, możesz uruchomić aplikację konsoli języka Python:

* **FileUpload.py**, która przekazuje plik do magazynu przy użyciu zestawu SDK urządzenia środowiska Python.

> [!NOTE]
> Centrum IoT Hub obsługuje wiele platform urządzeń i językach (w tym C, .NET, Javascript, Python i Java) za pomocą zestawów SDK urządzeń Azure IoT. Zapoznaj się [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot) instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do usługi Azure IoT Hub.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Python 2.x lub 3.x](https://www.python.org/downloads/). Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*](https://pip.pypa.io/en/stable/installing/).

* Zainstaluj [Pakiet redystrybucyjny języka Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* Centrum IoT hub w ramach swojego konta platformy Azure przy użyciu tożsamości urządzenia do testowania funkcji przekazywania plików. 

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekaż plik z aplikacji urządzenia

W tej sekcji opisano tworzenie aplikacji urządzenia, aby przekazać plik do usługi IoT hub.

1. W wierszu polecenia, uruchom następujące polecenie, aby zainstalować **azure-iothub-device-client** pakietu:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Za pomocą edytora tekstu Utwórz plik testowy, który zostanie przekazany do magazynu obiektów blob.

    > [!NOTE]
    > IoT Hub Python SDK aktualnie obsługuje tylko przekazywania plików opartego na znakach, takich jak **.txt** plików.

3. Za pomocą edytora tekstów Utwórz **FileUpload.py** pliku w folderze roboczym.

4. Dodaj następujący kod `import` instrukcji i zmienne na początku **FileUpload.py** pliku. 

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

5. W pliku, Zastąp `[Device Connection String]` przy użyciu parametrów połączenia urządzenia Centrum IoT. Zastąp `[Full path to file]` ze ścieżką do pliku testu, który został utworzony lub dowolnego pliku na urządzeniu, które chcesz przekazać. Zastąp `[File name for storage]` nazwą, której chcesz nadać do pliku po został przekazany do magazynu obiektów blob. 

6. Utwórz wywołanie zwrotne dla **upload_blob** funkcji:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Dodaj następujący kod do połączenia klienta, a następnie przekaż plik. Również obejmować `main` procedury:

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

8. Zapisz i Zamknij **UploadFile.py** pliku.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w folderze roboczym uruchom następujące polecenie:

    ```cmd/sh
    python FileUpload.py
    ```

2. Poniższy zrzut ekranu przedstawia dane wyjściowe z **FileUpload** aplikacji:

    ![Dane wyjściowe z aplikacji symulowane urządzenia](./media/iot-hub-python-python-file-upload/1.png)

3. Aby wyświetlić przekazany plik w kontenerze magazynu, które zostały skonfigurowane, można użyć portalu:

    ![Przekazany plik](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia funkcji przekazywania plików usługi IoT Hub można uproszczenie przekazywania plików z urządzeń. Możesz kontynuować poznawanie funkcji Centrum IoT i scenariusze z następujących artykułów:

* [Programistyczne tworzenie Centrum IoT hub](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)
