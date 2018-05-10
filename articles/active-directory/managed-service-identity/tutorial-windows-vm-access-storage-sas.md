---
title: Umożliwia dostęp do usługi Azure Storage przy użyciu poświadczeń sygnatury dostępu Współdzielonego MSI maszyny Wirtualnej systemu Windows
description: Samouczek przedstawiający sposób użycia systemu Windows maszyny Wirtualnej zarządzane usługi tożsamości (MSI) dostęp do magazynu Azure, a klucz dostępu do konta magazynu przy użyciu poświadczeń sygnatury dostępu Współdzielonego.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 203e53cb21e1a2b4b0e31bcb5b8213d9e8cb01c7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Umożliwia dostęp do usługi Azure Storage za pomocą poświadczeń sygnatury dostępu Współdzielonego wirtualna zarządzane tożsamości usługi systemu Windows

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek pokazuje, jak włączyć zarządzane tożsamości usługi (MSI) dla maszyny wirtualnej systemu Windows, a następnie umożliwia uzyskanie poświadczeń dostępu sygnatury dostępu Współdzielonego magazynu MSI. W szczególności [poświadczeń sygnatury dostępu Współdzielonego usługi](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

Sygnatury dostępu Współdzielonego usługi pozwala, aby przyznać ograniczony dostęp do obiektów na koncie magazynu przez ograniczony czas i określonej usługi (w tym przypadku usługa blob), bez narażania klucz dostępu do konta. Można użyć w zwykły sposób podczas wykonywania operacji magazynu, na przykład w przypadku korzystania z zestawu SDK usługi Magazyn poświadczeń sygnatury dostępu Współdzielonego. W tym samouczku przedstawiony przekazywanie i pobieranie obiektu blob przy użyciu programu PowerShell magazynu Azure. Dowiesz się jak:


> [!div class="checklist"]
> * Włącz MSI w maszynie wirtualnej systemu Windows 
> * Przyznać uprawnienia do konta magazynu SAS w Menedżerze zasobów maszyny Wirtualnej 
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu pobrać sygnatury dostępu Współdzielonego z Menedżera zasobów 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Windows w nowej grupy zasobów

W tym samouczku utworzymy nową maszynę Wirtualną systemu Windows. Można również włączyć MSI na istniejącej maszyny Wirtualnej.

1.  Kliknij przycisk **+/ Utwórz nową usługę** znaleziono przycisku w lewym górnym rogu portalu Azure.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Username** i **hasło** utworzony, w tym miejscu jest poświadczeń umożliwia logowanie do maszyny wirtualnej.
4.  Wybierz odpowiednią **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5.  Aby wybrać nowy **grupy zasobów** chcesz maszynę wirtualną można utworzyć w, wybierz **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

    ![Tekst alternatywny obrazu](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włącz MSI na maszynie Wirtualnej

MSI maszyny wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. W obszarze obejmuje włączenie MSI wykonuje dwie czynności: rejestrów maszyny Wirtualnej z usługi Azure Active Directory do utworzenia zarządzanej tożsamości, a konfiguruje tożsamości na maszynie Wirtualnej.

1. Przejdź do grupy zasobów nowej maszyny wirtualnej, a następnie wybierz maszynę wirtualną, utworzony w poprzednim kroku.
2. W obszarze VM "Ustawienia" w lewym panelu kliknij **konfiguracji**.
3. Aby zarejestrować i włączyć MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz opcję nie.
4. Upewnij się, możesz kliknąć przycisk **zapisać** Aby zapisać konfigurację.

    ![Tekst alternatywny obrazu](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli nie masz jeszcze jeden, teraz utworzysz konto magazynu. Można również pominąć ten krok i przyznać uprawnienia MSI maszyny Wirtualnej do istniejącego konta magazynu poświadczeń sygnatury dostępu Współdzielonego. 

1. Kliknij przycisk **+/ Utwórz nową usługę** znaleziono przycisku w lewym górnym rogu portalu Azure.
2. Kliknij przycisk **magazynu**, następnie **konta magazynu**, i wyświetli nowy panel "Tworzenie konta magazynu".
3. Wprowadź nazwę konta magazynu, który będzie używany w później.  
4. **Model wdrażania** i **konta rodzaju** powinien być ustawiony odpowiednio na "Resource manager" i "Ogólnego przeznaczenia". 
5. Upewnij się, **subskrypcji** i **grupy zasobów** odpowiadały określony podczas tworzenia maszyny Wirtualnej w poprzednim kroku.
6. Kliknij przycisk **Utwórz**.

    ![Utwórz nowe konto magazynu](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Tworzenie kontenera obiektów blob na koncie magazynu

Firma Microsoft będzie później przekazywanie i pobranie pliku do nowego konta magazynu. Ponieważ pliki wymagają magazynu obiektów blob, należy utworzyć kontener obiektów blob do przechowywania pliku.

1. Przejdź z powrotem do użytkownika nowo utworzone konto magazynu.
2. Kliknij przycisk **kontenery** łącze w lewym panelu w obszarze "Usługa Blob".
3. Kliknij przycisk **+ kontener** górnej części strony, a "nowy kontener" panelu slajdów wychodzących.
4. Nadaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa będzie używana później w samouczku. 

    ![Tworzenie kontenera magazynu](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Przyznanie dostępu MSI z maszyną Wirtualną do używania magazynu SAS 

Magazyn Azure nie obsługuje natywnie uwierzytelniania usługi Azure AD.  Jednak można użyć Instalatora MSI można pobrać z magazynem SAS z Menedżera zasobów, a następnie użyj sygnatury dostępu Współdzielonego dostępu do magazynu.  W tym kroku należy przyznać uprawnienia MSI maszyny Wirtualnej na koncie magazynu SAS.   

1. Przejdź z powrotem do użytkownika nowo utworzone konto magazynu.   
2. Kliknij przycisk **(IAM) kontroli dostępu** łącze w lewym panelu.  
3. Kliknij przycisk **+ Dodaj** u góry strony, aby dodać nowe przypisanie roli dla maszyny Wirtualnej
4. Ustaw **roli** "Magazynu konta Współautor", w prawej części strony.  
5. Na liście rozwijanej dalej, ustaw **przypisany dostęp** zasobu "Maszyny wirtualnej".  
6. Następnie sprawdź odpowiednie subskrypcji znajduje się w **subskrypcji** listy rozwijanej, a następnie ustaw **grupy zasobów** do "Wszystkie grupy zasobów".  
7. Ponadto w obszarze **wybierz** wybierz maszynę wirtualną systemu Windows na liście rozwijanej, a następnie kliknij przycisk **zapisać**. 

    ![Tekst alternatywny obrazu](../media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i użyć go do wywołania usługi Azure Resource Manager 

W pozostałej części tego samouczka firma Microsoft będzie działać z maszyny Wirtualnej, utworzony wcześniej.

Należy użyć poleceń cmdlet programu PowerShell usługi Azure Resource Manager w tej części.  Jeśli nie jest zainstalowany, [Pobierz najnowszą wersję](https://docs.microsoft.com/powershell/azure/overview) przed kontynuowaniem.

1. W portalu Azure, przejdź do **maszyn wirtualnych**, przejdź do maszyny wirtualnej systemu Windows, następnie z **omówienie** kliknij **Connect** u góry.
2. Wprowadź w Twojej **Username** i **hasło** dla którego zostanie dodane po utworzeniu maszyny Wirtualnej systemu Windows. 
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program PowerShell w sesji zdalnej. 
4. Przy użyciu programu Powershell w Invoke-WebRequest, Wyślij żądanie do lokalnego punktu końcowego MSI do Uzyskaj token dostępu usługi Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Wartość parametru "zasobu" musi być dokładnego dopasowania dla oczekiwano przez usługę Azure AD. Podczas korzystania z usługi Azure Resource Manager identyfikator zasobu, należy dołączyć końcowy ukośnik w identyfikatorze URI.
    
    Następnie wyodrębnić elementu "Zawartość", który jest przechowywany jako ciąg w formacie JavaScript Object Notation (JSON) w obiekcie $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Następnie Wyodrębnij token dostępu z odpowiedzi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Uzyskiwanie poświadczeń sygnatury dostępu Współdzielonego z usługi Azure Resource Manager w celu wykonywania wywołań magazynu 

Do wywołania usługi Resource Manager przy użyciu tokenu dostępu, którego możemy pobrać w poprzedniej sekcji do tworzenia poświadczeń sygnatury dostępu Współdzielonego magazynu za pomocą programu PowerShell. Operacje magazynu mogą nazywa się po mamy poświadczeń sygnatury dostępu Współdzielonego.

Dla tego żądania użyjemy wykonaj parametry żądania HTTP do tworzenia poświadczeń sygnatury dostępu Współdzielonego:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Te parametry są zawarte w treści POST żądania dostępu do poświadczeń sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji na parametry w celu utworzenia poświadczeń sygnatury dostępu Współdzielonego, zobacz [REST sygnatury dostępu Współdzielonego usługi listy odwołania](/rest/api/storagerp/storageaccounts/listservicesas).

Najpierw należy przekonwertować na format JSON parametry, a następnie wywołaj magazynu `listServiceSas` punktu końcowego można utworzyć sygnatury dostępu Współdzielonego poświadczeń:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Adres URL jest rozróżniana wielkość liter, dlatego upewnij się, użyj tego samego dokładnej używane wcześniej, gdy nazwa grupy zasobów, w tym wielkie litery "G" w "resourceGroups." 

Teraz możemy Wyodrębnij poświadczeń sygnatury dostępu Współdzielonego z odpowiedzi:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Jeśli sprawdzenie faktury korygu SAS, zobaczysz ekran podobny do tego:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Następnie tworzymy plik o nazwie "test.txt". Następnie użyć do uwierzytelniania za pomocą poświadczeń sygnatury dostępu Współdzielonego `New-AzureStorageContent` polecenia cmdlet, przekazać go do naszego kontenera obiektów blob, a następnie Pobierz plik.

```bash
echo "This is a test text file." > test.txt
```

Upewnij się, najpierw zainstaluj polecenia cmdlet usługi Azure Storage za pomocą `Install-Module Azure.Storage`. Następnie przekazania obiektu blob nowo utworzone przy użyciu `Set-AzureStorageBlobContent` polecenia cmdlet programu PowerShell:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Odpowiedź:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Możesz również pobrać przekazanym właśnie, za pomocą obiektu blob `Get-AzureStorageBlobContent` polecenia cmdlet programu PowerShell:

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Odpowiedź:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](overview.md).
- Aby dowiedzieć się, jak to zrobić na tej samej instrukcji za pomocą klucza konta magazynu, zobacz [użyć maszyny Wirtualnej z systemem Windows zarządzanych tożsamości usługi dostępu do magazynu Azure](tutorial-windows-vm-access-storage.md)
- Aby uzyskać więcej informacji o funkcji sygnatury dostępu Współdzielonego konta usługi Azure Storage zobacz:
  - [Przy użyciu sygnatury dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Utworzenie sygnatury dostępu Współdzielonego usługi](/rest/api/storageservices/Constructing-a-Service-SAS.md)

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.


