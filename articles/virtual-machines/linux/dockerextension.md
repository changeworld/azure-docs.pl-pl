---
title: Użyj rozszerzenia maszyny Wirtualnej Azure Docker | Dokumentacja firmy Microsoft
description: Informacje o sposobie rozszerzenia maszyny Wirtualnej platformy Docker umożliwia szybkie i bezpieczne wdrażanie środowisku Docker na platformie Azure przy użyciu szablonów usługi Resource Manager i 2.0 interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 1e5a4fcfd758c12213d6de7d0f5cfcc78531ee97
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30240912"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Tworzenie środowiska Docker na platformie Azure przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker
Docker jest popularnych kontenera zarządzania i tworzenia obrazu platformy, która pozwala na szybkie pracować z kontenerami w systemie Linux. Na platformie Azure istnieją różne sposoby Docker można wdrożyć zgodnie z potrzebami. Ten artykuł dotyczy przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker i szablony usługi Azure Resource Manager 2.0 interfejsu wiersza polecenia platformy Azure. Czynności te można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 1.0](dockerextension-nodejs.md).

> [!WARNING]
> Rozszerzenie maszyny Wirtualnej platformy Docker Azure dla systemu Linux jest przestarzałe i zostaną wycofane 2018 listopad.
> Rozszerzenie jedynie instaluje Docker, dlatego lepszym sposobem zainstalowania wersji Docker wybór są rozwiązań alternatywnych, takich jak chmury init lub niestandardowe rozszerzenie skryptu. Aby uzyskać więcej informacji na temat korzystania z chmury init, zobacz [dostosować Maszynę wirtualną systemu Linux z chmury init](tutorial-automate-vm-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Omówienie rozszerzenia Docker maszyny Wirtualnej platformy Azure
Rozszerzenie maszyny Wirtualnej Azure Docker instaluje i konfiguruje demon Docker, klient Docker i rozwiązania Docker Compose na maszynie wirtualnej systemu Linux (VM). Przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker Azure, masz więcej funkcje kontroli i niż po prostu przy użyciu rozwiązania Docker maszyny lub tworzenie hostów Docker samodzielnie. Te dodatkowe funkcje, takie jak [rozwiązania Docker Compose](https://docs.docker.com/compose/overview/), marka rozszerzenia maszyny Wirtualnej Azure Docker nadaje się do bardziej niezawodne środowiska dewelopera lub produkcji.

Aby uzyskać więcej informacji o różnych metodach wdrażania, tym przy użyciu rozwiązania Docker maszyny i usługi kontenera platformy Azure zobacz następujące artykuły:

* Aby szybko prototypu usługi aplikacji można utworzyć przy użyciu jednego hosta Docker [maszyny Docker](docker-machine.md).
* Aby utworzyć środowiska gotowe do produkcji, skalowalnej, które zapewniają dodatkowe planowanie i narzędzia do zarządzania, można wdrożyć [Kubernetes](../../container-service/kubernetes/index.yml) lub [Docker Swarm](../../container-service/dcos-swarm/index.yml) klastra na usługi kontenera platformy Azure.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Wdrażanie szablonu z rozszerzeniem maszyny Wirtualnej Azure Docker
Użyjmy istniejący szablon szybkiego startu można utworzyć maszyny Wirtualnej systemu Ubuntu używa rozszerzenia maszyny Wirtualnej Azure Docker do instalowania i konfigurowania hosta Docker. Możesz wyświetlić szablon, w tym miejscu: [proste wdrożenie maszyny Wirtualnej systemu Ubuntu z rozwiązaniem Docker z](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie należy wdrożyć maszynę Wirtualną z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_create) zawierającej rozszerzenie Azure Docker VM z [tego szablonu usługi Azure Resource Manager w witrynie GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Po wyświetleniu monitu podaj unikatowe wartości dla *newStorageAccountName*, *adminUsername*, *adminPassword*, i *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Trwa kilka minut dla wdrożenia, aby zakończyć.


## <a name="deploy-your-first-nginx-container"></a>Wdrażanie Twojego pierwszego kontener NGINX
Aby wyświetlić szczegóły maszyny Wirtualnej, łącznie z nazwą DNS, należy użyć [az maszyny wirtualnej pokazu](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH do nowego hosta platformy Docker. Podaj własną nazwę użytkownika i nazwę DNS z powyższych kroków:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Po zalogowaniu się na hoście Docker, uruchom teraz kontener NGINX:

```bash
sudo docker run -d -p 80:80 nginx
```

Wynik jest podobny do poniższego przykładu jako obraz NGINX jest pobierany i uruchomić kontener:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Sprawdź stan kontenery uruchomionych na hoście Docker w następujący sposób:

```bash
sudo docker ps
```

Dane wyjściowe podobne do poniższego przykładu, przedstawiający kontener NGINX jest uruchomiona i porty TCP 80 i 443 i przesyłane dalej:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Aby zapoznać się z kontenera w akcji, otwórz przeglądarkę sieci web i wprowadź nazwę DNS hosta Docker:

![Kontener ngnix uruchomione](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Odwołanie do szablonu Azure rozszerzenia maszyny Wirtualnej platformy Docker
W poprzednim przykładzie użyto istniejący szablon szybkiego startu. Można także wdrożyć rozszerzenie maszyny Wirtualnej Azure Docker z szablonami usługi Resource Manager. Aby to zrobić, dodaj następującą wartość do szablonów usługi Resource Manager, definiowanie `vmName` maszyny wirtualnej odpowiednio:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Można znaleźć bardziej szczegółowe wskazówki na korzystanie z szablonów usługi Resource Manager odczytując [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Kolejne kroki
Warto [skonfiguruj demona Docker TCP port](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), zrozumieć [zabezpieczeń Docker](https://docs.docker.com/engine/security/security/), lub wdrażanie kontenerów przy użyciu [rozwiązania Docker Compose](https://docs.docker.com/compose/overview/). Aby uzyskać więcej informacji na rozszerzenia maszyny Wirtualnej Azure Docker, się, zobacz [projektu GitHub](https://github.com/Azure/azure-docker-extension/).

Przeczytaj więcej o dodatkowe opcje wdrażania Docker na platformie Azure:

* [Użyj maszyny Docker ze sterownikiem Azure](docker-machine.md)  
* [Rozpoczynanie pracy z Docker i wysyłanych do definiowania i uruchomić aplikację usługi kontenera na maszynie wirtualnej platformy Azure](docker-compose-quickstart.md).
* [Wdrażanie klastra usługi Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)

