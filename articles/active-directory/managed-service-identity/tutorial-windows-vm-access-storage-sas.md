---
title: Umożliwia dostęp do usługi Azure Storage przy użyciu poświadczeń sygnatury dostępu Współdzielonego Windows Zarządzanej maszyny Wirtualnej
description: Samouczek, w którym pokazano, jak używać Windows VM tożsamość usługi zarządzanej (MSI) do dostępu do usługi Azure Storage przy użyciu poświadczeń sygnatury dostępu Współdzielonego zamiast klucza dostępu do konta magazynu.
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
ms.openlocfilehash: 2648c2ea0e54a3713bb5d89bf5cf5fcd029a9f99
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006448"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Samouczek: Umożliwia dostęp do usługi Azure Storage za pomocą poświadczeń sygnatury dostępu Współdzielonego Windows VM tożsamości usługi zarządzanej

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek pokazuje, jak włączyć tożsamości usługi zarządzanej (MSI) na maszynę wirtualną Windows, a następnie Uzyskiwanie poświadczeń sygnatury dostępu współdzielonego (SAS) magazynu przy użyciu pliku MSI. W szczególności [poświadczeń sygnatury dostępu Współdzielonego usługi](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

Sygnatury dostępu Współdzielonego usługi umożliwia przyznawanie ograniczonego dostępu do obiektów na koncie magazynu przez ograniczony czas i określonej usługi (w naszym przypadku na usługę blob service), bez narażania klucz dostępu konta. Możesz użyć poświadczeń sygnatury dostępu Współdzielonego w zwykły sposób podczas wykonywania operacji magazynu, na przykład w przypadku korzystania z zestawu SDK usługi Storage. W tym samouczku pokażemy, przekazywanie i pobieranie obiektu blob przy użyciu programu PowerShell magazynu platformy Azure. Pokażemy ci, jak:


> [!div class="checklist"]
> * Włączanie tożsamości usługi Zarządzanej na maszynie wirtualnej Windows 
> * Udzielanie maszynie Wirtualnej dostęp do konta magazynu sygnatur dostępu Współdzielonego w usłudze Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu pobrania sygnatury dostępu Współdzielonego usługi Resource Manager 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną Windows w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę Wirtualną Windows. Można również włączyć tożsamości usługi Zarządzanej istniejącej maszyny wirtualnej.

1.  Kliknij przycisk **+/ Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure portal.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Username** i **hasło** utworzony, w tym miejscu jest poświadczenia, których używasz do logowania do maszyny wirtualnej.
4.  Wybierz odpowiednią **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5.  Aby wybrać nowy **grupy zasobów** chcesz maszyny wirtualnej pod kątem można tworzyć w **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

    ![Tekst ALT obrazu](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włączanie tożsamości usługi Zarządzanej na maszynie Wirtualnej

Z tożsamości usługi Zarządzanej maszyny wirtualnej umożliwia uzyskiwanie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie tożsamości usługi Zarządzanej w sposób niewidoczny wykonuje dwie czynności: rejestrów przy użyciu usługi Azure Active Directory, aby utworzyć jego tożsamość zarządzaną i konfiguruje tożsamości na maszynie Wirtualnej.

1. Przejdź do grupy zasobów z nową maszyną wirtualną, a następnie wybierz maszynę wirtualną, utworzonej w poprzednim kroku.
2. W obszarze maszyny Wirtualnej "Ustawienia" w lewym panelu kliknij **konfiguracji**.
3. Aby zarejestrować i włączyć plik MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz pozycję nie.
4. Upewnij się, możesz kliknąć pozycję **Zapisz** Aby zapisać konfigurację.

    ![Tekst ALT obrazu](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli nie masz jeszcze jeden, teraz utworzysz konto magazynu. Można również pominąć ten krok i Udziel dostępu tożsamości usługi Zarządzanej maszyny Wirtualnej do poświadczeń sygnatury dostępu Współdzielonego istniejącego konta magazynu. 

1. Kliknij przycisk **+/ Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure portal.
2. Kliknij przycisk **magazynu**, następnie **konta magazynu**, i wyświetla nowy panel "Tworzenie konta magazynu".
3. Wprowadź nazwę konta magazynu, w którym będzie on potrzebny później.  
4. **Model wdrażania** i **rodzaj konta** powinna być równa "Resource manager" i "Zastosowania ogólne", odpowiednio. 
5. Upewnij się, **subskrypcji** i **grupy zasobów** są zgodne z typami, które zostały określone podczas tworzenia maszyny Wirtualnej w poprzednim kroku.
6. Kliknij przycisk **Utwórz**.

    ![Utwórz nowe konto magazynu](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Utwórz kontener obiektów blob na koncie magazynu

Firma Microsoft będzie później Przekaż i pobierze plik do nowego konta magazynu. Ponieważ pliki wymagają magazynu obiektów blob, należy utworzyć kontener obiektów blob, w której chcesz zapisać plik.

1. Przejdź z powrotem do Twojej nowo utworzone konto magazynu.
2. Kliknij przycisk **kontenery** łącze w panelu po lewej stronie w obszarze "Usługi obiektów Blob".
3. Kliknij przycisk **+ kontener** u góry strony i "nowy kontener" panelu wysuwa się.
4. Nadaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa będzie używana w dalszej części tego samouczka. 

    ![Tworzenie kontenera magazynu](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Udzielanie dostępu tożsamości usługi Zarządzanej maszyny Wirtualnej do używania magazynu SAS 

Usługa Azure Storage nie obsługuje natywnie uwierzytelniania usługi Azure AD.  Jednak można pobrać sygnatury dostępu Współdzielonego magazynu usługi Resource Manager przy użyciu pliku MSI. następnie dostępu do magazynu przy użyciu sygnatury dostępu Współdzielonego.  W tym kroku udzielasz dostępu do usługi Zarządzanej maszyny Wirtualnej do swojego konta magazynu, sygnatury dostępu Współdzielonego.   

1. Przejdź z powrotem do Twojej nowo utworzone konto magazynu.   
2. Kliknij przycisk **kontrola dostępu (IAM)** łącze w panelu po lewej stronie.  
3. Kliknij przycisk **+ Dodaj** na górze strony Aby dodać nowe przypisanie roli maszyny wirtualnej
4. Ustaw **roli** do "Współautor konta magazynu", w prawej części strony.  
5. Na następnej liście rozwijanej, ustaw **Przypisz dostęp do** zasobu "Maszyny wirtualnej".  
6. Następnie upewnij się, odpowiednie subskrypcji znajduje się w **subskrypcji** listy rozwijanej, a następnie ustaw **grupy zasobów** na "Wszystkie grupy zasobów".  
7. Na koniec w obszarze **wybierz** wybierz maszynę wirtualną Windows na liście rozwijanej, a następnie kliknij przycisk **Zapisz**. 

    ![Tekst ALT obrazu](../media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i użyć go do wywołania usługi Azure Resource Manager 

W pozostałej części tego samouczka firma Microsoft będzie działać z maszyny Wirtualnej utworzony wcześniej.

Należy użyć poleceń cmdlet programu Azure PowerShell Resource Manager w tej części.  Jeśli nie jest ona zainstalowana, [Pobierz najnowszą wersję](https://docs.microsoft.com/powershell/azure/overview) przed kontynuowaniem.

1. W witrynie Azure portal przejdź do **maszyn wirtualnych**, przejdź do Windows maszynę wirtualną, następnie z **Przegląd** kliknij **Connect** u góry.
2. Wprowadź w swojej **Username** i **hasło** dla którego można dodać podczas tworzenia maszyny Wirtualnej Windows. 
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program PowerShell w sesji zdalnej. 
4. Przy użyciu programu Powershell Invoke-WebRequest, wysłać żądanie do lokalnego punktu końcowego pliku MSI do uzyskania tokenu dostępu usługi Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Wartość parametru "resource" musi być dokładnie dopasowany do oczekiwań przez usługę Azure AD. Korzystając z Identyfikatora zasobu usługi Azure Resource Manager, należy dołączyć końcowy ukośnik w identyfikatorze URI.
    
    Następnie można wyodrębnić elementu "Zawartość", który jest przechowywany jako ciąg w formacie JavaScript Object Notation (JSON) w obiekcie $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Następnie Wyodrębnij token dostępu z odpowiedzi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Pobierz poświadczenia sygnatury dostępu Współdzielonego z usługi Azure Resource Manager do nawiązywania połączeń z magazynu 

Teraz można użyć programu PowerShell do wywołania usługi Resource Manager przy użyciu tokenu dostępu, możemy pobrać w poprzedniej sekcji, aby utworzyć poświadczenia sygnatury dostępu Współdzielonego magazynu. Gdy będziemy już mieć poświadczenia sygnatury dostępu Współdzielonego, firma Microsoft może wywoływać operacje magazynu.

Dla tego żądania użyjemy postępuj zgodnie z parametrów żądania HTTP do tworzenia poświadczeń sygnatury dostępu Współdzielonego:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Te parametry są objęte treść wpisu wniosku o poświadczenia sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji na temat parametrów do tworzenia poświadczeń sygnatury dostępu Współdzielonego, zobacz [dokumentacja interfejsu REST sygnatury dostępu Współdzielonego usługi listy](/rest/api/storagerp/storageaccounts/listservicesas).

Najpierw należy przekonwertować parametrów do formatu JSON, a następnie wywołaj magazynu `listServiceSas` poświadczeń punktu końcowego, aby utworzyć sygnaturę dostępu Współdzielonego:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Adres URL jest uwzględniana wielkość liter, dlatego upewnij się, użyj tego samego dokładnej użyta wcześniej, podczas nosi nazwę grupy zasobów, w tym wielkie litery "G" w "resourceGroups." 

Teraz możemy wyodrębnić poświadczenia sygnatury dostępu Współdzielonego z odpowiedzi:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Zbadaj poświadczenie sygnatury dostępu Współdzielonego zostanie wyświetlony podobny do poniższego:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Następnie tworzymy plik o nazwie "jako". Następnie użyj poświadczeń sygnatury dostępu Współdzielonego do uwierzytelniania za pomocą `New-AzureStorageContent` polecenia cmdlet, przekaż go do naszego kontenera obiektów blob, a następnie pobrać plik.

```bash
echo "This is a test text file." > test.txt
```

Pamiętaj zainstalować polecenia cmdlet usługi Azure Storage, za pomocą `Install-Module Azure.Storage`. Następnie przekaż obiekt blob został utworzony, za pomocą `Set-AzureStorageBlobContent` polecenia cmdlet programu PowerShell:

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

Możesz również pobrać obiekt blob, właśnie został przekazany, za pomocą `Get-AzureStorageBlobContent` polecenia cmdlet programu PowerShell:

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

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób tworzenia tożsamości usługi zarządzanej dostępu do magazynu platformy Azure przy użyciu poświadczeń sygnatury dostępu Współdzielonego.  Aby dowiedzieć się więcej na temat sygnatury dostępu Współdzielonego Azure Storage, zobacz:

> [!div class="nextstepaction"]
>[Używanie sygnatur dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)


