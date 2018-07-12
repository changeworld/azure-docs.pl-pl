---
title: Uzyskiwanie dostępu do usługi Azure Storage przy użyciu poświadczeń użytkownika przypisane MSI na maszynie Wirtualnej systemu Linux
description: Samouczek, który przeprowadzi Cię przez proces dostęp do usługi Azure Storage, za pomocą użytkownika przypisać tożsamość usługi zarządzanej (MSI) na maszynie Wirtualnej systemu Linux.
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
ms.openlocfilehash: 4a1a2d0c40012649f6cd89193fd3f704f325e38a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611048"
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Użyj użytkownik przypisany tożsamość usługi zarządzanej (MSI) na maszynie Wirtualnej systemu Linux dostępu do magazynu Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ten samouczek pokazuje, jak utworzyć i używać użytkownik przypisany tożsamość usługi zarządzanej (MSI) z maszyny wirtualnej systemu Linux, a następnie umożliwia dostęp do usługi Azure Storage. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz użytkownika przypisane do tożsamości usługi zarządzanej (MSI)
> * Przypisz MSI użytkownik przypisany do maszyny wirtualnej systemu Linux
> * Udziel dostępu tożsamości usługi Zarządzanej do wystąpienia usługi Azure Storage
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości przypisanych przez użytkownika tożsamości usługi Zarządzanej i przy jego użyciu dostęp do usługi Azure Storage

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia w tym samouczku, masz dwie opcje:

- Użyj [usługi Azure Cloud Shell](~/articles/cloud-shell/overview.md) w witrynie Azure portal lub za pomocą przycisku "Try It", znajduje się w prawym górnym rogu każdego bloku kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Linux w nowej grupie zasobów

Najpierw należy utworzyć nową maszynę Wirtualną systemu Linux. Można również włączyć tożsamości usługi Zarządzanej istniejącej maszyny wirtualnej, jeśli użytkownik sobie tego życzy.

1. Kliknij przycisk **+/ Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. Aby uzyskać **typ uwierzytelniania**, wybierz opcję **klucz publiczny SSH** lub **hasło**. Utworzono poświadczenia umożliwiają logowanie do maszyny Wirtualnej.

    ![Tekst ALT obrazu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Wybierz **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5. Aby wybrać nowy **grupy zasobów** chcesz maszyny wirtualnej pod kątem można tworzyć w **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycję **Wyświetl wszystkie** lub zmień filtr obsługiwany typ dysku. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

## <a name="create-a-user-assigned-msi"></a>Tworzenie pliku MSI przypisanych przez użytkownika

1. Jeśli używana jest konsola interfejsu wiersza polecenia (a nie w ramach sesji usługi Azure Cloud Shell), należy uruchomić, logując się do platformy Azure. Użyj konta które jest skojarzone z subskrypcją platformy Azure w ramach której chcesz utworzyć nowy plik MSI:

    ```azurecli
    az login
    ```

2. Utwórz przypisanych przez użytkownika tożsamości usługi Zarządzanej przy użyciu [Utwórz tożsamość az](/cli/azure/identity#az_identity_create). `-g` Parametr określa grupę zasobów, w której zostanie utworzony plik MSI, a `-n` parametr określa jej nazwę. Koniecznie Zastąp `<RESOURCE GROUP>` i `<MSI NAME>` wartości parametrów własnymi wartościami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Odpowiedź zawiera szczegóły msi przypisanych przez użytkownika, utworzono, podobnie do poniższego przykładu. Uwaga `id` wartości dla swojej tożsamości usługi Zarządzanej, ponieważ zostaną użyte w następnym kroku:

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Przypisz plik MSI z użytkownik przypisany do maszyny Wirtualnej systemu Linux

W odróżnieniu od przypisany systemowo pliku MSI Instalatora MSI przypisanych przez użytkownika może służyć przez klientów w wielu zasobów platformy Azure. W tym samouczku można przypisać do jednej maszyny Wirtualnej. Można również przypisać do więcej niż jednej maszyny Wirtualnej.

Przypisz MSI użytkownik przypisany do maszyny Wirtualnej systemu Linux przy użyciu [az vm Przypisz identity](/cli/azure/vm#az-vm-identity-assign). Koniecznie Zastąp `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów własnymi wartościami. Użyj `id` zwracana przez właściwość w poprzednim kroku dla `--identities` wartość parametru:

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli nie masz jeszcze jeden, Utwórz teraz konto magazynu. Można również pominąć ten krok i użyć istniejącego konta magazynu, jeśli użytkownik sobie tego życzy. 

1. Kliknij przycisk **+/ Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure portal.
2. Kliknij przycisk **magazynu**, następnie **konta magazynu**, i wyświetla nowy panel "Tworzenie konta magazynu".
3. Wprowadź **nazwa** dla konta magazynu, którego można użyć później.  
4. **Model wdrażania** i **rodzaj konta** powinna być równa "Resource manager" i "Zastosowania ogólne", odpowiednio. 
5. Upewnij się, **subskrypcji** i **grupy zasobów** są zgodne z typami, które zostały określone podczas tworzenia maszyny Wirtualnej w poprzednim kroku.
6. Kliknij przycisk **Utwórz**.

    ![Utwórz nowe konto magazynu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Utwórz kontener obiektów blob na koncie magazynu

Ponieważ pliki wymagają magazynu obiektów blob, należy utworzyć kontener obiektów blob, w której chcesz zapisać plik. Następnie przekazywania i pobierania pliku do kontenera obiektów blob, w ramach nowego konta magazynu.

1. Przejdź z powrotem do Twojej nowo utworzone konto magazynu.
2. Kliknij przycisk **kontenery** link po lewej stronie w obszarze "Usługi obiektów Blob".
3. Kliknij przycisk **+ kontener** u góry strony i "nowy kontener" panelu wysuwa się.
4. Nadaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa jest również używany w dalszej części tego samouczka. 

    ![Tworzenie kontenera magazynu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Przekazywanie pliku do nowo utworzony kontener, klikając nazwę kontenera, a następnie **przekazywanie**, wybierz plik, a następnie kliknij przycisk **przekazywanie**.

    ![Przekaż plik tekstowy](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Udziel dostępu MSI przypisać użytkownika do kontenera usługi Azure Storage

Za pomocą Instalatora MSI, kod można uzyskać tokenów dostępu w celu uwierzytelniania w zasobach, które obsługują uwierzytelnianie usługi Azure AD. W tym samouczku użyjesz usługi Azure Storage.

Najpierw udzielisz jej dostępu tożsamości MSI na kontenerze usługi Azure Storage. W tym przypadku używasz kontenerów utworzone wcześniej. Zaktualizuj wartości dla `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, i `<CONTAINER NAME>` odpowiednie dla danego środowiska. Ponadto Zastąp `<MSI PRINCIPALID>` z `principalId` właściwości zwróconej przez `az identity create` polecenia w pliku [Tworzenie pliku MSI użytkownik przypisany](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

Odpowiedź zawiera szczegółowe informacje dotyczące przydział roli został utworzony:

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

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości MSI przypisanych przez użytkownika i użyć go do wywołania usługi Azure Storage

W pozostałej części tego samouczka potrzebne do pracy z maszyny Wirtualnej została utworzona wcześniej.

Aby wykonać te kroki, należy klienta SSH. Jeśli używasz Windows, możesz użyć klienta SSH w [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeśli potrzebujesz pomocy w konfigurowaniu klucze Twój klient SSH, zobacz [jak klucze używanie protokołu SSH z Windows Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), lub [sposobu tworzenia i używania publicznych i prywatnych pary kluczy SSH dla maszyn wirtualnych systemu Linux na platformie Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. W witrynie Azure portal przejdź do **maszyn wirtualnych**, przejdź do maszyny wirtualnej systemu Linux, następnie z **Przegląd** kliknij **Connect** u góry. Skopiuj parametry, które umożliwiają połączenie z maszyną wirtualną.
2. **Połącz** do maszyny Wirtualnej przy użyciu klienta SSH wybranych przez użytkownika. 
3. W oknie terminalu przy użyciu programu CURL, wysłać żądanie do lokalnego punktu końcowego pliku MSI do uzyskania tokenu dostępu dla usługi Azure Storage.

   Żądanie programu CURL i uzyskiwanie tokenu dostępu jest wyświetlany w poniższym przykładzie. Koniecznie Zastąp `<CLIENT ID>` z `clientId` właściwości zwróconej przez `az identity create` polecenia w pliku [Tworzenie pliku MSI użytkownik przypisany](#create-a-user-assigned-msi):
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > W żądaniu poprzedniej wartości `resource` parametr musi być dokładnie dopasowany do oczekiwań przez usługę Azure AD. Korzystając z Identyfikatora zasobu usługi Azure Storage, należy dołączyć końcowy ukośnik w identyfikatorze URI.
   > W poniższym odpowiedzi elementu access_token, ponieważ została skrócona w celu skrócenia programu.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Teraz można użyć tokenu dostępu, aby dostęp do usługi Azure Storage, na przykład odczytać zawartość przykładowego pliku, który został wcześniej przekazany do kontenera. Zastąp wartości `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, i `<FILE NAME>` przy użyciu wartości określonej wcześniej i `<ACCESS TOKEN>` przy użyciu tokenu zwróconym w poprzednim kroku.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Odpowiedź zawiera zawartość pliku:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](msi-overview.md).
- Aby dowiedzieć się, jak skorzystać z tego samouczka ten sam przy użyciu magazynu poświadczeń sygnatury dostępu Współdzielonego, zobacz [Linux VM tożsamości usługi zarządzanej umożliwia dostęp do usługi Azure Storage za pomocą poświadczeń sygnatury dostępu Współdzielonego](msi-tutorial-linux-vm-access-storage-sas.md)
- Aby uzyskać więcej informacji na temat funkcji sygnatury dostępu Współdzielonego konta usługi Azure Storage zobacz:
  - [Używanie sygnatur dostępu współdzielonego (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Utworzenie sygnatury dostępu Współdzielonego usługi](/rest/api/storageservices/Constructing-a-Service-SAS.md)

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.





