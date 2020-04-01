---
title: 'Samouczek: Aplikacja Linux Python z Postgre'
description: Dowiedz się, jak uzyskać aplikację Języka Linux Python działającą w usłudze Azure App Service, z połączeniem z bazą danych PostgreSQL na platformie Azure. Django jest używany w tym samouczku.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523938"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Samouczek: Uruchamianie aplikacji internetowej Python (Django) z postgreSql w usłudze Azure App Service

[Usługa Azure App Service](app-service-linux-intro.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingu. W tym samouczku pokazano, jak połączyć aplikację internetową Python Django sterowaną danymi z bazą danych usługi Azure Database for PostgreSQL oraz wdrożyć i uruchomić aplikację w usłudze Azure App Service.

![Aplikacja internetowa Python Django w usłudze azure app service](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych usługi Azure Database dla postgreSql i łączenie z nią aplikacji sieci web
> * Wdrażanie aplikacji sieci Web w usłudze Azure App Service
> * Wyświetlanie dzienników diagnostycznych
> * Zarządzanie aplikacją sieci Web w witrynie Azure portal

Możesz wykonać kroki opisane w tym artykule w systemach macOS, Linux lub Windows. Kroki są podobne w większości przypadków, chociaż różnice nie są szczegółowo opisane w tym samouczku. Większość poniższych przykładów `bash` używa okna terminala w systemie Linux. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Zainstaluj [Git](https://git-scm.com/).
- Zainstaluj [Pythona 3](https://www.python.org/downloads/).
- Zainstaluj i uruchom [PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Przetestuj instalację PostgreSQL i utwórz bazę danych

Najpierw połącz się z lokalnym serwerem PostgreSQL i utwórz bazę danych: 

W oknie terminala `psql` lokalnego uruchom, aby połączyć się z lokalnym `postgres` serwerem PostgreSQL jako wbudowany użytkownik.

```bash
sudo su - postgres
psql
```
lub
```PowerShell
psql -U postgres
```

Pomyślne nawiązanie połączenia oznacza, że baza danych PostgreSQL działa. W przeciwnym razie upewnij się, że lokalna baza danych PostgresQL została uruchomiona, wykonując kroki opisane w temacie dla Twojego systemu operacyjnego: [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Zasoby do pobrania — dystrybucja podstawowa serwera PostgreSQL).

Utwórz nową bazę danych o nazwie *pollsdb*i skonfiguruj użytkownika bazy danych o nazwie *manager* z *supersecretpass*hasła:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Tworzenie i uruchamianie lokalnej aplikacji Języka Python

Następnie skonfiguruj i uruchom przykładową aplikację internetową Python Django.

Repozytorium próbek [djangoapp](https://github.com/Azure-Samples/djangoapp) zawiera opartą na danych aplikację [ankiety Django,](https://www.djangoproject.com/) którą otrzymujesz, wykonując [pisanie pierwszej aplikacji Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) w dokumentacji Django.

### <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

W oknie terminala uruchom następujące polecenia, aby sklonować przykładowe repozytorium aplikacji i przejść do nowego katalogu roboczego:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Konfigurowanie środowiska wirtualnego języka Python

Tworzenie i aktywowanie środowiska wirtualnego języka Python w celu uruchomienia aplikacji.

```bash
python3 -m venv venv
source venv/bin/activate
```
lub
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

W `venv` środowisku uruchom *env.sh* lub *env.ps1,* aby ustawić zmienne środowiskowe, które będą używane dla ustawień połączenia *z* bazą danych.

```bash
source ./env.sh
```
lub
```PowerShell
.\env.ps1
```

Zainstaluj wymagane pakiety z *pliku requirements.txt*, uruchom [migracje Django](https://docs.djangoproject.com/en/2.1/topics/migrations/)i [utwórz użytkownika administratora:](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Uruchamianie aplikacji internetowej

Po utworzeniu użytkownika administratora uruchom serwer Django.

```bash
python manage.py runserver
```

Gdy aplikacja internetowa Django jest w pełni załadowana, zwraca coś następującego:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Przejdź do *\/http: /localhost:8000* w przeglądarce. Powinien zostać wyświetlony komunikat **Brak dostępnych ankiet.** 

Przejdź do *\/http: /localhost:8000/admin* i zaloguj się przy użyciu użytkownika administratora utworzonego w ostatnim kroku. Wybierz **pozycję Dodaj** obok pozycji **Pytania**i utwórz pytanie ankietowe z wybranymi wyborami.

![Uruchamianie aplikacji Python Django w usługach app services lokalnie](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Przejdź do *\/http: /localhost:8000* ponownie, aby zobaczyć pytanie ankiety i odpowiedzieć na pytanie. Lokalna przykładowa aplikacja Django zapisuje i przechowuje dane użytkownika w lokalnej bazie danych PostgreSQL.

Aby zatrzymać serwer Django, wpisz ctrl+C w terminalu.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Większość pozostałych kroków w tym artykule należy użyć poleceń interfejsu wiersza polecenia platformy Azure w usłudze Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Tworzenie bazy danych usługi Azure Database dla usługi PostgreSQL i łączenie się z nią

W tej sekcji utworzysz usługę Azure Database dla serwera postgreSql i bazy danych i połączyć aplikację sieci web z nim. Podczas wdrażania aplikacji sieci web w usłudze Azure App Service aplikacja korzysta z tej bazy danych w chmurze. 

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Można utworzyć nową grupę zasobów dla usługi Azure Database dla serwera PostgreSQL lub użyć istniejącej grupy zasobów. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Tworzenie serwera PostgreSQL z [az postgres serwera utworzyć](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) polecenie w Cloud Shell.

> [!NOTE]
> Przed utworzeniem usługi Azure Database dla serwera PostgreSQL sprawdź, które [generowanie obliczeń](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) jest dostępne w Twoim regionie. Jeśli twój region nie obsługuje sprzętu Gen4, zmień *--sku-name* w następującym wierszu polecenia na wartość obsługiwaną w Twoim regionie, taką jak Gen5. 

W poniższym poleceniu zastąp * \<>nazwy postgresql* unikatową nazwą serwera. Nazwa serwera jest częścią punktu końcowego PostgreSQL *https://\<postgresql-name>.postgres.database.azure.com*, więc nazwa musi być unikatowa na wszystkich serwerach na platformie Azure. 

Zastąp * \<nazwę grupy zasobów>* i * \<>regionu* nazwą i regionem grupy zasobów, której chcesz użyć. W * \<* przypadku>>i * \<>hasła administratora *należy utworzyć poświadczenia użytkownika dla konta administratora bazy danych. Zapamiętaj * \<>nazwy użytkownika* i * \<hasła administratora>* do późniejszego logowania się do serwera postgreSql i baz danych.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Po utworzeniu serwera usługi Azure Database for PostgreSQL narzędzie sieci web usługi Azure cli zwraca kod JSON, podobnie jak w poniższym przykładzie:

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

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Tworzenie reguł zapory dla serwera Usługi Azure Database dla postgreSQL

Uruchom polecenie [tworzenia reguły zapory serwera az postgres,](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) aby zezwolić na dostęp do bazy danych z zasobów platformy Azure. Zastąp * \<*>i * \<nazwę grupy zasobów>* symbolami zastępczymi wartościami.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Powyższe ustawienie umożliwia połączenia sieciowe ze wszystkich adresów IP w sieci platformy Azure. Do użytku w środowisku produkcyjnym spróbuj skonfigurować najbardziej restrykcyjne reguły zapory, [zezwalając tylko na wychodzące adresy IP używane przez aplikację.](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)

Uruchom `firewall-rule create` polecenie ponownie, aby zezwolić na dostęp z komputera lokalnego. * \<Wymień swój adres IP>* lokalnym adresem IP [IPv4](https://www.whatsmyip.org/). Zastąp * \<>i* * \<nazwę grupy zasobów>* symbolami zastępczymi własnymi wartościami.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Tworzenie bazy danych usługi Azure Database dla postgreSQL i łączenie się z nią

Połącz się z serwerem usługi Azure Database for PostgreSQL, uruchamiając następujące polecenie. Użyj własnej * \<nazwy postgresql>* i * \<>nazwy użytkownika administratora *i zaloguj się przy użyciu utworzonego hasła.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Podobnie jak na lokalnym serwerze PostgreSQL, utwórz bazę danych i użytkownika na serwerze Usługi Azure Database for PostgreSQL:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Najlepszym rozwiązaniem jest tworzenie użytkowników bazy danych z ograniczonymi uprawnieniami dla określonych aplikacji, zamiast używać użytkownika administratora. Użytkownik `manager` ma pełne uprawnienia *only* tylko `pollsdb` do bazy danych.

Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testowanie łączności aplikacji z bazą danych usługi Azure PostgreSQL

Edytuj plik *env.sh* lokalnej lub *env.ps1,* aby wskazać bazę danych PostgreSQL w chmurze, zastępując * \<>nazwą postgresql nazwą* usługi Azure Database dla postgreSQL.

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

W `venv` środowisku w oknie terminala lokalnego uruchom edytowane *env.sh* lub *env.ps1*. 
```bash
source ./env.sh
```
lub
```PowerShell
.\env.ps1
```

Uruchom migrację Django do bazy danych platformy Azure i utwórz użytkownika administratora.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Po utworzeniu administratora uruchom serwer Django.

```bash
python manage.py runserver
```

W przeglądarce przejdź do *\/http: /localhost:8000*, a powinieneś zobaczyć komunikat **Brak sondowania są ponownie dostępne.** 

Przejdź do *\/http: /localhost:8000/admin*, zaloguj się przy użyciu utworzonego użytkownika administratora i utwórz pytanie ankiety, jak poprzednio.

![Uruchamianie aplikacji Python Django w usługach app services lokalnie](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Przejdź do *\/http: /localhost:8000* ponownie i zobacz wyświetlane pytanie ankiety. Aplikacja jest teraz zapisywanie danych do bazy danych usługi Azure database dla postgreSql bazy danych.

Aby zatrzymać serwer Django, wpisz ctrl+C w terminalu.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Wdrażanie aplikacji sieci Web w usłudze Azure App Service

W tym kroku wdrożyć usługę Azure Database for PostgreSQL aplikacji Python połączonej z bazą danych do usługi Azure App Service.

### <a name="configure-repository"></a>Konfigurowanie repozytorium

Ponieważ w tym samouczku używa próbki Django, należy zmienić i dodać niektóre ustawienia w pliku *djangoapp/azuresite/settings.py* do pracy z usługą Azure App Service. 

1. Platforma Django weryfikuje nagłówek `HTTP_HOST` w żądaniach przychodzących. Aby twoja aplikacja internetowa Django działała w usłudze App Service, musisz dodać w pełni kwalifikowaną nazwę domeny aplikacji do dozwolonych hostów. 
   
   Edytuj *azuresite/settings.py,* `ALLOWED_HOSTS` aby zmienić wiersz w następujący sposób:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django nie obsługuje [obsługi plików statycznych w produkcji.](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/) W tym samouczku użyj [Funkcji WhiteNoise,](https://whitenoise.evans.io/en/stable/) aby włączyć obsługę plików. Pakiet WhiteNoise został już zainstalowany z *requirements.txt*. 
   
   Aby skonfigurować Django do używania WhiteNoise, w *azuresite/settings.py*, znajdź `MIDDLEWARE` ustawienie i dodaj `whitenoise.middleware.WhiteNoiseMiddleware` do listy, tuż za wierszem. `django.middleware.security.SecurityMiddleware` Ustawienie `MIDDLEWARE` powinno wyglądać następująco:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Na końcu *azuresite/settings.py*dodaj następujące wiersze:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Aby uzyskać więcej informacji na temat konfigurowania WhiteNoise, zobacz [dokumentację WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Sekcja ustawień bazy danych jest już zgodna z najlepszym rozwiązaniem z zakresu bezpieczeństwa polegającym na używaniu zmiennych środowiskowych. Aby uzyskać kompletne zalecenia dotyczące wdrażania, zobacz [Dokumentacja Django: lista kontrolna wdrażania](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Zaobeńcowanie zmian w rozwidlieniu repozytorium *djangoapp:*

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

W usłudze Azure App Service można ustawić zmienne środowiskowe jako *ustawienia aplikacji,* używając polecenia [az webapp config appsettings set.](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)

W usłudze Azure Cloud Shell uruchom następujące polecenie, aby określić szczegóły połączenia z bazą danych jako ustawienia aplikacji. * \<Zastąp>nazwy aplikacji, * * \<>nazwy grupy zasobów *i * \<>nazw postgresql* własnymi wartościami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Aby uzyskać informacje o tym, jak kod uzyskuje dostęp do tych ustawień aplikacji, zobacz [Dostęp do zmiennych środowiskowych](how-to-configure-python.md#access-environment-variables).

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

Przejdź do wdrożonej aplikacji z adresem URL *http:\//\<nazwa aplikacji>.azurewebsites.net*. Trwa trochę czasu, ponieważ kontener musi zostać pobrany i uruchomiony, gdy aplikacja jest żądana po raz pierwszy. Jeśli strona przekroczy limit czasu lub wyświetli komunikat o błędzie, odczekaj kilka minut i odśwież stronę.

Powinny być widoczne pytania ankiety utworzone wcześniej. 

Usługa App Service wykrywa projekt Django w repozytorium, szukając *pliku wsgi.py* w każdym `manage.py startproject` podkatalogu, który tworzy domyślnie. Gdy usługa App Service znajdzie plik, ładuje aplikację internetową Django. Aby uzyskać więcej informacji na temat sposobu ładowania aplikacji języka Python przez usługę App Service, zobacz [Konfigurowanie wbudowanego obrazu języka Python](how-to-configure-python.md).

Przejdź do *\//\<http: nazwa aplikacji>.azurewebsites.net/admin* i zaloguj się przy użyciu utworzonego użytkownika administratora. Jeśli chcesz, utwórz więcej pytań ankietowych.

![Uruchamianie aplikacji Python Django w usługach app services na platformie Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Gratulacje!** Korzystasz z aplikacji sieci Web Python (Django) w usłudze Azure App Service dla systemu Linux.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Zarządzanie aplikacją w witrynie Azure portal

W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz utworzoną aplikację.

![Przejdź do aplikacji Python Django w witrynie Azure portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Domyślnie portal pokazuje stronę **Przegląd** aplikacji. Ta strona udostępnia widok sposobu działania aplikacji. W tym miejscu można również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywania, ponownego uruchamiania i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Zarządzanie aplikacją Python Django na stronie Przegląd w witrynie Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak zamapować niestandardową nazwę DNS na aplikację:

> [!div class="nextstepaction"]
> [Samouczek: Mapowanie niestandardowej nazwy DNS do aplikacji](../app-service-web-tutorial-custom-domain.md)

Lub sprawdź inne zasoby:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Języka Python](how-to-configure-python.md)
