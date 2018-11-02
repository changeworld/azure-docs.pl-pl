---
title: Możliwości usługi Azure IoT Edge w trybie offline | Dokumentacja firmy Microsoft
description: Dowiedz się, jak urządzenia usługi IoT Edge i moduły mogą działać w trybie offline dłuższy czas i jak włączyć regularne urządzeniach IoT, aby działać w trybie offline za usługi IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d84df3e5e0b961b8a53044102f99205ee0fe9896
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914111"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Zrozumienie rozszerzone możliwości w trybie offline dla urządzeń usługi IoT Edge, moduły i podrzędny urządzenia (wersja zapoznawcza)

Usługa Azure IoT Edge obsługuje rozszerzonych operacji w trybie offline na urządzeniach brzegowych IoT i zbyt umożliwia operacje trybu offline na urządzeniach z podrzędnych-Edge. Tak długo, jak urządzenia usługi IoT Edge miał jedną szansę nawiązać połączenie z Centrum IoT Hub, go i wszystkie podrzędne urządzenia można nadal funkcji z tymczasowymi lub brak połączenia internetowego. 

>[!NOTE]
>Trwa obsługi trybu offline do usługi IoT Edge [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Jak to działa

Gdy urządzenia usługi IoT Edge przechodzi do trybu offline, Centrum usługi Edge przenosi się na trzech ról. Po pierwsze są przechowywane komunikaty, które przejdzie nadrzędnego, a następnie zapisuje te elementy, aż urządzenie połączy się ponownie. Po drugie działa w imieniu usługi IoT Hub do uwierzytelniania modułów i podrzędny urządzeń, dzięki czemu będą one działać. Po trzecie umożliwia komunikację między urządzeniami podrzędne, które normalnie musieli przejść za pomocą usługi IoT Hub. 

Poniższy przykład pokazuje, jak scenariusza użycia usługi IoT Edge działa w trybie offline:

1. **Konfigurowanie urządzenia usługi IoT Edge.**

   Urządzenia usługi IoT Edge mają automatycznie możliwości w trybie offline. Aby rozszerzyć tej możliwości do innych urządzeń IoT, należy zadeklarować relacji nadrzędny podrzędny między urządzeniami w usłudze IoT Hub. 

2. **Synchronizacja z usługą IoT Hub.**

   Co najmniej raz po zakończeniu instalacji środowiska uruchomieniowego usługi IoT Edge, urządzenia usługi IoT Edge musi być w trybie online w celu synchronizacji z usługą IoT Hub. W tym synchronizacji urządzenia usługi IoT Edge pobierania szczegółowych informacji o żadnych urządzeń podrzędnych do niej przypisany. Urządzenia usługi IoT Edge jednocześnie możliwość bezpiecznego aktualizuje lokalnej pamięci podręcznej umożliwia operacje trybu offline i umożliwia pobranie ustawień dla lokalnego magazynu danych telemetrycznych wiadomości. 

3. **Przejście do trybu offline.**

   Przy braku połączenia z usługi IoT Hub, urządzenia usługi IoT Edge, jego wdrożone moduły i dowolnego urządzenia IoT elementy podrzędne mogą działać na czas nieokreślony. Moduły i urządzeń podrzędnych można uruchomić i uruchom ponownie przy użyciu Centrum usługi Edge podczas w trybie offline. Dane telemetryczne powiązany nadrzędne do usługi IoT Hub są przechowywane lokalnie. Komunikacji między modułami, czy między urządzeniami IoT podrzędne są obsługiwane za pośrednictwem metod bezpośrednich lub wiadomości. 

4. **Połącz się ponownie i ponownie zsynchronizować z usługą IoT Hub.**

   Po przywróceniu połączenia z usługą IoT Hub ponownej synchronizacji urządzenia usługi IoT Edge. Lokalnie przechowywane komunikaty były dostarczane w tej samej kolejności, w którym zostały zapisane. Wszelkie różnice między żądaną i zgłaszanych właściwości modułów i urządzenia są uzgodnione. Urządzenia usługi IoT Edge aktualizuje wszystkie zmiany do jego zestawu urządzeń IoT przypisane podrzędnych.

## <a name="restrictions-and-limits"></a>Limity i ograniczenia

Rozszerzone możliwości w trybie offline, opisane w tym artykule są dostępne w [usługi IoT Edge w wersji 1.0.2 lub nowszej](https://github.com/Azure/azure-iotedge/releases). Wcześniejszych wersjach mają jakiś podzestaw funkcji w trybie offline. Istniejące usługi IoT Edge urządzenia, które nie mają rozszerzone możliwości w trybie offline nie może zostać uaktualniona, zmieniając wersję środowiska uruchomieniowego, ale musi zostać ponownie skonfigurowany z nową tożsamość urządzenia usługi IoT Edge w celu uzyskania tych funkcji. 

Rozszerzona pomoc techniczna w trybie offline jest dostępna we wszystkich regionach, w którym usługi IoT Hub jest dostępne, z wyjątkiem wschodnie stany USA i Europa Zachodnia. 

Tylko - Edge IoT urządzenia można dodać jako urządzenia podrzędnych. 

Urządzenia usługi IoT Edge i ich urządzeń przypisanych podrzędne może działać na czas nieokreślony w trybie offline, po zakończeniu synchronizacji początkowej, jednorazowe. Przechowywanie komunikatów zależy jednak od tego, jaki czas wygaśnięcia (TTL), ustawienia i dostępnego miejsca na dysku do przechowywania komunikatów. 

## <a name="set-up-an-edge-device"></a>Konfigurowanie urządzenia usługi Edge

Dla każdego urządzenia usługi IoT Edge, który ma zostać wykonana dłuższy okres czasu w trybie offline i skonfigurować środowisko uruchomieniowe usługi IoT Edge do komunikacji za pośrednictwem protokołu MQTT. 

Urządzenia usługi IoT Edge rozszerzyć jej rozszerzone możliwości w trybie offline na urządzeniach IoT podrzędnych należy zadeklarować relacji nadrzędny podrzędny w witrynie Azure portal.

### <a name="set-the-upstream-protocol-to-mqtt"></a>Ustaw nadrzędnego protokołu MQTT

Konfigurowanie Centrum usługi Edge i agent usługi Edge do komunikowania się z protokołu MQTT na protokół nadrzędnego. Ten protokół jest zadeklarowana za pomocą zmiennych środowiskowych w pliku manifestu wdrożenia. 

W witrynie Azure portal możesz dostęp do Centrum usługi Edge i definicje modułów agenta usługi Edge, wybierając **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge** przycisku Ustawianie modułów na potrzeby wdrożenia. Dla obu modułów, należy utworzyć zmienną środowiskową o nazwie **UpstreamProtocol** i ustawić jej wartość na **MQTT**. 

W szablonie wdrożenia, JSON zmienne środowiskowe są deklarowane, jak pokazano w poniższym przykładzie: 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "UpstreamProtocol": {
            "value": "MQTT"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

### <a name="assign-child-devices"></a>Przypisz urządzenia podrzędne

Podrzędne urządzenia mogą być dowolnego urządzenia bez krawędzi zarejestrowany w tym samym Centrum IoT Hub. Możesz zarządzać relacji nadrzędny podrzędny o tworzeniu nowego urządzenia lub ze strony szczegółów urządzenia albo nadrzędnej urządzenie usługi IoT Edge lub urządzenia IoT podrzędnego. 

   ![Zarządzanie urządzeniami podrzędnych z poziomu strony szczegółów urządzenia usługi IoT Edge](./media/offline-capabilities/manage-child-devices.png)

Nadrzędny urządzenia mogą mieć wiele urządzeń podrzędnych, ale urządzenie podrzędny może mieć tylko jedną jednostkę nadrzędną.

## <a name="optional-offline-settings"></a>Opcjonalne ustawienia w trybie offline

Jeśli oczekujesz, że urządzenia, aby środowisko okresach czasu w trybie offline, po którym mają być zbierane wszystkie komunikaty, które zostały wygenerowane, skonfiguruj Centrum usługi Edge tak, aby je można przechowywać wszystkie komunikaty. Istnieją dwa zmiany, że można wprowadzać Centrum usługi Edge do włączenia przechowywania długoterminowego wiadomości. Najpierw zwiększyć czas wygaśnięcia ustawienia, a następnie dodaj dodatkowe miejsce na dysku dla magazynu komunikatów. 

### <a name="time-to-live"></a>Czas wygaśnięcia

Czas wygaśnięcia ustawienie to ilość czasu (w sekundach) oczekiwania przez komunikat do dostarczenia przed jego wygaśnięciem. Wartość domyślna to 7200 sekund (2 godziny). 

To ustawienie jest żądaną właściwość Centrum usługi Edge, który jest przechowywany w bliźniaczej reprezentacji modułu. Można skonfigurować w witrynie Azure portal w **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge** sekcji lub bezpośrednio w ramach wdrożenia manifestu. 

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

Domyślnie komunikaty są przechowywane w Centrum usługi Edge kontenera w systemie plików. Jeśli tego ilość miejsca jest niewystarczająca do potrzeb w trybie offline, można przypisać magazyn lokalny na urządzeniu usługi IoT Edge. Należy utworzyć zmienną środowiskową dla Centrum usługi Edge, który wskazuje na folder przechowywania w kontenerze. Następnie użyj opcji tworzenia, aby powiązać ten folder storage do folderu na komputerze hosta. 

Można skonfigurować zmienne środowiskowe i opcjami tworzenia moduł Centrum usługi Edge, które znajdują się w witrynie Azure portal w **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge** sekcji. Ewentualnie można skonfigurować go bezpośrednio w pliku manifestu wdrożenia. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"Binds\":[\"<HostStoragePath>:<ModuleStoragePath>\"],\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
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

Zastąp `<HostStoragePath>` i `<ModuleStoragePath>` z hosta i modułu magazynu ścieżka magazynu ścieżki; hosta i moduł musi być ścieżką bezwzględną.  Na przykład `\"Binds\":[\"/etc/iotedge/storage/:/iotedge/storage/"` oznacza, że hostowanie ścieżki `/etc/iotedge/storage` jest mapowany na ścieżkę kontenera `/iotedge/storage/`.  Możesz również znaleźć więcej szczegółów na temat CreateOptions, można żądań z [dokumentacja platformy docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Kolejne kroki

Włącz rozszerzone operacji w trybie offline w swojej [przezroczystej bramy](how-to-create-transparent-gateway.md) scenariuszy.
