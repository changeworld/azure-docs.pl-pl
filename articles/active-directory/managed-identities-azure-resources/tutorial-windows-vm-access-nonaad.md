---
title: Samouczek`:` korzystania z tożsamości zarządzanej w celu uzyskania dostępu do Azure Key Vault-Windows-Azure AD
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Key Vault.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a5c2c101b934f07ab85082e726cb8e1341e5d0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888718"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób używania tożsamości zarządzanej przypisanej przez system dla maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do usługi Azure Key Vault. Usługa Key Vault, używana przy uruchamianiu, umożliwia aplikacji klienckiej użycie wpisu tajnego do uzyskania dostępu do zasobów, które nie są zabezpieczane przez usługę Azure Active Directory (AD). Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. 

Omawiane kwestie:


> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>Udzielanie dostępu  
 
W tej sekcji pokazano, jak udzielić dostępu do maszyny wirtualnej do wpisu tajnego przechowywanego w Key Vault. Używając tożsamości zarządzanych dla zasobów platformy Azure, kod może uzyskiwać tokeny dostępu, aby uwierzytelniać się w zasobach obsługujących uwierzytelnianie usługi Azure AD.  Jednak nie wszystkie usługi platformy Azure obsługują uwierzytelnianie w usłudze Azure AD. Aby używać tożsamości zarządzanych dla zasobów platformy Azure w ramach tych usług, należy przechowywać poświadczenia usługi w Azure Key Vault i używać tożsamości zarządzanej maszyny wirtualnej w celu uzyskania dostępu do Key Vault w celu pobrania poświadczeń. 

Najpierw musimy utworzyć usługę Key Vault i udzielić przypisanej przez system tożsamości zarządzanej naszej maszyny wirtualnej dostępu do usługi Key Vault.   

1. W górnej części lewego paska nawigacyjnego wybierz opcję **Utwórz zasób** > **Bezpieczeństwo i obsługa tożsamości** > **Key Vault**.  
2. Podaj **Nazwę** dla nowej usługi Key Vault. 
3. Znajdź usługę Key Vault w tej samej subskrypcji i grupie zasobów co wcześniej utworzona maszyna wirtualna. 
4. Wybierz opcję **Zasady dostępu** i kliknij opcję **Dodaj nową**. 
5. W pozycji Konfiguruj na podstawie szablonu wybierz opcję **Zarządzanie wpisami tajnymi**. 
6. Wybierz opcję **Wybierz podmiot zabezpieczeń**, a następnie w polu wyszukiwania wprowadź nazwę wcześniej utworzonej maszyny wirtualnej.  Na liście wyników wybierz maszynę wirtualną, a następnie kliknij pozycję **Wybierz**. 
7. Kliknij przycisk **OK**, aby zakończyć dodawanie nowych zasad dostępu, a następnie kliknij przycisk **OK**, aby zakończyć wybór zasad dostępu. 
8. Kliknij przycisk **Utwórz**, aby zakończyć tworzenie usługi Key Vault. 

    ![Alternatywny tekst obrazu](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Następnie dodaj wpis tajny do usługi Key Vault, aby umożliwić późniejsze pobranie wpisu tajnego przy użyciu kodu uruchomionego na maszynie wirtualnej: 

1. Wybierz opcję **Wszystkie zasoby**, a następnie znajdź i wybierz utworzoną usługę Key Vault. 
2. Wybierz opcję **Wpisy tajne** i kliknij opcję **Dodaj**. 
3. Wybierz opcję **Ręczne** z pozycji **Opcje przekazywania**. 
4. Wprowadź nazwę i wartość wpisu tajnego.  Wartość może być dowolna. 
5. Pozostaw pustą datę aktywacji i datę wygaśnięcia oraz zostaw opcję **Włączone** ustawioną na wartość **Tak**. 
6. Kliknij pozycję **Utwórz**, aby utworzyć wpis tajny. 
 
## <a name="get-an-access-token"></a>Pobranie tokenu dostępu  

W tej sekcji pokazano, jak uzyskać token dostępu przy użyciu tożsamości maszyny wirtualnej i użyć go do pobrania klucza tajnego z Key Vault. Jeśli nie masz zainstalowanego programu PowerShell 4.3.1 (lub nowszej wersji), musisz [pobrać i zainstalować najnowszą wersję](https://docs.microsoft.com/powershell/azure/overview).

Najpierw użyjemy przypisanej przez system tożsamości zarządzanej maszyny wirtualnej, aby uzyskać token dostępu i przeprowadzić uwierzytelnianie w usłudze Key Vault:
 
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
    
    Na koniec użyj polecenia Invoke-WebRequest programu PowerShell, aby pobrać wpis tajny utworzony wcześniej w usłudze Key Vault, przekazując token dostępu w nagłówku autoryzacji.  Wymagany jest adres URL Key Vault, który znajduje się w sekcji **podstawowe** strony **Przegląd** Key Vault.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Odpowiedź będzie wyglądać następująco: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Po pobraniu wpisu tajnego z usługi Key Vault możesz użyć go do uwierzytelnienia w usłudze wymagającej nazwy i hasła. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób używania przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do usługi Azure Key Vault.  Dowiedz się więcej o usłudze Azure Key Vault:

> [!div class="nextstepaction"]
>[Usługa Azure Key Vault](/azure/key-vault/key-vault-overview)
