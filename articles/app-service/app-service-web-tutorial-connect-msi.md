---
title: Zabezpieczanie połączenia usługi SQL Database za pomocą tożsamości zarządzanej — Azure App Service | Microsoft Docs
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
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481010"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Samouczek: Zabezpieczanie połączenia usługi Azure SQL Database z usługi App Service za pomocą tożsamości zarządzanej

Usługa [App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie na platformie Azure. Zapewnia także [tożsamość zarządzaną](overview-managed-identity.md) dla aplikacji, czyli gotowe rozwiązanie do zabezpieczania dostępu do usługi [Azure SQL Database](/azure/sql-database/) i innych usług platformy Azure. Tożsamości zarządzane w usłudze App Service zwiększają bezpieczeństwo aplikacji przez wyeliminowanie wpisów tajnych z aplikacji, takich jak poświadczenia w parametrach połączenia. W tym samouczku dodasz tożsamość zarządzaną do przykładowej aplikacji internetowej ASP.NET skompilowanej w artykule [Samouczek: tworzenie aplikacji ASP.NET na platformie Azure przy użyciu usługi SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Po zakończeniu Twoja przykładowa aplikacja będzie bezpiecznie łączyć się z usługą SQL Database bez konieczności podawania nazwy użytkownika i hasła.

> [!NOTE]
> Ten scenariusz jest obecnie obsługiwane przez program .NET Framework 4.7.2 lub nowszym. Program [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) obsługuje scenariusz, ale nie jest jeszcze dołączony do domyślnych obrazów w usłudze App Service. 
>

Omawiane kwestie:

> [!div class="checklist"]
> * Włączanie tożsamości zarządzanych
> * Udzielanie usłudze SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie platformy Entity Framework, aby użyć uwierzytelniania usługi Azure AD z usługą SQL Database
> * Łączenie bazy danych SQL w programie Visual Studio przy użyciu uwierzytelniania usługi Azure AD

> [!NOTE]
>Uwierzytelnianie usługi Azure AD jest _różnych_ z [uwierzytelniania zintegrowanego Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) w lokalnej usłudze Active Directory (AD DS). Usługi AD DS a usługą Azure AD należy użyć całkowicie różnych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [dokumentację usług Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule kontynuowana jest praca rozpoczęta w artykule [Samouczek: Tworzenie aplikacji ASP.NET na platformie Azure przy użyciu usługi SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md). Jeśli jeszcze tego nie zrobiono, należy najpierw ukończyć ten samouczek. Alternatywnie można dostosować kroki do własnej aplikacji ASP.NET w usłudze SQL Database.

Aby debugować aplikację przy użyciu bazy danych SQL jako zaplecza, upewnij się, że masz [dozwolone połączenia klienckie z komputera](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Użytkownika usługi AD Azure udzielanie dostępu do bazy danych

Najpierw należy włączyć uwierzytelnianie usługi Azure AD do usługi SQL Database, przypisując użytkownika usługi Azure AD jako administratora usługi Active Directory serwera bazy danych SQL. Ten użytkownik jest inny niż konta Microsoft, którego użyto podczas tworzenia konta dla subskrypcji platformy Azure. Musi być użytkownika, który utworzone, zaimportowane, zsynchronizowane lub zaproszenie do usługi Azure AD. Dozwolone użytkowników usługi Azure AD, można znaleźć więcej informacji na temat [funkcji usługi Azure AD i ograniczenia w usłudze SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). 

Znajdź identyfikator obiektu użytkownika usługi Azure AD za pomocą [ `az ad user list` ](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) i Zastąp  *\<użytkownika principal-name >* . Wynik zostanie zapisany do zmiennej.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Aby wyświetlić listę wszystkich głównych nazw użytkowników w usłudze Azure AD, uruchom `az ad user list --query [].userPrincipalName`.
>

Dodaj tego użytkownika usługi Azure AD jako administratora usługi Active Directory przy użyciu [ `az sql server ad-admin create` ](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) polecenia w usłudze Cloud Shell. W poniższym poleceniu zastąp  *\<nazwa serwera >* .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Aby uzyskać więcej informacji na temat dodawania administratora usługi Active Directory, zobacz [aprowizacji administrator usługi Azure Active Directory dla serwera bazy danych SQL Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Konfigurowanie programu Visual Studio

Aby włączyć programowania i debugowania w programie Visual Studio, Dodaj użytkownika usługi Azure AD w programie Visual Studio, wybierając **pliku** > **ustawienia konta** menu, a następnie kliknij **Dodaj konto**.

Aby ustawić użytkownika usługi Azure AD do uwierzytelniania usługi platformy Azure, wybierz **narzędzia** > **opcje** z menu polecenie **uwierzytelnianie usługi Azure**  >  **Wybór konta**. Wybierz użytkownika usługi Azure AD został dodany, a następnie kliknij przycisk **OK**.

Możesz teraz przystąpić do tworzenia i debugowania aplikacji przy użyciu bazy danych SQL jako zaplecza, przy użyciu uwierzytelniania usługi Azure AD.

## <a name="modify-aspnet-project"></a>Modyfikowanie projektu programu ASP.NET

W programie Visual Studio otwórz konsolę menedżera pakietów, a następnie dodaj pakiet NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

W *Web.config*, pracujesz w górnej części pliku i wprowadź następujące zmiany:

- W `<configSections>`, dodaj następującą deklarację sekcji w nim:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- poniżej zamykającym `</configSections>` tag, Dodaj następujący kod XML dla `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Znajdź parametry połączenia o nazwie `MyDbConnection` i zastąp jego `connectionString` wartością `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Zastąp  _\<nazwa serwera >_ i  _\<Nazwa bazy danych >_ nazwą swojego serwera i nazwę bazy danych.

Typ `Ctrl+F5` ponownie uruchomić aplikację. Ta sama aplikacja CRUD w przeglądarce jest teraz połączenie z usługi Azure SQL Database bezpośrednio przy użyciu uwierzytelniania usługi Azure AD. Ta konfiguracja pozwala Uruchom migracje baz danych. Nowszym w przypadku wdrożenia zmiany w usłudze App Service, tego samego ustawienia współdziałają z tożsamości zarządzanej aplikacji.

## <a name="use-managed-identity-connectivity"></a>Użyj połączenia tożsamości zarządzanej

Następnie należy skonfigurować aplikację usługi App Service, aby nawiązać połączenie z bazą danych SQL za pomocą tożsamości zarządzanej przypisana przez system.

### <a name="enable-managed-identity-on-app"></a>Włączanie tożsamości zarządzanej aplikacji

Aby włączyć tożsamość zarządzanej dla aplikacji platformy Azure, użyj polecenia [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) w usłudze Cloud Shell. W poniższym poleceniu zastąp  *\<Nazwa aplikacji >* .

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

Aby udzielić dostępu tożsamości do usługi SQL Database, należy dodać go do [grupy usługi Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). W usłudze Cloud Shell Dodaj go do nowej grupy o nazwie _myAzureSQLDBAccessGroup_, jak pokazano w poniższym skrypcie:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Jeśli chcesz wyświetlić pełne dane wyjściowe JSON dla każdego polecenia, porzuć parametry `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Udziel uprawnień do grupy usługi Azure AD

W usłudze Cloud Shell zaloguj się do usługi SQL Database przy użyciu polecenia SQLCMD. Zastąp  _\<nazwa serwera >_ z Twoją nazwą serwera SQL Database  _\<Nazwa bazy danych >_ z bazą danych nazwy używany przez aplikację i  _\< nazwy użytkownika usługi AAD >_ i  _\<hasło usługi aad >_ przy użyciu poświadczeń użytkownika usługi Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

W wierszu polecenia usługi SQL dla bazy danych, które chcesz uruchom następujące polecenia, aby dodać usługi Azure AD, grupy i udzielanie uprawnień aplikacji wymaga. Na przykład: 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Wpisz polecenie `EXIT`, aby powrócić do wiersza polecenia usługi Cloud Shell.

### <a name="modify-connection-string"></a>Modyfikowanie parametrów połączenia

Należy pamiętać, że te same zmiany wprowadzono w `Web.config` współpracuje z tożsamości zarządzanej, więc jedynym elementem w celu jest usunięcie istniejących parametrów połączenia w swojej aplikacji, które Visual Studio stworzył wdrażania aplikacji po raz pierwszy. Użyj następującego polecenia, ale zastąp  *\<Nazwa aplikacji >* nazwą swojej aplikacji.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publikowanie zmian

Teraz pozostało tylko opublikowanie zmian na platformie Azure.

W obszarze **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **DotNetAppSqlDb** i wybierz polecenie **Publikuj**.

![Publikowanie z Eksploratora rozwiązań](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stronie publikowania kliknij przycisk **Publikuj**. Gdy nowa strona internetowa wyświetla listę zadań do wykonania, aplikacja nawiązuje połączenie z bazą danych za pomocą tożsamości zarządzanej.

![Aplikacja platformy Azure po zakończeniu migracji Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Teraz powinno być możliwe edytowanie listy zadań do wykonania tak jak wcześniej.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Kolejne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Włączanie tożsamości zarządzanych
> * Udzielanie usłudze SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie platformy Entity Framework, aby użyć uwierzytelniania usługi Azure AD z usługą SQL Database
> * Łączenie bazy danych SQL w programie Visual Studio przy użyciu uwierzytelniania usługi Azure AD

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na usługę Azure App Service](app-service-web-tutorial-custom-domain.md)
