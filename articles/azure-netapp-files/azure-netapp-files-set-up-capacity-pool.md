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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 0e9203f5b4e2a9043e242b804c82017cf6fc3ee1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010807"
---
# <a name="set-up-a-capacity-pool"></a>Konfigurowanie puli pojemności
Skonfigurowanie puli pojemności umożliwia tworzenie woluminów w tej puli.  

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Potrzebujesz utworzonego konta usługi NetApp.   

[Tworzenie konta usługi NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Kroki 

1. Przejdź do bloku zarządzania na koncie usługi NetApp, a następnie w okienku nawigacji wybierz pozycję **Pule pojemności**.

2. Kliknij pozycję **+ Dodaj pule**, aby dodać nową pulę pojemności.   
    Zostanie wyświetlone okno Nowa pula pojemności.

3. Podaj następujące informacje dotyczące nowej puli pojemności:  
  * **Nazwa**  
    Określ nazwę puli pojemności.  
    Nazwa puli pojemności musi być unikatowa na tym koncie usługi NetApp.

  * **Poziom usługi**   
    To pole wskazuje docelową wydajność puli pojemności.  
    Obecnie jest dostępny tylko poziom usługi Premium. 

  *  **Rozmiar**     
      Określ rozmiar kupowanej puli pojemności.        
      Minimalny rozmiar puli pojemności to 4 TiB. Można utworzyć pulę o rozmiarze będącym wielokrotnością 4 TiB.   
      
      ![Nowa pula pojemności](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki 

1. [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
2. [Konfigurowanie zasad eksportu dla woluminu (opcjonalnie)](azure-netapp-files-configure-export-policy.md)

