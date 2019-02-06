---
title: Używanie narzędzia Docker Compose na maszynie Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak używać platformy Docker i Compose maszyn wirtualnych systemu Linux przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: cynthn
ms.openlocfilehash: 257083e1ae0c3c1cb3c5421882ffd0e06e2d1f5c
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752147"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Wprowadzenie do platformy Docker i Compose, aby zdefiniować i uruchomić aplikację obsługującą wiele kontenerów na platformie Azure
Za pomocą [Compose](http://github.com/docker/compose), użyj prostego pliku tekstowego do definiowania aplikacja składająca się z wielu kontenerów platformy Docker. Następnie uruchom aplikację w pojedynczym poleceniu, tak, wszystko, co do wdrożenia środowiska zdefiniowane. Na przykład w tym artykule pokazano, jak szybko skonfigurować bloga WordPress z zapleczem bazy danych MariaDB SQL na maszynie Wirtualnej systemu Ubuntu. Redaguj umożliwia również konfigurowanie bardziej złożonych aplikacji.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Skonfiguruj Maszynę wirtualną systemu Linux jako hosta platformy Docker
Aby utworzyć Maszynę wirtualną systemu Linux i skonfigurować go jako hosta platformy Docker, można użyć różnych procedur platformy Azure i dostępne obrazy lub szablonów usługi Resource Manager w witrynie Azure Marketplace. Na przykład zobacz [korzystania z rozszerzenia maszyny Wirtualnej platformy Docker w celu wdrożenia środowiska](dockerextension.md) szybko utworzyć Maszynę wirtualną Ubuntu przy użyciu rozszerzenia maszyny Wirtualnej Azure Docker, za pomocą [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Korzystając z rozszerzenia maszyny Wirtualnej Docker, maszyna wirtualna jest automatycznie ustawiana jako hosta Docker i Compose jest już zainstalowana.


### <a name="create-docker-host-with-azure-cli"></a>Tworzenie hosta platformy Docker przy użyciu wiersza polecenia platformy Azure
Zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [az login](/cli/azure/reference-index).

Najpierw utwórz grupę zasobów w środowisku platformy Docker przy użyciu [Tworzenie grupy az](/cli/azure/group). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie należy wdrożyć Maszynę wirtualną za pomocą [Utwórz wdrożenie grupy az](/cli/azure/group/deployment) zawierającej rozszerzenie maszyny Wirtualnej Azure Docker z [tego szablonu usługi Azure Resource Manager w witrynie GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Po wyświetleniu monitu podaj własne unikatowe wartości *newStorageAccountName*, *adminUsername*, *adminPassword*, i *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Trwa kilka minut na zakończenie wdrożenia.


## <a name="verify-that-compose-is-installed"></a>Sprawdź, czy jest zainstalowany Compose
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

Aby sprawdzić, czy Compose jest zainstalowany na maszynie Wirtualnej, uruchom następujące polecenie:

```bash
docker-compose --version
```

Zostaną wyświetlone dane wyjściowe podobne do *docker-compose 1.6.2, tworzyć 4d 72027*.

> [!TIP]
> Jeśli użyto innej metody do tworzenia hosta platformy Docker i należy zainstalować narzędzia Compose samodzielnie, zobacz [tworzą dokumentację](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Tworzenie pliku konfiguracji platformy docker-compose.yml
Następnie należy utworzyć `docker-compose.yml` pliku, który jest po prostu tekst pliku konfiguracji, aby zdefiniować kontenerów platformy Docker do uruchamiania na maszynie Wirtualnej. Plik Określa obraz do uruchamiania na każdy kontener (lub może być kompilacji z pliku Dockerfile), potrzebne zmienne środowiskowe i zależności, porty i łącza między kontenerów. Szczegółowe informacje na temat składni plik yml, [tworzą odwołanie do pliku](https://docs.docker.com/compose/compose-file/).

Tworzenie *docker-compose.yml* pliku. Użyj ulubionego edytora tekstu, aby dodać niektóre dane do pliku. Poniższy przykład tworzy plik z monit o podanie `sensible-editor` i wybierz edytor który chcesz użyć:

```bash
sensible-editor docker-compose.yml
```

Wklej poniższy przykład do pliku narzędzia Docker Compose. Ta konfiguracja korzysta z obrazów z [rejestru DockerHub](https://registry.hub.docker.com/_/wordpress/) zainstalował WordPress ("open source" do obsługi blogów i system zarządzania zawartością) i zapleczem połączonej bazy danych MariaDB SQL. Wprowadź własne *MYSQL_ROOT_PASSWORD* w następujący sposób:

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Uruchom kontenery z Compose
W tym samym katalogu co Twoje *docker-compose.yml* pliku, uruchom następujące polecenie (w zależności od środowiska, może być konieczne uruchomienie `docker-compose` przy użyciu `sudo`):

```bash
docker-compose up -d
```

To polecenie uruchamia kontenery platformy Docker określonego w *docker-compose.yml*. Trwa minutę lub dwie do ukończenia tego kroku. Zostaną wyświetlone dane wyjściowe podobne do poniższego przykładu:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Należy użyć **-d** opcja uruchamiania, aby uruchomić w tle stale kontenerów.


Aby zweryfikować, że kontenery są włączone, wpisz `docker-compose ps`. Powinien zostać wyświetlony, mniej więcej tak:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Teraz można połączyć się WordPress bezpośrednio na maszynie Wirtualnej na porcie 80. Otwórz przeglądarkę sieci web, a następnie wprowadź nazwę DNS maszyny wirtualnej (takie jak `http://mypublicdns.eastus.cloudapp.azure.com`). Powinien zostać wyświetlony WordPress ekranu startowego, gdzie może ukończyć instalację i rozpocząć pracę z aplikacją.

![Ekran startowy WordPress][wordpress_start]

## <a name="next-steps"></a>Kolejne kroki
* Przejdź do [Podręcznik użytkownika rozszerzenia maszyny Wirtualnej Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) więcej opcji skonfigurować platformy Docker i Compose w maszynie Wirtualnej platformy Docker. Na przykład jedną z opcji jest umieścić plik yml Compose (przekonwertowane na format JSON) bezpośrednio w konfiguracji rozszerzenia maszyny Wirtualnej Docker.
* Zapoznaj się z [Compose wiersza polecenia](http://docs.docker.com/compose/reference/) i [Podręcznik użytkownika](http://docs.docker.com/compose/) więcej przykładów dotyczących tworzenia i wdrażania aplikacji obsługującej wiele kontenerów.
* Albo użyj szablonu usługi Azure Resource Manager, usługi posiada lub w jednym przyczyniły się z [społeczności](https://azure.microsoft.com/documentation/templates/), aby wdrożyć Maszynę wirtualną platformy Azure z platformami Docker i skonfigurować za pomocą tworzenia aplikacji. Na przykład [wdrożyć blog WordPress przy użyciu rozwiązania Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) szablon używa platformy Docker i Compose, szybko wdrożyć WordPress z zapleczem MySQL na maszynie Wirtualnej systemu Ubuntu.
* Spróbuj integracji narzędzia Docker Compose za pomocą klastra Docker Swarm. Zobacz [przy użyciu narzędzia Compose przy użyciu koordynatora Swarm](https://docs.docker.com/compose/swarm/) do scenariuszy.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
