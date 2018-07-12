---
title: Tworzenie aplikacji internetowej platformy Docker przy użyciu języka Python i bazy danych PostgreSQL na platformie Azure | Microsoft Docs
description: Dowiedz się, jak uruchomić aplikację platformy Docker napisaną w języku Python na platformie Azure z użyciem połączenia z bazą danych PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 2728c354a84c4b13b0ad8509d038837733251975
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38306898"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Tworzenie aplikacji internetowej platformy Docker przy użyciu języka Python i bazy danych PostgreSQL na platformie Azure

Usługa Web App for Containers oferuje wysoce skalowalną i samonaprawialną usługę hostowaną w Internecie. W tym samouczku przedstawiono, jak utworzyć podstawową aplikację internetową platformy Docker przy użyciu języka Python na platformie Azure. Ta aplikacja zostanie połączona z bazą danych PostgreSQL. Po zakończeniu aplikacja platformy Python Flask będzie działała w kontenerze Docker w usłudze [App Service w systemie Linux](app-service-linux-intro.md).

![Aplikacja platformy Docker Python Flask w usłudze App Service w systemie Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych PostgreSQL na platformie Azure
> * Łączenie aplikacji w języku Python z bazą danych PostgreSQL
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Zarządzanie aplikacją w witrynie Azure Portal

Kroki opisane w tym artykule można wykonać w systemie macOS. Instrukcje dotyczące systemów Linux i Windows są takie same w większości przypadków, ale występujące różnice nie są szczegółowo opisane w tym samouczku.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
1. [Zainstaluj język Python](https://www.python.org/downloads/)
1. [Zainstaluj i uruchom serwer PostgreSQL](https://www.postgresql.org/download/)
1. [Zainstaluj platformę Docker Community Edition](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testowanie lokalnej instalacji serwera PostgreSQL i tworzenie bazy danych

Otwórz okno terminalu i uruchom polecenie `psql`, aby nawiązać połączenie z lokalnym serwerem PostgreSQL.

```bash
sudo -u postgres psql
```

Pomyślne nawiązanie połączenia oznacza, że baza danych PostgreSQL działa. W przeciwnym razie upewnij się, że lokalna baza danych PostgresQL została uruchomiona, wykonując kroki opisane w temacie [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Zasoby do pobrania — dystrybucja podstawowa serwera PostgreSQL).

Utwórz bazę danych o nazwie *eventregistration* i skonfiguruj oddzielnego użytkownika bazy danych o nazwie *manager* z hasłem *supersecretpass*.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Tworzenie lokalnej aplikacji platformy Python Flask

Ten krok umożliwia skonfigurowanie lokalnego projektu platformy Python Flask.

### <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Otwórz okno terminalu i za pomocą polecenia `CD` przejdź do katalogu roboczego.

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium i przejścia do wydania *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

To przykładowe repozytorium zawiera aplikację [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Uruchamianie aplikacji

> [!NOTE] 
> W dalszej części tego samouczka ten proces zostanie uproszczony przez utworzenie kontenera Docker, którego można używać z produkcyjną bazą danych.

Zainstaluj wymagane pakiety i uruchom aplikację.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Po całkowitym załadowaniu aplikacji zostanie wyświetlony komunikat podobny do następującego:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

W przeglądarce przejdź do adresu `http://localhost:5000`. Kliknij pozycję **Zarejestruj!** i utwórz użytkownika testowego.

![Działająca lokalnie aplikacja platformy Python Flask](./media/tutorial-docker-python-postgresql-app/local-app.png)

Przykładowa aplikacja platformy Flask przechowuje dane użytkowników w bazie danych. Po pomyślnym zarejestrowaniu użytkownika aplikacja będzie zapisywała dane w lokalnej bazie danych PostgreSQL.

Aby w dowolnym momencie zatrzymać serwer Flask, naciśnij klawisze Ctrl+C w terminalu. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Tworzenie produkcyjnej bazy danych PostgreSQL

W tym kroku utworzysz bazę danych PostgreSQL na platformie Azure. Aplikacja wdrożona na platformie Azure używa tej bazy danych w chmurze.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Za pomocą polecenia [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) utwórz serwer PostgreSQL.

W poniższym poleceniu zastąp symbol zastępczy *\<postgresql_name>* unikatową nazwą serwera, a symbol zastępczy *\<admin_username>* nazwą użytkownika. Ta nazwa serwera jest używana jako część punktu końcowego bazy danych PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), więc nazwa musi być unikatowa na wszystkich serwerach platformy Azure. Nazwa użytkownika to użyta na początku nazwa konta użytkownika administratora bazy danych. Zostanie wyświetlony monit o wybranie hasła dla tego użytkownika.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

Po utworzeniu serwera usługi Azure Database for PostgreSQL w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Tworzenie reguły zapory dla serwera usługi Azure Database for PostgreSQL

Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby zezwolić na dostęp do bazy danych ze wszystkich adresów IP. Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

W interfejsie wiersza polecenia platformy Azure zostanie wyświetlone potwierdzenie utworzenia reguły zapory podobne do następującego:

```json
{
  "endIpAddress": "0.0.0.0",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAzureIPs",
  "name": "AllowAzureIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

> [!TIP] 
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

## <a name="connect-your-python-flask-application-to-the-database"></a>Łączenie aplikacji platformy Python Flask z bazą danych

W tym kroku połączysz przykładową aplikację platformy Python Flask z utworzonym serwerem usługi Azure Database for PostgreSQL.

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Tworzenie pustej bazy danych i konfigurowanie nowego użytkownika aplikacji bazy danych

Utwórz użytkownika bazy danych z dostępem do tylko jednej bazy danych. Użycie tych poświadczeń uniemożliwi aplikacji uzyskanie pełnego dostępu do serwera.

Nawiąż połączenie z bazą danych (zostanie wyświetlony monit o hasło administratora).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Utwórz bazę danych i użytkownika, korzystając z interfejsu wiersza polecenia programu PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Lokalne testowanie aplikacji względem bazy danych Azure PostgreSQL

Po powrocie do folderu *app* w sklonowanym repozytorium GitHub możesz uruchomić aplikację platformy Python Flask, aktualizując zmienne środowiskowe bazy danych.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Po całkowitym załadowaniu aplikacji zostanie wyświetlony komunikat podobny do następującego:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

W przeglądarce przejdź do adresu http://localhost:5000. Kliknij pozycję **Zarejestruj!** i utwórz rejestrację testową. Dane będą teraz zapisywane w bazie danych na platformie Azure.

![Działająca lokalnie aplikacja platformy Python Flask](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Uruchamianie aplikacji z kontenera Docker

Skompiluj obraz kontenera Docker.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Na platformie Docker zostanie wyświetlone potwierdzenie pomyślnego utworzenia kontenera.

```bash
Successfully built 7548f983a36b
```

W katalogu głównym repozytorium dodaj plik zmiennych środowiskowych o nazwie _db.env_, a następnie dodaj do niego poniższe zmienne środowiskowe bazy danych. Aplikacja nawiązuje połączenie z produkcyjną bazą danych usługi Azure Database for PostgreSQL.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Uruchom aplikację z poziomu kontenera Docker. Następujące polecenie określa plik zmiennych środowiskowych i mapuje domyślny port platformy Flask 5000 na port lokalny 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Wynik jest podobny do przedstawionego wcześniej. Nie jest już jednak konieczne przeprowadzanie początkowej migracji bazy danych, więc została ona pominięta.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Baza danych zawiera już utworzoną wcześniej rejestrację.

![Działająca lokalnie aplikacja platformy Python Flask oparta na kontenerze Docker](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Przekazywanie kontenera Docker do rejestru kontenerów

W tym kroku kontener Docker zostanie przekazany do rejestru kontenerów. W tym celu zostanie użyta usługa Azure Container Registry, ale możesz również użyć innych popularnych usług, na przykład Docker Hub.

### <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry

W następującym poleceniu umożliwiającym utworzenie rejestru kontenerów zastąp ciąg *\<registry_name>* unikatową, wybraną nazwą rejestru kontenerów platformy Azure.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Dane wyjściowe

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Pobieranie poświadczeń rejestru na potrzeby wypychania i ściągania obrazów platformy Docker

Aby wyświetlić poświadczenia rejestru, włącz najpierw tryb administratora.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Zobaczysz dwa hasła. Zanotuj nazwę użytkownika i pierwsze hasło.

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Przekazywanie kontenera Docker do usługi Azure Container Registry

Zaloguj się do rejestru. Po wyświetleniu monitu podaj pobrane wcześniej hasło.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Wypchnij obraz platformy Docker do rejestru.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Wdrażanie aplikacji platformy Docker Python Flask na platformie Azure

W tym kroku wdrożysz swoją aplikację platformy Python Flask opartą na kontenerze Docker w usłudze Azure App Service.

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

W planie *myAppServicePlan* usługi App Service utwórz aplikację internetową przy użyciu polecenia [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create).

Aplikacja internetowa zapewnia miejsce hostowania do wdrożenia kodu oraz udostępnia adres URL w celu wyświetlania wdrożonej aplikacji. Użyj polecenia, aby utworzyć aplikację internetową.

W poniższym poleceniu zastąp symbol zastępczy *\<app_name>* unikatową nazwą aplikacji. Ta nazwa jest częścią domyślnego adresu URL aplikacji internetowej, dlatego musi być unikatowa wśród wszystkich aplikacji w usłudze Azure App Service.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>Konfigurowanie zmiennych środowiskowych bazy danych

Wcześniej w tym samouczku zdefiniowano zmienne środowiskowe na potrzeby nawiązywania połączeń z bazą danych PostgreSQL.

W usłudze App Service zmienne środowiskowe są ustawiane jako _ustawienia aplikacji_ za pomocą polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

W poniższym przykładzie określono szczegóły połączenia z bazą danych jako ustawienia aplikacji. Jest tu również używana zmienna *PORT* umożliwiająca zmapowanie portu 5000 kontenera Docker, aby odbierał ruch HTTP na porcie 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Konfigurowanie wdrażania kontenera Docker

Usługa AppService może automatycznie pobierać i uruchamiać kontener Docker.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Po zaktualizowaniu kontenera Docker lub zmianie ustawień należy uruchomić ponownie aplikację. Ponowne uruchomienie gwarantuje, że wszystkie ustawienia zostaną zastosowane, a z rejestru zostanie ściągnięty najnowszy kontener.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure 

Przejdź do wdrożonej aplikacji internetowej w przeglądarce internetowej. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> Ładowanie aplikacji internetowej trwa dłużej, ponieważ po zmianie konfiguracji kontenera musi on zostać pobrany i uruchomiony.

Widać wcześniej zarejestrowanych gości, którzy zostali zapisani w produkcyjnej bazie danych platformy Azure w poprzednim kroku.

![Działająca lokalnie aplikacja platformy Python Flask oparta na kontenerze Docker](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Gratulacje!** Twoja aplikacja platformy Python Flask oparta na kontenerze Docker działa w usłudze Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Aktualizowanie modelu danych i ponowne wdrażanie

W tym kroku do każdej rejestracji zdarzeń dodasz pewną liczbę uczestników, aktualizując model gościa.

Wyewidencjonuj wydanie *0.2-migration* przy użyciu następującego polecenia git:

```bash
git checkout tags/0.2-migration
```

To wydanie wprowadziło już niezbędne zmiany w widokach, kontrolerach i modelu. Obejmuje ono również migrację bazy danych uruchamianą za pomocą polecenia *alembic* (`flask db migrate`). Za pomocą następującego polecenia git można wyświetlić wszystkie wprowadzone zmiany:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Lokalne testowanie zmian

Uruchom następujące polecenia, aby lokalnie przetestować zmiany przez uruchomienie serwera Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

W przeglądarce przejdź do adresu http://localhost:5000, aby przejrzeć zmiany. Utwórz rejestrację testową.

![Działająca lokalnie aplikacja platformy Python Flask oparta na kontenerze Docker](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

Skompiluj nowy obraz kontenera Docker, wypchnij go do rejestru kontenerów i uruchom ponownie aplikację.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Przejdź do aplikacji internetowej platformy Azure i wypróbuj ponownie nowe funkcje. Utwórz inną rejestrację zdarzeń.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplikacja platformy Docker Python Flask w usłudze Azure App Service](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Zarządzanie aplikacją internetową platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby wyświetlić utworzoną aplikację internetową.

W lewym menu kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji internetowej platformy Azure.

![Nawigacja w portalu do aplikacji internetowej platformy Azure](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Domyślnie portal zawiera stronę **Omówienie** aplikacji internetowej. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure Web Apps (Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure)](../app-service-web-tutorial-custom-domain.md)
