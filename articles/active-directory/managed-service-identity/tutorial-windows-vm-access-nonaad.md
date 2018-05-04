---
title: Umożliwia dostęp do usługi Azure Key Vault MSI maszyny Wirtualnej systemu Windows
description: Samouczek, który przeprowadzi Cię przez proces przy użyciu systemu Windows maszyny Wirtualnej zarządzane usługi tożsamości (MSI) można uzyskać dostępu do usługi Azure Key Vault.
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: c65e2dc3d1b7a754bda54bb9127bbc777b514768
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Umożliwia dostęp do usługi Azure Key Vault Windows wirtualna zarządzane usługi tożsamości (MSI) 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek pokazuje, jak włączyć zarządzane tożsamości usługi (MSI) dla maszyny wirtualnej systemu Windows, a następnie użyj tej tożsamości można uzyskać dostępu do usługi Azure Key Vault. Służy jako bootstrap, magazyn kluczy umożliwia aplikacji klienta można następnie użyć klucza tajnego dostępu do zasobów nie zabezpieczone przez usługi Azure Active Directory (AD). Zarządzane tożsamości usługi automatycznie są zarządzane przez usługę Azure i umożliwiają uwierzytelniania w usłudze, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności wstawić poświadczeń do kodu. 

Omawiane kwestie:


> [!div class="checklist"]
> * Włącz tożsamość usługi zarządzanej na maszynie wirtualnej systemu Windows 
> * Przyznać uprawnienia maszyny Wirtualnej do klucza tajnego przechowywane w magazynie kluczy 
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu można pobrać klucza tajnego z magazynu kluczy 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Windows w nowej grupy zasobów

W tym samouczku utworzymy nową maszynę Wirtualną systemu Windows. Można również włączyć MSI na istniejącej maszyny Wirtualnej.

1.  Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Username** i **hasło** utworzony, w tym miejscu jest poświadczeń umożliwia logowanie do maszyny wirtualnej.
4.  Wybierz odpowiednią **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5.  Aby wybrać nowy **grupy zasobów** chcesz maszynę wirtualną można utworzyć w, wybierz **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

    ![Tekst alternatywny obrazu](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włącz MSI na maszynie Wirtualnej 

MSI maszyny wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie MSI informuje Azure w celu utworzenia tożsamości zarządzanego dla maszyny wirtualnej. W obszarze obejmuje włączenie MSI wykonuje dwie czynności: rejestruje maszyny Wirtualnej w usłudze Azure Active Directory w celu utworzenia tożsamości zarządzanych i konfiguruje tożsamości na maszynie Wirtualnej.

1.  Wybierz **maszyny wirtualnej** chcesz włączyć MSI.  
2.  Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**. 
3.  Zostanie wyświetlony **zarządzane tożsamość usługi**. Aby zarejestrować i włączyć MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz opcję nie. 
4.  Upewnij się, możesz kliknąć przycisk **zapisać** Aby zapisać konfigurację.  

    ![Tekst alternatywny obrazu](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Przyznać uprawnienia maszyny Wirtualnej na kluczu tajnym przechowywane w magazynie kluczy 
 
Za pomocą Instalatora MSI kodu mogą uzyskiwać tokeny dostępu do uwierzytelniania do zasobów, które obsługują uwierzytelnianie usługi Azure AD.  Jednak nie wszystkie usługi Azure obsługuje uwierzytelniania usługi Azure AD. Aby użyć MSI z tych usług, przechowywane poświadczenia usługi w usłudze Azure Key Vault i użyj MSI Key Vault w celu pobrania poświadczenia dostępu do. 

Najpierw należy utworzyć magazyn kluczy i udzielanie dostępu tożsamości naszych maszyny Wirtualnej w magazynie kluczy.   

1. U góry na lewym pasku nawigacyjnym, zaznacz pole wyboru **Utwórz zasób** > **bezpieczeństwo i Obsługa tożsamości** > **Key Vault**.  
2. Podaj **nazwa** dla nowego magazynu kluczy. 
3. Zlokalizuj Key Vault w tej samej grupie subskrypcji i zasobu jako maszyny Wirtualnej utworzone wcześniej. 
4. Wybierz **zasady dostępu** i kliknij przycisk **Dodaj nowe**. 
5. W konfiguracji z szablonu, wybierz **zarządzania klucz tajny**. 
6. Wybierz **wybierz główny**, a w polu wyszukiwania wprowadź nazwę maszyny wirtualnej utworzone wcześniej.  Wybierz maszynę Wirtualną na liście wyników, a następnie kliknij przycisk **wybierz**. 
7. Kliknij przycisk **OK** do zakończenia dodawania nowych zasad dostępu i **OK** aby ukończyć wybieranie zasad dostępu. 
8. Kliknij przycisk **Utwórz** aby zakończyć tworzenie magazynu kluczy. 

    ![Tekst alternatywny obrazu](../media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Następnie dodaj klucz tajny w magazynie kluczy, tak aby później można pobrać klucza tajnego za pomocą kodu uruchamianego w środowisku maszyny Wirtualnej: 

1. Wybierz **wszystkie zasoby**, Znajdź i wybierz utworzony magazyn kluczy. 
2. Wybierz **kluczy tajnych**i kliknij przycisk **Dodaj**. 
3. Wybierz **ręcznego**, z **opcje przekazywania**. 
4. Wprowadź nazwę i wartość klucza tajnego.  Wartość może być dowolny. 
5. Pozostaw Data aktywacji i wyczyść Data wygaśnięcia i pozostawić **włączone** jako **tak**. 
6. Kliknij przycisk **Utwórz** można utworzyć klucza tajnego. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu można pobrać klucza tajnego z magazynu kluczy  

Jeśli nie masz programu PowerShell 4.3.1 lub nowsza wersja, konieczne będzie [Pobierz i zainstaluj najnowszą wersję](https://docs.microsoft.com/powershell/azure/overview).

Po pierwsze stosujemy MSI maszyny Wirtualnej do uzyskania tokenu dostępu do uwierzytelniania usługi Key Vault:
 
1. W portalu, przejdź do **maszyn wirtualnych** i przejdź do maszyny wirtualnej systemu Windows i w **omówienie**, kliknij przycisk **Connect**.
2. Wprowadź w Twojej **Username** i **hasło** dla którego zostanie dodane po utworzeniu **maszyny Wirtualnej systemu Windows**.  
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program PowerShell w sesji zdalnej.  
4. W programie PowerShell Wywołaj dla dzierżawcy żądania sieci web, aby uzyskać token dla hosta lokalnego w określonym porcie dla maszyny Wirtualnej.  

    Żądanie programu PowerShell:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Następnie Wyodrębnij całą odpowiedź, który jest przechowywany jako ciąg w formacie JavaScript Object Notation (JSON) w obiekcie $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Następnie Wyodrębnij token dostępu z odpowiedzi.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Na koniec użyj polecenia Invoke-WebRequest w programu PowerShell, można pobrać klucza tajnego utworzonego wcześniej w magazynie kluczy, przekazywanie tokenu dostępu w nagłówku autoryzacji.  Będziesz potrzebować adres URL magazynu kluczy, który znajduje się w **Essentials** sekcji **omówienie** strony magazynu kluczy.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Odpowiedź będzie wyglądać następująco: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Po klucz tajny zostały pobrane z magazynu kluczy, służy ona do uwierzytelniania do usługi, która wymaga podania nazwy i hasła. 

## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](overview.md).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
