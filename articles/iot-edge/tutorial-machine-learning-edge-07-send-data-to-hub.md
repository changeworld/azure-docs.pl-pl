---
title: Wyślij dane urządzenie za pośrednictwem przezroczystej bramy — Machine Learning na platformie Azure IoT Edge | Dokumentacja firmy Microsoft
description: Używać komputera deweloperskiego jako symulowane urządzenie usługi IoT Edge do wysyłania danych do usługi IoT Hub na urządzeniu za pośrednictwem urządzenia skonfigurowane jako przezroczystej bramy.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 12793ff28bf13f26bc2cc3d436b644601fc48ac8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081385"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Samouczek: Wysyłanie danych za pośrednictwem przezroczystej bramy

> [!NOTE]
> Ten artykuł jest częścią serii, samouczek dotyczący przy użyciu usługi Azure Machine Learning w usłudze IoT Edge. Jeśli została wyświetlona bezpośrednio w tym artykule, firma Microsoft zachęca rozpoczynać się [najpierw artykuł](tutorial-machine-learning-edge-01-intro.md) z tej serii, aby uzyskać najlepsze wyniki.

W tym artykule używamy ponownie na komputerze deweloperskim jako symulowanego urządzenia, ale zamiast wysyłać dane bezpośrednio do usługi IoT Hub urządzenie wysyła dane do urządzenia usługi IoT Edge skonfigurowane jako przezroczystej bramy.

Możemy monitorować działanie urządzenia usługi IoT Edge, podczas gdy symulowane urządzenie wysyła dane. Po zakończeniu urządzenie działa, przyjrzymy się dane naszych koncie magazynu, aby zweryfikować wszystko zadziałała zgodnie z oczekiwaniami.

Ten krok jest najczęściej wykonywane przez dewelopera chmury lub urządzenia.

## <a name="review-device-harness"></a>Przejrzyj kontroler urządzenia

Ponowne użycie [projektu DeviceHarness](tutorial-machine-learning-edge-03-generate-data.md) symulować podrzędnego (lub liści) urządzenia. Połączenie z przezroczystej bramy wymaga dwie dodatkowe czynności:

* Zarejestruj certyfikat podrzędnego urządzeń (w tym przypadku maszyna deweloperska z naszych) zaufany urząd certyfikacji używany przez środowisko uruchomieniowe usługi IoT Edge.
* Parametry połączenia urządzenia, należy dodać w krawędzi bramy w pełni kwalifikowana nazwa domeny (FQDN).

Spójrz na kod, aby zobaczyć, jak te dwa elementy są implementowane.

1. Na komputerze deweloperskim Otwórz program Visual Studio Code.

2. Użyj **pliku** > **Otwórz Folder...**  otworzyć C:\\źródła\\IoTEdgeAndMlSample\\DeviceHarness.

3. Przyjrzyj się metoda InstallCertificate() w pliku Program.cs.

4. Należy pamiętać, że jeśli kod wykryje ścieżka certyfikatu, wywołuje metodę CertificateManager.InstallCACert, aby zainstalować certyfikat na komputerze.

5. Teraz sprawdźmy GetIotHubDevice metody w klasie TurbofanDevice.

6. Kiedy użytkownik Określa nazwę FQDN przy użyciu bramy "-g" opcja, czy wartość jest przekazywana do tej metody jako gatewayFqdn, która pobiera dołączany do parametrów połączenia urządzenia.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Skompilować i uruchomić urządzenia liścia

1. Z projektem DeviceHarness jest wciąż otwarty w programie Visual Studio Code, skompiluj projekt (Ctrl + Shift + B lub **terminalu** > **uruchomienia zadania kompilacji...** ) i wybierz **kompilacji** z poziomu okna dialogowego.

2. Znajdź w pełni kwalifikowana nazwa domeny (FQDN) dla swojej bramy na krawędzi, przechodząc do maszyny wirtualnej urządzenia usługi IoT Edge w portalu i kopiowanie wartości dla **nazwy DNS** od omówienia.

3. Otwórz terminal programu Visual Studio Code (**terminalu** > **nowym terminalu**) i uruchom następujące polecenie, zastępując `<edge_device_fqdn>` z nazwą DNS, który został skopiowany z maszyny wirtualnej:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Aplikacja próbuje zainstalować certyfikat na komputerze deweloperskim. Gdy tak się stanie, należy zaakceptować ostrzeżenie o zabezpieczeniach.

5. Po wyświetleniu monitu o kliknij parametry połączenia Centrum IoT Hub wielokropek ( **...** ) w usłudze Azure IoT Hub panelu urządzenia i wybierz **parametry połączenia Centrum IoT Hub kopiowania**. Wklej tę wartość do terminala.

6. Zostanie wyświetlone dane wyjściowe, takie jak:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Należy pamiętać, dodanie "GatewayHostName" Parametry połączenia urządzenia, co powoduje, że urządzenia do komunikowania się za pośrednictwem usługi IoT Hub za pomocą przezroczystej bramy usługi IoT Edge.

## <a name="check-output"></a>Sprawdzanie danych wyjściowych

### <a name="iot-edge-device-output"></a>Dane wyjściowe urządzenia usługi IoT Edge

Dane wyjściowe z modułu avroFileWriter można je łatwo obserwować, patrząc na urządzeniu usługi IoT Edge.

1. Nawiąż połączenie z maszyną wirtualną usługi IoT Edge.

2. Wyszukaj pliki zapisane na dysku.

   ```bash
   find /data/avrofiles -type f
   ```

3. Dane wyjściowe polecenia będzie wyglądać następująco:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Masz więcej niż jeden plik w zależności od czasu uruchomienia.

4. Należy zwrócić uwagę na sygnatury czasowe. Moduł avroFileWriter przekazuje te pliki do chmury, gdy czas ostatniej modyfikacji jest większa niż 10 minut w przeszłości (zobacz zmodyfikowane\_pliku\_uploader.py w avroFileWriter module przekroczenie limitu czasu).

5. Po upływie 10 minut, moduł należy przekazać pliki. Jeśli przesyłanie zakończy się pomyślnie, usuwa pliki z dysku.

### <a name="azure-storage"></a>Azure Storage

Można zaobserwować wyników naszego urządzenia liścia wysyłanie danych przez spojrzenie na konta magazynu, w którym oczekujemy, że dane kierowania.

1. Na komputerze deweloperskim Otwórz program Visual Studio Code.

2. W panelu "Usługi AZURE STORAGE" w oknie Eksploruj Przejdź drzewa można znaleźć konta magazynu.

3. Rozwiń **kontenery obiektów Blob** węzła.

4. Z utworu postępowanie w poprzedniej części samouczka, oczekujemy, że **ruldata** kontener może zawierać komunikaty, w których wartość pozostałego czasu eksploatacji. Rozwiń **ruldata** węzła.

5. Zostanie wyświetlony jeden lub więcej plików obiektów blob o nazwie, takich jak: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Kliknij prawym przyciskiem myszy na jeden z plików, a następnie wybierz **pobieranie obiektu Blob** można zapisać pliku na maszynie deweloperskiej.

7. Następnie rozwiń węzeł **uploadturbofanfiles** węzła. W poprzednim artykule, możemy ustawić tę lokalizację jako element docelowy dla plików przekazanych przez moduł avroFileWriter.

8. Kliknij prawym przyciskiem myszy nad plikami, a następnie wybierz **pobieranie obiektu Blob** Aby zapisać je na komputerze deweloperskim.

### <a name="read-avro-file-contents"></a>Avro odczytu zawartości pliku

Dodaliśmy prostego narzędzia wiersza polecenia do odczytu pliku Avro i zwraca ciąg JSON komunikaty w pliku. W tej sekcji firma Microsoft Zainstaluj i uruchom go.

1. Otwórz terminal programu Visual Studio Code (**terminalu** > **nowym terminalu**).

2. Zainstaluj hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Odczytywanie plików Avro, który został pobrany z przy użyciu hubavroreader **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Należy pamiętać, że treść komunikatu wygląda jak firma Microsoft oczekuje z Identyfikatorem urządzenia i przewiduje pozostały czas eksploatacji.

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

5. Tego samego polecenia przekazywania plików Avro, który został pobrany z **uploadturbofanfiles**.

6. Zgodnie z oczekiwaniami, te komunikaty zawierają wszystkie dane z czujników i operacyjne ustawienia z oryginalnej wiadomości. Te dane można używane w celu ulepszenia modelu pozostałego czasu eksploatacji na naszych urządzeniu usługi edge.

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
           "EnqueuedTimeUtc": “<time>”
   }
   ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz eksplorować zasoby używane w ramach tego samouczka end-to-end, poczekaj, aż wszystko będzie gotowe, aby wyczyścić zasoby, które zostały utworzone. Jeśli planujesz kontynuować pracy, umożliwia je usunąć następujące czynności:

1. Usuwanie grupy zasobów, utworzony w celu przechowywania maszyn wirtualnych Dev, IoT Edge z maszyny Wirtualnej, Centrum IoT, konto magazynu, obszar roboczy usługi uczenie maszynowe (i tworzone zasoby: rejestr kontenerów, usługa application insights, magazynu kluczy konta magazynu).

2. Usuń projekt usługi machine learning w [notesy platformy Azure](https://notebooks.azure.com).

3. Jeśli zostało sklonowane repozytorium lokalne, zamknij wszystkie okna programu PowerShell lub programu VS Code, odnoszące się do lokalnego repozytorium, następnie usuń katalog repozytorium.

4. Jeśli utworzono certyfikatów lokalnie, należy usunąć folder c:\\edgeCertificates.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule użyliśmy naszych komputera deweloperskiego do symulacji czujnika wysyłania urządzenia liścia i danych operacyjnych do naszych urządzenia usługi edge. Możemy zweryfikować, czy modułów na urządzeniu kierowane, niejawnych, trwały i najpierw przekazać dane, sprawdzając w czasie rzeczywistym działania urządzenia edge, a następnie, analizując przekazane do konta magazynu.

Więcej informacji można znaleźć na następujących stronach:

* [Podłącz urządzenie z podrzędnych do bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md)
* [Store danych na urządzeniach brzegowych za pomocą usługi Azure Blob Storage na urządzeniach brzegowych IoT Edge (wersja zapoznawcza)](how-to-store-data-blob.md)
