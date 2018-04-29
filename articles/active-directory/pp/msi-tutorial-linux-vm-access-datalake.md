---
title: Jak używać systemu Linux VM zarządzane usługi tożsamości (MSI) można uzyskać dostępu do usługi Azure Data Lake Store
description: Samouczek przedstawiający sposób użycia Linux VM zarządzane usługi tożsamości (MSI) można uzyskać dostępu do usługi Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a70f02fca5ebf575bc009623c3af648a5a80fd70
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Umożliwia dostęp do usługi Azure Data Lake Store Linux VM zarządzane usługi tożsamości (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

W tym samouczku przedstawiono sposób użycia zarządzane tożsamości usługi (MSI) dla maszyny wirtualnej systemu Linux (VM) do usługi Azure Data Lake Store. Zarządzane tożsamości usługi automatycznie są zarządzane przez usługę Azure i umożliwiają uwierzytelniania w usłudze, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności wstawić poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Włącz MSI w maszynie Wirtualnej systemu Linux 
> * Udziel dostępu maszyny Wirtualnej do usługi Azure Data Lake Store
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i umożliwia dostęp do usługi Azure Data Lake Store

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Linux w nowej grupy zasobów

W tym samouczku utworzymy nową maszynę Wirtualną systemu Linux. Można również włączyć MSI na istniejącej maszyny Wirtualnej.

1. Kliknij przycisk **Utwórz zasób** w lewym górnym rogu portalu Azure.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. Aby uzyskać **typ uwierzytelniania**, wybierz pozycję **klucz publiczny SSH** lub **hasło**. Utworzony poświadczenia umożliwiają Zaloguj się do maszyny Wirtualnej.

   ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Wybierz **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5. Aby wybrać nowy **grupy zasobów** chcesz maszyny wirtualnej utworzone w, wybierz **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiary, wybierz **Wyświetl wszystkie** lub zmień filtr typu dysku obsługiwane. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

## <a name="enable-msi-on-your-vm"></a>Włącz MSI na maszynie Wirtualnej

MSI maszyny wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. W obszarze obejmuje włączenie MSI wykonuje dwie czynności: instalacji rozszerzenia maszyny Wirtualnej MSI w maszynie Wirtualnej i umożliwia korzystanie z pliku MSI usługi Azure Resource Manager.  

1. Wybierz **maszyny wirtualnej** chcesz włączyć MSI.
2. Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**.
3. Zostanie wyświetlony **zarządzane tożsamość usługi**. Aby zarejestrować i włączyć MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz opcję nie.
4. Upewnij się, możesz kliknąć przycisk **zapisać** Aby zapisać konfigurację.

   ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Jeśli chcesz sprawdzić, które rozszerzenia są na tym **maszyny Wirtualnej systemu Linux**, kliknij przycisk **rozszerzenia**. Po włączeniu MSI **ManagedIdentityExtensionforLinux** pojawia się na liście.

   ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Udziel dostępu maszyny Wirtualnej do usługi Azure Data Lake Store

Teraz można przyznać dostęp do plików i folderów w usłudze Azure Data Lake — magazyn maszyny Wirtualnej.  Dla tego kroku można użyć istniejącej usługi Data Lake Store lub Utwórz nową.  Aby utworzyć nowy Data Lake Store przy użyciu portalu Azure, wykonaj to [szybkiego startu usługi Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Dostępne są także Przewodniki Szybki Start, którego wiersza polecenia platformy Azure i programu Azure PowerShell w programie [dokumentacji usługi Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md).

W usługi Data Lake Store Utwórz nowy folder i przyznanie uprawnień do odczytu, zapisu i wykonywania pliki w tym folderze MSI maszyny Wirtualnej:

1. W portalu Azure kliknij **usługi Data Lake Store** w obszarze nawigacji po lewej stronie.
2. Kliknij przycisk Data Lake Store, którego chcesz użyć na potrzeby tego samouczka.
3. Kliknij przycisk **Eksploratora danych** na pasku poleceń.
4. Wybrano folderu głównego usługi Data Lake Store.  Kliknij przycisk **dostępu** na pasku poleceń.
5. Kliknij pozycję **Add** (Dodaj).  W **wybierz** wprowadź nazwę maszyny Wirtualnej, na przykład **DevTestVM**.  Kliknij, aby wybrać maszyny Wirtualnej w wynikach wyszukiwania, a następnie kliknij przycisk **wybierz**.
6. Kliknij przycisk **wybierz uprawnienia**.  Wybierz **odczytu** i **Execute**, Dodaj do **ten folder**i Dodaj jako **uprawnienie**.  Kliknij przycisk **OK**.  Uprawnienie powinno być dodane pomyślnie.
7. Zamknij **dostępu** bloku.
8. W tym samouczku Utwórz nowy folder.  Kliknij przycisk **nowy Folder** w pasku poleceń, a także pozwalają nowy folder na przykład nazwa **TestFolder**.  Kliknij przycisk **OK**.
9. Kliknij utworzony folder, a następnie kliknij przycisk **dostępu** na pasku poleceń.
10. Podobne do kroku 5, kliknij przycisk **Dodaj**w **wybierz** polu wprowadź nazwę maszyny Wirtualnej, zaznacz go i kliknij **wybierz**.
11. Podobne do kroku 6, kliknij przycisk **wybierz uprawnienia**, wybierz pozycję **odczytu**, **zapisu**, i **Execute**, Dodaj do **ten folder**i Dodaj jako **wpisu uprawnienia dostępu i wpis uprawnienia domyślne**.  Kliknij przycisk **OK**.  Uprawnienie powinno być dodane pomyślnie.

MSI Twojej maszyny Wirtualnej można teraz wykonywać wszystkie operacje na plikach w folderze, który został utworzony.  Dla więcej informacji na temat zarządzania dostępem do usługi Data Lake Store, przeczytaj ten artykuł na [kontroli dostępu w usłudze Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Uzyskaj token dostępu za pomocą Instalatora MSI maszyny Wirtualnej i użyć go do wywołania w systemie plików usługi Azure Data Lake Store

Azure Data Lake Store natywnie obsługuje usługi Azure AD uwierzytelnianie, więc bezpośrednio może akceptować tokeny dostępu uzyskany przy użyciu Instalatora MSI.  Do uwierzytelniania w systemie plików usługi Data Lake Store możesz wysłać token dostępu wystawiony przez usługę Azure AD dla usługi Data Lake Store filesystem punktu końcowego, w nagłówku autoryzacji w formacie "Bearer \<ACCESS_TOKEN_VALUE\>".  Aby dowiedzieć się więcej na temat usługi Data Lake Store obsługę uwierzytelniania usługi Azure AD, przeczytaj [uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

W tym samouczku uwierzytelniania w systemie plików usługi Data Lake Store żądań interfejsu API REST przy użyciu programu CURL dokonanie REST.

> [!NOTE]
> Klient systemu plików usługi Data Lake Store zestawów SDK nie jest jeszcze obsługiwany zarządzane tożsamości usługi.  W tym samouczku zostaną zaktualizowane po dodaniu obsługi do zestawu SDK.

Aby wykonać te kroki, należy klient SSH. Jeśli korzystasz z systemu Windows, możesz użyć klienta SSH w [podsystemu systemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeśli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [sposobu użycia SSH kluczy systemu Windows Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), lub [sposobu tworzenia i używania SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux na platformie Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu przejdź do maszyny Wirtualnej systemu Linux i w **omówienie**, kliknij przycisk **Connect**.  
2. **Połącz** do maszyny Wirtualnej przy użyciu klienta SSH wybranych przez użytkownika. 
3. W oknie terminalu przy użyciu programu CURL, Wyślij żądanie do lokalnego punktu końcowego MSI do Uzyskaj token dostępu do systemu plików usługi Data Lake Store.  Identyfikator zasobu usługi Data Lake Store jest "https://datalake.azure.net/."  Należy dołączyć końcowy ukośnik w identyfikator zasobu.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Odpowiedź oznaczająca Powodzenie zwraca token dostępu, który używany do uwierzytelniania do usługi Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Przy użyciu programu CURL, Wyślij żądanie do punkt końcowy REST systemu plików usługi Data Lake Store, aby wyświetlić listę folderów w folderze głównym.  Jest to prosty sposób sprawdzić, czy wszystko jest poprawnie skonfigurowana.  Skopiuj wartość tokenu dostępu z poprzedniego kroku.  Jest ważne, czy ciągu "Bearer" w nagłówku autoryzacji ma duże "B".  Można znaleźć nazwy usługi Data Lake Store w **omówienie** części bloku Data Lake Store w portalu Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Odpowiedź oznaczająca Powodzenie wygląda następująco:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Teraz możesz spróbować przekazywanie pliku do usługi Data Lake Store.  Najpierw należy utworzyć plik do przekazania.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Przy użyciu programu CURL, Wyślij żądanie do punktu końcowego REST systemu plików usługi Data Lake Store można przekazać pliku do folderu utworzonego wcześniej.  Przekazywanie obejmuje przekierowanie i ZWINIĘCIE następuje przekierowanie automatycznie. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Odpowiedź oznaczająca Powodzenie wygląda następująco:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Przy użyciu systemu plików inne usługi Data Lake Store interfejsów API, możesz dołączyć do plików, pobierania plików i inne.

Gratulacje!  Uwierzytelniono systemie plików usługi Data Lake Store za pomocą MSI maszyny Wirtualnej.

## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](msi-overview.md).
- W przypadku zarządzania operacji Data Lake Store używa usługi Azure Resource Manager.  Aby uzyskać więcej informacji na temat używania MSI maszyny Wirtualnej do uwierzytelniania do Menedżera zasobów, przeczytaj [umożliwia dostęp do Menedżera zasobów systemu Linux VM zarządzane usługi tożsamości (MSI)](../managed-service-identity/msi-tutorial-linux-vm-access-arm.md).
- Dowiedz się więcej o [uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Dowiedz się więcej o [operacji systemu plików w usłudze Azure Data Lake Store za pomocą interfejsu API REST](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) lub [interfejsów API systemu plików WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis.md).
- Dowiedz się więcej o [kontroli dostępu w usłudze Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.