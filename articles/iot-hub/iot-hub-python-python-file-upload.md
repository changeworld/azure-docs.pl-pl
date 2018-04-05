---
title: Przekazywanie plików z urządzenia do Centrum IoT Azure języka Python | Dokumentacja firmy Microsoft
description: Sposób przekazywania plików z urządzenia do chmury przy użyciu urządzenia Azure IoT SDK dla języka Python. Przekazano pliki są przechowywane w kontenerze obiektu blob magazynu Azure.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: kgremban
ms.openlocfilehash: 7f64783f5e1c79436b671ef98f30f5e3594b94e6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Przekazywanie plików z urządzenia do chmury z Centrum IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym samouczku wykonuje sposób użycia [pliku możliwości przekazywania Centrum IoT](iot-hub-devguide-file-upload.md) można przekazać pliku do [magazynu obiektów blob Azure](../storage/index.yml). Samouczek pokazuje, jak do:

- Bezpiecznie przekazać kontener magazynu do przekazywania pliku.
- Korzystając z klienta Python do przekazania pliku za pośrednictwem Centrum IoT.

[Rozpoczynanie pracy z Centrum IoT](iot-hub-node-node-getstarted.md) samouczku został omówiony podstawowych funkcji obsługi komunikatów urządzenia do chmury Centrum IoT. Jednak w niektórych scenariuszach nie można łatwo mapować dane, które Twojego urządzenia wysyłają do stosunkowo mały wiadomości urządzenia do chmury, które akceptuje Centrum IoT. Gdy trzeba wyżynne pliki z urządzenia, można nadal używać bezpieczeństwa i niezawodności Centrum IoT.

> [!NOTE]
> Zestaw SDK Python Centrum IoT aktualnie obsługuje tylko przekazywanie plików opartego na znakach, takich jak **.txt** plików.

Na końcu tego samouczka możesz uruchomić aplikację konsoli języka Python:

* **FileUpload.py**, która przekazuje plik do zestawu SDK Python urządzenia pamięci masowej.

> [!NOTE]
> Centrum IoT obsługuje wiele platform urządzeń i języków (w tym C, .NET, Javascript, Python i Java) za pośrednictwem zestawy SDK urządzenia Azure IoT. Zapoznaj się [Azure IoT Developer Center] instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do Centrum IoT Azure.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](http://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)


[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>Przekaż plik z aplikacji przez urządzenia

W tej sekcji zostanie utworzona aplikacja urządzenia, aby przekazać plik z Centrum IoT.

1. Z wiersza polecenia, uruchom następujące polecenie, aby zainstalować **urządzenia klienta, azure Centrum iothub w-** pakietu:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Za pomocą edytora tekstu, Utwórz **FileUpload.py** pliku w folderze roboczym.

1. Dodaj następujące `import` instrukcje i zmienne na początku **FileUpload.py** pliku. Zastąp `deviceConnectionString` z parametrami połączenia urządzenia Centrum IoT:

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name on storage after upload]"
    ```

1. Tworzenie wywołań zwrotnych dla **upload_blob** funkcji:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. Dodaj następujący kod, aby połączyć klienta i przekazać plik. Obejmuje to też `main` procedury:

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

1. Zapisz i Zamknij **UploadFile.py** pliku.

1. Skopiuj przykładowy plik tekstowy do folderu roboczego i zmień jego nazwę `sample.txt`.

    > [!NOTE]
    > Zestaw SDK Python Centrum IoT aktualnie obsługuje tylko przekazywanie plików opartego na znakach, takich jak **.txt** plików.


## <a name="run-the-application"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w folderze pracy uruchom następujące polecenie:

    ```cmd/sh
    python FileUpload.py
    ```

1. Poniższy zrzut ekranu przedstawia dane wyjściowe z **przekazywaniem plików** aplikacji:

    ![Dane wyjściowe z aplikacji symulowane urządzenie](./media/iot-hub-python-python-file-upload/1.png)

1. Portal służy do wyświetlania przekazany plik w kontenerze magazynu, które zostały skonfigurowane:

    ![Przekazany plik](./media/iot-hub-python-python-file-upload/2.png)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób korzystania z funkcji przekazywania pliku Centrum IoT uprościć przekazywania plików z urządzeń. Można kontynuować poznać funkcje Centrum IoT i scenariusze z następujących artykułów:

* [Programowo tworzenia Centrum IoT][lnk-create-hub]
* [Wprowadzenie do zestawu SDK C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

<!-- Links -->
[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
