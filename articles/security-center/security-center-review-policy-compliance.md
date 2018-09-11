---
title: Przeglądanie zgodności z zasadami usługi Security Center za pomocą interfejsu API REST platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Przejrzyj bieżące zgodność z zasadami usługi Security Center za pomocą interfejsów API REST platformy Azure.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 1443486590859aac5591aff2ab0551bed9228d7b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301753"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Zgodność z zasadami Centrum zabezpieczeń przeglądu przy użyciu interfejsów API REST

Usługa Security Center okresowo sprawdza poprawność swoich zasobów platformy Azure względem zasad zabezpieczeń zdefiniowane. Usługa Security Center zapewnia także interfejsu API REST, który umożliwia sprawdzenie zgodności z własnych aplikacji; można bezpośrednio zapytań do usługi lub Importuj wyniki JSON do innych aplikacji. 

Tutaj dowiesz się, można pobrać bieżącego zestawu zaleceń z wszystkich zasobów platformy Azure skojarzone z subskrypcją.

Aby pobrać bieżący zestaw zaleceń:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Żądanie kompilacji  

`{subscription-id}` Parametr jest wymagany i może zawierać identyfikator subskrypcji dla subskrypcji platformy Azure, określanie zasad. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

`api-version` Parametr jest wymagany. W tej chwili te punkty końcowe są obsługiwane tylko w przypadku `api-version=2015-06-01-preview`. 

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Typ zawartości:*|Wymagany. Ustaw `application/json`.|  
|*Autoryzacja:*|Wymagany. Ustawić prawidłową `Bearer` [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Odpowiedź  

Zwróciła kod stanu 200 (OK) pomyślnej odpowiedzi, który zawiera listę zalecanych zadań w celu zabezpieczenia zasobów platformy Azure.

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

Każdy element na **wartość** reprezentuje zalecenia:

|Właściwość Response|Opis|
|----------------|----------|
|**state** | Wskazuje, czy to zalecenie `active` lub `resolved`. |
|**CreationTimeUtc** | Data i godzina w formacie UTC, które są wyświetlane, gdy zalecenie zostało utworzone. |
|**lastStateChangeUtc** | Data i godzina w UTV ostatniej zmiany stanu, jeśli istnieje. |
|**securityTaskParameters** | Szczegóły zalecenia; właściwości różnią się zgodnie z zaleceniem bazowego. |
||
  
Obecnie obsługiwane zaleceń, zobacz [zalecenia dotyczące implementowania zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Inne kody stanu wskazują błędy. W takich przypadkach obiekt odpowiedzi zawiera opis wyjaśniający, dlaczego żądanie nie powiodło się.

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

Ta odpowiedź pokazuje zaleceń dwóch; Każdy element na liście odnosi się do konkretne zalecenie. Pierwsze zaleca się szyfrowanie magazynu w maszynie wirtualnej systemu Linux, a druga sugeruje, że inspekcja została włączona dla programu SQL server.

Zalecenia różnić w zależności od zasad które zostało włączone. Aby uzyskać więcej informacji, w tym aktualnie dostępne zalecenia, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Zobacz także  
- [Ustawianie zasad zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Usługa Azure interfejs API REST dostawcy zasobów zabezpieczeń](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Wprowadzenie do interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/)   
- [Moduł programu PowerShell usługi Azure Security Center](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
