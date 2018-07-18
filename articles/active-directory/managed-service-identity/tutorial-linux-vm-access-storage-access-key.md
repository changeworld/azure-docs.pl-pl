---
title: Uzyskiwanie dostępu do usługi Azure Storage przy użyciu tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux
description: Samouczek przedstawiający proces użycia tożsamości usługi zarządzanej (MSI) na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Storage.
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
ms.openlocfilehash: cde1af63fe609170c65bc469fa57573d7bc48490
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901514"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Samouczek: uzyskiwanie dostępu do usługi Azure Storage za pośrednictwem klucza dostępu przy użyciu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Linux

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek przedstawia sposób włączania tożsamości usługi zarządzanej (MSI) dla maszyny wirtualnej z systemem Linux, a następnie użycia tej tożsamości do pobrania kluczy dostępu do konta magazynu. Możesz użyć kluczy dostępu do magazynu w zwykły sposób wykorzystywany podczas wykonywania operacji magazynu, np. podczas używania zestawu SDK usługi Storage. W tym samouczku przekażemy i pobierzemy obiekty blob przy użyciu interfejsu wiersza polecenia platformy Azure. Poznasz następujące czynności:

> [!div class="checklist"]
> * Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux 
> * Udzielanie maszynie wirtualnej dostępu do kluczy dostępu konta magazynu w usłudze Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania kluczy dostępu magazynu z usługi Resource Manager  

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Tworzenie maszyny wirtualnej z systemem Linux w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę wirtualną z systemem Linux. Możesz też włączyć tożsamość usługi zarządzanej na istniejącej maszynie wirtualnej.

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. W obszarze **Typ uwierzytelniania** wybierz pozycję **Klucz publiczny SSH** lub **Hasło**. Utworzone poświadczenia umożliwiają logowanie na maszynie wirtualnej.

    ![Alternatywny tekst obrazu](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Wybierz **subskrypcję** dla maszyny wirtualnej z listy rozwijanej.
5. Aby wybrać nową **grupę zasobów**, w której chcesz utworzyć maszynę wirtualną, wybierz pozycję **Utwórz nową**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycję **Wyświetl wszystkie** lub zmień filtr Obsługiwany typ dysku. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

## <a name="enable-msi-on-your-vm"></a>Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej

Tożsamość usługi zarządzanej maszyny wirtualnej umożliwia uzyskanie tokenów dostępu z usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. Włączenie tożsamości usługi zarządzanej na maszynie wirtualnej powoduje wykonanie dwóch czynności: zapewnia rejestrację maszyny wirtualnej w usłudze Azure Active Directory, aby utworzyć tożsamość zarządzaną, oraz konfiguruje tożsamość na maszynie wirtualnej.  

1. Przejdź do grupy zasobów nowej maszyny wirtualnej, a następnie wybierz maszynę wirtualną utworzoną w poprzednim kroku.
2. W sekcji „Ustawienia” maszyny wirtualnej po lewej stronie kliknij opcję **Konfiguracja**.
3. Aby zarejestrować i włączyć tożsamość usługi zarządzanej, wybierz opcję **Tak**. Jeśli chcesz ją wyłączyć, wybierz opcję Nie.
4. Pamiętaj, aby kliknąć przycisk **Zapisz** w celu zapisania konfiguracji.

    ![Alternatywny tekst obrazu](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Jeśli jeszcze nie masz konta magazynu, teraz je utworzysz.  Możesz też pominąć ten krok i przyznać tożsamości usługi zarządzanej maszyny wirtualnej dostęp do kluczy na istniejącym koncie magazynu. 

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij opcję **Magazyn**, a następnie **Konto magazynu**. Zostanie wyświetlony nowy panel „Utwórz konto magazynu”.
3. Wprowadź **nazwę** konta magazynu, której będziesz używać później.  
4. Opcje **Model wdrażania** i **Rodzaj konta** należy ustawić na „Resource Manager” i „Ogólnego przeznaczenia”. 
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.
6. Kliknij przycisk **Utwórz**.

    ![Tworzenie nowego konta magazynu](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Tworzenie kontenera obiektów blob na koncie magazynu

Później przekażemy i pobierzemy plik do nowego konta magazynu. Ponieważ pliki wymagają magazynu obiektów blob, musimy utworzyć kontener obiektów blob, w którym będziemy przechowywać plik.

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. Kliknij link **Kontenery** po lewej stronie, w sekcji „Usługa obiektów blob”.
3. Kliknij pozycję **+ Kontener** w górnej części strony, aby wysunąć panel „Nowy kontener”.
4. Podaj nazwę kontenera, wybierz poziom dostępu, a następnie kliknij przycisk **OK**. Podana nazwa będzie używana w dalszej części tego samouczka. 

    ![Tworzenie kontenera magazynu](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Udzielanie tożsamości usługi zarządzanej maszyny wirtualnej dostępu do używania kluczy dostępu konta magazynu

Usługa Azure Storage nie zapewnia natywnej obsługi uwierzytelniania usługi Azure AD.  Jednak możesz użyć tożsamości usługi zarządzanej, aby pobrać klucze dostępu konta magazynu z usługi Resource Manager, a następnie użyć klucza do uzyskania dostępu do magazynu.  W tym kroku udzielasz tożsamości usługi zarządzanej maszyny wirtualnej dostępu do kluczy do konta magazynu.   

1. Przejdź z powrotem do nowo utworzonego konta magazynu.
2. Kliknij link **Kontrola dostępu (IAM)** w panelu po lewej stronie.  
3. Kliknij przycisk **+ Dodaj** w górnej części strony, aby dodać nowe przypisanie roli dla maszyny wirtualnej.
4. Ustaw opcję **Rola** na „Rola usługi operatora kluczy konta magazynu” w prawej części strony. 
5. Na kolejnej liście rozwijanej ustaw opcję **Przypisz dostęp do** na zasób „Maszyna wirtualna”.  
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej **Subskrypcja**, po czym ustaw opcję **Grupa zasobów** na wartość „Wszystkie grupy zasobów”.  
7. Na koniec w pozycji **Wybierz** użyj listy rozwijanej, aby wybrać maszynę wirtualną z systemem Linux, i kliknij przycisk **Zapisz**. 

    ![Alternatywny tekst obrazu](../media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

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
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Uzyskiwanie kluczy dostępu konta magazynu z usługi Azure Resource Manager w celu wykonywania wywołań magazynu  

Teraz użyj programu CURL, aby wywołać usługę Resource Manager przy użyciu tokenu dostępu pozyskanego w poprzedniej sekcji, aby pobrać klucz dostępu do magazynu. Po uzyskaniu klucza dostępu do magazynu możemy wywołać operacje przekazywania/pobierania magazynu. Pamiętaj, aby zastąpić parametry `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` i `<STORAGE ACCOUNT NAME>` własnymi wartościami. Zastąp wartość `<ACCESS TOKEN>` tokenem dostępu pobranym wcześniej:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> W tekście w poprzednim adresie URL rozróżniana jest wielkość liter, więc upewnij się, że stosujesz odpowiednie małe i wielkie litery dla grup zasobów, aby odpowiednio je przedstawić. Ponadto ważna informacja jest taka, że jest to żądanie POST, a nie GET. Należy upewnić się, że zostaje przekazana wartość do przechwytywania limitu długości z parametrem -d, który może mieć wartość NULL.  

Odpowiedź programu CURL zapewnia listę kluczy:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Utwórz przykładowy plik obiektu blob do przekazania do kontenera magazynu obiektów blob. Na maszynie wirtualnej z systemem Linux możesz to zrobić przy użyciu następującego polecenia. 

```bash
echo "This is a test file." > test.txt
```

Następnie przeprowadź uwierzytelnianie przy użyciu polecenia `az storage` interfejsu wiersza polecenia, korzystając z klucza dostępu do magazynu, i przekaż plik do kontenera obiektów blob. W tym kroku musisz [zainstalować najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na maszynie wirtualnej, jeśli jeszcze tego nie zrobiono.
 

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

Ponadto możesz pobrać plik przy użyciu interfejsu wiersza polecenia platformy Azure oraz przeprowadzić uwierzytelnianie przy użyciu klucza dostępu do magazynu. 

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

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Storage przy użyciu klucza dostępu.  Aby dowiedzieć się więcej o kluczach dostępu usługi Azure Storage, zobacz:

> [!div class="nextstepaction"]
>[Zarządzanie kluczami dostępu do magazynu](/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys)
