---
title: Tworzenie hostów Docker na platformie Azure z maszyną Docker | Dokumentacja firmy Microsoft
description: W tym artykule opisano stosowania Docker maszyny, aby utworzyć hostów docker na platformie Azure.
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
ms.openlocfilehash: 766d327a87ed13e04166d71c3d9ae0a1e7a66d19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23883880"
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Tworzenie hostów platformy Docker na platformie Azure przy użyciu maszyny platformy Docker
Uruchomiona [Docker](https://www.docker.com/) kontenery wymaga hosta demona docker uruchomionych maszyn wirtualnych.
W tym temacie opisano sposób użycia [docker maszyny](https://docs.docker.com/machine/) polecenie, aby utworzyć nowych maszyn wirtualnych systemu Linux, skonfigurowano demona Docker działające na platformie Azure. 

**Uwaga:** 

* *W tym artykule zależy od 0.9.0-rc2 wersją maszyny docker lub większa*
* *Kontenery systemu Windows będą obsługiwane za pośrednictwem docker maszyny w najbliższej przyszłości*

## <a name="create-vms-with-docker-machine"></a>Tworzenie maszyn wirtualnych z maszyną Docker
Tworzenie docker hosta maszyn wirtualnych na platformie Azure z `docker-machine create` polecenie, używając `azure` sterownika. 

Azure sterownik wymaga Twojego identyfikatora subskrypcji. Można użyć [interfejsu wiersza polecenia Azure](cli-install-nodejs.md) lub [Azure Portal](https://portal.azure.com) można pobrać subskrypcji platformy Azure. 

**Przy użyciu portalu Azure**

* Wybierz **subskrypcje** ze strony nawigacji po lewej stronie i skopiuj identyfikator subskrypcji.

**Korzystanie z interfejsu wiersza polecenia platformy Azure**

* Typ ```azure account list``` i skopiuj identyfikator subskrypcji.

Typ `docker-machine create --driver azure` opcje i ich wartości domyślne.
Możesz również sprawdzić [dokumentacji sterownika Azure Docker](https://docs.docker.com/machine/drivers/azure/) Aby uzyskać więcej informacji. 

Poniższy przykład, opiera się na [wartości domyślne](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), ale opcjonalnie ustawiona te wartości: 

* usługi Azure dns dla nazwy skojarzonej z publicznego adresu IP i certyfikaty generowane. Jest to nazwa DNS na komputerze wirtualnym. Maszyna wirtualna może, a następnie bezpiecznie Zatrzymaj, zwolnij dynamicznego adresu IP i umożliwiają Połącz ponownie, gdy maszyna wirtualna uruchamia się ponownie z nowego adresu IP. Prefiks nazwy musi być unikatowa w danym regionie UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Otwórz port 80 na Maszynie wirtualnej wychodzący dostęp do Internetu
* rozmiar maszyny Wirtualnej, aby korzystać z szybsze magazyn w warstwie premium
* Magazyn w warstwie Premium przeznaczony dla dysku maszyny wirtualnej

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Wybierz hosta z maszyną docker docker
Po utworzeniu wpisu docker maszyny dla hosta, można ustawić domyślnego hosta podczas uruchamiania polecenia docker.

## <a name="using-powershell"></a>Korzystanie z programu PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Przy użyciu Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Teraz możesz uruchamiać polecenia docker na określonym hoście

```
docker ps
docker info
```

## <a name="run-a-container"></a>Uruchom kontenera
Za pomocą skonfigurowanego można teraz uruchomić serwera sieci web proste, aby sprawdzić, czy host został skonfigurowany prawidłowo.
W tym miejscu wyświetlonym możemy użyć obrazu standardowe nginx, określ, czy powinien nasłuchiwać na porcie 80, a także że po ponownym uruchomieniu hosta maszyny Wirtualnej, będą kontenera Uruchom ponownie również (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

Dane wyjściowe powinien wyglądać jak poniżej:

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
Sprawdź uruchomionych kontenerów przy użyciu `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

I wyświetlić uruchomionych kontenera, wpisz `docker-machine ip <VM name>` Aby znaleźć adres IP, aby wprowadzić w przeglądarce:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Kontener ngnix uruchomione](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Podsumowanie
Docker komputera z można łatwo udostępnić hostów docker na platformie Azure dla operacji sprawdzania poprawności programu docker poszczególnych hosta.
W środowisku produkcyjnym hosting kontenerów, zobacz [usługi kontenera platformy Azure](http://aka.ms/AzureContainerService)

Aby opracować aplikacji .NET Core z programem Visual Studio, zobacz [Docker Tools for Visual Studio](http://aka.ms/DockerToolsForVS)

