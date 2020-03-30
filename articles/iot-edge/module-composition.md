---
title: Wdrażanie tras & modułów za pomocą manifestów wdrażania — Usługa Azure IoT Edge
description: Dowiedz się, jak manifest wdrożenia deklaruje, które moduły wdrożyć, jak je wdrożyć i jak tworzyć trasy komunikatów między nimi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8eb24fe878638853cd8519c08045552a91f0c190
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271397"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Learn how to deploy modules and establish routes in IoT Edge (Dowiedz się, jak wdrażać moduły i ustanawiać trasy w usłudze IoT Edge).

Każde urządzenie IoT Edge działa co najmniej dwa moduły: $edgeAgent i $edgeHub, które są częścią środowiska wykonawczego IoT Edge. Urządzenie IoT Edge może uruchamiać wiele dodatkowych modułów dla dowolnej liczby procesów. Użyj manifestu wdrożenia, aby poinformować urządzenie, które moduły zainstalować i jak skonfigurować je do współpracy.

*Manifest wdrażania* jest dokumentem JSON, który opisuje:

* Bliźniacza bliźniaczej reprezentacji **agenta IoT Edge,** która zawiera trzy składniki:
  * Obraz kontenera dla każdego modułu, który działa na urządzeniu.
  * Poświadczenia dostępu do rejestrów kontenerów prywatnych, które zawierają obrazy modułów.
  * Instrukcje dotyczące sposobu tworzenia i zarządzania każdym modułem.
* Bliźniacza bliźniaczej reprezentacji **modułu usługi IoT Edge,** która obejmuje przepływ wiadomości między modułami i ostatecznie do usługi IoT Hub.
* Żądane właściwości dowolnych dodatkowych bliźniaczych modułu (opcjonalnie).

Wszystkie urządzenia usługi IoT Edge muszą być skonfigurowane przy pomocą manifestu wdrażania. Nowo zainstalowane środowisko uruchomieniowe IoT Edge zgłasza kod błędu, dopóki nie zostanie skonfigurowany z prawidłowym manifestem.

W samouczkach usługi Azure IoT Edge tworzysz manifest wdrażania, przechodząc przez kreatora w portalu usługi Azure IoT Edge. Manifest wdrożenia można również zastosować programowo przy użyciu rest lub SDK usługi Usługi Usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [Opis wdrożeń usługi IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Tworzenie manifestu wdrożenia

Na wysokim poziomie manifest wdrożenia jest lista bliźniacze modułu, które są skonfigurowane z ich żądanych właściwości. Manifest wdrażania informuje urządzenie usługi IoT Edge (lub grupę urządzeń), które moduły do zainstalowania i jak je skonfigurować. Manifesty wdrażania zawierają *żądane właściwości* dla każdej bliźniaczej reprezentacji modułu. Urządzenia IoT Edge zgłaszają *zgłoszone właściwości* dla każdego modułu.

W każdym manifeście wdrażania wymagane `$edgeAgent`są `$edgeHub`dwa moduły: , i . Te moduły są częścią środowiska wykonawczego usługi IoT Edge, który zarządza urządzeniem Usługi IoT Edge i uruchomionymi na nim modułami. Aby uzyskać więcej informacji na temat tych modułów, zobacz [Opis środowiska wykonawczego usługi IoT Edge i jego architektury.](iot-edge-runtime.md)

Oprócz dwóch modułów środowiska uruchomieniowego można dodać do 20 własnych modułów do uruchomienia na urządzeniu Z krawędzią IoT.

Manifest wdrożenia, który zawiera tylko środowisko uruchomieniowe usługi IoT Edge (edgeAgent i edgeHub) jest prawidłowy.

Manifesty wdrażania są zgodne z tą strukturą:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Konfigurowanie modułów

Zdefiniuj sposób, w jaki środowisko uruchomieniowe usługi IoT Edge instaluje moduły we wdrożeniu. Agent usługi IoT Edge to składnik środowiska uruchomieniowego, który zarządza instalacją, aktualizacjami i raportowaniem stanu urządzenia usługi IoT Edge. W związku z tym bliźniacza $edgeAgent modułu zawiera informacje o konfiguracji i zarządzaniu dla wszystkich modułów. Informacje te obejmują parametry konfiguracji samego agenta usługi IoT Edge.

Aby uzyskać pełną listę właściwości, które mogą lub muszą zostać uwzględnione, zobacz [Właściwości agenta usługi IoT Edge i centrum Usługi IoT Edge](module-edgeagent-edgehub.md).

Właściwości $edgeAgent są zgodne z tą strukturą:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Deklarowanie tras

Centrum usługi IoT Edge zarządza komunikacją między modułami, centrum IoT Hub i dowolnymi urządzeniami typu liść. W związku z tym $edgeHub modułu bliźniaczej reprezentacji zawiera żądaną właściwość o nazwie *trasy,* która deklaruje, jak wiadomości są przekazywane w ramach wdrożenia. W ramach tego samego wdrożenia może być dostępnych wiele tras.

Trasy są deklarowane w **$edgeHub** żądane właściwości z następującą składnią:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Każda trasa potrzebuje źródła i ujścia, ale warunek jest opcjonalny element, który można użyć do filtrowania wiadomości.

### <a name="source"></a>Element źródłowy

Źródło określa, skąd pochodzą komunikaty. Aplikacja IoT Edge może kierować wiadomości z modułów lub urządzeń typu liść.

Za pomocą SDK IoT moduły można zadeklarować określonych kolejek wyjściowych dla swoich wiadomości przy użyciu ModuleClient klasy. Kolejki wyjściowe nie są konieczne, ale są przydatne do zarządzania wieloma trasami. Urządzenia typu Liść mogą używać klasy DeviceClient sdków IoT do wysyłania wiadomości do urządzeń bramy usługi IoT Edge w taki sam sposób, w jaki będą wysyłać wiadomości do centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [Opis zestawów SDK usługi Azure IoT Hub i korzystania z niej.](../iot-hub/iot-hub-devguide-sdks.md)

Właściwość źródło może być dowolną z następujących wartości:

| Element źródłowy | Opis |
| ------ | ----------- |
| `/*` | Wszystkie komunikaty między urządzeniami w chmurze lub powiadomienia o podwójnej zmianie z dowolnego modułu lub urządzenia typu liść |
| `/twinChangeNotifications` | Każda zmiana bliźniaczej reprezentacji (zgłoszone właściwości) pochodząca z dowolnego modułu lub urządzenia typu liść |
| `/messages/*` | Wszelkie komunikaty typu device-to-cloud wysyłane przez moduł przez niektóre dane wyjściowe lub żadne lub przez urządzenie typu leaf |
| `/messages/modules/*` | Wszelkie komunikaty z urządzenia do chmury wysyłane przez moduł za pośrednictwem niektórych danych wyjściowych lub bez |
| `/messages/modules/<moduleId>/*` | Wszelkie komunikaty z urządzenia do chmury wysyłane przez określony moduł za pośrednictwem niektórych |
| `/messages/modules/<moduleId>/outputs/*` | Wszelkie komunikaty z urządzenia do chmury wysyłane przez określony moduł za pośrednictwem niektórych danych wyjściowych |
| `/messages/modules/<moduleId>/outputs/<output>` | Wszelkie komunikaty z urządzenia do chmury wysyłane przez określony moduł za pośrednictwem określonego wyjścia |

### <a name="condition"></a>Warunek

Warunek jest opcjonalny w deklaracji trasy. Jeśli chcesz przekazać wszystkie wiadomości ze źródła do ujścia, po prostu całkowicie pominąć **klauzulę WHERE.** Lub można użyć [języka zapytań Usługi IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) do filtrowania dla niektórych wiadomości lub typów wiadomości, które spełniają warunek. Trasy IoT Edge nie obsługują filtrowania wiadomości na podstawie bliźniaczych tagów lub właściwości.

Komunikaty, które przechodzą między modułami w usłudze IoT Edge są sformatowane tak samo jak komunikaty, które przechodzą między urządzeniami i usługi Azure IoT Hub. Wszystkie wiadomości są sformatowane jako JSON i mają **systemProperties**, **appProperties**i parametry **treści.**

Można tworzyć kwerendy wokół dowolnego z trzech parametrów z następującą składnią:

* Właściwości systemu: `$<propertyName>` lub`{$<propertyName>}`
* Właściwości aplikacji:`<propertyName>`
* Właściwości ciała:`$body.<propertyName>`

Aby zapoznać się z przykładami dotyczącymi tworzenia kwerend dotyczących właściwości wiadomości, zobacz [Trasy wyrażeń kwerendy z komunikatem z chmury.](../iot-hub/iot-hub-devguide-routing-query-syntax.md)

Przykładem, który jest specyficzny dla usługi IoT Edge jest, gdy chcesz filtrować wiadomości, które dotarły do urządzenia bramy z urządzenia typu liść. Komunikaty pochodzące z modułów obejmują właściwość systemową o nazwie **connectionModuleId**. Jeśli więc chcesz rozsyłać wiadomości z urządzeń typu liść bezpośrednio do usługi IoT Hub, użyj następującej trasy, aby wykluczyć komunikaty modułu:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Ujście

Zlew definiuje, gdzie wiadomości są wysyłane. Tylko moduły i Centrum IoT mogą odbierać wiadomości. Wiadomości nie można kierować do innych urządzeń. Nie ma żadnych opcji symboli wieloznacznych we właściwości zlewu.

Właściwość sink może być dowolną z następujących wartości:

| Ujście | Opis |
| ---- | ----------- |
| `$upstream` | Wysyłanie wiadomości do Centrum IoT |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Wysyłanie wiadomości do określonego wejścia określonego modułu |

IoT Edge zapewnia co najmniej raz gwarancje. Usługa IoT Edge przechowuje wiadomości lokalnie w przypadku, gdy trasa nie może dostarczyć wiadomości do jego ujścia. Na przykład jeśli centrum usługi IoT Edge nie może połączyć się z centrum IoT Hub lub moduł docelowy nie jest połączony.

Centrum usługi IoT Edge przechowuje wiadomości `storeAndForwardConfiguration.timeToLiveSecs` do czasu określonego we właściwości [żądanych właściwościach centrum usługi IoT Edge.](module-edgeagent-edgehub.md)

## <a name="define-or-update-desired-properties"></a>Definiowanie lub aktualizowanie żądanych właściwości

Manifest wdrażania określa żądane właściwości dla każdego modułu wdrożonego na urządzeniu Usługi IoT Edge. Żądane właściwości w manifeście wdrożenia zastępują wszystkie żądane właściwości obecnie w bliźniaczej reprezentacji modułu.

Jeśli nie określisz żądane właściwości bliźniaczej reprezentacji modułu w manifeście wdrażania, Centrum IoT nie zmodyfikuje bliźniaczej reprezentacji modułu w żaden sposób. Zamiast tego można zaprogramować żądane właściwości programowo.

Te same mechanizmy, które umożliwiają modyfikowanie bliźniacze urządzenia są używane do modyfikowania bliźniacze modułu bliźniacze. Aby uzyskać więcej informacji, zobacz [przewodnik dla deweloperów bliźniaczych modułów](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Przykład manifestu wdrożenia

W poniższym przykładzie pokazano, jak może wyglądać prawidłowy dokument manifestu wdrożenia.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełną listę właściwości, które mogą lub muszą być uwzględnione w $edgeAgent i $edgeHub, zobacz [Właściwości agenta usługi IoT Edge i centrum IoT Edge](module-edgeagent-edgehub.md).

* Teraz, gdy wiesz, jak używane są moduły IoT Edge, [poznaj wymagania i narzędzia do tworzenia modułów IoT Edge](module-development.md).
