---
title: Budowy modułu w usłudze Azure IoT krawędzi | Dokumentacja firmy Microsoft
description: Dowiedz się, co jest umieszczane w modułach Azure IoT krawędzi i jak można ponownie
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 7df566ced755e1e817b3107dac8f17e9f6e9b8e4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Skonfigurowane, zrozumieć, jak moduły krawędzi IoT mogą być używane i ponownie - preview

Poszczególne urządzenia IoT krawędzi działa co najmniej dwa moduły: $edgeAgent i $edgeHub, które składają się na krawędzi IoT środowiska uruchomieniowego. Oprócz tych dwóch standardowe wszystkie urządzenia IoT można uruchomić wiele modułów, aby wykonać dowolną liczbę procesów. Jednocześnie wdrażania tych modułów do urządzenia, należy zadeklarować, które moduły są uwzględniane w sposób współdziałają ze sobą. 

*Manifest rozmieszczenia* jest dokumentem JSON, który opisuje:

* Które moduły krawędzi IoT trzeba można wdrożyć, wraz z ich opcje tworzenie i zarządzanie nimi.
* Konfiguracja koncentratora krawędzi, w tym, jak komunikaty przepływ między modułami i ostatecznie Centrum IoT.
* Opcjonalnie wartości, które można ustawić we właściwościach żądaną twins modułu do konfigurowania aplikacji pojedynczego modułu.

Wszystkie urządzenia IoT krawędzi, należy skonfigurować z manifestu wdrożenia. Nowo zainstalowany moduł wykonawczy krawędzi IoT raporty do czasu skonfigurowania z prawidłowym manifestem kod błędu. 

W samouczkach Azure IoT krawędzi tworzenia manifest rozmieszczenia za pośrednictwem kreatora w portalu Azure IoT krawędzi. Można także zastosować dla manifest wdrażania, programowo przy użyciu przerwę lub zestawu SDK usług Centrum IoT. Zapoznaj się [wdrażanie i monitorowanie] [ lnk-deploy] uzyskać więcej informacji o wdrożeniach IoT krawędzi.

## <a name="create-a-deployment-manifest"></a>Tworzenie manifestu wdrożenia

Na wysokim poziomie manifest wdrażania konfiguruje dwie modułu odpowiednie właściwości dla modułów krawędzi IoT wdrożone na urządzeniu IoT krawędzi. Zawsze występują dwa z tych modułów: agent krawędzi i koncentrator krawędzi.

Manifest wdrażania, zawierający tylko krawędzi IoT środowiska uruchomieniowego (agenta i koncentrator) jest nieprawidłowy.

Manifest tej struktury są następujące:

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
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

Oprócz ustalenia odpowiednie właściwości wszelkich modułów, które mają zostać wdrożone, należy Poinformuj środowiska uruchomieniowego krawędzi IoT, jak mają być instalowane. Informacje o konfiguracji i zarządzania dla wszystkich modułów przechodzi wewnątrz **$edgeAgent** żądanego właściwości. Informacje te obejmują parametry konfiguracji dla samego agenta krawędzi. 

Aby uzyskać pełną listę właściwości, które mogą lub muszą być włączone, zobacz [właściwości krawędź agent i koncentrator krawędzi](module-edgeagent-edgehub.md).

Właściwości $edgeAgent wykonaj tej struktury:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
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
            "{module1}": { //optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Deklarowanie tras

Koncentrator krawędzi zapewnia sposób deklaratywnego tras wiadomości między modułami i między modułami i Centrum IoT. Centrum krawędzi zarządza cała komunikacja, informacje o trasie przechodzi wewnątrz **$edgeHub** żądanego właściwości. Może mieć wiele tras w ramach tego samego wdrożenia.

Trasy są zadeklarowane w **$edgeHub** żądanego właściwości przy użyciu następującej składni:

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

Każdy wymaga źródłowy i odbiorczy, ale ten warunek jest opcjonalny fragment, który służy do filtrowania wiadomości. 


### <a name="source"></a>Element źródłowy
Źródło Określa, skąd wiadomości. Może być jedną z następujących wartości:

| Element źródłowy | Opis |
| ------ | ----------- |
| `/*` | Wszystkie komunikaty urządzenia do chmury z każdego urządzenia lub modułu |
| `/messages/*` | Dowolny komunikat urządzenia do chmury wysyłanych przez urządzenia, czy moduł za pomocą niektórych lub nie danych wyjściowych |
| `/messages/modules/*` | Dowolny komunikat urządzenia do chmury wysyłane przez moduł za pomocą niektórych lub nie danych wyjściowych |
| `/messages/modules/{moduleId}/*` | Dowolny komunikat urządzenia do chmury wysyłane przez {moduleId} z żadnych danych wyjściowych |
| `/messages/modules/{moduleId}/outputs/*` | Dowolny komunikat urządzenia do chmury wysyłane przez {moduleId}, przy czym niektóre dane wyjściowe |
| `/messages/modules/{moduleId}/outputs/{output}` | Dowolny komunikat urządzenia do chmury wysyłany przy użyciu {moduleId} {wyjściowych} |

### <a name="condition"></a>Warunek
Warunek jest opcjonalne w deklaracji trasy. Jeśli chcesz przekazać wszystkie komunikaty z sink do źródła, po prostu Opuść **gdzie** klauzuli całkowicie. Lub użyć [język zapytań Centrum IoT] [ lnk-iothub-query] do filtrowania niektórych komunikatów lub typów komunikatów, które spełniają warunek.

Komunikaty, które upłynąć między modułami w programie IoT Edge sformatowane taki sam, jak komunikaty, które są przekazywane między urządzeniami a Centrum IoT Azure. Wszystkie komunikaty są w formacie JSON i mieć **systemProperties**, **parametr appProperties**, i **treści** parametrów. 

Może tworzyć kwerendy wokół wszystkie trzy parametry przy użyciu następującej składni: 

* Właściwości systemu: `$<propertyName>` lub `{$<propertyName>}`
* Właściwości aplikacji: `<propertyName>`
* Właściwości treści: `$body.<propertyName>` 

Przykłady dotyczące tworzenia zapytań dotyczących właściwości wiadomości, zobacz [komunikat urządzenia do chmury wyrażenia zapytań tras](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).

Przykładem jest specyficzna dla krawędzi IoT jest można filtrować komunikaty, które dotarły urządzenie bramy z urządzenia typu liść. Komunikaty, które pochodzą z modułów zawiera właściwość systemu o nazwie **connectionModuleId**. Dlatego do przesyłania wiadomości z urządzeń liścia bezpośrednio do Centrum IoT, użyć Następująca trasa wykluczyć modułu:

```sql
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Ujście
Obiekt sink definiuje, którego wysyłane są komunikaty. Może być jedną z następujących wartości:

| Ujście | Opis |
| ---- | ----------- |
| `$upstream` | Wysyła komunikat do Centrum IoT |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Wyślij wiadomość do wprowadzania `{input}` modułu `{moduleId}` |

Należy pamiętać, że Centrum krawędzi zapewnia na najmniej jednokrotne gwarancje, co oznacza, że komunikaty są przechowywane lokalnie w przypadku trasy nie może dostarczyć wiadomości do jego odbioru, na przykład Centrum krawędzi nie może połączyć się z Centrum IoT lub moduł docelowy nie jest połączona.

Koncentrator krawędzi przechowuje komunikaty do przez czas określony w `storeAndForwardConfiguration.timeToLiveSecs` właściwość [Centrum krawędzi żądanego właściwości](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Zdefiniuj lub zaktualizuj odpowiednie właściwości 

Manifest wdrażania można określić właściwości żądaną dwie modułu każdego modułu wdrożony na urządzeniu IoT krawędzi. Jeśli odpowiednie właściwości są określone w manifeście rozmieszczenia, zastępują wszystkie odpowiednie właściwości aktualnie w dwie modułu.

Jeśli nie określisz dwie modułu odpowiednie właściwości w manifeście rozmieszczenia, Centrum IoT nie zmodyfikuje dwie modułu w jakikolwiek sposób i będzie można programowo Ustaw odpowiednie właściwości.

Te same mechanizmy, które umożliwiają modyfikowanie twins urządzenia są używane do modyfikowania twins modułu. Zapoznaj się [przewodnik dewelopera dwie urządzenia](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins) Aby uzyskać więcej informacji.   

## <a name="deployment-manifest-example"></a>Przykład manifestu wdrożenia

Ten przykład dokumentu JSON manifestu wdrożenia.

```json
{
"moduleContent": {
    "$edgeAgent": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
                "type": "docker",
                "settings": {
                    "minDockerVersion": "v1.25",
                    "loggingOptions": ""
                }
            },
            "systemModules": {
                "edgeAgent": {
                    "type": "docker",
                    "settings": {
                    "image": "microsoft/azureiotedge-agent:1.0-preview",
                    "createOptions": ""
                    }
                },
                "edgeHub": {
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                    "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

* Aby uzyskać pełną listę właściwości, które mogą lub muszą być zawarte w $edgeAgent i $edgeHub, zobacz [właściwości krawędź agent i koncentrator krawędzi](module-edgeagent-edgehub.md).

* Teraz, gdy wiesz, jak są używane moduły krawędzi IoT, [zrozumieć wymagania i narzędzi do tworzenia modułów krawędzi IoT][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
