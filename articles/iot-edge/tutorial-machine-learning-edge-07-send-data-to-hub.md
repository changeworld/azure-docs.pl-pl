---
title: 'Samouczek: Wysyłanie danych urządzenia za pośrednictwem przezroczystej bramy — uczenie maszynowe w usłudze Azure IoT Edge'
description: W tym samouczku pokazano, jak można używać komputera deweloperującego jako symulowanego urządzenia usługi IoT Edge do wysyłania danych do centrum IoT Hub, przechodząc przez urządzenie skonfigurowane jako przezroczysta brama.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 50f339b257110f0a5dc0ac08b9f40043ee384afb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706909"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Samouczek: Wysyłanie danych za pośrednictwem przezroczystej bramy

> [!NOTE]
> Ten artykuł jest częścią serii samouczka na temat korzystania z usługi Azure Machine Learning w usłudze IoT Edge. Jeśli dotarłeś do tego artykułu bezpośrednio, zachęcamy do rozpoczęcia [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii, aby uzyskać najlepsze wyniki.

W tym artykule ponownie używamy komputera deweloperującego jako symulowanego urządzenia, ale zamiast wysyłać dane bezpośrednio do centrum IoT Hub urządzenie wysyła dane do urządzenia usługi IoT Edge skonfigurowanego jako przezroczysta brama.

Monitorujemy działanie urządzenia Usługi IoT Edge podczas wysyłania danych przez symulowane urządzenie. Po zakończeniu pracy urządzenia przyjrzymy się danym na naszym koncie magazynu, aby zweryfikować wszystko, co działało zgodnie z oczekiwaniami.

Ten krok jest zazwyczaj wykonywane przez dewelopera chmury lub urządzenia.

## <a name="review-device-harness"></a>Przegląd uprzęży urządzenia

Ponownie użyć [DeviceHarness projektu](tutorial-machine-learning-edge-03-generate-data.md) do symulacji urządzenia niższego rzędu (lub liścia). Połączenie z przezroczystą bramą wymaga dwóch dodatkowych rzeczy:

* Zarejestruj certyfikat, aby urządzenie podrzędne (w tym przypadku nasz komputer deweloperski) ufało urzędowi certyfikacji używanej przez środowisko uruchomieniowe IoT Edge.
* Dodaj bramę krawędzi w pełni kwalifikowaną nazwę domeny (FQDN) do ciągu połączenia urządzenia.

Spójrz na kod, aby zobaczyć, jak te dwa elementy są implementowane.

1. Na komputerze deweloperskim otwórz program Visual Studio Code.

2. Użyj **folderu otwierania** > **pliku...** aby otworzyć C:\\źródło\\\\IoTEdgeAndMlSample DeviceHarness.

3. Spójrz na InstallCertificate() metoda w Program.cs.

4. Należy zauważyć, że jeśli kod znajdzie ścieżkę certyfikatu, wywołuje metodę CertificateManager.InstallCACert, aby zainstalować certyfikat na komputerze.

5. Teraz spójrz na GetIotHubDevice metody w Klasie TurbofanDevice.

6. Gdy użytkownik określa nazwy FQDN bramy przy użyciu opcji "-g", ta wartość jest przekazywana do tej metody jako gatewayFqdn, który pobiera dołączane do ciągu połączenia urządzenia.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Tworzenie i uruchamianie urządzenia liścia

1. Z DeviceHarness projektu nadal otwarte w programie Visual Studio Code, skompilować projekt (Ctrl + Shift + B lub **Terminal** > **Run Build Task ...**) i wybierz **Build** z okna dialogowego.

2. Znajdź w pełni kwalifikowaną nazwę domeny (FQDN) dla bramy brzegowej, przechodząc do maszyny wirtualnej urządzenia Usługi IoT Edge w portalu i kopiując wartość **nazwy DNS** z przeglądu.

3. Otwórz terminal Kodu programu Visual Studio **(Terminal** > **Nowy terminal)** i uruchom następujące polecenie, zastępując `<edge_device_fqdn>` nazwą DNS skopiowaną z maszyny wirtualnej:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Aplikacja próbuje zainstalować certyfikat na komputerze deweloperskim. W tym przypadku zaakceptuj ostrzeżenie o zabezpieczeniach.

5. Po wyświetleniu monitu o parametry połączenia usługi IoT Hub kliknij wielokropek (**...**) w panelu Urządzenia usługi Azure IoT Hub i wybierz **pozycję Kopiuj ciąg połączenia usługi IoT Hub**. Wklej wartość do terminala.

6. Zobaczysz dane wyjściowe, takie jak:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Należy zwrócić uwagę na dodanie "GatewayHostName" do ciągu połączenia urządzenia, co powoduje, że urządzenie do komunikowania się za pośrednictwem usługi IoT Hub za pośrednictwem bramy przezroczystej usługi IoT Edge.

## <a name="check-output"></a>Sprawdź dane wyjściowe

### <a name="iot-edge-device-output"></a>Wyjście urządzenia IoT Edge

Dane wyjściowe z modułu avroFileWriter można łatwo zaobserwować, patrząc na urządzenie IoT Edge.

1. SSH do maszyny wirtualnej Usługi IoT Edge.

2. Poszukaj plików zapisanych na dysku.

   ```bash
   find /data/avrofiles -type f
   ```

3. Dane wyjściowe polecenia będą wyglądać następująco:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Może mieć więcej niż jeden plik w zależności od czasu uruchomienia.

4. Zwróć uwagę na znaczniki czasu. Moduł avroFileWriter przesyła pliki do chmury, gdy ostatni czas modyfikacji jest więcej niż 10 minut w przeszłości (patrz ZMODYFIKOWANY\_LIMIT CZASU PLIKU\_w uploader.py w module avroFileWriter).

5. Po upływie 10 minut moduł powinien przesłać pliki. Jeśli przekazywanie zakończy się pomyślnie, usuwa pliki z dysku.

### <a name="azure-storage"></a>Azure Storage

Możemy obserwować wyniki wysyłania danych przez nasze urządzenie liściowe, patrząc na konta pamięci masowej, na których oczekujemy, że dane zostaną przekierowane.

1. Na komputerze deweloperskim otwórz program Visual Studio Code.

2. W panelu "AZURE STORAGE" w oknie eksplorowania przejdź do drzewa, aby znaleźć swoje konto magazynu.

3. Rozwiń węzeł **Kontenery obiektów blob.**

4. Z pracy zrobiliśmy w poprzedniej części samouczka, oczekujemy, że kontener **ruldata** powinien zawierać wiadomości z RUL. Rozwiń węzeł **ruldata.**

5. Zostanie wyświetlony jeden lub więcej plików `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`obiektów blob o nazwach: .

6. Kliknij prawym przyciskiem myszy jeden z plików i wybierz **polecenie Pobierz obiekt Blob,** aby zapisać plik na komputerze deweloperskim.

7. Następnie rozwiń węzeł **uploadturbofanfiles.** W poprzednim artykule ustawiamy tę lokalizację jako miejsce docelowe dla plików przesłanych przez moduł avroFileWriter.

8. Kliknij prawym przyciskiem myszy pliki i wybierz **pobierz obiekt Blob,** aby zapisać go na komputerze deweloperskim.

### <a name="read-avro-file-contents"></a>Odczyt zawartości pliku Avro

Załączyliśmy proste narzędzie wiersza polecenia do odczytu pliku Avro i zwracania ciągu JSON wiadomości w pliku. W tej sekcji zainstalujemy go i uruchomimy.

1. Otwórz terminal w programie Visual Studio Code (**Terminal** > **Nowy terminal**).

2. Zainstaluj hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Użyj hubavroreader, aby odczytać plik Avro, który został pobrany z **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Należy zauważyć, że treść wiadomości wygląda zgodnie z oczekiwaniami z identyfikatorem urządzenia i przewidywanym RUL.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

5. Uruchom to samo polecenie przekazując plik Avro, który został pobrany z **uploadturbofanfiles**.

6. Zgodnie z oczekiwaniami te komunikaty zawierają wszystkie dane czujnika i ustawienia operacyjne z oryginalnej wiadomości. Dane te mogą być wykorzystane do ulepszenia modelu RUL na naszym urządzeniu brzegowym.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz eksplorować zasoby używane przez ten samouczek end-to-end, poczekaj, aż skończysz, aby oczyścić utworzone zasoby. Jeśli nie zamierzasz kontynuować, wykonaj następujące kroki, aby je usunąć:

1. Usuń grupy zasobów utworzone w celu przechowywania maszyny Wirtualnej deweloperów, maszyny Wirtualnej usługi IoT Edge, centrum IoT Hub, konta magazynu, usługi obszaru roboczego uczenia maszynowego (i utworzonych zasobów: rejestru kontenerów, wglądu w aplikacje, magazynu kluczy, konta magazynu).

2. Usuń projekt uczenia maszynowego w [notesach platformy Azure](https://notebooks.azure.com).

3. Jeśli repozytorium zostanie sklonowane lokalnie, zamknij wszystkie okna programu PowerShell lub VS Code odnoszące się do lokalnego repozytorium, a następnie usuń katalog repozytorium.

4. Jeśli certyfikaty zostały utworzone lokalnie,\\usuń folder c: edgeCertificates.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyliśmy naszej maszyny programistycznej do symulacji czujnika wysyłania urządzenia liściowego i danych operacyjnych do naszego urządzenia brzegowego. Sprawdziliśmy, czy moduły na urządzeniu są kierowane, klasyfikowane, utrwalane i przesyłane najpierw przez sprawdzenie działania urządzenia brzegowego w czasie rzeczywistym, a następnie przeglądanie plików przesłanych na konto magazynu.

Więcej informacji można znaleźć na następujących stronach:

* [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)
* [Przechowywanie danych na urządzeniach brzegowych za pomocą usługi Azure Blob Storage w usłudze IoT Edge (wersja zapoznawcza)](how-to-store-data-blob.md)
