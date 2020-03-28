---
title: 'Samouczek: Tworzenie i wdrażanie modułów niestandardowych — uczenie maszynowe w usłudze Azure IoT Edge'
description: W tym samouczku pokazano, jak tworzyć i wdrażać moduły usługi IoT Edge, które przetwarzają dane z urządzeń typu liść za pośrednictwem modelu uczenia maszynowego, a następnie wysyłają szczegółowe informacje do usługi IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3cba7781ac80ae567b2bfd54c4131429ed94b90f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75772367"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Samouczek: Tworzenie i wdrażanie niestandardowych modułów usługi IoT Edge

> [!NOTE]
> Ten artykuł jest częścią serii samouczka na temat korzystania z usługi Azure Machine Learning w usłudze IoT Edge. Jeśli dotarłeś do tego artykułu bezpośrednio, zachęcamy do rozpoczęcia [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii, aby uzyskać najlepsze wyniki.

W tym artykule tworzymy trzy moduły usługi IoT Edge, które odbierają wiadomości z urządzeń typu liść, uruchamiają dane za pośrednictwem modelu uczenia maszynowego, a następnie przekazują szczegółowe informacje do usługi IoT Hub.

Piasta IoT Edge ułatwia komunikację modułów z modułami. Korzystanie z usługi IoT Edge hub jako broker wiadomości utrzymuje moduły niezależne od siebie. Moduły muszą tylko określić dane wejściowe, na których akceptują komunikaty i dane wyjściowe, do których piszą wiadomości.

Chcemy, aby urządzenie IoT Edge osiągił dla nas cztery rzeczy:

* Odbieranie danych z urządzeń liściowych
* Przewidywanie pozostałego okresu użytkowania (RUL) dla urządzenia, które wysłało dane
* Wyślij wiadomość tylko z RUL dla urządzenia do Usługi IoT Hub (ta funkcja może być modyfikowana tylko wysłać dane, jeśli RUL spadnie poniżej pewnego poziomu)
* Zapisz dane urządzenia liścia w pliku lokalnym na urządzeniu Usługi IoT Edge. Ten plik danych jest okresowo przekazywany do usługi IoT Hub za pośrednictwem przekazywania plików w celu uściślania szkolenia modelu uczenia maszynowego. Korzystanie z przekazywania plików zamiast stałego przesyłania strumieniowego wiadomości jest bardziej opłacalne.

Aby wykonać te zadania, używamy trzech modułów niestandardowych:

* **Klasyfikator RUL:** Moduł turboFanRulClassifier, który utworzyliśmy w [train i wdrożyć model usługi Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) jest standardowy moduł uczenia maszynowego, który udostępnia dane wejściowe o nazwie "amlInput" i dane wyjściowe o nazwie "amlOutput". "AmlInput" oczekuje, że jego dane wejściowe będą wyglądać dokładnie tak, jak dane wejściowe, które wysłaliśmy do usługi sieci web opartej na aci. Podobnie "amlOutput" zwraca te same dane co usługa sieci web.

* **Pisarz Avro:** Ten moduł odbiera komunikaty na wejściu "avroModuleInput" i utrzymuje komunikat w formacie Avro na dysk w celu późniejszego przekazania do centrum IoT Hub.

* **Moduł routera:** Moduł routera odbiera wiadomości z urządzeń podrzędnych liścia, a następnie formatuje i wysyła wiadomości do klasyfikatora. Moduł następnie odbiera wiadomości z klasyfikatora i przekazuje wiadomość do modułu modułu modułu avro writer. Na koniec moduł wysyła tylko przewidywanie RUL do centrum IoT Hub.

  * Wejścia:
    * **deviceInput**: odbiera wiadomości z urządzeń typu leaf
    * **rulInput:** odbiera wiadomości z "amlOutput"

  * Wyjść:
    * **classify:** wysyła wiadomości do "amlInput"
    * **writeAvro:** wysyła wiadomości do "avroModuleInput"
    * **toIotHub:** wysyła wiadomości do $upstream, który przekazuje wiadomości do połączonego Centrum IoT

Poniższy diagram przedstawia moduły, wejścia, wyjścia i trasy usługi IoT Edge Hub dla pełnego rozwiązania:

![Diagram architektury trzech modułów usługi IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Kroki opisane w tym artykule są zazwyczaj wykonywane przez dewelopera chmury.

## <a name="create-a-new-iot-edge-solution"></a>Tworzenie nowego rozwiązania IoT Edge

Podczas wykonywania drugiego z naszych dwóch notesów platformy Azure utworzyliśmy i opublikowaliśmy obraz kontenera zawierający nasz model RUL. Usługa Azure Machine Learning, w ramach procesu tworzenia obrazu, spakowane tego modelu, dzięki czemu obraz jest wdrażalny jako moduł usługi Azure IoT Edge. W tym kroku utworzymy rozwiązanie usługi Azure IoT Edge przy użyciu modułu "Azure Machine Learning" i wskażemy modułowi obrazowi opublikowanej przy użyciu notesów platformy Azure.

1. Otwórz sesję pulpitu zdalnego na komputerze deweloperskim.

2. Otwórz folder **\\C: źródło\\IoTEdgeAndMlSample** w programie Visual Studio Code.

3. Kliknij prawym przyciskiem myszy panel eksploratora (w pustym miejscu) i wybierz **pozycję Nowe rozwiązanie IoT Edge**.

    ![Tworzenie nowego rozwiązania IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Zaakceptuj domyślną nazwę rozwiązania **EdgeSolution**.

5. Wybierz **usługę Azure Machine Learning** jako szablon modułu.

6. Nazwij moduł **turbofanRulClassifier**.

7. Wybierz obszar roboczy uczenia maszynowego.

8. Wybierz obraz utworzony podczas uruchamiania notesu platformy Azure.

9. Spójrz na rozwiązanie i zwróć uwagę na utworzone pliki:

   * **deployment.template.json:** Ten plik zawiera definicję każdego z modułów w rozwiązaniu. Istnieją trzy sekcje, na które należy zwrócić uwagę w tym pliku:

     * **Poświadczenia rejestru:** Definiuje zestaw niestandardowych rejestrów kontenerów, których używasz w rozwiązaniu. W tej chwili powinien zawierać rejestr z obszaru roboczego uczenia maszynowego, czyli miejsca, w którym został zapisany obraz usługi Azure Machine Learning. Możesz mieć dowolną liczbę rejestrów kontenerów, ale dla uproszczenia użyjemy tego jednego rejestru dla wszystkich modułów

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Moduły:** Ta sekcja zawiera zestaw modułów zdefiniowanych przez użytkownika, które idą z tym rozwiązaniem. Można zauważyć, że ta sekcja zawiera obecnie dwa moduły: SimulatedTemperatureSensor i turbofanRulClassifier. Program SimulatedTemperatureSensor został zainstalowany przez szablon kodu programu Visual Studio, ale nie jest potrzebny dla tego rozwiązania. Definicję modułu SimulatedTemperatureSensor można usunąć z sekcji modułów. Należy zauważyć, że definicja modułu turbofanRulClassifier wskazuje na obraz w rejestrze kontenerów. Jak dodamy więcej modułów do rozwiązania, pojawią się one w tej sekcji.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
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

     * **Trasy:** będziemy pracować z trasami sporo w tym samouczku. Trasy określają sposób komunikowania się modułów ze sobą. Dwie trasy zdefiniowane przez szablon nie są zgodne z routingiem, którego potrzebujemy. Pierwsza trasa wysyła wszystkie dane z dowolnego wyjścia klasyfikatora do usługi IoT Hub ($upstream). Druga trasa jest dla SymulowanegoTemperatureSensor, który właśnie usunęliśmy. Usuń dwie trasy domyślne.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **deployment.debug.template.json:** ten plik jest wersją debugowania deployment.template.json. Powinniśmy dublowanie wszystkich zmian z deployment.template.json do tego pliku.

   * **.env:** ten plik jest, gdzie należy podać nazwę użytkownika i hasło dostępu do rejestru.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Kliknij prawym przyciskiem myszy plik deployment.template.json w Eksploratorze kodu programu Visual Studio i wybierz polecenie **Buduj rozwiązanie IoT Edge**.

11. Należy zauważyć, że to polecenie tworzy folder konfiguracji z plikiem deployment.amd64.json. Ten plik jest szablonem wdrożenia konkretnej dla rozwiązania.

## <a name="add-router-module"></a>Dodaj moduł routera

Następnie dodajemy moduł routera do naszego rozwiązania. Moduł Routera obsługuje kilka obowiązków związanych z naszym rozwiązaniem:

* **Odbieranie wiadomości z urządzeń typu liść:** gdy wiadomości docierają do urządzenia usługi IoT Edge z urządzeń podrzędnych, moduł routera odbiera komunikat i rozpoczyna organizowanie routingu wiadomości.
* **Wysyłaj wiadomości do modułu klasyfikatora RUL:** po odebraniu nowej wiadomości z urządzenia podrzędnego moduł routera przekształca wiadomość do formatu oczekiwanego przez klasyfikatora RUL. Router wysyła komunikat do klasyfikatora RUL dla prognozowania RUL. Po dokonaniu prognozowania, wysyła komunikat z powrotem do modułu routera.
* **Wysyłaj wiadomości RUL do usługi IoT Hub:** gdy router odbiera wiadomości od klasyfikatora, przekształca komunikat w celu zawierającego tylko podstawowe informacje, identyfikator urządzenia i RUL i wysyła skróconą wiadomość do centrum IoT hub. Dalsze udoskonalenie, którego nie zrobiliśmy w tym miejscu, spowoduje wysłanie wiadomości do centrum IoT tylko wtedy, gdy przewidywanie RUL spadnie poniżej progu (na przykład, gdy RUL jest mniejsza niż 100 cykli). Filtrowanie w ten sposób zmniejszyłoby liczbę wiadomości i zmniejszyło koszt centrum IoT Hub.
* **Wyślij wiadomość do modułu Avro Writer:** aby zachować wszystkie dane wysyłane przez urządzenie podrzędne, moduł Router wysyła całą wiadomość otrzymaną od klasyfikatora do modułu Avro Writer, który będzie się powtarzał i przesyłał dane za pomocą przekazywania plików IoT Hub.

> [!NOTE]
> Opis obowiązków modułu może sprawić, że przetwarzanie wydaje się sekwencyjne, ale przepływ jest oparty na wiadomości/zdarzenia. Dlatego potrzebujemy modułu aranżacji, takiego jak nasz moduł routera.

### <a name="create-module-and-copy-files"></a>Tworzenie modułów i kopiowanie plików

1. Kliknij prawym przyciskiem myszy folder modułów w programie Visual Studio Code i wybierz polecenie **Dodaj moduł IoT Edge**.

2. Wybierz **moduł C#**.

3. Nazwij moduł **turbofanRouter**.

4. Po wyświetleniu monitu o repozytorium obrazów platformy Docker użyj rejestru z obszaru roboczego uczenia maszynowego (można go znaleźć w węźle registryCredentials pliku *deployment.template.json).* Ta wartość jest w pełni kwalifikowany adres do rejestru, takich jak ** \<\>rejestru .azurecr.io/turbofanrouter**.

    > [!NOTE]
    > W tym artykule używamy rejestru kontenerów platformy Azure utworzonego przez obszar roboczy usługi Azure Machine Learning, którego użyto do uczenia i wdrażania naszego klasyfikatora. Jest to wyłącznie dla wygody. Mogliśmy utworzyć nowy rejestr kontenerów i opublikować tam nasze moduły.

5. Otwórz nowe okno terminala w programie Visual Studio Code **(Wyświetl** > **terminal)** i skopiuj pliki z katalogu modułów.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Po wyświetleniu monitu o zastąpienie program.cs `y` naciśnij, `Enter`a następnie naciśnij przycisk .

### <a name="build-router-module"></a>Tworzenie modułu routera

1. W programie Visual Studio Code wybierz pozycję **Terminal** > **Konfiguruj domyślne zadanie kompilacji**.

2. Kliknij **pozycję Utwórz plik tasks.json z szablonu**.

3. Kliknij **.NET Core**.

4. Po otwarciu pliku tasks.json zastąp zawartość:

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

6. Uruchom kompilację za `Ctrl + Shift + B` pomocą zadania **kompilacji lub uruchamiania terminalu** > **Run Build Task**.

### <a name="set-up-module-routes"></a>Konfigurowanie tras modułów

Jak wspomniano powyżej, środowisko uruchomieniowe usługi IoT Edge używa tras skonfigurowanych w pliku *deployment.template.json* do zarządzania komunikacją między luźno sprzężonymi modułami. W tej sekcji, możemy drążyć, jak ustawić trasy dla modułu turbofanRouter. Najpierw omówimy trasy wejściowe, a następnie przejdziemy na wyjścia.

#### <a name="inputs"></a>Dane wejściowe

1. W metodzie Init() Program.cs rejestrujemy dwa wywołania zwrotne dla modułu:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Pierwsze wywołanie zwrotne nasłuchuje wiadomości wysyłanych do **deviceInput** sink. Na powyższym diagramie widzimy, że chcemy rozsyłać wiadomości z dowolnego urządzenia typu liść do tego wejścia. W pliku *deployment.template.json* dodaj trasę, która informuje koncentrator krawędzi, aby skierowała dowolną wiadomość odebraną przez urządzenie Usługi IoT Edge, która nie została wysłana przez moduł usługi IoT Edge do wejścia o nazwie "deviceInput" w module turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Następnie dodaj trasę dla wiadomości z modułu rulClassifier do modułu turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Dane wyjściowe

Dodaj cztery dodatkowe trasy do parametru trasy $edgeHub, aby obsłużyć wyjścia z modułu Router.

1. Program.cs definiuje metodę SendMessageToClassifier(), która używa klienta modułu do wysyłania wiadomości do klasyfikatora RUL przy użyciu trasy:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() używa klienta modułu do wysyłania tylko danych RUL dla urządzenia do centrum IoT hub za pośrednictwem trasy:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() używa klienta modułu do wysyłania wiadomości z danymi RUL dodanymi do modułu avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() wysyła komunikaty, które nie powiodły się, upstream Centrum IoT, gdzie mogą być kierowane na później.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Ze wszystkimi trasami razem wzięte twój węzeł "$edgeHub" powinien wyglądać następująco JSON:

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
> Dodanie modułu turbofanRouter utworzono `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`następującą dodatkową trasę: . Usuń tę trasę, pozostawiając tylko trasy wymienione powyżej w pliku deployment.template.json.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Kopiowanie tras do pliku deployment.debug.template.json

Na ostatnim etapie, aby nasze pliki były zsynchronizowane, należy odzwierciedlić zmiany wprowadzone w deployment.template.json w deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Dodaj moduł Avro Writer

Moduł Avro Writer ma dwa obowiązki w naszym rozwiązaniu, do przechowywania wiadomości i przesyłania plików.

* **Przechowywanie wiadomości**: gdy moduł Avro Writer odbiera wiadomość, zapisuje wiadomość do lokalnego systemu plików w formacie Avro. Używamy powiązania mount, który montuje katalog (w tym przypadku /data/avrofiles) do ścieżki w kontenerze modułu. Ten uchwyt umożliwia modułowi zapis do ścieżki lokalnej (/avrofiles) i mają te pliki dostępne bezpośrednio z urządzenia Usługi IoT Edge.

* **Przekaż pliki:** moduł Avro Writer używa funkcji przekazywania plików usługi Azure IoT Hub do przekazywania plików na konto magazynu platformy Azure. Po pomyślnym przesłaniu pliku moduł usuwa plik z dysku

### <a name="create-module-and-copy-files"></a>Tworzenie modułów i kopiowanie plików

1. W palecie poleceń wyszukaj, a następnie wybierz **Python: Wybierz interpreter**.

1. Wybierz interpreter znaleziony w\\języku C: Python37.

1. Ponownie otwórz paletę poleceń i wyszukaj pozycję **Terminal: Wybierz domyślną powłokę**.

1. Po wyświetleniu monitu wybierz polecenie **Wiersz polecenia**.

1. Otwórz nową powłokę **terminala, Terminal** > **New Terminal**.

1. Kliknij prawym przyciskiem myszy folder modułów w programie Visual Studio Code i wybierz polecenie **Dodaj moduł IoT Edge**.

1. Wybierz pozycję **Moduł Python**.

1. Nazwij moduł "avroFileWriter".

1. Po wyświetleniu monitu o repozytorium obrazów platformy Docker należy używać tego samego rejestru, który był używany podczas dodawania modułu Router.

1. Skopiuj pliki z modułu przykładowego do rozwiązania.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Jeśli zostanie wyświetlony monit o zastąpienie main.py, wpisz, `y` a następnie naciśnij . `Enter`

1. Należy zauważyć, że filemanager.py i schema.py zostały dodane do rozwiązania i main.py został zaktualizowany.

> [!NOTE]
> Po otwarciu pliku Python może zostać wyświetlony monit o zainstalowanie pylint. Nie trzeba instalować linter, aby zakończyć ten samouczek.

### <a name="bind-mount-for-data-files"></a>Mocowanie wiązania dla plików danych

Jak wspomniano we wstępie, moduł moduł modułu modułu modułu polega na obecności bind mount do pisania plików Avro do systemu plików urządzenia.

#### <a name="add-directory-to-device"></a>Dodawanie katalogu do urządzenia

1. Połącz się z maszyną wirtualną urządzenia IoT Edge za pomocą SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Utwórz katalog, w który będzie przechowywać zapisane komunikaty urządzenia liścia.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Zaktualizuj uprawnienia katalogu, aby można go było zapisać w kontenerze.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Sprawdź poprawność katalogu ma teraz uprawnienia zapisu (w) dla użytkownika, grupy i właściciela.

   ```bash
   ls -la /data
   ```

   ![Uprawnienia katalogu dla avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Dodawanie katalogu do modułu

Aby dodać katalog do kontenera modułu, zmodyfikujemy pliki Dockerfiles skojarzone z modułem avroFileWriter. Z modułem są skojarzone trzy pliki dockerfiles: Dockerfile.amd64, Dockerfile.amd64.debug i Dockerfile.arm32v7. Pliki te powinny być synchronizowane w przypadku, gdy chcemy debugować lub wdrożyć na urządzeniu arm32. W tym artykule skup się tylko na pliku Dockerfile.amd64.

1. Na komputerze deweloperskim otwórz plik **Dockerfile.amd64.**

2. Zmodyfikuj plik tak, aby wyglądał jak w poniższym przykładzie:

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

   Polecenia `mkdir` `chown` i polecenia instruują proces kompilacji platformy Docker, aby utworzyć katalog najwyższego poziomu o nazwie /avrofiles na obrazie, a następnie uczynić modułu właścicielem tego katalogu. Ważne jest, aby te polecenia były wstawiane po dodaniu użytkownika modułu do obrazu za pomocą `useradd` polecenia i przed przełączeniem kontekstu do użytkownika modułu (user moduleuser).

3. Należy wprowadzić odpowiednie zmiany w plikach Dockerfile.amd64.debug i Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Aktualizowanie konfiguracji modułu

Ostatnim krokiem tworzenia powiązania jest zaktualizowanie plików deployment.template.json (i deployment.debug.template.json) o informacje o powiązaniach.

1. Otwórz deployment.template.json.

2. Zmodyfikuj definicję modułu `Binds` dla avroFileWriter, dodając parametr, który wskazuje katalog kontenerów /avrofiles do katalogu lokalnego na urządzeniu brzegowym. Definicja modułu powinna być zgodna z tym przykładem:

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

3. Wprowadzać odpowiednie zmiany w pliku deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Bind mount w celu uzyskania dostępu do pliku config.yaml

Musimy dodać jeszcze jedno powiązanie dla modułu modułu modułu modułu modułu modułu modułu zapisującego. To powiązanie daje modułowi dostęp do odczytu ciągu połączenia z pliku /etc/iotedge/config.yaml na urządzeniu Usługi IoT Edge. Potrzebujemy ciągu połączenia, aby utworzyć IoTHubClient, dzięki\_czemu\_możemy wywołać metodę asynchronii przekazywania obiektu blob, aby przekazać pliki do centrum IoT Hub. Kroki dodawania tego powiązania są podobne do tych w poprzedniej sekcji.

#### <a name="update-directory-permission"></a>Uprawnienie do aktualizowania katalogu

1. Połącz się z urządzeniem IoT Edge za pomocą SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Dodaj uprawnienie do odczytu do pliku config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Sprawdź poprawność uprawnień są ustawione poprawnie.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Upewnij się, że uprawnienia dla pliku config.yaml są **-r--r--r--**.

#### <a name="add-directory-to-module"></a>Dodawanie katalogu do modułu

1. Na komputerze deweloperskim otwórz plik **Dockerfile.amd64.**

2. Dodaj dodatkowy zestaw `mkdir` `chown` i polecenia do pliku, tak aby wyglądał:

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

3. Należy wprowadzić odpowiednie zmiany w plikach Dockerfile.amd64.debug i Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Aktualizowanie konfiguracji modułu

1. Otwórz plik **deployment.template.json.**

2. Zmodyfikuj definicję modułu dla avroFileWriter, dodając drugi wiersz do parametru, `Binds` który wskazuje katalog kontenerów (/app/iotconfig) do lokalnego katalogu na urządzeniu (/etc/iotedge).

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

3. Wprowadzać odpowiednie zmiany w pliku deployment.debug.template.json.

## <a name="install-dependencies"></a>Instalowanie zależności

Moduł modułu modułu modułu zapisującego przyjmuje zależność od dwóch bibliotek języka Python, fastavro i PyYAML. Musimy zainstalować zależności na naszym komputerze deweloperskim i poinstruować proces kompilacji platformy Docker, aby zainstalować je na obrazie naszego modułu.

### <a name="pyyaml"></a>PyYAML (PyYAML)

1. Na komputerze deweloperskim otwórz plik **requirements.txt** i dodaj pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Otwórz plik **Dockerfile.amd64** i `pip install` dodaj polecenie uaktualnienia setuptools.

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

3. Wprowadzać odpowiednie zmiany w pliku Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Instalowanie pyyaml lokalnie, otwierając terminal w programie Visual Studio Code i wpisując tekst

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro (fastavro)

1. W requirements.txt dodaj fastavro po pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Zainstaluj fastavro na komputerze deweloperskim przy użyciu terminalu kodu programu Visual Studio.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Ponowne konfigurowanie centrum IoT

Wprowadzając do systemu urządzenie i moduły usługi IoT Edge, zmieniliśmy nasze oczekiwania dotyczące tego, jakie dane zostaną wysłane do koncentratora i w jakim celu. Musimy ponownie skonfigurować routing w centrum, aby poradzić sobie z naszą nową rzeczywistością.

> [!NOTE]
> Ponownie skonfigurujemy koncentrator przed wdrożeniem modułów, ponieważ niektóre ustawienia koncentratora, w szczególności przekazywanie plików, muszą być poprawnie skonfigurowane, aby moduł avroFileWriter działał poprawnie

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Konfigurowanie trasy dla wiadomości RUL w Centrum IoT

Z routerem i klasyfikatorem w miejscu, oczekujemy, że będą otrzymywać regularne komunikaty zawierające tylko identyfikator urządzenia i przewidywanie RUL dla urządzenia. Chcemy przekierować dane RUL do własnego miejsca przechowywania, gdzie możemy monitorować stan urządzeń, tworzyć raporty i alarmy pożarowe w razie potrzeby. Jednocześnie chcemy, aby wszystkie dane urządzenia, które są nadal wysyłane bezpośrednio przez urządzenie typu liść, które nie zostały jeszcze dołączone do naszego urządzenia Usługi IoT Edge, aby kontynuować trasę do bieżącej lokalizacji magazynu.

#### <a name="create-a-rul-message-route"></a>Tworzenie trasy wiadomości RUL

1. W witrynie Azure portal przejdź do centrum IoT Hub.

2. W lewej nawigacji wybierz pozycję **Routing wiadomości**.

3. Wybierz pozycję **Dodaj**.

4. Nazwij trasę **RulMessageRoute**.

5. Wybierz **pozycję Dodaj** obok selektora punktu **końcowego** i wybierz pozycję Magazyn obiektów **Blob**.

6. W formularzu **Dodaj punkt końcowy magazynu** nazwij punkt końcowy **ruldata**.

7. Wybierz **wybierz wybierz wybierz kontener**.

8. Wybierz konto magazynu używane w tym samouczku, który nosi nazwę **iotedgeandml\<unikatowy przyrostek\>**.

9. Wybierz kontener **ruldata** i kliknij przycisk **Wybierz**.

10. Kliknij **przycisk Utwórz,** aby utworzyć punkt końcowy magazynu.

11. W przypadku **kwerendy routingu**wprowadź następującą kwerendę:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Rozwiń sekcję **Test,** a następnie sekcję **Treść wiadomości.** Zastąp wiadomość tym przykładem naszych oczekiwanych wiadomości:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Wybierz **trasę testu**. Jeśli test zakończy się pomyślnie, zostanie wyświetlony komunikat "Komunikat dopasowany do kwerendy."

14. Kliknij przycisk **Zapisz**.

#### <a name="update-turbofandevicetostorage-route"></a>Aktualizacja trasy turbofanDeviceToStorage

Nie chcemy kierować nowych danych prognoz do naszej starej lokalizacji magazynu, więc zaktualizuj trasę, aby temu zapobiec.

1. Na stronie **Routing wiadomości** w centrum IoT wybierz kartę **Trasy.**

2. Wybierz **turbofanDeviceDataToStorage**lub dowolną nazwę nadaną początkowej trasy danych urządzenia.

3. Aktualizowanie kwerendy routingu do

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Rozwiń sekcję **Test,** a następnie sekcję **Treść wiadomości.** Zastąp wiadomość tym przykładem naszych oczekiwanych wiadomości:

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

5. Wybierz **trasę testu**. Jeśli test zakończy się pomyślnie, zostanie wyświetlony komunikat "Komunikat dopasowany do kwerendy."

6. Wybierz **pozycję Zapisz**.

### <a name="configure-file-upload"></a>Konfigurowanie przekazywania plików

Skonfiguruj funkcję przekazywania plików w centrum IoT, aby umożliwić modułowi zapisywacza plików przekazywanie plików do magazynu.

1. W lewym nawigatorze w Centrum IoT wybierz pozycję **Przekazywanie plików**.

2. Wybierz **kontener usługi Azure Storage**.

3. Wybierz konto magazynu z listy.

4. Wybierz kontener **uploadturbofanfiles** i kliknij przycisk **Wybierz**.

5. Wybierz **pozycję Zapisz**. Portal powiadamia użytkownika po zakończeniu zapisywania.

> [!Note]
> Nie włączamy powiadomienia o przekazywaniu dla tego samouczka, ale zobacz [Odbierz powiadomienie o przekazywaniu plików,](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) aby uzyskać szczegółowe informacje na temat obsługi powiadomienia o przekazywaniu plików.

## <a name="build-publish-and-deploy-modules"></a>Tworzenie, publikowanie i wdrażanie modułów

Teraz, gdy dokonaliśmy zmian konfiguracji, jesteśmy gotowi do tworzenia obrazów i publikowania ich w naszym rejestrze kontenerów platformy Azure. Proces kompilacji używa pliku deployment.template.json do określenia, które moduły muszą zostać utworzone. Ustawienia dla każdego modułu, w tym wersji, znajdują się w pliku module.json w folderze modułu. Proces kompilacji najpierw uruchamia docker kompilacji na Dockerfiles dopasowania bieżącej konfiguracji znalezionej w pliku module.json, aby utworzyć obraz. Następnie publikuje obraz do rejestru z pliku module.json z tagiem wersji pasującym do tego w pliku module.json. Na koniec tworzy manifest wdrożenia specyficzne dla konfiguracji (na przykład deployment.amd64.json), które wdrożymy na urządzeniu usługi IoT Edge. Urządzenie usługi IoT Edge odczytuje informacje z manifestu wdrażania i na podstawie instrukcji pobierze moduły, skonfiguruje trasy i ustawi wszystkie żądane właściwości. Ta metoda wdrażania ma dwa działania niepożądane, o których należy pamiętać:

* **Opóźnienie wdrożenia:** ponieważ środowisko uruchomieniowe usługi IoT Edge musi rozpoznać zmianę żądanych właściwości, zanim zacznie ponownie skonfigurować, może upłynąć trochę czasu po wdrożeniu modułów, dopóki środowisko wykonawcze nie uruchomi je i rozpocznie aktualizację urządzenia Usługi IoT Edge.

* **Wersje modułów mają znaczenie:** jeśli opublikujesz nową wersję kontenera modułu w rejestrze kontenerów przy użyciu tych samych tagów wersji, co poprzedni moduł, środowisko wykonawcze nie pobierze nowej wersji modułu. Wykonuje porównanie tagu wersji obrazu lokalnego i żądanego obrazu z manifestu wdrożenia. Jeśli te wersje są zgodne, środowisko wykonawcze nie podejmuje żadnych akcji. W związku z tym ważne jest, aby zwiększać wersję modułu za każdym razem, gdy chcesz wdrożyć nowe zmiany. Przyrost wersji, zmieniając właściwość **version** pod **właściwościtagu** w pliku module.json dla modułu, który zmieniasz. Następnie skompiluj i opublikuj moduł.

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

### <a name="build-and-publish"></a>Tworzenie i publikowanie

1. W programie Visual Studio Code na dewelopera maszyny Wirtualnej otwórz okno terminala kodu programu Visual Studio i zaloguj się do rejestru kontenerów.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. W programie Visual Studio Code kliknij prawym przyciskiem myszy na deployment.template.json i wybierz polecenie **Build and Push IoT Edge Solution**.

### <a name="view-modules-in-the-registry"></a>Wyświetlanie modułów w rejestrze

Po pomyślnym zakończeniu kompilacji będziemy mogli korzystać z witryny Azure portal do przeglądania naszych opublikowanych modułów.

1. W witrynie Azure portal przejdź do obszaru roboczego usługi Azure Machine Learning i kliknij **hiperłącze do rejestru**.

    ![Przechodzenie do rejestru z obszaru roboczego usługi uczenia maszynowego](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. W nawigatorze po stronie rejestru wybierz pozycję **Repozytoria**.

3. Należy pamiętać, że oba utworzone moduły, **avrofilewriter** i **turbofanrouter**, pojawiają się jako repozytoria.

4. Wybierz **turbofanrouter** i pamiętaj, że opublikowano jedno zdjęcie oznaczone jako 0.0.1-amd64.

   ![Zobacz pierwszą oznakowane wersji turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Wdrażanie modułów na urządzeniu IoT Edge

Zbudowaliśmy i skonfigurowaliśmy moduły w naszym rozwiązaniu, teraz wdrożymy moduły na urządzeniu IoT Edge.

1. W programie Visual Studio Code kliknij prawym przyciskiem myszy plik **deployment.amd64.json** w folderze konfiguracji.

2. Wybierz **pozycję Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz urządzenie IoT Edge, **aaTurboFanEdgeDevice**.

4. Odśwież panel urządzeń usługi Azure IoT Hub w Eksploratorze kodu programu Visual Studio. Powinieneś zobaczyć, że trzy nowe moduły są wdrożone, ale nie są jeszcze uruchomione.

5. Odśwież ponownie po kilku minutach, a zobaczysz uruchomione moduły.

   ![Wyświetlanie uruchomionych modułów w programie Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Może upłynąć kilka minut, aby moduły uruchomić i osiedlić się w stanie stałego działania. W tym czasie mogą być widoczne moduły uruchamiania i zatrzymywania, gdy próbują ustanowić połączenie z modułem centrum usługi IoT Edge.

## <a name="diagnosing-failures"></a>Diagnozowanie błędów

W tej sekcji udostępniamy kilka technik zrozumienia, co poszło nie tak z modułem lub modułami. Często błąd można najpierw dostrzec ze stanu w programie Visual Studio Code.

### <a name="identify-failed-modules"></a>Identyfikowanie nieudanych modułów

* **Kod programu Visual Studio:** Spójrz na panel urządzeń usługi Azure IoT Hub. Jeśli większość modułów jest w stanie uruchomionym, ale jeden jest zatrzymany, należy zbadać, że zatrzymany moduł dalej. Jeśli wszystkie moduły są w stanie zatrzymania przez długi okres czasu, może to również wskazywać na niepowodzenie.

* **Portal Azure:** Przechodząc do centrum IoT w portalu, a następnie znajdując stronę szczegółów urządzenia (w obszarze IoT Edge, drąż do urządzenia) może się okazać, że moduł zgłosił błąd lub nigdy nie zgłosił niczego do centrum IoT hub.

### <a name="diagnosing-from-the-device"></a>Diagnozowanie z urządzenia

Logując się do urządzenia Usługi IoT Edge, można uzyskać dostęp do wiele informacji o stanie modułów. Głównym mechanizmem, którego używamy, są polecenia platformy Docker, które pozwalają nam sprawdzić kontenery i obrazy na urządzeniu.

1. Wyświetl listę wszystkich uruchomionych kontenerów. Oczekujemy, że zobaczy kontener dla każdego modułu o nazwie odpowiadającej modułowi. Ponadto to polecenie wyświetla dokładny obraz kontenera, w tym wersję, dzięki czemu można dopasować do oczekiwań. Można również wyświetlić listę obrazów, zastępując "obraz" dla "kontener" w poleceniu.

   ```bash
   sudo docker container ls
   ```

2. Pobierz dzienniki dla kontenera. To polecenie wyprowadza wszystko, co zostało napisane do StdErr i StdOut w kontenerze. To polecenie działa dla kontenerów, które zostały uruchomione, a następnie zmarł z jakiegoś powodu. Jest również przydatne do zrozumienia, co się dzieje z edgeAgent lub edgeHub kontenerów.

   ```bash
   sudo docker container logs <container name>
   ```

3. Sprawdź pojemnik. To polecenie podaje mnóstwo informacji o obrazie. Dane mogą być filtrowane w zależności od tego, czego szukasz. Na przykład, jeśli chcesz sprawdzić, czy powiązania na avroFileWriter są poprawne, możesz użyć polecenia:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Połącz się z uruchomionym kontenerem. To polecenie może być pomocne, jeśli chcesz sprawdzić kontenera, gdy jest uruchomiony:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzyliśmy rozwiązanie IoT Edge w programie Visual Studio Code z trzema modułami, klasyfikatorem, routerem i modułem zapisu/przekazywania plików. Skonfigurowaliśmy trasy, aby umożliwić modułom komunikowanie się ze sobą na urządzeniu brzegowym, zmodyfikowaliśmy konfigurację urządzenia brzegowego i zaktualizowaliśmy pliki dockerfiles, aby zainstalować zależności i dodać instalacje wiążące do kontenerów modułów. Następnie zaktualizowaliśmy konfigurację Usługi IoT Hub, aby rozsyłać nasze wiadomości na podstawie typu i obsługiwać przekazywanie plików. Mając wszystko na swoim miejscu, wdrożyliśmy moduły na urządzeniu IoT Edge i upewniliśmy się, że moduły działają poprawnie.

Więcej informacji można znaleźć na następujących stronach:

* [Learn how to deploy modules and establish routes in IoT Edge (Dowiedz się, jak wdrażać moduły i ustanawiać trasy w usłudze IoT Edge)](module-composition.md).
* [Składnia zapytania dotyczącego routingu komunikatów usługi IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Routing komunikatów w centrum IoT: teraz z routingiem na treści wiadomości](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Przekazywanie plików za pomocą usługi IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Przejdź do następnego artykułu, aby rozpocząć wysyłanie danych i wyświetlić rozwiązanie w działaniu.

> [!div class="nextstepaction"]
> [Wysyłanie danych za pośrednictwem przezroczystej bramy](tutorial-machine-learning-edge-07-send-data-to-hub.md)
