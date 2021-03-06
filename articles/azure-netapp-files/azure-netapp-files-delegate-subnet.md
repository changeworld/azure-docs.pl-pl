---
title: Delegowanie podsieci do usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano, jak delegować podsieć do usługi Azure NetApp Files.
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
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054460"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegowanie podsieci do usługi Azure NetApp Files 

Podsieć należy delegować do usługi Azure NetApp Files.   Podczas tworzenia woluminu należy określić delegowaną podsieć.

## <a name="considerations"></a>Zagadnienia do rozważenia
* Kreator tworzenia nowej podsieci przyjmuje wartość domyślną maski sieci /24, która zapewnia 251 dostępnych adresów IP. Użycie maski sieci /28, która zapewnia 16 adresów IP, jest wystarczająca dla tej usługi.
* W każdej sieci wirtualnej Azure Virtual Network (VNet) można delegować tylko jedną podsieć do usługi Azure NetApp Files.   
   Platforma Azure umożliwia tworzenie wielu delegowanych podsieci w sieci wirtualnej.  Jednak wszelkie próby utworzenia nowego woluminu zakończy się niepowodzeniem, jeśli używasz więcej niż jednej podsieci delegowanej.
* W delegowanej podsieci nie można wyznaczyć sieciowej grupy zabezpieczeń ani punktu końcowego usługi. Jeśli się to zrobi, delegowanie podsieci nie powiedzie się.
* Dostęp do woluminu z globalnie równorzędnej sieci wirtualnej nie jest obecnie obsługiwany.
* Tworzenie [tras niestandardowych zdefiniowanych przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) w podsieciach maszyn wirtualnych z prefiksem adresu (miejscem docelowym) do podsieci delegowanej do plików NetApp platformy Azure jest nieobsługiwane. W ten sposób wpłynie na łączność maszyn wirtualnych.

## <a name="steps"></a>Kroki 
1.  Przejdź do bloku **Sieci wirtualne** w witrynie Azure Portal i wybierz sieć wirtualną, której chcesz użyć na potrzeby usługi Azure NetApp Files.    

1. Wybierz pozycję **Podsieci** w bloku Sieć wirtualna, a następnie kliknij przycisk **+ Podsieć**. 

1. Utwórz nową podsieć na potrzeby usługi Azure NetApp Files, wypełniając następujące wymagane pola na stronie Dodawanie podsieci:
    * **Nazwa**: Określ nazwę podsieci.
    * **Zakres adresów**: Określ zakres adresów IP.
    * **Delegowanie podsieci:** wybierz **microsoft.NetApp/woluminy**. 

      ![Delegowanie podsieci](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Podsieć można również utworzyć i delegować podczas [tworzenia woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Następne kroki  
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


