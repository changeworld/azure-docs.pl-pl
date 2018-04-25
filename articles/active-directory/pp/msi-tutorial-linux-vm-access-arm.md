---
title: "Umożliwia dostęp do usługi Azure Resource Manager MSI przypisany użytkownik maszyny Wirtualnej systemu Linux"
description: "Samouczek, który przeprowadzi Cię przez proces przy użyciu User-Assigned zarządzane usługi tożsamości (MSI) na maszynie Wirtualnej systemu Linux, można uzyskać dostępu do usługi Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c2b6d70e441dc3d300f49adff1c02d7cc65788d2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Użyj przypisany użytkownik zarządzane usługi tożsamości (MSI) na maszynie Wirtualnej systemu Linux, można uzyskać dostępu do usługi Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

W tym samouczku wyjaśniono, jak utworzyć przypisany użytkownik zarządzane usługi tożsamości (MSI), przypisz je do maszyny wirtualnej systemu Linux (VM), a następnie użyj tej tożsamości można uzyskać dostępu do interfejsu API Azure Resource Manager. 

Azure automatycznie zarządza zarządzanych tożsamości usługi. Umożliwiają one uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności osadzania poświadczeń w kodzie.

Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie pliku MSI przypisany użytkownik
> * Przypisz MSI do maszyny Wirtualnej systemu Linux 
> * Udziel dostępu do pliku MSI do grupy zasobów w usłudze Azure Resource Manager 
> * Uzyskaj token dostępu przy użyciu MSI i użyć go do wywołania usługi Azure Resource Manager 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, w tym samouczku, masz dwie opcje:

- Użyj [powłoki chmury Azure](~/articles/cloud-shell/overview.md) w portalu Azure lub za pomocą przycisku "Spróbuj on" znajduje się w prawym górnym rogu każdej blok kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 lub nowsza), jeśli wolisz korzystać z konsoli lokalnej interfejsu wiersza polecenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do portalu Azure pod adresem [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Linux w nowej grupy zasobów

W tym samouczku należy najpierw utworzyć nową maszynę Wirtualną systemu Linux. Można również zdecydować się na użycie istniejącej maszyny Wirtualnej.

1. Kliknij przycisk **Utwórz zasób** w lewym górnym rogu portalu Azure.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. Aby uzyskać **typ uwierzytelniania**, wybierz pozycję **klucz publiczny SSH** lub **hasło**. Utworzony poświadczenia umożliwiają Zaloguj się do maszyny Wirtualnej.

    ![Tworzenie maszyny Wirtualnej systemu Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Wybierz **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5. Aby wybrać nowy **grupy zasobów** chcesz maszyny wirtualnej utworzone w, wybierz **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiary, wybierz **Wyświetl wszystkie** lub zmień filtr typu dysku obsługiwane. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

## <a name="create-a-user-assigned-msi"></a>Tworzenie pliku MSI przypisany użytkownik

1. Jeśli używana jest konsola interfejsu wiersza polecenia (zamiast sesję powłoki chmury Azure), Rozpocznij od logowanie do platformy Azure. Użyj konta, które jest skojarzone z subskrypcją platformy Azure, w którym chcesz utworzyć nowy plik MSI:

    ```azurecli
    az login
    ```

2. Tworzenie przypisany użytkownik MSI przy użyciu [utworzenia tożsamości az](/cli/azure/identity#az_identity_create). `-g` Parametr określa grupę zasobów, w której jest tworzony plik MSI, a `-n` parametr określa jego nazwę. Pamiętaj zastąpić `<RESOURCE GROUP>` i `<MSI NAME>` wartości parametrów z własne wartości:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Odpowiedź zawiera szczegóły msi przypisany użytkownik utworzone, podobnie do poniższego przykładu. Uwaga `id` wartość Twojej msi, jak będzie on używany w następnym kroku:

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Przypisz MSI użytkownika przypisane przez użytkownika do maszyny Wirtualnej systemu Linux

W odróżnieniu od przypisane systemu pliku MSI Instalatora MSI przypisany użytkownik może służyć przez klientów na wielu zasobów platformy Azure. W tym samouczku można przypisać do jednej maszyny Wirtualnej. Można również przypisać do więcej niż jednej maszyny Wirtualnej.

Przypisz MSI użytkownik przypisany do maszyny Wirtualnej systemu Linux przy użyciu [tożsamość Przypisz az maszyny wirtualnej](/cli/azure/vm#az_vm_assign_identity). Pamiętaj zastąpić `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów z własne wartości. Użyj `id` właściwości zwracane w poprzednim kroku dla `--identities` wartość parametru:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Przyznać uprawnienia MSI przypisane przez użytkownika do grupy zasobów w usłudze Azure Resource Manager 

MSI zapewnia kodu przy użyciu tokenu dostępu do uwierzytelniania zasobu interfejsów API, które obsługują uwierzytelniania usługi Azure AD. W tym samouczku kod uzyskuje dostęp do interfejsu API Azure Resource Manager. 

Przed kodu dostępu do interfejsu API jednak, należy udzielić MSI tożsamości dostępu do zasobów w usłudze Azure Resource Manager. W takim przypadku grupę zasobów, w którym znajduje się maszyna wirtualna. Zaktualizuj wartości dla `<SUBSCRIPTION ID>` i `<RESOURCE GROUP>` jako odpowiednie dla danego środowiska. Ponadto Zastąp `<MSI PRINCIPALID>` z `principalId` właściwości zwróconej przez `az identity create` w [utworzyć instalatora MSI przypisany użytkownik](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Odpowiedź zawiera szczegóły dotyczące przypisania roli utworzone, podobnie do poniższego przykładu:

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

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i użyć go do wywołania usługi Resource Manager 

W pozostałej części tego samouczka firma Microsoft będzie działać z maszyny Wirtualnej, utworzony wcześniej.

Aby wykonać te kroki, należy klient SSH. Jeśli korzystasz z systemu Windows, możesz użyć klienta SSH w [podsystemu systemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeśli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [sposobu użycia SSH kluczy systemu Windows Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), lub [sposobu tworzenia i używania SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux na platformie Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu Azure, przejdź do **maszyn wirtualnych**, przejdź do maszyny wirtualnej systemu Linux, następnie z **omówienie** kliknij **Connect** u góry. Skopiuj ciąg, aby nawiązać połączenie z maszyną Wirtualną.
2. **Połącz** do maszyny Wirtualnej przy użyciu klienta SSH wybranych przez użytkownika.  
3. W oknie terminalu przy użyciu programu CURL, Wyślij żądanie do lokalnego punktu końcowego MSI do Uzyskaj token dostępu usługi Azure Resource Manager.  

   W poniższym przykładzie przedstawiono żądania CURL można uzyskać tokenu dostępu. Pamiętaj zastąpić `<CLIENT ID>` z `clientId` właściwości zwróconej przez `az identity create` w [utworzyć instalatora MSI przypisany użytkownik](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Wartość `resource` parametr musi być dokładnego dopasowania dla oczekiwano przez usługę Azure AD. Korzystając z Menedżera zasobów identyfikator zasobu, musi zawierać ukośnika w identyfikatorze URI. 
    
    Odpowiedź zawiera token dostępu, musisz mieć dostęp do usługi Azure Resource Manager. 
    
    Przykład odpowiedzi:  

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

4. Teraz Użyj tokenu dostępu, aby uzyskać dostępu do usługi Azure Resource Manager i odczytywania właściwości grupy zasobów, do której wcześniej przyznano dostęp MSI przypisane przez użytkownika. Pamiętaj zastąpić `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` z wartościami określone wcześniej, i `<ACCESS TOKEN>` z tokenem zwracanej w poprzednim kroku.

    > [!NOTE]
    > Adres URL jest rozróżniana wielkość liter, dlatego należy dokładnie wielkością liter wcześniej używane podczas nosi nazwę grupy zasobów i wielkie litery "G" w `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Odpowiedź zawiera określone informacje grupy zasobów, podobnie do poniższego przykładu: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Kolejne kroki

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](msi-overview.md).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
