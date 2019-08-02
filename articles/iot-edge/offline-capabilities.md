---
title: Obsługuj urządzenia w trybie offline — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak urządzenia usługi IoT Edge i moduły mogą działać bez połączenia internetowego przez dłuższy czas i jak włączyć regularne urządzeniach IoT, aby działać w trybie offline za usługi IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4a46128d3b0e77ff7921e1f4875c318a95309769
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598607"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Informacje o rozszerzonych możliwościach trybu offline dla urządzeń IoT Edge, modułów i urządzeń podrzędnych

Azure IoT Edge obsługuje rozszerzone operacje offline na urządzeniach IoT Edge, a także włącza operacje w trybie offline na nieIoT Edgeych urządzeniach podrzędnych. Tak długo, jak urządzenia usługi IoT Edge miał jedną szansę nawiązać połączenie z Centrum IoT Hub, go i wszystkie podrzędne urządzenia można nadal funkcji z tymczasowymi lub brak połączenia internetowego. 


## <a name="how-it-works"></a>Jak to działa

Gdy urządzenie IoT Edge przejdzie w tryb offline, centrum IoT Edge przyjmuje trzy role. Po pierwsze są przechowywane komunikaty, które przejdzie nadrzędnego, a następnie zapisuje te elementy, aż urządzenie połączy się ponownie. Po drugie działa w imieniu usługi IoT Hub do uwierzytelniania modułów i podrzędny urządzeń, dzięki czemu będą one działać. Po trzecie umożliwia komunikację między urządzeniami podrzędne, które normalnie musieli przejść za pomocą usługi IoT Hub. 

Poniższy przykład pokazuje, jak scenariusza użycia usługi IoT Edge działa w trybie offline:

1. **Konfigurowanie urządzeń**

   Urządzenia usługi IoT Edge mają automatycznie możliwości w trybie offline. Aby rozszerzyć tej możliwości do innych urządzeń IoT, należy zadeklarować relacji nadrzędny podrzędny między urządzeniami w usłudze IoT Hub. Następnie należy skonfigurować urządzenia podrzędne do ufania przypisanym urządzeniu nadrzędnym i skierowanie komunikacji między urządzeniem a chmurą za pośrednictwem elementu nadrzędnego jako bramy. 

2. **Synchronizuj z IoT Hub**

   Co najmniej raz po zakończeniu instalacji środowiska uruchomieniowego usługi IoT Edge, urządzenia usługi IoT Edge musi być w trybie online w celu synchronizacji z usługą IoT Hub. W tym synchronizacji urządzenia usługi IoT Edge pobierania szczegółowych informacji o żadnych urządzeń podrzędnych do niej przypisany. Urządzenia usługi IoT Edge jednocześnie możliwość bezpiecznego aktualizuje lokalnej pamięci podręcznej umożliwia operacje trybu offline i umożliwia pobranie ustawień dla lokalnego magazynu danych telemetrycznych wiadomości. 

3. **Przejdź do trybu offline**

   Przy braku połączenia z usługi IoT Hub, urządzenia usługi IoT Edge, jego wdrożone moduły i dowolnego urządzenia IoT elementy podrzędne mogą działać na czas nieokreślony. Moduły i urządzenia podrzędne można uruchomić i uruchomić ponownie, uwierzytelniając się przy użyciu Centrum IoT Edge w trybie offline. Dane telemetryczne powiązany nadrzędne do usługi IoT Hub są przechowywane lokalnie. Komunikacji między modułami, czy między urządzeniami IoT podrzędne są obsługiwane za pośrednictwem metod bezpośrednich lub wiadomości. 

4. **Ponowne łączenie i ponowne synchronizowanie za pomocą IoT Hub**

   Po przywróceniu połączenia z usługą IoT Hub ponownej synchronizacji urządzenia usługi IoT Edge. Lokalnie przechowywane komunikaty były dostarczane w tej samej kolejności, w którym zostały zapisane. Wszelkie różnice między żądaną i zgłaszanych właściwości modułów i urządzenia są uzgodnione. Urządzenia usługi IoT Edge aktualizuje wszystkie zmiany do jego zestawu urządzeń IoT przypisane podrzędnych.

## <a name="restrictions-and-limits"></a>Limity i ograniczenia

Rozszerzone możliwości trybu offline opisane w tym artykule są dostępne w [IoT Edge w wersji 1.0.7 lub nowszej](https://github.com/Azure/azure-iotedge/releases). Wcześniejszych wersjach mają jakiś podzestaw funkcji w trybie offline. Istniejące usługi IoT Edge urządzenia, które nie mają rozszerzone możliwości w trybie offline nie może zostać uaktualniona, zmieniając wersję środowiska uruchomieniowego, ale musi zostać ponownie skonfigurowany z nową tożsamość urządzenia usługi IoT Edge w celu uzyskania tych funkcji. 

Rozszerzona pomoc techniczna w trybie offline jest dostępna we wszystkich regionach, w których usługi IoT Hub jest dostępna, **z wyjątkiem** wschodnie stany USA.

Tylko urządzenia inne niż IoT Edge mogą być dodawane jako urządzenia podrzędne. 

Urządzenia usługi IoT Edge i ich urządzeń przypisanych podrzędne może działać na czas nieokreślony w trybie offline, po zakończeniu synchronizacji początkowej, jednorazowe. Przechowywanie komunikatów zależy jednak od tego, jaki czas wygaśnięcia (TTL), ustawienia i dostępnego miejsca na dysku do przechowywania komunikatów. 

## <a name="set-up-parent-and-child-devices"></a>Konfigurowanie urządzeń nadrzędnych i podrzędnych

Aby urządzenie IoT Edge rozszerać swoje rozszerzone możliwości trybu offline na podrzędne urządzenia IoT, należy wykonać dwa kroki. Najpierw Zadeklaruj relacje nadrzędny-podrzędny w Azure Portal. Następnie należy utworzyć relację zaufania między urządzeniem nadrzędnym i wszystkimi urządzeniami podrzędnymi, a następnie skonfigurować komunikację między urządzeniem a chmurą, aby przejść przez element nadrzędny jako bramę. 

### <a name="assign-child-devices"></a>Przypisz urządzenia podrzędne

Urządzenia podrzędne mogą być dowolnym urządzeniem innym niż IoT Edge zarejestrowanym w tej samej IoT Hub. Urządzenia nadrzędne mogą mieć wiele urządzeń podrzędnych, ale urządzenie podrzędne ma tylko jeden element nadrzędny. Istnieją trzy opcje ustawiania urządzeń podrzędnych na urządzeniu brzegowym: za pośrednictwem Azure Portal przy użyciu interfejsu wiersza polecenia platformy Azure lub zestawu SDK usługi IoT Hub. 

Poniższe sekcje zawierają przykłady sposobu deklarowania relacji nadrzędny/podrzędny w IoT Hub istniejących urządzeń IoT. Jeśli tworzysz nowe tożsamości urządzeń dla urządzeń podrzędnych, zobacz temat [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md) , aby uzyskać więcej informacji.

#### <a name="option-1-iot-hub-portal"></a>Option 1: Portal IoT Hub

Można zadeklarować relację nadrzędny-podrzędny podczas tworzenia nowego urządzenia. Lub w przypadku istniejących urządzeń można zadeklarować relację ze strony Szczegóły urządzenia na nadrzędnym urządzeniu IoT Edge lub podrzędnym urządzeniu IoT. 

   ![Zarządzanie urządzeniami podrzędnych z poziomu strony szczegółów urządzenia usługi IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Opcja 2: Korzystanie z `az` narzędzia wiersza polecenia

Korzystając z [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) z [rozszerzeniem IoT](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 lub nowszy), można zarządzać relacjami nadrzędnych obiektów podrzędnych za pomocą podpoleceń [tożsamości urządzenia](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) . W poniższym przykładzie używa się zapytania do przypisywania wszystkich urządzeń, które nie są IoT Edge w koncentratorze, jako urządzeń podrzędnych urządzenia IoT Edge. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

Możesz zmodyfikować [zapytanie](../iot-hub/iot-hub-devguide-query-language.md) , aby wybrać inny podzbiór urządzeń. Wykonanie polecenia może potrwać kilka sekund w przypadku określenia dużego zestawu urządzeń.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opcja 3: Korzystanie z zestawu SDK usługi IoT Hub 

Można na koniec zarządzać relacjami nadrzędnymi podrzędnymi przy C#użyciu zestawu SDK usługi IoT Hub Java lub Node. js. Oto [przykład przypisywania urządzenia podrzędnego](https://aka.ms/set-child-iot-device-c-sharp) przy użyciu C# zestawu SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Skonfiguruj urządzenie nadrzędne jako bramę

Relację nadrzędny/podrzędny można traktować jako nieprzezroczystą bramę, w której urządzenie podrzędne ma własną tożsamość w IoT Hub ale komunikuje się za pośrednictwem chmury za pośrednictwem jego elementu nadrzędnego. W celu zapewnienia bezpiecznej komunikacji urządzenie podrzędne musi mieć możliwość sprawdzenia, czy urządzenie nadrzędne pochodzi z zaufanego źródła. W przeciwnym razie inne firmy mogą skonfigurować złośliwe urządzenia w celu personifikacji rodziców i przechwycenia komunikacji. 

Jednym ze sposobów tworzenia tej relacji zaufania jest szczegółowo opisany w następujących artykułach: 
* [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md)
* [Łączenie podrzędnego urządzenia (podrzędnego) z bramą Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Określ serwery DNS 

W celu zwiększenia niezawodności należy określić adresy serwerów DNS używane w danym środowisku. Aby [ustawić serwer DNS w artykule dotyczącym rozwiązywania problemów,](troubleshoot.md#resolution-7)Zobacz dwie opcje.

## <a name="optional-offline-settings"></a>Opcjonalne ustawienia w trybie offline

Jeśli urządzenia przechodzą w tryb offline, urządzenie nadrzędne IoT Edge przechowuje wszystkie komunikaty z urządzenia do chmury do momentu ponownego nawiązania połączenia. Moduł IoT Edge Hub zarządza przechowywaniem i przekazywaniem komunikatów w trybie offline. W przypadku urządzeń, które mogą przechodzić w tryb offline przez dłuższy czas, należy zoptymalizować wydajność, konfigurując dwa IoT Edge ustawienia centrum. 

Najpierw Zwiększ ustawienie czasu wygaśnięcia, aby zapewnić, że usługa IoT Edge Hub będzie utrzymywać wystarczająco dużo pamięci, aby umożliwić ponowne nawiązanie połączenia przez urządzenie. Następnie Dodaj dodatkowe miejsce na dysku do przechowywania komunikatów. 

### <a name="time-to-live"></a>Czas wygaśnięcia

Czas wygaśnięcia ustawienie to ilość czasu (w sekundach) oczekiwania przez komunikat do dostarczenia przed jego wygaśnięciem. Wartość domyślna to 7200 sekund (2 godziny). Wartość maksymalna jest ograniczona tylko przez maksymalną wartość zmiennej liczb całkowitych, która jest około 2 000 000 000. 

To ustawienie jest odpowiednią właściwością Centrum IoT Edge, które jest przechowywane w postaci dwuosiowego modułu. Można ją skonfigurować w Azure Portal lub bezpośrednio w manifeście wdrożenia. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>Dodatkowy magazyn w trybie offline

Komunikaty są domyślnie przechowywane w systemie plików kontenerów IoT Edge centrum. Jeśli tego ilość miejsca jest niewystarczająca do potrzeb w trybie offline, można przypisać magazyn lokalny na urządzeniu usługi IoT Edge. Utwórz zmienną środowiskową dla Centrum IoT Edge, która wskazuje folder magazynu w kontenerze. Następnie użyj opcji tworzenia, aby powiązać ten folder storage do folderu na komputerze hosta. 

Zmienne środowiskowe i opcje tworzenia dla modułu IoT Edge Hub można skonfigurować w Azure Portal w sekcji **Konfigurowanie ustawień zaawansowanego środowiska uruchomieniowego Edge** . Ewentualnie można skonfigurować go bezpośrednio w pliku manifestu wdrożenia. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Zastąp `<HostStoragePath>` i `<ModuleStoragePath>` z hosta i modułu magazynu ścieżka magazynu ścieżki; hosta i moduł musi być ścieżką bezwzględną. W opcjach tworzenia Powiąż wszystkie ścieżki magazynu hosta i modułu. Następnie utwórz zmienną środowiskową, która wskazuje ścieżkę magazynu modułów.  

Na przykład oznacza `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` , że katalog **/etc/iotedge/Storage** w systemie hosta jest mapowany do katalogu **/iotedge/Storage/** w kontenerze. Inny przykład dla systemów Windows oznacza, `"Binds":["C:\\temp:C:\\contemp"]` że katalog **c:\\temp** w systemie hosta jest mapowany do katalogu **c\\:** na tymczasowej sekcji kontenera. 

Więcej informacji na temat opcji tworzenia można znaleźć w dokumentacji [platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o konfigurowaniu przezroczystej bramy dla połączeń urządzeń nadrzędnych/podrzędnych: 

* [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md)
* [Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Łączenie urządzenia podrzędnego z bramą Azure IoT Edge](how-to-connect-downstream-device.md)
