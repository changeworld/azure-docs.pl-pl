---
title: Zarządzanie migawkami przy użyciu usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia migawek na żądanie dla woluminu lub przywracanie z migawki do nowego woluminu za pomocą usługi Azure Files NetApp.
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
ms.topic: how-to-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 48cb88b9815ba723d93c18caf63f33b50eea850c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009196"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Zarządzanie migawkami przy użyciu usługi Azure Files NetApp
Usługi Azure Files NetApp służy do tworzenia migawek na żądanie dla woluminu lub przywracanie z migawki do nowego woluminu.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Tworzenie migawki woluminu na żądanie
Można utworzyć migawki tylko na żądanie.  Zasady migawki nie są obecnie obsługiwane.  
1.  W bloku zarządzanie woluminem kliknij **migawek**, następnie kliknij przycisk **+ Dodaj migawki** można utworzyć migawki na żądanie dla woluminu.

2.  W oknie nową migawkę Podaj nazwę nową migawkę, który tworzysz.   

3. Kliknij przycisk **OK**. 


## <a name="restore-a-snapshot-to-a-new-volume"></a>Przywracanie migawki do nowego woluminu
Obecnie można przywrócić migawki tylko do nowego woluminu. 
1. Przejdź do **Zarządzaj migawkami** bloku z bloku woluminu, aby wyświetlić listę migawek. 
2. Wybierz migawkę do przywrócenia.  
3. Kliknij prawym przyciskiem myszy nazwę migawki, a następnie wybierz pozycję **Przywracanie do nowego woluminu** z opcji menu.  

    ![Przywracanie migawki do nowego woluminu](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. W oknie nowego woluminu Podaj informacje dotyczące nowego woluminu:  
    * **Nazwa**   
        Określ nazwę dla woluminu, który tworzysz.  
        
        Nazwa musi być unikatowa w obrębie grupy zasobów. Musi być co najmniej 3 znaków.  Może używać żadnych znaków alfanumerycznych.

    * **Ścieżka pliku**     
        Określ ścieżkę pliku, która będzie służyć do tworzenia ścieżki eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania i dostęp do woluminu.   
        
        Miejsce docelowe instalacji jest punkt końcowy adres IP usługi systemu plików NFS. Są one automatycznie generowane.   
        
        Nazwa ścieżki pliku może zawierać litery, cyfry i łączniki ("-") tylko. Należy się od 16 do 40 znaków. 

    * **Quota**  
        Określ rozmiar magazynu logicznego, który jest przydzielony do woluminu.  

        **Dostępny limit przydziału** polu jest wyświetlana kwota nieużywane miejsce w puli wybranym pojemność, która służy do tworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępny limit przydziału.

    *   **Sieć wirtualna**  
        Określ Azure sieci wirtualnej (Vnet), z którego chcesz uzyskać dostęp do woluminu. 
        
        Sieć wirtualna, które określisz musi mieć usługi Azure Files NetApp skonfigurowane. Usługa Azure NetApp Files jest możliwy tylko z sieci wirtualnej, która znajduje się w tej samej lokalizacji co wolumin.  

    ![Przywrócono nowy wolumin](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
    
5. Kliknij przycisk **OK**.   
    Nowy wolumin, do którego zostanie przywrócona migawka zostanie wyświetlony w bloku woluminów.

