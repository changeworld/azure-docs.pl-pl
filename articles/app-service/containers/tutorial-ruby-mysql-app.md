---
title: Tworzenie aplikacji internetowej w języku Ruby i korzystającej z bazy danych MySQL w usłudze Azure App Service w systemie Linux | Microsoft Docs
description: Dowiedz się, jak uruchomić aplikację języka Ruby na platformie Azure z użyciem połączenia z bazą danych MySQL na platformie Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a026eafeb71c67a2cb98c20c4fc5af16073be083
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31789099"
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Tworzenie aplikacji internetowej w języku Ruby i korzystającej z bazy danych MySQL w usłudze Azure App Service w systemie Linux

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym samouczku pokazano, jak utworzyć aplikację internetową w języku Ruby i połączyć ją z bazą danych MySQL. Po zakończeniu będziesz mieć aplikację platformy [Ruby on Rails](http://rubyonrails.org/) uruchomioną w usłudze App Service w systemie Linux.

![Aplikacja platformy Ruby on Rails uruchomiona w usłudze Azure App Service](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych MySQL na platformie Azure
> * Łączenie aplikacji platformy Ruby on Rails z usługą MySQL
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Zainstaluj oprogramowanie Git](https://git-scm.com/)
* [Zainstaluj oprogramowanie Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [Zainstaluj oprogramowanie Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Zainstaluj i uruchom oprogramowanie MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Przygotowywanie lokalnego środowiska MySQL

W tym kroku utworzysz bazę danych na lokalnym serwerze MySQL przeznaczoną do użytku w tym samouczku.

### <a name="connect-to-local-mysql-server"></a>Nawiązywanie połączenia z lokalnym serwerem MySQL

W oknie terminala nawiąż połączenie z lokalnym serwerem MySQL. W tym oknie terminala możesz uruchamiać wszystkie polecenia z tego samouczka.

```bash
mysql -u root -p
```

Jeśli zostanie wyświetlony monit o hasło, wprowadź hasło do konta `root`. Jeśli nie pamiętasz hasła do konta root, zobacz [MySQL: How to Reset the Root Password (MySQL: Jak zresetować hasło konta root)](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Jeśli polecenie zostanie pomyślnie uruchomione, oznacza to, że serwer MySQL działa. Jeśli nie, upewnij się, że lokalny serwer MySQL został uruchomiony, postępując zgodnie z [procedurą poinstalacyjną MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Zakończ połączenie z serwerem, wpisując polecenie `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Tworzenie aplikacji platformy Ruby on Rails lokalnie
W tym kroku uzyskasz przykładową aplikację platformy Ruby on Rails, skonfigurujesz jej połączenie z bazą danych i uruchomisz ją lokalnie. 

### <a name="clone-the-sample"></a>Klonowanie przykładu

W oknie terminalu dodaj element `cd` do katalogu roboczego.

Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

Przy użyciu polecenia `cd` przejdź do sklonowanego katalogu. Zainstaluj wymagane pakiety.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>Konfigurowanie połączenia z serwerem MySQL

W repozytorium otwórz plik _config/database.yml_, a następnie podaj nazwę i hasło użytkownika root lokalnego oprogramowania MySQL (wiersz 13). Jeśli oprogramowanie MySQL zainstalowano przy użyciu narzędzia takiego jak [Homebrew](https://brew.sh/), poświadczenia w pliku są już ustawione na wartości domyślne (czyli `root` i puste hasło).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>Uruchamianie przykładu lokalnie

Uruchom [migracje platformy Rails](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations), aby utworzyć tabele wymagane przez aplikację. Aby zobaczyć, które tabele zostały utworzone w migracjach, zajrzyj do katalogu _db/migrate_ w repozytorium Git.

```bash
rake db:create
rake db:migrate
```

Uruchom aplikację.

```bash
rails server
```

W przeglądarce przejdź do adresu `http://localhost:3000`. Dodaj na stronie kilka zadań.

![Pomyślne połączenie platformy Ruby on Rails z serwerem MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Aby zatrzymać serwer platformy Rails, naciśnij w terminalu klawisze `Ctrl + C`.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Tworzenie bazy danych MySQL na platformie Azure

W tym kroku utworzysz bazę danych MySQL w usłudze [Azure Database for MySQL (wersja zapoznawcza)](/azure/mysql). Następnie skonfigurujesz aplikację platformy Ruby on Rails i połączysz ją z tą bazą danych.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-mysql-server"></a>Tworzenie serwera MySQL

Utwórz serwer w usłudze Azure Database for MySQL (wersja zapoznawcza) przy użyciu polecenia [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

W następującym poleceniu zamień tekst zastępczy _&lt;mysql_server_name>_ na nazwę swojego serwera MySQL (dozwolone znaki to `a-z`, `0-9` i `-`). Ta nazwa jest częścią nazwy hosta serwera MySQL (`<mysql_server_name>.mysql.database.azure.com`) i musi być unikatowa w skali globalnej.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

Po utworzeniu serwera MySQL w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Konfigurowanie zapory serwera

Przy użyciu polecenia [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) utwórz regułę zapory dla serwera MySQL, aby zezwolić na połączenia klientów. Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-mysql-server-locally"></a>Nawiązywanie połączenia z serwerem produkcyjnym MySQL lokalnie

W oknie terminala nawiąż połączenie z serwerem MySQL na platformie Azure. Zamiast ciągu _&lt;mysql_server_name>_ użyj określonej wcześniej wartości.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

Gdy zostanie wyświetlony monit o hasło, wpisz hasło _My5up3r$tr0ngPa$w0rd!_, które określono podczas tworzenia serwera bazy danych.

### <a name="create-a-production-database"></a>Tworzenie produkcyjnej bazy danych

Utwórz bazę danych w wierszu polecenia `mysql`.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Tworzenie użytkownika z uprawnieniami

Utwórz użytkownika bazy danych o nazwie _railsappuser_ i nadaj mu wszystkie uprawnienia w bazie danych `sampledb`.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Zakończ połączenie z serwerem, wpisując polecenie `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Łączenie aplikacji z bazą danych Azure MySQL

W tym kroku połączysz aplikację platformy Ruby on Rails z bazą danych MySQL utworzoną w usłudze Azure Database for MySQL (wersja zapoznawcza).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurowanie połączenia z bazą danych

Otwórz plik _config/database.yml_ w repozytorium. W dolnej części pliku zastąp zmienne produkcyjne następującym kodem. Zamiast tekstu zastępczego _&lt;mysql_server_name>_ użyj nazwy utworzonego serwera MySQL.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

Zapisz zmiany.

> [!NOTE]
> Zostanie dodany element `sslca`, który wskazuje istniejący plik _pem_ w przykładowym repozytorium. Domyślnie usługa Azure Database for MySQL wymusza nawiązywanie połączeń SSL przez klientów. Certyfikat `.pem` umożliwia nawiązywanie połączeń SSL z usługą Azure Database for MySQL. Aby uzyskać więcej informacji, zobacz [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL (Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MySQL)](../../mysql/howto-configure-ssl.md).
>

### <a name="test-the-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

W terminalu lokalnym ustaw następujące zmienne środowiskowe:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Uruchom migracje baz danych platformy Rails z właśnie skonfigurowanymi wartościami produkcyjnymi, aby utworzyć tabele w bazie danych MySQL w usłudze Azure Database for MySQL (wersja zapoznawcza). 

```bash
rake db:migrate RAILS_ENV=production
```

Gdy aplikacja platformy Rails jest uruchamiana w środowisku produkcyjnym, wymaga prekompilowanych zasobów. Wygeneruj wymagane zasoby przy użyciu następującego polecenia:

```bash
rake assets:precompile
```

W środowisku produkcyjnym platformy Rails do zarządzania zabezpieczeniami używane są także wpisy tajne. Wygeneruj klucz tajny.

```bash
rails secret
```

Zapisz ten klucz tajny w określonych zmiennych używanych przez środowisko produkcyjne platformy Rails. Dla wygody możesz używać tego samego klucza dla obu zmiennych.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Włącz w środowisku produkcyjnym platformy Rails udostępnianie plików JavaScript i CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Uruchom przykładową aplikację w środowisku produkcyjnym.

```bash
rails server -e production
```

Przejdź do adresu `http://localhost:3000`. Jeśli strona ładuje się bez błędów, oznacza to, że aplikacja platformy Ruby on Rails nawiązuje połączenie z bazą danych MySQL na platformie Azure.

Dodaj na stronie kilka zadań.

![Pomyślne połączenie platformy Ruby on Rails z usługą Azure Database for MySQL (wersja zapoznawcza)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Aby zatrzymać serwer platformy Rails, naciśnij w terminalu klawisze `Ctrl + C`.

### <a name="commit-your-changes"></a>Zatwierdzanie zmian

Aby zatwierdzić zmiany, uruchom następujące polecenia Git:

```bash
git add .
git commit -m "database.yml updates"
```

Aplikacja jest gotowa do wdrożenia.

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

W tym kroku wdrożysz aplikację platformy Rails połączoną z bazą danych MySQL w usłudze Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji sieci Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Konfigurowanie ustawień bazy danych

W usłudze App Service zmienne środowiskowe ustawia się jako _ustawienia aplikacji_ za pomocą polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) w usłudze Cloud Shell.

Następujące polecenie usługi Cloud Shell konfiguruje ustawienia aplikacji `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` i `DB_PASSWORD`. Zamień teksty zastępcze _&lt;appname>_ i _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Konfigurowanie zmiennych środowiskowych platformy Rails

W terminalu lokalnym wygeneruj nowy klucz tajny dla środowiska produkcyjnego platformy Rails na platformie Azure.

```bash
rails secret
```

Skonfiguruj zmienne wymagane przez środowisko produkcyjne platformy Rails.

W następującym poleceniu usługi Cloud Shell zastąp dwa teksty zastępcze _&lt;output_of_rails_secret>_ nowy kluczem tajnym wygenerowanym w terminalu lokalnym.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

Właściwość `ASSETS_PRECOMPILE="true"` informuje domyślny kontener języka Ruby, aby wstępnie skompilował zasoby przy każdym wdrożeniu narzędzia Git.

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

W terminalu lokalnym dodaj zdalną platformę Azure do lokalnego repozytorium Git.

```bash
git remote add azure <paste_copied_url_here>
```

Wykonaj wypchnięcie na zdalną platformę Azure w celu wdrożenia aplikacji platformy Ruby on Rails. Zostanie wyświetlony monit o podanie hasła określonego wcześniej w ramach tworzenia użytkownika wdrożenia.

```bash
git push azure master
```

Podczas wdrażania usługa Azure App Service przekaże postęp za pomocą narzędzi Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure

Przejdź do adresu `http://<app_name>.azurewebsites.net` i dodaj kilka zadań do listy.

![Aplikacja platformy Ruby on Rails uruchomiona w usłudze Azure App Service](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Gratulacje! Masz uruchomioną opartą na danych aplikację platformy Ruby on Rails w usłudze Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Lokalne aktualizowanie modelu i ponowne wdrażanie

W tym kroku wprowadzisz prostą zmianę w modelu danych `task` i aplikacji internetowej, a następnie opublikujesz tę aktualizację na platformie Azure.

W scenariuszu tego zadania zmodyfikujesz aplikację tak, aby można było oznaczyć zadanie jako zakończone.

### <a name="add-a-column"></a>Dodawanie kolumny

W terminalu przejdź do katalogu głównego repozytorium Git.

Wygeneruj nową migrację, która doda kolumnę logiczną o nazwie `Done` do tabeli `Tasks`:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

To polecenie wygeneruje plik nowej migracji w katalogu _db/migrate_.


W terminalu uruchom migracje baz danych platformy Rails, aby wprowadzić zmianę w lokalnej bazie danych.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Aktualizowanie logiki aplikacji

Otwórz plik *app/controllers/tasks_controller.rb*. Na końcu pliku dodaj następujący wiersz:

```rb
params.require(:task).permit(:Description)
```

Zmodyfikuj ten wiersz tak, aby zawierał nowy parametr `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Aktualizowanie widoków

Otwórz plik *app/views/tasks/_form.html.erb*, który jest formularzem edycji.

Znajdź wiersz `<%=f.error_span(:Description) %>` i wstaw bezpośrednio pod nim następujący kod:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Otwórz plik *app/views/tasks/show.html.erb*, który jest stroną widoku z pojedynczym rekordem. 

Znajdź wiersz `<dd><%= @task.Description %></dd>` i wstaw bezpośrednio pod nim następujący kod:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Otwórz plik *app/views/tasks/index.html.erb*, który jest stroną indeksu dla wszystkich rekordów.

Znajdź wiersz `<th><%= model_class.human_attribute_name(:Description) %></th>` i wstaw bezpośrednio pod nim następujący kod:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

W tym samym pliku znajdź wiersz `<td><%= task.Description %></td>` i wstaw bezpośrednio pod nim następujący kod:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Lokalne testowanie zmian

W terminalu lokalnym uruchom serwer platformy Rails.

```bash
rails server
```

Aby zobaczyć zmianę stanu zadania, przejdź do adresu `http://localhost:3000` i dodaj lub edytuj elementy.

![Dodanie pola wyboru do zadania](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Aby zatrzymać serwer platformy Rails, naciśnij w terminalu klawisze `Ctrl + C`.

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

W terminalu uruchom migracje baz danych platformy Rails dla środowiska produkcyjnego, aby wprowadzić zmianę w bazie danych platformy Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Zatwierdź wszystkie zmiany w narzędziu Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Po zakończeniu wykonywania polecenia `git push` przejdź do aplikacji internetowej platformy Azure i przetestuj nowe funkcje.

![Zmiany w modelu i bazie danych opublikowane na platformie Azure](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Jeśli dodano jakiekolwiek zadania, zostaną one zachowane w bazie danych. Aktualizacje schematu danych pozostawiają dane bez zmian.

## <a name="manage-the-azure-web-app"></a>Zarządzanie aplikacją internetową platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby zarządzać utworzoną aplikacją internetową.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji internetowej platformy Azure.

![Nawigacja w portalu do aplikacji sieci Web platformy Azure](./media/tutorial-php-mysql-app/access-portal.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. W tym miejscu możesz wykonywać podstawowe zadania zarządzania, takie jak zatrzymywanie, uruchamianie, ponowne uruchamianie, przeglądanie i usuwanie.

Menu po lewej stronie zawiera strony służące do konfigurowania aplikacji.

![Strona usługi App Service w witrynie Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych MySQL na platformie Azure
> * Łączenie aplikacji platformy Ruby on Rails z usługą MySQL
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zamapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure Web Apps (Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure)](../app-service-web-tutorial-custom-domain.md)
