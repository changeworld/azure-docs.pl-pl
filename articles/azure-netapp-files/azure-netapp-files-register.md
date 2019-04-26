---
title: Rejestrowanie się w usłudze Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano, jak przesłać prośbę, aby zarejestrować się w usłudze Azure NetApp Files.
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
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 86c016a5dbcc0d78378e59bc6b3606ddf2c54f64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452783"
---
# <a name="register-for-azure-netapp-files"></a>Rejestrowanie w usłudze Azure NetApp Files
Przed rozpoczęciem korzystania z usługi Azure NetApp Files należy przesłać prośbę o zarejestrowanie w usłudze Azure NetApp Files.  Po oficjalnym zarejestrowaniu można zarejestrować się, aby korzystać z usługi.

## <a name="request-to-enroll-in-the-service"></a>Prośba o zarejestrowanie w usłudze
Musisz uczestniczyć w programie podglądu publicznego i znajdować się na liście dozwolonych, aby móc uzyskiwać dostęp do dostawcy zasobów Microsoft.NetApp. Szczegółowe informacje na temat dołączania do programu podglądu publicznego można znaleźć na [stronie rejestracji w podglądzie publicznym usługi Azure NetApp Files](https://aka.ms/nfspublicpreview). 


## <a name="register-the-netapp-resource-provider"></a>Rejestrowanie dostawcy zasobów usługi NetApp

Aby korzystać z usługi, należy zarejestrować dostawcę zasobów platformy Azure dla usługi Azure NetApp Files. 

1. W witrynie Azure Portal kliknij ikonę usługi Azure Cloud Shell w prawym górnym rogu:

      ![Ikona usługi Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Jeśli na swoim koncie platformy Azure masz wiele subskrypcji, wybierz tę, która została umieszczona na liście dozwolonych dla usługi Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. W konsoli usługi Azure Cloud Shell wprowadź następujące polecenie, aby sprawdzić, czy subskrypcja została umieszczona na liście dozwolonych:
    
        az feature list | grep NetApp

   Dane wyjściowe polecenia są podobne do następujących:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` to identyfikator Twojej subskrypcji.

4. W konsoli usługi Azure Cloud Shell wprowadź następujące polecenie, aby zarejestrować dostawcę zasobów platformy Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   Parametr `--wait` instruuje konsolę, aby zaczekać na ukończenie rejestracji. Proces rejestracji może trochę potrwać.

5. W konsoli usługi Azure Cloud Shell wprowadź następujące polecenie, aby sprawdzić, czy dostawca zasobów platformy Azure został zarejestrowany: 
    
        az provider show --namespace Microsoft.NetApp

   Dane wyjściowe polecenia są podobne do następujących:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` to identyfikator Twojej subskrypcji.  Wartość parametru `state` wskazuje stan `Registered`.

6. W witrynie Azure Portal kliknij blok **Subskrypcje**.
7. W bloku Subskrypcje kliknij identyfikator subskrypcji. 
8. W ustawieniach subskrypcji kliknij pozycję **Dostawcy zasobów**, aby sprawdzić, czy dostawca Microsoft.NetApp Provider wskazuje stan Zarejestrowane: 

      ![Zarejestrowany dostawca Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Kolejne kroki  

[Tworzenie konta usługi NetApp](azure-netapp-files-create-netapp-account.md)