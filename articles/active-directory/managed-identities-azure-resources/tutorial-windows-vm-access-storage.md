---
title: Używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure Storage
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Storage.
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
ms.date: 01/24/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 147ee2450a6a67f8ca02149105533401d038a53a
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191085"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Samouczek: uzyskiwanie dostępu do usługi Azure Storage za pośrednictwem klucza dostępu przy użyciu przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


> [!IMPORTANT] 
> Usługa Azure Storage obsługuje teraz uwierzytelnianie usługi Azure AD. Najlepszym rozwiązaniem jest użycie [uwierzytelniania usługi Azure AD](tutorial-vm-windows-access-storage.md) zamiast kluczy dostępu. 


Ten samouczek przedstawia sposób używania tożsamości zarządzanej przypisanej przez system do maszyny wirtualnej z systemem Windows do pobrania kluczy dostępu do konta magazynu. Możesz użyć kluczy dostępu do magazynu w zwykły sposób wykorzystywany podczas wykonywania operacji magazynu, np. podczas używania zestawu SDK usługi Storage. W tym samouczku przekażemy i pobierzemy obiekty blob przy użyciu programu PowerShell i usługi Azure Storage. Poznasz następujące czynności:


> [!div class="checklist"]
> * Tworzenie konta magazynu
> * Udzielanie maszynie wirtualnej dostępu do kluczy dostępu konta magazynu w usłudze Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania kluczy dostępu magazynu z usługi Resource Manager 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli jeszcze nie masz konta magazynu, teraz je utworzysz. Możesz też pominąć ten krok i przyznać przypisanej przez system tożsamości zarządzanej maszyny wirtualnej dostęp do kluczy na istniejącym koncie magazynu. 

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij opcję **Magazyn**, a następnie **Konto magazynu**. Zostanie wyświetlony nowy panel „Utwórz konto magazynu”.
3. Wprowadź nazwę konta magazynu, której będziesz używać później.  
4. Opcje **Model wdrażania** i **Rodzaj konta** należy ustawić na „Resource Manager” i „Ogólnego przeznaczenia”. 
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.
6. Kliknij pozycję **Utwórz**.

    ![Tworzenie nowego konta magazynu](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Tworzenie kontenera obiektów blob na koncie magazynu

Później przekażemy i pobierzemy plik do nowego konta magazynu. Ponieważ pliki wymagają magazynu obiektów blob, musimy utworzyć kontener obiektów blob, w którym będziemy przechowywać plik.

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. Kliknij link **Kontenery** po lewej stronie, w sekcji „Usługa obiektów blob”.
3. Kliknij pozycję **+ Kontener** w górnej części strony, aby wysunąć panel „Nowy kontener”.
4. Podaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa będzie używana w dalszej części tego samouczka. 

    ![Tworzenie kontenera magazynu](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Udzielanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej dostępu do używania kluczy dostępu konta magazynu 

Usługa Azure Storage nie zapewnia natywnej obsługi uwierzytelniania usługi Azure AD.  Możesz jednak użyć przypisanej przez system tożsamości zarządzanej maszyny wirtualnej, aby pobrać klucze dostępu konta magazynu z usługi Resource Manager, a następnie użyć klucza do uzyskania dostępu do magazynu.  W tym kroku udzielasz przypisanej przez system tożsamości zarządzanej maszyny wirtualnej dostępu do kluczy do konta magazynu.   

1. Przejdź z powrotem do nowo utworzonego konta magazynu.  
2. Kliknij link **Kontrola dostępu (IAM)** w panelu po lewej stronie.  
3. Kliknij przycisk **+ Dodaj przypisanie roli** w górnej części strony, aby dodać nowe przypisanie roli dla maszyny wirtualnej
4. Ustaw opcję **Rola** na „Rola usługi operatora kluczy konta magazynu” w prawej części strony. 
5. Na kolejnej liście rozwijanej ustaw opcję **Przypisz dostęp do** na zasób „Maszyna wirtualna”.  
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej **Subskrypcja**, po czym ustaw opcję **Grupa zasobów** na wartość „Wszystkie grupy zasobów”.  
7. Na koniec w pozycji **Wybierz** użyj listy rozwijanej, aby wybrać maszynę wirtualną z systemem Windows, i kliknij przycisk **Zapisz**. 

    ![Alternatywny tekst obrazu](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-to-call-azure-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu przypisanej przez system tożsamości zarządzanej maszyny wirtualnej do wywołania usługi Azure Resource Manager 

W pozostałej części tego samouczka będziemy pracować z poziomu wcześniej utworzonej maszyny wirtualnej. 

W tej części będzie wymagane użycie poleceń cmdlet programu PowerShell w usłudze Azure Resource Manager.  Jeżeli nie jest on zainstalowany, [pobierz najnowszą wersję](https://docs.microsoft.com/powershell/azure/overview) przed kontynuowaniem.

1. W witrynie Azure Portal przejdź do pozycji **Maszyny wirtualne**, przejdź do maszyny wirtualnej z systemem Windows, a następnie na stronie **Przegląd** kliknij opcję **Połącz** u góry. 
2. Wprowadź **nazwę użytkownika** i **hasło** dodane podczas tworzenia maszyny wirtualnej z systemem Windows. 
3. Teraz, po utworzeniu **połączenia pulpitu zdalnego** z maszyną wirtualną, otwórz program PowerShell w sesji zdalnej.
4. Używając polecenia Invoke-WebRequest programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanej dla zasobów platformy Azure, aby uzyskać token dostępu na potrzeby usługi Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Wartość parametru „resource” musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD. W przypadku użycia identyfikatora zasobu usługi Azure Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI.
    
    Następnie wyodrębnij element„Content”, który jest przechowywany w ciągu w formacie JavaScript Object Notation (JSON) w obiekcie $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Następnie wyodrębnij token dostępu z odpowiedzi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Uzyskiwanie kluczy dostępu konta magazynu z usługi Azure Resource Manager w celu wykonywania wywołań magazynu  

Teraz użyj programu PowerShell, aby wywołać usługę Resource Manager przy użyciu tokenu dostępu pozyskanego w poprzedniej sekcji, aby pobrać klucz dostępu do magazynu. Po uzyskaniu klucza dostępu do magazynu możemy wywołać operacje przekazywania/pobierania magazynu.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> W adresie URL rozróżniana jest wielkość liter, więc upewnij się, że użyto takich samych wartości jak wcześniej, podczas nazywania grupy zasobów — z wielką literą „G” w nazwie „resourceGroups”. 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Następnie utworzymy plik o nazwie „test.txt”. Później użyj klucza dostępu do magazynu, aby dokonać uwierzytelniania przy użyciu polecenia cmdlet `New-AzStorageContent`, przekazać plik do kontenera obiektów blob, a następnie pobrać plik.

```bash
echo "This is a test text file." > test.txt
```

Pamiętaj, aby najpierw zainstalować polecenia cmdlet usługi Azure Storage przy użyciu polecenia `Install-Module Az.Storage`. Następnie przekaż właśnie utworzony obiekt blob przy użyciu polecenia cmdlet `Set-AzStorageBlobContent` programu PowerShell:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Odpowiedź:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Możesz też pobrać właśnie przekazany obiekt blob przy użyciu polecenia cmdlet `Get-AzStorageBlobContent` programu PowerShell:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Odpowiedź:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia przypisanej przez system tożsamości zarządzanej w celu uzyskania dostępu do usługi Azure Storage przy użyciu klucza dostępu.  Aby dowiedzieć się więcej o kluczach dostępu usługi Azure Storage, zobacz:

> [!div class="nextstepaction"]
>[Zarządzanie kluczami dostępu do magazynu](/azure/storage/common/storage-create-storage-account)

