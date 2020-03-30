---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485984"
---
Wpisy w `deploymentconfig.json` dokumencie są mapowane na parametry [aciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr Metody | Opis |
| ----- | ----- | ----- |
| `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. W przypadku ACI wartość `ACI`musi być . |
| `containerResourceRequirements` | Nie dotyczy | Kontener dla jednostek procesora CPU i pamięci. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Liczba rdzeni procesora CPU do przydzielenia. Ustawienia domyślne`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Ilość pamięci (w GB) do przydzielenia dla tej usługi sieci web. Domyślny`0.5` |
| `location` | `location` | Region platformy Azure, aby wdrożyć tę usługę sieci Web. Jeśli nie zostanie określona, zostanie użyta lokalizacja obszaru roboczego. Więcej informacji na temat dostępnych regionów można znaleźć tutaj: [Regiony ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Czy włączyć omów dla tej usługi sieci Web. Domyślnie wartość False (Wartość domyślna) |
| `sslEnabled` | `ssl_enabled` | Czy włączyć SSL dla tej usługi sieci Web. Domyślnie wartość False. |
| `appInsightsEnabled` | `enable_app_insights` | Czy włączyć AppInsights dla tej usługi sieci Web. Domyślnie wartość False (Wartość domyślna) |
| `sslCertificate` | `ssl_cert_pem_file` | Plik cert potrzebny, jeśli protokół SSL jest włączony |
| `sslKey` | `ssl_key_pem_file` | Plik klucza potrzebny, jeśli jest włączony protokół SSL |
| `cname` | `ssl_cname` | Nazwa cname dla tego, czy protokół SSL jest włączony |
| `dnsNameLabel` | `dns_name_label` | Etykieta nazwy dns punktu końcowego oceniania. Jeśli nie określono, dla punktu końcowego oceniania zostanie wygenerowana unikatowa etykieta nazwy dns. |

Następujący JSON jest przykładową konfiguracją wdrożenia do użytku z wierszem polecenia:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```