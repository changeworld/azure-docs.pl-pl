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
ms.topic: get-started-article
ms.date: 11/13/2018
ms.author: b-juche
ms.openlocfilehash: fc8ec5c3bbe0157f5737e2eb757478357aa6e867
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414306"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegowanie podsieci do usługi Azure NetApp Files 

Podsieć należy delegować do usługi Azure NetApp Files.   Podczas tworzenia woluminu należy określić delegowaną podsieć.

## <a name="about-this-task"></a>Informacje dotyczące tego zadania
* Kreator tworzenia nowej podsieci przyjmuje wartość domyślną maski sieci /24, która zapewnia 251 dostępnych adresów IP. Użycie maski sieci /28, która zapewnia 16 adresów IP, jest wystarczająca dla tej usługi.
* W delegowanej podsieci nie można wyznaczyć sieciowej grupy zabezpieczeń ani punktu końcowego usługi. Jeśli się to zrobi, delegowanie podsieci nie powiedzie się.
* W każdej sieci wirtualnej Azure Virtual Network (VNet) można delegować tylko jedną podsieć do usługi Azure NetApp Files.
* Dostęp do woluminu z równorzędnej sieci wirtualnej nie jest obecnie obsługiwany.

## <a name="steps"></a>Kroki 
1.  Przejdź do bloku **Sieci wirtualne** w witrynie Azure Portal i wybierz sieć wirtualną, której chcesz użyć na potrzeby usługi Azure NetApp Files.    

1. Wybierz pozycję **Podsieci** w bloku Sieć wirtualna, a następnie kliknij przycisk **+ Podsieć**. 

1. Utwórz nową podsieć na potrzeby usługi Azure NetApp Files, wypełniając następujące wymagane pola na stronie Dodawanie podsieci:
    * **Nazwa**: podaj nazwę podsieci.
    * **Zakres adresów**: określ zakres adresów IP.
    * **Delegowanie podsieci**: wybierz pozycję **Microsoft.NetApp/woluminy**. 

      ![Delegowanie podsieci](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Podsieć można również utworzyć i delegować podczas [tworzenia woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Następne kroki  
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


