---
title: Konfigurowanie uwierzytelniania usługi Azure Active Directory - SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie bazy danych SQL, zarządzane wystąpienia i SQL Data Warehouse przy użyciu usługi Azure uwierzytelnianie usługi Active Directory — po skonfigurowaniu usługi Azure AD.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: 1f5f4a4ece116503c8ddb5eaa4998b5b1a407bb1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory z bazy danych SQL, zarządzane wystąpienia lub SQL Data Warehouse

W tym artykule przedstawiono sposób tworzenia i wypełniania usługi Azure AD, a następnie użyj usługi Azure AD z bazy danych SQL Azure i SQL Data Warehouse. Aby uzyskać ogólne informacje, zobacz [uwierzytelniania usługi Azure Active Directory](sql-database-aad-authentication.md).

>  [!NOTE]  
>  Dołączanie do programu SQL Server uruchomionego na maszynie Wirtualnej platformy Azure nie jest obsługiwane przy użyciu konta usługi Azure Active Directory. Zamiast tego użyj domeny konta usługi Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Utworzyć i wypełnić usługi Azure AD
Tworzenie usługi Azure AD i wypełnić ją użytkowników i grup. Usługi Azure AD może być początkowej usługi Azure AD domeny zarządzanej. Usługi Azure AD można także lokalnych usług domenowych Active Directory, który jest Sfederowane z usługą Azure AD.

Aby uzyskać więcej informacji, zobacz tematy [Integrating your on-premises identities with Azure Active Directory](../active-directory/active-directory-aadconnect.md) (Integrowanie tożsamości lokalnych z usługą Azure Active Directory), [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Platforma Microsoft Azure obsługuje teraz federację z usługą Windows Servder Active Directory), [Administering your Azure AD directory](../active-directory/active-directory-administer.md) (Administrowanie katalogiem usługi Azure AD), [Manage Azure AD using Windows PowerShell](/powershell/azure/overview?view=azureadps-2.0) (Zarządzanie usługą Azure AD przy użyciu programu Windows PowerShell) i [Hybrid Identity Required Ports and Protocols](..//active-directory/connect/active-directory-aadconnect-ports.md) (Wymagane porty i protokoły tożsamości hybrydowych).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Skojarz lub Dodaj subskrypcję platformy Azure do usługi Azure Active Directory

1. Kojarzenie subskrypcji platformy Azure do usługi Azure Active Directory poprzez katalogu zaufanego katalogu subskrypcji platformy Azure obsługującym bazę danych. Aby uzyskać więcej informacji, zobacz [jak subskrypcje platformy Azure skojarzonych z usługą Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md).
2. Użyj przełącznik katalogu w portalu Azure, aby przełączyć się do subskrypcji skojarzonych z domeną.

   **Informacje dodatkowe:** subskrypcji Azure co ma relację zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Relacja zaufania między subskrypcją a katalogiem różni się od relacji subskrypcji z wszystkimi innymi zasobami na platformie Azure (witrynami sieci Web, bazami danych itd.), które przypominają bardziej podrzędne zasoby subskrypcji. Jeśli subskrypcja wygaśnie, dostęp do innych zasobów skojarzonych z subskrypcją również nie będzie możliwy. Lecz katalog pozostanie na platformie Azure i będzie można skojarzyć z nim inną subskrypcję oraz kontynuować zarządzanie użytkownikami w katalogu. Aby uzyskać więcej informacji o zasobach, zobacz [opis dostęp do zasobów na platformie Azure](../active-directory/active-directory-b2b-admin-add-users.md). Aby dowiedzieć się więcej o tym zaufane Zobacz relacji [jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Utwórz administrator usługi Azure AD dla serwera Azure SQL
Każdy serwer Azure SQL, (który jest hostem bazy danych SQL lub SQL Data Warehouse) rozpoczyna się przy użyciu konta administratora pojedynczego serwera, który jest administratorem całego serwera Azure SQL. Drugi administratora programu SQL Server musi zostać utworzony, który jest konto usługi Azure AD. Tego podmiotu zabezpieczeń jest tworzony jako użytkownik zawartej bazy danych w bazie danych master. Jako administratorów, kont administratora serwera są elementami członkowskimi **db_owner** roli w każdej użytkownika bazy danych, a następnie wprowadź każdej bazy danych użytkownika w postaci **dbo** użytkownika. Aby uzyskać więcej informacji na temat kont administratora serwera, zobacz [Zarządzanie bazami danych i Logowaniami w bazie danych SQL Azure](sql-database-manage-logins.md).

Jeśli używasz usługi Azure Active Directory z replikacja geograficzna, administrator usługi Azure Active Directory musi być skonfigurowany dla serwera podstawowego i serwerów pomocniczych. Jeśli serwer nie ma administrator usługi Azure Active Directory, logowania do usługi Azure Active Directory, aby użytkownicy otrzymają "nie można nawiązać połączenia" Błąd serwera.

> [!NOTE]
> Użytkownicy, którzy nie są oparte na koncie usługi Azure AD (łącznie z konta administratora serwera Azure SQL), nie można utworzyć usługi Azure AD użytkownicy, ponieważ nie mają uprawnień do weryfikowania użytkowników bazą danych z usługą Azure AD.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Administrator usługi Azure Active Directory dla wystąpienia zarządzane udostępniania

> [!IMPORTANT]
> Tylko wykonaj następujące kroki w przypadku udostępniania wystąpienia programu zarządzane. Ta operacja może być wykonywane tylko przez administratora globalnego/firmy w usłudze Azure AD. Poniżej opisano proces przyznawania uprawnień dla użytkowników z różnych uprawnień w katalogu.

Zarządzane wystąpienia wymaga uprawnień do odczytu do usługi Azure AD pomyślnie wykonywanie zadań takich jak uwierzytelnianie użytkowników za pośrednictwem przynależności do grupy zabezpieczeń lub tworzenia nowych użytkowników. Aby to zrobić należy udzielić uprawnień do zarządzanego wystąpienia odczytać usługi Azure AD. Istnieją dwa sposoby to zrobić: z portalu i programu PowerShell. Następujące kroki obu metod.

1. W portalu Azure, w prawym górnym rogu kliknij połączenie na liście rozwijanej listę możliwych usług Active Directory. 
2. Wybierz prawidłowe usługi Active Directory jako domyślnej usługi Azure AD. 

   Ten krok łączy subskrypcję skojarzoną z usługi Active Directory z wystąpieniem zarządzane, upewniając się, że tej samej subskrypcji jest używana zarówno dla usługi Azure AD i zarządzane wystąpienia.
3. Przejdź do wystąpienia zarządzane i wybierz jedną, który ma być używany dla integracji z usługą Azure AD.

   ![usługi AAD](./media/sql-database-aad-authentication/aad.png)

4.  Kliknij na transparencie na górze strony administratora usługi Active Directory. Jeśli użytkownik jest zalogowany jako administrator globalny/firmy w usłudze Azure AD, możesz to zrobić z portalu Azure lub przy użyciu programu PowerShell.

    ![Udziel uprawnień portalu](./media/sql-database-aad-authentication/grant-permissions.png)

    ![Udziel uprawnień powershell](./media/sql-database-aad-authentication/grant-permissions-powershell.png)
    
    Jeśli użytkownik jest zalogowany jako administrator globalny/firmy w usłudze Azure AD, możesz to zrobić w portalu Azure lub skrypt programu PowerShell.

5. Po pomyślnym zakończeniu operacji następujące powiadomienia będą widoczne w prawym górnym rogu:

    ![powodzenie](./media/sql-database-aad-authentication/success.png)

6.  Teraz możesz administratora usługi Azure AD dla swojego wystąpienia zarządzane. W tym na stronie administratora usługi Active Directory kliknij **ustawić administratora** polecenia.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na stronie Administracja Dodaj wyszukiwania dla użytkownika, wybierz użytkownika lub grupy jako administrator, a następnie kliknij przycisk **wybierz**. 

   Strona Administrator usługi Active Directory zawiera wszystkie elementy członkowskie i grup usługi Active Directory. Nie można wybrać użytkowników lub grup, które są wygaszone, ponieważ nie są obsługiwane jako administratorów usługi Azure AD. Zobacz listę obsługiwanych Administratorzy w [funkcje usługi Azure AD i ograniczenia](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Kontrola dostępu oparta na rolach (RBAC) ma zastosowanie tylko do portalu Azure i nie są propagowane do programu SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. W górnej części strony administratora usługi Active Directory, kliknij przycisk **zapisać**.

    ![zapisz](./media/sql-database-aad-authentication/save.png)

    Proces zmiany administrator może potrwać kilka minut. Następnie w polu administratora usługi Active Directory pojawi się nowego administratora.

> [!IMPORTANT]
> Podczas konfigurowania usługi Azure AD administratora, Nowa nazwa administratora (użytkownik lub grupa) nie może być już dostępne w bazie danych master wirtualnego jako użytkownik uwierzytelniania programu SQL Server. Jeśli jest obecny, spowoduje to niepowodzenie instalacji administratora usługi Azure AD i wycofywanie jej tworzenia i wskazujący, że takie jak administrator (nazwa) już istnieje. Ponieważ taki użytkownik uwierzytelniania programu SQL Server nie jest częścią usługi Azure AD, próbuje połączyć się z serwerem przy użyciu uwierzytelniania usługi Azure AD nie powiedzie się.

> [!TIP]
> Aby później usunąć administratora, w górnej części strony administratora usługi Active Directory, kliknij przycisk **usunąć administratora**, a następnie kliknij przycisk **zapisać**.
 
## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Zapewnij administrator usługi Azure Active Directory dla serwera bazy danych SQL Azure

> [!IMPORTANT]
> Tylko wykonaj następujące kroki w przypadku udostępniania serwera bazy danych SQL Azure lub magazynu danych.

Poniższe dwie procedury pokazują, jak udostępnić administratorem serwera Azure SQL w portalu Azure i przy użyciu programu PowerShell usługi Azure Active Directory.

### <a name="azure-portal"></a>Azure Portal
1. W [portalu Azure](https://portal.azure.com/), w prawym górnym rogu kliknij połączenie na liście rozwijanej listę możliwych usług Active Directory. Wybierz prawidłowe usługi Active Directory jako domyślnej usługi Azure AD. Ten krok łączy skojarzone z subskrypcją usługi Active Directory z serwerem Azure SQL, upewniając się, że tej samej subskrypcji jest używana zarówno dla usługi Azure AD i programu SQL Server. (Serwer Azure SQL można obsługiwać, baza danych SQL Azure lub usługi Azure SQL Data Warehouse.)   
    ![choose-ad][8]   
    
2. Na transparencie po lewej stronie wybierz **serwerów SQL**, wybierz pozycję z **programu SQL server**, a następnie w **programu SQL Server** kliknij przycisk **administratora usługi Active Directory**.   
3. W **administratora usługi Active Directory** kliknij przycisk **ustawić administratora**.   
    ![Wybierz usługi active directory](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. W **dodać administratora** , Wyszukaj użytkownika, wybierz użytkownika lub grupy jako administrator, a następnie kliknij przycisk **wybierz**. (Strony administratora usługi Active Directory zawiera wszystkie elementy członkowskie i grup usługi Active Directory. Nie można wybrać użytkowników lub grup, które są wygaszone, ponieważ nie są obsługiwane jako administratorów usługi Azure AD. (Zobacz listę obsługiwanych Administratorzy w **funkcje usługi Azure AD i ograniczenia** sekcji [Użyj Azure uwierzytelnianie usługi Active Directory do uwierzytelniania przy użyciu bazy danych SQL lub SQL Data Warehouse](sql-database-aad-authentication.md).) Kontrola dostępu oparta na rolach (RBAC) ma zastosowanie tylko do portalu i nie są propagowane do programu SQL Server.   
    ![Wybierz administratora](./media/sql-database-aad-authentication/select-admin.png)  
    
5. W górnej części **administratora usługi Active Directory** kliknij przycisk **ZAPISAĆ**.   
    ![Zapisz administratora](./media/sql-database-aad-authentication/save-admin.png)   

Proces zmiany administrator może potrwać kilka minut. Następnie nowego administratora jest wyświetlana w **administratora usługi Active Directory** pole.

   > [!NOTE]
   > Podczas konfigurowania usługi Azure AD administratora, Nowa nazwa administratora (użytkownik lub grupa) nie może być już dostępne w bazie danych master wirtualnego jako użytkownik uwierzytelniania programu SQL Server. Jeśli jest obecny, instalacja administratora usługi Azure AD nie powiedzie się; Wycofywanie jej tworzenia i wskazujący tego takie jak administrator (nazwa) już istnieje. Ponieważ taki użytkownik uwierzytelniania programu SQL Server nie jest częścią usługi Azure AD, próbuje połączyć się z serwerem przy użyciu uwierzytelniania usługi Azure AD nie powiedzie się.
   > 


Aby później usunąć administratora, w górnej części **administratora usługi Active Directory** kliknij przycisk **usunąć administratora**, a następnie kliknij przycisk **zapisać**.

### <a name="powershell"></a>PowerShell
Do uruchamiania poleceń cmdlet programu PowerShell, należy mieć programu Azure PowerShell zainstalowany i uruchomiony. Aby uzyskać szczegółowe informacje, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell).

Aby udostępnić administrator usługi Azure AD, wykonaj następujące polecenia programu PowerShell systemu Azure:

* Connect-AzureRmAccount
* Select-AzureRmSubscription

Polecenia cmdlet używane do udostępniania i zarządzania nimi administratora usługi Azure AD:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Inicjuje administrator usługi Azure Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. (Musi być z bieżącej subskrypcji). |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Usuwa administratora usługi Azure Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |Zwraca informacje o obecnie skonfigurowane dla serwera Azure SQL lub usługi Azure SQL Data Warehouse administratora usługi Azure Active Directory. |

Użyj polecenia PowerShell get-help, aby uzyskać więcej informacji dla każdego z tych poleceń, na przykład ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Poniższy skrypt postanowienia o nazwie grupy administratorów usługi Azure AD **DBA_Group** (identyfikator obiektu `40b79501-b343-44ed-9ce7-da4c8cc7353f`) dla **demo_server** serwera w grupie zasobów o nazwie **grupy-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** akceptuje parametr wejściowy, nazwa wyświetlana usługi Azure AD lub główną nazwę użytkownika. Na przykład ``DisplayName="John Smith"`` i ``DisplayName="johns@contoso.com"``. Dla grup usługi Azure AD tylko usługi Azure AD Nazwa wyświetlana jest obsługiwane.

> [!NOTE]
> Polecenie programu Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` nie uniemożliwiają inicjowania obsługi administracyjnej administratorów usługi Azure AD dla użytkowników nieobsługiwany. Nieobsługiwany użytkownika można udostępnić, ale nie można nawiązać połączenia z bazą danych. 
> 

W poniższym przykładzie użyto opcjonalnego **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Azure AD **ObjectID** jest wymagany, gdy **DisplayName** nie jest unikatowa. Aby pobrać **ObjectID** i **DisplayName** wartości, przy użyciu sekcji usługi Active Directory w klasycznym portalu Azure, a następnie wyświetlić właściwości użytkownika lub grupy.
> 

Poniższy przykład zwraca informacje o bieżącej usługi Azure AD administratora serwera Azure SQL:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Poniższy przykład umożliwia usunięcie administrator usługi Azure AD:

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Administrator usługi Azure Active Directory można również udostępniać za pomocą interfejsów API REST. Aby uzyskać więcej informacji, zobacz [operacje dla operacji bazy danych SQL Azure dla baz danych SQL Azure i dokumentacja interfejsu API REST usługi zarządzania](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>Interfejs wiersza polecenia  
Administrator usługi Azure AD można również udostępniać, wywołując interfejs wiersza polecenia następujące polecenia:
| Polecenie | Opis |
| --- | --- |
|[Tworzenie serwera sql az ad — administrator](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |Inicjuje administrator usługi Azure Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. (Musi być z bieżącej subskrypcji). |
|[Usuń ad administratora serwera sql az](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |Usuwa administratora usługi Azure Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. |
|[Lista ad administratora serwera sql az](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |Zwraca informacje o obecnie skonfigurowane dla serwera Azure SQL lub usługi Azure SQL Data Warehouse administratora usługi Azure Active Directory. |
|[Aktualizacja ad administratora programu sql server az](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |Aktualizuje administratora usługi Active Directory dla serwera Azure SQL lub usługi Azure SQL Data Warehouse. |

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  


## <a name="configure-your-client-computers"></a>Konfigurowanie komputerów klienckich
Na wszystkich komputerach klienckich, z których aplikacje lub użytkowników połączyć się z bazą danych SQL Azure lub usługi Azure SQL Data Warehouse przy użyciu tożsamości usługi Azure AD, należy zainstalować następujące oprogramowanie:

* .NET framework 4.6 lub nowszy z [ https://msdn.microsoft.com/library/5a4x27ek.aspx ](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* Azure Active Directory Authentication Library dla programu SQL Server (**ADALSQL. Biblioteki DLL**) są dostępne w wielu językach (x x86 i amd64) z Centrum pobierania [Microsoft Active Directory Authentication Library dla programu Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

Można spełnienia tych wymagań:

* Zainstalowanie dowolnej [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) lub [programu SQL Server Data Tools dla programu Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) spełnia wymaganie, .NET Framework 4.6.
* Wersja programu SSMS instaluje x86 **ADALSQL. Biblioteki DLL**.
* Program SSDT instaluje wersję amd64 **ADALSQL. Biblioteki DLL**.
* Najnowsze Visual Studio z [programu Visual Studio pobiera](https://www.visualstudio.com/downloads/download-visual-studio-vs) spełnia wymaganie, .NET Framework 4.6, ale nie instaluje wersję wymagane amd64 **ADALSQL. Biblioteki DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Utwórz użytkowników zawartej bazy danych w bazie danych mapowany na tożsamość usługi Azure AD

Uwierzytelnianie usługi Active Directory systemu Azure wymaga od użytkowników bazy danych zostanie utworzony jako użytkowników zawartej bazy danych. Użytkownik zawartej bazy danych na podstawie tożsamości usługi Azure AD jest użytkownika bazy danych, która nie ma nazwy logowania w bazie danych master, i który jest mapowany na tożsamość w katalogu usługi Azure AD, która jest skojarzona z bazą danych. Tożsamość usługi Azure AD może być indywidualne konto użytkownika lub grupę. Aby uzyskać więcej informacji dotyczących użytkowników zawartej bazy danych, zobacz [zawartych użytkowników bazy danych — wprowadzanie Your przenośne bazy danych](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Nie można utworzyć bazy danych użytkowników (z wyjątkiem administratorów) przy użyciu portalu Azure. Role RBAC nie są propagowane do programu SQL Server, SQL Database lub SQL Data Warehouse. Role RBAC Azure są używane do zarządzania zasobami Azure i nie dotyczą uprawnień do bazy danych. Na przykład **programu SQL Server współautora** roli nie powoduje przyznania dostępu do nawiązania połączenia z bazą danych SQL lub SQL Data Warehouse. Bezpośrednio w bazie danych przy użyciu instrukcji języka Transact-SQL, należy udzielić uprawnień dostępu.
>

Aby utworzyć użytkowników platformy Azure AD na podstawie zawarte bazy danych (innego niż administrator serwera, który jest właścicielem bazy danych), połączenie z bazą danych przy użyciu tożsamości usługi Azure AD jako użytkownik z co najmniej **ALTER dowolny użytkownik** uprawnienia. Następnie należy użyć następującej składni języka Transact-SQL:

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* może być główną nazwę użytkownika usługi Azure AD lub nazwę wyświetlaną grupy usługi Azure AD.

**Przykłady:** można utworzyć zawartej bazy danych użytkowników usługi Azure AD reprezentująca federacyjni lub zarządzani użytkownika domeny:
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Aby utworzyć zawartej bazy danych użytkownika reprezentujący usługi Azure AD lub Sfederowanych grupy domeny, należy podać nazwę wyświetlaną grupy zabezpieczeń:
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Aby utworzyć aplikację, która łączy się przy użyciu usługi Azure AD token reprezentujący użytkownika zawartej bazy danych:

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  Nie można bezpośrednio utworzyć użytkownika z usługi Azure Active Directory innego niż Azure Active Directory, który jest skojarzony z subskrypcją platformy Azure. Jednak członkami innych katalogów Active, które są importowanych użytkowników w usłudze Active Directory skojarzony (znany jako użytkownicy zewnętrzni) można dodać do grupy usługi Active Directory w dzierżawie usługi Active Directory. Tworząc użytkownika zawartej bazy danych dla tej grupy usługi Active Directory, użytkowników zewnętrznych usługi Active Directory mogą uzyskać dostęp do bazy danych SQL.   

Aby uzyskać więcej informacji o tworzeniu zawarte bazy danych użytkowników na podstawie tożsamości usługi Azure Active Directory, zobacz [Tworzenie użytkownika (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Usuwanie administratora usługi Azure Active Directory dla usługi Azure SQL server zapobiega każdy użytkownik uwierzytelniania usługi Azure AD połączenia z serwerem. Jeśli to konieczne, korzystanie z niej użytkowników usługi Azure AD można usunąć ręcznie przez administratora bazy danych SQL.   

>  [!NOTE]
>  Jeśli zostanie wyświetlony **upłynął limit czasu połączenia**, musisz ustawić `TransparentNetworkIPResolution` parametr ciągu połączenia na wartość false. Aby uzyskać więcej informacji, zobacz [problem limitu czasu połączenia z platformy .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).   

   
Podczas tworzenia użytkownika bazy danych, użytkownik otrzymuje **CONNECT** uprawnień i mogą łączyć się z bazą danych jako element członkowski **publicznego** roli. Początkowo tylko uprawnienia dostępne dla użytkownika są uprawnienia do **publicznego** roli lub uprawnienia przyznane do żadnej z grup usługi Azure AD są członkami. Po zainicjowanie obsługi usługi Azure AD na podstawie zawarte bazy danych użytkownika, można przyznać użytkownika dodatkowe uprawnienia, tak samo jak Przyznaj uprawnienie do żadnego innego typu użytkownika. Zwykle przyznaj uprawnienia do ról bazy danych, a następnie dodać użytkowników do ról. Aby uzyskać więcej informacji, zobacz [podstawy uprawnień aparatu bazy danych](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Aby uzyskać więcej informacji na temat specjalne role bazy danych SQL, zobacz [Zarządzanie bazami danych i Logowaniami w bazie danych SQL Azure](sql-database-manage-logins.md).
Konto użytkownika domeny federacyjnej, który został zaimportowany do domeny zarządzanej jako użytkownik zewnętrzny musi używać tożsamości domeny zarządzanej.

> [!NOTE]
> Użytkownicy usługi Azure AD są oznaczone w metadanych bazy danych o typie E (EXTERNAL_USER) i grupy z typem X (EXTERNAL_GROUPS). Aby uzyskać więcej informacji, zobacz [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx). 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Połącz do magazynu bazy danych lub dane użytkownika przy użyciu narzędzia SSMS lub program SSDT  
Aby upewnić się, administrator usługi Azure AD jest poprawnie skonfigurowana, połącz się z **wzorca** bazy danych przy użyciu konta administratora usługi Azure AD.
Aby zainicjować obsługę usługi Azure AD na podstawie zawarte bazy danych użytkownika (innego niż administrator serwera, który jest właścicielem bazy danych), połączenia z bazą danych przy użyciu tożsamości usługi Azure AD, który ma dostęp do bazy danych.

> [!IMPORTANT]
> Obsługa uwierzytelniania usługi Azure Active Directory jest dostępny z [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) w programie Visual Studio 2015. Wersja sierpnia 2016 SSMS obejmuje również obsługę uniwersalnych uwierzytelnianie usługi Active Directory, dzięki czemu administratorzy będą musieli przy użyciu połączenia telefonicznego, wiadomości tekstowej, karty inteligentne z numeru pin lub powiadomienie aplikacji mobilnej usługi Multi-Factor Authentication.
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Nawiązywanie połączenia przy użyciu narzędzia SSMS lub program SSDT przy użyciu tożsamości usługi Azure AD  

Poniższe procedury zawierają sposób nawiązywania połączenia z bazą danych SQL przy użyciu tożsamości usługi Azure AD przy użyciu programu SQL Server Management Studio lub narzędzia bazy danych programu SQL Server.

### <a name="active-directory-integrated-authentication"></a>Zintegrowane uwierzytelnianie usługi Active Directory

Użyj tej metody, jeśli użytkownik jest zalogowany do systemu Windows przy użyciu poświadczeń usługi Azure Active Directory z domeny federacyjnej.

1. Uruchom Management Studio lub narzędzia danych i w **Połącz z serwerem** (lub **nawiązywanie połączenia z aparatem bazy danych**) okna dialogowego, **uwierzytelniania** wybierz opcję  **Zintegrowana usługa Active Directory -**. Hasło nie jest potrzebne lub można wprowadzić, ponieważ zobaczy istniejących poświadczeń dla połączenia.   

    ![Wybierz opcję zintegrowanego uwierzytelniania AD][11]
2. Kliknij przycisk **opcje** przycisku i na **właściwości połączenia** strony w **Połącz z bazą danych** wpisz nazwę bazy danych chcesz nawiązać połączenie. ( **Identyfikatora nazwy lub dzierżawy domeny AD**"opcja jest obsługiwana tylko dla **uniwersalnych z połączeniem MFA** opcje, w przeciwnym razie on jest nieaktywna.)  

    ![Wybierz nazwę bazy danych][13]

## <a name="active-directory-password-authentication"></a>Uwierzytelnianie za pomocą hasła w usłudze Active Directory

Użyj tej metody, podczas nawiązywania połączenia z głównej nazwy usługi Azure AD przy użyciu usługi Azure AD zarządzanych domeny. Można również użyć federacyjnych kont bez dostępu do domeny, na przykład podczas pracy zdalnej.

Ta metoda służy do uwierzytelniania na SQL bazy danych/magazynu danych z usługą Azure AD dla natywnego programu Azure AD użytkownicy federacyjni.
Macierzysty użytkownika jest jawnie utworzonej w usłudze Azure AD i uwierzytelnianie przy użyciu nazwy użytkownika i hasła, gdy użytkownik federacyjny jest użytkownikiem systemu Windows którego domena jest Sfederowane przy użyciu usługi Azure AD. Druga metoda (przy użyciu użytkownika i hasło) może służyć gdy użytkownik chce, aby użyć jego poświadczeń systemu windows, ale jego komputer lokalny nie jest dołączony z domeną (np. przy użyciu dostępu zdalnego). W takim przypadku użytkownika systemu Windows może wskazywać na jego konto domeny i hasło i może uwierzytelnić się SQL bazy danych/magazynu danych przy użyciu poświadczeń federacyjnych.

1. Uruchom Management Studio lub narzędzia danych i w **Połącz z serwerem** (lub **nawiązywanie połączenia z aparatem bazy danych**) okna dialogowego, **uwierzytelniania** wybierz opcję  **Usługi Active Directory — hasło**.
2. W **nazwy użytkownika** wpisz nazwę użytkownika usługi Azure Active Directory w formacie **username@domain.com**. Musi to być konto w usłudze Azure Active Directory lub konta z domeny utworzenia federacji z usługą Azure Active Directory.
3. W **hasło** wpisz hasło użytkownika dla konta usługi Azure Active Directory lub konta domeny federacyjnej.

    ![Wybierz uwierzytelnianie hasłem usługi AD][12]
4. Kliknij przycisk **opcje** przycisku i na **właściwości połączenia** strony w **Połącz z bazą danych** wpisz nazwę bazy danych chcesz nawiązać połączenie. (Zobacz grafiki w poprzedniej opcji).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Do łączenia z aplikacji klienta przy użyciu tożsamości usługi Azure AD

Poniższe procedury zawierają sposób nawiązywania połączenia z bazą danych SQL przy użyciu tożsamości usługi Azure AD z aplikacji klienta.

###  <a name="active-directory-integrated-authentication"></a>Zintegrowane uwierzytelnianie usługi Active Directory

Do korzystania ze zintegrowanego uwierzytelniania systemu Windows, należy federacyjną Twojej domeny usługi Active Directory z usługą Azure Active Directory. Aplikacja kliencka (lub usługą) łączenia z bazą danych musi działać na komputerze przyłączonym do domeny z poświadczeniami użytkownika domeny.

Aby połączyć się z bazą danych przy użyciu zintegrowanego uwierzytelnianie i tożsamość usługi Azure AD, słowo kluczowe uwierzytelniania w parametrach połączenia bazy danych musi mieć ustawioną Active Directory Integrated. Poniższy przykład kodu C# używa ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Słowo kluczowe parametrów połączenia ``Integrated Security=True`` nie jest obsługiwana dla łączenia z bazą danych SQL Azure. Podczas tworzenia połączenia ODBC, należy usunąć spacje i ustawić uwierzytelniania "ActiveDirectoryIntegrated".

### <a name="active-directory-password-authentication"></a>Uwierzytelnianie za pomocą hasła w usłudze Active Directory

Aby połączyć się z bazą danych przy użyciu zintegrowanego uwierzytelnianie i tożsamość usługi Azure AD, słowo kluczowe uwierzytelniania musi mieć ustawioną hasła usługi Active Directory. Ciąg połączenia musi zawierać identyfikator/UID użytkownika i hasło/PWD słowa kluczowe i wartości. Poniższy przykład kodu C# używa ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Dowiedz się więcej na temat metod uwierzytelniania usługi Azure AD przy użyciu przykłady kodu pokaz dostępne pod adresem [pokaz GitHub uwierzytelniania w usłudze Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token usługi Azure AD
Ta metoda uwierzytelniania umożliwia usługi warstwy środkowej, aby nawiązać połączenie bazy danych SQL Azure lub usługi Azure SQL Data Warehouse, uzyskując tokenu z usługi Azure Active Directory (AAD). Umożliwia zaawansowane scenariusze, w tym uwierzytelniania opartego na certyfikatach. Należy wykonać cztery podstawowe kroki, aby użyć tokenu uwierzytelniania usługi Azure AD:

1. Zarejestrować aplikację w usłudze Azure Active Directory i pobieranie identyfikatora klienta dla kodu. 
2. Utwórz użytkownika bazy danych reprezentujący aplikacji. (Ukończono wcześniej w kroku 6).
3. Utwórz certyfikat na uruchamia komputer klienta aplikacji.
4. Dodaj certyfikat jako klucz dla aplikacji.

Przykładowy ciąg połączenia:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Aby uzyskać więcej informacji, zobacz [Blog zabezpieczeń programu SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Informacje dotyczące dodawania certyfikatu, zobacz [wprowadzenie do uwierzytelniania opartego na certyfikacie w usłudze Azure Active Directory](../active-directory/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Poniższe instrukcje łączyć się przy użyciu wersji 13.1 programu sqlcmd, który jest dostępny z [Centrum pobierania](http://go.microsoft.com/fwlink/?LinkID=825643).

```
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

