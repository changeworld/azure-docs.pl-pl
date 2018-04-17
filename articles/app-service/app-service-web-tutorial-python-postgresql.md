---
title: Tworzenie aplikacji internetowej przy użyciu języka Python i bazy danych PostgreSQL na platformie Azure | Microsoft Docs
description: Dowiedz się, jak uruchomić aplikację napisaną w języku Python na platformie Azure z użyciem połączenia z bazą danych PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 9694dcfec7b3297e925bb45bee5e74b69609063d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-build-a-python-and-postgresql-web-app-in-azure"></a>Samouczek: tworzenie aplikacji internetowej przy użyciu języka Python i bazy danych PostgreSQL na platformie Azure

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. W przypadku wdrażania w usłudze App Service w systemie _Linux_ zobacz [Tworzenie aplikacji internetowej platformy Docker przy użyciu języka Python i bazy danych PostgreSQL na platformie Azure](./containers/tutorial-docker-python-postgresql-app.md).
>

Usługa [Azure App Service](app-service-web-overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. W tym samouczku przedstawiono, jak utworzyć podstawową aplikację internetową przy użyciu języka Python na platformie Azure. Ta aplikacja zostanie połączona z bazą danych PostgreSQL. Po zakończeniu aplikacja platformy Python Flask będzie działała w usłudze App Service.

![Aplikacja platformy Python Flask w usłudze App Service w systemie Linux](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych PostgreSQL na platformie Azure
> * Łączenie aplikacji w języku Python z bazą danych MySQL
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Zarządzanie aplikacją w witrynie Azure Portal

Kroki opisane w tym samouczku można wykonać w systemie macOS. Instrukcje dotyczące systemów Linux i Windows są takie same w większości przypadków, ale występujące różnice nie są szczegółowo opisane w tym samouczku.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
1. [Zainstaluj język Python](https://www.python.org/downloads/)
1. [Zainstaluj i uruchom serwer PostgreSQL](https://www.postgresql.org/download/)

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

Uruchom następujące polecenia, aby sklonować repozytorium przykładowe, a następnie cofnij do zatwierdzenia dla aplikacji początkowej (przed poleceniem `modelChange`).

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

To przykładowe repozytorium zawiera aplikację [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Uruchamianie aplikacji

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

![Działająca lokalnie aplikacja platformy Python Flask](./media/app-service-web-tutorial-python-postgresql/local-app.png)

Przykładowa aplikacja platformy Flask przechowuje dane użytkowników w bazie danych. Po pomyślnym zarejestrowaniu użytkownika aplikacja będzie zapisywała dane w lokalnej bazie danych PostgreSQL.

Aby w dowolnym momencie zatrzymać serwer Flask, naciśnij klawisze Ctrl+C w terminalu. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Tworzenie bazy danych PostgreSQL na platformie Azure

W tym kroku utworzysz bazę danych PostgreSQL na platformie Azure. Aplikacja wdrożona na platformie Azure używa tej bazy danych w chmurze.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>Tworzenie serwera PostgreSQL

Za pomocą polecenia [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) utwórz serwer PostgreSQL.

W poniższym poleceniu zastąp symbol zastępczy *\<postgresql_name>*unikatową nazwą serwera, a symbol zastępczy *\<admin_username>* nazwą użytkownika. Ta nazwa serwera jest używana jako część punktu końcowego bazy danych PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), więc nazwa musi być unikatowa na wszystkich serwerach platformy Azure. Nazwa użytkownika to użyta na początku nazwa konta użytkownika administratora bazy danych. Zostanie wyświetlony monit o wybranie hasła dla tego użytkownika.

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

### <a name="configure-server-firewall"></a>Konfigurowanie zapory serwera

Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby zezwolić na dostęp do bazy danych ze wszystkich adresów IP.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

W interfejsie wiersza polecenia platformy Azure zostanie wyświetlone potwierdzenie utworzenia reguły zapory podobne do następującego:

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

### <a name="create-a-production-database-and-user"></a>Tworzenie produkcyjnej bazy danych i użytkownika

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

### <a name="test-app-locally-with-azure-postgresql"></a>Lokalne testowanie aplikacji za pomocą bazy danych PostgreSQL na platformie Azure

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

![Działająca lokalnie aplikacja platformy Python Flask](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

W tym kroku wdrożysz aplikację języka Python połączoną z bazą danych PostgreSQL w usłudze Azure App Service.

Repozytorium Git zawiera już następujące pliki potrzebne do uruchomienia aplikacji internetowej platformy Flask w usłudze App Service:

- `.deployment`: określa niestandardowy skrypt wdrożeniowy do uruchomienia.
- `deploy.cmd`: skrypt wdrożeniowy. Jest to lokalizacja uruchamiania polecenia `pip install`.
- `web.config`: określa skrypt punktu wejścia do uruchomienia w witrynie `httpPlatformHandler` w usługach IIS.
- `run_waitress_server.py`: skrypt punktu wejścia. Uruchamia aplikację internetową platformy Flask na serwerze [`waitress`](https://docs.pylonsproject.org/projects/waitress).

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Tworzenie aplikacji sieci Web

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Instalacja języka Python

W tym kroku zainstalujesz środowisko Python 3.6.2 za pomocą [rozszerzeń witryny](https://www.siteextensions.net/packages?q=Tags%3A%22python%22) w usłudze App Service. Aby przeprowadzić uwierzytelnienie względem punktu końcowego REST, użyjesz poświadczeń skonfigurowanych w kroku [Konfigurowanie użytkownika wdrożenia](#configure-a-deployment-user).

Uruchom poniższe polecenie w usłudze Cloud Shell, aby uzyskać informacje o pakiecie Python 3.6.2. Zastąp ciąg *\<deployment_user>* za pomocą skonfigurowanej wcześniej nazwy użytkownika wdrożenia, a ciąg *\<app_name>* za pomocą nazwy swojej aplikacji. Po wyświetleniu monitu użyj skonfigurowanego wcześniej hasła wdrożenia.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

Uruchom poniższe polecenie w usłudze Cloud Shell, aby zainstalować pakiet Python. Zastąp ciąg *\<deployment_user>* za pomocą skonfigurowanej wcześniej nazwy użytkownika wdrożenia, a ciąg *\<app_name>* za pomocą nazwy swojej aplikacji. Po wyświetleniu monitu użyj skonfigurowanego wcześniej hasła wdrożenia.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

Dane wyjściowe polecenia wskazują, że pakiet Python został zainstalowany w ścieżce `D:\home\python362x86\python.exe`.

### <a name="configure-database-settings"></a>Konfigurowanie ustawień bazy danych

Wcześniej w tym samouczku zdefiniowano zmienne środowiskowe na potrzeby nawiązywania połączeń z bazą danych PostgreSQL.

W usłudze App Service zmienne środowiskowe określa się jako _ustawienia aplikacji_ za pomocą polecenia [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

W poniższym przykładzie określono szczegóły połączenia z bazą danych jako ustawienia aplikacji. Zastąp ciąg *\<app_name>* nazwą swojej aplikacji, a ciąg *\<postgresql_name>* nazwą swojego serwera PostgreSQL.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure 

Przejdź do wdrożonej aplikacji internetowej w przeglądarce internetowej. 

```bash 
http://<app_name>.azurewebsites.net 
```

Widać wcześniej zarejestrowanych gości, którzy zostali zapisani w produkcyjnej bazie danych platformy Azure w poprzednim kroku.

![Działająca lokalnie aplikacja platformy Python Flask](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**Gratulacje!** Twoja aplikacja platformy Python Flask działa w usłudze Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Aktualizowanie modelu danych i ponowne wdrażanie

W tym kroku do każdej rejestracji wydarzenia dodasz pewną liczbę uczestników, aktualizując model `Guest`.

Wyewidencjonuj pliki otagowane przez zatwierdzenie `modelChange`:

```bash
git checkout modelChange -- *
```

To wydanie wprowadziło już niezbędne zmiany w widokach, kontrolerach i modelu. Obejmuje ono również migrację bazy danych uruchamianą za pomocą polecenia *alembic* (`flask db migrate`). Zmiany wprowadzone we wszystkich plikach można wyświetlić w [widoku zatwierdzania usługi GitHub](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e).

### <a name="test-your-changes-locally"></a>Lokalne testowanie zmian

Uruchom następujące polecenia, aby lokalnie przetestować zmiany przez uruchomienie serwera Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

W przeglądarce przejdź do adresu http://localhost:5000, aby przejrzeć zmiany. Utwórz rejestrację testową.

![Działająca lokalnie aplikacja platformy Python Flask](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

W oknie lokalnego terminala zatwierdź wszystkie zmiany w usłudze Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Przejdź do aplikacji internetowej platformy Azure i wypróbuj ponownie nowe funkcje. Utwórz inną rejestrację zdarzeń.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplikacja platformy Python Flask w usłudze Azure App Service](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Zarządzanie aplikacją internetową platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby wyświetlić utworzoną aplikację internetową.

W lewym menu kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji sieci Web platformy Azure.

![Nawigacja w portalu do aplikacji sieci Web platformy Azure](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

Domyślnie portal zawiera stronę **Omówienie** aplikacji internetowej. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure Web Apps (Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure)](app-service-web-tutorial-custom-domain.md)
