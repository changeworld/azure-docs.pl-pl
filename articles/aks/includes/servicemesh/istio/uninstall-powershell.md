---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: ed021f969bf861482a5730a312e4d17a42915745
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530526"
---
Aby usunąć CRDs, uruchom następujące polecenie:

```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

Aby usunąć wpisy tajne, uruchom następujące polecenie:

```powershell
(kubectl get secret --all-namespaces -o json | ConvertFrom-Json).items.metadata |% { if ($_.name -match "istio.") { "Deleting {0}.{1}" -f $_.namespace, $_.name; kubectl delete secret -n $_.namespace $_.name } }
```
