---
title: Budowy modułu usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak manifest wdrożenia oświadcza, które moduły do wdrożenia, jak wdrażać je oraz sposób tworzenia trasy wiadomości między nimi.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: beb7574653375024f36912c4b3a37b01d2f59bd5
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248407"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Dowiedz się, jak wdrażać moduły oraz określenia trasy w usługi IoT Edge

Każde urządzenie usługi IoT Edge działa co najmniej dwa moduły: $edgeAgent i $edgeHub, które tworzą środowisko uruchomieniowe usługi IoT Edge. Ponadto dowolnego urządzenia IoT Edge można uruchamiać wiele modułów, aby wykonać dowolną liczbę procesów. Wdrażając te moduły na urządzeniu tylko raz, potrzebujesz sposobu do deklarowania modułów, które są dołączone i jak współdziałają ze sobą. 

*Manifest wdrożenia* to dokument JSON, który opisuje:

* Konfiguracja agenta usługi Edge, który zawiera obraz kontenera dla każdego modułu, poświadczenia, które mają dostęp do kontenera prywatnych rejestrów i instrukcje dotyczące sposobu tworzenia i zarządzania nimi każdy moduł.
* Konfiguracja Centrum usługi Edge, która obejmuje przepływ komunikatów między modułami i ostatecznie do usługi IoT Hub.
* Opcjonalnie żądane właściwości bliźniaczych reprezentacjach modułów.

Wszystkie urządzenia usługi IoT Edge trzeba mieć skonfigurowaną manifest wdrożenia. Nowo zainstalowane środowisko uruchomieniowe usługi IoT Edge zgłasza kodu błędu, dopóki skonfigurowany z prawidłowym manifestem. 

Samouczki usługi Azure IoT Edge możesz tworzyć manifestu wdrożenia za pośrednictwem kreatora w portalu usługi Azure IoT Edge. Można także zastosować manifest wdrożenia, w sposób programowy za pomocą REST lub zestawu SDK z usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [wdrożenia usługi IoT Edge zrozumieć](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Tworzenie manifestu wdrożenia

Na wysokim poziomie manifest wdrożenia konfiguruje żądane właściwości bliźniaczego modułu dla modułów usługi IoT Edge wdrożony na urządzeniu usługi IoT Edge. Dwa z tych modułów są zawsze obecne: `$edgeAgent`, i `$edgeHub`.

Manifest wdrażania, zawierający tylko środowisko uruchomieniowe usługi IoT Edge (agent i koncentrator) jest nieprawidłowy.

Ta struktura jest zgodna manifestu:

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Konfiguruj moduły

Należy sprawdzić środowisko uruchomieniowe usługi IoT Edge, jak zainstalować moduły w danym wdrożeniu. Informacje o konfiguracji i zarządzania dla wszystkich modułów przechodzi wewnątrz **$edgeAgent** żądane właściwości. Informacje te obejmują parametry konfiguracji dla sam agent usługi Edge. 

Aby uzyskać pełną listę właściwości, które mogą lub muszą być włączone, zobacz [właściwości agenta usługi Edge i Centrum usługi Edge](module-edgeagent-edgehub.md).

Właściwości $edgeAgent wykonaj tę strukturę:

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
            "{module1}": { // optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Zadeklaruj trasy

Centrum usługi Edge zapewnia sposób deklaratywne kierowanie komunikatów między modułami i między modułami i IoT Hub. Centrum usługi Edge zarządza całej komunikacji, więc informacje o trasie przechodzi wewnątrz **$edgeHub** żądane właściwości. Może mieć wiele tras w tym samym wdrożeniu.

Trasy są deklarowane w **$edgeHub** żądane właściwości przy użyciu następującej składni:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Każdy wymaga źródła i ujścia, ale warunek jest opcjonalne, można użyć do filtrowania wiadomości. 


### <a name="source"></a>Element źródłowy
Źródło Określa, skąd pochodzą komunikaty. Może być dowolną z następujących wartości:

| Element źródłowy | Opis |
| ------ | ----------- |
| `/*` | Wszystkie komunikaty urządzenie chmura z każdego urządzenia lub modułu |
| `/messages/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez urządzenie, czy moduł niektórych lub dane wyjściowe |
| `/messages/modules/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez moduł niektórych lub dane wyjściowe |
| `/messages/modules/{moduleId}/*` | Wszystkie komunikaty z urządzenia do chmury przez {moduleId} wysyłane żadne dane wyjściowe |
| `/messages/modules/{moduleId}/outputs/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez {moduleId} pewne dane wyjściowe |
| `/messages/modules/{moduleId}/outputs/{output}` | Wszystkie komunikaty z urządzenia do chmury przesyłane przy użyciu {moduleId}, {wyjściowych} |

### <a name="condition"></a>Warunek
Warunek jest opcjonalny w deklaracji trasy. Jeśli chcesz przekazać wszystkie komunikaty z ujścia w źródle, po prostu Opuść **gdzie** klauzuli całkowicie. Możesz też [język zapytań usługi IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) do filtrowania niektórych komunikatów lub typy komunikatów, które spełniają warunek.

Komunikaty, które przechodzą między modułów usługi IoT Edge są formatowane w taki sam jak komunikaty, które przechodzą między urządzeniami i usługi Azure IoT Hub. Wszystkie komunikaty są w formacie JSON i mieć **systemProperties**, **parametr appProperties**, i **treści** parametrów. 

Można tworzyć zapytania dotyczące wszystkich trzech parametrów przy użyciu następującej składni: 

* Właściwości systemu: `$<propertyName>` lub `{$<propertyName>}`
* Właściwości aplikacji: `<propertyName>`
* Właściwości treści: `$body.<propertyName>` 

Przykłady o sposobach tworzenia zapytań dotyczących właściwości wiadomości, zobacz [komunikatów z urządzenia do chmury trasy wyrażeniach zapytań](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Przykładem, które są specyficzne dla usługi IoT Edge jest, jeśli chcesz filtrować pod kątem komunikatów, które dotarły urządzenie bramy z urządzenia typu liść. Komunikaty, które pochodzą z modułów zawierają właściwość systemu o nazwie **connectionModuleId**. Dlatego jeśli chcesz routing komunikatów z urządzeń liścia bezpośrednio do usługi IoT Hub umożliwia Następująca trasa wyłączenia modułu wiadomości:

```sql
FROM /messages/\* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Ujście
Obiekt sink definiuje, w której są wysyłane komunikaty. Może być dowolną z następujących wartości:

| Ujście | Opis |
| ---- | ----------- |
| `$upstream` | Wysyła komunikat do Centrum IoT Hub |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Wyślij wiadomość do wprowadzania `{input}` modułu `{moduleId}` |

Usługa IoT Edge zapewnia gwarancji co najmniej jednokrotne. Centrum usługi Edge komunikaty są przechowywane lokalnie w przypadku, gdy trasa nie może dostarczyć komunikatu do jego ujściem. Na przykład jeśli Centrum usługi Edge nie można nawiązać połączenia usługi IoT Hub lub modułu docelowy nie jest połączony.

Centrum usługi Edge zapisuje komunikaty maksymalnie przez czas określony w `storeAndForwardConfiguration.timeToLiveSecs` właściwość [Centrum usługi Edge żądane właściwości](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Zdefiniuj lub aktualizowanie żądanych właściwości 

Manifest wdrażania można określić żądane właściwości bliźniaczej reprezentacji modułu każdego modułu, wdrożone na urządzeniu usługi IoT Edge. Jeśli żądane właściwości są określone w manifeście wdrożenia, zastępują wszystkie żądane właściwości, obecnie w bliźniaczej reprezentacji modułu.

Jeśli nie określisz żądane właściwości bliźniaczego modułu w pliku manifestu wdrożenia, usługi IoT Hub nie zmodyfikuje bliźniaczą reprezentację modułu w dowolny sposób i będzie można programowo ustawić żądane właściwości.

Te same mechanizmy, które umożliwiają modyfikowanie bliźniaczych reprezentacji urządzeń są używane do modyfikowania bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji, zobacz [przewodnik dla deweloperów bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md).   

## <a name="deployment-manifest-example"></a>Przykład manifestu wdrożenia

Ten przykład dokumentem JSON manifestu wdrażania.

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
                "password": "{password}",
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
              "createOptions": ""
            }
          }
        },
        "modules": {
          "tempSensor": {
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
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
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

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać pełną listę właściwości, które mogą lub muszą być zawarte w $edgeAgent i $edgeHub, zobacz [właściwości agenta usługi Edge i Centrum usługi Edge](module-edgeagent-edgehub.md).

* Teraz, gdy wiesz, jak są używane moduły usługi IoT Edge, [zrozumieć wymagania i narzędzia do tworzenia modułów usługi IoT Edge](module-development.md).
