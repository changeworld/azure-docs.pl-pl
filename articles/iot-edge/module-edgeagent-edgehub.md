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
ms.openlocfilehash: caee247dfb1f7068e935be9c293a28870cfb98ce
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74069323"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Właściwości agenta IoT Edge i modułu IoT Edge Hub bliźniaczych reprezentacji

Agent IoT Edge i centrum IoT Edge to dwa moduły, które tworzą środowisko uruchomieniowe IoT Edge. Aby uzyskać więcej informacji o jakie obowiązków, wykonuje każdy moduł, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md). 

Ten artykuł zawiera żądane właściwości i zgłaszanych właściwości bliźniaczych reprezentacjach modułów środowiska uruchomieniowego. Aby uzyskać więcej informacji na temat sposobu wdrażania modułów na IoT Edge urządzeniach, zobacz [informacje na temat wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

Sznurki modułu obejmuje: 

* **Żądane właściwości**. Zaplecze rozwiązania może ustawić żądane właściwości, a moduł może je odczytać. Moduł może również odbierać powiadomienia o zmianach w odpowiednich właściwościach. Odpowiednie właściwości są używane wraz z zgłoszonymi właściwościami do synchronizacji konfiguracji lub warunków modułu.

* **Raportowane właściwości**. Moduł może ustawiać raportowane właściwości, a zaplecze rozwiązania może odczytywać i wysyłać do nich zapytania. Raportowane właściwości są używane wraz z pożądanymi właściwościami do synchronizacji konfiguracji lub warunków modułu. 

## <a name="edgeagent-desired-properties"></a>EdgeAgent żądane właściwości

Sznurek modułu dla agenta IoT Edge jest nazywany `$edgeAgent` i koordynuje komunikację między agentem IoT Edge działającym na urządzeniu i IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifest wdrożenia na konkretnym urządzeniu jako część wdrożenia jednym urządzeniu lub na dużą skalę. 

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| schemaVersion | Musi być "1.0" | Yes |
| runtime.type | Musi być "docker" | Yes |
| runtime.settings.minDockerVersion | Wartość minimalna wersja platformy Docker wymaganych przez ten manifest wdrażania | Yes |
| runtime.settings.loggingOptions | Skonwertowanej JSON zawierający opcje rejestrowania dla kontenera agenta IoT Edge. [Opcje rejestrowania platformy docker](https://docs.docker.com/engine/admin/logging/overview/) | Nie |
| runtime.settings.registryCredentials<br>. .username {registryId} | Nazwa rejestru kontenerów. Usługi Azure Container Registry nazwa użytkownika jest zazwyczaj nazwa rejestru.<br><br> Poświadczenia rejestru są niezbędne dla wszelkich obrazów modułu, które nie są publicznie udostępniane. | Nie |
| runtime.settings.registryCredentials<br>. .password {registryId} | Hasło dla rejestru kontenerów. | Nie |
| runtime.settings.registryCredentials<br>. .address {registryId} | Adres rejestru kontenera. W przypadku Azure Container Registry adres jest zwykle *{Nazwa rejestru}. azurecr. IO*. | Nie |  
| systemModules.edgeAgent.type | Musi być "docker" | Yes |
| systemModules.edgeAgent.settings.image | Identyfikator URI obrazu agenta IoT Edge. Obecnie Agent IoT Edge nie może zaktualizować siebie. | Yes |
| systemModules.edgeAgent.settings<br>.createOptions | Skonwertowanej JSON zawierający opcje tworzenia kontenera agenta IoT Edge. [Opcje tworzenia platformy docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeAgent.configuration.id | Identyfikator wdrożenia, które są wdrożone w tym module. | IoT Hub ustawia tę właściwość, gdy manifest zostanie zastosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |
| systemModules.edgeHub.type | Musi być "docker" | Yes |
| systemModules.edgeHub.status | Musi być "uruchomiona" | Yes |
| systemModules.edgeHub.restartPolicy | Musi być "zawsze" | Yes |
| systemModules.edgeHub.settings.image | Identyfikator URI obrazu Centrum IoT Edge. | Yes |
| systemModules.edgeHub.settings<br>.createOptions | Skonwertowanej JSON zawierający opcje tworzenia kontenera Centrum IoT Edge. [Opcje tworzenia platformy docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| systemModules.edgeHub.configuration.id | Identyfikator wdrożenia, które są wdrożone w tym module. | IoT Hub ustawia tę właściwość, gdy manifest zostanie zastosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |
| moduły. {moduleId} .version | Zdefiniowane przez użytkownika ciąg reprezentujący wersję tego modułu. | Yes |
| moduły. .Type — {moduleId} | Musi być "docker" | Yes |
| moduły. .status {moduleId} | {"uruchomiona" \| "zatrzymana"} | Yes |
| moduły. .restartPolicy {moduleId} | {"nigdy nie" \| "on-Failure" \| "w złej kondycji" \| "zawsze"} | Yes |
| Moduły. {moduleId}. imagePullPolicy | {"on-Create" \| "nigdy"} | Nie |
| modules.{moduleId}.settings.image | Identyfikator URI do obrazu modułu. | Yes |
| modules.{moduleId}.settings.createOptions | Opcje tworzenia kontenera moduł zawierający JSON skonwertowanej do formatu tekstowego. [Opcje tworzenia platformy docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nie |
| moduły. .configuration.id {moduleId} | Identyfikator wdrożenia, które są wdrożone w tym module. | IoT Hub ustawia tę właściwość, gdy manifest zostanie zastosowany przy użyciu wdrożenia. Nie jest częścią manifestu wdrażania. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent zgłaszanych właściwości

Agenci IoT Edge zgłosili właściwości zawierają trzy główne informacje:

1. Stan aplikacji widoczne ostatni żądane właściwości;
2. Stan modułów aktualnie uruchomionych na urządzeniu, zgłoszony przez agenta IoT Edge; lub
3. Kopia żądane właściwości, które są aktualnie uruchomione na urządzeniu.

Ta Ostatnia informacja, kopia aktualnie żądanych właściwości, jest przydatna do stwierdzenia, czy urządzenie zastosowało najnowsze wymagane właściwości, czy nadal działa w poprzednim manifeście wdrożenia.

> [!NOTE]
> Raportowane właściwości agenta IoT Edge są przydatne, ponieważ można z nich [wysyłać zapytania przy użyciu języka zapytań IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) w celu zbadania stanu wdrożeń w odpowiedniej skali. Aby uzyskać więcej informacji na temat używania IoT Edge właściwości agenta dla stanu, zobacz [opis IoT Edge wdrożeń dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

Poniższa tabela nie zawiera informacje, które są kopiowane z żądanych właściwości.

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądanych właściwości przetworzonych przez agenta IoT Edge. |
| lastDesiredStatus.code | Ten kod stanu odnosi się do ostatnich wymaganych właściwości widzianych przez agenta IoT Edge. Dozwolone wartości: `200` sukces, `400` nieprawidłowa konfiguracja `412` Nieprawidłowa wersja schematu, `417` żądanych właściwości są puste, `500` nie powiodło się |
| lastDesiredStatus.description | Opis stanu |
| deviceHealth | `healthy` Jeśli stan czasu wykonywania wszystkich modułów `running` lub `stopped`, `unhealthy` inaczej |
| configurationHealth.{deploymentId}.health | `healthy` Jeśli stan czasu wykonywania wszystkich modułów ustawiony przez wdrożenie {deploymentId} `running` lub `stopped`, `unhealthy` inaczej |
| runtime.platform.OS | Raportowanie systemu operacyjnego, uruchomione na urządzeniu |
| runtime.platform.architecture | Raportowanie architektury procesora urządzenia |
| systemModules.edgeAgent.runtimeStatus | Zgłoszony stan IoT Edge Agent: {"uruchomiona" \| "w złej kondycji"} |
| systemModules.edgeAgent.statusDescription | Opis tekstowy stanu zgłoszonego przez agenta IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Stan IoT Edge centrum: {"uruchomiona" \| "zatrzymana" \| "Niepowodzenie" \| "wycofywania" \| "złej kondycji"} |
| systemModules.edgeHub.statusDescription | Opis tekstowy stanu Centrum IoT Edge w przypadku złej kondycji. |
| systemModules.edgeHub.exitCode | Kod zakończenia raportowany przez kontener Centrum IoT Edge w przypadku zakończenia kontenera |
| systemModules.edgeHub.startTimeUtc | Godzina ostatniego uruchomienia Centrum IoT Edge |
| systemModules.edgeHub.lastExitTimeUtc | Godzina, o której ostatnio zakończył się IoT Edge centrum |
| systemModules.edgeHub.lastRestartTimeUtc | Czas ostatniego ponownego uruchomienia Centrum IoT Edge |
| systemModules.edgeHub.restartCount | Liczba przypadków, gdy ten moduł został ponownie uruchomiony jako część zasady ponownego uruchamiania. |
| modules.{moduleId}.runtimeStatus | Stan modułu: {"uruchomiona" \| "zostało zatrzymane" \| "nie powiodło się" \| "wycofywania" \| "w złej kondycji"} |
| moduły. .statusDescription {moduleId} | Opis tekstowy stanu modułu w złej kondycji. |
| moduły. .exitCode {moduleId} | Kod zakończenia raportowany przez kontener modułów w przypadku opuszczenia kontenera |
| modules.{moduleId}.startTimeUtc | Godzina ostatniego uruchomienia modułu |
| modules.{moduleId}.lastExitTimeUtc | Czas, kiedy moduł ostatnio zakończył się |
| modules.{moduleId}.lastRestartTimeUtc | Czas, kiedy moduł został ostatniego ponownego uruchomienia |
| modules.{moduleId}.restartCount | Liczba przypadków, gdy ten moduł został ponownie uruchomiony jako część zasady ponownego uruchamiania. |

## <a name="edgehub-desired-properties"></a>EdgeHub żądane właściwości

Sznurki modułowe Centrum IoT Edge jest nazywane `$edgeHub` i koordynuje komunikację między centrum IoT Edge działającym na urządzeniu i IoT Hub. Żądane właściwości są ustawiane podczas stosowania manifest wdrożenia na konkretnym urządzeniu jako część wdrożenia jednym urządzeniu lub na dużą skalę. 

| Właściwość | Opis | Wymagane w pliku manifestu wdrożenia |
| -------- | ----------- | -------- |
| schemaVersion | Musi być "1.0" | Yes |
| routes.{routeName} | Ciąg reprezentujący trasę Centrum IoT Edge. Aby uzyskać więcej informacji, zobacz [deklarowanie tras](module-composition.md#declare-routes). | `routes` Element może być istnieje, ale jest pusty. |
| storeAndForwardConfiguration.timeToLiveSecs | Czas (w sekundach), przez jaki usługa IoT Edge Hub zachowuje komunikaty, jeśli są odłączone od punktów końcowych routingu, niezależnie od tego, czy IoT Hub czy moduł lokalny Wartość może być dowolną dodatnią liczbą całkowitą. | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub zgłaszanych właściwości

| Właściwość | Opis |
| -------- | ----------- |
| lastDesiredVersion | Ta liczba całkowita odnosi się do ostatniej wersji żądanych właściwości przetworzonych przez Centrum IoT Edge. |
| lastDesiredStatus.code | Kod stanu odnoszący się do ostatnich żądanych właściwości widzianych przez Centrum IoT Edge. Dozwolone wartości: `200` sukces, `400` nieprawidłowa konfiguracja `500` nie powiodło się |
| lastDesiredStatus.description | Opis tekstowy stanu. |
| Klienci. .status {urządzenia lub moduleId} | Stan połączenia to urządzenie lub modułu. Możliwe wartości {"Połączono" \| "odłączony"}. Tylko moduł tożsamości może być w stanie odłączonym. Urządzenia podrzędne łączące się z Centrum IoT Edge są wyświetlane tylko po nawiązaniu połączenia. |
| Klienci. .lastConnectTime {urządzenia lub moduleId} | Czas ostatniego połączenia urządzenia lub modułu. |
| Klienci. .lastDisconnectTime {urządzenia lub moduleId} | Czas ostatniego odłączenia urządzenia lub modułu. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak budować manifesty wdrożenia za pomocą tych właściwości, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).
