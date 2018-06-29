---
title: Usługa Azure IoT EdgeAgent i EdgeHub odwołania | Dokumentacja firmy Microsoft
description: Sprawdź właściwości i ich wartości dla twins modułu edgeAgent i edgeHub
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2858179d42ebf51cbb24d95d2e0093f8577bacef
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030567"
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Właściwości krawędź agent i twins modułu Centrum krawędzi

Krawędź agent i koncentrator krawędzi są dwa moduły, które składają się na krawędzi IoT środowiska uruchomieniowego. Aby uzyskać więcej informacji o jakie obowiązków wykonuje każdy moduł, zobacz [zrozumienie środowiska uruchomieniowego Azure IoT Edge i jego architektura](iot-edge-runtime.md). 

Ten artykuł zawiera odpowiednie właściwości i zgłoszone właściwości twins moduł środowiska wykonawczego. Aby uzyskać więcej informacji na temat wdrażania modułów na krawędzi IoT urządzenia, zobacz [wdrażania i monitorowania][lnk-deploy].

## <a name="edgeagent-desired-properties"></a>Właściwości EdgeAgent potrzeby

Dwie modułu dla agenta krawędzi jest nazywany `$edgeAgent` i koordynuje komunikacji między agentem krawędzi uruchomionej na urządzeniu i Centrum IoT. Żądane właściwości są ustawiane podczas stosowania manifest wdrażania na urządzeniach określonych jako część wdrożenia jednym urządzeniu lub na dużą skalę. 

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| Atrybut schemaVersion | Musi być "1.0" | Yes |
| runtime.type | Musi być "docker" | Yes |
| runtime.settings.minDockerVersion | Wartość minimalna wersja platformy Docker wymaganych przez ten manifest wdrażania | Yes |
| runtime.settings.loggingOptions | Stringified JSON, zawierające opcje rejestrowania dla kontenera agenta krawędzi. [Opcje rejestrowania platformy docker][lnk-docker-logging-options] | Nie |
| runtime.settings.registryCredentials<br>. .username {registryId} | Nazwa rejestru kontenera. Rejestr kontenera platformy Azure nazwa użytkownika jest zazwyczaj nazwa rejestru.<br><br> Poświadczenia rejestru są niezbędne do żadnych obrazów modułu, które nie są publicznie udostępniane. | Nie |
| runtime.settings.registryCredentials<br>. .password {registryId} | Hasło do rejestru kontenera. | Nie |
| runtime.settings.registryCredentials<br>. .address {registryId} | Adres rejestru kontenera. Do rejestru kontenera platformy Azure, adres jest zwykle *.azurecr.io {registryname}*. | Nie |  
| systemModules.edgeAgent.type | Musi być "docker" | Yes |
| systemModules.edgeAgent.settings.image | Identyfikator URI agenta krawędzi obrazu. Krawędź agent nie jest obecnie mogli automatycznie zaktualizowana. | Yes |
| systemModules.edgeAgent.settings<br>.createOptions | Stringified JSON, zawierające opcje tworzenia kontenera agenta krawędzi. [Opcje tworzenia docker][lnk-docker-create-options] | Nie |
| systemModules.edgeAgent.configuration.id | Identyfikator wdrożenia, które wdrożyć ten moduł. | Ta właściwość jest ustawiana przez Centrum IoT podczas tego manifestu jest stosowany przy użyciu wdrożenia. Nie jest częścią manifest wdrażania. |
| systemModules.edgeHub.type | Musi być "docker" | Yes |
| systemModules.edgeHub.status | Musi być "uruchomiona" | Yes |
| systemModules.edgeHub.restartPolicy | Musi być "zawsze" | Yes |
| systemModules.edgeHub.settings.image | Identyfikator URI obrazu Centrum krawędzi. | Yes |
| systemModules.edgeHub.settings<br>.createOptions | Stringified JSON, zawierające opcje tworzenia kontenera Centrum krawędzi. [Opcje tworzenia docker][lnk-docker-create-options] | Nie |
| systemModules.edgeHub.configuration.id | Identyfikator wdrożenia, które wdrożyć ten moduł. | Ta właściwość jest ustawiana przez Centrum IoT podczas tego manifestu jest stosowany przy użyciu wdrożenia. Nie jest częścią manifest wdrażania. |
| moduły. {moduleId} .version | Zdefiniowane przez użytkownika ciąg reprezentujący wersję tego modułu. | Yes |
| moduły. .type {moduleId} | Musi być "docker" | Yes |
| moduły. .restartPolicy {moduleId} | {"nigdy" \| "— nie powiodło się" \| "na — zła" \| "zawsze"} | Yes |
| modules.{moduleId}.settings.image | Identyfikator URI do obrazu modułu. | Yes |
| modules.{moduleId}.settings.createOptions | Stringified JSON, zawierające opcje tworzenia kontenera modułu. [Opcje tworzenia docker][lnk-docker-create-options] | Nie |
| moduły. .configuration.id {moduleId} | Identyfikator wdrożenia, które wdrożyć ten moduł. | Ta właściwość jest ustawiana przez Centrum IoT podczas tego manifestu jest stosowany przy użyciu wdrożenia. Nie jest częścią manifest wdrażania. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent zgłosił właściwości

Agent krawędzi zgłosił, że właściwości obejmują trzy główne informacje:

1. Stan stosowania widoczne ostatnie odpowiednie właściwości;
2. Stan modułów aktualnie uruchomionej na urządzeniu, zgłoszonej przez agenta krawędzi; i
3. Kopia odpowiednie właściwości, które są aktualnie uruchomione na urządzeniu.

Ten ostatni element informacji jest przydatne w przypadku najnowszych odpowiednie właściwości nie są stosowane pomyślnie w czasie wykonywania, a urządzenie jest nadal uruchomiona poprzedniej manifest wdrażania.

> [!NOTE]
> Zgłoszony właściwości agenta krawędzi są przydatne, jak mogą być przeszukiwane przy [język zapytań Centrum IoT] [ lnk-iothub-query] do badania stanu wdrożeń na dużą skalę. Aby uzyskać więcej informacji na temat korzystania z właściwości agenta krawędzi dla stanu, zobacz [wdrożeń zrozumieć IoT Edge dla urządzeń z jednego lub na dużą skalę][lnk-deploy].

Poniższa tabela nie zawiera informacje, które jest kopiowana żądanej właściwości.

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odwołuje się do ostatniej wersji odpowiednie właściwości, które są przetwarzane przez agenta krawędzi. |
| lastDesiredStatus.code | To jest kod stanu odwołujących się do ostatniego odpowiednie właściwości zostały odebrane przez agenta krawędzi. Dozwolone wartości: `200` sukces, `400` nieprawidłową konfigurację `412` wersji nieprawidłowy schemat `417` odpowiednie właściwości są puste, `500` nie powiodło się |
| lastDesiredStatus.description | Opis stanu |
| deviceHealth | `healthy` Jeśli stan środowiska uruchomieniowego dla wszystkich modułów `running` lub `stopped`, `unhealthy` inaczej |
| configurationHealth.{deploymentId}.health | `healthy` Jeśli stan środowiska uruchomieniowego dla wszystkich modułów ustawione przez wdrożenie {deploymentId} `running` lub `stopped`, `unhealthy` inaczej |
| runtime.platform.OS | Raportowanie systemu operacyjnego działającej na urządzeniu |
| runtime.platform.architecture | Raportowanie architektury procesora na urządzeniu |
| systemModules.edgeAgent.runtimeStatus | Zgłaszany stan agenta krawędzi: {"uruchomiona" \| "zła"} |
| systemModules.edgeAgent.statusDescription | Opis tekstowy zgłaszany stan agenta krawędzi. |
| systemModules.edgeHub.runtimeStatus | Bieżący stan koncentratora krawędzi: {"uruchomiona" \| 'stopped' \| "nie powiodło się" \| "wycofywania" \| "zła"} |
| systemModules.edgeHub.statusDescription | Opis tekstowy przy bieżącym stanie krawędzi koncentratora, jeśli złej kondycji. |
| systemModules.edgeHub.exitCode | Jeśli został zakończony, kod zakończenia zgłoszone przez kontener Centrum krawędzi |
| systemModules.edgeHub.startTimeUtc | Godzina ostatniego uruchomienia Centrum krawędzi |
| systemModules.edgeHub.lastExitTimeUtc | Czas, po Centrum krawędź ostatniego zakończony |
| systemModules.edgeHub.lastRestartTimeUtc | Czas, gdy Centrum krawędź ostatniego ponownego uruchomienia |
| systemModules.edgeHub.restartCount | Ile razy ten moduł został ponownie uruchomiony jako część zasada ponownego uruchamiania. |
| modules.{moduleId}.runtimeStatus | Bieżący stan modułu: {"uruchomiona" \| 'stopped' \| "nie powiodło się" \| "wycofywania" \| "zła"} |
| moduły. .statusDescription {moduleId} | Opis tekstowy przy bieżącym stanie modułu, jeśli jest zła. |
| moduły. .exitCode {moduleId} | Jeśli został zakończony, kod zakończenia zgłaszane przez moduł kontenera |
| modules.{moduleId}.startTimeUtc | Godzina ostatniego uruchomienia modułu |
| modules.{moduleId}.lastExitTimeUtc | Czas, gdy moduł ostatnio zakończony |
| modules.{moduleId}.lastRestartTimeUtc | Czas, gdy moduł ostatniego ponownego uruchomienia |
| modules.{moduleId}.restartCount | Ile razy ten moduł został ponownie uruchomiony jako część zasada ponownego uruchamiania. |

## <a name="edgehub-desired-properties"></a>Właściwości EdgeHub potrzeby

Dwie modułu dla koncentratora krawędzi jest nazywany `$edgeHub` i koordynuje komunikacji między centrum krawędzi uruchomionej na urządzeniu i Centrum IoT. Żądane właściwości są ustawiane podczas stosowania manifest wdrażania na urządzeniach określonych jako część wdrożenia jednym urządzeniu lub na dużą skalę. 

| Właściwość | Opis | Wymagane w manifeście rozmieszczenia |
| -------- | ----------- | -------- |
| Atrybut schemaVersion | Musi być "1.0" | Yes |
| routes.{routeName} | Ciąg reprezentujący trasę koncentratora krawędzi. | `routes` Element może być istnieje, ale jest pusty. |
| storeAndForwardConfiguration.timeToLiveSecs | Czas w sekundach Centrum krawędzi przechowuje komunikaty w przypadku odłączonych routingu punktów końcowych, na przykład odłączony od centrum IoT lub lokalnego modułu | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub zgłosił właściwości

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odwołuje się do ostatniej wersji odpowiednie właściwości, które są przetwarzane przez Centrum krawędzi. |
| lastDesiredStatus.code | To jest kod stanu odwołujących się do ostatniego odpowiednie właściwości zostały odebrane przez Centrum krawędzi. Dozwolone wartości: `200` sukces, `400` nieprawidłową konfigurację `500` nie powiodło się |
| lastDesiredStatus.description | Opis stanu |
| Klienci. .status {tożsamość urządzenia lub moduł} | Stan połączenia to urządzenie lub modułu. Możliwe wartości {"połączone" \| "odłączony"}. Tylko moduł tożsamości mogą być w stanie rozłączenia. Podrzędne urządzeń nawiązujących połączenie z koncentratorem krawędzi są wyświetlane tylko w przypadku połączenia. |
| Klienci. .lastConnectTime {tożsamość urządzenia lub moduł} | Czas ostatniego urządzenia lub moduł połączone |
| Klienci. .lastDisconnectTime {tożsamość urządzenia lub moduł} | Czas ostatniego urządzenia lub moduł odłączona |

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak użyć ich do tworzenia manifesty wdrożenia, zobacz [zrozumieć, jak moduły krawędzi IoT może być używany, skonfigurowane i ponownie](module-composition.md).

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
