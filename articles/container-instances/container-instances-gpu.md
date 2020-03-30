---
title: Wdrażanie wystąpienia kontenera z obsługą procesora GPU
description: Dowiedz się, jak wdrożyć wystąpienia kontenerów platformy Azure w celu uruchamiania aplikacji kontenerów intensywnie korzystających z procesorów graficznych przy użyciu zasobów procesora GPU.
ms.topic: article
ms.date: 02/19/2020
ms.openlocfilehash: 0f1d21c62be5d7ae099faa2c6fcc440829bb451f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525292"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Wdrażanie wystąpień kontenerów korzystających z zasobów gpu

Aby uruchomić niektóre obciążenia wymagające dużej mocy obliczeniowej w wystąpieniach kontenerów platformy Azure, należy wdrożyć [grupy kontenerów](container-instances-container-groups.md) z *zasobami gpu.* Wystąpienia kontenerów w grupie mogą uzyskiwać dostęp do co najmniej jednego procesora graficznego NVIDIA Tesla podczas uruchamiania obciążeń kontenerów, takich jak CUDA i aplikacje do uczenia głębokiego.

W tym artykule pokazano, jak dodawać zasoby gpu podczas wdrażania grupy kontenerów przy użyciu [pliku YAML](container-instances-multi-container-yaml.md) lub [szablonu Menedżera zasobów](container-instances-multi-container-group.md). Można również określić zasoby gpu podczas wdrażania wystąpienia kontenera przy użyciu witryny Azure portal.

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej i obowiązują pewne [ograniczenia](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

W wersji zapoznawczej następujące ograniczenia mają zastosowanie podczas korzystania z zasobów gpu w grupach kontenerów. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Z czasem zostanie dodana pomoc techniczna dla dodatkowych regionów.

**Obsługiwane typy systemu operacyjnego:** tylko Linux

**Dodatkowe ograniczenia:** Nie można używać zasobów gpu podczas wdrażania grupy kontenerów w [sieci wirtualnej.](container-instances-vnet.md)

## <a name="about-gpu-resources"></a>Informacje o zasobach GPU

> [!IMPORTANT]
> Zasoby GPU są dostępne tylko na życzenie. Aby zażądać dostępu do zasobów gpu, prześlij [żądanie pomocy technicznej platformy Azure][azure-support].

### <a name="count-and-sku"></a>Liczba i jednostka SKU

Aby użyć procesorów GPU w wystąpieniu kontenera, określ *zasób gpu* z następującymi informacjami:

* **Count** - Liczba procesorów graficznych: **1**, **2**lub **4**.
* **SKU** - SKU GPU: **K80**, **P100**lub **V100**. Każda jednostka SKU jest mapowana na procesor gpu NVIDIA Tesla w jednej z następujących rodzin maszyn wirtualnych obsługujących procesory gpu platformy Azure:

  | SKU | Rodzina maszyn wirtualnych |
  | --- | --- |
  | K80 ( K80 ) | [NC](../virtual-machines/nc-series.md) |
  | P100 ( P100 ) | [NCv2 (właśc.](../virtual-machines/ncv2-series.md) |
  | Wersja V100 | [NCv3 (właśc.](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Podczas wdrażania zasobów gpu, ustawić zasoby procesora CPU i pamięci odpowiednie dla obciążenia, do maksymalnych wartości wyświetlanych w powyższej tabeli. Wartości te są obecnie większe niż zasoby procesora CPU i pamięci dostępne w grupach kontenerów bez zasobów gpu.  

### <a name="things-to-know"></a>Co należy wiedzieć

* **Czas wdrożenia** — utworzenie grupy kontenerów zawierającej zasoby gpu trwa do **8-10 minut.** Jest to spowodowane dodatkowym czasem na aprowizowania i konfigurowania maszyny wirtualnej gpu na platformie Azure. 

* **Cennik** — podobnie jak w przypadku grup kontenerów bez zasobów gpu, platforma Azure rozlicza zasoby zużywane przez *cały czas trwania* grupy kontenerów z zasobami gpu. Czas trwania jest obliczany od czasu, aby wyciągnąć obraz pierwszego kontenera, aż grupa kontenera kończy. Nie zawiera czasu wdrożenia grupy kontenerów.

  Zobacz [szczegóły cen .](https://azure.microsoft.com/pricing/details/container-instances/)

* **Sterowniki CUDA** — wystąpienia kontenerów z zasobami GPU są wstępnie aprowiowane za pomocą sterowników NVIDIA CUDA i środowiskach wykonywania kontenerów, dzięki czemu można używać obrazów kontenerów opracowanych dla obciążeń CUDA.

  Na tym etapie wspieramy CUDA 9.0. Można na przykład użyć następujących obrazów podstawowych dla pliku platformy Docker:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorpowy/tensorp: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Przykład YAML

Jednym ze sposobów dodawania zasobów GPU jest wdrożenie grupy kontenerów przy użyciu [pliku YAML](container-instances-multi-container-yaml.md). Skopiuj następujący plik YAML do nowego pliku o nazwie *gpu-deploy-aci.yaml,* a następnie zapisz plik. Ten YAML tworzy grupę kontenerów o nazwie *gpucontainergroup* określającą wystąpienie kontenera z procesorem graficznym K80. Wystąpienie uruchamia przykładową aplikację dodawania wektora CUDA. Żądania zasobów są wystarczające do uruchomienia obciążenia.

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

Wdrażanie grupy kontenerów za pomocą polecenia [az container create,][az-container-create] określającego nazwę pliku YAML dla tego parametru. `--file` Należy podać nazwę grupy zasobów i lokalizację dla grupy kontenerów, takich jak *eastus,* który obsługuje zasoby GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Przeprowadzenie wdrożenia zajmuje kilka minut. Następnie kontener uruchamia i uruchamia operację dodawania wektora CUDA. Uruchom polecenie [dzienniki kontenera az,][az-container-logs] aby wyświetlić dane wyjściowe dziennika:

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

## <a name="resource-manager-template-example"></a>Przykład szablonu Menedżera zasobów

Innym sposobem wdrożenia grupy kontenerów z zasobami GPU jest użycie [szablonu Menedżera zasobów](container-instances-multi-container-group.md). Rozpocznij od utworzenia pliku o nazwie `gpudeploy.json`, a następnie skopiuj do niego następujący JSON. W tym przykładzie wdraża wystąpienie kontenera z procesorem graficznym V100, który uruchamia zadanie szkoleniowe [TensorFlow](https://www.tensorflow.org/) względem zestawu danych MNIST. Żądania zasobów są wystarczające do uruchomienia obciążenia.

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

Wdrażanie szablonu za pomocą polecenia [tworzenie wdrażania grupy AZ.][az-group-deployment-create] Należy podać nazwę grupy zasobów, która została utworzona w regionie, takich jak *eastus,* który obsługuje zasoby GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Przeprowadzenie wdrożenia zajmuje kilka minut. Następnie kontener uruchamia i uruchamia zadanie TensorFlow. Uruchom polecenie [dzienniki kontenera az,][az-container-logs] aby wyświetlić dane wyjściowe dziennika:

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

Ponieważ przy użyciu zasobów GPU może być kosztowne, upewnij się, że kontenery nie działają nieoczekiwanie przez długi czas. Monitoruj kontenery w witrynie Azure portal lub sprawdź stan grupy kontenerów za pomocą polecenia [az container show.][az-container-show] Przykład:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Po zakończeniu pracy z utworzonymi wystąpieniami kontenera usuń je za pomocą następujących poleceń:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o wdrażaniu grupy kontenerów przy użyciu [pliku YAML](container-instances-multi-container-yaml.md) lub [szablonu Menedżera zasobów](container-instances-multi-container-group.md).
* Dowiedz się więcej o rozmiarach maszyn [wirtualnych zoptymalizowanych pod kątem procesora GPU](../virtual-machines/linux/sizes-gpu.md) na platformie Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
