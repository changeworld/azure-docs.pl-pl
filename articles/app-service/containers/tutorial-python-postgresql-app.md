---
title: 'Samouczek: aplikacja systemu Linux Python z Postgre'
description: Dowiedz się, jak pobrać aplikację dla systemu Linux w Azure App Service z połączeniem z bazą danych PostgreSQL na platformie Azure. Django jest używany w tym samouczku.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523938"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Samouczek: uruchamianie aplikacji sieci Web języka Python (Django) za pomocą PostgreSQL w Azure App Service

Usługa [Azure App Service](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. W tym samouczku przedstawiono sposób łączenia aplikacji sieci Web w języku Python Django opartej na danych z bazą danych Azure Database for PostgreSQL oraz wdrażania i uruchamiania aplikacji na Azure App Service.

![Aplikacja internetowa Python Django w Azure App Service](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych Azure Database for PostgreSQL i łączenie z nią aplikacji sieci Web
> * Wdróż aplikację sieci Web w usłudze Azure App Service
> * Wyświetlanie dzienników diagnostyki
> * Zarządzanie aplikacją internetową w Azure Portal

Kroki opisane w tym artykule można wykonać w systemie macOS, Linux lub Windows. Kroki są podobne w większości przypadków, chociaż różnice nie są szczegółowo opisane w tym samouczku. W większości przykładów poniżej użyto okna terminalu `bash` w systemie Linux. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- [Zainstaluj oprogramowanie Git](https://git-scm.com/).
- Zainstaluj język [Python 3](https://www.python.org/downloads/).
- Zainstaluj i uruchom program [PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Testowanie instalacji PostgreSQL i tworzenie bazy danych

Najpierw Połącz się z lokalnym serwerem PostgreSQL i Utwórz bazę danych: 

W oknie terminalu lokalnego Uruchom `psql`, aby nawiązać połączenie z lokalnym serwerem PostgreSQL jako wbudowaną `postgres` użytkownika.

```bash
sudo su - postgres
psql
```
lub
```PowerShell
psql -U postgres
```

Pomyślne nawiązanie połączenia oznacza, że baza danych PostgreSQL działa. W przeciwnym razie upewnij się, że lokalna baza danych PostgresQL została uruchomiona, wykonując kroki opisane w temacie dla Twojego systemu operacyjnego: [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Zasoby do pobrania — dystrybucja podstawowa serwera PostgreSQL).

Utwórz nową bazę danych o nazwie *pollsdb*i skonfiguruj użytkownika bazy danych o nazwie *Menedżer* przy użyciu hasła *supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Tworzenie i uruchamianie lokalnej aplikacji języka Python

Następnie skonfiguruj i uruchom przykładową aplikację sieci Web w języku Python Django.

Przykładowe repozytorium [djangoapp](https://github.com/Azure-Samples/djangoapp) zawiera aplikację do sondowania [Django](https://www.djangoproject.com/) opartego na danych, którą uzyskasz, wykonując od [zapisania pierwszej aplikacji Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) w dokumentacji Django.

### <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

W oknie terminalu uruchom następujące polecenia, aby sklonować repozytorium przykładowej aplikacji, a następnie przejdź do nowego katalogu roboczego:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Konfigurowanie środowiska wirtualnego języka Python

Utwórz i aktywuj środowisko wirtualne języka Python, aby uruchomić aplikację.

```bash
python3 -m venv venv
source venv/bin/activate
```
lub
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

W środowisku `venv` Uruchom *ENV.sh* lub *ENV. ps1* , aby ustawić zmienne środowiskowe, których *azuresite/Settings. PR* będzie używać dla ustawień połączenia z bazą danych.

```bash
source ./env.sh
```
lub
```PowerShell
.\env.ps1
```

Zainstaluj wymagane pakiety z programu *Requirements. txt*, uruchom [migracje Django](https://docs.djangoproject.com/en/2.1/topics/migrations/)i [Utwórz użytkownika administracyjnego](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user):

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Uruchamianie aplikacji internetowej

Po utworzeniu użytkownika administracyjnego Uruchom serwer Django.

```bash
python manage.py runserver
```

Gdy aplikacja sieci Web Django jest w pełni załadowana, zwraca komunikat podobny do następującego:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Przejdź do *protokołu http:\//localhost: 8000* w przeglądarce. Powinien zostać wyświetlony komunikat **Brak dostępnych sondowań**. 

Przejdź do *protokołu http:\//localhost: 8000/admin* i zaloguj się przy użyciu administratora utworzonego w ostatnim kroku. Wybierz pozycję **Dodaj** obok pozycji **pytania**i Utwórz pytanie dotyczące ankiety z wybranymi opcjami.

![Uruchamianie aplikacji Django języka Python w App Services lokalnie](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Przejdź do *protokołu http:\//localhost: 8000* ponownie, aby zobaczyć pytanie sondy i odpowiedzieć na pytanie. Lokalna aplikacja Przykładowa Django zapisuje i zapisuje dane użytkownika w lokalnej bazie danych PostgreSQL.

Aby zatrzymać serwer Django, wpisz Ctrl + C w terminalu.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Większość pozostałych kroków tego artykułu korzysta z poleceń interfejsu wiersza polecenia platformy Azure w Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Tworzenie Azure Database for PostgreSQL i nawiązywanie z nimi połączeń

W tej sekcji utworzysz serwer Azure Database for PostgreSQL i bazę danych, a następnie nawiążesz połączenie z aplikacją sieci Web. Po wdrożeniu aplikacji sieci Web do Azure App Service aplikacja korzysta z tej bazy danych w chmurze. 

### <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Można utworzyć nową grupę zasobów dla serwera Azure Database for PostgreSQL lub użyć istniejącej grupy zasobów. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Serwer PostgreSQL można utworzyć za pomocą polecenia [AZ Postgres Server Create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) w Cloud Shell.

> [!NOTE]
> Przed utworzeniem serwera Azure Database for PostgreSQL Sprawdź, które [generowanie obliczeń](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) jest dostępne w Twoim regionie. Jeśli region nie obsługuje sprzętu obliczenia, Zmień *nazwę jednostki SKU* na następujący wiersz polecenia na wartość obsługiwaną w Twoim regionie, na przykład 5 rdzeń. 

W poniższym poleceniu Zastąp *\<PostgreSQL-name >* nazwą unikatowego serwera. Nazwa serwera jest częścią PostgreSQLego punktu końcowego *https://\<PostgreSQL-name >. Postgres. Database. Azure. com*, więc nazwa musi być unikatowa na wszystkich serwerach na platformie Azure. 

Zastąp\<grupę zasobów *>* i *\<region >* z nazwą i regionem grupy, której chcesz użyć. W przypadku *\<admin-username >* i *\<> hasła administratora*Utwórz poświadczenia użytkownika dla konta administratora bazy danych. Należy pamiętać, że *\<admin-username >* i *\<administrator-Password >* do logowania się do serwera i baz danych PostgreSQL.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Po utworzeniu serwera Azure Database for PostgreSQL interfejs wiersza polecenia platformy Azure zwraca kod JSON, taki jak Poniższy przykład:

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Tworzenie reguł zapory dla serwera Azure Database for PostgreSQL

Uruchom polecenie [AZ Postgres Server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) , aby zezwolić na dostęp do bazy danych z zasobów platformy Azure. Zastąp *\<PostgreSQL-name >* i\<symbolami zastępczymi *> nazw* .

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Powyższe ustawienie umożliwia nawiązywanie połączeń sieciowych ze wszystkich adresów IP w sieci platformy Azure. W celu użycia w środowisku produkcyjnym spróbuj skonfigurować najbardziej restrykcyjne reguły zapory, [zezwalając tylko na wychodzące adresy IP używane przez aplikację](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Uruchom ponownie polecenie `firewall-rule create`, aby zezwolić na dostęp z komputera lokalnego. Zastąp *\<adres ip >* z [lokalnym adresem IP IPv4](https://www.whatsmyip.org/). Zastąp *\<PostgreSQL-name >* i *\<* symboli zastępczych >

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Tworzenie bazy danych Azure Database for PostgreSQL i nawiązywanie z nią połączenia

Połącz się z serwerem Azure Database for PostgreSQL, uruchamiając następujące polecenie. Użyj własnych *\<PostgreSQL-name >* i *\<admin-username >* i zaloguj się przy użyciu utworzonego hasła.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Tak jak w lokalnym serwerze PostgreSQL, Utwórz bazę danych i użytkownika na serwerze Azure Database for PostgreSQL:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Najlepszym rozwiązaniem jest tworzenie użytkowników bazy danych z ograniczonymi uprawnieniami dla określonych aplikacji, zamiast korzystać z administratora. Użytkownik `manager` ma pełne uprawnienia *tylko* do bazy danych `pollsdb` Database.

Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testowanie łączności aplikacji z usługą Azure PostgreSQL Database

Edytuj lokalny plik *ENV.sh* lub *ENV. ps1* , aby wskazywał swoją bazę danych PostgreSQL w chmurze, zastępując *\<PostgreSQL-Name >* nazwą serwera Azure Database for PostgreSQL.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
lub
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

W środowisku `venv` w lokalnym oknie terminala Uruchom edytowane *ENV.sh* lub *ENV. ps1*. 
```bash
source ./env.sh
```
lub
```PowerShell
.\env.ps1
```

Uruchom migrację Django do bazy danych platformy Azure i Utwórz użytkownika administracyjnego.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Po utworzeniu administratora uruchom serwer Django.

```bash
python manage.py runserver
```

W przeglądarce przejdź do *protokołu http:\//localhost: 8000*i zobaczysz komunikat, że **nie są dostępne żadne sondy** . 

Przejdź do *protokołu http:\//localhost: 8000/administrator*, zaloguj się przy użyciu utworzonego przez Ciebie użytkownika administracyjnego i Utwórz pytanie dotyczące ankiety, jak wcześniej.

![Uruchamianie aplikacji Django języka Python w App Services lokalnie](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Przejdź do *protokołu http:\//localhost: 8000* ponownie i Wyświetl wyświetlone pytanie dotyczące sondowania. Aplikacja zapisuje teraz dane do bazy danych Azure Database for PostgreSQL.

Aby zatrzymać serwer Django, wpisz Ctrl + C w terminalu.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Wdróż aplikację sieci Web w usłudze Azure App Service

W tym kroku zostanie wdrożona aplikacja języka Python połączona z bazą danych Azure Database for PostgreSQL, aby Azure App Service.

### <a name="configure-repository"></a>Konfigurowanie repozytorium

Ponieważ w tym samouczku jest używany przykład Django, należy zmienić i dodać niektóre ustawienia w pliku *djangoapp/azuresite/Settings. PR* , aby współdziałać z Azure App Service. 

1. Platforma Django weryfikuje nagłówek `HTTP_HOST` w żądaniach przychodzących. Aby aplikacja sieci Web Django działała w App Service, należy dodać w pełni kwalifikowaną nazwę domeny aplikacji do dozwolonych hostów. 
   
   Edytuj *azuresite/Settings. PR* , aby zmienić wiersz `ALLOWED_HOSTS` w następujący sposób:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django nie obsługuje obsługi [plików statycznych w środowisku produkcyjnym](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). W tym samouczku użyjesz [WhiteNoise](https://whitenoise.evans.io/en/stable/) , aby włączyć obsługę plików. Pakiet WhiteNoise został już zainstalowany z *wymaganiami. txt*. 
   
   Aby skonfigurować Django do korzystania z WhiteNoise, w obszarze *azuresite/Settings. PR*Znajdź `MIDDLEWARE` ustawienie i Dodaj `whitenoise.middleware.WhiteNoiseMiddleware` do listy, tuż po wierszu `django.middleware.security.SecurityMiddleware`. Ustawienie `MIDDLEWARE` powinno wyglądać następująco:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Na końcu *azuresite/Settings. PR*Dodaj następujące wiersze:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Więcej informacji o konfigurowaniu WhiteNoise można znaleźć w [dokumentacji WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Sekcja ustawień bazy danych jest już zgodna z najlepszym rozwiązaniem z zakresu bezpieczeństwa polegającym na używaniu zmiennych środowiskowych. Aby uzyskać pełne zalecenia dotyczące wdrażania, zobacz [dokumentację Django: Lista kontrolna wdrożenia](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Zatwierdź zmiany w rozwidleniu repozytorium *djangoapp* :

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Wcześniej w tym samouczku zdefiniowano zmienne środowiskowe na potrzeby nawiązywania połączeń z bazą danych PostgreSQL.

W Azure App Service, zmienne środowiskowe są ustawiane jako *Ustawienia aplikacji*za pomocą polecenia [AZ webapp config AppSettings Set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) .

W Azure Cloud Shell Uruchom następujące polecenie, aby określić szczegóły połączenia z bazą danych jako ustawienia aplikacji. Zastąp *ciąg\<App-name >* , *\<nazwę resource-name >* i *\<PostgreSQL-Name >* własnymi wartościami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Aby uzyskać informacje o tym, jak kod uzyskuje dostęp do tych ustawień aplikacji, zobacz [dostęp do zmiennych środowiskowych](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

Serwer wdrażania usługi App Service widzi plik *requirements.txt* w katalogu głównym repozytorium i automatycznie uruchamia zarządzanie pakietami języka Python po wykonaniu polecenia `git push`.

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do wdrożonej aplikacji przy użyciu adresu URL *http:\//\<App-name >. azurewebsites. NET*. Uruchamianie jest czasochłonne, ponieważ kontener musi być pobrany i uruchomiony, gdy aplikacja zostanie zażądana po raz pierwszy. Jeśli strona przekroczy limit czasu lub wyświetli komunikat o błędzie, odczekaj kilka minut i odśwież stronę.

Powinny pojawić się wcześniej utworzone pytania dotyczące sondowania. 

App Service wykrywa projekt Django w repozytorium, wyszukując plik *WSGI.py* w każdym podkatalogu, który jest domyślnie tworzony `manage.py startproject`. Gdy App Service odnajdzie plik, ładuje aplikację sieci Web Django. Aby uzyskać więcej informacji na temat sposobu ładowania aplikacji języka Python przez usługę App Service, zobacz [Konfigurowanie wbudowanego obrazu języka Python](how-to-configure-python.md).

Przejdź do *protokołu http:\//\<App-name >. azurewebsites. NET/administrator* i zaloguj się przy użyciu utworzonego przez Ciebie administratora. Jeśli chcesz, Utwórz więcej pytań dotyczących sondowania.

![Uruchamianie aplikacji Django języka Python w App Services na platformie Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Gratulacje!** Korzystasz z aplikacji sieci Web języka Python (Django) w Azure App Service dla systemu Linux.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Zarządzanie aplikacją w Azure Portal

W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz utworzoną aplikację.

![Przejdź do aplikacji w języku Python Django w Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Domyślnie portal pokazuje stronę **Przegląd** aplikacji. Ta strona udostępnia widok sposobu działania aplikacji. W tym miejscu można również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Zarządzanie aplikacją Django języka Python na stronie Przegląd w Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację:

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](../app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji języka Python](how-to-configure-python.md)
