---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1799193d7c1ef0c20fc1c338c30fb805d17df093
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530591"
---
W bash Shell w systemie Linux, [podsystem Windows dla systemu Linux][install-wsl] lub MacOS, użyj `curl`, aby pobrać wersję wykresu Consul Helm, w następujący sposób:

```bash
# Specify the Consul Helm chart version that will be leveraged throughout these instructions
CONSUL_HELM_VERSION=0.10.0

curl -sL "https://github.com/hashicorp/consul-helm/archive/v$CONSUL_HELM_VERSION.tar.gz" | tar xz
mv consul-helm-$CONSUL_HELM_VERSION consul-helm
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10