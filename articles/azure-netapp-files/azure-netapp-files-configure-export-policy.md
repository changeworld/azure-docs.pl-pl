---
title: Konfigurowanie zasad eksportu dla woluminu systemu plików NFS przy użyciu usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania zasad eksportu do kontrolowania dostępu do woluminu systemu plików NFS przy użyciu usługi Azure Files NetApp
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
ms.date: 03/20/2019
ms.author: b-juche
ms.openlocfilehash: 8cda5921a1aec86d28beabbd9cea5b07a203a0e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61086173"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurowanie zasad eksportu dla woluminu NFS

Masz możliwość opcjonalnego skonfigurowania zasad eksportu w celu kontrolowania dostępu do woluminu usługi Azure NetApp Files. Eksportowanie zasad jest obsługiwane tylko woluminy systemu plików NFS. 

## <a name="steps"></a>Kroki 

1.  Kliknij blok **Tworzenie zasad eksportu** w bloku Zarządzanie woluminem. 

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

    * **Protokoły**   
        Określ, jaki protokół będzie używany przez zasady eksportu.   
        Obecnie zasady eksportu plików NetApp Azure obsługuje tylko NFSv3.

    ![Zasady eksportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Kolejne kroki 
* [Zarządzanie woluminami](azure-netapp-files-manage-volumes.md)
* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Zarządzanie migawkami](azure-netapp-files-manage-snapshots.md)
