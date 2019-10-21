---
title: Konfigurowanie zasad eksportowania dla woluminu NFS przy użyciu Azure NetApp Files | Microsoft Docs
description: Opisuje sposób konfigurowania zasad eksportu w celu kontrolowania dostępu do woluminu NFS przy użyciu Azure NetApp Files
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d323bd0b9684cfe4930d8c779a6728fcfd3836fb
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674928"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurowanie zasad eksportu dla woluminu NFS

Masz możliwość opcjonalnego skonfigurowania zasad eksportu w celu kontrolowania dostępu do woluminu usługi Azure NetApp Files. Zasady eksportowania Azure NetApp Files obsługują tylko woluminy NFS.  Obsługiwane są zarówno NFSv3, jak i NFSv4. 

## <a name="steps"></a>Kroki 

1.  Kliknij pozycję **Eksportuj zasady** w okienku nawigacji Azure NetApp Files. 

2.  Podaj informacje w następujących polach, aby utworzyć regułę zasad eksportu:   
    *  **Indeks**   
        Określ numer indeksu dla tej reguły.  
        Zasady eksportu mogą zawierać maksymalnie pięć reguł. Reguły są oceniane zgodnie z kolejnością numerów indeksu. Reguły o niższych numerach indeksu są oceniane w pierwszej kolejności. Na przykład reguła o numerze indeksu 1 zostanie oceniona przed regułą o numerze indeksu 2. 

    * **Dozwoleni klienci**   
        Określ wartość w jednym z następujących formatów:  
        * Adres IPv4, na przykład `10.1.12.24`. 
        * Adres IPv4 z maską podsieci wyrażoną za pomocą liczby bitów, na przykład `10.1.12.10/4`.

    * **Dostęp**  
        Wybierz jeden z następujących typów dostępu:  
        * Brak dostępu 
        * Odczyt i zapis
        * Tylko do odczytu

    ![Zasady eksportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Następne kroki 
* [Zarządzanie woluminami](azure-netapp-files-manage-volumes.md)
* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Zarządzanie migawkami](azure-netapp-files-manage-snapshots.md)
