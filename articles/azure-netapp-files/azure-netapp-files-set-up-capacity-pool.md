---
title: Konfigurowanie puli pojemności na potrzeby usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania puli pojemności, w której można następnie tworzyć woluminy.
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
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60691102"
---
# <a name="set-up-a-capacity-pool"></a>Konfigurowanie puli pojemności

Skonfigurowanie puli pojemności umożliwia tworzenie woluminów w tej puli.  

## <a name="before-you-begin"></a>Przed rozpoczęciem 

Potrzebujesz utworzonego konta usługi NetApp.   

[Tworzenie konta usługi NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Kroki 

1. Przejdź do bloku zarządzania na koncie usługi NetApp, a następnie w okienku nawigacji kliknij pozycję **Pule pojemności**.  
    
    ![Przechodzenie do puli pojemności](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Kliknij pozycję **+ Dodaj pule**, aby dodać nową pulę pojemności.   
    Zostanie wyświetlone okno Nowa pula pojemności.

3. Podaj następujące informacje dotyczące nowej puli pojemności:  
   * **Nazwa**  
     Określ nazwę puli pojemności.  
     Nazwa puli pojemności musi być unikatowa na tym koncie usługi NetApp.

   * **Poziom usługi**   
     To pole wskazuje docelową wydajność puli pojemności.  
     Określ poziom usługi dla puli pojemności: [**Premium**](azure-netapp-files-service-levels.md#Premium) lub [**Standardowa**](azure-netapp-files-service-levels.md#Standard).

   * **Rozmiar**     
     Określ rozmiar kupowanej puli pojemności.        
     Minimalny rozmiar puli pojemności to 4 TiB. Można utworzyć pulę o rozmiarze będącym wielokrotnością 4 TiB.   
      
     ![Nowa pula pojemności](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kliknij przycisk **OK**.

## <a name="next-steps"></a>Kolejne kroki 

- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- Informacje o różnych poziomach usługi można uzyskać na [stronie z cennikiem dla usługi Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
