---
title: Korzystanie z narzędzia Docker Compose
description: Jak zainstalować i używać platformy Docker i compose na maszynach wirtualnych systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 434a3ef8c9bc1738252d59a5dca5bec16d85e45e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970303"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Wprowadzenie do platformy Docker i compose w celu zdefiniowania i uruchomienia aplikacji z wieloma kontenerami na platformie Azure
Za pomocą [compose](https://github.com/docker/compose), można użyć prostego pliku tekstowego do zdefiniowania aplikacji składającej się z wielu kontenerów platformy Docker. Następnie należy rozkręcić aplikację w jednym poleceniu, które wykonuje wszystko, aby wdrożyć zdefiniowane środowisko. Na przykład w tym artykule pokazano, jak szybko skonfigurować blog WordPress z wewnętrznej bazy danych SQL bazy danych MariaDB na maszynie wirtualnej Ubuntu. Można również użyć compose do konfigurowania bardziej złożonych aplikacji.

Ten artykuł został ostatnio przetestowany w dniu 14.02.2019 przy użyciu [usługi Azure Cloud Shell](https://shell.azure.com/bash) i interfejsu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w wersji 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Tworzenie hosta platformy Docker za pomocą interfejsu wiersza polecenia platformy Azure
Zainstaluj najnowszą [platformę Cli platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

Najpierw utwórz grupę zasobów dla środowiska platformy Docker z [utworzeniem grupy AZ](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Utwórz plik o nazwie *cloud-init.txt* i wklej następującą konfigurację. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. 

```yaml
#include https://get.docker.com
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. Poniższy przykład tworzy maszynę wirtualną o nazwie *myDockerVM* i otwiera port 80 do ruchu sieci web.

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

                 

## <a name="install-compose"></a>Instalowanie compose


SSH do nowej maszyny Wirtualnej hosta platformy Docker. Podaj swój własny adres IP.

```bash
ssh azureuser@10.10.111.11
```

Zainstaluj compose na maszynie wirtualnej.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Tworzenie pliku konfiguracyjnego docker-compose.yml
Utwórz `docker-compose.yml` plik konfiguracji, aby zdefiniować kontenery platformy Docker do uruchomienia na maszynie wirtualnej. Plik określa obraz do uruchomienia na każdym kontenerze, niezbędne zmienne środowiskowe i zależności, porty i łącza między kontenerami. Aby uzyskać szczegółowe informacje na temat składni pliku yml, zobacz [Redagowanie odwołania do pliku](https://docs.docker.com/compose/compose-file/).

Utwórz plik *docker-compose.yml.* Użyj ulubionego edytora tekstu, aby dodać niektóre dane do pliku. Poniższy przykład tworzy plik z `sensible-editor` monitem o wybranie edytora, którego chcesz użyć.

```bash
sensible-editor docker-compose.yml
```

Wklej poniższy przykład do pliku docker compose. Ta konfiguracja używa obrazów z [rejestru DockerHub](https://registry.hub.docker.com/_/wordpress/) do zainstalowania WordPress (open source blogów i system zarządzania treścią) i połączone zaplecza bazy danych MARIADB SQL. Wprowadź własną *MYSQL_ROOT_PASSWORD*.

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

## <a name="start-the-containers-with-compose"></a>Uruchom kontenery za pomocą compose
W tym samym katalogu co plik *docker-compose.yml* uruchom następujące polecenie (w zależności `docker-compose` od `sudo`środowiska może być konieczne uruchomienie):

```bash
sudo docker-compose up -d
```

To polecenie uruchamia kontenery platformy Docker określone w *pliku docker-compose.yml*. Wykonanie tego kroku zajmuje minutę lub dwie. Zobaczysz dane wyjściowe podobne do następujących:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Aby sprawdzić, czy kontenery `sudo docker-compose ps`są w górę, wpisz . Powinny zostać wyświetlone informacje podobne do następujących:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Teraz możesz połączyć się z WordPress bezpośrednio na maszynie wirtualnej na porcie 80. Otwórz przeglądarkę internetową i wprowadź nazwę adresu IP maszyny wirtualnej. Powinieneś teraz zobaczyć ekran startowy WordPress, gdzie możesz zakończyć instalację i rozpocząć pracę z aplikacją.

![Ekran startowy WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [odwołaniem do wiersza polecenia Redpose](https://docs.docker.com/compose/reference/) i [podręcznikiem użytkownika,](https://docs.docker.com/compose/) aby uzyskać więcej przykładów tworzenia i wdrażania aplikacji z wieloma kontenerami.
* Użyj szablonu usługi Azure Resource Manager, własnego lub szablonu wniesionego przez [społeczność,](https://azure.microsoft.com/documentation/templates/)aby wdrożyć maszynę wirtualną platformy Azure za pomocą platformy Docker i aplikacji skonfigurowanej za pomocą aplikacji Compose. Na [przykład, Wdrażanie bloga WordPress z szablonem Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) używa platformy Docker i Compose do szybkiego wdrażania WordPress z zapleczem MySQL na maszynie wirtualnej Ubuntu.
* Spróbuj zintegrować docker Compose z klastrem Rój platformy Docker. Zobacz [Korzystanie z komponowania z roju](https://docs.docker.com/compose/swarm/) dla scenariuszy.

