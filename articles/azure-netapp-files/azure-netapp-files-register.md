---
title: Rejestrowanie się w usłudze Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano sposób rejestrowania się w celu korzystania z plików NetApp usługi Azure.
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
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274062"
---
# <a name="register-for-azure-netapp-files"></a>Rejestrowanie w usłudze Azure NetApp Files

> [!IMPORTANT] 
> Przed zarejestrowaniem dostawcy zasobów usługi Azure NetApp Files musisz otrzymać wiadomość e-mail od zespołu plików NetApp platformy Azure z potwierdzeniem przyznania ci dostępu do usługi. 

W tym artykule dowiesz się, jak zarejestrować się w usłudze Azure NetApp Files, aby rozpocząć korzystanie z usługi.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Prześlij żądanie listy oczekujących na dostęp do usługi

1. Prześlij żądanie listy oczekujących na dostęp do usługi Azure NetApp Files za pośrednictwem [strony przesyłania listy oczekujących plików Azure NetApp](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Rejestracja listy oczekujących nie gwarantuje natychmiastowego dostępu do usługi. 

2. Przed kontynuowaniem wykonywania innych zadań należy odczekać oficjalną wiadomość e-mail z potwierdzeniem od zespołu plików usługi Azure NetApp Files. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Rejestrowanie dostawcy zasobów usługi NetApp

Aby korzystać z usługi, należy zarejestrować dostawcę zasobów platformy Azure dla usługi Azure NetApp Files.

> [!NOTE] 
> Będziesz mógł pomyślnie zarejestrować dostawcę zasobów NetApp, nawet bez uzyskania dostępu do usługi. Jednak bez autoryzacji dostępu wszelkie żądania portalu platformy Azure lub interfejsu API w celu utworzenia konta NetApp lub innego zasobu usługi Azure NetApp Files zostaną odrzucone z następującym błędem:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. W witrynie Azure Portal kliknij ikonę usługi Azure Cloud Shell w prawym górnym rogu:

      ![Ikona usługi Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Jeśli na swoim koncie platformy Azure masz wiele subskrypcji, wybierz tę, która została umieszczona na liście dozwolonych dla usługi Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. W konsoli usługi Azure Cloud Shell wprowadź następujące polecenie, aby sprawdzić, czy subskrypcja została umieszczona na liście dozwolonych:
    
        az feature list | grep NetApp

   Dane wyjściowe polecenia są podobne do następujących:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` to identyfikator Twojej subskrypcji.

    Jeśli nie widzisz nazwy `Microsoft.NetApp/ANFGA`funkcji, nie masz dostępu do usługi. Zatrzymaj się na tym kroku. Postępuj zgodnie z instrukcjami w [Prześlij żądanie listy oczekujących na dostęp](#waitlist) do usługi, aby zażądać dostępu do usługi przed kontynuowaniem. 

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


## <a name="next-steps"></a>Następne kroki

[Tworzenie konta usługi NetApp](azure-netapp-files-create-netapp-account.md)