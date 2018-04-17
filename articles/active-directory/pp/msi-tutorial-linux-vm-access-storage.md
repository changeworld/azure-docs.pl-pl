---
title: Umożliwia dostęp do usługi Azure Storage użytkownik, któremu przypisano MSI w maszynie Wirtualnej systemu Linux
description: Samouczek, który przeprowadzi Cię przez proces przy użyciu użytkownika przypisane zarządzane usługi tożsamości (MSI) na maszynie Wirtualnej systemu Linux dostęp do magazynu Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: dd82f1757d9c5a5fc8fb110cc36ec9f4bbd73e8a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Użyj przypisany użytkownik zarządzane usługi tożsamości (MSI) na maszynie Wirtualnej systemu Linux dostęp do magazynu Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

W tym samouczku przedstawiono sposób tworzenia i użyj przypisany użytkownik zarządzane usługi tożsamości (MSI) z maszyny wirtualnej systemu Linux, a następnie umożliwia dostęp do usługi Azure Storage. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz użytkownika przypisać tożsamość usługi zarządzanej (MSI)
> * Przypisz MSI użytkownik przypisany do maszyny wirtualnej systemu Linux
> * Udziel dostępu MSI do wystąpienia usługi Azure Storage
> * Uzyskaj token dostępu przy użyciu tożsamości MSI przypisany użytkownik i korzystać z niego uzyskać dostępu do magazynu Azure

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, w tym samouczku, masz dwie opcje:

- Użyj [powłoki chmury Azure](~/articles/cloud-shell/overview.md) w portalu Azure lub za pomocą przycisku "Spróbuj on" znajduje się w prawym górnym rogu każdej blok kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 lub nowsza), jeśli wolisz korzystać z konsoli lokalnej interfejsu wiersza polecenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Linux w nowej grupy zasobów

Najpierw należy utworzyć nową maszynę Wirtualną systemu Linux. Jeśli wolisz, można również włączyć MSI na istniejącej maszyny Wirtualnej.

1. Kliknij przycisk **+/ Utwórz nową usługę** znaleziono przycisku w lewym górnym rogu portalu Azure.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. Aby uzyskać **typ uwierzytelniania**, wybierz pozycję **klucz publiczny SSH** lub **hasło**. Utworzony poświadczenia umożliwiają Zaloguj się do maszyny Wirtualnej.

    ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
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

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli nie masz jeszcze jeden teraz utworzyć konta magazynu. Można również pominąć ten krok i użyj istniejącego konta magazynu, jeśli wolisz. 

1. Kliknij przycisk **+/ Utwórz nową usługę** znaleziono przycisku w lewym górnym rogu portalu Azure.
2. Kliknij przycisk **magazynu**, następnie **konta magazynu**, i wyświetla nowy panel "Tworzenie konta magazynu".
3. Wprowadź **nazwa** dla konta magazynu, którego można użyć później.  
4. **Model wdrażania** i **konta rodzaju** powinien być ustawiony odpowiednio na "Resource manager" i "Ogólnego przeznaczenia". 
5. Upewnij się, **subskrypcji** i **grupy zasobów** odpowiadały określony podczas tworzenia maszyny Wirtualnej w poprzednim kroku.
6. Kliknij przycisk **Utwórz**.

    ![Utwórz nowe konto magazynu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Tworzenie kontenera obiektów blob na koncie magazynu

Ponieważ pliki wymagają magazynu obiektów blob, należy utworzyć kontener obiektów blob do przechowywania pliku. Następnie przekaż i pobranie pliku do kontenera obiektów blob na koncie magazynu.

1. Przejdź z powrotem do użytkownika nowo utworzone konto magazynu.
2. Kliknij przycisk **kontenery** łącze w z lewej strony, w obszarze "Usługa Blob".
3. Kliknij przycisk **+ kontener** górnej części strony, a "nowy kontener" panelu slajdów wychodzących.
4. Nadaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa jest również używana później w samouczku. 

    ![Tworzenie kontenera magazynu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Przekazywanie pliku do nowo utworzonej kontenera, klikając nazwę kontenera, następnie **przekazać**, wybierz plik, a następnie kliknij przycisk **przekazać**.

    ![Przekaż plik tekstowy](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Przyznać uprawnienia użytkownika MSI przypisane do kontenera magazynu Azure

Za pomocą Instalatora MSI, kodu mogą uzyskiwać tokeny dostępu do uwierzytelniania do zasobów, które obsługują uwierzytelnianie usługi Azure AD. W tym samouczku jest używany Magazyn Azure.

Najpierw należy udzielić dostępu tożsamości MSI do kontenera magazynu Azure. W takim przypadku można użyć kontenera utworzony wcześniej. Zaktualizuj wartości dla `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, i `<CONTAINER NAME>` jako odpowiednie dla danego środowiska. Ponadto Zastąp `<MSI PRINCIPALID>` z `principalId` właściwości zwróconej przez `az identity create` w [utworzyć instalatora MSI przypisany użytkownik](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

Odpowiedź zawiera szczegóły dotyczące przypisania roli, utworzyć:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Uzyskaj token dostępu przy użyciu tożsamości MSI przypisane przez użytkownika i użyć go do wywołania usługi Azure Storage

W pozostałej części tego samouczka należy do pracy z maszyny Wirtualnej utworzone wcześniej.

Aby wykonać te kroki, należy klient SSH. Jeśli korzystasz z systemu Windows, możesz użyć klienta SSH w [podsystemu systemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeśli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [sposobu użycia SSH kluczy systemu Windows Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), lub [sposobu tworzenia i używania SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux na platformie Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu Azure, przejdź do **maszyn wirtualnych**, przejdź do maszyny wirtualnej systemu Linux, następnie z **omówienie** kliknij **Connect** u góry. Skopiuj ciąg, aby nawiązać połączenie z maszyną Wirtualną.
2. **Połącz** do maszyny Wirtualnej przy użyciu klienta SSH wybranych przez użytkownika. 
3. W oknie terminalu przy użyciu programu CURL, Utwórz żądanie lokalnego punktu końcowego MSI, aby uzyskać dostęp do tokenu usługi Azure Storage.

   W poniższym przykładzie przedstawiono żądania CURL można uzyskać tokenu dostępu. Pamiętaj zastąpić `<CLIENT ID>` z `clientId` właściwości zwróconej przez `az identity create` w [utworzyć instalatora MSI przypisany użytkownik](#create-a-user-assigned-msi):
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > W żądaniu poprzedniej wartości `resource` parametr musi być dokładnego dopasowania dla oczekiwano przez usługę Azure AD. Podczas korzystania z usługi Azure Storage identyfikator zasobu, należy dołączyć końcowy ukośnik w identyfikatorze URI.
   > W poniższych odpowiedzi elementu ' access_token ', jak została skrócona do skrócenia.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Teraz używać tokenu dostępu, dostęp do magazynu Azure, na przykład w celu odczytu zawartości przykładowy plik, który został wcześniej przekazany do kontenera. Zastąp wartości `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, i `<FILE NAME>` z wartościami określone wcześniej, i `<ACCESS TOKEN>` z tokenem zwracanej w poprzednim kroku.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Odpowiedź zawiera zawartość pliku:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Kolejne kroki

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](msi-overview.md).
- Aby dowiedzieć się, jak to zrobić to opisane w tym samouczku przy użyciu magazynu poświadczeń sygnatury dostępu Współdzielonego, zobacz [umożliwia dostęp do usługi Azure Storage za pomocą poświadczeń sygnatury dostępu Współdzielonego maszyny Wirtualnej systemu Linux zarządzanych tożsamości usługi](msi-tutorial-linux-vm-access-storage-sas.md)
- Aby uzyskać więcej informacji o funkcji sygnatury dostępu Współdzielonego konta usługi Azure Storage zobacz:
  - [Przy użyciu sygnatury dostępu współdzielonego (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Utworzenie sygnatury dostępu Współdzielonego usługi](/rest/api/storageservices/Constructing-a-Service-SAS.md)

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.





