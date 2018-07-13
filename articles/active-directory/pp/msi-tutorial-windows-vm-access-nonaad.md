---
title: Umożliwia dostęp do usługi Azure Key Vault Windows Zarządzanej maszyny Wirtualnej
description: Samouczek, który przeprowadzi Cię przez proces dostępu do usługi Azure Key Vault przy użyciu Windows VM tożsamość usługi zarządzanej (MSI).
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
ms.openlocfilehash: b3d334edd770ac381a7e0ae6aaa1a9db8c91b961
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002943"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Umożliwia dostęp do usługi Azure Key Vault Windows VM tożsamość usługi zarządzanej (MSI) 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ten samouczek pokazuje, jak włączyć tożsamości usługi zarządzanej (MSI) na maszynę wirtualną Windows, a następnie użyć tej tożsamości dostępu do usługi Azure Key Vault. Służy jako bootstrap, usługa Key Vault umożliwia aplikacji klienckiej następnie użyj klucza tajnego dostępu do zasobów nie zabezpieczone przez usługi Azure Active Directory (AD). Tożsamości usługi zarządzanej są zarządzane automatycznie przez platformę Azure i umożliwiają uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności Wstaw poświadczeń do kodu. 

Omawiane kwestie:


> [!div class="checklist"]
> * Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej Windows 
> * Przyznaj dostęp maszyny Wirtualnej do wpisu tajnego przechowywanych w usłudze Key Vault 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu można pobrać klucza tajnego z usługi Key Vault 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną Windows w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę Wirtualną Windows. Można również włączyć tożsamości usługi Zarządzanej istniejącej maszyny wirtualnej.

1.  Kliknij przycisk **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Username** i **hasło** utworzony, w tym miejscu jest poświadczenia, których używasz do logowania do maszyny wirtualnej.
4.  Wybierz odpowiednią **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5.  Aby wybrać nowy **grupy zasobów** chcesz maszyny wirtualnej pod kątem można tworzyć w **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włączanie tożsamości usługi Zarządzanej na maszynie Wirtualnej 

Z tożsamości usługi Zarządzanej maszyny wirtualnej umożliwia uzyskiwanie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie tożsamości usługi Zarządzanej informuje Azure w celu utworzenia tożsamości zarządzanej dla maszyny wirtualnej. Włączanie tożsamości usługi Zarządzanej w sposób niewidoczny wykonuje dwie czynności: instaluje rozszerzenia tożsamości usługi Zarządzanej maszyny Wirtualnej na maszynie Wirtualnej oraz umożliwia tożsamości usługi Zarządzanej w usłudze Azure Resource Manager.

1.  Wybierz **maszyny wirtualnej** chcesz włączyć tożsamości usługi Zarządzanej.  
2.  Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**. 
3.  Zostanie wyświetlony **tożsamości usługi zarządzanej**. Aby zarejestrować i włączyć plik MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz pozycję nie. 
4.  Upewnij się, możesz kliknąć pozycję **Zapisz** Aby zapisać konfigurację.  

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Jeśli użytkownik chce Sprawdź i upewnij się, jakie rozszerzenia są na tej maszynie Wirtualnej, kliknij przycisk **rozszerzenia**. Jeśli plik MSI jest włączona, następnie **ManagedIdentityExtensionforWindows** pojawia się na liście.

    ![Tekst ALT obrazu](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udzielić dostępu do sieci maszyny Wirtualnej przechowywanych w usłudze Key Vault wpisu tajnego 
 
Za pomocą pliku MSI kodu można uzyskać tokenów dostępu w celu uwierzytelniania w zasobach, które obsługują uwierzytelnianie usługi Azure AD.  Jednak nie wszystkie usługi platformy Azure obsługują uwierzytelnianie usługi Azure AD. Aby z tymi usługami przy użyciu pliku MSI, przechowywać poświadczenia usługi w usłudze Azure Key Vault i dostęp do usługi Key Vault można pobrać poświadczeń przy użyciu pliku MSI. 

Najpierw musimy utworzyć usługę Key Vault i Udziel dostępu tożsamości naszej maszyny Wirtualnej do usługi Key Vault.   

1. W górnej części paska nawigacyjnego po lewej stronie wybierz **+ nowy** następnie **bezpieczeństwo i Obsługa tożsamości** następnie **usługi Key Vault**.  
2. Podaj **nazwa** dla nowego magazynu kluczy. 
3. Znajdź usługę Key Vault w tej samej subskrypcji i grupie zasobów co maszyna wirtualna została utworzona wcześniej. 
4. Wybierz **zasady dostępu** i kliknij przycisk **Dodaj nowe**. 
5. Konfiguruj z szablonu, wybierz **zarządzania wpisami tajnymi**. 
6. Wybierz **Wybierz podmiot zabezpieczeń**, a w polu wyszukiwania wprowadź nazwę maszyny wirtualnej została utworzona wcześniej.  Wybierz maszynę Wirtualną na liście wyników, a następnie kliknij przycisk **wybierz**. 
7. Kliknij przycisk **OK** do zakończenia dodawania nowych zasad dostępu i **OK** aby ukończyć wybieranie zasad dostępu. 
8. Kliknij przycisk **Utwórz** aby zakończyć tworzenie usługi Key Vault. 

    ![Tekst ALT obrazu](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Następnie dodaj wpis tajny do usługi Key Vault, tak aby później można pobrać klucza tajnego przy użyciu kodu działającego na maszynie wirtualnej: 

1. Wybierz **wszystkie zasoby**i Znajdź i wybierz utworzony w usłudze Key Vault. 
2. Wybierz **wpisów tajnych**i kliknij przycisk **Dodaj**. 
3. Wybierz **ręczne**, z **opcje przekazywania**. 
4. Wprowadź nazwę i wartość dla klucza tajnego.  Wartość może być coś, czego chcesz. 
5. Pozostaw Data aktywacji i wygaśnięcia wyczyść i pozostawić **włączone** jako **tak**. 
6. Kliknij przycisk **Utwórz** do utworzenia klucza tajnego. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny Wirtualnej i przy jego użyciu można pobrać klucza tajnego z usługi Key Vault  

Jeśli nie masz programu PowerShell 4.3.1 lub większą, konieczne będzie [Pobierz i zainstaluj najnowszą wersję](https://docs.microsoft.com/powershell/azure/overview).

Po pierwsze używamy tożsamości usługi Zarządzanej maszyny Wirtualnej do uzyskania tokenu dostępu do uwierzytelniania w usłudze Key Vault:
 
1. W portalu, przejdź do **maszyn wirtualnych** i przejdź do maszyny wirtualnej Windows w **Przegląd**, kliknij przycisk **Connect**.
2. Wprowadź w Twojej **Username** i **hasło** dla którego można dodać podczas tworzenia **Windows VM**.  
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program PowerShell w sesji zdalnej.  
4. W programie PowerShell należy wywołać żądania sieci web w ramach dzierżawy, aby uzyskać token dla hosta lokalnego w określonym porcie dla maszyny Wirtualnej.  

    Żądanie programu PowerShell:
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    Następnie Wyodrębnij pełnej odpowiedzi, który jest przechowywany jako ciąg w formacie JavaScript Object Notation (JSON) w obiekcie $response.  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    Następnie Wyodrębnij token dostępu z odpowiedzi.  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    Na koniec użyj polecenia Invoke-WebRequest programu PowerShell, aby pobrać klucz tajny, który został utworzony we wcześniejszej części usługi Key Vault, przekazywanie tokenu dostępu w nagłówku autoryzacji.  Będziesz potrzebować adresu URL usługi Key Vault, który znajduje się w **Essentials** części **Przegląd** strony usługi Key Vault.  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Odpowiedź będzie wyglądać następująco: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Gdy wpis tajny zostały pobrane z usługi Key Vault, służy do uwierzytelniania to usługa, która wymaga nazwy i hasła. 

## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](msi-overview.md).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.
