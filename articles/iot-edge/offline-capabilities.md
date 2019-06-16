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
ms.openlocfilehash: 4f3e5c1566271573b43e24a1749b42daa7530555
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051953"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Zrozumienie rozszerzone możliwości w trybie offline dla urządzeń, moduły i podrzędny urządzenia usługi IoT Edge

Usługa Azure IoT Edge obsługuje rozszerzonych operacji w trybie offline na urządzeniach usługi IoT Edge i zbyt umożliwia operacje trybu offline na urządzeniach podrzędne — IoT Edge. Tak długo, jak urządzenia usługi IoT Edge miał jedną szansę nawiązać połączenie z Centrum IoT Hub, go i wszystkie podrzędne urządzenia można nadal funkcji z tymczasowymi lub brak połączenia internetowego. 


## <a name="how-it-works"></a>Jak to działa

Gdy urządzenia usługi IoT Edge przechodzi do trybu offline, Centrum usługi IoT Edge przenosi się na trzech ról. Po pierwsze są przechowywane komunikaty, które przejdzie nadrzędnego, a następnie zapisuje te elementy, aż urządzenie połączy się ponownie. Po drugie działa w imieniu usługi IoT Hub do uwierzytelniania modułów i podrzędny urządzeń, dzięki czemu będą one działać. Po trzecie umożliwia komunikację między urządzeniami podrzędne, które normalnie musieli przejść za pomocą usługi IoT Hub. 

Poniższy przykład pokazuje, jak scenariusza użycia usługi IoT Edge działa w trybie offline:

1. **Konfigurowanie urządzeń**

   Urządzenia usługi IoT Edge mają automatycznie możliwości w trybie offline. Aby rozszerzyć tej możliwości do innych urządzeń IoT, należy zadeklarować relacji nadrzędny podrzędny między urządzeniami w usłudze IoT Hub. Następnie możesz skonfigurować urządzenia podrzędnych zaufania urządzenia przypisane nadrzędnego do kierowania komunikacją urządzenia do chmury przez nadrzędne jako brama. 

2. **Synchronizacja z usługą IoT Hub**

   Co najmniej raz po zakończeniu instalacji środowiska uruchomieniowego usługi IoT Edge, urządzenia usługi IoT Edge musi być w trybie online w celu synchronizacji z usługą IoT Hub. W tym synchronizacji urządzenia usługi IoT Edge pobierania szczegółowych informacji o żadnych urządzeń podrzędnych do niej przypisany. Urządzenia usługi IoT Edge jednocześnie możliwość bezpiecznego aktualizuje lokalnej pamięci podręcznej umożliwia operacje trybu offline i umożliwia pobranie ustawień dla lokalnego magazynu danych telemetrycznych wiadomości. 

3. **Przejdź do trybu offline**

   Przy braku połączenia z usługi IoT Hub, urządzenia usługi IoT Edge, jego wdrożone moduły i dowolnego urządzenia IoT elementy podrzędne mogą działać na czas nieokreślony. Moduły i urządzeń podrzędnych można uruchomić i uruchom ponownie, korzystając z uwierzytelniania za pomocą usługi IoT Edge hub podczas w trybie offline. Dane telemetryczne powiązany nadrzędne do usługi IoT Hub są przechowywane lokalnie. Komunikacji między modułami, czy między urządzeniami IoT podrzędne są obsługiwane za pośrednictwem metod bezpośrednich lub wiadomości. 

4. **Połącz się ponownie i ponowna synchronizacja z usługą IoT Hub**

   Po przywróceniu połączenia z usługą IoT Hub ponownej synchronizacji urządzenia usługi IoT Edge. Lokalnie przechowywane komunikaty były dostarczane w tej samej kolejności, w którym zostały zapisane. Wszelkie różnice między żądaną i zgłaszanych właściwości modułów i urządzenia są uzgodnione. Urządzenia usługi IoT Edge aktualizuje wszystkie zmiany do jego zestawu urządzeń IoT przypisane podrzędnych.

## <a name="restrictions-and-limits"></a>Limity i ograniczenia

Rozszerzone możliwości w trybie offline, opisane w tym artykule są dostępne w [usługi IoT Edge wersji 1.0.4 lub nowszej](https://github.com/Azure/azure-iotedge/releases). Wcześniejszych wersjach mają jakiś podzestaw funkcji w trybie offline. Istniejące usługi IoT Edge urządzenia, które nie mają rozszerzone możliwości w trybie offline nie może zostać uaktualniona, zmieniając wersję środowiska uruchomieniowego, ale musi zostać ponownie skonfigurowany z nową tożsamość urządzenia usługi IoT Edge w celu uzyskania tych funkcji. 

Rozszerzona pomoc techniczna w trybie offline jest dostępna we wszystkich regionach, w których usługi IoT Hub jest dostępna, **z wyjątkiem** wschodnie stany USA.

Tylko urządzenia — IoT Edge można dodać jako urządzenia podrzędnych. 

Urządzenia usługi IoT Edge i ich urządzeń przypisanych podrzędne może działać na czas nieokreślony w trybie offline, po zakończeniu synchronizacji początkowej, jednorazowe. Przechowywanie komunikatów zależy jednak od tego, jaki czas wygaśnięcia (TTL), ustawienia i dostępnego miejsca na dysku do przechowywania komunikatów. 

## <a name="set-up-parent-and-child-devices"></a>Konfigurowanie urządzeń nadrzędne i podrzędne

Urządzenia usługi IoT Edge rozszerzyć jej rozszerzone możliwości w trybie offline na urządzeniach IoT podrzędnych należy wykonać dwie czynności. Najpierw należy zadeklarować nadrzędny podrzędny relacje w witrynie Azure portal. Po drugie utworzyć relację zaufania między urządzeniem nadrzędnego i podrzędnego żadnych urządzeń z, a następnie skonfigurować komunikację urządzenia do chmury przechodzić przez nadrzędne jako brama. 

### <a name="assign-child-devices"></a>Przypisz urządzenia podrzędne

Urządzeń podrzędnych może być dowolnym urządzeniu — IoT Edge zarejestrowany w tym samym Centrum IoT Hub. Nadrzędny urządzenia mogą mieć wiele urządzeń podrzędnych, ale urządzenie podrzędny może mieć tylko jedną jednostkę nadrzędną. Dostępne są trzy opcje, aby ustawić podrzędnych urządzenia na urządzeniu usługi edge: za pośrednictwem witryny Azure portal przy użyciu wiersza polecenia platformy Azure lub przy użyciu zestawu SDK usługi IoT Hub. 

Poniższe sekcje zawierają przykłady sposobu można zadeklarować relacji nadrzędny/podrzędny w usłudze IoT Hub dla istniejących urządzeń IoT. Jeśli tworzysz nowych tożsamości urządzeń dla dziecka urządzeń, zobacz [uwierzytelnienia podrzędnym urządzenia do usługi Azure IoT Hub](how-to-authenticate-downstream-device.md) Aby uzyskać więcej informacji.

#### <a name="option-1-iot-hub-portal"></a>Opcja 1: Portal usługi IoT Hub

Można zadeklarować relacji nadrzędny podrzędny, podczas tworzenia nowego urządzenia. Lub w przypadku istniejących urządzeń można zadeklarować relację ze strony szczegółów urządzenia macierzyste urządzenie usługi IoT Edge lub urządzenia IoT podrzędnego. 

   ![Zarządzanie urządzeniami podrzędnych z poziomu strony szczegółów urządzenia usługi IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Opcja 2: Użyj `az` narzędzie wiersza polecenia

Za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) z [rozszerzenia IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 lub nowszej), można zarządzać nadrzędny podrzędny relacji z [tożsamości urządzenia](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) podpoleceń polecenia. W poniższym przykładzie użyto zapytania, można przypisać wszystkich urządzeń — IoT Edge w piaście aby ądzenia podrzędnych urządzenia usługi IoT Edge. 

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

Możesz zmodyfikować [zapytania](../iot-hub/iot-hub-devguide-query-language.md) można wybrać podzestaw różnych urządzeń. Wykonanie polecenia może potrwać kilka sekund, jeśli określisz duży zbiór urządzeń.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opcja 3: Użyj zestawu SDK usługi IoT Hub 

Na koniec można zarządzać relacji nadrzędny podrzędny, programowo przy użyciu C#, Java lub Node.js zestawu SDK usługi IoT Hub. Oto [przykład przypisywania urządzenia podrzędnego](https://aka.ms/set-child-iot-device-c-sharp) przy użyciu C# zestawu SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Konfigurowanie urządzenia nadrzędnego jako bramy

Można potraktować relacji nadrzędny/podrzędny rolę przezroczystej bramy, w którym urządzenie podrzędnych ma własną tożsamość w usłudze IoT Hub, ale komunikuje się za pośrednictwem chmury, za pośrednictwem jego obiektu nadrzędnego. Do bezpiecznej komunikacji urządzenia podrzędnego musi mieć możliwość weryfikacji, że urządzenie nadrzędne pochodzi z zaufanego źródła. W przeciwnym razie innych firm, można skonfigurować złośliwego urządzeń w celu personifikacji rodziców i przechwycić komunikacji. 

Jednym ze sposobów tworzenia tej relacji zaufania jest szczegółowo opisane w następujących artykułach: 
* [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md)
* [Podłącz urządzenie podrzędne (podrzędne) do bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Określ serwer DNS 

W celu poprawy niezawodności, zdecydowanie zaleca się określić adresy serwera DNS, które są używane w danym środowisku. Zobacz dwie opcje [Ustaw serwer DNS w artykuł dotyczący rozwiązywania problemów](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Opcjonalne ustawienia w trybie offline

Jeśli urządzenie przejdą w tryb offline, urządzenia usługi IoT Edge nadrzędne są przechowywane wszystkie komunikaty z urządzenia do chmury, przed ponownym ustanowieniu połączenia. Moduł usługi IoT Edge hub zarządza przechowywaniem i przesyłanie dalej wiadomości w trybie offline. W przypadku urządzeń, które może przejść do trybu offline przez dłuższy czas należy zoptymalizować wydajność, konfigurując dwa ustawienia Centrum IoT Edge. 

Po pierwsze wydłużenie czasu na żywo ustawienie, aby Centrum usługi IoT Edge zostanie zachowana, aby komunikaty wystarczająco długi, dla Twojego urządzenia w celu ponownego nawiązania połączenia. Następnie należy dodać dodatkowe miejsce na dysku dla magazynu komunikatów. 

### <a name="time-to-live"></a>Czas wygaśnięcia

Czas wygaśnięcia ustawienie to ilość czasu (w sekundach) oczekiwania przez komunikat do dostarczenia przed jego wygaśnięciem. Wartość domyślna to 7200 sekund (2 godziny). Wartość maksymalna jest ograniczona tylko przez wartość maksymalna zmienną całkowitoliczbową, czyli około 2 mld. 

To ustawienie jest żądaną właściwość Centrum IoT Edge, który jest przechowywany w bliźniaczej reprezentacji modułu. Można go skonfigurować w witrynie Azure portal lub bezpośrednio w pliku manifestu wdrożenia. 

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

Wiadomości są domyślnie przechowywane w kontenerze Centrum IoT Edge w systemie plików. Jeśli tego ilość miejsca jest niewystarczająca do potrzeb w trybie offline, można przypisać magazyn lokalny na urządzeniu usługi IoT Edge. Utwórz zmienną środowiskową dla Centrum IoT Edge, który wskazuje na folder przechowywania w kontenerze. Następnie użyj opcji tworzenia, aby powiązać ten folder storage do folderu na komputerze hosta. 

Można skonfigurować w witrynie Azure portal w zmiennych środowiskowych i opcji tworzenia dla modułu usługi IoT Edge hub **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge** sekcji. Ewentualnie można skonfigurować go bezpośrednio w pliku manifestu wdrożenia. 

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

Zastąp `<HostStoragePath>` i `<ModuleStoragePath>` z hosta i modułu magazynu ścieżka magazynu ścieżki; hosta i moduł musi być ścieżką bezwzględną. Za pomocą opcji tworzenia powiązania ścieżki magazynu hosta i modułów ze sobą. Następnie Utwórz zmienną środowiskową, wskazujący ścieżka magazynu modułu.  

Na przykład `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` oznacza, że katalog **/etc/iotedge/storage** na hoście systemu jest zamapowany na katalog **/iotedge/storage/** w kontenerze. Lub inny przykład dla systemów Windows, `"Binds":["C:\\temp:C:\\contemp"]` oznacza, że katalog **C:\\temp** na hoście systemu jest zamapowany na katalog **C:\\contemp** w kontenerze. 

Można także znaleźć więcej szczegółów na temat tworzenia opcje [dokumentacja platformy docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat sposobu konfigurowania przezroczystej bramy dla połączeń urządzeń nadrzędne/podrzędne: 

* [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md)
* [Uwierzytelnienia podrzędnym urządzenia do usługi Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Podłącz urządzenie z podrzędnych do bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md)
