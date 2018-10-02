---
title: Dokumentacja usługi Azure IoT EdgeAgent i EdgeHub | Dokumentacja firmy Microsoft
description: Przejrzyj określonych właściwości i ich wartości dla edgeAgent i edgeHub bliźniaczych reprezentacjach modułów
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 259d61125828ee487b74daa525f3635cfa592ce7
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017708"
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Właściwości agenta usługi Edge i bliźniaczych reprezentacjach modułów Centrum usługi Edge

Agent usługi Edge i Centrum usługi Edge są dwa moduły, które tworzą środowisko uruchomieniowe usługi IoT Edge. Aby uzyskać więcej informacji o jakie obowiązków, wykonuje każdy moduł, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md). 

Ten artykuł zawiera żądane właściwości i zgłaszanych właściwości bliźniaczych reprezentacjach modułów środowiska uruchomieniowego. Aby uzyskać więcej informacji na temat wdrażania modułów na urządzeniach IoT Edge, zobacz [wdrażania i monitorowania][lnk-deploy].

## <a name="edgeagent-desired-properties"></a>EdgeAgent żądane właściwości

Agent usługi Edge bliźniaczą reprezentację modułu nosi nazwę `$edgeAgent` i służy do koordynowania komunikacji między agent usługi Edge uruchomionej na urządzeniu i Centrum IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifest wdrożenia na konkretnym urządzeniu jako część wdrożenia jednym urządzeniu lub na dużą skalę. 

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| schemaVersion | Musi być "1.0" | Yes |
| runtime.type | Musi być "docker" | Yes |
| runtime.settings.minDockerVersion | Wartość minimalna wersja platformy Docker wymaganych przez ten manifest wdrażania | Yes |
| runtime.settings.loggingOptions | JSON skonwertowanej do formatu tekstowego, zawierający opcje rejestrowania dla kontenera agenta usługi Edge. [Opcje rejestrowania platformy docker][lnk-docker-logging-options] | Nie |
| runtime.settings.registryCredentials<br>. .username {registryId} | Nazwa rejestru kontenerów. Usługi Azure Container Registry nazwa użytkownika jest zazwyczaj nazwa rejestru.<br><br> Poświadczenia rejestru są niezbędne dla wszelkich obrazów modułu, które nie są publicznie udostępniane. | Nie |
| runtime.settings.registryCredentials<br>. .password {registryId} | Hasło dla rejestru kontenerów. | Nie |
| runtime.settings.registryCredentials<br>. .address {registryId} | Adres rejestru kontenera. Usługi Azure Container Registry, adres, jest zazwyczaj *.azurecr.io {registryname}*. | Nie |  
| systemModules.edgeAgent.type | Musi być "docker" | Yes |
| systemModules.edgeAgent.settings.image | Identyfikator URI obrazu agenta usługi Edge. Obecnie agent usługi Edge nie będzie mógł automatycznie zaktualizowana. | Yes |
| systemModules.edgeAgent.settings<br>.createOptions | JSON skonwertowanej do formatu tekstowego zawierającego opcje tworzenia kontenera agenta usługi Edge. [Opcje tworzenia platformy docker][lnk-docker-create-options] | Nie |
| systemModules.edgeAgent.configuration.id | Identyfikator wdrożenia, które są wdrożone w tym module. | Ta właściwość jest ustawiona przez usługę IoT Hub, po zastosowaniu tego manifestu, przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |
| systemModules.edgeHub.type | Musi być "docker" | Yes |
| systemModules.edgeHub.status | Musi być "uruchomiona" | Yes |
| systemModules.edgeHub.restartPolicy | Musi być "zawsze" | Yes |
| systemModules.edgeHub.settings.image | Identyfikator URI obrazu Centrum usługi Edge. | Yes |
| systemModules.edgeHub.settings<br>.createOptions | JSON skonwertowanej do formatu tekstowego zawierającego opcje tworzenia kontenera Centrum usługi Edge. [Opcje tworzenia platformy docker][lnk-docker-create-options] | Nie |
| systemModules.edgeHub.configuration.id | Identyfikator wdrożenia, które są wdrożone w tym module. | Ta właściwość jest ustawiona przez usługę IoT Hub, po zastosowaniu tego manifestu, przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |
| moduły. {moduleId} .version | Zdefiniowane przez użytkownika ciąg reprezentujący wersję tego modułu. | Yes |
| moduły. .Type — {moduleId} | Musi być "docker" | Yes |
| moduły. .status {moduleId} | {"uruchomiona" \| "zatrzymana"} | Yes |
| moduły. .restartPolicy {moduleId} | {"nigdy" \| "na — nie powiodło się" \| "na — zła" \| "zawsze"} | Yes |
| modules.{moduleId}.settings.image | Identyfikator URI do obrazu modułu. | Yes |
| modules.{moduleId}.settings.createOptions | Opcje tworzenia kontenera moduł zawierający JSON skonwertowanej do formatu tekstowego. [Opcje tworzenia platformy docker][lnk-docker-create-options] | Nie |
| moduły. .configuration.id {moduleId} | Identyfikator wdrożenia, które są wdrożone w tym module. | Ta właściwość jest ustawiona przez usługę IoT Hub, po zastosowaniu tego manifestu, przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent zgłaszanych właściwości

Agent usługi Edge zgłosił, że właściwości obejmują trzy główne informacje:

1. Stan aplikacji widoczne ostatni żądane właściwości;
2. Stan modułów aktualnie uruchomione na urządzeniu, zgłoszonej przez agenta usługi Edge; i
3. Kopia żądane właściwości, które są aktualnie uruchomione na urządzeniu.

Ten ostatni element informacji jest przydatna w przypadku, gdy najnowszy żądane właściwości nie są stosowane pomyślnie w czasie wykonywania, a urządzenie jest nadal uruchomiona poprzedniego manifest wdrożenia.

> [!NOTE]
> Zgłoszone właściwości agenta usługi Edge są przydatne, ponieważ może być odpytywany za pomocą [język zapytań usługi IoT Hub] [ lnk-iothub-query] aby zbadać stan wdrożeń na dużą skalę. Aby uzyskać więcej informacji na temat sposobu używania właściwości agenta usługi Edge dla stanu, zobacz [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali][lnk-deploy].

Poniższa tabela nie zawiera informacje, które są kopiowane z żądanych właściwości.

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądane właściwości przetwarzana przez agenta usługi Edge. |
| lastDesiredStatus.code | Jest to kod stanu, odnoszące się do ostatniego żądane właściwości widzianych przez agenta usługi Edge. Dozwolone wartości: `200` sukces, `400` nieprawidłowa konfiguracja `412` Nieprawidłowa wersja schematu, `417` żądanych właściwości są puste, `500` nie powiodło się |
| lastDesiredStatus.description | Opis stanu |
| deviceHealth | `healthy` Jeśli stan czasu wykonywania wszystkich modułów `running` lub `stopped`, `unhealthy` inaczej |
| configurationHealth.{deploymentId}.health | `healthy` Jeśli stan czasu wykonywania wszystkich modułów ustawiony przez wdrożenie {deploymentId} `running` lub `stopped`, `unhealthy` inaczej |
| runtime.platform.OS | Raportowanie systemu operacyjnego, uruchomione na urządzeniu |
| runtime.platform.architecture | Raportowanie architektury procesora urządzenia |
| systemModules.edgeAgent.runtimeStatus | Zgłaszany stan agenta usługi Edge: {"uruchomiona" \| "nieprawidłowy"} |
| systemModules.edgeAgent.statusDescription | Opis tekstowy zgłaszany stan agenta usługi Edge. |
| systemModules.edgeHub.runtimeStatus | Bieżący stan Centrum usługi Edge: {"uruchomiona" \| "zatrzymana" \| "nie powiodło się" \| "wycofywania" \| "nieprawidłowy"} |
| systemModules.edgeHub.statusDescription | Opis tekstowy bieżący stan Centrum usługi Edge, jeżeli zła. |
| systemModules.edgeHub.exitCode | Jeśli został zakończony, kod zakończenia zgłoszone przez kontener Centrum usługi Edge |
| systemModules.edgeHub.startTimeUtc | Godzina ostatniego uruchomienia Centrum usługi Edge |
| systemModules.edgeHub.lastExitTimeUtc | Czas, gdy Centrum usługi Edge ostatnio zakończył się |
| systemModules.edgeHub.lastRestartTimeUtc | Czas, gdy Centrum usługi Edge ostatniego ponownego uruchomienia |
| systemModules.edgeHub.restartCount | Liczba przypadków, gdy ten moduł został ponownie uruchomiony jako część zasady ponownego uruchamiania. |
| modules.{moduleId}.runtimeStatus | Bieżący stan modułu: {"uruchomiona" \| "zatrzymana" \| "nie powiodło się" \| "wycofywania" \| "nieprawidłowy"} |
| moduły. .statusDescription {moduleId} | Opis tekstowy bieżący stan modułu, jeżeli zła. |
| moduły. .exitCode {moduleId} | Jeśli został zakończony, kod zakończenia zgłoszone przez kontener modułu |
| modules.{moduleId}.startTimeUtc | Godzina ostatniego uruchomienia modułu |
| modules.{moduleId}.lastExitTimeUtc | Czas, kiedy moduł ostatnio zakończył się |
| modules.{moduleId}.lastRestartTimeUtc | Czas, kiedy moduł został ostatniego ponownego uruchomienia |
| modules.{moduleId}.restartCount | Liczba przypadków, gdy ten moduł został ponownie uruchomiony jako część zasady ponownego uruchamiania. |

## <a name="edgehub-desired-properties"></a>EdgeHub żądane właściwości

Bliźniacza reprezentacja modułu dla Centrum usługi Edge jest nazywany `$edgeHub` i służy do koordynowania komunikacji między centrum usługi Edge uruchomionej na urządzeniu i Centrum IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifest wdrożenia na konkretnym urządzeniu jako część wdrożenia jednym urządzeniu lub na dużą skalę. 

| Właściwość | Opis | Wymagane w pliku manifestu wdrożenia |
| -------- | ----------- | -------- |
| schemaVersion | Musi być "1.0" | Yes |
| routes.{routeName} | Ciąg reprezentujący trasę Centrum usługi Edge. | `routes` Element może być istnieje, ale jest pusty. |
| storeAndForwardConfiguration.timeToLiveSecs | Czas w sekundach, przez Centrum Edge hub przechowuje komunikaty o odłączony routingu punktów końcowych, na przykład w przypadku odłączony od usługi IoT Hub lub modułu lokalnego | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub zgłaszanych właściwości

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądane właściwości, które są przetwarzane przez Centrum usługi Edge. |
| lastDesiredStatus.code | Jest to kod stanu, odnoszące się do ostatniego żądane właściwości widzianych przez Centrum usługi Edge. Dozwolone wartości: `200` sukces, `400` nieprawidłowa konfiguracja `500` nie powiodło się |
| lastDesiredStatus.description | Opis stanu |
| Klienci. .status {urządzenia lub moduleId} | Stan połączenia to urządzenie lub modułu. Możliwe wartości {"Połączono" \| "odłączony"}. Tylko moduł tożsamości może być w stanie odłączonym. Podrzędne nawiązywania połączenia z Centrum usługi Edge ukazaniu się tylko wtedy, gdy jest podłączone. |
| Klienci. .lastConnectTime {urządzenia lub moduleId} | Ostatni raz, urządzenia lub moduł połączone |
| Klienci. .lastDisconnectTime {urządzenia lub moduleId} | Czas ostatniego urządzenia lub moduł odłączony |

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak budować manifesty wdrożenia za pomocą tych właściwości, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
