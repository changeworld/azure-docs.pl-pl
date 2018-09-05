---
title: Używanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Key Vault
description: Samouczek przedstawiający proces użycia tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Resource Manager.
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
ms.openlocfilehash: 9a4aaa17c8ab325f93002d55e301363fd9f2d4e5
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885048"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-to-access-azure-key-vault"></a>Samouczek: używanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Key Vault 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób używania tożsamości przypisanej przez system dla maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Key Vault. Usługa Key Vault, używana przy uruchamianiu, umożliwia aplikacji klienckiej użycie wpisu tajnego do uzyskania dostępu do zasobów, które nie są zabezpieczane przez usługę Azure Active Directory (AD). Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. 

Omawiane kwestie:

> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault 
 
## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Zalogowanie się w witrynie Azure Portal](https://portal.azure.com)

- [Utworzenie maszyny wirtualnej z systemem Linux](/azure/virtual-machines/linux/quick-create-portal)

- [Włączenie tożsamości przypisanej przez system na maszynie wirtualnej](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udzielanie maszynie wirtualnej dostępu do wpisu tajnego przechowywanego w usłudze Key Vault  

Przy użyciu tożsamości usługi zarządzanej kod może uzyskać tokeny dostępu, aby uwierzytelniać zasoby obsługujące uwierzytelnianie usługi Azure Active Directory. Jednak nie wszystkie usługi platformy Azure obsługują uwierzytelnianie usługi Azure AD. Aby użyć tożsamości usługi zarządzanej z tymi usługami, zapisz poświadczenia usługi w usłudze Azure Key Vault, a następnie użyj tożsamości usługi zarządzanej, aby uzyskać dostęp do usługi Key Vault i pobrać te poświadczenia. 

Najpierw musimy utworzyć usługę Key Vault i udzielić tożsamości naszej maszyny wirtualnej dostępu do usługi Key Vault.   

1. W górnej części lewego paska nawigacyjnego wybierz opcję **Utwórz zasób** > **Bezpieczeństwo i obsługa tożsamości** > **Key Vault**.  
2. Podaj **Nazwę** dla nowej usługi Key Vault. 
3. Znajdź usługę Key Vault w tej samej subskrypcji i grupie zasobów co wcześniej utworzona maszyna wirtualna. 
4. Wybierz opcję **Zasady dostępu** i kliknij opcję **Dodaj nową**. 
5. W pozycji Konfiguruj na podstawie szablonu wybierz opcję **Zarządzanie wpisami tajnymi**. 
6. Wybierz opcję **Wybierz podmiot zabezpieczeń**, a następnie w polu wyszukiwania wprowadź nazwę wcześniej utworzonej maszyny wirtualnej.  Wybierz maszynę wirtualną z listy wyników i kliknij opcję **Wybierz**. 
7. Kliknij przycisk **OK**, aby zakończyć dodawanie nowych zasad dostępu, a następnie kliknij przycisk **OK**, aby zakończyć wybór zasad dostępu. 
8. Kliknij przycisk **Utwórz**, aby zakończyć tworzenie usługi Key Vault. 

    ![Alternatywny tekst obrazu](../managed-service-identity/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Następnie dodaj wpis tajny do usługi Key Vault, aby umożliwić późniejsze pobranie wpisu tajnego przy użyciu kodu uruchomionego na maszynie wirtualnej: 

1. Wybierz opcję **Wszystkie zasoby**, a następnie znajdź i wybierz utworzoną usługę Key Vault. 
2. Wybierz opcję **Wpisy tajne** i kliknij opcję **Dodaj**. 
3. Wybierz opcję **Ręczne** z pozycji **Opcje przekazywania**. 
4. Wprowadź nazwę i wartość wpisu tajnego.  Wartość może być dowolna. 
5. Pozostaw pustą datę aktywacji i datę wygaśnięcia oraz zostaw opcję **Włączone** ustawioną na wartość **Tak**. 
6. Kliknij pozycję **Utwórz**, aby utworzyć wpis tajny. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do pobrania wpisu tajnego z usługi Key Vault  

Aby wykonać te kroki, potrzebujesz klienta SSH.  Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. W portalu przejdź do maszyny wirtualnej z systemem Linux i w pozycji **Przegląd** kliknij opcję **Połącz**. 
2. **Połącz** się z maszyną wirtualną przy użyciu wybranego klienta SSH. 
3. W oknie terminalu, używając narzędzia CURL, wyślij żądanie do lokalnego punktu końcowego tożsamości usługi zarządzanej, aby uzyskać token dostępu do usługi Azure Key Vault.  
 
    Żądanie programu CURL dla tokenu dostępu znajduje się poniżej.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    Odpowiedź zawiera token dostępu wymagany do uzyskania dostępu do usługi Resource Manager. 
    
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
    
    Możesz użyć tego tokenu dostępu, aby przeprowadzić uwierzytelnianie do usługi Azure Key Vault.  Kolejne żądanie programu CURL przedstawia sposób odczytu wpisu tajnego z usługi Key Vault przy użyciu programu CURL i interfejsu API REST usługi Key Vault.  Będziesz potrzebować adresu URL usługi Key Vault, który znajduje się w sekcji **Podstawowe elementy** na stronie **Przegląd** usługi Key Vault.  Ponadto będziesz potrzebować tokenu dostępu uzyskanego w poprzednim wywołaniu. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpowiedź będzie wyglądać następująco: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Po pobraniu wpisu tajnego z usługi Key Vault możesz użyć go do uwierzytelnienia w usłudze wymagającej nazwy i hasła.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Key Vault.  Dowiedz się więcej o usłudze Azure Key Vault:

> [!div class="nextstepaction"]
>[Usługa Azure Key Vault](/azure/key-vault/key-vault-whatis)




