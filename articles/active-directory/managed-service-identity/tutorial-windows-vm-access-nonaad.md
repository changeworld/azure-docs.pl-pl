---
title: Uzyskiwanie dostępu do usługi Azure Key Vault przy użyciu tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows
description: Samouczek przedstawiający proces użycia tożsamości usługi zarządzanej (MSI) na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Key Vault.
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
ms.openlocfilehash: 81509108060b636e47154a8c375f5569cac73648
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902738"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Samouczek: używanie tożsamości usługi zarządzanej (MSI) na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Key Vault 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek przedstawia sposób włączania tożsamości usługi zarządzanej (MSI) dla maszyny wirtualnej z systemem Windows, a następnie użycia tej tożsamości do uzyskania dostępu do usługi Azure Key Vault. Usługa Key Vault, używana przy uruchamianiu, umożliwia aplikacji klienckiej użycie wpisu tajnego do uzyskania dostępu do zasobów, które nie są zabezpieczane przez usługę Azure Active Directory (AD). Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. 

Omawiane kwestie:


> [!div class="checklist"]
> * Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows 
> * Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Tworzenie maszyny wirtualnej z systemem Windows w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę wirtualną z systemem Windows. Możesz też włączyć tożsamość usługi zarządzanej na istniejącej maszynie wirtualnej.

1.  Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Nazwa użytkownika** i **Hasło** utworzone w tym miejscu są poświadczeniami używanymi do logowania do maszyny wirtualnej.
4.  Wybierz odpowiednią **Subskrypcję** dla maszyny wirtualnej z listy rozwijanej.
5.  Aby wybrać nową **grupę zasobów**, w której chcesz utworzyć maszynę wirtualną, wybierz opcję **Utwórz nową**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. W bloku ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

    ![Alternatywny tekst obrazu](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej 

Tożsamość usługi zarządzanej maszyny wirtualnej umożliwia uzyskanie tokenów dostępu z usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. Włączenie tożsamości usługi zarządzanej powiadamia platformę Azure o potrzebie utworzenia tożsamości zarządzanej dla maszyny wirtualnej. Włączenie tożsamości usługi zarządzanej na maszynie wirtualnej powoduje wykonanie dwóch niejawnych czynności: zapewnia rejestrację maszyny wirtualnej w usłudze Azure Active Directory, aby utworzyć tożsamość zarządzaną, oraz skonfigurowanie tożsamości na maszynie wirtualnej.

1.  Wybierz **maszynę wirtualną**, dla której chcesz włączyć tożsamość usługi zarządzanej.  
2.  Na lewym pasku nawigacyjnym kliknij opcję **Konfiguracja**. 
3.  Zobaczysz ekran **Tożsamość usługi zarządzanej**. Aby zarejestrować i włączyć tożsamość usługi zarządzanej, wybierz opcję **Tak**. Jeśli chcesz ją wyłączyć, wybierz opcję Nie. 
4.  Pamiętaj, aby kliknąć przycisk **Zapisz** w celu zapisania konfiguracji.  

    ![Alternatywny tekst obrazu](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault 
 
Przy użyciu tożsamości usługi zarządzanej kod może uzyskać tokeny dostępu, aby uwierzytelniać zasoby obsługujące uwierzytelnianie usługi Azure AD.  Jednak nie wszystkie usługi platformy Azure obsługują uwierzytelnianie usługi Azure AD. Aby użyć tożsamości usługi zarządzanej z tymi usługami, zapisz poświadczenia usługi w usłudze Azure Key Vault, a następnie użyj tożsamości usługi zarządzanej, aby uzyskać dostęp do usługi Key Vault i pobrać te poświadczenia. 

Najpierw musimy utworzyć usługę Key Vault i udzielić tożsamości naszej maszyny wirtualnej dostępu do usługi Key Vault.   

1. W górnej części lewego paska nawigacyjnego wybierz opcję **Utwórz zasób** > **Bezpieczeństwo i obsługa tożsamości** > **Key Vault**.  
2. Podaj **Nazwę** dla nowej usługi Key Vault. 
3. Znajdź usługę Key Vault w tej samej subskrypcji i grupie zasobów co wcześniej utworzona maszyna wirtualna. 
4. Wybierz opcję **Zasady dostępu** i kliknij opcję **Dodaj nową**. 
5. W pozycji Konfiguruj na podstawie szablonu wybierz opcję **Zarządzanie wpisami tajnymi**. 
6. Wybierz opcję **Wybierz podmiot zabezpieczeń**, a następnie w polu wyszukiwania wprowadź nazwę wcześniej utworzonej maszyny wirtualnej.  Wybierz maszynę wirtualną z listy wyników i kliknij opcję **Wybierz**. 
7. Kliknij przycisk **OK**, aby zakończyć dodawanie nowych zasad dostępu, a następnie kliknij przycisk **OK**, aby zakończyć wybór zasad dostępu. 
8. Kliknij przycisk **Utwórz**, aby zakończyć tworzenie usługi Key Vault. 

    ![Alternatywny tekst obrazu](../media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Następnie dodaj wpis tajny do usługi Key Vault, aby umożliwić późniejsze pobranie wpisu tajnego przy użyciu kodu uruchomionego na maszynie wirtualnej: 

1. Wybierz opcję **Wszystkie zasoby**, a następnie znajdź i wybierz utworzoną usługę Key Vault. 
2. Wybierz opcję **Wpisy tajne** i kliknij opcję **Dodaj**. 
3. Wybierz opcję **Ręczne** z pozycji **Opcje przekazywania**. 
4. Wprowadź nazwę i wartość wpisu tajnego.  Wartość może być dowolna. 
5. Pozostaw pustą datę aktywacji i datę wygaśnięcia oraz zostaw opcję **Włączone** ustawioną na wartość **Tak**. 
6. Kliknij pozycję **Utwórz**, aby utworzyć wpis tajny. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault  

Jeśli nie masz zainstalowanego programu PowerShell 4.3.1 (lub nowszej wersji), musisz [pobrać i zainstalować najnowszą wersję](https://docs.microsoft.com/powershell/azure/overview).

Najpierw użyjemy tożsamości usługi zarządzanej maszyny wirtualnej, aby uzyskać token dostępu i przeprowadzić uwierzytelnianie w usłudze Key Vault:
 
1. W portalu przejdź do pozycji **Maszyny wirtualne**, a następnie przejdź do swojej maszyny wirtualnej z systemem Windows i w pozycji **Przegląd** kliknij przycisk **Połącz**.
2. Wprowadź **nazwę użytkownika** i **hasło** dodane podczas tworzenia **maszyny wirtualnej z systemem Windows**.  
3. Teraz, po utworzeniu **połączenia pulpitu zdalnego** z maszyną wirtualną, otwórz program PowerShell w sesji zdalnej.  
4. W programie PowerShell wywołaj żądanie internetowe w dzierżawie, aby uzyskać token dla hosta lokalnego w konkretnym porcie dla maszyny wirtualnej.  

    Żądanie programu PowerShell:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Następnie wyodrębnij pełną odpowiedź, która jest przechowywana w ciągu w formacie JavaScript Object Notation (JSON) w obiekcie $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Następnie wyodrębnij token dostępu z odpowiedzi.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Na koniec użyj polecenia Invoke-WebRequest programu PowerShell, aby pobrać wpis tajny utworzony wcześniej w usłudze Key Vault, przekazując token dostępu w nagłówku autoryzacji.  Będziesz potrzebować adresu URL usługi Key Vault, który znajduje się w sekcji **Podstawowe elementy** na stronie **Przegląd** usługi Key Vault.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Odpowiedź będzie wyglądać następująco: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Po pobraniu wpisu tajnego z usługi Key Vault możesz użyć go do uwierzytelnienia w usłudze wymagającej nazwy i hasła. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia tożsamości usługi zarządzanej w celu uzyskania dostępu do serwera usługi Azure Key Vault.  Dowiedz się więcej o usłudze Azure Key Vault:

> [!div class="nextstepaction"]
>[Usługa Azure Key Vault](/azure/key-vault/key-vault-whatis)
