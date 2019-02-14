---
title: Tworzenie konta usługi NetApp i uzyskiwanie dostępu do usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano, jak uzyskać dostęp do usługi Azure NetApp Files i utworzyć konto usługi NetApp, co umożliwia skonfigurowanie puli pojemności i utworzenie woluminu.
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
ms.openlocfilehash: 47b9d25f8db2241bb578528780e28f43d56371e5
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963084"
---
# <a name="create-a-netapp-account"></a>Tworzenie konta usługi NetApp
Utworzenie konta usługi NetApp umożliwia skonfigurowanie puli pojemności, w której następnie można utworzyć wolumin. Nowe konto usługi NetApp możesz utworzyć w bloku usługi Azure NetApp Files.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Należy zarejestrować subskrypcję na potrzeby używania dostawcy zasobów usługi NetApp i funkcji w publicznej wersji zapoznawczej.

[Rejestrowanie w usłudze Azure NetApp Files](azure-netapp-files-register.md)

## <a name="steps"></a>Kroki 

1. Zaloguj się do Portalu Azure. 
2. Przejdź do bloku usługi Azure NetApp Files za pomocą jednej z następujących metod:  
  * Wyszukaj usługę **Azure NetApp Files** w polu wyszukiwania witryny Azure Portal.  
  * Kliknij przycisk **Wszystkie usługi** w obszarze nawigacji, a następnie wprowadź nazwę usługi Azure NetApp Files w filtrze.  

  Możesz dodać blok usługi Azure NetApp Files do ulubionych, klikając ikonę gwiazdki obok tej usługi. 

3. Kliknij pozycję **+ Dodaj**, aby utworzyć nowe konto usługi NetApp.  
  Zostanie wyświetlone okno Nowe konto usługi NetApp.  

4. Podaj następujące informacje dotyczące konta usługi NetApp: 
  * **Nazwa konta**  
    Określ unikatową nazwę subskrypcji.
  *  **Subskrypcja**  
    Wybierz subskrypcję z listy istniejących subskrypcji.
  * **Grupa zasobów**   
    Użyj istniejącej grupy zasobów lub utwórz nową.
  * **Lokalizacja**  
    Wybierz region, w którym będzie znajdować się konto i jego zasoby podrzędne.  

    ![Nowe konto usługi NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Kliknij pozycję **Utwórz**.     
  Utworzone konto usługi NetApp znajduje się teraz w bloku usługi Azure NetApp Files. 

## <a name="next-steps"></a>Następne kroki  

[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)

