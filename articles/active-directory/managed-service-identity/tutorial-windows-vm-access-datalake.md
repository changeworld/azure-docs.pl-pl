---
title: Jak używać systemu Windows maszyny Wirtualnej zarządzane usługi tożsamości (MSI) można uzyskać dostępu do usługi Azure Data Lake Store
description: Samouczek przedstawiający sposób użycia systemu Windows maszyny Wirtualnej zarządzane usługi tożsamości (MSI) można uzyskać dostępu do usługi Azure Data Lake Store.
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
ms.openlocfilehash: 31afe8579580ab392411aa8428f023fd52c4c4c6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595295"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Samouczek: Dostęp do usługi Azure Data Lake Store za pomocą systemu Windows maszyny Wirtualnej zarządzane usługi tożsamości (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek pokazuje, jak używać zarządzanego tożsamości usługi (MSI) dla systemu Windows maszyny wirtualnej (VM) można uzyskać dostępu do usługi Azure Data Lake Store. Zarządzane tożsamości usługi automatycznie są zarządzane przez usługę Azure i umożliwiają uwierzytelniania w usłudze, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności wstawić poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Włącz MSI na Windows maszyny Wirtualnej 
> * Udziel dostępu maszyny Wirtualnej do usługi Azure Data Lake Store
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i umożliwia dostęp do usługi Azure Data Lake Store

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Windows w nowej grupy zasobów

W tym samouczku utworzymy nową maszynę Wirtualną systemu Windows.  Można również włączyć MSI na istniejącej maszyny Wirtualnej.

1. Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3. Wprowadź informacje o maszynie wirtualnej. **Username** i **hasło** utworzony, w tym miejscu jest poświadczeń umożliwia logowanie do maszyny wirtualnej.
4. Wybierz odpowiednią **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5. Aby wybrać nowy **grupy zasobów** w której chcesz utworzyć maszynę wirtualną, wybrać **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. Na stronie ustawienia Zachowaj wartości domyślne, a następnie kliknij przycisk **OK**.

   ![Tekst alternatywny obrazu](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włącz MSI na maszynie Wirtualnej 

MSI maszyny Wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie MSI informuje Azure w celu utworzenia tożsamości zarządzanego dla maszyny Wirtualnej. W obszarze obejmuje włączenie MSI wykonuje dwie czynności: rejestrów maszyny Wirtualnej z usługi Azure Active Directory do utworzenia zarządzanej tożsamości, a konfiguruje tożsamości na maszynie Wirtualnej.

1. Wybierz **maszyny wirtualnej** chcesz włączyć MSI.  
2. Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**. 
3. Zostanie wyświetlony **zarządzane tożsamość usługi**. Aby zarejestrować i włączyć MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz opcję nie. 
4. Upewnij się, możesz kliknąć przycisk **zapisać** Aby zapisać konfigurację.  
   ![Tekst alternatywny obrazu](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Jeśli chcesz sprawdzić i sprawdź, które rozszerzenia są na tej maszynie Wirtualnej, kliknij przycisk **rozszerzenia**. Jeśli MSI jest włączona, następnie **ManagedIdentityExtensionforWindows** pojawią się na liście.

   ![Tekst alternatywny obrazu](../media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Udziel dostępu maszyny Wirtualnej do usługi Azure Data Lake Store

Teraz można przyznać dostęp do plików i folderów w usłudze Azure Data Lake — magazyn maszyny Wirtualnej.  Dla tego kroku można użyć istniejącej usługi Data Lake Store lub Utwórz nową.  Aby utworzyć nowy Data Lake Store przy użyciu portalu Azure, wykonaj to [szybkiego startu usługi Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Dostępne są także Przewodniki Szybki Start, którego wiersza polecenia platformy Azure i programu Azure PowerShell w programie [dokumentacji usługi Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

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

Azure Data Lake Store natywnie obsługuje usługi Azure AD uwierzytelnianie, więc bezpośrednio może akceptować tokeny dostępu uzyskany przy użyciu Instalatora MSI.  Do uwierzytelniania w systemie plików usługi Data Lake Store możesz wysłać token dostępu wystawiony przez usługę Azure AD dla usługi Data Lake Store filesystem punktu końcowego, w nagłówku autoryzacji w formacie "Bearer < ACCESS_TOKEN_VALUE >".  Aby dowiedzieć się więcej na temat usługi Data Lake Store obsługę uwierzytelniania usługi Azure AD, przeczytaj [uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)

> [!NOTE]
> Klient systemu plików usługi Data Lake Store zestawów SDK nie jest jeszcze obsługiwany zarządzane tożsamości usługi.  W tym samouczku zostaną zaktualizowane po dodaniu obsługi do zestawu SDK.

W tym samouczku uwierzytelniania w systemie plików usługi Data Lake Store żądań interfejsu API REST wprowadzać REST przy użyciu programu PowerShell. Aby MSI maszyny Wirtualnej jest używany do uwierzytelniania, należy utworzyć żądania z maszyny Wirtualnej.

1. W portalu, przejdź do **maszyn wirtualnych**, przejdź do maszyny Wirtualnej systemu Windows, a w **omówienie** kliknij **Connect**.
2. Wprowadź w Twojej **Username** i **hasło** dla którego zostanie dodane po utworzeniu maszyny Wirtualnej systemu Windows. 
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, otwórz **PowerShell** w sesji zdalnej. 
4. Przy użyciu programu PowerShell w `Invoke-WebRequest`, Wyślij żądanie do lokalnego punktu końcowego MSI do Uzyskaj token dostępu dla usługi Azure Data Lake Store.  Identyfikator zasobu usługi Data Lake Store jest "https://datalake.azure.net/".  Data Lake nie dokładnego dopasowania na podstawie identyfikatora zasobu i ważne jest wiodący ukośnik.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Konwertuj odpowiedzi z obiektu JSON na obiekt programu PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Token dostępu wyodrębnienie z odpowiedzi.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Przy użyciu programu PowerShell w "Invoke-WebRequest", Wyślij żądanie do punktu końcowego REST usługi Data Lake Store w, aby wyświetlić listę folderów w folderze głównym.  Jest to prosty sposób sprawdzić, czy wszystko jest poprawnie skonfigurowana.  Jest ważne, czy ciągu "Bearer" w nagłówku autoryzacji ma duże "B".  Można znaleźć nazwy usługi Data Lake Store w **omówienie** części bloku Data Lake Store w portalu Azure.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Odpowiedź oznaczająca Powodzenie wygląda następująco:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Teraz możesz spróbować przekazywanie pliku do usługi Data Lake Store.  Najpierw należy utworzyć plik do przekazania.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Przy użyciu programu PowerShell w `Invoke-WebRequest`, Wyślij żądanie do punktu końcowego REST usługi Data Lake Store w można przekazać pliku do folderu utworzonego wcześniej.  To żądanie przyjmuje dwa kroki.  W pierwszym kroku możesz utworzyć żądanie i uzyskać przekierowanie, do której można przekazać pliku.  W drugim kroku faktycznie przekazać plik.  Pamiętaj, aby ustawić nazwę folderu i pliku odpowiednio, jeśli używasz innej wartości niż w tym samouczku. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Jeśli sprawdzenie wartości `$HdfsRedirectResponse` powinna wyglądać następującą odpowiedź:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Zakończyć przekazywanie, wysyłając żądanie do punktu końcowego przekierowania:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Wygląd pomyślnej odpowiedzi, takich jak:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Przy użyciu systemu plików inne usługi Data Lake Store interfejsów API, możesz dołączyć do plików, pobierania plików i inne.

Gratulacje!  Uwierzytelniono systemie plików usługi Data Lake Store za pomocą MSI maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób korzystania z tożsamości usługi zarządzania dla maszyny wirtualnej systemu Windows można uzyskać dostępu do usługi Azure Data Lake Store. Aby dowiedzieć się więcej o usłudze Azure Data Lake Store, zobacz:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
