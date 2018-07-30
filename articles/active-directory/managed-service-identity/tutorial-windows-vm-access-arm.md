---
title: Uzyskiwanie dostępu do usługi Azure Resource Manager przy użyciu tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows
description: Samouczek przedstawiający proces użycia tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: bd314dd1543280cf2533e45f156ca634d15d1d2a
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247248"
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-resource-manager"></a>Używanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Resource Manager

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób włączania tożsamości usługi zarządzanej na potrzeby maszyny wirtualnej z systemem Windows. Następnie można użyć tej tożsamości do uzyskania dostępu do interfejsu API usługi Azure Resource Manager. Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows 
> * Udzielanie maszynie wirtualnej dostępu do grupy zasobów w usłudze Azure Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania usługi Azure Resource Manager

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Tworzenie maszyny wirtualnej z systemem Windows w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę wirtualną z systemem Windows.  Możesz również włączyć tożsamość usługi zarządzanej na istniejącej maszynie wirtualnej.

1.  Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Nazwa użytkownika** i **Hasło** utworzone w tym miejscu są poświadczeniami używanymi do logowania do maszyny wirtualnej.
4.  Wybierz odpowiednią **Subskrypcję** dla maszyny wirtualnej z listy rozwijanej.
5.  Aby wybrać nową **Grupę zasobów**, w której chcesz utworzyć maszynę wirtualną, wybierz opcję **Utwórz nową**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. Na stronie ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

    ![Alternatywny tekst obrazu](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej 

Tożsamość usługi zarządzanej maszyny wirtualnej umożliwia uzyskanie tokenów dostępu z usługi Azure AD bez potrzeby umieszczania poświadczeń w kodzie. Włączenie tożsamości usługi zarządzanej na maszynie wirtualnej powoduje wykonanie dwóch czynności: zapewnia rejestrację maszyny wirtualnej w usłudze Azure Active Directory, aby utworzyć tożsamość zarządzaną, oraz konfiguruje tożsamość na maszynie wirtualnej.

1.  Wybierz **maszynę wirtualną**, na której chcesz włączyć tożsamość usługi zarządzanej.  
2.  Na lewym pasku nawigacyjnym kliknij opcję **Konfiguracja**. 
3.  Zobaczysz ekran **Tożsamość usługi zarządzanej**. Aby zarejestrować i włączyć tożsamość usługi zarządzanej, wybierz pozycję **Tak**. Jeśli chcesz ją wyłączyć, wybierz pozycję Nie. 
4.  Pamiętaj, aby kliknąć przycisk **Zapisz** w celu zapisania konfiguracji.  
    ![Alternatywny tekst obrazu](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Udzielanie maszynie wirtualnej dostępu do grupy zasobów w usłudze Resource Manager
Przy użyciu tożsamości usługi zarządzanej kod może uzyskać tokeny dostępu, aby uwierzytelniać zasoby obsługujące uwierzytelnianie usługi Azure AD.  Usługa Azure Resource Manager obsługuje uwierzytelnianie usługi Azure AD.  Najpierw musimy zapewnić tożsamości tej maszyny wirtualnej dostęp do zasobu w usłudze Resource Manager, w tym przypadku do grupy zasobów, która zawiera maszynę wirtualną.  

1.  Przejdź do karty **Grupy zasobów**. 
2.  Wybierz konkretną **grupę zasobów**, która została utworzona na potrzeby **maszyny wirtualnej z systemem Windows**. 
3.  Przejdź do pozycji **Kontrola dostępu (IAM)** w panelu po lewej stronie. 
4.  Następnie **Dodaj** nowy przydział roli do **maszyny wirtualnej z systemem Windows**.  W pozycji **Rola** wybierz opcję **Czytelnik**. 
5.  Na następnej liście rozwijanej **Przypisz dostęp do** zasobu **Maszyna wirtualna**. 
6.  Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej **Subskrypcja**. W pozycji **Grupa zasobów** wybierz opcję **Wszystkie grupy zasobów**. 
7.  Na koniec w pozycji **Wybierz** użyj listy rozwijanej, aby wybrać maszynę wirtualną z systemem Windows i kliknij przycisk **Zapisz**.

    ![Alternatywny tekst obrazu](media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do wywołania usługi Azure Resource Manager 

W tej części musisz użyć programu **PowerShell**.  Jeśli program nie został zainstalowany, pobierz go [stąd](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  W portalu przejdź do pozycji **Maszyny wirtualne**, a następnie przejdź do swojej maszyny wirtualnej z systemem Windows i w pozycji **Przegląd** kliknij przycisk **Połącz**. 
2.  Wprowadź **nazwę użytkownika** i **hasło** dodane podczas tworzenia maszyny wirtualnej z systemem Windows. 
3.  Teraz, po utworzeniu **połączenia pulpitu zdalnego** z maszyną wirtualną, otwórz program **PowerShell** w sesji zdalnej. 
4.  Używając polecenia Invoke-WebRequest programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości usługi zarządzanej, aby uzyskać token dostępu na potrzeby usługi Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Wartość parametru „resource” musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD. W przypadku użycia identyfikatora zasobu usługi Azure Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI.
    
    Następnie wyodrębnij pełną odpowiedź, która jest przechowywana w ciągu w formacie JavaScript Object Notation (JSON) w obiekcie $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Następnie wyodrębnij token dostępu z odpowiedzi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Na koniec wywołaj usługę Azure Resource Manager przy użyciu tokenu dostępu. W tym przykładzie używamy również polecenia Invoke-WebRequest programu PowerShell, aby wysłać wywołanie do usługi Azure Resource Manager i uwzględnić token dostępu w nagłówku autoryzacji.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > W adresie URL rozróżniana jest wielkość liter, więc upewnij się, że użyto takich samych wartości jak wcześniej, podczas nazywania grupy zasobów — z wielką literą „G” w nazwie „resourceGroups”.
        
    Poniższe polecenie zwraca szczegóły grupy zasobów:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia tożsamości przypisanej przez użytkownika i dołączania jej do maszyny wirtualnej platformy Azure w celu uzyskania dostępu do interfejsu API usługi Azure Resource Manager.  Aby dowiedzieć się więcej o usłudze Azure Resource Manager, zobacz:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

