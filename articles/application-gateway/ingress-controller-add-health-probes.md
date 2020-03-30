---
title: Dodawanie sond kondycji do zasobników AKS
description: Ten artykuł zawiera informacje na temat dodawania sond kondycji (gotowość i/lub żywotność) do zasobników usługi AKS za pomocą bramy aplikacji.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795593"
---
# <a name="add-health-probes-to-your-service"></a>Dodawanie sond kondycji do usługi
Domyślnie kontroler transferu danych przychodzących będzie aprowizować sondę HTTP GET dla narażonych zasobników.
Właściwości sondy można dostosować, dodając [gotowość lub sondę żywotności](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do specyfikacji. `deployment` / `pod`

## <a name="with-readinessprobe-or-livenessprobe"></a>Z `readinessProbe` lub`livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Odwołanie do interfejsu API kubernetes:
* [Sondy kontenerowe](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Akcja httpget](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`i `livenessProbe` są obsługiwane po `httpGet`skonfigurowaniu z programem .
> * Sondowanie na porcie innym niż ten narażony na zasobniku nie jest obecnie obsługiwane.
> * `HttpHeaders`, `InitialDelaySeconds` `SuccessThreshold` nie są obsługiwane.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Bez `readinessProbe` lub`livenessProbe`
Jeśli powyższe sondy nie są dostarczane, a następnie kontroler transferu danych `Path` przychodzących przyjąć założenie, że usługa jest osiągalna na określone dla `backend-path-prefix` adnotacji lub `path` określone w `ingress` definicji usługi.

## <a name="default-values-for-health-probe"></a>Wartości domyślne dla sondy kondycji
Dla każdej właściwości, które nie można wywnioskować przez sondę gotowości/żywotności, Domyślne wartości są ustawiane.

| Właściwość sondowania bramy aplikacji | Wartość domyślna |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |