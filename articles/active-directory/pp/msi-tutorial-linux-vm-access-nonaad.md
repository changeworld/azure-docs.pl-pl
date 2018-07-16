---
title: Umożliwia dostęp do usługi Azure Key Vault Zarządzanej maszyny Wirtualnej systemu Linux
description: Samouczek, który przeprowadzi Cię przez proces dostępu do usługi Azure Resource Manager przy użyciu systemu Linux VM tożsamość usługi zarządzanej (MSI).
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
ms.openlocfilehash: 95f54cf9cda2bf6dede6e7bfb72471b22707c1c2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056076"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Umożliwia dostęp do usługi Azure Key Vault Linux VM tożsamość usługi zarządzanej (MSI) 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ten samouczek pokazuje, jak włączyć tożsamości usługi zarządzanej (MSI) dla maszyny wirtualnej systemu Linux, a następnie użyć tej tożsamości dostępu do usługi Azure Key Vault. Służy jako bootstrap, usługa Key Vault umożliwia aplikacji klienckiej następnie użyj klucza tajnego dostępu do zasobów nie zabezpieczone przez usługi Azure Active Directory (AD). Tożsamości usługi zarządzanej są zarządzane automatycznie przez platformę Azure i umożliwiają uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności Wstaw poświadczeń do kodu. 

Omawiane kwestie:

> [!div class="checklist"]
> * Włączanie tożsamości usługi Zarządzanej na maszynie wirtualnej systemu Linux 
> * Przyznaj dostęp maszyny Wirtualnej do wpisu tajnego przechowywanych w usłudze Key Vault 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu można pobrać klucza tajnego z usługi Key Vault 
 
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
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycję **Wyświetl wszystkie** lub zmień filtr obsługiwany typ dysku. Na stronie ustawień pozostaw wartości domyślne, a następnie kliknij przycisk **OK**.

## <a name="enable-msi-on-your-vm"></a>Włączanie tożsamości usługi Zarządzanej na maszynie Wirtualnej

Z tożsamości usługi Zarządzanej maszyny wirtualnej umożliwia uzyskiwanie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie tożsamości usługi Zarządzanej w sposób niewidoczny wykonuje dwie czynności: instaluje rozszerzenia tożsamości usługi Zarządzanej maszyny Wirtualnej na maszynie Wirtualnej oraz umożliwia tożsamości usługi Zarządzanej dla maszyny Wirtualnej.  

1. Wybierz **maszyny wirtualnej** chcesz włączyć tożsamości usługi Zarządzanej.
2. Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**.
3. Zostanie wyświetlony **tożsamości usługi zarządzanej**. Aby zarejestrować i włączyć plik MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz pozycję nie.
4. Upewnij się, możesz kliknąć pozycję **Zapisz** Aby zapisać konfigurację.

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Jeśli chcesz sprawdzić, jakie rozszerzenia są na tym **maszyny Wirtualnej systemu Linux**, kliknij przycisk **rozszerzenia**. Po włączeniu tożsamości usługi Zarządzanej **ManagedIdentityExtensionforLinux** pojawia się na liście.

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udzielić dostępu do sieci maszyny Wirtualnej przechowywanych w usłudze Key Vault wpisu tajnego  

Za pomocą pliku MSI kodu można uzyskać tokenów dostępu w celu uwierzytelniania w zasobach, które obsługują uwierzytelnianie usługi Azure Active Directory. Jednak nie wszystkie usługi platformy Azure obsługują uwierzytelnianie usługi Azure AD. Aby z tymi usługami przy użyciu pliku MSI, przechowywać poświadczenia usługi w usłudze Azure Key Vault i dostęp do usługi Key Vault można pobrać poświadczeń przy użyciu pliku MSI. 

Najpierw musimy utworzyć usługę Key Vault i Udziel dostępu tożsamości naszej maszyny Wirtualnej do usługi Key Vault.   

1. W górnej części paska nawigacyjnego po lewej stronie wybierz **+ nowy** następnie **bezpieczeństwo i Obsługa tożsamości** następnie **usługi Key Vault**.  
2. Podaj **nazwa** dla nowego magazynu kluczy. 
3. Znajdź usługę Key Vault w tej samej subskrypcji i grupie zasobów co maszyna wirtualna została utworzona wcześniej. 
4. Wybierz **zasady dostępu** i kliknij przycisk **Dodaj nowe**. 
5. Konfiguruj z szablonu, wybierz **zarządzania wpisami tajnymi**. 
6. Wybierz **Wybierz podmiot zabezpieczeń**, a w polu wyszukiwania wprowadź nazwę maszyny wirtualnej została utworzona wcześniej.  Wybierz maszynę Wirtualną na liście wyników, a następnie kliknij przycisk **wybierz**. 
7. Kliknij przycisk **OK** do zakończenia dodawania nowych zasad dostępu i **OK** aby ukończyć wybieranie zasad dostępu. 
8. Kliknij przycisk **Utwórz** aby zakończyć tworzenie usługi Key Vault. 

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Następnie dodaj wpis tajny do usługi Key Vault, tak aby później można pobrać klucza tajnego przy użyciu kodu działającego na maszynie wirtualnej: 

1. Wybierz **wszystkie zasoby**i Znajdź i wybierz utworzony w usłudze Key Vault. 
2. Wybierz **wpisów tajnych**i kliknij przycisk **Dodaj**. 
3. Wybierz **ręczne**, z **opcje przekazywania**. 
4. Wprowadź nazwę i wartość dla klucza tajnego.  Wartość może być coś, czego chcesz. 
5. Pozostaw Data aktywacji i wygaśnięcia wyczyść i pozostawić **włączone** jako **tak**. 
6. Kliknij przycisk **Utwórz** do utworzenia klucza tajnego. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu można pobrać klucza tajnego z usługi Key Vault  

Aby wykonać te kroki, należy klienta SSH.  Jeśli używasz Windows, możesz użyć klienta SSH w [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeśli potrzebujesz pomocy w konfigurowaniu klucze Twój klient SSH, zobacz [jak klucze używanie protokołu SSH z Windows Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), lub [sposobu tworzenia i używania publicznych i prywatnych pary kluczy SSH dla maszyn wirtualnych systemu Linux na platformie Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
1. W portalu, przejdź do maszyny Wirtualnej systemu Linux i w **Przegląd**, kliknij przycisk **Connect**. 
2. **Połącz** do maszyny Wirtualnej przy użyciu klienta SSH wybranych przez użytkownika. 
3. W oknie terminalu przy użyciu programu CURL, wysłać żądanie do lokalnego punktu końcowego pliku MSI do uzyskania tokenu dostępu usługi Azure Key Vault.  
 
    Żądanie programu CURL dla tokenu dostępu znajduje się poniżej.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    Odpowiedź zawiera token dostępu, musisz mieć dostęp do usługi Resource Manager. 
    
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
    
    Ten token dostępu służy do uwierzytelniania w usłudze Azure Key Vault.  Kolejne żądanie CURL przedstawiono odczytu wpisu tajnego z usługi Key Vault przy użyciu programu CURL i interfejsu API REST usługi Key Vault.  Będziesz potrzebować adresu URL usługi Key Vault, który znajduje się w **Essentials** części **Przegląd** strony usługi Key Vault.  Należy również token dostępu uzyskany na poprzednie wywołanie. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpowiedź będzie wyglądać następująco: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Gdy wpis tajny zostały pobrane z usługi Key Vault, służy do uwierzytelniania to usługa, która wymaga nazwy i hasła.


## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](msi-overview.md).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.




