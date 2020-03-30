---
title: Korzystanie z magazynu lokalnego urządzenia usługi IoT Edge z modułu — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Użyj zmiennych środowiskowych i utwórz opcje, aby umożliwić dostęp modułu do lokalnej pamięci masowej urządzenia Usługi IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434518"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Zapewnianie modułom dostępu do magazynu lokalnego na urządzeniu

Oprócz przechowywania danych przy użyciu usług magazynu platformy Azure lub w magazynie kontenerów urządzenia można również poświęcić magazyn na samym urządzeniu usługi Host IoT Edge, aby zwiększyć niezawodność, szczególnie podczas pracy w trybie offline.

## <a name="link-module-storage-to-device-storage"></a>Łączenie pamięci masowej modułów z pamięcią urządzenia

Aby włączyć łącze z magazynu modułu do magazynu w systemie hosta, należy utworzyć zmienną środowiskową dla modułu, która wskazuje folder magazynu w kontenerze. Następnie użyj opcji tworzenia, aby powiązać ten folder magazynu z folderem na komputerze-hoście.

Na przykład jeśli chcesz włączyć centrum usługi IoT Edge do przechowywania wiadomości w magazynie lokalnym urządzenia i pobrać je później, można skonfigurować zmienne środowiskowe i opcje tworzenia w witrynie Azure portal w **ustawieniach środowiska wykonawczego** sekcji.

1. Dla centrum IoT Edge i agenta usługi IoT Edge należy dodać zmienną środowiskową o nazwie **storageFolder,** która wskazuje katalog w module.
1. W przypadku koncentratora usługi IoT Edge i agenta usługi IoT Edge należy dodać powiązania w celu połączenia katalogu lokalnego na komputerze-hoście z katalogiem w module. Przykład:

   ![Dodawanie opcji tworzenia i zmiennych środowiskowych dla magazynu lokalnego](./media/how-to-access-host-storage-from-module/offline-storage.png)

Lub można skonfigurować magazyn lokalny bezpośrednio w manifeście wdrażania. Przykład:

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

Zastąp `<HostStoragePath>` i `<ModuleStoragePath>` ścieżkę przechowywania hosta i modułu; obie wartości muszą być ścieżką bezwzględną.

Na przykład w systemie `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` Linux oznacza, że katalog **/etc/iotedge/storage** w systemie hosta jest mapowany do katalogu **/iotedge/storage/** w kontenerze. W systemie Windows, jako `"Binds":["C:\\temp:C:\\contemp"]` inny przykład, oznacza, że katalog **\\C: temp** w systemie hosta jest mapowany do katalogu **C:\\contemp** w kontenerze.

Ponadto na urządzeniach z systemem Linux upewnij się, że profil użytkownika modułu ma wymagane uprawnienia do odczytu, zapisu i wykonywania do katalogu systemu hosta. Wracając do wcześniejszego przykładu włączania usługi IoT Edge hub do przechowywania wiadomości w magazynie lokalnym urządzenia, należy udzielić uprawnień do jego profilu użytkownika, UID 1000. (Agent usługi IoT Edge działa jako katalog główny, więc nie potrzebuje dodatkowych uprawnień). Istnieje kilka sposobów zarządzania uprawnieniami do katalogów `chown` w systemach Linux, `chmod` w tym używanie do zmiany właściciela katalogu, a następnie zmienianie uprawnień, takich jak:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Więcej informacji na temat tworzenia opcji można znaleźć w [docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowy przykład uzyskiwania dostępu do magazynu hosta z modułu, zobacz [Przechowywanie danych na krawędzi za pomocą usługi Azure Blob Storage w usłudze IoT Edge.](how-to-store-data-blob.md)
