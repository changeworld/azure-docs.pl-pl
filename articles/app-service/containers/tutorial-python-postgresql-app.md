---
title: Tworzenie aplikacji internetowej przy użyciu języka Python i bazy danych PostgreSQL w usłudze Azure App Service | Microsoft Docs
description: Dowiedz się, jak uruchomić opartą na danych aplikację napisaną w języku Python na platformie Azure z użyciem połączenia z bazą danych PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435790"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Tworzenie aplikacji internetowej platformy Docker przy użyciu języka Python i bazy danych PostgreSQL na platformie Azure

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. W tym samouczku przedstawiono sposób tworzenia opartej na danych aplikacji internetowej w języku Python z użyciem bazy danych PostgreSQL jako wewnętrznej bazy danych. Po zakończeniu aplikacja platformy Python Flask będzie działała w kontenerze Docker w usłudze App Service w systemie Linux.

![Aplikacja platformy Docker Python Flask w usłudze App Service w systemie Linux](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych PostgreSQL na platformie Azure
> * Łączenie aplikacji w języku Python z bazą danych PostgreSQL
> * Wdrażanie aplikacji na platformie Azure
> * Wyświetlanie dzienników diagnostycznych
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Zarządzanie aplikacją w witrynie Azure Portal

Kroki opisane w tym artykule można wykonać w systemie macOS. Instrukcje dotyczące systemów Linux i Windows są takie same w większości przypadków, ale występujące różnice nie są szczegółowo opisane w tym samouczku.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
1. [Zainstaluj język Python](https://www.python.org/downloads/)
1. [Zainstaluj i uruchom serwer PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testowanie lokalnej instalacji serwera PostgreSQL i tworzenie bazy danych

W lokalnym oknie terminalu uruchom polecenie `psql`, aby nawiązać połączenie z lokalnym serwerem PostgreSQL.

```bash
sudo -u postgres psql postgres
```

Jeśli otrzymasz komunikat o błędzie podobny do `unknown user: postgres`, instalacja bazy danych PostgreSQL może być skonfigurowana przy użyciu nazwy zalogowanego użytkownika. Spróbuj użyć następującego polecenia zamiast tego.

```bash
psql postgres
```

Pomyślne nawiązanie połączenia oznacza, że baza danych PostgreSQL działa. W przeciwnym razie upewnij się, że lokalna baza danych PostgresQL została uruchomiona, wykonując kroki opisane w temacie dla Twojego systemu operacyjnego: [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Zasoby do pobrania — dystrybucja podstawowa serwera PostgreSQL).

Utwórz bazę danych o nazwie *eventregistration* i skonfiguruj oddzielnego użytkownika bazy danych o nazwie *manager* z hasłem *supersecretpass*.

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Tworzenie lokalnej aplikacji w języku Python

Ten krok umożliwia skonfigurowanie lokalnego projektu platformy Python Flask.

### <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

Otwórz okno terminalu i za pomocą polecenia `CD` przejdź do katalogu roboczego.

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

To przykładowe repozytorium zawiera aplikację [Flask](http://flask.pocoo.org/).

### <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Zainstaluj wymagane pakiety i uruchom aplikację.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
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

![Działająca lokalnie aplikacja platformy Python Flask](./media/tutorial-python-postgresql-app/local-app.png)

Przykładowa aplikacja platformy Flask przechowuje dane użytkowników w bazie danych. Po pomyślnym zarejestrowaniu użytkownika aplikacja będzie zapisywała dane w lokalnej bazie danych PostgreSQL.

Aby w dowolnym momencie zatrzymać serwer Flask, naciśnij klawisze Ctrl+C w terminalu.

## <a name="create-a-production-postgresql-database"></a>Tworzenie produkcyjnej bazy danych PostgreSQL

W tym kroku utworzysz bazę danych PostgreSQL na platformie Azure. Aplikacja wdrożona na platformie Azure używa tej bazy danych w chmurze.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Za pomocą polecenia [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) utwórz serwer PostgreSQL w usłudze Cloud Shell.

W poniższym przykładowym poleceniu zastąp wyrażenie *\<postgresql_name>* unikatową nazwą serwera, a wyrażenia *\<admin_username>* i *\<admin_password>* poświadczeniami odpowiedniego użytkownika. Poświadczenia użytkownika są dla konta administratora bazy danych. Ta nazwa serwera jest używana jako część punktu końcowego bazy danych PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), więc nazwa musi być unikatowa na wszystkich serwerach platformy Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
```

Po utworzeniu serwera usługi Azure Database for PostgreSQL w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "administratorLogin": "<admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Zapamiętaj wyrażenia \<admin_username> oraz \<admin_password> na później. Będą one potrzebne do zalogowania się do serwera Postgre i jego baz danych.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>Tworzenie reguł zapory dla serwera PostgreSQL

W usłudze Cloud Shell uruchom następujące polecenia interfejsu wiersza polecenia platformy Azure, aby zezwolić na dostęp do bazy danych z zasobów platformy Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> To ustawienie umożliwia nawiązywanie połączeń sieciowych ze wszystkich adresów IP w sieci platformy Azure. Na potrzeby użytku produkcyjnego spróbuj skonfigurować możliwie najbardziej restrykcyjne reguły zapory, [używając tylko adresów IP ruchu wychodzącego wykorzystywanych przez Twoją aplikację](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

W usłudze Cloud Shell ponownie uruchom to samo polecenie, aby umożliwić dostęp z komputera lokalnego, zastępując wyrażenie *\<you_ip_address>* [lokalnym adresem IP w wersji IPv4](http://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Łączenie aplikacji w języku Python z produkcyjną bazą danych

W tym kroku połączysz przykładową aplikację platformy Flask z utworzonym serwerem usługi Azure Database for PostgreSQL.

### <a name="create-empty-database-and-user-access"></a>Tworzenie pustej bazy danych oraz dostępu użytkownika

W lokalnym oknie terminalu połącz się z bazą danych, uruchamiając poniższe polecenie. Po wyświetleniu monitu o hasło administratora użyj tego samego hasła, które zostało określone w sekcji [Tworzenie serwera usługi Azure Database for PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Tak jak w przypadku lokalnego serwera Postgres, utwórz bazę danych i użytkownika na serwerze Postgres na platformie Azure.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL.

> [!NOTE]
> To najlepsze rozwiązanie umożliwiające tworzenie użytkowników bazy danych z ograniczonymi uprawnieniami do wybranych aplikacji, zamiast korzystania z roli administratora. W tym przykładzie użytkownik `manager` ma pełne uprawnienia _tylko_ do bazy danych `eventregistration`.

### <a name="test-app-connectivity-to-production-database"></a>Testowanie łączności aplikacji z produkcyjną bazą danych

Po powrocie do lokalnego okna terminalu uruchom następujące polecenia, aby rozpocząć migrację bazy danych i serwera Flask.

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

![Działająca lokalnie aplikacja platformy Python Flask](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

W tym kroku wdrożysz aplikację języka Python połączoną z bazą danych Postgres w usłudze Azure App Service.

### <a name="configure-repository"></a>Konfigurowanie repozytorium

Aparat wdrażania Git w usłudze App Service wywołuje automatyzację `pip`, jeżeli w katalogu głównym repozytorium znajduje się plik _application.py_. W tym samouczku aparat wdrożenia przeprowadzi automatyzację za Ciebie. W lokalnym oknie terminalu przejdź do katalogu głównego repozytorium, utwórz fikcyjny plik _application.py_ i zatwierdź zmiany.

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Wcześniej w tym samouczku zdefiniowano zmienne środowiskowe na potrzeby nawiązywania połączeń z bazą danych PostgreSQL.

W usłudze App Service zmienne środowiskowe ustawia się jako _ustawienia aplikacji_ za pomocą polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell.

W poniższym przykładzie określono szczegóły połączenia z bazą danych jako ustawienia aplikacji. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

### <a name="configure-entry-point"></a>Konfigurowanie punktu wejścia

Domyślnie wbudowany obraz szuka pliku _wsgi.py_ lub _application.py_ w katalogu głównym jako punktu wejścia, ale tutaj punkt wejścia to _app/app.py_. Plik _application.py_ dodany wcześniej jest pusty i nic nie robi.

W usłudze Cloud Shell uruchom polecenie [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set), aby ustawić niestandardowy skrypt uruchamiania.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

Parametr `--startup-file` przyjmuje polecenie niestandardowe lub ścieżkę do pliku, który zawiera polecenie niestandardowe. Polecenie niestandardowe powinno mieć następujący format:

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

W poleceniu niestandardowym element `--chdir` jest wymagany, jeśli punkt wejścia nie znajduje się w katalogu głównym, a `<subdirectory>` to podkatalog. `<module>` to nazwa pliku _.py_, a `<variable>` to zmienna w module reprezentująca aplikację internetową.

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure

Przejdź do wdrożonej aplikacji internetowej. Uruchomienie przy pierwszym żądaniu aplikacji może trochę potrwać, ponieważ wymaga pobrania i uruchomienia kontenera. Jeśli strona przekroczy limit czasu lub wyświetli komunikat o błędzie, odczekaj kilka minut i odśwież stronę.

```bash
http://<app_name>.azurewebsites.net
```

Widać wcześniej zarejestrowanych gości, którzy zostali zapisani w produkcyjnej bazie danych platformy Azure w poprzednim kroku.

![Aplikacja platformy Python Flask działająca na platformie Azure](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**Gratulacje!** Twoja aplikacja języka Python działa w usłudze App Service dla systemu Linux.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

Ponieważ aplikacja języka Python jest uruchomiona w kontenerze, usługa App Service w systemie Linux umożliwia dostęp do dzienników konsoli generowanych z poziomu kontenera. Aby znaleźć dzienniki, przejdź do tego adresu URL:

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

Powinny być widoczne dwa obiekty JSON, każdy z właściwością `href`. Właściwość jednego obiektu `href` wskazuje dzienniki konsoli Docker (kończy się na `_docker.log`), a właściwość drugiego obiektu `href` wskazuje dzienniki konsoli wygenerowane w kontenerze języka Python. 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

Skopiuj żądaną wartość `href` do okna przeglądarki, aby przejść do dzienników. Dzienniki nie są przesyłane strumieniowo, więc może wystąpić pewne opóźnienie. Aby zobaczyć nowe dzienniki, odśwież stronę przeglądarki.

## <a name="update-data-model-and-redeploy"></a>Aktualizowanie modelu danych i ponowne wdrażanie

W tym kroku do każdej rejestracji wydarzenia dodasz pewną liczbę uczestników, aktualizując model `Guest`, a następnie przeprowadzając ponowne wdrożenie aktualizacji na platformie Azure.

W lokalnym oknie terminalu wyewidencjonuj pliki z gałęzi `modelChange` przy użyciu następującego polecenia git:

```bash
git checkout origin/modelChange -- .
```

To wyewidencjonowanie wprowadza już niezbędne zmiany w modelu, widokach i kontrolerach. Obejmuje ono również migrację bazy danych uruchamianą za pomocą polecenia *alembic* (`flask db migrate`). Za pomocą następującego polecenia git można wyświetlić wszystkie zmiany:

```bash
git diff master origin/modelChange
```

### <a name="test-your-changes-locally"></a>Lokalne testowanie zmian

W lokalnym oknie terminalu uruchom następujące polecenia, aby lokalnie przetestować zmiany przez uruchomienie serwera Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

W przeglądarce przejdź do adresu http://localhost:5000, aby przejrzeć zmiany. Utwórz rejestrację testową.

![Działająca lokalnie aplikacja platformy Python Flask oparta na kontenerze Docker](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

W oknie lokalnego terminala zatwierdź wszystkie zmiany w usłudze Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Przejdź do aplikacji internetowej platformy Azure i wypróbuj ponownie nowe funkcje. Upewnij się, że strona została odświeżona.

```bash
http://<app_name>.azurewebsites.net
```

![Aplikacja platformy Docker Python Flask w usłudze Azure App Service](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>Zarządzanie aplikacją internetową w witrynie Azure Portal

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby wyświetlić utworzoną aplikację internetową.

W lewym menu kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji internetowej platformy Azure.

![Nawigacja w portalu do aplikacji internetowej platformy Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Domyślnie portal zawiera stronę **Omówienie** aplikacji internetowej. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych PostgreSQL na platformie Azure
> * Łączenie aplikacji w języku Python z bazą danych PostgreSQL
> * Wdrażanie aplikacji na platformie Azure
> * Wyświetlanie dzienników diagnostycznych
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Konfigurowanie wbudowanego obrazu języka Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure Web Apps (Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure)](../app-service-web-tutorial-custom-domain.md)

