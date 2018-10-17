---
title: Zabezpieczanie połączenia usługi Azure SQL Database z usługi App Service za pomocą tożsamości zarządzanej | Microsoft Docs
description: Dowiedz się, jak zabezpieczyć połączenie z bazą danych za pomocą tożsamości zarządzanej, a także jak zastosować ją do innych usług platformy Azure.
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
ms.date: 04/17/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 3125db03dc13f70524fd094736f50b563ef712a4
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379931"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Samouczek: zabezpieczanie połączenia usługi Azure SQL Database z usługi App Service za pomocą tożsamości zarządzanej

Usługa [App Service](app-service-web-overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie na platformie Azure. Zapewnia także [tożsamość zarządzaną](app-service-managed-service-identity.md) dla aplikacji, czyli gotowe rozwiązanie do zabezpieczania dostępu do usługi [Azure SQL Database](/azure/sql-database/) i innych usług platformy Azure. Tożsamości zarządzane w usłudze App Service zwiększają bezpieczeństwo aplikacji przez wyeliminowanie wpisów tajnych z aplikacji, takich jak poświadczenia w parametrach połączenia. W tym samouczku dodasz tożsamość zarządzaną do przykładowej aplikacji internetowej ASP.NET skompilowanej w artykule [Samouczek: tworzenie aplikacji ASP.NET na platformie Azure przy użyciu usługi SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Po zakończeniu Twoja przykładowa aplikacja będzie bezpiecznie łączyć się z usługą SQL Database bez konieczności podawania nazwy użytkownika i hasła.

Omawiane kwestie:

> [!div class="checklist"]
> * Włączanie tożsamości zarządzanych
> * Udzielanie usłudze SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie kodu aplikacji do uwierzytelniania w usłudze SQL Database przy użyciu uwierzytelniania usługi Azure Active Directory
> * Przyznawanie minimalnych uprawnień tożsamości zarządzanej w usłudze SQL Database

> [!NOTE]
> Uwierzytelnianie usługi Azure Active Directory _różni się_ od [zintegrowanego uwierzytelniania systemu Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) w lokalnej usłudze Active Directory (AD DS). W usługach AD DS i Azure Active Directory są używane całkowicie różne protokoły uwierzytelniania. Aby uzyskać więcej informacji, zobacz [The difference between Windows Server AD DS and Azure AD (Różnice między usługą AD DS w systemie Windows Server a usługą Azure AD)](../active-directory/fundamentals/understand-azure-identity-solutions.md#the-difference-between-windows-server-ad-ds-and-azure-ad).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest kontynuacją samouczka [Samouczek: tworzenie aplikacji ASP.NET na platformie Azure przy użyciu usługi SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Jeśli jeszcze tego nie zrobiono, należy najpierw ukończyć ten samouczek. Alternatywnie można dostosować kroki do własnej aplikacji ASP.NET w usłudze SQL Database.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-identities"></a>Włączanie tożsamości zarządzanych

Aby włączyć tożsamość zarządzanej dla aplikacji platformy Azure, użyj polecenia [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) w usłudze Cloud Shell. W poniższym poleceniu zastąp ciąg *\<nazwa aplikacji>*.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Oto przykładowe dane wyjściowe po utworzeniu tożsamości w usłudze Azure Active Directory:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

Wartości atrybutu `principalId` użyjesz w następnym kroku. Jeśli chcesz wyświetlić szczegóły nowej tożsamości w usłudze Azure Active Directory, uruchom następujące polecenie opcjonalne z wartością `principalId`:

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>Udzielanie tożsamości dostępu do bazy danych

Następnie należy przyznać tożsamości zarządzanej aplikacji dostęp do bazy danych, używając polecenia [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin_create) w usłudze Cloud Shell. W poniższym poleceniu zastąp ciągi *\<nazwa_serwera >* i <principalid_from_last_step>. Wpisz nazwę administratora dla parametru *\<administrator>*.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

Tożsamość zarządzana ma teraz dostęp do serwera usługi Azure SQL Database.

## <a name="modify-connection-string"></a>Modyfikowanie parametrów połączenia

Aby zmodyfikować połączenie wcześniej ustawione dla aplikacji, użyj polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w usłudze Cloud Shell. W poniższym poleceniu zastąp ciąg *\<nazwa aplikacji>* nazwą aplikacji i zastąp ciągi *\<nazwa_serwera>* i *\<nazwa_bazy_danych>*  wartościami odpowiednimi dla swojej usługi SQL Database.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Modyfikowanie kodu platformy ASP.NET

W projekcie **DotNetAppSqlDb** w programie Visual Studio otwórz plik _packages.config_ i dodaj poniższy wiersz do listy pakietów.

```xml
<package id="Microsoft.Azure.Services.AppAuthentication" version="1.1.0-preview" targetFramework="net461" />
```

Otwórz plik _Models\MyDatabaseContext.cs_ i na początku pliku dodaj następujące instrukcje `using`:

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

W klasie `MyDatabaseContext` dodaj następujący konstruktor:

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

Ten konstruktor konfiguruje niestandardowy obiekt SqlConnection w celu używania tokenu dostępu do usługi Azure SQL Database z usługi App Service. Token dostępu jest używany przez aplikację usługi App Service do uwierzytelniania w usłudze Azure SQL Database za pomocą jej tożsamości zarządzanej. Aby uzyskać więcej informacji, zobacz artykuł [Obtaining tokens for Azure resources](app-service-managed-service-identity.md#obtaining-tokens-for-azure-resources) (Uzyskiwanie tokenów dla zasobów platformy Azure). Instrukcja `if` pozwala kontynuować testowanie aplikacji lokalnie za pomocą bazy LocalDB.

> [!NOTE]
> Właściwość `SqlConnection.AccessToken` jest obecnie obsługiwana tylko w programie .NET Framework 4.6 lub nowszym, nie na platformie [.NET Core](https://www.microsoft.com/net/learn/get-started/windows).
>

Aby użyć tego nowego konstruktora, otwórz plik `Controllers\TodosController.cs` i znajdź wiersz `private MyDatabaseContext db = new MyDatabaseContext();`. Istniejący kod używa domyślnego kontrolera `MyDatabaseContext` do tworzenia bazy danych przy użyciu standardowych parametrów połączenia, które zawierały nazwę użytkownika i hasła w postaci zwykłego tekstu, zanim [zostało to zmienione](#modify-connection-string).

Zastąp cały wiersz poniższym kodem:

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new SqlConnection());
```

### <a name="publish-your-changes"></a>Publikowanie zmian

Teraz pozostało tylko opublikowanie zmian na platformie Azure.

W obszarze **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **DotNetAppSqlDb** i wybierz polecenie **Publikuj**.

![Publikowanie z Eksploratora rozwiązań](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stronie publikowania kliknij przycisk **Publikuj**. Gdy nowa strona internetowa wyświetla listę zadań do wykonania, aplikacja nawiązuje połączenie z bazą danych za pomocą tożsamości zarządzanej.

![Aplikacja internetowa platformy Azure po zakończeniu migracji Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Teraz powinno być możliwe edytowanie listy zadań do wykonania tak jak wcześniej.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Udzielanie tożsamości minimalnych uprawnień

W poprzednich krokach można zauważyć, że tożsamość zarządzana jest połączona z programem SQL Server jako administrator usługi Azure AD. Aby przyznać tożsamości zarządzanej minimalne uprawnienia, należy zalogować się na serwerze usługi Azure SQL Database jako administrator usługi Azure AD, a następnie dodać grupę usługi Azure Active Directory zawierającą tożsamość zarządzaną. 

### <a name="add-managed-identity-to-an-azure-active-directory-group"></a>Dodawanie tożsamości zarządzanej do grupy usługi Azure Active Directory

W usłudze Cloud Shell dodaj tożsamość zarządzaną aplikacji do nowej grupy usługi Azure Active Directory o nazwie _myAzureSQLDBAccessGroup_, jak pokazano w poniższym skrypcie:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Jeśli chcesz wyświetlić pełne dane wyjściowe JSON dla każdego polecenia, porzuć parametry `--query objectId --output tsv`.

### <a name="reconfigure-azure-ad-administrator"></a>Ponowne konfigurowanie administratora usługi Azure AD

Wcześniej przypisano tożsamość zarządzaną jako administrator usługi Azure AD dla usługi SQL Database. Tej tożsamości nie można użyć do logowania interaktywnego (aby dodać użytkowników bazy danych), więc należy użyć rzeczywistego użytkownika usługi Azure AD. Aby dodać użytkownika usługi Azure AD, wykonaj kroki opisane w temacie [Provision an Azure Active Directory administrator for your Azure SQL Database Server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) (Udostępnianie administratora usługi Azure Active Directory dla serwera usługi Azure SQL Database). 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Nadawanie uprawnień grupie usługi Azure Active Directory

W usłudze Cloud Shell zaloguj się do usługi SQL Database przy użyciu polecenia SQLCMD. Zastąp ciąg _\<servername >_ nazwą serwera usługi SQL Database i zastąp ciągi _\<AADusername >_ i _\<AADpassword>_ poświadczeniami użytkownika usługi Azure AD.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

W wierszu polecenia usługi SQL uruchom następujące polecenia, aby dodać grupę usługi Azure Active Directory utworzoną wcześniej jako użytkownik.

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
GO
```

Wpisz polecenie `EXIT`, aby powrócić do wiersza polecenia usługi Cloud Shell. Następnie ponownie uruchom polecenie SQLCMD, ale określ nazwę bazy danych w elemencie _\<dbname>_.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

W wierszu polecenia usługi SQL dla bazy danych, uruchom następujące polecenia, aby udzielić uprawnień do odczytu i zapisu grupie Azure Active Directory.

```sql
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Włączanie tożsamości zarządzanych
> * Udzielanie usłudze SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie kodu aplikacji do uwierzytelniania w usłudze SQL Database przy użyciu uwierzytelniania usługi Azure Active Directory
> * Przyznawanie minimalnych uprawnień tożsamości zarządzanej w usłudze SQL Database

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure Web Apps (Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure)](app-service-web-tutorial-custom-domain.md)
