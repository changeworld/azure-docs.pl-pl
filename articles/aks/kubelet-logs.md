---
title: Pobierz dzienniki kubelet z usługi Kubernetes Azure (AKS)
description: Pobieranie dzienników kubelet z węzłów klastra usługi Kubernetes Azure (AKS)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0467be7e91fdbf4685fc41a375ea86a503e26009
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Pobieranie dzienników kubelet z węzłów klastra usługi Kubernetes Azure (AKS)

Czasami może być konieczne pobieranie dzienników kubelet z węzła usługi Kubernetes Azure (AKS) na potrzeby rozwiązywania problemów. Ten dokument zawiera szczegóły jedną z opcji ściąganie tych dzienników.

## <a name="create-an-ssh-connection"></a>Utwórz połączenie SSH

Najpierw należy utworzyć połączenie SSH z węzłem, na którym trzeba pobierać dzienników kubelet. Ta operacja została szczegółowo opisana w [SSH w węzłach klastra usługi Kubernetes Azure (AKS)] [ aks-ssh] dokumentu.

## <a name="get-kubelet-logs"></a>Pobieranie dzienników agenta kubelet

Po podłączeniu do węzła, uruchom następujące polecenie, aby pobierać dzienników kubelet.

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

Przykładowe dane wyjściowe:

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md