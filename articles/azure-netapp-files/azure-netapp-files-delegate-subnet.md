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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: fd8e380ad68b86b9ffd0f1e40efde8bdadfb19c5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763308"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegowanie podsieci do usługi Azure NetApp Files 

Podsieć należy delegować do usługi Azure NetApp Files.   Podczas tworzenia woluminu należy określić delegowaną podsieć.

## <a name="considerations"></a>Zagadnienia do rozważenia
* Kreator tworzenia nowej podsieci przyjmuje wartość domyślną maski sieci /24, która zapewnia 251 dostępnych adresów IP. Użycie maski sieci /28, która zapewnia 16 adresów IP, jest wystarczająca dla tej usługi.
* W każdej sieci wirtualnej Azure Virtual Network można delegować tylko jedną podsieć do usługi Azure NetApp Files.
* W delegowanej podsieci nie można wyznaczyć sieciowej grupy zabezpieczeń ani punktu końcowego usługi. Jeśli się to zrobi, delegowanie podsieci nie powiedzie się.
* Dostęp do woluminu z globalnie równorzędnej sieci wirtualnej nie jest obecnie obsługiwane.
* Tworzenie [zdefiniowanych przez użytkownika trasy niestandardowe](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) w podsieci maszyny Wirtualnej przy użyciu adresu prefiksu (docelowy) do podsieci delegować domenę do usługi Azure Files NetApp nie jest obsługiwany. To będzie miało wpływ na łączność maszyn wirtualnych.

## <a name="steps"></a>Kroki 
1.  Przejdź do bloku **Sieci wirtualne** w witrynie Azure Portal i wybierz sieć wirtualną, której chcesz użyć na potrzeby usługi Azure NetApp Files.    

1. Wybierz pozycję **Podsieci** w bloku Sieć wirtualna, a następnie kliknij przycisk **+ Podsieć**. 

1. Utwórz nową podsieć na potrzeby usługi Azure NetApp Files, wypełniając następujące wymagane pola na stronie Dodawanie podsieci:
    * **Nazwa**: podaj nazwę podsieci.
    * **Zakres adresów**: określ zakres adresów IP.
    * **Delegowanie podsieci**: wybierz pozycję **Microsoft.NetApp/woluminy**. 

      ![Delegowanie podsieci](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Podsieć można również utworzyć i delegować podczas [tworzenia woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Kolejne kroki  
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


