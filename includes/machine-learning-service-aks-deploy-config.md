---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486060"
---
Wpisy w `deploymentconfig.json` dokumencie są mapowane na parametry [usługi AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr Metody | Opis |
| ----- | ----- | ----- |
| `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. W przypadku usługi AKS `aks`wartość musi być . |
| `autoScaler` | Nie dotyczy | Zawiera elementy konfiguracji skalowania automatycznego. Zobacz tabelę skalowania automatycznego. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Czy włączyć skalowanie automatyczne dla usługi sieci web. `numReplicas`  = Jeśli `0` `True`, ; w `False`przeciwnym razie , . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Minimalna liczba kontenerów do użycia podczas skalowania automatycznego tej usługi sieci web. Domyślnie `1`, . |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Maksymalna liczba kontenerów do użycia podczas skalowania automatycznego tej usługi sieci web. Domyślnie `10`, . |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Jak często skalowanie automatyczne próbuje skalować tę usługę sieci web. Domyślnie `1`, . |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Docelowe wykorzystanie (w procentach ze 100), które skalowanie automatyczne należy podjąć próbę utrzymania dla tej usługi sieci web. Domyślnie `70`, . |
| `dataCollection` | Nie dotyczy | Zawiera elementy konfiguracji do zbierania danych. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Czy włączyć zbieranie danych modelu dla usługi sieci web. Domyślnie `False`, . |
| `authEnabled` | `auth_enabled` | Określa, czy włączyć uwierzytelnianie kluczy w usłudze sieci web. Zarówno, `authEnabled` jak `True` `tokenAuthEnabled` i nie może być . Domyślnie `True`, . |
| `tokenAuthEnabled` | `token_auth_enabled` | Określa, czy włączyć uwierzytelnianie tokenu dla usługi sieci web. Zarówno, `authEnabled` jak `True` `tokenAuthEnabled` i nie może być . Domyślnie `False`, . |
| `containerResourceRequirements` | Nie dotyczy | Kontener dla jednostek procesora CPU i pamięci. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Liczba rdzeni procesora CPU do przydzielenia dla tej usługi sieci web. Ustawienia domyślne`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Ilość pamięci (w GB) do przydzielenia dla tej usługi sieci web. Domyślny`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Czy włączyć rejestrowanie usługi web dla usługi sieci Web usługi Application Insights. Domyślnie `False`, . |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Limit czasu wymuszania oceniania wywołań do usługi sieci web. Domyślnie `60000`, . |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Maksymalna liczba równoczesnych żądań na węzeł dla tej usługi sieci web. Domyślnie `1`, . |
| `maxQueueWaitMs` | `max_request_wait_time` | Maksymalny czas, przez który żądanie pozostanie w kolejce (w milisekundach) przed zwróceniem błędu 503. Domyślnie `500`, . |
| `numReplicas` | `num_replicas` | Liczba kontenerów do przydzielenia dla tej usługi sieci web. Brak wartości domyślnej. Jeśli ten parametr nie jest ustawiony, skalowanie automatyczne jest domyślnie włączone. |
| `keys` | Nie dotyczy | Zawiera elementy konfiguracji kluczy. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Podstawowy klucz yuth do użycia w tej uczynkowej uczynek |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Dodatkowy klucz umajny używany dla tej usługi sieci Web |
| `gpuCores` | `gpu_cores` | Liczba rdzeni GPU (replika na kontener) do przydzielenia dla tej usługi sieci Web. Domyślna wartość wynosi 1. Obsługuje tylko wartości liczb y podstawowych. |
| `livenessProbeRequirements` | Nie dotyczy | Zawiera elementy konfiguracji dla wymagań sondy żywotności. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Jak często (w sekundach) do wykonania sondy żywotności. Domyślnie 10 sekund. Minimalna wartość to 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Liczba sekund po uruchomieniu kontenera przed zainicjowaniem sondy żywotności. Wartość domyślna to 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Liczba sekund, po których sonda żywotności upysze czas czasu. Wartość domyślna to 2 sekundy. Minimalna wartość to 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Minimalne sukcesy z rzędu dla sondy żywotności należy uznać za udane po nie powiodło się. Wartość domyślna to 1. Minimalna wartość to 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Po uruchomieniu zasobnika i sondy żywotności nie powiedzie się, Kubernetes spróbuje failureThreshold razy przed rezygnacją. Wartość domyślna to 3. Minimalna wartość to 1. |
| `namespace` | `namespace` | Obszar nazw kubernetes, w których jest wdrażana usługa sieci web. Do 63 małych liter alfanumeryczne ('a'-'z', '0'-'9') i znaki łącznika ('-'). Pierwszy i ostatni znak nie może być łącznikami. |

Następujący JSON jest przykładową konfiguracją wdrożenia do użytku z wierszem polecenia:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
