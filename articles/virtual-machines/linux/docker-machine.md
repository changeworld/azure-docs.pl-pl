---
title: Umożliwia tworzenie hosty systemu Linux na platformie Azure maszyny Docker | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania maszyny Docker można utworzyć hostów Docker na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 26e54efc32aa316e1da93598cc861003aefff182
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29125507"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Tworzenie hostów na platformie Azure przy użyciu rozwiązania Docker maszyny
W tym artykule szczegółowo sposób użycia [maszyny Docker](https://docs.docker.com/machine/) utworzyć hostów na platformie Azure. `docker-machine` Polecenie tworzy maszynę wirtualną systemu Linux (VM) na platformie Azure, a następnie instaluje Docker. Następnie można zarządzać hostach Docker na platformie Azure przy użyciu tych samych narzędzi lokalnych i przepływów pracy. Aby użyć docker komputera w systemie Windows 10, należy użyć bash systemu Linux.

## <a name="create-vms-with-docker-machine"></a>Tworzenie maszyn wirtualnych z maszyną Docker
Najpierw uzyskać identyfikator subskrypcji platformy Azure z [Pokaż konto az](/cli/azure/account#az_account_show) w następujący sposób:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Tworzenie maszyn wirtualnych z hostów Docker na platformie Azure z `docker-machine create` , określając *azure* jako sterownika. Aby uzyskać więcej informacji, zobacz [dokumentacji Docker Azure sterownika](https://docs.docker.com/machine/drivers/azure/)

Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM*, na podstawie planu "Standard D2 v2", tworzy konto użytkownika o nazwie *azureuser*i powoduje otwarcie portu *80* na hoście maszyny Wirtualnej. Wykonaj żadnych monitów, aby zalogować się do konta platformy Azure i udzielić maszyny Docker uprawnienia do tworzenia i zarządzania zasobami.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2" \
    myvm
```

Dane wyjściowe wygląda podobnie do poniższego przykładu:

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

## <a name="configure-your-docker-shell"></a>Skonfiguruj powłoki Docker
Aby połączyć się z hostów Docker na platformie Azure, należy zdefiniować odpowiednie ustawienia połączeń. Jak wspomniano na końcu danych wyjściowych, wyświetlić informacje o połączeniu dla hosta Docker w następujący sposób: 

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

Aby zdefiniować ustawienia połączenia, albo można uruchomić polecenie sugerowanych konfiguracji (`eval $(docker-machine env myvm)`), lub ręcznie ustawić zmienne środowiskowe. 

## <a name="run-a-container"></a>Uruchom kontenera
Aby wyświetlić kontenera akcji, umożliwia uruchamianie podstawowy serwer sieci Web NGINX. Tworzenie kontenera z `docker run` i ujawnia port 80 dla ruchu w sieci web w następujący sposób:

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

Widok uruchomionych kontenerów z `docker ps`. Następujące przykładowe dane wyjściowe wyglądają kontener NGINX z portu 80 udostępniane:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Kontener testu
Uzyskaj publiczny adres IP hosta Docker w następujący sposób:


```bash
docker-machine ip myvm
```

Aby wyświetlić kontenera w akcji, otwórz przeglądarkę sieci web i wprowadź publicznego adresu IP w danych wyjściowych poprzednie polecenie:

![Kontener ngnix uruchomione](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Kolejne kroki
Można również utworzyć hostów z [rozszerzenia maszyny Wirtualnej platformy Docker](dockerextension.md). Przykłady przy użyciu rozwiązania Docker Compose można znaleźć [wprowadzenie Docker i redagowanie na platformie Azure](docker-compose-quickstart.md).
