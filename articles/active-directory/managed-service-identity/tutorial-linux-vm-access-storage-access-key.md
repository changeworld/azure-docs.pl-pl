---
title: Umożliwia dostęp do usługi Azure Storage MSI maszyny Wirtualnej systemu Linux
description: Samouczek, który przeprowadzi Cię przez proces przy użyciu systemu Linux VM zarządzane usługi tożsamości (MSI), dostęp do magazynu Azure.
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
ms.openlocfilehash: 6ca43f86943a0dd90369d285c6bb3356ae7e0b5f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Umożliwia dostęp do usługi Azure Storage za pomocą klucza dostępu maszyny Wirtualnej systemu Linux zarządzanych tożsamości usługi

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek pokazuje, jak włączyć zarządzane tożsamości usługi (MSI) dla maszyny wirtualnej systemu Linux, a następnie użyć tej tożsamości można pobrać klucze dostępu do konta magazynu. Można użyć klucza dostępu do magazynu w zwykły sposób podczas wykonywania operacji magazynu, na przykład w przypadku korzystania z zestawu SDK usługi Magazyn. W tym samouczku będziemy przekazywanie i pobieranie obiektów blob przy użyciu wiersza polecenia platformy Azure. Dowiesz się jak:

> [!div class="checklist"]
> * Włącz MSI w maszynie wirtualnej systemu Linux 
> * Przyznać uprawnienia maszyny Wirtualnej do kluczy dostępu do konta magazynu w programie Menedżer zasobów 
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu pobrać klucze dostępu do magazynu z Menedżera zasobów  

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
2. Kliknij przycisk **kontenery** łącze w z lewej strony, w obszarze "Usługa Blob".
3. Kliknij przycisk **+ kontener** górnej części strony, a "nowy kontener" panelu slajdów wychodzących.
4. Nadaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa będzie używana później w samouczku. 

    ![Tworzenie kontenera magazynu](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Przyznanie dostępu MSI z maszyną Wirtualną do używania kluczy dostępu do konta magazynu

Magazyn Azure nie obsługuje natywnie uwierzytelniania usługi Azure AD.  Jednak można użyć Instalatora MSI można pobrać klucze dostępu do konta magazynu z Menedżera zasobów, a następnie użyć klucza dostępu do magazynu.  W tym kroku należy przyznać uprawnienia MSI maszyny Wirtualnej do kluczy do konta magazynu.   

1. Przejdź z powrotem do użytkownika nowo utworzone konto magazynu.
2. Kliknij przycisk **(IAM) kontroli dostępu** łącze w lewym panelu.  
3. Kliknij przycisk **+ Dodaj** u góry strony, aby dodać nowe przypisanie roli dla maszyny Wirtualnej
4. Ustaw **roli** do "Magazynu konta klucza usługi roli operatora", w prawej części strony. 
5. Na liście rozwijanej dalej, ustaw **przypisany dostęp** zasobu "Maszyny wirtualnej".  
6. Następnie sprawdź odpowiednie subskrypcji znajduje się w **subskrypcji** listy rozwijanej, a następnie ustaw **grupy zasobów** do "Wszystkie grupy zasobów".  
7. Ponadto w obszarze **wybierz** wybierz maszynę wirtualną systemu Linux na liście rozwijanej, a następnie kliknij przycisk **zapisać**. 

    ![Tekst alternatywny obrazu](../media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

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
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Uzyskaj z usługi Azure Resource Manager w celu wykonywania wywołań magazynu kluczy dostępu do konta magazynu  

Teraz używać CURL, aby wywołać Resource Manager przy użyciu tokenu dostępu, możemy pobrany w poprzedniej sekcji, aby pobrać klucz dostępu do magazynu. Po klucz dostępu do magazynu, firma Microsoft może nazywa się operacje przekazywania/Pobierz magazynu. Pamiętaj zastąpić `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, i `<STORAGE ACCOUNT NAME>` wartości parametrów z własne wartości. Zastąp `<ACCESS TOKEN>` wartość przy użyciu tokenu dostępu został wcześniej pobrany:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Tekst w adresie URL wcześniejsze jest uwzględniana wielkość liter, dlatego upewnij się, jeśli używasz górna małe dla grupy zasobów, uwzględnienie w związku z tym. Ponadto jest ważne dowiedzieć się, że jest to żądanie POST nie żądanie GET i upewnij się, że należy przekazać wartość do przechwytywania limit długości - d, który może mieć wartości NULL.  

Odpowiedź CURL zawiera listę kluczy:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Utwórz obiekt blob przykładowy plik do przekazania do Twojej kontenera magazynu obiektów blob. Na maszynie Wirtualnej systemu Linux można to zrobić za pomocą następującego polecenia. 

```bash
echo "This is a test file." > test.txt
```

Następnie uwierzytelniania za pomocą interfejsu wiersza polecenia `az storage` polecenie, używając klucz dostępu do magazynu i przekazać go do kontenera obiektów blob. W tym kroku musisz [zainstalować najnowsze interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na maszynie Wirtualnej, jeśli nie jest jeszcze.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Odpowiedź: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Ponadto można pobrać pliku za pomocą wiersza polecenia platformy Azure oraz uwierzytelniania klucz dostępu do magazynu. 

Żądanie: 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Odpowiedź: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
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
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
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

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](overview.md).
- Aby dowiedzieć się, jak to zrobić to opisane w tym samouczku przy użyciu magazynu poświadczeń sygnatury dostępu Współdzielonego, zobacz [umożliwia dostęp do usługi Azure Storage za pomocą poświadczeń sygnatury dostępu Współdzielonego maszyny Wirtualnej systemu Linux zarządzanych tożsamości usługi](tutorial-linux-vm-access-storage-sas.md)
- Aby uzyskać więcej informacji o funkcji sygnatury dostępu Współdzielonego konta usługi Azure Storage zobacz:
  - [Przy użyciu sygnatury dostępu współdzielonego (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Utworzenie sygnatury dostępu Współdzielonego usługi](/rest/api/storageservices/Constructing-a-Service-SAS.md)

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
