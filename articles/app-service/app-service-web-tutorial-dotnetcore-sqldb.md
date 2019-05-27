---
title: Platforma ASP.NET Core z usługą SQL Database — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchomić aplikację .NET Core w usłudze Azure App Service z użyciem połączenia z usługą SQL Database.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c83e14d65b30775f0dad54ab9ade1a7bed5ac821
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66139460"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Samouczek: Tworzenie aplikacji platformy ASP.NET Core i SQL Database w usłudze Azure App Service

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć usługę App Service w systemie _Linux_, zobacz [Tworzenie aplikacji platformy .NET Core i usługi SQL Database w usłudze Azure App Service w systemie Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

Usługa [App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie na platformie Azure. W tym samouczku pokazano, jak utworzyć aplikację platformy .NET Core i połączyć ją z usługą SQL Database. Po zakończeniu aplikacja MVC platformy .NET Core będzie działać w usłudze App Service.

![aplikacja działająca w usłudze App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie bazy danych SQL Database na platformie Azure
> * Łączenie aplikacji .NET Core z bazą danych SQL Database
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Zainstaluj oprogramowanie Git](https://git-scm.com/)
* [Zainstaluj program .NET Core](https://www.microsoft.com/net/core/)

## <a name="create-local-net-core-app"></a>Tworzenie lokalnej aplikacji .NET Core

Ten krok umożliwia skonfigurowanie lokalnego projektu platformy .NET Core.

### <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W oknie terminalu dodaj element `cd` do katalogu roboczego.

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium i zmiany jego katalogu głównego.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Przykładowy projekt zawiera podstawową aplikację CRUD (create-read-update-delete, tworzenie-odczytywanie-aktualizowanie-usuwanie) korzystającą z rozwiązania [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom następujące polecenia, aby zainstalować wymagane pakiety, uruchom migracje baz danych i uruchom aplikację.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

W przeglądarce przejdź do adresu `http://localhost:5000`. Wybierz link **Utwórz nowy** i utwórz kilka elementów typu _zadanie do wykonania_.

![pomyślne połączenie z bazą danych SQL Database](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Aby zatrzymać platformę .NET Core w dowolnym momencie, naciśnij kombinację klawiszy `Ctrl+C` w terminalu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Tworzenie produkcyjnej bazy danych SQL Database

W tym kroku utworzysz bazę danych SQL Database na platformie Azure. Aplikacja wdrożona na platformie Azure używa tej bazy danych w chmurze.

W tym samouczku jako baza danych SQL jest używana baza danych [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Tworzenie serwera logicznego bazy danych SQL Database

W usłudze Cloud Shell utwórz serwer logiczny usługi SQL Database za pomocą polecenia [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Zastąp symbol zastępczy *\<nazwa_serwera>* unikatową nazwą bazy danych SQL Database. Ta nazwa jest używana jako część punktu końcowego bazy danych SQL Database, `<server_name>.database.windows.net`, więc nazwa musi być unikatowa na wszystkich serwerach logicznych platformy Azure. Nazwa może zawierać tylko małe litery, cyfry oraz znak łącznika (-) i musi się składać z 3–50 znaków. Ponadto zastąp elementy *\<nazwa_bazy_danych>* i *\<hasło_bazy_danych>* wybraną nazwą użytkownika i hasłem. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Po utworzeniu serwera logicznego SQL Database w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Konfigurowanie reguły zapory serwera

Utwórz [regułę zapory na poziomie serwera usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md) za pomocą polecenia [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-a-database"></a>Tworzenie bazy danych

Utwórz bazę danych [o poziomie wydajności S0](../sql-database/sql-database-service-tiers-dtu.md) na serwerze za pomocą polecenia [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Tworzenie parametrów połączenia

Zastąp poniższe parametry użytym wcześniej ciągiem *\<nazwa_serwera>*, *\<nazwa_bazy_danych>* i *\<hasło_bazy_danych>*.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

To są parametry połączenia dla aplikacji .NET Core. Skopiuj je w celu późniejszego użycia.

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku wdrożysz aplikację .NET Core połączoną z bazą danych SQL Database w usłudze App Service.

### <a name="configure-local-git-deployment"></a>Konfigurowanie lokalnego wdrożenia narzędzia Git

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Konfigurowanie zmiennej środowiskowej

Aby ustawić parametry połączenia dla aplikacji platformy Azure, użyj polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell. W poniższym poleceniu zastąp parametry *\<nazwa_aplikacji>* i *\<parametry_połączenia>* utworzonymi wcześniej parametrami połączenia.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection_string>' --connection-string-type SQLServer
```

Następnie wybierz dla ustawienia aplikacji `ASPNETCORE_ENVIRONMENT` wartość _Produkcja_. To ustawienie umożliwia sprawdzenie, czy pracujesz na platformie Azure, ponieważ używasz języka SQLite w lokalnym środowisku programowania i bazy danych SQL Database w środowisku platformy Azure.

W poniższym przykładzie pokazano konfigurowanie ustawienia aplikacji `ASPNETCORE_ENVIRONMENT` w aplikacji platformy Azure. Zastąp symbol zastępczy *\<nazwa_aplikacji>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>Łączenie z bazą danych SQL Database w środowisku produkcyjnym

W repozytorium lokalnym otwórz plik Startup.cs i znajdź następujący kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Zastąp go poniższym kodem, który używa skonfigurowanych wcześniej zmiennych środowiskowych.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Jeśli kod wykryje, że został uruchomiony w środowisku produkcyjnym (co wskazuje na środowisko platformy Azure), używa skonfigurowanych parametrów połączenia w celu połączenia z bazą danych SQL Database.

Wywołanie `Database.Migrate()` jest pomocne, gdy działa na platformie Azure, ponieważ automatycznie tworzy bazy danych, których potrzebuje aplikacja .NET Core, w oparciu o konfigurację migracji. 

> [!IMPORTANT]
> W przypadku aplikacji produkcyjnych wymagających skalowania w poziomie postępuj zgodnie z najlepszymi rozwiązaniami opisanymi w rozdziale [Stosowanie migracji w środowisku produkcyjnym](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

Zapisz zmiany, a następnie zatwierdź je w repozytorium Git. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do wdrożonej aplikacji w przeglądarce internetowej.

```bash
http://<app_name>.azurewebsites.net
```

Dodaj kilka elementów do wykonania.

![aplikacja działająca w usłudze App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Gratulacje!** Używasz opartej na danych aplikacji .NET Core w usłudze App Service.

## <a name="update-locally-and-redeploy"></a>Lokalne aktualizowanie i ponowne wdrażanie

W tym kroku wprowadzisz zmianę schematu bazy danych i opublikujesz ją na platformie Azure.

### <a name="update-your-data-model"></a>Aktualizowanie modelu danych

Otwórz plik _Models\Todo.cs_ w edytorze kodu. Dodaj następującą właściwość do klasy `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Lokalne uruchamianie migracji Code First

Uruchom kilka poleceń, aby zastosować aktualizacje w lokalnej bazie danych.

```bash
dotnet ef migrations add AddProperty
```

Zaktualizuj lokalną bazę danych:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Używanie nowej właściwości

Wprowadź zmiany kodu, aby użyć właściwości `Done`. Dla uproszczenia w tym samouczku zmienisz tylko widoki `Index` i `Create`, aby zobaczyć, jak działa właściwość.

Otwórz plik _Controllers\TodosController.cs_.

Znajdź metodę `Create([Bind("ID,Description,CreatedDate")] Todo todo)` i dodaj element `Done` do listy właściwości w atrybucie `Bind`. Po zakończeniu podpis metody `Create()` będzie wyglądać podobnie do następującego kodu:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Otwórz plik _Views\Todos\Create.cshtml_.

W kodzie Razor powinien zostać wyświetlony element `<div class="form-group">` dla `Description`, a następnie inny element `<div class="form-group">` dla `CreatedDate`. Bezpośrednio po tych dwóch elementach dodaj kolejny element `<div class="form-group">` dla `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Otwórz plik _Views\Todos\Index.cshtml_.

Wyszukaj pusty element `<th></th>`. Bezpośrednio nad tym elementem dodaj następujący kod Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Znajdź element `<td>` z pomocnikami tagów (`asp-action`). Bezpośrednio nad tym elementem dodaj następujący kod Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

To już wszystko, czego potrzebujesz, aby zobaczyć zmiany w widokach `Index` i `Create`.

### <a name="test-your-changes-locally"></a>Lokalne testowanie zmian

Uruchom aplikację lokalnie.

```bash
dotnet run
```

W przeglądarce przejdź do `http://localhost:5000/`. Teraz możesz dodać element do wykonania i zaznaczyć pole **Gotowe**. Następnie powinien zostać on wyświetlony na stronie głównej jako ukończony. Pamiętaj, że widok `Edit` nie zawiera pola `Done`, ponieważ nie zmieniono widoku `Edit`.

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Po ukończeniu `git push` przejdź do aplikacji usługi App Service i wypróbuj nowe funkcje.

![Aplikacja platformy Azure po zakończeniu migracji Code First](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Nadal wyświetlane są wszystkie istniejące elementy do wykonania. Po ponownym opublikowaniu aplikacji .NET Core dane istniejące w bazie danych SQL Database nie zostaną utracone. Ponadto migracje Entity Framework Core zmieniają tylko schemat danych i pozostawiają istniejące dane bez zmian.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

Gdy aplikacja ASP.NET Core działa w usłudze Azure App Service, dzienniki konsoli można przesłać potokiem do usługi Cloud Shell. W ten sposób można użyć komunikatów diagnostycznych w celu ułatwienia debugowania błędów aplikacji.

Przykładowy projekt jest już zgodny ze wskazówkami dostępnymi w [opisie rejestrowania platformy ASP.NET Core na platformie Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#logging-in-azure) z dwiema zmianami konfiguracji:

- Zawiera odwołanie do `Microsoft.Extensions.Logging.AzureAppServices` w pliku *DotNetCoreSqlDb.csproj*.
- Wywołuje `loggerFactory.AddAzureWebAppDiagnostics()` w pliku *Startup.cs*.

Aby zmienić domyślny [poziom rejestrowania](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) platformy ASP.NET Core `Warning` w usłudze App Service na poziom `Information`, użyj w usłudze Cloud Shell polecenia [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config).

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Poziom rejestrowania projektu jest już ustawiony na `Information` w pliku *appsettings.json*.
> 

Aby rozpocząć przesyłanie strumieniowe dzienników, użyj polecenia [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) w usłudze Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Po rozpoczęciu przesyłania strumieniowego dzienników odśwież aplikację platformy Azure w przeglądarce, aby wywołać pewien ruch w Internecie. Teraz można zobaczyć dzienniki konsoli przesłane potokiem do terminala. Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

Aby w dowolnym momencie zatrzymać przesyłanie strumieniowe dzienników, wpisz `Ctrl`+`C`.

Aby uzyskać więcej informacji na temat dostosowywania dzienników platformy ASP.NET Core, zobacz [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Rejestrowanie na platformie ASP.NET Core).

## <a name="manage-your-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby wyświetlić utworzoną aplikację.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji na platformie Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Domyślnie portal pokazuje stronę **Przegląd** aplikacji. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Tworzenie bazy danych SQL Database na platformie Azure
> * Łączenie aplikacji .NET Core z bazą danych SQL Database
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników z platformy Azure do terminala
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na usługę Azure App Service](app-service-web-tutorial-custom-domain.md)
