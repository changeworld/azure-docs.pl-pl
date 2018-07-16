---
title: Umożliwia dostęp do magazynu Azure Windows Zarządzanej maszyny Wirtualnej
description: Samouczek, który przeprowadzi Cię przez proces przy użyciu Windows VM tożsamość usługi zarządzanej (MSI), dostęp do magazynu Azure.
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
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81b508661ac7195f690739fe7961691ddbedc9b0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049364"
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Umożliwia dostęp do usługi Azure Storage za pomocą klucza dostępu Windows VM tożsamości usługi zarządzanej

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ten samouczek pokazuje, jak włączyć tożsamości usługi zarządzanej (MSI) na maszynę wirtualną Windows, a następnie użyć tej tożsamości można pobrać kluczy dostępu do konta magazynu. Możesz użyć kluczy dostępu do magazynu w zwykły sposób podczas wykonywania operacji magazynu, na przykład w przypadku korzystania z zestawu SDK usługi Storage. W tym samouczku będziemy przekazywanie i pobieranie obiektów blob za pomocą usługi Azure Storage w programie PowerShell. Pokażemy ci, jak:


> [!div class="checklist"]
> * Włączanie tożsamości usługi Zarządzanej na maszynie wirtualnej Windows 
> * Udzielanie maszynie Wirtualnej dostęp do kluczy dostępu do konta magazynu w usłudze Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu Pobieranie kluczy dostępu do magazynu usługi Resource Manager 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

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

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włączanie tożsamości usługi Zarządzanej na maszynie Wirtualnej

Z tożsamości usługi Zarządzanej maszyny wirtualnej umożliwia uzyskiwanie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie tożsamości usługi Zarządzanej w sposób niewidoczny wykonuje dwie czynności: instaluje rozszerzenia tożsamości usługi Zarządzanej maszyny Wirtualnej na maszynie Wirtualnej oraz umożliwia tożsamości usługi Zarządzanej dla maszyny wirtualnej.  

1. Przejdź do grupy zasobów z nową maszyną wirtualną, a następnie wybierz maszynę wirtualną, utworzonej w poprzednim kroku.
2. W ramach maszyny Wirtualnej "Ustawienia" po lewej stronie kliknij pozycję **konfiguracji**.
3. Aby zarejestrować i włączyć plik MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz pozycję nie.
4. Upewnij się, możesz kliknąć pozycję **Zapisz** Aby zapisać konfigurację.

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Jeśli chcesz sprawdzić, jakie rozszerzenia na maszynie Wirtualnej, kliknij przycisk **rozszerzenia**. Po włączeniu tożsamości usługi Zarządzanej **ManagedIdentityExtensionforWindows** pojawia się na liście.

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli nie masz jeszcze jeden, teraz utworzysz konto magazynu. Można również pominąć ten krok i przyznać tożsamości usługi Zarządzanej maszyny Wirtualnej dostępu do kluczy istniejącego konta magazynu. 

1. Kliknij przycisk **+/ Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure portal.
2. Kliknij przycisk **magazynu**, następnie **konta magazynu**, i wyświetla nowy panel "Tworzenie konta magazynu".
3. Wprowadź nazwę konta magazynu, w którym będzie on potrzebny później.  
4. **Model wdrażania** i **rodzaj konta** powinna być równa "Resource manager" i "Zastosowania ogólne", odpowiednio. 
5. Upewnij się, **subskrypcji** i **grupy zasobów** są zgodne z typami, które zostały określone podczas tworzenia maszyny Wirtualnej w poprzednim kroku.
6. Kliknij przycisk **Utwórz**.

    ![Utwórz nowe konto magazynu](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Utwórz kontener obiektów blob na koncie magazynu

Firma Microsoft będzie później Przekaż i pobierze plik do nowego konta magazynu. Ponieważ pliki wymagają magazynu obiektów blob, należy utworzyć kontener obiektów blob, w której chcesz zapisać plik.

1. Przejdź z powrotem do Twojej nowo utworzone konto magazynu.
2. Kliknij przycisk **kontenery** link po lewej stronie w obszarze "Usługi obiektów Blob".
3. Kliknij przycisk **+ kontener** u góry strony i "nowy kontener" panelu wysuwa się.
4. Nadaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa będzie używana w dalszej części tego samouczka. 

    ![Tworzenie kontenera magazynu](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Udzielanie dostępu tożsamości usługi Zarządzanej maszyny Wirtualnej do używania kluczy dostępu do konta magazynu 

Usługa Azure Storage nie obsługuje natywnie uwierzytelniania usługi Azure AD.  Jednak można pobrać kluczy dostępu do konta magazynu z Menedżera zasobów przy użyciu pliku MSI. następnie użyć klucza dostępu do magazynu.  W tym kroku możesz udzielić Zarządzanej dostępu do kluczy konta magazynu.   

1. Przejdź z powrotem do Twojej nowo utworzone konto magazynu.  
2. Kliknij przycisk **kontrola dostępu (IAM)** łącze w panelu po lewej stronie.  
3. Kliknij przycisk **+ Dodaj** na górze strony Aby dodać nowe przypisanie roli maszyny wirtualnej
4. Ustaw **roli** na "Magazyn klucz Rola usługi Operator kont", w prawej części strony. 
5. Na następnej liście rozwijanej, ustaw **Przypisz dostęp do** zasobu "Maszyny wirtualnej".  
6. Następnie upewnij się, odpowiednie subskrypcji znajduje się w **subskrypcji** listy rozwijanej, a następnie ustaw **grupy zasobów** na "Wszystkie grupy zasobów".  
7. Na koniec w obszarze **wybierz** wybierz maszynę wirtualną Windows na liście rozwijanej, a następnie kliknij przycisk **Zapisz**. 

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i użyć go do wywołania usługi Azure Resource Manager 

W pozostałej części tego samouczka firma Microsoft będzie działać z maszyny Wirtualnej utworzony wcześniej. 

Należy użyć poleceń cmdlet programu Azure PowerShell Resource Manager w tej części.  Jeśli nie jest ona zainstalowana, [Pobierz najnowszą wersję](https://docs.microsoft.com/powershell/azure/overview) przed kontynuowaniem.

1. W witrynie Azure portal przejdź do **maszyn wirtualnych**, przejdź do Windows maszynę wirtualną, następnie z **Przegląd** kliknij **Connect** u góry. 
2. Wprowadź w swojej **Username** i **hasło** dla którego można dodać podczas tworzenia maszyny Wirtualnej Windows. 
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program PowerShell w sesji zdalnej.
4. Przy użyciu programu Powershell Invoke-WebRequest, wysłać żądanie do lokalnego punktu końcowego pliku MSI do uzyskania tokenu dostępu usługi Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
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
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Uzyskaj z usługi Azure Resource Manager do nawiązywania połączeń z magazynu kluczy dostępu do konta magazynu  

Teraz za pomocą programu PowerShell do wywoływania usługi Resource Manager przy użyciu tokenu dostępu, możemy pobrać w poprzedniej sekcji, aby pobrać klucz dostępu do magazynu. Gdy będziemy już mieć klucz dostępu do magazynu, możemy wywołać magazyn — liczba operacji pobierania/przekazywania.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> Adres URL jest uwzględniana wielkość liter, dlatego upewnij się, użyj tego samego dokładnej użyta wcześniej, podczas nosi nazwę grupy zasobów, w tym wielkie litery "G" w "resourceGroups." 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Następnie tworzymy plik o nazwie "jako". Następnie użyj klucza dostępu do magazynu, do uwierzytelniania za pomocą `New-AzureStorageContent` polecenia cmdlet, przekaż go do naszego kontenera obiektów blob, a następnie pobrać plik.

```bash
echo "This is a test text file." > test.txt
```

Pamiętaj zainstalować polecenia cmdlet usługi Azure Storage, za pomocą `Install-Module Azure.Storage`. Następnie przekaż obiekt blob został utworzony, za pomocą `Set-AzureStorageBlobContent` polecenia cmdlet programu PowerShell:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
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
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](msi-overview.md).
- Aby dowiedzieć się, jak skorzystać z tego samouczka ten sam przy użyciu magazynu poświadczeń sygnatury dostępu Współdzielonego, zobacz [Windows VM tożsamości usługi zarządzanej umożliwia dostęp do usługi Azure Storage za pomocą poświadczeń sygnatury dostępu Współdzielonego](msi-tutorial-windows-vm-access-storage-sas.md)
- Aby uzyskać więcej informacji na temat funkcji sygnatury dostępu Współdzielonego konta usługi Azure Storage zobacz:
  - [Używanie sygnatur dostępu współdzielonego (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Utworzenie sygnatury dostępu Współdzielonego usługi](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Użyj następujących sekcji komentarzy opinią i Pomóż nam analizy i połącz kształt naszej zawartości


