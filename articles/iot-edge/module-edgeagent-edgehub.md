---
title: Właściwości bliźniaczych modułów agenta i modułu koncentratora — usługa Azure IoT Edge
description: Przejrzyj określone właściwości i ich wartości dla bliźniacze modułów edgeAgent i edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f2d6603c264c9da3f2700f460a8c61b24681fac6
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546189"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Właściwości agenta usługi IoT Edge i bliźniaczy modułów koncentratora usługi IoT Edge

Agent usługi IoT Edge i centrum usługi IoT Edge to dwa moduły, które tworzą środowisko uruchomieniowe usługi IoT Edge. Aby uzyskać więcej informacji na temat obowiązków każdego modułu środowiska wykonawczego, zobacz [Opis środowiska uruchomieniowego usługi Azure IoT Edge i jego architektury.](iot-edge-runtime.md)

Ten artykuł zawiera żądane właściwości i zgłaszane właściwości bliźniacze modułu wykonawczego. Aby uzyskać więcej informacji na temat wdrażania modułów na urządzeniach usługi IoT Edge, zobacz [Dowiedz się, jak wdrażać moduły i ustalać trasy w uliczce IoT.](module-composition.md)

Bliźniacza reprezentacja modułu obejmuje:

* **Żądane właściwości**. Wewnętrznej bazy danych rozwiązania można ustawić żądane właściwości, a moduł może je odczytać. Moduł może również otrzymywać powiadomienia o zmianach w żądanych właściwościach. Żądane właściwości są używane wraz ze zgłoszonymi właściwościami w celu synchronizacji konfiguracji lub warunków modułu.

* **Zgłoszone właściwości**. Moduł można ustawić zgłaszane właściwości, a wewnętrznej bazy danych rozwiązania można je odczytać i zbadać je. Zgłoszone właściwości są używane wraz z żądanymi właściwościami do synchronizowania konfiguracji modułu lub warunków.

## <a name="edgeagent-desired-properties"></a>Właściwości żądane na krawędziAgent

Bliźniacza reprezentacja modułu dla `$edgeAgent` agenta usługi IoT Edge jest wywoływana i koordynuje komunikację między agentem usługi IoT Edge uruchomionym na urządzeniu i centrum IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifestu wdrożenia na określonym urządzeniu jako część wdrożenia na jednym urządzeniu lub na dużą skalę.

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| wersja schematu | Musi być "1.0" | Tak |
| runtime.type | Musi być "docker" | Tak |
| runtime.settings.minDockerVersion | Ustaw minimalną wersję platformy Docker wymaganą w tym manifeście wdrożenia | Tak |
| runtime.settings.loggingOpcje | Ciąg owy JSON zawierający opcje rejestrowania kontenera agenta usługi IoT Edge. [Opcje rejestrowania platformy Docker](https://docs.docker.com/engine/admin/logging/overview/) | Nie |
| środowisko uruchomieniowe.settings.registryCredentials<br>. {registryId}.nazwa użytkownika | Nazwa użytkownika rejestru kontenerów. W przypadku rejestru kontenerów platformy Azure nazwa użytkownika jest zwykle nazwą rejestru.<br><br> Poświadczenia rejestru są niezbędne dla wszystkich obrazów modułów prywatnych. | Nie |
| środowisko uruchomieniowe.settings.registryCredentials<br>. {registryId}.password | Hasło rejestru kontenerów. | Nie |
| środowisko uruchomieniowe.settings.registryCredentials<br>. {registryId}.address | Adres rejestru kontenerów. W przypadku rejestru kontenerów platformy Azure adres jest zwykle *{nazwa rejestru}.azurecr.io*. | Nie |  
| systemModules.edgeAgent.type | Musi być "docker" | Tak |
| systemModules.edgeAgent.settings.image | Identyfikator URI obrazu agenta ioT edge. Obecnie agent usługi IoT Edge nie może się zaktualizować. | Tak |
| systemModules.edgeAgent.settings<br>.createOptions (nie ma już więcej) | Stringified JSON zawierające opcje tworzenia kontenera agenta usługi IoT Edge. [Opcje tworzenia platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeAgent.configuration.id | Identyfikator wdrożenia, które wdrożyło ten moduł. | Centrum IoT ustawia tę właściwość, gdy manifest jest stosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |
| systemModules.edgeHub.type | Musi być "docker" | Tak |
| systemModules.edgeHub.status | Musi być "uruchomiony" | Tak |
| systemModules.edgeHub.restartPolicy | Musi być "zawsze" | Tak |
| systemModules.edgeHub.settings.image | Identyfikator URI obrazu centrum usługi IoT Edge. | Tak |
| systemModules.edgeHub.settings<br>.createOptions (nie ma już więcej) | Stringified JSON zawierające opcje tworzenia kontenera centrum usługi IoT Edge. [Opcje tworzenia platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeHub.configuration.id | Identyfikator wdrożenia, które wdrożyło ten moduł. | Centrum IoT ustawia tę właściwość, gdy manifest jest stosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |
| Moduły. {moduleId}.version | Ciąg zdefiniowany przez użytkownika reprezentujący wersję tego modułu. | Tak |
| Moduły. {moduleId}.type | Musi być "docker" | Tak |
| Moduły. {identyfikator modułu}.stan | {"bieganie" \| "zatrzymane"} | Tak |
| Moduły. {moduleId}.restartPolicy | {"nigdy" \| "on-failure" \| "on-unhealthy" \| "always"} | Tak |
| Moduły. {moduleId}.imagePullPolicy | {"on-create" \| "never"} | Nie |
| Moduły. {moduleId}.env | Lista zmiennych środowiskowych do przekazania do modułu. Przyjmuje format`"<name>": {"value": "<value>"}` | Nie |
| Moduły. {moduleId}.settings.image | Identyfikator URI do obrazu modułu. | Tak |
| Moduły. {moduleId}.settings.createOptions | Stringified JSON zawierające opcje tworzenia kontenera modułu. [Opcje tworzenia platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| Moduły. {moduleId}.configuration.id | Identyfikator wdrożenia, które wdrożyło ten moduł. | Centrum IoT ustawia tę właściwość, gdy manifest jest stosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |

## <a name="edgeagent-reported-properties"></a>Właściwości zgłaszane przez edgeagenta

Agent usługi IoT Edge zgłaszane właściwości obejmują trzy główne informacje:

1. Stan stosowania ostatnio widzianych pożądanych właściwości;
2. Stan modułów aktualnie uruchomionych na urządzeniu, zgodnie z raportem agenta IoT Edge; I
3. Kopia żądanych właściwości aktualnie uruchomionych na urządzeniu.

Kopia bieżących żądanych właściwości jest przydatne, aby stwierdzić, czy urządzenie zastosowało najnowsze wdrożenie lub nadal jest uruchomione poprzedniego manifestu wdrożenia.

> [!NOTE]
> Zgłoszone właściwości agenta usługi IoT Edge są przydatne, ponieważ można je wyszukiwać za pomocą [języka zapytań usługi IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) w celu zbadania stanu wdrożeń na dużą skalę. Aby uzyskać więcej informacji na temat używania właściwości agenta usługi IoT Edge dla stanu, zobacz [Opis wdrożeń usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę.](module-deployment-monitoring.md)

Poniższa tabela nie zawiera informacji, które są kopiowane z żądanych właściwości.

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądanych właściwości przetwarzanych przez agenta IoT Edge. |
| lastDesiredStatus.code | Ten kod stanu odnosi się do ostatnich żądanych właściwości widzianych przez agenta IoT Edge. Dozwolone wartości: `200` Sukces, `400` Nieprawidłowa konfiguracja, `412` Nieprawidłowa wersja schematu, `417` żądane właściwości są puste, `500` Nie powiodło się |
| lastDesiredStatus.description | Opis tekstowy stanu |
| deviceHealth | `healthy`jeśli stan środowiska wykonawczego wszystkich modułów `running` `stopped`jest `unhealthy` albo , w inny sposób |
| konfiguracjaZdrowie. {deploymentId}.health | `healthy`jeśli stan środowiska wykonawczego wszystkich modułów ustawionych przez wdrożenie `running` {deploymentId} jest albo lub `stopped`, `unhealthy` w przeciwnym razie |
| runtime.platform.OS | Raportowanie systemu operacyjnego działającego na urządzeniu |
| środowisko uruchomieniowe.platform.architecture | Raportowanie architektury procesora na urządzeniu |
| systemModules.edgeAgent.runtimeStatus | Zgłoszony stan agenta IoT Edge: \| {"uruchomiony" "w złej kondycji"} |
| systemModules.edgeAgent.statusDescription | Opis tekstowy zgłoszonego stanu agenta usługi IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Stan centrum usługi IoT Edge: \| { \| "running" \| "stopped" \| "failed" "backoff" "unhealthy" } |
| systemModules.edgeHub.statusDescription | Opis tekstowy stanu usługi IoT Edge hub w przypadku złej kondycji. |
| systemModules.edgeHub.exitCode | Kod zakończenia zgłoszony przez kontener koncentratora usługi IoT Edge, jeśli kontener zostanie wyjęty |
| systemModules.edgeHub.startTimeUtc | Czas ostatniego rozpoczęcia centrum IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Czas ostatniego zakończenia koncentratora usługi IoT Edge |
| systemModules.edgeHub.lastRestartTimeUtc | Czas ostatniego ponownego uruchomienia centrum usługi IoT Edge |
| systemModules.edgeHub.restartCount | Liczba ponownych uruchomiono tego modułu w ramach zasad ponownego uruchamiania. |
| Moduły. {moduleId}.runtimeStatus | Stan \| modułu: { "running" \| "stopped" \| "failed" \| "backoff" "unhealthy" } |
| Moduły. {moduleId}.statusScription | Opis tekstowy stanu modułu w złej kondycji. |
| Moduły. {moduleId}.exitCode | Kod zakończenia zgłoszony przez kontener modułu, jeśli kontener zostanie |
| Moduły. {moduleId}.startTimeUtc | Czas ostatniego startu modułu |
| Moduły. {moduleId}.lastExitTimeUtc | Czas ostatniego wyjętego modułu |
| Moduły. {moduleId}.lastRestartTimeUtc | Czas ostatniego ponownego uruchomienia modułu |
| Moduły. {moduleId}.restartCount | Liczba ponownych uruchomiono tego modułu w ramach zasad ponownego uruchamiania. |

## <a name="edgehub-desired-properties"></a>EdgeHub żądane właściwości

Bliźniacza reprezentacja modułu dla `$edgeHub` centrum usługi IoT Edge jest wywoływana i koordynuje komunikację między koncentratorem usługi IoT Edge działającym na urządzeniu i centrum IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifestu wdrożenia na określonym urządzeniu jako część wdrożenia na jednym urządzeniu lub na dużą skalę.

| Właściwość | Opis | Wymagane w manifeście wdrażania |
| -------- | ----------- | -------- |
| wersja schematu | Musi być "1.0" | Tak |
| Trasy. {routeName} | Ciąg reprezentujący trasę koncentratora usługi IoT Edge. Aby uzyskać więcej informacji, zobacz [Deklarowanie tras](module-composition.md#declare-routes). | Element `routes` może być obecny, ale pusty. |
| storeAndForWardConfiguration.timeToLiveSecs | Czas w sekundach, w której centrum Usługi IoT Edge przechowuje wiadomości po odłączeniu od punktów końcowych routingu, niezależnie od tego, czy jest to centrum IoT, czy moduł lokalny. Wartość może być dowolną dodatnią całkowitej liczby. | Tak |

## <a name="edgehub-reported-properties"></a>EdgeHub zgłaszane właściwości

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądanych właściwości przetwarzanych przez centrum usługi IoT Edge. |
| lastDesiredStatus.code | Kod stanu odnoszący się do ostatnio żądanych właściwości widzianych przez centrum usługi IoT Edge. Dozwolone wartości: `200` Sukces, `400` Nieprawidłowa konfiguracja, `500` Nie powiodło się |
| lastDesiredStatus.description | Opis tekstowy stanu. |
| Klientów. {identyfikator urządzenia lub modułu}.status | Stan łączności tego urządzenia lub modułu. Możliwe wartości {"connected" \| "disconnected"}. Tylko tożsamości modułu mogą być w stanie rozłączony. Urządzenia podrzędne łączące się z koncentratorem IoT Edge są wyświetlane tylko po podłączeniu. |
| Klientów. {identyfikator urządzenia lub modułu}.lastConnectTime | Ostatni raz urządzenie lub moduł były podłączone. |
| Klientów. {identyfikator urządzenia lub modułu}.lastDisconnectTime | Ostatni raz urządzenie lub moduł odłączone. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać tych właściwości do tworzenia manifestów wdrażania, zobacz [Opis sposobu używania, konfigurowania i ponownego używania modułów usługi IoT Edge.](module-composition.md)
