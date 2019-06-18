---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 7058d7f46373f8adaacbcbf90e5ea591a15f8f37
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "67125617"
---
Na urządzeniu krawędź pola danych, które ma rolę obliczeń skonfigurowane, podzbiór platformy docker do monitorowania lub Rozwiązywanie problemów z modułami dostępnych poleceń. Aby wyświetlić listę dostępnych poleceń [nawiązać połączenie z interfejsu programu PowerShell](#connect-to-the-powershell-interface) i użyj `dkrdbe` funkcji.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
Poniższa tabela zawiera krótki opis poleceń dostępnych dla `dkrdbe`:

|Polecenie  |Opis |
|---------|---------|
|`image`     | Zarządzanie obrazami       |
|`images`     | Tworzenie listy obrazów         |
|`inspect`     | Zwraca informacje niskiego poziomu na obiekty platformy Docker         |
|`login`     | Zaloguj się do rejestru platformy Docker         |
|`logout`     | Wyloguj się z rejestrem platformy Docker         |
|`logs`     | Pobierz dzienniki kontenera        |
|`port`     | Lista mapowania portów lub określone mapowanie dla kontenera        |
|`ps`     | Wyświetlanie listy kontenerów        |
|`pull`     | Ściągnij obraz lub repozytorium z rejestru         |
|`start`     | Uruchom co najmniej jeden kontener zatrzymania         |
|`stats`     | Wyświetlanie statystyk użycia zasobów kontenerów strumienia na żywo         |
|`stop`     | Zatrzymać co najmniej jeden uruchomione kontenery        |
|`system`     | Manage Docker         |
|`top`     | Wyświetl uruchomione procesy kontenera         |

Aby uzyskać pomoc dotyczącą dowolnego dostępne polecenia, należy użyć `dkrdbe <command-name> --help`.

Na przykład, aby zrozumieć wykorzystanie `port` polecenia, wpisz:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

Dostępne polecenia `dkrdbe` funkcji są używane te same parametry niż te używane dla poleceń docker normalny. Aby uzyskać opcje i parametry używane z polecenia docker, przejdź do [użyć wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>Aby sprawdzić, jeśli moduł wdrożony pomyślnie

Że moduły są kontenery zawierające logikę biznesową, zaimplementowane mocy obliczeniowej. Aby sprawdzić, jeśli moduł obliczeniowy zostaje wdrożony pomyślnie, należy uruchomić `ps` poleceń i sprawdź, czy kontener (odpowiadający moduł obliczeniowy) jest uruchomiona.

Aby uzyskać listę wszystkich kontenerów (w tym te, które są wstrzymane), uruchom `ps -a` polecenia.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Jeśli wystąpił błąd podczas tworzenia obrazu kontenera lub podczas ściągania obrazu, należy uruchomić `logs edgeAgent`.  `EdgeAgent` jest kontenerem środowisko uruchomieniowe usługi IoT Edge, który jest odpowiedzialny za aprowizowanie inne kontenery.

Ponieważ `logs edgeAgent` zrzuty dzienników, sposób sprawdzić, ostatnie błędy, należy użyć opcji `--tail 20`.


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>Aby uzyskać dzienniki kontenerów

Aby pobrać dzienniki dla określonego kontenera, najpierw listy kontenera, a następnie dzienniki dla kontenera, do którego interesuje Cię.

1. [Nawiązać połączenie z interfejsu programu PowerShell](#connect-to-the-powershell-interface).
2. Aby uzyskać listę uruchomionych kontenerów, uruchom `ps` polecenia.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Zanotuj identyfikator kontenera dla kontenera, którego potrzebujesz w dziennikach.

4. Aby pobrać dzienniki dla kontenera dla, uruchomić `logs` polecenie, podając identyfikator kontenera.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Do monitorowania statystyk użycia urządzenia

Aby monitorować pamięci, użycie procesora CPU i we/wy na urządzeniu, należy użyć `stats` polecenia.

1. [Nawiązać połączenie z interfejsu programu PowerShell](#connect-to-the-powershell-interface).
2. Uruchom `stats` polecenia tak, aby wyłączyć transmisji strumieniowej na żywo i ściąganie tylko pierwszego wyniku.

   ```powershell
   dkrdbe stats --no-stream
   ```

   Poniższy przykład przedstawia sposób użycia tego polecenia cmdlet:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

