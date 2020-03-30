---
title: Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu interfejsu API REST
description: W tym artykule dowiesz się, jak skonfigurować, zainicjować i zarządzać operacjami tworzenia kopii zapasowych usługi Azure VM Backup przy użyciu interfejsu API REST.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 4789ef1e0e09df521f8cab539d972e9e669e0a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248166"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Tworzenie kopii zapasowych maszyny Wirtualnej platformy Azure przy użyciu usługi Azure Backup za pośrednictwem interfejsu API REST

W tym artykule opisano sposób zarządzania kopiami zapasowymi dla maszyny Wirtualnej platformy Azure przy użyciu usługi Azure Backup za pośrednictwem interfejsu API REST. Skonfiguruj ochronę po raz pierwszy dla wcześniej niechronionej maszyny Wirtualnej platformy Azure, wyzwalaj kopię zapasową na żądanie dla chronionej maszyny Wirtualnej platformy Azure i modyfikuj właściwości kopii zapasowej kopii zapasowej maszyny Wirtualnej za pośrednictwem interfejsu API REST, jak wyjaśniono poniżej.

Zapoznaj się z [tworzeniem przechowalni](backup-azure-arm-userestapi-createorupdatevault.md) i [tworzenie zasad](backup-azure-arm-userestapi-createorupdatepolicy.md) REST interfejs api samouczki do tworzenia nowych magazynów i zasad.

Załóżmy, że chcesz chronić maszynę wirtualną "testVM" w grupie zasobów "testRG" do magazynu usług odzyskiwania "testVault", obecny w grupie zasobów "testVaultRG", z zasadą domyślną (o nazwie "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurowanie kopii zapasowej dla niechronionej maszyny Wirtualnej platformy Azure przy użyciu interfejsu API REST

### <a name="discover-unprotected-azure-vms"></a>Odnajduj niechronione maszyny wirtualne platformy Azure

Po pierwsze magazyn powinien być w stanie zidentyfikować maszynę wirtualną platformy Azure. Jest to wyzwalane za pomocą [operacji odświeżania](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Jest to operacja asynchronizaj *post,* która upewnia się, że magazyn pobiera najnowszą listę wszystkich niechronionych maszyn wirtualnych w bieżącej subskrypcji i "buforuje" je. Gdy maszyna wirtualna jest "buforowana", usługi odzyskiwania będą mogły uzyskać dostęp do maszyny Wirtualnej i chronić ją.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

Identyfikator URI `{subscriptionId}`POST `{vaultName}` `{vaultresourceGroupName}`ma `{fabricName}` , , parametry. Jest `{fabricName}` "Azure". Jak na nasz `{vaultName}` przykład, jest "testVault" i `{vaultresourceGroupName}` jest "testVaultRG". Ponieważ wszystkie wymagane parametry są podane w identyfikatorze URI, nie ma potrzeby oddzielnej treści żądania.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Odpowiedzi

Operacja "odświeżanie" jest [operacją asynchronizacyjną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (Zaakceptowane) po utworzeniu innej operacji, a następnie 200 (OK) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|204 Brak treści     |         |  OK bez zwracania zawartości      |
|202 Zaakceptowane     |         |     Zaakceptowane    |

##### <a name="example-responses"></a>Przykładowe odpowiedzi

Po przesłaniu żądania *POST* zwracana jest odpowiedź 202 (zaakceptowana).

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
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Śledzenie wynikowej operacji za pomocą nagłówka "Lokalizacja" za pomocą prostego polecenia *GET*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Po wykryciu wszystkich maszyn wirtualnych platformy Azure polecenie GET zwraca odpowiedź 204 (bez zawartości). Magazyn jest teraz w stanie odnajdywać dowolną maszynę wirtualną w ramach subskrypcji.

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

### <a name="selecting-the-relevant-azure-vm"></a>Wybieranie odpowiedniej maszyny Wirtualnej platformy Azure

 Można potwierdzić, że "buforowanie" odbywa się przez [listę wszystkich elementów chronionych](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) w ramach subskrypcji i zlokalizować żądaną maszynę wirtualną w odpowiedzi. [Odpowiedź tej operacji](#example-responses-1) zawiera również informacje na temat sposobu, w jaki usługi odzyskiwania identyfikuje maszynę wirtualną.  Po zapoznaniu się ze wzorcem możesz pominąć ten krok i bezpośrednio przystąpić do [włączania ochrony](#enabling-protection-for-the-azure-vm).

Ta operacja jest operacją *GET.*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*Identyfikator URI GET* ma wszystkie wymagane parametry. Nie jest wymagana żadna dodatkowa treść żądania.

#### <a name="responses"></a><a name="responses-1"></a>Odpowiedzi

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 ok.     | [Lista danych WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses"></a><a name="example-responses-1"></a>Przykładowe odpowiedzi

Po przesłaniu żądania *GET* zwracana jest odpowiedź 200 (OK).

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
> Liczba wartości w odpowiedzi *GET* jest ograniczona do 200 dla "strony". Użyj pola "nextLink", aby uzyskać adres URL następnego zestawu odpowiedzi.

Odpowiedź zawiera listę wszystkich niechronionych maszyn wirtualnych `{value}` platformy Azure i każda zawiera wszystkie informacje wymagane przez usługę Azure Recovery Service do konfigurowania kopii zapasowej. Aby skonfigurować kopię `{name}` zapasową, `{virtualMachineId}` zanotuj pole i pole w `{properties}` sekcji. Skonstruuj dwie zmienne z tych wartości pól, jak wspomniano poniżej.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "vm;" +`{name}`
- `{virtualMachineId}`jest używany później w [treści żądania](#example-request-body)

W powyższym przykładzie powyższe wartości przekładają się na:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Włączanie ochrony maszyny Wirtualnej platformy Azure

Po odpowiedniej maszyny Wirtualnej jest "buforowane" i "zidentyfikowane", wybierz zasady do ochrony. Aby dowiedzieć się więcej o istniejących zasadach w przechowalni, zobacz [interfejs API zasad listy](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Następnie wybierz [odpowiednie zasady,](/rest/api/backup/protectionpolicies/get) odwołując się do nazwy zasad. Aby utworzyć zasady, zapoznaj się z [samouczka tworzenia zasad](backup-azure-arm-userestapi-createorupdatepolicy.md). "DefaultPolicy" jest zaznaczone w poniższym przykładzie.

Włączenie ochrony jest asynchroniką *put* operacji, która tworzy "element chroniony".

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

I `{containerName}` `{protectedItemName}` są tak skonstruowane powyżej. Jest `{fabricName}` "Azure". W naszym przykładzie przekłada się to na:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Tworzenie treści żądania

Aby utworzyć element chroniony, poniżej znajdują się składniki treści żądania.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Właściwości zasobu ProtectedItem         |

Aby uzyskać pełną listę definicji treści żądania i inne szczegóły, zobacz [tworzenie chronionego elementu REST dokumentu INTERFEJSU API](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Przykładowa treść żądania

Poniższa treść żądania definiuje właściwości wymagane do utworzenia chronionego elementu.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

Jest `{sourceResourceId}` `{virtualMachineId}` to wymienione powyżej z [odpowiedzi na listę przedmiotów chronionych](#example-responses-1).

#### <a name="responses"></a>Odpowiedzi

Utworzenie chronionego elementu jest [operacją asynchroniką](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (Zaakceptowane) po utworzeniu innej operacji, a następnie 200 (OK) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 ok.     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 Zaakceptowane     |         |     Zaakceptowane    |

##### <a name="example-responses"></a>Przykładowe odpowiedzi

Po przesłaniu żądania PUT dla utworzenia lub aktualizacji chronionego elementu początkowa odpowiedź jest 202 (zaakceptowana) z nagłówkiem lokalizacji lub nagłówkiem Azure-async.Once submit the *PUT* request for protected item creation or update, the initial response is 202 (Accepted) with a location header or Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Następnie śledź wynikową operację przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą prostego polecenia *GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Po zakończeniu operacji zwraca 200 (OK) z zawartością chronionego elementu w treści odpowiedzi.

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

Potwierdza to, że ochrona jest włączona dla maszyny Wirtualnej i pierwsza kopia zapasowa zostanie wyzwolona zgodnie z harmonogramem zasad.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Wyzwalanie kopii zapasowej na żądanie dla chronionej maszyny Wirtualnej platformy Azure

Gdy maszyna wirtualna platformy Azure jest skonfigurowana do tworzenia kopii zapasowych, kopie zapasowe są nachylne zgodnie z harmonogramem zasad. Możesz poczekać na pierwszą zaplanowaną kopię zapasową lub wyzwolić kopię zapasową na żądanie w dowolnym momencie. Przechowywanie kopii zapasowych na żądanie jest niezależne od przechowywania zasad kopii zapasowych i można je określić w określonym dniu i godzinie. Jeśli nie zostanie określony, zakłada się, że 30 dni od dnia wyzwalania kopii zapasowej na żądanie.

Wyzwalanie kopii zapasowej na żądanie jest operacją *POST.*

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

I `{containerName}` `{protectedItemName}` są tak skonstruowane [powyżej](#responses-1). Jest `{fabricName}` "Azure". W naszym przykładzie przekłada się to na:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>Tworzenie treści żądania

Aby wyzwolić kopię zapasową na żądanie, poniżej znajdują się składniki treści żądania.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|properties     | [IaaSVMBackupZawszezachecz](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Właściwości BackupRequestResource         |

Pełna lista definicji treści żądania i innych szczegółów można znaleźć w [celu wykonania kopii zapasowych wyzwalania chronionych elementów dokumentu interfejsu API REST](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Przykładowa treść żądania

Następująca treść żądania definiuje właściwości wymagane do wyzwolenia kopii zapasowej dla chronionego elementu. Jeśli przechowywanie nie zostanie określony, zostanie zachowane przez 30 dni od momentu wyzwolenia zadania kopii zapasowej.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Odpowiedzi

Wyzwalanie kopii zapasowej na żądanie jest [operacją asynchronizacyjną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (Zaakceptowane) po utworzeniu innej operacji, a następnie 200 (OK) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|202 Zaakceptowane     |         |     Zaakceptowane    |

#### <a name="example-responses"></a><a name="example-responses-3"></a>Przykładowe odpowiedzi

Po przesłaniu żądania *POST* dla kopii zapasowej na żądanie początkowa odpowiedź jest 202 (zaakceptowana) z nagłówkiem lokalizacji lub nagłówkiem asynchronizowania platformy Azure.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Następnie śledź wynikową operację przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą prostego polecenia *GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Po zakończeniu operacji zwraca 200 (OK) z identyfikatorem wynikowego zadania tworzenia kopii zapasowej w treści odpowiedzi.

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

Ponieważ zadanie tworzenia kopii zapasowej jest długotrwałą operacją, musi być śledzone zgodnie z opisem w [zadaniach monitora przy użyciu dokumentu INTERFEJSU API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Modyfikowanie konfiguracji kopii zapasowej chronionej maszyny Wirtualnej platformy Azure

### <a name="changing-the-policy-of-protection"></a>Zmiana polityki ochrony

Aby zmienić zasady, za pomocą których maszyna wirtualna jest chroniona, można użyć tego samego formatu, co [włączenie ochrony](#enabling-protection-for-the-azure-vm). Wystarczy podać nowy identyfikator zasad w [treści żądania](#example-request-body) i przesłać żądanie. Na przykład: Aby zmienić zasady testVM z "DefaultPolicy" na "ProdPolicy", podaj identyfikator "ProdPolicy" w treści żądania.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Odpowiedź będzie zgodna z tym samym formatem, co [wspomniany, aby umożliwić ochronę](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Zatrzymaj ochronę, ale zachowaj istniejące dane

Aby usunąć ochronę na chronionej maszynie wirtualnej, ale zachować dane już kopii zapasowej, usuń zasady w treści żądania i przesłać żądanie. Po usunięciu skojarzenia z zasadami kopie zapasowe nie są już wyzwalane i nie są tworzone żadne nowe punkty odzyskiwania.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Odpowiedź będzie zgodna z tym samym formatem, co wspomniane [dla wyzwalania kopii zapasowej na żądanie](#example-responses-3). Wynikowe zadanie powinno być śledzone zgodnie z opisem w [zadaniach monitora przy użyciu dokumentu INTERFEJSU API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Zatrzymywać ochronę i usuwać dane

Aby usunąć ochronę na chronionej maszynie wirtualnej i usunąć dane kopii zapasowej, wykonaj operację usuwania, jak opisano [tutaj](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Zatrzymanie ochrony i usunięcie danych jest operacją *DELETE.*

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

I `{containerName}` `{protectedItemName}` są tak skonstruowane [powyżej](#responses-1). `{fabricName}`jest "Azure". W naszym przykładzie przekłada się to na:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses"></a><a name="responses-2"></a>Odpowiedzi

*Ochrona DELETE* jest [operacją asynchronizacyjną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (Zaakceptowane) po utworzeniu innej operacji, a następnie 204 (NoContent) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent (NoContent)       |
|202 Zaakceptowane     |         |     Zaakceptowane    |

> [!IMPORTANT]
> Aby chronić przed przypadkowym usunięciem scenariuszy, dostępna jest [funkcja usuwania nietrwałego](use-restapi-update-vault-properties.md#soft-delete-state) dla magazynu usług odzyskiwania. Jeśli stan usuwania nietrwałego przechowalni jest włączony, operacja usuwania NIE spowoduje natychmiastowego usunięcia danych. Będzie przechowywany przez 14 dni, a następnie trwale oczyszczony. Klient nie jest obciążony opłatą za przechowywanie przez ten 14 dni. Aby cofnąć operację usunięcia, zapoznaj się z [sekcją cofania usuwania](#undo-the-stop-protection-and-delete-data).

### <a name="undo-the-stop-protection-and-delete-data"></a>Cofanie ochrony przed zatrzymaniem i usuwanie danych

Cofanie przypadkowego usunięcia jest podobne do tworzenia elementu kopii zapasowej. Po cofnięciu usunięcia element jest zachowywany, ale nie są wyzwalane żadne przyszłe kopie zapasowe.

Cofnij usuwanie jest *operacją PUT,* która jest bardzo [podobna](#changing-the-policy-of-protection) do zmiany zasad i/lub [umożliwienia ochrony.](#enabling-protection-for-the-azure-vm) Wystarczy podać zamiar cofnąć usunięcie ze zmienną *isRehydrate* w [treści żądania](#example-request-body) i przesłać żądanie. Na przykład: Aby cofnąć usunięcie dla testVM, należy użyć następującej treści żądania.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

Odpowiedź będzie zgodna z tym samym formatem, co wspomniane [dla wyzwalania kopii zapasowej na żądanie](#example-responses-3). Wynikowe zadanie powinno być śledzone zgodnie z opisem w [zadaniach monitora przy użyciu dokumentu INTERFEJSU API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Następne kroki

[Przywróć dane z kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-arm-userestapi-restoreazurevms.md).

Aby uzyskać więcej informacji na temat interfejsów API rest kopii zapasowej platformy Azure, zobacz następujące dokumenty:

- [Interfejs API REST dostawcy usług odzyskiwania platformy Azure](/rest/api/recoveryservices/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)
