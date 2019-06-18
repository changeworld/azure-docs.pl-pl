---
title: Tworzenie i wdrażanie niestandardowych modułów — Machine Learning na platformie Azure IoT Edge | Dokumentacja firmy Microsoft
description: Tworzenie i wdrażanie moduły usługi IoT Edge, przetwarzanie danych z urządzeń liścia za pośrednictwem modelu uczenia maszynowego, które następnie możesz wysłać szczegółowe informacje do usługi IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6c4636fe370a4046b1c5020aee249529f1498639
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155526"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Samouczek: Tworzenie i wdrażanie niestandardowych modułów usługi IoT Edge

> [!NOTE]
> Ten artykuł jest częścią serii, samouczek dotyczący przy użyciu usługi Azure Machine Learning w usłudze IoT Edge. Jeśli została wyświetlona bezpośrednio w tym artykule, firma Microsoft zachęca rozpoczynać się [najpierw artykuł](tutorial-machine-learning-edge-01-intro.md) z tej serii, aby uzyskać najlepsze wyniki.

W tym artykule możemy utworzyć trzy moduły usługi IoT Edge, które odbiera komunikaty z urządzenia typu liść, uruchomić je za pośrednictwem modelu uczenia maszynowego i następnie przekazywać informacje do usługi IoT Hub.

Centrum usługi IoT Edge usprawnia komunikację na moduł. Za pomocą usługi IoT Edge hub jako broker komunikatów przechowuje moduły niezależni od siebie nawzajem. Moduły muszą tylko określić danych wejściowych, które akceptują wiadomości i danych wyjściowych, do których one zapisywania komunikatów.

Chcemy, aby urządzenia usługi IoT Edge, aby wykonać cztery czynności dla nas:

* Odbieranie danych z urządzenia typu liść
* Prognozowanie pozostałego czasu eksploatacji dla urządzenia, które wysłane dane
* Wysyłanie wiadomości z tylko pozostałego czasu eksploatacji urządzenia do usługi IoT Hub (Ta funkcja może zostać zmodyfikowany do wysyłania danych tylko, jeśli wartość pozostałego czasu eksploatacji spadnie poniżej pewnego poziomu)
* Zapisz dane urządzenia liścia pliku lokalnego na urządzeniu usługi IoT Edge. Ten plik danych jest okresowo przekazywane do usługi IoT Hub przy użyciu przekazywania pliku, aby zawęzić szkolenie modelu uczenia maszynowego. Przy użyciu przekazywania pliku zamiast stałej komunikat przesyłania strumieniowego jest bardziej opłacalna.

Aby wykonać te zadania, użyjemy trzech moduły niestandardowe:

* **Klasyfikator pozostałego czasu eksploatacji:** Moduł turboFanRulClassifier utworzonego w [szkolenie i wdrożyć model usługi Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) to standardowa maszyna modułu nauczania, która udostępnia dane wejściowe o nazwie "amlInput" i dane wyjściowe o nazwie "amlOutput". "amlInput" oczekuje, że dane wejściowe, aby wyglądał dokładnie tak jak dane wejściowe, który wysłaliśmy do usługi sieci web opartych na usłudze ACI. Podobnie "amlOutput" zwraca tych samych danych jako usługę sieci web.

* **Moduł zapisujący systemu Avro:** Ten moduł odbiera wiadomości na dane wejściowe "avroModuleInput" i będzie się powtarzał wiadomości w formacie Avro dysku nowsze przekazać go do usługi IoT Hub.

* **Moduł routera:** Moduł router odbiera komunikaty z podrzędnymi urządzeniami liścia, a następnie formatuje i wysyła komunikaty do klasyfikatora. Moduł następnie odbiera komunikaty z klasyfikatora i przesyła wiadomość na moduł zapisu Avro. Na koniec moduł wysyła tylko Prognozowanie pozostałego czasu eksploatacji w Centrum IoT Hub.

  * Dane wejściowe:
    * **deviceInput**: odbiera komunikaty z urządzenia typu liść
    * **rulInput:** odbiera komunikaty z "amlOutput"

  * Dane wyjściowe:
    * **klasyfikowanie:** wysyła wiadomości do "amlInput"
    * **writeAvro:** wysyła komunikaty "avroModuleInput"
    * **toIotHub:** wysyła komunikaty do $nadrzędne, które przekazuje komunikaty do połączonego Centrum IoT

Na poniższym diagramie przedstawiono modułów, danych wejściowych, danych wyjściowych i usługi IoT Edge Hub trasy dla całego rozwiązania:

![Diagram architektury usługi IoT Edge trzy modułów](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Kroki opisane w tym artykule są najczęściej wykonywane przez dewelopera w chmurze.

## <a name="create-a-new-iot-edge-solution"></a>Utwórz nowe rozwiązanie IoT Edge

Podczas wykonywania drugiego naszych dwa notesy platformy Azure możemy utworzonych i opublikowanych obraz kontenera zawierający nasz model pozostałego czasu eksploatacji. Usługa Azure Machine Learning, jako część procesu tworzenia obrazu wbudowane elementy, aby utworzyć obraz można wdrożyć jako moduł usługi Azure IoT Edge. W tym kroku firma Microsoft zamierza utworzyć rozwiązanie Azure IoT Edge, przy użyciu modułu "Azure Machine Learning" i wskaż moduł obraz, który opublikowaliśmy przy użyciu notesów usługi Azure.

1. Otwórz sesję pulpitu zdalnego na maszynie deweloperskiej.

2. Otwórz folder **C:\\źródła\\IoTEdgeAndMlSample** w programie Visual Studio Code.

3. Kliknij prawym przyciskiem myszy w panelu explorer (w puste miejsce), a następnie wybierz pozycję **nowe rozwiązanie graniczne IoT**.

    ![Utwórz nowe rozwiązanie IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Zaakceptuj domyślną nazwę rozwiązania **EdgeSolution**.

5. Wybierz **usługi Azure Machine Learning** jako szablon modułu.

6. Nazwa modułu **turbofanRulClassifier**.

7. Wybierz Twojego obszaru roboczego usługi machine learning.

8. Wybierz obraz, który został utworzony podczas uruchamiania notesu platformy Azure.

9. Spójrz na rozwiązanie i zwróć uwagę, pliki, które zostały utworzone:

   * **deployment.template.json:** Ten plik zawiera definicję każdego z modułów w rozwiązaniu. Istnieją trzy sekcje, aby zwrócić uwagę na w tym pliku:

     * **Poświadczenia rejestru:** Definiuje zestaw rejestry kontenerów niestandardowych, którego używasz w rozwiązaniu. Po prawej stronie teraz powinien zawierać rejestru z Twojego obszaru roboczego uczenia maszynowego, czyli przechowywania obrazu usługi Azure Machine Learning. Może mieć dowolną liczbę rejestry kontenerów, ale dla uproszczenia używamy tego jednego rejestru dla wszystkich modułów

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io”
         }
       }
       ```

     * **Moduły:** Ta sekcja zawiera zestaw modułów zdefiniowanych przez użytkownika, które bardziej szczegółowo za pomocą tego rozwiązania. Można zauważyć, że ta sekcja zawiera obecnie dwa moduły: tempSensor i turbofanRulClassifier. TempSensor został zainstalowany przez szablon programu Visual Studio Code, ale nie potrzebujemy jej dla tego rozwiązania. Możesz usunąć tempSensor definicji modułu z sekcji modules. Należy pamiętać, że definicji modułu turbofanRulClassifier wskazuje obraz w rejestrze kontenera. Ponieważ nieustannie dodajemy więcej modułów do rozwiązania będą wyświetlani w tej sekcji.

       ```json
       "modules": {
         "tempSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Trasy:** będziemy używać strony z trasami dość coś w ramach tego samouczka. Umożliwia zdefiniowanie tras, jak moduły komunikują się ze sobą. Dwie trasy definiowane przez szablon nie są zgodne z routingiem, której potrzebujesz. Pierwsza trasa wysyła wszystkie dane z żadnych danych wyjściowych klasyfikatora do usługi IoT Hub (od początku$). Druga trasa dotyczy tempSensor, który został usunięty. Usuń dwa domyślne trasy.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **Deployment.Debug.template.JSON:** ten plik jest deployment.template.json wersję debugowania. Firma Microsoft należy dublować wszystkie zmiany z deployment.template.json do tego pliku.

   * **ENV:** ten plik jest, gdzie należy podać nazwę użytkownika i hasło dostępu do rejestru.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Kliknij prawym przyciskiem myszy plik deployment.template.json w Eksploratorze programu Visual Studio Code, a następnie wybierz pozycję **kompilacji z rozwiązania IoT Edge**.

11. Należy zauważyć, że to polecenie tworzy folder konfiguracji przy użyciu pliku deployment.amd64.json. Ten plik jest szablon wdrażania konkretnego rozwiązania.

## <a name="add-router-module"></a>Dodaj moduł routera

Następnie dodamy moduł routera do naszego rozwiązania. Moduł routera obsługuje kilka obowiązki dla naszego rozwiązania:

* **Odbieranie komunikatów z urządzenia typu liść:** nadejścia nowych wiadomości do urządzenia usługi IoT Edge z podrzędnym urządzeń, moduł Router odbiera komunikat i rozpoczyna się organizowanie routing wiadomości.
* **Wysyłanie komunikatów do modułu klasyfikatora pozostałego czasu eksploatacji:** po odebraniu nowej wiadomości z podrzędnym urządzenia modułu routera przekształca wiadomości do formatu, który oczekuje klasyfikatora pozostałego czasu eksploatacji. Router wysyła komunikat do pozostałego czasu eksploatacji klasyfikatora do przewidywania pozostałego czasu eksploatacji. Gdy klasyfikatora podejścia biznesowego uczyniło prognozę, wysyła wiadomość do modułu routera.
* **Wysyłanie komunikatów pozostałego czasu eksploatacji do usługi IoT Hub:** podczas Router odbiera komunikaty z klasyfikatora, przekształca komunikat, który ma zawierać tylko podstawowe informacje, identyfikator urządzenia i pozostałego czasu eksploatacji i wysyła komunikat skrócona do usługi IoT hub. Dalsze dopracowanie nie wykonaliśmy w tym miejscu, będzie wysyłać komunikaty do usługi IoT Hub, tylko wtedy, gdy Prognozowanie pozostałego czasu eksploatacji spadnie poniżej progu (na przykład, gdy pozostałego czasu eksploatacji jest mniej niż 100 cykli). Filtrowanie w ten sposób spowoduje zmniejszenie liczby komunikatów i zmniejszenie kosztów Centrum IoT hub.
* **Wyślij wiadomość do modułu zapisywania Avro:** zachować wszystkie dane wysyłane przez urządzenie podrzędne, moduł Router wysyła cała wiadomość odebrana od klasyfikatora moduł zapisywania Avro, który będzie zostaną zachowane, a następnie przekazać dane przy użyciu pliku usługi IoT Hub Przekaż.

> [!NOTE]
> Opis obowiązki modułu mogą wprowadzać przetwarzanie pozornie sekwencyjnego przepływu jest jednak komunikatów/oparte na zdarzeniach. Jest to, dlaczego jest potrzebny moduł aranżacji, takich jak nasze modułu routera.

### <a name="create-module-and-copy-files"></a>Tworzenie modułu i kopiować pliki

1. W folderze modułów w programie Visual Studio Code kliknij prawym przyciskiem myszy i wybierz polecenie **Dodaj moduł usługi IoT Edge**.

2. Wybierz  **C# modułu**.

3. Nazwa modułu **turbofanRouter**.

4. Po wyświetleniu monitu dla repozytorium obrazów platformy Docker za pomocą rejestru z obszaru roboczego usługi machine learning (rejestru można znaleźć w węźle registryCredentials swoje *deployment.template.json* pliku). Ta wartość jest w pełni kwalifikowany adres do rejestru, takie jak  **\<rejestru\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > W tym artykule używamy usługi Azure Container Registry utworzone przez obszaru roboczego usługi Azure Machine Learning, używany do uczenia i wdrażania naszej klasyfikatora. Jest to wyłącznie dla wygody. Firma Microsoft można utworzyć nowego rejestru kontenerów i opublikowane naszych modułów istnieje.

5. Otwórz nowe okno terminalu w programie Visual Studio Code (**widoku** > **terminalu**) i skopiuj pliki z katalogu modułów.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Po wyświetleniu monitu o zastąpienie pliku program.cs, naciśnij klawisz `y` a następnie naciśnij klawisz `Enter`.

### <a name="build-router-module"></a>Tworzenie modułu routera

1. W programie Visual Studio Code wybierz **terminalu** > **skonfigurować zadania kompilacji domyślne**.

2. Kliknij pozycję **pliku tasks.json Utwórz z szablonu**.

3. Kliknij pozycję **platformy .NET Core**.

4. Po otwarciu tasks.json Zastąp zawartość za pomocą:

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Zapisz i zamknij tasks.json.

6. Uruchom kompilację z `Ctrl + Shift + B` lub **terminalu** > **uruchomienia zadania kompilacji**.

### <a name="set-up-module-routes"></a>Konfigurowanie modułu tras

Jak wspomniano powyżej, środowisko uruchomieniowe usługi IoT Edge używa trasy skonfigurowane w *deployment.template.json* pliku do zarządzania komunikacją między luźno sprzężonych modułów. W tej sekcji przejdziemy do sposobu konfigurowania tras dla modułu turbofanRouter. Firma Microsoft najpierw pokryć tras wejściowych i następnie przenieść w danych wyjściowych.

#### <a name="inputs"></a>Dane wejściowe

1. Metoda Init() pliku program.cs zarejestrujemy dwa wywołania zwrotne modułu:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Pierwsze wywołanie zwrotne nasłuchuje komunikatów wysłanych do **deviceInput** ujścia. Z powyższym diagramie widzimy, że chcemy trasy wiadomości z dowolnego urządzenia liścia do tych danych wejściowych. W *deployment.template.json* Dodaj trasę, która nakazuje Centrum edge hub się kierować wszystkie komunikaty odbierane przez urządzenie usługi IoT Edge, która nie została wysłana przez moduł usługi IoT Edge w danych wejściowych o nazwie "deviceInput" w turbofanRouter module:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Następnie Dodaj trasę dla komunikatów z modułu rulClassifier do modułu turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/classifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Dane wyjściowe

Dodaj cztery dodatkowe trasy do $edgeHub parametru trasy, aby obsłużyć dane wyjściowe z modułu routera.

1. Plik program.cs definiuje metodę SendMessageToClassifier(), które używa klienta modułu, aby wysłać komunikat do pozostałego czasu eksploatacji klasyfikatora przy użyciu trasy:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/classifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() używa klienta modułu, aby wysyłać tylko dane pozostały czas eksploatacji urządzenia usługi IoT Hub za pomocą trasy:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() używa klienta modułu, aby wysłać wiadomość z danymi pozostałego czasu eksploatacji, dodane do modułu avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() wysyła komunikaty zakończone niepowodzeniem nadrzędne Centrum IoT, gdzie można je skierować na później.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Wszystkie trasy, razem wzięte swoje "$edgeHub" węzła powinna wyglądać następujące dane JSON:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> Dodawanie modułu turbofanRouter utworzone następujące dodatkowe trasy: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Usuń tę trasę, pozostawiając tylko trasy wymienionych powyżej w pliku deployment.template.json.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Skopiuj trasy do deployment.debug.template.json

W ostatnim kroku do synchronizowania plików, dublowanie deployment.template.json w deployment.debug.template.json wprowadzone zmiany.

## <a name="add-avro-writer-module"></a>Dodaj moduł zapisywania Avro

Moduł zapisywania Avro ma dwa obowiązki w naszym rozwiązaniu, do przechowywania komunikatów i przekazywanie plików.

* **Store wiadomości**: gdy moduł zapisywania Avro otrzymuje komunikat, zapisuje komunikat do lokalnego systemu plików w formacie Avro. Używamy instalacji powiązania, który instaluje katalogu (w tym /data/avrofiles wielkości liter) do ścieżki w kontenerze modułu. Zezwala na tej instalacji modułu do zapisywania ścieżkę lokalną (/ avrofiles) i mieć tych plików, które są dostępne bezpośrednio z urządzenia usługi IoT Edge.

* **Przekazywanie plików**: Moduł zapisywania Avro używa funkcji przekazywania plików usługi Azure IoT Hub do przekazywania plików do konta usługi Azure storage. Po pomyślnym przekazaniu pliku moduł usuwa plik z dysku

### <a name="create-module-and-copy-files"></a>Tworzenie modułu i kopiować pliki

1. Paleta poleceń wyszukiwania, a następnie wybierz pozycję **Python: Select Interpreter** (Python: wybierz interpreter).

1. Wybierz interpreter w C:\\Python37.

1. Ponownie otwórz paletę poleceń i wyszukiwania, a następnie wybierz **terminalu: Wybierz domyślną powłokę**.

1. Po wyświetleniu monitu wybierz **polecenia**.

1. Otwórz nowy terminal powłokę, **terminalu** > **nowy Terminal**.

1. W folderze modułów w programie Visual Studio Code kliknij prawym przyciskiem myszy i wybierz polecenie **Dodaj moduł usługi IoT Edge**.

1. Wybierz pozycję **Moduł Python**.

1. Nazwa modułu "avroFileWriter".

1. Po wyświetleniu monitu dla repozytorium obrazów platformy Docker za pomocą tego samego rejestru jak użyte podczas dodawania modułu routera.

1. Kopiuj pliki z przykładowego modułu, do rozwiązania.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Jeśli zostanie wyświetlony monit, aby zastąpić main.py, wpisz `y` a następnie naciśnij klawisz `Enter`.

1. Należy zauważyć, że filemanager.py i schema.py zostały dodane do rozwiązania i main.py został zaktualizowany.

> [!NOTE]
> Po otwarciu pliku języka Python może zostać monit o zainstalowanie pylint. Nie musisz zainstalować linter do ukończenia tego samouczka.

### <a name="bind-mount-for-data-files"></a>Powiąż instalacji dla plików danych

Jak wspomniano w wprowadzenia, moduł zapisu zależy od obecności instalacji powiązania do zapisania plików Avro do systemu plików urządzenia.

#### <a name="add-directory-to-device"></a>Dodaj katalog do urządzenia

1. Łączenie z urządzeniem usługi IoT Edge maszyny Wirtualnej przy użyciu protokołu SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Utwórz katalog, w którym będą przechowywane zapisane liścia komunikaty z urządzenia.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Zaktualizuj uprawnienia katalogu, aby stał się zapisywalne przez kontener.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Weryfikowanie katalogu ma teraz uprawnienia do zapisu (w) dla użytkownika, grupy i właściciela.

   ```bash
   ls -la /data
   ```

   ![Uprawnienia katalogu dla avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Dodaj katalog do modułu

Aby dodać katalog do modułu kontenera, zmodyfikujemy plików Dockerfile, skojarzone z modułem avroFileWriter. Istnieją trzy pliki Dockerfile skojarzone z modułem: Dockerfile.AMD64 Dockerfile.amd64.debug i Dockerfile.arm32v7. Pliki te powinny być synchronizowane w przypadku, gdy firma Microsoft chce debugowania lub wdrożyć na urządzeniu z systemem arm32. W tym artykule należy skoncentrować się tylko na Dockerfile.amd64.

1. Na komputerze deweloperskim, należy otworzyć **Dockerfile.amd64** pliku.

2. Zmodyfikuj plik w taki sposób, aby wygląda następująco:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   `mkdir` i `chown` polecenia poinstruować procesu kompilacji platformy Docker, aby utworzyć katalog najwyższego poziomu o nazwie /avrofiles na obrazie a następnie ustaw moduleuser jako właściciela tego katalogu. Jest ważne, że te polecenia są wstawiane po użytkownik moduł zostanie dodany do obrazu z `useradd` polecenia i przed przełączeń kontekstu do moduleuser (moduleuser użytkownika).

3. Dockerfile.amd64.debug i Dockerfile.arm32v7, należy wprowadzić odpowiednie zmiany.

#### <a name="update-the-module-configuration"></a>Aktualizacja konfiguracji modułu

Ostatnim krokiem tworzenia powiązania jest do aktualizacji plików deployment.template.json (i deployment.debug.template.json) z informacjami o powiązania.

1. Otwórz deployment.template.json.

2. Modyfikowanie definicji modułu avroFileWriter, dodając `Binds` parametr, który wskazuje kontenera /avrofiles katalog do katalogu lokalnego na urządzeniu usługi edge. Definicji modułu powinien być zgodny w tym przykładzie:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Aby deployment.debug.template.json, należy wprowadzić odpowiednie zmiany.

### <a name="bind-mount-for-access-to-configyaml"></a>Powiąż instalacji dla dostępu z config.yaml

Musimy dodać jeden więcej powiązania dla modułu zapisywania. To powiązanie daje dostęp modułu, można odczytać parametrów połączenia z pliku /etc/iotedge/config.yaml na urządzeniu usługi IoT Edge. Potrzebujemy parametry połączenia do utworzenia usługi IoTHubClient, dzięki czemu możemy wywołać przekazywania\_blob\_metody asynchronicznej, aby przekazać pliki do usługi IoT hub. Kroki, aby dodać to powiązanie są podobne do tych w poprzedniej sekcji.

#### <a name="update-directory-permission"></a>Aktualizowanie uprawnień do katalogu

1. Łączenie z urządzeniem usługi IoT Edge przy użyciu protokołu SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Dodaj uprawnienia do odczytu do pliku config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Sprawdź poprawność uprawnienia zostały ustawione prawidłowo.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Upewnij się, że uprawnienia dla config.yaml **- r--r r----** .

#### <a name="add-directory-to-module"></a>Dodaj katalog do modułu

1. Na komputerze deweloperskim, należy otworzyć **Dockerfile.amd64** pliku.

2. Dodaj dodatkowy zestaw `mkdir` i `chown` polecenia w pliku tak oznacza to wygląda podobnie do:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Dockerfile.amd64.debug i Dockerfile.arm32v7, należy wprowadzić odpowiednie zmiany.

#### <a name="update-the-module-configuration"></a>Aktualizacja konfiguracji modułu

1. Otwórz **deployment.template.json** pliku.

2. Modyfikowanie definicji modułu avroFileWriter, dodając drugi wiersz do `Binds` parametr, który wskazuje katalogu kontenera (/ app/iotconfig) do katalogu lokalnego na urządzeniu (/ etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Aby deployment.debug.template.json, należy wprowadzić odpowiednie zmiany.

## <a name="install-dependencies"></a>Instalowanie zależności

Moduł zapisywania przejmuje dwie biblioteki Python fastavro i PyYAML zależności. Musimy zainstalować zależności na maszynie deweloperskiej z naszych i poinformuj procesu kompilacji platformy Docker do zainstalowania go na ilustracji naszych modułu.

### <a name="pyyaml"></a>PyYAML

1. Na komputerze deweloperskim, należy otworzyć **requirements.txt** pliku i Dodaj pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Otwórz **Dockerfile.amd64** pliku i Dodaj `pip install` polecenie, aby uaktualnić setuptools.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Aby Dockerfile.amd64.debug, należy wprowadzić odpowiednie zmiany. <!--may not be necessary. Add 'if needed'?-->

4. Zainstaluj pyyaml lokalnie, otwierając terminal programu Visual Studio Code i wpisując

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. W pliku requirements.txt należy dodać fastavro po pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Zainstaluj fastavro na maszynie deweloperskiej przy użyciu programu Visual Studio Code terminala.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Skonfiguruj ponownie usługę IoT Hub

Wprowadzenie do urządzenia usługi IoT Edge i moduły systemu, zmieniliśmy nasze oczekiwania o jakie dane zostaną wysłane do Centrum, jak i w jakim celu. Należy ponownie skonfigurować routingu w piaście radzenia sobie z naszych nowa rzeczywistość.

> [!NOTE]
> Możemy zmienić konfigurację koncentratora przed wdrożeniem modułów, ponieważ niektóre ustawienia koncentratora, w szczególności pliku przekazywania musi być prawidłowo skonfigurowany do modułu avroFileWriter by działała poprawnie

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Skonfiguruj trasy dla pozostałego czasu eksploatacji komunikatów w usłudze IoT Hub

Router i klasyfikatora w miejscu oczekujemy, że do odbierania komunikatów regularnych zawierających tylko identyfikator urządzenia i przewidywania pozostałego czasu eksploatacji dla urządzenia. Chcemy kierować dane pozostały czas eksploatacji własnej lokalizacji magazynu, gdzie możemy monitorować stan urządzeń, tworzyć raporty i wyzwalać alerty w razie potrzeby. W tym samym czasie chcemy, aby wszelkie dane urządzenie, które nadal jest wysyłany bezpośrednio przez urządzenia liścia, które jeszcze nie został dołączony do naszych urządzenie usługi IoT Edge, aby przejść do kierowania do bieżącej lokalizacji magazynu.

#### <a name="create-a-rul-message-route"></a>Tworzenie trasy wiadomości pozostałego czasu eksploatacji

1. W witrynie Azure portal przejdź do Centrum IoT Hub.

2. Na lewym pasku nawigacyjnym wybierz **routing komunikatów**.

3. Wybierz pozycję **Dodaj**.

4. Nazwa trasy **RulMessageRoute**.

5. Wybierz **Dodaj** obok **punktu końcowego** selektor i wybierz polecenie **magazynu obiektów Blob**.

6. W **Dodawanie punktu końcowego magazynu** formularza, określ nazwę punktu końcowego **ruldata**.

7. Wybierz **Wybierz kontener**.

8. Wybierz konto magazynu używane w tym samouczku, który nosi nazwę takich jak **iotedgeandml\<unikatowy sufiks\>** .

9. Wybierz **ruldata** kontenera i kliknij przycisk **wybierz**.

10. Kliknij przycisk **Utwórz** do utworzenia punktu końcowego magazynu.

11. Aby uzyskać **Routing zapytania**, wprowadź następujące zapytanie:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Rozwiń **testu** sekcji i następnie **treść komunikatu** sekcji. Zastąp komunikat ten przykład naszego oczekiwane komunikaty:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Wybierz **trasy testu**. Jeśli test wypadnie pomyślnie, zobaczysz "komunikat wynik kwerendy".

14. Kliknij pozycję **Zapisz**.

#### <a name="update-turbofandevicetostorage-route"></a>Update turbofanDeviceToStorage route

Nie chcemy kierować nowe dane prognozy do naszej starej lokalizacji magazynu, więc zaktualizowania marszruty, zapobiegając jej.

1. W usłudze IoT Hub **routing komunikatów** wybierz opcję **trasy** kartę.

2. Wybierz **turbofanDeviceDataToStorage**, lub dowolną nazwę, jak spowodował urządzenie początkowe dane trasy.

3. Aktualizuj zapytanie routingu, aby

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Rozwiń **testu** sekcji i następnie **treść komunikatu** sekcji. Zastąp komunikat ten przykład naszego oczekiwane komunikaty:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Wybierz **trasy testu**. Jeśli test wypadnie pomyślnie, zobaczysz "komunikat wynik kwerendy".

6. Wybierz pozycję **Zapisz**.

### <a name="configure-file-upload"></a>Konfigurowanie przekazywania plików

Konfigurowanie funkcji przekazywania plików usługi IoT Hub, aby włączyć moduł zapisywania plików przekazać pliki do magazynu.

1. W Nawigatorze po lewej stronie w usłudze IoT Hub, wybierz **przekazywanie pliku**.

2. Wybierz **kontener usługi Azure Storage**.

3. Wybierz konto magazynu z listy.

4. Wybierz **uploadturbofanfiles** kontenera i kliknij przycisk **wybierz**.

5. Wybierz pozycję **Zapisz**. Portalu zostanie wyświetlone powiadomienie po zakończeniu zapisu.

> [!Note]
> Firma Microsoft nie są Włączanie przekazywania powiadomień na potrzeby tego samouczka, ale zobacz [otrzymywać powiadomienie o przekazywaniu pliku](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) dla szczegółowe informacje na temat sposobu obsługi pliku przekazywania powiadomień.

## <a name="build-publish-and-deploy-modules"></a>Tworzenie, publikowanie i wdrażanie modułów

Teraz, gdy zostały wprowadzone zmiany konfiguracji, możemy przystąpić do tworzenia obrazów i opublikuj je w naszej usłudze Azure container registry. Proces kompilacji używa pliku deployment.template.json, aby określić, które moduły muszą zostać skompilowane. Ustawienia dla każdego modułu, w tym wersja, znajdują się w pliku module.json w folderze modułu. Proces kompilacji pierwszego uruchomienia kompilacji platformy Docker na pliki Dockerfile dopasowania bieżącej konfiguracji w pliku module.json do utworzenia obrazu. Następnie publikuje obrazu do rejestru z pliku module.json z tagiem wersji zgodne w pliku module.json. Na koniec generuje manifest wdrażania specyficznych dla konfiguracji (na przykład deployment.amd64.json), który wdrożymy urządzenia usługi IoT Edge. Urządzenia usługi IoT Edge odczytuje informacje z wdrożenia manifestu i oparte na instrukcjami zostanie pobrana modułów, skonfigurować trasy i ustaw żądane właściwości. Ta metoda wdrażania zawiera dwa efekty uboczne, które należy wiedzieć:

* **Opóźnienie wdrożenia:** ponieważ środowisko uruchomieniowe usługi IoT Edge musi rozpoznać zmiany jego żądane właściwości, przed jej rozpoczęciem zmienić konfigurację, może upłynąć pewien czas, po wdrożeniu moduły, dopóki środowisko uruchomieniowe przejmuje je i rozpoczyna się do aktualizacji usługi IoT Edge urządzenie.

* **Sprawy wersji modułu:** Jeśli opublikujesz nową wersję modułu kontenera do rejestru kontenerów za pomocą tych samych tagów w wersji co poprzedni moduł środowiska uruchomieniowego nie pobierze nową wersję modułu. Robi porównanie tag wersji lokalny obraz i odpowiedni obraz z pliku manifestu wdrożenia. Jeśli te wersje są zgodne, środowisko wykonawcze nie podejmuje żadnych działań. W związku z tym jest ważne, aby zwiększyć wersji modułu za każdym razem, gdy chcesz wdrożyć nowe zmiany. Zwiększ wersji, zmieniając **wersji** właściwości **tag** właściwości w pliku module.json dla modułu, w przypadku zmiany. Następnie zbudujesz i opublikować modułu.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Twórz i Publikuj

1. W programie Visual Studio Code na do tworzenia maszyny Wirtualnej Otwórz okno terminala z programu Visual Studio Code i zaloguj się do rejestru kontenerów.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. W programie Visual Studio Code kliknij prawym przyciskiem myszy na deployment.template.json, a następnie wybierz **kompilacji i wypychania IoT nowoczesne rozwiązanie**.

### <a name="view-modules-in-the-registry"></a>Wyświetlanie modułów w rejestrze

Po pomyślnym zakończeniu kompilacji, firma Microsoft będzie można przejrzeć nasze modułów opublikowanych przy użyciu witryny Azure portal.

1. W witrynie Azure portal przejdź do obszaru roboczego usługi Azure Machine Learning, a następnie kliknij hiperlink do **rejestru**.

    ![Przejdź do rejestru z obszaru roboczego usługi uczenie maszynowe](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Wybierz z Nawigatora rejestru po stronie **repozytoriów**.

3. Należy pamiętać, oba moduły utworzony, **avrofilewriter** i **turbofanrouter**, są traktowane jako repozytoriów.

4. Wybierz **turbofanrouter** i zwróć uwagę, opublikowano jeden obraz oznaczony jako 0.0.1-amd64.

   ![Widok pierwszej wersji oznakowane turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Wdrażanie modułów na urządzeniu usługi IoT Edge

Firma Microsoft utworzone i skonfigurowane moduły w naszym rozwiązaniu, teraz wdrożymy modułów na urządzeniu usługi IoT Edge.

1. W programie Visual Studio Code kliknij prawym przyciskiem myszy **deployment.amd64.json** pliku w folderze konfiguracji.

2. Wybierz **tworzenie wdrożenia dla jednego urządzenia**.

3. Wybierz swoje urządzenie usługi IoT Edge **aaTurboFanEdgeDevice**.

4. Odśwież panelu urządzenia Azure IoT Hub w Eksploratorze programu Visual Studio Code. Powinieneś zobaczyć, że trzy nowe moduły są wdrażane, ale nie zostały jeszcze uruchomiona.

5. Odśwież ponownie za kilka minut, a zobaczysz modułów uruchomionych.

   ![Wyświetl uruchomione moduły w programie Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Go może potrwać kilka minut dla modułów do uruchamiania i osadzenie ich w stały uruchomiona. W tym czasie może zostać wyświetlony modułów, uruchamiać i zatrzymywać, kiedy będą próbowali nawiązać połączenie z modułem Centrum IoT Edge.

## <a name="diagnosing-failures"></a>Diagnozowanie błędów

W tej sekcji udostępniamy kilka technik dla zrozumienia, co przeszedł problem z modułem lub modułów. Często awarię, można najpierw wykrył ze stanu programu Visual Studio Code.

### <a name="identify-failed-modules"></a>Identyfikowanie modułów nie powiodło się

* **Visual Studio Code:** Spójrz na panelu urządzenia Azure IoT Hub. Większość modułów są w stanie uruchomienia, ale jeden jest zatrzymana, należy zbadać ten moduł zatrzymania dalszych. Jeśli wszystkie moduły są w stanie zatrzymanym przez długi czas, może to oznaczać błąd także.

* **Witryna Azure portal:** Przejdź do Centrum IoT w portalu, a następnie znajdowanie strony szczegółów urządzenia (w ramach usługi IoT Edge teraz przejść do szczegółów urządzenia) może się okazać, że moduł zgłosił błąd lub nigdy nie zgłosił żadnych do usługi IoT hub.

### <a name="diagnosing-from-the-device"></a>Diagnozowanie z urządzenia

Logując się do urządzenia usługi IoT Edge, możesz uzyskać dostęp do dużą część informacji o stanie moduły. Mechanizm głównym, używanych przez firmę Microsoft są polecenia Docker, które NAS Sprawdź kontenery i obrazy na urządzeniu.

1. Wyświetl listę wszystkich uruchomionych kontenerów. Oczekujemy zobaczyć kontenera dla każdego modułu o nazwie, która odnosi się do modułu. Ponadto to polecenie wyświetla dokładny obraz kontenera, w tym wersję, dzięki czemu może odnosić się przy użyciu Twoje oczekiwania. Wstawiając "obraz" do "container", w poleceniu, wyświetlenie listy obrazów.

   ```bash
   sudo docker container ls
   ```

2. Pobierz dzienniki dla kontenera. To polecenie generuje, niezależnie od rodzaju został zapisany do strumienia wyjściowego StdErr i StdOut w kontenerze. To polecenie działa w przypadku kontenerów, które mają pracę i następnie zakończenia przyczyny. Jest to również przydatne dla zrozumienia, co ma został dzieje z kontenerami edgeAgent lub edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Sprawdź, czy kontener. To polecenie daje ogromne informacje o obrazie. Dane mogą być filtrowane w zależności od tego, czego szukasz. Na przykład jeśli chcesz zobaczyć, czy powiązania na avroFileWriter są poprawne można użyć polecenia:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Nawiązać uruchomionego kontenera. To polecenie może być przydatne, jeśli chcesz sprawdzić kontener jest uruchomiona:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzyliśmy rozwiązania IoT Edge w programie Visual Studio Code za pomocą trzech modułów, klasyfikatora, router i pliku moduł zapisujący/moduł przesyłający. Możemy skonfigurować trasy w celu zezwalania na moduły, aby komunikować się ze sobą na urządzeniu usługi edge zmodyfikował konfigurację urządzenia usługi edge i zaktualizowane pliki Dockerfile, aby zainstalować zależności i dodać powiązania instaluje moduły dostępne w kontenerach. Następnie Zaktualizowaliśmy konfiguracji centrum IoT Hub do rozsyłania nasze komunikaty na podstawie typu i obsługiwać operacje przekazywania plików. Wszystko w miejscu firma Microsoft wdrożone moduły na urządzeniu usługi IoT Edge i ustalono, że moduły działały poprawnie.

Więcej informacji można znaleźć na następujących stronach:

* [Dowiedz się, jak wdrażać moduły oraz określenia trasy w usługi IoT Edge](module-composition.md)
* [Kierowanie Składnia kwerendy komunikatów usługi IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Routing komunikatów usługi IoT Hub: teraz przy użyciu routingu na treść komunikatu](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Przekazywanie plików za pomocą usługi IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Przejdź do następnego artykułu, aby rozpocząć wysyłanie danych i wyświetlić swoje rozwiązanie w działaniu.

> [!div class="nextstepaction"]
> [Wysyłanie danych za pośrednictwem przezroczystej bramy](tutorial-machine-learning-edge-07-send-data-to-hub.md)
