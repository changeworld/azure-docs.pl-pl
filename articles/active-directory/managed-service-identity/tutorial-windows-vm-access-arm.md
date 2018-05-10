---
title: Umożliwia dostęp do usługi Azure Resource Manager MSI maszyny Wirtualnej systemu Windows
description: Samouczek, który przeprowadzi Cię przez proces przy użyciu systemu Windows maszyny Wirtualnej zarządzane usługi tożsamości (MSI) można uzyskać dostępu do usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 1b23101de9a84da487e73558366cc574a22e849d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>Umożliwia dostęp do Menedżera zasobów systemu Windows maszyny Wirtualnej zarządzane usługi tożsamości (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ten samouczek pokazuje, jak włączyć zarządzane tożsamości usługi (MSI) dla systemu Windows maszyny wirtualnej (VM). Tej tożsamości można następnie umożliwia dostęp do interfejsu API Azure Resource Manager. Zarządzane tożsamości usługi automatycznie są zarządzane przez usługę Azure i umożliwiają uwierzytelnianie usług, które obsługują usługi Azure AD authentication bez konieczności wstawić poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Włącz MSI na Windows maszyny Wirtualnej 
> * Przyznać uprawnienia maszyny Wirtualnej do grupy zasobów w usłudze Azure Resource Manager 
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i użyć go do wywołania usługi Azure Resource Manager

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Windows w nowej grupy zasobów

W tym samouczku utworzymy nową maszynę Wirtualną systemu Windows.  Można również włączyć MSI na istniejącej maszyny Wirtualnej.

1.  Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.
2.  Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3.  Wprowadź informacje o maszynie wirtualnej. **Username** i **hasło** utworzony, w tym miejscu jest poświadczeń umożliwia logowanie do maszyny wirtualnej.
4.  Wybierz odpowiednią **subskrypcji** dla maszyny wirtualnej na liście rozwijanej.
5.  Aby wybrać nowy **grupy zasobów** w której chcesz utworzyć maszynę wirtualną, wybrać **Utwórz nowy**. Po zakończeniu kliknij przycisk **OK**.
6.  Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. Na stronie ustawienia Zachowaj wartości domyślne, a następnie kliknij przycisk **OK**.

    ![Tekst alternatywny obrazu](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włącz MSI na maszynie Wirtualnej 

MSI maszyny Wirtualnej umożliwia pobieranie tokenów dostępu z usługi Azure AD bez konieczności umieścić poświadczeń w kodzie. Włączanie zarządzania tożsamości usługi na maszynie Wirtualnej, wykonuje dwie czynności: rejestrów maszyny Wirtualnej z usługi Azure Active Directory do utworzenia zarządzanej tożsamości, a konfiguruje tożsamości na maszynie Wirtualnej.

1.  Wybierz **maszyny wirtualnej** chcesz włączyć MSI.  
2.  Na pasku nawigacyjnym po lewej stronie kliknij **konfiguracji**. 
3.  Zostanie wyświetlony **zarządzane tożsamość usługi**. Aby zarejestrować i włączyć MSI, wybierz **tak**, jeśli chcesz ją wyłączyć, wybierz opcję nie. 
4.  Upewnij się, możesz kliknąć przycisk **zapisać** Aby zapisać konfigurację.  
    ![Tekst alternatywny obrazu](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Przyznać uprawnienia maszyny Wirtualnej do grupy zasobów w Menedżerze zasobów
Za pomocą Instalatora MSI kodu mogą uzyskiwać tokeny dostępu do uwierzytelniania do zasobów, które obsługują uwierzytelnianie usługi Azure AD.  Usługi Azure Resource Manager obsługuje uwierzytelnianie w usłudze Azure AD.  Najpierw należy udzielić tej maszyny Wirtualnej tożsamości dostępu do zasobów w Menedżerze zasobów w tym przypadku grupę zasobów, w którym znajduje się maszyna wirtualna.  

1.  Przejdź do karty dla **grup zasobów**. 
2.  Wybierz konkretnych **grupy zasobów** utworzony dla Twojej **maszyny Wirtualnej systemu Windows**. 
3.  Przejdź do **(IAM) kontroli dostępu** w lewym panelu. 
4.  Następnie **Dodaj** nowe przypisanie roli dla Twojego **maszyny Wirtualnej systemu Windows**.  Wybierz **roli** jako **czytnika**. 
5.  W następnej listy rozwijanej **przypisany dostęp** zasobu **maszyny wirtualnej**. 
6.  Następnie sprawdź odpowiednie subskrypcji znajduje się w **subskrypcji** listy rozwijanej. I **grupy zasobów**, wybierz pozycję **wszystkich grup zasobów**. 
7.  Ponadto w **wybierz** wybierz maszyny Wirtualnej systemu Windows w pliku listy rozwijanej i kliknij przycisk **zapisać**.

    ![Tekst alternatywny obrazu](../media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskaj token dostępu przy użyciu tożsamości maszyny Wirtualnej i użyć go do wywołania usługi Azure Resource Manager 

Będą musieli używać **PowerShell** w tej części.  Jeśli nie został zainstalowany, pobierz go [tutaj](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  W portalu, przejdź do **maszyn wirtualnych** i przejdź do maszyny wirtualnej systemu Windows i w **omówienie**, kliknij przycisk **Connect**. 
2.  Wprowadź w Twojej **Username** i **hasło** dla którego zostanie dodane po utworzeniu maszyny Wirtualnej systemu Windows. 
3.  Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, otwórz **PowerShell** w sesji zdalnej. 
4.  Przy użyciu programu Powershell w Invoke-WebRequest, Wyślij żądanie do lokalnego punktu końcowego MSI do Uzyskaj token dostępu usługi Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Wartość parametru "zasobu" musi być dokładnego dopasowania dla oczekiwano przez usługę Azure AD. Podczas korzystania z usługi Azure Resource Manager identyfikator zasobu, należy dołączyć końcowy ukośnik w identyfikatorze URI.
    
    Następnie Wyodrębnij całą odpowiedź, który jest przechowywany jako ciąg w formacie JavaScript Object Notation (JSON) w obiekcie $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Następnie Wyodrębnij token dostępu z odpowiedzi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Ponadto wywołanie usługi Azure Resource Manager przy użyciu tokenu dostępu. W tym przykładzie również używamy w programie PowerShell Invoke-WebRequest wykonać wywołanie do usługi Azure Resource Manager i zawierać token dostępu w nagłówku autoryzacji.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > Adres URL jest rozróżniana wielkość liter, dlatego upewnij się, jeśli używasz tego samego dokładnej zgodnie z wcześniej używane podczas nosi nazwę grupy zasobów i wielkie litery "G" w "resourceGroups."
        
    Poniższe polecenie zwraca szczegółów grupy zasobów:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](overview.md).

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.

