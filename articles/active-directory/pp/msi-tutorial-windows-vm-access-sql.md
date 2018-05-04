---
title: Umożliwia dostęp do usługi Azure SQL MSI maszyny Wirtualnej systemu Windows
description: Samouczek, który przeprowadzi Cię przez proces otwieranie Azure SQL za pomocą systemu Windows maszyny Wirtualnej zarządzane usługi tożsamości (MSI).
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 682998bb979c9b155b7b1389d8f605018ae135b6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Umożliwia dostęp do usługi Azure SQL systemu Windows maszyny Wirtualnej zarządzane usługi tożsamości (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ten samouczek pokazuje, jak używać zarządzane tożsamości usługi (MSI) dla systemu Windows maszyny wirtualnej (VM) dostęp do serwera Azure SQL. Zarządzane tożsamości usługi automatycznie są zarządzane przez usługę Azure i umożliwiają uwierzytelniania w usłudze, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności wstawić poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Włącz MSI na Windows maszyny Wirtualnej 
> * Przyznać uprawnienia maszyny Wirtualnej na serwerze Azure SQL
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i użyj go do badania serwera Azure SQL

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Windows w nowej grupy zasobów

W tym samouczku utworzymy nową maszynę Wirtualną systemu Windows.  Można również włączyć MSI na istniejącej maszyny Wirtualnej.

1.  Kliknij przycisk **Utwórz zasób** w lewym górnym rogu portalu Azure.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Username** i **hasło** utworzony, w tym miejscu jest poświadczeń umożliwia logowanie do maszyny wirtualnej.
4.  Wybierz odpowiednią **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5.  Aby wybrać nowy **grupy zasobów** w której chcesz utworzyć maszynę wirtualną, wybrać **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. Na stronie ustawienia Zachowaj wartości domyślne, a następnie kliknij przycisk **OK**.

    ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włącz MSI na maszynie Wirtualnej 

MSI maszyny Wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie MSI informuje Azure w celu utworzenia tożsamości zarządzanego dla maszyny Wirtualnej. W obszarze obejmuje włączenie MSI wykonuje dwie czynności: instalacji rozszerzenia maszyny Wirtualnej MSI w maszynie Wirtualnej, i umożliwia korzystanie z pliku MSI usługi Azure Resource Manager.

1.  Wybierz **maszyny wirtualnej** chcesz włączyć MSI.  
2.  Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**. 
3.  Zostanie wyświetlony **zarządzane tożsamość usługi**. Aby zarejestrować i włączyć MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz opcję nie. 
4.  Upewnij się, możesz kliknąć przycisk **zapisać** Aby zapisać konfigurację.  
    ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Jeśli chcesz sprawdzić i sprawdź, które rozszerzenia są na tej maszynie Wirtualnej, kliknij przycisk **rozszerzenia**. Jeśli MSI jest włączona, następnie **ManagedIdentityExtensionforWindows** pojawią się na liście.

    ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Przyznać uprawnienia maszyny Wirtualnej do bazy danych na serwerze Azure SQL

Teraz można przyznać dostęp maszyny Wirtualnej do bazy danych na serwerze Azure SQL.  Dla tego kroku można użyć istniejącego serwera SQL lub Utwórz nową.  Aby utworzyć nowy serwer i bazy danych przy użyciu portalu Azure, wykonaj to [Szybki Start Azure SQL](~/articles/sql-database/sql-database-get-started-portal.md). Dostępne są także Przewodniki Szybki Start, którego wiersza polecenia platformy Azure i programu Azure PowerShell w programie [dokumentacji programu SQL Azure](~/articles/sql-database/index.yml).

Istnieją trzy kroki w celu przyznania dostępu maszyny Wirtualnej do bazy danych:
1.  Utwórz grupę w usłudze Azure AD i upewnij MSI maszyny Wirtualnej do grupy.
2.  Włącz uwierzytelnianie usługi Azure AD dla programu SQL server.
3.  Utwórz **zawartego użytkownika** w bazie danych, który reprezentuje grupę usługi Azure AD.

> [!NOTE]
> Zazwyczaj należy utworzyć zawartych w niej użytkownika, który mapuje bezpośrednio do maszyny Wirtualnej MSI.  Azure SQL nie zezwala obecnie główną usługi Azure AD, reprezentujący MSI maszyny Wirtualnej można mapować na zawartego użytkownika.  Jako obejście obsługiwanych możesz upewnij MSI maszyny Wirtualnej jest członkiem grupy usługi Azure AD, a następnie utworzenia zawartego użytkownika w bazie danych, który reprezentuje grupę.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Utwórz grupę w usłudze Azure AD i upewnij MSI maszyny Wirtualnej do grupy

Można użyć istniejącej grupy usługi Azure AD, lub Utwórz nową przy użyciu programu Azure AD PowerShell.  

Najpierw zainstaluj [programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modułu. Następnie zaloguj się przy użyciu `Connect-AzureAD`, uruchom następujące polecenie, aby utworzyć grupę i zapisz go w zmiennej:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

Dane wyjściowe wygląda podobnie do następującej, który sprawdza również wartość zmiennej:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Następnie dodaj MSI maszyny Wirtualnej do grupy.  Należy MSI **ObjectId**, który można pobrać użyciu programu Azure PowerShell.  Najpierw pobierz [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Następnie zaloguj się przy użyciu `Connect-AzureRmAccount`, i uruchom następujące polecenia, aby:
- Upewnij się, że kontekst sesji ma ustawioną wartość odpowiednią subskrypcji platformy Azure, jeśli masz wiele migawek.
- Sprawdź w nazw maszyn wirtualnych i grupy zasobów poprawne, wyświetlić listę dostępnych zasobów w Twojej subskrypcji platformy Azure.
- Pobierz właściwości maszyny Wirtualnej MSI, przy użyciu wartości odpowiednich dla `<RESOURCE-GROUP>` i `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

Dane wyjściowe wygląda podobnie do następującej, który sprawdza również usługi identyfikator obiektu podmiotu zabezpieczeń msi maszyny Wirtualnej:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Teraz Dodaj MSI maszyny Wirtualnej do grupy.  Można tylko dodać nazwy głównej usługi do grupy przy użyciu programu Azure AD PowerShell.  Uruchom następujące polecenie:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Jeśli możesz również sprawdzić członkostwa w grupie później, dane wyjściowe wygląda następująco:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Włącz uwierzytelnianie usługi Azure AD dla programu SQL server

Po utworzeniu grupy i dodać MSI maszyny Wirtualnej do członkostwa, możesz [Konfigurowanie uwierzytelniania usługi Azure AD dla programu SQL server](~/articles/sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) wykonując następujące czynności:

1.  W portalu Azure wybierz **serwerów SQL** z nawigacji po lewej stronie.
2.  Kliknij przycisk programu SQL server należy włączyć funkcję uwierzytelniania usługi Azure AD.
3.  W **ustawienia** sekcji bloku, kliknij przycisk **administratora usługi Active Directory**.
4.  Na pasku poleceń, kliknij przycisk **ustawić administratora**.
5.  Wybierz konto użytkownika usługi Azure AD można wprowadzać administrator serwera, a następnie kliknij przycisk **wybierz.**
6.  Na pasku poleceń, kliknij przycisk **zapisać.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Utworzenia zawartego użytkownika w bazie danych, który reprezentuje grupę usługi Azure AD

W tym kroku dalej, konieczne będzie [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Przed rozpoczęciem należy również mogą być pomocne poniższe artykuły podstawowe informacje dotyczące integracji z usługą Azure AD:

- [Uwierzytelnianiem uniwersalnym z bazy danych SQL i magazyn danych SQL (Obsługa SSMS MFA)](~/articles/sql-database/sql-database-ssms-mfa-authentication.md)
- [Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory z bazą danych SQL lub SQL Data Warehouse](~/articles/sql-database/sql-database-aad-authentication-configure.md)

1.  Uruchom program SQL Server Management Studio.
2.  W **Połącz z serwerem** okna dialogowego, wprowadź nazwę serwera SQL w **nazwy serwera** pola.
3.  W **uwierzytelniania** pól, zaznacz **usługi Active Directory - uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**.
4.  W **nazwy użytkownika** wprowadź nazwę konta usługi Azure AD, które można ustawić jako administrator serwera, na przykład helen@woodgroveonline.com
5.  Kliknij pozycję **Opcje**.
6.  W **Połącz z bazą danych** wprowadź nazwę bazy danych nie ma systemu do skonfigurowania.
7.  Kliknij przycisk **Połącz**.  Zakończenie procesu logowania.
8.  W **Eksplorator obiektów**, rozwiń węzeł **baz danych** folderu.
9.  Kliknij prawym przyciskiem myszy bazę danych użytkownika, a następnie kliknij przycisk **nowe zapytanie**.
10.  W oknie zapytania, wprowadź następujące polecenie, a następnie kliknij przycisk **Execute** na pasku narzędzi:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     Polecenie powinno zakończyć się pomyślnie, utworzenia zawartego użytkownika w grupie.
11.  Wyczyść okno kwerendy, wprowadź następujące polecenie, a następnie kliknij przycisk **Execute** na pasku narzędzi:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     Polecenie powinno zakończyć się pomyślnie, udzielanie zawartego użytkownika możliwości odczytu całej bazy danych.

Kodu uruchomionego na maszynie wirtualnej można teraz uzyskać token z pliku MSI i używać tokenu do uwierzytelniania programu SQL server.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i użyć go do wywołania Azure SQL 

Azure SQL natywnie obsługuje usługi Azure AD uwierzytelnianie, więc bezpośrednio może akceptować tokeny dostępu uzyskany przy użyciu Instalatora MSI.  Możesz użyć **token dostępu** metody tworzenia połączenia SQL.  To jest częścią usługi Azure SQL integracji z usługą Azure AD i różni się od podawania poświadczeń w parametrach połączenia.

Oto przykład kodu .net otwarcia połączenia SQL przy użyciu tokenu dostępu.  Ten kod należy uruchomić na maszynie Wirtualnej, aby można było uzyskać dostępu do punktu końcowego maszyny Wirtualnej MSI.  **.NET framework 4.6** lub nowszego jest wymagany przy użyciu metody tokenu dostępu.  Zastąp wartości AZURE-SQL-SERVERNAME i bazy danych w związku z tym.  Należy zwrócić uwagę identyfikator zasobu Azure SQL jest "https://database.windows.net/".

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call MSI endpoint.
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

Można również szybko Testuj ustawienia pełnego bez konieczności zapisu i wdrażanie aplikacji na maszynie Wirtualnej jest przy użyciu programu PowerShell.

1.  W portalu, przejdź do **maszyn wirtualnych** i przejdź do maszyny wirtualnej systemu Windows i w **omówienie**, kliknij przycisk **Connect**. 
2.  Wprowadź w Twojej **Username** i **hasło** dla którego zostanie dodane po utworzeniu maszyny Wirtualnej systemu Windows. 
3.  Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, otwórz **PowerShell** w sesji zdalnej. 
4.  Przy użyciu programu PowerShell w `Invoke-WebRequest`, Utwórz żądanie lokalnego punktu końcowego MSI, aby uzyskać dostęp do tokenu dla bazy danych SQL Azure.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    Konwertuj odpowiedzi z obiektu JSON na obiekt programu PowerShell. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Token dostępu wyodrębnienie z odpowiedzi.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Otwórz połączenie z programem SQL server. Pamiętaj, aby zastąpić wartości AZURE-SQL-SERVERNAME i bazy danych.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Następnie tworzyć i wysyłać zapytania do serwera.  Pamiętaj, aby zastąpić wartość dla tabeli.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Sprawdź wartość `$DataSet.Tables[0]` , aby wyświetlić wyniki zapytania.  Gratulacje, zostały zapytanie bazy danych używającej MSI maszyny Wirtualnej i bez konieczności aby podać poświadczenia!

## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](msi-overview.md).
- Dowiedz się więcej o [Azure SQL obsługę uwierzytelniania usługi Azure AD](~/articles/sql-database/sql-database-aad-authentication.md).
- Dowiedz się więcej o [Konfigurowanie Azure SQL obsługę uwierzytelniania usługi Azure AD](~/articles/sql-database/sql-database-aad-authentication-configure.md).
- Dowiedz się więcej o [uwierzytelniania i dostępu w programie SQL server](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
