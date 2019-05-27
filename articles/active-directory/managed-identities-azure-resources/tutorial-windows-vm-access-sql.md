---
title: Używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure SQL
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure SQL.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6d5452f23e830ca7a9ffe5ca5ed3d4aa12fb717
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66236045"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Samouczek: Używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób używania tożsamości przypisanej przez system dla maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do usługi Azure SQL. Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej dostępu do serwera Azure SQL
> * Włączanie uwierzytelniania usługi Azure AD na potrzeby serwera SQL
> * Tworzenie w bazie danych zawartego użytkownika, który będzie reprezentować tożsamość maszyny wirtualnej przypisaną przez system
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wysyłania zapytań do serwera Azure SQL

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Udzielanie maszynie wirtualnej dostępu do bazy danych na serwerze Azure SQL

Aby udzielić maszynie wirtualnej dostępu do bazy danych na serwerze SQL platformy Azure, możesz użyć istniejącego serwera SQL lub utworzyć nowy. Aby utworzyć nowy serwer i bazę danych przy użyciu witryny Azure Portal, wykonaj ten [przewodnik Szybki start usługi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). W [dokumentacji usługi Azure SQL ](https://docs.microsoft.com/azure/sql-database/) dostępne są również przewodniki Szybki start, które używają interfejsu wiersza polecenia platformy Azure oraz programu Azure PowerShell.

Istnieją dwa kroki związane z udzielaniem maszynie wirtualnej dostępu do bazy danych:

1. Włącz uwierzytelnianie usługi Azure AD na potrzeby serwera SQL.
2. Tworzenie w bazie danych **zawartego użytkownika**, który będzie reprezentować tożsamość maszyny wirtualnej przypisaną przez system.

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Włączanie uwierzytelniania usługi Azure AD na potrzeby serwera SQL

[Skonfiguruj uwierzytelnianie usługi Azure AD dla serwera SQL](/azure/sql-database/sql-database-aad-authentication-configure), wykonując następujące czynności:

1.  W witrynie Azure Portal wybierz opcję **Serwery SQL** z lewego paska nawigacyjnego.
2.  Kliknij serwer SQL, aby włączyć go do uwierzytelniania usługi Azure AD.
3.  W sekcji **Ustawienia** bloku kliknij opcję **Administrator usługi Active Directory**.
4.  Na pasku poleceń kliknij przycisk **Ustaw administratora**.
5.  Wybierz konto użytkownika usługi Azure AD, które zostanie administratorem serwera, i kliknij przycisk **Wybierz**.
6.  Na pasku poleceń kliknij przycisk **Zapisz**.

## <a name="create-a-contained-user-in-the-database-that-represents-the-vms-system-assigned-identity"></a>Tworzenie w bazie danych zawartego użytkownika, który będzie reprezentować tożsamość maszyny wirtualnej przypisaną przez system

W tym kroku będziesz potrzebować programu [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Przed rozpoczęciem pomocne może być sprawdzenie następujących artykułów, aby uzyskać podstawowe informacje o integracji usługi Azure AD:

- [Uniwersalne uwierzytelnianie przy użyciu usługi SQL Database i SQL Data Warehouse (obsługa SSMS w przypadku uwierzytelniania wieloskładnikowego)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Konfigurowanie i zarządzanie uwierzytelnianiem usługi Azure Active Directory przy użyciu usługi SQL Database lub SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

Usługa SQL Database wymaga unikatowych nazw wyświetlanych usługi AAD. Zatem konta usługi AAD, takie jak użytkownicy, grupy, jednostki usługi (aplikacje) i nazwy maszyn wirtualnych włączone dla tożsamości zarządzanej muszą mieć unikatowe definicje w usłudze AAD w odniesieniu do ich nazw wyświetlanych. Baza danych SQL sprawdza, czy nazwa wyświetlana usługi AAD podczas tworzenia takich użytkowników języka T-SQL i nie jest unikatowa, polecenie nie powiedzie się zażądał Podaj unikatową nazwę wyświetlaną usługi AAD dla danego konta.

1. Uruchom program SQL Server Management Studio.
2. W oknie dialogowym **Łączenie z serwerem** wprowadź nazwę serwera SQL w polu **Nazwa serwera**.
3. W polu **Uwierzytelnianie** wybierz opcję **Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego**.
4. W polu **Nazwa użytkownika** wprowadź nazwę konta usługi Azure AD, które zostało ustawione jako administrator serwera, np. helen@woodgroveonline.com
5. Kliknij pozycję **Opcje**.
6. W polu **Połącz z bazą danych** wpisz nazwę niesystemowej bazy danych, którą chcesz skonfigurować.
7. Kliknij przycisk **Połącz**. Zakończ proces logowania.
8. W **Eksploratorze obiektów** rozwiń folder **Bazy danych**.
9. Kliknij prawym przyciskiem myszy bazę danych użytkownika, a następnie kliknij pozycję **Nowe zapytanie**.
10. W oknie zapytania wpisz następujący wiersz, a następnie kliknij przycisk **Wykonaj** na pasku narzędzi:

    > [!NOTE]
    > Element `VMName` w poniższym poleceniu to nazwa maszyny wirtualnej, dla której w sekcji wymagań wstępnych włączono tożsamość przypisaną przez system.
    
    ```
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```
    
    Polecenie powinno zakończyć się pomyślnie, tworząc zawartego użytkownika na potrzeby tożsamości maszyny wirtualnej przypisanej przez system.
11. Wyczyść okno zapytania, wpisz następujący wiersz, a następnie kliknij przycisk **Wykonaj** na pasku narzędzi:

    > [!NOTE]
    > Element `VMName` w poniższym poleceniu to nazwa maszyny wirtualnej, dla której w sekcji wymagań wstępnych włączono tożsamość przypisaną przez system.
    
    ```
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    Polecenie powinno zakończyć się pomyślnie, udzielając zawartemu użytkownikowi prawa do odczytu całej bazy danych.

Kod uruchomiony na maszynie wirtualnej może teraz pobrać token za pomocą swojej przypisanej przez system tożsamości zarządzanej i użyć tokenu do uwierzytelnienia na serwerze SQL.

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-sql"></a>Uzyskiwanie tokenu dostępu przy użyciu przypisanej przez system tożsamości zarządzanej maszyny wirtualnej oraz używanie go do wywołania usługi Azure SQL

Usługa Azure SQL natywnie obsługuje uwierzytelnianie usługi Azure AD, więc może bezpośrednio akceptować tokeny dostępu pozyskane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Możesz użyć metody **tokena dostępu** dla tworzenia połączeń z serwerem SQL. Jest to część integracji usługi Azure SQL z usługą Azure AD. Takie rozwiązanie różni się od podawania poświadczeń w parametrach połączenia.

Poniżej przedstawiono przykładowy kod .NET otwarcia połączenia do bazy danych SQL przy użyciu tokenu dostępu. Ten kod należy uruchomić na maszynie wirtualnej, aby mógł uzyskać dostęp do punktu końcowego przypisanej przez system tożsamości zarządzanej maszyny wirtualnej. **.NET framework 4.6** lub nowszego jest wymagany przy użyciu metody token dostępu. Zastąp odpowiednio wartości AZURE-SQL-SERVERNAME i DATABASE. Należy pamiętać, identyfikator zasobu dla usługi Azure SQL jest `https://database.windows.net/`.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Alternatywną metodą szybkiego przetestowania kompleksowej konfiguracji bez potrzeby pisania i wdrażania aplikacji na maszynie wirtualnej jest użycie programu PowerShell.

1.  W portalu przejdź do pozycji **Maszyny wirtualne**, a następnie przejdź do swojej maszyny wirtualnej z systemem Windows i w pozycji **Przegląd** kliknij przycisk **Połącz**.
2.  Wprowadź **nazwę użytkownika** i **hasło** dodane podczas tworzenia maszyny wirtualnej z systemem Windows.
3.  Teraz, po utworzeniu **połączenia pulpitu zdalnego** z maszyną wirtualną, otwórz program **PowerShell** w sesji zdalnej.
4.  Używając polecenia `Invoke-WebRequest` programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanej, aby uzyskać token dostępu na potrzeby usługi Azure SQL.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    Skonwertuj odpowiedź z obiektu JSON do obiektu PowerShell. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Wyodrębnij token dostępu z odpowiedzi.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5. Otwórz połączenie z serwerem SQL. Pamiętaj, aby zastąpić wartości AZURE SQL-SERVERNAME i DATABASE.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Następnie utwórz i wyślij zapytanie do serwera. Pamiętaj, aby zastąpić wartość w pozycji TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Sprawdź wartość `$DataSet.Tables[0]`, aby wyświetlić wyniki zapytania.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób używania przypisanej przez system tożsamości zarządzanej w celu uzyskania dostępu do serwera usługi Azure SQL. Aby dowiedzieć się więcej o usłudze Azure SQL Server, zobacz:

> [!div class="nextstepaction"]
> [Usługa Azure SQL Database](/azure/sql-database/sql-database-technical-overview)
