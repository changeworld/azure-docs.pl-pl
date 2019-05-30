---
title: Rejestrowanie się w usłudze Azure NetApp Files | Microsoft Docs
description: Opisuje sposób zarejestrować, aby używać usługi Azure Files NetApp.
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
ms.openlocfilehash: fbe0b82008d7b15332c4e2cd62c49c611f20fe89
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794711"
---
# <a name="register-for-azure-netapp-files"></a>Rejestrowanie w usłudze Azure NetApp Files

> [!IMPORTANT] 
> Przed zarejestrowaniem dostawcy zasobów usługi Azure Files NetApp, musi mieć otrzymasz wiadomość e-mail od zespołu usługi Azure Files NetApp potwierdzenie, że musisz mieć przyznany dostęp do usługi. 

Ten artykuł zawiera informacje o sposobie rejestrowania dla usługi Azure Files NetApp, tak aby mogli rozpocząć korzystanie z usługi.

## <a name="waitlist"></a>Wniosek o liście oczekujących do uzyskiwania dostępu do usługi

1. Wniosek o liście oczekujących do uzyskiwania dostępu do usługi Azure NetApp Files za pośrednictwem [stronę przesyłania na liście oczekujących plików NetApp Azure](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Rejestracja na liście oczekujących nie gwarantuje usługi bezpośredniego dostępu. 

2. Poczekaj wiadomość e-mail z potwierdzeniem oficjalne, od zespołu usługi Azure Files NetApp, przed kontynuowaniem pracy z innymi zadaniami. 

## <a name="resource-provider"></a>Zarejestruj dostawcę zasobów NetApp

Aby korzystać z usługi, należy zarejestrować dostawcę zasobów platformy Azure dla usługi Azure NetApp Files.

> [!NOTE] 
> Będzie można pomyślnie zarejestrować dostawcę zasobów NetApp, nawet jeśli nie zostanie im przyznany dostęp do usługi. Jednak bez autoryzacji dostępu do witryny Azure portal ani żądania interfejsu API, aby utworzyć konto NetApp lub innego zasobu usługi Azure Files NetApp zostanie odrzucone z powodu następującego błędu:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


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

    Jeśli nie widać nazwy funkcji `Microsoft.NetApp/publicPreviewADC`, nie masz dostępu do usługi. Zatrzymaj, w tym kroku. Postępuj zgodnie z instrukcjami wyświetlanymi w [wniosek o liście oczekujących do uzyskiwania dostępu do usługi](#waitlist) Aby zażądać dostępu usługi przed kontynuowaniem. 

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