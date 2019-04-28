---
title: Używanie narzędzia Docker Compose na maszynie Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zainstalować i używać platformy Docker i Compose maszyn wirtualnych systemu Linux przy użyciu wiersza polecenia platformy Azure
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
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 03501ea774cf58a4be88ed9155e5cfdfb99f0379
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474088"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Wprowadzenie do platformy Docker i Compose, aby zdefiniować i uruchomić aplikację obsługującą wiele kontenerów na platformie Azure
Za pomocą [Compose](https://github.com/docker/compose), użyj prostego pliku tekstowego do definiowania aplikacja składająca się z wielu kontenerów platformy Docker. Następnie uruchom aplikację w pojedynczym poleceniu, tak, wszystko, co do wdrożenia środowiska zdefiniowane. Na przykład w tym artykule pokazano, jak szybko skonfigurować bloga WordPress z zapleczem bazy danych MariaDB SQL na maszynie Wirtualnej systemu Ubuntu. Redaguj umożliwia również konfigurowanie bardziej złożonych aplikacji.

W tym artykule ostatniego testowania przy użyciu 2/14/2019 [usługi Azure Cloud Shell](https://shell.azure.com/bash) i [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) wersji 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Tworzenie hosta platformy Docker przy użyciu wiersza polecenia platformy Azure
Zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [az login](/cli/azure/reference-index).

Najpierw utwórz grupę zasobów w środowisku platformy Docker przy użyciu [Tworzenie grupy az](/cli/azure/group). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Utwórz plik o nazwie *cloud-init.txt* i Wklej poniższą konfigurację. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. 

```yaml
#include https://get.docker.com
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myDockerVM* i otwiera port 80 na potrzeby ruchu sieciowego.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Utworzenie maszyny wirtualnej, zainstalowanie pakietów i uruchomienie aplikacji potrwa kilka minut. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Podczas tworzenia maszyny wirtualnej zanotuj wartość `publicIpAddress` wyświetlaną w wierszu polecenia platformy Azure. 

                 

## <a name="install-compose"></a>Zainstaluj narzędzia Compose


Protokół SSH z nowej maszyny Wirtualnej z hosta platformy Docker. Podaj własny adres IP.

```bash
ssh azureuser@10.10.111.11
```

Zainstaluj narzędzia Compose na maszynie Wirtualnej.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Tworzenie pliku konfiguracji platformy docker-compose.yml
Utwórz `docker-compose.yml` plik konfiguracji, aby zdefiniować kontenerów platformy Docker do uruchamiania na maszynie Wirtualnej. Plik Określa obraz do uruchamiania na każdy kontener, potrzebne zmienne środowiskowe i zależności, portów i łącza między kontenerów. Szczegółowe informacje na temat składni plik yml, [tworzą odwołanie do pliku](https://docs.docker.com/compose/compose-file/).

Tworzenie *docker-compose.yml* pliku. Użyj ulubionego edytora tekstu, aby dodać niektóre dane do pliku. Poniższy przykład tworzy plik z monit o podanie `sensible-editor` i wybierz edytor który chcesz użyć.

```bash
sensible-editor docker-compose.yml
```

Wklej poniższy przykład do pliku narzędzia Docker Compose. Ta konfiguracja korzysta z obrazów z [rejestru DockerHub](https://registry.hub.docker.com/_/wordpress/) zainstalował WordPress ("open source" do obsługi blogów i system zarządzania zawartością) i zapleczem połączonej bazy danych MariaDB SQL. Wprowadź własne *MYSQL_ROOT_PASSWORD*.

```yml
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
sudo docker-compose up -d
```

To polecenie uruchamia kontenery platformy Docker określonego w *docker-compose.yml*. Trwa minutę lub dwie do ukończenia tego kroku. Zostaną wyświetlone dane wyjściowe podobne do następujących:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Aby zweryfikować, że kontenery są włączone, wpisz `sudo docker-compose ps`. Powinien zostać wyświetlony, mniej więcej tak:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Teraz można połączyć się WordPress bezpośrednio na maszynie Wirtualnej na porcie 80. Otwórz przeglądarkę sieci web, a następnie wprowadź nazwę adresu IP maszyny wirtualnej. Powinien zostać wyświetlony WordPress ekranu startowego, gdzie może ukończyć instalację i rozpocząć pracę z aplikacją.

![Ekran startowy WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z [Compose wiersza polecenia](https://docs.docker.com/compose/reference/) i [Podręcznik użytkownika](https://docs.docker.com/compose/) więcej przykładów dotyczących tworzenia i wdrażania aplikacji obsługującej wiele kontenerów.
* Albo użyj szablonu usługi Azure Resource Manager, usługi posiada lub w jednym przyczyniły się z [społeczności](https://azure.microsoft.com/documentation/templates/), aby wdrożyć Maszynę wirtualną platformy Azure z platformami Docker i skonfigurować za pomocą tworzenia aplikacji. Na przykład [wdrożyć blog WordPress przy użyciu rozwiązania Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) szablon używa platformy Docker i Compose, szybko wdrożyć WordPress z zapleczem MySQL na maszynie Wirtualnej systemu Ubuntu.
* Spróbuj integracji narzędzia Docker Compose za pomocą klastra Docker Swarm. Zobacz [przy użyciu narzędzia Compose przy użyciu koordynatora Swarm](https://docs.docker.com/compose/swarm/) do scenariuszy.

