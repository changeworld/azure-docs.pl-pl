---
title: Obsługa urządzeń w trybie offline — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Dowiedz się, jak urządzenia i moduły usługi IoT Edge mogą działać bez połączenia z Internetem przez dłuższy czas oraz jak usługa IoT Edge może umożliwiać korzystanie z zwykłych urządzeń IoT w trybie offline.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 55512491121aee28404ab5f85b4223c67a2f0e1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80236062"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Opis rozszerzonych możliwości offline dla urządzeń, modułów i urządzeń podrzędnych IoT Edge

Usługa Azure IoT Edge obsługuje rozszerzone operacje w trybie offline na urządzeniach usługi IoT Edge i umożliwia operacje w trybie offline na urządzeniach podrzędnych innych niż IoT Edge. Tak długo, jak urządzenie Usługi IoT Edge miało jedną możliwość nawiązania połączenia z usługą IoT Hub, to urządzenie i wszystkie urządzenia podrzędne mogą nadal działać z przerywanym lub bez połączenia z Internetem.

## <a name="how-it-works"></a>Jak to działa

Gdy urządzenie usługi IoT Edge przejdzie w tryb offline, centrum usługi IoT Edge przyjmuje trzy role. Po pierwsze przechowuje wszystkie komunikaty, które będą iść w górę i zapisuje je, dopóki urządzenie ponownie się połączy. Po drugie działa w imieniu usługi IoT Hub do uwierzytelniania modułów i urządzeń podrzędnych, tak aby mogły one nadal działać. Po trzecie umożliwia komunikację między urządzeniami podrzędnymi, które normalnie przechodziłyby przez Centrum IoT Hub.

W poniższym przykładzie pokazano, jak scenariusz ioT edge działa w trybie offline:

1. **Konfiguruj urządzenia**

   Urządzenia IoT Edge automatycznie mają włączone funkcje offline. Aby rozszerzyć tę możliwość na inne urządzenia IoT, należy zadeklarować relację nadrzędny-podrzędny między urządzeniami w centrum IoT Hub. Następnie należy skonfigurować urządzenia podrzędne, aby ufały przypisanemu urządzeniu nadrzędnemu i kierować komunikację między urządzeniami a chmurami za pośrednictwem nadrzędnej jako bramy.

2. **Synchronizacja z Centrum IoT**

   Co najmniej raz po zainstalowaniu środowiska wykonawczego usługi IoT Edge urządzenie usługi IoT Edge musi być w trybie online, aby można było zsynchronizować z centrum IoT Hub. W tej synchronizacji urządzenie Usługi IoT Edge pobiera szczegółowe informacje o wszystkich urządzeniach podrzędnych przypisanych do niego. Urządzenie usługi IoT Edge również bezpiecznie aktualizuje swoją lokalną pamięć podręczną, aby włączyć operacje w trybie offline i pobiera ustawienia lokalnego przechowywania wiadomości telemetrycznych.

3. **Przejdź do trybu offline**

   Po odłączeniu od usługi IoT Hub urządzenie Usługi IoT Edge, wdrożone moduły i wszystkie urządzenia IoT dla dzieci mogą działać przez czas nieokreślony. Moduły i urządzenia podrzędne można uruchomić i uruchomić ponownie, uwierzytelniając się za pomocą usługi IoT Edge hub w trybie offline. Dane telemetryczne powiązane z centrum IoT Hub są przechowywane lokalnie. Komunikacja między modułami lub między podrzędnymi urządzeniami IoT jest obsługiwana za pomocą bezpośrednich metod lub wiadomości.

4. **Ponowne łączenie i ponowne synchronizanie za pomocą usługi IoT Hub**

   Po przywróceniu połączenia z centrum IoT Hub urządzenie Usługi IoT Edge zostanie ponownie zsynchronizowane. Lokalnie przechowywane wiadomości są dostarczane do usługi IoT Hub od razu, ale są zależne od szybkości połączenia, opóźnienia usługi IoT Hub i powiązanych czynników. Są one dostarczane w tej samej kolejności, w jakiej były przechowywane.

   Wszelkie różnice między żądanymi i zgłoszonymi właściwościami modułów i urządzeń są uzgadniane. Urządzenie IoT Edge aktualizuje wszelkie zmiany w zestawie przypisanych podrzędnych urządzeń IoT.

## <a name="restrictions-and-limits"></a>Ograniczenia i limity

Rozszerzone możliwości offline opisane w tym artykule są dostępne w [wersji IoT Edge 1.0.7 lub nowszej](https://github.com/Azure/azure-iotedge/releases). Starsze wersje mają podzbiór funkcji w trybie offline. Istniejących urządzeń usługi IoT Edge, które nie mają rozszerzonych funkcji w trybie offline, nie można uaktualnić, zmieniając wersję środowiska wykonawczego, ale należy ponownie skonfigurować przy nowej tożsamości urządzenia usługi IoT Edge, aby uzyskać te funkcje.

Jako urządzenia podrzędne można dodawać tylko urządzenia inne niż IoT Edge.

Urządzenia usługi IoT Edge i przypisane przez nie urządzenia podrzędne mogą działać przez czas nieokreślony w trybie offline po początkowej, jednorazowej synchronizacji. Jednak przechowywanie wiadomości zależy od czasu wygaśnięcia (TTL) ustawienie i dostępne miejsce na dysku do przechowywania wiadomości.

## <a name="set-up-parent-and-child-devices"></a>Konfigurowanie urządzeń nadrzędnych i podrzędnych

Aby urządzenie IoT Edge rozszerzyło swoje rozszerzone możliwości offline na podrzędne urządzenia IoT, musisz wykonać dwa kroki. Najpierw zadeklarować relacje nadrzędny podrzędny w witrynie Azure portal. Po drugie utwórz relację zaufania między urządzeniem nadrzędnym a dowolnymi urządzeniami podrzędnymi, a następnie skonfiguruj komunikację między urządzeniami a chmurą, aby przejść przez element nadrzędny jako bramę.

### <a name="assign-child-devices"></a>Przypisywanie urządzeń podrzędnych

Urządzenia podrzędne mogą być dowolnym urządzeniem z krawędzią IoT zarejestrowanym w tym samym centrum IoT Hub. Urządzenia nadrzędne mogą mieć wiele urządzeń podrzędnych, ale urządzenie podrzędne ma tylko jeden element nadrzędny. Istnieją trzy opcje, aby ustawić urządzenia podrzędne do urządzenia brzegowego: za pośrednictwem witryny Azure portal, przy użyciu interfejsu wiersza polecenia platformy Azure lub przy użyciu zestawu SDK usługi Usługi IoT Hub.

W poniższych sekcjach przedstawiono przykłady, jak można zadeklarować relację nadrzędny/podrzędny w Centrum IoT dla istniejących urządzeń IoT. Jeśli tworzysz nowe tożsamości urządzeń dla urządzeń podrzędnych, zobacz [Uwierzytelnianie urządzenia podrzędnego do usługi Azure IoT Hub, aby](how-to-authenticate-downstream-device.md) uzyskać więcej informacji.

#### <a name="option-1-iot-hub-portal"></a>Opcja 1: Portal Centrum IoT

Relację nadrzędny podrzędny można zadeklarować podczas tworzenia nowego urządzenia. Lub dla istniejących urządzeń można zadeklarować relację ze strony szczegółów urządzenia nadrzędnego urządzenia IoT Edge lub podrzędnego urządzenia IoT.

   ![Zarządzanie urządzeniami podrzędnymi na stronie Szczegóły urządzenia IoT Edge](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Opcja 2: `az` Użyj narzędzia wiersza polecenia

Za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) z [rozszerzeniem IoT](https://github.com/azure/azure-iot-cli-extension) (wersja 0.7.0 lub nowsza), można zarządzać nadrzędnymi relacjami podrzędnymi za pomocą podpoleceń [tożsamości urządzenia.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) W poniższym przykładzie użyto kwerendy do przypisania wszystkich urządzeń innych niż IoT Edge w centrum do urządzeń podrzędnych urządzenia usługi IoT Edge.

```azurecli
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

Można zmodyfikować [kwerendę,](../iot-hub/iot-hub-devguide-query-language.md) aby wybrać inny podzbiór urządzeń. Polecenie może potrwać kilka sekund, jeśli określisz duży zestaw urządzeń.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opcja 3: Korzystanie z zestawu SDK usługi IoT Hub

Na koniec można zarządzać nadrzędnymi relacjami podrzędnymi programowo przy użyciu sDK usługi C#, Java lub Node.js IoT Hub. Oto [przykład przypisywania urządzenia podrzędnego](https://aka.ms/set-child-iot-device-c-sharp) przy użyciu SDK języka C#.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Konfigurowanie urządzenia nadrzędnego jako bramy

Relacja nadrzędna i podrzędna może być uważana za przejrzystą bramę, w której urządzenie podrzędne ma własną tożsamość w centrum IoT Hub, ale komunikuje się za pośrednictwem chmury za pośrednictwem obiektu nadrzędnego. Aby zapewnić bezpieczną komunikację, urządzenie podrzędne musi mieć możliwość sprawdzenia, czy urządzenie nadrzędne pochodzi z zaufanego źródła. W przeciwnym razie strony trzecie mogą skonfigurować złośliwe urządzenia do podszywania się pod rodziców i przechwytywania komunikacji.

Jednym ze sposobów utworzenia tej relacji zaufania jest szczegółowo opisane w następujących artykułach:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Łączenie podrzędnego urządzenia z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Określanie serwerów DNS

Aby zwiększyć niezawodność, zdecydowanie zaleca się określenie adresów serwera DNS używanych w twoim środowisku. Aby ustawić serwer DNS dla usługi IoT Edge, zobacz rozdzielczość [modułu agenta usługi Edge stale raportuje "pusty plik konfiguracyjny" i nie uruchamia się żadnych modułów na urządzeniu](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) w artykule rozwiązywania problemów.

## <a name="optional-offline-settings"></a>Opcjonalne ustawienia trybu offline

Jeśli urządzenia przejdą do trybu offline, urządzenie nadrzędne usługi IoT Edge przechowuje wszystkie komunikaty między urządzeniami w chmurze, dopóki połączenie nie zostanie przywrócone. Moduł centrum usługi IoT Edge zarządza magazynowaniem i przekazywaniem wiadomości w trybie offline. W przypadku urządzeń, które mogą przechodzić w tryb offline przez dłuższy czas, zoptymalizuj wydajność, konfigurując dwa ustawienia centrum usługi IoT Edge.

Najpierw wydłuż ustawienie czasu na żywo, aby centrum usługi IoT Edge przechowywać wiadomości wystarczająco długo, aby urządzenie zostało ponownie nawiązane. Następnie dodaj dodatkowe miejsce na dysku do przechowywania wiadomości.

### <a name="time-to-live"></a>Czas wygaśnięcia

Ustawienie czasu na żywo to czas (w sekundach), przez który wiadomość może czekać na dostarczenie wiadomości przed jej wygaśnięciem. Wartość domyślna to 7200 sekund (dwie godziny). Maksymalna wartość jest ograniczona tylko przez maksymalną wartość zmiennej całkowitej, która wynosi około 2 miliardów.

To ustawienie jest żądaną właściwością centrum usługi IoT Edge, która jest przechowywana w bliźniaczej reprezentacji modułu. Można go skonfigurować w witrynie Azure portal lub bezpośrednio w manifeście wdrażania.

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

### <a name="host-storage-for-system-modules"></a>Pamięć masowa hosta dla modułów systemowych

Komunikaty i informacje o stanie modułu są domyślnie przechowywane w lokalnym systemie plików kontenerów usługi IoT Edge. Aby zwiększyć niezawodność, szczególnie w trybie offline, można również poświęcić pamięć masową na urządzeniu hosta IoT Edge. Aby uzyskać więcej informacji, zobacz [Udzielanie modułom dostępu do lokalnej pamięci urządzenia](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o konfigurowaniu przezroczystej bramy dla połączeń urządzenia nadrzędnego z dzieckiem:

* [Konfigurowanie urządzenia usługi IoT Edge, aby działało jako przezroczysta brama](how-to-create-transparent-gateway.md)
* [Uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Łączenie urządzenia podrzędnego z bramą usługi Azure IoT Edge](how-to-connect-downstream-device.md)
