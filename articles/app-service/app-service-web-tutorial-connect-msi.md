---
title: Bezpieczne połączenie SQL Database z zarządzaną tożsamością — Azure App Service | Microsoft Docs
description: Dowiedz się, jak zwiększyć bezpieczeństwo łączności z bazą danych przy użyciu tożsamości zarządzanej, a także jak zastosować ją do innych usług platformy Azure.
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
ms.date: 09/16/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: b39c1596dd16f8ec6235878abdbf37492abd1ea8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177074"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Samouczek: bezpieczne połączenie Azure SQL Database z App Service przy użyciu tożsamości zarządzanej

[App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web na platformie Azure. Zapewnia również [tożsamość zarządzaną](overview-managed-identity.md) dla aplikacji, która jest rozwiązaniem z kluczem do zabezpieczenia dostępu do [Azure SQL Database](/azure/sql-database/) i innych usług platformy Azure. Zarządzane tożsamości w App Service zwiększają bezpieczeństwo aplikacji przez wyeliminowanie wpisów tajnych z aplikacji, takich jak poświadczenia w parametrach połączenia. W tym samouczku dodasz tożsamość zarządzaną do przykładowej aplikacji internetowej skompilowanej w jednym z następujących samouczków: 

- [Samouczek: Tworzenie aplikacji ASP.NET na platformie Azure za pomocą SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Samouczek: Tworzenie aplikacji ASP.NET Core i SQL Database w Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Po zakończeniu Twoja aplikacja Przykładowa będzie łączyć się z SQL Database bezpiecznie bez konieczności podania nazwy użytkownika i hasła.

> [!NOTE]
> Kroki opisane w tym samouczku obsługują następujące wersje:
> 
> - .NET Framework 4.7.2 i nowsze.
> - .NET Core 2,2 lub nowszy.
>

Co uzyskasz:

> [!div class="checklist"]
> * Włącz zarządzane tożsamości
> * Udzielanie SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie Entity Framework do korzystania z uwierzytelniania usługi Azure AD z usługą SQL Database
> * Nawiązywanie połączenia z SQL Database z programu Visual Studio przy użyciu uwierzytelniania usługi Azure AD

> [!NOTE]
>Uwierzytelnianie usługi Azure AD _różni_ się od [zintegrowanego uwierzytelniania systemu Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) w Active Directory lokalnym (AD DS). AD DS i usługa Azure AD używają całkowicie różnych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [dokumentację Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł kontynuuje pracę w miejscu pracy w [samouczku: Tworzenie aplikacji ASP.NET na platformie Azure za pomocą SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) lub [samouczek: Tworzenie ASP.NET Core i aplikacji SQL Database w Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md). Jeśli jeszcze tego nie zrobiono, należy najpierw wykonać jeden z dwóch samouczków. Alternatywnie możesz dostosować kroki dla własnej aplikacji .NET za pomocą SQL Database.

Aby debugować aplikację przy użyciu SQL Database jako zaplecza, upewnij się, że z komputera zezwolono na połączenie z klientem. W przeciwnym razie Dodaj adres IP klienta, wykonując czynności opisane w sekcji [Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu Azure Portal](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Udzielanie dostępu do bazy danych użytkownikowi usługi Azure AD

Najpierw należy włączyć uwierzytelnianie usługi Azure AD, aby SQL Database, przypisując użytkownika usługi Azure AD jako administratora Active Directory serwera SQL Database. Ten użytkownik różni się od konto Microsoft użytego do zarejestrowania się w ramach subskrypcji platformy Azure. Musi to być użytkownik, który został utworzony, zaimportowany, zsynchronizowany lub zaproszony do usługi Azure AD. Aby uzyskać więcej informacji na temat dozwolonych użytkowników usługi Azure AD, zobacz temat [funkcje i ograniczenia usługi Azure AD w SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Jeśli dzierżawa usługi Azure AD nie ma jeszcze użytkownika, utwórz ją, wykonując czynności opisane w sekcji [Dodawanie lub usuwanie użytkowników przy użyciu Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Znajdź identyfikator obiektu użytkownika usługi Azure AD przy użyciu [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) i zastąp *\<user-principal-name >* . Wynik jest zapisywany w zmiennej.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Aby wyświetlić listę wszystkich głównych nazw użytkowników w usłudze Azure AD, uruchom `az ad user list --query [].userPrincipalName`.
>

Dodaj tego użytkownika usługi Azure AD jako administratora Active Directory przy użyciu polecenia [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) w Cloud Shell. W poniższym poleceniu Zastąp *\<server-name >* nazwą serwera SQL Database (bez sufiksu `.database.windows.net`).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Aby uzyskać więcej informacji na temat dodawania administratora Active Directory, zapoznaj się z tematem [udostępnianie Azure Active Directory administratorem dla serwera Azure SQL Database](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Konfigurowanie programu Visual Studio

### <a name="windows"></a>Windows
Program Visual Studio dla systemu Windows jest zintegrowany z uwierzytelnianiem w usłudze Azure AD. Aby włączyć programowanie i debugowanie w programie Visual Studio, Dodaj użytkownika usługi Azure AD w programie Visual Studio, wybierając z menu pozycję **plik** > **Ustawienia konta** , a następnie kliknij pozycję **Dodaj konto**.

Aby ustawić użytkownika usługi Azure AD na potrzeby uwierzytelniania usługi platformy Azure, wybierz pozycję **narzędzia** > **Opcje** z menu, a następnie wybierz pozycję **uwierzytelnianie usługi platformy Azure**@no__t —**wybór konta**. Wybierz dodanego użytkownika usługi Azure AD, a następnie kliknij przycisk **OK**.

Teraz możesz przystąpić do tworzenia i debugowania aplikacji za pomocą SQL Database jako zaplecza przy użyciu uwierzytelniania usługi Azure AD.

### <a name="macos"></a>MacOS

Visual Studio dla komputerów Mac nie jest zintegrowana z uwierzytelnianiem w usłudze Azure AD. Jednak biblioteka [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) , która będzie używana później, może używać tokenów z interfejsu wiersza polecenia platformy Azure. Aby włączyć programowanie i debugowanie w programie Visual Studio, najpierw musisz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na komputerze lokalnym.

Po zainstalowaniu interfejsu wiersza polecenia platformy Azure na komputerze lokalnym Zaloguj się w interfejsie wiersza polecenia platformy Azure przy użyciu następujące polecenie za pomocą użytkownika usługi Azure AD:

```bash
az login --allow-no-subscriptions
```
Teraz możesz przystąpić do tworzenia i debugowania aplikacji za pomocą SQL Database jako zaplecza przy użyciu uwierzytelniania usługi Azure AD.

## <a name="modify-your-project"></a>Modyfikowanie projektu

Kroki opisane w projekcie zależą od tego, czy jest to projekt ASP.NET czy projekt ASP.NET Core.

- [Modyfikuj ASP.NET](#modify-aspnet)
- [Modyfikuj ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modyfikuj ASP.NET

W programie Visual Studio Otwórz konsolę Menedżera pakietów i Dodaj pakiet NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

W pliku *Web. config*pracują w oparciu o górę i wprowadź następujące zmiany:

- W `<configSections>` Dodaj następującą deklarację sekcji:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- poniżej tagu zamykającego `</configSections>` Dodaj następujący kod XML dla `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Znajdź parametry połączenia o nazwie `MyDbConnection` i Zastąp wartość `connectionString` wartością `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Zastąp _\<server-name >_ i _\<db-Name >_ nazwą serwera i nazwą bazy danych.

Jest to każda czynność, którą należy połączyć z SQL Database. Podczas debugowania w programie Visual Studio kod używa użytkownika usługi Azure AD, który został skonfigurowany w konfiguracji programu [Visual Studio](#set-up-visual-studio). Serwer SQL Database zostanie skonfigurowany później, aby umożliwić połączenie z tożsamości zarządzanej aplikacji App Service.

Wpisz `Ctrl+F5`, aby ponownie uruchomić aplikację. Ta sama aplikacja CRUD w przeglądarce jest teraz łącząca się bezpośrednio z Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD. Ta konfiguracja pozwala uruchamiać migracje baz danych z programu Visual Studio.

### <a name="modify-aspnet-core"></a>Modyfikuj ASP.NET Core

W programie Visual Studio Otwórz konsolę Menedżera pakietów i Dodaj pakiet NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

W [samouczku ASP.NET Core i SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)parametry połączenia `MyDbConnection` nie są używane, ponieważ lokalne środowisko programistyczne używa pliku bazy danych programu SQLite, a środowisko produkcyjne platformy Azure używa parametrów połączenia z App Service. W przypadku uwierzytelniania Active Directory w obu środowiskach należy używać tych samych parametrów połączenia. W pliku *appSettings. JSON*Zastąp wartość parametrów połączenia `MyDbConnection`:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

W *Startup.cs*Usuń sekcję kodu dodaną przed:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

I zastąp go następującym kodem:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Następnie podaj kontekst bazy danych Entity Framework z tokenem dostępu dla SQL Database. W *Data\MyDatabaseContext.cs*Dodaj następujący kod wewnątrz nawiasów klamrowych pustego konstruktora `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)`:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> Ten kod demonstracyjny jest synchronicznie do przejrzystości. Aby uzyskać więcej informacji, zobacz [Przewodnik asynchroniczny dla konstruktorów](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

Jest to każda czynność, którą należy połączyć z SQL Database. Podczas debugowania w programie Visual Studio kod używa użytkownika usługi Azure AD, który został skonfigurowany w konfiguracji programu [Visual Studio](#set-up-visual-studio). Serwer SQL Database zostanie skonfigurowany później, aby umożliwić połączenie z tożsamości zarządzanej aplikacji App Service. Klasa `AzureServiceTokenProvider` buforuje token w pamięci i pobiera go z usługi Azure AD tuż przed wygaśnięciem. Nie jest potrzebny żaden kod niestandardowy w celu odświeżenia tokenu.

> [!TIP]
> Jeśli skonfigurowany użytkownik usługi Azure AD ma dostęp do wielu dzierżawców, wywołaj `GetAccessTokenAsync("https://database.windows.net/", tenantid)` z żądanym IDENTYFIKATORem dzierżawy, aby pobrać odpowiedni token dostępu.

Wpisz `Ctrl+F5`, aby ponownie uruchomić aplikację. Ta sama aplikacja CRUD w przeglądarce jest teraz łącząca się bezpośrednio z Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD. Ta konfiguracja pozwala uruchamiać migracje baz danych z programu Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Korzystanie z łączności tożsamości zarządzanej

Następnie skonfigurujesz aplikację App Service, aby połączyć się z SQL Database przy użyciu tożsamości zarządzanej przypisanej do systemu.

### <a name="enable-managed-identity-on-app"></a>Włącz zarządzaną tożsamość w aplikacji

Aby włączyć zarządzaną tożsamość aplikacji platformy Azure, użyj polecenia [AZ webapp Identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) w Cloud Shell. W poniższym poleceniu Zastąp *\<APP-name >* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Oto przykład danych wyjściowych:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Dodawanie tożsamości zarządzanej do grupy usługi Azure AD

Aby udzielić tej tożsamości dostępu do SQL Database, musisz ją dodać do [grupy usługi Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). W Cloud Shell Dodaj go do nowej grupy o nazwie _myAzureSQLDBAccessGroup_, jak pokazano na poniższym skrypcie:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Jeśli chcesz zobaczyć pełne dane wyjściowe JSON dla każdego polecenia, Porzuć parametry `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Przyznawanie uprawnień grupie usługi Azure AD

W Cloud Shell Zaloguj się do SQL Database przy użyciu polecenia SQLCMD. Zastąp _\<server-name >_ nazwą serwera SQL Database, _\<db-Name >_ z nazwą bazy danych używaną przez aplikację, a _@no__t 5aad-user-name >_ i _\<Aad-Password >_ z użytkownikiem usługi Azure AD uwierzytelniające.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

W wierszu polecenia SQL dla bazy danych, uruchom następujące polecenie, aby dodać grupę usługi Azure AD i przyznać uprawnienia wymagane przez aplikację. Na przykład 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Wpisz `EXIT`, aby powrócić do Cloud Shell monitu.

### <a name="modify-connection-string"></a>Modyfikuj parametry połączenia

Należy pamiętać, że te same zmiany wprowadzone w *pliku Web. config* lub *appSettings. JSON* współdziałają z tożsamością zarządzaną, więc jedyną czynnością, którą należy wykonać, jest usunięcie istniejących parametrów połączenia w App Service, które program Visual Studio najpierw stworzył wdrożenie aplikacji pierwszym. Użyj poniższego polecenia, ale Zastąp *\<APP-name >* nazwą aplikacji.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publikowanie zmian

Wszystko, co już teraz, ma publikować zmiany na platformie Azure.

**Jeśli pochodziłeś z [samouczka: tworzenie aplikacji ASP.NET na platformie Azure przy użyciu SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)** , Opublikuj zmiany w programie Visual Studio. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **DotNetAppSqlDb** i wybierz polecenie **Publikuj**.

![Publikowanie z Eksplorator rozwiązań](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stronie publikowanie kliknij pozycję **Publikuj**. 

**Jeśli pochodziłeś z [samouczka: tworzenie aplikacji ASP.NET Core i SQL Database w Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)** , Opublikuj zmiany za pomocą usługi git, używając następujących poleceń:

```bash
git commit -am "configure managed identity"
git push azure master
```

Gdy nowa strona sieci Web Wyświetla listę czynności do wykonania, aplikacja nawiązuje połączenie z bazą danych przy użyciu tożsamości zarządzanej.

![Aplikacja platformy Azure po przeprowadzeniu migracji Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Teraz powinno być możliwe edytowanie listy do wykonania tak jak wcześniej.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Informacje, które uczysz:

> [!div class="checklist"]
> * Włącz zarządzane tożsamości
> * Udzielanie SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie Entity Framework do korzystania z uwierzytelniania usługi Azure AD z usługą SQL Database
> * Nawiązywanie połączenia z SQL Database z programu Visual Studio przy użyciu uwierzytelniania usługi Azure AD

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację sieci Web.

> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](app-service-web-tutorial-custom-domain.md)
