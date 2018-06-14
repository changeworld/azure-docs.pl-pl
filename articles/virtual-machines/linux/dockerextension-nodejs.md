---
title: Użyj rozszerzenia maszyny Wirtualnej Azure Docker z interfejsu wiersza polecenia platformy Azure w wersji 1.0 | Dokumentacja firmy Microsoft
description: Informacje o sposobie rozszerzenia maszyny Wirtualnej platformy Docker umożliwia szybkie i bezpieczne wdrażanie środowisku Docker na platformie Azure przy użyciu szablonów usługi Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 2f981a50887138660d26a9d011870a05f1270c94
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30906617"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Tworzenie środowiska Docker na platformie Azure przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker z interfejsu wiersza polecenia platformy Azure w wersji 1.0
Docker jest popularnych kontenera zarządzania i tworzenia obrazu platformy, która pozwala szybko pracować z kontenerami w systemie Linux (i również z systemem Windows). Na platformie Azure istnieją różne sposoby Docker można wdrożyć zgodnie z potrzebami. Ten artykuł dotyczy przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker i szablonów usługi Azure Resource Manager. 

Aby uzyskać więcej informacji o różnych metodach wdrażania, tym przy użyciu rozwiązania Docker maszyny i usługi kontenera platformy Azure zobacz następujące artykuły:

* Aby szybko prototypu usługi aplikacji można utworzyć przy użyciu jednego hosta Docker [maszyny Docker](docker-machine.md).
* W przypadku większych i bardziej stabilne środowisk, można użyć rozszerzenia maszyny Wirtualnej Azure Docker, który również obsługuje [rozwiązania Docker Compose](https://docs.docker.com/compose/overview/) można wygenerować wdrożenia spójne kontenerów. Szczegóły tego artykułu przy użyciu rozszerzenia maszyny Wirtualnej Azure Docker.
* Aby utworzyć środowiska gotowe do produkcji, skalowalnej, które zapewniają dodatkowe planowanie i narzędzia do zarządzania, można wdrożyć [Docker Swarm klastra na usługi kontenera platformy Azure](../../container-service/dcos-swarm/container-service-deployment.md).

## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](dockerextension.md) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami 

## <a name="azure-docker-vm-extension-overview"></a>Omówienie rozszerzenia Docker maszyny Wirtualnej platformy Azure
Rozszerzenie maszyny Wirtualnej Azure Docker instaluje i konfiguruje demon Docker, klient Docker i rozwiązania Docker Compose na maszynie wirtualnej systemu Linux (VM). Przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker Azure, masz więcej funkcje kontroli i niż po prostu przy użyciu rozwiązania Docker maszyny lub tworzenie hostów Docker samodzielnie. Te dodatkowe funkcje, takie jak [rozwiązania Docker Compose](https://docs.docker.com/compose/overview/), marka rozszerzenia maszyny Wirtualnej Azure Docker nadaje się do bardziej niezawodne środowiska dewelopera lub produkcji.

Szablony usługi Azure Resource Manager definiować strukturę całego środowiska. Szablony umożliwiają tworzenie i konfigurowanie zasobów, takich jak host Docker maszyn wirtualnych, magazynu, kontroli dostępu opartej na rolach (RBAC) i diagnostyki. Można ponownie użyć tych szablonów, aby utworzyć dodatkowe wdrożenia w sposób ciągły. Aby uzyskać więcej informacji na temat usługi Azure Resource Manager i szablony, zobacz [omówienie Resource Manager](../../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Wdrażanie szablonu z rozszerzeniem maszyny Wirtualnej Azure Docker
Użyjmy istniejący szablon szybkiego startu można utworzyć maszyny Wirtualnej systemu Ubuntu używa rozszerzenia maszyny Wirtualnej Azure Docker do instalowania i konfigurowania hosta Docker. Możesz wyświetlić szablon, w tym miejscu: [proste wdrożenie maszyny Wirtualnej systemu Ubuntu z rozwiązaniem Docker z](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Należy [najnowsze interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md) zainstalowane i zarejestrowane w trybie Menedżera zasobów w następujący sposób:

```azurecli
azure config mode arm
```

Wdrożyć szablon przy użyciu wiersza polecenia platformy Azure, określając szablon identyfikatora URI. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *westus*. Użyć własnego Nazwa grupy zasobów i lokalizacji:

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Odpowiedź z monitami, aby nazwa konta magazynu, podaj nazwę użytkownika i hasło i podaj nazwę DNS. Dane wyjściowe są podobne do poniższego przykładu:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Zwraca interfejsu wiersza polecenia Azure, do wiersza polecenia po tylko kilka sekund, ale Docker host jest nadal jest utworzony i skonfigurowany przez rozszerzenie maszyny Wirtualnej Azure Docker. Trwa kilka minut dla wdrożenia, aby zakończyć. Można wyświetlić szczegółowe informacje o użyciu stanu hostów Docker `azure vm show` polecenia.

Poniższy przykład umożliwia sprawdzenie stanu maszyny wirtualnej o nazwie *myDockerVM* (nazwa domyślnego szablonu — nie zmieniać tej nazwy) w grupie zasobów o nazwie *myResourceGroup*. Wprowadź nazwę grupy zasobów utworzonej w poprzednim kroku:

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

Dane wyjściowe `azure vm show` polecenia jest podobny do poniższego przykładu:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

W górnej części danych wyjściowych widać **ProvisioningState** maszyny wirtualnej. Gdy zostaną wyświetlone *zakończyło się pomyślnie*, wdrożenie zostało ukończone i można SSH z maszyną Wirtualną.

Pod koniec dane wyjściowe *FQDN* wyświetla w pełni kwalifikowaną nazwę hosta programu Docker. Ta nazwa FQDN, które jest używane do SSH z hostem Docker w pozostałych kroków.

## <a name="deploy-your-first-nginx-container"></a>Wdrażanie Twojego pierwszego kontener nginx
Po zakończeniu wdrażania SSH do nowego hosta platformy Docker z komputera lokalnego. Wprowadź własne nazwy użytkownika i nazwy FQDN:

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

Po zalogowaniu się na hoście Docker, uruchom teraz kontener nginx:

```bash
sudo docker run -d -p 80:80 nginx
```

Wynik jest podobny do poniższego przykładu jako obraz nginx jest pobierany i uruchomić kontener:

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

Dane wyjściowe podobne do poniższego przykładu, przedstawiający kontener nginx jest uruchomiona i porty TCP 80 i 443 i przesyłane dalej:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Aby zapoznać się z kontenera w akcji, otwórz przeglądarkę sieci web i wprowadź nazwę FQDN hosta Docker:

![Kontener ngnix uruchomione](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Odwołanie do szablonu Azure rozszerzenia maszyny Wirtualnej platformy Docker
W poprzednim przykładzie użyto istniejący szablon szybkiego startu. Można także wdrożyć rozszerzenie maszyny Wirtualnej Azure Docker z szablonami usługi Resource Manager. Aby to zrobić, dodaj następującą wartość do szablonów usługi Resource Manager, definiowanie *vmName* maszyny wirtualnej odpowiednio:

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
    "typeHandlerVersion": "1.1",
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

