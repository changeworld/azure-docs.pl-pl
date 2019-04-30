---
title: Konfigurowanie usługi Azure Files NetApp i Utwórz wolumin systemu plików NFS | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak szybko skonfigurować usługi Azure Files NetApp i utworzyć wolumin.
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
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 5b9e85da29b3130d5183f577a7d83d164e217310
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759828"
---
# <a name="set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Konfigurowanie usługi Azure Files NetApp i Utwórz wolumin systemu plików NFS 

W tym artykule pokazano, jak szybko skonfigurować usługi Azure Files NetApp i utworzyć wolumin. 

## <a name="before-you-begin"></a>Przed rozpoczęciem 

Musisz uczestniczyć w programie podglądu publicznego i znajdować się na liście dozwolonych, aby móc uzyskiwać dostęp do dostawcy zasobów Microsoft.NetApp. Szczegółowe informacje na temat dołączania do programu podglądu publicznego można znaleźć na [stronie rejestracji w podglądzie publicznym usługi Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Rejestrowanie na potrzeby plików NetApp platformy Azure i dostawcy zasobów NetApp

1. W witrynie Azure portal kliknij ikonę usługi Azure Cloud Shell w prawym górnym rogu.

      ![Ikona usługi Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Określ subskrypcję, która została umieszczona na białej liście dla usługi Azure Files NetApp:
    
        az account set --subscription <subscriptionId>

3. Zarejestruj dostawcę zasobów platformy Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Proces rejestracji może trochę potrwać.

## <a name="create-a-netapp-account"></a>Tworzenie konta usługi NetApp

1. W polu wyszukiwania w witrynie Azure portal, wprowadź **usługi Azure Files NetApp** , a następnie wybierz **plików NetApp Azure (wersja zapoznawcza)** z wyświetlonej listy.

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

## <a name="next-steps"></a>Kolejne kroki  

* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Zarządzanie woluminami za pomocą usługi Azure Files NetApp](azure-netapp-files-manage-volumes.md) 
