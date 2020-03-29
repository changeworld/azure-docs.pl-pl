---
title: Tworzenie hostów systemu Linux za pomocą komputera platformy Docker Machine
description: W tym artykule opisano sposób tworzenia hostów platformy Docker na platformie Azure za pomocą komputera docker.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: c3165410809d98fd0ac4eeb515fbf30578633ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968815"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Jak utworzyć hosty na platformie Azure za pomocą komputera platformy Docker Machine
W tym artykule opisano, jak używać [platformy Docker Machine](https://docs.docker.com/machine/) do tworzenia hostów na platformie Azure. Polecenie `docker-machine` tworzy maszynę wirtualną systemu Linux (VM) na platformie Azure, a następnie instaluje platformę Docker. Następnie można zarządzać hostami platformy Docker na platformie Azure przy użyciu tych samych narzędzi lokalnych i przepływów pracy. Aby korzystać z komputera docker w systemie Windows 10, należy użyć bash Linux.

## <a name="create-vms-with-docker-machine"></a>Tworzenie maszyn wirtualnych za pomocą komputera docker
Najpierw uzyskaj identyfikator subskrypcji platformy Azure za pomocą [konta az, pokaż](/cli/azure/account) w następujący sposób:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Maszyny wirtualne hosta platformy Docker można utworzyć na platformie `docker-machine create` Azure, określając *platformę Azure* jako sterownik. Aby uzyskać więcej informacji, zobacz [dokumentację sterownika platformy Azure platformy Docker](https://docs.docker.com/machine/drivers/azure/)

Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM*, na podstawie planu "Standard D2 v2", tworzy konto użytkownika o nazwie *azureuser*i otwiera port *80* na wirtualnej platformie wirtualnej hosta. Postępuj zgodnie z instrukcjami, aby zalogować się do konta platformy Azure i udzielić uprawnień platformy Docker Machine do tworzenia zasobów i zarządzania nimi.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

Dane wyjściowe wyglądają podobnie do następującego przykładu:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>Konfigurowanie powłoki platformy Docker
Aby połączyć się z hostem platformy Docker na platformie Azure, zdefiniuj odpowiednie ustawienia połączenia. Jak wspomniano na końcu danych wyjściowych, informacje o połączeniu dla hosta platformy Docker są następujące: 

```bash
docker-machine env myvm
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

Aby zdefiniować ustawienia połączenia, można uruchomić polecenie sugerowanej konfiguracji (`eval $(docker-machine env myvm)`) lub ręcznie ustawić zmienne środowiskowe. 

## <a name="run-a-container"></a>Uruchamianie kontenera
Aby wyświetlić kontener w akcji, uruchom podstawowy serwer www NGINX. Utwórz kontener `docker run` z portem 80 i udostępnij go w następujący sposób:

```bash
docker run -d -p 80:80 --restart=always nginx
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Wyświetlanie uruchomionych `docker ps`kontenerów z plikem . Poniższy przykładowy wynik przedstawia kontener NGINX uruchomiony z portem 80 narażone:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Przetestuj pojemnik
Uzyskaj publiczny adres IP hosta platformy Docker w następujący sposób:


```bash
docker-machine ip myvm
```

Aby wyświetlić kontener w akcji, otwórz przeglądarkę internetową i wprowadź publiczny adres IP odnotowany w danych wyjściowych poprzedniego polecenia:

![Uruchamianie kontenera ngnix](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z przykładami używania docker Compose, zobacz [Wprowadzenie do platformy Docker i tworzenie kompozycji na platformie Azure](docker-compose-quickstart.md).
