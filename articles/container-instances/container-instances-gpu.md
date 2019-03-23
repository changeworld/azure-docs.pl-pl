---
title: Wdrażanie z włączonymi procesorami GPU w usłudze Azure container instances
description: Dowiedz się, jak wdrożyć w usłudze Azure container instances do uruchamiania na zasoby procesora GPU.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/29/2018
ms.author: danlep
ms.openlocfilehash: cc47ca07a843daf5cc35d23b838761166d39bdcc
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351375"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Wdrażanie wystąpień kontenerów, które używają zasobów procesora GPU

Do uruchamiania niektórych obciążeń intensywnie korzystających z obliczeń w usłudze Azure Container Instances, należy wdrożyć swoje [grup kontenerów](container-instances-container-groups.md) z *zasoby procesora GPU*. Wystąpienia kontenerów w grupie mają dostęp do co najmniej jeden procesory GPU NVIDIA Tesla podczas uruchamiania obciążeń kontenerów, takich jak CUDA i głębokiego uczenia aplikacji.

Jak pokazano w tym artykule, można dodać zasobów procesora GPU podczas wdrażania grupy kontenerów za pomocą [pliku YAML](container-instances-multi-container-yaml.md) lub [szablonu usługi Resource Manager](container-instances-multi-container-group.md).

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

W wersji zapoznawczej poniższe ograniczenia mają zastosowanie podczas korzystania z zasobów procesora GPU w grupy kontenerów. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Obsługa zostanie dodana dla dodatkowych regionów wraz z upływem czasu.

**Obsługiwane typy systemu operacyjnego**: Tylko system Linux

**Dodatkowe ograniczenia**: Zasoby procesora GPU nie można używać w przypadku wdrażania w grupie kontenerów [sieci wirtualnej](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>O zasoby procesora GPU

### <a name="count-and-sku"></a>Liczba i jednostki SKU

Aby użyć procesorach GPU znajdujących się w wystąpieniu kontenera, określ *zasobów procesora GPU* z następującymi informacjami:

* **Liczba** -liczba procesorów GPU: **1**, **2**, lub **4**.
* **Jednostka SKU** -jednostki SKU procesora GPU: **K80**, **P100**, lub **V100**. Każda jednostka SKU mapuje GPU NVIDIA Tesla w jednym następujących rodzin maszyn wirtualnych z obsługą procesorów GPU platformy Azure:

  | SKU | Rodzina maszyn wirtualnych |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Podczas wdrażania zasobów procesora GPU, ustaw odpowiednią dla obciążenia, do maksymalnej wartości podanych w powyższej tabeli zasobów Procesora i pamięci. Te wartości są obecnie większy niż zasoby Procesora i pamięci, które muszą być dostępne w grupy kontenerów bez zasobów procesora GPU.  

### <a name="things-to-know"></a>Należy znać

* **Czas wdrożenia** — Tworzenie grupy kontenerów, zawierający zasoby procesora GPU trwa maksymalnie **8 – 10 minut**. Jest to spowodowane dodatkowy czas, aby aprowizować i skonfigurować Maszynę wirtualną procesora GPU na platformie Azure. 

* **Cennik** — jest to podobne do grup kontenerów bez zasobów procesora GPU, platformie Azure są naliczane za zasoby używane za pośrednictwem *czas trwania* grupy kontenerów, z zasobami procesora GPU. Czas trwania jest obliczany od momentu do ściągania obrazów swój pierwszy kontener do momentu zakończenia działania grupy kontenerów. Nie ma czas wdrażania grupy kontenerów.

  Ceny są nowsze dla grupy kontenerów za pomocą procesorów GPU zasobów niż dla grupy kontenerów bez. Zobacz [cennik](https://azure.microsoft.com/pricing/details/container-instances/).

* **Sterowniki CUDA** — usługa Container instances za pomocą zasobów procesora GPU to wstępnie zainicjowana sterowniki NVIDIA CUDA i środowiska uruchomieniowe kontenera, aby można było używać obrazów kontenerów opracowana dla obciążeń CUDA.

  Na tym etapie firma Microsoft obsługuje CUDA 9.0. Na przykład można użyć następującego obrazy podstawowe dla pliku platformy Docker:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Przykład kodu YAML

Jednym ze sposobów, aby dodać zasoby procesora GPU jest wdrożenie grupy kontenerów za pomocą [pliku YAML](container-instances-multi-container-yaml.md). Skopiuj poniższego kodu YAML do nowego pliku o nazwie *procesora gpu wdrażanie aci.yaml*, następnie zapisz plik. Ta YAML tworzy grupę kontenerów o nazwie *gpucontainergroup* określając wystąpienie kontenera z K80 GPU. Przykładowa aplikacja dodanie wektor CUDA uruchamiane wystąpienie. Żądania zasobów są wystarczające do uruchomienia obciążenia.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Wdróż grupę kontenerów z [utworzyć kontener az] [ az-container-create] polecenie, określając nazwę pliku YAML `--file` parametru. Należy podać nazwę grupy zasobów i lokalizację dla grupy kontenerów, takich jak *eastus* , która obsługuje zasoby procesora GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Przeprowadzenie wdrożenia zajmuje kilka minut. Następnie kontenera rozpoczyna się i uruchamia operację dodawania CUDA wektora. Uruchom [dzienniki kontenerów az] [ az-container-logs] polecenie, aby wyświetlić dane wyjściowe dziennika:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Dane wyjściowe:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Przykładowy szablon usługi Resource Manager

Innym sposobem wdrożenia grupy kontenerów, z zasobami procesora GPU polega na użyciu [szablonu usługi Resource Manager](container-instances-multi-container-group.md). Rozpocznij od utworzenia pliku o nazwie `gpudeploy.json`, następnie skopiuj następujący kod JSON do niego. W tym przykładzie wdrażasz wystąpienie kontenera z V100 procesora GPU, który uruchamia [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) zadania szkolenia względem [zestawu danych mnist ręcznie ZAPISANYCH](http://yann.lecun.com/exdb/mnist/). Żądania zasobów są wystarczające do uruchomienia obciążenia.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Wdrażanie szablonu przy użyciu [Utwórz wdrożenie grupy az] [ az-group-deployment-create] polecenia. Należy podać nazwę grupy zasobów, która została utworzona w regionie, takich jak *eastus* , która obsługuje zasoby procesora GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Przeprowadzenie wdrożenia zajmuje kilka minut. Następnie kontenera rozpoczyna się i uruchamia zadania TensorFlow. Uruchom [dzienniki kontenerów az] [ az-container-logs] polecenie, aby wyświetlić dane wyjściowe dziennika:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Dane wyjściowe:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Korzystanie z zasobów procesora GPU może być kosztowne, upewnij się, że kontenery nie uruchamiaj nieoczekiwanie przez długi czas. Monitorowania kontenerów w witrynie Azure portal lub sprawdzić stan grupę kontenerów, z [az container show] [ az-container-show] polecenia. Na przykład:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Po zakończeniu pracy z usługą container instances został utworzony, usuń je za pomocą następujących poleceń:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o wdrażaniu grupę kontenerów za pomocą [pliku YAML](container-instances-multi-container-yaml.md) lub [szablonu usługi Resource Manager](container-instances-multi-container-group.md).
* Dowiedz się więcej o [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](../virtual-machines/linux/sizes-gpu.md) na platformie Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
