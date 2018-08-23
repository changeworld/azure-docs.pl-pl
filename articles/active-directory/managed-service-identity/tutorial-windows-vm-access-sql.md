---
title: Uzyskiwanie dostępu do usługi Azure SQL przy użyciu tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows
description: Samouczek przedstawiający proces użycia tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure SQL.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: ca920a93d754254390a5c5c5a066be3144b47fc7
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "41919814"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-sql"></a>Samouczek: używanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure SQL

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób używania tożsamości usługi zarządzanej dla maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do serwera Azure SQL. Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows 
> * Udzielanie maszynie wirtualnej dostępu do serwera Azure SQL
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wysyłania zapytań do serwera Azure SQL

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Tworzenie maszyny wirtualnej z systemem Windows w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę wirtualną z systemem Windows.  Możesz również włączyć tożsamość usługi zarządzanej na istniejącej maszynie wirtualnej.

1.  Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Nazwa użytkownika** i **Hasło** utworzone w tym miejscu są poświadczeniami używanymi do logowania do maszyny wirtualnej.
4.  Wybierz odpowiednią **Subskrypcję** dla maszyny wirtualnej z listy rozwijanej.
5.  Aby wybrać nową **Grupę zasobów**, w której chcesz utworzyć maszynę wirtualną, wybierz opcję **Utwórz nową**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. Na stronie ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

    ![Alternatywny tekst obrazu](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej 

Tożsamość usługi zarządzanej maszyny wirtualnej umożliwia uzyskanie tokenów dostępu z usługi Azure AD bez potrzeby umieszczania poświadczeń w kodzie. Włączenie tożsamości usługi zarządzanej powiadamia platformę Azure o potrzebie utworzenia tożsamości zarządzanej dla maszyny wirtualnej. Włączenie tożsamości usługi zarządzanej na maszynie wirtualnej powoduje wykonanie dwóch niejawnych czynności: zapewnia rejestrację maszyny wirtualnej w usłudze Azure Active Directory, aby utworzyć tożsamość zarządzaną, oraz konfiguruje tożsamość na maszynie wirtualnej.

1.  Wybierz **maszynę wirtualną**, na której chcesz włączyć tożsamość usługi zarządzanej.  
2.  Na lewym pasku nawigacyjnym kliknij opcję **Konfiguracja**. 
3.  Zobaczysz ekran **Tożsamość usługi zarządzanej**. Aby zarejestrować i włączyć tożsamość usługi zarządzanej, wybierz pozycję **Tak**. Jeśli chcesz ją wyłączyć, wybierz pozycję Nie. 
4.  Pamiętaj, aby kliknąć przycisk **Zapisz** w celu zapisania konfiguracji.  
    ![Alternatywny tekst obrazu](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Udzielanie maszynie wirtualnej dostępu do bazy danych na serwerze Azure SQL

Teraz możesz udzielić maszynie wirtualnej dostępu do bazy danych na serwerze Azure SQL.  W tym kroku możesz użyć istniejącego serwera SQL lub utworzyć nowy serwer.  Aby utworzyć nowy serwer i bazę danych przy użyciu witryny Azure Portal, wykonaj ten [przewodnik Szybki start usługi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). W [dokumentacji usługi Azure SQL ](https://docs.microsoft.com/azure/sql-database/) dostępne są również przewodniki Szybki start, które używają interfejsu wiersza polecenia platformy Azure oraz programu Azure PowerShell.

Istnieją trzy kroki związane z udzielaniem maszynie wirtualnej dostępu do bazy danych:
1.  Utwórz grupę w usłudze Azure AD i ustaw tożsamość usługi zarządzanej maszyny wirtualnej jako członka grupy.
2.  Włącz uwierzytelnianie usługi Azure AD na potrzeby serwera SQL.
3.  Utwórz **zawartego użytkownika** w bazie danych, który będzie reprezentować grupę usługi Azure AD.

> [!NOTE]
> Zazwyczaj tworzy się zawartego użytkownika, który jest mapowany bezpośrednio do tożsamości usługi zarządzanej maszyny wirtualnej.  Obecnie usługa Azure SQL nie zezwala na mapowanie jednostki usługi Azure AD reprezentującej tożsamość usługi zarządzanej na maszynie wirtualnej do zawartego użytkownika.  W ramach obsługiwanego obejścia możesz ustawić tożsamość usługi zarządzanej na maszynie wirtualnej jako członka grupy usługi Azure AD, a następnie utworzyć zawartego użytkownika w bazie danych, który reprezentuje grupę.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-managed-service-identity-a-member-of-the-group"></a>Tworzenie grupy w usłudze Azure AD i ustawianie tożsamości usługi zarządzanej maszyny wirtualnej jako członka grupy

Możesz użyć istniejącej grupy usługi Azure AD lub utworzyć nową grupę przy użyciu programu Azure AD PowerShell.  

Najpierw zainstaluj moduł [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Następnie zaloguj się przy użyciu polecenia `Connect-AzureAD` i uruchom następujące polecenie, aby utworzyć grupę, a następnie zapisz ją w zmiennej:

```powershell
$Group = New-AzureADGroup -DisplayName "VM Managed Service Identity access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

Dane wyjściowe wyglądają podobnie do poniższych, co również umożliwia sprawdzenie wartości zmiennej:

```powershell
$Group = New-AzureADGroup -DisplayName "VM Managed Service Identity access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM Managed Service Identity access to SQL
```

Następnie dodaj do grupy tożsamość usługi zarządzanej maszyny wirtualnej.  Potrzebujesz identyfikatora **ObjectId** tożsamości usługi zarządzanej, który możesz pobrać przy użyciu programu Azure PowerShell.  Najpierw pobierz program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Następnie zaloguj się przy użyciu polecenia `Connect-AzureRmAccount` i uruchom poniższe polecenia, aby:
- upewnić się, że kontekst sesji jest ustawiony na żądaną subskrypcję platformy Azure, jeżeli masz ich wiele;
- wyświetlić listę dostępnych zasobów w subskrypcji platformy Azure, aby zweryfikować prawidłową grupę zasobów i nazwy maszyn wirtualnych.
- Pobierz właściwości maszyny wirtualnej tożsamości zarządzanej, korzystając z odpowiednich wartości dla `<RESOURCE-GROUP>` i `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

Dane wyjściowe wyglądają podobnie do poniższych, co również umożliwia sprawdzenie identyfikatora obiektu jednostki usługi tożsamości usługi zarządzanej maszyny wirtualnej:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Teraz dodaj do grupy tożsamość usługi zarządzanej maszyny wirtualnej.  Możesz dodać tylko jednostkę usługi do grupy przy użyciu programu Azure AD PowerShell.  Uruchom następujące polecenie:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Jeżeli później również zbadasz członkostwo grupy, dane wyjściowe będą wyglądać następująco:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Włączanie uwierzytelniania usługi Azure AD na potrzeby serwera SQL

Teraz po utworzeniu grupy i dodaniu tożsamości usługi zarządzanej maszyny wirtualnej do członkostwa możesz [skonfigurować uwierzytelnianie usługi Azure AD dla serwera SQL](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server) przy użyciu następujących instrukcji:

1.  W witrynie Azure Portal wybierz opcję **Serwery SQL** z lewego paska nawigacyjnego.
2.  Kliknij serwer SQL, aby włączyć go do uwierzytelniania usługi Azure AD.
3.  W sekcji **Ustawienia** bloku kliknij opcję **Administrator usługi Active Directory**.
4.  Na pasku poleceń kliknij przycisk **Ustaw administratora**.
5.  Wybierz konto użytkownika usługi Azure AD, które zostanie administratorem serwera, i kliknij przycisk **Wybierz**.
6.  Na pasku poleceń kliknij przycisk **Zapisz**.

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Tworzenie w bazie danych użytkownika zawartego, który będzie reprezentować grupę usługi Azure AD

W tym kroku będziesz potrzebować programu [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Przed rozpoczęciem pomocne może być sprawdzenie następujących artykułów, aby uzyskać podstawowe informacje o integracji usługi Azure AD:

- [Uniwersalne uwierzytelnianie przy użyciu usługi SQL Database i SQL Data Warehouse (obsługa SSMS w przypadku uwierzytelniania wieloskładnikowego)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Konfigurowanie i zarządzanie uwierzytelnianiem usługi Azure Active Directory przy użyciu usługi SQL Database lub SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

1.  Uruchom program SQL Server Management Studio.
2.  W oknie dialogowym **Łączenie z serwerem** wprowadź nazwę serwera SQL w polu **Nazwa serwera**.
3.  W polu **Uwierzytelnianie** wybierz opcję **Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego**.
4.  W polu **Nazwa użytkownika** wprowadź nazwę konta usługi Azure AD, które zostało ustawione jako administrator serwera, np. helen@woodgroveonline.com
5.  Kliknij pozycję **Opcje**.
6.  W polu **Połącz z bazą danych** wpisz nazwę niesystemowej bazy danych, którą chcesz skonfigurować.
7.  Kliknij przycisk **Połącz**.  Zakończ proces logowania.
8.  W **Eksploratorze obiektów** rozwiń folder **Bazy danych**.
9.  Kliknij prawym przyciskiem myszy bazę danych użytkownika, a następnie kliknij pozycję **Nowe zapytanie**.
10.  W oknie zapytania wpisz następujący wiersz, a następnie kliknij przycisk **Wykonaj** na pasku narzędzi:
    
     ```
     CREATE USER [VM Managed Service Identity access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     Polecenie powinno zakończyć się pomyślnie, tworząc zawartego użytkownika grupy.
11.  Wyczyść okno zapytania, wpisz następujący wiersz, a następnie kliknij przycisk **Wykonaj** na pasku narzędzi:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM Managed Service Identity access to SQL]
     ```

     Polecenie powinno zakończyć się pomyślnie, udzielając zawartemu użytkownikowi prawa do odczytu całej bazy danych.

Kod uruchomiony na maszynie wirtualnej może teraz pobrać token z tożsamości usługi zarządzanej i użyć tokenu do uwierzytelnienia na serwerze SQL.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania usługi Azure SQL 

Usługa Azure SQL natywnie obsługuje uwierzytelnianie usługi Azure AD, więc może bezpośrednio akceptować tokeny dostępu pozyskane przy użyciu tożsamości usługi zarządzanej.  Możesz użyć metody **tokena dostępu** dla tworzenia połączeń z serwerem SQL.  Jest to część integracji usługi Azure SQL z usługą Azure AD. Takie rozwiązanie różni się od podawania poświadczeń w parametrach połączenia.

Poniżej przedstawiono przykład kodu platformy .Net otwierającego połączenie z serwerem SQL przy użyciu tokenu dostępu.  Ten kod należy uruchomić na maszynie wirtualnej, aby mógł uzyskać dostęp do punktu końcowego tożsamości usługi zarządzanej maszyny wirtualnej.  Wymagany jest program **.Net Framework 4.6** (lub nowszy) w celu użycia metody tokenu dostępu.  Zastąp odpowiednio wartości AZURE-SQL-SERVERNAME i DATABASE.  Pamiętaj, że identyfikator zasobu usługi Azure SQL to „https://database.windows.net/”.

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
    // Call Managed Service Identity endpoint.
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
4.  Używając polecenia `Invoke-WebRequest` programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości usługi zarządzanej, aby uzyskać token dostępu na potrzeby usługi Azure SQL.

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

5.  Otwórz połączenie z serwerem SQL. Pamiętaj, aby zastąpić wartości AZURE SQL-SERVERNAME i DATABASE.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Następnie utwórz i wyślij zapytanie do serwera.  Pamiętaj, aby zastąpić wartość w pozycji TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Sprawdź wartość `$DataSet.Tables[0]`, aby wyświetlić wyniki zapytania.  Gratulacje — wysłano zapytanie do bazy danych przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej bez potrzeby podawania poświadczeń.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia tożsamości usługi zarządzanej w celu uzyskania dostępu do serwera usługi Azure SQL.  Aby dowiedzieć się więcej o usłudze Azure SQL Server, zobacz:

> [!div class="nextstepaction"]
>[Usługa Azure SQL Database](/azure/sql-database/sql-database-technical-overview)
