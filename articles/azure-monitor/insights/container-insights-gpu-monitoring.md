---
title: Konfigurowanie monitorowania gpu za pomocą usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak skonfigurować monitorowanie klastrów Kubernetes za pomocą węzłów obsługujących procesory NVIDIA i PROCESOR GPU FIRMY AMD za pomocą usługi Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373312"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Konfigurowanie monitorowania procesora GPU za pomocą usługi Azure Monitor dla kontenerów

Począwszy od wersji agenta *ciprod03022019*, monitor platformy Azure dla kontenerów zintegrowany agent obsługuje teraz monitorowanie użycia procesora GPU (procesor graficzny) na węzłach klastra Kubernetes obsługujących procesor GPU i monitoruj zasoby zasobami/kontenerami, żądając i korzystając z zasobów GPU.

## <a name="supported-gpu-vendors"></a>Obsługiwani dostawcy procesorów GPU

Usługa Azure Monitor for Containers obsługuje monitorowanie klastrów gpu od następujących dostawców procesorów GPU:

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Usługa Azure Monitor dla kontenerów automatycznie rozpoczyna monitorowanie użycia procesora GPU w węzłach, a procesor GPU żąda zasobników i obciążeń, zbierając następujące metryki w odstępach 60sek i przechowując je w tabeli **InsightMetrics:**

|Nazwa metryki |Wymiar metryczny (znaczniki) |Opis |
|------------|------------------------|------------|
|kontenerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Procent czasu w ciągu minionego okresu próbkowania (60 sekund), podczas którego procesor GPU był zajęty/aktywnie przetwarzał kontener. Cykl pracy to liczba od 1 do 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Każdy kontener można określić limity jako jeden lub więcej procesorów GPU. Nie można zażądać ani ograniczyć ułamka procesora GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Każdy kontener może zażądać jednego lub więcej procesorów GPU. Nie można zażądać ani ograniczyć ułamka procesora GPU.|
|kontenerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Ilość pamięci GPU w bajtach dostępnych do użycia dla określonego kontenera. |
|kontenerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Ilość pamięci GPU w bajtach używanych przez określony kontener. |
|węzełGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Liczba procesorów graficznych w węźle, który może być używany przez kubernetes. |
|węzełGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Całkowita liczba procesorów graficznych w węźle. |

## <a name="gpu-performance-charts"></a>Wykresy wydajności GPU 

Usługa Azure Monitor dla kontenerów zawiera wstępnie skonfigurowane wykresy dla metryk wymienionych wcześniej w tabeli jako skoroszytu gpu dla każdego klastra. Skoroszyt GPU **węzeł GPU** można znaleźć bezpośrednio w klastrze AKS, wybierając **skoroszyty** z lewego okienka oraz z listy rozwijanej **Widok skoroszytów** w aplikacji Insight.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Używanie procesorów GPU do obciążeń intensywnie korzystających z obliczeń w usłudze Azure Kubernetes](../../aks/gpu-cluster.md) Service (AKS), aby dowiedzieć się, jak wdrożyć klaster AKS zawierający węzły obsługujące procesora GPU.

- Dowiedz się więcej o [jednostkach SKU maszyn wirtualnych zoptymalizowanych pod kątem procesorów GPU na platformie Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Przejrzyj [obsługę procesora GPU w ulotce Kubernetes,](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) aby dowiedzieć się więcej o eksperymentalnej obsłudze usługi Kubernetes w zakresie zarządzania procesorami GPU w co najmniej jednym węzłach w klastrze.