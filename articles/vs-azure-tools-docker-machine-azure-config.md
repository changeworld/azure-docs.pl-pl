---
title: Tworzenie hostów platformy Docker na platformie Azure przy użyciu maszyny platformy Docker | Dokumentacja firmy Microsoft
description: W tym artykule opisano używanie maszyny platformy Docker do tworzenia hostów platformy docker na platformie Azure.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 98b2d76f4b8ba9ba8316f3a33fd2ff5ed5899833
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969531"
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Tworzenie hostów platformy Docker na platformie Azure przy użyciu maszyny platformy Docker
Uruchamianie [Docker](https://www.docker.com/) kontenery wymaga hosta maszyny Wirtualnej z systemem demona platformy docker.
W tym temacie opisano sposób użycia [maszyny platformy docker](https://docs.docker.com/machine/) polecenie, aby tworzyć nowe maszyny wirtualne systemu Linux, skonfigurowano demona platformy Docker, działające na platformie Azure. 

**Uwaga:** 

* *W tym artykule zależy na 0.9.0-rc2 wersji maszyny platformy docker lub nowszej*
* *Kontenery Windows będą obsługiwane przy użyciu maszyny platformy docker w najbliższej przyszłości*

## <a name="create-vms-with-docker-machine"></a>Tworzenie maszyn wirtualnych przy użyciu maszyny platformy Docker
Tworzenie maszyn wirtualnych z hosta platformy docker na platformie Azure przy użyciu `docker-machine create` polecenie, używając `azure` sterownika. 

Sterownik platformy Azure wymaga identyfikatora subskrypcji. Możesz użyć [wiersza polecenia platformy Azure](cli-install-nodejs.md) lub [witryny Azure Portal](https://portal.azure.com) można pobrać subskrypcji platformy Azure. 

**Za pomocą witryny Azure Portal**

* Wybierz **subskrypcje** z nawigacji po lewej stronie i skopiuj identyfikator subskrypcji.

**Korzystanie z interfejsu wiersza polecenia platformy Azure**

* Typ ```azure account list``` i skopiuj identyfikator subskrypcji.

Typ `docker-machine create --driver azure` Aby wyświetlić opcje i ich wartości domyślne.
Można również wyświetlić [dokumentacji sterownika Azure Docker](https://docs.docker.com/machine/drivers/azure/) Aby uzyskać więcej informacji. 

Poniższy przykład opiera się na [wartości domyślne](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), ale te wartości opcjonalnie ustawione: 

* usługi Azure dns dla nazwy skojarzonej z publicznym adresem IP i certyfikatów wygenerowanych. Jest to nazwa DNS maszyny wirtualnej. Maszyna wirtualna może, a następnie bezpiecznie zatrzymać, wersji dynamicznego adresu IP i zapewniają możliwość Połącz ponownie, gdy maszyna wirtualna uruchamia się ponownie za pomocą nowego adresu IP. Prefiks nazwy musi być unikatowa dla tego regionu UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Otwieranie portu 80 na maszynie Wirtualnej dla wychodzącego dostępu do Internetu
* rozmiar maszyny wirtualnej, aby korzystać z szybszego magazynu premium
* Usługa Premium storage używany na potrzeby dysku maszyny wirtualnej

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Wybierz hosta platformy docker przy użyciu maszyny platformy docker
Po utworzeniu wpisu w maszyny platformy docker dla hosta, można ustawić domyślnego hosta podczas uruchamiania polecenia docker.

## <a name="using-powershell"></a>Korzystanie z programu PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Za pomocą powłoki Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Można teraz uruchamianie poleceń docker względem określonego hosta

```
docker ps
docker info
```

## <a name="run-a-container"></a>Do uruchomienia kontenera
Host został skonfigurowany możesz teraz uruchomić prosty serwer sieci web do sprawdzenia, czy hoście zostały poprawnie skonfigurowane.
W tym miejscu wyświetlonym firma Microsoft za pomocą obrazu serwera nginx standardowe, określ, czy powinna nasłuchiwać na porcie 80, a, jeśli ponownym uruchomieniu hosta maszyny Wirtualnej, kontener ponownie uruchomić także (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

Dane wyjściowe powinny wyglądać następująco:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Kontener testu
Sprawdź uruchomione kontenery przy użyciu `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Ponadto aby wyświetlić działający kontener, wpisz `docker-machine ip <VM name>` znaleźć adres IP, aby wprowadzić w przeglądarce:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Działający kontener ngnix](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Podsumowanie
Przy użyciu maszyny platformy docker można zainicjować obsługę administracyjną hostów platformy docker na platformie Azure dla sprawdzanie poprawności usługi docker poszczególnych hostów.
W środowisku produkcyjnym hostowania kontenerów, zobacz [usługi Azure Container Service](http://aka.ms/AzureContainerService)

Tworzenie aplikacji .NET Core z programem Visual Studio, zobacz [narzędzia aparatu Docker dla programu Visual Studio](http://aka.ms/DockerToolsForVS)

