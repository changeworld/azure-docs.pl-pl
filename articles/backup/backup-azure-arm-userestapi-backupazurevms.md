---
title: 'Usługa Azure Backup: Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu interfejsu API REST'
description: Zarządzanie operacjami kopii zapasowej przy użyciu interfejsu API REST usługi Azure VM Backup
services: backup
author: pvrk
manager: shivamg
keywords: INTERFEJS API REST; Kopia zapasowa maszyny Wirtualnej platformy Azure; Przywracanie maszyny Wirtualnej platformy Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 8a47d3cf346d7961e9f8b1c4fa615a2faa6b1da0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60646779"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu usługi Azure Backup przy użyciu interfejsu API REST

W tym artykule opisano sposób zarządzania kopiami zapasowymi maszyny wirtualnej platformy Azure przy użyciu usługi Azure Backup przy użyciu interfejsu API REST. Skonfiguruj ochronę wcześniej niechronionych maszyn wirtualnych platformy Azure po raz pierwszy, wyzwalaczy kopii zapasowej na żądanie dla chronionych maszyn wirtualnych platformy Azure i modyfikować właściwości kopii zapasowej kopii zapasowej maszyny Wirtualnej za pomocą interfejsu API REST, jak wyjaśniono poniżej.

Zapoznaj się [Utwórz magazyn](backup-azure-arm-userestapi-createorupdatevault.md) i [Tworzenie zasad](backup-azure-arm-userestapi-createorupdatepolicy.md) samouczki interfejsu API REST do tworzenia nowych magazynów i zasad.

Załóżmy, że chcesz chronić maszynę Wirtualną "testVM" w grupie zasobów "testRG" w magazynie usługi Recovery Services "testVault", istnieje w grupie zasobów "testVaultRG" z zasadami domyślnymi (o nazwie "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurowanie kopii zapasowej dla niechronionego Maszynie wirtualnej platformy Azure przy użyciu interfejsu API REST

### <a name="discover-unprotected-azure-vms"></a>Odkryj niechronionych maszyn wirtualnych platformy Azure

Po pierwsze magazyn, powinno być możliwe do identyfikowania maszyny Wirtualnej platformy Azure. Jest to wyzwalane za pomocą [operacja odświeżania](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Jest asynchroniczna *WPIS* operacji, która zapewnia, że magazyn pobiera najnowszą listę wszystkich niechronionych maszyn wirtualnych w bieżącej subskrypcji, a "buforuje" je. Gdy maszyna wirtualna jest "pamięci podręcznej", usługi Recovery services będzie można dostęp do maszyny Wirtualnej, aby chronić je.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

Identyfikator URI WPIS ma `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}` parametrów. `{fabricName}` Jest "Azure". Zgodnie z naszym przykładzie `{vaultName}` jest "testVault" i `{vaultresourceGroupName}` jest "testVaultRG". Ponieważ wszystkie wymagane parametry są podane w identyfikatorze URI, nie ma potrzeby treści oddzielne żądanie.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Responses

Operacja "Odśwież" jest [operację asynchroniczną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca ona dwie odpowiedzi: 202 (zaakceptowano), gdy inna operacja zostanie utworzony, a następnie 200 (OK), po zakończeniu tej operacji.

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|204 No Content     |         |  OK bez zawartości zwracane      |
|202 zaakceptowano     |         |     Zaakceptowane    |

##### <a name="example-responses"></a>Przykładowe odpowiedzi

Gdy *WPIS* przesłaniu żądania zwróceniem odpowiedzi 202 (zaakceptowano).

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Śledzenia wynikowych operacji przy użyciu nagłówek "Lokalizacja" przy użyciu prostego *UZYSKAĆ* polecenia

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
```

Po odnalezieniu wszystkich maszyn wirtualnych platformy Azure, polecenie GET zwraca odpowiedź 204 (Brak zawartości). Magazyn jest teraz mogły odnaleźć dowolnej maszyny Wirtualnej w ramach subskrypcji.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>Wybranie odpowiednich maszyn wirtualnych platformy Azure

 Możesz potwierdzić, że "buforowanie" jest wykonywane przez [listę wszystkich elementów podlegających](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) w ramach subskrypcji i Znajdź tę maszynę Wirtualną w odpowiedzi. [Odpowiedź tej operacji](#example-responses-1) dostarcza również informacji o jak usług Recovery services identyfikuje Maszynę wirtualną.  Po przejściu na wzorcu, możesz pominąć ten krok i bezpośrednio przejść do [włączania ochrony](#enabling-protection-for-the-azure-vm).

Ta operacja jest *UZYSKAĆ* operacji.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*UZYSKAĆ* identyfikator URI ma wszystkie wymagane parametry. Nie treści żądania dodatkowych jest wymagana.

#### <a name="responses"></a>Responses

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

##### <a name="example-responses"></a>Przykładowe odpowiedzi

Gdy *UZYSKAĆ* przesłaniu żądania zwrócił odpowiedź 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Liczba wartości w *UZYSKAĆ* odpowiedzi może mieć maksymalnie 200 dla strony. Użyj pola "nextLink", aby uzyskać adres URL dla następnego zestawu odpowiedzi.

Odpowiedź zawiera listę wszystkich niechronionych maszyn wirtualnych platformy Azure, a każdy `{value}` zawiera wszystkie informacje wymagane przez usługę Azure Recovery Service w celu skonfigurowania kopii zapasowej. Pamiętaj, aby skonfigurować kopię zapasową, `{name}` pola i `{virtualMachineId}` pole `{properties}` sekcji. Utworzyć dwie zmienne z tych wartości pola, zgodnie z poniższymi.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "vm;"+ `{name}`
- `{virtualMachineId}` jest używany później w [treść żądania](#example-request-body)

W przykładzie powyżej wartości przełożyć na:

- containerName = "iaasvmcontainer iaasvmcontainerv2; testRG; testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Włączanie ochrony maszyny wirtualnej platformy Azure

Po odpowiednich maszyn wirtualnych "buforowane" i "zidentyfikowane", wybierz zasady do ochrony. Aby dowiedzieć się więcej o istniejących zasad w magazynie, zapoznaj się [listy zasad interfejsu API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Następnie wybierz pozycję [odpowiednich zasad](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) , odwołując się do nazwy zasad. Aby utworzyć zasady, zapoznaj się [Tworzenie zasad samouczek](backup-azure-arm-userestapi-createorupdatepolicy.md). Wybrano "DefaultPolicy" w poniższym przykładzie.

Włączenie ochrony jest asynchroniczna *umieścić* operację tworzącą "chronionego elementu".

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

`{containerName}` i `{protectedItemName}` są konstruowane powyżej. `{fabricName}` Jest "Azure". W naszym przykładzie przekłada się to:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

#### <a name="create-the-request-body"></a>Tworzenie treści żądania

Aby utworzyć chroniony element, poniżej przedstawiono składniki w treści żądania.

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Właściwości zasobu ProtectedItem         |

Aby uzyskać pełną listę definicji treści żądania i inne szczegóły, zobacz [Utwórz chroniony element interfejsu API REST dokument](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Przykład treść żądania

Następującą treść żądania określa właściwości wymagane do utworzenia chronionego elementu.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}` Jest `{virtualMachineId}` wymienionych powyżej z [odpowiedzi listy elementów podlegających](#example-responses-1).

#### <a name="responses"></a>Responses

Tworzenie chronionego elementu jest [operację asynchroniczną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca ona dwie odpowiedzi: 202 (zaakceptowano), gdy inna operacja zostanie utworzony, a następnie 200 (OK), po zakończeniu tej operacji.

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 zaakceptowano     |         |     Zaakceptowane    |

##### <a name="example-responses"></a>Przykładowe odpowiedzi

Po przesłaniu *umieścić* żądania dla chronionego elementu tworzenia lub aktualizacji, początkową odpowiedź jest 202 (zaakceptowano) za pomocą nagłówek lokalizacji lub Azure async nagłówka.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Następnie śledzenia wynikowych operacji przy użyciu nagłówek lokalizacji lub nagłówek operację asynchroniczną na platformie Azure przy użyciu prostego *UZYSKAĆ* polecenia.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

Po zakończeniu tej operacji zwraca 200 (OK) z zawartością chronionego elementu w treści odpowiedzi.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Będzie to potwierdzenie, że włączeniu ochrony dla maszyny Wirtualnej, a pierwsza kopia zapasowa zostanie wyzwolone zgodnie z harmonogramem zasad.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Wyzwalanie kopii zapasowej na żądanie dla chronionych maszyn wirtualnych platformy Azure

Po skonfigurowaniu maszyny Wirtualnej platformy Azure do tworzenia kopii zapasowych kopie powinny być wykonywane zgodnie z harmonogramem zasad. Można czekać na pierwszą zaplanowaną kopią zapasową lub wyzwalacza kopii zapasowej na żądanie w dowolnym momencie. Przechowywanie kopii zapasowych na żądanie jest oddzielony od zasad tworzenia kopii zapasowej, przechowywanie i może być określony do określonej daty i godziny. Jeśli nie zostanie określony, zakłada się 30 dni od dnia wyzwalacz kopii zapasowej na żądanie.

Wyzwolenie tworzenia kopii zapasowej na żądanie jest *WPIS* operacji.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` i `{protectedItemName}` są konstruowane [powyżej](#responses-1). `{fabricName}` Jest "Azure". W naszym przykładzie przekłada się to:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>Tworzenie treści żądania

Aby wyzwolić kopii zapasowej na żądanie, poniżej przedstawiono składniki w treści żądania.

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Właściwości BackupRequestResource         |

Aby uzyskać pełną listę definicji treści żądania i inne szczegóły, zobacz [wyzwalanie tworzenia kopii zapasowych dla chronionych elementów interfejsu API REST dokumentu](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Przykład treść żądania

Następującą treść żądania określa właściwości wymagane w celu wyzwolenia kopii zapasowej dla chronionego elementu. Jeśli okres przechowywania nie jest określony, zostaną zachowane przez 30 dni od chwili wyzwalacz zadania tworzenia kopii zapasowej.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Responses

Wyzwolenie tworzenia kopii zapasowej na żądanie jest [operację asynchroniczną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca ona dwie odpowiedzi: 202 (zaakceptowano), gdy inna operacja zostanie utworzony, a następnie 200 (OK), po zakończeniu tej operacji.

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|202 zaakceptowano     |         |     Zaakceptowane    |

#### <a name="example-responses"></a>Przykładowe odpowiedzi

Po przesłaniu *WPIS* żądania do utworzenia kopii zapasowej na żądanie początkową odpowiedź jest 202 (zaakceptowano) za pomocą nagłówek lokalizacji lub Azure async nagłówka.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Następnie śledzenia wynikowych operacji przy użyciu nagłówek lokalizacji lub nagłówek operację asynchroniczną na platformie Azure przy użyciu prostego *UZYSKAĆ* polecenia.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

Po zakończeniu tej operacji zwraca 200 (OK) z Identyfikatorem zadania tworzenia kopii zapasowej wynikowe w treści odpowiedzi.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Ponieważ zadania tworzenia kopii zapasowej jest operacją wymagającą dużo czasu, musi być śledzone, zgodnie z objaśnieniem w [monitorowania zadań za pomocą interfejsu API REST dokumentu](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Modyfikowanie konfiguracji kopii zapasowej dla chronionych maszyn wirtualnych platformy Azure

### <a name="changing-the-policy-of-protection"></a>Zmiana zasad ochrony

Aby zmienić zasady za pomocą której maszyna wirtualna jest chroniona, można użyć tego samego formatu co [włączania ochrony](#enabling-protection-for-the-azure-vm). Po prostu podać identyfikator zasad w [treść żądania](#example-request-body) i prześlij żądanie. Na przykład: Aby zmienić zasady testVM z "DefaultPolicy" do "ProdPolicy", należy podać identyfikator "ProdPolicy" w treści żądania.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Odpowiedź zostanie postępuj zgodnie z tym samym formacie jak wspomniano [potrzeby włączania ochrony](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Zatrzymaj ochronę przy zachowaniu istniejących danych

Aby usunąć ochronę na chronionej maszynie Wirtualnej, ale zachować dane już kopię zapasową, Usuń zasady w treści żądania i prześlij żądanie. Po usunięciu skojarzenia z zasadami tworzenia kopii zapasowych nie jest już są wyzwalane, a żadne nowe punkty odzyskiwania są tworzone.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Odpowiedź zostanie postępuj zgodnie z tym samym formacie jak wspomniano [służącą do wyzwalania kopii zapasowej na żądanie](#example-responses-3). Wynikowe zadanie powinny być śledzone, zgodnie z objaśnieniem w [monitorowania zadań za pomocą interfejsu API REST dokumentu](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Zatrzymanie ochrony i usunięcie danych

Aby usunąć ochronę na chronionej maszynie Wirtualnej i Usuń dane kopii zapasowej, należy wykonać operację usunięcia, zgodnie z opisem [tutaj](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Zatrzymanie ochrony i usuwanie danych jest *Usuń* operacji.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

`{containerName}` i `{protectedItemName}` są konstruowane [powyżej](#responses-1). `{fabricName}` to "Azure". W naszym przykładzie przekłada się to:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

### <a name="responses"></a>Responses

*Usuń* ochrona jest [operację asynchroniczną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca ona dwie odpowiedzi: 202 (zaakceptowano), gdy inna operacja zostanie utworzony, a następnie 204 (NoContent), po zakończeniu tej operacji.

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 zaakceptowano     |         |     Zaakceptowane    |

## <a name="next-steps"></a>Kolejne kroki

[Przywracanie danych z kopii zapasowej usługi Azure Virtual machine](backup-azure-arm-userestapi-restoreazurevms.md).

Więcej informacji na temat interfejsów API REST usługi Azure Backup można znaleźć w następujących dokumentach:

- [Interfejs API REST platformy Azure w zakresie dostawcy usługi Recovery Services](/rest/api/recoveryservices/)
- [Wprowadzenie do interfejsu API REST platformy Azure](/rest/api/azure/)
