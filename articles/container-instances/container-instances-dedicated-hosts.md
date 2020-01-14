---
title: Wdróż na dedykowanych hostach
description: Używanie dedykowanych hostów do osiągnięcia prawdziwej izolacji na poziomie hosta dla obciążeń
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903758"
---
# <a name="deploy-on-dedicated-hosts"></a>Wdróż na dedykowanych hostach

"Dedykowana" to jednostka SKU Azure Container Instances (ACI), która zapewnia odizolowane i dedykowane środowisko obliczeniowe do bezpiecznego uruchamiania kontenerów. Użycie dedykowanej jednostki SKU powoduje, że każda grupa kontenerów ma dedykowany serwer fizyczny w centrum danych platformy Azure, zapewniając pełną izolację obciążenia, która pomaga spełnić wymagania dotyczące zabezpieczeń i zgodności w organizacji. 

Dedykowana jednostka SKU jest odpowiednia dla obciążeń kontenera, które wymagają izolacji obciążenia z perspektywy serwera fizycznego.

## <a name="using-the-dedicated-sku"></a>Korzystanie z dedykowanej jednostki SKU

> [!IMPORTANT]
> Użycie dedykowanej jednostki SKU jest dostępne tylko w najnowszej wersji interfejsu API (2019-12-01), która jest obecnie wycofywana. Określ tę wersję interfejsu API w szablonie wdrożenia. Ponadto domyślny limit dla każdej subskrypcji w celu użycia dedykowanej jednostki SKU wynosi 0. Jeśli chcesz używać tej jednostki SKU do wdrożeń kontenerów produkcyjnych, Utwórz [support Request platformy Azure][azure-support]

Począwszy od interfejsu API w wersji 2019-12-01, istnieje właściwość "SKU" w sekcji Właściwości grupy kontenerów szablonu wdrożenia, który jest wymagany do wdrożenia ACI. Obecnie można użyć tej właściwości jako części szablonu wdrażania Azure Resource Manager ACI. Więcej informacji na temat wdrażania zasobów ACI za pomocą szablonu można znaleźć w [samouczku: Wdrażanie grupy wielu kontenerów przy użyciu szablonu Menedżer zasobów](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Właściwość SKU może mieć jedną z następujących wartości:
* Standardowa — wybór standardowego wdrożenia ACI, który nadal gwarantuje zabezpieczenia na poziomie funkcji hypervisor 
* Dedykowana — używana do izolacji poziomu obciążenia z dedykowanymi hostami fizycznymi dla grupy kontenerów

## <a name="modify-your-json-deployment-template"></a>Modyfikowanie szablonu wdrożenia JSON

W szablonie wdrożenia, w którym określono zasób grupy kontenerów, upewnij się, że `"apiVersion": "2019-12-01",`. W sekcji Właściwości zasobu grupy kontenerów Ustaw `"sku": "Dedicated",`.

Oto przykładowy fragment dla sekcji Resources szablonu wdrożenia grupy kontenerów, który używa dedykowanej jednostki SKU:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux",
            },
            "location": "eastus2euap",
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Wdróż grupę kontenerów

Jeśli utworzono i edytowano plik szablonu wdrożenia na pulpicie, można przekazać go do katalogu Cloud Shell, przeciągając plik do niego. 

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdróż szablon za pomocą polecenia [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure. Po zakończeniu wdrożenia wszystkie dane związane z nim utrwalane przez usługę ACI zostaną zaszyfrowane przy użyciu podanego klucza.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
