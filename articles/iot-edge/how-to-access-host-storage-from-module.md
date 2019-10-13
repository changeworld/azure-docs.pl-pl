---
title: Użyj magazynu lokalnego urządzenia IoT Edge z poziomu modułu Azure IoT Edge | Microsoft Docs
description: Użyj zmiennych środowiskowych i Utwórz opcje, aby umożliwić dostęp modułu do magazynu lokalnego IoT Edge urządzenia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2526f33f0053b5805394a4a898af88d86187066c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301284"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Zapewnianie modułom dostępu do magazynu lokalnego na urządzeniu

Oprócz przechowywania danych przy użyciu usług Azure Storage lub magazynu kontenerów na urządzeniu można również przeznaczyć magazyn na hoście IoT Edge samo urządzenie w celu zwiększenia niezawodności, szczególnie w przypadku działania w trybie offline.

Aby skonfigurować magazyn w systemie hosta, należy utworzyć zmienną środowiskową dla modułu, która wskazuje folder magazynu w kontenerze. Następnie użyj opcji tworzenia, aby powiązać ten folder magazynu z folderem na komputerze-hoście.

Na przykład jeśli chcesz włączyć Centrum IoT Edge do przechowywania wiadomości w lokalnym magazynie urządzenia i pobrać je później, możesz skonfigurować zmienne środowiskowe i opcje tworzenia w Azure Portal w obszarze **Konfigurowanie ustawień zaawansowanego środowiska uruchomieniowego Edge** sekcja.

1. W przypadku agentów IoT Edge Hub i IoT Edge Dodaj zmienną środowiskową o nazwie **storageFolder** , która wskazuje katalog w module.
1. Dla Centrum IoT Edge i agenta IoT Edge Dodaj powiązania, aby połączyć katalog lokalny na komputerze hosta z katalogiem w module. Na przykład:

   ![Dodawanie opcji tworzenia i zmiennych środowiskowych dla magazynu lokalnego](./media/how-to-access-host-storage-from-module/offline-storage.png)

Lub można skonfigurować magazyn lokalny bezpośrednio w manifeście wdrożenia. Na przykład:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Zastąp wartości `<HostStoragePath>` i `<ModuleStoragePath>` identyfikatorem magazynu hosta i modułu; obie wartości muszą być ścieżką bezwzględną.

Na przykład w systemie Linux `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` oznacza, że katalog **/etc/iotedge/Storage** w systemie hosta jest mapowany do katalogu **/iotedge/Storage/** w kontenerze. W systemie Windows w innym przykładzie `"Binds":["C:\\temp:C:\\contemp"]` oznacza katalog **c: \\temp** w systemie hosta jest mapowany do katalogu **c: \\contemp** w kontenerze.

Ponadto na urządzeniach z systemem Linux upewnij się, że profil użytkownika dla modułu ma wymagane uprawnienia Odczyt, zapis i wykonywanie do katalogu systemu hosta. Powracanie do wcześniejszego przykładu włączenia IoT Edge centrum do przechowywania wiadomości w lokalnym magazynie urządzenia, należy przyznać uprawnienia do jego profilu użytkownika, UID 1000. (Agent IoT Edge działa jako element główny, więc nie potrzebuje dodatkowych uprawnień). Istnieje kilka sposobów zarządzania uprawnieniami katalogu w systemach Linux, w tym przy użyciu `chown` Aby zmienić właściciela katalogu, a następnie `chmod`, aby zmienić uprawnienia, takie jak:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Więcej informacji o opcjach tworzenia można znaleźć w dokumentacji [platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).
