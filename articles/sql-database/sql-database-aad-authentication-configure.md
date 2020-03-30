---
title: Konfigurowanie uwierzytelniania usługi Azure Active Directory
description: Dowiedz się, jak połączyć się z bazą danych SQL, wystąpieniem zarządzanym i magazynem danych SQL przy użyciu uwierzytelniania usługi Azure Active Directory — po skonfigurowaniu usługi Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 01/07/2020
ms.openlocfilehash: 881c7076d5131746c730757a07da6fb938429c38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125038"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania w usłudze Azure Active Directory i zarządzanie nim przy użyciu języka SQL)

W tym artykule pokazano, jak tworzyć i wypełniać usługę Azure AD, a następnie używać usługi Azure AD z usługą Azure [SQL Database](sql-database-technical-overview.md), [wystąpienie zarządzane](sql-database-managed-instance.md)i magazyn danych [SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Aby uzyskać omówienie, zobacz [Uwierzytelnianie usługi Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Ten artykuł dotyczy serwera SQL platformy Azure oraz baz danych SQL Database i baz danych SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

> [!IMPORTANT]  
> Łączenie się z programem SQL Server uruchomionym na maszynie Wirtualnej platformy Azure nie jest obsługiwane przy użyciu konta usługi Azure Active Directory. Zamiast tego użyj konta usługi Active Directory w domenie.

## <a name="create-and-populate-an-azure-ad"></a>Tworzenie i wypełnianie usługi Azure AD

Utwórz usługę Azure AD i wypełnij ją użytkownikami i grupami. Usługa Azure AD może być początkową domeną zarządzana usługi Azure AD. Usługa Azure AD może być również lokalnymi usługami domenowymi Active Directory, które są sfederowane z usługą Azure AD.

Aby uzyskać więcej informacji, zobacz tematy [Integrating your on-premises identities with Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) (Integrowanie tożsamości lokalnych z usługą Azure Active Directory), [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Platforma Microsoft Azure obsługuje teraz federację z usługą Windows Servder Active Directory), [Administering your Azure AD directory](../active-directory/fundamentals/active-directory-administer.md) (Administrowanie katalogiem usługi Azure AD), [Manage Azure AD using Windows PowerShell](/powershell/azure/overview) (Zarządzanie usługą Azure AD przy użyciu programu Windows PowerShell) i [Hybrid Identity Required Ports and Protocols](../active-directory/hybrid/reference-connect-ports.md) (Wymagane porty i protokoły tożsamości hybrydowych).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory

1. Skojarz subskrypcję platformy Azure z usługą Azure Active Directory, czyniąc katalog zaufanym katalogiem subskrypcji platformy Azure obsługującym bazę danych. Aby uzyskać szczegółowe informacje, zobacz [Jak subskrypcje platformy Azure są skojarzone z usługą Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Użyj przełącznika katalogu w witrynie Azure portal, aby przełączyć się do subskrypcji skojarzonej z domeną.

   > [!IMPORTANT]
   > Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Relacja zaufania między subskrypcją a katalogiem różni się od relacji subskrypcji z wszystkimi innymi zasobami na platformie Azure (witrynami sieci Web, bazami danych itd.), które przypominają bardziej podrzędne zasoby subskrypcji. Jeśli subskrypcja wygaśnie, dostęp do innych zasobów skojarzonych z subskrypcją również nie będzie możliwy. Lecz katalog pozostanie na platformie Azure i będzie można skojarzyć z nim inną subskrypcję oraz kontynuować zarządzanie użytkownikami w katalogu. Aby uzyskać więcej informacji na temat zasobów, zobacz [Opis dostępu do zasobów na platformie Azure](../active-directory/active-directory-b2b-admin-add-users.md). Aby dowiedzieć się więcej o tej zaufanej relacji, zobacz [Jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Tworzenie administratora usługi Azure AD dla serwera SQL platformy Azure

Każdy serwer SQL platformy Azure (który obsługuje bazę danych SQL lub magazyn danych SQL) rozpoczyna się od jednego konta administratora serwera, który jest administratorem całego serwera SQL platformy Azure. Należy utworzyć drugiego administratora programu SQL Server, czyli konto usługi Azure AD. Ten podmiot zabezpieczeń jest tworzony jako użytkownik zawartej bazy danych w głównej bazie danych. Jako administratorzy konta administratora serwera są członkami **db_owner** roli w każdej bazie danych użytkowników i wprowadź każdą bazę danych użytkownika jako użytkownika **dbo.** Aby uzyskać więcej informacji na temat kont administratora serwera, zobacz [Zarządzanie bazami danych i logowaniami w bazie danych SQL azure](sql-database-manage-logins.md).

Podczas korzystania z usługi Azure Active Directory z replikacją geograficzną administrator usługi Azure Active Directory musi być skonfigurowany zarówno dla serwerów podstawowych, jak i pomocniczych. Jeśli serwer nie ma administratora usługi Azure Active Directory, a następnie logowania usługi Azure Active Directory i użytkownicy otrzymują "Nie można połączyć się" z błędem serwera.

> [!NOTE]
> Użytkownicy, którzy nie są oparte na koncie usługi Azure AD (w tym konta administratora serwera SQL azure), nie można utworzyć użytkowników opartych na usłudze Azure AD, ponieważ nie mają uprawnień do sprawdzania poprawności proponowanych użytkowników bazy danych za pomocą usługi Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Aprowizuj administratora usługi Azure Active Directory dla wystąpienia zarządzanego

> [!IMPORTANT]
> Postępuj zgodnie z tymi krokami tylko wtedy, gdy inicjujesz inicjowanie obsługi administracyjnej wystąpienia zarządzanego. Ta operacja może być wykonywana tylko przez administratora globalnej/firmowej lub administratora ról uprzywilejowanych w usłudze Azure AD. Poniższe kroki opisują proces udzielania uprawnień użytkownikom z różnymi uprawnieniami w katalogu.

> [!NOTE]
> Dla administratorów usługi Azure AD dla mi utworzone przed GA, ale kontynuować działanie ga post, nie ma żadnych zmian funkcjonalnych do istniejącego zachowania. Aby uzyskać więcej informacji, zobacz [nową funkcję administratora usługi Azure AD dla mi sekcji,](#new-azure-ad-admin-functionality-for-mi) aby uzyskać więcej informacji.

Wystąpienie zarządzane wymaga uprawnień do odczytu usługi Azure AD, aby pomyślnie wykonać zadania, takie jak uwierzytelnianie użytkowników za pomocą członkostwa w grupie zabezpieczeń lub tworzenie nowych użytkowników. Aby to zadziałało, należy udzielić uprawnień do wystąpienia zarządzanego, aby odczytać usługę Azure AD. Istnieją dwa sposoby, aby to zrobić: z portal i programu PowerShell. Następujące kroki obie metody.

1. W witrynie Azure Portal w prawym górnym rogu wybierz swoje połączenia, aby wyświetlić listę rozwijaną możliwych usług Active Directory.

2. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD.

   Ten krok łączy subskrypcję skojarzoną z usługą Active Directory z wystąpieniem zarządzanym, upewniając się, że ta sama subskrypcja jest używana zarówno dla usługi Azure AD, jak i dla wystąpienia zarządzanego.

3. Przejdź do wystąpienia zarządzanego i wybierz wystąpienie, którego chcesz użyć do integracji usługi Azure AD.

   ![Aad](./media/sql-database-aad-authentication/aad.png)

4. Wybierz baner na stronie administratora usługi Active Directory i przyznaj uprawnienia bieżącemu użytkownikowi. Jeśli jesteś zalogowany jako administrator globalny/firmowy w usłudze Azure AD, możesz to zrobić z witryny Azure portal lub przy użyciu programu PowerShell przy użyciu poniższego skryptu.

    ![udziel uprawnień-portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

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
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Po pomyślnym zakończeniu operacji w prawym górnym rogu pojawi się następujące powiadomienie:

    ![powodzenie](./media/sql-database-aad-authentication/success.png)

6. Teraz możesz wybrać administratora usługi Azure AD dla wystąpienia zarządzanego. W tym celu na stronie administratora usługi Active Directory wybierz polecenie **Ustaw administratora.**

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na stronie administrator AAD wyszukaj użytkownika, wybierz użytkownika lub grupę, która ma być administratorem, a następnie wybierz pozycję **Wybierz**.

   Strona administratora usługi Active Directory zawiera wszystkich członków i grupy usługi Active Directory. Nie można wybrać użytkowników lub grup, które są wyszarzone, ponieważ nie są one obsługiwane jako administratorzy usługi Azure AD. Zobacz listę obsługiwanych administratorów w [obszarze Funkcje i ograniczenia usługi Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Kontrola dostępu oparta na rolach (RBAC) ma zastosowanie tylko do witryny Azure portal i nie jest propagowana do programu SQL Server.

    ![Dodawanie administratora usługi Azure Active Directory](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. U góry strony administratora usługi Active Directory wybierz pozycję **Zapisz**.

    ![zapisywanie](./media/sql-database-aad-authentication/save.png)

    Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu Administrator usługi Active Directory.

Po zainicjowaniu obsługi administracyjnej usługi Azure AD administratora dla wystąpienia zarządzanego, można rozpocząć tworzenie podmiotów serwera usługi Azure AD (logowania) ze <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">składnią CREATE LOGIN.</a> Aby uzyskać więcej informacji, zobacz [omówienie wystąpienia zarządzanego](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Aby później usunąć administratora, u góry strony administratora usługi Active Directory wybierz pozycję **Usuń administratora,** a następnie wybierz pozycję **Zapisz**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nowa funkcja administratora usługi Azure AD dla mi

W poniższej tabeli podsumowano funkcje publicznego administratora logowania usługi Azure AD w wersji zapoznawczej dla mi w porównaniu z nową funkcją dostarczaną z ga dla logowania usługi Azure AD.

| Administrator logowania usługi Azure AD dla mi podczas publicznej wersji zapoznawczej | Ga funkcje dla administratora usługi Azure AD dla MI |
| --- | ---|
| Zachowuje się w podobny sposób jak administrator usługi Azure AD dla bazy danych SQL, która umożliwia uwierzytelnianie usługi Azure AD, ale administrator usługi Azure AD nie może utworzyć logowania usługi Azure AD lub SQL w głównej bazie danych dla mi. | Administrator usługi Azure AD ma uprawnienia sysadmin i może tworzyć identyfikatory AAD i SQL logowania w głównej bazy danych dla MI. |
| Nie ma go w widoku sys.server_principals | Jest obecny w widoku sys.server_principals |
| Umożliwia konfigurowanie poszczególnych użytkowników-gości usługi Azure AD jako administratora usługi Azure AD dla mi. Aby uzyskać więcej informacji, zobacz [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](../active-directory/b2b/add-users-administrator.md). | Wymaga utworzenia grupy usługi Azure AD z użytkownikami-gośćmi jako członkami, aby skonfigurować tę grupę jako administratora usługi Azure AD dla mi. Aby uzyskać więcej informacji, zobacz [Azure AD business to business support](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Najlepszym rozwiązaniem dla istniejących administratorów usługi Azure AD dla mi utworzonych przed ga ga i nadal działających ga post, zresetować administratora usługi Azure AD przy użyciu witryny Azure portal "Usuń administratora" i "Ustaw administratora" dla tego samego użytkownika usługi Azure AD lub grupy.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Znane problemy z ga logowania usługi Azure AD dla MI

- Jeśli w głównej bazie danych dla mi istnieje identyfikator logowania usługi `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`Azure AD, utworzony przy użyciu polecenia T-SQL, nie można go skonfigurować jako administrator usługi Azure AD dla mi. Wystąpi błąd podczas ustawiania logowania jako administrator usługi Azure AD przy użyciu poleceń azure portal, programu PowerShell lub interfejsu wiersza polecenia w celu utworzenia logowania usługi Azure AD.
  - Logowanie musi zostać usunięte w głównej `DROP LOGIN [myaadaccount]`bazie danych za pomocą polecenia , zanim konto może zostać utworzone jako administrator usługi Azure AD.
  - Skonfiguruj konto administratora usługi `DROP LOGIN` Azure AD w witrynie Azure portal po pomyślnym. 
  - Jeśli nie możesz skonfigurować konta administratora usługi Azure AD, zaewidencjonuj główną bazę danych wystąpienia zarządzanego dla logowania. Użyj następującego polecenia:`SELECT * FROM sys.server_principals`
  - Konfigurowanie administratora usługi Azure AD dla mi automatycznie utworzy login w głównej bazie danych dla tego konta. Usunięcie administratora usługi Azure AD spowoduje automatyczne upuszczenie logowania z głównej bazy danych.

- Poszczególni użytkownicy-goście-goście usługi Azure AD nie są obsługiwani jako administratorzy usługi Azure AD dla mi. Użytkownicy-goście muszą być częścią grupy usługi Azure AD, aby skonfigurować go jako administratora usługi Azure AD. Obecnie blok portalu Azure nie wyszarza użytkowników-gości dla innej usługi Azure AD, umożliwiając użytkownikom kontynuowanie konfiguracji administratora. Zapisywanie użytkowników-gości jako administratora usługi Azure AD spowoduje niepowodzenie instalacji.
  - Jeśli chcesz, aby użytkownik-gość był administratorem usługi Azure AD dla mi, dołącz użytkownika gościa do grupy usługi Azure AD i ustaw tę grupę jako administratora usługi Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>Wystąpienie zarządzanego programu PowerShell dla programu SQL

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aby uruchomić polecenia cmdlet programu PowerShell, musisz mieć zainstalowany i uruchomiony program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz temat [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

Aby aprowizować administratora usługi Azure AD, należy wykonać następujące polecenia programu Azure PowerShell:

- Connect-AzAccount
- Wybierz-AzSubscription

Polecenia cmdlet używane do inicjowania obsługi administracyjnej usługi Azure AD i zarządzania nim dla wystąpienia zarządzanego sql:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Apezuruje administratora usługi Azure AD dla wystąpienia zarządzanego SQL w bieżącej subskrypcji. (Musi pochodzić z bieżącej subskrypcji)|
| [Usuń-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Usuwa administratora usługi Azure AD dla wystąpienia zarządzanego SQL w bieżącej subskrypcji. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Zwraca informacje o administratorze usługi Azure AD dla wystąpienia zarządzanego SQL w bieżącej subskrypcji.|

Następujące polecenie pobiera informacje o administratorze usługi Azure AD dla wystąpienia zarządzanego o nazwie ManagedInstance01 skojarzonego z grupą zasobów o nazwie ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Następujące polecenia apozyją grupę administratorów usługi Azure AD o nazwie DBA dla wystąpienia zarządzanego o nazwie ManagedInstance01. Ten serwer jest skojarzony z grupą zasobów ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Następujące polecenie usuwa administratora usługi Azure AD dla wystąpienia zarządzanego o nazwie ManagedInstanceName01 skojarzonego z grupą zasobów ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Można również aprowizować administratora usługi Azure AD dla wystąpienia zarządzanego SQL, wywołując następujące polecenia interfejsu wiersza polecenia:

| Polecenie | Opis |
| --- | --- |
|[az sql mi ad-admin tworzenie](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Apowaj administratora usługi Azure Active Directory dla wystąpienia zarządzanego SQL. (Musi pochodzić z bieżącej subskrypcji) |
|[az sql mi ad-admin usuń](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Usuwa administratora usługi Azure Active Directory dla wystąpienia zarządzanego SQL. |
|[az sql mi ad-admin lista](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Zwraca informacje o administratorze usługi Azure Active Directory aktualnie skonfigurowanym dla wystąpienia zarządzanego SQL. |
|[az sql mi ad-admin aktualizacja](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Aktualizuje administratora usługi Active Directory dla wystąpienia zarządzanego SQL. |

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Aprowizowanie administratora usługi Azure Active Directory dla serwera usługi Azure SQL Database

> [!IMPORTANT]
> Wykonaj następujące kroki tylko wtedy, gdy inicjujesz aprowizacji serwera usługi Azure SQL Database lub hurtowni danych.

Poniższe dwie procedury pokazują, jak aprowizować administratora usługi Azure Active Directory dla serwera SQL platformy Azure w witrynie Azure portal i przy użyciu programu PowerShell.

### <a name="azure-portal"></a>Portal Azure

1. W witrynie [Azure Portal](https://portal.azure.com/) w prawym górnym rogu wybierz swoje połączenia, aby wyświetlić listę rozwijaną możliwych usług Active Directory. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD. W tym kroku powiązana z subskrypcją usługa Active Directory zostaje połączona z serwerem Azure SQL. Dzięki temu mamy pewność, że ta sama subskrypcja jest używana zarówno przez usługę Azure AD, jak i SQL Server. (Serwer Azure SQL może hostować usługę Azure SQL Database lub Azure SQL Data Warehouse).

    ![Wybór usługi AD][8]

2. Wyszukaj i wybierz **serwer SQL**.

    ![Wyszukiwanie i wybieranie serwerów SQL](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Na tej stronie przed wybraniem **serwerów SQL**można wybrać **gwiazdkę** obok nazwy, aby *ulubieć* kategorię i dodać **serwery SQL** do lewego paska nawigacyjnego.

3. Na stronie **PROGRAMU SQL Server** wybierz pozycję Administrator usługi Active **Directory**.

4. Na stronie **Administrator usługi Active Directory** wybierz pozycję **Ustaw administratora**.

    ![Serwery SQL ustawiają administratora usługi Active Directory](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Na stronie **Dodawanie administratora** wyszukaj użytkownika, wybierz użytkownika lub grupę do pełnienia funkcji administratora, a następnie kliknij opcję **Wybierz**. (Na stronie administratora usługi Active Directory wyświetlono wszystkich członków i grupy danej usługi Active Directory). Nie można wybrać wyszarzonych użytkowników lub grup, ponieważ nie są oni obsługiwani jako administratorzy usługi Azure AD. (Zobacz listę obsługiwanych administratorów w sekcji **Funkcje i ograniczenia usługi Azure AD** w sekcji Korzystanie z [uwierzytelniania usługi Azure Active Directory do uwierzytelniania za pomocą bazy danych SQL lub magazynu danych SQL).](sql-database-aad-authentication.md) Kontrola dostępu oparta na rolach (RBAC) ma zastosowanie tylko do portalu i nie jest propagowana do programu SQL Server.

    ![Wybieranie administratora usługi Azure Active Directory](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. W górnej części strony **Administrator usługi Active Directory** wybierz opcję **ZAPISZ**.

    ![zapisywanie administratora](./media/sql-database-aad-authentication/save-admin.png)

Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu **Administrator usługi Active Directory**.

   > [!NOTE]
   > Podczas konfigurowania administratora usługi Azure AD nowa nazwa administratora (użytkownika lub grupy) nie może już istnieć w wirtualnej bazie danych master jako nazwa użytkownika używana do uwierzytelniania na serwerze SQL. Jeśli już istnieje, konfiguracja administratora usługi Azure AD nie powiedzie się. Tworzenie administratora zostanie cofnięte i wyświetli się komunikat, że administrator z taką nazwą już istnieje. Ponieważ nazwa użytkownika do uwierzytelniania na serwerze SQL nie jest częścią usługi Azure AD, próba połączenia się z serwerem za pomocą uwierzytelniania z usługi AD nie powiedzie się.

Aby później usunąć administratora, u góry strony **administratora usługi Active Directory** wybierz pozycję **Usuń administratora,** a następnie wybierz pozycję **Zapisz**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Usługa PowerShell dla usługi Azure SQL Database i usługi Azure SQL Data Warehouse

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aby uruchomić polecenia cmdlet programu PowerShell, musisz mieć zainstalowany i uruchomiony program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz temat [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Aby aprowizować administratora usługi Azure AD, należy wykonać następujące polecenia programu Azure PowerShell:

- Connect-AzAccount
- Wybierz-AzSubscription

Polecenia cmdlet używane do inicjowania obsługi administracyjnej usługi Azure AD i zarządzania nimi dla usługi Azure SQL Database i usługi Azure SQL Data Warehouse:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Apruje administratora usługi Azure Active Directory dla serwera SQL platformy Azure lub usługi Azure SQL Data Warehouse. (Musi pochodzić z bieżącej subskrypcji) |
| [Usuń-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Usuwa administratora usługi Azure Active Directory dla serwera SQL platformy Azure lub usługi Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Zwraca informacje o administratorze usługi Azure Active Directory aktualnie skonfigurowanym dla serwera SQL azure lub usługi Azure SQL Data Warehouse. |

Aby wyświetlić więcej informacji o każdym z tych poleceń, użyj funkcji pomocy w zakresie polecenia programu PowerShell. Na przykład `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Następujący skrypt udostępnia grupę administratorów usługi Azure AD `40b79501-b343-44ed-9ce7-da4c8cc7353f`o nazwie **DBA_Group** (identyfikator obiektu) dla serwera **demo_server** w grupie zasobów o nazwie **Grupa-23:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Parametr wejściowy **DisplayName** akceptuje nazwę wyświetlaną usługi Azure AD lub nazwę główną użytkownika. Na przykład ``DisplayName="John Smith"`` ``DisplayName="johns@contoso.com"``i . W przypadku grup usługi Azure AD obsługiwana jest tylko nazwa wyświetlana usługi Azure AD.

> [!NOTE]
> Polecenie ```Set-AzSqlServerActiveDirectoryAdministrator``` programu Azure PowerShell nie uniemożliwia inicjowania obsługi administracyjnej usługi Azure AD dla nieobsługiwałych użytkowników. Nieobsługiwał użytkownika można aprowizować, ale nie można połączyć się z bazą danych.

W poniższym przykładzie użyto opcjonalnego **identyfikatora obiektu:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Obiekt usługi Azure AD **objectID** jest wymagany, gdy **nazwa displayname** nie jest unikatowa. Aby pobrać wartości **ObjectID** i **DisplayName,** użyj sekcji Usługi Active Directory w portalu Azure Classic Portal i wyświetl właściwości użytkownika lub grupy.

Poniższy przykład zwraca informacje o bieżącym administratorze usługi Azure AD dla serwera SQL platformy Azure:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Poniższy przykład usuwa administratora usługi Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Administrator usługi Azure AD można aprowizować, wywołując następujące polecenia interfejsu wiersza polecenia:

| Polecenie | Opis |
| --- | --- |
|[az sql server ad-admin tworzenie](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Apruje administratora usługi Azure Active Directory dla serwera SQL platformy Azure lub usługi Azure SQL Data Warehouse. (Musi pochodzić z bieżącej subskrypcji) |
|[az sql server ad-admin delete az sql server ad-admin delete az sql server ad-admin delete az](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Usuwa administratora usługi Azure Active Directory dla serwera SQL platformy Azure lub usługi Azure SQL Data Warehouse. |
|[az sql server ad-admin lista](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Zwraca informacje o administratorze usługi Azure Active Directory aktualnie skonfigurowanym dla serwera SQL azure lub usługi Azure SQL Data Warehouse. |
|[az sql server ad-admin aktualizacja](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Aktualizuje administratora usługi Active Directory dla serwera SQL platformy Azure lub usługi Azure SQL Data Warehouse. |

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Można również aprowizować administratora usługi Azure Active Directory przy użyciu interfejsów API REST. Aby uzyskać więcej informacji, zobacz [Odwołanie i operacje interfejsu API REST zarządzania usługami dla usługi Azure SQL Database operations for Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Konfigurowanie komputerów klienckich

Na wszystkich komputerach klienckich, z których aplikacje lub użytkownicy łączą się z usługą Azure SQL Database lub usługa Azure SQL Data Warehouse przy użyciu tożsamości usługi Azure AD, należy zainstalować następujące oprogramowanie:

- .NET Framework 4.6 [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)lub nowszych z .
- Biblioteka uwierzytelniania usługi Azure Active Directory dla programu SQL Server *(ADAL). DLL*). Poniżej znajdują się łącza do pobrania, aby zainstalować najnowszy sterownik SSMS, ODBC i OLE DB, który zawiera *ADAL. Biblioteka DLL.*
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [Sterownik ODBC 17 dla programu SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [Sterownik OLE DB 18 dla programu SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Wymagania te można spełnić, korzystając z następujących wymagań:

- Zainstalowanie najnowszej wersji [programu SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) lub SQL Server Data [Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) spełnia wymagania programu .NET Framework 4.6.
    - SSMS instaluje wersję X86 *ADAL. DLL*.
    - SSDT instaluje wersję *amd64 ADAL. DLL*.
    - Najnowsza wersja programu Visual Studio z [programu Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) spełnia wymagania programu .NET Framework 4.6, ale nie instaluje wymaganej wersji *usługi AMD64 usługi ADAL. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Tworzenie użytkowników zawartej bazy danych w bazie danych mapowanych na tożsamości usługi Azure AD

> [!IMPORTANT]
> Wystąpienie zarządzane obsługuje teraz podmioty serwera usługi Azure AD (logowania), co umożliwia tworzenie loginów z użytkowników, grup lub aplikacji usługi Azure AD. Podmioty serwera usługi Azure AD (logowania) zapewnia możliwość uwierzytelniania do wystąpienia zarządzanego bez konieczności tworzenia użytkowników bazy danych jako użytkownika zawartej bazy danych. Aby uzyskać więcej informacji, zobacz [omówienie wystąpienia zarządzanego](sql-database-managed-instance.md#azure-active-directory-integration). Aby uzyskać składnię tworzenia podmiotów serwera usługi Azure AD (loginów), zobacz <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">TWORZENIE LOGOWANIA.</a>

Uwierzytelnianie w usłudze Azure Active Directory wymaga utworzenia użytkowników bazy danych jako użytkowników zawartej bazy danych. Użytkownik zawartej bazy danych oparty na tożsamości w usłudze Azure AD to użytkownik bazy danych, który nie ma loginu w bazie danych master i jest zamapowany na tożsamość w katalogu Azure AD skojarzonym z bazą danych. Tożsamość w usłudze Azure AD może być indywidualnym kontem użytkownika lub grupą. Aby uzyskać więcej informacji na temat użytkowników zawartej bazy danych, patrz [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartych baz danych — tworzenie przenośnej bazy danych).

> [!NOTE]
> Użytkowników bazy danych (z wyjątkiem administratorów) nie można tworzyć za pośrednictwem witryny Azure Portal. Role RBAC nie są propagowane do serwera SQL Server, bazy danych SQL Database lub magazynu danych SQL Data Warehouse. Role RBAC na platformie Azure są używane do zarządzania zasobami platformy Azure i nie mają zastosowania do uprawnień do bazy danych. Przykład: osoba o roli **Współautor serwera SQL Server** nie może udzielać dostępu do połączenia z bazą danych SQL Database lub magazynem danych SQL Data Warehouse. Uprawnienie dostępu należy nadać bezpośrednio w bazie danych za pomocą instrukcji języka Transact-SQL.

> [!WARNING]
> Znaki specjalne, takie jak dwukropek `:` lub ampersand `&` uwzględnione w nazwie użytkownika w instrukcjach języka T-SQL CREATE LOGIN  i CREATE USER nie są obsługiwane.

Aby utworzyć użytkownika zawartej w bazie danych opartej na usłudze Azure AD (innego niż administrator serwera, który jest właścicielem bazy danych), połącz się z bazą danych z tożsamością usługi Azure AD jako użytkownik z co najmniej uprawnieniem **ALTER ANY USER.** Następnie użyj następującej składni Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* może być główną nazwą użytkownika usługi Azure AD lub nazwą wyświetlaną grupy usługi Azure AD.

**Przykłady:** Aby utworzyć użytkownika zawartej bazy danych reprezentującego użytkownika domeny federacyjnej lub zarządzanej usługi Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Aby utworzyć użytkownika zawartej bazy danych reprezentującego usługę Azure AD lub grupę domen federacyjnej, podaj nazwę wyświetlaną grupy zabezpieczeń:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Aby utworzyć użytkownika zawartej bazy danych reprezentującego aplikację, która łączy się przy użyciu tokenu usługi Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> To polecenie wymaga, aby sql access Azure AD ("dostawca zewnętrzny") w imieniu zalogowanego użytkownika. Czasami pojawią się okoliczności, które powodują, że usługa Azure AD zwrócić wyjątek z powrotem do języka SQL. W takich przypadkach użytkownik zobaczy błąd SQL 33134, który powinien zawierać komunikat o błędzie specyficzny dla usługi AAD. W większości przypadków błąd będzie mówił, że odmowa dostępu lub że użytkownik musi zarejestrować się w usłudze MFA, aby uzyskać dostęp do zasobu lub że dostęp między aplikacjami firmowymi musi być obsługiwany za pośrednictwem preautoryzowania. W pierwszych dwóch przypadkach problem jest zwykle spowodowany przez zasady dostępu warunkowego, które są ustawione w dzierżawie usługi AAD użytkownika: uniemożliwiają one użytkownikowi dostęp do zewnętrznego dostawcy. Aktualizacja zasad urzędu certyfikacji w celu umożliwienia dostępu do aplikacji '00000002-0000-0000-c0000-0000000000" (identyfikator aplikacji interfejsu API wykresu AAD) powinna rozwiązać problem. W przypadku, gdy błąd mówi, że dostęp między aplikacjami innych firm musi być obsługiwany za pośrednictwem preauthorization, problem jest, ponieważ użytkownik jest zalogowany jako jednostki usługi. Polecenie powinno zakończyć się pomyślnie, jeśli jest wykonywane przez użytkownika zamiast tego.

> [!TIP]
> Nie można bezpośrednio utworzyć użytkownika z usługi Azure Active Directory innej niż usługa Azure Active Directory skojarzona z subskrypcją platformy Azure. Jednak członkowie innych usług Active Directories, które są importowanymi użytkownikami w skojarzonej usłudze Active Directory (nazywanej użytkownikami zewnętrznymi), mogą być dodani do grupy usługi Active Directory w dzierżawie usługi Active Directory. Tworząc użytkownika zawartej bazy danych dla tej grupy usług AD, użytkownicy z zewnętrznej usługi Active Directory mogą uzyskać dostęp do bazy danych SQL.

Aby uzyskać więcej informacji na temat tworzenia użytkowników zawartej bazy danych na podstawie tożsamości usługi Azure Active Directory, zobacz [TWORZENIE UŻYTKOWNIKA (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Usunięcie administratora usługi Azure Active Directory dla serwera SQL platformy Azure uniemożliwia każdemu użytkownikowi uwierzytelniania usługi Azure AD nawiązanie połączenia z serwerem. W razie potrzeby bezużyteczne użytkowników usługi Azure AD mogą być usuwane ręcznie przez administratora bazy danych SQL.

> [!NOTE]
> Jeśli zostanie **wyświetlony limit czasu połączenia wygasł,** może `TransparentNetworkIPResolution` być konieczne ustawienie parametru ciągu połączenia na false. Aby uzyskać więcej informacji, zobacz [Problem z limitem czasu połączenia z .NET Framework 4.6.1 — TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Podczas tworzenia użytkownika bazy danych, że użytkownik otrzymuje **uprawnienie CONNECT** i może połączyć się z tej bazy danych jako członek roli **PUBLIC.** Początkowo jedynymi uprawnieniami dostępnymi dla użytkownika są wszelkie uprawnienia przyznane roli **PUBLIC** lub wszelkie uprawnienia przyznane dowolnym grupom usługi Azure AD, do których są członkami. Po zainicjowaniu obsługi administracyjnej użytkownika bazy danych opartej na usłudze Azure AD można udzielić użytkownikowi dodatkowych uprawnień, w taki sam sposób, jak udzielanie uprawnień do innego typu użytkownika. Zazwyczaj udziel uprawnień do ról bazy danych i dodaj użytkowników do ról. Aby uzyskać więcej informacji, zobacz [Podstawy uprawnień aparatu bazy danych](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Aby uzyskać więcej informacji na temat specjalnych ról bazy danych SQL, zobacz [Zarządzanie bazami baz danych i logowaniami w bazie danych SQL Azure](sql-database-manage-logins.md).
Konto użytkownika domeny federacyjnej, które jest importowane do domeny zarządzanej jako użytkownik zewnętrzny, musi używać tożsamości domeny zarządzanej.

> [!NOTE]
> Użytkownicy usługi Azure AD są oznaczeni w metadanych bazy danych typem E (EXTERNAL_USER), a grupy typem X (EXTERNAL_GROUPS). Aby uzyskać więcej informacji, zobacz [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Łączenie się z bazą danych użytkowników lub magazynem danych przy użyciu usługi SSMS lub SSDT  

Aby potwierdzić, że administrator usługi Azure AD jest poprawnie skonfigurowany, połącz się z **główną** bazą danych przy użyciu konta administratora usługi Azure AD.
Aby aprowizować użytkownika zawartej w bazie danych opartej na usłudze Azure AD (innego niż administrator serwera, który jest właścicielem bazy danych), połącz się z bazą danych za pomocą tożsamości usługi Azure AD, która ma dostęp do bazy danych.

> [!IMPORTANT]
> Obsługa uwierzytelniania usługi Azure Active Directory jest dostępna w [programie SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i SQL Server Data [Tools](https://msdn.microsoft.com/library/mt204009.aspx) w programie Visual Studio 2015. Wersja SSMS z sierpnia 2016 r. obejmuje również obsługę uniwersalnego uwierzytelniania usługi Active Directory, która umożliwia administratorom wymaganie uwierzytelniania wieloskładnikowego przy użyciu połączenia telefonicznego, wiadomości tekstowej, kart inteligentnych z pinem lub powiadomień aplikacji mobilnej.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Używanie tożsamości usługi Azure AD do łączenia się przy użyciu usługi SSMS lub SSDT

Poniższe procedury pokazują, jak połączyć się z bazą danych SQL za pomocą tożsamości usługi Azure AD przy użyciu programu SQL Server Management Studio lub sql server database tools.

### <a name="active-directory-integrated-authentication"></a>Zintegrowane uwierzytelnianie usługi Active Directory

Tej metody należy użyć, jeśli użytkownik jest zalogowany w systemie Windows przy użyciu poświadczeń usługi Azure Active Directory z domeny federacyjnej.

1. Uruchom Studio zarządzania lub narzędzia danych oraz w oknie dialogowym **Połącz z serwerem** (lub **Z aparatem baz danych)** w polu **Uwierzytelnianie** wybierz pozycję **Active Directory - Integrated**. Hasło nie jest potrzebne ani można je wprowadzić, ponieważ istniejące poświadczenia zostaną wyświetlone dla połączenia.

    ![Wybieranie zintegrowanego uwierzytelniania usługi AD][11]

2. Wybierz przycisk **Opcje,** a następnie na stronie **Właściwości połączenia** w polu **Połącz z bazą danych** wpisz nazwę bazy danych użytkowników, z którą chcesz się połączyć. (Opcja **nazwa domeny usługi AD lub identyfikator dzierżawy**" jest obsługiwana tylko dla aplikacji Universal z opcjami połączenia usługi **MFA,** w przeciwnym razie jest wyszarzona).  

    ![Wybieranie nazwy bazy danych][13]

## <a name="active-directory-password-authentication"></a>Uwierzytelnianie hasłem usługi Active Directory

Tej metody należy używać podczas łączenia się z główną nazwą usługi Azure AD przy użyciu domeny zarządzanej usługi Azure AD. Można go również używać do kont federacyjnej bez dostępu do domeny, na przykład podczas pracy zdalnej.

Ta metoda służy do uwierzytelniania do bazy danych SQL/DW z usługi Azure AD dla użytkowników natywnych lub federowanych usługi Azure AD. Użytkownik macierzysty jest jawnie utworzony w usłudze Azure AD i jest uwierzytelniony przy użyciu nazwy użytkownika i hasła, podczas gdy użytkownik federowany jest użytkownikiem systemu Windows, którego domena jest sfederowana z usługą Azure AD. Tej ostatniej metody (przy użyciu hasła & użytkownika) można użyć, gdy użytkownik chce użyć swoich poświadczeń systemu Windows, ale jego komputer lokalny nie jest połączony z domeną (na przykład przy użyciu dostępu zdalnego). W takim przypadku użytkownik systemu Windows może wskazać swoje konto domeny i hasło i może uwierzytelnić się w bazie danych SQL/DW przy użyciu poświadczeń federacyjnej.

1. Uruchom Studio zarządzania lub narzędzia danych oraz w oknie dialogowym **Łączenie się z serwerem** (lub **Aparat bazy danych)** w polu **Uwierzytelnianie** wybierz pozycję **Active Directory - Password**.

2. W polu **Nazwa użytkownika** wpisz nazwę użytkownika usługi Azure Active Directory w formacie **domain.com .\@** Nazwy użytkowników muszą być kontem z usługi Azure Active Directory lub kontem z domeny federacji z usługą Azure Active Directory.

3. W polu **Hasło** wpisz hasło użytkownika dla konta usługi Azure Active Directory lub konta domeny federacyjnej.

    ![Wybieranie uwierzytelniania hasła usługi AD][12]

4. Wybierz przycisk **Opcje,** a następnie na stronie **Właściwości połączenia** w polu **Połącz z bazą danych** wpisz nazwę bazy danych użytkowników, z którą chcesz się połączyć. (Zobacz grafikę w poprzedniej opcji).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Używanie tożsamości usługi Azure AD do łączenia się z aplikacją kliencką

Poniższe procedury pokazują, jak połączyć się z bazą danych SQL za pomocą tożsamości usługi Azure AD z aplikacji klienckiej.

### <a name="active-directory-integrated-authentication"></a>Zintegrowane uwierzytelnianie usługi Active Directory

Aby korzystać ze zintegrowanego uwierzytelniania systemu Windows, usługa Active Directory domeny musi być sfederowana z usługą Azure Active Directory. Aplikacja kliencka (lub usługa) łącząca się z bazą danych musi być uruchomiona na komputerze przyłączanym do domeny pod poświadczeniami domeny użytkownika.

Aby połączyć się z bazą danych przy użyciu zintegrowanego uwierzytelniania i tożsamości usługi Azure AD, słowo kluczowe Uwierzytelnianie w ciągu połączenia bazy danych musi być ustawione na Zintegrowane z usługą Active Directory. Poniższy przykład kodu Języka C# używa ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Słowo kluczowe `Integrated Security=True` parametry połączenia nie jest obsługiwane do łączenia się z usługą Azure SQL Database. Podczas nawiązywać połączenie ODBC należy usunąć spacje i ustawić uwierzytelnianie na "ActiveDirectoryIntegrated".

### <a name="active-directory-password-authentication"></a>Uwierzytelnianie hasłem usługi Active Directory

Aby połączyć się z bazą danych przy użyciu zintegrowanego uwierzytelniania i tożsamości usługi Azure AD, słowo kluczowe Uwierzytelnianie musi być ustawione na hasło usługi Active Directory. Parametry połączenia muszą zawierać słowa kluczowe i wartości nazwy użytkownika i identyfikatora UID oraz hasła/identyfikatora PWD. Poniższy przykład kodu Języka C# używa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Dowiedz się więcej o metodach uwierzytelniania usługi Azure AD przy użyciu przykładów kodu demonstracyjnego dostępnych w [witrynie Demonstracyjne GitHub uwierzytelniania usługi Azure AD.](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)

## <a name="azure-ad-token"></a>Token usługi Azure AD

Ta metoda uwierzytelniania umożliwia usługom warstwy środkowej łączenie się z usługą Azure SQL Database lub usługą Azure SQL Data Warehouse przez uzyskanie tokenu z usługi Azure Active Directory (AAD). Umożliwia zaawansowane scenariusze, w tym uwierzytelnianie oparte na certyfikatach. Aby korzystać z uwierzytelniania tokenu usługi Azure AD, należy wykonać cztery podstawowe kroki:

1. Zarejestruj aplikację w usłudze Azure Active Directory i uzyskaj identyfikator klienta dla kodu.
2. Utwórz użytkownika bazy danych reprezentującego aplikację. (Ukończono wcześniej w kroku 6.)
3. Utwórz certyfikat na komputerze klienckim uruchamia aplikację.
4. Dodaj certyfikat jako klucz dla aplikacji.

Przykładowy ciąg połączenia:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Aby uzyskać więcej informacji, zobacz [Blog zabezpieczeń programu SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Aby uzyskać informacje dotyczące dodawania certyfikatu, zobacz [Wprowadzenie do uwierzytelniania opartego na certyfikatach w usłudze Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Następujące instrukcje, połączyć za pomocą wersji 13.1 sqlcmd, który jest dostępny w [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd`z `-G` poleceniem nie działa z tożsamościami systemowych i wymaga logowania głównego użytkownika.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Rozwiązywanie problemów z uwierzytelnianiem usługi Azure AD

Wskazówki dotyczące rozwiązywania problemów z uwierzytelnianiem usługi Azure AD można znaleźć w następującym blogu:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać przegląd loginów, użytkowników, ról bazy danych i uprawnień w bazie danych SQL, zobacz [Logowania, użytkownicy, role baz danych i konta użytkowników](sql-database-manage-logins.md).
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
