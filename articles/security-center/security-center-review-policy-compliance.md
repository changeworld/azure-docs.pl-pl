---
title: Przegląd zgodności zasad Centrum zabezpieczeń z interfejsu API REST Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsów API REST usługi Azure do przeglądania bieżącej zgodności z zasadami Centrum zabezpieczeń.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 6c6764eec59633f0bdd0fa396c1581117a0c1e1d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Centrum zabezpieczeń przeglądu zasad zgodności przy użyciu interfejsów API REST

Centrum zabezpieczeń okresowo sprawdza poprawność zasobów platformy Azure przed zasady zabezpieczeń zdefiniowane. Centrum zabezpieczeń zawiera także interfejsu API REST, który umożliwia sprawdzenie zgodności z własnych aplikacji; można wysłać zapytania do usługi bezpośrednio lub zaimportować wyniki JSON do innych aplikacji. 

W tym miejscu możesz dowiedzieć się pobrać bieżący zestaw zaleceń wszystkie zasoby platformy Azure skojarzone z subskrypcją.

Aby pobrać bieżący zestaw zaleceń:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Żądanie kompilacji  

`{subscription-id}` Parametr jest wymagany i musi zawierać identyfikator subskrypcji dla subskrypcji platformy Azure, określanie zasad. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

`api-version` Parametr jest wymagany. W tej chwili te punkty końcowe są obsługiwane tylko w przypadku `api-version=2015-06-01-preview`. 

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Typ zawartości:*|Wymagany. Ustaw `application/json`.|  
|*Autoryzacji:*|Wymagany. Ustaw prawidłową `Bearer` [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Odpowiedź  

Kod stanu 200 (OK) jest zwracana dla pomyślnej odpowiedzi, który zawiera listę zalecanych zadań w celu zabezpieczenia zasobów platformy Azure.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

Każdy element **wartość** reprezentuje zalecenia:

|Właściwość Response|Opis|
|----------------|----------|
|**state** | Wskazuje, czy zalecenie jest `active` lub `resolved`. |
|**creationTimeUtc** | Data i godzina w formacie UTC, przedstawiający utworzenia zalecenia. |
|**lastStateChangeUtc** | Data i godzina w UTV ostatniej zmiany stanu, jeśli istnieje. |
|**securityTaskParameters** | Szczegóły zalecenia; właściwości różny w zależności od podstawowej zalecenia. |
||
  
Dla aktualnie obsługiwanych zalecenia, zobacz [zaimplementować zalecenia dotyczące zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Pozostałe kody stanu wskazują na warunki błędu. W takich przypadkach obiekt odpowiedzi zawiera opis wyjaśniający, dlaczego żądanie zawiodło.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>Przykładowa odpowiedź  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

Ta odpowiedź zawiera zalecenia dotyczące dwóch; Każdy element na liście odpowiada określonym zalecenia. Pierwszy zaleca szyfrowania magazynu w maszynie wirtualnej systemu Linux i drugą sugeruje, Włącz inspekcję dla programu SQL server.

Zalecenia różny w zależności od zasad, które zostały włączone. Aby dowiedzieć się więcej, w tym aktualnie dostępne zalecenia, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Zobacz także  
- [Ustawianie zasad zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Dostawca usługi Azure Resource zabezpieczeń interfejsu API REST](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Wprowadzenie do interfejsu API REST Azure](https://docs.microsoft.com/rest/api/azure/)   
- [Moduł programu PowerShell Centrum zabezpieczeń Azure](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
