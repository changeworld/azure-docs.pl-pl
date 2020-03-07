---
title: Wdróż & module Routes z manifestami wdrożenia — Azure IoT Edge
description: Dowiedz się, jak manifest wdrożenia oświadcza, które moduły do wdrożenia, jak wdrażać je oraz sposób tworzenia trasy wiadomości między nimi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8eb24fe878638853cd8519c08045552a91f0c190
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379411"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Dowiedz się, jak wdrażać moduły oraz określenia trasy w usługi IoT Edge

Każde urządzenie usługi IoT Edge działa co najmniej dwa moduły: $edgeAgent i $edgeHub, które są częścią środowiska uruchomieniowego usługi IoT Edge. Na urządzeniu IoT Edge można uruchamiać wiele dodatkowych modułów dla dowolnej liczby procesów. Użyj manifestu wdrożenia, aby poinformować urządzenie, które moduły instalować i jak skonfigurować je do pracy ze sobą.

*Manifest wdrożenia* jest dokumentem JSON opisującym:

* Moduł **IoT Edge Agent** , który obejmuje trzy składniki:
  * Obraz kontenera dla każdego modułu, który działa na urządzeniu.
  * Poświadczenia umożliwiające dostęp do prywatnych rejestrów kontenerów zawierających obrazy modułów.
  * Instrukcje dotyczące sposobu tworzenia i zarządzania każdym modułem.
* **IoT Edgey moduł centralny** , który obejmuje sposób przepływu komunikatów między modułami i ostatecznie do IoT Hub.
* Żądane właściwości dowolnego dodatkowego modułu bliźniaczych reprezentacji (opcjonalnie).

Wszystkie urządzenia usługi IoT Edge musi być skonfigurowany z manifestu wdrażania. Nowo zainstalowane środowisko uruchomieniowe usługi IoT Edge zgłasza kodu błędu, dopóki skonfigurowany z prawidłowym manifestem.

Samouczki usługi Azure IoT Edge możesz tworzyć manifestu wdrożenia za pośrednictwem kreatora w portalu usługi Azure IoT Edge. Można także zastosować manifest wdrożenia, w sposób programowy za pomocą REST lub zestawu SDK z usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [Omówienie wdrożeń IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Tworzenie manifestu wdrożenia

Na wysokim poziomie manifest wdrażania znajduje się lista bliźniaczych reprezentacjach modułów, które są skonfigurowane przy użyciu ich żądane właściwości. Manifest wdrażania informuje urządzenia usługi IoT Edge (lub grupy urządzeń), które moduły do zainstalowania i sposobach ich konfigurowania. Manifesty wdrożenia obejmują *odpowiednie właściwości* dla każdego sznurka modułu. IoT Edge urządzeń raportuje z powrotem *raportowane właściwości* dla każdego modułu.

W każdym manifeście wdrożenia są wymagane dwa moduły: `$edgeAgent`i `$edgeHub`. Te moduły są częścią środowiska uruchomieniowego usługi IoT Edge, które zarządza urządzenia usługi IoT Edge i modułów, uruchomić na nim. Aby uzyskać więcej informacji o tych modułach, zobacz [Omówienie środowiska uruchomieniowego IoT Edge i jego architektury](iot-edge-runtime.md).

Oprócz dwa moduły środowiska uruchomieniowego można dodać maksymalnie 20 modułów własne uruchomić na urządzeniu usługi IoT Edge.

Manifest wdrażania, zawierający tylko środowisko uruchomieniowe usługi IoT Edge (edgeAgent i edgeHub) jest nieprawidłowy.

Manifesty wdrożenia, wykonaj tę strukturę:

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

## <a name="configure-modules"></a>Konfiguruj moduły

Zdefiniuj, jak środowisko uruchomieniowe usługi IoT Edge instaluje moduły w danym wdrożeniu. Agent usługi IoT Edge jest składnika środowiska uruchomieniowego, który zarządza instalacji, aktualizacje i raportowanie stanu dla urządzenia usługi IoT Edge. Z tego względu sznury modułu $edgeAgent zawiera informacje o konfiguracji i zarządzaniu dla wszystkich modułów. Te informacje obejmują parametry konfiguracji dla samego agenta IoT Edge.

Aby uzyskać pełną listę właściwości, które mogą lub muszą być dołączone, zobacz [Właściwości agenta IoT Edge i IoT Edge centrum](module-edgeagent-edgehub.md).

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

## <a name="declare-routes"></a>Zadeklaruj trasy

Centrum usługi IoT Edge zarządza komunikacją między modułów, usługa IoT Hub i wszystkie urządzenia typu liść. Z tego względu sznurek modułu $edgeHub zawiera pożądaną właściwość o nazwie *trasy* , które deklarują sposób przekazywania komunikatów w ramach wdrożenia. Może mieć wiele tras w tym samym wdrożeniu.

Trasy są deklarowane w **$edgeHub** żądanych właściwości z następującą składnią:

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

Każdy wymaga źródła i ujścia, ale warunek jest opcjonalne, można użyć do filtrowania wiadomości.

### <a name="source"></a>Element źródłowy

Źródło Określa, skąd pochodzą komunikaty. IoT Edge może kierować wiadomości z modułów lub urządzeń liścia.

Korzystając z zestawów SDK IoT, moduły mogą deklarować określone kolejki wyjściowe dla swoich komunikatów przy użyciu klasy ModuleClient. Kolejki wyjściowe nie są potrzebne, ale są pomocne w zarządzaniu wieloma trasami. Urządzenia liścia mogą używać klasy DeviceClient zestawów SDK IoT do wysyłania komunikatów do urządzeń bramy IoT Edge w taki sam sposób, że wysyłają komunikaty do IoT Hub. Aby uzyskać więcej informacji, zobacz [Omówienie i używanie zestawów sdk IoT Hub platformy Azure](../iot-hub/iot-hub-devguide-sdks.md).

Właściwość źródło może być dowolny z następujących wartości:

| Element źródłowy | Opis |
| ------ | ----------- |
| `/*` | Wszystkie komunikaty z urządzenia do chmury lub bliźniaczej reprezentacji powiadomienia o zmianie z dowolnego urządzenia modułu lub typu liść |
| `/twinChangeNotifications` | Każda zmiana bliźniaczych reprezentacji (zgłoszone właściwości aktualizuje) pochodzące z dowolnego urządzenia modułu lub typu liść |
| `/messages/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez moduł za pomocą niektórych lub niewyjściowych lub przez urządzenie liścia |
| `/messages/modules/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez moduł niektórych lub dane wyjściowe |
| `/messages/modules/<moduleId>/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określonego modułu niektórych lub dane wyjściowe |
| `/messages/modules/<moduleId>/outputs/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określonego modułu przy użyciu pewne dane wyjściowe |
| `/messages/modules/<moduleId>/outputs/<output>` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określonego modułu przy użyciu określonych danych wyjściowych |

### <a name="condition"></a>Warunek

Warunek jest opcjonalny w deklaracji trasy. Jeśli chcesz przekazać wszystkie komunikaty ze źródła do ujścia, po prostu pozostaw klauzulę **WHERE** całkowicie. Można też użyć [języka zapytań IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) do filtrowania pewnych komunikatów lub typów komunikatów, które spełniają warunek. Trasy usługi IoT Edge nie obsługują komunikaty o filtrowaniu na podstawie tagów bliźniaczych reprezentacji lub właściwości.

Komunikaty, które przechodzą między modułów usługi IoT Edge są formatowane w taki sam jak komunikaty, które przechodzą między urządzeniami i usługi Azure IoT Hub. Wszystkie komunikaty są sformatowane w formacie JSON i mają parametry **systemProperties**, **appProperties**i **Body** .

Można tworzyć zapytania dotyczące dowolnej z trzech parametrów przy użyciu następującej składni:

* Właściwości systemu: `$<propertyName>` lub `{$<propertyName>}`
* Właściwości aplikacji: `<propertyName>`
* Właściwości treści: `$body.<propertyName>`

Aby zapoznać się z przykładami dotyczącymi tworzenia zapytań dotyczących właściwości wiadomości, zobacz [wyrażenia zapytania dotyczącego urządzenia do chmury](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Przykładem, które są specyficzne dla usługi IoT Edge jest, jeśli chcesz filtrować pod kątem komunikatów, które dotarły urządzenie bramy z urządzenia typu liść. Komunikaty pochodzące z modułów obejmują Właściwość systemową o nazwie **connectionModuleId**. Dlatego jeśli chcesz routing komunikatów z urządzeń liścia bezpośrednio do usługi IoT Hub umożliwia Następująca trasa wyłączenia modułu wiadomości:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Ujście

Obiekt sink definiuje, w której są wysyłane komunikaty. Tylko moduły i Centrum IoT Hub może odbierać komunikaty. Nie można skierować wiadomości do innych urządzeń. Nie istnieją żadne opcje symboli wieloznacznych, we właściwości ujścia.

Właściwości obiektu sink może być dowolny z następujących wartości:

| Ujście | Opis |
| ---- | ----------- |
| `$upstream` | Wysyła komunikat do Centrum IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Wysłać wiadomość do określonych danych wejściowych z określonego modułu |

Usługa IoT Edge zapewnia gwarancji co najmniej jednokrotne. Centrum IoT Edge przechowuje komunikaty lokalnie na wypadek, gdyby trasa nie mogła dostarczyć wiadomości do ujścia. Na przykład jeśli Centrum IoT Edge nie może połączyć się z IoT Hub lub moduł docelowy nie jest połączony.

IoT Edge Hub przechowuje komunikaty do czasu określonego we właściwości `storeAndForwardConfiguration.timeToLiveSecs` [centrum IoT Edge właściwości](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Zdefiniuj lub aktualizowanie żądanych właściwości

Manifest wdrażania określa żądane właściwości dla każdego modułu wdrożonego na urządzeniu usługi IoT Edge. Żądane właściwości w pliku manifestu wdrożenia zastępują wszystkie żądane właściwości, obecnie w bliźniaczej reprezentacji modułu.

Jeśli nie określisz żądane właściwości bliźniaczego modułu w pliku manifestu wdrożenia, usługa IoT Hub nie umożliwia modyfikowania bliźniaczą reprezentację modułu w dowolny sposób. Zamiast tego żądane właściwości można ustawić programowo.

Te same mechanizmy, które umożliwiają modyfikowanie bliźniaczych reprezentacji urządzeń są używane do modyfikowania bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji, zobacz [Przewodnik dla deweloperów modułu bliźniaczy](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Przykład manifestu wdrożenia

Poniższy przykład pokazuje, jak może wyglądać dokumentu manifestu prawidłowe wdrożenie.

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

* Aby uzyskać pełną listę właściwości, które mogą lub muszą być zawarte w $edgeAgent i $edgeHub, zobacz [Właściwości agenta IoT Edge i IoT Edge centrum](module-edgeagent-edgehub.md).

* Teraz, gdy wiesz już, jak są używane moduły IoT Edge, [Poznaj wymagania i narzędzia do opracowywania modułów IoT Edge](module-development.md).
