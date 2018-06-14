---
title: Użyj rozwiązania Docker Compose się na Maszynę wirtualną systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak używać Docker i tworzenia maszyn wirtualnych systemu Linux z wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 387f3095e8eebce3fa6c2f47ffc87995e65bfe2b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29848410"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Rozpoczynanie pracy z Docker i wysyłanych do definiowania i uruchomić aplikację usługi kontenera platformy Azure
Z [Redaguj](http://github.com/docker/compose), zdefiniuj aplikacja składająca się z wielu kontenerów Docker przy użyciu pliku zwykły tekst. Następnie pokrętła się na aplikację za pomocą jednego polecenia, który wykonuje wszystkie informacje niezbędne do wdrożenia środowiska zdefiniowane. Na przykład w tym artykule przedstawiono sposób szybko skonfigurować bloga WordPress z bazy danych MariaDB SQL na maszynie Wirtualnej systemu Ubuntu wewnętrznej bazie danych. Redaguj umożliwia również skonfigurowanie bardziej złożonych aplikacji.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Skonfiguruj Maszynę wirtualną systemu Linux jako Docker host
Aby utworzyć Maszynę wirtualną systemu Linux i skonfigurować jako hosta Docker można użyć różnych procedur Azure i dostępnych obrazów lub szablony Menedżera zasobów w portalu Azure Marketplace. Na przykład, zobacz [wdrażanie środowiska za pomocą rozszerzenia maszyny Wirtualnej platformy Docker](dockerextension.md) do szybkiego tworzenia maszyny Wirtualnej systemu Ubuntu rozszerzenie maszyny Wirtualnej platformy Docker Azure za pomocą [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Gdy używasz rozszerzenia maszyny Wirtualnej platformy Docker maszyny Wirtualnej jest automatycznie skonfigurowany jako Docker host i redagowanie jest już zainstalowana.


### <a name="create-docker-host-with-azure-cli-20"></a>Utwórz hosta Docker 2.0 interfejsu wiersza polecenia platformy Azure
Zainstaluj najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [logowania az](/cli/azure/reference-index#az_login).

Najpierw utwórz grupę zasobów dla danego środowiska Docker [Tworzenie grupy az](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Następnie należy wdrożyć maszynę Wirtualną z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_create) zawierającej rozszerzenie Azure Docker VM z [tego szablonu usługi Azure Resource Manager w witrynie GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Po wyświetleniu monitu podaj unikatowe wartości dla *newStorageAccountName*, *adminUsername*, *adminPassword*, i *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Trwa kilka minut dla wdrożenia, aby zakończyć.


## <a name="verify-that-compose-is-installed"></a>Sprawdź, czy Redaguj jest zainstalowany
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

Aby sprawdzić, czy Redaguj jest zainstalowany na Maszynie wirtualnej, uruchom następujące polecenie:

```bash
docker-compose --version
```

Zobacz dane wyjściowe podobne do *rozwiązania docker compose 1.6.2, kompilacji 4d 72027*.

> [!TIP]
> Jeśli używasz innej metody do tworzenia hostów Docker i trzeba instalować samodzielnie utworzyć, zobacz [tworzą dokumentacji](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Utwórz plik docker-compose.yml konfiguracji
Następnie należy utworzyć `docker-compose.yml` pliku, który jest tylko konfiguracja pliku tekstowego, aby zdefiniować kontenery Docker do uruchamiania na maszynie Wirtualnej. Plik Określa obraz do uruchamiania na każdego kontenera (lub może być kompilacji z plik Dockerfile), zmienne środowiskowe niezbędne i zależności, porty i linki między kontenerów. Aby uzyskać więcej informacji o składni pliku yml, zobacz [tworzą odwołanie do pliku](https://docs.docker.com/compose/compose-file/).

Utwórz *docker-compose.yml* pliku. Aby dodać niektóre dane do pliku, użyj w ulubionym edytorze tekstów. Poniższy przykład tworzy plik z monit o `sensible-editor` wybierz edytor, którego chcesz użyć:

```bash
sensible-editor docker-compose.yml
```

Wklej poniższy przykład w pliku rozwiązania Docker Compose. Ta konfiguracja korzysta z obrazów z [rejestru DockerHub](https://registry.hub.docker.com/_/wordpress/) zainstalować WordPress (typu open source obsługi blogów i system zarządzania zawartością) i połączone wewnętrznej bazy danych MariaDB SQL bazy danych. Wprowadź własne *MYSQL_ROOT_PASSWORD* w następujący sposób:

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

## <a name="start-the-containers-with-compose"></a>Kontenery zaczynać się Redaguj
W tym samym katalogu co Twoje *docker-compose.yml* plików, uruchom następujące polecenie (w zależności od środowiska, może być konieczne uruchomienie `docker-compose` przy użyciu `sudo`):

```bash
docker-compose up -d
```

To polecenie uruchamia kontenery Docker określone w *docker-compose.yml*. Trwa minutę lub dwie do ukończenia tego kroku. Zostaną wyświetlone dane wyjściowe podobne do poniższego przykładu:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Należy użyć **-d** opcję przy uruchamianiu, dzięki czemu kontenery stale uruchomione w tle.


Aby sprawdzić, czy kontenery są uruchomione, wpisz `docker-compose ps`. Powinien zostać wyświetlony wyglądać mniej więcej tak:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Można teraz nawiązać WordPress bezpośrednio na Maszynie wirtualnej na porcie 80. Otwórz przeglądarkę sieci web, a następnie wprowadź nazwę DNS maszyny Wirtualnej (takie jak `http://mypublicdns.eastus.cloudapp.azure.com`). Powinien zostać wyświetlony WordPress ekranu startowego, gdzie może ukończyć instalację i rozpocząć pracę z aplikacją.

![Ekran startowy WordPress][wordpress_start]

## <a name="next-steps"></a>Kolejne kroki
* Przejdź do [Podręcznik użytkownika rozszerzenia maszyny Wirtualnej platformy Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) dla więcej opcji, aby skonfigurować Docker i redagowanie w maszynie Wirtualnej platformy Docker. Na przykład jedną opcję jest umieszczony plik yml Redaguj (przekonwertowane na format JSON) bezpośrednio w konfiguracji rozszerzenia maszyny Wirtualnej platformy Docker.
* Zapoznaj się z [tworzą wiersza polecenia](http://docs.docker.com/compose/reference/) i [Podręcznik użytkownika](http://docs.docker.com/compose/) więcej przykładów dotyczących tworzenia i wdrażania aplikacji usługi kontenera.
* Albo użyj szablonu usługi Azure Resource Manager, Twoje własne lub jeden przyczyniły się z [społeczności](https://azure.microsoft.com/documentation/templates/), aby wdrożyć maszyny Wirtualnej platformy Azure z tworzenia aplikacji i Docker. Na przykład [wdrażanie bloga WordPress z rozwiązaniem Docker z](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) szablon używa Docker i redagowanie można szybko wdrożyć WordPress z wewnętrznej bazy danych MySQL na maszynie Wirtualnej systemu Ubuntu.
* Spróbuj integrowanie rozwiązania Docker Compose z klastrem Docker Swarm. Zobacz [przy użyciu tworzą z Swarm](https://docs.docker.com/compose/swarm/) dla scenariuszy.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
