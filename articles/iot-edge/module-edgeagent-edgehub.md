---
title: EdgeAgent i EdgeHub żądane odwołanie do właściwości — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Przejrzyj określonych właściwości i ich wartości dla edgeAgent i edgeHub bliźniaczych reprezentacjach modułów
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e8a8170023c8f529894522e27a4c6231325089af
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190983"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Właściwości agenta usługi IoT Edge i bliźniaczych reprezentacjach modułów Centrum IoT Edge

Agent usługi IoT Edge i Centrum usługi IoT Edge są dwa moduły, które tworzą środowisko uruchomieniowe usługi IoT Edge. Aby uzyskać więcej informacji o jakie obowiązków, wykonuje każdy moduł, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md). 

Ten artykuł zawiera żądane właściwości i zgłaszanych właściwości bliźniaczych reprezentacjach modułów środowiska uruchomieniowego. Aby uzyskać więcej informacji na temat wdrażania modułów na urządzeniach IoT Edge, zobacz [Dowiedz się, jak wdrażać moduły oraz określenia trasy w usługi IoT Edge](module-composition.md).

Bliźniacza reprezentacja modułu obejmuje: 

* **Żądane właściwości**. Zaplecze rozwiązania może ustaw żądane właściwości, a moduł może je odczytać. Moduł może również odbierać powiadomienia o zmianach w odpowiednich właściwości. Żądane właściwości są używane wraz z zgłaszanych właściwości do synchronizacji konfiguracji modułu lub warunki.

* **Zgłaszane właściwości**. Moduł można ustawić właściwości zgłoszone, a zaplecze rozwiązania może odczytywać i wyszukiwać w nich. Zgłaszane właściwości są używane wraz z żądanych właściwości do synchronizacji konfiguracji modułu lub warunki. 

## <a name="edgeagent-desired-properties"></a>EdgeAgent żądane właściwości

Bliźniacza reprezentacja modułu dla agenta usługi IoT Edge jest nazywany `$edgeAgent` i służy do koordynowania komunikacji między tego agenta usługi IoT Edge na urządzeniu i Centrum IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifest wdrożenia na konkretnym urządzeniu jako część wdrożenia jednym urządzeniu lub na dużą skalę. 

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| schemaVersion | Musi być "1.0" | Yes |
| runtime.type | Musi być "docker" | Yes |
| runtime.settings.minDockerVersion | Wartość minimalna wersja platformy Docker wymaganych przez ten manifest wdrażania | Yes |
| runtime.settings.loggingOptions | JSON skonwertowanej do formatu tekstowego, zawierający opcje rejestrowania dla kontenera agenta usługi IoT Edge. [Opcje rejestrowania platformy docker](https://docs.docker.com/engine/admin/logging/overview/) | Nie |
| runtime.settings.registryCredentials<br>. .username {registryId} | Nazwa rejestru kontenerów. Usługi Azure Container Registry nazwa użytkownika jest zazwyczaj nazwa rejestru.<br><br> Poświadczenia rejestru są niezbędne dla wszelkich obrazów modułu, które nie są publicznie udostępniane. | Nie |
| runtime.settings.registryCredentials<br>. .password {registryId} | Hasło dla rejestru kontenerów. | Nie |
| runtime.settings.registryCredentials<br>. .address {registryId} | Adres rejestru kontenera. Usługi Azure Container Registry, adres, jest zazwyczaj *.azurecr.io {nazwa rejestru}* . | Nie |  
| systemModules.edgeAgent.type | Musi być "docker" | Yes |
| systemModules.edgeAgent.settings.image | Identyfikator URI obrazu agenta usługi IoT Edge. Obecnie usługa agenta usługi IoT Edge nie będzie mógł automatycznie zaktualizowana. | Yes |
| systemModules.edgeAgent.settings<br>.createOptions | JSON skonwertowanej do formatu tekstowego zawierającego opcje tworzenia kontenera agenta usługi IoT Edge. [Opcje tworzenia platformy docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeAgent.configuration.id | Identyfikator wdrożenia, które są wdrożone w tym module. | Usługa IoT Hub ustawia tę właściwość, jeśli manifest jest stosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |
| systemModules.edgeHub.type | Musi być "docker" | Yes |
| systemModules.edgeHub.status | Musi być "uruchomiona" | Yes |
| systemModules.edgeHub.restartPolicy | Musi być "zawsze" | Yes |
| systemModules.edgeHub.settings.image | Identyfikator URI obrazu Centrum usługi IoT Edge. | Tak |
| systemModules.edgeHub.settings<br>.createOptions | JSON skonwertowanej do formatu tekstowego zawierającego opcje tworzenia kontenera Centrum IoT Edge. [Opcje tworzenia platformy docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeHub.configuration.id | Identyfikator wdrożenia, które są wdrożone w tym module. | Usługa IoT Hub ustawia tę właściwość, jeśli manifest jest stosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |
| moduły. {moduleId} .version | Zdefiniowane przez użytkownika ciąg reprezentujący wersję tego modułu. | Yes |
| moduły. .Type — {moduleId} | Musi być "docker" | Yes |
| moduły. .status {moduleId} | {"uruchomiona" \| "zatrzymana"} | Yes |
| moduły. .restartPolicy {moduleId} | {"nigdy" \| "po awarii" \| "na — zła" \| "zawsze"} | Tak |
| modules.{moduleId}.settings.image | Identyfikator URI do obrazu modułu. | Yes |
| modules.{moduleId}.settings.createOptions | Opcje tworzenia kontenera moduł zawierający JSON skonwertowanej do formatu tekstowego. [Opcje tworzenia platformy docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| moduły. .configuration.id {moduleId} | Identyfikator wdrożenia, które są wdrożone w tym module. | Usługa IoT Hub ustawia tę właściwość, jeśli manifest jest stosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent zgłaszanych właściwości

Agent usługi IoT Edge zgłosił, że właściwości obejmują trzy główne informacje:

1. Stan aplikacji widoczne ostatni żądane właściwości;
2. Stan modułów aktualnie uruchomione na urządzeniu, zgłoszonej przez agenta usługi IoT Edge; i
3. Kopia żądane właściwości, które są aktualnie uruchomione na urządzeniu.

Ten ostatni element o kopię bieżącego żądane właściwości przydaje się stwierdzić, czy została zainstalowana najnowsza wersja żądane właściwości lub urządzenia nadal działa poprzedniego manifest wdrożenia.

> [!NOTE]
> Zgłaszane właściwości agenta usługi IoT Edge są przydatne, ponieważ może być odpytywany za pomocą [język zapytań usługi IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) aby zbadać stan wdrożeń na dużą skalę. Aby uzyskać więcej informacji na temat korzystania z usługi IoT Edge właściwości agenta dla stanu, zobacz [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md).

Poniższa tabela nie zawiera informacje, które są kopiowane z żądanych właściwości.

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądane właściwości przetwarzana przez agenta usługi IoT Edge. |
| lastDesiredStatus.code | Ten kod stanu odnosi się do ostatniego żądane właściwości widzianych przez agenta usługi IoT Edge. Dozwolone wartości: `200` Sukces, `400` nieprawidłowa konfiguracja `412` Nieprawidłowa wersja schematu, `417` żądanych właściwości są puste, `500` nie powiodło się |
| lastDesiredStatus.description | Opis stanu |
| deviceHealth | `healthy` Jeśli stan czasu wykonywania wszystkich modułów `running` lub `stopped`, `unhealthy` inaczej |
| configurationHealth.{deploymentId}.health | `healthy` Jeśli stan czasu wykonywania wszystkich modułów ustawiony przez wdrożenie {deploymentId} `running` lub `stopped`, `unhealthy` inaczej |
| runtime.platform.OS | Raportowanie systemu operacyjnego, uruchomione na urządzeniu |
| runtime.platform.architecture | Raportowanie architektury procesora urządzenia |
| systemModules.edgeAgent.runtimeStatus | Zgłaszany stan agenta usługi IoT Edge: {"uruchomiona" \| "nieprawidłowy"} |
| systemModules.edgeAgent.statusDescription | Opis tekstowy zgłaszany stan agenta usługi IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Stan Centrum usługi IoT Edge: {"uruchomiona" \| "zatrzymana" \| "nie powiodło się" \| "wycofywania" \| "nieprawidłowy"} |
| systemModules.edgeHub.statusDescription | Opis tekstowy stan Centrum usługi IoT Edge, jeżeli zła. |
| systemModules.edgeHub.exitCode | Kod zakończenia raportowanych przez kontener centrów IoT Edge kończy działanie kontenera |
| systemModules.edgeHub.startTimeUtc | Godzina ostatniego uruchomienia Centrum usługi IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Czas, gdy Centrum usługi IoT Edge ostatnio zakończył się |
| systemModules.edgeHub.lastRestartTimeUtc | Czas podczas ostatniego ponownego uruchomienia Centrum usługi IoT Edge |
| systemModules.edgeHub.restartCount | Liczba przypadków, gdy ten moduł został ponownie uruchomiony jako część zasady ponownego uruchamiania. |
| modules.{moduleId}.runtimeStatus | Stan modułu: {"uruchomiona" \| "zatrzymana" \| "nie powiodło się" \| "wycofywania" \| "nieprawidłowy"} |
| moduły. .statusDescription {moduleId} | Opis tekstowy stanu modułu, jeżeli zła. |
| moduły. .exitCode {moduleId} | Kod zakończenia raportowanych przez kontener modułu kończy działanie kontenera |
| modules.{moduleId}.startTimeUtc | Godzina ostatniego uruchomienia modułu |
| modules.{moduleId}.lastExitTimeUtc | Czas, kiedy moduł ostatnio zakończył się |
| modules.{moduleId}.lastRestartTimeUtc | Czas, kiedy moduł został ostatniego ponownego uruchomienia |
| modules.{moduleId}.restartCount | Liczba przypadków, gdy ten moduł został ponownie uruchomiony jako część zasady ponownego uruchamiania. |

## <a name="edgehub-desired-properties"></a>EdgeHub żądane właściwości

Bliźniacza reprezentacja modułu dla Centrum IoT Edge jest nazywany `$edgeHub` i służy do koordynowania komunikacji między centrum usługi IoT Edge, uruchomione na urządzeniu i Centrum IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifest wdrożenia na konkretnym urządzeniu jako część wdrożenia jednym urządzeniu lub na dużą skalę. 

| Właściwość | Opis | Wymagane w pliku manifestu wdrożenia |
| -------- | ----------- | -------- |
| schemaVersion | Musi być "1.0" | Yes |
| routes.{routeName} | Ciąg reprezentujący trasę Centrum IoT Edge. Aby uzyskać więcej informacji, zobacz [zadeklarować trasy](module-composition.md#declare-routes). | `routes` Element może być istnieje, ale jest pusty. |
| storeAndForwardConfiguration.timeToLiveSecs | Czas w sekundach, tym Centrum IoT Edge przechowuje komunikaty o odłączony od routingu punktów końcowych, tego, czy usługi IoT Hub lub modułu lokalnego. Wartość może być dowolną dodatnią liczbą całkowitą. | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub zgłaszanych właściwości

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądane właściwości, które są przetwarzane przez Centrum usługi IoT Edge. |
| lastDesiredStatus.code | Kod stanu, odnoszące się do ostatniego żądane właściwości widzianych przez Centrum usługi IoT Edge. Dozwolone wartości: `200` Sukces, `400` nieprawidłowa konfiguracja `500` nie powiodło się |
| lastDesiredStatus.description | Tekst opisu stanu. |
| Klienci. .status {urządzenia lub moduleId} | Stan połączenia to urządzenie lub modułu. Możliwe wartości {"Połączono" \| "odłączony"}. Tylko moduł tożsamości może być w stanie odłączonym. Podrzędne nawiązywania połączenia z Centrum usługi IoT Edge ukazaniu się tylko wtedy, gdy jest podłączone. |
| Klienci. .lastConnectTime {urządzenia lub moduleId} | Ostatni raz, urządzenia lub moduł połączone. |
| Klienci. .lastDisconnectTime {urządzenia lub moduleId} | Ostatni raz, urządzenia lub moduł odłączony. |

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak budować manifesty wdrożenia za pomocą tych właściwości, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).
