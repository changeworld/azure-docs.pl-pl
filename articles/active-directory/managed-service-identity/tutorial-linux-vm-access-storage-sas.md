---
title: Uzyskiwanie dostępu do usługi Azure Storage za pośrednictwem poświadczeń SAS przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Linux
description: Samouczek, który przedstawia sposób użycia tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux do uzyskania dostępu do usługi Azure Storage przy użyciu poświadczeń SAS zamiast klucza dostępu do konta magazynu.
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: a8eb733cf90d0160fe4b36cfb8c30df3ff19566e
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258504"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Samouczek: uzyskiwanie dostępu do usługi Azure Storage za pośrednictwem poświadczeń SAS przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Linux

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek przedstawia sposób włączania tożsamości usługi zarządzanej dla maszyny wirtualnej z systemem Linux, a następnie użycia tej tożsamości do uzyskania poświadczeń sygnatury dostępu współdzielonego (SAS). W szczególności [poświadczeń SAS usługi](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

Sygnatura dostępu współdzielonego usługi zapewnia możliwość udzielenia ograniczonego dostępu do obiektów na koncie magazynu przez ograniczony czas oraz dla konkretnej usługi (w naszym przypadku usługi obiektów blob) bez ujawniania klucza dostępu do konta. Możesz użyć poświadczeń SAS w zwykły sposób wykorzystywany podczas wykonywania operacji magazynu, np. podczas używania zestawu SDK usługi Storage. W tym samouczku zademonstrujemy przekazywanie i pobieranie obiektu blob przy użyciu interfejsu wiersza polecenia usługi Azure Storage. Poznasz następujące czynności:


> [!div class="checklist"]
> * Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux 
> * Udzielanie maszynie wirtualnej dostępu do sygnatury SAS konta magazynu w usłudze Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania sygnatury SAS z usługi Resource Manager 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Tworzenie maszyny wirtualnej z systemem Linux w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę wirtualną z systemem Linux. Możesz również włączyć tożsamość usługi zarządzanej na istniejącej maszynie wirtualnej.

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. W obszarze **Typ uwierzytelniania** wybierz pozycję **Klucz publiczny SSH** lub **Hasło**. Utworzone poświadczenia umożliwiają logowanie na maszynie wirtualnej.

    ![Alternatywny tekst obrazu](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Wybierz **subskrypcję** dla maszyny wirtualnej z listy rozwijanej.
5. Aby wybrać nową **grupę zasobów**, w której chcesz utworzyć maszynę wirtualną, wybierz pozycję **Utwórz nową**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycję **Wyświetl wszystkie** lub zmień filtr Obsługiwany typ dysku. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

## <a name="enable-managed-service-identity-on-your-vm"></a>Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej

Tożsamość usługi zarządzanej maszyny wirtualnej umożliwia uzyskanie tokenów dostępu z usługi Azure AD bez potrzeby umieszczania poświadczeń w kodzie. Włączenie tożsamości usługi zarządzanej na maszynie wirtualnej powoduje wykonanie dwóch czynności: zapewnia rejestrację maszyny wirtualnej w usłudze Azure Active Directory, aby utworzyć tożsamość zarządzaną, oraz konfiguruje tożsamość na maszynie wirtualnej. 

1. Przejdź do grupy zasobów nowej maszyny wirtualnej, a następnie wybierz maszynę wirtualną utworzoną w poprzednim kroku.
2. W sekcji „Ustawienia” maszyny wirtualnej po lewej stronie kliknij opcję **Konfiguracja**.
3. Aby zarejestrować i włączyć tożsamość usługi zarządzanej, wybierz pozycję **Tak**. Jeśli chcesz ją wyłączyć, wybierz pozycję Nie.
4. Pamiętaj, aby kliknąć przycisk **Zapisz** w celu zapisania konfiguracji.

    ![Alternatywny tekst obrazu](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli jeszcze nie masz konta magazynu, teraz je utworzysz.  Możesz też pominąć ten krok i przyznać tożsamości usługi zarządzanej maszyny wirtualnej dostęp do kluczy na istniejącym koncie magazynu. 

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij opcję **Magazyn**, a następnie **Konto magazynu**. Zostanie wyświetlony nowy panel „Utwórz konto magazynu”.
3. Wprowadź **nazwę** konta magazynu, której będziesz używać później.  
4. Opcje **Model wdrażania** i **Rodzaj konta** należy ustawić na „Resource Manager” i „Ogólnego przeznaczenia”. 
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.
6. Kliknij pozycję **Utwórz**.

    ![Tworzenie nowego konta magazynu](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Tworzenie kontenera obiektów blob na koncie magazynu

Później przekażemy i pobierzemy plik do nowego konta magazynu. Ponieważ pliki wymagają magazynu obiektów blob, musimy utworzyć kontener obiektów blob, w którym będziemy przechowywać plik.

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. Kliknij link **Kontenery** w panelu po lewej stronie, w sekcji „Usługa obiektów blob”.
3. Kliknij pozycję **+ Kontener** w górnej części strony, aby wysunąć panel „Nowy kontener”.
4. Podaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa będzie używana w dalszej części tego samouczka. 

    ![Tworzenie kontenera magazynu](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-managed-service-identity-access-to-use-a-storage-sas"></a>Udzielanie dostępu tożsamości usługi zarządzanej maszyny wirtualnej w celu korzystania z sygnatury dostępu współdzielonego magazynu 

Usługa Azure Storage nie zapewnia natywnej obsługi uwierzytelniania usługi Azure AD.  Możesz jednak użyć tożsamości usługi zarządzanej, aby pobrać sygnaturę SAS magazynu z usługi Resource Manager, a następnie użyć sygnatury SAS do uzyskania dostępu do magazynu.  W tym kroku udzielasz tożsamości usługi zarządzanej maszyny wirtualnej dostępu do sygnatury SAS konta magazynu.   

1. Przejdź z powrotem do nowo utworzonego konta magazynu.   
2. Kliknij link **Kontrola dostępu (IAM)** w panelu po lewej stronie.  
3. Kliknij przycisk **+ Dodaj** w górnej części strony, aby dodać nowe przypisanie roli dla maszyny wirtualnej.
4. Ustaw opcję **Rola** na „Współautor konta magazynu” w prawej części strony. 
5. Na kolejnej liście rozwijanej ustaw opcję **Przypisz dostęp do** na zasób „Maszyna wirtualna”.  
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej **Subskrypcja**, po czym ustaw opcję **Grupa zasobów** na wartość „Wszystkie grupy zasobów”.  
7. Na koniec w pozycji **Wybierz** użyj listy rozwijanej, aby wybrać maszynę wirtualną z systemem Linux, i kliknij przycisk **Zapisz**.  

    ![Alternatywny tekst obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania usługi Azure Resource Manager

W pozostałej części tego samouczka będziemy pracować z poziomu wcześniej utworzonej maszyny wirtualnej.

Aby wykonać te kroki, będziesz potrzebować klienta SSH. Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. W witrynie Azure Portal przejdź do pozycji **Maszyny wirtualne**, przejdź do maszyny wirtualnej z systemem Linux, a następnie na stronie **Przegląd** kliknij opcję **Połącz** u góry. Skopiuj ciąg, aby nawiązać połączenie z maszyną wirtualną. 
2. Połącz się z maszyną wirtualną przy użyciu klienta SSH.  
3. Zobaczysz monit o wprowadzenie **hasła** dodanego podczas tworzenia **maszyny wirtualnej z systemem Linux**. Logowanie powinno wtedy zostać pomyślnie wykonane.  
4. Użyj programu CURL, aby uzyskać token dostępu dla usługi Azure Resource Manager.  

    Żądanie programu CURL oraz odpowiedź dla tokenu dostępu znajduje się poniżej:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > W poprzednim żądaniu wartość parametru „resource” musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD. W przypadku użycia identyfikatora zasobu usługi Azure Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI.
    > W poniższej odpowiedzi element access_token został skrócony, aby zapewnić zwięzłość informacji.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Pobieranie poświadczeń SAS z usługi Azure Resource Manager w celu wykonywania wywołań do magazynu

Teraz użyj programu CURL, aby wywołać usługę Resource Manager przy użyciu tokenu dostępu pozyskanego w poprzedniej sekcji, aby utworzyć poświadczenia SAS magazynu. Po uzyskaniu sygnatury SAS magazynu możemy wywołać operacje przekazywania/pobierania magazynu.

W przypadku tego żądania użyjemy następujących parametrów żądania HTTP, aby utworzyć poświadczenia SAS:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Te parametry są uwzględnione w treści żądania POST dla poświadczeń SAS. Aby uzyskać więcej informacji o parametrach potrzebnych do tworzenia poświadczeń SAS, zobacz [Dokumentację interfejsu REST sygnatury dostępu współdzielonego usługi listy](/rest/api/storagerp/storageaccounts/listservicesas).

Użyj następującego żądania CURL, aby uzyskać poświadczenia SAS. Upewnij się, że parametry `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` i `<EXPIRATION TIME>` zostały zastąpione własnymi wartościami. Zastąp wartość `<ACCESS TOKEN>` tokenem dostępu pobranym wcześniej:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> W tekście w poprzednim adresie URL rozróżniana jest wielkość liter, więc upewnij się, że stosujesz odpowiednie małe i wielkie litery dla grup zasobów, aby odpowiednio je przedstawić. Ponadto należy pamiętać, że jest to żądanie POST, a nie żądanie GET.

Odpowiedź programu CURL zwróci poświadczenia SAS:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Utwórz przykładowy plik obiektu blob do przekazania do kontenera magazynu obiektów blob. Na maszynie wirtualnej z systemem Linux możesz to zrobić przy użyciu następującego polecenia. 

```bash
echo "This is a test file." > test.txt
```

Następnie przeprowadź uwierzytelnianie przy użyciu polecenia `az storage` interfejsu wiersza polecenia, korzystając z poświadczeń SAS, i przekaż plik do kontenera obiektów blob. W tym kroku musisz [zainstalować najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na maszynie wirtualnej, jeśli jeszcze tego nie zrobiono.

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Odpowiedź: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

Ponadto możesz pobrać plik przy użyciu interfejsu wiersza polecenia platformy Azure oraz przeprowadzić uwierzytelnianie przy użyciu poświadczeń SAS. 

Żądanie: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

Odpowiedź: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Storage przy użyciu poświadczeń SAS.  Aby dowiedzieć się więcej o sygnaturze dostępu współdzielonego usługi Azure Storage, zobacz:

> [!div class="nextstepaction"]
>[Używanie sygnatury dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
