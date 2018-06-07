---
title: Umożliwia dostęp do usługi Azure Storage przy użyciu poświadczeń sygnatury dostępu Współdzielonego MSI maszyny Wirtualnej systemu Linux
description: Samouczek przedstawiający sposób użycia Linux VM zarządzane usługi tożsamości (MSI), dostęp do magazynu Azure, a klucz dostępu do konta magazynu przy użyciu poświadczeń sygnatury dostępu Współdzielonego.
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
ms.openlocfilehash: e5e08985f88c7cf3018ecadce2cabac743e4bd37
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594856"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Samouczek: Umożliwia dostęp do usługi Azure Storage za pomocą poświadczeń sygnatury dostępu Współdzielonego maszyny Wirtualnej systemu Linux zarządzanych tożsamości usługi

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek pokazuje, jak włączyć zarządzane tożsamości usługi (MSI) dla maszyny wirtualnej systemu Linux, a następnie umożliwia uzyskanie poświadczeń dostępu sygnatury dostępu Współdzielonego magazynu MSI. W szczególności [poświadczeń sygnatury dostępu Współdzielonego usługi](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

Sygnatury dostępu Współdzielonego usługi pozwala, aby przyznać ograniczony dostęp do obiektów na koncie magazynu przez ograniczony czas i określonej usługi (w tym przypadku usługa blob), bez narażania klucz dostępu do konta. Można użyć w zwykły sposób podczas wykonywania operacji magazynu, na przykład w przypadku korzystania z zestawu SDK usługi Magazyn poświadczeń sygnatury dostępu Współdzielonego. W tym samouczku przedstawiony przekazywanie i pobieranie obiektu blob przy użyciu interfejsu wiersza polecenia Azure magazynu. Dowiesz się jak:


> [!div class="checklist"]
> * Włącz MSI w maszynie wirtualnej systemu Linux 
> * Przyznać uprawnienia do konta magazynu SAS w Menedżerze zasobów maszyny Wirtualnej 
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu pobrać sygnatury dostępu Współdzielonego z Menedżera zasobów 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Linux w nowej grupy zasobów

W tym samouczku utworzymy nową maszynę Wirtualną systemu Linux. Można również włączyć MSI na istniejącej maszyny Wirtualnej.

1. Kliknij przycisk **+/ Utwórz nową usługę** znaleziono przycisku w lewym górnym rogu portalu Azure.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. Aby uzyskać **typ uwierzytelniania**, wybierz pozycję **klucz publiczny SSH** lub **hasło**. Utworzony poświadczenia umożliwiają Zaloguj się do maszyny Wirtualnej.

    ![Tekst alternatywny obrazu](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Wybierz **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5. Aby wybrać nowy **grupy zasobów** chcesz maszyny wirtualnej utworzone w, wybierz **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiary, wybierz **Wyświetl wszystkie** lub zmień filtr typu dysku obsługiwane. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

## <a name="enable-msi-on-your-vm"></a>Włącz MSI na maszynie Wirtualnej

MSI maszyny wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie zarządzania tożsamości usługi na maszynie Wirtualnej, wykonuje dwie czynności: rejestrów maszyny Wirtualnej z usługi Azure Active Directory do utworzenia zarządzanej tożsamości, a konfiguruje tożsamości na maszynie Wirtualnej. 

1. Przejdź do grupy zasobów nowej maszyny wirtualnej, a następnie wybierz maszynę wirtualną, utworzony w poprzednim kroku.
2. W obszarze VM "Ustawienia", po lewej stronie kliknij **konfiguracji**.
3. Aby zarejestrować i włączyć MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz opcję nie.
4. Upewnij się, możesz kliknąć przycisk **zapisać** Aby zapisać konfigurację.

    ![Tekst alternatywny obrazu](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli nie masz jeszcze jeden, teraz utworzysz konto magazynu.  Można również pominąć ten krok i przyznać uprawnienia MSI maszyny Wirtualnej do istniejącego konta magazynu kluczy. 

1. Kliknij przycisk **+/ Utwórz nową usługę** znaleziono przycisku w lewym górnym rogu portalu Azure.
2. Kliknij przycisk **magazynu**, następnie **konta magazynu**, i wyświetli nowy panel "Tworzenie konta magazynu".
3. Wprowadź **nazwa** dla konta magazynu, który będzie używany w później.  
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
7. Ponadto w obszarze **wybierz** wybierz maszynę wirtualną systemu Linux na liście rozwijanej, a następnie kliknij przycisk **zapisać**.  

    ![Tekst alternatywny obrazu](../media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i użyć go do wywołania usługi Azure Resource Manager

W pozostałej części tego samouczka firma Microsoft będzie działać z maszyny Wirtualnej, utworzony wcześniej.

Aby wykonać te kroki, konieczne będzie klienta SSH. Jeśli korzystasz z systemu Windows, możesz użyć klienta SSH w [podsystemu systemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Jeśli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [sposobu użycia SSH kluczy systemu Windows Azure](../../virtual-machines/linux/ssh-from-windows.md), lub [sposobu tworzenia i używania SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu Azure, przejdź do **maszyn wirtualnych**, przejdź do maszyny wirtualnej systemu Linux, następnie z **omówienie** kliknij **Connect** u góry. Skopiuj ciąg, aby nawiązać połączenie z maszyną Wirtualną. 
2. Nawiązać połączenie z maszyną Wirtualną przy użyciu klienta SSH.  
3. Następnie pojawi się monit o wprowadzenie w Twojej **hasło** dodane podczas tworzenia **maszyny Wirtualnej systemu Linux**. Użytkownik powinien następnie można pomyślnie zarejestrowany.  
4. Umożliwia ZWINIĘCIE Uzyskaj token dostępu usługi Azure Resource Manager.  

    ZWINIĘCIE żądania i odpowiedzi tokenu dostępu znajduje się poniżej:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > W żądaniu poprzedniej wartości parametru "zasobu" musi być dokładnego dopasowania dla oczekiwano przez usługę Azure AD. Podczas korzystania z usługi Azure Resource Manager identyfikator zasobu, należy dołączyć końcowy ukośnik w identyfikatorze URI.
    > W poniższych odpowiedzi elementu ' access_token ', jak została skrócona do skrócenia.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Uzyskiwanie poświadczeń sygnatury dostępu Współdzielonego z usługi Azure Resource Manager w celu wykonywania wywołań magazynu

Teraz można użyć CURL do wywołania usługi Resource Manager przy użyciu tokenu dostępu, którego możemy pobrać w poprzedniej sekcji do tworzenia poświadczeń sygnatury dostępu Współdzielonego magazynu. Operacje wysyłania/pobierania magazynu można nazywa się po mamy poświadczeń sygnatury dostępu Współdzielonego.

Dla tego żądania użyjemy wykonaj parametry żądania HTTP do tworzenia poświadczeń sygnatury dostępu Współdzielonego:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Te parametry są zawarte w treści POST żądania dostępu do poświadczeń sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji na parametry w celu utworzenia poświadczeń sygnatury dostępu Współdzielonego, zobacz [REST sygnatury dostępu Współdzielonego usługi listy odwołania](/rest/api/storagerp/storageaccounts/listservicesas).

Użyj następującego żądania CURL, aby uzyskać poświadczenia SAS. Pamiętaj zastąpić `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>`, i `<EXPIRATION TIME>` wartości parametrów z własne wartości. Zastąp `<ACCESS TOKEN>` wartość przy użyciu tokenu dostępu został wcześniej pobrany:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Tekst w adresie URL wcześniejsze jest uwzględniana wielkość liter, dlatego upewnij się, jeśli używasz górna małe dla grupy zasobów, uwzględnienie w związku z tym. Ponadto jest ważne dowiedzieć się, że jest to żądanie POST nie żądanie GET.

Odpowiedź CURL zwraca poświadczenia SAS:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Utwórz obiekt blob przykładowy plik do przekazania do Twojej kontenera magazynu obiektów blob. Na maszynie Wirtualnej systemu Linux można to zrobić za pomocą następującego polecenia. 

```bash
echo "This is a test file." > test.txt
```

Następnie uwierzytelniania za pomocą interfejsu wiersza polecenia `az storage` polecenie, używając poświadczeń sygnatury dostępu Współdzielonego i przekazać go do kontenera obiektów blob. W tym kroku musisz [zainstalować najnowsze interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na maszynie Wirtualnej, jeśli nie jest jeszcze.

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

Ponadto można pobrać pliku za pomocą wiersza polecenia platformy Azure oraz uwierzytelniania poświadczeń sygnatury dostępu Współdzielonego. 

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

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób korzystania z tożsamości usługi zarządzane na maszynie wirtualnej systemu Linux dostęp do magazynu Azure przy użyciu poświadczeń sygnatury dostępu Współdzielonego.  Aby dowiedzieć się więcej o SAS magazynu Azure, zobacz:

> [!div class="nextstepaction"]
>[Przy użyciu sygnatury dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
