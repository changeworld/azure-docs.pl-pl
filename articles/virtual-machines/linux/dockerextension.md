---
title: Za pomocą rozszerzenia maszyny Wirtualnej Azure Docker | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szybko i bezpiecznie wdrożyć w środowisku platformy Docker na platformie Azure przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure za pomocą rozszerzenia maszyny Wirtualnej Docker
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: cynthn
ms.openlocfilehash: f30305374b1fcaabfb36533195a098073d33b6c3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197606"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Utwórz środowisko platformy Docker na platformie Azure przy użyciu rozszerzenia maszyny Wirtualnej Docker

Docker to Zarządzanie kontenerami popularnych i tworzenia obrazu platformy, która pozwala szybko pracować z kontenerami w systemie Linux. Na platformie Azure istnieją różne sposoby, które można wdrożyć platformy Docker zgodnie z potrzebami. Ten artykuł koncentruje się na temat korzystania z rozszerzenia maszyny Wirtualnej Docker i szablonów usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure. 

> [!WARNING]
> Rozszerzenie maszyny Wirtualnej Azure Docker dla systemu Linux jest przestarzały i zostanie wycofana z użycia listopada 2018 r.
> Rozszerzenia instaluje tylko Docker, co rozwiązań alternatywnych, takich jak pakiet cloud-init lub rozszerzenia niestandardowego skryptu są lepszy sposób, aby zainstalować wersję platformy Docker wybór. Aby uzyskać więcej informacji na temat korzystania z pakietu cloud-init, zobacz [dostosowywania maszyny Wirtualnej systemu Linux przy użyciu pakietu cloud-init](tutorial-automate-vm-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Rozszerzenie maszyny Wirtualnej Docker platformy Azure — omówienie
Rozszerzenie maszyny Wirtualnej Azure Docker instaluje i konfiguruje demona platformy Docker, klient platformy Docker i narzędzie Docker Compose na maszynie wirtualnej systemu Linux (VM). Za pomocą rozszerzenia maszyny Wirtualnej Azure Docker, należy mieć więcej kontroli i funkcje niż po prostu przy użyciu maszyny platformy Docker lub tworzenie hosta platformy Docker, samodzielnie. Te dodatkowe funkcje, takie jak [narzędzia Docker Compose](https://docs.docker.com/compose/overview/), markę rozszerzenia maszyny Wirtualnej Azure Docker odpowiednie dla bardziej niezawodnego środowiska dla deweloperów lub produkcji.

Aby uzyskać więcej informacji na temat różnych metod wdrażania, w tym przy użyciu maszyny platformy Docker i usług Azure Container Service zobacz następujące artykuły:

* Szybko prototypować wiadomość aplikacji, można utworzyć jeden host platformy Docker przy użyciu [maszyny platformy Docker](docker-machine.md).
* Tworzenie środowisk gotowe do produkcji, skalowalne, które obejmują dodatkowe narzędzia planowania i zarządzania, możesz je wdrożyć [Kubernetes](../../container-service/kubernetes/index.yml) lub [Docker Swarm](../../container-service/dcos-swarm/index.yml) klastra w usłudze Azure Container Service.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Wdrażanie szablonu przy użyciu rozszerzenia maszyny Wirtualnej Azure Docker
Użyjmy istniejący szablon szybkiego startu, aby utworzyć maszynę Wirtualną Ubuntu, który używa rozszerzenia maszyny Wirtualnej Azure Docker do zainstalowania i skonfigurowania hosta platformy Docker. Aby wyświetlić szablon w tym miejscu: [Proste wdrożenie maszyny wirtualnej systemu Ubuntu przy użyciu rozwiązania Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index#az_login).

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie należy wdrożyć Maszynę wirtualną za pomocą [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_create) zawierającej rozszerzenie maszyny Wirtualnej Azure Docker z [tego szablonu usługi Azure Resource Manager w witrynie GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Po wyświetleniu monitu podaj własne unikatowe wartości *newStorageAccountName*, *adminUsername*, *adminPassword*, i *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Trwa kilka minut na zakończenie wdrożenia.


## <a name="deploy-your-first-nginx-container"></a>Wdrożysz swój pierwszy kontener NGINX
Aby wyświetlić szczegóły maszyny wirtualnej, łącznie z nazwą DNS, należy użyć [az vm show](/cli/azure/vm):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH do nowego hosta platformy Docker. Podaj swoją nazwę użytkownika i nazwę DNS z poprzednich kroków:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Po zalogowaniu się do hosta platformy Docker, umożliwia uruchamianie kontenera NGINX:

```bash
sudo docker run -d -p 80:80 nginx
```

Rezultat jest podobny do poniższego przykładu, jako obraz NGINX jest pobierany i uruchomić kontener:

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

Sprawdź stan kontenery działające na hoście platformy Docker w następujący sposób:

```bash
sudo docker ps
```

Dane wyjściowe są podobne do poniższego przykładu, którym widać, że kontener NGINX jest uruchomiona i porty TCP 80 i 443 i przesyłane:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Aby wyświetlić kontenera w akcji, otwórz przeglądarkę sieci web, a następnie wprowadź nazwę DNS hosta platformy Docker:

![Działający kontener ngnix](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Odwołanie do szablonu usługi Azure rozszerzenia maszyny Wirtualnej Docker
W poprzednim przykładzie użyto istniejący szablon Szybki Start. Rozszerzenie maszyny Wirtualnej Azure Docker można także wdrożyć za pomocą szablonów usługi Resource Manager. Aby to zrobić, Dodaj następujący element do szablonów usługi Resource Manager, definiując `vmName` maszyny wirtualnej odpowiednio:

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

Można znaleźć bardziej szczegółowe wskazówki na temat korzystania z szablonów usługi Resource Manager, zapoznając się [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Kolejne kroki
Możesz też chcieć [skonfigurować demona platformy Docker, TCP port](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), zrozumieć [zabezpieczeń platformy Docker](https://docs.docker.com/engine/security/security/), lub Wdrażaj kontenery przy użyciu [narzędzia Docker Compose](https://docs.docker.com/compose/overview/). Aby uzyskać więcej informacji na temat rozszerzenia maszyny Wirtualnej Azure Docker, samego, zobacz [projektu GitHub](https://github.com/Azure/azure-docker-extension/).

Przeczytaj więcej informacji o dodatkowych opcjach wdrażania platformy Docker na platformie Azure:

* [Używanie maszyny platformy Docker ze sterownikiem platformy Azure](docker-machine.md)  
* [Wprowadzenie do platformy Docker i Compose, aby zdefiniować i uruchomić aplikację obsługującą wiele kontenerów na maszynę wirtualną platformy Azure](docker-compose-quickstart.md).
* [Wdrażanie klastra usługi Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)

