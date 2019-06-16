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
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 25cae58663f6fa7ef27995c10509eb33e49dd4c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522815"
---
# <a name="create-a-netapp-account"></a>Tworzenie konta usługi NetApp
Utworzenie konta usługi NetApp umożliwia skonfigurowanie puli pojemności, w której następnie można utworzyć wolumin. Nowe konto usługi NetApp możesz utworzyć w bloku usługi Azure NetApp Files.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Musi mieć otrzymasz wiadomość e-mail od zespołu usługi Azure Files NetApp potwierdzenie, że musisz mieć przyznany dostęp do usługi. Zobacz [wniosek o liście oczekujących do uzyskiwania dostępu do usługi](azure-netapp-files-register.md#waitlist).

Należy również zarejestrowano subskrypcją usługi za pomocą dostawcy zasobów NetApp. Zobacz [Zarejestruj dostawcę zasobów NetApp](azure-netapp-files-register.md#resource-provider).

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
   * **Subskrypcja**  
     Wybierz subskrypcję z listy istniejących subskrypcji.
   * **Grupa zasobów**   
     Użyj istniejącej grupy zasobów lub utwórz nową.
   * **Lokalizacja**  
     Wybierz region, w którym będzie znajdować się konto i jego zasoby podrzędne.  

     ![Nowe konto usługi NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Kliknij pozycję **Utwórz**.     
   Utworzone konto usługi NetApp znajduje się teraz w bloku usługi Azure NetApp Files. 

> [!NOTE] 
> Jeśli użytkownik nie udzielono dostępu do usługi Azure NetApp Files, zostanie wyświetlony następujący błąd podczas próby utworzenia pierwszego konta NetApp:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Kolejne kroki  

[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)

