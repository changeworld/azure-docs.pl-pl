---
title: Obsługuj urządzenia w trybie offline — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak urządzenia usługi IoT Edge i moduły mogą działać bez połączenia internetowego przez dłuższy czas i jak włączyć regularne urządzeniach IoT, aby działać w trybie offline za usługi IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e82c842ec8fce703c48c98eaf09ea5c8d91be9be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60998531"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Zrozumienie rozszerzone możliwości w trybie offline dla urządzeń usługi IoT Edge, moduły i podrzędny urządzenia (wersja zapoznawcza)

Usługa Azure IoT Edge obsługuje rozszerzonych operacji w trybie offline na urządzeniach brzegowych IoT i zbyt umożliwia operacje trybu offline na urządzeniach z podrzędnych-Edge. Tak długo, jak urządzenia usługi IoT Edge miał jedną szansę nawiązać połączenie z Centrum IoT Hub, go i wszystkie podrzędne urządzenia można nadal funkcji z tymczasowymi lub brak połączenia internetowego. 

>[!NOTE]
>Trwa obsługi trybu offline do usługi IoT Edge [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Jak to działa

Gdy urządzenia usługi IoT Edge przechodzi do trybu offline, Centrum usługi IoT Edge przenosi się na trzech ról. Po pierwsze są przechowywane komunikaty, które przejdzie nadrzędnego, a następnie zapisuje te elementy, aż urządzenie połączy się ponownie. Po drugie działa w imieniu usługi IoT Hub do uwierzytelniania modułów i podrzędny urządzeń, dzięki czemu będą one działać. Po trzecie umożliwia komunikację między urządzeniami podrzędne, które normalnie musieli przejść za pomocą usługi IoT Hub. 

Poniższy przykład pokazuje, jak scenariusza użycia usługi IoT Edge działa w trybie offline:

1. **Konfigurowanie urządzenia usługi IoT Edge.**

   Urządzenia usługi IoT Edge mają automatycznie możliwości w trybie offline. Aby rozszerzyć tej możliwości do innych urządzeń IoT, należy zadeklarować relacji nadrzędny podrzędny między urządzeniami w usłudze IoT Hub. 

2. **Synchronizacja z usługą IoT Hub.**

   Co najmniej raz po zakończeniu instalacji środowiska uruchomieniowego usługi IoT Edge, urządzenia usługi IoT Edge musi być w trybie online w celu synchronizacji z usługą IoT Hub. W tym synchronizacji urządzenia usługi IoT Edge pobierania szczegółowych informacji o żadnych urządzeń podrzędnych do niej przypisany. Urządzenia usługi IoT Edge jednocześnie możliwość bezpiecznego aktualizuje lokalnej pamięci podręcznej umożliwia operacje trybu offline i umożliwia pobranie ustawień dla lokalnego magazynu danych telemetrycznych wiadomości. 

3. **Przejście do trybu offline.**

   Przy braku połączenia z usługi IoT Hub, urządzenia usługi IoT Edge, jego wdrożone moduły i dowolnego urządzenia IoT elementy podrzędne mogą działać na czas nieokreślony. Moduły i urządzeń podrzędnych można uruchomić i uruchom ponownie, korzystając z uwierzytelniania za pomocą usługi IoT Edge hub podczas w trybie offline. Dane telemetryczne powiązany nadrzędne do usługi IoT Hub są przechowywane lokalnie. Komunikacji między modułami, czy między urządzeniami IoT podrzędne są obsługiwane za pośrednictwem metod bezpośrednich lub wiadomości. 

4. **Połącz się ponownie i ponowna synchronizacja z usługą IoT Hub.**

   Po przywróceniu połączenia z usługą IoT Hub ponownej synchronizacji urządzenia usługi IoT Edge. Lokalnie przechowywane komunikaty były dostarczane w tej samej kolejności, w którym zostały zapisane. Wszelkie różnice między żądaną i zgłaszanych właściwości modułów i urządzenia są uzgodnione. Urządzenia usługi IoT Edge aktualizuje wszystkie zmiany do jego zestawu urządzeń IoT przypisane podrzędnych.

## <a name="restrictions-and-limits"></a>Limity i ograniczenia

Rozszerzone możliwości w trybie offline, opisane w tym artykule są dostępne w [usługi IoT Edge wersji 1.0.4 lub nowszej](https://github.com/Azure/azure-iotedge/releases). Wcześniejszych wersjach mają jakiś podzestaw funkcji w trybie offline. Istniejące usługi IoT Edge urządzenia, które nie mają rozszerzone możliwości w trybie offline nie może zostać uaktualniona, zmieniając wersję środowiska uruchomieniowego, ale musi zostać ponownie skonfigurowany z nową tożsamość urządzenia usługi IoT Edge w celu uzyskania tych funkcji. 

Rozszerzona pomoc techniczna w trybie offline jest dostępna we wszystkich regionach, w których usługi IoT Hub jest dostępna, **z wyjątkiem** wschodnie stany USA.

Tylko - Edge IoT urządzenia można dodać jako urządzenia podrzędnych. 

Urządzenia usługi IoT Edge i ich urządzeń przypisanych podrzędne może działać na czas nieokreślony w trybie offline, po zakończeniu synchronizacji początkowej, jednorazowe. Przechowywanie komunikatów zależy jednak od tego, jaki czas wygaśnięcia (TTL), ustawienia i dostępnego miejsca na dysku do przechowywania komunikatów. 

## <a name="set-up-an-iot-edge-device"></a>Konfigurowanie urządzenia usługi IoT Edge

Urządzenia usługi IoT Edge rozszerzyć jej rozszerzone możliwości w trybie offline na urządzeniach IoT podrzędnych należy zadeklarować relacji nadrzędny podrzędny w witrynie Azure portal.

### <a name="assign-child-devices"></a>Przypisz urządzenia podrzędne

Podrzędne urządzenia mogą być dowolnego urządzenia bez krawędzi zarejestrowany w tym samym Centrum IoT Hub. Możesz zarządzać relacji nadrzędny podrzędny o tworzeniu nowego urządzenia lub ze strony szczegółów urządzenia albo nadrzędnej urządzenie usługi IoT Edge lub urządzenia IoT podrzędnego. 

   ![Zarządzanie urządzeniami podrzędnych z poziomu strony szczegółów urządzenia usługi IoT Edge](./media/offline-capabilities/manage-child-devices.png)

Nadrzędny urządzenia mogą mieć wiele urządzeń podrzędnych, ale urządzenie podrzędny może mieć tylko jedną jednostkę nadrzędną.

### <a name="specifying-dns-servers"></a>Określenie serwerów DNS 

W celu poprawy niezawodności, zalecane jest określenie adresów serwerów DNS, które są używane w danym środowisku. Na przykład w systemie Linux, należy zaktualizować **/etc/docker/daemon.json** (może być konieczne do utworzenia pliku) do uwzględnienia:

```json
{
    "dns": ["1.1.1.1"]
}
```

Jeśli używasz lokalnego serwera DNS, należy zastąpić 1.1.1.1 adres IP serwera DNS. Uruchom ponownie usługę docker, aby zmiany zaczęły obowiązywać.


## <a name="optional-offline-settings"></a>Opcjonalne ustawienia w trybie offline

Jeśli oczekujesz zbierać wszystkie komunikaty, które generują urządzeń w okresach czasu w trybie offline, należy skonfigurować Centrum usługi IoT Edge, aby je można przechowywać wszystkie komunikaty. Istnieją dwa zmiany można wprowadzenie do usługi IoT Edge hub włączenia przechowywania długoterminowego wiadomości. Po pierwsze wydłużenie czasu wygaśnięcia ustawienie. Następnie należy dodać dodatkowe miejsce na dysku dla magazynu komunikatów. 

### <a name="time-to-live"></a>Czas wygaśnięcia

Czas wygaśnięcia ustawienie to ilość czasu (w sekundach) oczekiwania przez komunikat do dostarczenia przed jego wygaśnięciem. Wartość domyślna to 7200 sekund (2 godziny). 

To ustawienie jest żądaną właściwość Centrum IoT Edge, który jest przechowywany w bliźniaczej reprezentacji modułu. Można skonfigurować w witrynie Azure portal w **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge** sekcji lub bezpośrednio w ramach wdrożenia manifestu. 

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

Włącz rozszerzone operacji w trybie offline w swojej [przezroczystej bramy](how-to-create-transparent-gateway.md) scenariuszy.
