---
title: Umożliwia dostęp do usługi Azure Key Vault MSI maszyny Wirtualnej systemu Linux
description: Samouczek, który przeprowadzi Cię przez proces przy użyciu systemu Linux VM zarządzane usługi tożsamości (MSI) można uzyskać dostępu do usługi Azure Resource Manager.
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e42ed14b9c7c89972021a5422ebdfe415a70830d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29384705"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Umożliwia dostęp do usługi Azure Key Vault Linux VM zarządzane usługi tożsamości (MSI) 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

W tym samouczku przedstawiono sposób włączenia zarządzane tożsamości usługi (MSI) dla maszyny wirtualnej systemu Linux, a następnie użyj tej tożsamości można uzyskać dostępu do usługi Azure Key Vault. Służy jako bootstrap, magazyn kluczy umożliwia aplikacji klienta można następnie użyć klucza tajnego dostępu do zasobów nie zabezpieczone przez usługi Azure Active Directory (AD). Zarządzane tożsamości usługi automatycznie są zarządzane przez usługę Azure i umożliwiają uwierzytelniania w usłudze, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności wstawić poświadczeń do kodu. 

Omawiane kwestie:

> [!div class="checklist"]
> * Włącz MSI w maszynie wirtualnej systemu Linux 
> * Przyznać uprawnienia maszyny Wirtualnej do klucza tajnego przechowywane w magazynie kluczy 
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu można pobrać klucza tajnego z magazynu kluczy 
 
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
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiary, wybierz **Wyświetl wszystkie** lub zmień filtr typu dysku obsługiwane. Na stronie ustawienia Zachowaj wartości domyślne, a następnie kliknij przycisk **OK**.

## <a name="enable-msi-on-your-vm"></a>Włącz MSI na maszynie Wirtualnej

MSI maszyny wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. W obszarze obejmuje włączenie MSI wykonuje dwie czynności: instalacji rozszerzenia maszyny Wirtualnej MSI w maszynie Wirtualnej i umożliwia korzystanie z pliku MSI dla maszyny Wirtualnej.  

1. Wybierz **maszyny wirtualnej** chcesz włączyć MSI.
2. Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**.
3. Zostanie wyświetlony **zarządzane tożsamość usługi**. Aby zarejestrować i włączyć MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz opcję nie.
4. Upewnij się, możesz kliknąć przycisk **zapisać** Aby zapisać konfigurację.

    ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Jeśli chcesz sprawdzić, które rozszerzenia są na tym **maszyny Wirtualnej systemu Linux**, kliknij przycisk **rozszerzenia**. Po włączeniu MSI **ManagedIdentityExtensionforLinux** pojawia się na liście.

    ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Przyznać uprawnienia maszyny Wirtualnej na kluczu tajnym przechowywane w magazynie kluczy  

Za pomocą Instalatora MSI kodu można uzyskać tokenów dostępu do uwierzytelniania do zasobów, które obsługują uwierzytelnianie usługi Azure Active Directory. Jednak nie wszystkie usługi Azure obsługuje uwierzytelniania usługi Azure AD. Aby użyć MSI z tych usług, przechowywane poświadczenia usługi w usłudze Azure Key Vault i użyj MSI Key Vault w celu pobrania poświadczenia dostępu do. 

Najpierw należy utworzyć magazyn kluczy i udzielanie dostępu tożsamości naszych maszyny Wirtualnej w magazynie kluczy.   

1. W górnej części na pasku nawigacyjnym po lewej stronie wybierz **+ nowy** następnie **bezpieczeństwo i Obsługa tożsamości** następnie **Key Vault**.  
2. Podaj **nazwa** dla nowego magazynu kluczy. 
3. Zlokalizuj Key Vault w tej samej grupie subskrypcji i zasobu jako maszyny Wirtualnej utworzone wcześniej. 
4. Wybierz **zasady dostępu** i kliknij przycisk **Dodaj nowe**. 
5. W konfiguracji z szablonu, wybierz **zarządzania klucz tajny**. 
6. Wybierz **wybierz główny**, a w polu wyszukiwania wprowadź nazwę maszyny wirtualnej utworzone wcześniej.  Wybierz maszynę Wirtualną na liście wyników, a następnie kliknij przycisk **wybierz**. 
7. Kliknij przycisk **OK** do zakończenia dodawania nowych zasad dostępu i **OK** aby ukończyć wybieranie zasad dostępu. 
8. Kliknij przycisk **Utwórz** aby zakończyć tworzenie magazynu kluczy. 

    ![Tekst alternatywny obrazu](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Następnie dodaj klucz tajny w magazynie kluczy, tak aby później można pobrać klucza tajnego za pomocą kodu uruchamianego w środowisku maszyny Wirtualnej: 

1. Wybierz **wszystkie zasoby**, Znajdź i wybierz utworzony magazyn kluczy. 
2. Wybierz **kluczy tajnych**i kliknij przycisk **Dodaj**. 
3. Wybierz **ręcznego**, z **opcje przekazywania**. 
4. Wprowadź nazwę i wartość klucza tajnego.  Wartość może być dowolny. 
5. Pozostaw Data aktywacji i wyczyść Data wygaśnięcia i pozostawić **włączone** jako **tak**. 
6. Kliknij przycisk **Utwórz** można utworzyć klucza tajnego. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu można pobrać klucza tajnego z magazynu kluczy  

Aby wykonać te kroki, należy klient SSH.  Jeśli korzystasz z systemu Windows, możesz użyć klienta SSH w [podsystemu systemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeśli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [sposobu użycia SSH kluczy systemu Windows Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), lub [sposobu tworzenia i używania SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux na platformie Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
1. W portalu przejdź do maszyny Wirtualnej systemu Linux i w **omówienie**, kliknij przycisk **Connect**. 
2. **Połącz** do maszyny Wirtualnej przy użyciu klienta SSH wybranych przez użytkownika. 
3. W oknie terminalu przy użyciu programu CURL, Wyślij żądanie do lokalnego punktu końcowego MSI do Uzyskaj token dostępu dla usługi Azure Key Vault.  
 
    ZWINIĘCIE żądanie tokenu dostępu jest niższa.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    Odpowiedź zawiera token dostępu, należy otworzyć Menedżera zasobów. 
    
    Odpowiedź:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Ten token dostępu służy do uwierzytelniania usługi Azure Key Vault.  Przy następnym żądaniu CURL pokazuje, jak można odczytać klucza tajnego z magazynu kluczy przy użyciu CURL i interfejsu API REST magazynu kluczy.  Będziesz potrzebować adres URL magazynu kluczy, który znajduje się w **Essentials** sekcji **omówienie** strony magazynu kluczy.  Należy również uzyskanego w poprzednim wywołaniu tokenu dostępu. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpowiedź będzie wyglądać następująco: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Po klucz tajny zostały pobrane z magazynu kluczy, służy ona do uwierzytelniania do usługi, która wymaga podania nazwy i hasła.


## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](msi-overview.md).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.




