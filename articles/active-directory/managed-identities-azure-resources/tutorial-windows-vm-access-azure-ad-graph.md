---
title: Samouczek`:`korzystania z tożsamości zarządzanej maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do grafu usługi Azure AD
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do interfejsu API funkcji Azure AD Graph.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43ef467adb8970d410404c151d0028ee4cda92b9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183018"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows w celu uzyskiwania dostępu do interfejsu API funkcji Azure AD Graph

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

W tym samouczku pokazano, jak za pomocą zarządzanej przez system tożsamości maszyny wirtualnej systemu Windows (VM) uzyskać dostęp do interfejs API programu Graph usługi Azure AD w celu pobrania ich członkostw w grupach. Tożsamości zarządzane dla zasobów platformy Azure są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu.  W tym samouczku wykonasz zapytanie dotyczące członkostwa tożsamości maszyny wirtualnej w grupach usługi Azure AD. Informacje o grupie są często używane na przykład podczas podejmowania decyzji o autoryzacji. Tożsamość zarządzana maszyny wirtualnej jest niejawnie reprezentowana przez **jednostkę usługi** w usłudze Azure AD. Przed wykonaniem zapytania dotyczącego grupy dodaj jednostkę usługi reprezentującą tożsamość maszyny wirtualnej do grupy w usłudze Azure AD. Możesz to zrobić przy użyciu programu Azure PowerShell lub programu PowerShell usługi Azure AD albo interfejsu wiersza polecenia platformy Azure.

> [!div class="checklist"]
> * Łączenie z usługą Azure AD
> * Dodawanie tożsamości maszyny wirtualnej do grupy w usłudze Azure AD 
> * Udzielanie tożsamości maszyny wirtualnej dostępu do funkcji Azure AD Graph 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania funkcji Azure AD Graph

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Aby można było udzielić tożsamości maszyny wirtualnej dostępu do funkcji Azure AD Graph, do konta musi być przypisana rola **Administrator globalny** w usłudze Azure AD.
- Zainstaluj najnowszą wersję modułu [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), jeśli jeszcze nie zostało to zrobione. 

## <a name="connect-to-azure-ad"></a>Łączenie z usługą Azure AD

Nawiązanie połączenia z usługą Azure AD jest wymagane do przypisania maszyny wirtualnej do grupy oraz udzielenia maszynie wirtualnej uprawnień do pobrania członkostw w grupach. Możesz użyć polecenia cmdlet Connect-AzureAD bezpośrednio lub w połączeniu z parametrem TenantId, jeśli masz wiele dzierżaw.

```powershell
Connect-AzureAD
```
LUB
```powershell
Connect-AzureAD -TenantId "Object Id of the tenant"
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Dodawanie tożsamości maszyny wirtualnej do grupy w usłudze Azure AD

Włączenie przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Windows spowodowało utworzenie jednostki usługi w usłudze Azure AD.  Teraz musisz dodać maszynę wirtualną do grupy.  Poniższy przykład przedstawia tworzenie nowej grupy w usłudze Azure AD i dodawanie jednostki usługi maszyny wirtualnej do tej grupy:

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Udzielanie maszynie wirtualnej dostępu do interfejsu API funkcji Azure AD Graph

Używając tożsamości zarządzanych dla zasobów platformy Azure, kod może uzyskiwać tokeny dostępu, aby uwierzytelniać się w zasobach obsługujących uwierzytelnianie usługi Azure AD. Interfejs API funkcji Microsoft Azure AD Graph obsługuje uwierzytelnianie usługi Azure AD. W tym kroku udzielisz jednostce usługi tożsamości maszyny wirtualnej dostępu do funkcji Azure AD Graph, aby umożliwić wykonanie zapytania dotyczącego członkostwa w grupach. Aby udzielić jednostce usługi dostępu do programu Microsoft Graph lub funkcji Azure AD Graph, użyj **uprawnień aplikacji**. Typ uprawnień aplikacji do udzielenia zależy od jednostki, do której chcesz uzyskać dostęp w programie MS Graph lub funkcji Azure AD Graph.

W tym samouczku umożliwisz tożsamości maszyny wirtualnej wykonanie zapytania dotyczącego członkostw w grupach przy użyciu uprawnień aplikacji ```Directory.Read.All```. Aby udzielić tych uprawnień, musisz mieć konto użytkownika, do którego przypisano rolę administratora globalnego w usłudze Azure AD. Uprawnień aplikacji zwykle udziela się, przechodząc do strony rejestracji aplikacji w witrynie Azure Portal i dodając uprawnienia. Jednak tożsamości zarządzane zasobów platformy Azure nie rejestrują obiektów aplikacji w usłudze Azure AD, tylko rejestrują jednostki usługi. Aby zarejestrować uprawnienia aplikacji, użyj narzędzia wiersza polecenia programu Azure AD PowerShell. 

Azure AD Graph:
- Identyfikator aplikacji jednostki usługi (używany podczas udzielania uprawnień aplikacji): 00000002-0000-0000-c000-000000000000
- Identyfikator zasobu (używany podczas żądania tokenu dostępu z tożsamości zarządzanych dla zasobów platformy Azure): https://graph.windows.net
- Dokumentacja zakresu uprawnień: [dokumentacja uprawnień funkcji Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Udzielanie uprawnień aplikacji przy użyciu programu PowerShell usługi Azure AD

Ta opcja wymaga użycia programu PowerShell usługi Azure AD. Jeśli nie jest on zainstalowany, [pobierz najnowszą wersję](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) przed kontynuowaniem.

1. Otwórz okno programu PowerShell i połącz się z usługą Azure AD:

   ```powershell
   Connect-AzureAD
   ```
   Aby połączyć się z określoną usługą Azure Active Directory, użyj parametru _TenantId_ w następujący sposób:

   ```powershell
   Connect-AzureAD -TenantId "Object Id of the tenant"
   ```

   
2. Uruchom następujące polecenia programu PowerShell, aby przypisać uprawnienie aplikacji ``Directory.Read.All`` jednostce usługi, która reprezentuje tożsamość maszyny wirtualnej.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   W danych wyjściowych końcowego polecenia, które wyglądają mniej więcej tak jak poniżej, jest zwracany identyfikator przypisania:
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   Jeśli wywołanie polecenia `New-AzureAdServiceAppRoleAssignment` spowoduje wygenerowanie komunikatu o błędzie `bad request, one or more properties are invalid`, może to oznaczać, że jednostka usługi tożsamości maszyny wirtualnej ma już przypisane uprawnienie aplikacji. Następujące polecenia programu PowerShell umożliwiają sprawdzenie, czy tożsamość maszyny wirtualnej ma już uprawnienie aplikacji do funkcji Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   Następujące polecenia programu PowerShell umożliwiają wyświetlenie wszystkich przyznanych uprawnień aplikacji do funkcji Azure AD Graph:

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   Następujące polecenia programu PowerShell umożliwiają usunięcie uprawnień aplikacji do funkcji Azure AD Graph, które zostały przyznane tożsamości maszyny wirtualnej:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej na potrzeby wywołania funkcji Azure AD Graph 

Aby użyć przypisanej przez system tożsamości zarządzanej maszyny wirtualnej na potrzeby uwierzytelniania w funkcji Azure AD Graph, musisz wysłać żądania z maszyny wirtualnej.

1. W portalu otwórz sekcję **Maszyny wirtualne** i przejdź do swojej maszyny wirtualnej z systemem Windows, a następnie w bloku **Przegląd** kliknij przycisk **Połącz**.  
2. Wprowadź **nazwę użytkownika** i **hasło** użyte podczas tworzenia maszyny wirtualnej z systemem Windows.
3. Po utworzeniu połączenia pulpitu zdalnego z maszyną wirtualną otwórz program PowerShell w sesji zdalnej.  
4. Używając polecenia Invoke-WebRequest programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanych zasobów platformy Azure, aby uzyskać token dostępu na potrzeby komunikacji z funkcją Azure AD Graph.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   Skonwertuj odpowiedź z obiektu JSON do obiektu PowerShell.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Wyodrębnij token dostępu z odpowiedzi.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. Identyfikator obiektu jednostki usługi tożsamości maszyny wirtualnej (możesz pobrać tę wartość za pomocą zmiennej zadeklarowanej we wcześniejszych krokach: ``$ManagedIdentitiesServicePrincipal.ObjectId``) pozwala wykonywać zapytania w interfejsie API funkcji Azure AD Graph umożliwiające pobieranie członkostw w grupach. Zastąp `<OBJECT ID>` IDENTYFIKATORem obiektu z poprzedniego kroku, a <`ACCESS-TOKEN>` z tokenem dostępu uzyskanym wcześniej:

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   Odpowiedź zawiera element `Object ID` grupy, do której dodano jednostkę usługi maszyny wirtualnej we wcześniejszych krokach.

   Odpowiedź:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób używania przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do funkcji Azure AD Graph.  Aby dowiedzieć się więcej o funkcji Azure AD Graph, zobacz:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
