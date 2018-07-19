---
title: Używanie przypisanej przez użytkownika tożsamości usługi zarządzanej maszyny wirtualnej do uzyskiwania dostępu do usługi Azure Resource Manager
description: Samouczek przedstawiający proces użycia przypisanej przez użytkownika tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 06abd7867a99c20597ed17faf6fa61b91f70baaa
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007710"
---
# <a name="tutorial-use-a-user-assigned-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Samouczek: używanie tożsamości przypisanej przez użytkownika na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

W tym samouczku przedstawiono sposób tworzenia tożsamości przypisanej przez użytkownika, przypisywania jej do maszyny wirtualnej z systemem Linux, a następnie używania tej tożsamości w celu uzyskania dostępu do interfejsu API usługi Azure Resource Manager. Tożsamości usługi zarządzanej są zarządzane automatycznie przez platformę Azure. Umożliwiają one uwierzytelnianie do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności osadzania poświadczeń w kodzie. 

Tożsamości usługi zarządzanej są zarządzane automatycznie przez platformę Azure. Umożliwiają one uwierzytelnianie do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności osadzania poświadczeń w kodzie.

Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie tożsamości przypisanej przez użytkownika
> * Przypisywanie tożsamości przypisanej przez użytkownika do maszyny wirtualnej z systemem Linux 
> * Udzielanie tożsamości przypisanej przez użytkownika dostępu do grupy zasobów w usłudze Azure Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości przypisanej przez użytkownika oraz używanie go do wywołania usługi Azure Resource Manager 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości usługi zarządzanej, zapoznaj się z sekcją [omówienia](overview.md). **Pamiętaj o zapoznaniu się z [różnicami między tożsamościami przypisanymi przez system i przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby przeprowadzić tworzenie wymaganych zasobów i wykonać kroki zarządzania rolami opisane w tym samouczku, Twoje konto musi mieć uprawnienia „Właściciel” w odpowiednim zakresie (subskrypcji lub grupy zasobów). Jeśli potrzebujesz pomocy dotyczącej przypisania roli, zobacz [Korzystanie z kontroli dostępu opartej na rolach do zarządzania dostępem do zasobów subskrypcji platformy Azure](/azure/role-based-access-control/role-assignments-portal).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Tworzenie maszyny wirtualnej z systemem Linux w nowej grupie zasobów

W tym samouczku najpierw utworzysz nową maszynę wirtualną z systemem Linux. Możesz także użyć istniejącej maszyny wirtualnej.

1. W lewym górnym rogu witryny Azure Portal kliknij pozycję **Utwórz zasób**.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. W obszarze **Typ uwierzytelniania** wybierz pozycję **Klucz publiczny SSH** lub **Hasło**. Utworzone poświadczenia umożliwiają logowanie na maszynie wirtualnej.

    ![Tworzenie maszyny wirtualnej z systemem Linux](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Wybierz **subskrypcję** dla maszyny wirtualnej z listy rozwijanej.
5. Aby wybrać nową **grupę zasobów**, w której chcesz utworzyć maszynę wirtualną, wybierz pozycję **Utwórz nową**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycję **Wyświetl wszystkie** lub zmień filtr Obsługiwany typ dysku. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

## <a name="create-a-user-assigned-identity"></a>Tworzenie tożsamości przypisanej przez użytkownika

1. Jeśli jest używana konsola interfejsu wiersza polecenia (a nie sesja usługi Azure Cloud Shell), rozpocznij od zalogowania do platformy Azure. Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz utworzyć nową tożsamość przypisaną przez użytkownika:

    ```azurecli
    az login
    ```

2. Utwórz tożsamość przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az_identity_create). Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość usługi zarządzanej, a parametr `-n` określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<MSI NAME>` zostały zastąpione własnymi wartościami:
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <MSI NAME>
```

Odpowiedź zawiera szczegóły utworzonej tożsamości przypisanej przez użytkownika, podobne do poniższego przykładu. Zanotuj wartość `id` dla tożsamości przypisanej przez użytkownika, ponieważ będzie używana w następnym kroku:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
"location": "westcentralus",
"name": "<MSI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-a-user-assigned-identity-to-your-linux-vm"></a>Przypisywanie tożsamości przypisanej przez użytkownika do maszyny wirtualnej z systemem Linux

Tożsamość przypisana przez użytkownika może być używana przez klientów w obrębie wielu zasobów platformy Azure. Użyj poniższych poleceń, aby przypisać tożsamość przypisaną przez użytkownika do pojedynczej maszyny wirtualnej. Użyj właściwości `Id` zwróconej w poprzednim kroku dla parametru `-IdentityID`.

Przypisz przypisaną przez użytkownika tożsamość usługi zarządzanej do maszyny wirtualnej z systemem Linux przy użyciu polecenia [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. Użyj właściwości `id` zwróconej w poprzednim kroku dla wartości parametru `--identities`.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Udzielanie tożsamości przypisanej przez użytkownika dostępu do grupy zasobów w usłudze Azure Resource Manager 

Tożsamość usługi zarządzanej udostępnia tożsamości, których kod może używać do żądania tokenów dostępu w celu uwierzytelniania do interfejsów API zasobów obsługujących uwierzytelnianie usługi Azure AD. W tym samouczku kod będzie uzyskiwać dostęp do interfejsu API usługi Azure Resource Manager.  

Aby kod mógł uzyskać dostęp do interfejsu API, musisz udzielić dostępu tożsamości do zasobu w usłudze Azure Resource Manager. W tym przypadku jest to grupa zasobów, w której znajduje się maszyna wirtualna. Zaktualizuj wartość `<SUBSCRIPTION ID>` i `<RESOURCE GROUP>` zgodnie z potrzebami środowiska. Dodatkowo zastąp element `<MSI PRINCIPALID>` właściwością `principalId` zwróconą przez polecenie `az identity create` w części [Tworzenie przypisanej przez użytkownika tożsamości usługi zarządzanej](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Odpowiedź zawiera szczegóły utworzonego przypisania roli, podobne do poniższego przykładu:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania usługi Resource Manager 

W pozostałej części tego samouczka będziemy pracować z poziomu wcześniej utworzonej maszyny wirtualnej.

Aby wykonać te kroki, potrzebujesz klienta SSH. Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W portalu przejdź do pozycji **Maszyny wirtualne**, a następnie przejdź do swojej maszyny wirtualnej z systemem Linux i w obszarze **Omówienie** kliknij pozycję **Połącz**. Skopiuj ciąg, aby nawiązać połączenie z maszyną wirtualną.
3. Połącz się z maszyną wirtualną przy użyciu wybranego klienta SSH. Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. W oknie terminalu, używając narzędzia CURL, wyślij żądanie do punktu końcowego usługi Azure Instance Metadata Service (IMDS), aby uzyskać token dostępu do usługi Azure Resource Manager.  

   Żądanie programu CURL służące do uzyskiwania tokenu dostępu zostało przedstawione w poniższym przykładzie. Pamiętaj o zastąpieniu elementu `<CLIENT ID>` właściwością `clientId` zwróconą przez polecenie `az identity create` w części [Tworzenie przypisanej przez użytkownika tożsamości usługi zarządzanej](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Wartość parametru `resource` musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD. W przypadku użycia identyfikatora zasobu usługi Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI. 
    
    Odpowiedź zawiera token dostępu wymagany do uzyskania dostępu do usługi Azure Resource Manager. 
    
    Przykładowa odpowiedź:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Użyj tokenu dostępu w celu uzyskania dostępu do usługi Azure Resource Manager i odczytaj właściwości grupy zasobów, której wcześniej udzielono dostępu tożsamości przypisanej przez użytkownika. Pamiętaj o zastąpieniu elementów `<SUBSCRIPTION ID>` i `<RESOURCE GROUP>` wartościami określonymi wcześniej oraz elementu `<ACCESS TOKEN>` tokenem zwróconym w poprzednim kroku.

    > [!NOTE]
    > W adresie URL jest uwzględniana wielkość liter, więc podczas nazywania grupy zasobów upewnij się, że użyto takich samych wielkości liter jak wcześniej — z wielką literą „G” w nazwie `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Odpowiedź zawiera informacje o określonej grupie zasobów, podobne do poniższego przykładu: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia tożsamości przypisanej przez użytkownika i dołączania jej do maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do interfejsu API usługi Azure Resource Manager.  Aby dowiedzieć się więcej o usłudze Azure Resource Manager, zobacz:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

