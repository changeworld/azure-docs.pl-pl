---
title: Obsługiwane zasoby dla usługi Azure Red Hat OpenShift
description: Dowiedz się, które regiony platformy Azure i rozmiary maszyn wirtualnych są obsługiwane przez usługę Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243668"
---
# <a name="azure-red-hat-openshift-resources"></a>Zasoby usługi Azure Red Hat OpenShift

W tym temacie wymieniono regiony platformy Azure i rozmiary maszyn wirtualnych obsługiwane przez usługę Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Zobacz [Produkty dostępne dla regionu](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) dla bieżącej listy regionów, w których można wdrożyć klastry Azure Red Hat OpenShift.

## <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych

Oto obsługiwane rozmiary maszyn wirtualnych, które można określić dla węzłów obliczeniowych w klastrze OpenShift usługi Azure Red Hat.

> [!Important]
> Każda maszyna wirtualna ma inną liczbę dysków, które można podłączyć. Może to nie być tak natychmiast jasne, jak rozmiar pamięci lub procesora.
> Nie wszystkie rozmiary maszyn wirtualnych są dostępne we wszystkich regionach. Nawet jeśli interfejs API obsługuje określony rozmiar, może pojawić się błąd, jeśli rozmiar nie jest dostępny w określonym regionie.
> Aby uzyskać więcej informacji, zobacz [Bieżąca lista obsługiwanych rozmiarów maszyn wirtualnych na region.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)

## <a name="compute-node-sizes"></a>Rozmiary węzłów obliczeniowych

Następujące rozmiary węzłów obliczeniowych są obsługiwane przez interfejs API rest usługi Azure Red Hat OpenShift:

|Rozmiar|Procesor wirtualny|Pamięć RAM|
|-|-|-|
|Standardowe D4s v3|4|16 GB|
|Standardowe D8s v3|8|32 GB|
|Standardowe D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standardowe F8s v2|8|16 GB|
|Standardowe F16s v2|16|32 GB|
|Standardowe F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Rozmiary węzłów głównych

Następujące rozmiary węzłów wzorca / infrastruktury są obsługiwane przez interfejs API rest usługi Azure Red Hat OpenShift:

|Rozmiar|Procesor wirtualny|Pamięć RAM|
|-|-|-|
|Standardowe D4s v3|4|16 GB|
|Standardowe D8s v3|8|32 GB|
|Standardowe D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek [tworzenia klastra OpenShift red hat.](tutorial-create-cluster.md)
