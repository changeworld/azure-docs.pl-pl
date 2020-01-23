---
title: Obsługuj urządzenia w trybie offline — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak urządzenia usługi IoT Edge i moduły mogą działać bez połączenia internetowego przez dłuższy czas i jak włączyć regularne urządzeniach IoT, aby działać w trybie offline za usługi IoT Edge.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a057eac8d2a0114cb58f738277e3e9a8fed90672
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548666"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Informacje o rozszerzonych możliwościach trybu offline dla urządzeń IoT Edge, modułów i urządzeń podrzędnych

Azure IoT Edge obsługuje rozszerzone operacje offline na urządzeniach IoT Edge, a także włącza operacje w trybie offline na nieIoT Edgeych urządzeniach podrzędnych. Tak długo, jak urządzenie IoT Edge miało jedną okazję do nawiązania połączenia z IoT Hub, to urządzenie i wszystkie urządzenia podrzędne mogą nadal działać z nieprzerwanym lub bez połączenia internetowego.

## <a name="how-it-works"></a>Zasady działania

Gdy urządzenie IoT Edge przejdzie w tryb offline, centrum IoT Edge przyjmuje trzy role. Po pierwsze są przechowywane komunikaty, które przejdzie nadrzędnego, a następnie zapisuje te elementy, aż urządzenie połączy się ponownie. Po drugie działa w imieniu usługi IoT Hub do uwierzytelniania modułów i podrzędny urządzeń, dzięki czemu będą one działać. Po trzecie umożliwia komunikację między urządzeniami podrzędne, które normalnie musieli przejść za pomocą usługi IoT Hub.

Poniższy przykład pokazuje, jak scenariusza użycia usługi IoT Edge działa w trybie offline:

1. **Konfigurowanie urządzeń**

   Urządzenia usługi IoT Edge mają automatycznie możliwości w trybie offline. Aby rozszerzyć tej możliwości do innych urządzeń IoT, należy zadeklarować relacji nadrzędny podrzędny między urządzeniami w usłudze IoT Hub. Następnie należy skonfigurować urządzenia podrzędne do ufania przypisanym urządzeniu nadrzędnym i skierowanie komunikacji między urządzeniem a chmurą za pośrednictwem elementu nadrzędnego jako bramy.

2. **Synchronizuj z IoT Hub**

   Co najmniej raz po zakończeniu instalacji środowiska uruchomieniowego usługi IoT Edge, urządzenia usługi IoT Edge musi być w trybie online w celu synchronizacji z usługą IoT Hub. W tym synchronizacji urządzenia usługi IoT Edge pobierania szczegółowych informacji o żadnych urządzeń podrzędnych do niej przypisany. Urządzenia usługi IoT Edge jednocześnie możliwość bezpiecznego aktualizuje lokalnej pamięci podręcznej umożliwia operacje trybu offline i umożliwia pobranie ustawień dla lokalnego magazynu danych telemetrycznych wiadomości.

3. **Przejdź do trybu offline**

   Przy braku połączenia z usługi IoT Hub, urządzenia usługi IoT Edge, jego wdrożone moduły i dowolnego urządzenia IoT elementy podrzędne mogą działać na czas nieokreślony. Moduły i urządzenia podrzędne można uruchomić i uruchomić ponownie, uwierzytelniając się przy użyciu Centrum IoT Edge w trybie offline. Dane telemetryczne powiązany nadrzędne do usługi IoT Hub są przechowywane lokalnie. Komunikacji między modułami, czy między urządzeniami IoT podrzędne są obsługiwane za pośrednictwem metod bezpośrednich lub wiadomości.

4. **Ponowne łączenie i ponowne synchronizowanie za pomocą IoT Hub**

   Po przywróceniu połączenia z usługą IoT Hub ponownej synchronizacji urządzenia usługi IoT Edge. Komunikaty przechowywane lokalnie są dostarczane do IoT Hub od razu, ale są zależne od szybkości połączenia, opóźnień IoT Hub i powiązanych czynników. Są one dostarczane w takiej samej kolejności, w jakiej były przechowywane.

   Wszelkie różnice między żądaną i zgłaszanych właściwości modułów i urządzenia są uzgodnione. Urządzenia usługi IoT Edge aktualizuje wszystkie zmiany do jego zestawu urządzeń IoT przypisane podrzędnych.

## <a name="restrictions-and-limits"></a>Limity i ograniczenia

Rozszerzone możliwości trybu offline opisane w tym artykule są dostępne w [IoT Edge w wersji 1.0.7 lub nowszej](https://github.com/Azure/azure-iotedge/releases). Wcześniejszych wersjach mają jakiś podzestaw funkcji w trybie offline. Istniejące usługi IoT Edge urządzenia, które nie mają rozszerzone możliwości w trybie offline nie może zostać uaktualniona, zmieniając wersję środowiska uruchomieniowego, ale musi zostać ponownie skonfigurowany z nową tożsamość urządzenia usługi IoT Edge w celu uzyskania tych funkcji.

Tylko urządzenia inne niż IoT Edge mogą być dodawane jako urządzenia podrzędne.

Urządzenia IoT Edge i przypisane do nich urządzenia podrzędne mogą działać w trybie offline po początkowej synchronizacji jednorazowej. Jednak przechowywanie komunikatów zależy od ustawienia czasu wygaśnięcia (TTL) i dostępnego miejsca na dysku do przechowywania komunikatów.

## <a name="set-up-parent-and-child-devices"></a>Konfigurowanie urządzeń nadrzędnych i podrzędnych

Aby urządzenie IoT Edge rozszerać swoje rozszerzone możliwości trybu offline na podrzędne urządzenia IoT, należy wykonać dwa kroki. Najpierw Zadeklaruj relacje nadrzędny-podrzędny w Azure Portal. Następnie należy utworzyć relację zaufania między urządzeniem nadrzędnym i wszystkimi urządzeniami podrzędnymi, a następnie skonfigurować komunikację między urządzeniem a chmurą, aby przejść przez element nadrzędny jako bramę.

### <a name="assign-child-devices"></a>Przypisz urządzenia podrzędne

Urządzenia podrzędne mogą być dowolnym urządzeniem innym niż IoT Edge zarejestrowanym w tej samej IoT Hub. Urządzenia nadrzędne mogą mieć wiele urządzeń podrzędnych, ale urządzenie podrzędne ma tylko jeden element nadrzędny. Istnieją trzy opcje ustawiania urządzeń podrzędnych na urządzeniu brzegowym: za pośrednictwem Azure Portal przy użyciu interfejsu wiersza polecenia platformy Azure lub zestawu SDK usługi IoT Hub.

Poniższe sekcje zawierają przykłady sposobu deklarowania relacji nadrzędny/podrzędny w IoT Hub istniejących urządzeń IoT. Jeśli tworzysz nowe tożsamości urządzeń dla urządzeń podrzędnych, zobacz temat [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md) , aby uzyskać więcej informacji.

#### <a name="option-1-iot-hub-portal"></a>Opcja 1: Portal IoT Hub

Można zadeklarować relację nadrzędny-podrzędny podczas tworzenia nowego urządzenia. Lub w przypadku istniejących urządzeń można zadeklarować relację ze strony Szczegóły urządzenia na nadrzędnym urządzeniu IoT Edge lub podrzędnym urządzeniu IoT.

   ![Zarządzanie urządzeniami podrzędnych z poziomu strony szczegółów urządzenia usługi IoT Edge](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Opcja 2: korzystanie z narzędzia wiersza polecenia `az`

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

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opcja 3: korzystanie z zestawu SDK usługi IoT Hub

Można na koniec zarządzać relacjami nadrzędnymi podrzędnymi przy C#użyciu zestawu SDK usługi IoT Hub Java lub Node. js. Oto [przykład przypisywania urządzenia podrzędnego](https://aka.ms/set-child-iot-device-c-sharp) przy użyciu C# zestawu SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Skonfiguruj urządzenie nadrzędne jako bramę

Relację nadrzędny/podrzędny można traktować jako nieprzezroczystą bramę, w której urządzenie podrzędne ma własną tożsamość w IoT Hub ale komunikuje się za pośrednictwem chmury za pośrednictwem jego elementu nadrzędnego. W celu zapewnienia bezpiecznej komunikacji urządzenie podrzędne musi mieć możliwość sprawdzenia, czy urządzenie nadrzędne pochodzi z zaufanego źródła. W przeciwnym razie inne firmy mogą skonfigurować złośliwe urządzenia w celu personifikacji rodziców i przechwycenia komunikacji.

Jednym ze sposobów tworzenia tej relacji zaufania jest szczegółowo opisany w następujących artykułach:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Łączenie podrzędnego urządzenia (podrzędnego) z bramą Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Określ serwery DNS

W celu zwiększenia niezawodności należy określić adresy serwerów DNS używane w danym środowisku. Aby ustawić serwer DNS dla IoT Edge, zobacz rozwiązanie dla agenta programu [Edge stale raportuje pusty plik konfiguracji i nie uruchamia żadnych modułów na urządzeniu w artykule dotyczącym](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) rozwiązywania problemów.

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

### <a name="host-storage-for-system-modules"></a>Magazyn hosta dla modułów systemowych

Komunikaty i informacje o stanie modułów są domyślnie przechowywane w lokalnym kontenerze systemu IoT Edge centrum. Aby zwiększyć niezawodność, szczególnie w przypadku działania w trybie offline, można również przeznaczyć magazyn na hoście IoT Edge urządzenia. Aby uzyskać więcej informacji, zobacz [zapewnianie modułom dostępu do magazynu lokalnego na urządzeniu](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o konfigurowaniu przezroczystej bramy dla połączeń urządzeń nadrzędnych/podrzędnych:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)
