---
title: Zadeklaruj, moduły i trasy z manifesty wdrożenia — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak manifest wdrożenia oświadcza, które moduły do wdrożenia, jak wdrażać je oraz sposób tworzenia trasy wiadomości między nimi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f4828b59ffa43365f48c002262368d383dfcff05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389363"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Dowiedz się, jak wdrażać moduły oraz określenia trasy w usługi IoT Edge

Każde urządzenie usługi IoT Edge działa co najmniej dwa moduły: $edgeAgent i $edgeHub, które są częścią środowiska uruchomieniowego usługi IoT Edge. Urządzenie usługi IoT Edge można uruchamiać wiele dodatkowych modułów dla dowolnej liczby procesów. Użyj manifestu wdrażania, sprawdzić urządzenia, które moduły do zainstalowania i sposobu ich konfigurowania pod kątem współpracują ze sobą. 

*Manifest wdrożenia* to dokument JSON, który opisuje:

* **Agenta usługi IoT Edge** bliźniaczą reprezentację modułu, który zawiera trzy składniki. 
  * Obraz kontenera dla każdego modułu, która jest uruchamiana na urządzeniu.
  * Poświadczenia dostępu do kontenera prywatnych rejestrów, zawierających obrazy modułu.
  * Instrukcje dotyczące sposobu tworzenia i zarządzania nimi każdego modułu.
* **Centrum usługi IoT Edge** bliźniaczą reprezentację modułu, która obejmuje przepływ komunikatów między modułami i ostatecznie do usługi IoT Hub.
* Opcjonalnie żądane właściwości bliźniaczych reprezentacjach żadnych dodatkowych modułów.

Wszystkie urządzenia usługi IoT Edge musi być skonfigurowany z manifestu wdrażania. Nowo zainstalowane środowisko uruchomieniowe usługi IoT Edge zgłasza kodu błędu, dopóki skonfigurowany z prawidłowym manifestem. 

Samouczki usługi Azure IoT Edge możesz tworzyć manifestu wdrożenia za pośrednictwem kreatora w portalu usługi Azure IoT Edge. Można także zastosować manifest wdrożenia, w sposób programowy za pomocą REST lub zestawu SDK z usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [wdrożenia usługi IoT Edge zrozumieć](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Tworzenie manifestu wdrożenia

Na wysokim poziomie manifest wdrażania znajduje się lista bliźniaczych reprezentacjach modułów, które są skonfigurowane przy użyciu ich żądane właściwości. Manifest wdrażania informuje urządzenia usługi IoT Edge (lub grupy urządzeń), które moduły do zainstalowania i sposobach ich konfigurowania. Manifesty wdrożenia zawierają *żądane właściwości* dla każdego bliźniaczą reprezentację modułu. Urządzenia usługi IoT Edge raportować *zgłaszanych właściwości* dla każdego modułu. 

Na co manifest wdrożenia wymagane są dwa moduły: `$edgeAgent`, i `$edgeHub`. Te moduły są częścią środowiska uruchomieniowego usługi IoT Edge, które zarządza urządzenia usługi IoT Edge i modułów, uruchomić na nim. Aby uzyskać więcej informacji na temat tych modułów, zobacz [zrozumieć środowisko uruchomieniowe usługi IoT Edge oraz jej architektury](iot-edge-runtime.md).

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

Zdefiniuj, jak środowisko uruchomieniowe usługi IoT Edge instaluje moduły w danym wdrożeniu. Agent usługi IoT Edge jest składnika środowiska uruchomieniowego, który zarządza instalacji, aktualizacje i raportowanie stanu dla urządzenia usługi IoT Edge. W związku z tym bliźniacza reprezentacja modułu $edgeAgent wymaga konfiguracji i informacji dotyczących zarządzania dla wszystkich modułów. Informacje te obejmują parametry konfiguracji dla sam agent usługi IoT Edge. 

Aby uzyskać pełną listę właściwości, które mogą lub muszą być włączone, zobacz [właściwości agenta usługi IoT Edge i Centrum usługi IoT Edge](module-edgeagent-edgehub.md).

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

Centrum usługi IoT Edge zarządza komunikacją między modułów, usługa IoT Hub i wszystkie urządzenia typu liść. W związku z tym, bliźniacza reprezentacja modułu $edgeHub zawiera żądaną właściwość o nazwie *trasy* Określa jak wiadomości są przekazywane w ramach danego wdrożenia. Może mieć wiele tras w tym samym wdrożeniu.

Trasy są deklarowane w **$edgeHub** żądane właściwości przy użyciu następującej składni:

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

Źródło Określa, skąd pochodzą komunikaty. Usługi IoT Edge można przesyłać wiadomości z modułów lub liści urządzeń. 

Za pomocą zestawów SDK IoT, modułów mogą zadeklarować określonych danych wyjściowych kolejki komunikatów, ich za pomocą klasy ModuleClient. Kolejki danych wyjściowych nie są niezbędne, ale są przydatne do zarządzania wiele tras. Urządzenia liścia można użyć klasy DeviceClient zestawami SDK IoT do wysyłania komunikatów do urządzenia bramy usługi IoT Edge w taki sam sposób, który prześle komunikaty do usługi IoT Hub. Aby uzyskać więcej informacji, zobacz [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).

Właściwość źródło może być dowolny z następujących wartości:

| Element źródłowy | Opis |
| ------ | ----------- |
| `/*` | Wszystkie komunikaty z urządzenia do chmury lub bliźniaczej reprezentacji powiadomienia o zmianie z dowolnego urządzenia modułu lub typu liść |
| `/twinChangeNotifications` | Każda zmiana bliźniaczych reprezentacji (zgłoszone właściwości aktualizuje) pochodzące z dowolnego urządzenia modułu lub typu liść |
| `/messages/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez moduł niektórych lub dane wyjściowe lub za pomocą urządzenia liścia |
| `/messages/modules/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez moduł niektórych lub dane wyjściowe |
| `/messages/modules/<moduleId>/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określonego modułu niektórych lub dane wyjściowe |
| `/messages/modules/<moduleId>/outputs/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określonego modułu przy użyciu pewne dane wyjściowe |
| `/messages/modules/<moduleId>/outputs/<output>` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określonego modułu przy użyciu określonych danych wyjściowych |

### <a name="condition"></a>Warunek
Warunek jest opcjonalny w deklaracji trasy. Jeśli chcesz przekazać wszystkie komunikaty ze źródła do ujścia, po prostu Opuść **gdzie** klauzuli całkowicie. Możesz też [język zapytań usługi IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) do filtrowania niektórych komunikatów lub typy komunikatów, które spełniają warunek. Trasy usługi IoT Edge nie obsługują komunikaty o filtrowaniu na podstawie tagów bliźniaczych reprezentacji lub właściwości. 

Komunikaty, które przechodzą między modułów usługi IoT Edge są formatowane w taki sam jak komunikaty, które przechodzą między urządzeniami i usługi Azure IoT Hub. Wszystkie komunikaty są w formacie JSON i mieć **systemProperties**, **parametr appProperties**, i **treści** parametrów. 

Można tworzyć zapytania dotyczące dowolnej z trzech parametrów przy użyciu następującej składni: 

* Właściwości systemu: `$<propertyName>` lub `{$<propertyName>}`
* Właściwości aplikacji: `<propertyName>`
* Właściwości treści: `$body.<propertyName>` 

Przykłady o sposobach tworzenia zapytań dotyczących właściwości wiadomości, zobacz [komunikatów z urządzenia do chmury trasy wyrażeniach zapytań](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Przykładem, które są specyficzne dla usługi IoT Edge jest, jeśli chcesz filtrować pod kątem komunikatów, które dotarły urządzenie bramy z urządzenia typu liść. Komunikaty, które pochodzą z modułów zawierają właściwość systemu o nazwie **connectionModuleId**. Dlatego jeśli chcesz routing komunikatów z urządzeń liścia bezpośrednio do usługi IoT Hub umożliwia Następująca trasa wyłączenia modułu wiadomości:

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

Usługa IoT Edge zapewnia gwarancji co najmniej jednokrotne. Centrum usługi IoT Edge komunikaty są przechowywane lokalnie w przypadku, gdy trasa nie może dostarczyć komunikatu do jego ujściem. Na przykład jeśli Centrum usługi IoT Edge nie można nawiązać połączenia usługi IoT Hub lub modułu docelowy nie jest połączony.

Centrum usługi IoT Edge zapisuje komunikaty maksymalnie przez czas określony w `storeAndForwardConfiguration.timeToLiveSecs` właściwość [Centrum usługi IoT Edge żądane właściwości](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Zdefiniuj lub aktualizowanie żądanych właściwości 

Manifest wdrażania określa żądane właściwości dla każdego modułu wdrożonego na urządzeniu usługi IoT Edge. Żądane właściwości w pliku manifestu wdrożenia zastępują wszystkie żądane właściwości, obecnie w bliźniaczej reprezentacji modułu.

Jeśli nie określisz żądane właściwości bliźniaczego modułu w pliku manifestu wdrożenia, usługa IoT Hub nie umożliwia modyfikowania bliźniaczą reprezentację modułu w dowolny sposób. Zamiast tego żądane właściwości można ustawić programowo.

Te same mechanizmy, które umożliwiają modyfikowanie bliźniaczych reprezentacji urządzeń są używane do modyfikowania bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji, zobacz [przewodnik dla deweloperów bliźniaczej reprezentacji modułu](../iot-hub/iot-hub-devguide-module-twins.md).   

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

* Aby uzyskać pełną listę właściwości, które mogą lub muszą być zawarte w $edgeAgent i $edgeHub, zobacz [właściwości agenta usługi IoT Edge i Centrum usługi IoT Edge](module-edgeagent-edgehub.md).

* Teraz, gdy wiesz, jak są używane moduły usługi IoT Edge, [zrozumieć wymagania i narzędzia do tworzenia modułów usługi IoT Edge](module-development.md).
