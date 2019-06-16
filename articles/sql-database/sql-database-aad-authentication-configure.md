---
title: Konfigurowanie uwierzytelniania usługi Azure Active Directory - SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z bazy danych SQL Database, wystąpienia zarządzanego i SQL Data Warehouse przy użyciu usługi Azure Active Directory Authentication — po skonfigurowaniu usługi Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: abb4a43176026fca5a80409ade13af1f8f96d9f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390582"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory przy użyciu języka SQL

W tym artykule pokazano, jak utworzyć i wypełnić usługi Azure AD, a następnie użyj usługi Azure AD za pomocą platformy Azure [bazy danych SQL](sql-database-technical-overview.md), [wystąpienia zarządzanego](sql-database-managed-instance.md), i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Aby uzyskać przegląd, zobacz [uwierzytelniania usługi Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Ten artykuł dotyczy serwera Azure SQL i bazy danych SQL Database i SQL Data Warehouse baz danych, które są tworzone na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.
> [!IMPORTANT]  
> Nawiązywanie połączenia z SQL Server uruchomiony na Maszynie wirtualnej platformy Azure nie jest obsługiwana przy użyciu konta usługi Azure Active Directory. Zamiast tego użyj domeny konta usługi Active Directory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

## <a name="create-and-populate-an-azure-ad"></a>Utworzyć i wypełnić usługi Azure AD

Tworzenie usługi Azure AD i wypełnianie jej użytkowników i grup. Usługa Azure AD może być początkowej usługi Azure AD domeny zarządzanej. Usługa Azure AD może być również lokalne Active Directory Domain Services, które są Sfederowane z usługą Azure AD.

Aby uzyskać więcej informacji, zobacz tematy [Integrating your on-premises identities with Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) (Integrowanie tożsamości lokalnych z usługą Azure Active Directory), [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Platforma Microsoft Azure obsługuje teraz federację z usługą Windows Servder Active Directory), [Administering your Azure AD directory](../active-directory/fundamentals/active-directory-administer.md) (Administrowanie katalogiem usługi Azure AD), [Manage Azure AD using Windows PowerShell](/powershell/azure/overview) (Zarządzanie usługą Azure AD przy użyciu programu Windows PowerShell) i [Hybrid Identity Required Ports and Protocols](../active-directory/hybrid/reference-connect-ports.md) (Wymagane porty i protokoły tożsamości hybrydowych).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory

1. Kojarzenie subskrypcji platformy Azure do usługi Azure Active Directory, wprowadzając katalogu zaufanego katalogu subskrypcji platformy Azure, bazę danych. Aby uzyskać więcej informacji, zobacz [jak subskrypcje platformy Azure są kojarzone z usługą Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Użyj przełącznika katalogów w witrynie Azure portal, aby przełączyć się do subskrypcji powiązanej z domeną.

   **Aby uzyskać dodatkowe informacje:** Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Relacja zaufania między subskrypcją a katalogiem różni się od relacji subskrypcji z wszystkimi innymi zasobami na platformie Azure (witrynami sieci Web, bazami danych itd.), które przypominają bardziej podrzędne zasoby subskrypcji. Jeśli subskrypcja wygaśnie, dostęp do innych zasobów skojarzonych z subskrypcją również nie będzie możliwy. Lecz katalog pozostanie na platformie Azure i będzie można skojarzyć z nim inną subskrypcję oraz kontynuować zarządzanie użytkownikami w katalogu. Aby uzyskać więcej informacji na temat zasobów, zobacz [dostęp do zasobów na platformie Azure zrozumienie](../active-directory/active-directory-b2b-admin-add-users.md). Aby dowiedzieć się więcej na ten temat zaufanej relacji, zobacz [jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Utwórz administratora usługi Azure AD dla serwera Azure SQL

Każdy serwer Azure SQL, (który jest hostem bazy danych SQL Database lub SQL Data Warehouse) rozpoczyna się przy użyciu konta administratora pojedynczego serwera, który jest administratorem całego serwera Azure SQL. Drugi administratora programu SQL Server musi zostać utworzona, które jest kontem usługi Azure AD. Ta jednostka zostanie utworzona jako użytkownika zawartej bazy danych w bazie danych master. Jako administratorów, kont administratora serwera są elementami członkowskimi **db_owner** rolę w każdy użytkownik bazy danych, a następnie wprowadź każdej bazy danych użytkownika jako **dbo** użytkownika. Aby uzyskać więcej informacji na temat konta administratora serwera, zobacz [Zarządzanie bazami danych i nazwami logowania w usłudze Azure SQL Database](sql-database-manage-logins.md).

Korzystając z usługi Azure Active Directory za pomocą replikacji geograficznej, administrator usługi Azure Active Directory musi być skonfigurowany zarówno podstawowego, jak i serwerów pomocniczych. Jeśli serwer nie ma administrator usługi Azure Active Directory, następnie logowania do usługi Azure Active Directory, aby użytkownicy otrzymują "nie można nawiązać połączenia" Błąd serwera.

> [!NOTE]
> Użytkownicy, którzy nie są oparte na konto usługi Azure AD (w tym konta administratora serwera Azure SQL), nie można utworzyć użytkowników na podstawie usługi AD platformy Azure, ponieważ nie mają uprawnień do weryfikowania użytkowników bazą danych z usługą Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Aprowizowanie administrator usługi Azure Active Directory dla wystąpienia zarządzanego

> [!IMPORTANT]
> Tylko tych kroków w przypadku aprowizowania wystąpienia zarządzanego. Tę operację można wykonać tylko przez administratora globalnego/firmy w usłudze Azure AD. Poniżej opisano proces przyznawania uprawnień dla użytkowników z różnych uprawnień w katalogu.

Wystąpienie zarządzane wymaga uprawnień do odczytu usługi Azure AD, aby pomyślnie wykonać zadania, takie jak uwierzytelnianie użytkowników za pomocą członkostwa w grupie zabezpieczeń lub tworzenie nowych użytkowników. Aby to działało musisz przyznać uprawnienia do wystąpienia zarządzanego do odczytu usługi Azure AD. Istnieją dwa sposoby, aby to zrobić: z portalu i programu PowerShell. Następujące kroki w obu tych metod.

1. W witrynie Azure portal, w prawym górnym rogu wybierz listę rozwijaną listę możliwych katalogów aktywne połączenie.
2. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD.

   W tym kroku łączy subskrypcji powiązanej z usługą Active Directory za pomocą wystąpienia zarządzanego, upewniając się, że tej samej subskrypcji jest używany zarówno dla usługi Azure AD i wystąpienia zarządzanego.
3. Przejdź do wystąpienia zarządzanego, a następnie wybierz jedno z nich, którego chcesz użyć do integracji z usługą Azure AD.

   ![usługi AAD](./media/sql-database-aad-authentication/aad.png)

4. Wybierz Baner u góry strony administratora usługi Active Directory i przyznaj uprawnienie do bieżącego użytkownika. Jeśli zalogowano Cię jako administratora globalnego/firmy w usłudze Azure AD, możesz zrobić to z witryny Azure portal lub przy użyciu programu PowerShell za pomocą poniższego skryptu.

    ![udzielanie uprawnień — portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the Managed Instance.
    # Can be executed only by a "Company Administrator" or "Global Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Po pomyślnym zakończeniu operacji, następujące powiadomienie pojawi się w prawym górnym rogu:

    ![powodzenie](./media/sql-database-aad-authentication/success.png)

6. Teraz możesz wybrać administrator usługi Azure AD dla wystąpienia zarządzanego. W tym na stronie administratora usługi Active Directory wybierz **Ustaw administratora** polecenia.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na stronie administratora usługi AAD, Wyszukaj użytkownika, wybierz użytkownika lub grupy, uprawnienia administratora, a następnie wybierz **wybierz**.

   Strona administratora usługi Active Directory zawiera wszystkie elementy członkowskie i grupy usługi Active Directory. Nie można wybrać użytkowników lub grup, które są wyszarzone, ponieważ nie są one obsługiwane jako Administratorzy usługi Azure AD. Zobacz listę obsługiwanych administratorów w usłudze [funkcje usługi Azure AD i ograniczenia](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Kontrola dostępu oparta na rolach (RBAC) ma zastosowanie tylko do witryny Azure portal i nie są propagowane do programu SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. W górnej części strony administratora usługi Active Directory wybierz **Zapisz**.

    ![Zapisz](./media/sql-database-aad-authentication/save.png)

    Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu administratora usługi Active Directory.

Po zainicjowaniu obsługi administracyjnej administratora usługi Azure AD, wystąpienia zarządzanego, możesz rozpocząć tworzenie usługi Azure AD serwera jednostek (logowania) (**publicznej wersji zapoznawczej**) za pomocą <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> składni. Aby uzyskać więcej informacji, zobacz [zarządzane — Przegląd instancji](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Aby później usunąć administratora, w górnej części strony administratora usługi Active Directory, zaznacz **Usuń administratora**, a następnie wybierz pozycję **Zapisz**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Aprowizowanie administratora usługi Azure Active Directory dla serwera usługi Azure SQL Database

> [!IMPORTANT]
> Tylko tych kroków w przypadku udostępniania serwera Azure SQL Database lub magazynu danych.

Poniższe dwie procedury dowiesz się, jak wykonać aprowizację administrator usługi Azure Active Directory dla serwera Azure SQL w witrynie Azure portal i za pomocą programu PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. W witrynie [Azure Portal](https://portal.azure.com/) w prawym górnym rogu wybierz swoje połączenia, aby wyświetlić listę rozwijaną możliwych usług Active Directory. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD. W tym kroku powiązana z subskrypcją usługa Active Directory zostaje połączona z serwerem Azure SQL. Dzięki temu mamy pewność, że ta sama subskrypcja jest używana zarówno przez usługę Azure AD, jak i SQL Server. (Serwer Azure SQL można hostingu, Azure SQL Database lub Azure SQL Data Warehouse.) ![wybierz ad][8]

2. Na banerze po lewej stronie wybierz **wszystkich usług**, a w przypadku typu filtru w **programu SQL server**. Wybierz **serwerów Sql**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > Na tej stronie, zanim wybierzesz **serwerów SQL**, możesz wybrać **gwiazdki** obok nazwy, aby *ulubionych* kategorię i Dodaj **serwerów SQL**na pasku nawigacyjnym po lewej stronie.

3. Na **programu SQL Server** wybierz opcję **administratora usługi Active Directory**.
4. W **administratora usługi Active Directory** wybierz opcję **Ustaw administratora**.  ![wybierz usługi active directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. Na stronie **Dodawanie administratora** wyszukaj użytkownika, wybierz użytkownika lub grupę do pełnienia funkcji administratora, a następnie kliknij opcję **Wybierz**. (Na stronie administratora usługi Active Directory wyświetlono wszystkich członków i grupy danej usługi Active Directory). Nie można wybrać wyszarzonych użytkowników lub grup, ponieważ nie są oni obsługiwani jako administratorzy usługi Azure AD. (Zobacz listę obsługiwanych administratorów w sekcji **Funkcje i ograniczenia usługi Azure AD** artykułu [Korzystanie z uwierzytelniania usługi Azure Active Directory do uwierzytelniania w usłudze SQL Database lub SQL Data Warehouse](sql-database-aad-authentication.md)). Kontrola dostępu oparta na rolach (RBAC) ma zastosowanie tylko do portalu i nie jest stosowana na serwerze SQL.
    ![wybór administratora](./media/sql-database-aad-authentication/select-admin.png)  

6. W górnej części strony **Administrator usługi Active Directory** wybierz opcję **ZAPISZ**.
    ![zapisywanie administratora](./media/sql-database-aad-authentication/save-admin.png)

Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu **Administrator usługi Active Directory**.

   > [!NOTE]
   > Podczas konfigurowania administratora usługi Azure AD nowa nazwa administratora (użytkownika lub grupy) nie może już istnieć w wirtualnej bazie danych master jako nazwa użytkownika używana do uwierzytelniania na serwerze SQL. Jeśli już istnieje, konfiguracja administratora usługi Azure AD nie powiedzie się. Tworzenie administratora zostanie cofnięte i wyświetli się komunikat, że administrator z taką nazwą już istnieje. Ponieważ nazwa użytkownika do uwierzytelniania na serwerze SQL nie jest częścią usługi Azure AD, próba połączenia się z serwerem za pomocą uwierzytelniania z usługi AD nie powiedzie się.

Aby później usunąć administratora, w górnej części **administratora usługi Active Directory** wybierz opcję **Usuń administratora**, a następnie wybierz pozycję **Zapisz**.

### <a name="powershell"></a>PowerShell

Aby uruchomić polecenia cmdlet programu PowerShell, musisz mieć programu Azure PowerShell zainstalowany i uruchomiony. Aby uzyskać szczegółowe informacje, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell). Aby zainicjować obsługę administratora usługi Azure AD, wykonaj następujące polecenia programu Azure PowerShell:

- Połącz AzAccount
- Select-AzSubscription

Polecenia cmdlet używane do udostępniania i zarządzania nimi administrator usługi Azure AD:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Aprowizuje administrator usługi Azure Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. (Musi być z bieżącej subskrypcji). |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Usuwa administratora usługi Azure Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Zwraca informacje o obecnie skonfigurowane dla serwera Azure SQL lub usługi Azure SQL Data Warehouse administratora usługi Azure Active Directory. |

Użyj polecenia PowerShell get-help, aby wyświetlić dodatkowe informacje dotyczące każdego z tych poleceń, na przykład ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

Poniższy skrypt aprowizuje grupy administrator usługi Azure AD o nazwie **DBA_Group** (identyfikator obiektu `40b79501-b343-44ed-9ce7-da4c8cc7353f`) dla **demo_server** serwer w grupie zasobów o nazwie **grupy-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** akceptuje parametr wejściowy, nazwę wyświetlaną usługi Azure AD lub główną nazwę użytkownika. Na przykład ``DisplayName="John Smith"`` i ``DisplayName="johns@contoso.com"``. Dla grup usługi Azure AD tylko usługi Azure AD Nazwa wyświetlana jest obsługiwane.

> [!NOTE]
> Polecenia programu Azure PowerShell ```Set-AzSqlServerActiveDirectoryAdministrator``` uniemożliwia Inicjowanie obsługi administracyjnej Administratorzy usługi Azure AD dla użytkowników nieobsługiwany. Nieobsługiwana użytkownika mogą być udostępniane, ale nie można nawiązać połączenia z bazą danych.

W poniższym przykładzie użyto opcjonalnego **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Usługi Azure AD **ObjectID** jest wymagany, gdy **DisplayName** nie jest unikatowa. Aby pobrać **ObjectID** i **DisplayName** wartości, użyj sekcji Active Directory w klasycznym portalu Azure i wyświetlić właściwości użytkownika lub grupy.

Poniższy przykład zwraca informacje o bieżącej administrator usługi Azure AD dla usługi Azure SQL server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Poniższy przykład usuwa administrator usługi Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Administrator usługi Azure Active Directory można również udostępniać za pomocą interfejsów API REST. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST zarządzania usługi i operacji na operacje bazy danych SQL Azure dla usługi Azure SQL Database](https://docs.microsoft.com/rest/api/sql/)

### <a name="cli"></a>Interfejs wiersza polecenia  

Można również udostępniać administratora usługi Azure AD, wywołując następujących poleceń interfejsu wiersza polecenia:

| Polecenie | Opis |
| --- | --- |
|[Utwórz az sql server ad administratora](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Aprowizuje administrator usługi Azure Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. (Musi być z bieżącej subskrypcji). |
|[AZ sql server ad admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Usuwa administratora usługi Azure Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. |
|[AZ sql server administrator usługi ad listy](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Zwraca informacje o obecnie skonfigurowane dla serwera Azure SQL lub usługi Azure SQL Data Warehouse administratora usługi Azure Active Directory. |
|[Aktualizacja ad admin az sql server](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Aktualizuje administratora usługi Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. |

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia, zobacz [SQL — az sql](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Konfigurowanie komputerów klienckich

Na wszystkich komputerach klienckich, z których aplikacje lub użytkowników łączyć się z usługi Azure SQL Database lub Azure SQL Data Warehouse przy użyciu tożsamości usługi Azure AD, należy zainstalować następujące oprogramowanie:

- .NET framework 4.6 lub nowszy z [ https://msdn.microsoft.com/library/5a4x27ek.aspx ](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Usługa Azure Active Directory Authentication Library dla programu SQL Server (**ADALSQL. Biblioteka DLL**) jest dostępna w wielu językach (x86 i amd64) z Centrum pobierania w [Microsoft Active Directory Authentication Library dla programu Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Należy spełnić te wymagania przez:

- Instalacji [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) lub [programu SQL Server Data Tools dla programu Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) spełnia wymagania platformy .NET Framework 4.6.
- Wersja programu SSMS instaluje x86 **ADALSQL. Biblioteka DLL**.
- Program SSDT instaluje wersję amd64 **ADALSQL. Biblioteka DLL**.
- Najnowszy program Visual Studio z [pobieranie Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) spełnia wymagania platformy .NET Framework 4.6, ale nie instaluje wymagane amd64 wersję **ADALSQL. Biblioteka DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Tworzenie użytkowników zawartej bazy danych w bazie danych mapowany do tożsamości usługi Azure AD

>[!IMPORTANT]
>Wystąpienie zarządzane obsługuje teraz podmiotów zabezpieczeń serwera usługi Azure AD (logowania) (**publicznej wersji zapoznawczej**), co umożliwia tworzenie identyfikatorów logowania na podstawie użytkowników, grup lub aplikacji usługi Azure AD. Jednostki serwera w usłudze Azure AD (logowania) zapewnia możliwość uwierzytelniania do wystąpienia zarządzanego bez wymagania od użytkowników bazy danych, który zostanie utworzony jako użytkownika zawartej bazy danych. Aby uzyskać więcej informacji, zobacz [zarządzane — Przegląd instancji](sql-database-managed-instance.md#azure-active-directory-integration). Dla składni na temat tworzenia podmiotów zabezpieczeń serwera (logowania) usługi Azure AD, zobacz <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

Uwierzytelnianie w usłudze Azure Active Directory wymaga utworzenia użytkowników bazy danych jako użytkowników zawartej bazy danych. Użytkownik zawartej bazy danych oparty na tożsamości w usłudze Azure AD to użytkownik bazy danych, który nie ma loginu w bazie danych master i jest zamapowany na tożsamość w katalogu Azure AD skojarzonym z bazą danych. Tożsamość w usłudze Azure AD może być indywidualnym kontem użytkownika lub grupą. Aby uzyskać więcej informacji na temat użytkowników zawartej bazy danych, patrz [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartych baz danych — tworzenie przenośnej bazy danych).

> [!NOTE]
> Użytkowników bazy danych (z wyjątkiem administratorów) nie można tworzyć za pośrednictwem witryny Azure Portal. Role RBAC nie są propagowane do serwera SQL Server, bazy danych SQL Database lub magazynu danych SQL Data Warehouse. Role RBAC na platformie Azure są używane do zarządzania zasobami platformy Azure i nie mają zastosowania do uprawnień do bazy danych. Przykład: osoba o roli **Współautor serwera SQL Server** nie może udzielać dostępu do połączenia z bazą danych SQL Database lub magazynem danych SQL Data Warehouse. Uprawnienie dostępu należy nadać bezpośrednio w bazie danych za pomocą instrukcji języka Transact-SQL.
> [!WARNING]
> Znaki specjalne, takie jak dwukropek `:` lub ampersand `&` uwzględnione w nazwie użytkownika w instrukcjach języka T-SQL CREATE LOGIN  i CREATE USER nie są obsługiwane.

Aby utworzyć użytkownikiem platformy Azure na podstawie usługi AD zawarte bazy danych (innego niż administrator serwera, który jest właścicielem bazy danych), połączenia z bazą danych przy użyciu tożsamości usługi Azure AD jako użytkownik z co najmniej **ALTER ANY użytkownika** uprawnień. Następnie należy użyć następującej składni języka Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* może być główną nazwę użytkownika usługi Azure AD lub nazwę wyświetlaną grupy usługi Azure AD.

**Przykłady:** Aby utworzyć zawartej bazy danych użytkownika reprezentowanie usługi Azure AD federacyjnych lub zarządzanego użytkownika domeny:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Aby utworzyć użytkownika zawartej bazy danych, reprezentowanie usługi Azure AD lub Sfederowanych grupy domeny, należy podać nazwę wyświetlaną grupy zabezpieczeń:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Aby utworzyć użytkownika zawartej bazy danych, reprezentujący aplikację, która nawiązuje połączenie za pomocą tokenu usługi Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> Nie można bezpośrednio utworzyć użytkownika z usługi Azure Active Directory niż Azure Active Directory, który jest skojarzony z subskrypcją platformy Azure. Jednak członkowie innych aktywnych katalogów, będące importowanych użytkowników w usłudze Active Directory skojarzone (znanych jako użytkownicy zewnętrzni) można dodać do grupy usługi Active Directory w dzierżawie usługi Active Directory. Dzięki tworzenia użytkownika zawartej bazy danych dla tej grupy usługi AD dla użytkowników zewnętrznych usługi Active Directory można uzyskać dostępu do bazy danych SQL.

Aby uzyskać więcej informacji o tworzeniu zawarte bazy danych użytkowników w oparciu o tożsamości usługi Azure Active Directory, zobacz [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Usuwanie administratora usługi Azure Active Directory dla serwera Azure SQL uniemożliwia łączenie się z serwerem dowolnego użytkownika uwierzytelniania usługi Azure AD. Jeśli to konieczne, pustych użytkowników usługi Azure AD można porzucić ręcznie przez administratora bazy danych SQL.
> [!NOTE]
> Jeśli zostanie wyświetlony **upłynął limit czasu połączenia**, może być konieczne ustawienie `TransparentNetworkIPResolution` parametr parametry połączenia na wartość false. Aby uzyskać więcej informacji, zobacz [problem limitu czasu połączenia z programem .NET Framework 4.6.1 — TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Podczas tworzenia użytkownika bazy danych, użytkownik otrzymuje **CONNECT** uprawnień i mogą się łączyć z tą bazą danych jako członek **publicznych** roli. Początkowo tylko uprawnienia dostępne dla użytkownika są wszystkie uprawnienia przyznane **publicznych** rolę lub uprawnienia udzielone do żadnej z grup usługi Azure AD, są członkami. Po zainicjowaniu użytkownikiem platformy Azure na podstawie usługi AD zawarte bazy danych można przyznać użytkownikowi dodatkowe uprawnienia, taki sam sposób jak udzielić uprawnień do jakichkolwiek innych typów użytkownika. Zazwyczaj przyznać uprawnienia do ról bazy danych i dodawania użytkowników do ról. Aby uzyskać więcej informacji, zobacz [podstawowe uprawnienia aparatu bazy danych](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Aby uzyskać więcej informacji na temat specjalne role bazy danych SQL, zobacz [Zarządzanie bazami danych i nazwami logowania w usłudze Azure SQL Database](sql-database-manage-logins.md).
Konto użytkownika domeny federacyjnej, który jest importowany do domeny zarządzanej, jako użytkownik zewnętrzny, należy użyć tożsamość domeny zarządzanej.

> [!NOTE]
> Użytkownicy usługi Azure AD są oznaczeni w metadanych bazy danych typem E (EXTERNAL_USER), a grupy typem X (EXTERNAL_GROUPS). Aby uzyskać więcej informacji, zobacz [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Łączenie z magazynu bazy danych lub danych użytkownika za pomocą programu SSMS lub program SSDT  

Aby upewnić się, administrator usługi Azure AD nie jest prawidłowo skonfigurowany, połączyć się z **wzorca** bazy danych przy użyciu konta administratora usługi Azure AD.
Aby zainicjować obsługę użytkownikiem platformy Azure na podstawie usługi AD zawarte bazy danych (innego niż administrator serwera, który jest właścicielem bazy danych), połączenia z bazą danych przy użyciu tożsamości usługi Azure AD, który ma dostęp do bazy danych.

> [!IMPORTANT]
> Obsługa uwierzytelniania usługi Azure Active Directory jest dostępna z [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) w programie Visual Studio 2015. Sierpnia 2016 r. programu SSMS obejmuje również obsługę uniwersalne uwierzytelnianie usługi Active Directory, co pozwala administratorom Wymagaj uwierzytelniania wieloskładnikowego przy użyciu połączenia telefonicznego, wiadomości SMS, kart inteligentnych przy użyciu numeru pin lub powiadomienie aplikacji mobilnej.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Przy użyciu tożsamości usługi Azure AD, aby nawiązać połączenie przy użyciu programu SSMS lub program SSDT

Poniższe procedury pokazują, jak nawiązać połączenie z bazą danych SQL przy użyciu tożsamości usługi Azure AD przy użyciu programu SQL Server Management Studio lub narzędzia bazy danych programu SQL Server.

### <a name="active-directory-integrated-authentication"></a>Zintegrowane uwierzytelnianie usługi Active Directory

Użyj tej metody, jeśli użytkownik jest zalogowany do Windows przy użyciu poświadczeń usługi Azure Active Directory z domeny federacyjnej.

1. Rozpocznij Management Studio lub Data Tools i **Połącz z serwerem** (lub **nawiązywanie połączenia z aparatem bazy danych**) okno dialogowe, **uwierzytelniania** wybierz pozycję  **Active Directory — zintegrowana**. Hasło nie jest potrzebne lub można wprowadzić, ponieważ istniejące poświadczenia zostaną przedstawione na potrzeby połączenia.

    ![Wybierz opcję Zintegrowane uwierzytelnianie usługi AD][11]
2. Wybierz **opcje** przycisku, a następnie na **właściwości połączenia** strony w **Połącz z bazą danych** wpisz nazwę bazy danych, aby nawiązać połączenie z. ( **Identyfikatora nazwy lub dzierżawy domeny AD**"opcja jest obsługiwana tylko dla **Universal z połączeniem usługi MFA** opcji, w przeciwnym razie jego jest nieaktywna.)  

    ![Wybierz nazwę bazy danych][13]

## <a name="active-directory-password-authentication"></a>Uwierzytelnianie hasłem usługi Active Directory

Ta metoda domeny zarządzanej przez połączenie przy użyciu nazwy głównej usługi Azure AD przy użyciu usługi Azure AD. Można również użyć w przypadku kont federacyjnych bez dostępu do domeny, na przykład podczas pracy zdalnej.

Ta metoda służy do uwierzytelniania do bazy danych/magazyn danych SQL z usługą Azure AD native lub Sfederowanych użytkowników usługi Azure AD. Natywne użytkownika jest jawnie utworzonej w usłudze Azure AD i uwierzytelniane przy użyciu nazwy użytkownika i hasła, gdy użytkownik federacyjny jest użytkownikiem Windows domeny są Sfederowane z usługą Azure AD. Druga metoda (przy użyciu użytkownika i hasło) może służyć po użytkownik chce, aby używać ich poświadczenia systemu windows, ale ich komputer lokalny nie jest sprzężony z domeny (na przykład za pomocą dostępu zdalnego). W takim przypadku użytkownik Windows może wskazywać na swoje konto domeny i hasło i mogą uwierzytelniać do bazy danych/magazyn danych SQL przy użyciu poświadczeń federacyjnych.

1. Rozpocznij Management Studio lub Data Tools i **Połącz z serwerem** (lub **nawiązywanie połączenia z aparatem bazy danych**) okno dialogowe, **uwierzytelniania** wybierz pozycję  **Usługi Active Directory — hasło**.
2. W **nazwa_użytkownika** wpisz swoją nazwę użytkownika usługi Azure Active Directory w formacie **username\@domena.com**. Nazwy użytkowników musi być kontem usługi Azure Active Directory lub konto z domeny Federację z usługą Azure Active Directory.
3. W **hasło** wpisz hasło użytkownika dla konta usługi Azure Active Directory lub konta domeny federacyjnej.

    ![Wybierz uwierzytelnianie hasłem usługi AD][12]
4. Wybierz **opcje** przycisku, a następnie na **właściwości połączenia** strony w **Połącz z bazą danych** wpisz nazwę bazy danych, aby nawiązać połączenie z. (Zobacz grafikę w przypadku poprzedniej opcji).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Aby połączyć się z aplikacją klienta przy użyciu tożsamości usługi Azure AD

Poniższe procedury pokazują, jak połączyć z bazą danych SQL przy użyciu tożsamości usługi Azure AD od aplikacji klienckiej.

### <a name="active-directory-integrated-authentication"></a>Zintegrowane uwierzytelnianie usługi Active Directory

Do korzystania ze zintegrowanego uwierzytelniania Windows, usługi Active Directory w Twojej domeny musi Sfederowane z usługą Azure Active Directory. Aplikacja kliencka (lub usługi), połączenie z bazą danych musi działać na komputerze przyłączonym do domeny w ramach poświadczeń domeny użytkownika.

Aby połączyć się z bazą danych przy użyciu zintegrowanego uwierzytelniania i tożsamości usługi Azure AD, słowo kluczowe uwierzytelniania w parametrach połączenia bazy danych musi być równa zintegrowane usługi Active Directory. Poniższy przykładowy kod języka C# używa ADO .NET.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Słowo kluczowe parametrów połączenia ``Integrated Security=True`` nie jest obsługiwana dla łączenia z bazą danych SQL Azure. Podczas nawiązywania połączenia ODBC, należy usunąć spacje i ustaw uwierzytelnianie "ActiveDirectoryIntegrated".

### <a name="active-directory-password-authentication"></a>Uwierzytelnianie hasłem usługi Active Directory

Aby połączyć się z bazą danych przy użyciu zintegrowanego uwierzytelniania i tożsamości usługi Azure AD, uwierzytelnianie — słowo kluczowe musi być równa Active Directory haseł. Ciąg połączenia może zawierać Identyfikatora użytkownika/identyfikatora UID i hasła/PWD słowa kluczowe i wartości. Poniższy przykładowy kod języka C# używa ADO .NET.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Dowiedz się więcej na temat metod uwierzytelniania usługi Azure AD przy użyciu przykładów kodu pokaz dostępne pod adresem [pokaz GitHub uwierzytelniania usługi Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Tokenu usługi Azure AD

Ta metoda uwierzytelniania umożliwia usługi warstwy środkowej, aby nawiązać połączenie usługi Azure SQL Database lub Azure SQL Data Warehouse, uzyskując token z usługi Azure Active Directory (AAD). Umożliwia zaawansowane scenariusze, w tym uwierzytelniania opartego na certyfikatach. Należy wykonać cztery podstawowe kroki, aby użyć uwierzytelniania tokenu usługi Azure AD:

1. Zarejestrować aplikację w usłudze Azure Active Directory i uzyskać identyfikator klienta w kodzie.
2. Utwórz użytkownika bazy danych, reprezentujący aplikację. (Wykonana wcześniej w kroku 6).
3. Aby utworzyć certyfikat, należy na uruchomień komputerów klienckich w aplikacji.
4. Dodaj certyfikat jako klucz dla swojej aplikacji.

Przykładowy ciąg połączenia:

```c#
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Aby uzyskać więcej informacji, zobacz [Blog zabezpieczeń programu SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Aby dowiedzieć się, jak dodawanie certyfikatu, zobacz [wprowadzenie do uwierzytelniania opartego na certyfikatach w usłudze Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Poniższe instrukcje, nawiązywanie połączenia przy użyciu narzędzia sqlcmd, która jest dostępna w wersji 13.1 [Centrum pobierania](https://go.microsoft.com/fwlink/?LinkID=825643).

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Kolejne kroki

- Dostęp i kontrola w usłudze SQL Database zostały omówione w temacie [Kontrola dostępu w usłudze SQL Database](sql-database-control-access.md).
- Dane logowania, użytkownicy i role bazy danych w usłudze SQL Database zostały omówione w temacie [Logins, users, and database roles](sql-database-manage-logins.md) (Dane logowania, użytkownicy i role bazy danych).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](https://msdn.microsoft.com/library/ms181127.aspx) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](https://msdn.microsoft.com/library/ms189121.aspx) (Role bazy danych).
- Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
