---
title: Tworzenie woluminu dla usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano sposób tworzenia woluminu dla usługi Azure NetApp Files.
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
ms.date: 12/17/2018
ms.author: b-juche
ms.openlocfilehash: 0d4629651e17c917720196c1e5f6152c50c2a33a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770270"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Tworzenie woluminu dla usługi Azure NetApp Files

Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.  W jednej puli pojemności można utworzyć wiele woluminów, ale ich łączne użycie pojemności nie może przekraczać rozmiaru puli. 

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Potrzebujesz skonfigurowanej puli pojemności.   
[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)   
Podsieć musi być delegowana do usługi Azure NetApp Files.  
[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>Kroki 
1.  Kliknij blok **Woluminy** w bloku Zarządzanie pulami pojemności. 
2.  Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Zostanie wyświetlone okno Nowy wolumin.

3.  W oknie Nowy wolumin kliknij pozycję **Utwórz** i wprowadź informacje w następujących polach:   
    * **Nazwa**      
        Określ nazwę tworzonego woluminu.   

        Nazwa musi być unikatowa w obrębie grupy zasobów. Musi zawierać co najmniej trzy znaki.  Dozwolone są dowolne znaki alfanumeryczne.

    * **Ścieżka pliku**  
        Określ ścieżkę pliku, na podstawie której zostanie utworzona ścieżka eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania woluminu oraz uzyskiwania do niego dostępu.   
     
        Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki („-”). Musi mieć długość od 16 do 40 znaków.  

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskiwać dostęp do woluminu.  

        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej znajdującej się w tym samym regionie, co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Dostęp do woluminu można również uzyskać z sieci lokalnej za pośrednictwem usługi Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. Pamiętaj, że w każdej sieci wirtualnej można delegować tylko jedną podsieć do usługi Azure NetApp Files.   
 
        ![Nowy wolumin](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  Kliknij przycisk **OK**. 
 
Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="next-steps"></a>Kolejne kroki  
* [Konfigurowanie zasad eksportu dla woluminu (opcjonalnie)](azure-netapp-files-configure-export-policy.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

