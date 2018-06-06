---
title: Użyj tożsamości zarządzanych systemu Windows maszyny Wirtualnej dostępu do magazynu Azure
description: Samouczek, który przeprowadzi Cię przez proces dostęp do magazynu Azure za pomocą tożsamości zarządzanych maszyn wirtualnych systemu Windows.
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
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: 9ccc94727a18fbcd77f00000531934be57b8e132
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801368"
---
# <a name="tutorial-use-a-windows-vm-managed-identity-to-access-azure-storage"></a>Samouczek: Korzystania z tożsamości zarządzanego maszyny Wirtualnej systemu Windows dostęp do magazynu Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób włączyć zarządzane tożsamości dla maszyny wirtualnej systemu Windows, a dostęp do usługi Azure Storage za pomocą tej tożsamości.  Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz maszynę wirtualną systemu Windows w nowej grupy zasobów 
> * Włącz zarządzane tożsamości systemu Windows maszyny wirtualnej (VM)
> * Tworzenie kontenera obiektów blob na koncie magazynu
> * Udostępnienia maszyny Wirtualnej systemu Windows tożsamości zarządzanego konta magazynu 
> * Uzyskiwanie dostępu i użyć go do wywołania usługi Azure Storage 

> [!NOTE]
> Azure uwierzytelniania usługi Active Directory dla usługi Azure Storage znajduje się w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Windows w nowej grupy zasobów

W tej sekcji utworzysz maszynę Wirtualną systemu Windows, która otrzymuje później tożsamością zarządzane.

1.  Kliknij przycisk **+/ Utwórz nową usługę** znaleziono przycisku w lewym górnym rogu portalu Azure.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Username** i **hasło** utworzony, w tym miejscu jest poświadczeń umożliwia logowanie do maszyny wirtualnej.
4.  Wybierz odpowiednią **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5.  Aby wybrać nowy **grupy zasobów** chcesz maszynę wirtualną można utworzyć w, wybierz **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

    ![Tekst alternatywny obrazu](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-identity-on-your-vm"></a>Włącz zarządzane tożsamości na maszynie Wirtualnej

Tożsamość zarządzane maszyny wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. W obszarze obejmuje włączenie zarządzane tożsamości na maszynie wirtualnej za pośrednictwem portalu Azure wykonuje dwie czynności: rejestruje maszyny Wirtualnej z usługą Azure AD można utworzyć tożsamości zarządzanych oraz konfiguruje tożsamości na maszynie Wirtualnej. 

1. Przejdź do grupy zasobów nowej maszyny wirtualnej, a następnie wybierz maszynę wirtualną, utworzony w poprzednim kroku.
2. W obszarze **ustawienia** kategorii, kliknij przycisk **konfiguracji**.
3. Aby włączyć tożsamości zarządzanego, wybierz **tak**.
4. Kliknij przycisk **zapisać** Aby zastosować konfigurację. 

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

W tej sekcji utworzysz konto magazynu. 

1. Kliknij przycisk **+ Utwórz zasób** znaleziono przycisku w lewym górnym rogu portalu Azure.
2. Kliknij przycisk **magazynu**, następnie **konta magazynu — obiekt blob, plików, tabeli, kolejki**.
3. W obszarze **nazwa**, wprowadź nazwę konta magazynu.  
4. **Model wdrażania** i **konta rodzaju** powinien być ustawiony na **Menedżera zasobów** i **magazynu (ogólnego przeznaczenia v1)**. 
5. Upewnij się, **subskrypcji** i **grupy zasobów** odpowiadały określony podczas tworzenia maszyny Wirtualnej w poprzednim kroku.
6. Kliknij przycisk **Utwórz**.

    ![Utwórz nowe konto magazynu](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Tworzenie kontenera obiektów blob i przekazywanie pliku do konta magazynu

Pliki wymagają magazynu obiektów blob, dlatego należy utworzyć kontener obiektów blob do przechowywania pliku. Następnie możesz przekazać plik do kontenera obiektów blob na koncie magazynu.

1. Przejdź z powrotem do użytkownika nowo utworzone konto magazynu.
2. W obszarze **usługa Blob**, kliknij przycisk **kontenery**.
3. Kliknij przycisk **+ kontener** górnej części strony.
4. W obszarze **nowy kontener**, wprowadź nazwę dla kontenera, a następnie w obszarze **poziom dostępu publicznego** zachować wartość domyślną.

    ![Tworzenie kontenera magazynu](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Za pomocą edytora wybranym, Utwórz plik zatytułowany *hello world.txt* na komputerze lokalnym.  Otwórz plik i dodać tekst (bez cudzysłowów) "Witaj świecie! :) ", a następnie zapisz go. 
6. Przekaż plik na nowo utworzony kontenera, klikając nazwę kontenera, następnie **Przekaż**
7. W **przekazywanie obiektu blob** okienku w obszarze **pliki**, kliknij ikonę folderu i przejdź do pliku **hello_world.txt** na komputerze lokalnym, wybierz plik, a następnie kliknij przycisk **Przekazać**.
    ![Przekaż plik tekstowy](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Przyznać uprawnienia maszyny Wirtualnej do kontenera magazynu Azure 

Korzystania z tożsamości zarządzanego maszyny Wirtualnej, aby pobrać dane z obiektu blob magazynu Azure.   

1. Przejdź z powrotem do użytkownika nowo utworzone konto magazynu.  
2. Kliknij przycisk **(IAM) kontroli dostępu** łącze w lewym panelu.  
3. Kliknij przycisk **+ Dodaj** u góry strony, aby dodać nowe przypisanie roli dla maszyny Wirtualnej.
4. W obszarze **roli**, wybierz z listy rozwijanej, **czytnik danych magazynu obiektów Blob (wersja zapoznawcza)**. 
5. Na liście rozwijanej dalej w obszarze **przypisany dostęp**, wybierz **maszyny wirtualnej**.  
6. Następnie sprawdź odpowiednie subskrypcji znajduje się w **subskrypcji** listy rozwijanej, a następnie ustaw **grupy zasobów** do **wszystkich grup zasobów**.  
7. W obszarze **wybierz**, wybrać maszyny Wirtualnej, a następnie kliknij przycisk **zapisać**. 

    ![Przypisywanie uprawnień](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Uzyskaj token dostępu i użyć go do wywołania usługi Azure Storage 

Magazyn Azure natywnie obsługuje usługi Azure AD uwierzytelnianie, więc bezpośrednio może akceptować tokeny dostępu uzyskany przy użyciu tożsamości usługi zarządzane. To jest częścią usługi Azure Storage integracji z usługą Azure AD i różni się od podawania poświadczeń w parametrach połączenia.

Oto przykład kodu .net otwarcia połączenia do magazynu Azure przy użyciu tokenu dostępu, a następnie odczytanie zawartości pliku utworzonego wcześniej. Ten kod należy uruchomić na maszynie Wirtualnej, aby można było uzyskać dostępu do punktu końcowego zarządzanych tożsamości maszyny Wirtualnej. .NET framework 4.6 lub nowszy jest wymagany przy użyciu metody tokenu dostępu. Zastąp wartość `<URI to blob file>` odpowiednio. Tę wartość można uzyskać, przechodząc do pliku utworzony i przekazania jej do magazynu obiektów blob i kopiowanie **adres URL** w obszarze **właściwości** **omówienie** strony.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");
           
            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);
        
            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }            
    }
}
```

Odpowiedź zawiera zawartość pliku:

`Hello world! :)`

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób włączenia maszyny wirtualnej systemu Windows tożsamości zarządzanego dostępu do magazynu Azure.  Aby dowiedzieć się więcej o usłudze Azure Storage, zobacz:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)



