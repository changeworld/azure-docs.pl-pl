---
title: Python (Django) z bazą danych PostgreSQL w systemie Linux — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchomić opartą na danych aplikację napisaną w języku Python na platformie Azure z użyciem połączenia z bazą danych PostgreSQL. Django jest używany w tym samouczku.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: b3d262a33ecbc35ada278019ee0998486bc92efe
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678925"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>Tworzenie aplikacji przy użyciu języka Python i bazy danych PostgreSQL w usłudze Azure App Service

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. W tym samouczku przedstawiono sposób tworzenia opartej na danych aplikacji w języku Python z użyciem bazy danych PostgreSQL jako wewnętrznej bazy danych. Po zakończeniu aplikacja Django będzie działała w usłudze App Service w systemie Linux.

![Aplikacja Django w usłudze App Service w systemie Linux](./media/tutorial-python-postgresql-app/django-admin-azure.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych PostgreSQL na platformie Azure
> * Łączenie aplikacji w języku Python z bazą danych PostgreSQL
> * Wdrażanie aplikacji na platformie Azure
> * Wyświetlanie dzienników diagnostycznych
> * Zarządzanie aplikacją w witrynie Azure Portal

> [!NOTE]
> Przed utworzeniem bazy danych Azure Database for PostgreSQL sprawdź, [która generacja obliczeń jest dostępna w Twoim regionie](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

Kroki opisane w tym artykule można wykonać w systemie macOS. Instrukcje dotyczące systemów Linux i Windows są takie same w większości przypadków, ale występujące różnice nie są szczegółowo opisane w tym samouczku.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
2. [Zainstaluj język Python](https://www.python.org/downloads/)
3. [Zainstaluj i uruchom serwer PostgreSQL](https://www.postgresql.org/download/)

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

Utwórz bazę danych o nazwie *pollsdb* i skonfiguruj oddzielnego użytkownika bazy danych o nazwie *manager* z hasłem *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Tworzenie lokalnej aplikacji w języku Python

Ten krok umożliwia skonfigurowanie lokalnego projektu Python Django.

### <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

Otwórz okno terminalu i za pomocą polecenia `CD` przejdź do katalogu roboczego.

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

To przykładowe repozytorium zawiera aplikację [Django](https://www.djangoproject.com/). Jest to ta sama aplikacja oparta na danych, którą by otrzymano, wykonując czynności opisane w [samouczku z wprowadzeniem w dokumentacji platformy Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Ten samouczek nie uczy obsługi platformy Django, ale pokazuje, jak wdrożyć i uruchomić aplikację Django (lub inną aplikację języka Python opartą na danych) w usłudze App Service.

### <a name="configure-environment"></a>Konfigurowanie środowiska

Utwórz środowisko wirtualne języka Python, a następnie ustaw ustawienia połączenia bazy danych przy użyciu skryptu.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

Zmienne środowiskowe zdefiniowane w plikach *env.sh* i *env.ps1* są używane w pliku _azuresite/settings.py_ do definiowania ustawień bazy danych.

### <a name="run-app-locally"></a>Uruchamianie aplikacji lokalnie

Zainstaluj wymagane pakiety, [uruchom migracje platformy Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) i [utwórz administratora](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

Po utworzeniu administratora uruchom serwer Django.

```bash
python manage.py runserver
```

Po całkowitym załadowaniu aplikacji zostanie wyświetlony komunikat podobny do następującego:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

W przeglądarce przejdź do adresu `http://localhost:8000`. Powinien zostać wyświetlony komunikat `No polls are available.`. 

Przejdź pod adres `http://localhost:8000/admin` i zaloguj się przy użyciu konta administratora utworzonego w poprzednim kroku. Kliknij pozycję **Dodaj** obok pozycji **Pytania** i utwórz pytanie ankiety z kilkoma opcjami wyboru.

![Działająca lokalnie aplikacja platformy Django](./media/tutorial-python-postgresql-app/django-admin-local.png)

Przejdź ponownie pod adres `http://localhost:8000` i zobacz wyświetlane pytanie ankiety.

Przykładowa aplikacja platformy Django przechowuje dane użytkowników w bazie danych. Po pomyślnym dodaniu pytania ankiety aplikacja będzie zapisywała dane w lokalnej bazie danych PostgreSQL.

Aby w dowolnym momencie zatrzymać serwer Django, naciśnij klawisze Ctrl+C w terminalu.

## <a name="create-a-production-postgresql-database"></a>Tworzenie produkcyjnej bazy danych PostgreSQL

W tym kroku utworzysz bazę danych PostgreSQL na platformie Azure. Aplikacja wdrożona na platformie Azure używa tej bazy danych w chmurze.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Za pomocą polecenia [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) utwórz serwer PostgreSQL w usłudze Cloud Shell.

W poniższym przykładzie polecenia Zastąp  *\<postgresql-name >* z unikatową nazwę serwera i Zastąp  *\<admin-username >* i  *\<hasło administratora >* za pomocą poświadczeń odpowiedniego użytkownika. Poświadczenia użytkownika są dla konta administratora bazy danych. Ta nazwa serwera jest używana jako część punktu końcowego bazy danych PostgreSQL (`https://<postgresql-name>.postgres.database.azure.com`), więc nazwa musi być unikatowa na wszystkich serwerach platformy Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

Po utworzeniu serwera usługi Azure Database for PostgreSQL w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
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
> Należy pamiętać, \<admin-username > i \<hasło administratora > na później. Będą one potrzebne do zalogowania się do serwera Postgre i jego baz danych.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Tworzenie reguł zapory dla serwera PostgreSQL

W usłudze Cloud Shell uruchom następujące polecenia interfejsu wiersza polecenia platformy Azure, aby zezwolić na dostęp do bazy danych z zasobów platformy Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> To ustawienie umożliwia nawiązywanie połączeń sieciowych ze wszystkich adresów IP w sieci platformy Azure. Na potrzeby użytku produkcyjnego spróbuj skonfigurować możliwie najbardziej restrykcyjne reguły zapory, [używając tylko adresów IP ruchu wychodzącego wykorzystywanych przez Twoją aplikację](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

W usłudze Cloud Shell Uruchom polecenie ponownie, aby zezwolić na dostęp z komputera lokalnego, zastępując  *\<Twój adres ip >* z [lokalnego adresu IP protokołu IPv4](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Łączenie aplikacji w języku Python z produkcyjną bazą danych

W tym kroku połączysz przykładową aplikację platformy Django z utworzonym serwerem usługi Azure Database for PostgreSQL.

### <a name="create-empty-database-and-user-access"></a>Tworzenie pustej bazy danych oraz dostępu użytkownika

W usłudze Cloud Shell nawiąż połączenie z bazą danych, uruchamiając poniższe polecenie. Po wyświetleniu monitu o hasło administratora użyj tego samego hasła, które zostało określone w sekcji [Tworzenie serwera usługi Azure Database for PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Tak jak w przypadku lokalnego serwera Postgres, utwórz bazę danych i użytkownika na serwerze Postgres na platformie Azure.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Wpisz polecenie `\q`, aby zamknąć klienta PostgreSQL.

> [!NOTE]
> To najlepsze rozwiązanie umożliwiające tworzenie użytkowników bazy danych z ograniczonymi uprawnieniami do wybranych aplikacji, zamiast korzystania z roli administratora. W tym przykładzie użytkownik `manager` ma pełne uprawnienia _tylko_ do bazy danych `pollsdb`.

### <a name="test-app-connectivity-to-production-database"></a>Testowanie łączności aplikacji z produkcyjną bazą danych

W lokalnym oknie terminalu zmień zmienne środowiskowe bazy danych (które skonfigurowano wcześniej, uruchamiając plik *env.sh* lub *env.ps1*):

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Uruchom migrację platformy Django do bazy danych platformy Azure i utwórz administratora.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Po utworzeniu administratora uruchom serwer Django.

```bash
python manage.py runserver
```

Przejdź ponownie pod adres `http://localhost:8000`. Powinien zostać ponownie wyświetlony komunikat `No polls are available.`. 

Przejdź pod adres `http://localhost:8000/admin` i zaloguj się przy użyciu utworzonego konta administratora, a następnie utwórz pytanie ankiety jak wcześniej.

![Działająca lokalnie aplikacja platformy Python Django](./media/tutorial-python-postgresql-app/django-admin-local.png)

Przejdź ponownie pod adres `http://localhost:8000` i zobacz wyświetlane pytanie ankiety. Aplikacja zapisuje teraz dane w bazie danych na platformie Azure.

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

W tym kroku wdrożysz aplikację języka Python połączoną z bazą danych Postgres w usłudze Azure App Service.

### <a name="configure-repository"></a>Konfigurowanie repozytorium

Platforma Django weryfikuje nagłówek `HTTP_HOST` w żądaniach przychodzących. Aby aplikacja Django działała w usłudze App Service, musisz dodać w pełni kwalifikowaną nazwę domeny aplikacji do dozwolonych hostów. Otwórz plik _azuresite/settings.py_ i znajdź ustawienie `ALLOWED_HOSTS`. Zmień ten wiersz na:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Platforma Django nie obsługuje [dostarczania plików statycznych w środowisku produkcyjnym](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/), więc musisz włączyć tę funkcję ręcznie. W tym samouczku używany jest pakiet [WhiteNoise](https://whitenoise.evans.io/en/stable/). Pakiet WhiteNoise jest już uwzględniony w pliku _requirements.txt_. Wystarczy skonfigurować platformę Django, aby z niego korzystać. 

W pliku _azuresite/settings.py_ znajdź ustawienie `MIDDLEWARE` i dodaj oprogramowanie pośredniczące `whitenoise.middleware.WhiteNoiseMiddleware` do listy, tuż pod oprogramowaniem pośredniczącym `django.middleware.security.SecurityMiddleware`. Ustawienie `MIDDLEWARE` powinno wyglądać następująco:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

Na końcu pliku _azuresite/settings.py_ dodaj następujące wiersze.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Aby uzyskać więcej informacji na temat konfigurowania pakietu WhiteNoise, zobacz [dokumentację pakietu WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Sekcja ustawień bazy danych jest już zgodna z najlepszym rozwiązaniem z zakresu bezpieczeństwa polegającym na używaniu zmiennych środowiskowych. Aby uzyskać zalecenia dotyczące całego procesu wdrażania, zobacz [Django Documentation: deployment checklist](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) (Dokumentacja platformy Django: lista kontrolna dotycząca wdrażania).

Zatwierdź zmiany w repozytorium.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Wcześniej w tym samouczku zdefiniowano zmienne środowiskowe na potrzeby nawiązywania połączeń z bazą danych PostgreSQL.

W usłudze App Service zmienne środowiskowe ustawia się jako _ustawienia aplikacji_ za pomocą polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell.

W poniższym przykładzie określono szczegóły połączenia z bazą danych jako ustawienia aplikacji. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Aby uzyskać informacji na temat sposobu te ustawienia aplikacji są dostępne w kodzie, zobacz [dostęp do zmiennych środowiskowych](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

Serwer wdrażania usługi App Service widzi plik _requirements.txt_ w katalogu głównym repozytorium i automatycznie uruchamia zarządzanie pakietami języka Python po wykonaniu polecenia `git push`.

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do wdrożonej aplikacji. Uruchomienie przy pierwszym żądaniu aplikacji może trochę potrwać, ponieważ wymaga pobrania i uruchomienia kontenera. Jeśli strona przekroczy limit czasu lub wyświetli komunikat o błędzie, odczekaj kilka minut i odśwież stronę.

```bash
http://<app-name>.azurewebsites.net
```

Powinno zostać wyświetlone utworzone wcześniej pytanie ankiety. 

Usługa App Service wykrywa projekt Django w repozytorium, wyszukując w poszczególnych podkatalogach plik _wsgi.py_ utworzony domyślnie przez element `manage.py startproject`. Po znalezieniu pliku ładuje aplikację Django. Aby uzyskać więcej informacji na temat sposobu ładowania aplikacji języka Python przez usługę App Service, zobacz [Konfigurowanie wbudowanego obrazu języka Python](how-to-configure-python.md).

Przejdź do strony `<app-name>.azurewebsites.net` i zaloguj się przy użyciu tego samego konta administratora, które utworzono wcześniej. Jeśli chcesz, spróbuj utworzyć więcej pytań ankiety.

![Działająca lokalnie aplikacja platformy Python Django](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Gratulacje!** Twoja aplikacja języka Python działa w usłudze App Service dla systemu Linux.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby wyświetlić utworzoną aplikację.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji na platformie Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Domyślnie portal pokazuje stronę **Przegląd** aplikacji. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych PostgreSQL na platformie Azure
> * Łączenie aplikacji w języku Python z bazą danych PostgreSQL
> * Wdrażanie aplikacji na platformie Azure
> * Wyświetlanie dzienników diagnostycznych
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: Mapowanie niestandardowej nazwy DNS na aplikację](../app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji języka Python](how-to-configure-python.md)