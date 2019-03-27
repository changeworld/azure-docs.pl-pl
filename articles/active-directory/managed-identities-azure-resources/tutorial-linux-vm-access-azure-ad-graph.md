---
title: Używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do interfejsu API funkcji Azure AD Graph
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do interfejsu API funkcji Azure AD Graph.
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
ms.openlocfilehash: 481cb560daa26e59de2c78cc64bab9fb168eed58
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445398"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Samouczek: Używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do interfejsu API funkcji Azure AD Graph

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

W tym samouczku pokazano, jak uzyskać dostęp do interfejsu API funkcji Azure AD Graph przy użyciu tożsamości zarządzanej przypisanej przez system do maszyny wirtualnej z systemem Linux w celu pobrania członkostw w grupach. Tożsamości zarządzane dla zasobów platformy Azure są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu.  

W tym samouczku wykonasz zapytanie dotyczące członkostwa tożsamości maszyny wirtualnej w grupach usługi Azure AD. Informacje o grupie są często używane podczas podejmowania decyzji o autoryzacji. Tożsamość zarządzana maszyny wirtualnej jest niejawnie reprezentowana przez **jednostkę usługi** w usłudze Azure AD. 

> [!div class="checklist"]
> * Łączenie z usługą Azure AD
> * Dodawanie tożsamości maszyny wirtualnej do grupy w usłudze Azure AD 
> * Udzielanie tożsamości maszyny wirtualnej dostępu do funkcji Azure AD Graph 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania funkcji Azure AD Graph

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Zainstalowanie najnowszej wersji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Aby można było udzielić tożsamości maszyny wirtualnej dostępu do funkcji Azure AD Graph, do konta musi być przypisana rola **Administrator globalny** w usłudze Azure AD.

## <a name="connect-to-azure-ad"></a>Łączenie z usługą Azure AD

Nawiązanie połączenia z usługą Azure AD jest wymagane do przypisania maszyny wirtualnej do grupy oraz udzielenia maszynie wirtualnej uprawnień do pobrania członkostw w grupach.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Dodawanie tożsamości maszyny wirtualnej do grupy w usłudze Azure AD

Włączenie przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Linux spowodowało utworzenie jednostki usługi w usłudze Azure AD.  Musisz dodać maszynę wirtualną do grupy. Zapoznaj się z następującym artykułem, aby uzyskać instrukcje dotyczące dodawania maszyny wirtualnej do grupy w usłudze Azure AD:

- [Dodawanie członków grupy](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Udzielanie maszynie wirtualnej dostępu do interfejsu API funkcji Azure AD Graph

Używając tożsamości zarządzanych dla zasobów platformy Azure, kod może uzyskiwać tokeny dostępu, aby uwierzytelniać się w zasobach obsługujących uwierzytelnianie usługi Azure AD. Interfejs API funkcji Microsoft Azure AD Graph obsługuje uwierzytelnianie usługi Azure AD. W tym kroku udzielisz jednostce usługi tożsamości maszyny wirtualnej dostępu do funkcji Azure AD Graph, aby umożliwić wykonanie zapytania dotyczącego członkostwa w grupach. Aby udzielić jednostce usługi dostępu do programu Microsoft Graph lub funkcji Azure AD Graph, użyj **uprawnień aplikacji**. Typ uprawnień aplikacji do udzielenia zależy od jednostki, do której chcesz uzyskać dostęp w programie MS Graph lub funkcji Azure AD Graph.

W tym samouczku umożliwisz tożsamości maszyny wirtualnej wykonanie zapytania dotyczącego członkostw w grupach przy użyciu uprawnień aplikacji `Directory.Read.All`. Aby udzielić tych uprawnień, musisz mieć konto użytkownika, do którego przypisano rolę administratora globalnego w usłudze Azure AD. Uprawnień aplikacji zwykle udziela się, przechodząc do strony rejestracji aplikacji w witrynie Azure Portal i dodając uprawnienia. Jednak tożsamości zarządzane zasobów platformy Azure nie rejestrują obiektów aplikacji w usłudze Azure AD, tylko rejestrują jednostki usługi. Aby zarejestrować uprawnienia aplikacji, użyj narzędzia wiersza polecenia programu Azure AD PowerShell. 

Azure AD Graph:
- Identyfikator aplikacji jednostki usługi (używany podczas udzielania uprawnień aplikacji): 00000002-0000-0000-c000-000000000000
- Identyfikator zasobu (używany podczas żądania tokenu dostępu z tożsamości zarządzanych dla zasobów platformy Azure): https://graph.windows.net
- Dokumentacja dotycząca zakresu uprawnień: [Dokumentacja uprawnień programu Graph usługi Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>Udzielanie aplikacji uprawnień przy użyciu programu CURL

1. Pobierz token umożliwiający wykonywanie żądań programu CURL:

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. Musisz pobrać i zanotować element `objectId` maszyny wirtualnej. Zostanie on użyty w następnych krokach w celu udzielenia maszynie wirtualnej uprawnień do odczytu członkostwa w grupie. Zastąp wartość `<ACCESS TOKEN>` tokenem dostępu pobranym w poprzednim kroku.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. Za pomocą identyfikatora aplikacji funkcji Azure AD Graph (00000002-0000-0000-c000-000000000000) pobierz i zanotuj element `objectId` dla elementu `odata.type: Microsoft.DirectoryServices.ServicePrincipal` i element `id` dla uprawnień roli aplikacji `Directory.Read.All`.  Zastąp wartość `<ACCESS TOKEN>` tokenem dostępu pobranym wcześniej.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Odpowiedź:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. Teraz udziel jednostce usługi maszyny wirtualnej uprawnień odczytu do obiektów katalogu usługi Azure AD przy użyciu interfejsu API funkcji Azure AD Graph.  Wartość `id` jest wartością uprawnienia roli aplikacji `Directory.Read.All`, a wartość `resourceId` jest wartością `objectId` jednostki usługi `odata.type:Microsoft.DirectoryServices.ServicePrincipal` (wartości zanotowane w poprzednim kroku).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej na potrzeby wywołania funkcji Azure AD Graph 

Aby wykonać te kroki, będziesz potrzebować klienta SSH. Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu przejdź do maszyny wirtualnej z systemem Linux i w pozycji **Przegląd** kliknij opcję **Połącz**.  
2. **Połącz** się z maszyną wirtualną przy użyciu wybranego klienta SSH. 
3. W oknie terminalu, używając narzędzia CURL, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanych dla zasobów platformy Azure, aby uzyskać token dostępu do funkcji Azure AD Graph.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   Odpowiedź zawiera token dostępu wymagany do uzyskania dostępu do funkcji Azure AD Graph.

   Odpowiedź:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. Identyfikator obiektu jednostki usługi maszyny wirtualnej (wartość pobrana we wcześniejszych krokach) umożliwia wykonywanie zapytań w interfejsie API funkcji Azure AD Graph w celu pobierania członkostw w grupach. Zastąp wartość `<OBJECT-ID>` identyfikatorem obiektu jednostki usługi maszyny wirtualnej i zastąp wartość `<ACCESS-TOKEN>` uzyskanym wcześniej tokenem dostępu:

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   Odpowiedź:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób używania przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do funkcji Azure AD Graph.  Aby dowiedzieć się więcej o funkcji Azure AD Graph, zobacz:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
