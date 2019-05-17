---
title: Obsługę zasobów Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Dowiedz się, które regiony platformy Azure i rozmiary maszyn wirtualnych są obsługiwane przez program Microsoft Azure Red Hat OpenShift.
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/15/2019
ms.openlocfilehash: 5182a5e325bd7883af1a7d102d3e02b277a5089e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788695"
---
# <a name="azure-red-hat-openshift-resources"></a>Zasoby platformy Azure Red Hat OpenShift

Ten temat zawiera listę regionów platformy Azure i rozmiary maszyn wirtualnych obsługiwanych przez usługę Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Regiony platformy Azure

Zobacz [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) bieżącą listę regionów, w którym można wdrożyć Azure Red Hat OpenShift klastrów.

## <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych

Poniżej przedstawiono rozmiary obsługiwanych maszyn wirtualnych, które możesz określić dla węzłów obliczeniowych w klastrze Azure Red Hat OpenShift.

> [!Important]
> Każda maszyna wirtualna ma różną liczbę dysków, które mogą być dołączone. To może nie być jak od razu oczywista jako pamięci lub procesora CPU, rozmiaru.
> Nie wszystkie rozmiary maszyn wirtualnych są dostępne we wszystkich regionach. Nawet wtedy, gdy interfejs API obsługuje rozmiar, które określisz, możesz otrzymać błąd Jeśli rozmiar nie jest dostępna w regionie, które określisz.
> Zobacz [bieżącą listę obsługiwanych maszyn wirtualnych o rozmiarach na region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) Aby uzyskać więcej informacji.

## <a name="compute-node-sizes"></a>Rozmiary węzłów obliczeniowych

Następujących rozmiarów węzłów obliczeniowych są obsługiwane przez API REST platformy Azure Red Hat OpenShift:

|Rozmiar|Procesor wirtualny|Pamięć RAM|
|-|-|-|
|Standardowa D4s v3|4|16 GB|
|Standardowa D8s v3|8|32 GB|
|Standardowa D16s v3|16|64 GB|
|Standardowa D32s v3|32|128 GB|
|-|-|-|
|Standardowa E4s v3|4|32 GB|
|Standardowa E8s v3|8|64 GB|
|Standardowa E16s v3|16|128 GB|
|Standardowa E32s v3|32|256 GB|
|-|-|-|
|Standardowa F8s v2|8|16 GB|
|Standardowa F16s v2|16|32 GB|
|Standardowa F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Rozmiary węzła głównego

Następujący wzorzec / rozmiary węzłów infrastruktury są obsługiwane w systemie Red Hat OpenShift API REST usługi Azure:

|Rozmiar|Procesor wirtualny|Pamięć RAM|
|-|-|-|
|Standardowa D4s v3|4|16 GB|
|Standardowa D8s v3|8|32 GB|
|Standardowa D16s v3|16|64 GB|
|Standardowa D32s v3|32|128 GB|

## <a name="next-steps"></a>Kolejne kroki

Spróbuj [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.