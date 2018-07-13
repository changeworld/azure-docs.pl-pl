---
title: Jak używać systemu Linux VM tożsamość usługi zarządzanej (MSI) do dostępu do usługi Azure Data Lake Store
description: Samouczek, w którym pokazano, jak dostęp do usługi Azure Data Lake Store przy użyciu systemu Linux VM tożsamość usługi zarządzanej (MSI).
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
ms.openlocfilehash: b1091ae25e247a0e2ce00de831886cde44ae14a7
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007836"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Umożliwia dostęp do usługi Azure Data Lake Store Linux VM tożsamość usługi zarządzanej (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

W tym samouczku dowiesz się, jak używać tożsamość usługi zarządzanej (MSI) dla systemu Linux maszyny wirtualnej (VM) do dostępu do usługi Azure Data Lake Store. Tożsamości usługi zarządzanej są zarządzane automatycznie przez platformę Azure i umożliwiają uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności Wstaw poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Włączanie tożsamości usługi Zarządzanej maszyny wirtualnej systemu Linux 
> * Udzielanie dostępu do sieci maszyny Wirtualnej do usługi Azure Data Lake Store
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu dostęp do usługi Azure Data Lake Store

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Linux w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę Wirtualną systemu Linux. Można również włączyć tożsamości usługi Zarządzanej istniejącej maszyny wirtualnej.

1. Kliknij przycisk **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Ubuntu Server 16.04 LTS**.
3. Wprowadź informacje o maszynie wirtualnej. Aby uzyskać **typ uwierzytelniania**, wybierz opcję **klucz publiczny SSH** lub **hasło**. Utworzono poświadczenia umożliwiają logowanie do maszyny Wirtualnej.

   ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Wybierz **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5. Aby wybrać nowy **grupy zasobów** chcesz maszyny wirtualnej pod kątem można tworzyć w **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycję **Wyświetl wszystkie** lub zmień filtr obsługiwany typ dysku. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

## <a name="enable-msi-on-your-vm"></a>Włączanie tożsamości usługi Zarządzanej na maszynie Wirtualnej

Z tożsamości usługi Zarządzanej maszyny wirtualnej umożliwia uzyskiwanie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie tożsamości usługi Zarządzanej w sposób niewidoczny wykonuje dwie czynności: instaluje rozszerzenia tożsamości usługi Zarządzanej maszyny Wirtualnej na maszynie Wirtualnej oraz umożliwia tożsamości usługi Zarządzanej w usłudze Azure Resource Manager.  

1. Wybierz **maszyny wirtualnej** chcesz włączyć tożsamości usługi Zarządzanej.
2. Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**.
3. Zostanie wyświetlony **tożsamości usługi zarządzanej**. Aby zarejestrować i włączyć plik MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz pozycję nie.
4. Upewnij się, możesz kliknąć pozycję **Zapisz** Aby zapisać konfigurację.

   ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Jeśli chcesz sprawdzić, jakie rozszerzenia są na tym **maszyny Wirtualnej systemu Linux**, kliknij przycisk **rozszerzenia**. Po włączeniu tożsamości usługi Zarządzanej **ManagedIdentityExtensionforLinux** pojawia się na liście.

   ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Udzielanie dostępu do sieci maszyny Wirtualnej do usługi Azure Data Lake Store

Teraz można udzielać dostępu do sieci maszyny Wirtualnej, do plików i folderów w usłudze Azure Data Lake Store.  W tym kroku możesz użyć istniejącej w Data Lake Store lub Utwórz nową.  Aby utworzyć nowy Store jeziora danych przy użyciu portalu Azure, postępuj zgodnie z tym [szybkiego startu usługi Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Dostępne są także Przewodniki Szybki Start, użyj wiersza polecenia platformy Azure i programu Azure PowerShell w [dokumentacji usługi Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md).

W usługi Data Lake Store Utwórz nowy folder i przyznaj uprawnienie usługi Zarządzanej maszyny Wirtualnej do odczytu, zapisu i wykonywania plików w tym folderze:

1. W witrynie Azure portal kliknij pozycję **Data Lake Store** w obszarze nawigacji po lewej stronie.
2. Kliknij pozycję Data Lake Store, którego chcesz użyć na potrzeby tego samouczka.
3. Kliknij przycisk **Eksplorator danych** na pasku poleceń.
4. W folderze głównym Data Lake Store jest zaznaczone.  Kliknij przycisk **dostępu** na pasku poleceń.
5. Kliknij pozycję **Add** (Dodaj).  W **wybierz** pole, wprowadź nazwę maszyny wirtualnej, na przykład **DevTestVM**.  Kliknij, aby wybrać maszynę Wirtualną z poziomu wyników wyszukiwania, a następnie kliknij przycisk **wybierz**.
6. Kliknij przycisk **wybierz uprawnienia**.  Wybierz **odczytu** i **Execute**, Dodaj do **ten folder**i Dodaj jako **uprawnienie**.  Kliknij przycisk **OK**.  Uprawnienia powinny zostać dodane pomyślnie.
7. Zamknij **dostępu** bloku.
8. Utwórz nowy folder na potrzeby tego samouczka.  Kliknij przycisk **nowy Folder** w pasku poleceń, a także pozwalają nowy folder na przykład nazwa **TestFolder**.  Kliknij przycisk **OK**.
9. Kliknij utworzony folder, a następnie kliknij przycisk **dostępu** na pasku poleceń.
10. Podobne do kroku 5, kliknij przycisk **Dodaj**w **wybierz** pole, wprowadź nazwę maszyny Wirtualnej, zaznacz go i kliknij **wybierz**.
11. Podobne do kroku 6, kliknij przycisk **wybierz uprawnienia**, wybierz opcję **odczytu**, **zapisu**, i **Execute**, Dodaj do **ten folder**i Dodaj jako **wpis uprawnień dostępu i domyślny wpis uprawnień**.  Kliknij przycisk **OK**.  Uprawnienia powinny zostać dodane pomyślnie.

Usługi Zarządzanej maszyny Wirtualnej można teraz wykonywać wszystkie operacje na plikach w folderze, który został utworzony.  Aby więcej informacji na temat zarządzania dostępem do programu Data Lake Store, przeczytaj ten artykuł w witrynie [kontroli dostępu w Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Uzyskiwanie tokenu dostępu przy użyciu Zarządzanej maszyny Wirtualnej i użyć go do wywołania w systemie plików usługi Azure Data Lake Store

Azure Data Lake Store natywnie obsługuje usługi Azure AD uwierzytelniania, dzięki czemu bezpośrednio może akceptować tokeny dostępu uzyskany przy użyciu tożsamości usługi Zarządzanej.  Do uwierzytelniania w systemie plików Data Lake Store Wyślij token dostępu wystawiony przez usługę Azure AD do usługi Data Lake Store filesystem punktu końcowego w nagłówku autoryzacji w formacie "Bearer \<ACCESS_TOKEN_VALUE\>".  Aby dowiedzieć się więcej na temat obsługi Data Lake Store uwierzytelniania usługi Azure AD, przeczytaj [uwierzytelniania za pomocą programu Data Lake Store przy użyciu usługi Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

W tym samouczku uwierzytelniania w systemie plików Data Lake Store, żądań interfejsu API REST przy użyciu programu CURL, aby REST.

> [!NOTE]
> Zestawy SDK klientów systemu plików Data Lake Store nie jest jeszcze obsługiwany tożsamości usługi zarządzanej.  W tym samouczku zostaną zaktualizowane, gdy obsługa zostanie dodany do zestawu SDK.

Aby wykonać te kroki, należy klienta SSH. Jeśli używasz Windows, możesz użyć klienta SSH w [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeśli potrzebujesz pomocy w konfigurowaniu klucze Twój klient SSH, zobacz [jak klucze używanie protokołu SSH z Windows Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), lub [sposobu tworzenia i używania publicznych i prywatnych pary kluczy SSH dla maszyn wirtualnych systemu Linux na platformie Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu, przejdź do maszyny Wirtualnej systemu Linux i w **Przegląd**, kliknij przycisk **Connect**.  
2. **Połącz** do maszyny Wirtualnej przy użyciu klienta SSH wybranych przez użytkownika. 
3. W oknie terminalu przy użyciu programu CURL, wysłać żądanie do lokalnego punktu końcowego pliku MSI do uzyskania tokenu dostępu do systemu plików Data Lake Store.  Identyfikator zasobu Data Lake Store jest "https://datalake.azure.net/."  Należy dołączyć końcowy ukośnik identyfikator zasobu.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Odpowiedź oznaczająca Powodzenie zwraca token dostępu, które służy do uwierzytelniania, aby Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Używając programu CURL, wysłać żądanie do punktu końcowego REST systemu plików Data Lake Store do listy folderów w folderze głównym.  Jest to prosty sposób w celu sprawdzenia, czy wszystko jest poprawnie skonfigurowane.  Skopiuj wartość tokenu dostępu z poprzedniego kroku.  Jest ważne, że ciąg "Bearer" w nagłówku autoryzacji ma wielkiej litery "B".  Można znaleźć nazwy usługi Data Lake Store, w **Przegląd** części bloku Data Lake Store w witrynie Azure portal.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Odpowiedź oznaczająca Powodzenie wygląda następująco:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Teraz możesz wypróbować, przekazując plik do usługi Data Lake Store.  Najpierw utwórz plik do przekazania.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Używając programu CURL, wysłać żądanie do punktu końcowego REST systemu plików Data Lake Store można przekazać pliku do folderu, która została utworzona wcześniej.  Przekazywanie obejmuje przekierowanie, a CURL następuje automatyczne przekierowania. 

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

Przy użyciu innego systemu plików Data Lake Store interfejsów API można dołączyć do plików, pobierania plików i inne.

Gratulacje!  Uwierzytelniono do systemu plików Data Lake Store za pomocą Zarządzanej maszyny Wirtualnej.

## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](msi-overview.md).
- Do zarządzania operations Data Lake Store używa usługi Azure Resource Manager.  Aby uzyskać więcej informacji na temat korzystania z tożsamości usługi Zarządzanej maszyny Wirtualnej w celu uwierzytelnienia do usługi Resource Manager, przeczytaj [umożliwia dostęp do usługi Resource Manager Linux VM tożsamość usługi zarządzanej (MSI)](../managed-service-identity/msi-tutorial-linux-vm-access-arm.md).
- Dowiedz się więcej o [uwierzytelniania za pomocą programu Data Lake Store przy użyciu usługi Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Dowiedz się więcej o [operacje systemu plików w usługi Azure Data Lake Store, przy użyciu interfejsu API REST](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) lub [interfejsów API systemu plików WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Dowiedz się więcej o [kontroli dostępu w Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.