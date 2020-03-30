---
title: Automatyczne naprawianie węzłów usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej o funkcjach automatycznej naprawy węzłów i o tym, jak system AKS naprawia uszkodzone węzły procesu roboczego.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284844"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatyczna naprawa węzła usługi Azure Kubernetes (AKS)

Usługi AKS stale sprawdza stan kondycji węzłów procesu roboczego i wykonuje automatyczną naprawę węzłów, jeśli staną się one w złej kondycji. W tej dokumentacji opisano, jak usługa Azure Kubernetes Service (AKS) monitoruje węzły procesu roboczego i naprawia węzły procesu roboczego w złej kondycji.  Dokumentacja ma na celu poinformowanie operatorów AKS o zachowaniu funkcji naprawy węzłów. Należy również pamiętać, że platforma Azure [wykonuje konserwację na maszynach wirtualnych,][vm-updates] które występują problemy. Usługi AKS i platforma Azure współpracują ze sobą w celu zminimalizowania zakłóceń w usługach klastrów.

> [!Important]
> Funkcja automatycznego naprawy węzłów nie jest obecnie obsługiwana w pulach węzłów systemu Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS sprawdza, czy nie ma węzłów w złej kondycji

> [!Note]
> AKS wykonuje akcję naprawy w węzłach za pomocą konta użytkownika **aks-remediator**.

AKS używa reguł, aby określić, czy węzeł jest w złej kondycji i wymaga naprawy. AKS używa następujących reguł, aby określić, czy wymagana jest automatyczna naprawa.

* Węzeł zgłasza stan **NotReady** na kolejnych kontrolach w ciągu 10 minut
* Węzeł nie zgłasza stanu w ciągu 10 minut

Można ręcznie sprawdzić stan kondycji węzłów za pomocą kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Jak działa automatyczna naprawa

> [!Note]
> AKS wykonuje akcję naprawy w węzłach za pomocą konta użytkownika **aks-remediator**.

To zachowanie dotyczy **zestawów skalowania maszyny wirtualnej**.  Automatyczna naprawa wymaga kilku kroków w celu naprawy uszkodzonego węzła.  Jeśli węzeł zostanie określony jako w złej kondycji, AKS próbuje wykonać kilka kroków korygowania.  Kroki są wykonywane w następującej kolejności:

1. Po środowisko wykonawcze kontenera przestaje odpowiadać przez 10 minut, usługi wykonawcze nie działa są ponownie uruchamiane w węźle.
2. Jeśli węzeł nie jest gotowy w ciągu 10 minut, węzeł zostanie ponownie uruchomiony.
3. Jeśli węzeł nie jest gotowy w ciągu 30 minut, węzeł jest ponownie obrazowa.

> [!Note]
> Jeśli wiele węzłów jest w złej kondycji, są naprawiane jeden po drugim

## <a name="next-steps"></a>Następne kroki

Użyj [stref dostępności,][availability-zones] aby zwiększyć wysoką dostępność z obciążeniami klastra AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
