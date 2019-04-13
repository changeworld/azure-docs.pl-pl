---
title: Tworzenie aplikacji wielokontenerowej przy użyciu funkcji Web App for Containers — Azure App Service
description: Dowiedz się, jak używać wielu kontenerów na platformie Azure, korzystając z plików konfiguracji narzędzi Docker Compose i Kubernetes, z aplikacją WordPress i MySQL.
keywords: azure app service, aplikacja internetowa, linux, docker, compose, wielokontenerowe, wielokontenerowa, web app for containers, wiele kontenerów, kontener, kubernetes, wordpress, azure db dla mysql, produkcyjna baza danych z kontenerami
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: cd7edb576264ac8bb8a076bbb4b2970579056f13
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547635"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Samouczek: Tworzenie aplikacji wielokontenerowej (w wersji zapoznawczej) przy użyciu funkcji Web App for Containers

Funkcja [Web App for Containers](app-service-linux-intro.md) oferuje elastyczny sposób korzystania z obrazów platformy Docker. Z tego samouczka dowiesz się, jak utworzyć aplikację wielokontenerową przy użyciu rozwiązań WordPress i MySQL. Użyjesz tego samouczka w usłudze Cloud Shell, ale możesz również uruchomić te polecenia lokalnie za pomocą narzędzia wiersza polecenia [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (w wersji 2.0.32 lub nowszej).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konwertowanie konfiguracji narzędzia Docker Compose do użycia z funkcją Web App for Containers
> * Konwertowanie konfiguracji narzędzia Kubernetes do użycia z funkcją Web App for Containers
> * Wdrażanie aplikacji wielokontenerowej na platformie Azure
> * Dodawanie ustawień aplikacji
> * Używanie magazynu trwałego na potrzeby kontenerów
> * Nawiązywanie połączenia z usługą Azure Database for MySQL
> * Rozwiązywanie problemów

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka wymagane jest doświadczenie z narzędziem [Docker Compose](https://docs.docker.com/compose/) lub [Kubernetes](https://kubernetes.io/).

## <a name="download-the-sample"></a>Pobierz przykład

Na potrzeby tego samouczka będziesz używać pliku Compose platformy [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), modyfikując go w celu uwzględnienia usługi Azure Database for MySQL, magazynu trwałego i usługi Redis. Plik konfiguracji można znaleźć na stronie z [przykładami dla platformy Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Opcje konfiguracji obsługiwane w temacie [opcje narzędzia Docker Compose](configure-custom-container.md#docker-compose-options).

W usłudze Cloud Shell utwórz katalog samouczka, a następnie przejdź do niego.

```bash
mkdir tutorial

cd tutorial
```

Uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji do katalogu samouczka. Następnie zmień katalog na `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

W usłudze Cloud Shell utwórz grupę zasobów za pomocą polecenia [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *Południowo-środkowe stany USA*. Aby wyświetlić wszystkie obsługiwane lokalizacje dla usługi App Service w systemie Linux w warstwie **Standardowa**, uruchom polecenie [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie.

Po zakończeniu działania polecenia zostaną wyświetlone dane wyjściowe JSON z właściwościami grupy zasobów.

## <a name="create-an-azure-app-service-plan"></a>Tworzenie planu usługi Azure App Service

W usłudze Cloud Shell utwórz plan usługi App Service w grupie zasobów za pomocą polecenia [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

Poniższy przykład tworzy plan usługi App Service o nazwie `myAppServicePlan` przy użyciu warstwy cenowej **Standardowa** (`--sku S1`) w kontenerze systemu Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Po utworzeniu planu usługi App Service usługa Cloud Shell wyświetli informacje podobne do następujących:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Narzędzie Docker Compose z kontenerami WordPress i MySQL

## <a name="create-a-docker-compose-app"></a>Tworzenie aplikacji narzędzia Docker Compose

W usłudze Cloud Shell utwórz wielokontenerową [aplikację internetową](app-service-linux-intro.md) w ramach planu usługi App Service `myAppServicePlan`, używając polecenia [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Nie zapomnij zastąpić  _\<Nazwa aplikacji >_ unikatową nazwą aplikacji.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Po utworzeniu aplikacji internetowej w usłudze Cloud Shell zostaną wyświetlone dane wyjściowe podobne do następujących:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji pod adresem `http://<app-name>.azurewebsites.net`. Ładowanie aplikacji może potrwać kilka minut. Jeśli wystąpi błąd, zaczekaj jeszcze kilka minut, a następnie odśwież przeglądarkę. Jeśli występują problemy i chcesz je rozwiązać, sprawdź [dzienniki kontenera](#find-docker-container-logs).

![Przykładowa aplikacja wielokontenerowa funkcji Web App for Containers][1]

**Gratulacje**, udało Ci się utworzyć aplikację wielokontenerową funkcji Web App for Containers. Następnie skonfigurujesz aplikację do użycia usługi Azure Database for MySQL. Na razie nie instaluj rozwiązania WordPress.

## <a name="connect-to-production-database"></a>Nawiązywanie połączenia z produkcyjną bazą danych

Używanie kontenerów baz danych w środowisku produkcyjnym jest niezalecane. Kontenery lokalne nie są skalowalne. Zamiast tego użyjesz usługi Azure Database for MySQL, która jest skalowalna.

### <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL

Utwórz serwer usługi Azure Database for MySQL, używając polecenia [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create).

W poniższym poleceniu zastąp nazwę swojego serwera MySQL tam, gdzie zobaczysz  _&lt;nazwę serwera mysql >_ symbolu zastępczego (prawidłowe znaki to `a-z`, `0-9`, i `-`). Ta nazwa jest częścią nazwy hosta serwera MySQL (`<mysql-server-name>.database.windows.net`) i musi być unikatowa w skali globalnej.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

Tworzenie serwera może potrwać kilka minut. Po utworzeniu serwera MySQL w usłudze Cloud Shell zostaną wyświetlone informacje podobne do następujących:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql-server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>",
  "location": "southcentralus",
  "name": "<mysql-server-name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Konfigurowanie zapory serwera

Przy użyciu polecenia [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) utwórz regułę zapory dla serwera MySQL, aby zezwolić na połączenia klientów. Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Tworzenie bazy danych WordPress

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

Po utworzeniu bazy danych w usłudze Cloud Shell zostaną wyświetlone informacje podobne do następujących:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>Konfigurowanie zmiennych bazy danych w rozwiązaniu WordPress

Aby połączyć aplikację WordPress z nowym serwerem MySQL, należy skonfigurować kilka zmiennych środowiskowych właściwych dla rozwiązania WordPress, w tym ścieżkę urzędu certyfikacji SSL, definiowaną przez zmienną `MYSQL_SSL_CA`. Poniższy [obraz niestandardowy](https://docs.microsoft.com/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations) zawiera urząd [Baltimore CyberTrust Root](https://www.digicert.com/digicert-root-certificates.htm) firmy [DigiCert](https://www.digicert.com/).

Aby wprowadzić te zmiany, uruchom polecenie [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell. Ustawienia aplikacji są rozdzielane spacjami i rozróżniana jest w nich wielkość liter.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Po utworzeniu ustawień aplikacji w usłudze Cloud Shell zostaną wyświetlone informacje podobne do następujących:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql-server-name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
```

Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [skonfigurować zmienne środowiskowe](configure-custom-container.md#configure-environment-variables).

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Używanie obrazu niestandardowego na potrzeby korzystania z protokołu SSL w usłudze MySQL i innych konfiguracji

Domyślnie w usłudze Azure Database for MySQL jest używany protokół SSL. Rozwiązanie WordPress wymaga dodatkowej konfiguracji na potrzeby korzystania z protokołu SSL z usługą MySQL. „Oficjalny obraz” rozwiązania WordPress nie zawiera tej dodatkowej konfiguracji, ale dla Twojej wygody został przygotowany [obraz niestandardowy](https://hub.docker.com/r/microsoft/multicontainerwordpress/builds/). W praktyce możesz dodać wymagane zmiany do własnego obrazu.

Ten obraz niestandardowy został utworzony na podstawie „oficjalnego obrazu” rozwiązania [WordPress, dostępnego w witrynie Docker Hub](https://hub.docker.com/_/wordpress/). W obrazie niestandardowym na potrzeby usługi Azure Database for MySQL wprowadzono następujące zmiany:

* [Dodano plik certyfikatu SLL Baltimore Cyber Trust Root do usługi MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [Użyto ustawienia aplikacji dotyczącego certyfikatu SSL urzędu certyfikacji dla usługi MySQL w pliku wp-config.php rozwiązania WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [Dodano definicję zmiennej MYSQL_CLIENT_FLAGS dla rozwiązania WordPress, wymaganą przez protokół SSL w usłudze MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Wprowadzono następujące zmiany na potrzeby usługi Redis (potrzebne w kolejnej sekcji):

* [Dodano rozszerzenie PHP dla usługi Redis w wersji 4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Dodano operację unzip na potrzeby wyodrębniania plików.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Dodano wtyczkę Redis Object Cache 1.3.8 dla rozwiązania WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Użyto ustawienia aplikacji dotyczącego nazwy hosta usługi Redis w pliku wp-config.php rozwiązania WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Aby użyć obrazu niestandardowego, należy zaktualizować plik docker-compose-wordpress.yml. W usłudze Cloud Shell wpisz `nano docker-compose-wordpress.yml`, aby otworzyć edytor tekstów nano. Zmień wartość `image: wordpress` na `image: microsoft/multicontainerwordpress`. Kontener bazy danych nie będzie już potrzebny. Usuń sekcje `db`, `environment`, `depends_on` i `volumes` z pliku konfiguracji. Zawartość pliku powinna wyglądać podobnie do następującego kodu:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Zapisz zmiany i zamknij program nano. Użyj polecenia `^O` w celu zapisania i polecenia `^X` w celu zamknięcia programu.

### <a name="update-app-with-new-configuration"></a>Aktualizowanie aplikacji przy użyciu nowej konfiguracji

W usłudze Cloud Shell zmień konfigurację wielokontenerowej [aplikacji internetowej](app-service-linux-intro.md), używając polecenia [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Nie zapomnij zastąpić  _\<Nazwa aplikacji >_ o nazwie wcześniej utworzonej aplikacji sieci web.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Po zmianie konfiguracji aplikacji internetowej usługa Cloud Shell wyświetli informacje podobne do następujących:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji pod adresem `http://<app-name>.azurewebsites.net`. Aplikacja korzysta teraz z usługi Azure Database for MySQL.

![Przykładowa aplikacja wielokontenerowa funkcji Web App for Containers][1]

## <a name="add-persistent-storage"></a>Dodawanie magazynu trwałego

Twoja aplikacja wielokontenerowa działa teraz w funkcji Web App for Containers. Jeśli jednak teraz zainstalujesz rozwiązanie WordPress, a później ponownie uruchomisz aplikację, nie znajdziesz instalacji rozwiązania WordPress. Dzieje się tak, ponieważ konfiguracja narzędzia Docker Compose aktualnie wskazuje lokalizację magazynu wewnątrz kontenera. Pliki zainstalowane w kontenerze są zachowywane tylko do momentu ponownego uruchomienia aplikacji. W tej sekcji będziesz [Dodaj pojemności magazynu trwałego](configure-custom-container.md#use-persistent-shared-storage) do kontenera systemu WordPress.

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Aby używać magazynu trwałego, należy włączyć to ustawienie w usłudze App Service. Aby wprowadzić tę zmianę, uruchom polecenie [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell. Ustawienia aplikacji są rozdzielane spacjami i rozróżniana jest w nich wielkość liter.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Po utworzeniu ustawień aplikacji w usłudze Cloud Shell zostaną wyświetlone informacje podobne do następujących:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="modify-configuration-file"></a>Modyfikowanie pliku konfiguracji

W usłudze Cloud Shell wpisz `nano docker-compose-wordpress.yml`, aby otworzyć edytor tekstów nano.

Opcja `volumes` mapuje system plików na katalog w kontenerze. `${WEBAPP_STORAGE_HOME}` to zmienna środowiskowa w usłudze App Service mapowana na magazyn trwały aplikacji. Użyjesz tej zmiennej środowiskowej w opcji „volumes”, aby pliki rozwiązania WordPress były instalowane w magazynie trwałym, a nie w kontenerze. Wprowadź poniższe zmiany do pliku:

W sekcji `wordpress` dodaj opcję `volumes` zgodnie z poniższym kodem:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Aktualizowanie aplikacji przy użyciu nowej konfiguracji

W usłudze Cloud Shell zmień konfigurację wielokontenerowej [aplikacji internetowej](app-service-linux-intro.md), używając polecenia [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Nie zapomnij zastąpić  _\<Nazwa aplikacji >_ unikatową nazwą aplikacji.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Po uruchomieniu polecenia zostaną wyświetlone dane wyjściowe podobne do poniższych:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji pod adresem `http://<app-name>.azurewebsites.net`.

Kontener WordPress korzysta teraz z usługi Azure Database for MySQL i magazynu trwałego.

## <a name="add-redis-container"></a>Dodawanie kontenera Redis

 „Oficjalny obraz” rozwiązania WordPress nie zawiera zależności wymaganych na potrzeby usługi Redis. Ten [obraz niestandardowy](https://github.com/Azure-Samples/multicontainerwordpress) został przygotowany z uwzględnieniem tych zależności i dodatkowej konfiguracji wymaganej do korzystania z usługi Redis z rozwiązaniem WordPress. W praktyce możesz dodać wymagane zmiany do własnego obrazu.

Ten obraz niestandardowy został utworzony na podstawie „oficjalnego obrazu” rozwiązania [WordPress, dostępnego w witrynie Docker Hub](https://hub.docker.com/_/wordpress/). W obrazie niestandardowym na potrzeby usługi Redis wprowadzono następujące zmiany:

* [Dodano rozszerzenie PHP dla usługi Redis w wersji 4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Dodano operację unzip na potrzeby wyodrębniania plików.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Dodano wtyczkę Redis Object Cache 1.3.8 dla rozwiązania WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Użyto ustawienia aplikacji dotyczącego nazwy hosta usługi Redis w pliku wp-config.php rozwiązania WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Dodaj kontener Redis na końcu pliku konfiguracji, zgodnie z następującym przykładem:

[!code-yml[Main](../../../azure-app-service-multi-container/compose-wordpress.yml)]

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Aby używać usługi Redis, należy włączyć ustawienie `WP_REDIS_HOST` w usłudze App Service. Jest to *ustawienie wymagane* w celu umożliwienia komunikacji rozwiązania WordPress z hostem usługi Redis. Aby wprowadzić tę zmianę, uruchom polecenie [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell. Ustawienia aplikacji są rozdzielane spacjami i rozróżniana jest w nich wielkość liter.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WP_REDIS_HOST="redis"
```

Po utworzeniu ustawień aplikacji w usłudze Cloud Shell zostaną wyświetlone informacje podobne do następujących:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>Aktualizowanie aplikacji przy użyciu nowej konfiguracji

W usłudze Cloud Shell zmień konfigurację wielokontenerowej [aplikacji internetowej](app-service-linux-intro.md), używając polecenia [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Nie zapomnij zastąpić  _\<Nazwa aplikacji >_ unikatową nazwą aplikacji.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Po uruchomieniu polecenia zostaną wyświetlone dane wyjściowe podobne do poniższych:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji pod adresem `http://<app-name>.azurewebsites.net`.

Wykonaj odpowiednie czynności i zainstaluj rozwiązanie WordPress.

### <a name="connect-wordpress-to-redis"></a>Łączenie rozwiązania WordPress z usługą Redis

Zaloguj się do panelu administratora rozwiązania WordPress. W obszarze nawigacji po lewej stronie wybierz pozycję **Plugins** (Wtyczki), a następnie **Installed plugins** (Zainstalowane wtyczki).

![Wybieranie wtyczek rozwiązania WordPress][2]

Pokaż wszystkie wtyczki

Na stronie Plugins (Wtyczki) znajdź wtyczkę **Redis Object Cache** i kliknij przycisk **Activate** (Aktywuj).

![Aktywowanie usługi Redis][3]

Kliknij pozycję **Settings** (Ustawienia).

![Klikanie pozycji Settings (Ustawienia)][4]

Kliknij przycisk **Enable Object Cache** (Włącz usługę Object Cache).

![Klikanie przycisku „Enable Object Cache” (Włącz usługę Object Cache)][5]

Rozwiązanie WordPress połączy się z serwerem usługi Redis. **Stan** połączenia jest widoczny na tej samej stronie.

![Rozwiązanie WordPress połączy się z serwerem usługi Redis. **Stan** połączenia jest widoczny na tej samej stronie.][6]

**Gratulacje**, udało Ci się połączyć rozwiązanie WordPress z usługą Redis. Aplikacja gotowa do produkcji korzysta teraz z **usługi Azure Database for MySQL, magazynu trwałego i usługi Redis**. Możesz teraz skalować swój plan usługi App Service na wiele wystąpień.

## <a name="use-a-kubernetes-configuration-optional"></a>Używanie konfiguracji narzędzia Kubernetes (opcjonalnie)

W tej sekcji dowiesz się, jak wdrożyć wiele kontenerów przy użyciu konfiguracji narzędzia Kubernetes. Upewnij się, że zostały wykonane wcześniejsze kroki w celu utworzenia [grupy zasobów](#create-a-resource-group) i [planu usługi App Service](#create-an-azure-app-service-plan). Ponieważ większość czynności jest podobna do tych opisanych w sekcji dotyczącej narzędzia Compose, plik konfiguracji został połączony.

### <a name="kubernetes-configuration-file"></a>Plik konfiguracji platformy Kubernetes

W tej części samouczka użyjesz pliku *kubernetes-wordpress.yml*. Możesz zapoznać się z nim tutaj:

[!code-yml[Main](../../../azure-app-service-multi-container/kubernetes-wordpress.yml)]

Opcje konfiguracji obsługiwane w temacie [opcje konfiguracji rozwiązania Kubernetes](configure-custom-container.md#kubernetes-configuration-options)

### <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL

Utwórz serwer w usłudze Azure Database for MySQL przy użyciu polecenia [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create).

W poniższym poleceniu zastąp nazwę swojego serwera MySQL tam, gdzie zobaczysz  _&lt;nazwę serwera mysql >_ symbolu zastępczego (prawidłowe znaki to `a-z`, `0-9`, i `-`). Ta nazwa jest częścią nazwy hosta serwera MySQL (`<mysql-server-name>.database.windows.net`) i musi być unikatowa w skali globalnej.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

Po utworzeniu serwera MySQL w usłudze Cloud Shell zostaną wyświetlone informacje podobne do następujących:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql-server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>",
  "location": "southcentralus",
  "name": "<mysql-server-name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Konfigurowanie zapory serwera

Przy użyciu polecenia [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) utwórz regułę zapory dla serwera MySQL, aby zezwolić na połączenia klientów. Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Tworzenie bazy danych WordPress

Jeśli nie zostało to jeszcze zrobione, utwórz [serwer usługi Azure Database for MySQL](#create-an-azure-database-for-mysql-server).

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

Po utworzeniu bazy danych w usłudze Cloud Shell zostaną wyświetlone informacje podobne do następujących:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="create-a-multi-container-app-kubernetes"></a>Tworzenie aplikacji wielokontenerowej (Kubernetes)

W usłudze Cloud Shell utwórz wielokontenerową [aplikację internetową](app-service-linux-intro.md) w ramach grupy zasobów `myResourceGroup` i planu usługi App Service `myAppServicePlan`, używając polecenia [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Nie zapomnij zastąpić  _\<Nazwa aplikacji >_ unikatową nazwą aplikacji.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type kube --multicontainer-config-file kubernetes-wordpress.yml
```

Po utworzeniu aplikacji internetowej w usłudze Cloud Shell zostaną wyświetlone dane wyjściowe podobne do następujących:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-database-variables-in-wordpress"></a>Konfigurowanie zmiennych bazy danych w rozwiązaniu WordPress

Aby połączyć aplikację WordPress z nowym serwerem MySQL, należy skonfigurować kilka zmiennych środowiskowych właściwych dla rozwiązania WordPress. Aby wprowadzić tę zmianę, uruchom polecenie [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell. Ustawienia aplikacji są rozdzielane spacjami i rozróżniana jest w nich wielkość liter.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Po utworzeniu ustawień aplikacji w usłudze Cloud Shell zostaną wyświetlone informacje podobne do następujących:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql-server-name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  }
]
```

### <a name="add-persistent-storage"></a>Dodawanie magazynu trwałego

Twoja aplikacja wielokontenerowa działa teraz w funkcji Web App for Containers. Dane zostaną usunięte przy ponownym uruchomieniu, ponieważ pliki nie znajdują się w magazynie trwałym. W tej sekcji będziesz [Dodaj pojemności magazynu trwałego](configure-custom-container.md#use-persistent-shared-storage) do kontenera systemu WordPress.

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Aby używać magazynu trwałego, należy włączyć to ustawienie w usłudze App Service. Aby wprowadzić tę zmianę, uruchom polecenie [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell. Ustawienia aplikacji są rozdzielane spacjami i rozróżniana jest w nich wielkość liter.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Po utworzeniu ustawień aplikacji w usłudze Cloud Shell zostaną wyświetlone informacje podobne do następujących:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji pod adresem `http://<app-name>.azurewebsites.net`.

Aplikacja z wieloma kontenerami działa teraz w funkcji Web App for Containers.

![Przykładowa aplikacja wielokontenerowa funkcji Web App for Containers][1]

**Gratulacje**, udało Ci się utworzyć aplikację wielokontenerową funkcji Web App for Containers.

Aby użyć usługi Redis, wykonaj czynności opisane w sekcji [Łączenie rozwiązania WordPress z usługą Redis](#connect-wordpress-to-redis).

## <a name="find-docker-container-logs"></a>Znajdowanie dzienników kontenerów platformy Docker

Jeśli podczas korzystania z wielu kontenerów wystąpią problemy, możesz wyświetlić dzienniki kontenerów, przechodząc do lokalizacji `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Zostaną wyświetlone dane wyjściowe podobne do następujących:

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app-name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

Zobaczysz jeden dziennik dla każdego kontenera oraz dodatkowy dziennik procesu nadrzędnego. Skopiuj odpowiednią wartość `href` do przeglądarki, aby wyświetlić dziennik.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konwertowanie konfiguracji narzędzia Docker Compose do użycia z funkcją Web App for Containers
> * Konwertowanie konfiguracji narzędzia Kubernetes do użycia z funkcją Web App for Containers
> * Wdrażanie aplikacji wielokontenerowej na platformie Azure
> * Dodawanie ustawień aplikacji
> * Używanie magazynu trwałego na potrzeby kontenerów
> * Nawiązywanie połączenia z usługą Azure Database for MySQL
> * Rozwiązywanie problemów

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: Mapowanie niestandardowej nazwy DNS na aplikację](../app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie niestandardowego kontenera](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
