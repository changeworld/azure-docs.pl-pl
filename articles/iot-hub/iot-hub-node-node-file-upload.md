---
title: Przekazywanie plików z urządzenia do Centrum IoT Azure z węzłem | Dokumentacja firmy Microsoft
description: Sposób przekazywania plików z urządzenia do chmury przy użyciu urządzenia Azure IoT SDK dla środowiska Node.js. Przekazano pliki są przechowywane w kontenerze obiektu blob magazynu Azure.
services: iot-hub
documentationcenter: nodejs
author: msebolt
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo
ms.openlocfilehash: dd6f25173806d6e420bfeebaae3f81e39f8fd97f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Przekazywanie plików z urządzenia do chmury z Centrum IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym samouczku opiera się na kodzie w [wysyłać chmury do urządzenia z Centrum IoT](iot-hub-node-node-c2d.md) samouczek pokazanie sposobu używania [pliku możliwości przekazywania Centrum IoT](iot-hub-devguide-file-upload.md) można przekazać pliku do [magazynu obiektów blob Azure](../storage/index.yml). Samouczek pokazuje, jak do:

- Bezpieczne udostępnianie urządzenia platformy Azure blob identyfikatora URI pobierania pliku.
- Powiadomienia o przekazywania plików Centrum IoT umożliwia wyzwalanie przetwarzania plików w sieci wewnętrznej aplikacji.

[Rozpoczynanie pracy z Centrum IoT](iot-hub-node-node-getstarted.md) samouczku został omówiony podstawowych funkcji obsługi komunikatów urządzenia do chmury Centrum IoT. Jednak w niektórych scenariuszach nie można łatwo mapować dane, które Twojego urządzenia wysyłają do stosunkowo mały wiadomości urządzenia do chmury, które akceptuje Centrum IoT. Na przykład:

* Duże pliki, które zawierają obrazów
* Filmy wideo
* Dane wibrację próbkowanych zgodnie o dużej częstotliwości
* Niektóre formularz wstępnie przetworzonych danych.

Pliki te są zwykle partii przetwarzania w chmurze za pomocą narzędzi takich jak [fabryki danych Azure](../data-factory/introduction.md) lub [Hadoop](../hdinsight/index.md) stosu. Gdy trzeba wyżynne pliki z urządzenia, można nadal używać bezpieczeństwa i niezawodności Centrum IoT.

Na końcu tego samouczka możesz uruchomić dwóch aplikacji konsoli Node.js:

* **SimulatedDevice.js**, która przekazuje plik do magazynu przy użyciu identyfikatora URI połączenia SAS udostępniane przez Centrum IoT.
* **ReadFileUploadNotification.js**, która odbiera powiadomienia o przekazywania plików z Centrum IoT.

> [!NOTE]
> Centrum IoT obsługuje wiele platform urządzeń i języków (w tym C, .NET, Javascript, Python i Java) za pośrednictwem zestawy SDK urządzenia Azure IoT. Zapoznaj się [Azure IoT Developer Center] instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do Centrum IoT Azure.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko Node.js w wersji 4.0.x lub nowszej.
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](http://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekaż plik z aplikacji przez urządzenia

W tej sekcji zostanie utworzona aplikacja urządzenia, aby przekazać plik z Centrum IoT.

1. Utwórz pusty folder o nazwie ```simulateddevice```.  W folderze ```simulateddevice``` utwórz plik package.json, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

1. W wierszu polecenia w folderze ```simulateddevice``` uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iot-device** i pakiet **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Za pomocą edytora tekstów utwórz plik **SimulatedDevice.js** w folderze ```simulateddevice```.

1. Dodaj następujące instrukcje ```require``` na początku pliku **SimulatedDevice.js**:

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Dodaj zmienną ```deviceconnectionstring``` i użyj jej do utworzenia wystąpienia **Client**.  Zastąp ```{deviceconnectionstring}``` nazwą urządzenia został utworzony w _tworzenia Centrum IoT_ sekcji:

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Dla uproszczenia należy ciągu połączenia jest uwzględnione w kodzie: to nie jest zalecanym rozwiązaniem i w zależności od architektura i przypadek użycia warto wziąć pod uwagę bardziej bezpieczne metody przechowywania ten klucz tajny.

1. Dodaj następujący kod, aby połączyć klienta:

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Wywołanie zwrotne tworzenia i używania **uploadToBlob** funkcji można przekazać pliku.

    ```nodejs
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);
    
        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

1. Zapisz i zamknij plik **SimulatedDevice.js**.

1. Skopiuj plik obrazu do `simulateddevice` folder i zmień jego nazwę `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Otrzymasz powiadomienie przekazywania pliku

W tej sekcji utworzysz aplikację konsoli Node.js, który odbiera komunikaty powiadomień dotyczących przekazywania plików z Centrum IoT.

Można użyć **iothubowner** parametrów połączenia z Centrum IoT do ukończenia tej sekcji. Można znaleźć w ciągu połączenia [portalu Azure](https://portal.azure.com/) na **zasady dostępu współużytkowanego** bloku.

1. Utwórz pusty folder o nazwie ```fileuploadnotification```.  W folderze ```fileuploadnotification``` utwórz plik package.json, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

1. Z wiersza polecenia w ```fileuploadnotification``` folderu, uruchom następujące polecenie, aby zainstalować **Centrum iothub azure** pakiet SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Za pomocą edytora tekstu, Utwórz **FileUploadNotification.js** w pliku ```fileuploadnotification``` folderu.

1. Dodaj następujące ```require``` instrukcje na początku **FileUploadNotification.js** pliku:

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Dodaj zmienną ```iothubconnectionstring``` i użyj jej do utworzenia wystąpienia **Client**.  Zastąp ```{iothubconnectionstring}``` ciągu połączenia z Centrum IoT utworzony w _tworzenia Centrum IoT_ sekcji:

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Dla uproszczenia należy ciągu połączenia jest uwzględnione w kodzie: to nie jest zalecanym rozwiązaniem i w zależności od architektura i przypadek użycia warto wziąć pod uwagę bardziej bezpieczne metody przechowywania ten klucz tajny.

1. Dodaj następujący kod, aby połączyć klienta:

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Otwórz klienta i użyj **getFileNotificationReceiver** funkcji, aby otrzymywać aktualizacje stanu.

    ```nodejs
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Zapisz i Zamknij **FileUploadNotification.js** pliku.

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

W wierszu polecenia `fileuploadnotification` folderu, uruchom następujące polecenie:

```cmd/sh
node FileUploadNotification.js
```

W wierszu polecenia `simulateddevice` folderu, uruchom następujące polecenie:

```cmd/sh
node SimulatedDevice.js
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z **SimulatedDevice** aplikacji:

![Dane wyjściowe z aplikacji symulowane urządzenie](./media/iot-hub-node-node-file-upload/simulated-device.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z **FileUploadNotification** aplikacji:

![Dane wyjściowe z aplikacji odczytu pliku przekazywania — powiadomienie](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Portal służy do wyświetlania przekazany plik w kontenerze magazynu, które zostały skonfigurowane:

![Przekazany plik](./media/iot-hub-node-node-file-upload/uploaded-file.png)

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
