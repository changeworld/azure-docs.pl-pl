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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 42e475f4da2102bb8b010daec6e6451ba7f13848
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011096"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Tworzenie woluminu dla usługi Azure NetApp Files

Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.  W jednej puli pojemności można utworzyć wiele woluminów, ale ich łączne użycie pojemności nie może przekraczać rozmiaru puli. 

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Potrzebujesz skonfigurowanej puli pojemności.  

[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)

## <a name="steps"></a>Kroki 
1.  Kliknij blok **Woluminy** w bloku Zarządzanie pulami pojemności. 
2.  Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Zostanie wyświetlone okno Nowy wolumin.

3.  W oknie Nowy wolumin kliknij pozycję **Utwórz** i wprowadź informacje w następujących polach:   
    * **Nazwa**      
        Określ nazwę tworzonego woluminu.   
        Nazwa musi być unikatowa w obrębie grupy zasobów. Musi składać się z co najmniej 3 znaków.  Dozwolone są dowolne znaki alfanumeryczne.

    * **Ścieżka pliku**  
        Określ ścieżkę pliku, na podstawie której zostanie utworzona ścieżka eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania woluminu oraz uzyskiwania do niego dostępu.   
        Miejsce docelowe instalacji to punkt końcowy adresu IP usługi NFS. Ta wartość jest generowana automatycznie.    
        Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki („-”). Musi mieć długość od 16 do 40 znaków.  

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  
        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskiwać dostęp do woluminu. W wybranej sieci wirtualnej musi być skonfigurowana usługa Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z sieci wirtualnej znajdującej się w tej samej lokalizacji co wolumin.   

    ![Nowy wolumin](../media/azure-netapp-files/azure-netapp-files-new-volume.png)

4.  Kliknij przycisk **OK**. 
 
Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="next-steps"></a>Następne kroki  
[Konfigurowanie zasad eksportu dla woluminu (opcjonalnie)](azure-netapp-files-configure-export-policy.md)

