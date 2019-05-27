---
title: Opracowywanie zawartości dla plików NetApp platformy Azure przy użyciu interfejsu API REST | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak rozpocząć pracę przy użyciu interfejsu API REST plików NetApp platformy Azure.
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
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957041"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Opracowywanie zawartości dla plików NetApp platformy Azure przy użyciu interfejsu API REST 

Interfejs API REST usługi Azure NetApp Files definiuje operacji HTTP wykonywanych względem zasobów, takich jak konta NetApp, pojemność puli, woluminów i migawek. Ten artykuł ułatwia rozpoczynanie pracy z usługą przy użyciu interfejsu API REST plików NetApp platformy Azure.

## <a name="azure-netapp-files-rest-api-specification"></a>Usługa Azure specyfikacji interfejsu API REST plików NetApp

Specyfikacja interfejsu API REST dla usługi Azure Files NetApp zostanie opublikowana przy użyciu [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Dostęp do plików platformy Azure NetApp interfejsu API REST  

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Jeśli nie zostało to jeszcze zrobione.
2. Tworzenie jednostki usługi w usłudze Active Directory Azure (Azure AD):
   1. Sprawdź, czy [wystarczające uprawnienia](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

   1. Wprowadź następujące polecenie w interfejsie wiersza polecenia platformy Azure:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      Dane wyjściowe polecenia będą podobne do poniższego przykładu:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Zachowaj dane wyjściowe polecenia.  Konieczne będzie `appId`, `password`, i `tenant` wartości. 

3. Żądanie tokenu dostępu OAuth:

    W przykładach w tym artykule używane jest narzędzie cURL.  Umożliwia także różne narzędzia interfejsów API takich jak [Postman](https://www.getpostman.com/), [o braku usypiania](https://insomnia.rest/), i [Paw](https://paw.cloud/).  

    Zastąp zmienne w poniższym przykładzie dane wyjściowe polecenia z kroku 2 powyżej. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    Dane wyjściowe zawiera token dostępu jest podobny do poniższego przykładu:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Wyświetlony token jest ważny przez 3600 sekund. Po tym musisz uzyskać nowy token. 
    Zapisz ten token do edytora tekstów.  Należy ją do kolejnego kroku.

4. Wyślij rozmowy badanie i zawierać token, aby zweryfikować Twojego dostępu do interfejsu API REST:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Przykłady korzystania z interfejsu API  

W tym artykule używa następującego adresu URL dla linii bazowej żądań. Ten adres URL wskazuje do katalogu głównego przestrzeni nazw usługi Azure Files NetApp. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Należy zastąpić `subID` i `resourceGroups` wartości w poniższych przykładach własnymi wartościami. 

### <a name="get-request-examples"></a>Pobierz przykłady żądania

W poniższych przykładach pokazano używane żądanie GET do kwerendy obiekty usługi Azure Files NetApp w ramach subskrypcji: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Umieść przykładowe żądanie

Żądanie PUT umożliwia tworzenie nowych obiektów w usłudze Azure Files NetApp, jak w poniższych przykładach pokazano. Treść żądania PUT mogą obejmować dane formatu JSON, aby zmiany lub można określić plik do odczytu. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Przykłady JSON

Poniższy przykład pokazuje, jak utworzyć konto NetApp:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

Poniższy przykład pokazuje, jak utworzyć pulę pojemność: 

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

Poniższy przykład pokazuje, jak utworzyć nowy wolumin: 

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

Poniższy przykład pokazuje, jak można utworzyć migawki woluminu: 

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
> Należy określić `fileSystemId` dla tworzenia migawki.  Możesz uzyskać `fileSystemId` wartość żądanie GET do woluminu. 

## <a name="next-steps"></a>Kolejne kroki

[Zobacz odwołania do interfejsu API REST plików NetApp Azure](https://docs.microsoft.com/rest/api/netapp/)
