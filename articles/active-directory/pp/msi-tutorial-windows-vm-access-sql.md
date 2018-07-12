---
title: Umożliwia dostęp do usługi Azure SQL z tożsamości usługi Zarządzanej maszyny Wirtualnej Windows
description: Samouczek, który przeprowadzi Cię przez proces dostęp do usługi Azure SQL przy użyciu Windows VM tożsamość usługi zarządzanej (MSI).
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610936"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Umożliwia dostęp do usługi Azure SQL Windows VM tożsamość usługi zarządzanej (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ten samouczek pokazuje, jak używać tożsamość usługi zarządzanej (MSI) dla Windows maszyny wirtualnej (VM), aby uzyskiwać dostęp do serwera Azure SQL. Tożsamości usługi zarządzanej są zarządzane automatycznie przez platformę Azure i umożliwiają uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności Wstaw poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Włączanie tożsamości usługi Zarządzanej w Windows maszyny Wirtualnej 
> * Udzielanie maszynie Wirtualnej dostęp do serwera Azure SQL
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu zapytań serwera usługi Azure SQL

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną Windows w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę Wirtualną Windows.  Można również włączyć tożsamości usługi Zarządzanej istniejącej maszyny wirtualnej.

1.  Kliknij przycisk **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Username** i **hasło** utworzony, w tym miejscu jest poświadczenia, których używasz do logowania do maszyny wirtualnej.
4.  Wybierz odpowiednią **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5.  Aby wybrać nowy **grupy zasobów** w której chcesz utworzyć maszynę wirtualną, wybrać **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. Na stronie ustawień pozostaw wartości domyślne, a następnie kliknij przycisk **OK**.

    ![Tekst ALT obrazu](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włączanie tożsamości usługi Zarządzanej na maszynie Wirtualnej 

Zarządzanej maszyny Wirtualnej umożliwia uzyskiwanie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie tożsamości usługi Zarządzanej informuje Azure w celu utworzenia tożsamości zarządzanej dla maszyny Wirtualnej. Włączanie tożsamości usługi Zarządzanej w sposób niewidoczny wykonuje dwie czynności: instaluje rozszerzenia tożsamości usługi Zarządzanej maszyny Wirtualnej na maszynie Wirtualnej oraz umożliwia tożsamości usługi Zarządzanej w usłudze Azure Resource Manager.

1.  Wybierz **maszyny wirtualnej** chcesz włączyć tożsamości usługi Zarządzanej.  
2.  Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**. 
3.  Zostanie wyświetlony **tożsamości usługi zarządzanej**. Aby zarejestrować i włączyć plik MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz pozycję nie. 
4.  Upewnij się, możesz kliknąć pozycję **Zapisz** Aby zapisać konfigurację.  
    ![Tekst ALT obrazu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Jeśli użytkownik chce Sprawdź i upewnij się, jakie rozszerzenia są na tej maszynie Wirtualnej, kliknij przycisk **rozszerzenia**. Jeśli plik MSI jest włączona, następnie **ManagedIdentityExtensionforWindows** pojawia się na liście.

    ![Tekst ALT obrazu](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Udzielanie maszynie Wirtualnej dostęp do bazy danych na serwerze Azure SQL

Teraz można przyznać dostęp maszyny Wirtualnej do bazy danych na serwerze Azure SQL.  W tym kroku możesz użyć istniejącego serwera SQL lub Utwórz nową.  Aby utworzyć nowego serwera i bazy danych przy użyciu portalu Azure, postępuj zgodnie z tym [szybkiego startu usługi Azure SQL](~/articles/sql-database/sql-database-get-started-portal.md). Dostępne są także Przewodniki Szybki Start, użyj wiersza polecenia platformy Azure i programu Azure PowerShell w [dokumentację usługi Azure SQL](~/articles/sql-database/index.yml).

Istnieją trzy kroki, aby udzielanie maszynie Wirtualnej dostęp do bazy danych:
1.  Utwórz grupę w usłudze Azure AD i wprowadzić Zarządzanej maszyny Wirtualnej jako członka grupy.
2.  Włącz uwierzytelnianie usługi Azure AD dla programu SQL server.
3.  Tworzenie **zawartego użytkownika** w bazie danych, który reprezentuje grupę usługi Azure AD.

> [!NOTE]
> Zazwyczaj należy utworzyć zawartego użytkownika, który mapuje bezpośrednio do tożsamości usługi Zarządzanej maszyny Wirtualnej.  Obecnie usługa Azure SQL nie zezwala na jednostki usługi platformy Azure AD, który reprezentuje Zarządzanej maszyny Wirtualnej, które mają być mapowane do zawartego użytkownika.  Obsługiwane obejścia możesz wprowadzić Zarządzanej maszyny Wirtualnej jako członka grupy usługi Azure AD, a następnie utworzenia zawartego użytkownika w bazie danych, która reprezentuje grupę.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Utwórz grupę w usłudze Azure AD i wprowadzić Zarządzanej maszyny Wirtualnej jako członka grupy

Możesz użyć istniejącej grupy usługi Azure AD lub utworzyć nową przy użyciu programu Azure AD PowerShell.  

Najpierw zainstaluj [usługi Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modułu. Następnie zaloguj się przy użyciu `Connect-AzureAD`, uruchom następujące polecenie, aby utworzyć grupę i zapisz go w zmiennej:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

Dane wyjściowe wyglądają podobnie do poniższego, który również sprawdza, czy wartość zmiennej:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Następnie dodaj tożsamości usługi Zarządzanej maszyny Wirtualnej do grupy.  Potrzebujesz MSI **ObjectId**, który można można uzyskać za pomocą programu Azure PowerShell.  Najpierw pobierz [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Następnie zaloguj się przy użyciu `Connect-AzureRmAccount`, i uruchom następujące polecenia, aby:
- Upewnij się, że kontekst sesji jest ustawiona na odpowiedniej subskrypcji platformy Azure, jeśli masz wiele migawek.
- Wyświetla listę dostępnych zasobów w subskrypcji platformy Azure w, sprawdź, grupy zasobów poprawne i nazwy maszyn wirtualnych.
- Pobierz właściwości tożsamości usługi Zarządzanej maszyny Wirtualnej, przy użyciu odpowiednie wartości dla `<RESOURCE-GROUP>` i `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

Dane wyjściowe wyglądają podobnie do poniższego, który również sprawdza, czy usługa identyfikator obiektu jednostki Zarządzanej maszyny Wirtualnej:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Teraz Dodaj Zarządzanej maszyny Wirtualnej do grupy.  Nazwy głównej usługi można tylko dodać do grupy przy użyciu programu Azure AD PowerShell.  Uruchom następujące polecenie:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Jeśli możesz również sprawdzić członkostwa w grupie później, dane wyjściowe wyglądają następująco:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Włącz uwierzytelnianie usługi Azure AD dla programu SQL server

Po utworzeniu grupy i dodać Zarządzanej maszyny Wirtualnej do członkostwa, możesz [Konfigurowanie uwierzytelniania usługi Azure AD dla programu SQL server](~/articles/sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) wykonując następujące czynności:

1.  W witrynie Azure portal wybierz **serwerów SQL** nawigacji po lewej stronie.
2.  Kliknij pozycję serwera SQL w celu włączenia uwierzytelniania usługi Azure AD.
3.  W **ustawienia** części bloku kliknij pozycję **administratora usługi Active Directory**.
4.  Na pasku poleceń kliknij **Ustaw administratora**.
5.  Wybierz konto użytkownika usługi Azure AD, można wprowadzić administratora serwera, a następnie kliknij przycisk **wybierz.**
6.  Na pasku poleceń kliknij **Zapisz.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Utwórz użytkownika zawartej bazy danych, który reprezentuje grupę usługi Azure AD

W tym kroku dalej, konieczne będzie [programu Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Przed rozpoczęciem, może to być również przydatne zapoznać się z następującymi artykułami, aby uzyskać podstawowe informacje dotyczące integracji z usługą Azure AD:

- [Uniwersalne uwierzytelnianie przy użyciu bazy danych SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](~/articles/sql-database/sql-database-ssms-mfa-authentication.md)
- [Konfigurowanie i zarządzanie nimi w usłudze Azure Active Directory uwierzytelnianie przy użyciu bazy danych SQL Database lub SQL Data Warehouse](~/articles/sql-database/sql-database-aad-authentication-configure.md)

1.  Uruchom program SQL Server Management Studio.
2.  W **Połącz z serwerem** Enter, program SQL server nazwa w oknie dialogowym **nazwy serwera** pola.
3.  W **uwierzytelniania** pól, zaznacz **usługi Active Directory — uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**.
4.  W **nazwa_użytkownika** wprowadź nazwę konta usługi Azure AD, które można ustawić jako administratora serwera, na przykład helen@woodgroveonline.com
5.  Kliknij pozycję **Opcje**.
6.  W **Połącz z bazą danych** wprowadź nazwę bazy danych-system, którą chcesz skonfigurować.
7.  Kliknij przycisk **Połącz**.  Zakończenie procesu logowania.
8.  W **Eksplorator obiektów**, rozwiń węzeł **baz danych** folderu.
9.  Kliknij prawym przyciskiem myszy bazę danych użytkownika, a następnie kliknij przycisk **nowe zapytanie**.
10.  W oknie kwerendy wpisz następujący wiersz, a następnie kliknij przycisk **Execute** na pasku narzędzi:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     Polecenie powinno zakończyć się pomyślnie, tworząc zawartego użytkownika dla grupy.
11.  Wyczyść okno zapytania, wprowadź następujące polecenie, a następnie kliknij przycisk **Execute** na pasku narzędzi:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     Polecenie powinno zakończyć się pomyślnie, udzielanie zawartego użytkownika umożliwia czytanie całą bazę danych.

Teraz kod działający na maszynie wirtualnej i pobrania tokenu z pliku MSI i uwierzytelniania programu SQL server przy użyciu tokenu.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu wywołania Azure SQL 

Usługi Azure SQL natywnie obsługuje usługi Azure AD uwierzytelniania, dzięki czemu bezpośrednio może akceptować tokeny dostępu uzyskany przy użyciu pliku MSI.  Możesz użyć **token dostępu** metody tworzenia połączenia do bazy danych SQL.  To jest częścią integracji usługi Azure SQL z usługą Azure AD i różni się od dostarczenie poświadczeń połączenia.

Poniżej przedstawiono przykładowy kod .net otwarcia połączenia do bazy danych SQL przy użyciu tokenu dostępu.  Ten kod, należy uruchomić na maszynie Wirtualnej, aby mieć możliwość dostępu do endpoint Zarządzanej maszyny Wirtualnej.  **.NET framework 4.6** lub nowszego jest wymagany przy użyciu metody token dostępu.  Zastąp wartości SERVERNAME-AZURE-SQL i bazy danych w związku z tym.  Należy pamiętać, identyfikator zasobu dla usługi Azure SQL jest "https://database.windows.net/".

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

Szybko Testuj ustawienia typu end to end bez konieczności pisania i wdrażania aplikacji na maszynie Wirtualnej jest również przy użyciu programu PowerShell.

1.  W portalu, przejdź do **maszyn wirtualnych** i przejdź do maszyny wirtualnej Windows w **Przegląd**, kliknij przycisk **Connect**. 
2.  Wprowadź w swojej **Username** i **hasło** dla którego można dodać podczas tworzenia maszyny Wirtualnej Windows. 
3.  Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną Otwórz **PowerShell** w sesji zdalnej. 
4.  Przy użyciu programu PowerShell `Invoke-WebRequest`, wysłać żądanie do lokalnego punktu końcowego pliku MSI do uzyskania tokenu dostępu dla usługi Azure SQL.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    Konwertowanie odpowiedzi z obiektu JSON na obiekt programu PowerShell. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Wyodrębnij token dostępu z odpowiedzi.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Otwórz połączenie z programem SQL server. Pamiętaj, aby zastąpić wartości SERVERNAME-AZURE-SQL i bazy danych.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Następnie utwórz i wysłać zapytanie do serwera.  Pamiętaj zastąpić wartość dla tabeli.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Sprawdź wartość `$DataSet.Tables[0]` do wyświetlenia wyników zapytania.  Gratulacje, została kwerenda bazy danych, korzystając z tożsamości usługi Zarządzanej maszyny Wirtualnej, jak i bez konieczności podać poświadczenia!

## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](msi-overview.md).
- Dowiedz się więcej o [Azure SQL, obsługę uwierzytelniania usługi Azure AD](~/articles/sql-database/sql-database-aad-authentication.md).
- Dowiedz się więcej o [konfigurowania usługi Azure SQL obsługę uwierzytelniania usługi Azure AD](~/articles/sql-database/sql-database-aad-authentication-configure.md).
- Dowiedz się więcej o [uwierzytelnianiem i dostępem w programie SQL server](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.
