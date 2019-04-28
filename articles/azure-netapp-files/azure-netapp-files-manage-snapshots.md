---
title: Zarządzanie migawkami przy użyciu usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia migawek woluminu lub przywracanie z migawki do nowego woluminu za pomocą usługi Azure Files NetApp.
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
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 01387d0c219c86f33762b9c3fbf9f81cf04b4455
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086854"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Zarządzanie migawkami przy użyciu usługi Azure Files NetApp

Usługi Azure Files NetApp służy do tworzenia migawek na żądanie dla woluminu lub przywracanie z migawki do nowego woluminu.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Tworzenie migawki woluminu na żądanie

Można utworzyć migawki tylko na żądanie. Zasady migawki nie są obecnie obsługiwane.

1.  Z poziomu bloku woluminów kliknij **migawek**.

    ![Przejdź do migawki](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kliknij przycisk **+ Dodaj migawki** można utworzyć migawki na żądanie dla woluminu.

    ![Dodaj migawkę](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  W oknie nową migawkę Podaj nazwę nową migawkę, który tworzysz.   

    ![Nowa migawka](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Kliknij przycisk **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Przywracanie migawki do nowego woluminu

Obecnie można przywrócić migawki tylko do nowego woluminu. 
1. Przejdź do **Zarządzaj migawkami** bloku z bloku woluminu, aby wyświetlić listę migawek. 
2. Wybierz migawkę do przywrócenia.  
3. Kliknij prawym przyciskiem myszy nazwę migawki, a następnie wybierz pozycję **Przywracanie do nowego woluminu** z opcji menu.  

    ![Przywracanie migawki do nowego woluminu](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. W oknie nowego woluminu Podaj informacje dotyczące nowego woluminu:  
    * **Nazwa**   
        Określ nazwę tworzonego woluminu.  
        
        Nazwa musi być unikatowa w obrębie grupy zasobów. Musi zawierać co najmniej trzy znaki.  Dozwolone są dowolne znaki alfanumeryczne.

    * **Ścieżka pliku**     
        Określ ścieżkę pliku, na podstawie której zostanie utworzona ścieżka eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania woluminu oraz uzyskiwania do niego dostępu.   
        
        Miejsce docelowe instalacji to punkt końcowy adresu IP usługi NFS. Ta wartość jest generowana automatycznie.   
        
        Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki („-”). Musi mieć długość od 16 do 40 znaków. 

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.

    *   **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskiwać dostęp do woluminu.  
        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Można uzyskać dostęp do usługi Azure Files NetApp, tylko z tej samej podsieci lub sieci wirtualnej, która znajduje się w tym samym regionie jako wolumin za pomocą komunikacji równorzędnej sieci wirtualnej. Z sieci lokalnej za pośrednictwem Express Route, mają dostęp do woluminu. 

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Podsieć, którą określisz musi delegować do usługi Azure NetApp Files. Można utworzyć nowej podsieci, wybierając **Utwórz nową** pod polem podsieci.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Kliknij przycisk **OK**.   
    Nowy wolumin, do którego zostanie przywrócona migawka zostanie wyświetlony w bloku woluminów.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)