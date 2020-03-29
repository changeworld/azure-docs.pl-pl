---
title: Tworzenie plików NetApp platformy Azure za pomocą interfejsu API REST | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak rozpocząć korzystanie z interfejsu API REST plików NetApp platformy Azure.
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
ms.date: 05/17/2019
ms.author: b-juche
ms.openlocfilehash: 996fbcc7c3c9af0da9160216785ecd54840660e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65957041"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Tworzenie plików NetApp platformy Azure za pomocą interfejsu API REST 

Interfejs API REST dla usługi Azure NetApp Files definiuje operacje HTTP względem zasobów, takich jak konto NetApp, pula pojemności, woluminy i migawki. Ten artykuł ułatwia rozpoczęcie korzystania z interfejsu API REST plików NetApp platformy Azure.

## <a name="azure-netapp-files-rest-api-specification"></a>Specyfikacja interfejsu API interfejsu REST plików NetApp usługi Azure

Specyfikacja interfejsu API REST dla plików NetApp platformy Azure jest publikowana za pośrednictwem [usługi GitHub:](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Dostęp do interfejsu API REST plików NetApp platformy Azure  

1. [Zainstaluj interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) jeśli jeszcze tego nie zrobiono.
2. Tworzenie jednostki usługi w usłudze Azure Active Directory (Azure AD):
   1. Sprawdź, czy masz [wystarczające uprawnienia](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

   1. Wprowadź następujące polecenie w interfejsie wiersza polecenia platformy Azure:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      Dane wyjściowe polecenia są podobne do następującego przykładu:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Zachowaj wyjście polecenia.  Będziesz potrzebował `appId`, `password`i `tenant` wartości. 

3. Poproś o token dostępu OAuth:

    Przykłady w tym artykule użyć cURL.  Można również użyć różnych narzędzi API, takich jak [Listonosz](https://www.getpostman.com/), [Bezsenność](https://insomnia.rest/)i [Łapa](https://paw.cloud/).  

    Zastąp zmienne w poniższym przykładzie na dane wyjściowe polecenia z kroku 2 powyżej. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    Dane wyjściowe udostępnia token dostępu podobny do następującego przykładu:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Wyświetlany token jest ważny przez 3600 sekund. Następnie należy zażądać nowego tokenu. 
    Zapisz token w edytorze tekstu.  Będziesz go potrzebować do następnego kroku.

4. Wyślij wywołanie testowe i dołącz token, aby sprawdzić poprawność dostępu do interfejsu API REST:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Przykłady przy użyciu interfejsu API  

W tym artykule użyto następującego adresu URL dla planu bazowego żądań. Ten adres URL wskazuje katalog główny obszaru nazw plików Usługi Azure NetApp. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Należy zastąpić `subID` i `resourceGroups` wartości w poniższych przykładach z własnych wartości. 

### <a name="get-request-examples"></a>Przykłady żądań GET

Żądanie GET służy do wykonywania zapytań obiektów plików NetApp usługi Azure w ramach subskrypcji, jak pokazują poniższe przykłady: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Przykłady żądań PUT

Żądanie PUT służy do tworzenia nowych obiektów w usłudze Azure NetApp Files, jak pokazano w poniższych przykładach. Treść żądania PUT może zawierać dane sformatowane json dla zmian lub można określić plik do odczytu. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Przykłady JSON

W poniższym przykładzie pokazano, jak utworzyć konto NetApp:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

W poniższym przykładzie pokazano, jak utworzyć pulę pojemności: 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

W poniższym przykładzie pokazano, jak utworzyć nowy wolumin: 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

W poniższym przykładzie pokazano, jak utworzyć migawkę woluminu: 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> Należy określić `fileSystemId` do tworzenia migawki.  Można uzyskać `fileSystemId` wartość z żądaniem GET do woluminu. 

## <a name="next-steps"></a>Następne kroki

[Zobacz odwołanie interfejsu API interfejsu REST plików NetApp platformy Azure](https://docs.microsoft.com/rest/api/netapp/)
