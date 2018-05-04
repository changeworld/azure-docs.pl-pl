---
title: Tworzenie aplikacji internetowej języka Java i MySQL na platformie Azure
description: Dowiedz się, jak pobrać aplikację języka Java, która łączy się z usługą bazy danych Azure MySQL działającą w usłudze Azure App Service.
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 0f88ca7c0353c4ab63bf4f6ca5509b0e4504929f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-build-a-java-and-mysql-web-app-in-azure"></a>Samouczek: tworzenie aplikacji internetowej języka Java i MySQL na platformie Azure

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć aplikację App Service w systemie _Linux_, zobacz [Deploy a containerized Spring Boot app to Azure (Wdrażanie konteneryzowanej aplikacji Spring Boot na platformie Azure)](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin).
>

W tym samouczku pokazano, jak utworzyć aplikację internetową języka Java na platformie Azure i połączyć ją z bazą danych MySQL. Po zakończeniu będziesz mieć aplikację [Spring Boot](https://projects.spring.io/spring-boot/) przechowującą dane w usłudze [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) i działającą w usłudze [Azure App Service Web Apps](app-service-web-overview.md).

![Aplikacja Java uruchomiona w usłudze Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych MySQL na platformie Azure
> * Łączenie przykładowej aplikacji z bazą danych
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Monitorowanie aplikacji w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

1. [Pobierz i zainstaluj narzędzie Git](https://git-scm.com/)
1. [Pobierz i zainstaluj zestaw JDK języka Java 7 lub nowszy](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [Pobierz, zainstaluj i uruchom oprogramowanie MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Przygotowywanie lokalnego środowiska MySQL 

W tym kroku na lokalnym serwerze MySQL utworzysz bazę danych, która będzie używana podczas testowania aplikacji lokalnie na maszynie.

### <a name="connect-to-mysql-server"></a>Łączenie z serwerem MySQL

W oknie terminala nawiąż połączenie z lokalnym serwerem MySQL. W tym oknie terminala możesz uruchamiać wszystkie polecenia z tego samouczka.

```bash
mysql -u root -p
```

Jeśli zostanie wyświetlony monit o hasło, wprowadź hasło do konta `root`. Jeśli nie pamiętasz hasła do konta root, zobacz [MySQL: How to Reset the Root Password (MySQL: Jak zresetować hasło konta root)](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Jeśli polecenie zostanie pomyślnie uruchomione, oznacza to, że serwer MySQL już działa. Jeśli nie, upewnij się, że lokalny serwer MySQL został uruchomiony, postępując zgodnie z [procedurą poinstalacyjną MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Tworzenie bazy danych 

W wierszu polecenia `mysql` utwórz bazę danych i tabelę dla elementów do wykonania.

```sql
CREATE DATABASE tododb;
```

Zakończ połączenie z serwerem, wpisując polecenie `quit`.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Tworzenie i uruchamianie przykładowej aplikacji 

W tym kroku sklonujesz przykładową aplikację Spring Boot, skonfigurujesz ją do korzystania z lokalnej bazy danych MySQL i uruchomisz ją na komputerze. 

### <a name="clone-the-sample"></a>Klonowanie przykładu

W oknie terminala przejdź do katalogu roboczego i sklonuj przykładowe repozytorium. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Konfigurowanie aplikacji do korzystania z bazy danych MySQL

Zaktualizuj element `spring.datasource.password` i wartość w pliku *spring-boot-mysql-todo/src/main/resources/application.properties* przy użyciu tego samego hasła konta głównego, którego użyto w wierszu polecenia MySQL:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

Skompiluj i uruchom przykład, używając otoki Maven uwzględnionej w repozytorium:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Otwórz w przeglądarce adres `http://localhost:8080`, aby zobaczyć, jak działa przykład. Podczas dodawania zadań do listy użyj następujących poleceń SQL w wierszu polecenia MySQL, aby wyświetlić dane przechowywane w środowisku MySQL.

```SQL
use testdb;
select * from todo_item;
```

Zatrzymaj aplikację za pomocą kombinacji klawiszy `Ctrl`+`C` w terminalu. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Tworzenie bazy danych Azure MySQL

W tym kroku utworzysz wystąpienie usługi [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). W dalszej części samouczka skonfigurujesz przykładową aplikację do korzystania z tej bazy danych.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [`az group create`](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów, takich jak aplikacje internetowe, bazy danych i konta magazynu, oraz zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów w regionie Europa Północna:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

Aby sprawdzić, jakich wartości można użyć dla lokalizacji `--location`, użyj polecenia[`az appservice list-locations`](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Tworzenie serwera MySQL

W usłudze Cloud Shell utwórz serwer w usłudze Azure Database for MySQL przy użyciu polecenia [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

W poniższym poleceniu zamień symbol zastępczy *\<mysql_name>* na unikatową nazwę serwera, symbol zastępczy *\<admin_user>* na nazwę użytkownika, a symbol zastępczy *\<admin_password>* na hasło. Ta nazwa serwera jest używana jako część punktu końcowego bazy danych PostgreSQL (`https://<mysql_name>.mysql.database.azure.com`), więc nazwa musi być unikatowa na wszystkich serwerach platformy Azure.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name mydemoserver --location "West Europe" --admin-user <admin_user> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

> [!NOTE]
> Ponieważ w tym samouczku używamy kilku poświadczeń, aby uniknąć pomyłek, elementy `--admin-user` i `--admin-password` są ustawiane na wartości zastępcze. Podczas wybierania właściwej nazwy użytkownika i hasła serwera MySQL na platformie Azure w środowisku produkcyjnym należy stosować najlepsze rozwiązania dotyczące bezpieczeństwa.
>
>

Po utworzeniu serwera MySQL w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "additionalProperties": {},
  "administratorLogin": "<admin_user>",
  "earliestRestoreDate": "2018-04-19T22:56:40.990000+00:00",
  "fullyQualifiedDomainName": "<mysql_name>.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "additionalProperties": {},
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

### <a name="configure-server-firewall"></a>Konfigurowanie zapory serwera

W usłudze Cloud Shell przy użyciu polecenia [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) utwórz regułę zapory dla serwera MySQL, aby zezwolić na połączenia klientów. Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](app-service-ip-addresses.md#find-outbound-ips).
>

## <a name="configure-the-azure-mysql-database"></a>Konfigurowanie bazy danych Azure MySQL

W oknie terminala lokalnego nawiąż połączenie z serwerem MySQL na platformie Azure. Zamiast ciągu _&lt;mysql_server_name>_ użyj określonej wcześniej wartości. Gdy zostanie wyświetlone pytanie o hasło, podaj hasło określone podczas tworzenia bazy danych na platformie Azure.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Tworzenie bazy danych 

W wierszu polecenia `mysql` utwórz bazę danych i tabelę dla elementów do wykonania.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Tworzenie użytkownika z uprawnieniami

Utwórz użytkownika bazy danych i nadaj mu wszystkie uprawnienia w bazie danych `tododb`. Zastąp symbole zastępcze `<Javaapp_user>` i `<Javaapp_password>` własną unikatową nazwą aplikacji.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Zakończ połączenie z serwerem, wpisując polecenie `quit`.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Wdrażanie przykładu w usłudze Azure App Service

Utwórz plan usługi Azure App Service w warstwie cenowej **BEZPŁATNA** za pomocą polecenia [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) interfejsu wiersza polecenia. Plan usługi App Service definiuje zasoby fizyczne używane do hostowania aplikacji. Wszystkie aplikacje przypisane do planu usługi App Service współdzielą te zasoby, ograniczając koszt hostowania wielu aplikacji. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Gdy plan jest gotowy, interfejs wiersza polecenia platformy Azure zawiera dane wyjściowe podobne do poniższego przykładu:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Tworzenie aplikacji internetowej platformy Azure

W usłudze Cloud Shell za pomocą polecenia [`az webapp create`](/cli/azure/appservice/web#az_appservice_web_create) interfejsu wiersza polecenia utwórz definicję aplikacji internetowej w planie usługi App Service `myAppServicePlan`. Definicja aplikacji internetowej zawiera adres URL umożliwiający uzyskanie dostępu do aplikacji i konfiguruje kilka opcji wdrażania kodu na platformie Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Zastąp symbol zastępczy `<app_name>` własną unikatową nazwą aplikacji. Ta unikatowa nazwa jest częścią domyślnej nazwy domeny aplikacji internetowej, dlatego musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Niestandardowy wpis nazwy domeny można zmapować na aplikację internetową przed udostępnieniem jej użytkownikom.

Gdy definicja aplikacji internetowej jest gotowa, w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących: 

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

### <a name="configure-java"></a>Konfigurowanie środowiska Java 

W usłudze Cloud Shell ustaw konfigurację środowiska uruchomieniowego języka Java wymaganą przez aplikację przy użyciu polecenia [`az webapp config set`](/cli/azure/webapp/config#az_webapp_config_set).

Następujące polecenie konfiguruje aplikację internetową do uruchamiania razem z zestawem Java 8 JDK i środowiskiem [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Konfigurowanie aplikacji do korzystania z bazy danych Azure SQL Database

Przed uruchomieniem przykładowej aplikacji skonfiguruj ustawienia aplikacji internetowej tak, aby korzystała z bazy danych Azure MySQL utworzonej na platformie Azure. Te właściwości są uwidaczniane w aplikacji internetowej jako zmienne środowiskowe i zastępują wartości ustawione w pliku application.properties wewnątrz pakietu aplikacji internetowej. 

W usłudze Cloud Shell skonfiguruj ustawienia aplikacji przy użyciu polecenia [`az webapp config appsettings`](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) w interfejsie wiersza polecenia:

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>Pobieranie poświadczeń wdrożenia FTP 
Wdrożenie aplikacji na platformie Azure można przeprowadzić na wiele różnych sposobów, w tym z użyciem protokołu FTP, lokalnego narzędzia Git i usługi GitHub oraz usług Visual Studio Team Services i BitBucket. Na przykład protokołu FTP można użyć do wdrożenia utworzonego wcześniej pliku WAR na maszynie lokalnej w usłudze Azure App Service.

Aby określić, które poświadczenia należy przekazać w poleceniu ftp do aplikacji internetowej, użyj polecenia [`az appservice web deployment list-publishing-profiles`](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) w usłudze Cloud Shell: 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>Przekazywanie aplikacji za pomocą protokołu FTP

Użyj ulubionego narzędzia FTP w celu wdrożenia pliku WAR w folderze */site/wwwroot/webapps* do adresu serwera pobranego z pola `URL` w poprzednim poleceniu. Usuń istniejący katalog domyślny (ROOT) aplikacji i zastąp istniejący plik ROOT.war plikiem WAR skompilowanym wcześniej w tym samouczku.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>Testowanie aplikacji internetowej

Przejdź do adresu `http://<app_name>.azurewebsites.net/` i dodaj kilka zadań do listy. 

![Aplikacja Java uruchomiona w usłudze Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Gratulacje!** Używasz opartej na danych aplikacji Java w usłudze Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

Zaktualizuj aplikację, aby na liście zadań do wykonania uwzględnić dodatkową kolumnę dla dnia, dla którego utworzono element. Usługa Spring Boot obsługuje aktualizowanie schematu bazy danych, ponieważ model danych zmienia się bez modyfikowania istniejących rekordów bazy danych.

1. W systemie lokalnym otwórz plik *src/main/java/com/example/fabrikam/TodoItem.java* i dodaj następujące instrukcje importu do klasy:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Dodaj właściwość `String` `timeCreated` do pliku *src/main/java/com/example/fabrikam/TodoItem.java*, inicjując go przy użyciu znacznika czasu podczas tworzenia obiektu. Dodaj metody pobierające/ustawiające właściwości dla nowej właściwości `timeCreated` podczas edytowania tego pliku.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. Zaktualizuj plik *src/main/java/com/example/fabrikam/TodoDemoController.java* przy użyciu wiersza w metodzie `updateTodo`, aby ustawić znacznik czasu:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Dodaj obsługę nowego pola w szablonie `Thymeleaf`. Zaktualizuj plik *src/main/resources/templates/index.html* przy użyciu nowego nagłówka tabeli dla znacznika czasu oraz nowego pola służącego do wyświetlania wartości znacznika czasu w każdym wierszu danych tabeli.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. Ponownie skompiluj aplikację:

    ```bash
    mvnw clean package 
    ```

6. Tak jak poprzednio, za pomocą protokołu FTP przekaż zaktualizowany plik WAR, usuwając istniejący katalog *site/wwwroot/webapps/ROOT* i plik *ROOT.war*, a następnie przekazując zaktualizowany plik WAR jako plik ROOT.war. 

Po odświeżeniu aplikacji kolumna **Godzina utworzenia** będzie widoczna. W przypadku dodawania nowego zadania aplikacja będzie automatycznie wypełniać znacznik czasu. Istniejące zadania pozostaną niezmienione i współdziałają z aplikacją, nawet jeśli odpowiedni model danych został zmieniony. 

![Aplikacja języka Java zaktualizowana przy użyciu nowej kolumny](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych 

Gdy aplikacja Java działa w usłudze Azure App Service, dzienniki konsoli można przesłać potokiem bezpośrednio do terminala. W ten sposób można użyć komunikatów diagnostycznych w celu ułatwienia debugowania błędów aplikacji.

Aby rozpocząć przesyłanie strumieniowe dzienników, użyj polecenia [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) w usłudze Cloud Shell.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Zarządzanie aplikacją internetową platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby wyświetlić utworzoną aplikację internetową.

W lewym menu kliknij pozycję **App Service**, a następnie kliknij nazwę swojej aplikacji sieci Web platformy Azure.

![Nawigacja w portalu do aplikacji sieci Web platformy Azure](./media/app-service-web-tutorial-java-mysql/access-portal.png)

Domyślnie strona aplikacji internetowej zawiera stronę **Przegląd**. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać zadania zarządzania, takie jak zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

Te karty na stronie pokazują wiele doskonałych funkcji, które możesz dodać do aplikacji sieci Web. Poniższa lista zawiera tylko kilka możliwości:
* Mapowanie niestandardowej nazwy DNS
* Tworzenie powiązania niestandardowego certyfikatu SSL
* Konfigurowanie ciągłego wdrażania
* Skalowanie w górę i w dół
* Dodawanie uwierzytelniania użytkownika

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz tych zasobów w innym samouczku (zobacz [Następne kroki](#next)), możesz je usunąć, uruchamiając następujące polecenie w usłudze Cloud Shell: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Następne kroki

> [!div class="checklist"]
> * Tworzenie bazy danych MySQL na platformie Azure
> * Łączenie przykładowej aplikacji Java z programem MySQL
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"] 
> [Map an existing custom DNS name to Azure Web Apps (Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure)](app-service-web-tutorial-custom-domain.md)
