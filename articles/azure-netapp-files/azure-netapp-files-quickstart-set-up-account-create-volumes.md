---
title: Ustaw Szybki Start — Konfigurowanie usługi Azure Files NetApp i utworzyć wolumin systemu plików NFS | Dokumentacja firmy Microsoft
description: Przewodnik Szybki Start — w tym artykule opisano sposób szybkiego konfigurowania usługi Azure Files NetApp i utworzyć wolumin.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 4/16/2019
ms.author: b-juche
ms.openlocfilehash: 4ea511bec75557bc6f7d37b1724b4b0db65ba9cc
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299417"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Szybki start: Konfigurowanie usługi Azure NetApp Files i tworzenie woluminu NFS 

W tym artykule pokazano, jak szybko skonfigurować usługi Azure Files NetApp i utworzyć wolumin. 

W tym przewodniku Szybki Start będzie skonfigurować następujące elementy:

- Rejestracja w celu uzyskania plików NetApp platformy Azure i dostawcy zasobów NetApp
- Konto NetApp
- Pojemność puli
- Wolumin systemu plików NFS dla usługi Azure Files NetApp

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem 

> [!IMPORTANT] 
> Należy mieć uprawnienia dostępu do usługi Azure NetApp Files.  Aby zażądać dostępu do usługi, zobacz [stronę przesyłania na liście oczekujących plików NetApp Azure](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  Przed kontynuowaniem należy poczekać do wiadomości e-mail z potwierdzeniem oficjalne, od zespołu usługi Azure Files NetApp. 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Rejestrowanie na potrzeby plików NetApp platformy Azure i dostawcy zasobów NetApp

1. W witrynie Azure portal kliknij ikonę usługi Azure Cloud Shell w prawym górnym rogu.

    ![Ikona usługi Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell-window.png)

2. Określ subskrypcję, która została umieszczona na białej liście dla usługi Azure Files NetApp:
    
        az account set --subscription <subscriptionId>

3. Zarejestruj dostawcę zasobów platformy Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Proces rejestracji może trochę potrwać.

## <a name="create-a-netapp-account"></a>Tworzenie konta usługi NetApp

1. W polu wyszukiwania w witrynie Azure portal, wprowadź **usługi Azure Files NetApp** , a następnie wybierz **usługi Azure Files NetApp** z wyświetlonej listy.

      ![Wybierz pliki NetApp platformy Azure](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Kliknij pozycję **+ Dodaj**, aby utworzyć nowe konto usługi NetApp.

     ![Utwórz nowe konto NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. W oknie nowego konta NetApp Podaj następujące informacje: 
   1. Wprowadź **myaccount1** dla nazwy konta. 
   2. Wybierz subskrypcję.
   3. Wybierz **Utwórz nową** do tworzenia nowej grupy zasobów. Wprowadź **myRG1** nazwy grupy zasobów. Kliknij przycisk **OK**. 
   4. Wybierz swoją lokalizację konta.  

      ![Nowe okno konta NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Okno w grupie zasobów](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Kliknij przycisk **Utwórz** do utworzenia nowego konta NetApp.

## <a name="set-up-a-capacity-pool"></a>Konfigurowanie puli pojemności

1. Z bloku zarządzania usługi Azure Files NetApp, wybierz swoje konto NetApp (**myaccount1**).

    ![Wybierz konto NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. W bloku zarządzania usługi Azure Files NetApp Twojego konta rozwiązania NetApp, kliknij **pojemności pul**.

    ![Kliknij przycisk pojemności pul](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Kliknij przycisk **+ Dodaj pule**. 

    ![Kliknij przycisk Dodaj pule](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Podaj informacje dotyczące pojemności puli: 
    1. Wprowadź **mypool1** jako nazwy puli.
    2. Wybierz **Premium** poziomu usługi. 
    3. Określ **4 (TiB)** jako rozmiar puli. 

5. Kliknij przycisk **OK**.

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Utwórz wolumin systemu plików NFS dla usługi Azure Files NetApp

1. W bloku zarządzania usługi Azure Files NetApp Twojego konta rozwiązania NetApp, kliknij **woluminów**.

    ![Klikanie pozycji woluminy](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Kliknij pozycję **+ Dodaj wolumin**.

    ![Kliknij pozycję Dodaj woluminy](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. W tworzenie okna woluminu Podaj informacje dla danego woluminu: 
   1. Wprowadź **myvol1** jako nazwa woluminu. 
   3. Wybierz pojemność puli (**mypool1**).
   4. Użyj wartości domyślnej dla limitu przydziału. 
   5. W ramach sieci wirtualnej, kliknij przycisk **Utwórz nową** do tworzenia nowej sieci wirtualnej platformy Azure (Vnet).  Następnie wprowadź następujące informacje:
       * Wprowadź **myvnet1** jako nazwa sieci wirtualnej.
       * Określ przestrzeń adresową dla ustawienia, na przykład 10.7.0.0/16
       * Wprowadź **myANFsubnet** jako nazwę podsieci.
       * Określ zakres adresów podsieci, na przykład 10.7.0.0/24. Należy pamiętać, że nie można udostępnić dedykowaną podsieć z innymi zasobami.
       * Wybierz **Microsoft.NetApp/volumes** dla celów delegacji podsieci.
       * Kliknij przycisk **OK** do tworzenia sieci wirtualnej.
   6. W podsieci, wybierz nowo utworzoną sieć wirtualną (**myvnet1**) jako podsieć delegata.

      ![Utwórz okno woluminu](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Tworzenie sieci wirtualnej okna](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Kliknij przycisk **protokołu**, a następnie wybierz **systemu plików NFS** jako typ protokołu dla woluminu.   

    Wprowadź **myfilepath1** jako ścieżkę pliku, która będzie służyć do tworzenia ścieżki eksportu woluminu. 

    ![Określenie protokołu NFS w przewodniku Szybki Start](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Kliknij pozycję **Przegląd + utwórz**.

    ![Przejrzyj i utworzyć okno](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Przejrzyj informacje o woluminie, a następnie kliknij przycisk **Utwórz**.  
    Utworzono wolumin zostanie wyświetlony w bloku woluminów.

    ![Wolumin utworzony](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy wszystko będzie gotowe, i chcesz, możesz usunąć grupę zasobów. Akcja usuwania grupy zasobów jest nieodwracalne.  

> [!IMPORTANT]
> Wszystkie zasoby w ramach grupy zasobów zostaną trwale usunięte i nie można cofnąć. 

1. W polu wyszukiwania w witrynie Azure portal, wprowadź **usługi Azure Files NetApp** , a następnie wybierz **usługi Azure Files NetApp** z wyświetlonej listy.

2. Na liście subskrypcji kliknij grupę zasobów (myRG1), który chcesz usunąć. 

    ![Przejdź do grupy zasobów](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na stronie grupy zasobów kliknij **Usuń grupę zasobów**.

    ![Usuwanie grupy zasobów](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Zostanie otwarte okno zawierające ostrzeżenie dotyczące zasobów, które zostaną usunięte razem z grupą zasobów.

4. Wprowadź nazwę grupy zasobów (myRG1), aby upewnić się, że chcesz trwale usunąć grupę zasobów i wszystkie zawarte w niej zasoby, a następnie kliknij przycisk **Usuń**.

    ![Usuwanie grupy zasobów](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

## <a name="next-steps"></a>Kolejne kroki  

> [!div class="nextstepaction"]
> [Zarządzanie woluminami za pomocą usługi Azure Files NetApp](azure-netapp-files-manage-volumes.md)  
