---
title: Umożliwia dostęp do usługi Azure Data Lake Store zarządzane tożsamości usługi dla maszyny Wirtualnej systemu Linux
description: Samouczek przedstawiający sposób użycia zarządzane usługi tożsamości (MSI) dla maszyny Wirtualnej systemu Linux można uzyskać dostępu do usługi Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: 70dd61758b30c1c7a89374e708fe37c1443d3a2a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Samouczek: Użyj zarządzanych tożsamości usługi dla maszyny Wirtualnej systemu Linux uzyskać dostępu do usługi Azure Data Lake Store

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób użycia zarządzane tożsamości usługi dla maszyny wirtualnej systemu Linux (VM) do usługi Azure Data Lake Store. Azure automatycznie zarządza tożsamości, które można tworzyć za pomocą Instalatora MSI. MSI służy do uwierzytelniania do usług, które obsługują uwierzytelnianie w usłudze Azure Active Directory (Azure AD), bez konieczności wstawić poświadczeń do kodu. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włącz MSI w maszynie Wirtualnej systemu Linux. 
> * Przyznać uprawnienia maszyny Wirtualnej do usługi Azure Data Lake Store.
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i umożliwia dostęp do usługi Azure Data Lake Store.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Linux w nowej grupy zasobów

W tym samouczku utworzymy nową maszynę Wirtualną systemu Linux. Można również włączyć MSI na istniejącej maszyny Wirtualnej.

1. Wybierz **nowy** przycisk w lewym górnym rogu portalu Azure.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. Aby uzyskać **typ uwierzytelniania**, wybierz pozycję **klucz publiczny SSH** lub **hasło**. Utworzony poświadczenia umożliwiają Zaloguj się do maszyny Wirtualnej.

   ![Okienko "Podstawowe" do tworzenia maszyny wirtualnej](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. W **subskrypcji** listy, wybierz subskrypcję dla maszyny wirtualnej.
5. Aby wybrać nową grupę zasobów, interesujące maszyny wirtualnej zostały utworzone w, zaznacz **grupy zasobów** > **Utwórz nowy**. Gdy skończysz, wybierz **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. W okienku ustawienia Zachowaj wartości domyślne, a następnie wybierz **OK**.

## <a name="enable-msi-on-your-vm"></a>Włącz MSI na maszynie Wirtualnej

MSI maszyny Wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie zarządzania tożsamości usługi na maszynie Wirtualnej, wykonuje dwie czynności: rejestrów maszyny Wirtualnej z usługi Azure Active Directory do utworzenia zarządzanej tożsamości, a konfiguruje tożsamości na maszynie Wirtualnej.

1. Aby uzyskać **maszyny wirtualnej**, wybierz maszynę wirtualną, którą chcesz włączyć MSI.
2. W okienku po lewej stronie wybierz **konfiguracji**.
3. Zostanie wyświetlony **tożsamość usługi zarządzanej**. Aby zarejestrować i włączyć MSI, wybierz **tak**. Jeśli chcesz ją wyłączyć, wybierz **nr**.
   ![Wybór "Rejestrowanie w usłudze Azure Active Directory"](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. Wybierz pozycję **Zapisz**.

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Udziel dostępu maszyny Wirtualnej do usługi Azure Data Lake Store

Teraz można przyznać dostęp do plików i folderów w usłudze Azure Data Lake — magazyn maszyny Wirtualnej. Dla tego kroku można użyć istniejącego wystąpienia usługi Data Lake Store lub Utwórz nową. Aby utworzyć wystąpienie usługi Data Lake Store za pomocą portalu Azure, wykonaj [szybkiego startu usługi Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Dostępne są także Przewodniki Szybki Start, którego wiersza polecenia platformy Azure i programu Azure PowerShell w programie [dokumentacji usługi Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

W usłudze Data Lake Store Utwórz nowy folder i przyznać MSI uprawnienia do odczytu, zapisu i wykonywania pliki w tym folderze:

1. W portalu Azure wybierz **usługi Data Lake Store** w okienku po lewej stronie.
2. Wybierz wystąpienie usługi Data Lake Store, którego chcesz używać.
3. Wybierz **Eksploratora danych** na pasku poleceń.
4. Folder główny wystąpienia usługi Data Lake Store jest zaznaczone. Wybierz **dostępu** na pasku poleceń.
5. Wybierz pozycję **Dodaj**.  W **wybierz** wprowadź nazwę maszyny Wirtualnej — na przykład **DevTestVM**. Wybierz maszyny Wirtualnej w wynikach wyszukiwania, a następnie kliknij przycisk **wybierz**.
6. Kliknij przycisk **wybierz uprawnienia**.  Wybierz **odczytu** i **Execute**, Dodaj do **ten folder**i Dodaj jako **uprawnienie**. Wybierz **Ok**.  Uprawnienie powinno być dodane pomyślnie.
7. Zamknij **dostępu** okienka.
8. W tym samouczku Utwórz nowy folder. Wybierz **nowy Folder** na pasku poleceń, a także pozwalają nowy folder nazwa — na przykład **TestFolder**.  Wybierz **Ok**.
9. Wybierz folder, który zostanie utworzony, a następnie wybierz **dostępu** na pasku poleceń.
10. Podobne do kroku 5, wybierz opcję **Dodaj**. W **wybierz** wprowadź nazwę maszyny Wirtualnej. Wybierz maszyny Wirtualnej w wynikach wyszukiwania, a następnie kliknij przycisk **wybierz**.
11. Podobne do kroku 6, wybierz opcję **wybierz uprawnienia**. Wybierz **odczytu**, **zapisu**, i **Execute**, Dodaj do **ten folder**i Dodaj jako **wpisu uprawnienia dostępu i domyślnym wpis uprawnienia**. Wybierz **Ok**.  Uprawnienie powinno być dodane pomyślnie.

MSI można teraz wykonywać wszystkie operacje na plikach w folderze, który został utworzony. Aby uzyskać więcej informacji na temat zarządzania dostęp do usługi Data Lake Store, zobacz [kontroli dostępu w usłudze Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Uzyskaj token dostępu i wywołań systemu plików usługi Data Lake Store

Azure Data Lake Store natywnie obsługuje usługi Azure AD uwierzytelnianie, więc bezpośrednio może akceptować tokeny dostępu uzyskać za pomocą Instalatora MSI. Na potrzeby uwierzytelniania w systemie plików usługi Data Lake Store, możesz wysłać token dostępu wystawiony przez usługę Azure AD dla punktu końcowego systemu plików usługi Data Lake Store. Token dostępu jest w nagłówku autoryzacji w formacie "Bearer \<ACCESS_TOKEN_VALUE\>".  Aby dowiedzieć się więcej na temat usługi Data Lake Store obsługę uwierzytelniania usługi Azure AD, zobacz [uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

W tym samouczku uwierzytelnić się na interfejs API REST dla systemu plików usługi Data Lake Store za pomocą cURL na wysyłanie żądań REST.

> [!NOTE]
> Zestawy SDK klienta dla systemu plików usługi Data Lake Store nie jest jeszcze obsługiwany zarządzane tożsamości usługi.

Aby wykonać te kroki, należy klient SSH. Jeśli korzystasz z systemu Windows, możesz użyć klienta SSH w [podsystemu systemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeśli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [kluczy sposobu korzystania z protokołu SSH z systemem Windows Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [sposobu tworzenia i używania SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu przejdź do maszyny Wirtualnej systemu Linux. W **omówienie**, wybierz pozycję **Connect**.  
2. Połączenie z maszyną Wirtualną przy użyciu klienta SSH wybranych przez użytkownika. 
3. W oknie terminalu przy użyciu zwinięcie, Utwórz żądanie lokalnego punktu końcowego MSI, aby uzyskać token dostępu dla usługi Data Lake Store systemu plików. Identyfikator zasobu usługi Data Lake Store jest "https://datalake.azure.net/".  Należy dołączyć końcowy ukośnik w identyfikator zasobu.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Odpowiedź oznaczająca Powodzenie zwraca tokenu dostępu, którego używasz do uwierzytelniania do usługi Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Za pomocą cURL, należy wysłać żądanie do punktu końcowego REST systemu plików usługi Data Lake Store, aby wyświetlić listę folderów w folderze głównym. Jest to prosty sposób sprawdzić, czy wszystko jest poprawnie skonfigurowany. Skopiuj wartość tokenu dostępu z poprzedniego kroku. Należy pamiętać, że ciąg "Bearer" w nagłówku autoryzacji ma kapitału "B" Można znaleźć nazwy wystąpienia usługi Data Lake Store w **omówienie** sekcji **usługi Data Lake Store** okienku w portalu Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Odpowiedź oznaczająca Powodzenie wygląda następująco:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Możesz teraz przekazać pliku do Twojego wystąpienia usługi Data Lake Store. Najpierw należy utworzyć plik do przekazania.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Za pomocą cURL, należy wysłać żądanie do punktu końcowego REST systemu plików usługi Data Lake Store można przekazać pliku do folderu, który został utworzony wcześniej. Przekazywanie obejmuje przekierowanie i zwinięcie następuje przekierowanie automatycznie. 

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

Przy użyciu innych interfejsów API systemu plików usługi Data Lake Store, możesz dołączyć do plików, pobierania plików i inne.

Gratulacje! W systemie plików usługi Data Lake Store uwierzytelniono za pomocą Instalatora MSI dla maszyny Wirtualnej systemu Linux.

## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](overview.md).
- Dla operacji zarządzania Data Lake Store używa usługi Azure Resource Manager.  Aby uzyskać więcej informacji na temat używania MSI do uwierzytelniania do Menedżera zasobów, zobacz [umożliwia dostęp do Menedżera zasobów systemu Linux VM zarządzane usługi tożsamości (MSI)](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- Dowiedz się więcej o [uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- Dowiedz się więcej o [plików operacji w systemie Azure Data Lake Store przy użyciu interfejsu API REST](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) lub [interfejsów API systemu plików WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Dowiedz się więcej o [kontroli dostępu w usłudze Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
